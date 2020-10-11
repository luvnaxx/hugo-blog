分组校验

1. 对需要分组校验的字段添加**groups**

   ![1][]

2. 使用`@Validated`代替`@Valid`

   ![2][]

3. 默认没有指定分组的校验注解，在分组校验情况`@Validated({AddGroup.class})`下不生效，只会在`@Validated`生效；

   ![3][]

![4][]
![5][]





[1]: https://blog-1252667810.cos.ap-shanghai.myqcloud.com/image/2020/09/f1cf9e73c7ea44f78f22228a0d645fdb.png
[2]: https://blog-1252667810.cos.ap-shanghai.myqcloud.com/image/2020/09/0173c5f9f89d440581f5977fa1d125fd.png
[3]: https://blog-1252667810.cos.ap-shanghai.myqcloud.com/image/2020/09/47a004df3492488a8dc454530cf699ba.png
[4]: https://blog-1252667810.cos.ap-shanghai.myqcloud.com/image/2020/09/5792e2e45fd9429db7f25f6e2fb9f56c.png
[5]: https://blog-1252667810.cos.ap-shanghai.myqcloud.com/image/2020/09/7b99117580e04e48bd4571d706fb67a2.png