延迟（Latency）：一个封包从来源端送出后，到目的端接收到这个封包，中间所花的时间。
频宽（Bandwidth）：传输媒介的最大吞吐量（throughput）

Parallelizability:

Algorithms vary significantly in how parallelizable they are, ranging from easily parallelizable to completely unparallelizable. 
Further, a given problem may accommodate different algorithms, which may be more or less parallelizable.

Some problems are easy to divide up into pieces in this way – these are called embarrassingly parallel problems. 
For example, splitting up the job of checking all of the numbers from one to a hundred thousand to see which are primes 
could be done by assigning a subset of the numbers to each available processor, and then putting the list of positive results 
back together. Algorithms are also used for things such as solving Rubik's Cubes and for hash decryption.

Some problems cannot be split up into parallel portions, as they require the results from a preceding step to 
effectively carry on with the next step – these are called inherently serial problems. Examples include iterative numerical methods, 
such as Newton's method, iterative solutions to the three-body problem, and most of the available algorithms to compute

Motivation:

Parallel algorithms on individual devices have become more common since the early 2000s
because of substantial improvements in multiprocessing systems and the rise of multi-core processors. 
Up until the end of 2004, single-core processor performance rapidly increased via frequency scaling, 
and thus it was easier to construct a computer with a single fast core than one with many slower cores with the same throughput, 
so multicore systems were of more limited use. Since 2004 however, frequency scaling hit a wall, 
and thus multicore systems have become more widespread, making parallel algorithms of more general use.


Issues：

Communication：

The cost or complexity of serial algorithms is estimated in terms of the space (memory) and time (processor cycles) that they take. 
Parallel algorithms need to optimize one more resource, the communication between different processors. 
There are two ways parallel processors communicate, shared memory or message passing.

Shared memory processing needs additional locking for the data, imposes the overhead of additional processor and bus cycles, 
and also serializes some portion of the algorithm.

Message passing processing uses channels and message boxes but this communication adds transfer overhead on the bus, 
additional memory need for queues and message boxes and latency in the messages. Designs of parallel processors use special buses 
like crossbar so that the communication overhead will be small but it is the parallel algorithm that decides the volume of the traffic.

If the communication overhead of additional processors outweighs the benefit of adding another processor, 
one encounters parallel slowdown.

Load balancing：

Another problem with parallel algorithms is ensuring that they are suitably load balanced, 
by ensuring that load (overall work) is balanced, rather than input size being balanced. 
For example, checking all numbers from one to a hundred thousand for primality is easy to split amongst processors;
however, if the numbers are simply divided out evenly (1–1,000, 1,001–2,000, etc.), the amount of work will be unbalanced,
as smaller numbers are easier to process by this algorithm (easier to test for primality),
and thus some processors will get more work to do than the others, which will sit idle until the loaded processors complete.




