```java

Thread 1	Thread 2
x = 1;	int r1 = y;
y = 2;	int r2 = x;
If no reorderings are performed, and the read of y in Thread 2 returns the value 2, 
then the subsequent read of x should return the value 1, because the write to x was performed before the write to y. 
However, if the two writes are reordered, then the read of y can return the value 2, and the read of x can return the value 0.
happens-before

从JDK5开始，java使用新的JSR -133内存模型（本文除非特别说明，针对的都是JSR- 133内存模型）。JSR-133提出了happens-before的概念，
通过这个概念来阐述操作之间的内存可见性。如果一个操作执行的结果需要对另一个操作可见，那么这两个操作之间必须存在happens-before关系。
这里提到的两个操作既可以是在一个线程之内，也可以是在不同线程之间。 与程序员密切相关的happens-before规则如下：

程序顺序规则：一个线程中的每个操作，happens- before 于该线程中的任意后续操作。
监视器锁规则：对一个监视器锁的解锁，happens- before 于随后对这个监视器锁的加锁。
volatile变量规则：对一个volatile域的写，happens- before 于任意后续对这个volatile域的读。
传递性：如果A happens- before B，且B happens- before C，那么A happens- before C。


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



存储的Coherence模型主要考虑对于同一内存位置的写操作对于所有的处理器的可见性。理想情况下，我们希望写内存的结果可以立即被所有处理器看到，
也就是说写操作后其它处理器的读操作所读到的值都是新值。但事实上，由于层次存储模型的存在，这样的假设由于太强不可能实现。
因此，现实中往往放宽即时性而强调有序性，这被称为Coherence。会影响到Coherence的因素主要包括存储的分层结构。
在存储的分层结构中，Cache被用来作为内存数据的快速缓存。在直写（write-through）模式下，当处理器写入内存时，
同时也会更新本地Cache，这将成为破坏存储Coherence的隐患。假设下面的场景：处理器1发出写内存申请，
这时仅更新本地Cache，物理内存并末即时更新。之后处理器2发出写内存申请，同时更新本地Cache，同样物理内存也并末立即改变。
之后处理器1将新值正式写入内存，然后处理器2也将新值正式写入内存。假设整个过程两个处理器更新的为同一内存区域。
在这个过程中，处理器2先看到了它自己要更新的值（因为本地Cache即时更新），后看到处理器1更新的值（处理器1写操作正式写入内存时），
最后又看到处理器2更新的值（处理器2写操作正式写入内存时）。这样就违反了存储的Coherence模型。
 
存储的Consistency模型的范围则广得多。和Coherence相比，其主要差别有：1.Consistency不仅针对同一内存区域的访问；
2.Consistency中的内存访问包括读和写两种。当然，最理想的情况就是所有指令的执行顺序和程序里写的一模一样，
这样当然不会违反内存的Consistency，但事实上这也是不可能的。因为考虑到并行优化，几乎所有平台都会改变指令顺序来提高程序运行速度，
这不可避免地会违反Consistency原则。前面提到存储的Consistency模型中的内存访问分两种，即读和写。
经组合，易得违反Consistency原则的情况也可能有四种：R-R hazard，R-Whazard，W-R hazard和W-Whazard。
过强的Consistency约束会使优化程度极大降低，因此很多平台会选择放弃其中的一种或几种。
目前，为了最大程度地优化程序，一些平台放弃了所有的内存Consistency约束，而将保证多线程程序正确性的工作转移给程序员。
这里引入了一个假设，就是程序员应该知道，并且也只有程序员知道为了保证程序的正确性，哪些内存访问操作不能被乱序。
所以我们才会在这么多项目（如Linux kernel）中看到这么多memory barrier语句。
```
