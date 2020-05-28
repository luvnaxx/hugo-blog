# JDK8:函数式编程

## stream

> 使用LongStream输出斐波那契数列

```java
import java.util.Arrays;
import java.util.Comparator;
import java.util.List;
import java.util.function.LongSupplier;
import java.util.stream.LongStream;

public class Main {

    public static void main(String[] args) {

        // 使用LongStream输出斐波那契数列
        LongStream longStream = LongStream.generate(new FibonacciSupplier());
        longStream.limit(5).forEach(System.out::println);
    }
}


class FibonacciSupplier implements LongSupplier {

    int a = 1;
    int b = 0;

    @Override
    public long getAsLong() {
        int sum = a + b;
        a = b;
        b = sum;
        return sum;
    }
}
```



----

## filter

> 使用filter过滤出及格的同学，然后打印名字

```java
package com.luvnaxx.simple;

import java.util.Arrays;
import java.util.List;
import lombok.AllArgsConstructor;
import lombok.Data;

public class Main {

    public static void main(String[] args) {

        List<Person> persons = Arrays
                        .asList(new Person("小明", 88), new Person("小黑", 62), new Person("小白", 45),
                                new Person("小黄", 78), new Person("小红", 99), new Person("小林", 58));
        // 请使用filter过滤出及格的同学，然后打印名字:
        persons.stream().filter(score -> score.getScore() >= 60).map(Person::getName)
                .forEach(System.out::println);
    }
}

@Data
@AllArgsConstructor
class Person {

    private String name;
    private Integer score;
}
```

