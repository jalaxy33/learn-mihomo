# TUN防火墙配置

## nftables

如果使用 nftables：

```sh
sudo vim /etc/nftables.conf
```

在这些位置添加配置：

```conf
# 这里改为tun设备名
define mihomo_if = "mihomo"

table inet filter {
  chain input {
    # mihomo TUN
    iifname $mihomo_if accept
  }

  chain forward {
    # mihomo TUN
    iifname $mihomo_if accept
    oifname $mihomo_if accept
  }
}
```
