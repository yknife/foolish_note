1. numPartitions与numReduceTasks的关系，
   * numReduceTasks=1:那么走hadoop默认逻辑只开启一个reduceTask
   * numPartitions>numReduceTasks>1:这时候会io异常
   * numPartitions<numReduceTasks:这时候会有多余的reduceTask创建，但是不会有异常

