## jdbc.properties

```properties
# jdbc.properties
Notice.jdbc.driverClassName=com.microsoft.sqlserver.jdbc.SQLServerDriver
Notice.jdbc.url=jdbc:sqlserver://192.168.240.109:1433;DatabaseName=Email_Server;
Notice.jdbc.username=ARAdmin
Notice.jdbc.password=AR#Admin#
```

### getDcDutyUserList【查询】

`CommonServiceImpl`.`getDcDutyUserList(String dc_name) `

### setNotice

```java
// MainServiceImpl.java
private boolean setNotice(HashMap<String, Object> param,NoticeWay noticeWay){
    // ...
    for(Contact contact:noticeWay.getContact_list()){

        HashMap<String,Object> emailMap = new HashMap<String,Object>();

        if(noticeWay.isEmail_notice()){// public boolean isEmail_notice() {return email_notice;}
            String From_x = Const.PROPERTIES.getProperty("email.fromAddress");//发件人
            String pm_email = contact.getEmail();
            emailMap.put("From_x", From_x);
            emailMap.put("To_x", pm_email);
            emailMap.put("Subject", noticeWay.getNotice_title());
            emailMap.put("Plain_Text_Body", notice_temp);
            emailMap.put("bccAddress", Const.PROPERTIES.getProperty("email.bccAddress"));
            emailMap.put("contentType", 2);
            RemedyEmail.sendMailData(emailMap);
        }
        // ...
    }
   // ...
}

// RemedyEmail.java
public static boolean sendMailData(Map<String,Object> param){
    try{
        String sql = "insert into Email_Messages (Subject,Body,Status,[From],[To],SubmitTime,Submitter,Bcc,contentType)values( "
            +"'"+param.get("Subject")+"',"  
            +"'"+param.get("Plain_Text_Body")+"',"
            +"1,"
            +"'"+param.get("From_x")+"',"
            +"'"+param.get("To_x")+"',"
            +"getdate(),"
            +"'CBMS',"	
            +"'"+param.get("bccAddress")+"',"  
            +"'2'"
            +" )";

        boolean result = exeSql(sql);
        return result;			
    }catch (Exception e) {
        e.printStackTrace();
        return false;
    }
}
```

#### setNoticeByAlarmId【无调用】

```java
// MainServiceImpl.java
public void setNoticeByAlarmId(HashMap<String, Object> param,NoticeWay noticeWay) {
	boolean value = setNotice(param,noticeWay);
    // ...
}

// MainController.java
@RequestMapping("/setNoticeByAlarmId")
@ResponseBody
public Object setNoticeByAlarmId(HttpSession session,HttpServletRequest request) {

	// ...
    try{
        String notice_way_json = request.getParameter("notice_way")==""?null:request.getParameter("notice_way");
        NoticeWay notice_way = JsonMapper.alwaysMapper().fromJson(notice_way_json, NoticeWay.class);
		// ...
        mainService.setNoticeByAlarmId(param,notice_way);

        return makeResult(true,"success",null);
    }catch(Exception e){
        e.printStackTrace();
        return makeResult(false,"failure",null);
    }
}

// dc_monitor_warn.js
function SendNotice(data){ // 无调用
	var email_notice = $("#warn_notice_email")[0].checked;
	// ...
	var notice_way = {
			email_notice	:email_notice,
			message_notice	:message_notice,
			wechat_notice   :wechat_notice,
			notice_title	:notice_title,
			notice_desc		:notice_desc,
			alarm_id		:data.alarm_id,
			contact_list	:notice_list,
			type			:data.type
	};
	
	var obj = {
			notice_way	:JSON.stringify(notice_way),
			alarm_id	:data.alarm_id,
			type		:data.type
	};
	
    ajaxRequest("main/setNoticeByAlarmId", null, obj, function(msg){
        if(msg.result){
        	ctrlCloseDlg();
        }else{
            alert("失败");
        }
    }, function(msg){
    	alert("失败");
    });
}
```

#### setNoticeCustomerList【】

```java
// MainServiceImpl.java
public void setNoticeCustomerList(HashMap<String, Object> param,List<NoticeWay> noticeWayList) {
    for(NoticeWay noticeWay: noticeWayList){
        setNotice(param,noticeWay);
    }
}

// MainController.java
//一键通知客户
@RequestMapping("/setNoticeCustomerList")
@ResponseBody
public Object setNoticeCustomerList(HttpSession session,HttpServletRequest request) {
	// ...
    try{

        String notice_list_str = request.getParameter("notice_list")==""?null:request.getParameter("notice_list");//通知对象
        ObjectMapper mapper = new ObjectMapper();
        List<NoticeWay> notice_list = mapper.readValue(notice_list_str, new TypeReference<List<NoticeWay>>(){});
		// ...
        mainService.setNoticeCustomerList(param,notice_list);

        return makeResult(true,"success",null);
    }catch(Exception e){
        e.printStackTrace();
        return makeResult(false,"failure",null);
    }
}

// dc_monitor_refresh_rifht.js
//根据已选的通知方式发送通知
function onCustomerSendSelNotice(){
	// ...
	$.each(row_list,function(index ,row){
		// ...
		/*获取行选择通知f方式*/
		var wechat_notice = false;//微信通知
		var email_notice = false;//邮件通知
		var message_notice = false;//短信通知
		var ways = $(row).find(".sel_notice_way_div .selected");
		$.each(ways,function(w_i,way){
			if($(way).attr("notice_way") == "wechat_notice"){
				wechat_notice = true;
			}else if($(way).attr("notice_way") == "email_notice"){
				email_notice = true;
			}else if($(way).attr("notice_way") == "message_notice"){
				message_notice = true;
			}
		});
		// ...
		var notice_detail = {
				email_notice	:email_notice,
				message_notice	:message_notice,
				wechat_notice	:wechat_notice,
				notice_title	:notice_title,
				notice_desc		:notice_desc,
				contact_list	:contact_list
		};
		if(contact_list.length > 0 && (wechat_notice || email_notice || message_notice ) ){
			notice_list.push(notice_detail);
		}
		
	});

	if(notice_list.length > 0){
		ajaxRequest(ctx+"/main/setNoticeCustomerList", null, {notice_list:JSON.stringify(notice_list)}, function(msg){
            if(msg.result){
            	$("#customer-notice-all-modal").modal("toggle");
            }else{
            	layer.msg("通知失败。");
            }
        }, function(msg){
        	layer.msg("数据异常。");
        });
	}else{
		layer.msg("请选择需要通知的对象，以及通知方式。");
	}
	
}
```

### noticeUser

```java
// RegionServiceImpl.java
public void noticeUser(HashMap<String, Object> param) {
		// ...
		for(Map<String,Object> map : beNotified_list){
			user_list.add(""+map.get("account"));
		    String pm_phone = "" + map.get("phone");
		    messageMap.put("pm_phone", pm_phone);
		    messageMap.put("content", param.get("notice_title")+":"+param.get("notice_desc"));
			RemedyEmail.sendMessageData(messageMap);
		}
		
		param.put("user_list", StringUtils.join(user_list.toArray(),","));
		regionDao.saveNoticeLog(param);
	}
```

#### sendWarnNotice【定时任务已关闭】

```xml
<!-- 2分钟轮询发送告警互备通知
<task:scheduled ref="taskJob" method="sendWarnNotice" cron="0 0/2 * * * ? "/>
-->
```

#### noticeUser 【】

```java
// RegionController.java
@RequestMapping("/noticeUser")
@ResponseBody
public Object noticeUser(AlarmAct alarmact , HttpSession session,HttpServletRequest request) {
    	// ...
}

// region_monitor_console.js
function sendNotice(dc_id){
	// ...
    ajaxRequest("region/noticeUser", null, param, function(msg){
        if(msg.result){
        	ctrlCloseDlg();
        	layer.msg("已通知");
        }else{
        	layer.msg("数据异常。");
        }
    }, function(msg){
    	layer.msg("访问失败。");
    });
}
```



