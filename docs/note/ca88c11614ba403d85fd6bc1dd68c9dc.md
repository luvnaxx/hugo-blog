# 解决Linux下环境变量失效问题

很多时候，我们对环境变量的配置不是很熟悉的时候，使用`source`
`~/.bashrc`后很容易出现各种**xx: command not found**问题，下面给出一种解决方法。

在终端输入`export PATH=/bin:/usr/bin`，然后修改**~/.bashrc**删除或者修改刚刚写的环境变量，然后再重新`source ~/.bashrc`一下

