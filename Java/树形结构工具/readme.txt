1.//返回json字符串，为zTree提供数据  {id:"",pId:"",name:"",checked:"true|false"}
	public void roleModuleJsonStr() throws IOException{
		/*
		 * 1.查询数据库返回所有的模块
		 * 2.查询当前角色拥有的模块
		 */
		
		List<Module> moduleList = roleService.getModuleList();
		String roleId = model.getId();
		Role role = roleService.get(Role.class, roleId);
		Set<Module> moduleSet = role.getModules();			//拥有的模块
		
		
		StringBuilder sb = new StringBuilder();
		int size = moduleList.size();
		sb.append("[");
		for (Module o : moduleList) {
			size--;
			sb.append("{id:\"");
			sb.append(o.getId());
			sb.append("\",pId:\"");
			sb.append(o.getParentId());					//getParentid
			sb.append("\",name:\"");
			sb.append(o.getName());
			
			//根据已有的权限，实现是否勾选	
			sb.append("\",checked:\"");
			if(moduleSet.contains(o)){
				sb.append("true");
			}else{
				sb.append("false");
			}

			sb.append("\"}");
			if (size > 0) {
				sb.append(",");
			}
		}
		sb.append("]");
		
		HttpServletResponse response = ServletActionContext.getResponse();    
		
		response.setContentType("text/xml;charset=utf-8");   
		response.setHeader("Cache-Control", "no-cache");   
		 
		PrintWriter out = response.getWriter();  //注意这句必须放在上面两句下面，否则中文乱码   
		out.write(sb.toString());
		out.flush();
		out.close();
	}
	
2.导入zTree相关的js及css
   css文件夹下的内容

   js文件夹下的部分内容
	jquery-1.4.4.min.js
	jquery.ztree.core-3.5.min.js
	jquery.ztree.excheck-3.5.min.js


3.在jsp页面中引入
	<link rel="stylesheet" href="${ctx }/components/zTree/css/zTreeStyle/zTreeStyle.css" type="text/css" />

	<script type="text/javascript" src="${ctx }/components/zTree/js/jquery-1.4.4.min.js"></script>
	<script type="text/javascript" src="${ctx }/components/zTree/js/jquery.ztree.core-3.5.min.js"></script>
	<script type="text/javascript" src="${ctx }/components/zTree/js/jquery.ztree.excheck-3.5.min.js"></script>

4编写代码
   <script type="text/javascript">
		var zTreeObj;
		var setting = {
			check : {
				enable : true
			},
			data : {
				simpleData : {
					enable : true
				}
			}
		};
		
		$(document).ready(function() {
			$.ajax( {
				url : "${ctx}/sysadmin/roleAction_roleModuleJsonStr.action?id=${id}",
				type : "get",
				dataType : "text",
				success : initZtree
			});
		});
		
		//初始化ZTree树
		function initZtree(data) {
			var zNodes = eval("(" + data + ")");		//动态js语句
			zTreeObj = $.fn.zTree.init($('#jkTree'), setting, zNodes);	//jkTree 树的id，支持多个树
			zTreeObj.expandAll(true);		//展开所有树节点
		}
		
		//获取所有选择的节点
		function submitCheckedNodes(treeNode) {
			var nodes = new Array();
			nodes = zTreeObj.getCheckedNodes(true);		//取得选中的结点
			var str = "";
			for (i = 0; i < nodes.length; i++) {
				if (str != "") {
					str += ",";
				}
				str += nodes[i].id;
			}
			$('#moduleIds').val(str);
		}
	</script>

5.要添加zTree的树
<div>  
	<ul id="jkTree" class="ztree"></ul>  
</div>