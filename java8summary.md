PS：Lambda表达式即为内部类实现的简写形式。可理解为专注于业务的编写，忽略繁琐的类名、方法名的约束。但是也一定意义上降低代码所传递的信息量。

# Lambda表达式

Lambda需要使用一种新的语法元素和操作符，这个操作符为“->”，所以也叫箭头函数，这个操作符称为Lambda操作符。这个操作符将Lambda分为了两个部分

箭头左侧：指定了Lambda表法式需要的参数列表

箭头右侧：指定了Lambda体，是抽象方法的实现逻辑，也就是我们最需要关心的地方

准备工作：为了更好演示Lambda表达式，先给出5个接口，分别对应：无参无返回值，一个参数无返回值，两个参数(多个)无返回值，无参有返回值，一个参数有返回值

```java
public interface NoReturnNoParam {
    void method();
}
public interface NoReturnOneParam {
    void method(int a);
}
public interface NoReturnMutilParam {
    void method(int a , int b);
}
public interface ReturnNoParam {
    int method();
}
public interface ReturnOneParam {
    int method(int a);
}
```



```java
 @Test
    public void Test(){
        Lambda语法一：无参无返回值

        NoReturnNoParam nrnp = () -> { System.out.println("method方法执行了"); };
        nrnp.method();

        Lambda语法二：一个参数无返回值

        NoReturnOneParam nrop = (int a) -> { System.out.println(a + "\tmethod方法执行了"); };
        nrop.method(1);
				//也可以省略参数类型
        NoReturnOneParam nrop = (a) -> { System.out.println(a + "\tmethod方法执行了"); };
        nrop.method(1);
				//接着 可以把小括号去掉
        NoReturnOneParam nrop = a -> { System.out.println(a + "\tmethod方法执行了"); };
        nrop.method(1);
				//在Lambda体中只有一条语句，可以省略大括号
        NoReturnOneParam nrop = a -> System.out.println(a + "\tmethod方法执行了");
        nrop.method(1);

        Lambda语法三：多个参数无返回值

        NoReturnMutilParam nrmp = (int a , int b) -> { System.out.println(a + b + "\tmethod方法执行了"); };
        nrmp.method(1, 2);
				//可以省略参数类型
        NoReturnMutilParam nrmp = (a , b) -> { System.out.println(a + b + "\tmethod方法执行了"); };
        nrmp.method(1, 2);
        //这里就不能省略括号了，因为有两个参数
				//在Lambda体中只有一条语句，可以省略大括号
				
        Lambda语法四：无参有返回值

        ReturnNoParam rnp = () -> {return 1;};
        System.out.println(rnp.method());
        //Lambda体中只有一条语句，可以省略return与大括号
        ReturnNoParam rnp = () -> 1;
        System.out.println(rnp.method());


    }
```

# 函数式接口

使用的众多方法的形参中即为Function<T,R>最为常见

| 函数式接口              | 参数类型 | 返回类型 | 抽象方法          | 用途                                                   |
| ----------------------- | -------- | -------- | ----------------- | ------------------------------------------------------ |
| Consumer消费型接口      | T        | void     | void accept(T t)  | 对类型为T的对象应用操作                                |
| Supplier供给型接口      | 无       | T        | T get()           | 返回类型为T的对象                                      |
| Function<T,R>函数型接口 | T        | R        | R apply(T t)      | 对类型为T的对象应用操作，并返回结果，结果是R类型的对象 |
| Predicate 断定型接口    | T        | boolean  | boolean test(T t) | 确定类型为T的对象是否满足约束条件                      |

```java
 Consumer是消费型接口，有一个参数无返回类型，意味你给我东西消费

    public static void main(String[] args) {
        happyTime(100, money -> System.out.println("我消费了" + money));
    }

    public static void happyTime(double money, Consumer<Double> con){
        con.accept(money);
    }

    public static void main(String[] args) {
        List<String> list1 = Arrays.asList("天津", "北京", "南京", "东京", "西京");
        List<String> list = filterString(list1, str -> str.contains("京"));
        System.out.println(list);
    }

    public static List<String> filterString(List<String> list, Predicate<String> pred){
        List<String> res = new ArrayList<>();
        for(String str : list){
            if(pred.test(str)){
                res.add(str);
            }
        }
        return res;
    }
```



# Stream API（集合Lamba表达式与函数式接口与一身的使用实例）

Stream API的概述：

1）Java 8中有两大最重要的改变。第一个是Lambda表达式：另一个则是Stream API。

2）Stream API（java.util.stream）把真正的函数式编程风格引入到Java中。这是目前为止对Java类库最好的补充，因为Stream API可以极大提供Java程序员的生产力，让程序员写出<font color='red'>高效率、干净、简洁</font>的代码。

3）Stream关注的是对数据的运算，与CPU打交道，集合关注的是数据的存储，与内存打交道。

为什么使用Stream API：

Stream和Collection集合的区别：Collection是一种静态的内存数据结构，而Stream是有关计算的。前者是主要面向内存，存储在内存中，后者主要是面向CPU，通过CPU实现计算。

Stream的理解：

        Stream是数据渠道，用于操作数据源（集合、数组等）所生成的元素序列。“集合讲的是数据，Stream讲的是计算”。

<font color='red'>注意：</font>

1）Stream自己不会存储元素。

2）Stream不会改变源对象。相反，他们会返回一个持有结果的新Stream。

```java
Integer[] integers = new Integer[]{1,2,3,4,5};
List<Integer> collect = Arrays.stream(integers).filter(e -> e.equals(1)).collect(Collectors.toList());
System.out.println(Arrays.asList(integers));
System.out.println(collect);
```

<font color='blue'>3）Stream操作是延迟执行的。这意味着他们会等到需要结果的时候才执行。</font>

Stream操作的三个步骤：

1）创建Stream：一个数据源（如：集合数组），获取一个流，Stream的实例化

2）中间操作：一个中间操作链，对数据源的数据进行处理（如：过滤、映射等）

3）终止操作（终端操作）：一旦执行终止操作，就执行中间操作链，并产生结果。之后，就不会再被使用。



1-筛选与切片

| 方法                                         | 描述                                                         |
| -------------------------------------------- | ------------------------------------------------------------ |
| <font color='red'>filter(Predicate p)</font> | 接收Lambda，从流中排除某些元素                               |
| distinct()                                   | 筛选，通过流所生成元素的hashCode()和equals()去除重复的元素   |
| limit(long maxSize)                          | 截断流，使其元素不超过给定数量                               |
| skip(long n)                                 | 跳过元素，返回一个扔掉了前n个元素的流。若流中元素不足n个，则返回一个空流。与limit(n)互补 |

2-映射

| 方法                                     | 描述                                                         |
| ---------------------------------------- | ------------------------------------------------------------ |
| <font color='red'>map(Function f)</font> | 接收上一个函数作为参数，该函数会被应用到每个元素上，并将其映射成一个新的元素 |
| mapToDouble(ToDoubleFunction f)          | 接收一个函数作为参数，该函数会被应用到每个元素上，产生一个新的DoubleStream |
| mapToInt(ToIntFunction f)                | 接收一个函数作为参数，该函数会被应用到每个元素上，产生一个新的IntStream |
| mapToLong(ToLongFunction f)              | 接受一个函数作为参数，该函数会被应用到每个元素上，产生一个新的LongStream |



3-排序 

| **方法**               | **描述**                           |
| ---------------------- | ---------------------------------- |
| sorted()               | 产生一个新流，其中按自然顺序排序   |
| sorted(Comparator com) | 产生一个新流，其中按比例器顺序排序 |

Stream的终止操作：
1）终端操作会从流的流水线生成结果。其结果可以是任何不是流的值，例如：List、Integer。甚至是void。

2）流进行了终止操作后，不能再次使用。

1-匹配与查找

| 方法                                         | 描述                                                         |
| -------------------------------------------- | ------------------------------------------------------------ |
| allMatch(Predicate p)                        | 检查是否匹配所有元素                                         |
| anyMatch(Predicate p)                        | 检查是否至少匹配一个元素                                     |
| noneMatch(Predicate p)                       | 检查是否没有匹配所有元素                                     |
| findFirst()                                  | 返回第一个元素                                               |
| findAny()                                    | 返回当前流中的任意元素                                       |
| count()                                      | 返回流中元素总数                                             |
| max(Comparator c)                            | 返回流中最大值                                               |
| min(Comparator c)                            | 返回流中最小值                                               |
| <font color='red'>forEach(Consumer c)</font> | 内部迭代（使用Collection接口需要用户去做迭代，称为外部迭代。相反，Stream API使用内部迭代——它帮你把迭代做了）。 |

其中forEach()中使用return，返回到stream流当行继续执行后续业务代码，手写for循环中出现return，直接返回方法调用处，后续业务代码不会执行，可利用此特性根据业务需求进行适当使用。
2-归约

| **方法**                        | **描述**                                                |
| ------------------------------- | ------------------------------------------------------- |
| reduce(T iden,BinaryOperator b) | 可以将流中元素反复结合起来，得到一个值。返回T           |
| reduce(BinaryOperator b)        | 可以将流中元素反复结合起来，得到一个值。返回Optional<T> |

备注：map和reduce的连接通常称为map-reduce模式，因Google用它来进行网络搜索而出名。

3-收集

| **方法**                                      | **描述**                                                     |
| --------------------------------------------- | ------------------------------------------------------------ |
| <font color='red'>collect(Collector c)</font> | 将流转换成其他形式。接收一个Collector接口的实现，用于给Stream中元素做汇总的方法。 |

​        Collector接口中方法的实现决定了如何对流执行收集的操作（如收集到List、Set、Map）。

        另外，Collectors实用类提供了很多静态方法，可以方便地创建常见收集器实例，具体方法与实例如下表：

| 方法                                                         | 返回类型             | 作用                                      |
| ------------------------------------------------------------ | -------------------- | ----------------------------------------- |
| toList                                                       | List<T>              | 把流中元素收集到List                      |
| List<Employee> emps=list.stream().collect(Collectors.toList()); |                      |                                           |
| toSet                                                        | Set<T>               | 把流中元素收集到Set                       |
| Set<Employee> emps=list.stream().collect(Collectors.toSet()); |                      |                                           |
| toCollectiom                                                 | Collection<T>        | 把流中元素收集到创建地集合                |
| Collection<Employee> emps=listt.stream().collect(Collectors.toCollection(ArrayList::new)) |                      |                                           |
| counting                                                     | Long                 | 计算流中元素的个数                        |
| long count=list.stream().collect(Collectors.counting());     |                      |                                           |
| summingInt                                                   | Integer              | 对流中元素的整数属性求和                  |
| int total=list.stream().collect(Collectors.summingInt(Employee::getSalary)); |                      |                                           |
| averagingInt                                                 | Double               | 计算流中元素Integer属性的平均值           |
| double avg=list.stream().collect(Collectors.averagingInt(Employee::getSalary)); |                      |                                           |
| summarizingInt                                               | IntSummaryStatistics | 收集流流中Integer属性的统计值。如：平均值 |
| int SummaryStatisticsis=list.stream().collect(Collectors.summarizingInt(Employee::getSalary)); |                      |                                           |

```java
public class StreamToMapTest {
    public static void main(String[] args) {
        List<User> users = new ArrayList<>();
        User user1 = new User(1, "1");
        User user2 = new User(2, "2");
        User user3 = new User(3, "3");
        users.add(user1);
        users.add(user2);
        users.add(user3);
        // 将id属性读取出来组成list
        List<Integer> idList = users.stream().map(User::getId).collect(Collectors.toList());
        System.out.println(idList);

        // 将id属性读取出来组成list 并去重
        List<Integer> idList2 = users.stream().map(User::getId).distinct().collect(Collectors.toList());
        System.out.println(idList2);

        // 组装成map
        Map<Integer, String> mp1 = users.stream().collect(Collectors.toMap(User::getId, User::getName));
        System.out.println(mp1);

        User user3_1 = new User(3, "3-1");
        users.add(user3_1);

        // 组装成map
        // key 重复会报错
        // Map<Integer, String> mp2 = users.stream().collect(Collectors.toMap(User::getId, User::getName));
        // System.out.println(mp1);

        // 组装成map 值取前面的
        Map<Integer, String> mp3 = users.stream().collect(Collectors.toMap(User::getId, User::getName, (v1, v2) -> v1));
        System.out.println(mp3);

        // 组装成map 值取后面的
        Map<Integer, String> mp2 = users.stream().collect(Collectors.toMap(User::getId, User::getName, (v1, v2) -> v2));
        System.out.println(mp2);

        // 组装成map 值取累加
        Map<Integer, String> mp4 = users.stream().collect(Collectors.toMap(User::getId, User::getName, (v1, v2) -> v1+v2));
        System.out.println(mp4);

        // 根据id 组装list对象，对应id的对象成组
        Map<Integer, List<User>> mp5 = users.stream().collect(Collectors.groupingBy(User::getId));
        System.out.println(mp5);

        // 根据id 组装list对象，对应id的对象的某个属性成组
        Map<Integer,List<String>> mp6 = users.stream().collect(Collectors.groupingBy(User::getId, Collectors.mapping(User::getName, Collectors.toList())));
        System.out.println(mp6);

        Map<Integer, List<String>> mp7 = users.stream().collect(Collectors.toMap(User::getId,
                p ->  {
                    List<String> getNameList = new ArrayList<>();
                    getNameList.add(p.getName());
                    return getNameList;
                },
                (List<String> value1, List<String> value2) -> {
                    value1.addAll(value2);
                    return value1;
                }
        ));
        System.out.println(mp7);

        User user4 = new User(4, null);
        users.add(user4);

        // 属性为空值，也可以输出
        Map<Integer, List<String>> mp8 = users.stream().collect(Collectors.toMap(User::getId,
                p ->  {
                    List<String> getNameList = new ArrayList<>();
                    getNameList.add(p.getName());
                    return getNameList;
                },
                (List<String> value1, List<String> value2) -> {
                    value1.addAll(value2);
                    return value1;
                }
        ));
        System.out.println(mp8);

        // 以对象为值
        Map<Integer, User> mp9 = users.stream()
                .filter(distinctByKey(User::getId))
                .collect(Collectors.toMap(User::getId, User -> User));
        System.out.println(mp9);
    }

    public static <T> Predicate<T> distinctByKey(Function<? super T, ?> keyExtractor) {
        Map<Object,Boolean> seen = new ConcurrentHashMap<>();
        return t -> seen.putIfAbsent(keyExtractor.apply(t), Boolean.TRUE) == null;
    }
}

class User{
    private Integer id;
    private String name;

    public User() {
    }

    public User(Integer id, String name) {
        this.id = id;
        this.name = name;
    }

    public Integer getId() {
        return id;
    }

    public void setId(Integer id) {
        this.id = id;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }
}
```



# Optional类：

Optional类简介：
1）到目前为止，空指针异常是导致Java应用程序失败的最常见原因。以前，为了解决空指针异常，Google公司著名的Guava项目引入了Optional类，Guava通过使用检查空值的方式来防止代码污染，它鼓励程序员写更干净的代码。受到Google Guava的启发，Optional类已经成为Java 8类库的一部分。

2）Optional<T>类(java.util.Optional)是一个容器类，它可以保存类型T的值，代表这个值存在。或者仅仅保存null，表示这个值不存在。原来用null表示一个值不存在，现在Optional可以更好的表达这个概念。并且可以避免空指针异常。

3）Optional类的Javadoc描述如下：这是一个可以为null的容器对象。如果值存在则isPresent()方法会返回true。调用get()方法返回该对象。

Optional方法：
1）Optional提供很多有用的方法，这样就不用显式进行空值检测。

2）创建Optional类对象的方法：
        Optional.of(T t)：创建一个Optional实例，t必须非空

        Optional.empty()：创建一个空的Optional实例
    
        Optional.ofNullable(T t)：t可以为null

3）判断Optional容器中是否包含对象：

        boolean isPresent()：判断是否包含对象
    
        void ifPresent(Consumer<? super T> consumer)：如果有值，就执行Consumer接口的实现代码，并且该值会作为参数传给它。

4）获取Optional容器的对象：

        T get()：如果调用对象包含值，返回该值，否则抛异常
    
        T orElse(T other)：如果有值则将其返回，否则返回指定的other对象
    
        T orElseGet(Supplier<? extends T> other)：如果有值则将其返回，否则返回由Supplier接口实现提供的对象
    
        T orElseThrow(Supplier<? extends X> exceptionSupplier)：如果有值则将其返回，否则抛出由Supplier接口实现提供的异常



ps:orElse与orElseGet的区别：

下例中Line 3的asList方法不管collect是否为null，均会执行。倘若是耗时操作或者是对类文件及数据有改动的操作，需使用orElseGet，传入内部类，实现方法，即可保证collect不为null时，asList不执行。

```java
        Integer[] integers = new Integer[]{1,2,3,4,5};
        List<Integer> collect = Arrays.stream(integers).filter(e -> e.equals(1)).collect(Collectors.toList());
        Optional.of(collect).orElse(Arrays.asList(integers));
        Optional.ofNullable(collect).orElseGet(new Supplier<List<Integer>>() {
            @Override
            public List<Integer> get() {
                return Arrays.asList(integers);
            }
        });
```



# 日期

```java
jak8主要提供了3个类用于处理时间和日期(其实还有不少，这3个类最为常见)

LocalDate：表示一个具体的日期，但是不包含具体的时间，也不包含时区信息
LocalTime：表示一个具有的时间，但是不包含日期信息
LocalDateTime：是LocalDate和LocalTime的结合体

LocalDate

LocalDate date = LocalDate.of(2020, 1, 29);
int year = date.getYear();
Month month = date.getMonth();
int dayOfMonth = date.getDayOfMonth();
DayOfWeek dayOfWeek = date.getDayOfWeek();     // 一周的第几天
int length = date.lengthOfMonth();             // 月份的天数
boolean leapYear = date.isLeapYear();          // 是否为闰年
//获取当前日期
LocalDate now = LocalDate.now();

LocalTime

LocalTime localTime = LocalTime.of(17, 23, 52);     // 初始化一个时间：17:23:52
int hour = localTime.getHour();                     // 时：17
int minute = localTime.getMinute();                 // 分：23
int second = localTime.getSecond();                 // 秒：52

LocalDateTime

LocalDateTime ldt1 = LocalDateTime.of(2017, Month.JANUARY, 4, 17, 23, 52);

LocalDate localDate = LocalDate.of(2017, Month.JANUARY, 4);
LocalTime localTime = LocalTime.of(17, 23, 52);
LocalDateTime ldt2 = localDate.atTime(localTime);

日期格式化

LocalDateTime dateTime = LocalDateTime.now();
String strDate1 = dateTime.format(DateTimeFormatter.BASIC_ISO_DATE);    // 20170105
String strDate2 = dateTime.format(DateTimeFormatter.ISO_LOCAL_DATE);    // 2017-01-05
String strDate3 = dateTime.format(DateTimeFormatter.ISO_LOCAL_TIME);    // 14:20:16.998
String strDate4 = dateTime.format(DateTimeFormatter.ofPattern("yyyy-MM-dd"));   // 2017-01-05
String strDate5 = dateTime.format(DateTimeFormatter.ofPattern("今天是：YYYY年 MMMM DD日 E", Locale.CHINESE)); // 今天是：2017年 一月 05日 星期四
```
