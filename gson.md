# Gson study

## GSON的两个重要方法

---
在GSON的API中，提供了两个重要的方法：toJson()和fromJson()方法。其中，toJson()方法用来实现将Java对象转换为相应的JSON数据，fromJson()方法则用来实现将JSON数据转换为相应的Java对象。

### toJson()方法
toJson()方法用于将Java对象转换为相应的JSON数据，主要有以下几种形式：

+ String toJson(JsonElement jsonElement): 用于将JsonElement对象（可以是JsonObject、JsonArray等）转换成JSON数据.
+ String toJson(Object src): 用于将指定的Object对象序列化成相应的JSON数据
+ String toJson(Object src, Type typeOfSrc): 用于将指定的Object对象（可以包括泛型类型）序列化成相应的JSON数据。


### fromJson()方法
fromJson()方法用于将JSON数据转换为相应的Java对象，主要有以下几种形式：

+ <T> T fromJson(JsonElement json, Class<T> classOfT);
+ <T> T fromJson(JsonElement json, Type typeOfT);
+ <T> T fromJson(JsonReader reader, Type typeOfT);
+ <T> T fromJson(Reader reader, Class<T> classOfT);
+ <T> T fromJson(Reader reader, Type typeOfT);
+ <T> T fromJson(String json, Class<T> classOfT);
+ <T> T fromJson(String json, Type typeOfT);

以上的方法用于将不同形式的JSON数据解析成Java对象。



## Gson的基本用法

---
1. 基本数据类型的解析

		Gson gson = new Gson();

		int i = gson.fromJson("100", int.class);              //100
		Utils.println(String.valueOf(i));
		boolean b = gson.fromJson("true", boolean.class);     // true
		Utils.println(String.valueOf(b));
		String str = gson.fromJson("String", String.class);   // String
		Utils.println(str);

1. 基本数据类型的生成

        Gson gson = new Gson();
		String str = gson.toJson(100);
		Utils.println(str);
		str = gson.toJson(false);
		Utils.println(str);
		str = gson.toJson("hello");
		Utils.println(str);

1. POJO类的生成与解析

        public class Student {
            public int mNAge;
            public String mStrName;
            @Override
            public String toString() {
                StringBuilder sb = new StringBuilder();
                sb.append("age:").append(mNAge)
                .append(",name:").append(mStrName);
                return sb.toString();
            }
        }

    生成json:

        Student stu = new Student();
		stu.mNAge = 12;
		stu.mStrName = "Louis";
		str = gson.toJson(stu);
		Utils.println(str);

    解析json:

        String strGson = "{\"mNAge\":12,\"mStrName\":\"Louis\"}";
        Student stu = gson.fromJson(strGson, Student.class);
      



## 属性重命名

---
可以使用注解@SerializedName重命名属性,也可以使用GsonBuilder的FieldNamingStrategy接口重命名.


### 使用注解@SerializedName
直接在对象的字段上加@SerializedName.

使用的实体类如下:

```
    public class RenameInfo {
        public int field1;
        @SerializedName("field_2")
        public int field2;
    }
```
代码如下:

```
    RenameInfo info = new RenameInfo();
    info.field1 = 1;
    info.field2 = 4;
    Gson gson = new Gson();
    String str = gson.toJson(info);
```
通过使用@SerializedName("field_2"), field2变成field_2.

### 使用FieldNamingStrategy接口
实现FieldNamingStrategy, 然后设置到GsonBuilder,然后利用GsonBuilder生成Gson, 具体代码如下:

        private void usingFieldNamingStrategy(){
            Utils.printHead("usingFieldNamingStrategy");
            RenameInfo info = new RenameInfo();
            info.field1 = 1;
            info.field2 = 4;
            GsonBuilder gb = new GsonBuilder();
            Gson gson = gb.setFieldNamingStrategy(new FieldNamingStrateg() {
                
                @Override
                public String translateName(Field f) {
                    if (f.getName().equals("field1")){
                        return "field___1";
                    }else{
                        return f.getName();
                    }
                }
            }).create();
            
            String str = gson.toJson(info);
            Utils.println(str); //输出变成"{"field___1":1,"field_2":4}", field1变成"field___1"
        }

通过上面代码,field1变成field___1.

## 为POJO字段提供备选属性名

---
SerializedName注解提供了两个属性，上面用到了其中一个，别外还有一个属性alternate，接收一个String数组。
注：alternate需要2.4版本

    @SerializedName(value = "emailAddress", alternate = {"email", "email_address"})
    public String emailAddress;

当上面的三个属性(email_address、email、emailAddress)都中出现任意一个时均可以得到正确的结果。也就是说json string里只要包含其中的任何一个,都可以转成其他对象.


## 泛型相关

---
### 一般的数组
一般数组的解析比较简单, 可以采用如下的方法:

```
Gson gson = new Gson();
String jsonArray = "[\"Android\",\"Java\",\"PHP\"]";
String[] strings = gson.fromJson(jsonArray, String[].class);
``` 

### 泛型类型
但是对于泛型就不是这样了,如果上面的类型是List<String>,则采用List<String>.class 是行不通的。对于Java来说List<String> 和List<User> 这俩个的字节码文件只一个那就是List.class，这是Java泛型使用时要注意的问题 泛型擦除。

Gson为我们提供了TypeToken来实现对泛型的支持，所以当我们希望使用将以上的数据解析为List<String>时需要这样写:

```
Gson gson = new Gson();
String jsonArray = "[\"Android\",\"Java\",\"PHP\"]";
String[] strings = gson.fromJson(jsonArray, String[].class);
List<String> stringList = gson.fromJson(jsonArray, new TypeToken<List<String>>() {}.getType());
```

*** 注：TypeToken的构造方法是protected修饰的,所以上面才会写成new TypeToken<List<String>>() {}.getType() 而不是  new TypeToken<List<String>>().getType()***
即构造了一个匿名类的对象,然后通过getType()去取它对应的Type.

关于TypeToken:

>public class TypeToken<T>
extends Object
Represents a generic type T. Java doesn't yet provide a way to represent generic types, so this class does. Forces clients to create a subclass of this class which enables retrieval the type information even at runtime.
For example, to create a type literal for List<String>, you can create an empty anonymous inner class:  
>
>TypeToken<List<String>> list = new TypeToken<List<String>>() {};


## 关于GsonBuilder

---
>public final class GsonBuilder
extends Object
Use this builder to construct a Gson instance when you need to set configuration options other than the default. For Gson with default configuration, it is simpler to use new Gson(). GsonBuilder is best used by creating it, and then invoking its various configuration methods, and finally calling create.

一般情况下, 我们采用new Gson()去创建Gson,这时候所有的配置都是默认的,如果我们需要去设置一些配置选项, 则可以通过GsonBuilder去设置一些配置
下面是使用GsonBuilder创建Gson的例子:

```
Gson gson = new GsonBuilder()
     .registerTypeAdapter(Id.class, new IdTypeAdapter())
     .enableComplexMapKeySerialization()
     .serializeNulls()
     .setDateFormat(DateFormat.LONG)
     .setFieldNamingPolicy(FieldNamingPolicy.UPPER_CAMEL_CASE)
     .setPrettyPrinting()
     .setVersion(1.0)
     .create();
```

### GsonBuilder用法

一般的用法如下:

```
Gson gson = new GsonBuilder()
               //各种配置
               .create(); //生成配置好的Gson
```

#### 常用设置

##### 1. serializeNulls
>Configure Gson to serialize null fields.

Gson在默认情况下是不动导出值null的键的，如：

```
public class User {
    //省略其它
    public String name;
    public int age;
    public String email;
}
Gson gson = new Gson();
User user = new User("怪盗kidou",24);
System.out.println(gson.toJson(user)); //{"name":"怪盗kidou","age":24}
```
可以看出，email字段是没有在json中出现的. 使用serializeNulls()可以让null的也出现, 如下:

```
Gson gson = new GsonBuilder()
        .serializeNulls()
        .create();
```

##### 2. setPrettyPrinting
>Configures Gson to output Json that fits in a page for pretty printing.

```
Gson gson = new GsonBuilder()
        //序列化null
        .serializeNulls()
        //格式化输出
        .setPrettyPrinting()
        .create();
```

##### 3. setDateFormat
设置日期时间格式, 使用如下:

```
Gson gson = new GsonBuilder()
        // 设置日期时间格，另有2个重载方法
        // 在序列化和反序化时均生效
        .setDateFormat("yyyy-MM-dd")
        .create();
```

##### 4. 其他:
如下:

```
Gson gson = new GsonBuilder()
        //序列化null
        .serializeNulls()
        // 设置日期时间格，另有2个重载方法
        // 在序列化和反序化时均生效
        .setDateFormat("yyyy-MM-dd")
        // 禁此序列化内部类
        .disableInnerClassSerialization()
        //生成不可执行的Json（多了 )]}' 这4个字符）
        .generateNonExecutableJson()
        //禁止转义html标签
        .disableHtmlEscaping()
        //格式化输出
        .setPrettyPrinting()
        .create();
```


## 排除过滤字段的几种方法

---
### 1.使用@Expose
>An annotation that indicates this member should be exposed for JSON serialization or deserialization.
>
>This annotation has no effect unless you build Gson with a GsonBuilder and invoke GsonBuilder.excludeFieldsWithoutExposeAnnotation() method.

使用@Expose标明哪些字段会被序列化反序列化, 注意,@Expose只有在使用GsonBuilder创建Gson而且excludeFieldsWithoutExposeAnnotation被调用的时候才有效, 在这种情况下,没有添加@Expose的字段都不会被序列化反序列化.

@Expose包含下面两个属性:

类型|名称|说明
-|:-|:-
boolean|deserialize|如果为true,能从Json反序列化回来,否则不能反序列化回来.默认为true.
boolean|serialize|如果为tru,会被序列化到Json, 否则不参与序列化. 默认为true.

也就是说,要使用@Expose,需要用GsonBuilder创建Gson而且excludeFieldsWithoutExposeAnnotation被调用的时候才有效, 在这种情况下,没有添加@Expose的字段都不会被序列化反序列化.需要导出的字段上加上@Expose 注解，不导出的字段不加。注意是不导出的不加。

@Expose字段设置情况如下:

设置|说明
-|:-
@Expose|默认都为true,即序列化和反序列化都都生效
@Expose(deserialize = true,serialize = true) |序列化和反序列化都都生效
@Expose(deserialize = true,serialize = false) |反序列化时生效
@Expose(deserialize = false,serialize = true) |序列化时生效
@Expose(deserialize = false,serialize = false) | 和不写一样

对于下面的例子,序列化到Json,只有firstName,反序列化回对象,只有firstName和lastName有.

```
public class ExposeInfo {
	@Expose private String firstName;
	@Expose(serialize = false) private String lastName;
	@Expose (serialize = false, deserialize = false) private String emailAddress;
	private String password;
    ...
}
```

### 2.transient字
使用transient修饰字段,这个不需要用GsonBuilder,直接Gson就可以了.
如下,fristName别排除:

```
public class ExposeInfo {
	transient Expose private String firstName;
	private String lastName;
	private String emailAddress;
	private String password;
    ...
}
```

### 3.使用excludeFieldsWithModifiers排除指定字段
如下, protected类型的字段就会被排除在外:

```
Gson gson = new GsonBuilder()
		.excludeFieldsWithModifiers(Modifier.PROTECTED) // <---
		.create();
```

### 4.使用ExclusionStrategy定制字段排除策略
这种方式最灵活，下面的例子把所有以下划线"_"开头的字段全部都排除掉：

```
ExclusionStrategy myExclusionStrategy = new ExclusionStrategy() {

	    @Override
	    public boolean shouldSkipField(FieldAttributes fa) {
		return fa.getName().startsWith("_");
	    }

	    @Override
	    public boolean shouldSkipClass(Class<?> clazz) {
		return false;
	    }
	    
	};

Gson gson = new GsonBuilder()
		.setExclusionStrategies(myExclusionStrategy) // <---
		.create();
```

### 排除过滤总结
总共有四种方法:

1. 使用transient字段可以不需要使用GsonBuilder,其他都需要使用GsonBuilder创建Gson.
1. 使用ExclusionStrategy定制字段排除策略比较灵活,可以对字段和类进行各种过滤.
1. 使用@Expose必须使用GsonBuilder创建Gson,同时指定excludeFieldsWithoutExposeAnnotation(),Expose的属性默认都为true,没有指定@Expose的都被过滤.
1. 使用excludeFieldsWithModifiers也需要GsonBuilder创建Gson,可以对不同访问权限进行过滤.


## 关于对象字段的几种情况

---
### 1. 少了部分字段
能正常解析, 少了的部分空.

如下例子, 输入少了mStrName,解析出来的Student的该字段为空.

```
class Student {
	public int mNAge;
	public String mStrName;
}

String strGson = "{\"mNAge\":12}";
Gson gson = new GsonBuilder().serializeNulls().create();
Student stu = gson.fromJson(strGson, Student.class);
```

### 2. 多了部分字段
能正常解析, 内容不受影响.

还是Student举例, 如下,解析出来对象正常:

```
String strGson = "{\"mNAge\":12,\"mStrName\":\"Louis\",\"anotherField\":\"anther\"}";
Gson gson = new GsonBuilder().serializeNulls().create();
Student stu = gson.fromJson(strGson, Student.class);
```

### 3. json字段值包含null
也能正常解析, 对应字段值为null.


## Reference
1. [Gson User Guide](https://github.com/google/gson/blob/master/UserGuide.md)
1. [你真的会用Gson吗?Gson使用指南（一）](http://www.jianshu.com/p/e740196225a4)
1. [你真的会用Gson吗?Gson使用指南（二）](http://www.jianshu.com/p/c88260adaf5e)
1. [你真的会用Gson吗?Gson使用指南（三）](http://www.jianshu.com/p/0e40a52c0063)




## Reference
1. [Gson User Guide](https://github.com/google/gson/blob/master/UserGuide.md)







## others


//------------------------------
JsonParser












dd