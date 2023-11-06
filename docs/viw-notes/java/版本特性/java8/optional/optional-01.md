
<!-- TOC -->

- [optional](#optional)
  - [创建](#创建)
  - [判断](#判断)

<!-- /TOC -->


# optional







![image-20210602004116439](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20210602004116439.png)



## 创建

>

```java
    /**
     * 创建optional
     */
    @Test
    public void test1() {
        // 1.  of(T t) t not null
//        Book book = null;
//        Optional<Book> book1 = Optional.of(book);// exception book is null
        // 2. ofNullable(T t)创建一个t的optional t可 为null
        Book book = new Book() ;
        Optional<Book> book2 = Optional.ofNullable(book);
       // System.out.println(book2);//Optional[Book(name=null)]

        Book book1 = null;
        Optional<Book> book3 = Optional.ofNullable(book1);
        System.out.println(book3);//Optional.empty

        // 3. empty 创建一个空 optionak
        Optional<Object> empty = Optional.empty();
    }
```



## 判断

>

```java

    /**
     *
     */
    @Test
    public void test2(){
        Book book =null;
     //   System.out.println(book.getName().charAt(1));//java.lang.NullPointerException
        Optional<Book> book1 = Optional.ofNullable(book);
        Book book2 = book1.orElse(new Book("默认值"));
        System.out.println(book2.toString());// Book(name=默认值)

        Book book11 =new Book();
        //   System.out.println(book.getName().charAt(1));//java.lang.NullPointerException
        Optional<String> name = Optional.ofNullable(book11.getName());
        System.out.println(name.isPresent());//false
        String c = name.orElse("默认值");
        System.out.println(String.valueOf(c));//默认值

    }
```

