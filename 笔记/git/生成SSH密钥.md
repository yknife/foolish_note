### 一.查看是否已配置

###### 1.  进入ssh目录

```bash
cd ～/.ssh
```

###### 2.  如果不能进入该目录，说明没生成过，则需要检查下是否配置过git账户

```cpp
git config --list
```

###### 3. 查看ssh具体内容如果ssh文件夹中有`id_rsa`，`id_rsa.pub`，说明之前生成过ssh 秘钥，可以直接使用

```undefined
 ls
```

### 二. 重新配置

###### 1. 配置账户

用户名，建议拼音或英文

```csharp
git config --global user.name "account name" 
```

邮箱地址

```csharp
git config --global user.email "account email"
```

###### 2. 生成秘钥

```bash
ssh-keygen -t rsa -C "account email"
```

`account email`为上面的邮箱地址  提示输入保存密钥路径，直接回车即可（三次默认回车）。成功是显示如下

```ruby
+---[RSA 2048]----+
|  .o+o.          |
|   ....          |
|  +..+           |
| ..=. +          |
| .o..o  S        |
|...= .o+         |
|E+* .+=o.        |
|+=+++=o.=        |
|OB+.+=+o .       |
+----[SHA256]-----+
```

完成了账户创建、秘钥生成，.ssh文件夹下就多了id_rsa（私有秘钥）和id_rsa.pub（公有密钥）

###### 3. 查看公有密钥，全部复制，添加到github中

```css
cat id_rsa.pub
```

###### 4. 将公钥内容添加到GitHub

- 1. 打开GitHub网站进行登陆；

- 1. 到个人设置页Personal Settings；

- 1. 找到SSH and GPG keys；

- 1. 选择新建SSH key：new ssh key；

- 1. 填写和粘贴公钥内容（不含中文）。

     ![img](https:////upload-images.jianshu.io/upload_images/3239766-9f207e38726ee5a2.png?imageMogr2/auto-orient/strip|imageView2/2/w/1200/format/webp)

     将公钥内容添加到GitHub.png

