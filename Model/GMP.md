# [GPM模型](https://www.jianshu.com/p/fa696563c38a)

![符号含义](https://raw.githubusercontent.com/TDAkory/ImageResources/master/img/20220208110856.png)

![GMP调度模型](https://raw.githubusercontent.com/TDAkory/ImageResources/master/img/20220208110938.png)

![GMP调度模型2](https://raw.githubusercontent.com/TDAkory/ImageResources/master/img/GoLearn/gmp-model.png)

在 golang 中的并发工具（例如锁 mutex、通道 channel 等）均契合 gmp 作了适配改造，保证在执行阻塞操作时，会将阻塞粒度限制在 g（goroutine）而非 m（thread）的粒度，使得阻塞与唤醒操作都属于用户态行为，无需内核的介入，同时一个 g 的阻塞也完全不会影响 m 下其他 g 的运行.

上面这项结论看似理所当然，但实际上是一项非常重要的特性, c++ 标准库中的并发工具（如 lock、semaphore 等）对应的阻塞粒度都是 thread 级别的，这就导致一个协程（coroutine）的阻塞会上升到线程（thread）级别，并导致其他 coroutine 也丧失被执行的机会.

