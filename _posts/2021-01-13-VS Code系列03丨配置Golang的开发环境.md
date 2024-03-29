---
layout: post
title: VS Code系列03丨配置Golang的开发环境
tag: VS Code
---

### 一.VS Code中配置go

1.现在我们要为我们的VS Code编辑器安装Go扩展插件，让它支持Go语言开发。

![20210106001641](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20210106001641.png)

2.Windows平台按下`Ctrl+Shift+P`，Mac平台按`Command+Shift+P`，这个时候VS Code界面会弹出一个输入框，如下图：

![20210106002007](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20210106002007.png)

3.我们在这个输入框中输入`go:install`，下面会自动搜索相关命令，我们选择 `Go:Install/Update Tools`这个命令，按下图选中并会回车执行该命令

![20210106002148](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20210106002148.png)

4.全部选中，点击确定，进行安装

![20210106002421](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20210106002421.png)

5.等待所有工具都安装成功

![20210106003530](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20210106003530.png)

### 二.配置代码片段快捷键

1.按`Ctrl/Command+Shift+P`,按下图输入`snippets`，选择命令并执行：

![20210106004015](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20210106004015.png)

2.然后在弹出的窗口点击选择`go`选项

![20210106004145](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20210106004145.png)

3.我这里创建了两个快捷方式，一个是输入`pln`就会在编辑器中插入`fmt.Println()`代码；输入`plf`，就会插入`fmt.Printf("")`代码

![![20210106004502](httpscdn.jsdelivr.netghluckykangpicture_bedblogs_images20210106004502.png)](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/![20210106004502](httpscdn.jsdelivr.netghluckykangpicture_bedblogs_images20210106004502.png).png)

附代码：

    "println":{
        "prefix": "pln",
        "body":"fmt.Println($0)",
        "description": "println"
    },
    "printf":{
        "prefix": "plf",
        "body": "fmt.Printf(\"$0\")",
        "description": "printf"
    }

到这里就配置完成了，可以愉快的使用了！！