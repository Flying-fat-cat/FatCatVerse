#作者 Flying_fat_cat 会飞的肥猫

#日期 2025-04-14
## 介绍

### **[InteractiveHtmlBom](https://github.com/openscopeproject/InteractiveHtmlBom)**

此插件可生成方便的物料清单 （BOM） 列表，能够直观地关联并轻松搜索元件及其在 PCB 上的位置。它在手工焊接原型时特别有用，因为它允许用户快速找到电路板上元件组的位置。也可以通过单击电路板图纸上的封装来反向查找元件组。

该插件利用 Pcbnew python API 读取 PCB 数据并渲染丝印、fab 层、封装焊盘、文本和图纸。BOM 表字段和分组是完全可配置的，附加列（例如制造商 ID）可以在 Schematic 编辑器中添加，并通过网表文件、Eeschema 内部 BOM 工具生成的 XML 文件或电路板文件本身导入。


### **[InteractiveHtmlBomForAD](https://github.com/lianlian33/InteractiveHtmlBomForAD)**

由[lianlian33](https://github.com/lianlian33)创建的`InteractiveHtmlBom`AD插件移植版本，用于在AD中使用插件生成交互式BOM辅助焊接。

> [!NOTE] 注
> 近期我了解到AD官方也是有类似的在线生成交互式BOM的网站，但这个功能是收费的。因此个人用户还是使用插件生成交互式BOM比较合适。


由于某些原因，该项目很久没有维护，因此在新版本AD中运行会出现一些未知的错误，详情可见[高版本AD由于API缺失无法实现交互式Bom](https://github.com/lianlian33/InteractiveHtmlBomForAD/issues/14)。


### **[InteractiveHtmlBomForAD by Sakura-PI](https://github.com/Sakura-Pi/InteractiveHtmlBomForAD)**

由[Sakura Pi](https://github.com/Sakura-Pi)创建的`InteractiveHtmlBom`的分支，解决了上述某些BUG。


## 使用教程

### 步骤1

- 拉取或下载工程文件

![](肥猫的小世界/肥猫的学习世界/硬件%20PCB/AD25%20使用交互式BOM辅助焊接详细教程/图片/1.png)

### 步骤2

- 解压工程

![](肥猫的小世界/肥猫的学习世界/硬件%20PCB/AD25%20使用交互式BOM辅助焊接详细教程/图片/2.png)

### 步骤3

- 将该文件夹`InteractiveHtmlBomForAD-master`移动到不会删除的位置

![](肥猫的小世界/肥猫的学习世界/硬件%20PCB/AD25%20使用交互式BOM辅助焊接详细教程/图片/3.png)

### 步骤4

- 运行一次`Initialize.bat`脚本

![](肥猫的小世界/肥猫的学习世界/硬件%20PCB/AD25%20使用交互式BOM辅助焊接详细教程/图片/4.png)

### 步骤5

- 双击文件使用AD打开

![](肥猫的小世界/肥猫的学习世界/硬件%20PCB/AD25%20使用交互式BOM辅助焊接详细教程/图片/5.png)

- 这里使用的是AD25

![](肥猫的小世界/肥猫的学习世界/硬件%20PCB/AD25%20使用交互式BOM辅助焊接详细教程/图片/6.png)

### 步骤6

- `文件 - 打开 - 选择文件`打开想要生成交互式BOM的PCB工程

![](肥猫的小世界/肥猫的学习世界/硬件%20PCB/AD25%20使用交互式BOM辅助焊接详细教程/图片/7.png)

- 双击PCB文件，打开PCB

![](肥猫的小世界/肥猫的学习世界/硬件%20PCB/AD25%20使用交互式BOM辅助焊接详细教程/图片/8.png)
### 步骤7

- 点击`文件 - 运行脚本`

![](肥猫的小世界/肥猫的学习世界/硬件%20PCB/AD25%20使用交互式BOM辅助焊接详细教程/图片/9.png)

- 选择运行脚本文件中的`main()`文件
- 若没找到这个文件，返回步骤4，重新运行脚本

![](肥猫的小世界/肥猫的学习世界/硬件%20PCB/AD25%20使用交互式BOM辅助焊接详细教程/图片/10.png)


### 步骤8

- 生成完成
- html文件位于PCB工程文件夹下`PnPout`文件夹中


![](肥猫的小世界/肥猫的学习世界/硬件%20PCB/AD25%20使用交互式BOM辅助焊接详细教程/图片/11.png)

- 打开该html文件，查看交互式BOM

![](肥猫的小世界/肥猫的学习世界/硬件%20PCB/AD25%20使用交互式BOM辅助焊接详细教程/图片/12.png)

## 教程结束 相关链接

1. **[InteractiveHtmlBom](https://github.com/openscopeproject/InteractiveHtmlBom)**
2. **[InteractiveHtmlBomForAD](https://github.com/lianlian33/InteractiveHtmlBomForAD)**
3. **[InteractiveHtmlBomForAD by Sakura-PI](https://github.com/Sakura-Pi/InteractiveHtmlBomForAD)**
4. ……