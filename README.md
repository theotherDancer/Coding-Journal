# Coding Journal

记录牛客刷题中的错题、答对后值得复习的知识点，以及它们在练习代码中的用法。

## 牛客刷题记录

- [2026-11-14 · 未完成 · 初步接触刷题](#day-2026-11-14)
- [2026-11-15 · 两组 Java 专项练习与错题复盘](#day-2026-11-15)

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

### 2026-11-15 · 两组 Java 专项练习与错题复盘

今天继续做牛客专项练习，两组各答对 **3/5（60%）**。第一组错在布尔赋值和 MySQL 字符串拼接。第二组留空了 `HashMap` 树化题，抽象类与接口的多选题漏了两项。

#### 为什么 `if (flag = true)` 打印 `true`？

我原选 D，正确是 C。从 `Boolean flag = false` 出发，`flag = true` 先把 `true` 写进 `flag`，赋值表达式再把这个值交给 `if`，判断时自动拆箱。写成 `flag == true` 则会比较原来的 `false` 和 `true`，比较结果为 `false`，`flag` 仍是 `false`。

#### MySQL 用什么拼接字符串？

我原选 A `+`，正确是 C `||`。MySQL 的 `+` 用于数值加法，`SELECT '12' + '34';` 得到 `46`。拼接可以写 `SELECT CONCAT('12', '34');`，得到字符串 `1234`。启用题目指定的 `PIPES_AS_CONCAT` 模式后，`SELECT 'Java' || 'Web';` 得到 `JavaWeb`。我在复盘中答对了这个例子。 [MySQL 手册](https://dev.mysql.com/doc/refman/8.4/en/sql-mode.html#sqlmode_pipes_as_concat)

#### 抽象类的构造方法什么时候会执行？

我在「Java 8 之前」这道多选题只选了 D，正确答案是 ABD。一个类只能有一个直接父类，可以实现多个接口。抽象类可以声明实例字段和构造方法，子类构造时能调用它。接口没有构造方法，字段隐式为 `public static final`。抽象类可以有静态方法，接口从 Java 8 起才支持。 [Java 7 类规范](https://docs.oracle.com/javase/specs/jls/se7/html/jls-8.html) · [Java 7 接口规范](https://docs.oracle.com/javase/specs/jls/se7/html/jls-9.html) · [Java 8 接口规范](https://docs.oracle.com/javase/specs/jls/se8/html/jls-9.html#jls-9.4)

用 `Person` 和 `Student` 推演了两个情况。父类只有 `Person(String name)` 时，子类省略 `super(name)` 会隐式尝试调用 `super()`，因此编译失败。父类加上可访问的 `Person() {}` 后，以下代码可以编译。创建 `new Student("小明")` 时，若 `Student` 构造方法没有给字段赋值，`name` 仍是 `null`。在该构造方法里写 `super(name);` 则会把 `name` 初始化为 `"小明"`。

```java
abstract class Person {
    String name;

    Person() {}

    Person(String name) {
        this.name = name;
    }
}

class Student extends Person {
    Student(String name) {
        // 这里隐式调用 Person()。
    }
}
```

#### 其他题的复习要点

- **Socket。** 在客户端和服务端分别看同一条 TCP 连接时，各自的 `getLocalAddress()` 都读取本端地址。我判断出了服务端调用时读到的是服务器 IP。
- **默认构造方法与重载。** `void Student() {}` 是普通方法。类没有声明构造方法时，编译器才补默认构造方法。无参构造可用 `this("未命名")` 调用同类的带参构造。我推对了直接写 `new Student("小明")` 只进入带参构造。 [Java 语言规范](https://docs.oracle.com/javase/specs/jls/se25/html/jls-8.html#jls-8.8)
- **`List` 与 `Set`。** 添加两个相同的名字和两个 `null` 后，`ArrayList` 大小是 4，`HashSet` 大小是 2。支持 `null` 的 `Set` 至多保留一个 `null`。`List` 有位置顺序，`HashSet` 不保证遍历顺序。
- **自增和三元运算符。** 从 `i = 0` 开始，`a = i++` 取旧值，所以 `a = 0`、`i = 1`。`b = ++a` 取新值，所以 `a = 1`、`b = 1`。`a == 1 ? b : c` 取 `b`，最终 `a` 和 `d` 都是 `1`。成绩页上的 `while (++x < 5)` 题也提示了同一规则，最后一次判断会先自增到 `5`，然后才退出循环。
- **循环优化。** 牛客答案为 ABD。代码外提把循环内结果不变的计算移到循环前，强度削弱可用固定步长的递增代替每轮乘法，删除归纳变量则用已有变量接管循环控制。删除多余运算可在普通代码段中应用。 [GCC 循环优化文档](https://gcc.gnu.org/onlinedocs/gccint/Tree-SSA-passes.html)

**验证与下次起点。** 以上结果来自牛客成绩页和手算。IDEA 构造方法示例和 Workbench 的 SQL 示例待运行验证。下一道重点复习第二组留空的 `HashMap` 树化题。题目答案是 B，接下来结合 OpenJDK 源码区分桶内链表长度和数组容量。
