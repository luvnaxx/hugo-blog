# Maven打包将依赖放到指定位置

使用命令

```shell
mvn dependency:copy-dependencies -DoutputDirectory=yourPath  -DincludeScope=runtime
```

