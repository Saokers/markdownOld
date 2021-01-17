# 幻灯片后台

- ### 分析题目

  任务11：     管理端：实现幻灯片管理功能（8分）

  【功能说明】

  在Shopping管理端实现幻灯片管理功能。

  【任务要求】 

  1. 实现幻灯片详情数据显示（幻灯片名称、描述、缩略图、更新者、操作），如图3-10。 

  2. 点击【编辑】按钮，可编辑当前幻灯片的名称、描述、图片、排序序号等信息，编辑后可以保存当前幻灯片信息。如图3-11。
3. 点击【删除】按钮，可以将当前幻灯片信息删除。

- ### 进行逆向生成代码

  #### 1.数据库

  #### 2.配置

  ##### 2.1根目录(需要 mysql jar 和 新建 src 文件夹)

  ##### 2.2配置文件 （用记事本转换为ANSI编码）

  #### 3.编译

  4.将逆向编译的代码文件放进项目 （src文件夹）

- ### 编写Service层

  #### 1.AdvertService

  ```java
  	/**
  	 * 获取幻灯片列表
  	 */
  	public Map<String, Object> getAdvertInByCondition(PageInformation pageInfo,Advert advert);
  	/**
  	 *删除
  	 */
  	public int delteAdvert(String id);
  	
  	public int addAdvert(Advert advert);
  	
  	public int updateAdvert(Advert advert);
  	
  	public List<Advert> getAdvertById(int id);
  ```
  
  #### 2.AdvertServiceImpl

```java
@Service
public class AdvertServiceImpl implements AdvertService {
	@Autowired
	AdvertMapper advertMapper;
	@Autowired
	PagingTool pagingTool;

	@Override
	public Map<String, Object> getAdvertInByCondition(PageInformation pageInfo,Advert advert) {
		// TODO Auto-generated method stub
		AdvertExample advertExample = new AdvertExample();
		Criteria createCriteria = advertExample.createCriteria();
		long countByExample = advertMapper.countByExample(advertExample);
		advertExample.setOrderByClause("sort_id asc");
		createCriteria.andTitleLike("%"+advert.getTitle()+"%");
		createCriteria.andMessageLike("%"+advert.getMessage()+"%");
		
		
		List<Advert> pagingData = pagingTool.PagingData("com.sybinal.shop.mapper.AdvertMapper.selectByExample", advertExample,
				Integer.parseInt(pageInfo.getiDisplayStart()) / Integer.parseInt(pageInfo.getiDisplayLength()),
				Integer.parseInt(pageInfo.getiDisplayLength()));
		
		
		HashMap<String, Object> datas = new HashMap<String, Object>();
		datas.put("sEcho", pageInfo.getsEcho());
		datas.put("iTotalRecords", countByExample);
		datas.put("iTotalDisplayRecords", countByExample);
		datas.put("aaData", pagingData);
		return datas;
	}

	@Override
	public int delteAdvert(String id) {
		// TODO Auto-generated method stub
		int i = 0;
		List<Integer> ids = new ArrayList<Integer>();
		String[] categoryIds = id.split("-");
		if(categoryIds != null && categoryIds.length != 0){
			for(String categoryId : categoryIds){
				Integer idTemp = Integer.parseInt(categoryId);
				ids.add(idTemp);
			}
		}
		i = advertMapper.deleteAdvertList(ids);
		return i;
	}

	@Override
	public int addAdvert(Advert advert) {
		// TODO Auto-generated method stub
		return advertMapper.insertSelective(advert);
	}

	@Override
	public int updateAdvert(Advert advert) {
		// TODO Auto-generated method stub
		return advertMapper.updateByPrimaryKeySelective(advert);
	}

	@Override
	public List<Advert> getAdvertById(int id) {
		// TODO Auto-generated method stub
		AdvertExample advertExample=new AdvertExample();
		Criteria createCriteria = advertExample.createCriteria();
		createCriteria.andIdEqualTo(id);
		List<Advert> selectByExample = advertMapper.selectByExample(advertExample);
		return selectByExample;
	}

}
```



#### 3.AdvertController

```java
@Controller
public class AdvertController {
	@Autowired
	AdvertService advertService;

	@RequestMapping(value = "/admin/advert", method = RequestMethod.GET)
	public ModelAndView getAdvert() {
		ModelAndView model = new ModelAndView();
		model.setViewName("admin/advert/advertMain");
		return model;
	}

	@RequestMapping(value = "/admin/advertInList", method = RequestMethod.POST)
	@ResponseBody
	public Map<String, Object> productCategorynfoDataTable(PageInformation pageInfo, Advert advert) {
		return advertService.getAdvertInByCondition(pageInfo, advert);
	}

	@RequestMapping(value = "/admin/delAdvert", method = RequestMethod.POST)
	@ResponseBody
	public Map<String, Object> checkVerifyProduct(@RequestBody String id) {
		Map<String, Object> map = new HashMap<String, Object>();
		if (StringUtils.isEmpty(id) == true) {
			map.put("error", "-1"); // 失败
		} else {
			int i = advertService.delteAdvert(id);
			if (i > 0) {
				map.put("error", "0"); // 成功
			} else {
				map.put("error", "-1"); // 失败
			}
		}
		return map;
	}

	@RequestMapping(value = "/admin/addAdvert", method = RequestMethod.GET)
	public ModelAndView addAdvert() {
		ModelAndView model = new ModelAndView();
		model.setViewName("admin/advert/advertEdit");
		return model;
	}
	
	@RequestMapping(value = "/admin/advert/edit", method = RequestMethod.POST)
	@ResponseBody
	public ModelAndView editAdvert(@RequestParam("id") Integer id) {
		ModelAndView model = new ModelAndView();
		List<Advert> advertById = advertService.getAdvertById(id);
		Advert advert=new Advert();
		advert=advertById.get(0);
		if (advert != null) {
			model.addObject("advert", advert);
		}
		model.setViewName("admin/advert/advertEdit");
		return model;
	}

	@RequestMapping(value = "/admin/advert/save", method = RequestMethod.POST)
	@ResponseBody
	public Map<String, Object> saveAdvert(@RequestBody Advert advert) {
		Map<String, Object> map = new HashMap<String, Object>();
		int i = 0;
		if (advert.getId() == null) {
			i = advertService.addAdvert(advert);
		} else {
			i = advertService.updateAdvert(advert);
		}
		if (i > 0) {
			map.put("error", "0"); // 成功
			map.put("data", advert); // 数据
		} else {
			map.put("error", "-1"); // 失败
			map.put("data", advert); // 数据成
		}
		return map;
	}
}

```



#### 4.advertMain.jsp

```jsp
<%@ page language="java" contentType="text/html; charset=UTF-8"
	pageEncoding="UTF-8"%>
<%@ taglib prefix="spring" uri="http://www.springframework.org/tags"%>
<%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core"%>
<%@page session="true"%>
<!DOCTYPE html>
<html>
<head>
<meta charset="utf-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<meta http-equiv="X-UA-Compatible" content="IE=edge">
<meta name="_csrf" content="${_csrf.token}" />
<meta name="_csrf_header" content="${_csrf.headerName}" />
<title>商城后台</title>
<jsp:include page="../commonCss.jsp" />
</head>
<body class="blank">
	<jsp:include page="../main/top.jsp" />
	<jsp:include page="../main/left.jsp" />
	<jsp:include page="../commonJs.jsp" />
	<div id="wrapper">
		<div class="small-header transition">
			<div class="content">
				<div class="hpanel">
					<div class="panel-body">
						<div class="row">
							<div class="hpanel">
								<div id="error_div" class="text-left"></div>
								<form id="form_productCategory" name="form_productCategory"
									action="" method="POST">
									<div class="form-group col-lg-6">
										<label>幻灯片</label> <input type="text" id="name" name="name"
											class="form-control" maxlength="50" placeholder="幻灯片" />
									</div>
									<div class="form-group col-lg-6">
										<label>描述</label> <input type="text" id="description"
											name="description" class="form-control" maxlength="50"
											placeholder="描述" />
									</div>
									<div class="text-right m-t-xs">
										<button id="search" class="btn btn-success"
											style="margin-right: 5px; margin-top: 5px" type="button">
											<strong>查询</strong>
										</button>
										<button id="reset" class="btn btn-success"
											style="margin-right: 5px; margin-top: 5px"
											style="margin-right: 20px;" type="reset">
											<strong>重置</strong>
										</button>
										<button id="add" class="btn btn-success"
											style="margin-right: 5px; margin-top: 5px" type="button">
											<strong>添加幻灯</strong>
										</button>
										<button id="delBtn" class="btn btn-success"
											style="margin-right: 5px; margin-top: 5px" type="button">
											<strong>删除幻灯</strong>
										</button>
									</div>
								</form>
							</div>
						</div>
					</div>
				</div>

			</div>

		</div>
		<div class="content">
			<div class="row">
				<div class="col-lg-12">
					<div class="hpanel">
						<div class="panel-body">
							<table id="advertlist" style="width: 100%;"
								class="table table-striped table-bordered table-hover"></table>
						</div>
					</div>
				</div>
			</div>
		</div>
	</div>

	<form id="forwordform"
		action='<c:url value="/admin/advert/edit?${_csrf.parameterName}=${_csrf.token}" />'
		method='POST'>
		<input type="hidden" name="id" id="id" />
	</form>

	<script type="text/javascript">
		$('#leftMenuProduct').addClass('active');
		var oTable;
		var header = $("meta[name='_csrf_header']").attr("content");
		var token = $("meta[name='_csrf']").attr("content");
		var contextPath = '${pageContext.request.contextPath}';
		$(function() {
			$('.date').datepicker();
			$(document).ajaxSend(function(e, xhr, options) {
				xhr.setRequestHeader(header, token);
			});
			AdvertListData();
		});
		$('#add').click(function() {
			window.location.href = contextPath + '/admin/addAdvert';
		});

		$('#search').click(function() {
			AdvertListData();
		});

		$('#delBtn').click(function() {
			var ids = document.getElementsByName("ids");
			var strIds = new Array();
			var idTemp = "";
			for (var i = 0; i < ids.length; i++) {
				if (ids[i].checked) {
					strIds[strIds.length] = ids[i].value;
					idTemp += ids[i].value + "-";
				}
			}
			if (strIds.length == 0) {
				swal({
					title : "提示",
					text : "请选择需要删除的的幻灯片！"
				});
				return false;
			} else {
				swal({
					title : "确定删除吗?",
					type : "warning",
					showCancelButton : true,
					confirmButtonColor : "#62cb31",
					confirmButtonText : "删除",
					cancelButtonText : '取消',
				}, function() {
					deleteAdvert(idTemp);
				});
			}
		});

		function AdvertListData() {
			if (typeof oTable == 'undefined') {
				// dataTable load
				oTable = $('#advertlist')
						.dataTable(
								{
									'bPaginate' : true, // 翻页功能
									'sServerMethod' : 'POST', // 提交方式
									'bServerSide' : true,
									'bProcessing' : true,
									'bFilter' : false,
									"bSort" : false,
									'sAjaxSource' : contextPath
											+ '/admin/advertInList',
									"fnServerParams" : function(aoData) { // 查询条件
										aoData.push({
											"name" : "title",
											"value" : $('[name=name]').val()
													.trim()
										}, {
											"name" : "message",
											"value" : $('[name=description]')
													.val().trim()
										});
									},
									aoColumns : [
											{
												'mData' : 'id',
												'sTitle' : '<input type="checkbox" id="checkedAll" name="checkedAll" onclick="checkedAll()">',
												'sWidth' : '2%',
												'mRender' : function(data) {
													var sRendor = '<input type="checkbox" name="ids" value="'+ data +'">';
													return sRendor;
												}
											},
											{
												'sTitle' : '排序',
												'sWidth' : '10%',
												'mData' : 'sortId'
											},
											{
												'sTitle' : '幻灯片',
												'sWidth' : '10%',
												'mData' : 'title'
											},
											{
												'sTitle' : '缩略图',
												'sWidth' : '20%',
												'mData' : 'url',
												'mRender' : function(data) {
													var returnValue = '<img src="/Shopping'+
													data+'" style="width:400px;"/>'
													return returnValue;
												}
											},
											{
												'sTitle' : '描述',
												'sWidth' : '30%',
												'mData' : 'message'

											},
											{
												'sTitle' : '操作',
												'mData' : 'id',
												'sWidth' : '9%',
												'mRender' : function(data) {
													var returnValue = '<button id="update" class="btn btn-default btn-sm" onclick="editAdvert('
															+ data
															+ ')"><i class="fa fa-pencil"></i>编辑</button>&nbsp&nbsp'
															+ '<button id="update" class="btn btn-default btn-sm" onclick="delAdvert('
															+ data
															+ ')"><i class="fa fa-pencil"></i>删除</button>';
													return returnValue;
												}
											} ]

								});
			} else {
				oTable.fnDraw();
			}
		}
		function editAdvert(id) {
			$("#id").val(id);
			$("#forwordform").submit();
		}

		function deleteAdvert(id) {
			$.ajax({
				type : "POST",
				contentType : "application/json;charset=utf-8",
				url : contextPath + '/admin/delAdvert',
				data : id,
				dataType : "json",
				async : false,
				success : function(json) {
					if (json.error == '0') {
						AdvertListData();
					} else {
						alert("幻灯片删除失败！");
					}
				},
				error : function(json) {
					existFlag = true;
				}
			});
		}

		function checkedAll() {
			if ($("#checkedAll").is(':checked') == true) {
				//全选
				$("input[name='ids']:checkbox").prop('checked', true);
			} else {
				//取消全选
				$("input[name='ids']").removeAttr("checked");
			}
		}
		function delAdvert(id) {
			swal({
				title : "确定删除吗?",
				type : "warning",
				showCancelButton : true,
				confirmButtonColor : "#62cb31",
				confirmButtonText : "删除",
				cancelButtonText : '取消',
			}, function() {
				deleteAdvert(id + '-');
			});
		}
	</script>
</body>
</html>
```

#### 5.advertEdit

```jsp
<%@ page language="java" contentType="text/html; charset=UTF-8"
	pageEncoding="UTF-8"%>
<%@ taglib prefix="spring" uri="http://www.springframework.org/tags"%>
<%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core"%>
<%@ taglib uri="http://java.sun.com/jsp/jstl/fmt" prefix="fmt"%>   

<%@page session="true"%>
<!DOCTYPE html>
<html>
<head>
<meta charset="utf-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<meta http-equiv="X-UA-Compatible" content="IE=edge">
<meta name="_csrf" content="${_csrf.token}" />
<meta name="_csrf_header" content="${_csrf.headerName}" />
<link rel="stylesheet" type="text/css" href="${pageContext.request.contextPath}/resources/admin/vendor/webuploader/webuploader.css">
<title>商城后台</title>
<jsp:include page="../commonCss.jsp" />
</head>
<body class="blank">
<jsp:include page="../main/top.jsp" />
<jsp:include page="../main/left.jsp" />
<jsp:include page="../commonJs.jsp" />
<script src="${pageContext.request.contextPath}/resources/admin/vendor/jquery-validation/jquery.validate.js"></script>
<script src="${pageContext.request.contextPath}/resources/admin/vendor/bootstrap-fileinput-master/js/fileinput.js" type="text/javascript"></script>
<script src="${pageContext.request.contextPath}/resources/admin/vendor/bootstrap-fileinput-master/js/fileinput_locale_zh.js" type="text/javascript"></script>
<script src="${pageContext.request.contextPath}/resources/admin/vendor/webuploader/webuploader.js" type="text/javascript"></script>
<div id="wrapper">
	<div class="small-header transition">
		<div class="content">
			<div class="hpanel">
				<div class="panel-body">
					<div class="row">
							<div class="hpanel">
								<div id="error_div" class="text-left"></div>
								<form id="form_advert" name="form_advert" action='<c:url value="/admin/save?${_csrf.parameterName}=${_csrf.token}" />'  method="POST">
									<input type="hidden" id="id" name="id" value='${advert.id}'/>	
									<div class="form-group col-lg-12">
										<label>幻灯片</label>
										<input type="text" id="title" name="title" class="form-control" maxlength="50" placeholder="幻灯片"	value='${advert.title}' />
										<input type="hidden" id="titleOld" value='${advert.title}'/>
									</div>
									<div class="form-group col-lg-12">
										<label>排序</label>
										<input type="text" id="sortId" name="sortId" class="form-control" maxlength="50" placeholder="排序" value='${advert.sortId}' />
									</div>
									<div class="form-group col-lg-12">
										<label>图片</label>
										<input type="hidden" id="url" name="url" value='${advert.url}' />
                                           <!--dom结构部分-->
										<div id="uploader-demo">
										    <!--用来存放item-->
										    <div id="fileList" class="uploader-list"></div>
										    <div id="filePicker">选择图片</div>
										    <div id="imageFile">
										    	<c:if test="${not empty advert.url}">
										    		<img src="${pageContext.request.contextPath}${advert.url}" style="width: 150px;height: 150px"/>
											    	</c:if>
											    	<c:if test="${empty advert.url}">
											    		<img src=""/>
											    	</c:if>
										    </div>
										</div>
                                           <div style='padding-top:10px'>上传图片的最佳尺寸：300像素*300像素，其他尺寸会影响页面效果，格式png，jpeg，jpg，gif。大小不超过1M</div>
									</div>
									<div class="form-group col-lg-12">
										<label>描述</label>
										<textarea rows="3" cols="20" id="message" name="message" class="form-control" maxlength="2000"></textarea>
										<input type="hidden" id="messageName" value='${advert.message}' />
									</div>
									<div class="text-left m-t-xs">
										<button id="save" class="btn btn-success" style="margin-left: 2px;" type="button">
											<strong>保存</strong>
										</button>
										<button id="back" class="btn btn-success" type="button">
											<strong>返回</strong>
										</button>
									</div>
								</form>
							</div>
						</div>
					</div>
				</div>
			</div>
		</div>
	</div>
</div>
<script type="text/javascript">
$('#leftMenuProduct').addClass('active');
	var oTable;
	var header = $("meta[name='_csrf_header']").attr("content");
	var token = $("meta[name='_csrf']").attr("content");
	var contextPath = '${pageContext.request.contextPath}';
	var validate;
	// 初始化Web Uploader
	var uploader = WebUploader.create({
	    // 选完文件后，是否自动上传。
	    auto: true,
	    // swf文件路径
	 	swf:  contextPath + '/resources/vendor/webuploader/Uploader.swf',
	    // 文件接收服务端。
	    server: contextPath + '/admin/newUploadfile?${_csrf.parameterName}=${_csrf.token}' ,
	    // 选择文件的按钮。可选。
	    // 内部根据当前运行是创建，可能是input元素，也可能是flash.
	    pick: '#filePicker',
	    // 只允许选择图片文件。
	    accept: {
	        title: 'Images',
	        extensions: 'gif,jpg,jpeg,bmp,png',
	        mimeTypes: 'image/*'
	    }
	});
	
	uploader.on( 'uploadAccept', function( object , ret ) {
// 		console.log(object);
// 		console.log(ret.fileName);
		$("#url").val(ret.fileName);
	});
	
	// 当有文件添加进来的时候
	uploader.on( 'fileQueued', function( file ) {
		 $('#imageFile').html('');
		 var $li = $(
		     '<div id="' + file.id + '" class="file-item">' +
		          '<img>' +
		     '</div>'
		 );
		 var $img = $li.find('img');
		 // $list为容器jQuery实例
		 $('#imageFile').append( $li );
		 
		 var ratio = window.devicePixelRatio || 1;
	     // 缩略图大小
	     var  thumbnailWidth = 150 * ratio;
	     var  thumbnailHeight = 150 * ratio;
         uploader.makeThumb( file, function( error, src ) {
			 if ( error ) {
			     $img.replaceWith('<span>不能预览</span>');
			     return;
			 }
	         $img.attr( 'src', src );
		 }, thumbnailWidth, thumbnailHeight );
	});
	
	$(function() {
		document.getElementById("message").innerText=$("#message").val();
		$(document).ajaxSend(function(e, xhr, options) {
			xhr.setRequestHeader(header, token);
		});
		validate = $("#form_advert").validate({
			onsubmit:false,
			focusInvalid:false,
			errorClass:"error",
			onfocusout:false,
			onkeyup:false,
			onclick:false,
			ignoreTitle:true,
	        rules:{
	        	name:{
                	required:true,
                	maxlength : 50,
                },
                sortOrder:{
                  	required:true,
                  	digits:true,
                  	maxlength : 4
                }
            },
            messages:{
            	name:{
                    required:"商品类别名必填",
                    maxlength : "最大位数不能超过50位",
                },
                sortOrder:{
                    required:"排序必填",
                    digits : "只能输入正整数",
                    maxlength : "只能输入4位整数"
                }                                   
            }
        });    
	});
	
	/** 返回  */
	$("#back").bind("click",function(){
		window.location.href =contextPath+'/admin/advert';
	});
	
	/** 保存  */
	$("#save").bind("click",function(){
		if (!$("#form_advert").valid()) {
			return;
		}
		$.ajax({
			type : "POST",
			contentType : "application/json;charset=utf-8", 
			url : contextPath + '/admin/advert/save',
			data : formToJson($("#form_advert")),
			dataType : "json", 
			async : false,
			success : function(json) {
				$("#id").val(json.data.id)
				swal({
				    title: "保存成功!",
				    text: "",
				    type: "success"
				});
			},
			error : function(json) {
				alert(json);
			}
		});
	});
	
	/** 获取from表单数据  */
	function formToJson($form) {
		var data = {};
		$($form.serializeArray()).each(function(i, v) {
			data[v.name] = v.value;
		});
		return JSON.stringify(data);
	}
	
</script>
</body>
</html>
```

## 订单管理

##### Java compareTo() 方法

- 如果参数字符串等于此字符串，则返回值 0；
- 如果此字符串小于字符串参数，则返回一个小于 0 的值；
- 如果此字符串大于字符串参数，则返回一个大于 0 的值。

```java
	/**
	 * 图表数据来源
	 * APIController
	 * @param dateTime
	 * @return
	 * @throws ParseException
	 */
	@RequestMapping(value = "/order/data", method = RequestMethod.POST, headers = "Accept=application/json")
	public ApiResponseObject getOrderData(@RequestBody Map<String, Object> rmap) throws ParseException {
		DateFormat dateFormat = DateFormat.getDateInstance();
		Date beginDate = dateFormat.parse(rmap.get("beginDate").toString());
		Date endDate = dateFormat.parse(rmap.get("endDate").toString());
		if (beginDate.compareTo(endDate) >= 1) {
			return reponseJSON(ApiResponseEnum.FAIL.getCode(), ApiResponseEnum.FAIL.getName(), "开始日期不能大于结束日期");
		}
		return reponseJSON(ApiResponseEnum.SUCCESS.getCode(), ApiResponseEnum.SUCCESS.getName(),
				orderService.getOrderDate(rmap));
	}
	//Service
	public Map<String, List<String>> getOrderDate(Map<String, Object> reqMap);	

	//ServiceImpl
	@Override
	public Map<String, List<String>> getOrderDate(Map<String, Object> reqMap) {
		String beginDate = reqMap.get("beginDate").toString();
		beginDate = beginDate.substring(0, 7) + "-01";
		String endDate = reqMap.get("endDate").toString();
		endDate = endDate.substring(0, 7) + "-01";
		List<String> listCount = new ArrayList<>();
		List<String> listDate = new ArrayList<>();
		List<String> listPrice = new ArrayList<>();
		List<Map<String, Object>> orderData = orderMapper.getOrderDate(beginDate, endDate);
		if (orderData != null) {
			for (Map<String, Object> map : orderData) {
				listDate.add(map.get("date").toString());
				listCount.add(map.get("count").toString());
				listPrice.add(map.get("price").toString());
			}
		}
		Map<String, List<String>> resultMap = new HashMap<>();
		resultMap.put("count", listCount);
		resultMap.put("date", listDate);
		resultMap.put("price", listPrice);
		return resultMap;
	}
	
	Mapper.java
        List<Map<String, Object>> getOrderDate(@Param("beginDate")String beginDate,@Param("endDate")String endDate);

	Mapper.xml
	<select id="getOrderDate" parameterType="java.lang.String"
		resultType="java.util.Map">
		SELECT
		COUNT( t3.order_num ) AS `count`,
		IFNULL( SUM( t3.price ), 0 ) / 1000000 AS `price`,
		DATE_FORMAT( t2.date, '%Y.%m' ) AS `date`
		FROM
		(
		SELECT
		@date := DATE_ADD( @date, INTERVAL - 1 MONTH ) AS `date`
		FROM
		( SELECT @date := DATE_ADD( #{endDate}, INTERVAL + 1 MONTH ) FROM
		order_info ) t1
		WHERE
		@date >= DATE_ADD( #{beginDate}, INTERVAL + 1 MONTH )
		) t2
		LEFT JOIN order_info t3 ON DATE_FORMAT( t2.date, '%Y.%m' ) = DATE_FORMAT(
		t3.create_time, '%Y.%m' )
		GROUP BY
		t2.date
	</select>
```



```jsp
<%@ page language="java" contentType="text/html; charset=UTF-8"
	pageEncoding="UTF-8"%>
<%@ taglib prefix="spring" uri="http://www.springframework.org/tags"%>
<%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core"%>
<%@page session="true"%>
<!DOCTYPE html>
<html>
<head>
<meta charset="utf-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<meta http-equiv="X-UA-Compatible" content="IE=edge">
<meta name="_csrf" content="${_csrf.token}" />
<meta name="_csrf_header" content="${_csrf.headerName}" />
<title>商城后台</title>
<jsp:include page="../commonCss.jsp" />
</head>
<body class="blank">
	<jsp:include page="../main/top.jsp" />
	<jsp:include page="../main/left.jsp" />
	<jsp:include page="../commonJs.jsp" />
	<div id="wrapper">
		<div class="small-header transition animated fadeIn">
			<div class="content animate-panel">
				<div class="hpanel">
					<div class="panel-body">
						<div class="row">
							<div class="hpanel">
								<div id="error_div" class="text-left"></div>
								<form id="form_userInfo" name="form_userInfo" method="POST">
									<div class="form-group col-lg-6">
										<label>选择区间</label>
										<div class="input-group date">
											<input name="searchTimeFrom" type="text" class="form-control"/>
											<span class="input-group-addon"><i
												class="glyphicon glyphicon-calendar"></i></span>
										</div>
									</div>
									<div class="form-group col-lg-6">
										<label></label>
										<div class="input-group date">
											<input name="searchTimeTo" type="text" class="form-control"/>
											<span class="input-group-addon"><i
												class="glyphicon glyphicon-calendar"></i></span>
										</div>
									</div>
									<div class="text-right m-t-xs">
										<button id="search" class="btn btn-success"
											style="margin-right: 5px; margin-top: 5px" type="button">
											<strong>查询</strong>
										</button>
										<button id="reset" class="btn btn-success"
											style="margin-right: 5px; margin-top: 5px"
											style="margin-right: 20px;" type="reset">
											<strong>重置</strong>
										</button>
									</div>
								</form>
							</div>
						</div>
					</div>
				</div>

			</div>

		</div>
		<div class="content animate-panel">
			<div class="row">
				<div class="col-lg-12">
					<div class="hpanel">
						<div class="panel-body">
							<div id="char" style="width: 100%; height: 600px;"></div>
						</div>
					</div>
				</div>
			</div>
		</div>
	</div>

	<form id="forwordform"
		action='<c:url value="/admin/order/History?${_csrf.parameterName}=${_csrf.token}" />'
		method='POST'>
		<input type="hidden" name="userId" id="userId" /> <input
			type="hidden" name="roleStr" id="roleStr" /> <input type="hidden"
			name="orderNum" id="ordernum">

	</form>
	<script type="text/javascript">
		$('#leftMenuOrder').addClass('active');
		$('.date').datepicker();
		var myChart = echarts.init(document.getElementById('char'));
		var option = {
	            tooltip: {},
	            legend: {
	                data:['订单数(条)','订单金额(万元)']
	            },
	            xAxis: {
	                data: ${data.date}
	            },
	            yAxis: {},
	            series: [{
	                name: '订单数(条)',
	                type: 'bar',
	                data: ${data.count},
	                markPoint: {
	                   data: [
	                       {type: 'max'},
	                       {type: 'min'}
	                   ]
	               },
	               markLine: {
	                   data: [
	                       {type: 'average'}
	                   ]
	               }

	            },
	            {
	            	name: '订单金额(万元)',
	                type: 'bar',
	                data: ${data.price},
	                markPoint: {
	                   data: [
	                       {type: 'max'},
	                       {type: 'min'}
	                   ]
	               },
	               markLine: {
	                   data: [
	                       {type: 'average'}
	                   ]
	               }

	            }
	            ]
	        };
		myChart.setOption(option);
		$('#search').click(function() {
			var beginDate=$('[name=searchTimeFrom]').val();
			var endDate=$('[name=searchTimeTo]').val();
			if (beginDate==""||endDate=="") {
				return false;	
			}
			searchOrderData(beginDate,endDate);
		});
		
		function searchOrderData(beginDate,endDate){
			$.ajax({
				contentType : 'application/json;charset=utf-8',
				url : '/Shopping/api/v1/order/data',
				type : "POST",
				data : JSON.stringify({"beginDate":beginDate,"endDate":endDate}),
				dataType : "json", //ajax返回值设置为text(json格式也可用它返回，可打印出结果，也可设置成json)  
				success : function(result) {
					result=result.data;
					console.log(result);
					option.xAxis.data=result.date;
					option.series[0].data=result.count;
					option.series[1].data=result.price;
					myChart.setOption(option);
				}
			});
		}
	</script>
</body>
</html>
```

