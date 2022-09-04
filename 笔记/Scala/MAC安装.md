1. [下载地址](https://www.scala-lang.org/download/2.12.11.html)

2. 配置环境变量

   ```sh
   vim ~/.bash_profile
   export SCALA_HOME="/usr/local/scala-2.12.11"
   export PATH=$PATH:$SCALA_HOME/bin
   source ~/.bash_profile
   ```

3. 验证

   ```sh
   scala -version

