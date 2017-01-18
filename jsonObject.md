
# JSONObject & JSONArray study note


## JSONObject

---

```
public class JSONObject extends Object 
```

>A modifiable set of name/value mappings. Names are unique, non-null strings. Values may be any mix of JSONObjects, JSONArrays, Strings, Booleans, Integers, Longs, Doubles or NULL. Values may not be null, NaNs, infinities, or of any type not listed here.
是一个name/value的set(以为着不可重复). 是一个无序的键/值对集合。 名称是唯一的, 不是null的字符串, value可以是各种混合的类型JSONObjects, JSONArrays, 也可以是Strings, Booleans, Integers, Longs, Doubles or JSONOBJECt.NULL,但不可以是null.使用get()和opt()方法通过键来访问值，和使用put()/putOpt()方法通过键来添加或者替代值的对象。 


Warning: this class represents null in two incompatible ways: the standard Java null reference, and the sentinel value NULL. In particular, calling put(name, null) removes the named entry from the object but put(name, JSONObject.NULL) stores an entry whose value is JSONObject.NULL.

不要put(name,null), 因为put(name, null)实际上是将name从object中移除, 可以使用put(name, JSONObject.NULL) 往里面存放一个JSONObject.NULL.

Instances of this class are not thread safe. Although this class is nonfinal, it was not designed for inheritance and should not be subclassed. 该对象不是线程安全的.尽管该类不是final,但是不要从其派生子类.


### 构建

1. 使用构造函数构建

    构造函数声明|说明
    -|-
    JSONObject()| Creates a JSONObject with no name/value mappings.
    JSONObject(Map copyFrom)| Creates a new JSONObject by copying all name/value mappings from the given map.
    JSONObject(JSONTokener readFrom)| Creates a new JSONObject with name/value mappings from the next object in the tokener.
    JSONObject(String json)| Creates a new JSONObject with name/value mappings from the JSON string.
    JSONObject(JSONObject copyFrom, String[] names)| Creates a new JSONObject by copying mappings for the listed names from the given object.


### 获取

根据name获取对应的value.

获取分两种, getXXX(name)和optXXX(name), 两者的区别在于对如果没有匹配的value时的处理方式:

- 对于getXXX, 如果没有相应的value, 抛出JSONException.
- 对于optXXX, 如果没有相应的value, 返回null,或者指定一个备用的默认值.

1. getXXX方法

    方法|描述
    -|-
    Object get (String)|Returns the value mapped by name, or throws if no such mapping exists.
    boolean getBoolean (String)| Returns the value mapped by name if it exists and is a boolean or can be coerced to a boolean, or throws otherwise.
    double getDouble (String)| Returns the value mapped by name if it exists and is a double or can be coerced to a double, or throws otherwise.
    int getInt (String)| Returns the value mapped by name if it exists and is an int or can be coerced to an int, or throws otherwise.
    long getLong (String)| Returns the value mapped by name if it exists and is a long or can be coerced to a long, or throws otherwise.
    String getString (String)| Returns the value mapped by name if it exists, coercing it if necessary, or throws if no such mapping exists.
    JSONArray getJSONArray (String)| Returns the value mapped by name if it exists and is a JSONArray, or throws otherwise.
    JSONObject getJSONObject (String)| Returns the value mapped by name if it exists and is a JSONObject, or throws otherwise.

1. optXXX方法

    方法|描述
    -|-
    Object opt (String)|Returns the value mapped by name, or null if no such mapping exists.
    boolean optBoolean (String, boolean)| Returns the value mapped by name if it exists and is a boolean or can be coerced to a boolean, or fallback otherwise.
    boolean optBoolean (String)| Returns the value mapped by name if it exists and is a boolean or can be coerced to a boolean, or false otherwise.
    double optDouble (String, double)| Returns the value mapped by name if it exists and is a double or can be coerced to a double, or fallback otherwise.
    double optDouble (String)| Returns the value mapped by name if it exists and is a double or can be coerced to a double, or NaN otherwise.
    int optInt (String, int)| Returns the value mapped by name if it exists and is an int or can be coerced to an int, or fallback otherwise.
    int optInt (String)| Returns the value mapped by name if it exists and is an int or can be coerced to an int, or 0 otherwise.
    long optLong (String, long)| Returns the value mapped by name if it exists and is a long or can be coerced to a long, or fallback otherwise. 
    long optLong (String)| Returns the value mapped by name if it exists and is a long or can be coerced to a long, or 0 otherwise. 
    String optString (String, String)| Returns the value mapped by name if it exists, coercing it if necessary, or fallback if no such mapping exists.
    String optString (String)| Returns the value mapped by name if it exists, coercing it if necessary, or the empty string if no such mapping exists.
    JSONArray optJSONArray (String)| Returns the value mapped by name if it exists and is a JSONArray, or null otherwise.
    JSONObject optJSONObject (String)| Returns the value mapped by name if it exists and is a JSONObject, or null otherwise.


### 添加/更新

将相应的key/value放入. 有两种方法, put(name, value)和putOpt(name, value), 两者的区别主要表现在name或者value为null情况下的不同处理, 如下:

- put(name, value), 如果value为null, 会将原来的name/value移除.
- putOpt(name, value), 如果name或者value为null, 什么都不做. 

如果name,value都不为null, 这两个是一样处理的.

针对不同的value类型, 重载了多个方法. value可以对应a JSONObject, JSONArray, String, Boolean, Integer, Long, Double, NULL, or null.

**注意: 对于使用put(), 如果value为null, 是要将这个key/value移除, 所以尽量不使用null. 如果需要放入null, 使用JSONOBJECT.NULL. **

另外一个是accumulate, 就是往Json里面的某个array里面继续添加一个元素(Appends value to the array already mapped to name),原型如下:

```
JSONObject accumulate (String name, Object value)
```



### 是否包含的查询

可以使用has或者isNull.

1. has

    当包含name是返回true,当value为JSONOBJECT.NULL,即为null值时, 也返回true.

    ```
    boolean has (String name)
    ```

1. isNull

    当没有对应的name, 或者有对应的name, 但是里面的value为JSONOBJECT.NULL,即为null值时, 返回true.

    ```
    boolean isNull (String name)
    ```

    由上面可以看出, isNull为true的时候, has不一定为false, 这里有一个null的场景.


### 其他

1. 获取长度

    使用length():

    >int length ()  
    >Returns the number of name/value mappings in this object.

1. 获取所有的key

    使用keys(), 返回值为Iterator, 这个iterator支持remove:

    ```
    Iterator<String> keys ()
    ```

1. 获取所有的name

    使用names(), 如果JSONOBJECT为空,返回null:

    >JSONArray names ()
    Returns an array containing the string names in this object. This method returns null if this object contains no mappings.

1. 删除

    使用remove, 返回值为被删除掉的value, 如果没有匹配的name, 返回null:

    ```
    Object remove (String name)
    ```

1. toString()

    返回对应的Json字符串.

    toString ()返回如下:

    ```
    {"query":"Pizza","locations":[94043,90210]}

    ```

    toString (int indentSpaces)指定锁紧空格,更适合阅读, 返回类似如下:

    ```
    {
        "query": "Pizza",
        "locations": [
            94043,
            90210
        ]
    }
    ```


## JSONArray

---

```
public class JSONArray extends Object 
```

>A dense indexed sequence of values. Values may be any mix of JSONObjects, other JSONArrays, Strings, Booleans, Integers, Longs, Doubles, null or NULL. Values may not be NaNs, infinities, or of any type not listed here.  

>JSONArray has the same type coercion behavior and optional/mandatory accessors as JSONObject.

它是一个有序的序列值。它的表现形式是一个包裹在方括号的字符串，值和值之间使用逗号隔开。 
可以使用get()和opt()方法通过索引来访问值，和使用put()/putOpt()方法来添加或修改值的对象。 


它的value可以是任何混合的JSONObjects, other JSONArrays, Strings, Booleans, Integers, Longs, Doubles, null or JSONOBJECT.NULL.

** 它的方法也和JSONOBJECT类似, 比如有putXXX, optXXX, remove, toString,length.只是JSONOBJECT使用的是name去获取, 而这里使用index去获取. **


### 构建

构造方法|说明
-|-
JSONArray()| Creates a JSONArray with no values.
JSONArray(Collection copyFrom)| Creates a new JSONArray by copying all values from the given collection.
JSONArray(JSONTokener readFrom)| Creates a new JSONArray with values from the next array in the tokener.
JSONArray(String json)| Creates a new JSONArray with values from the JSON string.
JSONArray(Object array)| Creates a new JSONArray with values from the given primitive array.



## JSONStringer

---

是一个用于快速构造JSON文本的工具, JSONWriter的子类.

一些信息如下:

- object()开始一个对象，即添加{ ; enObject()结束一个对象，即添加}.
- array()开始一个数组，即添加[ ；endArray()结束一个数组，即添加] .
- key()表示添加一个key；value()表示添加一个value.



## 例子

---

### Json串的构建

1. 方法一: 使用JSONObject

    ```
    private JSONObject toJSON()
    {
        JSONObject event = new JSONObject();
        try
        {
            event.put("isPublic", true);
            event.put("age", 10);
            event.put("origin", "hangzhou"); //done
            event.accumulate("origin", "wenzhou"); //done
            Log.i(TAG, "toJson:"+event.toString(3));
            return event;
        }
        catch(JSONException e)
        {
            throw new RuntimeException(e);
        }
    }
    ```

    输出:

    ```
    toJson:{
    "origin": [
        "hangzhou",
        "wenzhou"
    ],
    "age": 10,
    "isPublic": true
    }
    ```

    也可以根据映射数组从一个JSONObject中获取一个子JSONObject, 如下:

    ```
    JSONObject jsonObj = ...
    String[] strNames = {"isPublic","age"};
    try {
        JSONObject obj = new JSONObject(jsonObj, strNames);
        Log.i(TAG, "new json obj:"+obj.toString(INDENT_SPACE_NUM));
    }catch(JSONException ex){
    }
    ```
    可以看到obj是从jsonObj里包含strNames中字段的内容. 如果jsonObj是面个例子中的内容,则obj输出如下:

    ```
    new json obj:{
    "age": 10,
    "isPublic": true
    }
    ```

1. 方法二: 使用JSONStringer

    ```
    private static String prepareJSONObject2(){  
        JSONStringer jsonStringer = new JSONStringer();  
        try {  
            jsonStringer.object();  
            jsonStringer.key("name");  
            jsonStringer.value("Jason");  
            jsonStringer.key("id");  
            jsonStringer.value(20130001);  
            jsonStringer.key("phone");  
            jsonStringer.value("13579246810");  
            jsonStringer.endObject();  
        } catch (JSONException e) {  
            e.printStackTrace();  
        }  
        return jsonStringer.toString();  
    }  
    ```

### 获取字段

获取字段使用getXXX或者optXXXX.

1. optXXX获取, 如果没有匹配返回null:

    ```
    private void preloadMetaIds() {
        JSONObject jsonMeta = getJSONMeta();
        if (jsonMeta == null)
            return;
        JSONObject jsonIDs = jsonMeta.optJSONObject("ids");
        if (jsonIDs == null)
            return;
        mBlogId = jsonIDs.optInt("site");
        mPostId = jsonIDs.optInt("post");
        mCommentId = jsonIDs.optLong("comment");
        mCommentParentId = jsonIDs.optLong("comment_parent");
    }
    ```

1. getXXX, 如果没有匹配则抛出JSONException:

    ```
    protected void loadDataAndLogin(JSONObject response) {
        if (response != null) {
            try {
                String csrfToken = response.getString("csrf_token");
                FlowAsyncClient.setCsrfToken(csrfToken);
            } catch (JSONException e) {
                Log.e(TAG, "Could not extract CSRF token from JSON response " + response.toString());
                Crashlytics.logException(e);
            }
        }
    ```

## temp

---

1. [JSONObject & JSONArray](http://www.jianshu.com/p/d406083e698e)
1. [Android JOSN 解析](http://www.jianshu.com/p/ff7cc01cc532)
1. [Android系列---JSON数据解析](http://www.cnblogs.com/xiaoluo501395377/p/3446605.html)



## Reference

---

1. [JSON入门之二：org.json的基本用法](http://blog.csdn.net/jediael_lu/article/details/25779087)
1. [Java Code Examples for org.json.JSONObject](http://www.programcreek.com/java-api-examples/index.php?api=org.json.JSONObject)
1. [JSON解析工具-org.json使用教程](http://www.open-open.com/lib/view/open1381566882614.html)