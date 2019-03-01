---
title:  JQuery.validate验证规则
date: 2019-03-01 17:23:00
tags: [JQuery, 表单验证]
categories:  JQuery
---

### JQuery.validate验证规则

```
jQuery.validator.addMethod("mobilePhone", function(value, element) {
	return this.optional(element) || /^(13|15|18)\d{9}$/.test(value);
},"请输入正确的手机号码！");
 
jQuery.validator.addMethod("phone", function(value, element) {
	return this.optional(element) || /^(\d{8}|\d{7})$/.test(value);
},"请输入正确的电话号码！"); 
 
//验证电话号码，包括验证国内区号,国际区号,分机号
jQuery.validator.addMethod("telephone", function(value, element) {
	return this.optional(element) || /^(\d{3,4}-)?\d{6,8}(-\d{3,5})?$/.test(value);
},"请输入正确的电话号码"); 
 
jQuery.validator.addMethod("fax", function(value, element) {
	return this.optional(element) || /^(\d{8}|\d{7})$/.test(value);
},"请输入正确的传真号！");
 
jQuery.validator.addMethod("regionNum", function(value, element) {
	return this.optional(element) || /^0\d{2,3}$/.test(value);
},"请输入正确的区号！");
 
jQuery.validator.addMethod("checkEmail", function(value, element) {
	return true;
},"请输入正确的邮箱！");
 
jQuery.validator.addMethod("carNum1", function(value, element) {
	return this.optional(element) || /^[\u4e00-\u9fa5]([A-Za-z]{1})[\u0000-\u00FF]{5}$/.test(value);
},"请输入正确的车牌号码！");

jQuery.validator.addMethod("carNum", function(value, element) {
	return this.optional(element) || /^[\u4e00-\u9fa5\da-zA-Z\-\_]+$/.test(value);
},"不能输入特殊字符！");

jQuery.validator.addMethod("normalwords", function(value, element) {
	return this.optional(element) || /^\w+$/.test(value);
},"匹配由数字、26个英文字母或者下划线组成的字符串");
 
jQuery.validator.addMethod("upcase", function(value, element) {
	return this.optional(element) || /^[A-Z]+$/.test(value);
},"匹配由26个英文字母的大写组成的字符串");
 
jQuery.validator.addMethod("lowcase",function(value, element) {
	return this.optional(element) || /^[a-z]+$/.test(value);
},"匹配由26个英文字母的小写写组成的字符串");
 
//验证百分比
jQuery.validator.addMethod("checkPercent", function(value, element) {
	var reg = /^-?\d+%$/;
	return this.optional(element) || reg.test(value);
}, "\u8bf7\u8f93\u5165\u6b63\u786e\u7684\u767e\u5206\u6bd4！");
 
//验证英文
jQuery.validator.addMethod("checkENG", function(value, element) {
	var reg = /^[a-zA-Z](\s*[a-zA-Z])*$/;
	return this.optional(element) || reg.test(value);
}, "\u8bf7\u8f93\u5165\u82f1\u6587！");
 
//验证中英文数字
jQuery.validator.addMethod("checkENGCHNNUM", function(value, element) {
	var reg =  /^[0-9a-zA-Z\u4E00-\u9FA5]+$/;
	return this.optional(element) || reg.test(value);
}, "请输入正确的字符、数字或字母！");
 
//验证中英文 add by ruijie.chen
jQuery.validator.addMethod("checkENGCHN", function(value, element) {
    var reg =  /^[a-zA-Z\u4E00-\u9FA5]+$/;
    return this.optional(element) || reg.test(value);
}, "请输入正确的中文或英文！");
 
//验证英文字母和数字
jQuery.validator.addMethod("checkENGNUM", function(value, element) {
	var reg =  /^[0-9a-zA-Z]+$/;
	return this.optional(element) || reg.test(value);
},"请输入正确的英文字母或数字！");

//只允许汉字、英文字母、数字及下划线
jQuery.validator.addMethod("checkTSNUM", function(value, element) {
	var reg =  /^[\u0391-\uFFE5\w]+$/;
	return this.optional(element) || reg.test(value);
},"只允许汉字、英文字母、数字及下划线");
 
//验证中文
jQuery.validator.addMethod("checkCHN", function(value, element) {
	var reg =  /^[\u4e00-\u9fa5]+$/;
	return this.optional(element) || reg.test(value);
}, "\u8bf7\u8f93\u5165\u4e2d\u6587！");
 
//验证不能有中文
jQuery.validator.addMethod("checkHasCHN", function(value, element) {
	var reg = /[\u4E00-\u9FA5]/g;
        if(reg.test(value)){
    	     return false;
        }else{
    	     return true;
   }
 
}, "不能包含中文字符");
 
//验证不能为数字
jQuery.validator.addMethod("checkNotNum", function(value, element) {
	var reg =  /(^[^0-9]+$)/;
	return this.optional(element) || reg.test(value);
}, "\u4e0d\u80fd\u586b\u5199\u6570\u5b57！");
 
//验证大于0的数字
jQuery.validator.addMethod("checkNum", function(value, element) {
	var reg = /^[1-9]+?[0-9]*$/;
	return this.optional(element) || reg.test(value);
}, "\u8bf7\u8f93\u5165\u5927\u4e8e\u96f6\u7684\u6574\u6570");
 
//检验是否全为数字
jQuery.validator.addMethod("isNum", function(value, element) {
	var numtype = "0123456789";
	for (i = 0; i < value.length; i++) { //检查是否有不在 0123456789之內的字 
		if (numtype.indexOf(value.charAt(i)) < 0) {
			return false;
		}
	}
	return true;
}, "请输入数字！");

//检验投保单
jQuery.validator.addMethod("isNum_policy", function(value, element) {
	var numtype = "0123456789";
	for (i = 0; i < value.length; i++) { //检查是否有不在 0123456789之內的字 
		if (numtype.indexOf(value.charAt(i)) < 0) {
			return false;
		}
	}
	return true;
}, "请输入正确的投保单号！");
 
//校验24小时时间如： 00:00
jQuery.validator.addMethod("checkTime", function(value, element) {
	var intt = value.indexOf(":");
	var befor = value.substring(0, intt);
	var after = value.substring(intt + 1, intt + 3);
	if (befor >= 24 || befor.length != 2 || after >= 60 || after.length != 2) {
		return false;
	}
	return true;
}, "请输入正确的时间格式，如：00:00！");
 
//校验Double
jQuery.validator.addMethod("checkDouble", function(value, element) {
	//var reg1 = /^\d+$/;
    var reg = /^(([1-9]\d*(\.\d*[1-9])?)|(0\.\d*[1-9]))$/;  //update by ruijie.chen 2010-03-01
	return this.optional(element) || reg.test(value);
}, "请输入Double型数字");
 
//校验邮政编码
jQuery.validator.addMethod("checkPost", function(value, element) {
	var reg = /^[0-9]\d{5}$/;
	return this.optional(element) || reg.test(value);
}, "\u8bf7\u8f93\u5165\u6b63\u786e\u7684\u90ae\u653f\u7f16\u7801！");
 
//校验包含中文字符的字符串的长度，中文每个字符算2个字符长度，英文为1个字符长度
jQuery.validator.addMethod("maxlengthC", function(value, element, param) {
	return this.optional(element) || value.replace(/[^\x00-\xff]/g,"**").length <= param;
}, $.validator.format("允许最大字符为 {0}，汉字为2个字符！"));
 
//校验日期 格式：yyyyMM
jQuery.validator.addMethod("date_yyyyMM", function(value, element) {
	return this.optional(element) || /^\d{4}(0[1-9])|(1[0-2])$/.test(value);
}, $.validator.format("请输入正确的日期格式，如：201003"));
 
//校验日期 格式：yyyy-MM-dd
jQuery.validator.addMethod("date_yyyy-MM-dd", function(value, element) {
	return this.optional(element) || /^\d{4}-\d{2}-\d{2}$/.test(value);
}, $.validator.format("请输入正确的日期格式，如：2010-03-09"));
 
//校验JAVA包名 格式：xxx.xxx.xxx 由小写英文单词和点组成，单词最多50个字母（系统一般支持200）
jQuery.validator.addMethod("checkPackageName", function(value, element) {
	return this.optional(element) || /^[a-z]{1,50}(\.[a-z]{1,50})*$/.test(value);
}, $.validator.format("请输入正确的包名格式，如：com.newtouch.dictionary"));
 
//校验JAVA类名 由英文字母和数字组成，必须以字母开头，不能使用JAVA保留字（保留字均由小写英文组成）
jQuery.validator.addMethod("checkClassName", function(value, element) {
	return this.optional(element) || /^[a-zA-Z]([a-zA-Z0-9])*$/.test(value);
}, $.validator.format("请输入正确的类名格式，如：HelloWorld"));
 
//校验日期 格式：yyyy-MM-dd
jQuery.validator.addMethod("dateDate", function(value, element) {
	return this.optional(element) || /^((((1[6-9]|[2-9]\d)\d{2})-(0?[13578]|1[02])-(0?[1-9]|[12]\d|3[01]))|(((1[6-9]|[2-9]\d)\d{2})-(0?[13456789]|1[012])-(0?[1-9]|[12]\d|30))|(((1[6-9]|[2-9]\d)\d{2})-0?2-(0?[1-9]|1\d|2[0-8]))|(((1[6-9]|[2-9]\d)(0[48]|[2468][048]|[13579][26])|((16|[2468][048]|[3579][26])00))-0?2-29))$/.test(value);
}, $.validator.format("请输入正确的日期格式，如：2010-01-20"));
 
//校验大于0,的金额数字,最多保留2位小数点  若必须设置2位小数点则:^((\d{1,3}(,\d{3})+?|\d+)(\.\d{2})?|(\.\d{2}))$
jQuery.validator.addMethod("checkMeny", function(value, element) {
	return this.optional(element) || /^((\d{1,3}(,\d{3})+?|\d+)(\.\d{1}|\.\d{2})?|(\.\d{1}|\.\d{2}))$/.test(value);
}, "请输入正确的金额，最多保留二位小数");

//校验大于0,的金额数字,最多保留2位小数点  若必须设置2位小数点则:^((\d{1,3}(,\d{3})+?|\d+)(\.\d{2})?|(\.\d{2}))$
jQuery.validator.addMethod("checkPolicyMeny", function(value, element) {
	return this.optional(element) || /^\d{1,6}([\.]\d{1,4})?$/.test(value);
}, "请输入正确的金额，最大值为999999.9999。");
 
//校验大于0,的金额数字,最多保留2位小数点  若必须设置2位小数点则:^((\d{1,3}(,\d{3})+?|\d+)(\.\d{2})?|(\.\d{2}))$
jQuery.validator.addMethod("checkTrainingMeny", function(value, element) {
	return this.optional(element) || /^\d{1,6}([\.]\d{1,4})?$/.test(value);
}, "请输入正确的金额");

//培训和招募校验使用：校验大于0,number(10,2)的数字,最多保留2位小数点 
jQuery.validator.addMethod("checkTenTwoNumber", function(value, element) {
	return this.optional(element) || /^\d{1,8}([\.]\d{1,2})?$/.test(value);
}, "请输入0~99999999.99內的金额");
 
//校验大于0,用户身高,最多3位整数保留1位小数点
jQuery.validator.addMethod("checkHeight", function(value, element) {
	return this.optional(element) || /^\d{1,3}([\.]\d{1})?$/.test(value);
}, "请输入正确的身高，最多保留一位小数");
 
//校验大于0,用户身高,最多3位整数保留2位小数点
jQuery.validator.addMethod("checkWeight", function(value, element) {
	return this.optional(element) || /^\d{1,3}([\.]\d{1,2})?$/.test(value);
}, "请输入正确的体重，最多保留二位小数");
 
//校验百分比1-100之间整数+最多保留2为小数点 不加%号  不支持=0.00   -->
//加百分号^([1-9][0-9]?(\.[0-9]{1,2})?)%$|^(0\.[1-9][0-9]?)%$|^(0\.[0-9][1-9])%$|^100(\.0|\.00)?%$
jQuery.validator.addMethod("checkPercent", function(value, element) {
	return this.optional(element) || /^([1-9][0-9]?(\.[0-9]{1,2})?)$|^(0\.[1-9][0-9]?)$|^(0\.[0-9][1-9])$|^100(\.0|\.00)?$/.test(value);
}, "请输入正确的百分比值,0.01-100");
 
//校验年龄1-120
jQuery.validator.addMethod("checkAge", function(value, element) {
	return this.optional(element) || /^(?:[1-9][0-9]?|1[01][0-9]|120)$/.test(value);
}, "请输入正确的年龄");

jQuery.validator.addMethod("checkProFee", function(value, element) {
	return this.optional(element) || /^(0\.([0-9]){1,4}|1)$/.test(value);
}, "请输入正确的金额，数值大小为0到1，最多4位小数");
 
//判断出生日期小于等于今天
jQuery.validator.addMethod("checkBirthday",function(value,element) {
	 var now = new Date();
	 var date = value+' 23:59:59';
	 if (Date.parse(value) - Date.parse(now) >0 ) {
		 return false
	 }
	 return true;
 }, "出生日期不能大于今天");
 
//检验文件上传类型 
$(document).ready(function() {
	$('input[type="file"]').change(function() {
		checkEXT($(this));
	});
	
	//校验样式效果,文本框获取焦点,隐藏相应错误信息
	$("form").find("input").each(function() {
		$(this).click(function() {
			var _this = $(this);
			if (_this.hasClass("error")) {
				_this.removeClass("error");
				var labelAR = _this.parent().find("label[class='error']");
				labelAR.remove();
			}
		});
	});
});
 
function checkEXT(element) {
	var file = element;
	var ext = file.val().substr(file.val().lastIndexOf(".")).toLowerCase();
	if (ext ==".exe" || ext==".bat" || ext==".com" || ext==".pif" || ext==".scr") {
		alert("不能上传后缀名为"+ext+"的文件");
		file.after(file.clone());  
		file.remove();
		$('input[type="file"]').change(function(){
			checkEXT($(this));
		});
	}
} 
```