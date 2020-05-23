# json字符串操作

不借助实体类，对json类型的字符串修改其内容

#### 原数据

```json
{
	"businessId": "e341a297849144568219713246952896",
	"customer": [{
		"name": "Jack",
		"phone": 12345678901,
		"address": {
			"country": "CN",
			"provence": "Shanghai",
			"county": "Pudong",
			"street": "xxx"
		}
	}],
	"businessMark": [{
		"businessId": "913200006891849616",
		"businessName": "Toy",
		"price": 1213.4
	}]
}
```

> 去除"customer"中的"address"中的"street"键值对，不使用实体映射



#### 目的

```json
{
	"businessId": "e341a297849144568219713246952896",
	"customer": [{
		"name": "Jack",
		"phone": 12345678901,
		"address": {
			"country": "CN",
			"provence": "Shanghai",
			"county": "Pudong"
		}
	}],
	"businessMark": [{
		"businessId": "913200006891849616",
		"businessName": "Toy",
		"price": 1213.4
	}]
}
```



#### 依赖

```xml
    <dependency>
      <groupId>com.alibaba</groupId>
      <artifactId>fastjson</artifactId>
      <version>1.2.58</version>
    </dependency>
```

#### 代码实现

```java
import com.alibaba.fastjson.JSON;
import com.alibaba.fastjson.JSONArray;
import com.alibaba.fastjson.JSONObject;
import java.util.Map;

/**
 * json类型字符串修改内容
 * <p>
 * 去除"customer"中的"address"中的"street"键值对，不使用实体映射
 *
 * @author 王越洋
 */
public class Str2JSON {

  public static void main(String[] args) {

    // 字符串类型的json数据
    String jsonData = "{\"businessId\":\"e341a297849144568219713246952896\",\"customer\":[{\"name\":\"Jack\",\"phone\":12345678901,\"address\":{\"country\":\"CN\",\"provence\":\"Shanghai\",\"county\":\"Pudong\",\"street\":\"xxx\"}}],\"businessMark\":[{\"businessId\":\"913200006891849616\",\"businessName\":\"Toy\",\"price\":1213.4}]}";

    JSONObject jsonObject;
    try {
      jsonObject = JSONObject.parseObject(jsonData);

      JSONArray customer = jsonObject.getJSONArray("customer");
      Map<String, Object> fileInfoMap = (Map<String, Object>) customer.get(0);

      Map<String, Object> address = (Map<String, Object>) fileInfoMap.get("address");
      address.remove("street");

      Map<String, Object> map = jsonObject;
      //map转字符串
      String jsonString = JSON.toJSONString(map);
      System.out.println(jsonString);
    } catch (Exception e) {
      e.printStackTrace();
    }
  }
}
```

