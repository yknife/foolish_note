1. 均衡

   ```sh
   sbin/start-balancer.sh -threshold 10
   //对于参数10，代表的是集群中各个节点的磁盘空间利用率相差不超过10%，可根据实际情况进行调整。
   ```

2. 停止均衡

   ```sh
   sbin/stop-balancer.sh
   ```

   