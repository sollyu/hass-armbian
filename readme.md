## 📖 说明

这是电视机顶盒个人使用Hass的套件服务，配置使用了httpd做了常用服务域名化，目前有下列服务：

```txt
hass.home.com     HomeAssistant
red.home.com      NodeRed
docker.home.com   Portainer.io
```

配置详情参照：[httpd-vhosts.conf](./httpd/conf/httpd-vhosts.conf)

###### 🚧 注意：

需要手动在本机或者路由器中指定[hosts](https://baike.baidu.com/item/hosts/10474546)为盒子IP，如我的配置如下：

```txt
10.168.1.212        docker.home.com
10.168.1.212        red.home.com
10.168.1.212        hass.home.com
```

> 其中`10.168.1.212`为我盒子的IP地址

## 👷 OpenLdap 

容器里内置了OpenLdap账号管理，首次安装时需要每个单独配置。
如若需要添加账号，请使用第三方OpenLdap管理工具进行添加，如：[LdapAdmin](http://www.ldapadmin.org/download/index.html)。默认的账号信息如下：

```txt
管理员账号：cn=admin,dc=home,dc=com
管理员密码：admin
```

> 如若修改管理员密码，请直接修改[docker-compose.yaml](./docker-compose.yaml#L105)的密码。

#### Portainer.io

```txt
LDAP Server       ：127.0.0.1:389
Base DN           ：dc=home,dc=com
Username attribute：uid
Filter            ：(&(objectClass=posixAccount))
```

###### 🚧 注意：

需要手动分配容器的访问权限，不然新账号里看不到内容。

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

