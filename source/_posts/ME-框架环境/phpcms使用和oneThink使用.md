---
title: phpcms使用和oneThink使用
date: 2016-01-01T08:54:53.000Z
tags: phpcms
categories: 环境框架语言
---

--------------------------------------------------------------------------------

<!-- more -->

# oneThink简单而强大的内容管理框架,让WEB开发更快速!

> thinkphp网站搭建过程

## 准备开发

  下载官方软件v1.0正式版 http://www.onethink.cn/
  下载本地服务器wampserver http://www.wampserver.com/

## 开发步骤

* 解压v1.0安装包将`www`文件目录的所有文件copy到`C:\wamp64\www`或`C:\wamp32\www`目录中
* 启动wampserver等待指示灯有红变绿 黄色不行
* 进入http://localhost/phpmyadmin/index.php?token=6de2f4e9ae5b49d31d01b84dd98ed44d 账号root 密码为空 新建一个数据库后面需要用到
* 启动浏览器输入localhost执行安装步骤
> 如果一直无法进入下一步说明cookie出错了，打开`C:\wamp64\www\Application\Install\Controller\InstallController.class.php`注释掉92行和103行代码，如果还是不行请删除`C:\wamp64\www\Runtime`所有文件夹，如果不行 重复此步骤或者去百度。
> 执行第三步之后出现`"Access denied for user 'admin'@'localhost' (using password: YES)"`说明数据库用户名不对，wampserver默认用户名 root 密码不用填

## 部署阿里云

开发步骤与上面相同，数据库账号密码以及数据库名称统一更换为阿里云的账户名称。


### 其他后台模板安装与之一样


 1.将phpcmsv9.zip解压 然后将install_package 下面的所有东西拷贝到www目录 2.打开localhost/index.html 3.安装过程中 第3步选择全新安装 第5步选择 账户 root root root 空 email1@2.com 一个是L一个是1 4.后台管理 phpcms phpcms

phpcms的安装

1. 将phpcmsv9.zip解压 然后将install_package 下面的所有东西拷贝到www目录
2. 打开localhost/index.html
3. 安装过程中 第3步选择全新安装 第5步选择 账户 root root root 空 email1@2.com 一个是L一个是1
4. 后台管理 phpcms phpcms

后台入口：localhost/admin.php 前台入口：localhost/index.php

# phpcms使用

localhost/admin.php

## 什么是内容管理系统？

用来制作动态网站的一个系统 phpcmsv9 把index.php公开给用户去访问 index.php是一段程序 这段程序根据得到的参数决定把哪个页面发送给用户 这段程序会在web服务器上寻找html和脚本,图片，拼接起来发送给用户

## html文件在哪里?

phpcms/templates(模板)/default/content/

## 脚本和图片在哪里?

statics/css statics/js statics/image

```javascript
引入js,css,image
<link rel="stylesheet" href="{CSS_PATH}xxx/yyy.css">
<script src="{JS_PATH}xxx/yyy.js"></script>
<body>
    <div class="logo">
    <img src="{IMG_PATH}xxx/yyy.png" alt="">
    </div>
</body>
.log{
    background-image:url(/statics/images/xxx/a.png)
}
```

## 如果想改样式和结构怎么？

header.html+ index.html + footer.html 修改header.html body以下都注释掉

<style>
</style>

````html
<div class="1_header">
    <ul>
    <!-- nav-site -->
    {pc}
    {loop}
    <li><a href=""></a></li>
    <li><a href=""></a></li>
    {/pc}
    {/loop}
    </ul>
</div>
```html

### 栏目页( 有子栏目的栏目)
图片模型 category_picture.html
文章模型 category.html
商品模型 category_shangpin.html
教师模型 category_jiaoshi.html

### 栏目页(没有子栏目的栏目)
图片模型 list_picture.html
文章模型 list.html
商品模型 list_shangpin.html
教师模型 list_jiaoshi.html

### 内容页
图片模型 show_picture.html
文章模型 show.html
商品模型 show_shangpin.html
教师模型 show_jiaoshi.html  

## index.html -> category.html -> list.html -> show.html

```html
取一级栏目
{pc:content action="category" catid="0"}
{loop $data $r}
{/loop}
{/pc}
<!-- <div class="l_header">
    <ul>
        <li><a href="{siteurl($siteid)}">首页</a></li>
        {pc:content action="category" catid="0" num="25" siteid="$siteid" order="listorder ASC"}
        {loop $data $r}
        <li><a href="{$r[url]}">{$r[catname]}</a></li>
        {/loop}
        {/pc}
    </ul>
</div> -->
取某个一级栏目下的二级栏目
{pc:content action="category" catid="$catid"}
{loop $data $r}
{/loop}
{/pc}
<!-- <div class="main">
    <ul>
    {pc:content action="category" catid="$catid"}
    {loop $data $r}
    <li><a href="{$r[url]}"><span>{$r[catname]}</span></a></li>
    {/loop}
    {/pc}
    </ul>
</div> -->
取栏目下的内容
{pc:content action="lists" catid="$catid"}
{loop $data $r}
{/loop}
{/pc}
<!-- <div class="main">
    <ul>
        {pc:content action="lists" catid="$catid"}
        {loop $data $r}
        <li><a href="{$r[url]}"><span>{$r[title]}</span></a></li>
        {/loop}
        {/pc}
    </ul>
</div> -->
取内容中的标题和文章 {title} {content}
<!-- <div class="main">
        <div class="biaoti"><h1>{$title}</h1></div>        
        <div class="neirong"><span>{$content}</span></div>
</div> -->
````

从推荐位取东西 {pc:content action="position" posid="1"} {loop $data $r} {/loop} {/pc}

```
    {php print_r(get_defined_vars() )}  
    //输出页面中所有能使用的变量
```

```
    {php print_r($r)}
```

## 在每个页面中我们能使用哪些php变量

index.html $CATEGORYS;

category.html $CATEGORYS $catid= 点击的当前一级栏目的id list.html $CATEGORYS $catid= 点击的当前栏目的id show.html $CATEGORYS $catid= 这个内容属于的那个栏目的id 数据模型中的字段 {title} {content}

moreinfo="1" 取更多内容 thumb 缩略图 description 描述 {template "content","header"}

list {$r[title]} {$r[thumb]} {$r[url]} show {$title} {$url} {$thumb}

lis.hide().eq(this).show()

建立模型

1. 新建三个 category_xxx.html list_xxx.html show_xxx.html
2. 模型管理-->添加模型-->添加字段
3. 添加栏目 添加内容
4. pc atcion=lists moreinfo="1"

index.php => h+index+f

创建模型,添加字段

nav-list {$r[catdir]} {if $r[catid] == $catid}active{/if} {$r[catdir]}

catud="$CATEGORYS[catid]['parentid']} 取父元素

.center{ width:980px; margin:0 auto; boreder:1px solid black; } .center:after{ clear:both; display:block; } .header{ height:100px; background:red; }
