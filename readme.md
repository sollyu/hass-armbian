## ð è¯´æ

è¿æ¯çµè§æºé¡¶çä¸ªäººä½¿ç¨Hassçå¥ä»¶æå¡ï¼éç½®ä½¿ç¨äº[Httpd](https://httpd.apache.org/)åäºå¸¸ç¨æå¡ååååä¸äºæå¡ä¼åï¼

â **ç½é¡µç¼å­**  â **ç»ä¸ç»å½**  â **åå­éå¶**  â **æ°æ®åç¦»**  â **å½åæ´æ°**

| åºå· | åç§°          | è¯´æ               | åå            |
|------|---------------|--------------------|-----------------|
| 1    | HomeAssistant | Hassæå¡           | hass.home.com   |
| 2    | NodeRed       | Hassè¾å©èªå¨å     | red.home.com    |
| 3    | Portainer     | Dockerå®¹å¨ç®¡ç     | docker.home.com |
| 4    | OpenLdap      | ç»ä¸è´¦æ·ç®¡ç       | -               |
| 5    | Httpd         | Httpæå¡           | -               |

> ååéç½®è¯¦æåç§ï¼[httpd-vhosts.conf](./httpd/conf/httpd-vhosts.conf)
>
> Bç«å·²åæ­¥ç¸å³çè§é¢æç¨ï¼https://b23.tv/MljyUoR 
###### ð§ æ³¨æï¼

å¦æéè¦ä½¿ç¨ååè®¿é®ï¼è¯·æå¨å¨æ¬æºæèè·¯ç±å¨ä¸­æå®[hosts](https://baike.baidu.com/item/hosts/10474546)ä¸ºçå­IPï¼å¦æçéç½®å¦ä¸ï¼

```txt
10.168.1.212  docker.home.com
10.168.1.212  red.home.com
10.168.1.212  hass.home.com
```

> å¶ä¸­`10.168.1.212`ä¸ºæçå­çIPå°å

## ð¥ ä¸é®å®è£

```shell
curl https://ghproxy.com/https://raw.githubusercontent.com/wiki/sollyu/hass-armbian/shell/install.v1.sh | bash
```

###### ç»éªä¹è°

å¨æ§è¡ä¸é®å®è£å½ä»¤ä¹åå¯ä½¿ç¨ä¸åå½ä»¤æ¥æå¨æå»ææ°çDockeréåï¼è¿æ ·çå¥½å¤å°±æ¯å¯ä»¥å¾æ¸æ¥çæç½éåæåçè¿åº¦ä»¥åå½åéåéç½®çæåµï¼ççæ§è¡ä¸é®å®è£å½ä»¤å¨é£éç­çæ èéé¡¶ã

```bash
docker pull homeassistant/home-assistant:stable
docker pull nodered/node-red:latest
docker pull portainer/portainer-ce:latest
docker pull httpd:latest
docker pull osixia/openldap:latest
```

## ð· OpenLdap

å®¹å¨éåç½®äº[OpenLdap](https://www.openldap.org/)ä½ä¸ºç»ä¸ç»éçè´¦å·ç®¡çæå¡ï¼ä»çå¥½å¤å°±æ¯ä¸ä¸ªè´¦æ·ç»éåç§æå¡ï¼åä¹ä¸ç¨ä¸ºä¸ä¸ªæå¡ä¸ä¸ªè´¦æ·å¯ç èç¦æ¼ã

é¦æ¬¡å®è£æ¶éè¦æ¯ä¸ªæå¡åç¬éç½®ï¼å¦ï¼Portainerãå ä¸ºè´¦æ·ä¸éè¦é¢ç¹å¢å æ¹æ¥ï¼æä»¥å°±æ²¡éç½®å¨çº¿ç®¡çè´¦æ·çæå¡ï¼åæ¶ä¹è½èççå­çèµæºãå¦è¥éè¦æ·»å è´¦å·ï¼å¯ä»¥ä½¿ç¨ç¬¬ä¸æ¹OpenLdapç®¡çå·¥å·è¿è¡æ·»å ï¼å¦ï¼[LdapAdmin](http://www.ldapadmin.org/download/index.html)ï¼é»è®¤çç®¡çåè´¦å·ä¿¡æ¯å¦ä¸ï¼

```txt
ç®¡çå°åï¼127.0.0.1:389
ç®¡çè´¦å·ï¼cn=admin,dc=home,dc=com
ç®¡çå¯ç ï¼admin
```

> å¢å éç¨è´¦æ·æä½æå¼ï¼åä¸[Wiki](https://github.com/sollyu/hass-armbian/wiki/OpenLdap%E6%B7%BB%E5%8A%A0%E8%B4%A6%E6%88%B7)ã

#### Portainer.io

è¿å¥å°è®¾ç½®ï¼æ¾å°è®¤è¯ï¼éæ©LDAPãéç½®ç¤ºä¾ï¼

```txt
Base DN           ï¼dc=home,dc=com
Username attributeï¼uid
Filter            ï¼(&(objectClass=posixAccount))
```

###### ð§ æ³¨æ

éè¦æå¨åéå®¹å¨çè®¿é®æéï¼ä¸ç¶æ°è´¦å·éçä¸å°åå®¹ã

###### ð å¶ä»

å¦æéè¦æ¥è¯¢æä¸ªç»ä¸­çæåï¼å¯åç§ä¸é¢éç½®
```txt
Group Base DN.            : cn=ldap-admin,dc=home,dc=com
Group Membership Attribute: uniqueMember
Group Filter.             : (objectClass=groupOfUniqueNames)
```

## ð Httpd

å¯¹äºä¸äºæ²¡ææä¾è´¦å·ç®¡ççæå¡ï¼å¯ä½¿ç¨httpdèªå¸¦çç½å³æå¡è¿è¡æéç®¡æ§ãè¿éç½åä¸ä¸æéç½®ï¼æ´ä¸ºè¯¦ç»çåé[å®æ¹ææ¡£](https://httpd.apache.org/docs/current/mod/mod_authnz_ldap.html#authldapurl)ã

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

## ð¡ å¸¸è§çé®é¢

```text
è¡¨è±¡ï¼å¯å¨æå¡
è§£å³ï¼docker-compose up --remove-orphans -d

è¡¨è±¡ï¼åæ­¢å¨é¨æå¡
è§£å³ï¼docker-compose down
å¤æ³¨ï¼æå¨åæ­¢åè®¾å¤éå¯ä¸ä¼èªå¨å¯å¨æå¡

è¡¨è±¡ï¼NodeRedèç¹
è§£å³ï¼node-red-contrib-home-assistant-websocket

è¡¨è±¡ï¼HACSå®è£
è§£å³ï¼https://github.com/hacs-china/integration

è¡¨è±¡ï¼å°ç±³ç½å³3
è§£å³ï¼{"method":"set_ip_info","params":{"ssid":"\"\"","pswd":"123123 ; passwd -d admin ; echo enable > /sys/class/tty/tty/enable; telnetd"}}
```
