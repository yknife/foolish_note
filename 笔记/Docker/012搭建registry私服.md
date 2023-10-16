1. 拉取私服镜像

   ```sh
   docker pull registry
   ```

2. 启动私服容器

   ```sh
   docker run -d -p 5000:5000 -v /u01/ysw/registry/:/tmp/registry --privileged=true registry
   ### 默认情况，仓库被创建在容器的/var/lib/registry目录，建议自行容器卷映射，方便宿主机联调
   ```

3. 安装net-tools，为了能使用ifconfig命令

   ```sh
   apt-get update
   apt-get -y install net-tools
   ```

4. commit本地镜像

   ```sh
   docker commit -m="add ifconfig" -a="yknife" bbd3030dfcc4 yknife/ubuntu:1.1
   ```

5. 查看本地私服仓库中镜像

   ```sh
   curl -XGET http://192.168.225.11:5000/v2/_catalog
   {"repositories":[]}
   ```

6. tag一份镜像标签，包含有私服信息

   ```sh
   docker tag yknife/ubuntu:1.1 192.168.225.11:5000/yknife/ubuntu:1.1
   ```

7. 修改配置文件支持http

   ```sh
   vim /etc/docker/daemon.json
   {
     "registry-mirrors": ["https://73ydyi8v.mirror.aliyuncs.com"],
     "insecure-registries":["192.168.225.11:5000"]
   }
   systemctl restart docker
   ```

8. 推送镜像到本地私服

   ```sh
   docker push 192.168.225.11:5000/yknife/ubuntu:1.1
   ```

9. 验证

   ```sh
   curl -XGET http://192.168.225.11:5000/v2/_catalog
   {"repositories":["yknife/ubuntu"]}
   ```

10. 从本地私服拉取镜像

   ```sh
   docker pull 192.168.225.11:5000/yknife/ubuntu:1.1
   ```