1. DispatcherServlet中doDispatch方法

2. 反射调用controller中的方法，并返回mv，mv中包含controller方法中对model的操作

   ```java
   // Actually invoke the handler.
   mv = ha.handle(processedRequest, response, mappedHandler.getHandler());
   ```

   ```java
   @GetMapping("/goto")
   public String testGoto(HttpServletRequest request, HttpServletResponse response, Map<String,Object> map, Model model){
       request.setAttribute("status",200);
       map.put("hello","xxx");
       model.addAttribute("world","yyy");
       Cookie cookie = new Cookie("c1", "v1");
       response.addCookie(cookie);
       return "forward:/success";
   }
   ```

3. 将model设置到request的attributes中(也在doDispatch)方法中

   ```java
   processDispatchResult(processedRequest, response, mappedHandler, mv, dispatchException);
   ```

4. 一些参考图片

![img](../截图/1603271442869-63b4c3c7-c721-4074-987d-cbe5999273ae.png)

   ### ![img](../截图/1603271678813-d8e1a1e5-94fa-412c-a7f1-6f27174fd127.png)

   ### ![img](../截图/1603271813894-037be041-92a5-49af-a49c-c350b3dd587a.png)