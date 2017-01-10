# Stone Task note


## Task发起

---

Task的发起如下, 调用Async的task接口,将请求地址和回调TaskBack传进去, 就可以发起一个请求了:

```
Async<List<ProjectCategoryBean>> task = ...;
TaskBack<List<ProjectCategoryBean>> listener = ...;

Type type = new TypeToken<ExecResult<ArrayList<ProjectCategoryBean>>>() {}.getType();
Map<String, String> params = new HashMap<>();
if (UserSession.getToken() != null) {
	params.put("token", UserSession.getToken());
}
task.task(_URL + "project/queryInProgressProjectV3.json", params, type, listener);
```

Async.task其实构建了一个XTask并执行该任务.

XTask是一个从AsyncTask派生的抽象类,它实现了AsyncTask几个接口(onPreExecute,onPostExecute,onCancelled), TaskEngine从XTask派生,实现了doInBackground, 在这里面做真正的任务(网络请求).

```
public abstract class XTask<T extends Serializable> extends AsyncTask<TaskParams<T>, Void, AsyncResult<T>>;
public class TaskEngine<T extends Serializable> extends XTask<T>;
```

关于Task的更多细节见下面.


### 关于Async<T>

从TaskStatusListener接口派生. 它根据传进来的请求参数,产生AysncTask<T>并执行网络请求,并将结果通过回调方式返回.

类图如下:

todo...XXX

AsyncTask作为XTask的一个回调. TaskStatusListener的onStart/onEdn/onCancel分别在AysncTask的onPreExecute/onPostExecute/onCancelled调用, 所以是在UI线程中执行的. 这里的回调并不是回调执行结果,只是回调执行状态(完成了,取消了).


## 任务执行

---

任务的执行是通过AsyncTask的方式执行的, 每个请求在TaskEngine的doInBackground中执行,执行完后在onPostExecute将结果回调. 回调的接口是从TaskParams中获取的, TaskParams从XTask的执行函数execute(xxx)传进来的.

```
@Override
protected void onPostExecute(AsyncResult<T> result) {
	if (mParams != null && getListener() != null)
		getListener().onResult(result);
        super.onPostExecute(result);
}
```


## 任务回调

---

请求任务在TaskEngine的doInBackground里执行,任务执行完后,通过回调将数据返回.

ExecResult包含的是response的信息,包含状态码,错误消息,是否成功,错误类别,以及response真正的数据(是个泛型).

AsyncResul包含ExeResult, 其外还包含了一些其他信息.


### 回调的数据的结构

1. ExecResult<T>

    它包含了response回来的状态码,错误消息,是否成功,错误类别,以及response真正的数据, 数据结构如下,其中T表示我们response回来的数据, 使用泛型,可以表示任意类型的数据:

    ```
    public class ExecResult<T> implements Serializable {
        private static final long serialVersionUID = -8895005939818095491L;
        public boolean success;
        public String errorMsg;
        public String errorType;
        public int code;
        public T result;
    }
    ```

    对应的response的Json如下:

    ```
    {
        "code": "0",
        "errorType": null,
        "errorMsg": "正常",
        "success": true
        "result": [
            {
                "type": 9,
                "title": "安全保障",
                "image": "http://img2.stlc.cn/moduleEntranceIcon/20161001/icon_1475293244.png",
                "url": "http://mobile.stlc.cn/build/html/jsbz/index.html"
            },
            {
                "type": 9,
                "title": "邀请好友",
                "image": "http://img2.stlc.cn/moduleEntranceIcon/20161001/icon_1475293321.png",
                "url": "http://tmobile.stlc.cn/build/html/register/index.html?cid=799"
            },
        ]
    }
    ```

1. AsyncResul<T>

    AsyncResult也是个泛型, 它包含了ExecResult<T>, 还包含了一些其他信息, 以及TaskParams<T>,TaskParams是和每个请求任何相关的一系列参数,具体后面会提到:

    ```
    public final class AsyncResult<T> implements Serializable {
        private static final long serialVersionUID = -8895005939818095491L;
        public String resultStr;
        public T t;

        public LoadFrom loadedFrom = LoadFrom.NET;
        /** net type */
        public NetType netType = NetType.NONE;
        /** exception */
        public Throwable e;
        /** task status */
        public ResultStatus status = ResultStatus.SUCCESS;
        public TaskParams<T> params;
    ```

*** AsyncResult是在TaskEngine<T>的doInBackground中创建的,然后通过回调传回. 而ExecResult是在response被解析后赋值的,然后在给AsyncResult, 然后在TaskBack.onResult中被传给回调***


### 回调接口

这里有两个回调接口:

1. 一个叫TaskStatusListener
    Async<xxx>就是实现了它, 它表示的是任务执行的状态(onEnd(),onStart(),onCancel())
    todo...XXX

1. 一个叫TaskCallBack(及子类TaskBack)
    它表示任务执行的成功是否以及成功后的数据的回调. 这个是保存在TaskParams的listener中的.
    todo...XXX


## 数据类型的指定和解析

---

参数类型通过TypeToken.getType()获取,然后在任务里执行,代码如下:

```
public static void projectList(Async<List<ProjectCategoryBean>> task, TaskBack<List<ProjectCategoryBean>> listener) {
	Type type = new TypeToken<ExecResult<ArrayList<ProjectCategoryBean>>>() {}.getType();
	Map<String, String> params = new HashMap<>();
	if (UserSession.getToken() != null) {
		params.put("token", UserSession.getToken());
	}
	task.task(_URL + "project/queryInProgressProjectV3.json", params, type, listener);

}
```

有了这个Type后, 就使用下面的Gson代码进行转换, 得出对应的类型:

```
return gson.fromJson(result, type);
```



## 总结

---

1. Stone是使用AysncTask执行任务的, 一个请求对应一个AsyncTask. TaskEngine从XTask派生,而XTask从AysncTask派生.
1. 数据结构ExecResult<T>是个泛型, 包含了response回来的状态码,错误消息,是否成功,错误类别,以及response真正的数据T.
1. AsyncResult也是个泛型, 它包含了ExecResult<T>, 还包含了一些其他信息, 以及TaskParams<T>.
1. 回调函是通过AsyncTask的执行参数TaskParams带进去的.
1. 请求是在TaskEngine的doInBackground中执行的,response回来的数据结果解密,gson的反序列化,再通过回调接口回调.