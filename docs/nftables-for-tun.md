# nftables 防火墙放行 tun 设备

## 前置步骤

首先查看 tun 设备是否创建：

```sh
ip a
```

查看是否有 mihomo 配置文件中设置的 tun 设备名（`tun` 下的 `device` 字段）

## 修改防火墙配置

- 编辑配置文件：

  ```sh
  sudo vim /etc/nftables.conf
  ```

  填写以下内容：

  ```conf
  # 这里改为tun设备名
  define mihomo_if = "mihomo"

  table inet filter {
    chain input {
      # 其他已有配置...

      # mihomo TUN
      iifname $mihomo_if accept
    }

    chain forward {
      # 其他已有配置...

      # mihomo TUN
      iifname $mihomo_if accept
      oifname $mihomo_if accept
    }
  }
  ```

- 检查配置语法：

  ```sh
  sudo nft -c -f /etc/nftables.conf
  ```

- 重启防火墙服务：

  ```sh
  sudo systemctl restart nftables
  ```
