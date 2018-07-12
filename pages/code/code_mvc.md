---
title: "MVC相關的開發範例"
keywords: 
tags: []
sidebar: code_sidebar
permalink: code_mvc.html
summary: MVC相關的開發範例
---

## 單檔維護
### 單檔維護
#### controller 
的單檔維護作業： (以XXXXXX代表)
```csharp
 public class XXXXController : BaseController
{
        public ActionResult Index()
        {
            return View();
        }

        #region Detail頁處理
        /// <summary>
        /// 詳細畫面
        /// </summary>
        /// <param name="XXXXX"></param>
        /// <returns></returns>
        public ActionResult Detail(string XXXXX)
        {
            MyPageStatus = PageStatus.View;
            var result = new XXXXX();
            //TODO:get detail
            return View("Detail", result);
        }

        /// <summary>新增資料頁面</summary>
        /// <returns></returns>
        public ActionResult Create()
        {
            //設定新增狀態的初始值
            var model = new XXXXX();
            MyPageStatus = PageStatus.Create;
            return View("Detail", model);
        }

        /// <summary>新增資料處理</summary>
        /// <param name="model"></param>
        /// <returns></returns>
        [HttpPost]
        public ActionResult Create(XXXXX model)
        {
            bool result = false;
            //TODO:執行新增
            if (result)
            {
                //執行成功時
                MyPageStatus = PageStatus.View;
                AppendMessage("新增成功", false);
                return RedirectToAction("Detail", new { id = XXXX });
            }
            else
            {
                //執行失敗
                AppendMessage("新增失敗", false);
                return View("Detail", model);
            }
        }

        /// <summary>更新資料頁面</summary>
        /// <param name="XXXXX"></param>
        /// <returns></returns>
        public ActionResult Edit(string XXXXX)
        {
            MyPageStatus = PageStatus.Edit;
            var result = new XXXXX();
            //TODO:get detail
            return View("Detail", result);
        }

        /// <summary>更新資料處理</summary>
        /// <param name="model"></param>
        /// <returns></returns>
        [HttpPost]
        public ActionResult Edit(XXXXX model)
        {

            bool result = false;
            var service = ServiceHelper.Create<IXXXXXService>();
            //TODO:執行更新
            //執行成功時
            if (result)
            {
                MyPageStatus = PageStatus.View;
                AppendMessage("更新成功", true);
                return RedirectToAction("Detail", new { id = XXXXX });
            }
            else
            {
                MyPageStatus = PageStatus.Edit;
                AppendMessage("更新失敗", false);
                return View("Detail", model);
            }
        }

        /// <summary>刪除資料處理</summary>
        /// <param name="XXXX"></param>
        /// <returns></returns>
        [HttpPost]
        public ActionResult Delete(string XXXX)
        {
            var result = false;
            var service = ServiceHelper.Create<IXXXXXService>();
            //TODO:執行刪除
            if (result)
            {
                //執行成功時
                AppendMessage("刪除成功", true);
                return RedirectToAction("Index");
            }
            else
            {
                //執行失敗
                AppendMessage("刪除失敗", true);
                return RedirectToAction("Detail", new { id = XXXXX });
            }
        }
        #endregion
}
```
### 查詢類
#### controller 
(base在 CUF的框架下)

```csharp
[HttpPost]

        /// <summary>
        /// 查詢XXXXXX
        /// </summary>
        /// <param name="condition">查詢條件</param>
        public void QueryXXXXX(XXXXXQueryCondition condition) {

            var service = ServiceHelper.Create<IXXXXXService>();
            var gridList = service.QueryXXXXX(condition);

            var cacheKey = Guid.NewGuid().ToString();
            Cache.Add(cacheKey, gridList);
            SetGridKey("XXXXX_cacheKey", cacheKey);
        }
        /// <summary>
        /// 查詢結果-供jqGrid使用
        /// </summary>
        /// <param name="jqParams"></param>
        /// <returns></returns>
        [HttpPost]
        public JsonResult BindXXXXX(jqGridParam jqParams)
        {
            var cacheKey = GetGridKey("XXXXX_cacheKey");
            return BaseGridBinding<XXXXXGridModel>(jqParams, () => Cache.Get<IEnumerable<XXXXXGridModel>>(cacheKey));
        }
```
#### View:
前端的HTML:Ajax Form Post格式 (use Razor)
```html
@section LeftActions{
    <input type="button" id="btnQuery" class="btn btn-default" value="查詢" onclick="QueryClick()" />
    <input type="button" id="btnQuit" class="btn btn-default" value="結束" onclick="CloseClick()" />

}

@*同一頁的操作則用Ajax.BeginForm*@
@using (Ajax.BeginForm("QueryBizFlowQuery", "FlowPermission", null, new AjaxOptions { OnSuccess = "callGrid" }, new { id = "formQuery" })) {
    <div id="divQueryBlock">
        <div class="row">
        </div>
    </div>
}
<hr />
<div>
    @*會自適應的表格區塊開始*@
    <div id='divResultBlock'>
        <table id='resultgrid'></table>
        <div id='resultpagger'></div>
    </div>
</div>
@section Scripts{
    <script>
        /**
        * 查詢事件
        * @summary .
        * @description .
        */
        function QueryClick() {
            clearMessage();@*清除訊息*@
            $("form#formQuery").trigger("submit");
        }
         /**
        * 關閉事件
        * @summary .
        * @description .
        */
         function CloseClick() {
            //window.close();
        }
         /**
        * bind Grid資訊
        * @summary .
        * @description .
        */
        function callGrid() {
            $("#resultgrid").jqGrid({

            });
        }
    </script>
}
```
### 明細類
#### 按鍵資訊
```html
@section LeftActions{
    @if (MyPageStatus == PageStatus.View) {
        <input id="btnEdit" type="button" class="btn btn-default" value="@EP.PlatformResources.編輯" onclick="EditClick()" />
        <input id="btnDelete" type="button" class="btn btn-default" value="@EP.PlatformResources.刪除" onclick="DeleteClick()" />
    }
    else {
        <input id="btnSave" type="button" class="btn btn-default" value="儲存" onclick="SaveClick()" />
        <input id="btnCancel" type="button" class="btn btn-default" value="取消" onclick="CancelClick()" />
    }
    <input id="btnClose" type="button" class="btn btn-default" value="結束" onclick="CloseClick()" />
}
```
#### Detail頁面
```html
@*執行新增/更新/刪除之動作*@
@using (Html.BeginForm("", "", null, FormMethod.Post, new { id = "formSubmit" })) {
    <div class="DetailContent" id="divModify1" data-bind="multiAccordionWidget: { openActive: [0, 1] }">
        <h3>
            <span class="DetailIcon"></span>
            <a href="#">詳細資料</a>
        </h3>
        <div class="DetailData line">
            <table class="DetailBlock" id="qryCondition">
                <col style="width: 150px" />
                <col />
                <tr>
                    <td class="title">
                        @Html.LabelFor(m => m.SysFlowID):
                    </td>
                    <td>
                        @Html.EditDisplayFor(m => m.SysFlowID, (MyPageStatus == PageStatus.View || MyPageStatus == PageStatus.Edit))
                    </td>
                </tr>
            </table>
        </div>
    </div>
}
```

#### Script動作
```js

@section Scripts{

    <script>
        //頁面載入要做的動作
        $(document).ready(function () {
        });

        @if (MyPageStatus == Microsoft.CUF.Web.PageStatus.View)
        {
            <text>
        //編輯按鍵觸發事件
        function EditClick() {
            clearMessage();@*清除訊息*@
            var sysFlowID = $("input[name=SysFlowID]").val();
            //將頁面改為編輯
            $("form#formChange #sysFlowID").val(sysFlowID);
            $("form#formChange #actionStatus").val("@PageStatus.Edit");
            $("form#formChange").submit();
        }
        //刪除按鍵觸發事件
        function DeleteClick() {
            clearMessage();@*清除訊息*@
            if (confirm("@EP.PlatformResources.確定刪除此項目?")) {
                //用Ajax處理刪除動作
                $("form#formSubmit").attr('action', '@Url.Action("Delete")');
                $("form#formSubmit").submit();
            }
        }
        </text>
        }
        else if (MyPageStatus == Microsoft.CUF.Web.PageStatus.Create)
        {
            <text>
        //儲存按鍵觸發事件
        function SaveClick() {
            clearMessage();@*清除訊息*@
            //處理新增動作
            $("form#formSubmit").attr('action', '@Url.Action("Create", "FlowDefine")');
            $("form#formSubmit").submit();
        }
        //取消按鍵觸發事件
        function CancelClick() {
            window.close();
        }
        </text>
        }
        else if (MyPageStatus == Microsoft.CUF.Web.PageStatus.Edit)
        {
            <text>
        //儲存按鍵觸發事件
        function SaveClick() {
            clearMessage();@*清除訊息*@
            //處理更新動作
            $("form#formSubmit").attr('action', '@Url.Action("Edit", "FlowDefine")');
            $("form#formSubmit").submit();
        }
        //取消按鍵觸發事件
        function CancelClick() {
            clearMessage();@*清除訊息*@
            //將頁面改為檢示
            var sysFlowID = $("input[name=SysFlowID]").val();
            $("form#formChange #sysFlowID").val(sysFlowID);
            $("form#formChange #actionStatus").val("@PageStatus.View");
            $("form#formChange").submit();
        }
        </text>
        }

        // 結束按鍵觸發事件
        function CloseClick() {
            window.close();
        }
    </script>
}

```



## 連動下拉選單

### 處理原則
1. 初始資料源以Razor實作 (call static Function)
2. 連動資料源以Ajax呼叫 Controller function

### 資料源：Static Method (ex:XXXHelper in MVC Web)
於Web端提供XXXHelper函式，資料源為呼叫Service函式，回傳為 IEnumerable SelectListItem 型別

通常會放置於Web Utility下的XXXHelper
``` csharp
        /// <summary>
        /// 依OOO查詢XXX資料
        /// </summary>
        /// <remarks></remarks>
        /// <param name="OOOType">OOO類型</param>
        /// <param name="display">客製選單的呈現文字</param>
        /// <returns>選單使用的資料源</returns>
        public static IEnumerable<SelectListItem> QueryXXXListItemsByOOO(string OOOType, Func<資料Model, string> display = null)
        {
            //note:"請選擇"類的default item 由Razor/js上定義
            var result = new List<SelectListItem>();
            var service = ServiceHelper.Create<IXXXService>();
            var dataList = service.QueryXXXByOOO(oooType);
            display = display ?? ((資料Model m) => m.XXX + "-" + m.YYYName);
            result.AddRange(dataList.Select(m => new SelectListItem { Value = m.XXXX, Text = display(m) }));
            return result;
        }
```

### Controller Action (供Ajax呼叫)
於Controller下定義出Action，此Action再呼叫 Helper相關的函式
(連動Action使用)
``` csharp

        /// <summary>
        /// 依OOO查詢XXX資料
        /// </summary>
        /// <remarks></remarks>
        /// <param name="OOOType">OOO類型</param>
        /// <returns>選單使用的資料源</returns>
        [HttpPost]
        public JsonResult QueryXXXByOOOType(string oooType)
        {
            var result = BizListHelper.QueryXXXListItemsByOOO(oooType);
            return Json(result);
        }
```

### View Razor寫法(Init)
不論是否為連動，都使用Razor的方式寫初始值
``` html
@Html.DropDownListFor(m => m.XXXType, BizListHelper.QueryXXXTypeListItems(),"全部")

//依XXXType連動
@Html.DropDownListFor(m => m.XXXCode, BizListHelper.QueryXXXListItemsByXXXType((Model!=null?Model.XXXType:null)),"全部") 
```


### View Razor寫法(連動)
註冊連動事件
```js
 $(function () {
            //註冊changeEvent
            $("#XXXType").change(function () {
                var xxxType = $("#XXXType").val();
                bindXXXCodeList(xxxType);
            })
        });
```
連動事件的JavaScirpt固定寫法 (會額外定義callbackFunc) 

callbackFunc：當下拉選單bind結束後，再要額外做什麼事(ex:其它連動...)
```js
/**
 * 查詢XXX資料
 * @constructor
 * @param {string} xxxType - 類型.
 * @param {string} callbackFunc - 執行成功時的回呼函式.
 * @return {Point} none.
 * @summary .
 * @description .
 */
function bindXXXCodeList(xxxType, callbackFunc) {
            $("#XXXCode").html('');
            var url = '@Url.Action("QueryXXXByXXXType", "BizCommon",new { area=""})';
            Microsoft.Common.Ajax(url, { insType: insType}, function (data) {
                $.each(data, function (i, item) {
                    $("#XXXCode").append($("<option></option>").attr("value", item.Value).text(item.Text));
                })
                if (callbackFunc != null) { callbackFunc(); }
            });
        }
```