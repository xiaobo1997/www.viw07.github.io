[TOC]



# 生产者消费者-模型



## 通知机制



```java

public class produceConsume {
    class Producer implements Runnable {
        private LinkedList<Integer> resources;
        /*当前仓库最大容量*/
        private int maxSize;

        /*需要生产的产品总数*/
        private int proSize;

        public Producer(LinkedList<Integer> resources, int maxSize, int proSize) {
            this.resources = resources;
            this.maxSize = maxSize;
            this.proSize = proSize;
        }

        @Override
        public void run() {
            synchronized (resources) {
                for (int i = 0; i < proSize; i++) {
                    // 当生产的产品总数大于仓库容量时，进行阻塞
                    while (resources.size() > maxSize) {
                        System.out.println("当前仓库已满，等待消费...");
                        try {
                            // 进入阻塞状态
                            resources.wait();
                        } catch (InterruptedException e) {
                            e.printStackTrace();
                        }
                    }
                    System.out.println("已经生产产品数:" + i + "\t现仓储量总量:" + resources.size());
                    resources.add(i);
                    // 唤醒所有消费者
                    resources.notifyAll();
                    // 唤醒单个消费者
                    //resources.notify();
                }
            }
        }
    }

    class Consumer implements Runnable {
        private LinkedList<Integer> resources;

        public Consumer(LinkedList<Integer> resources) {
            this.resources = resources;
        }

        @Override
        public void run() {
            String threadName = "thread_name" + Thread.currentThread().getName();
            while (true) {
                synchronized (resources) {
                    // 当前仓库没有产品的时候，进行阻塞
                    while (resources.size() <= 0) {
                        System.out.println(threadName + " 当前仓库没有产品，请稍等...");
                        try {
                            // 进入阻塞状态
                            resources.wait();
                        } catch (InterruptedException e) {
                            e.printStackTrace();
                        }
                    }
                    // 消费数据
                    int size = resources.size();
                    for (int i = 0; i < size; i++) {
                        Integer remove = resources.remove();
                        System.out.println(threadName + " 当前消费产品编号为：" + remove);
                    }
                    // 唤醒生产者
                    resources.notify();
                }
            }
        }
    }

    public static void main(String[] args) throws InterruptedException {
        LinkedList<Integer> integers = new LinkedList<>();
        produceConsume produceConsume = new produceConsume();
        Producer producer = produceConsume.new Producer(integers, 10, 30);
        Consumer consumer = produceConsume.new Consumer(integers);

        Thread producerThread = new Thread(producer);
        Thread consumer1thread = new Thread(consumer, "consumer_1");
        Thread consumer2thread = new Thread(consumer, "consumer_2");

        consumer2thread.start();
        consumer1thread.start();
        TimeUnit.SECONDS.sleep(2);
        producerThread.start();
    }
}
//
thread_nameconsumer_2 当前仓库没有产品，请稍等...
thread_nameconsumer_1 当前仓库没有产品，请稍等...
已经生产产品数:0	现仓储量总量:0
已经生产产品数:1	现仓储量总量:1
已经生产产品数:2	现仓储量总量:2
已经生产产品数:3	现仓储量总量:3
已经生产产品数:4	现仓储量总量:4
已经生产产品数:5	现仓储量总量:5
已经生产产品数:6	现仓储量总量:6
已经生产产品数:7	现仓储量总量:7
已经生产产品数:8	现仓储量总量:8
已经生产产品数:9	现仓储量总量:9
已经生产产品数:10	现仓储量总量:10
当前仓库已满，等待消费...
thread_nameconsumer_1 当前消费产品编号为：0
thread_nameconsumer_1 当前消费产品编号为：1
thread_nameconsumer_1 当前消费产品编号为：2
thread_nameconsumer_1 当前消费产品编号为：3
thread_nameconsumer_1 当前消费产品编号为：4
thread_nameconsumer_1 当前消费产品编号为：5
thread_nameconsumer_1 当前消费产品编号为：6
thread_nameconsumer_1 当前消费产品编号为：7
thread_nameconsumer_1 当前消费产品编号为：8
thread_nameconsumer_1 当前消费产品编号为：9
thread_nameconsumer_1 当前消费产品编号为：10
thread_nameconsumer_1 当前仓库没有产品，请稍等...
thread_nameconsumer_2 当前仓库没有产品，请稍等...
已经生产产品数:11	现仓储量总量:0
已经生产产品数:12	现仓储量总量:1
已经生产产品数:13	现仓储量总量:2
已经生产产品数:14	现仓储量总量:3
已经生产产品数:15	现仓储量总量:4
已经生产产品数:16	现仓储量总量:5
已经生产产品数:17	现仓储量总量:6
已经生产产品数:18	现仓储量总量:7
已经生产产品数:19	现仓储量总量:8
已经生产产品数:20	现仓储量总量:9
已经生产产品数:21	现仓储量总量:10
当前仓库已满，等待消费...
thread_nameconsumer_2 当前消费产品编号为：11
thread_nameconsumer_2 当前消费产品编号为：12
thread_nameconsumer_2 当前消费产品编号为：13
thread_nameconsumer_2 当前消费产品编号为：14
thread_nameconsumer_2 当前消费产品编号为：15
thread_nameconsumer_2 当前消费产品编号为：16
thread_nameconsumer_2 当前消费产品编号为：17
thread_nameconsumer_2 当前消费产品编号为：18
thread_nameconsumer_2 当前消费产品编号为：19
thread_nameconsumer_2 当前消费产品编号为：20
thread_nameconsumer_2 当前消费产品编号为：21
thread_nameconsumer_2 当前仓库没有产品，请稍等...
thread_nameconsumer_1 当前仓库没有产品，请稍等...
已经生产产品数:22	现仓储量总量:0
已经生产产品数:23	现仓储量总量:1
已经生产产品数:24	现仓储量总量:2
已经生产产品数:25	现仓储量总量:3
已经生产产品数:26	现仓储量总量:4
已经生产产品数:27	现仓储量总量:5
已经生产产品数:28	现仓储量总量:6
已经生产产品数:29	现仓储量总量:7
thread_nameconsumer_1 当前消费产品编号为：22
thread_nameconsumer_1 当前消费产品编号为：23
thread_nameconsumer_1 当前消费产品编号为：24
thread_nameconsumer_1 当前消费产品编号为：25
thread_nameconsumer_1 当前消费产品编号为：26
thread_nameconsumer_1 当前消费产品编号为：27
thread_nameconsumer_1 当前消费产品编号为：28
thread_nameconsumer_1 当前消费产品编号为：29
thread_nameconsumer_1 当前仓库没有产品，请稍等...
thread_nameconsumer_2 当前仓库没有产品，请稍等...
```



## 阻塞队列

