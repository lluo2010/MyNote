# RxJava随感


1. reactive: 响应, 可以理解为Observer把接收到数据或者事件后该怎么处理已经设想好(实现好), 只要数据/事件一发生就马上响应的意思?
1. Observer的实现不必关心事件是同步还是异步过来的.
1. 事件或者数据的产生是在OnSubscribe.call(Subscriber)里实现的, Observable包含一个onSubscribe. 而它的执行是通过Observable.subscribe(S)

    ```
    public class Observable<T> {
        final OnSubscribe<T> onSubscribe;
        ...
    ```

1.
