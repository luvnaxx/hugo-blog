# Windows自动同步一个文件夹到另一目录

<纪念2008年汶川地震12周年>

Windows系统下实现两个文件夹自动同步问题（mklink命令的使用）

----

> 将**C:\Users\wyy\笔记**同步到**C:\Users\wyy\OneDrive\个人\笔记**

步骤:

1. 以管理员身份打开cmd
2. 输入mklink /d C:\Users\wyy\OneDrive\个人\笔记 C:\Users\wyy\笔记，回车
3. 当出现"为 C:\Users\wyy\笔记 «===» C:\Users\wyy\OneDrive\个人\笔记 创建的符号链接”,即为成功

> 注意:目标文件夹应创建到**C:\Users\wyy\OneDrive\个人**，**笔记**文件夹会自动创建。否则会出现**当文件已存在时，无法创建该文件**