# Java生成16位随机字符串

Java生成16位随机字符串

<!--more-->

```java
    /**
     * 产生16位随机值
     */
    public String generateRandomString() {
        StringBuilder uid = new StringBuilder();
        // 产生16位的强随机数
        Random rd = new SecureRandom();
        for (int i = 0; i < 16; i++) {
            // 产生0-2的3位随机数
            int type = rd.nextInt(3);
            switch (type) {
                case 0:
                    // 0-9的随机数
                    uid.append(rd.nextInt(10));
                    break;
                case 1:
                    // ASCII在65-90之间为大写,获取大写随机
                    uid.append((char) (rd.nextInt(25) + 65));
                    break;
                case 2:
                    // ASCII在97-122之间为小写，获取小写随机
                    uid.append((char) (rd.nextInt(25) + 97));
                    break;
                default:
                    break;
            }
        }
        return uid.toString();
    }
```

> 20200506
>
> 将for循环中的16改为任意数，即可生成任意位数的随机数

