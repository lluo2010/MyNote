# J2EE 注解

### @FormParam

Binds the value(s) of a form parameter contained within a request entity body to a resource method parameter.
比如post里的body参数里有key/value, 通过@FormParam("key")可以获取value.


### @Conumes 和 @Produces

@Consumes 注释代表的是一个资源可以接受的 MIME 类型。@Produces 注释代表的是一个资源可以返回的 MIME 类型。这些注释均可在资源、资源方法、子资源方法、子资源定位器或子资源内找到。

例子:

```
@Path("/user")
@Produces("application/json")
public interface RestUserService {
	@POST
	@Path("/login")
	public JsonResult login(
			@FormParam("mobile") String mobile,
			@FormParam("mobile_auth_code") String mobileAuthCode,
            @FormParam("device_serial_id") String deviceSerialId, 
            @FormParam("device_type") String deviceType,
            @FormParam("device_id") String deviceId, 
            @FormParam("supper_user_id") String supperUserId,
            @FormParam("channel") String channel,
            @FormParam("registration_id") String registrationId,
            @FormParam("phone_system_version") String phoneSystemVersion,
			@FormParam("jpush_type") String jpushType,
			@FormParam("appType") String appType,
			@FormParam("third_id") String thirdId,
			@FormParam("third_type") String thirdType
			);
```

### @PathParam & @QueryParam

- @queryparam类似@RequestParam, 处理http://host:port/path?参数名=参数值
- @PathParaml类似@PathVariable时，URL是这样的：http://host:port/path/参数值







JAX-RS 中涉及 Resource 方法参数的标注包括：@PathParam、@MatrixParam、@QueryParam、@FormParam、@HeaderParam、@CookieParam、@DefaultValue 和 @Encoded,@Provider.