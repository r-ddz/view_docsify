
## 介绍
> Lambda表达式，是匿名内部类的简写。

## 特点
1. 方法的参数或局部变量类型必须为接口才能使用Lambda；
2. 接口中有且仅有一个抽象方法；【建议接口上加  @FunctionalInterface 注解来确保这是一个函数式接口】

## Lambda表达式的格式
> Lambda表达式的标准格式
```
(参数类型 参数名称) -> {
    代码体;
}
```
> Lambda表达式的省略格式
1. 小括号内参数的类型可以省略；
2. 如果小括号内有且仅有一个参数，则小括号可以省略；
3. 如果大括号内有且仅有一个语句，可以同时省略大括号、return关键字及语句分号；

## Lambda表达式与匿名内部类的写法对比
```java
public class Demo {
    public static void main(String[] args) {
        // 匿名内部类
        new Thread(new Runnable() {
            @Override
            public void run() {
                System.out.println("匿名内部类线程执行");
            }
        }).start();

        // Lambda表达式
        new Thread(() -> System.out.println("Lambda表达式线程执行")).start();
    }
}
```

## 常用的内置函数式接口

### Supplier<T>
> 供给型接口，通过 Supplier 接口中的 get 方法可以得到一个值，无参有返回值的接口。

*样例*
```java
import java.util.Arrays;
import java.util.function.Supplier;

public class Demo {
    public static void main(String[] args) {
        // 使用 Lambda 表达式与常用内置函数式接口 Supplier 返回数组元素最大值
        testSupplier(() -> {
            int[] arr = {1,2,3,4,5,99};
            Arrays.sort(arr); // 升序排序
            return arr[arr.length - 1];
        });
    }

    public static void testSupplier(Supplier<Integer> supplier) {
        int max = supplier.get();
        System.out.println("max = " + max);
    }
}
```

### Consumer<T>
> 消费型接口，可以拿到 accept 方法参数传递过来的数据进行处理，有参无返回值的接口。

*样例*
```java
import java.util.function.Consumer;

public class Demo {
    public static void main(String[] args) {
        // 使用 Lambda 表达式与常用内置函数式接口 Consumer 将一个字符串转大写
        testConsumer(str -> System.out.println(str.toUpperCase()));
    }

    public static void testConsumer(Consumer<String> consumer) {
        consumer.accept("Hello World");
    }
}
```

### Function<T, R>
> 转换型接口，对 apply 方法传入的 T 类型数据进行处理，返回 R 类型的结果，有参有返回值的接口。

*样例*
```java
import java.util.function.Function;

public class Demo {
    public static void main(String[] args) {
        // 使用 Lambda 表达式与常用内置函数式接口 Function 将字符串转数字
        testFunction(str -> Integer.parseInt(str));
    }

    public static void testFunction(Function<String, Integer> function) {
        Integer num = function.apply("10");
        System.out.println("num = " + num);
    }
}
```

### Predicate<T>
> 判断型接口，对 test 方法的参数 T 进行判断，返回 boolean 类型的结果，用于条件判断的场景。

*样例*
```java
import java.util.function.Predicate;

public class Demo {
    public static void main(String[] args) {
        // 使用 Lambda 表达式与常用内置函数式接口 Predicate 判断字符串长度 > 3
        testPredicate(str -> str.length() > 3);
    }

    public static void testPredicate(Predicate<String> predicate) {
        boolean isLong = predicate.test("ABCD");
        System.out.println("字符串长度是否 > 3 ： " + isLong);
    }
}
```

## 原理
> 在程序运行的时候会生成一个匿名内部类的 class 文件，Lambda 表达式实际上调用的就是这个匿名内部类，这个匿名内部类就是实现了接口的唯一方法，并且在当前执行类里生成了一个匿名的静态方法，Lambda表达式里的代码逻辑就全在这里了，这个匿名的静态方法会被匿名内部类所调用。【而正常的匿名内部类则是在编译的时候就生成了 class 文件，这也是两者的区别】

