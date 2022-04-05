1.//����json�ַ�����ΪzTree�ṩ����  {id:"",pId:"",name:"",checked:"true|false"}
	public void roleModuleJsonStr() throws IOException{
		/*
		 * 1.��ѯ���ݿⷵ�����е�ģ��
		 * 2.��ѯ��ǰ��ɫӵ�е�ģ��
		 */
		
		List<Module> moduleList = roleService.getModuleList();
		String roleId = model.getId();
		Role role = roleService.get(Role.class, roleId);
		Set<Module> moduleSet = role.getModules();			//ӵ�е�ģ��
		
		
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
			
			//�������е�Ȩ�ޣ�ʵ���Ƿ�ѡ	
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
		 
		PrintWriter out = response.getWriter();  //ע����������������������棬������������   
		out.write(sb.toString());
		out.flush();
		out.close();
	}
	
2.����zTree��ص�js��css
   css�ļ����µ�����

   js�ļ����µĲ�������
	jquery-1.4.4.min.js
	jquery.ztree.core-3.5.min.js
	jquery.ztree.excheck-3.5.min.js


3.��jspҳ��������
	<link rel="stylesheet" href="${ctx }/components/zTree/css/zTreeStyle/zTreeStyle.css" type="text/css" />

	<script type="text/javascript" src="${ctx }/components/zTree/js/jquery-1.4.4.min.js"></script>
	<script type="text/javascript" src="${ctx }/components/zTree/js/jquery.ztree.core-3.5.min.js"></script>
	<script type="text/javascript" src="${ctx }/components/zTree/js/jquery.ztree.excheck-3.5.min.js"></script>

4��д����
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
		
		//��ʼ��ZTree��
		function initZtree(data) {
			var zNodes = eval("(" + data + ")");		//��̬js���
			zTreeObj = $.fn.zTree.init($('#jkTree'), setting, zNodes);	//jkTree ����id��֧�ֶ����
			zTreeObj.expandAll(true);		//չ���������ڵ�
		}
		
		//��ȡ����ѡ��Ľڵ�
		function submitCheckedNodes(treeNode) {
			var nodes = new Array();
			nodes = zTreeObj.getCheckedNodes(true);		//ȡ��ѡ�еĽ��
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

5.Ҫ���zTree����
<div>  
	<ul id="jkTree" class="ztree"></ul>  
</div>