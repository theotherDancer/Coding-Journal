# Coding Journal

记录牛客刷题中的错题、答对后值得复习的知识点，以及它们在练习代码中的用法。

## 牛客刷题记录

- [2026-11-14 · 未完成 · 初步接触刷题](#day-2026-11-14)
- [2026-11-15 · Java 与 MySQL 知识点复盘](#day-2026-11-15)
- [2026-11-16 · Java 字段初始化、继承与垃圾回收](#day-2026-11-16)

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

<a name="day-2026-11-16"></a>

### 2026-11-16 · Java 字段初始化、继承与垃圾回收

#### `final` 实例字段什么时候必须赋值？

`final` 字段赋值后可以读取，字段本身不能再被赋值。`final int i;` 只声明了字段，没有写初始值，这叫**空白 `final` 字段**。Java 要求构造方法每条正常完成的路径上都明确给这个字段赋值。可以在声明时给初值，也可以在构造方法或实例初始化块中赋值。 [Java 语言规范 · `final` 字段](https://docs.oracle.com/javase/specs/jls/se25/html/jls-8.html#jls-8.3.1.2)

```java
class Foo {
    final int i;
    int j;

    void doSomething() {
        System.out.println(++j + i);
    }
}
```

这段代码编译失败。`Foo` 没写构造方法，编译器补上的默认构造方法也没有给 `i` 赋值。即使把打印语句改成 `System.out.println(i);`，缺少赋值的问题仍然存在。普通的 `int` 实例字段 `j` 会以 `0` 为默认值。默认值无法满足 `final` 字段的明确赋值要求。

在 `Foo` 里加入 `Foo() { i = 0; }` 后，第一次对新对象调用 `doSomething()`，`++j` 先把 `j` 从 `0` 加到 `1`，再计算 `1 + 0`，所以打印 `1`。对同一个对象再调用一次，会打印 `2`。两次调用都只修改了 `j`，`i` 一直是 `0`。

#### 父类构造方法为什么会调用子类的方法？

把下面的代码存成 `Base.java`，在 IDEA 运行 `main`。它同时考察**父子类的初始化顺序**和**实例方法的动态分派**。 [Java 语言规范 · 对象创建](https://docs.oracle.com/javase/specs/jls/se25/html/jls-12.html#jls-12.5)

```java
public class Base {
    private String baseName = "base";

    public Base() {
        callName();
    }

    public void callName() {
        System.out.println(baseName);
    }

    static class Sub extends Base {
        private String baseName = "sub";

        @Override
        public void callName() {
            System.out.println(baseName);
        }
    }

    public static void main(String[] args) {
        Base b = new Sub();
    }
}
```

1. `new Sub()` 为一个 `Sub` 对象分配空间。这个对象包含 `Base` 和 `Sub` 分别声明的两个 `baseName` 字段，它们此时都取 `String` 字段的默认值 `null`。
2. `Sub` 的默认构造方法调用 `super()`。进入 `Base()` 的方法体之前，父类声明的字段已经被赋成 `"base"`。
3. `Base()` 方法体里的 `callName();` 执行调用，相当于 `this.callName()`。当前对象实际是 `Sub`，所以执行子类重写的 `callName()`。方法体中的 `baseName` 指向 `Sub` 声明的字段，此时它还是 `null`，因此打印一行 `null`。
4. `Base()` 返回后，子类字段的初始化语句才把它赋成 `"sub"`。至此 `new Sub()` 完成，得到的对象引用交给变量 `b`。

| 运行到哪一步 | `Base` 声明的字段 | `Sub` 声明的字段 | 本步输出 |
| --- | --- | --- | --- |
| 刚分配对象 | `null` | `null` | 无 |
| `Base()` 中调用 `callName()` | `"base"` | `null` | `null` |
| `Base()` 返回，子类字段初始化 | `"base"` | `"sub"` | 无 |
| 如果这时另外调用 `b.callName()` | `"base"` | `"sub"` | `sub` |

`public void callName() { ... }` 是方法声明，真正触发输出的是构造方法体里的 `callName();`。字段读取在编译时就已确定，`Sub.callName()` 始终读取子类声明的那个字段，值为 `null` 时也照样读它。两个同名字段各有自己的值。

#### 继承了什么？`private` 和 `protected` 有什么区别？

继承让子类拥有符合规则的父类成员。字段里具体存什么值，则由每个对象的初始化过程和后续赋值决定。例如 `Base` 声明 `protected String name = "base";`，若 `Sub` 继承 `Base` 且没有再声明同名字段，`Sub` 可以直接读取继承来的 `name`。新建 `Sub` 对象时，父类的初始化代码给这个对象里的 `name` 赋成 `"base"`。

| 访问实例字段或方法的位置 | `private` | `protected` |
| --- | --- | --- |
| 声明它的类内部 | 可以 | 可以 |
| 同包、独立的另一个类 | 不可以 | 可以，即使没有继承关系 |
| 跨包的子类内部 | 不可以 | 可以通过 `this`、`super` 等访问。通过对象引用访问时，引用的编译时类型还必须是该子类或其子类 |

原例中的 `Sub` 嵌套在 `Base` 里面，同一个顶层类内部有权访问 `private` 成员。访问权限和继承是两件事，父类的 `private` 字段不会成为子类继承的成员，但这个字段仍保存在 `Sub` 实例中。原例打印 `null` 的直接原因是 `Sub.callName()` 读到了自己尚未赋初值的字段。 [Java 语言规范 · 访问控制](https://docs.oracle.com/javase/specs/jls/se25/html/jls-6.html#jls-6.6) · [跨包 `protected` 的访问规则](https://docs.oracle.com/javase/specs/jls/se25/html/jls-6.html#jls-6.6.2.1)

#### JVM 怎样判断哪个对象可以回收？

**垃圾回收（GC）**是 JVM 自动回收对象占用的内存。理解可达性时，可以把对象想成储物柜，把引用想成写着柜号的纸条。程序从还能用到的引用出发，沿着对象内部的引用继续找。能沿路径找到的对象是**可达对象**。在下面的例子里，局部变量 `a`、`b` 就是手里的两张纸条，A 的 `ref` 是柜子里存的一张纸条。 [Java 语言规范 · 对象可达性](https://docs.oracle.com/javase/specs/jls/se25/html/jls-12.html#jls-12.6.1)

```java
public class ReachabilityDemo {
    static class A {
        private B ref;

        void setB(B b) {
            ref = b;
        }
    }

    static class B {
        private A ref;

        void setA(A a) {
            ref = a;
        }
    }

    public static void main(String[] args) {
        A a = new A();
        B b = new B();

        a.setB(b);
        b = null;
        a = null;
    }
}
```

`a.setB(b)` 把变量 `b` 里存的引用复制给 A 的 `ref`。可以想成把 B 的柜号抄了一份，放进 A 的柜子。虽然 B 声明了 `setA()`，这里没有调用它，所以 B 的 `ref` 仍是 `null`。

```text
执行 a.setB(b) 后
a ──► A 对象 ── ref ──► B 对象
b ────────────────────► B 对象

执行 b = null 后
a ──► A 对象 ── ref ──► B 对象
b ──► null

继续执行 a = null 后
a ──► null
b ──► null
```

第一处置空只擦掉手里的 `b` 纸条，A 柜里的副本还在，仍可经 A 找到 B。第二处置空清掉了通往 A 的入口，A 柜里虽然还留着 B 的柜号，但已经走不到那张纸条。按这个引用图，两个对象都已不可达，具备被回收的条件。实际回收时机由 JVM 决定。

**自查。** 如果保留 `b`，只执行 `a = null`，A 已不可达，B 仍可通过 `b` 直接找到。画引用图时先写清正在判断的是哪个对象，再从手里仍有的纸条出发找路径。

**验证。** 本机 Java 验证了 `Foo` 原版编译失败，补构造方法后连续调用输出 `1`、`2`。`Base` 原版输出 `null`，新增 `b.callName()` 后输出 `null`、`sub`。GC 部分按引用关系推演，示例未测量对象实际回收的时刻。
