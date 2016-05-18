
# 使用 Office Web 小部件显示主机网站的信息
在 SharePoint 提供程序承载的加载项中实施 Office Web 小组件控件，以显示主机网站的信息。

 **上次修改时间：** 2015年8月7日

 _ **适用范围：** Office 365?| SharePoint 2013?| SharePoint Online_

 **注释**  名称"SharePoint 相关应用程序"将更改为"SharePoint 外接程序"。在转换期间，某些 SharePoint 产品和 Visual Studio 工具的文档和 UI 仍可能使用术语"SharePoint 相关应用程序"。有关详细信息，请参阅 [Office 和 SharePoint 相关应用程序的新名称](05b07b04-6c8b-4b7e-bd86-e32c589dfead.md#bk_newname)。

您可以使用 Office Web 小组件在提供程序承载的加载项上下文中显示 SharePoint 承载的网站的信息。Office Web 小组件是设计为与 SharePoint 承载的控件具有相似外观和行为的控件集。控件作为 [NuGet](https://www.nuget.org/) 包提供，它可提供 [Office Web 小组件库](http://www.nuget.org/packages/Microsoft.Office.WebWidgets.Experimental/)。

 >**警告**  Office Web 小组件是第三方功能。因为它们具有某些限制，请谨慎使用。有关详细信息，请参阅：

[Core.OfficeWebWidgets](https://github.com/OfficeDev/PnP/tree/dev/Components/Core.OfficeWebWidgets) 示例说明如何实施 Office Web 小组件库中包含的人员选取器和列表视图控件。此示例加载项会将控件插入到起始页的 HTML 中，然后使用位于项目"脚本"文件夹中的 app.js JavaScript 文件配置控件。
此示例起始页显示人员选取器控件。此页面允许您添加多个用户，其中包含两个用于用户输入的文本框。

**图 1. 人员选取器演示起始页**


![人员选取器演示启动页面](media/2d6c1585-9615-45c4-b931-a2e0e7d57b3d.png)Web 项目的 Default.aspx 文件中的以下代码将人员选取器控件放置在  `<div>` 标记内。



```
<div id="peoplePickerBackupSiteOwners" data-office-control="Office.Controls.PeoplePicker" data-office-options=
      '{ "placeholder" : "Please choose one or more backup site owner", 
      "allowMultipleSelections" : true,
      "onChange" : handleSiteOwnerBackupChange}'>
</div>

```

起始页还会显示列表视图控件，该控件可以显示 SharePoint 网站的主机 Web 和加载项 Web 的列表。

**图 2. 列表视图控件起始页**


![列表视图控件启动页](media/c8bc86d4-6cae-4dc0-94a4-97a0e5a49c7d.png)Default.aspx 文件中的以下 HTML 包括两个  `<div>` 标记，用于显示列表视图控件的两个实例。



```
The list view widget can show (currently no inline editing) data from a list on the add-in web:<br />
<div id="listViewAppWeb"></div>
        <br /><br />
        But the same applies for a list on the host web web:<br />
        <div id="listViewHostWeb"></div>

```

app.js 文件中的以下代码会创建列表视图控件的两个实例。



```
 var listViewAppWeb = new Office.Controls.ListView(document.getElementById("listViewAppWeb"),
          {
                     listUrl: appWebUrl + "/_api/web/lists/getbytitle('Announcements')"
           });

var listViewHostWeb = new Office.Controls.ListView(document.getElementById("listViewHostWeb"),
           {
                     listUrl: spHostUrl + "/_api/web/lists/getbytitle('Site Pages')"
           });

```


## 其他资源



- [SharePoint 2013 和 SharePoint Online 中的 UX 组件](ux-components-in-sharepoint-2013-and-sharepoint-online.md)
    
