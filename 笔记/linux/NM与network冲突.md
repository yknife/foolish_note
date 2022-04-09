[CentOS](https://so.csdn.net/so/search?q=CentOS&spm=1001.2101.3001.7020)的启动出错：

当执行 ifup ens33

出现错误：

[[root](https://so.csdn.net/so/search?q=root&spm=1001.2101.3001.7020)@localhost ~]# **ifup ens33**
Error: Connection activation failed: No suitable device found for this connection (device lo not available because device is strictly unmanaged).

解决办法：

[root@[localhost](https://so.csdn.net/so/search?q=localhost&spm=1001.2101.3001.7020) ~]# **chkconfig NetworkManager off**
Note: Forwarding request to ‘systemctl disable NetworkManager.service’.
Removed symlink /etc/systemd/system/multi-user.target.wants/NetworkManager.service.
Removed symlink /etc/systemd/system/dbus-org.freedesktop.NetworkManager.service.
Removed symlink /etc/systemd/system/dbus-org.freedesktop.nm-dispatcher.service.
Removed symlink /etc/systemd/system/network-online.target.wants/NetworkManager-wait-online.service.

[root@localhost ~]# **chkconfig network on**

[root@localhost ~]# **service NetworkManager stop**
Redirecting to /bin/systemctl stop NetworkManager.service

[root@localhost ~]# **service network start**
Starting network (via systemctl): [ OK ]