# 安装
## 下载

打开[MinGW-w64下载链接](https://sourceforge.net/projects/mingw-w64/)，进入官方下载页面。注意不要直接点击下载按钮，点击"File"进入文件浏览页面。

![](肥猫的小世界/肥猫的资源收藏世界/软件收藏/MinGW-w64/图片/点击FILE按键.png)

随后选择安装路径`Home/Toolchains targetting Win64/Personal Builds/mingw-builds/8.1.0/threads-posix/seh`，选择下载。

![](肥猫的小世界/肥猫的资源收藏世界/软件收藏/MinGW-w64/图片/下载文件.png)

## 解压

将下载好的压缩包解压，并将`mingw64`文件夹移动到C盘目录下。

![](肥猫的小世界/肥猫的资源收藏世界/软件收藏/MinGW-w64/图片/移动文件夹至C盘.png)

## 配置环境变量

1. 右键“此电脑”，点击属性。
2. 在属性面板中点击“高级系统设置”。
3. 在高级系统设置面板中点击“环境变量”。

![](肥猫的小世界/肥猫的资源收藏世界/软件收藏/MinGW-w64/图片/打开环境变量界面.png)

1. 进入环境变量面板后，找到“PATH”，选中并点击“编辑”。
2. 进入PATH环境变量编辑页面后，在右侧点击“浏览”。
3. 根据自己的文件路径，找到mingw64中的bin文件夹。
4. 选中bin文件夹，点击“确定”。
5. 再多次点击“确定”，完成环境变量的配置。

![](肥猫的小世界/肥猫的资源收藏世界/软件收藏/MinGW-w64/图片/添加路径至PATH.png)

# 使用

## 在 VS Code 中使用 C++

### 安装扩展

在VS Code扩展中搜索安装“C/C++”。

![](肥猫的小世界/肥猫的资源收藏世界/软件收藏/MinGW-w64/图片/安装C++扩展.png)

### 配置C/C++扩展

按“CTRL”+"P",打开搜索栏，输入`<C/C++`会看到”C/C++IntelliSense 配置“相关的选项。
点击进入后，修改”编译器路径“。

![](肥猫的小世界/肥猫的资源收藏世界/软件收藏/MinGW-w64/图片/编译器路径.png)

修改”IntelliSense 模式“为”gcc-x64“。

![](肥猫的小世界/肥猫的资源收藏世界/软件收藏/MinGW-w64/图片/编译器模式.png)

VS Code 中 C++环境配置完成。

## 在VS Code中测试环境

新建一个`.cpp`文件，在其中输入以下代码，编译并运行。
```
#include <iostream>
using namespace std;
int main()
{
    cout<<"HELLO WORD!"<<endl;
    system("PAUSE");
    return 0;
}
```

结果在输出中显示：

```
HELLO WORD!
Press any key to continue . . .
```

![](肥猫的小世界/肥猫的资源收藏世界/软件收藏/MinGW-w64/图片/测试代码.png)