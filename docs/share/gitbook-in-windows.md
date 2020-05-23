# 在Windows平台搭建hugo开发平台

### 安装Gitbook

#### 一：本地安装Gitbook

1. 在本地安装Nodejs，完成之后在命令行输入`npm install gitbook-cli -g`。
2. 在GitHub中创建新的仓库，设置为public并且勾选README.md
3. clone项目至本地目录
4. 在命令行中进入仓库所在本地目录，输入`gitbook init`，如果是第一次会安装GitBook。初始化之后会出现*SUMMARY.md* 文件
5. 命令行输入`gitbook serve`启动本地服务，浏览器输入[localhost:4000](访问地址)即可实时预览
6. 命令行中键入`gitbook build . docs`，将文件都编译到 *docs* 目录下

#### 二：发布到GitHub的page中

1. 将修改后的文件**PUSH**到远程仓库
2. 进入仓库的设置界面，在**GitHub Pages** → **Source**中选择*master branch/docs folder*
3. 访问生成的地址即可

#### 三：安装插件

1. 在项目根目录下添加`book.json`文件，添加插件
2. 执行`gitbook install`，或者使用`npm install` 插件名称进行安装



## 发布过程中遇到的问题

1.遇到如此提示：

![error][1]

解决方法：用户目录下找到以下文件。
*${USER_HOME}\.gitbook\versions\3.2.3\lib\output\website\copyPluginAssets.js*，将里面的`confirm: true`改为`confirm: false`，应该是有两个



## 自定义域名

生成的gitbook默认访问地址是`{username}.github.io`，可以自定义域名。

[1]: https://blog-1252667810.cos.ap-shanghai.myqcloud.com/image/201910/8dfb512b09ea46d59b87c998a9bf2fbb.png

