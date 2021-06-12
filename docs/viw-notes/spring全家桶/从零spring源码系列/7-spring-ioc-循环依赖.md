[TOC]



# 循环依赖



## 概念





图来自`https://juejin.cn/post/6844904122160775176#comment`

![image-20201221234910556](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20201221234910556.png)

## 循环依赖的本质是什么

> 



## spring是怎么解决循环依赖的



> 在spring中主要是通过缓存来解决的，如果是set方法di的，循环依赖是可以解决的，构造器就解决不了，除了你去改代码，但是一个是set注入，后面一个是构造器还是可以解决的，因为创建bean是有顺序的，而循环依赖中的多级缓存其实就是三个map，我们需要先去map中去找 bean，也就是缓存里面，如果没有的话就放进去，如果有其他bean依赖这个bean的话，就可以从这个map中取出来给他们用了。那三个map分别是 *singletonObjects* ，一个单例bean的池子，*singletonFactories* ，一个映射创建bean的工厂，*earlySingletonObjects* 。这个map中的bean这是一个bean Instance，后面两个Map只是为辅助第一个完成创建bean，创建完成就清除。
>
> 二级缓存解决不了，因为如果二级缓存的话  bean初始化就要完成aop代理，而在spring中是通过后置处理器来让bean在生命周期最后一步完成aop代理，而不是实例化就aop
>
> 引用：
>
> Spring通过三级缓存解决了循环依赖，其中一级缓存为单例池（`singletonObjects`）,二级缓存为早期曝光对象`earlySingletonObjects`，三级缓存为早期曝光对象工厂（`singletonFactories`）。当A、B两个类发生循环引用时，在A完成实例化后，就使用实例化后的对象去创建一个对象工厂，并添加到三级缓存中，如果A被AOP代理，那么通过这个工厂获取到的就是A代理后的对象，如果A没有被AOP代理，那么这个工厂获取到的就是A实例化的对象。当A进行属性注入时，会去创建B，同时B又依赖了A，所以创建B的同时又会去调用getBean(a)来获取需要的依赖，此时的getBean(a)会从缓存中获取，第一步，先获取到三级缓存中的工厂；第二步，调用对象工工厂的getObject方法来获取到对应的对象，得到这个对象后将其注入到B中。紧接着B会走完它的生命周期流程，包括初始化、后置处理器等。当B创建完后，会将B再注入到A中，此时A再完成它的整个生命周期。至此，循环依赖结束！
>
> 