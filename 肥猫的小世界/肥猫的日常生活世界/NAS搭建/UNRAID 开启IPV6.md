在UNRAID后台页面点击设置。
![[肥猫的小世界/肥猫的日常生活世界/NAS搭建/图片/UNRAID后台页面点击设置按钮.png]]

点击网络设置
![[肥猫的小世界/肥猫的日常生活世界/NAS搭建/图片/UNRAID进入网络设置.png]]

查看自己的IP设置，我这里目前为“仅IPV4”.
![[肥猫的小世界/肥猫的日常生活世界/NAS搭建/图片/UNRAID网络设置IPV4灰色.png]]

想要修改灰色部分的IPV4设置，需返回上一级“设置”页面，进入DOCKER设置界面。
![[肥猫的小世界/肥猫的日常生活世界/NAS搭建/图片/UNRAID进入Docker管理页面.png]]

将Docker启用改成否，并应用。
![[肥猫的小世界/肥猫的日常生活世界/NAS搭建/图片/UNRAID关闭启用Docker选项.png]]

返回“设置”页面，再次进入“虚拟机管理器”设置页面。
![[肥猫的小世界/肥猫的日常生活世界/NAS搭建/图片/UNRAID进入虚拟机管理页面.png]]

将虚拟机“启用虚拟机”选项设置为“否”，并应用。
![[肥猫的小世界/肥猫的日常生活世界/NAS搭建/图片/UNRAID关闭启用虚拟机选项.png]]

返回“网络设置”页面，将“网络协议”修改为“IPV4+IPV6”并应用。
![[肥猫的小世界/肥猫的日常生活世界/NAS搭建/图片/UNRAID修改网络协议为IPV4和IPV6.png]]

确保成功自动获取到IPV4和IPV6地址，并且确保IPV6地址为“240”开头，“240”开头为公网IP地址。
![[肥猫的小世界/肥猫的日常生活世界/NAS搭建/图片/UNRAID获取IPV4地址和IPV6地址.png]]

最后按上述步骤再次进入“DOCKER"和”虚拟机控制器“页面，将”是否开启“改为”是“选项。
