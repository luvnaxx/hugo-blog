# Java异常语句打印

```java
StringWriter sw = new StringWriter();
PrintWriter ps = new PrintWriter(sw, true);
e.printStackTrace(ps);
System.out.println(sw.getBuffer().toString());
```

