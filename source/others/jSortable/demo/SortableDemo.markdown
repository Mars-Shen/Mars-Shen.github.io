---
layout: plug_demo_page
date: 2014-07-01 12:00:00
author: "Start Bootstrap"
---
<div id="sortable">
</div>
 <script>
	  $(function () {
			var mockData = {
				sortJsonData : [{
						primaryKey:1524,
						index:1,
						key : "COD1",
						isActiveFlag : false,
						value : "item 1"
					}, {
						primaryKey:1525,
						index:3,
						key : "COD2",
						isActiveFlag : true,
						value : "item 2"
					}, {
						primaryKey:1526,
						index:5,
						key : "COD3",
						isActiveFlag : false,
						value : "item 3"
					}, {
						primaryKey:1527,
						index:2,
						key : "COD4",
						isActiveFlag : true,
						value : "item 4"
					},
					{
						primaryKey:1528,
						index:6,
						key : "COD5",
						isActiveFlag : true,
						value : "item 5"
					},
					{
						primaryKey:1529,
						index:7,
						key : "COD6",
						isActiveFlag : false,
						value : "item 6"
					}, {
						primaryKey:1530,
						index:8,
						key : "COD7",
						isActiveFlag : true,
						value : "item 7"
					}, {
						primaryKey:1531,
						index:4,
						key : "COD8",
						isActiveFlag : true,
						value : "item 8"
					}, {
						primaryKey:1532,
						index:9,
						key : "COD9",
						isActiveFlag : false,
						value : "item 9"
					},
					{
						primaryKey:1533,
						index:10,
						key : "COD10",
						isActiveFlag : true,
						value : "item 10"
					}
				]
			};
			var sortObj = $("#sortable").jSortable(mockData);
			//set submit callback function
			sortObj.jSortable("SubmitCallback", function () {
				var str = "";
				var obj = sortObj.jSortable("ModelData");
				$.each(obj.sortJsonData, function (i, v) {
					str += "#" +v.index+ ";ID:" + v.id + ";status:" + v.isActiveFlag + ";(key: "+ v.key +", value: " + v.value + ").\n ";
				});
				alert(str);
			});
			sortObj.jSortable("DeleteCallBack", function (selectItems) {
				return true;
			});
		});
  </script>