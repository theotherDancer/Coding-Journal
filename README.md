# Coding Journal

记录牛客刷题中的错题、答对后值得复习的知识点，以及它们在练习代码中的用法。

## 牛客刷题记录

- [2026-11-14 · 未完成 · 初步接触刷题](#day-2026-11-14)
- [2026-11-15 · Java 与 MySQL 知识点复盘](#day-2026-11-15)

<a name="day-2026-11-14"></a>

### 2026-11-14 · Java Socket 的本地地址

**状态：未完成。** 今天只是初步接触牛客刷题，回顾了第 1 道题，整套练习还没刷完。

**第 1 题。** Java 的 `Socket` 怎样获取本地 IP？我选了 B `getLocalAddress()`，答对。这次借题复习本端与对端的区别。下表的方法都从当前 `Socket` 的视角读取信息。 [Java Socket API](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/net/Socket.html#getLocalAddress())

| 方法 | 读取的信息 |
| --- | --- |
| `getLocalAddress()` | 本端绑定的 IP，返回 `InetAddress` |
| `getInetAddress()` | 对端 IP |
| `getLocalPort()` | 本端端口 |
| `getPort()` | 对端端口 |
| `getReuseAddress()` | 是否启用 `SO_REUSEADDR` |

**对照以前的 UDP 练习。** 发送方把 `10086` 写进 `DatagramPacket`，它是包的目的端口。接收方用 `new DatagramSocket(10086)`，它是接收套接字绑定的本地端口。收到包后，`packet.getPort()` 读到发送方的源端口。这次只读了源码并核对 API，尚未运行验证。 [DatagramSocket API](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/net/DatagramSocket.html) · [DatagramPacket API](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/net/DatagramPacket.html#getPort())

**下次起点。** 先复述同一个 `10086` 在发送端和接收端各代表什么，再继续刷题。

<a name="day-2026-11-15"></a>

### 2026-11-15 · Java 与 MySQL 知识点复盘

#### 赋值、比较与 `if` 条件

`=` 修改变量，赋值表达式的值是赋入的值。`==` 比较两边，产生一个布尔结果，变量保持原值。以 `Boolean flag = false` 为初值，`if (flag = true)` 会把 `flag` 改成 `true` 并进入 `if` 分支。单独从同一个初值出发，`if (flag == true)` 会比较 `false` 和 `true`，因此跳过 `if` 分支。`Boolean` 包装对象在这些判断中会自动拆箱成 `boolean`。

#### 前置自增、后置自增与三元运算符

`i++` 和 `++i` 都让 `i` 加 1。`i++` 的表达式值是旧值，`++i` 的表达式值是新值。读代码时，把变量的新值和表达式交给外层的值分别记下来。

```java
int i = 0;
int a = i++;                // a = 0，i = 1
int b = ++a;                // a = 1，b = 1
int c = a + b;              // c = 2
int d = (a == 1) ? b : c;    // 条件成立，取 b，d = 1
```

三元运算符 `条件 ? 真分支 : 假分支` 会先判断条件，然后只求值被选中的分支。在 `while` 中，条件也会先完整求值。若 `x` 从 0 开始，`while (++x < 5) { x += 1; }` 的循环体运行两次，把 `x` 分别加到 2 和 4。第三次判断时 `++x` 把它改成 5，随后条件为假，退出时 `x` 就是 5。

#### 构造方法、抽象类与接口

构造方法与类同名，省略返回类型。`void Student()` 会被识别为普通方法。对于普通顶层类，只有类中没有声明任何构造方法时，编译器才会补默认的无参构造方法。同类构造方法可以用 `this(...)` 委托初始化，比如 `Student()` 用 `this("未命名")` 调用 `Student(String name)`，这两个调用处理的是同一个对象。 [Java 语言规范 · 构造方法](https://docs.oracle.com/javase/specs/jls/se25/html/jls-8.html#jls-8.8)

抽象类可以有构造方法和实例字段。创建具体子类对象时，父类构造方法会参与初始化。下面两个 `Student` 构造方法各走一条路径。

```java
abstract class Person {
    String name;

    Person() {}

    Person(String name) {
        this.name = name;
    }
}

class Student extends Person {
    Student() {
        // 隐式调用 Person()，name 的值仍为 null。
    }

    Student(String name) {
        super(name); // 调用 Person(String)，初始化 name。
    }
}
```

删掉 `Person()` 后，上面的 `Student()` 会编译报错，因为它隐式调用的 `super()` 找不到可访问的无参构造方法。接口没有构造方法，接口字段隐式为 `public static final`。一个类只有一个直接父类，可以实现多个接口。抽象类可以定义静态方法，接口从 Java 8 起才支持静态方法。 [Java 7 类规范](https://docs.oracle.com/javase/specs/jls/se7/html/jls-8.html) · [Java 7 接口规范](https://docs.oracle.com/javase/specs/jls/se7/html/jls-9.html) · [Java 8 接口规范](https://docs.oracle.com/javase/specs/jls/se8/html/jls-9.html#jls-9.4)

#### `List` 与 `Set` 的重复元素和遍历顺序

`ArrayList` 按位置保存元素，可以保留重复值和多个 `null`。`Set` 去重，允许 `null` 的实现至多保留一个 `null`。例如依次加入 `"小明"`、`"小明"`、`null`、`null`，`ArrayList.size()` 是 `4`，`HashSet.size()` 是 `2`。`HashSet` 不保证遍历顺序，`LinkedHashSet` 按插入顺序遍历。

#### Socket 的本端和对端

同一条 TCP 连接在两端各有一个 `Socket`。从哪个对象调用 `getLocalAddress()`，就读取它在本端绑定的 IP。客户端调用时读取客户端 IP，服务端用已接受的连接调用时读取服务端 IP。`getInetAddress()` 从当前连接读取对端 IP。 [Java Socket API](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/net/Socket.html#getLocalAddress())

#### MySQL 字符串拼接和 SQL 模式

MySQL 的 `+` 用于数值加法，`CONCAT()` 用于字符串拼接。默认情况下 `||` 表示逻辑或。启用 `PIPES_AS_CONCAT` 后，同一个 `||` 运算符表示字符串拼接。 [MySQL 字符串函数](https://dev.mysql.com/doc/refman/8.4/en/string-functions.html#function_concat) · [MySQL SQL 模式](https://dev.mysql.com/doc/refman/8.4/en/sql-mode.html#sqlmode_pipes_as_concat)

```sql
SELECT '12' + '34';         -- 数值 46
SELECT CONCAT('12', '34');  -- 字符串 '1234'
SELECT 'Java' || 'Web';     -- 启用 PIPES_AS_CONCAT 时得到 'JavaWeb'
```

#### 编译原理中的三种循环优化

- **代码外提。** 如果 `width` 和 `height` 在循环中始终不变，并且提前求值能保持程序行为，就把 `width * height` 移到循环前，循环内复用结果。
- **强度削弱。** 当 `i` 依次取 `0`、`1`、`2` 时，`4 * i` 依次是 `0`、`4`、`8`。可以用从 0 开始、每轮加 4 的 `offset` 得到相同序列。
- **删除归纳变量。** 若 `i` 只用于控制上面的循环，已有的 `offset` 也可以用 `offset < 12` 控制同样的三次迭代，这时可以省掉 `i`。 [GCC 循环优化文档](https://gcc.gnu.org/onlinedocs/gccint/Tree-SSA-passes.html)

**练习进度。** 上面的 Java 和 SQL 结果来自手算，IDEA 和 Workbench 实测待补。下一次看 OpenJDK 的 `HashMap` 源码，区分单个桶的链表长度和底层数组容量，再看什么时候扩容、什么时候树化。
