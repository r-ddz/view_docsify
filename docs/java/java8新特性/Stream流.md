
## 特点
> Stream流属于管道流,只能被消费(使用)一次，
> 第一个Stream流调用完毕方法,数据就会流转到下一个Stream上，
> 而这时第一个Stream流已经使用完毕,就会关闭了，
> 所以第一个Stream流就不能再调用方法了,如果调用会报异常【IllegalStateException: stream has already been operated upon or closed】。

## 常用方法

### forEach 遍历
```java
import java.util.stream.Stream;

public class Demo {
    public static void main(String[] args) {
        //获取一个Stream流
        Stream<String> stream = Stream.of("张三", "李四", "王五", "赵六", "田七");
        //使用Stream流中的方法forEach对Stream流中的数据进行遍历
        stream.forEach(name -> System.out.println(name));
    }
}
```

### filter 过滤
```java
import java.util.stream.Stream;

public class Demo {
    public static void main(String[] args) {
        //创建一个Stream流
        Stream<String> stream = Stream.of("张三丰", "张翠山", "赵敏", "周芷若", "张无忌");
        //对Stream流中的元素进行过滤,只要姓张的人
        Stream<String> stream2 = stream.filter(name -> name.startsWith("张"));
        //遍历stream2流
        stream2.forEach(name -> System.out.println(name));
    }
}
```

### map 映射
```java
import java.util.stream.Stream;

public class Demo {
    public static void main(String[] args) {
        //获取一个String类型的Stream流
        Stream<String> stream = Stream.of("1", "2", "3", "4");
        //使用map方法,把字符串类型的整数,转换(映射)为Integer类型的整数
        Stream<Integer> stream2 = stream.map(s -> Integer.parseInt(s));
    }
}
```


### count 统计Stream流中元素的个数
```java
import java.util.ArrayList;
import java.util.List;
import java.util.stream.Stream;

public class Demo {
    public static void main(String[] args) {
        //获取一个Stream流
        List<Integer> list = new ArrayList<>();
        list.add(1);
        list.add(2);
        list.add(3);
        list.add(4);
        list.add(5);
        list.add(6);
        list.add(7);
        Stream<Integer> stream = list.stream();
        long count = stream.count();
        System.out.println(count);//7
    }
}
```

### limit 截取流里的前 n 个元素
```java
import java.util.stream.Stream;

public class Demo {
    public static void main(String[] args) {
        //获取一个Stream流
        String[] arr = {"美羊羊","喜洋洋","懒洋洋","灰太狼","红太狼"};
        Stream<String> stream = Stream.of(arr);
        //使用limit对Stream流中的元素进行截取,只要前3个元素
        Stream<String> stream2 = stream.limit(3);
    }
}
```

### skip 跳过流里的前 n 个元素
```java
import java.util.stream.Stream;

public class Demo {
    public static void main(String[] args) {
        //获取一个Stream流
        String[] arr = {"美羊羊", "喜洋洋", "懒洋洋", "灰太狼", "红太狼"};
        Stream<String> stream = Stream.of(arr);
        //使用skip方法跳过前3个元素
        Stream<String> stream2 = stream.skip(3);
        //遍历stream2流
        stream2.forEach(name -> System.out.println(name));
    }
}
```

### concat 合并流
```java
import java.util.stream.Stream;

public class Demo {
    public static void main(String[] args) {
        //创建一个Stream流
        Stream<String> stream1 = Stream.of("张三丰", "张翠山", "赵敏", "周芷若", "张无忌");
        //获取一个Stream流
        String[] arr = {"美羊羊", "喜洋洋", "懒洋洋", "灰太狼", "红太狼"};
        Stream<String> stream2 = Stream.of(arr);
        //把以上两个流组合为一个流
        Stream<String> concat = Stream.concat(stream1, stream2);
    }
}
```

## 获取 Stream 流的方式

```java
import java.util.*;
import java.util.stream.Stream;

public class Demo {
    /**
     * java.util.stream.Stream<T>是Java 8新加入的最常用的流接口。（这并不是一个函数式接口。）;获取一个流非常简单，有以下几种常用的方式：
     * 方式一：所有的Collection集合都可以通过stream默认方法获取流；default Stream<E> stream​()
     * 方式二：Stream接口的静态方法of可以获取数组对应的流。static <T> Stream<T> of​(T... values);参数是一个可变参数,那么我们就可以传递一个数组
     */
    public static void main(String[] args) {
        //把集合转换为Stream流
        List<String> list = new ArrayList<>();
        Stream<String> stream1 = list.stream();

        Set<String> set = new HashSet<>();
        Stream<String> stream2 = set.stream();

        Map<String, String> map = new HashMap<>();
        //获取键,存储到一个Set集合中
        Set<String> keySet = map.keySet();
        Stream<String> stream3 = keySet.stream();

        //获取值,存储到一个Collection集合中
        Collection<String> values = map.values();
        Stream<String> stream4 = values.stream();

        //获取键值对(键与值的映射关系 entrySet)
        Set<Map.Entry<String, String>> entries = map.entrySet();
        Stream<Map.Entry<String, String>> stream5 = entries.stream();

        //把数组转换为Stream流
        Stream<Integer> stream6 = Stream.of(1, 2, 3, 4, 5);
        //可变参数可以传递数组
        Integer[] arr = {1, 2, 3, 4, 5};
        Stream<Integer> stream7 = Stream.of(arr);
        String[] arr2 = {"a", "bb", "ccc"};
        Stream<String> stream8 = Stream.of(arr2);
    }
}
```