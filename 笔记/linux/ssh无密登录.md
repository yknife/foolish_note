1. 进入～路径

   ```sh
   cd ~
   # 可查看隐藏目录.ssh
   ll -al
   ```

2. 生成公钥和私钥

   ```sh
   ssh-keygen -t rsa
   ```

3. 复制公钥到其他服务器（注意：默认本机也需要执行）

   ```sh
   ssh-copy-id spark03
   ```

   