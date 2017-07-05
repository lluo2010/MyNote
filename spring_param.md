# Jax-RS 注解 study note

##  @Path

标识要请求的资源类或资源方法的uri路径。**注意：可以是资源类，也可以使资源方法**

例:

- @Path("animal")，表示下一层路径是animal时要处理的事务。
- @Path("{species}")这种带大括号的表示方法，表示下一层路径会被参数化，配合@PathParam("species")使用可以赋值给函数的参数。


例子：

    ···
    @Path("animal")  
    public class Animal {  
        public String species,name;  
        public int age;  
        public static Animal animal=new Animal();  
        
        @GET  
        @Path("{species}")  
        @Produces(MediaType.APPLICATION_JSON)  
        public Animal wsAnimal(@PathParam("species") String species,  
                @QueryParam("name") String name,  
                @QueryParam("age") int age  
                ){  
            animal.species=species;  
            animal.name=name;  
            animal.age=  age==0?2:age;  
            return animal;  
        }  
    }  
    ···

也就说，当方法中需要将路径参数化时，需要在Path中加'{}', 如上面的 @Path("{species}")，然后参数里使用@PathParam("species")。 如果参数里不需要引用路径， 则不需要‘{}’， 直接 @Path("species")就可以了。 比如:

    ```
        @POST
        @Path("/share")
        @Override
        public JsonResult share(@FormParam("token") String token, @FormParam("shareType") String shareType, @FormParam("shareResult") String shareResult ) throws ParseException {
            ...
        }
    ```

## @PathParam

将uri中指定的路径参数绑定到资源方法参数，资源类的字段，或资源类的bean属性。**使用@PathParam可以获取URI中指定规则的参数.**

比如：

    ```
    @Path("/user")
    public class UserResource {
        @GET
        @Path("{username"})
        @Produces(MediaType.APPLICATION_JSON)
        public User getUser(@PathParam("username") String userName) {
            ...
        }
    }
    ```
当浏览器请求http://localhost/user/jack时，userName值为jack。 

**注意，这里的username并不是说Key是username, value是jack而是说/user/后面跟着的东西就是username,这里username只是个变量.**


    ```
    @GET  
        @Path("{year}/{month}/{day}") 
        @Produces({ MediaType.APPLICATION_JSON })  
        public HashMap getClientedMessage2(@PathParam("year") int year,@PathParam("month") int month,   
                @PathParam("day") int day) { 
            HashMap<String, String> map = new HashMap<String, String>();  
            map.put("abc", "def");  
            map.put("abc1", ""+year);  
            map.put("abc2", ""+month);  
            map.put("abc3", ""+day);  
            return map;  
        }  

    ```
当请求方式为：http://localhost:8080/resourceRest-1/api/test/2011/11/12,year为2011， month为12。



## @QueryParam

@QueryParam用于获取GET请求中的查询参数`

如：

    ```
    @GET
    @Path("/user")
    @Produces("text/plain")
    public User getUser(@QueryParam("name") String name, @QueryParam("age") int age) {
        ...
    }
    ```
当浏览器请求http://host:port/user?name=rose&age=25时，name值为rose，age值为25。如果需要为参数设置默认值，可以使用@DefaultValue，如：

    ```
    @GET
    @Path("/user")
    @Produces("text/plain")
    public User getUser(@QueryParam("name") String name, @DefaultValue("26") @QueryParam("age") int age) {
        ...
    }
    ```
当浏览器请求http://host:port/user?name=rose时，name值为rose，age值为26。 



## @FormParam 

@FormParam，顾名思义，从POST请求的表单参数中获取数据。

In JAX-RS, you can use @FormParam annotation to bind HTML form parameters value to a Java method.

比如底下使用post method, form里（就是post的body）里有key "name"和"age"，通过@FormParam获取其对应的value:

    ```
    @Path("/user")
    public class UserService {

        @POST
        @Path("/add")
        public Response addUser(
            @FormParam("name") String name,
            @FormParam("age") int age) {

            return Response.status(200)
                .entity("addUser is called, name : " + name + ", age : " + age)
                .build();

        }

    }

    ```


## @HeaderParam

The @javax.ws.rs.HeaderParam annotation is used to inject HTTP request header values. For example, what if your application was interested in the web page that referred to or linked to your web service? You could access the HTTP Referer header using the @HeaderParam annotation:

    ```
    @Path("/myservice")
    public class MyService {

    @GET
    @Produces("text/html")
    public String get(@HeaderParam("Referer") String referer) {
        ...
    }
    }
    ```

通过@HeaderParam获取请求里的header中的一些字段，比如上面关心的是"Referer"字段。



## BeanParam


The @BeanParam annotation is added to the JAX-RS 2.0 specification. It allows you to inject a pojo whose property methods or fields are annotated with any of the injection parameters supported by JAX-RS. To name a few: @FormParam, @PathParam, @QueryParam and @HeaderParam and etc..

比如一个请求里有很多path或者参数我们需要，这时候我们可能需要很多的FormParam,@QueryParam,这时候我们可以使用一个POJO, 然后在POJO里将pojo中的字段和请求中的参数一一对应，最终我们在方法里简单的使用一个@BeanParam就可以了。

比如一个post的请求如下：

    ···
    <!DOCTYPE html>
    <html>
    <body>

    <h1>JAX-RS @BeanParam Example</h1>

    <form action="api/users?count=10" method="post">
        <label for="name">Name: </label>
        <input id="name" type="text" name="name" />
        <input type="submit" value="Submit" />
    </form>

    </body>
    </html>
    ···

这时候我们可以使用一个如下的POJO将我们关心的参数一一对应：

    ···
    import javax.ws.rs.FormParam;
    import javax.ws.rs.HeaderParam;
    import javax.ws.rs.QueryParam;

    public class UserInput {

        @FormParam("name")
        private String name;

        @QueryParam("count")
        private int count;

        @HeaderParam("Content-Type")
        private String contentType;

        @Override
        public String toString() {
            return "UserInput{" +
                    "name='" + name + '\'' +
                    ", count=" + count +
                    ", contentType='" + contentType + '\'' +
                    '}';
        }
    }
    ···

而请求的方法使用@BeanParam就可以了：

    ···
    import javax.ws.rs.*;

    @Path("/users")
    public class UserResource {

        @POST
        public void createCustomer(@BeanParam UserInput user){
            System.out.println(user);
        }

    }
    ···

## @Produces

@Produces注释用来指定将要返回给client端的数据标识类型（MIME）。@Produces可以作为class注释，也可以作为方法注释，方法的@Produces注释将会覆盖class的注释。 覆盖的意思是假如方法声明了自己的Produce，那么以方法的为准，class的仅供参考.

指定MIME类型:

- 指定单个MINE类型：@Produces(“application/json”):
- 指定多个MIME类型: @Produces({“application/json”,“application/xml”})


## @Consumes 

@Consumes与@Produces相反，用来指定可以接受client发送过来的MIME类型，同样可以用于class或者method，也可以指定多个MIME类型，一般用于@PUT，@POST.

## @Context

通过@Context可以获得以下信息：UriInfo、ServletConfig、ServletContext、HttpServletRequest、HttpServletResponse和HttpHeaders等.

todo...






## Reference
1. [Jersey 入门与Javabean @QueryParam @PathParam @FormParam**](http://blog.csdn.net/xx123698/article/details/47810389)
1. [restful-java-with-jax-rs-2-0-2rd-edition***](https://dennis-xlc.gitbooks.io/restful-java-with-jax-rs-2-0-2rd-edition/content/en/index.html)