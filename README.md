<!DOCTYPE html>
<html>
	<head>
		<meta charset="utf-8" />
		<title></title>
		<script src="js/jquery-2.1.4.min.js" type="text/javascript" charset="utf-8"></script>
		<script type="text/javascript">
			$(function(){
				$("input[type=button]").on("click", function(){
					var name = $("input[type=text]").val();
					var password = $("input[type=password]").val();
					
					var postParameter = {
						name:name,
						password:password
					}
					$.post("/interface/test_module/func1",postParameter,function(data){
						if(data.status && data.data == "success"){
							alert("登录成功");
						}
						else{
							alert(data.msg);
						}
					})
				})
			})
		</script>
	</head>
	<body>
		登录示例
		用户名： <input type="text" name="" id="" value="user1" />
		密码： <input type="password" name="" id="" value="pw" />
		<input type="button" name="" id="" value="提交" />
	</body>
</html>
