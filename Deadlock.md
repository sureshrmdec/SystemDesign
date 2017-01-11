```java
Resource A and resource B are used by process X and process Y

X starts to use A.
X and Y try to start using B
Y 'wins' and gets B first
now Y needs to use A
A is locked by X, which is waiting for Y


Ways to avoid having deadlocks are:

avoid having locks (if possible)
avoid having more than one lock
always take the locks in the same order.


```
