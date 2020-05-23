# Base64与文件的相关操作

> 使用java.util.Base64.Decoder和java.util.Base64.Encoder，替换sun.misc.BASE64Decoder和sun.misc.BASE64Encoder

```java
    /**
     * BASE64Encoder 加密
     *
     * @param data 要加密的数据
     * @return 加密后的字符串
     */
    public static String encryptBASE64(byte[] data) {
        // BASE64Encoder encoder = new BASE64Encoder();
        // String encode = encoder.encode(data);
        // 从JKD 9开始rt.jar包已废除，从JDK 1.8开始使用java.util.Base64.Encoder
        Encoder encoder = Base64.getEncoder();
        String encode = encoder.encodeToString(data);
        return encode;
    }

    /**
     * BASE64Decoder 解密
     *
     * @param data 要解密的字符串
     * @return 解密后的byte[]
     */
    public static byte[] decryptBASE64(String data) throws Exception {
        // BASE64Decoder decoder = new BASE64Decoder();
        // byte[] buffer = decoder.decodeBuffer(data);
        // 从JKD 9开始rt.jar包已废除，从JDK 1.8开始使用java.util.Base64.Decoder
        Decoder decoder = Base64.getDecoder();
        byte[] buffer = decoder.decode(data);
        return buffer;
    }
```

------

```java
public class Base64Util {

    /**
     * 通过文件路径将文件转成Base64编码
     *
     * @param path 文件路径
     * @return base64结果
     */
    public String fileToBase64(String path) {
        byte[] data = null;
        InputStream in = null;
        try {
            in = new FileInputStream(path);
            data = new byte[in.available()];
            in.read(data);
        } catch (IOException e) {
            e.printStackTrace();
        } finally {
            try {
                if (in != null) {
                    in.close();
                }
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
        Encoder encoder = Base64.getEncoder();
        return encoder.encodeToString(data);
    }

    /**
     * 将文件转成Base64编码
     *
     * @param file 文件
     * @return base64码
     */
    public String fileToBase64(File file) {
        byte[] data = null;
        InputStream in = null;
        try {
            in = new FileInputStream(file);
            data = new byte[in.available()];
            in.read(data);
        } catch (IOException e) {
            e.printStackTrace();
        } finally {
            try {
                if (in != null) {
                    in.close();
                }
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
        Encoder encoder = Base64.getEncoder();
        return encoder.encodeToString(data);
    }

    /**
     * byte[]字节转Base64编码
     *
     * @param bytes 数组字节
     * @return base64码
     */
    public String byteToBase64(byte[] bytes) {
        Encoder encoder = Base64.getEncoder();
        return encoder.encodeToString(bytes);
    }

    /**
     * 文件转byte[]数组
     *
     * @param file 文件
     * @return byte[]
     */
    public byte[] fileToByte(File file) {
        byte[] data = null;
        InputStream in = null;
        try {
            in = new FileInputStream(file);
            data = new byte[in.available()];
            in.read(data);
        } catch (IOException e) {
            e.printStackTrace();
        } finally {
            try {
                if (in != null) {
                    in.close();
                }
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
        return data;
    }

    /**
     * 对字节数组字符串进行Base64解码
     *
     * @param imgStr base64字符串
     * @return byte[]
     */
    public byte[] changeBase64(String imgStr) {
        Decoder decoder = Base64.getDecoder();
        byte[] bytes = null;
        try {
            bytes = decoder.decode(imgStr);
            for (int i = 0; i < bytes.length; ++i) {
                // 调整异常数据
                if (bytes[i] < 0) {
                    bytes[i] += 256;
                }
            }
        } catch (Exception e) {
            e.printStackTrace();
        }
        return bytes;
    }

    /**
     * 对字节数组字符串进行Base64解码并生成指定文件
     *
     * @param fileBase64  base64字符串
     * @param imgFilePath 指定输出路径
     * @return 布尔值
     */
    public boolean GenerateImage(String fileBase64, String imgFilePath) {
        if (fileBase64 == null) {
            return false;
        }
        OutputStream out = null;
        Decoder decoder = Base64.getDecoder();
        try {
            // Base64解码
            byte[] bytes = decoder.decode(fileBase64);
            for (int i = 0; i < bytes.length; ++i) {
                if (bytes[i] < 0) {// 调整异常数据
                    bytes[i] += 256;
                }
            }
            out = new FileOutputStream(imgFilePath);
            out.write(bytes);
            out.flush();
            return true;
        } catch (Exception e) {
            return false;
        } finally {
            try {
                if (out != null) {
                    out.close();
                }
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
    }
}
```