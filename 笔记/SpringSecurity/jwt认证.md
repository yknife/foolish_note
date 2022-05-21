1. 引入依赖

   ```xml
   <!--redis依赖-->
   <dependency>
       <groupId>org.springframework.boot</groupId>
       <artifactId>spring-boot-starter-data-redis</artifactId>
   </dependency>
   <!--fastjson依赖-->
   <dependency>
       <groupId>com.alibaba</groupId>
       <artifactId>fastjson</artifactId>
       <version>1.2.33</version>
   </dependency>
   <!--jwt依赖-->
   <dependency>
       <groupId>io.jsonwebtoken</groupId>
       <artifactId>jjwt</artifactId>
       <version>0.9.0</version>
   </dependency>
   ```

2. 编写UserDetailsLoginServiceImpl重写从数据源获取user对象的过程

   ```java
   @Service
   public class UserDetailsServiceImpl implements UserDetailsService {
   
       @Autowired
       UserMapper userMapper;
   
       @Override
       public UserDetails loadUserByUsername(String username) throws UsernameNotFoundException {
           LambdaQueryWrapper<User> queryWrapper = new LambdaQueryWrapper<>();
           queryWrapper.eq(User::getUserName,username);
           User user = userMapper.selectOne(queryWrapper);
           if(Objects.isNull(user)){
               throw new RuntimeException("用户不存在");
           }
           return new LoginUser(user);
       }
   }
   ```

3. 添加SecurityConfig用于配制security

   ```java
   @Configuration
   public class SecurityConfig extends WebSecurityConfigurerAdapter {
   
   		//配置密码加密方式
       @Bean
       public PasswordEncoder passwordEncoder(){
           return new BCryptPasswordEncoder();
       }
   
       @Override
       protected void configure(HttpSecurity http) throws Exception {
           http
                   //关闭csrf
                   .csrf().disable()
                   //不通过Session获取SecurityContext
                   .sessionManagement().sessionCreationPolicy(SessionCreationPolicy.STATELESS)
                   .and()
                   .authorizeRequests()
                   // 对于登录接口 允许匿名访问
                   .antMatchers("/user/login").anonymous()
                   // 除上面外的所有请求全部需要鉴权认证
                   .anyRequest().authenticated();
       }
   
     	//
       @Bean
       @Override
       public AuthenticationManager authenticationManagerBean() throws Exception {
           return super.authenticationManagerBean();
       }
   }
   ```

4. 登陆service

   ```java
   @Service
   public class LoginServiceImpl implements LoginService {
   
       @Autowired
       private AuthenticationManager authenticationManager;
       @Autowired
       private RedisCache redisCache;
   
       @Override
       public ResponseResult login(User user) {
           UsernamePasswordAuthenticationToken authenticationToken = new UsernamePasswordAuthenticationToken(user.getUserName(),user.getPassword());
           Authentication authenticate = authenticationManager.authenticate(authenticationToken);
           if(Objects.isNull(authenticate)){
               throw new RuntimeException("用户名或密码错误");
           }
           //使用userid生成token
           LoginUser loginUser = (LoginUser) authenticate.getPrincipal();
           String userId = loginUser.getUser().getId().toString();
           String jwt = JwtUtil.createJWT(userId);
           //authenticate存入redis
           redisCache.setCacheObject("login:"+userId,loginUser);
           //把token响应给前端
           HashMap<String,String> map = new HashMap<>();
           map.put("token",jwt);
           return new ResponseResult(200,"登陆成功",map);
       }
   }
   ```

5. 编写token过滤器

   ```java
   @Component
   public class JwtAuthenticationTokenFilter extends OncePerRequestFilter {
   
       @Autowired
       private RedisCache redisCache;
   
       @Override
       protected void doFilterInternal(HttpServletRequest request, HttpServletResponse response, FilterChain filterChain) throws ServletException, IOException {
           //获取token
           String token = request.getHeader("token");
           if (!StringUtils.hasText(token)) {
               //放行
               filterChain.doFilter(request, response);
               return;
           }
           //解析token
           String userid;
           try {
               Claims claims = JwtUtil.parseJWT(token);
               userid = claims.getSubject();
           } catch (Exception e) {
               e.printStackTrace();
               throw new RuntimeException("token非法");
           }
           //从redis中获取用户信息
           String redisKey = "login:" + userid;
           LoginUser loginUser = redisCache.getCacheObject(redisKey);
           if(Objects.isNull(loginUser)){
               throw new RuntimeException("用户未登录");
           }
           //存入SecurityContextHolder
           //TODO 获取权限信息封装到Authentication中
           UsernamePasswordAuthenticationToken authenticationToken =
                   new UsernamePasswordAuthenticationToken(loginUser,null,null);
           SecurityContextHolder.getContext().setAuthentication(authenticationToken);
           //放行
           filterChain.doFilter(request, response);
       }
   }
   ```

6. 配置token过滤器

   ```java
   @Configuration
   public class SecurityConfig extends WebSecurityConfigurerAdapter {
   
       @Autowired
       JwtAuthenticationTokenFilter jwtAuthenticationTokenFilter;
     
     	...
   
       @Override
       protected void configure(HttpSecurity http) throws Exception {
          	...
           //把token校验过滤器添加到过滤器链中
           http.addFilterBefore(jwtAuthenticationTokenFilter, UsernamePasswordAuthenticationFilter.class);
       }
   }
   ```

7. 登出

   ```java
   @Service
   public class LoginServiceImpl implements LoginService {
   		...
    @Override
    public ResponseResult logout() {
        Authentication authentication = SecurityContextHolder.getContext().getAuthentication();
        LoginUser loginUser = (LoginUser) authentication.getPrincipal();
        Long userid = loginUser.getUser().getId();
        redisCache.deleteObject("login:"+userid);
        return new ResponseResult(200,"退出成功");
    }
   }
   ```

   

   

   

   
