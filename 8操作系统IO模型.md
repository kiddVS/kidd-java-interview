操作系统IO模型
## java中的IO
* java中的IO分为 BIO（阻塞IO），NIO（非阻塞IO）以及AIO（异步IO）。
* java中提供的有关IO的api是依赖操作系统实现的
## 操作系统的IO模型
* 一次完整的IO操作指的是将磁盘上的文件数据拷贝到用户空间的过程。
* 阻塞IO

    * 应用程序通过系统调用recvfrom接收数据，如果内核没有准备好数据，那么应用程序就会一直阻塞等待，直到内核完成数据报的复制工作，应用程序才结束阻塞状态。
    * ![阻塞IO](https://mmbiz.qpic.cn/mmbiz_png/mQlO20PgUDLJyNAPpmHXFWjrXZ2uXvSeVo3htmYsApCW1lscbqBLOoqDSFEg47YxWfcyO6YqNnCpjuRbZGjbZw/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)
* 非阻塞IO
    * 应用程序系统不断调用recfrom，然后不断轮询内核是否完成数据报的复制工作，如果没有完成返回error，完成之后应用程序可以处理数据报。在两次轮询的间隔间，应用程序可以做其他的事情。
    * ![非阻塞IO](https://mmbiz.qpic.cn/mmbiz_jpg/mQlO20PgUDLJyNAPpmHXFWjrXZ2uXvSek6qWHALUicEVcAicEb9VBIehiaQEZWmUE62T87cQFTLToKme4JJhoCRYg/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)
* 信号驱动IO
    * 应用进程向操作系统注册信号处理函数，用户进程返回不阻塞，当内核数据准备好之后发信号给用户进程，用户进程收到信号后调用recfrom吧数据拷贝到用户空间中。
    * ![信号驱动IO](https://mmbiz.qpic.cn/mmbiz_jpg/mQlO20PgUDLJyNAPpmHXFWjrXZ2uXvSe3jGJekgJ1X4kjia7AABicVEAvNVXDQBI4o2pLW3b9EiaibiavFnTBABicUfw/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)
* IO复用模型
    * 多个进程IO注册到同一个管道上，管道和内核交互，当管道中某个请求的数据准备好后，把对应数据拷贝到用户空间中。
    * ![IO复用](https://mmbiz.qpic.cn/mmbiz_jpg/mQlO20PgUDLJyNAPpmHXFWjrXZ2uXvSeud0ZosTZicUtNxS1xwjibBAGIF1WInW43rJAzWdibsaSVUUMVgNsFrGibQ/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)
* 以上四种IO模型都是同步的，因为通过recfrom调用从内核拷贝数据的过程是同步的，由应用进程进行操作。
* 异步IO
    * 应用进程把IO请求传给内核之后，由内核去操作文件拷贝，内核完成操作之后，发信号通知应用进程本次IO已经完成。
    * ![异步IO](https://mmbiz.qpic.cn/mmbiz_jpg/mQlO20PgUDLJyNAPpmHXFWjrXZ2uXvSeM1kjXMOiaZ5CgM3bBPlEeUvhib2vRtMqvwL8r2OWiaGtzj4QwiawTPtHEg/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

* 五种IO方式的对比
* ![IO对比](https://mmbiz.qpic.cn/mmbiz_png/mQlO20PgUDLJyNAPpmHXFWjrXZ2uXvSeqeQfjNIVKzKA4lJUFPDKUic0FiayuEXticzTtnFPN74Y7poNjZbV0DygQ/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)