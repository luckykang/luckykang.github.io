---
layout: post
title: Tools系列01-markdown常用总结
tag: Tools
---


### 一些基本语法

标题            
H1 :# Header 1            
H2 :## Header 2           
H3 :### Header 3           
H4 :#### Header 4           
H5 :##### Header 5            
H6 :###### Header 6      
链接 :[我的邮箱](1304589181@qq.com)        
加粗 :**加粗了吗**        
斜体字 :*倾斜了吗*         
*删除线 :~~删除了吗~~                    
换行符 : 一行结束时输入两个空格           
列表 :* 添加星号成为一个新的列表项。          
引用 :> 引用内容               
内嵌代码 : `alert('Hello World');`        
画水平线 (HR) :-------- 
关键字
**重点：**
代码标记
```python
print(hello world)  
```

目录
> Blog        
> 　｜        
> 　｜－－ _config.yml       
> 　｜－－ node_modules      
> 　｜－－ public                
> 　｜－－ source                                                  
> 　｜－－ db.json	          
> 　｜－－ package.json          
> 　｜－－ scaffolds          
> 　｜－－ themes  

```
.
├── _config.yml 
├── _includes    
├── _layouts
├── _posts
├── _site
├── 404.html
├── about.md
├── archive.html

```

### 段落
- [iOS9AdaptationTips](https://github.com/ChenYilong/iOS9AdaptationTips) 
- [iOS9学习系列](http://www.cocoachina.com/ios/20150821/13140.html)

子段落
> 1. 在Info.plist中添加 `NSAppTransportSecurity`类型Dictionary，在`NSAppTransportSecurity`下添加`NSAllowsArbitraryLoads`，Boolean 为 YES。
> 2. 直接使用CFNetwork做网络请求，ASIHTTPRequest就是基于CFNetwotk做的封装
>- 首先需要制定埋点需求、设计埋点方案，需求有了后就去找开发沟通，埋点需求的讨论，双方确认需求通过后，然后准备需求文档，开发再根据文档来写具体的埋点代码。 
>* 代码写完后，App打包给测试人员，测试人员进行测试，同时还要对埋点参数的数据安全审核，参数是否有包含公司敏感信息。


空行
<p> </p>


<br>

转载请注明：[康瑶明的博客](https://luckykang.github.io) 