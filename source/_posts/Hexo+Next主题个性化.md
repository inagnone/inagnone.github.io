---
title: Hexo+Next主题样式自定义
date: 2020-03-30 00:22:05
categories: 
- 前段
tags: 
- HEXO
- Next
---

记录hexo+next下主题自定义样式的修改

<!-- more -->

# 首页

## 文章缩略

在文章中插入`<!-- more -->`,文章在首页中将会被截断，只显示开头到`<!-- more -->`之间的内容，达到缩略的效果

## 文章间距

文件``\themes\next\source\css\_schemes\Mist\_posts-expanded.styl``

```css
.posts-expand .post {
  margin-top: 30px;
  margin-bottom: 30px;
}
```



# 文章页样式

修改文件`theme/next/source/css/_custom/custom.styl`

## 页面添加背景图片

```````css
body{   
        background:url(图片链接);
        background-size:cover;
        background-repeat:no-repeat;
        background-attachment:fixed;
        background-position:center;
}
```````

图片链接是相对custom.styl文件的相对路径

## 页面头部和底部栏背景色设置

````css
.header {
    background:rgba(颜色rgb,透明度) none repeat scroll !important;
}
.footer {
    background:rgba(颜色rgb,透明度) none repeat scroll !important;
}
````

## 文章背景色及半透明设置

```````css
.content {
    border-radius: 10px;#设置文章背景圆角
    margin-top: 60px;
    background:rgba(颜色rgb,透明度) none repeat scroll !important;
}
```````

## 文章内单行代码样式

`````css
#posts code {
    color: 颜色代码;#字体颜色
    background-color:颜色代码;#背景颜色
}
`````
>### 代码块样式修改
>
>主题配置文件
>更改highlight_theme字段
`````yaml
# Code Highlight theme
# Available value:
#    normal | night | night eighties | night blue | night bright
# https://github.com/chriskempson/tomorrow-theme
highlight_theme: night bright
`````



## 文章页面宽度设置

````css
.main-inner {width: 页面宽度;}
````

## 文章添加阴影效果

````css
.post {
   margin-top: 60px;
   margin-bottom: 60px;
   padding: 25px;
   -webkit-box-shadow: 0 0 5px rgba(202, 203, 203, .5);
   -moz-box-shadow: 0 0 5px rgba(202, 203, 204, .5);
}
````



## 网页顶部和底部文字

- 顶部文字

>1. `theme/next/source/css/_common/components/header/site-meta.styl`
>
>   .brand{}中设置color属性
>
>   ```
>   .brand {
>     position: relative;
>     display: inline-block;
>     padding: 0 40px;
>   //  color: $brand-color;
>     color: #fff !important;
>     background: $brand-bg;
>     border-bottom: none;
>     &:hover { color: $brand-hover-color; }
>   }
>   ```
>
>   
>
>2. `theme/next/source/css/_common/components/header/menu.styl`
>
>   a{}中设置color属性
>
>   ```css
>   a {
>       display: block;
>       font-size: 13px;
>       line-height: inherit;
>       border-bottom: 1px solid $menu-link-border;
>       transition-property: border-color;
>       the-transition();
>   	color:	#fff !important;
>       &:hover { border-bottom-color: $menu-link-hover-border; }
>     }
>   ```
>
>   

- 底部字体

````css
.footer-inner {color: 颜色代码;}
````



# 文章字体

修改文件``theme/next/source/css/_common/scaffolding/base.styl``

````css
body {
  position: relative; /* Required by scrollspy */
  font-family: $font-family-base;
  font-size: $font-size-base;
  line-height: $line-height-base;
  color: 颜色代码;
  background: $body-bg-color;
  +mobile() { padding-right: 0 !important; }
  +tablet() { padding-right: 0 !important; }
  +desktop-large() { font-size: $font-size-large; }
}
````

# Mist风格侧边栏样式

## 侧边栏位置

1. 文件`next/source/js/src/motion.js`

   把所有`PaddingRight`更改为`paddingLeft`（有2处）

   ```js
   {paddingLeft: SIDEBAR_WIDTH},
   NexT.utils.isDesktop() && $('body').velocity('stop').velocity({paddingLeft: 0});
   ```

2. 文件`next/source/css/_common/components/sidebar/sidebar.styl`

   将`.sidebar{}`内的`right: 0;`改为`left: 0;`

   ````css
   .sidebar {
       position: fixed;
       left: 0;
   }
   ````

3. 文件`next/source/css/_common/components/sidebar/sidebar-toggle.styl`

   将`.sidebar-toggle{}`内的`right: $b2t-position-right;`改为`left: $b2t-position-right;`

   ````css
   .sidebar-toggle {
       position: fixed;
       left: $b2t-position-right;
   }
   ````

   

4. 文件`next/source/css/_common/components/back-to-top.styl`

   将`.back-to-top {}`内的`right: $b2t-position-right;`改为`left: $b2t-position-right;`

   ````css
   .back-to-top {
       box-sizing: border-box;
       position: fixed;
       bottom: $b2t-position-bottom;
       left: $b2t-position-right;
   }
   ````

   ## 作者头像旋转

   文件`\themes\next\source\css\_common\components\sidebar\sidebar-author.styl`

   ````css
   .site-author-image {
     display: block;
     margin: 0 auto;
     padding: $site-author-image-padding;
     max-width: $site-author-image-width;
     height: $site-author-image-height;
     border: $site-author-image-border-width solid $site-author-image-border-color;
   
     /* 头像圆形 */
     border-radius: 80px;
     -webkit-border-radius: 80px;
     -moz-border-radius: 80px;
     box-shadow: inset 0 -1px 0 #333sf;
   
     /* 设置循环动画 [animation: (play)动画名称 (2s)动画播放时长单位秒或微秒 (ase-out)动画播放的速度曲线为以低速结束 
       (1s)等待1秒然后开始动画 (1)动画播放次数(infinite为循环播放) ]*/
    
   
     /* 鼠标经过头像旋转360度 */
     -webkit-transition: -webkit-transform 1.0s ease-out;
     -moz-transition: -moz-transform 1.0s ease-out;
     transition: transform 1.0s ease-out;
   }
   
   img:hover {
     /* 鼠标经过停止头像旋转 
     -webkit-animation-play-state:paused;
     animation-play-state:paused;*/
   
     /* 鼠标经过头像旋转360度 */
     -webkit-transform: rotateZ(360deg);
     -moz-transform: rotateZ(360deg);
     transform: rotateZ(360deg);
   }
   
   /* Z 轴旋转动画 */
   @-webkit-keyframes play {
     0% {
       -webkit-transform: rotateZ(0deg);
     }
     100% {
       -webkit-transform: rotateZ(-360deg);
     }
   }
   @-moz-keyframes play {
     0% {
       -moz-transform: rotateZ(0deg);
     }
     100% {
       -moz-transform: rotateZ(-360deg);
     }
   }
   @keyframes play {
     0% {
       transform: rotateZ(0deg);
     }
     100% {
       transform: rotateZ(-360deg);
     }
   }
   ````

   # 参考博文

   >[http://mashirosorata.vicp.io/HEXO-NEXT%E4%B8%BB%E9%A2%98%E4%B8%AA%E6%80%A7%E5%8C%96%E9%85%8D%E7%BD%AE.html](http://mashirosorata.vicp.io/HEXO-NEXT%E4%B8%BB%E9%A2%98%E4%B8%AA%E6%80%A7%E5%8C%96%E9%85%8D%E7%BD%AE.html)
   >
   >[<https://segmentfault.com/a/1190000009544924#articleHeader21>](<https://segmentfault.com/a/1190000009544924#articleHeader21>)
   >
   >[<http://blog.csdn.net/MasterAnt_D/article/details/56839222#t50>](http://blog.csdn.net/MasterAnt_D/article/details/56839222#t50)
   >
   >[[https://thief.one/2017/03/03/Hexo%E6%90%AD%E5%BB%BA%E5%8D%9A%E5%AE%A2%E6%95%99%E7%A8%8B/](https://thief.one/2017/03/03/Hexo搭建博客教程/)](http://blog.csdn.net/MasterAnt_D/article/details/56839222#t50)
   
   

