## 📖 说明

这是电视机顶盒个人使用Hass的套件服务，配置使用了[Httpd](https://httpd.apache.org/)做了常用服务域名化和一些服务优化：

✅ **网页缓存**  ✅ **统一登录**  ✅ **自动更新**  ✅ **内存限制**  ✅ **数据分离**

| 序号 | 名称          | 说明               | 域名            |
|------|---------------|--------------------|-----------------|
| 1    | HomeAssistant | Hass服务           | hass.home.com   |
| 2    | NodeRed       | Hass辅助自动化     | red.home.com    |
| 3    | Portainer     | Docker容器管理     | docker.home.com |
| 4    | OpenLdap      | 统一账户管理       | -               |
| 5    | Httpd         | Http服务           | -               |
| 6    | WatchTower    | Docker镜像自动更新 | -               |

> 域名配置详情参照：[httpd-vhosts.conf](./httpd/conf/httpd-vhosts.conf)

###### 🚧 注意：

如果需要使用域名访问，请手动在本机或者路由器中指定[hosts](https://baike.baidu.com/item/hosts/10474546)为盒子IP，如我的配置如下：

```txt
10.168.1.212  docker.home.com
10.168.1.212  red.home.com
10.168.1.212  hass.home.com
```

> 其中`10.168.1.212`为我盒子的IP地址

## 👷 OpenLdap

容器里内置了[OpenLdap](https://www.openldap.org/)作为统一登陆的账号管理服务，他的好处就是一个账户登陆各种服务，再也不用为一个服务一个账户密码而烦恼。

首次安装时需要每个服务单独配置，如：Portainer。因为账户不需要频繁增删改查，所以就没配置在线管理账户的服务，同时也能节省盒子的资源。如若需要添加账号，可以使用第三方OpenLdap管理工具进行添加，如：[LdapAdmin](http://www.ldapadmin.org/download/index.html)，默认的管理员账号信息如下：

```txt
管理员账号：cn=admin,dc=home,dc=com
管理员密码：admin
```

> 如若修改管理员密码，请直接修改[docker-compose.yaml](./docker-compose.yaml#L105)的密码。
> 
> 操作指引，参与[Wiki](https://github.com/sollyu/hass-armbian/wiki/OpenLdap%E6%B7%BB%E5%8A%A0%E8%B4%A6%E6%88%B7)。

#### Portainer.io

进入到设置，找到认证，选择LDAP。配置示例：

```txt
LDAP Server       ：127.0.0.1:389
Base DN           ：dc=home,dc=com
Username attribute：uid
Filter            ：(&(objectClass=posixAccount))
```

###### 🚧 注意

需要手动分配容器的访问权限，不然新账号里看不到内容。

###### 📄 权限

新登陆进来的LDAP用户，可以加入到`ldap-admin`的`teams`里，这样就能看到容器了。
其他服务在启动的时候，就配置了这个属性。

#### 🐔 Httpd

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
######################################################
表象：启动命令
解决：docker-compose up --remove-orphans -d

######################################################
表象：NodeRed节点
解决：node-red-contrib-home-assistant-websocket

######################################################
表象：Hass启动失败
解决：初次启动的时候不要复制 configuration.yaml 。等第一次启动后再修改

######################################################
表象：HACS安装
解决：https://github.com/hacs-china/integration

######################################################
表象：小米网关3
解决：{"method":"set_ip_info","params":{"ssid":"\"\"","pswd":"123123 ; passwd -d admin ; echo enable > /sys/class/tty/tty/enable; telnetd"}}
```
