# Coding Journal

记录牛客刷题中的错题、答对后值得复习的知识点，以及它们在练习代码中的用法。

## 牛客刷题记录

- [2026-11-14 · 未完成 · 初步接触刷题](#day-2026-11-14)

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
