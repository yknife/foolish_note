### 5. Docker的卸载

```bash
# 1. 卸载依赖
yum remove docker-ce docker-ce-cli containerd.io
# 2. 删除资源  . /var/lib/docker是docker的默认工作路径
rm -rf /var/lib/docker
```