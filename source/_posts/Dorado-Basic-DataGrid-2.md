---
title: Dorado系列之基础篇:3.单表维护模块优化
date: 2018-05-25 11:06:24
tags: [Doarda7]
categories: [Doarda7]
---

之前我们已经新建一个单表维护模块,但是在许多的细节方面还没有进行优化.

<!--more-->

### BDF2中获取当前登录人:
```Java
public static IUser getLoginUser()	获取当前登录的用户对象，如果用户未登录，这里将返回null。
```

### 表格创建人Id与创建名称优化:
现在我们表格模块中显示还是创建人Id,但是正常在页面显示的时候应该显示创建人的名称,这个时候我们需要怎么做呢?
我们需要修改后端数据提供接口:
```Java
@Override
	public void pageQuery(Page<TFileEntity> page, Collection<Condition> conditions,
			Collection<? extends SortField<?>> orderCollections) {
		// TODO Auto-generated method stub
		super.pageQuery(page, conditions, orderCollections);
		List<TFileEntity> fileList = (List<TFileEntity>) page.getEntities();

		if (fileList == null || fileList.isEmpty()) {
			return;
		}

		List<Long> userIdList = fileList.stream().map(f -> f.getCreateUserId()).collect(Collectors.toList());
		Map<Long, UserEntity> userMap = userDao.getUserBasicInfo(userIdList);

		for (TFileEntity en : fileList) {
			Long userId = en.getCreateUserId();
			if (userMap != null && userMap.containsKey(userId)) {
				en.setCreateUserName(userMap.get(userId).getName());
			}
		}
	}
```
然后修改视图中的createUserId字段改为createUserName.

### DataGrid中如何拖拽行并进行数据排序:
#### 设置DataGrid的droppable和draggable等属性
![cmd-markdown-logo](http://p8x1t721u.bkt.clouddn.com/Dorado-DataGrid-19.png)
设置这几个属性后就可以对数据行进行拖拽了。

#### 在DataGrid的onDraggingSourceDrop事件(当有某被拖拽对象在此对象的区域内被释放时触发的事件)中，对数据表中的行进行排序，即手动设置数据的排序值，假设数据中有一个字段orderNumber为排序值，则代码如下：
```javascript
var items=self.get("itemModel").getItems().toArray();
for (var i=0;i<items.length;i++){
    items.set("order",i);
}
```

#### 最后使用UpdateAction把dataSet中的数据保存到服务器即可。

### DataGrid全选或者多选删除:
DataGrid有selectAll()方法(全选)，除此之外还有unselectAll()(全部不选)和selectInvert()(反向选择)。
#### 删除选中的记录:
```javascript
grid.get("selection").slice(0).each(function(entity){
  entity.remove();
});
```
其中slice(0)是js克隆数组的方法。因为remove()操作会间接改变selection数组的长度，造成迭代逻辑出现问题。迭代前克隆一下就安全了。