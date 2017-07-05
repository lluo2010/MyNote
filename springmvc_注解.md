Spring MVC 注解



	

    @ResponseBody
	@RequestMapping(value="detail", method= RequestMethod.GET)
	public String detail(@RequestParam("jobName") String jobName, @RequestParam("jobGroup") String jobGroup){
		TaskInfo infos = taskService.detail(jobName, jobGroup);
		return JSON.toJSONString(infos);
	}

    @ResponseBody
	@RequestMapping(value="list", method= RequestMethod.POST)
	public String list(){
        Map<String, Object> map = new HashMap<>();
		List<TaskInfo> infos = taskService.list();
		map.put("rows", infos);
		map.put("total", infos.size());
		return JSON.toJSONString(map);
    }

    @ResponseBody
	@RequestMapping(value="save", method= RequestMethod.POST, produces = "application/json; charset=UTF-8")
	public String save(TaskInfo info){
		try {
			if(info.getId() == 0) {
				taskService.add(info);
			}else{
				taskService.edit(info);
			}
		} catch (RuntimeException e) {
			return ResultInfo.error(-1, e.getMessage());
		}
		return ResultInfo.success();
	}


@Controller
public class TaskManageController {
    ...
}


@RestController
@RequestMapping("/task/walletRate")
public class WalletRateController {
}



//-------------------

@Service("restUserService")
@Path("/user")
@Scope("prototype")
@Produces("application/json")  /*表示各个方法返回的都是json*/
public class RestUserServiceImpl implements RestUserService {
    ...
    @POST
    @Path("/share")
    @Override
    public JsonResult share(@FormParam("token") String token, @FormParam("shareType") String shareType, @FormParam("shareResult") String shareResult ) throws ParseException {
        String userid = getUserIdByToken(token);
    }
    ...
}


