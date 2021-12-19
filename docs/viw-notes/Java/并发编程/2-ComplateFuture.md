
# complateFuture异步线程池

## 前言

> 异步线程池是1.8juc下面的Future接口的实现类，可以处理并返回结果，


## 代码

```java

package com.viw.jdk11.juc;

import lombok.extern.slf4j.Slf4j;
import org.junit.jupiter.api.Test;

import java.util.concurrent.*;

/**
 * @user xhb
 * @Classname jdk11-com.viw.jdk11.juc-ComplateFuture
 * @email xiaohaibo77@gmail.com
 * @Date 2021/12/19
 * @Description :
 * 1.
 */
@Slf4j
public class ComplateFuture {

    /**
     * 随便创建一个线程池
     */
    public static ExecutorService threadPool = Executors.newFixedThreadPool(5);


    /**
     * runAsync 和 supplyAsync
     *
     * @throws ExecutionException
     * @throws InterruptedException
     */
    @Test
    public void test1() throws ExecutionException, InterruptedException {
        //runAsync的类型是void  没有返回结果
        var voidCompletableFuture = CompletableFuture.runAsync(() -> {
            System.out.println("runAsync...");
        }, threadPool);
        //有返回结果
        var stringCompletableFuture = CompletableFuture.supplyAsync(() ->
                        "supplyAsync.."
                , threadPool);
        System.out.println(stringCompletableFuture.get());
    }

    /**
     * 处理上一步的执行结果
     *
     * @throws Exception
     */
    @Test
    public void test2() throws Exception {
        //处理上一步的结果 没有处理异常的情况
        CompletableFuture<String> stringCompletableFuture = CompletableFuture.supplyAsync(() -> "1", threadPool).whenComplete((res, ex) -> {
            // do something
            log.info("异常:{}", ex);
            log.info("上一个执行结果: 处理结果... " + res);
        });
        log.info(stringCompletableFuture.get());
        System.out.println("====");

        //处理future结果，并且处理出现异常的情况
        CompletableFuture<String> stringCompletableFuture1 = CompletableFuture.supplyAsync(() -> "1", threadPool).whenComplete((res, ex) -> {
            log.info("异常:{}", ex);
            log.info("上一个执行结果: 处理结果... " + res);
            int i = 10 / 0;
        }).exceptionally(ex -> {
            // do something  感知异常并处理异常
            log.info("{}", ex.getMessage());
            //throw new RuntimeException("异常的处理");
            return "异常了";
        });
        log.info("{}", stringCompletableFuture1.get());
        /**
         * 执行结果
         * 18:25:21.368 [main] INFO com.viw.jdk11.juc.ComplateFuture - 异常:{}
         * 18:25:21.370 [main] INFO com.viw.jdk11.juc.ComplateFuture - 上一个执行结果: 处理结果... 1
         * 18:25:21.371 [main] INFO com.viw.jdk11.juc.ComplateFuture - java.lang.ArithmeticException: / by zero
         * 18:25:21.374 [main] INFO com.viw.jdk11.juc.ComplateFuture - 异常了
         */
        System.out.println("=====");
        //使用不同的线程来处理结果  async结尾
        CompletableFuture<String> stringCompletableFuture2 = CompletableFuture.supplyAsync(() -> "1", threadPool).whenCompleteAsync((res, ex) -> {
            log.info("异常:{}", ex);
            log.info("上一个执行结果: 处理结果... " + res);
            int i = 10 / 0;
        }).exceptionally(ex -> {
            // do something  感知异常并处理异常
            log.info("{}", ex.getMessage());
            //throw new RuntimeException("异常的处理");
            return "异常了";
        });
        log.info("{}", stringCompletableFuture1.get());
    }


    /**
     * handle处理并修改返回结果
     */
    @Test
    public void test3() throws ExecutionException, InterruptedException {
        // 处理并改变返回结果
        CompletableFuture<String> future = CompletableFuture.supplyAsync(() -> {
            System.out.println("OK");
            String s = "结果";
            return s;
        }, threadPool).handle((result, exception) -> {
            // do something
            if (exception != null) {
                return "异常，业务处理";
            }
            if (result != null) {
                return "没异常业务处理：" + result;
            }
            return result;
        });
        System.out.println(future.get());
        System.out.println("main-end");
    }


    /**
     * * 线程串行化
     * * 1）、thenRun：不能获取到上一步的执行结果，无返回值
     * *  .thenRunAsync(() -> {
     * *             System.out.println("任务2启动了...");
     * *         }, executor);
     * * 2）、thenAcceptAsync;能接受上一步结果，但是无返回值
     * * 3）、thenApplyAsync：;能接受上一步结果，有返回值
     */
    @Test
    public void test5() throws ExecutionException, InterruptedException, TimeoutException {
        var thenRun = CompletableFuture.supplyAsync(() -> {
            return 1;
        }, threadPool).thenRun(() -> {
            System.out.println("thenRun");
        });
        System.out.println("=====");
        var c2 = CompletableFuture.supplyAsync(() -> {
            return 1;
        }).thenAccept((res) -> {
            if (res == 1) {
                log.info("res{}", res);
            } else {
                log.info("2");
            }
        });

        var c3 = CompletableFuture.supplyAsync(() -> {
            return "1";
        }).thenApply((res) -> {
            if (res.equals(1)) return "thenApply";
            else return "thenApply....";
        });
        log.info("{}", c3.get());
        log.info("{}", c3.get(3, TimeUnit.SECONDS));
        log.info("{}", c3.getNow("新结果"));
    }

    /**
     * 多任务
     */
    @Test
    public void test4() throws ExecutionException, InterruptedException {
        var c1 = CompletableFuture.supplyAsync(() -> "c1", threadPool);
        var c2 = CompletableFuture.supplyAsync(() -> "c2", threadPool);
        var c3 = CompletableFuture.supplyAsync(() -> "c3", threadPool);
        //
        CompletableFuture<Void> voidCompletableFuture = CompletableFuture.allOf(c1, c2, c3);

        CompletableFuture<Object> completableFuture = CompletableFuture.anyOf(c1, c2, c3);
        completableFuture.get();
    }

    /**
     * * 两个任务，只要有一个完成，我们就执行任务3
     * * runAfterEitherAsync：不感知结果，自己没有返回值
     * * acceptEitherAsync：感知结果，自己没有返回值
     * * applyToEitherAsync：感知结果，自己有返回值
     */
    @Test
    public void test6() {
        var c1 = CompletableFuture.supplyAsync(() -> "c1", threadPool);
        var c2 = CompletableFuture.supplyAsync(() -> "c2", threadPool);
        var c3 = CompletableFuture.supplyAsync(() -> "c3", threadPool);
        CompletableFuture<Void> c11 = c1.acceptEitherAsync(c2, (res) -> {
            if (res.equals("c1")) ;
        });


    }

}



```