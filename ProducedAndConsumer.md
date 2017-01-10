```java

public class ProducerConsumerPattern {

    public static void main(String[] args) {

        BlockingQueue blockingQueue = new LinkedBlockingQueue();

        Thread producer = new Thread(new Producer(blockingQueue));

        Thread consumer = new Thread(new Consumer(blockingQueue));

        producer.start();
        consumer.start();

    }


    static class Producer implements Runnable {
        private BlockingQueue blockingQueue;

        public Producer(BlockingQueue blockingQueue) {
            this.blockingQueue = blockingQueue;
        }

        @Override
        public void run() {

            for (int i = 0; i < 10; i++) {
                try {
                    System.out.println("Produced: " + i);
                    blockingQueue.put(i);
                } catch (InterruptedException ex) {
                    Logger.getLogger(Producer.class.getName()).log(Level.SEVERE, null, ex);
                }


            }

        }
    }


    static class Consumer implements Runnable {
        private BlockingQueue blockingQueue;

        public Consumer(BlockingQueue blockingQueue) {
            this.blockingQueue = blockingQueue;
        }

        @Override
        public void run() {
            while (true) {

                try {
                    System.out.println("Consumer: " + blockingQueue.take());
                } catch (InterruptedException ex) {
                    Logger.getLogger(Producer.class.getName()).log(Level.SEVERE, null, ex);


                }
            }
        }

    }
}


```
