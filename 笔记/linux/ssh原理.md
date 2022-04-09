### 非对称加密

由于对称加密的这个弊端，产生了非对称加密，非对称加密中有两个密钥：公钥和私钥。公钥由私钥产生，但却无法推算出私钥；公钥加密后的密文，只能通过对应的私钥来解密。

非对称加密的登录流程：
![img](/Users/yknife/Documents/笔记/截图/2160538013-5c7656118be4d.png)

初始状态：`topgun`终端要登录`Server`服务器，发起连接请求`ssh work@server.com`

1. 服务端运行有`ssh`服务，并持续监听`22`号端口，因此可以生成一对公钥和私钥；此时将公钥返回给客户端
2. 客户端使用公钥，对登录密码进行加密，（如服务器`work`用户密码为`xxx`），生成公钥加密字符串
3. 客户端将公钥加密字符串发送给服务端
4. 服务端使用私钥，解密公钥加密字符串，得到原始密码
5. 校验密码是否合法（此为本机`work`密码）
6. 返回登录结果给客户端：成功登录或密码错误

在非对称加密中，由于只有公钥会被传输，而私钥是服务端本地保存，因此即便公钥被监听，也无法拿到原始密码，从而登录服务器。

## 中间人攻击

在非对称加密中可以有效保护登录密码不被泄漏，但这是在建立连接到真实服务器的情况下。设想一下，如果供给者并不监听密码或公钥，而是直接伪装成服务器呢：
![img](/Users/yknife/Documents/笔记/截图/2898564151-5c765a0513308.png)
在该示例图中，存在`Hacker`服务器劫持了你的`ssh`建连请求（如通过`DNS`劫持等方式)，导致你与`Hacker`机器的连接一切正常，因此它能拿到你的明文密码，并通过明文密码来攻击真实的服务端。

那么`SSH`采用了非对称的加密方式，是怎么解决这个问题的呢？

```scala
[work@client.com: ~]$ ssh work@server.com
The authenticity of host 'server.com (10.10.10.24)' can't be established.
RSA key fingerprint is ad:2e:92:41:6f:31:b1:c1:35:43:eb:df:f1:18:a1:c1.
Are you sure you want to continue connecting (yes/no)?  yes
Warning: Permanently added 'server.com,10.10.10.24' (RSA) to the list of known hosts.
Password: (enter password) 
```

在这个认证信息中，可以看到提示：无法确认主机`server.com (10.10.10.24)`的真实性，不过知道它的公钥指纹，是否继续连接？

输入`yes`继续连接后，就会确认该服务器为可信任服务器，然后添加到`known_hosts`文件中，下次不用再次确认，然后跳转到输入密码的验证阶段。这种简单粗暴的方式相当于让我们肉眼比对来判断目标服务器是否是真实服务器，我觉得并不是最理想的方式，希望后续会有更完美的认证方式。

> 之所以用`fingerprint`（公钥指纹）代替`key`，主要是`key`过于长（`RSA`算法生成的公钥有`1024`位），很难直接比较。所以，对公钥进行`hash`生成一个`128`位的指纹，这样就方便比较了。

## `SSH`免密登录

我们已经掌握如何使用`ssh`登录远程服务器了，但是每次登录都要输入密码，比较麻烦。`ssh`提供一种免密登录的方式：公钥登录。

![img](https://image-static.segmentfault.com/109/320/1093209063-5c7673e3a12f9)

1. 在客户端使用`ssh-keygen`生成一对密钥：公钥+私钥
2. 将客户端公钥追加到服务端的`authorized_key`文件中，完成公钥认证操作
3. 认证完成后，客户端向服务端发起登录请求，并传递公钥到服务端
4. 服务端检索`authorized_key`文件，确认该公钥是否存在
5. 如果存在该公钥，则生成随机数`R`，并用公钥来进行加密，生成公钥加密字符串`pubKey(R)`
6. 将公钥加密字符串传递给客户端
7. 客户端使用私钥解密公钥加密字符串，得到`R`
8. 服务端和客户端通信时会产生一个会话`ID(sessionKey)`，用`MD5`对`R和SessionKey`进行加密，生成摘要（即`MD5`加密字符串）
9. 客户端将生成的`MD5`加密字符串传给服务端
10. 服务端同样生成`MD5(R,SessionKey)`加密字符串
11. 如果客户端传来的加密字符串等于服务端自身生成的加密字符串，则认证成功
12. 此时不用输入密码，即完成建连，可以开始远程执行`shell`命令了

## 实现免密登录

`ssh-genkey`是生成密钥的工具，执行完成后生成公钥和密钥，这两个文件会默认保存在`~/.ssh/`路径下。常用的参数为：

- `-t:` 指定生成密钥类型`（rsa、dsa）`。默认为`rsa`
- `-f:` 指定存放私钥的文件，公钥文件名为私钥文件名加`.pub`后缀。默认为`id_rsa`
- `-P:` 指定`passphrase`（私钥的密码），用于确保私钥的安全。默认为空
- `-C:` 备注。默认为`user@hostname`

我们直接执行来生成密钥，所有的参数都可以为空，也就是一直回车确认：

```ruby
$ ssh-keygen
Generating public/private rsa key pair.
Enter file in which to save the key (/home/work/.ssh/id_rsa): 
Enter passphrase (empty for no passphrase): 
Enter same passphrase again: 
Your identification has been saved in id_rsa.
Your public key has been saved in id_rsa.pub.
The key fingerprint is:
74:17:74:38:b2:c7:70:fd:1d:33:eb:e2:71:71:cc:11 work@client.com
```

此时，生成的文件在`~/.ssh/`目录下，我们会看到这些文件：

```rust
id_rsa    // 私钥文件  
id_rsa.pub    // 公钥文件
authorized_keys    // 存放客户端公钥的文件
known_hosts    // 确认过公钥指纹的可信服务器列表的文件
config    // 指定不同域名使用哪个密钥的配置文件
```

因为一台机器即能是客户端，又能是服务端，因此同时存在`authorized_keys`（在该机器为服务端时使用）和`Known_hosts`（在该机器为客户端时使用）。

我们的服务器会有很多的用户，如果所有的用户都用同一份密钥，可能就没办法划分权限或者区分用户，如多个用户提交`git`就需要按照用户名来生成密钥，用于区分用户。同时你可能也希望针对不同的服务器使用不同的密钥对，因此需要`config`配置文件来配置针对不同服务器的配置：

```javascript
$vim ~/.ssh/config
Host a.baidu.com
User work
IdentityFile ~/.ssh/id_rsa

Host b.baidu.com
User zhaoshuaiqiang
IdentityFile ~/.ssh/zhaoshuaiqiang
```

这样在连接不同的服务器时，就会使用不同的密钥文件来登录。

在客户端生成密钥对之后，将公钥追加到服务器的`authorized_keys`文件中即可。

```bash
 ...
 15 ssh-rsa AAAAB3NzaC1yc2EAAAABIwAAAQEAn30bjwtkLW82vTlLMQnI/a42J2g7o+HBCpSzBgNG+xfZuSNQOAU8+FNKQvriT4AL7ToiTtrZDLOEGqyQzaxQejuNnWG6aQ9ETh96qXhVLecWidaBieFGuv+F
    uXz6x551xtFXx64AzG+706dhnv1nOACYlrnfvXhi5kZzWzprET+CxMIeYhJQwwc19pF5zCWeU9QUvd1mOu0n8JVycevmuXRdVx9WpXq2+aaaaaaaa3uYGMBxvSLtT40O1AiEZ+k9EeYCnTEV
    tnGoVWCyxpwv6rR/GDOtJL/d+/Wb6I0HEKxxe087yZz8GWpQN5TEIAjq3oEjI/aiGw== work@a.baidu.com
 16 ssh-rsa AAAAB3NzaC1yc2EAAAABIwAAAQEAzRmK+CAVLnryXqQwweu8Yji8bGAvqzf8PSDzYn1nmE6ZeDkBbrWvFlVZ2yOip3IX8RjvuPc28mTCr0LsIOOmpvET0SzOkt0hzLBPiyCN/QvbKU/RbUv8v5y2
    hEAlbUkMEzv7qOHQEruGEvA5y99gf/nYjK5OFKVpmg838OScUV3i88Xbqk8rtcuRZHWuZLnuB5spBsEE5r1UrnH/Ik7frZr8Vb4X6aZWbAp1yc0SqZ8JXVbmOiakqq0WqkLm+zqhEwr+Ooh2guH23x9zjMqY
    Uy+aaaaaBEBC+LepCvskArBt/SRwJDJkcItz8T7kBC3CP0Y0X4hB+6k6/yM/pup5w== work@b.baidu.com
```

此时，即可免密登录服务器。

## 私钥的安全性

假设你已经实现了服务器集群的免密登录，那么如果你的一台客户端被攻击成功了，那么整个集群的安全性便荡然无存了。因此，我们有时还需要对密钥做安全性保障，即设置私钥密码：

```bash
// 使用rsa方法加密，生成test密钥对，私钥密码为123456
ssh-keygen -t rsa -f test -P 123456 
```

此时，各机器仍然是免密登录，但是需要你输入私钥密码：

```perl
$ssh work@a.baidu.com
Enter passphrase for key '/home/work/.ssh/test': 
```