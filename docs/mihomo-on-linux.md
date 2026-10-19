# 在 linux 上配置 mihomo 代理

Mihomo（原名 Clash Meta）是目前功能最完整、社区最活跃的 Clash 衍生内核。也是目前很多 clash 系代理软件（如 FlClash、Clash Verge 等）的底层服务。它本身没有图形界面，完全靠读取一份 YAML 格式的配置文件来工作。

本文介绍如何在 linux 上快速配置 mihomo 裸核并开启 TUN 透明代理的流程，相关原理和配置项具体含义请参考：

- [Mihomo 配置从入门到进阶：三阶段完全教程](https://github.com/HenryChiao/MIHOMO_YAMLS/wiki/Home)
- [虚空终端官方文档](https://wiki.metacubex.one/) · [配置项说明](https://wiki.metacubex.one/config/)

## 配置流程

1. 用系统包管理器安装 `mihomo`，以 archlinux 为例：

   ```sh
   sudo pacman -S mihomo
   ```

   > 在 arch 上这个包在 `archlinuxcn` 源里

2. 编辑 mihomo 配置文件：

   ```sh
   sudo vim /etc/mihomo/config.yaml
   ```

   默认配置可能不可用，需要修改，可以参考其他教程学习 mihomo 配置怎么写，也可以直接用我的：

   - [我的 mihomo 配置](../tun-config.yaml)（已配置TUN）

     > 配置使用方法：复制配置内容，在 `proxy_providers` 处填入订阅链接后保存

3. 修改配置文件后最好检查一下语法：

   ```sh
   sudo mihomo -t -f /etc/mihomo/config.yaml
   ```

4. 启动服务：

   - 先检查相应的 systemd 配置文件是否存在:

     ```sh
     systemctl cat mihomo
     ```

     如果没有，需要手动创建：

     ```sh
     sudo vim /etc/systemd/system/mihomo.service
     ```

     复制这个[配置文件](../assets/mihomo.service)的内容。

   - 启动服务：

     ```sh
     sudo systemctl start mihomo
     ```

     如果需要开机自启：

     ```sh
     sudo systemctl enable --now mihomo
     ```

5. 如果启用了防火墙（如 nftables ），还需要放行 tun 对应设备名（我的配置里是 `mihomo`）。

   - 查看 tun 设备是否创建

     ```sh
     ip a
     ```

     查看是否有 mihomo 配置文件中设置的 tun 设备名（`tun` 下的 `device` 字段）

   - 修改防火墙配置，放行 tun 设备：
     - [nftables 防火墙配置](./nftables-for-tun.md)

6. 测试代理是否生效：

   ```sh
   curl -I www.google.com
   ```

   返回 `HTTP ... 200 ...` 开头一大串内容就是成功了。

## 使用TIPS

- 服务管理

  - 查看服务状态

    ```sh
    systemctl status mihomo
    ```

  - 每次修改配置后，都需要重启服务

    ```sh
    sudo systemctl restart mihomo
    ```

  - 中止服务

    ```sh
    sudo systemctl stop mihomo
    ```

    > 如果用 `enable` 设置了自启动，下次开机时还会启动

  - 取消开机自启

    ```sh
    sudo systemctl disable mihomo
    ```

- 访问 mihomo 管理面板：[http://localhost:9090/ui/](http://localhost:9090/ui/)

  - 后端地址填入 `http://127.0.0.1:9090`
  - 密钥填入 secret 字段内容（未设置则留空）

  > 前提：在配置文件中设置 `external-controller`、`external-ui` 等配置
