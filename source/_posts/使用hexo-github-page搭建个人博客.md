---
title: 使用hexo + github page搭建个人博客
date: 2020-08-22 15:57:19
tags: 搭建博客
---

# 安装Node.js

首先下载稳定版[Node.js](https://nodejs.org/dist/v9.11.1/node-v9.11.1-x64.msi)，我这里给的是64位的。

安装选项全部默认，一路点击`Next`。

最后安装好之后，按`Win+R`打开命令提示符，输入`node -v`和`npm -v`，如果出现版本号，那么就安装成功了。

# 添加国内镜像源

如果没有梯子的话，可以使用阿里的国内镜像进行加速。



```bash
npm config set registry https://registry.npm.taobao.org
```

# 安装Git

为了把本地的网页文件上传到github上面去，我们需要用到分布式版本控制工具————Git[[下载地址\]](https://git-scm.com/download/win)。

安装选项还是全部默认，只不过最后一步添加路径时选择`Use Git from the Windows Command Prompt`，这样我们就可以直接在命令提示符里打开git了。

安装完成后在命令提示符中输入`git --version`验证是否安装成功。

<!--more-->

# 注册Github账号

接下来就去注册一个github账号，用来存放我们的网站。大多数小伙伴应该都有了吧，作为一个合格的程序猿（媛）还是要有一个的。

打开https://github.com/，新建一个项目，如下所示：

然后如下图所示，输入自己的项目名字，后面一定要加`.github.io`后缀，README初始化也要勾上。**名称一定要和你的github名字完全一样，比如你github名字叫`abc`，那么仓库名字一定要是`abc.github.io`。**

# 安装Hexo

在合适的地方新建一个文件夹，用来存放自己的博客文件，比如我的博客文件都存放在`D:\study\program\blog`目录下。

在该目录下右键点击`Git Bash Here`，打开git的控制台窗口，以后我们所有的操作都在git控制台进行，就不要用Windows自带的控制台了。

定位到该目录下，输入`npm i hexo-cli -g`安装Hexo。会有几个报错，无视它就行。

安装完后输入`hexo -v`验证是否安装成功。

然后就要初始化我们的网站，输入`hexo init`初始化文件夹，接着输入`npm install`安装必备的组件。

这样本地的网站配置也弄好啦，输入`hexo g`生成静态网页，然后输入`hexo s`打开本地服务器，然后浏览器打开http://localhost:4000/，就可以看到我们的博客啦，效果如下：

# 安装Hexo

在合适的地方新建一个文件夹，用来存放自己的博客文件，比如我的博客文件都存放在`D:\study\program\blog`目录下。

在该目录下右键点击`Git Bash Here`，打开git的控制台窗口，以后我们所有的操作都在git控制台进行，就不要用Windows自带的控制台了。

定位到该目录下，输入`npm i hexo-cli -g`安装Hexo。会有几个报错，无视它就行。

安装完后输入`hexo -v`验证是否安装成功。

然后就要初始化我们的网站，输入`hexo init`初始化文件夹，接着输入`npm install`安装必备的组件。

这样本地的网站配置也弄好啦，输入`hexo g`生成静态网页，然后输入`hexo s`打开本地服务器，然后浏览器打开http://localhost:4000/，就可以看到我们的博客啦，效果如下：

![img](D:\WorkSpace\blog\source\_posts\5.jpg)


# 写文章、发布文章

首先在博客根目录下右键打开git bash，安装一个扩展`npm i hexo-deployer-git`。

然后输入`hexo new post "article title"`，新建一篇文章。

然后打开`D:\study\program\blog\source\_posts`的目录，可以发现下面多了一个文件夹和一个`.md`文件，一个用来存放你的图片等数据，另一个就是你的文章文件啦。

编写完markdown文件后，根目录下输入`hexo g`生成静态网页，然后输入`hexo s`可以本地预览效果，最后输入`hexo d`上传到github上。这时打开你的github.io主页就能看到发布的文章啦。



# 绑定域名

现在默认的域名还是`xxx.github.io`，是不是很没有牌面？想不想也像我一样弄一个专属域名呢，首先你得购买一个域名，xx云都能买，看你个人喜好了。

以我的百度云为例，如下图所示，添加两条解析记录：

然后打开你的github博客项目，点击`settings`，拉到下面`Custom domain`处，填上你自己的域名，保存：

这时候你的项目根目录应该会出现一个名为`CNAME`的文件了。如果没有的话，打开你本地博客`/source`目录，我的是`D:\study\program\blog\source`，新建`CNAME`文件，注意没有后缀。然后在里面写上你的域名，保存。最后运行`hexo g`、`hexo d`上传到github。


# 备份博客源文件

有时候我们想换一台电脑继续写博客，这时候就可以将博客目录下的所有源文件都上传到github上面。

首先在github博客仓库下新建一个分支`hexo`，然后`git clone`到本地，把`.git`文件夹拿出来，放在博客根目录下。

然后`git checkout hexo`切换到`hexo`分支，然后`git add .`，然后`git commit -m "xxx"`，最后`git push origin hexo`提交就行了。

当然我新建了一个仓库存放了源文件，具体效果可以看我的博客源文件仓库：https://github.com/godweiyang/hexo-matery-modified。大家也可以先用下文hexo安装方法安装完hexo，然后直接`git clone git@github.com:godweiyang/hexo-matery-modified.git`克隆我的所有源文件，然后这是我修改完的基本没bug的定制化的博客，就可以直接拿来用啦。

**我这个源文件和原来的主题没有什么区别，只是我把插件都安装完了，有些小bug也修复了，所以拿来就能直接用，方便大家。**



# 博客源代码下载

如果大家不想这么麻烦的装一堆东西，然后还要自己修改bug，那么只需要做好上面的准备工作，然后下载我的源代码，改改个人配置就行了。

源码地址：https://github.com/godweiyang/hexo-matery-modified。大家可以直接下载下来使用，修改个人配置信息即可，当然环境要先搭好。

**如果大家下载好了源代码，就可以直接使用了，基本可以跳过文章后面的部分了！喜欢的记得star并fork哦！**

# 个性化设置（matery主题）

下面的个性化设置主要针对的是`matery`主题，如果你想用我现在博客这个主题，可以直接看这个章节。

## 更换主题

这两天花时间将我的博客换了一个主题，现在这个主题看着更加的炫（zhuang）酷（bi），并且响应式更友好，点起来就很舒服，功能也多很多。

主题的原地址在这里：[hexo-theme-matery](https://github.com/blinkfox/hexo-theme-matery)，它的文档写得也非常的详细，还有中英文两个版本，作者回复也很及时。效果图如下，可以看出非常合我的口味：



## 文章开启阅读更多按钮

如果不开启阅读更多按钮的话，默认是展示文章中所有内容的，这显然体验不好。

一般都会在文章中插入`<!--more-->`这种注释形式表示首页展示到注释处为止。