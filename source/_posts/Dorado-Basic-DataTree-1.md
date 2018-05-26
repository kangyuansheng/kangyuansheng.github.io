---
title: Dorado系列之基础篇:4.递归树模块
date: 2018-05-25 14:24:24
tags: [Doarda7]
categories: [Doarda7]
---

之前我们已经新建了一个DataGrid(表格)模块,对于Dorado已经有了一个基本的了解.现在我们再来继续讲一个常用的控件:DataTree(树)

<!--more-->

### 新建Entity,表结构,jooq,Dao
首先我们先来定义好一个树的Entity,我们就以文件夹模块来举例子.
```Java
@Id
@GeneratedValue(strategy = GenerationType.AUTO)
@Column(name = "id_")
private Long id;

@Column(name = "name_", length = 100)
private String name; // 名称

@Column(name = "parent_id_")
private Long parentId; // 父Id

@Column(name = "order_")
private int order; // 排序
```
剩下的部分的建立方式同上一节相同.再次就不在进行讲述.
到这里,我们的Entity,Dao和表都已经建好了.


### 新建Service并且添加数据查询方法:
现在我们需要新建一个Service
```Java
@Component("folderService")
public class FolderService {

	private TFolder SQL_FOLDER = TFolder.T_FOLDER;

	@Autowired
	private TFolderDao folderDao;

	@DataProvider
	public List<TFolderEntity> loadFolderList(Long parentId) {
		List<Condition> conds = new ArrayList<>();

		// 通过判断父Id是否存在来判断是查询的根节点还是子节点
		if (parentId != null && parentId > 0) {
			conds.add(SQL_FOLDER.PARENT_ID.eq(parentId));
		} else {
			conds.add(SQL_FOLDER.PARENT_ID.isNull());
		}

		List<SortField<Integer>> sort = new ArrayList<>();
		sort.add(SQL_FOLDER.ORDER.desc());

		return folderDao.getDsl().selectFrom(SQL_FOLDER).where(conds).orderBy(sort).fetchInto(TFolderEntity.class);
	}

}
```

### 新建视图:
在视图中添加一个DataType控件(TFolderEntity),生成PropertyDef属性.
在视图中添加一个DataSet控件,并且设置dataProvider为刚才Service层中的数据方法.
在DataType控件中添加一个EntityList(用于指向当前Entity下的所有孩子的集合)
![cmd-markdown-logo](http://p8x1t721u.bkt.clouddn.com/Dorado-DataTree-1.png)
懒加载的数据只有当需要的时候才会去后端调用方法.

### 添加DataTree(树控件):
#### 为DataTree控件添加具体的属性:
| 字段名        | 含义   |
| --------   | -----:  |
| id     | 树控件的id |
| labelProperty        |   以当前节点对应的实体对象的label属性作为树节点的标题信息   |
| name        |   名称  |
| recursive        |   是否支持递归处理  |
| childrenProperty        |   获取当前节点的categories属性，作为子节点的数据  |
![cmd-markdown-logo](http://p8x1t721u.bkt.clouddn.com/Dorado-DataTree-2.png)


#### 添加右击显示菜单:
DataTree控件的onContextMenu:当控件中的系统上下文菜单将要显示时触发的事件。
``` javascript
/**
 * 树被点击的时候弹出下拉菜单
 */
// @Bind #diaTree.onContextMenu
!function(self, arg) {
	view.id("mainMenu").show({
		position : {
			left : arg.event.pageX,
			top : arg.event.pageY
		}
	});
}
```
菜单项组(Menu)下的方法:show():显示浮动控件。
position:组件显示的位置，该参数应该为Object，用left、top属性表示绝对值。

#### 插入子节点:
``` javascript
// @Bind #addMenuItemChild.onClick
!function(self,arg) {
	//获取当前树
	var dataTree = view.get("#dataTree");
	//获取当前节点
	var currentNode = dataTree.get("currentNode");
	if (!currentNode) {
		throw new dorado.Exception("请先点中一个树节点！");
	}

	//获取当前节点的数据
	var currentEntity = currentNode.get("data");
	//获取当前节点下的孩子
	var childList = currentEntity.get("childList");
	//以异步的方式展开子节点
	currentNode.expandAsync(function() {
		childList.insert();
	});
	
	//获取当前节点的孩子集合的刚才新增加的Entity
	var lastEntity = childList.getLast();
	//设置新增加的Entity为当前实体
	dataTree.set("currentEntity", lastEntity);
	
	goDiaLog(view.get("#diaLogFolder"),"添加子文件夹",false,"url(>skin>common/icons.gif) -20px -40px","40%","50%");
}
```
这里DiaLog中的具体配置就不在进行过多的描述了.

#### 插入同级节点:
``` javascript
// @Bind #addMenuItem.onClick
!function(self,arg) {
	//获取当前树
	var dataTree = view.get("#dataTree");
	
	var currentNode = dataTree.get("currentNode");
	if (!currentNode) {
		throw new dorado.Exception("请先点中一个树节点！");
	}
	//获取当前节点的数据
	var currentEntity = currentNode.get("data");
	//获取当前节点的父数据集	
	var parentList = currentEntity.parent;
	
	parentList.insert();
	
	//获取当前节点的孩子集合的刚才新增加的Entity
	var lastEntity = parentList.getLast();
	
	//设置新增加的Entity为当前实体
	dataTree.set("currentEntity", lastEntity);
	
	goDiaLog(view.get("#diaLogFolder"),"添加同级文件夹",false,"url(>skin>common/icons.gif) -20px -40px","40%","50%");
}
```
在diaLog中保存按钮和取消按钮同dataGrid相同,在此就不在进行讲解.其他的UpdateAction或者后端java方法都和前面的dataGrid中的很类似，这里就不进行描述了.(修改同理).

#### 获取树选中的节点:
为什么要单独的描述一下获取树选中的节点呢?
这是因为在树之中获取DataSet当前选中的节点的方式比较特殊.
以前我们在其他的地方获取当前dataSet选择的记录都是通过:
``` javascript
	var dataSet = view.get("#dataSet");
	var currentEntity = dataSet.getData("#");
```
当时这种方式用在树里面的时候获取的永远是dataSet的第一条记录.
这个时候我们要怎么办呢？
``` javascript
	var dataSet = view.get("#dataSet");
	var currentEntity = dataSet.getData("!current_node");
```
我们可以通过这种方式去获取.这里的current_node是在DataTree中配置的.
![cmd-markdown-logo](http://p8x1t721u.bkt.clouddn.com/Dorado-DataTree-3.png)