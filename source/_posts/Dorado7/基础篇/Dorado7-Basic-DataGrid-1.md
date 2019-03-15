---
title: Dorado7系列之基础篇:2.新建一个单表维护模块
date: 2018-09-08 09:25:00
author: 康源晟
top: false
cover: false
toc: false
mathjax: false
summary: 新建一个单表维护模块
categories: Dorado7
tags:
  - Dorado7
---

本篇将介绍在Dorado7中如何新建一个简单的单表维护模块.

### 新建Entity

首先我们需要新建一个Entity：

```java
/**
  * 文件Entity
 * 
 * @author kangyuansheng
 *
 */
@Data
@Entity
@Table(name = BimsConstants.TABLE_PREFIX + "file" + BimsConstants.TABLE_SUFFIX)
public class TFileEntity implements Serializable {

	/**
	 * 
	 */
	private static final long serialVersionUID = -324997129956689919L;

	@Id
	@GeneratedValue(strategy = GenerationType.AUTO)
	@Column(name = "id_")
	private Long id;

	@Column(name = "file_path_", length = 255, nullable = false)
	private String filePath; // 文件路径

	@Column(name = "file_size_", nullable = false)
	private Long fileSize; // 文件大小

	@Column(name = "file_name_", nullable = false, length = 1000)
	private String fileName; // 文件名字

	@Column(name = "file_type_", nullable = false, length = 20)
	private FileFormatEnum fileType; // 文件类型

	@Column(name = "create_user_name_", nullable = false, length = 255)
	private String createUserName; // 创建人name

	@Column(name = "create_date_", nullable = false)
	private Date createDate; // 创建时间

	@Column(name = "remark_", nullable = true, length = 1000)
	private String remark; // 备注

}
```

然后我们跑一下项目就可以生成对应的表了。因为我们是使用Dorado JPA，它已经封装好了通用的JpaUtil，所以不需要我们再写dao了。当然你可以采用Spring Data JPA，也是支持的。

### 新建FileService和它的实现类

```java
/**
 * 文件Service
 */
@Service("fileService")
public class FileServiceImpl implements FileService {

}
```

### 新建视图:

我们新建一个file包,右击刚才新建的包->New->创建Dorado视图文件.

新建一个视图(FileView.view.xml),Dorado的视图都是这种xml文件.
![cmd-markdown-logo](http://kyshblogs.oss-cn-beijing.aliyuncs.com/Dorado7/Basic-Paper/Dorado7-BasicPaper-0001.png)
### 新建DataType:
#### DataType介绍:
DataType是用来描述所有的数据类型，既包括String、int这样的简单类型，也包括数据实体这样的复杂类型。DataType分为以下三类:

- 简单DataType，如String、int、Boolean、Date
- 聚合DataType，如List、Set、Array
- 实体DataType，如POJO Bean、Map

默认提供的DataType类型的查找方式可以通过dataType属性提供的编辑框查看其base中的DataType。

#### 在视图中添加一个DataType控件:

首先我们点击左侧的查询列表在搜索框中输入DataType.
![cmd-markdown-logo](http://kyshblogs.oss-cn-beijing.aliyuncs.com/Dorado7/Basic-Paper/Dorado7-BasicPaper-0002.png)
此时我们双击列表中的DataType控件就可以把DataType控件加入到我们的view视图中.

#### 为DataType控件添加具体的属性:
| 字段名        | 含义   |
| --------   | -----:  |
| name     | 数据类型的名称 |
| creationType        |   数据类型的定义/路径   |

#### 为DataType控件生成PropertyDef属性:
利用DataType的右键向导自动生成内部的PropertyDef，当系统生成内部的PropertyDef成功之后显示为:
![cmd-markdown-logo](http://kyshblogs.oss-cn-beijing.aliyuncs.com/Dorado7/Basic-Paper/Dorado7-BasicPaper-0003.png)

### 添加DataSet:
#### DataSet介绍:
DataSet最主要的功能就是为一份数据赋予一个名称，使这份数据可以以配置的方式被其他控件引用，以便于建立绑定关系。
#### 为DataSet控件添加具体的属性:
| 字段名        | 含义   |
| --------   | -----:  |
| id     | DataSet的id |
| dataProvider        |   根据路径找到对应的方法获取数据(BeanId#方法名)   |
| dataType        |   指定数据集的数据类型(可以指定二种形式:a.Default:对象，b.Collection:数组)   |
| parameter        |   每页显示的记录数   |
配置完成之后显示如下:
![cmd-markdown-logo](http://kyshblogs.oss-cn-beijing.aliyuncs.com/Dorado7/Basic-Paper/Dorado7-BasicPaper-0004.png)

#### 修改FileService中为DataSet控件添加获取数据的java方法:

```Java
@Override
@Transactional(readOnly = true)
public void load(Page<TFileEntity> page, Criteria criteria) {
    //根据查询条件分页查询数据
	JpaUtil.linq(TFileEntity.class).where(criteria).paging(page);

	Collection<TFileEntity> list = page.getEntities();
	if (list == null || list.isEmpty()) {
		return;
	}
    
    //java8写法:获取list集合中的所有的创建人的主键的集合
	List<String> createUserKeys = list.stream().map(i -> i.getCreateUserKey()).distinct().collect(toList());

    //根据用户主键的集合查询出对应的用户信息集合
	List<User> userList = userService.getUserListByUserKeyList(createUserKeys);
    //java8写法:将查询出来的用户信息集合按照用户主键封装成Map
	Map<String, User> userMap = userList.stream().collect(toMap(User::getUsername, u -> u));

    //遍历文件集合list，根据用户信息Map将每一个创建人的名称封装到集合中去
	for (TFileEntity entity : list) {
		if (userMap != null && userMap.containsKey(entity.getCreateUserName())) {
			entity.setCreateUserName(userMap.get(entity.getCreateUserName()).getNickname());
		}
	}
}
```

新建Controller层：

```java
/**
 * 文件Controller
 */
@Controller("fileController")
public class FileController {
    
    @Autowired
    protected FileService fileService;
    
    /**
     * 文件列表数据查询
     *
     * @param page
     * @param criteria
     */
    //@DataProvider注解为标识此方法为数据提供者。
    @DataProvider
    public void getFileList(Page<TFileEntity> page, Criteria criteria) {
        fileService.load(page, criteria);
    }
    
}
```



### 添加DataGrid(数据表格控件):

#### 为DataGrid控件添加具体的属性:

| 字段名        | 含义   |
| --------   | -----:  |
| id     | DataGrid的id |
| dataSet        |   数据控件绑定的数据集。   |
| dataPath        |   数据路径，用于指定数据控件与数据集中的哪些数据节点进行关联。   |

![cmd-markdown-logo](http://kyshblogs.oss-cn-beijing.aliyuncs.com/Dorado7/Basic-Paper/Dorado7-BasicPaper-0005.png)

添加完DataGrid之后，我们需要生成一下表格需要显示的列。[右击表格]->[Generate DataColumns]即可。但是这样会根据字段生成所有的列，这时我们把不需要的列删除就ok了。

好了,我们跑一下服务器.进入页面就可以看到一个数据表格了.

但是表格中的每一列的列名都是字段名称,看上去感觉很不好.其实我们只需要通过修改文件的DataType的某个PropertyDef的label属性就可以改变它了.

### 添加ToolBar(工具栏):
#### 在ToolBar控件下添加DataPilot控件:
| 字段名        | 含义   |
| --------   | -----:  |
| id     | DataGrid的id |
| dataSet        |   数据控件绑定的数据集。   |
| dataPath        |   数据路径，用于指定数据控件与数据集中的哪些数据节点进行关联。   |
#### 在ToolBar控件下添加Button(按钮)控件:
| 字段名        | 含义   |
| --------   | -----:  |
| id     | Button的id |
| caption        |   按钮的标题。   |
| icon        |   图标所在路径。   |

#### 添加该视图的js文件:
注意:这里的js名称一定要视图名称保持一直要不然程序检测不到.

#### 为Button(按钮)添加点击事件:
``` javascript
//@Bind #addButton.onClick
!function (self,arg) {
	//获取视图中的dataSet控件
	var dataSetFile = view.get("#dataSetFile");
	//获取dataSet控件的数据集
	var dataFileList = dataSetFile.getData();
	//在dataSet数据集中新增一条记录
	dataFileList.insert();
}
```
//@Bind 是dorado的固定写法,#xxx 是获取view中的id为xxx的控件.onClick则是点击事件.这里想要用什么事件就写什么.

### 添加DiaLog(对话框):
#### 为DiaLog控件添加具体的属性:
| 字段名        | 含义   |
| --------   | -----:  |
| id     | Button的id |
| caption        |   按钮的标题。   |
| icon        |   图标所在路径。   |
| width        |   宽度   |
| height        |   高度   |

在这里不推荐把DiaLog中的标题,图标之类的属性直接在DiaLog属性面板中编写.
比较推荐:
``` javascript
/**
 * 打开DiaLog
 */
function goDiaLog(diaLog, title, closeable, iconUrl) {
	diaLog.set("closeable", closeable);
	diaLog.set("caption", title);
	diaLog.set("icon", iconUrl);
	diaLog.show();
}
```

#### DiaLog控件的方法:
close():关闭面板。
show():显示浮动控件。
hide():隐藏浮动控件。

现在我们来补全刚才的js方法.
``` javascript
//@Bind #addButton.onClick
!function (self,arg) {
	//获取视图中的dataSet控件
	var dataSetFile = view.get("#dataSetFile");
	//获取dataSet控件的数据集
	var dataFileList = dataSetFile.getData();
	//在dataSet数据集中新增一条记录
	dataFileList.insert();
	var diaLog = view.get("#diaLogFile");
	diaLog.show();
	
	goDiaLog(view.get("#diaLogFile"),"添加文件",false,"url(>skin>common/icons.gif) -20px -40px");
}
```
但是这个时候我们看到我们的弹出窗口中什么东西也没有，这是因为我们还没有为它添加控件.

### 添加AutoForm(表单):
AutoForm是一种容器性的控件，可以在内部添加很多子控件。一般情况下都是添加AutoFormElement控件。
#### 为AutoForm控件添加具体的属性:
| 字段名        | 含义   |
| --------   | -----:  |
| id     | Button的id |
| cols        |   表单布局的分栏方式。   |
| dataSet        |  绑定的数据集。   |

### 为DiaLog控件添加Button(按钮)并且绑定具体的点击事件:
现在我们要为这个弹出窗口添加二个按钮,一个保存，一个取消按钮.

#### 为保存按钮绑定具体的点击事件:
既然是保存按钮的话，我们在这里肯定是要实现保存的功能的.
在这里既然要实现保存功能，我们就会用到的我们的一个UpdateAction(提交控件).
这个UpdateAction(提交控件)要怎么用呢？
我们先来看它的具体的属性:

| 字段名        | 含义   |
| --------   | -----:  |
| id     | 提交控件的id |
| confirmMessage        |   当此Action将要执行之前自动提示用户进行确认操作的提示信息。如果定义了此属性，在Action将要执行之前系统会自动向用户确认是否继续执行。   |
| dataSet        |  绑定的数据集。   |
| successMessage        |  当此Action执行成功之后自动提示的信息。   |
| dataResolver        |  数据处理器。   |
![cmd-markdown-logo](http://kyshblogs.oss-cn-beijing.aliyuncs.com/Dorado7/Basic-Paper/Dorado7-BasicPaper-0006.png)


现在我们来编写保存按钮的点击事件:
``` javascript
//@Bind #btnDialogSave.onClick
!function (self,arg) {
	var updateAction = view.get("#updateAction");
	updateAction.execute();
	view.get("#diaLogFile").hide();
}
```

### 对数据进行增删改操作
要实现对数据进行增删改操作就需要在Service中添加增删改的方法,因为在Dorado中有DataSet这个数据集,在Dorado中对数据的增删改操作时候产生的数据都在这个DataSet中,它是通过状态来区分的.

#### 修改FileService中为DataSet控件添加增删改的java方法:

```Java
/**
 * @param data 文件Entity
 * @return 文件Entity
 * @Description:保存文件
 */
@Override
@Transactional(rollbackFor = Exception.class)
public void saveFile(List<TFileEntity> datas) {
	/**
	 * 保存到数据库，当保存完成之后fileEntity中的id已经存在了。
	 * JpaUtil是bdf3中封装的操作数据库的工具类
	 * save：批量智能保存数据（根据EntityState执行对应的persist、merge和remove）
	 * 相当于这个方法save会根据entity的状态去自动的进行增加、修改、删除操作。
	 */
	JpaUtil.save(datas, new SmartSavePolicyAdapter() {
		/**
		 * 新增数据之前操作方法
		 */
		@Override
		public boolean beforeInsert(SaveContext context) {
			if (context.getEntity() instanceof TFileEntity) {
				TFileEntity entity = context.getEntity();
				//保存当前时间
				entity.setCreateDate(new Date());
				// 获取当前创建人
				User user = ContextUtils.getLoginUser();
				//保存创建人的主键
				entity.setCreateUserKey(user.getUsername());
			}
			return true;
		}

	});
}
```
这里贴一下JpaUtil具体的介绍与使用方法:[JpaUtil介绍与使用方法]()

#### 修改Controller层：

```java
/**
 * 文件列表数据保存
 *
 * @param page
 * @param criteria
 */
@DataResolver
public void saveFileList(List<TFileEntity> datas) {
	categoryService.save(datas);
}
```

其中@DataResolver是用于声明这个Java方法为一个DataResolver方法，可以被客户端的DataResolver技术所调用.
而DataResolver用于将DataSet中的数据抽取出来发送到Server端，以便于Server端进行相应的处理。
对于修改的按钮与新增基本类似,在此就不在过多讲述了.
对于删除的话，比较特殊.因为删除的点击事件会调用一个新的方法.

``` javascript
//获取视图中的dataSet控件
var dataSetFile = view.get("#dataSetFile");
//获取dataSet控件的数据集
var dataFileList = dataSetFile.getData();
//删除当前数据Entity
dataFileList.remove();
```

到这里表格模块已经基本完成了，这里贴一下完成之后的大致的结构。
![cmd-markdown-logo](http://kyshblogs.oss-cn-beijing.aliyuncs.com/Dorado7/Basic-Paper/Dorado7-BasicPaper-0007.png)

贴一下类似的js

```js
/**
 * 右侧表格：页面加载时规格属性显示设置
 */
// @Bind #hasAvailable.onRenderCell
!function(self, arg) {
	var hasAvailable = arg.data.get("hasAvailable");
	if (hasAvailable) {
		arg.dom.innerHTML = "可用";
	} else {
		arg.dom.innerHTML = "不可用";
	}
}

/**
 * 新增常用联系人
 */
// @Bind #buttonAdd.onClick
!function() {
	var entityList = view.get("#dataSet").getData();

	var ajaxActionGetLoginUser = view.get("#ajaxActionGetLoginUser");
	ajaxActionGetLoginUser.execute(function(user) {
		entityList.insert({
			createUserName : user.username,
			createUserNickName : user.nickname,
			hasAvailable : true
		});
	});

	goAddDiaLog(view.get("#dialog"), "新增常用联系人");
}

/**
 * 修改常用联系人
 */
// @Bind #buttonModify.onClick
!function() {
	var dataSet = view.get("#dataSet");
	var data = dataSet.getData("#");
	if (!data) {
		dorado.MessageBox.alert("请选择需要修改的常用联系人记录");
		return;
	}

	goModifyDiaLog(view.get("#dialog"), "修改常用联系人");
}

/**
 * 查看常用联系人详情
 */
// @Bind #buttonView.onClick
!function() {
	var dataSet = view.get("#dataSet");
	var data = dataSet.getData("#");
	if (!data) {
		dorado.MessageBox.alert("请选择需要查看的常用联系人记录");
		return;
	}

	goViewDiaLog(view.get("#dialogView"), "查看常用联系人详情");
}

/**
 * 删除常用联系人
 */
// @Bind #buttonDelete.onClick
!function() {
	var dataSet = view.get("#dataSet");
	var data = dataSet.getData("#");
	if (!data) {
		dorado.MessageBox.alert("请选择需要删除的常用联系人记录");
		return;
	}

	dorado.MessageBox.confirm("是否彻底删除该常用联系人?(彻底删除肯定会导致数据出现问题)", function() {
		var entity = view.get("#dataSet").getData("#");
		entity.remove();
	});
}

/**
 * 仓库弹出框:确定
 */
// @Bind #btnDialogSave.onClick
!function() {
	dorado.MessageBox.confirm("是否确定保存当前常用联系人?", function() {
		var entity = view.get("#dataSet").getData("#");
		entity.set("createDt", new Date());

		var updateAction = view.get("#updateAction");
		updateAction.execute(function() {
			closeDiaLog(view.get("#dialog"));
		});
	});
}

/**
 * 仓库弹出框:取消
 */
// @Bind #btnDialogCancel.onClick
!function() {
	dorado.MessageBox.confirm("是否放弃操作当前常用联系人?", function() {
		var entity = view.get("#dataSet").getData("#");
		entity.cancel();
		closeDiaLog(view.get("#dialog"));
	});
}
```

