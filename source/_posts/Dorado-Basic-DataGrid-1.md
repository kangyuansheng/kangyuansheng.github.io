---
title: Dorado系列之基础篇:2.新建一个单表维护模块
date: 2018-05-25 11:06:24
tags: [Doarda7]
categories: [Doarda7]
---

现在我们项目已经创建好了,那么就可以开始丧心病狂了.本篇将介绍在Dorado7中如何新建一个简单的单表维护模块.

<!--more-->

### 新建Entity
首先我们先来编写一个非常简单的Entity,我们就以文件模块来举例子.
```Java
@Data
@Entity
@Table(name = BimsConstants.TABLE_PREFIX + "file" + BimsConstants.TABLE_SUFFIX)
public class TFileEntity implements Serializable {

	private static final long serialVersionUID = 322581947710249241L;

	@Id
	@GeneratedValue(strategy = GenerationType.AUTO)
	@Column(name = "id_")
	private Long id;

	@Column(name = "file_path_", length = 100)
	private String filePath; // 文件路径

	@Column(name = "create_date_")
	private Date createDate; // 创建时间

	@Column(name = "file_size_")
	private Long fileSize; // 文件大小

	@Column(name = "file_name_")
	private String fileName; // 文件名字

	@Column(name = "remark_")
	private String remark; // 备注

	@Column(name = "create_user_id_")
	private Long createUserId; // 备注

	@Transient
	private String createUserName;
	
}
```

### 新建表
因为我在配置文件(todo:文件名称)中开启了根据Entity自动生成表结构的方式,所以我这里只需要定义好Entity就可以了.系统在我运行之后就会自动的在数据库中生成表结构.
todo配置

### 新建Dao
接下来我们需要定义一个Dao,Dao定义:因为这个框架我自己集成了Jooq,所以所有的Dao层定义都是基于Jooq的.
Ps:因为我是通过Entity去生成的表,一定要注意先要跑一遍项目生成数据对应的表,再去生成jooq.因为jooq生成的配置文件都是根据数据的表生成的.生成的文件名是:T+XXX(你的表名)+Record.

#### 生成jooq的方法:
右击项目->Run As->Maven generate-sources
![cmd-markdown-logo](http://p8x1t721u.bkt.clouddn.com/DataGrid-file-9.png)
这个时候我们在我们的项目下就可以看到系统自动生成的jooq文件了.
![cmd-markdown-logo](http://p8x1t721u.bkt.clouddn.com/DataGrid-file-10.png)

#### Dao定义例子:
```Java
@Repository
public class TFileDao extends JooqBaseDao<TFileRecord, FileEntity, Long> {
	@Override
	protected Long getId(FileEntity object) {
		// TODO Auto-generated method stub
		return object.getId();
	}
}
```
到这里,我们的Entity,Dao和表都已经建好了.


### 新建Service:
现在我们需要新建一个Service
```Java
@Component("fileService")
public class FileService {

}
```
### 新建视图:
我们新建一个file包,右击刚才新建的包->New->创建Dorado视图文件.
![cmd-markdown-logo](http://p8x1t721u.bkt.clouddn.com/DataGrid-file-11.png)

新建一个视图(FileView.view.xml),Dorado的视图都是这种xml文件.
![cmd-markdown-logo](http://p8x1t721u.bkt.clouddn.com/DataGrid-file-3.png)
### 新建DataType:
#### DataType介绍:
DataType是用来描述所有的数据类型，既包括String、int这样的简单类型，也包括数据实体这样的复杂类型。DataType分为以下三类:

- 简单DataType，如String、int、Boolean、Date
- 聚合DataType，如List、Set、Array
- 实体DataType，如POJO Bean、Map

默认提供的DataType类型的查找方式可以通过dataType属性提供的编辑框查看其base中的DataType，这些都是默认提供的DataType：
![cmd-markdown-logo](http://p8x1t721u.bkt.clouddn.com/DataGrid-file-1.png)
#### 在视图中添加一个DataType控件:
首先我们点击左侧的查询列表在搜索框中输入DataType.
![cmd-markdown-logo](http://p8x1t721u.bkt.clouddn.com/DataGrid-file-12.png)
此时我们双击列表中的DataType控件就可以把DataType控件加入到我们的view视图中.

#### 为DataType控件添加具体的属性:
| 字段名        | 含义   |
| --------   | -----:  |
| name     | 数据类型的名称 |
| creationType        |   数据类型的定义/路径   |

#### 为DataType控件生成PropertyDef属性:
利用DataType的右键向导自动生成内部的PropertyDef.
![cmd-markdown-logo](http://p8x1t721u.bkt.clouddn.com/DataGrid-file-8.png)
当系统生成内部的PropertyDef成功之后显示为:
![cmd-markdown-logo](http://p8x1t721u.bkt.clouddn.com/DataGrid-file-2.png)

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
![cmd-markdown-logo](http://p8x1t721u.bkt.clouddn.com/DataGrid-file-13.png)

#### 在Service中为DataSet控件添加获取数据的java方法:
@DataProvider注解为标识此方法为数据提供者.
```Java
@Component("fileService")
public class FileService {

	private TFile SQL_FILE = TFile.T_FILE;

	@Autowired
	private TFileDao fileDao;

	@DataProvider
	public void loadFileList(Page<TFileEntity> page) {
		List<Condition> conds = new ArrayList<>();

		List<SortField<Timestamp>> sort = new ArrayList<>();
		sort.add(SQL_FILE.CREATE_DATE.desc());

		fileDao.pageQuery(page, conds, sort);
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

![cmd-markdown-logo](http://p8x1t721u.bkt.clouddn.com/DataGrid-file-14.png)

好了,我们跑一下服务器.进入页面就可以看到一个数据表格了.
![cmd-markdown-logo](http://p8x1t721u.bkt.clouddn.com/DataGrid-file-15.png)

但是表格中的每一列的列名都是字段名称,看上去感觉很不好.其实我们只需要通过修改文件的DataType的某个PropertyDef的label属性就可以改变它了.
![cmd-markdown-logo](http://p8x1t721u.bkt.clouddn.com/DataGrid-file-16.png)

### 添加ToolBar(工具栏):
#### 在ToolBar控件下添加DataPilot控件:
| 字段名        | 含义   |
| --------   | -----:  |
| id     | DataGrid的id |
| dataSet        |   数据控件绑定的数据集。   |
| dataPath        |   数据路径，用于指定数据控件与数据集中的哪些数据节点进行关联。   |
![cmd-markdown-logo](http://p8x1t721u.bkt.clouddn.com/DataGrid-file-17.png)

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
![cmd-markdown-logo](http://p8x1t721u.bkt.clouddn.com/DataGrid-file-18.png)


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
#### 要实现对数据进行增删改操作就需要在Service中添加增删改的方法,因为在Dorado中有DataSet这个数据集,在Dorado中对数据的增删改操作时候产生的数据都在这个DataSet中,它是通过状态来区分的.

```Java
@DataResolver
	public void saveFileList(List<TFileEntity> datas, Map<String, Object> param) {
		if (datas == null || datas.isEmpty()) {
			return;
		}
		for (TFileEntity fileEntity : datas) {
			// dorado用来判断当前Entity的状态
			EntityState es = EntityUtils.getState(fileEntity);
			// 新增
			if (EntityState.NEW.equals(es)) {
				fileDao.insert(fileEntity);
			} else if (EntityState.MODIFIED.equals(es)) {// 修改
				fileDao.update(fileEntity);
			} else if (EntityState.DELETED.equals(es)) {// 删除
				fileDao.delete(fileEntity);
			}
		}
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

到这里表格模块已经基本完成了.