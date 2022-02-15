![Hello World... Image!](/9bc734d15e601f537f61f4d135ac38ef.png)

```
/etc/systemd/system/network-online.targets.wants/networking.service
sudo vi /etc/systemd/system/network-online.targets.wants/networking.service
21:TimeoutStartSec=5min
sudo systemctl daemon-reload
```
