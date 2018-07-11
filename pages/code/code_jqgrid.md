---
title: jqGrid 相關的開發模式
tags: [sample]
keywords: 
last_updated: 
summary: "jqGrid 相關的程式開發應用 "
sidebar: code_sidebar
permalink: code_jqgrid.html
folder: code
---
## 官方資源網站
about jqgrid code 常用的開發架構


## 預設樣式&Event定義
### Default 
#### default jqGrid 定義
```js
//定義jqGrid的預設值及Function Event
$(function () {
    $.extend($.jgrid.defaults, {
        //==========屬性處理================
        multiselect: false,//不多選
        rowNum: 10,
        rowList: [10, 20, 30, 50, 100],
        toppager: false,
        viewrecords: true,
        rownumbers: true,
        autowidth: true,
        altRows: true,
        altclass: 'jqgrow2',
        height: "100%",//高度隨筆數而定
        rownumWidth: 30,//RowNum寬度 default:25 http://goo.gl/y8BqR
        //==========Event處理===============
        //錯誤處理
        loadError: Microsoft.UI.Grid.loadError,
        serializeGridData: Microsoft.UI.Grid.serializeGridData,
        loadComplete: Microsoft.UI.Grid.loadComplete
    });
})
```
#### loadError Event
```js
/*************************************************
擴充：增加 Microsoft.UI.Grid.loadError 函式
針對grid的錯誤做統一的處理
*************************************************/
addMethod(Microsoft.UI.Grid, "loadError", function (xhr, status, error) {
    var msgArray = [];
    var showmessage = xhr.getResponseHeader("AppendMessage");//顯示訊息
    if (showmessage == null) { showmessage = ""; }
    if (showmessage != "") {
        msgArray.push(showmessage);
        if ($.isFunction(window.appendMessage)) {
            appendMessage(msgArray);//加上訊息於表頭
        }
    }
    $(document).ajaxStop($.unblockUI);
});
```
#### serializeGridData Event
```js
/*************************************************
擴充：增加 Microsoft.UI.Grid.serializeGridData 函式
jqGrid Binding 資料之前, 先將 tabUniqueId 加進 postData, 否則後端 catch 會抓不到資料
*************************************************/
addMethod(Microsoft.UI.Grid, "serializeGridData", function (data) {
    var newPostData = $.extend({}, data, {
        tabUniqueId: $('#tabUniqueId').val(),
        exportType: "csv",
        modelName: '',
        dataColNames: '',
        displayColNames: ''
    });
    return newPostData;
});
```
#### loadComplete event
```js
/*************************************************
擴充：增加 Microsoft.UI.Grid.loadComplete 函式
jqGrid Binding 資料完成後, 將後續 jqGrid 功能上須要的資料暫存到 hidden
*************************************************/
addMethod(Microsoft.UI.Grid, "loadComplete", function (data) {
    //處理Timeout的問題
    if (data != null && data.Redirect) {
        window.location.href = data.Redirect;
    }
    var pName = $(this).attr('id') + '_expName';
    if (!$('#' + pName).exists()) {
        $('<input>').attr({
            type: 'hidden',
            id: pName,
            name: pName
        }).appendTo('#hValueBar');
    }
    if (data != null && (typeof (data.exportParam) != 'undefined') && (data.exportParam != null)) {
        $('#' + pName).val(data.exportParam);
    }
    Microsoft.UI.Grid.ResizeGrid();
});
```
## Grid開發
#### Grid操作函式
```js
//清除查詢結果
$('#resultgrid').GridUnload('#resultgrid');
//觸發資料重載 (若grid setting/data有異動時)
$("#resultgrid").trigger("reloadGrid");
```

#### Html樣式
```html
                <div id="divResultBlock" class="QueryGrid">
                    <table id="resultgrid"></table>
                    <div id="resultpagger"></div>
                </div>
```

#### bindGrid()
採post json bind方式
```js
function bindGrid(data) {
            $("#resultgrid").jqGrid({
                url: '@Url.Action("XXX", "XXX", new { area= "XXX" })',
                datatype: "json",
                mtype: "POST",
                colNames: ["","名稱", "操作日期","幣別類","計數類",  "功能"],
                colModel: [
                    { name: "", hidden: true },@*指定它是unique row no*@
                    { name: "XXXName", index: "XXXName", align: 'center' },
                    { name: "XXXTime", index: "XXXTime", align: 'center', formatter: 'date', formatoptions: { newformat: 'Y/m/d' } },                    
                    { name: "XXXRate", index: "XXXRate", align: 'center', formatter: 'currency', formatoptions: { prefix: '', suffix: '%', thousandsSeparator: '', decimalPlaces: 2 } },
                    { name: "XXXCount", index: "XXXCount", align: 'center', formatter: 'currency', formatoptions: { prefix: '', suffix: '', thousandsSeparator: ',' } },
                    { name: 'displayButtons', index: 'displayButtons', align: 'center', sortable: false, formatter: displayButtonsFn }
                ],
                shrinkToFit: true,  @*//默認值為true,若true設置了width值，則每列寬度會根據width成比例縮放*@

                jsonReader: {       @*//讀JSON格式資料*@
                    repeatitems: false,
                    id: "TransactionLogId"
                },
                pager: "resultpagger"
            });
            $("#resultgrid").jqGrid("navGrid", "#resultpagger", { add: false, edit: false, del: false, search: false, refresh: false });
            $("#resultgrid").trigger("reloadGrid");
        }

         @*//功能按鍵*@
        function displayButtonsFun(cellValue, options, rowdata, action) {
            var tabUniqueId = $("#tabUniqueId").val();
            var detail = "<a href=\"@Url.Action("Detail")" + "?id=" + rowdata.Id + "&tabUniqueId=" + tabUniqueId + "\">明細</a>";
             var mydelete = "<a href='#' onclick=\"return DeleteClick('" + rowdata.Id + "')\">刪除</a>";
             var displayButtons = detail +"&nbsp;&nbsp;"+ mydelete;
            return displayButtons;
        }
```
採用datatype:"local"的差異
```js
 function bindGrid(data) {

         $("#resultgrid").jqGrid({
            datatype: "local",
            data:data,
            ......
         });
```