[TOC]

# 1.数据网格datagrid



## 1.说明

> datagrid ：以表格格式显示数据，并为选择、排序、分组和编辑数据提供了丰富的支持。数据网格（datagrid）的设计目的是为了减少开发时间，且不要求开发人员具备指定的知识。它是轻量级的，但是功能丰富。它的特性包括单元格合并，多列页眉，冻结列和页脚，等等。



![https://www.jeasyui.net/Uploads/images/datagrid.png]()



## 2.使用



### 创建



> html定义列，通过html来创建

```html
<table class="easyui-datagrid">
    <thead>
		<tr>
			<th data-options="field:'code'">Code</th>
			<th data-options="field:'name'">Name</th>
			<th data-options="field:'price'">Price</th>
		</tr>
    </thead>
    <tbody>
		<tr>
			<td>001</td><td>name1</td><td>2323</td>
		</tr>
		<tr>
			<td>002</td><td>name2</td><td>4612</td>
		</tr>
	</tbody>
</table>
```

> 通过js来创建

```js
<table id="panal"></table>
```

```js
$('#panal').datagrid({
    url:'datagrid_data.json', //可有可无
    fit: true, //填充当前表格容器
    fitColums: true, // 自适应列宽
    pagination: true, //分页控件
    pageSize: 20,//默认分页大小
    pageList: [10,20,50], // 可选择的分页数组
    idFidld: 'SMSNO', //记住非当前页的选中项
    //表格属性还有很多
    columns:[[
		{field:'code',title:'Code',width:100},
		{field:'name',title:'Name',width:100},
		{field:'price',title:'Price',width:100,align:'right'}
    ]]
});
```



### 常用数据网格(datagrid)的属性

> 常用的如下。 更多的看文档

| 名称          | 类型         | 描述                                                         | 默认值             |
| :------------ | :----------- | :----------------------------------------------------------- | :----------------- |
| columns       | array        | 数据网格（datagrid）的列（column）的配置对象，更多细节请参见列（column）属性。 如 上用例代码 | undefined          |
| frozenColumns | array        | 和列（column）属性一样，但是这些列将被冻结在左边。           | undefined          |
| fitColumns    | boolean      | 设置为 true，则会自动扩大或缩小列的尺寸以适应网格的宽度并且防止水平滚动。 | false              |
| resizeHandle  | string       | 调整列的位置，可用的值有：'left'、'right'、'both'。当设置为 'right' 时，用户可通过拖拽列头部的右边缘来调整列。 该属性自版本 1.3.2 起可用。 | right              |
| autoRowHeight | boolean      | 定义是否设置基于该行内容的行高度。设置为 false，则可以提高加载性能。 | true               |
| striped       | boolean      | 设置为 true，则把行条纹化。（即奇偶行使用不同背景色）        | false              |
| method        | string       | 请求远程数据的方法（method）类型。                           | post               |
| idField       | string       | 指示哪个字段是标识字段。                                     | null               |
| url           | string       | 从远程站点请求数据的 URL。                                   | null               |
| data          | array,object | 要加载的数据。该属性自版本 1.3.2 起可用。 代码实例：`$('#dg').datagrid({	data: [		{f1:'value11', f2:'value12'},		{f1:'value21', f2:'value22'}	]});` | null               |
| pagination    | boolean      | 设置为 true，则在数据网格（datagrid）底部显示分页工具栏。    | false              |
| rownumbers    | boolean      | 设置为 true，则显示带有行号的列。                            | false              |
| singleSelect  | boolean      | 设置为 true，则只允许选中一行。                              | false              |
| checkOnSelect | boolean      | 如果设置为 true，当用户点击某一行时，则会选中/取消选中复选框。如果设置为 false 时，只有当用户点击了复选框时，才会选中/取消选中复选框。 该属性自版本 1.3 起可用。 |                    |
| pagePosition  | string       | 定义分页栏的位置。可用的值有：'top'、'bottom'、'both'。 该属性自版本 1.3 起可用。 | bottom             |
| pageNumber    | number       | 当设置了 pagination 属性时，初始化页码。                     | 1                  |
| pageSize      | number       | 当设置了 pagination 属性时，初始化页面尺寸。                 | 10                 |
| pageList      | array        | 当设置了 pagination 属性时，初始化页面尺寸的选择列表。       | [10,20,30,40,50]   |
| sortName      | string       | 定义可以排序的列。                                           | null               |
| sortOrder     | string       | 定义列的排序顺序，只能用 'asc' 或 'desc'。                   | asc                |
| showHeader    | boolean      | 定义是否显示行的头部。                                       | true               |
| scrollbarSize | number       | 滚动条宽度（当滚动条是垂直的时候）或者滚动条的高度（当滚动条是水平的时候）。 | 18                 |
| loadFilter    | function     | 返回要显示的过滤数据。该函数有一个参数 'data' ，表示原始数据。您可以把原始数据变成标准数据格式。该函数必须返回标准数据对象，含有 'total' 和 'rows' 属性。 代码实例：`// removing 'd' object from asp.net web service json output$('#dg').datagrid({	loadFilter: function(data){		if (data.d){			return data.d;		} else {			return data;		}	}});` |                    |
| editors       | object       | 定义编辑行时的编辑器。                                       | predefined editors |
| view          | object       | 定义数据网格（datagrid）的视图。                             | default view       |



### 常用的列（column）的属性

> 数据网格（DataGrid） 的列（Column）是一个数组对象，它的每个元素也是一个数组。元素数组的元素是一个配置对象，它定义了每个列的字段。
>
> 常见属性定义如下表

```js
columns:[[
    {field:'itemid',title:'Item ID',rowspan:2,width:80,sortable:true},
    {field:'productid',title:'Product ID',rowspan:2,width:80,sortable:true},
    {title:'Item Details',colspan:4}
],[
    {field:'listprice',title:'List Price',width:80,align:'right',sortable:true},
    {field:'unitcost',title:'Unit Cost',width:80,align:'right',sortable:true},
    {field:'attr1',title:'Attribute',width:100},
    {field:'status',title:'Status',width:60}
]]
```

| 名称      | 类型          | 描述                                                         | 默认值    |
| :-------- | :------------ | :----------------------------------------------------------- | :-------- |
| title     | string        | 列的标题文本。                                               | undefined |
| field     | string        | 列的字段名。                                                 | undefined |
| width     | number        | 列的宽度。如果未定义，则宽度会自动扩展以适应它的内容。没有定义宽度将会降低性能。 | undefined |
| rowspan   | number        | 指示一个单元格占据多少行。                                   | undefined |
| colspan   | number        | 指示一个单元格占据多少列。                                   | undefined |
| align     | string        | 指示如何对齐该列的数据，可以用 'left'、'right'、'center'。   | undefined |
| halign    | string        | 指示如何对齐该列的头部，可能的值：'left'、'right'、'center'。如果没有分配值，则头部对齐方式将与通过 'align' 属性定义的数据对齐方式一致。该属性自版本 1.3.2 起可用。 | undefined |
| sortable  | boolean       | 设置为 true，则允许该列被排序。                              | undefined |
| order     | string        | 默认的排序顺序，只能用 'asc' 或 'desc'。该属性自版本 1.3.2 起可用。 | undefined |
| resizable | boolean       | 设置为 true，则允许该列可调整尺寸。                          | undefined |
| fixed     | boolean       | 设置为 true，则当 'fitColumns' 设置为 true 时放置调整宽度。  | undefined |
| hidden    | boolean       | 设置为 true，则隐藏该列。                                    | undefined |
| checkbox  | boolean       | 设置为 true，则显示复选框。复选框有固定宽度。                | undefined |
| editor    | string,object | 指示编辑类型。当是字符串（string）时则指编辑类型，当是对象（object）时则包含两个属性： type：字符串，编辑类型，可能的类型：text、textarea、checkbox、numberbox、validatebox、datebox、combobox、combotree。 options：对象，编辑类型对应的编辑器选项。 | undefined |



### 编辑器(editor)的属性

> 通过 $.fn.datagrid.defaults.editors 重写默认的 defaults。

编辑器的行为

| 名称     | 参数               | 描述                           |
| :------- | :----------------- | :----------------------------- |
| init     | container, options | 初始化编辑器并且返回目标对象。 |
| destroy  | target             | 如果必要就销毁编辑器。         |
| getValue | target             | 从编辑器的文本获取值。         |
| setValue | target , value     | 给编辑器设置值。               |
| resize   | target , width     | 如果必要就调整编辑器的尺寸。   |

```js
$.extend($.fn.datagrid.defaults.editors, {
    text: {
		init: function(container, options){
			var input = $('<input type="text" class="datagrid-editable-input">').appendTo(container);
			return input;
		},
		destroy: function(target){
			$(target).remove();
		},
		getValue: function(target){
			return $(target).val();
		},
		setValue: function(target, value){
			$(target).val(value);
		},
		resize: function(target, width){
			$(target)._outerWidth(width);
		}
    }
});
```

### 数据网格视图

> 视图（view）是一个对象，它告诉数据网格（datagrid）如何呈现行。

### 事件

> 更多的事件函数看文档

| onLoadSuccess | data                   | 当数据加载成功时触发。                                       |
| ------------- | ---------------------- | ------------------------------------------------------------ |
| onLoadError   | none                   | 加载远程数据发生某些错误时触发。                             |
| onBeforeLoad  | param                  | 发送加载数据的请求前触发，如果返回 false 加载动作就会取消。  |
| onClickRow    | rowIndex, rowData      | 当用户点击一行时触发，参数包括： rowIndex：被点击行的索引，从 0 开始 rowData：被点击行对应的记录 |
| onDblClickRow | rowIndex, rowData      | 当用户双击一行时触发，参数包括： rowIndex：被双击行的索引，从 0 开始 rowData：被双击行对应的记录 |
| onClickCell   | rowIndex, field, value | 当用户单击一个单元格时触发。                                 |
| rowStyler     | index,row              | 后台查出数据后前台界面展示根据其中字段的值改变字体颜色       |

### 方法

| options         | none   | 返回选项（options）对象。                                    |
| --------------- | ------ | ------------------------------------------------------------ |
| getPager        | none   | 返回分页（pager）对象。                                      |
| getEditors      | index  | 获取指定行的编辑器。每个编辑器有下列属性： actions：编辑器能做的动作，与编辑器定义相同。 target：目标编辑器的 jQuery 对象。 field：字段名。 type：编辑器的类型，比如：'text'、'combobox'、'datebox'，等等。 |
| getPanel        | none   | 返回面板（panel）对象。                                      |
| getColumnFields | frozen | 返回列的字段，如果 frozen 设置为 true，则冻结列的字段被返回。 代码实例：`var opts = $('#dg').datagrid('getColumnFields');	// get unfrozen columnsvar opts = $('#dg').datagrid('getColumnFields', true); // get frozen columns` |
| getColumnOption | field  | 返回指定列的选项。                                           |
| resize          | param  | 调整尺寸和布局。                                             |
| checkAll        | none   | 勾选当前页所有的行。该方法自版本 1.3 起可用。                |
| load            | param  | 加载并显示第一页的行，如果指定 'param' 参数，它将替换 queryParams 属性。通常情况下，通过传递一些从参数进行查询，该方法被调用来从服务器加载新数据。`$('#dg').datagrid('load',{	code: '01',	name: 'name01'});` |
| reload          | param  | 重新加载行，就像 load 方法一样，但是保持在当前页。           |
| reloadFooter    | footer | 重新加载底部的行。代码实例：`// update footer row values and then refreshvar rows = $('#dg').datagrid('getFooterRows');rows[0]['name'] = 'new name';rows[0]['salary'] = 60000;$('#dg').datagrid('reloadFooter'); // update footer rows with new data$('#dg').datagrid('reloadFooter',[	{name: 'name1', salary: 60000},	{name: 'name2', salary: 65000}]);` |
| loading         | none   | 显示正在加载状态。                                           |
| beginEdit       | index  | 开始对一行进行编辑。                                         |



## 3.用例



# 2. 分页组件

> 从远程服务器端加载数据，您应该设置 'url' 属性，在您的服务器端应该返回 JSON 格式数据。

```js
<table id="tt" class="easyui-datagrid" style="width:600px;height:250px"
		url="datagrid2_getdata.php"
		title="Load Data" iconCls="icon-save"
		rownumbers="true" pagination="true">
	<thead>
		<tr>
			<th field="itemid" width="80">Item ID</th>
			<th field="productid" width="80">Product ID</th>
			<th field="listprice" width="80" align="right">List Price</th>
			<th field="unitcost" width="80" align="right">Unit Cost</th>
			<th field="attr1" width="150">Attribute</th>
			<th field="status" width="60" align="center">Stauts</th>
		</tr>
	</thead>
</table>
```

> 定义数据网格（datagrid）列，并设置 'pagination' 属性为 true，它将在数据网格（datagrid）的底部生成一个分页（pagination）工具栏。pagination将发送两个参数到服务器：
>
> - page：页码，起始值 1。
> - rows：每页显示行。

```java
// 服务端大概代码
String page = request.getParameter("page");
String size = request.getParameter("pageSize");

Page pageRows = pageDao.ConditionSQL(sql,inputValue,request); // 处理后的结果
....
List<HashMap<String,Object>> resList = pageRows.getList();//    

int totol  = page.getTotolNum();

```







