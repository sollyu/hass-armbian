## 📖 说明

这是电视机顶盒个人使用Hass的套件服务，配置使用了[Httpd](https://httpd.apache.org/)做了常用服务域名化和一些服务优化：

✅ **网页缓存**  ✅ **统一登录**  ✅ **内存限制**  ✅ **数据分离**  ✅ **国内更新**

| 序号 | 名称          | 说明               | 域名            |
|------|---------------|--------------------|-----------------|
| 1    | HomeAssistant | Hass服务           | hass.home.com   |
| 2    | NodeRed       | Hass辅助自动化     | red.home.com    |
| 3    | Portainer     | Docker容器管理     | docker.home.com |
| 4    | OpenLdap      | 统一账户管理       | -               |
| 5    | Httpd         | Http服务           | -               |

> 域名配置详情参照：[httpd-vhosts.conf](./httpd/conf/httpd-vhosts.conf)
>
> B站已同步相关的视频教程：https://b23.tv/MljyUoR 
###### 🚧 注意：

如果需要使用域名访问，请手动在本机或者路由器中指定[hosts](https://baike.baidu.com/item/hosts/10474546)为盒子IP，如我的配置如下：

```txt
10.168.1.212  docker.home.com
10.168.1.212  red.home.com
10.168.1.212  hass.home.com
```

> 其中`10.168.1.212`为我盒子的IP地址

## 🔥 一键安装

```shell
curl https://ghproxy.com/https://raw.githubusercontent.com/wiki/sollyu/hass-armbian/shell/install.v1.sh | bash
```

###### 经验之谈

在执行一键安装命令之前可使用下列命令来手动拉去最新的Docker镜像，这样的好处就是可以很清楚的明白镜像拉取的进度以及国内镜像配置的情况，省的执行一键安装命令在那里等的无聊透顶。

```bash
docker pull homeassistant/home-assistant:stable
docker pull nodered/node-red:latest
docker pull portainer/portainer-ce:latest
docker pull httpd:latest
docker pull osixia/openldap:latest
```

## 👷 OpenLdap

容器里内置了[OpenLdap](https://www.openldap.org/)作为统一登陆的账号管理服务，他的好处就是一个账户登陆各种服务，再也不用为一个服务一个账户密码而烦恼。

首次安装时需要每个服务单独配置，如：Portainer。因为账户不需要频繁增删改查，所以就没配置在线管理账户的服务，同时也能节省盒子的资源。如若需要添加账号，可以使用第三方OpenLdap管理工具进行添加，如：[LdapAdmin](http://www.ldapadmin.org/download/index.html)，默认的管理员账号信息如下：

```txt
管理地址：127.0.0.1:389
管理账号：cn=admin,dc=home,dc=com
管理密码：admin
```

> 增加通用账户操作指引，参与[Wiki](https://github.com/sollyu/hass-armbian/wiki/OpenLdap%E6%B7%BB%E5%8A%A0%E8%B4%A6%E6%88%B7)。

#### Portainer.io

进入到设置，找到认证，选择LDAP。配置示例：

```txt
Base DN           ：dc=home,dc=com
Username attribute：uid
Filter            ：(&(objectClass=posixAccount))
```

###### 🚧 注意

需要手动分配容器的访问权限，不然新账号里看不到内容。

###### 📄 其他

如果需要查询某个组中的成员，可参照下面配置
```txt
Group Base DN.            : cn=ldap-admin,dc=home,dc=com
Group Membership Attribute: uniqueMember
Group Filter.             : (objectClass=groupOfUniqueNames)
```

## 🐔 Httpd

对于一些没有提供账号管理的服务，可使用httpd自带的网关服务进行权限管控。这里罗列一下我配置，更为详细的参阅[官方文档](https://httpd.apache.org/docs/current/mod/mod_authnz_ldap.html#authldapurl)。

```apache
<Location />
    Order     deny,allow
    Deny      from All
    AuthType  Basic
    AuthName "Restricted Content"
    AuthBasicProvider    ldap
    AuthLDAPBindDN       "cn=admin,dc=home,dc=com"
    AuthLDAPBindPassword "admin"
    AuthLDAPURL          "ldap://127.0.0.1:389/dc=home,dc=com?uid"
    Require valid-user
    Satisfy any
</Location>
```

## 💡 常见的问题

```text
表象：启动服务
解决：docker-compose up --remove-orphans -d

表象：停止全部服务
解决：docker-compose down
备注：手动停止后设备重启不会自动启动服务

表象：NodeRed节点
解决：node-red-contrib-home-assistant-websocket

表象：HACS安装
解决：https://github.com/hacs-china/integration

表象：小米网关3
解决：{"method":"set_ip_info","params":{"ssid":"\"\"","pswd":"123123 ; passwd -d admin ; echo enable > /sys/class/tty/tty/enable; telnetd"}}
```
