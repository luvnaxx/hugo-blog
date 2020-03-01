# 在Windows平台搭建hugo开发平台



## 安装hugo

1. 安装Chocolatey

   以管理员命令运行cmd命令行(powershell)，输入

   ```powershell
   Set-ExecutionPolicy Bypass -Scope Process -Force; iex ((New-Object System.Net.WebClient).DownloadString('https://chocolatey.org/install.ps1')); choco feature enable -n allowGlobalConfirmation
   ```

   完成之后，输入choco，会显示版本

2. 安装完成choco之后，继续在命令行执行

   ```
   choco install hugo -confirm
   ```
   
   成功后，执行hugo version。会显示hugo版本号



## 关联github与本地的hugo目录

1. 在github创建一个空白仓库，clone到本地

2. 进入clone下来的目录，**git bash here**打开窗口，执行

   ```shell
   hugo new site .
   ```

3. 修改配置文件config.toml(使用Jane主题样例配置)

   ```toml
   # This is a simple configuration.
   # If you want to see the full configuration, please check `full-config.toml` .
   # And see https://gohugo.io/getting-started/configuration/ .
   
   baseURL = "https://blog.luvnaxx.com"	# 如果没有自定义域名的需求，此处为空
   title = "Jane - A super concise theme for Hugo"
   publishDir = "docs"
   enableRobotsTXT = true
   enableEmoji = true
   theme = "jane"
   
   hasCJKLanguage = true     # has chinese/japanese/korean ? # 自动检测是否包含 中文\日文\韩文
   paginate = 5              # Number of articles displayed on the homepage  # 首页每页显示的文章数
   rssLimit = 20             # Limit Entry Count to Rss file # 限制 Rss 文章输出数量
   # disqusShortname = ""      # disqus_shortname
   # googleAnalytics = ""      # UA-XXXXXXXX-X
   copyright = "luvnaxx.com"            # default: author.name ↓        # 默认为下面配置的author.name ↓
   
   # language support # en / zh-cn / other... translations present in i18n/
   defaultContentLanguage = "en"           # Default language to use
   [languages.en]
     languageCode = "en"
   
   [author]                  # essential                     # 必需
     name = "luvnaxx"
   
   [sitemap]                 # essential                     # 必需
     changefreq = "weekly"
     priority = 0.5
     filename = "sitemap.xml"
   
   [[menu.main]]             # config your menu              # 配置目录
     name = "This is Home"
     weight = 10
     identifier = "home"
     url = "/"
   [[menu.main]]
     name = "Archives"
     weight = 20
     identifier = "archives"
     url = "/post/"
   [[menu.main]]
     name = "Tags"
     weight = 30
     identifier = "tags"
     url = "/tags/"
   [[menu.main]]
     name = "Categories"
     weight = 40
     identifier = "categories"
     url = "/categories/"
   [[menu.main]]
     name = "external-link"
     weight = 50
     url = "https://github.com/luvnaxx/hugo-blog"
   
   
   [params]
     since = "2019"            # Site creation time          # 站点建立时间
     homeFullContent = false   # if false, show post summaries on home page. Othewise show full content.
     rssFullContent = true     # if false, Rss feed instead of the summary
   
     # site info (optional)                                  # 站点信息（可选，不需要的可以直接注释掉）
     logoTitle = "Jane"        # default: the title value    # 默认值: 上面设置的title值
     keywords = ["knowledge", "life"]
     description = "luvnaxx's blog, build by Hugo, theme jane."
   
     # The date format to use; for a list of valid formats, see https://gohugo.io/functions/format/
     dateFormatToUse = "2006-01-02"
   
     # 一些全局开关，你也可以在每一篇内容的 front matter 中针对单篇内容关闭或开启某些功能，在 archetypes/default.md 查看更多信息。
     # Some global options, you can also close or open something in front matter for a single post, see more information from `archetypes/default.md`.
     toc = true                                                                            # 是否开启目录
     photoswipe = true         # see https://github.com/dimsemenov/PhotoSwipe            # 是否启用PhotoSwipe（图片可点击）
     # contentCopyright = '<a rel="license noopener" href="https://creativecommons.org/licenses/by-nc-nd/4.0/" target="_blank">CC BY-NC-ND 4.0</a>'
   
     # Link custom CSS and JS assets
     #   (relative to /static/css and /static/js respectively)
     customCSS = []            # if ['custom.css'], load '/static/css/custom.css' file
     customJS = []             # if ['custom.js'], load '/static/js/custom.js' file
   
     # [params.social]                                         # 社交链接
     #   a-email = "mailto:your@email.com"
     #   b-stack-overflow = "http://localhost:1313"
     #   c-twitter = "http://localhost:1313"
     #   d-facebook = "http://localhost:1313"
     #   e-linkedin = "http://localhost:1313"
     #   f-google = "http://localhost:1313"
     #   g-github = "http://localhost:1313"
     #   h-weibo = "http://localhost:1313"
     #   i-zhihu = "http://localhost:1313"
     #   j-douban = "http://localhost:1313"
     #   k-pocket = "http://localhost:1313"
     #   l-tumblr = "http://localhost:1313"
     #   m-instagram = "http://localhost:1313"
     #   n-gitlab = "http://localhost:1313"
     #   o-goodreads = "http://localhost:1313"
     #   p-coding = "http://localhost:1313"
     #   q-bilibili = "http://localhost:1313"
     #   r-codeforces = "http://localhost:1313"
     #   s-mastodon = "http://localhost:1313"
   ```

   修改**publishDIR**设置为**docs**以便发布在*GitHub Pages*中



## 安装主题

进入[主题网站](https://themes.gohugo.io/)选择，我选择的是[Jane](https://github.com/xianmin/hugo-theme-jane)，下载完成之后会在**themeS**文件夹下有个对应的文件夹，上述配置文件的`theme = "jane"`设置为和文件夹名称相同。



## 编写文章并发布

`git bash`执行`hugo new 你好.md`，会在**content**下产生**你好.md**文件。运行`hugo server`，运行本地服务器预览。



## 发布到github

```shell
hugo -t jane
```

将生成的**docs**文件夹上传，进入项目的设置中设置即可

---

## 遇到的问题

#### 自定义域名失效

> 在settings中设置域名并成功解析，能够通过域名访问。但是每次更新文章，重新push之后，自定义域名就失效了。

**原因: **生成的**docs**文件夹中，没有了**CNAME**文件。

**解决方案:** 将**CNAME**文件复制一份到**static**文件夹中。此时生成的**docs**文件夹中就会出现**CNAME**文件，再`push`即可