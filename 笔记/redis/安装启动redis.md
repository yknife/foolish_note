1. 下载redis

2. 解压、编译

   ```bash
   tar -xzvf redis-stable.tar.gz
   cd redis-stable
   make
   ```

3. 如果你编译成功，你会在src目录中看到若干个二进制文件

   - **redis-server**: redis服务端
   - **redis-cli** iredis客户端

4. 要安装这些文件到`/usr/local/bin`

   ```bash
   make install
   ```

5. 启动服务端

   ```bash
   redis-server
   ```

6. 关闭服务端，只需`Ctrl-C`即可。