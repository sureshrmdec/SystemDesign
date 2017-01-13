```java
Sequential Consistency（下文简称SC）是Java内存模型和即将到来的C++0x内存模型的一个关键概念，
它确保了你的多线程程序在执行时的正确性。Cache Coherence（下文简称CC）是多核CPU在硬件中已经实现的一种机制，
简单的说，它确保了对一个内存地址的任何读操作一定会返回那个内存地址最新的（被写入）的值。

java 可以用关键字 volatile 来保证 Memory consistency

class VolatileExample {
  int x = 0;
  volatile boolean v = false;
  public void writer() {
    x = 42;
    v = true;
  }
 
  public void reader() {
    if (v == true) {
      //uses x - guaranteed to see 42.
    }
  }
}

In a shared memory multiprocessor system with a separate cache memory for each processor, 
it is possible to have many copies of shared data: one copy in the main memory 
and one in the local cache of each processor that requested it. When one of the copies of data is changed, 
he other copies must reflect that change. Cache coherence is the discipline which ensures that the changes 
in the values of shared operands(data) are propagated throughout 
the system in a timely fashion

```
