
由于Jellyfin开源协议和Intel、NVDIA、AMD版权存在冲突问题，Jellyfin本身并不自带核显、独显等的驱动程序，想要单独安装驱动是一件很麻烦的事情，需要重新编译内核……因此推荐直接使用大佬@nyanmisaka打包的Jellyfin版本。[nyanmisaka/jellyfin](https://hub.docker.com/r/nyanmisaka/jellyfin)[参考链接](https://wiki.scio.icu/index.php/Unraid/%E6%B5%81%E5%AA%92%E4%BD%93%E5%B9%B3%E5%8F%B0%E7%9A%84%E5%9F%BA%E7%A1%80%E4%BD%BF%E7%94%A8)

# 安装Jellyfin-nyanmisaka版本

在UNRAID应用界面搜索Jellyfin或nyanmisaka，点击右上角`Click Here To Get More Results From DockerHub`显示更多Docker HUB的结果。
选择`nyanmisaka/jellyfin`点击安装。

等待一段时间后，该Docker的相关镜像下载成功，进入添加容器的配置界面。若下载失败，参考[[使用DDNS-GO绑定公网IP和域名#^08fab0]]。
在添加容器页面，为参数配置所映射的cache（缓存）与media（媒体库）文件夹。若不存在相关文件夹，可在命令行中使用`cd`命令进入某个文件夹，使用`mkdir`创建新的文件夹用于Docker的映射。（缓存文件就是保存了临时文件、和刮削出来的一些略缩图等）

![[Pasted image 20241210132355.png]]

在页面底部点击`添加另一个路径、端口、变量、标签或设备`选择”配置类型“为”设备“，填写”值“为驱动的地址`/dev/dri/`。

![[Pasted image 20241210133144.png]]

在页面底部点击添加另一个路径、端口、变量……创建媒体库文件夹映射。
![[Pasted image 20241210203717.png]]

点击应用，创建Docker。等待一段时间后完成安装。
![[Pasted image 20241210134342.png]]

随后通过IP+端口号进入Jellyfin。
![[Pasted image 20241210135112.png]]

发现访问进去后不是设置页面？**清除浏览器缓存**试试
或者直接访问：`http://你的内网ip:你的端口/web/index.html#/wizardstart.html` 试试
如若**行不通请重装**（重装之前在appdata目录删除jellyfin的文件夹）
之后再度访问它

进入设置页面，选择语言为简体中文，点击下一步。
![[Pasted image 20241210200002.png]]

设置管理员账户与密码，建议与NAS保持一致，防止忘记密码。
![[Pasted image 20241210200102.png]]

设置媒体库可跳过，之后随时可添加。
![[Pasted image 20241210200355.png]]

首选的元数据语言，语言：CHINESE，国家/地区： People's Republic of China中国全称。
![[Pasted image 20241210200552.png]]

端口映射自己在路由器上设置，不需要在此勾选。
![[Pasted image 20241210200709.png]]

点击下一步，并点击完成，到此Jellyfin安装完成。随后进入登陆页面。
![[Pasted image 20241210200807.png]]

# 配置Jellyfin
## **开启硬件解码功能**

在【**控制台**】—【**播放**】—【**转码**】栏选择“**QSV**”或“**VAAPI**”（我选择的是QSV）
ps：如果是选择了VAAPI会让你选择驱动路径，默认已经是在dev目录内了，所以找到dri目录并选择就行了
启用硬件解码相关的设置建议能勾都勾
在页面底部保存即可
![[Pasted image 20241210201352.png]]

## **添加媒体库**

 在【控制台】—【媒体库】中选择**添加媒体库**
扫描媒体库适用于规范的文件夹，可以快速添加，这里以手动创建为例
**选择内容类型**：根据你的需要选择
在此选择“电影”，语言：CHINESE，国家/地区： People's Republic of China。
![[Pasted image 20241210202037.png]]

点击添加文件夹。
![[Pasted image 20241210202137.png]]

选择自己映射的对应文件夹，我这里选择映射的Movies文件夹。

![[Pasted image 20241210205856.png]]

其他：
他是自带了一些内置的元数据获取插件（刮削器），不过有些可能需要魔法解决，后面看使用的具体情况考虑是否换就行，这里默认即可）

可以勾选“将媒体图像保存到媒体所在文件夹”，如其简介所说，方便编辑，然后刮削的单集截图文件会被存放在影视文件夹中，有一个新建文件夹：metadata

保存即可
![[Pasted image 20241210210346.png]]

鼠标悬浮至该媒体库，点击右下角的更多，可以修改。
![[Pasted image 20241210210425.png]]

在媒体库扫库时，如果扫描失败或扫描超级缓慢，可在Docker容器参数中添加指令，修改Jellyfin的HOST，实现连接能够访问的服务器。
在Jellyfin容器界面，点击右上角“基本视图”。
![[Pasted image 20241211234147.png]]

在额外参数中添加以下代码：
```
--add-host=image.tmdb.org:104.16.61.155 --add-host=api.themoviedb.org:13.35.67.86 --add-host=www.themoviedb.org:54.192.151.79 --add-host=themoviedb.org:99.84.238.136
```
完成后即可实现较快的媒体库扫描。

