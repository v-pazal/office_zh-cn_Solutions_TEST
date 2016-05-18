
# 使用 SharePoint 提供程序托管的外接程序创建 UX 控件
在 SharePoint 提供程序承载的加载项中创建 UX 控件，这些控件的工作和行为方式与主机 Web 上的 UX 控件类似。 

 **上次修改时间：** 2015年8月12日

 _ **适用范围：** Office 365?| SharePoint 2013?| SharePoint Online_

 **注释**  名称"SharePoint 相关应用程序"将更改为"SharePoint 外接程序"。在转换期间，某些 SharePoint 产品和 Visual Studio 工具的文档和 UI 仍可能使用术语"SharePoint 相关应用程序"。有关详细信息，请参阅 [Office 和 SharePoint 相关应用程序的新名称](05b07b04-6c8b-4b7e-bd86-e32c589dfead.md#bk_newname)。

本文描述了三个示例，向您介绍如何在提供程序承载的加载项中实施 UX 控件：

- [Core.PeoplePicker](https://github.com/OfficeDev/PnP/tree/dev/Components/Core.PeoplePicker) – 向您介绍如何添加人员选取器控件。
    
- [Core.TaxonomyMenu](https://github.com/OfficeDev/PnP/tree/dev/Components/Core.TaxonomyMenu) – 向您介绍如何实施可本地化的分类菜单控件。
    
- [Core.TaxonomyPicker](https://github.com/OfficeDev/PnP/tree/dev/Components/Core.TaxonomyPicker) – 向您介绍如何实施分类选取器控件。
    
这些示例使用 JavaScript 和 JSOM 与 SharePoint 通信，使用 [跨域库](https://msdn.microsoft.com/zh-cn/library/office/fp179927%28v=office.15%29.aspx)处理从加载项到主机网站集的函数调用。

## 人员选取器控件


[Core.PeoplePicker](https://github.com/OfficeDev/PnP/tree/dev/Components/Core.PeoplePicker) 示例向您介绍如何在提供程序承载的加载项中实施人员选取器控件。当用户开始向文本输入框键入名称时，控件将搜索用户配置文件存储中可能的匹配项，并将其显示在 UI 中。加载项将显示可配置且可扩展的人员选取器控件，此控件在远程主机上运行并查询主机网站上与用户输入匹配的用户配置文件存储。


**图 1. 人员选取器控件**

![人员选取器控件](media/ae6e2198-6f63-4ea1-a739-34f64ecd9117.png)
    
 **注释**  此示例的 Visual Studio 2013 解决方案包含一个名为"Dummy"的模块，确保当部署加载项时，它会创建加载项 Web。进行跨域调用需要加载项 Web。

Core.PeoplePickerWeb 项目的"脚本"文件夹包含 app.js 和 peoplepickercontrol.js 文件（以及用于获取其他语言支持的人员选取器资源文件）。app.js 文件使用跨域库获取客户端上下文，并将 Default.aspx 文件中的 HTML 挂接到人员选取器控件。Default.aspx 文件包含  `<div>` 标记，这些标记可实施文本框和人员搜索功能。


    
 **注释**  本文中的代码按原样提供，不提供任何明示或暗示的担保，包括对特定用途适用性、适销性或不侵权的默示担保。




```
<div id="divAdministrators" class="cam-peoplepicker-userlookup ms-fullWidth">
  <span id="spanAdministrators"></span>
<asp:TextBox ID="inputAdministrators" runat="server" CssClass="cam-peoplepicker-edit" Width="70"></asp:TextBox>
</div>
<div id="divAdministratorsSearch" class="cam-peoplepicker-usersearch ms-emphasisBorder"></div>
<asp:HiddenField ID="hdnAdministrators" runat="server" />

```

然后 app.js 文件将创建并配置人员选取器控件。




```
//Make a people picker control.
//1. context = SharePoint Client Context object
//2. $('#spanAdministrators') = SPAN that will 'host' the people picker control
//3. $('#inputAdministrators') = INPUT that will be used to capture user input
//4. $('#divAdministratorsSearch') = DIV that will show the 'drop-down' of the picker
//5. $('#hdnAdministrators') = INPUT hidden control that will host a resolved users
peoplePicker = new CAMControl.PeoplePicker(context, $('#spanAdministrators'), $('#inputAdministrators'), $('#divAdministratorsSearch'), $('#hdnAdministrators'));
// required to pass the variable name here!
peoplePicker.InstanceName = "peoplePicker";
// Hookup everything.
peoplePicker.Initialize();

```

人员选取器控件查询 JSOM 库中的  **ClientPeoplePickerWebServiceInterface** 对象，以开始搜索名称与输入的字符串匹配的用户。




```

if (searchText.length >= parent.GetMinimalCharactersBeforeSearching()) {
                            resultDisplay = 'Searching...';
                            if (typeof resultsSearching != 'undefined') {
                                resultDisplay = resultsSearching;
                            }

                  var searchbusy = parent.Format('<div class=\'ms-emphasisBorder\' style=\'width: 400px; padding: 4px; border-left: none; border-bottom: none; border-right: none; cursor: default;\'>{0}</div>', resultDisplay);
                            parent.PeoplePickerDisplay.html(searchbusy);
                            // Display the suggestion box.
                            parent.ShowSelectionBox();

                   var query = new SP.UI.ApplicationPages.ClientPeoplePickerQueryParameters();
                            query.set_allowMultipleEntities(false);
                            query.set_maximumEntitySuggestions(2000);
                            query.set_principalType(parent.GetPrincipalType());
                            query.set_principalSource(15);
                            query.set_queryString(searchText);
                            var searchResult = SP.UI.ApplicationPages.ClientPeoplePickerWebServiceInterface.clientPeoplePickerSearchUser(parent.SharePointContext, query);

                  // Update the global queryID variable so that you can correlate incoming delegate calls.
                            parent._queryID = parent._queryID + 1;
                            var queryIDToPass = parent._queryID;
                            parent._lastQueryID = queryIDToPass;

                  // Make the SharePoint request.
                            parent.SharePointContext.executeQueryAsync(Function.createDelegate(this, function () { parent.QuerySuccess(queryIDToPass, searchResult); }),
                                                Function.createDelegate(this, function () { parent.QueryFailure(queryIDToPass); }));

```


## 分类菜单控件


[Core.TaxonomyMenu](https://github.com/OfficeDev/PnP/tree/dev/Components/Core.TaxonomyMenu) 示例向您介绍如何实施从提供程序承载的加载项中的术语库填充的可本地化分类菜单控件。加载项还会设置用于填充菜单的所需术语库语言、组、集和术语，并检查用于设置显示语言的用户首选项。

加载项可实施  **TaxonomyHelper** 类 (CSOM)，它将设置术语库并使用术语进行填充。然后它将显示导航链接的 JavaScript 文件上载到网站的根文件夹。

加载项在主机网站上设置术语库。它使用 CSOM 对象和方法创建术语组和术语集，然后使用四个术语填充术语集。 


**图 2. 术语库设置屏幕**

![术语库设置屏幕](media/011ba839-7bc3-4a12-819a-6436deab2e34.png)选择"设置术语库"按钮时，加载项可： 



- 确保所需语言（英语、德语、法语和瑞典语）已在术语库中启用。
    
- 创建术语组和术语集并使用四个新术语填充术语集。
    
 **TaxonomyHelper** 类中的以下代码会验证所需语言是否已启用，如果没有，它将进行启用。




```

var languages = new int[] { 1031, 1033, 1036, 1053 };
            Array.ForEach(languages, l => { 
                if (!termStore.Languages.Contains(l)) 
                    termStore.AddLanguage(l); 
            });

            termStore.CommitAll();
            clientContext.ExecuteQuery();

// Create the term group.
termGroup = termStore.CreateGroup("Taxonomy Navigation", groupId);
                clientContext.Load(termGroup);
                clientContext.ExecuteQuery();

```

最后，相同  **TaxonomyHelper** 类中的以下代码会创建每个新术语以及德语、法语和瑞典语的标签。它还会为 **_Sys_Nav_SimpleLinkUrl** 属性设置一个值，这相当于术语库管理工具中的 **Simple Link or Header** 属性。在这种情况下，每个术语的 URL 会指回到根网站。




```

var term = termSet.CreateTerm(termName, 1033, Guid.NewGuid());
term.CreateLabel(termNameGerman, 1031, false);
term.CreateLabel(termNameFrench, 1036, false);
term.CreateLabel(termNameSwedish, 1053, false);
term.SetLocalCustomProperty("_Sys_Nav_SimpleLinkUrl", clientContext.Web.ServerRelativeUrl);

```

接下来，加载项会将 topnav.js 文件加载到主机网站的根文件夹。此文件包含 JavaScript，它会将链接从此术语集插入到主机网站主页的导航中。加载项 UI 还会显示加载项上载 JavaScript 文件后，导航链接在主机网站上的显示方式。

topnav.js 文件中的以下代码使用 JSOM 来检查用户的首选语言。




```

var targetUser = "i:0#.f|membership|" + _spPageContextInfo.userLoginName;
        context = new SP.ClientContext.get_current();
var peopleManager = new SP.UserProfiles.PeopleManager(context);
var userProperty = peopleManager.getUserProfilePropertyFor(targetUser, "SPS-MUILanguages");

```

然后加载项将确定用户的语言首选项是否与已启用的语言之一匹配。如果找到匹配项，以下代码将获取用户首选语言的术语和相关标签。




```

while (termEnumerator.moveNext()) {
    var currentTerm = termEnumerator.get_current();
    var label = currentTerm.getDefaultLabel(lcid);

    termItems.push(currentTerm);
    termLabels.push(label);
    context.load(currentTerm);

```

最后，topnav.js 文件中的以下代码会将包含术语的链接插入到主机网站的顶级导航元素。




```

html += "<ul style='margin-top: 0px; margin-bottom: 0px;'>"
        for (var i in termItems) {
            var term = termItems[i];
            var termLabel = termLabels[i];
            var linkName = termLabel.get_value() != 0 ? termLabel.get_value() : term.get_name();
            var linkUrl = term.get_localCustomProperties()['_Sys_Nav_SimpleLinkUrl'];

            html += "<li style='display: inline;list-style-type: none; padding-right: 20px;'><a href='" + linkUrl + "'>" + linkName + "</a></li>";
        }
        html += "</ul>";

        $('#DeltaTopNavigation').html(html);
        SP.UI.Notify.removeNotification(nid);

```


## 分类选取器控件


[Core.TaxonomyPicker](https://github.com/OfficeDev/PnP/tree/dev/Components/Core.TaxonomyPicker) 示例说明如何在提供程序承载的加载项中实施分类选取器控件。当用户开始向文本输入框键入术语时，控件将搜索术语库中可能的匹配项，并将其显示在输入框下的列表中。

加载项创建符合 JSOM 分类选取器要求的 HTML 页面，然后添加并配置加载项。它使用 JSOM 库查询主机网站的术语库。分类选取器与 SharePoint Managed Metadata Service 通信，这需要分类权限范围的写入权限，从而可以从关闭的术语集中读取并写入到打开的术语集。确保 AppManifest.xml 文件已在合适的范围设置写入权限。

[Core.TaxonomyPicker](https://github.com/OfficeDev/PnP/tree/dev/Components/Core.TaxonomyPicker) 项目的"脚本"文件夹包含 app.js 和 taxonomypickercontrol.js 文件（以及用于获取其他语言支持的分类选取器资源文件）。app.js 文件使用跨域库获取客户端上下文，并将 Default.aspx 文件中的 HTML 挂接到分类选取器控件。Default.aspx 文件包含隐藏字段，这些字段可实施文本框和分类选取器功能。它还会添加一个带标点符号的列表，以显示从术语库返回的建议。




```

<div style="left: 50%; width: 600px; margin-left: -300px; position: absolute;">
            <table>
                <tr>
                    <td class="ms-formlabel" valign="top"><h3 class="ms-standardheader">Keywords Termset:</h3></td>
                    <td class="ms-formbody" valign="top">
                        <div class="ms-core-form-line" style="margin-bottom: 0px;">
                            <asp:HiddenField runat="server" id="taxPickerKeywords" />
                        </div>
                    </td>
                </tr>
            </table>

            <asp:Button runat="server" OnClick="SubmitButton_Click" Text="Submit" />

            <asp:BulletedList runat="server" ID="SelectedValues" DataTextField="Label" />
</div>

```

然后 app.js 文件将创建并配置分类选取器控件。




```
// Load scripts for calling taxonomy APIs.
                    $.getScript(layoutsRoot + 'init.js',
                        function () {
                            $.getScript(layoutsRoot + 'sp.taxonomy.js',
                                function () {
                                    // Bind the taxonomy picker to the default keywords term set.
                                    $('#taxPickerKeywords').taxpicker({ isMulti: true, allowFillIn: true, useKeywords: true }, context);
                                });
                        });

```

分类选取器控件使用下面的代码打开 JSOM 中的  **TaxonomySession** 示例，以加载术语库中的所有术语。




```
// Get the taxonomy session by using CSOM.
            var taxSession = SP.Taxonomy.TaxonomySession.getTaxonomySession(spContext);
            //Use the default term store...this could be extended here to support additional term stores.
            var termStore = taxSession.getDefaultSiteCollectionTermStore();

            // Get the term set based on the properties of the term set.
            if (this.Id != null)
                this.RawTermSet = termStore.getTermSet(this.Id); // Get term set by ID.
            else if (this.UseHashtags)
                this.RawTermSet = termStore.get_hashTagsTermSet(); // Get the hashtags term set.
            else if (this.UseKeywords)
                this.RawTermSet = termStore.get_keywordsTermSet(); // Get the keywords term set.

            // Get all terms for the term set and organize them in the async callback.
            this.RawTerms = this.RawTermSet.getAllTerms();
            spContext.load(this.RawTermSet);
            spContext.load(this.RawTerms);
            spContext.executeQueryAsync(Function.createDelegate(this, this.termsLoadedSuccess), Function.createDelegate(this, this.termsLoadedFailed));

```

然后分类选取器控件从加载的术语中查找可能的匹配项，并根据需要将新术语添加到术语库。


## 其他资源



- [SharePoint 2013 和 SharePoint Online 中的 UX 组件](ux-components-in-sharepoint-2013-and-sharepoint-online.md)
    
- [使用跨域库从外接程序访问 SharePoint 2013 数据](http://msdn.microsoft.com/library/bc37ff5c-1285-40af-98ae-01286696242d%28Office.15%29.aspx)
    
