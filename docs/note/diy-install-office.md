# 自定义安装office

不安装office全家桶，自定义要安装的组件

<!--more-->

------

## 方法/步骤

1. 从官网下载[Office部署工具](https://www.microsoft.com/en-us/download/confirmation.aspx?id=49117)，虽然此工具是Office365的，但是无需担心。 

2. **安装/解压**该工具，会在我们指定的安装目录，生成以下的四个文件。这里我们只需要的**setup.exe**和**configuration-Office365-x64.xml**

3. 替换configuration-Office365-x64.xml文件中的内容

   ```xml
   <Configuration>
     <Add SourcePath="F:\" OfficeClientEdition="64" >
       <Product ID="ProPlusRetail">
         <Language ID="zh-CN" />
         <ExcludeApp ID="Access" />
         <ExcludeApp ID="Groove" />
         <ExcludeApp ID="InfoPath" />
         <ExcludeApp ID="Lync" />
         <ExcludeApp ID="OneNote" />
         <ExcludeApp ID="Outlook" />
         <ExcludeApp ID="Publisher" />
         <ExcludeApp ID="SharePointDesigner" />
       </Product>
     </Add>  
   </Configuration>
   ```

   > • OfficeClientEdition表示架构，安装32位则改为32。
   >
   > • SourcePath表示Office2016 ISO镜像加载位置；需提前加载镜像才会显示其加载的位置。
   >
   > • Language表示语言，zh-CN表示中文，英文则为en-us。
   >
   > • ExcludeApp表示不被安装的组件。

4. 进入第2步的安装目录，使用以下命令启动后Office的安装

   ```shell
   setup.exe /configure configuration-Office365-x64.xml
   ```

5. 可以看到这次只安装Word,Excel,PowerPoint三个软件，等待安装完成即可

   ![][1]



[1]: https://blog-1252667810.cos.ap-shanghai.myqcloud.com/image/2020/01/83d302a23aac4a23aebab84bb807e0a7.png

