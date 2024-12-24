首先需要在阿里云平台购买域名，一般仅需要几块钱到几十块钱。
点击进入控制台。
![[肥猫的小世界/肥猫的日常生活世界/NAS搭建/图片/阿里云页面.png]]

随后左侧功能列表，点击域名进入域名控制台。
![[肥猫的小世界/肥猫的日常生活世界/NAS搭建/图片/阿里云控制台产品与服务列表.png]]

域名列表中如果没有域名，进入域名购买页面，搜索并选购自己想要的域名。
[域名购买页面](wanwang.aliyun.com)
![[肥猫的小世界/肥猫的日常生活世界/NAS搭建/图片/阿里云域名下单页面.png]]


确保域名列表中，域名状态为”正常“。
![[肥猫的小世界/肥猫的日常生活世界/NAS搭建/图片/阿里云域名状态正常.png]]

接下来在NAS Docker中安装DDNS-GO。
注意，想要使用IPV6需要使用host模式。
打开[DDNS-GO](https://github.com/jeessy2/ddns-go)查看并复制Docker安装指令。
```
docker run -d --name ddns-go --restart=always --net=host -v /opt/ddns-go:/root jeessy/ddns-go
```

在UNRAID后台的终端中输入这条指令。
![[肥猫的小世界/肥猫的日常生活世界/NAS搭建/图片/UNRAID使用终端安装Docker.png]]

你会发现，国内根本拉不了任何Docker，需要手动修改国内的镜像源。
首先创建文件夹”docker“。 ^08fab0
```
mkdir /etc/docker
```
随后在此文件夹中创建并编辑”daemon.json“文件，若提示无权限，加`sudo`。
```
nano /etc/docker/daemon.json
```
在此文件中写入：
```
{
  "registry-mirrors": [
    "https://docker.m.daocloud.io",
    "https://noohub.ru",
    "https://huecker.io",
    "https://dockerhub.timeweb.cloud",
    "https://docker.rainbond.cc"
  ]
}
```
后保存文件，并重启Docker。

拉取Docker可成功。
![[肥猫的小世界/肥猫的日常生活世界/NAS搭建/图片/UNRAID安装Docker成功.png]]

最后进入Docker界面，发现DDNS-GO正在运行。
![[肥猫的小世界/肥猫的日常生活世界/NAS搭建/图片/UNRAID运行DDNS-GO成功.png]]

其中`192.168.5.66:9876`为DDNS-GO的后台地址。
第一次进入后台时，设置用户名和密码，建议设置为UNRAID管理员相同密码防止忘记。
![[肥猫的小世界/肥猫的日常生活世界/NAS搭建/图片/登录DDNS-GO.png]]

在此使用阿里云的DDNS服务，需要填入`AccessKey ID`和`AccessKey Secret`需要进入阿里云相关管理界面。
![[肥猫的小世界/肥猫的日常生活世界/NAS搭建/图片/阿里云域名控制台进入AccessKey页面.png]]

创建一个AccessKey。
![[肥猫的小世界/肥猫的日常生活世界/NAS搭建/图片/阿里云控制台创建AccessKey.png]]

保存好自己的AccessKey，并将其填入DDNS-GO相关选项中。
![[肥猫的小世界/肥猫的日常生活世界/NAS搭建/图片/获取AccessKey.png]]

填入AccessKey相关选项后，选择开启IPV4和IPV6选项，选择”通过接口获取“，并将域名填入”Domains“。
![[肥猫的小世界/肥猫的日常生活世界/NAS搭建/图片/设置DDNS-GO获取IP方式和域名.png]]

保存后，日志中显示将域名绑定置相应的IP地址。
![[肥猫的小世界/肥猫的日常生活世界/NAS搭建/图片/DDNS-GO成功获取IPV4和IPV6地址并绑定域名.png]]

使用手机流量访问两个域名，发现IPV4地址会跳转至路由器页面。这是因为家庭内局域网只有拨号的路由器一个设备获取到了真正的公网IP地址，其余设备需要端口转发，但端口有限不推荐使用IPV4连接。
到此DDNS-GO绑定IPV4和IPV6成功！！
![[肥猫的小世界/肥猫的日常生活世界/NAS搭建/图片/手机使用移动网络访问UNRAID后台.jpg]]
![[肥猫的小世界/肥猫的日常生活世界/NAS搭建/图片/手机通过移动网络使用IPV4访问到路由器后台.jpg]]