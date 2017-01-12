Deadlock
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

```java
public class TestThread {
   public static Object Lock1 = new Object();
   public static Object Lock2 = new Object();
   
   public static void main(String args[]) {
      ThreadDemo1 T1 = new ThreadDemo1();
      ThreadDemo2 T2 = new ThreadDemo2();
      T1.start();
      T2.start();
   }
   
   private static class ThreadDemo1 extends Thread {
      public void run() {
         synchronized (Lock1) {
            System.out.println("Thread 1: Holding lock 1...");
            
            try { Thread.sleep(10); }
            catch (InterruptedException e) {}
            System.out.println("Thread 1: Waiting for lock 2...");
            
            synchronized (Lock2) {
               System.out.println("Thread 1: Holding lock 1 & 2...");
            }
         }
      }
   }
   private static class ThreadDemo2 extends Thread {
      public void run() {
         synchronized (Lock2) {
            System.out.println("Thread 2: Holding lock 2...");
            
            try { Thread.sleep(10); }
            catch (InterruptedException e) {}
            System.out.println("Thread 2: Waiting for lock 1...");
            
            synchronized (Lock1) {
               System.out.println("Thread 2: Holding lock 1 & 2...");
            }
         }
      }
   } 
}

public class BankAccount {
    double balance;
    int id;
     
    BankAccount(int id, double balance) {
        this.id = id;
        this.balance = balance;
    }
     
    void withdraw(double amount) {
        // Wait to simulate io like database access ...
        try {Thread.sleep(10l);} catch (InterruptedException e) {}
        balance -= amount;
    }
     
    void deposit(double amount) {
        // Wait to simulate io like database access ...
        try {Thread.sleep(10l);} catch (InterruptedException e) {}
        balance += amount;
    }
     
    static void transfer(BankAccount from, BankAccount to, double amount) {
        synchronized(from) {
            from.withdraw(amount);
            synchronized(to) {
                to.deposit(amount);
            }
        }
    }
     
    public static void main(String[] args) {
        final BankAccount fooAccount = new BankAccount(1, 100d);
        final BankAccount barAccount = new BankAccount(2, 100d);
         
        new Thread() {
            public void run() {
                BankAccount.transfer(fooAccount, barAccount, 10d);
            }
        }.start();
         
        new Thread() {
            public void run() {
                BankAccount.transfer(barAccount, fooAccount, 10d);
            }
        }.start();
         
    }
}

```


Livelock

A thread often acts in response to the action of another thread. If the other thread's action is also a response to the action of another thread, then livelock may result. As with deadlock, livelocked threads are unable to make further progress. However, the threads are not blocked — they are simply too busy responding to each other to resume work. This is comparable to two people attempting to pass each other in a corridor: Alphonse moves to his left to let Gaston pass, while Gaston moves to his right to let Alphonse pass. Seeing that they are still blocking each other, Alphone moves to his right, while Gaston moves to his left. They're still blocking each other, so...


```java

import java.util.concurrent.locks.Lock;
import java.util.concurrent.locks.ReentrantLock;
 
public class BankAccount {
    double balance;
    int id;
    Lock lock = new ReentrantLock();
 
    BankAccount(int id, double balance) {
        this.id = id;
        this.balance = balance;
    }
 
    boolean withdraw(double amount) {
        if (this.lock.tryLock()) {
            // Wait to simulate io like database access ...
            try {Thread.sleep(10l);} catch (InterruptedException e) {}
            balance -= amount;
            return true;
        }
        return false;
    }
 
    boolean deposit(double amount) {
        if (this.lock.tryLock()) {
            // Wait to simulate io like database access ...
            try {Thread.sleep(10l);} catch (InterruptedException e) {}
            balance += amount;
            return true;
        }
        return false;
    }
 
    public boolean tryTransfer(BankAccount destinationAccount, double amount) {
        if (this.withdraw(amount)) {
            if (destinationAccount.deposit(amount)) {
                return true;
            } else {
                // destination account busy, refund source account.
                this.deposit(amount);
            }
        }
 
        return false;
    }
 
    public static void main(String[] args) {
        final BankAccount fooAccount = new BankAccount(1, 500d);
        final BankAccount barAccount = new BankAccount(2, 500d);
 
        new Thread(new Transaction(fooAccount, barAccount, 10d), "transaction-1").start();
        new Thread(new Transaction(barAccount, fooAccount, 10d), "transaction-2").start();
 
    }
 
}
class Transaction implements Runnable {
    private BankAccount sourceAccount, destinationAccount;
    private double amount;
 
    Transaction(BankAccount sourceAccount, BankAccount destinationAccount, double amount) {
        this.sourceAccount = sourceAccount;
        this.destinationAccount = destinationAccount;
        this.amount = amount;
    }
 
    public void run() {
        while (!sourceAccount.tryTransfer(destinationAccount, amount))
            continue;
        System.out.printf("%s completed ", Thread.currentThread().getName());
    }
 
}
```
Starvation

Starvation describes a situation where a thread is unable to gain regular access to shared resources and is unable to make progress. This happens when shared resources are made unavailable for long periods by "greedy" threads. For example, suppose an object provides a synchronized method that often takes a long time to return. If one thread invokes this method frequently, other threads that also need frequent synchronized access to the same object will often be blocked.


Imagine in you're in a queue to pay get food at a restaurant, for which pregnant woman has priority. And there's just a whole bunch of pregnant women arriving all the time.

You'll soon be starving ;)

Now imagine you are a low-priority process and the pregnant women are higher priority ones =)

Hope this illustration helps hehe

```java
public class BankAccount {
    private double balance;
    int id;
 
    BankAccount(int id, double balance) {
        this.id = id;
        this.balance = balance;
    }
     
    synchronized double getBalance() {
        // Wait to simulate io like database access ...
        try {Thread.sleep(100l);} catch (InterruptedException e) {}
        return balance;
    }
     
    synchronized void withdraw(double amount) {
        balance -= amount;
    }
 
    synchronized void deposit(double amount) {
        balance += amount;
    }
 
    synchronized void transfer(BankAccount to, double amount) {
            withdraw(amount);
            to.deposit(amount);
    }
 
    public static void main(String[] args) {
        final BankAccount fooAccount = new BankAccount(1, 500d);
        final BankAccount barAccount = new BankAccount(2, 500d);
         
        Thread balanceMonitorThread1 = new Thread(new BalanceMonitor(fooAccount), "BalanceMonitor");
        Thread transactionThread1 = new Thread(new Transaction(fooAccount, barAccount, 250d), "Transaction-1");
        Thread transactionThread2 = new Thread(new Transaction(fooAccount, barAccount, 250d), "Transaction-2");
         
        balanceMonitorThread1.setPriority(Thread.MAX_PRIORITY);
        transactionThread1.setPriority(Thread.MIN_PRIORITY);
        transactionThread2.setPriority(Thread.MIN_PRIORITY);
         
        // Start the monitor
        balanceMonitorThread1.start();
         
        // And later, transaction threads tries to execute.
        try {Thread.sleep(100l);} catch (InterruptedException e) {}
        transactionThread1.start();
        transactionThread2.start();
 
    }
 
}
class BalanceMonitor implements Runnable {
    private BankAccount account;
    BalanceMonitor(BankAccount account) { this.account = account;}
    boolean alreadyNotified = false;
     
    @Override
    public void run() {
        System.out.format("%s started execution%n", Thread.currentThread().getName());
        while (true) {
            if(account.getBalance() <= 0) {
                // send email, or sms, clouds of smoke ...
                break;
            }
        }
        System.out.format("%s : account has gone too low, email sent, exiting.", Thread.currentThread().getName());
    }
     
}
class Transaction implements Runnable {
    private BankAccount sourceAccount, destinationAccount;
    private double amount;
 
    Transaction(BankAccount sourceAccount, BankAccount destinationAccount, double amount) {
        this.sourceAccount = sourceAccount;
        this.destinationAccount = destinationAccount;
        this.amount = amount;
    }
 
    public void run() {
        System.out.format("%s started execution%n", Thread.currentThread().getName());
        sourceAccount.transfer(destinationAccount, amount);
        System.out.printf("%s completed execution%n", Thread.currentThread().getName());
    }
 
}


```


