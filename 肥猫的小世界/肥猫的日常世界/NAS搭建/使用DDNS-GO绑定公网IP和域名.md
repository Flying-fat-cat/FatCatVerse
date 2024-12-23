首先需要在阿里云平台购买域名，一般仅需要几块钱到几十块钱。
点击进入控制台。
![[Pasted image 20241209233932.png]]

随后左侧功能列表，点击域名进入域名控制台。
![[Pasted image 20241209234026.png]]

域名列表中如果没有域名，进入域名购买页面，搜索并选购自己想要的域名。
[域名购买页面](wanwang.aliyun.com)
![[Pasted image 20241209234808.png]]


确保域名列表中，域名状态为”正常“。
![[Pasted image 20241210012322.png]]

接下来在NAS Docker中安装DDNS-GO。
注意，想要使用IPV6需要使用host模式。
打开[DDNS-GO](https://github.com/jeessy2/ddns-go)查看并复制Docker安装指令。
```
docker run -d --name ddns-go --restart=always --net=host -v /opt/ddns-go:/root jeessy/ddns-go
```

在UNRAID后台的终端中输入这条指令。
![[Pasted image 20241210001816.png]]

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
![[Pasted image 20241210011946.png]]

最后进入Docker界面，发现DDNS-GO正在运行。
![[Pasted image 20241210012053.png]]

其中`192.168.5.66:9876`为DDNS-GO的后台地址。
第一次进入后台时，设置用户名和密码，建议设置为UNRAID管理员相同密码防止忘记。
![[Pasted image 20241210012242.png]]

在此使用阿里云的DDNS服务，需要填入`AccessKey ID`和`AccessKey Secret`需要进入阿里云相关管理界面。
![[Pasted image 20241210012516.png]]

创建一个AccessKey。
![[Pasted image 20241210012612.png]]

保存好自己的AccessKey，并将其填入DDNS-GO相关选项中。
![[Pasted image 20241210012714.png]]

填入AccessKey相关选项后，选择开启IPV4和IPV6选项，选择”通过接口获取“，并将域名填入”Domains“。
![[Pasted image 20241210013406.png]]

保存后，日志中显示将域名绑定置相应的IP地址。
![[Pasted image 20241210014212.png]]

使用手机流量访问两个域名，发现IPV4地址会跳转至路由器页面。这是因为家庭内局域网只有拨号的路由器一个设备获取到了真正的公网IP地址，其余设备需要端口转发，但端口有限不推荐使用IPV4连接。
到此DDNS-GO绑定IPV4和IPV6成功！！
![[Screenshot_20241210_014532_com.huawei.browser.jpg]]
![[Screenshot_20241210_014617_com.huawei.browser.jpg]]