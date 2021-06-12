

1. 打开页面或者关闭页面时触发，兼容IE8

```java

        window.onload = windowOnload;//加载页面触发事件
        window.onbeforeunload = windowOnBeforeUnload;//关闭页面触发事件
        function windowOnBeforeUnload() {
           .....
        }
        function windowOnload() {
            .....
        }
```

