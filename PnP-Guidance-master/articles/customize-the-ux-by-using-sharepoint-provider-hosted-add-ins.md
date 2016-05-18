
# 使用 SharePoint 提供程序托管的外接程序自定义用户体验
使用提供程序托管的外接程序远程自定义 SharePoint UX 组件。 

 **上次修改时间：** 2015年8月12日

 _ **适用范围：** Office 365?| SharePoint 2013?| SharePoint Online_

 **注释**  名称"SharePoint 相关应用程序"将更改为"SharePoint 外接程序"。在转换期间，某些 SharePoint 产品和 Visual Studio 工具的文档和 UI 仍可能使用术语"SharePoint 相关应用程序"。有关详细信息，请参阅 [Office 和 SharePoint 相关应用程序的新名称](05b07b04-6c8b-4b7e-bd86-e32c589dfead.md#bk_newname)。

本文介绍演示自定义 SharePoint UX 组件的最佳做法的示例，包括以下方案：

- 页面操作（添加和修改 Wiki 网页）。
    
- 显示模式对话框中的外接程序和数据。
    
- 创建个性化的 UI 元素。
    
- 客户端呈现（部署自定义 SharePoint 列表中字段呈现的 JSLink 文件）。
    
- Web 部件和外接程序部件的操作（远程设置和运行提供程序托管的外接程序中的外接程序脚本部件）。
    
- 数据聚合和缓存（使用 HTML5 本地存储和 HTTP cookie 来减少对 SharePoint 的服务调用次数）。
    

## 页面操作


[Core.ModifyPages](https://github.com/OfficeDev/PnP/tree/dev/Samples/Core.ModifyPages) 示例包含两个页面操作方案：


- 创建 Wiki 网页。
    
- 修改 Wiki 网页的布局。 
    
本示例使用默认的网站页面库和现有的全新布局。您还可以对其进行更新，以使用自定义 Wiki 网页库和自定义布局。外接程序 UI 包含两个按钮，可创建 Wiki 网页和两个用于查看您创建的 Wiki 网页的链接。


**图1. 页面操作的起始页示例**

![页面操作示例的启动页面](media/8c6353d9-b339-4563-b945-eaea3d4da2a8.png)第一个方案的示例代码将确定是否已创建 Wiki 网页。如果未创建，则将文件添加到网站页面库并返回其 URL。


    
 **注释**  本文中的代码按原样提供，不提供任何明示或暗示的担保，包括对特定用途适用性、适销性或不侵权的默示担保。




```
var newpage = pageLibrary.RootFolder.Files.AddTemplateFile(newWikiPageUrl, TemplateFileType.WikiPage);
ctx.Load(newpage);
ctx.ExecuteQuery();
wikiPageUrl = String.Format("sitepages/{0}", wikiPageName);

```

在这两种方案中，该示例使用名为  **LabHelper** 的帮助程序类中的 **AddHtmlToWikiPage** 方法添加通过起始页上的文本框输入的 HTML。此方法从 Wiki 网页的 _WikiField_ 字段内部的窗体中插入 HTML。




```
public void AddHtmlToWikiPage(ClientContext ctx, Web web, string folder, string html, string page)
        {
            Microsoft.SharePoint.Client.Folder pagesLib = web.GetFolderByServerRelativeUrl(folder);
            ctx.Load(pagesLib.Files);
            ctx.ExecuteQuery();

            Microsoft.SharePoint.Client.File wikiPage = null;

            foreach (Microsoft.SharePoint.Client.File aspxFile in pagesLib.Files)
            {
                if (aspxFile.Name.Equals(page, StringComparison.InvariantCultureIgnoreCase))
                {
                    wikiPage = aspxFile;
                    break;
                }
            }

            if (wikiPage == null)
            {
                return;
            }

            ctx.Load(wikiPage);
            ctx.Load(wikiPage.ListItemAllFields);
            ctx.ExecuteQuery();

            string wikiField = (string)wikiPage.ListItemAllFields["WikiField"];

            Microsoft.SharePoint.Client.ListItem listItem = wikiPage.ListItemAllFields;
            listItem["WikiField"] = html;
            listItem.Update();
            ctx.ExecuteQuery();
        }

```

第二种方案的示例代码创建一个新的  **WebPartEntity** 实例。然后使用帮助程序类中的方法使用 XML 填充 Web 部件。XML 显示升级链接列表，包括 [http://www.bing.com](http://www.bing.com) 和 [OfficeDev/PnP GitHub](https://github.com/OfficeDev/PnP) 存储库的主页。




```
WebPartEntity wp2 = new WebPartEntity();
wp2.WebPartXml = new LabHelper().WpPromotedLinks(linksID, string.Format("{0}/Lists/{1}", 
                                                                Request.QueryString["SPHostUrl"], "Links"), 
                                                                string.Format("{0}/{1}", Request.QueryString["SPHostUrl"], 
                                                                scenario2PageUrl), "$Resources:core,linksList");
wp2.WebPartIndex = 1;
wp2.WebPartTitle = "Links";

new LabHelper().AddWebPartToWikiPage(ctx, ctx.Web, "SitePages", wp2, scenario2Page, 2, 1, false);
new LabHelper().AddHtmlToWikiPage(ctx, ctx.Web, "SitePages", htmlEntry.Text, scenario2Page, 2, 2);

this.hplPage2.NavigateUrl = string.Format("{0}/{1}", Request.QueryString["SPHostUrl"], scenario2PageUrl);

```

帮助程序代码将通过  **XsltListViewWebPart** Web 部件中的表显示升级链接。


**图 2. 包含 XsltListViewWebPart 部件和升级链接表的第二个 Wiki 网页**

![包含 XsltListViewWeb 部件和升级链接表的第二个 Wiki 网页](media/6dc60a0b-b11a-4fe9-ad06-6b4d0d4b8b24.png) **LabHelper** 实例上的 **WpPromotedLinks** 对象包含可定义将插入 Wiki 网页中的 Web 部件外观的 XML。然后， **AddWebPartToWikiPage** 方法将插入 Wiki 网页上新 `div` 标记内部新定义的 Web 部件。




```
XmlDocument xd = new XmlDocument();
            xd.PreserveWhitespace = true;
            xd.LoadXml(wikiField);

            // Sometimes the wikifield content seems to be surrounded by an additional div. 
            XmlElement layoutsTable = xd.SelectSingleNode("div/div/table") as XmlElement;
            if (layoutsTable == null)
            {
                layoutsTable = xd.SelectSingleNode("div/table") as XmlElement;
            }

            XmlElement layoutsZoneInner = layoutsTable.SelectSingleNode(string.Format("tbody/tr[{0}]/td[{1}]/div/div", row, col)) as XmlElement;
            // - space element
            XmlElement space = xd.CreateElement("p");
            XmlText text = xd.CreateTextNode(" ");
            space.AppendChild(text);

            // - wpBoxDiv
            XmlElement wpBoxDiv = xd.CreateElement("div");
            layoutsZoneInner.AppendChild(wpBoxDiv);

            if (addSpace)
            {
                layoutsZoneInner.AppendChild(space);
            }

            XmlAttribute attribute = xd.CreateAttribute("class");
            wpBoxDiv.Attributes.Append(attribute);
            attribute.Value = "ms-rtestate-read ms-rte-wpbox";
            attribute = xd.CreateAttribute("contentEditable");
            wpBoxDiv.Attributes.Append(attribute);
            attribute.Value = "false";
            // - div1
            XmlElement div1 = xd.CreateElement("div");
            wpBoxDiv.AppendChild(div1);
            div1.IsEmpty = false;
            attribute = xd.CreateAttribute("class");
            div1.Attributes.Append(attribute);
            attribute.Value = "ms-rtestate-read " + wpdNew.Id.ToString("D");
            attribute = xd.CreateAttribute("id");
            div1.Attributes.Append(attribute);
            attribute.Value = "div_" + wpdNew.Id.ToString("D");
            // - div2
            XmlElement div2 = xd.CreateElement("div");
            wpBoxDiv.AppendChild(div2);
            div2.IsEmpty = false;
            attribute = xd.CreateAttribute("style");
            div2.Attributes.Append(attribute);
            attribute.Value = "display:none";
            attribute = xd.CreateAttribute("id");
            div2.Attributes.Append(attribute);
            attribute.Value = "vid_" + wpdNew.Id.ToString("D");

            ListItem listItem = webPartPage.ListItemAllFields;
            listItem["WikiField"] = xd.OuterXml;
            listItem.Update();
            ctx.ExecuteQuery();

```


## 显示模式对话框中的外接程序和数据


[Core.Dialog](https://github.com/OfficeDev/PnP/tree/dev/Scenarios/Core.Dialog) 示例显示两种方法，用来嵌入模式对话框链接。这些链接向 SharePoint 主机网站显示提供程序托管的外接程序页面。外接程序使用客户端对象模型 (CSOM) 来创建自定义操作和 JavaScript，以启动并将信息显示在对话框中。由于其中某些信息来自主机网站，外接程序还会使用 JavaScript 对象模型 (JSOM) 从主机网站中检索信息。由于外接程序运行在不同的域中，而不是运行在 SharePoint 主机网站中，因此它还使用 SharePoint 跨域库对主机网站进行调用。


 **注释**  有关此方案中使用跨域库的详细信息，请参阅 [使用跨域库从外接程序访问 SharePoint 2013 数据](http://msdn.microsoft.com/library/bc37ff5c-1285-40af-98ae-01286696242d%28Office.15%29.aspx)。

起始页是显示在对话框中的页面。若要处理给定显示上下文（对话框或整个页面）中的任何显示差异，外接程序需确定其是否将显示在对话框中。为此，可使用随启动对话框的链接一同传递的查询字符串参数。




```
private string SetIsDlg(string isDlgValue)
        {
            var urlParams = HttpUtility.ParseQueryString(Request.QueryString.ToString());
            urlParams.Set("IsDlg", isDlgValue);
            return string.Format("{0}://{1}:{2}{3}?{4}", Request.Url.Scheme, Request.Url.Host, Request.Url.Port, Request.Url.AbsolutePath, urlParams.ToString());
        }

```

例如，可以选择显示特定的 UI 元素（如按钮）甚至是不同的页面布局，具体取决于内容是否要在对话框中显示。

起始页 UI 为创建指向对话框的链接提供了两个选项，以及一个包含主机 web 上所有列表的列表。还提供了"确定"和"取消"按钮，您可以在对话框上下文中使用这些按钮来关闭对话框和/或提示外接程序中的其他操作。

当您选择"添加菜单项"按钮时，外接程序会创建启动对话框的  **CustomActionEntity** 对象。然后，将使用名为 **AddCustomAction** 的 [OfficeDevPnP 核心扩展](https://github.com/OfficeDev/PnP/tree/dev/OfficeDevPnP.Core)方法将新的自定义操作添加到"网站设置"菜单。




```
StringBuilder modelDialogScript = new StringBuilder(10);
modelDialogScript.Append("javascript:var dlg=SP.UI.ModalDialog.showModalDialog({url: '");
modelDialogScript.Append(String.Format("{0}", SetIsDlg("1")));
modelDialogScript.Append("', dialogReturnValueCallback:function(res, val) {} });");       

// Create a custom action.
CustomActionEntity customAction = new CustomActionEntity()
{
  Title = "Office AMS Dialog sample",                
  Description = "Shows how to start an add-in inside a dialog box.",
  Location = "Microsoft.SharePoint.StandardMenu",
  Group = "SiteActions",
  Sequence = 10000,
  Url = modelDialogScript.ToString(),
};

// Add the custom action to the site.
cc.Web.AddCustomAction(customAction);

```

 **AddCustomAction** 方法将自定义操作添加到与 SharePoint 网站相关联的 **UserCustomActions** 集合。




```
var newAction = existingActions.Add();
            newAction.Description = customAction.Description;
            newAction.Location = customAction.Location;
            if (customAction.Location == JavaScriptExtensions.SCRIPT_LOCATION)
            {
                newAction.ScriptBlock = customAction.ScriptBlock;
            }
            else
            {
                newAction.Sequence = customAction.Sequence;
                newAction.Url = customAction.Url;
                newAction.Group = customAction.Group;
                newAction.Title = customAction.Title;
                newAction.ImageUrl = customAction.ImageUrl;
                if (customAction.Rights != null)
                {
                    newAction.Rights = customAction.Rights;
                }
            }
            newAction.Update();
            web.Context.Load(web, w => w.UserCustomActions);
            web.Context.ExecuteQuery();

```

当您选择"使用脚本编辑器 web 部件添加页面"按钮时，外接程序将使用  **AddWikiPage** 和 **AddWebPartToWikiPage** 方法在网站页面库中创建 Wiki 网页，并将已配置的脚本编辑器 Web 部件添加到该页面。




```
string scenario1Page = String.Format("scenario1-{0}.aspx", DateTime.Now.Ticks);
string scenario1PageUrl = cc.Web.AddWikiPage("Site Pages", scenario1Page);
cc.Web.AddLayoutToWikiPage("SitePages", WikiPageLayout.OneColumn, scenario1Page);
WebPartEntity scriptEditorWp = new WebPartEntity();
scriptEditorWp.WebPartXml = ScriptEditorWebPart();
scriptEditorWp.WebPartIndex = 1;
scriptEditorWp.WebPartTitle = "Script editor test"; 
cc.Web.AddWebPartToWikiPage("SitePages", scriptEditorWp, scenario1Page, 1, 1, false);

```

 **AddWikiPage** 方法加载网站页面库。如果由外接程序 [OfficeDevPnP Core](https://github.com/OfficeDev/PnP/tree/dev/OfficeDevPnP.Core ) 指定的 Wiki 页面尚不存在，将创建该页面。




```
public static string AddWikiPage(this Web web, string wikiPageLibraryName, string wikiPageName)
        {
            string wikiPageUrl = "";


            var pageLibrary = web.Lists.GetByTitle(wikiPageLibraryName);

            web.Context.Load(pageLibrary.RootFolder, f => f.ServerRelativeUrl);
            web.Context.ExecuteQuery();

            var pageLibraryUrl = pageLibrary.RootFolder.ServerRelativeUrl;
            var newWikiPageUrl = pageLibraryUrl + "/" + wikiPageName;

            var currentPageFile = web.GetFileByServerRelativeUrl(newWikiPageUrl);

            web.Context.Load(currentPageFile, f => f.Exists);
            web.Context.ExecuteQuery();

            if (!currentPageFile.Exists)
            {
                var newpage = pageLibrary.RootFolder.Files.AddTemplateFile(newWikiPageUrl, TemplateFileType.WikiPage);

                web.Context.Load(newpage);
                web.Context.ExecuteQuery();

                wikiPageUrl = UrlUtility.Combine("sitepages", wikiPageName);
            }

            return wikiPageUrl;
        }

```

 **AddWebPartToWikiPage** 方法在 Wiki 网页上将新定义的 Web 部件插入新的 `<div>` 标记。然后使用 JSOM 和跨域库检索用来填充主机 web 上各 SharePoint 列表所组成的列表的信息。




```
function printAllListNamesFromHostWeb() {
        var context;
        var factory;
        var appContextSite;
        var collList;

        context = new SP.ClientContext(appWebUrl);
        factory = new SP.ProxyWebRequestExecutorFactory(appWebUrl);
        context.set_webRequestExecutorFactory(factory);
        appContextSite = new SP.AppContextSite(context, spHostUrl);

        this.web = appContextSite.get_web();
        collList = this.web.get_lists();
        context.load(collList);

        context.executeQueryAsync(
            Function.createDelegate(this, successHandler),
            Function.createDelegate(this, errorHandler)
        );

```


## 个性化的 UI 元素


[Branding.UIElementPersonalization](https://github.com/OfficeDev/PnP/tree/dev/Samples/Branding.UIElementPersonalization) 示例演示如何使用嵌入式 JavaScript 和用户配置文件中存储的值以及 SharePoint 列表在主机 web 上个性化设置 UI 元素。它还使用 HTML5 本地存储将对主机网站的调用次数减至最少。

示例的起始页将提示您将三个字符串（XX、YY 或 ZZ）中的一个添加到用户配置文件页面的"关于我"部分。 

外接程序已部署三个图像和一个包含每个图像的标题和 URL 的 SharePoint 列表，以及将每个图像绑定到其中一个字符串的其他字段。选择"插入自定义项"按钮之后，外接程序会将 personalize.js 文件嵌入到用户自定义操作集合中。




```
public void AddPersonalizeJsLink(ClientContext ctx, Web web)
        {
            string scenarioUrl = String.Format("{0}://{1}:{2}/Scripts", this.Request.Url.Scheme,
                                                this.Request.Url.DnsSafeHost, this.Request.Url.Port);
            string revision = Guid.NewGuid().ToString().Replace("-", "");
            string personalizeJsLink = string.Format("{0}/{1}?rev={2}", scenarioUrl, "personalize.js", revision);

            StringBuilder scripts = new StringBuilder(@"
                var headID = document.getElementsByTagName('head')[0]; 
                var");

            scripts.AppendFormat(@"
                newScript = document.createElement('script');
                newScript.type = 'text/javascript';
                newScript.src = '{0}';
                headID.appendChild(newScript);", personalizeJsLink);
            string scriptBlock = scripts.ToString();

            var existingActions = web.UserCustomActions;
            ctx.Load(existingActions);
            ctx.ExecuteQuery();

            var actions = existingActions.ToArray();
            foreach (var action in actions)
            {
                if (action.Description == "personalize" &amp;&amp;
                    action.Location == "ScriptLink")
                {
                    action.DeleteObject();
                    ctx.ExecuteQuery();
                }
            }

            var newAction = existingActions.Add();
            newAction.Description = "personalize";
            newAction.Location = "ScriptLink";
            
            newAction.ScriptBlock = scriptBlock;
            newAction.Update();
            ctx.Load(web, s => s.UserCustomActions);
            ctx.ExecuteQuery();
        }

```

默认情况下，由于 SharePoint 团队网站使用[最少下载策略 (MDS)](http://msdn.microsoft.com/zh-cn/library/office/dn456544%28v=office.15%29.aspx)，因此，personalize.js 文件中的代码将首先尝试使用 MDS 自行注册。如此一来，当您加载包含 JavaScript 的页面时，MDS 引擎将启动主函数 ( **RemoteManager_Inject**)。如果禁用 MDS，则此函数将立即启动。




```
// Register script for MDS, if possible.
RegisterModuleInit("personalize.js", RemoteManager_Inject); //MDS registration
RemoteManager_Inject(); //non-MDS run

if (typeof (Sys) != "undefined" &amp;&amp; Boolean(Sys) &amp;&amp; Boolean(Sys.Application)) {
    Sys.Application.notifyScriptLoaded();h
}

if (typeof (NotifyScriptLoadedAndExecuteWaitingJobs) == "function") {
    NotifyScriptLoadedAndExecuteWaitingJobs("scenario1.js");
}
// The RemoteManager_Inject function is the entry point for loading the other scripts that perform the customizations. When a given script depends on another script, be sure to load the dependent script after the one on which it depends. This sample loads the JQuery library before the personalizeIt function that uses JQuery.
function RemoteManager_Inject() {

    var jQuery = "https://ajax.aspnetcdn.com/ajax/jQuery/jquery-2.0.2.min.js";

    // Load jQuery. 
    loadScript(jQuery, function () {

        personalizeIt();

    });
}

```

 **personalizeIt()** 函数首先检查 HTML5 本地存储，然后查找用户配置文件信息。如果转到用户配置文件信息，将存储在 HTML5 本地存储中检索的信息。




```
function personalizeIt() {
    clientContext = SP.ClientContext.get_current();

    var fileref = document.createElement('script');
    fileref.setAttribute("type", "text/javascript");
    fileref.setAttribute("src", "/_layouts/15/SP.UserProfiles.js");
    document.getElementsByTagName("head")[0].appendChild(fileref);

    SP.SOD.executeOrDelayUntilScriptLoaded(function () {        

        // Get localstorage values if they exist.
        buCode = localStorage.getItem("bucode");
        buCodeTimeStamp = localStorage.getItem("buCodeTimeStamp");

        // Determine whether the page already has embedded personalized image.
        var pageTitle = $('#pageTitle')[0].innerHTML;
        if (pageTitle.indexOf("img") > -1) {
            personalized = true;
        }
        else {
            personalized = false;
        }        

        // If nothing is in localstorage, get profile data, which will also populate localstorage.
        if (buCode == "" || buCode == null) {
            getProfileData(clientContext);
            personalized = false;
        }
        else {
            // Check for expiration.            
            if (isKeyExpired("buCodeTimeStamp")) {                
                getProfileData(clientContext);

                if (buCode != "" || buCode != null) {
                    // Set timestamp for expiration.
                    currentTime = Math.floor((new Date().getTime()) / 1000);
                    localStorage.setItem("buCodeTimeStamp", currentTime);

                    // Set personalized to false so that the code can check for a new image in case buCode was updated.
                    personalized = false;
                }
            }            
        }

        // Load image or make sure it is current based on the value in AboutMe.
        if (!personalized) {
            loadPersonalizedImage(buCode);
        }


    }, 'SP.UserProfiles.js');
}

```

还可以通过检查 Personalize.js 文件中包含的代码来确定本地存储项是否已过期。这并未内置在 HTML5 本地存储中。




```
// Check to see if the key has expired
function isKeyExpired(TimeStampKey) {

    // Retrieve the example setting for expiration in seconds.
    var expiryStamp = localStorage.getItem(TimeStampKey);

    if (expiryStamp != null &amp;&amp; cacheTimeout != null) {

        // Retrieve the timestamp and compare against specified cache timeout settings to see if it is expired.
        var currentTime = Math.floor((new Date().getTime()) / 1000);

        if (currentTime - parseInt(expiryStamp) > parseInt(cacheTimeout)) {
            return true; //Expired
        }
        else {
            return false;
        }
    }
    else {
        //default 
        return true;
    }
}

```


## 客户端呈现


[Branding.ClientSideRendering](https://github.com/OfficeDev/PnP/tree/dev/Samples/Branding.ClientSideRendering) 示例演示如何使用提供程序托管的外接程序来远程设置 SharePoint 项目和 JSLink 文件，以便能够使用客户端呈现自定义 SharePoint 列表字段的外观和行为。JSLink 文件和客户端呈现使您能够控制控件在 SharePoint 页面（列表视图、列表字段、添加和编辑窗体）上的呈现方式。此控件可以减少或消除对自定义字段类型的需求。客户端呈现可以实现远程控制列表字段的外观。

此示例将[客户端呈现 (JSLink) 代码示例](http://code.msdn.microsoft.com/office/Client-side-rendering-JS-2ed3538a)中的 JSLink 示例与设置 JSLink 文件的提供程序托管的单个 SharePoint 外接程序相结合。客户端呈现使您能够使用标准 web 技术（如 HTML 和 JavaScript）定义自定义和预定义字段类型的呈现逻辑。

开始使用示例时，起始页会提示您提供所有示例。


**图 3. 客户端呈现起始页示例**

![客户端呈现示例的启动页](media/bfa1b472-976b-4bdc-95df-537cee5570e6.png)当您选择"提供示例"时，外接程序将部署图像以及每个示例中使用的所有 SharePoint 列表、列表视图、列表项、窗体和 JavaScript 文件。外接程序会在样式库中创建一个名为 JSLink-Samples 的文件夹，然后将 JavaScript 文件上传到该文件夹中。 **UploadFileToFolder** 方法在每个 JavaScript 文件中执行上载和检查操作。




```
public static void UploadFileToFolder(Web web, string filePath, Folder folder)
        {
            using (FileStream fs = new FileStream(filePath, FileMode.Open))
            {
                FileCreationInformation flciNewFile = new FileCreationInformation();

                flciNewFile.ContentStream = fs;
                flciNewFile.Url = System.IO.Path.GetFileName(filePath);
                flciNewFile.Overwrite = true;

                Microsoft.SharePoint.Client.File uploadFile = folder.Files.Add(flciNewFile);
                uploadFile.CheckIn("CSR sample js file", CheckinType.MajorCheckIn);

                folder.Context.Load(uploadFile);
                folder.Context.ExecuteQuery();
            }
        }

```


### 示例 1：将格式设置应用到列表栏

示例 1 演示如何根据字段值将格式设置应用到列表栏。优先级字段值 1（高）、2（标准）、3（低）分别以红色、橙色和黄色来显示。


**图 4. 自定义列表字段的显示**

![自定义列表字段的显示](media/1481adfa-efc9-4b59-b07f-bb330f02d2bb.png)以下 JavaScript 将替代默认字段显示，并创建"优先级"列表字段的新显示模板。匿名函数中加载有关您想要替代其显示内容的上下文信息的技术在所有示例中均有使用。




```
(function () {

    // Create object that has the context information about the field that you want to render differently.
    var priorityFiledContext = {};
    priorityFiledContext.Templates = {};
    priorityFiledContext.Templates.Fields = {
        // Apply the new rendering for Priority field in List View.
        "Priority": { "View": priorityFiledTemplate }
    };

    SPClientTemplates.TemplateManager.RegisterTemplateOverrides(priorityFiledContext);

})();

// This function provides the rendering logic for list view.
function priorityFiledTemplate(ctx) {

    var priority = ctx.CurrentItem[ctx.CurrentFieldSchema.Name];

    // Return HTML element with appropriate color based on the Priority column’s value.
    switch (priority) {
        case "(1) High":
            return "<span style='color :#f00'>" + priority + "</span>";
            break;
        case "(2) Normal":
            return "<span style='color :#ff6a00'>" + priority + "</span>";
            break;
        case "(3) Low":
            return "<span style='color :#cab023'>" + priority + "</span>";
    }
}

```


### 示例 2：缩短长文本

示例 2 演示如何截断"公告"列表的  **Body** 字段中存储的长文本。完整的文本将通过您每次将鼠标指针悬停在某个列表项上时出现的弹出窗口来显示，如图 8 所示。


**图 5. 显示弹出窗口的截断的列表字段显示**

![截断的列表域屏幕显示弹出窗口](media/a5e6d2af-f1f9-4bcc-9278-e56fd42c2e64.png)以下 JavaScript 缩短了  **Body** 字段文本，并通过 **span** 标记上的 **title** 属性使完整的文本显示为弹出窗口。




```
function bodyFiledTemplate(ctx) {

    var bodyValue = ctx.CurrentItem[ctx.CurrentFieldSchema.Name];

    // This regex expression is used to delete HTML tags from the Body field.
    var regex = /(<([^>]+)>)/ig;

    bodyValue = bodyValue.replace(regex, "");

    var newBodyValue = bodyValue;

    if (bodyValue &amp;&amp; bodyValue.length >= 100)
    {
        newBodyValue = bodyValue.substring(0, 100) + " ...";
    }

    return "<span title='" + bodyValue + "'>" + newBodyValue + "</span>";

}

```


### 示例 3：显示图像和文档名称

示例 3 演示如何在文档库中的某个文档名称旁边显示图像。每次将  **Confidential** 字段值设置为 **Yes** 时，将出现红色提示标记，如图 6 所示。


**图 6. 文档名称旁边的图像显示**

![文档名称旁边的图像显示](media/f2768dfd-ae05-4afc-8dbe-26dc4e7dca6d.png)以下 JavaScript 检查  **Confidential** 字段值，然后根据另一个字段值自定义 **Name** 字段显示。此示例使用您选择"提供示例"时所上载的图像。




```
function linkFilenameFiledTemplate(ctx) {

    var confidential = ctx.CurrentItem["Confidential"];
    var title = ctx.CurrentItem["FileLeafRef"];

    // This Regex expression use to delete extension (.docx, .pdf ...) form the file name.
    title = title.replace(/\.[^/.]+$/, "")

    // Check confidential field value.
    if (confidential &amp;&amp; confidential.toLowerCase() == 'yes') {
        // Render HTML that contains the file name and the confidential icon
        return title + "&amp;nbsp;<img src= '" + _spPageContextInfo.siteServerRelativeUrl + "/Style%20Library/JSLink-Samples/imgs/Confidential.png' alt='Confidential Document' title='Confidential Document'/>";
    }
    else
    {
        return title;
    }
}

```


### 示例 4：显示条形图

示例 4 演示如何在任务列表的"完成百分比"字段中显示条形图。条形图的外观取决于"完成百分比"字段的值，如图 10 所示。请注意，条形图还会显示在用于创建和编辑任务列表项目的窗体中。

以下代码将创建条形图显示，并将其与视图和显示窗体 ( **percentCompleteViewFiledTemplate**) 相关联，然后与新建和编辑窗体 ( **percentCompleteEditFiledTemplate**) 相关联。




```
// This function provides the rendering logic for View and Display forms.
function percentCompleteViewFiledTemplate(ctx) {

    var percentComplete = ctx.CurrentItem[ctx.CurrentFieldSchema.Name];
    return "<div style='background-color: #e5e5e5; width: 100px;  display:inline-block;'> \
            <div style='width: " + percentComplete.replace(/\s+/g, '') + "; background-color: #0094ff;'> \
            &amp;nbsp;</div></div>&amp;nbsp;" + percentComplete;

}

// This function provides the rendering logic for New and Edit forms.
function percentCompleteEditFiledTemplate(ctx) {

    var formCtx = SPClientTemplates.Utility.GetFormContextForCurrentField(ctx);

    // Register a callback just before submit.
    formCtx.registerGetValueCallback(formCtx.fieldName, function () {
        return document.getElementById('inpPercentComplete').value;
    });

    return "<input type='range' id='inpPercentComplete' name='inpPercentComplete' min='0' max='100' \
            oninput='outPercentComplete.value=inpPercentComplete.value' value='" + formCtx.fieldValue + "' /> \
            <output name='outPercentComplete' for='inpPercentComplete' >" + formCtx.fieldValue + "</output>%";
}

```


**图 7. 完成百分比字段中显示的条形图**

![完成百分比域中显示的条形图](media/fb36f27a-2eb7-4c5f-8428-b85b65369ea9.png)


### 示例 5：更改呈现模板

示例 5 演示了如何更改列表视图的呈现模板。此视图显示了在对其进行选择时，可以像可折叠项一样展开的列表项标题。展开的视图显示其他列表项字段，如图 8 所示。


**图 8. 折叠和展开的列表项视图**

![折叠和展开的列表项目视图](media/fd185eef-e2be-4523-9adb-d524b84ecc26.png)以下代码将设置模板，并使用列表模板对其进行注册。它将设置整个布局，然后使用  **OnPostRender** 事件处理程序来注册呈现列表时将执行的 JavaScript 函数。此函数将事件与 CSS 和事件处理相关联以实现折叠功能。




```
(function () {

    // jQuery library is required in this sample.
    // Fallback to loading jQuery from a CDN path if the local is unavailable
    (window.jQuery || document.write('<script src="//ajax.aspnetcdn.com/ajax/jquery/jquery-1.10.0.min.js"><\/script>'));

    // Create object that has the context information about the field that you want to render differently.
    var accordionContext = {};
    accordionContext.Templates = {};

    // Be careful when adding the header for the template, because it will break the default list view render.
    accordionContext.Templates.Header = "<div class='accordion'>";
    accordionContext.Templates.Footer = "</div>";

    // Add OnPostRender event handler to add accordion click events and style.
    accordionContext.OnPostRender = accordionOnPostRender;

    // This line of code tells the TemplateManager that you want to change all the HTML for item row rendering.
    accordionContext.Templates.Item = accordionTemplate;

    SPClientTemplates.TemplateManager.RegisterTemplateOverrides(accordionContext);

})();

// This function provides the rendering logic.
function accordionTemplate(ctx) {
    var title = ctx.CurrentItem["Title"];
    var description = ctx.CurrentItem["Description"];

    // Return whole item html
    return "<h2>" + title + "</h2><p>" + description + "</p><br/>";
}

function accordionOnPostRender() {

    // Register event to collapse and expand when selecting accordion header.
    $('.accordion h2').click(function () {
        $(this).next().slideToggle();
    }).next().hide();

    $('.accordion h2').css('cursor', 'pointer');
}

```


### 示例 6：验证字段值

 示例 6 演示如何使用正则表达式来验证用户提供的字段值。当用户向"电子邮件"字段文本框中键入无效的电子邮件地址时，将显示红色错误消息。当用户创建或编辑列表项时会发生这种情况，如图 9 所示。


**图 9. 无效字段文本输入的错误消息**

![无效字段文本输入的错误消息](media/d0d1faf1-aa03-4df8-a1a6-db86fff1f463.png)以下代码使用占位符设置模板以显示错误消息，并注册用户尝试提交窗体时触发的回调函数。第一次回调返回"电子邮件"栏的值，第二次回调使用正则表达式来验证字符串值。




```
function emailFiledTemplate(ctx) {

    var formCtx = SPClientTemplates.Utility.GetFormContextForCurrentField(ctx);

    // Register a callback just before submit.
    formCtx.registerGetValueCallback(formCtx.fieldName, function () {
        return document.getElementById('inpEmail').value;
    });

    // Create container for various validations.
    var validators = new SPClientForms.ClientValidation.ValidatorSet();
    validators.RegisterValidator(new emailValidator());

    // Validation failure handler.
    formCtx.registerValidationErrorCallback(formCtx.fieldName, emailOnError);

    formCtx.registerClientValidator(formCtx.fieldName, validators);

    return "<span dir='none'><input type='text' value='" + formCtx.fieldValue + "'  maxlength='255' id='inpEmail' class='ms-long'> \
            <br><span id='spnError' class='ms-formvalidation ms-csrformvalidation'></span></span>";
}

// Custom validation object to validate email format.
emailValidator = function () {
    emailValidator.prototype.Validate = function (value) {
        var isError = false;
        var errorMessage = "";

        //Email format Regex expression
        var emailRejex = /\S+@\S+\.\S+/;

        if (!emailRejex.test(value) &amp;&amp; value.trim()) {
            isError = true;
            errorMessage = "Invalid email address";
        }

        // Send error message to error callback function (emailOnError).
        return new SPClientForms.ClientValidation.ValidationResult(isError, errorMessage);
    };
};

// Add error message to spnError element under the input field element.
function emailOnError(error) {
    document.getElementById("spnError").innerHTML = "<span role='alert'>" + error.errorMessage + "</span>";
}

```


### 示例 7：将列表项编辑字段设置为只读

示例 7 演示如何将列表项编辑窗体字段设置为只读。只读字段中不显示编辑控件，如图 10 所示。


**图 10. 自定义列表编辑窗体中的只读字段**

![自定义列表编辑表单中的只读字段](media/de441289-8276-4e7c-8530-d08192e29000.png)以下代码示例将修改列表项编辑窗体中的"Title"、"AssignedTo"和"Priority"字段，使其仅显示字段值而不显示编辑控件。该代码演示如何处理不同字段类型的分析要求。




```
function readonlyFieldTemplate(ctx) {

    // Reuse SharePoint JavaScript libraries.
    switch (ctx.CurrentFieldSchema.FieldType) {
        case "Text":
        case "Number":
        case "Integer":
        case "Currency":
        case "Choice":
        case "Computed":
            return SPField_FormDisplay_Default(ctx);

        case "MultiChoice":
            prepareMultiChoiceFieldValue(ctx);
            return SPField_FormDisplay_Default(ctx);

        case "Boolean":
            return SPField_FormDisplay_DefaultNoEncode(ctx);

        case "Note":
            prepareNoteFieldValue(ctx);
            return SPFieldNote_Display(ctx);

        case "File":
            return SPFieldFile_Display(ctx);

        case "Lookup":
        case "LookupMulti":
                return SPFieldLookup_Display(ctx);           

        case "URL":
            return RenderFieldValueDefault(ctx);

        case "User":
            prepareUserFieldValue(ctx);
            return SPFieldUser_Display(ctx);

        case "UserMulti":
            prepareUserFieldValue(ctx);
            return SPFieldUserMulti_Display(ctx);

        case "DateTime":
            return SPFieldDateTime_Display(ctx);

        case "Attachments":
            return SPFieldAttachments_Default(ctx);

        case "TaxonomyFieldType":
            //Re-use JavaScript from the sp.ui.taxonomy.js SharePoint JavaScript library
            return SP.UI.Taxonomy.TaxonomyFieldTemplate.renderDisplayControl(ctx);
    }
}

// User control needs specific formatted value to render content correctly.
function prepareUserFieldValue(ctx) {
    var item = ctx['CurrentItem'];
    var userField = item[ctx.CurrentFieldSchema.Name];
    var fieldValue = "";

    for (var i = 0; i < userField.length; i++) {
        fieldValue += userField[i].EntityData.SPUserID + SPClientTemplates.Utility.UserLookupDelimitString + userField[i].DisplayText;

        if ((i + 1) != userField.length) {
            fieldValue += SPClientTemplates.Utility.UserLookupDelimitString
        }
    }

    ctx["CurrentFieldValue"] = fieldValue;
}

// Choice control needs specific formatted value to render content correctly.
function prepareMultiChoiceFieldValue(ctx) {

    if (ctx["CurrentFieldValue"]) {
        var fieldValue = ctx["CurrentFieldValue"];

        var find = ';#';
        var regExpObj = new RegExp(find, 'g');

        fieldValue = fieldValue.replace(regExpObj, '; ');
        fieldValue = fieldValue.replace(/^; /g, '');
        fieldValue = fieldValue.replace(/; $/g, '');

        ctx["CurrentFieldValue"] = fieldValue;
    }
}

// Note control needs specific formatted value to render content correctly.
function prepareNoteFieldValue(ctx) {

    if (ctx["CurrentFieldValue"]) {
        var fieldValue = ctx["CurrentFieldValue"];
        fieldValue = "<div>" + fieldValue.replace(/\n/g, '<br />'); + "</div>";

        ctx ["CurrentFieldValue"] = fieldValue;
    }
} 

```


### 示例 8：隐藏字段

示例 8 演示如何隐藏列表项的新建和编辑窗体中的字段。当用户创建或编辑任务列表项目时，此示例将隐藏"Predecessors"字段。

此示例将为名为  **CSR-Hide-Controls 列表**的列表部署编辑和新建窗体。有关在部署示例后如何查看窗体的信息，请参阅 [Branding.ClientSideRendering](https://github.com/OfficeDev/PnP/tree/dev/Samples/Branding.ClientSideRendering)。

以下代码在窗体的 HTML 中查找"Predecessors"字段并隐藏该字段。该字段仍存在于 HTML 中，但用户在浏览器中无法看到它。




```
(function () {

    // jQuery library is required in this sample.
    // Fallback to loading jQuery from a CDN path if the local is unavailable.
    (window.jQuery || document.write('<script src="//ajax.aspnetcdn.com/ajax/jquery/jquery-1.10.0.min.js"><\/script>'));

    // Create object that has the context information about the field that we want to render differently.
    var hiddenFiledContext = {};
    hiddenFiledContext.Templates = {}; 
    hiddenFiledContext.Templates.OnPostRender = hiddenFiledOnPreRender;
    hiddenFiledContext.Templates.Fields = {
        // Apply the new rendering for Predecessors field in New and Edit forms.
        "Predecessors": {
            "NewForm": hiddenFiledTemplate,
            "EditForm": hiddenFiledTemplate
        }
    };

    SPClientTemplates.TemplateManager.RegisterTemplateOverrides(hiddenFiledContext);

})();


// This function provides the rendering logic.
function hiddenFiledTemplate() {
    return "<span class='csrHiddenField'></span>";
}

// This function provides the rendering logic.
function hiddenFiledOnPreRender(ctx) {
    jQuery(".csrHiddenField").closest("tr").hide();
}

```


## Web 部件和外接程序部件操作


[Core.AppScriptPart](https://github.com/OfficeDev/PnP/tree/dev/Samples/Core.AppScriptPart) 示例演示如何使用外接程序脚本部件嵌入在 SharePoint 页面的提供程序托管的外接程序中运行的脚本。此示例演示如何通过部署外接程序脚本部件并将其从 web 部件库添加到 SharePoint 页面，来修改主机网站上的页面 UI。

外接程序脚本部件类似于 web 部件，您可以将其从 web 部件库添加到 SharePoint 页面中，但在这种情况下，.webpart 文件需嵌入一个远程运行在提供程序托管的外接程序中的 JavaScript 文件。外接程序脚本部件在 SharePoint 页面的  `<div>` 标记内部运行，因此，与使用运行在 Iframe 中的外接程序相比，外接程序脚本部件能够提供更具响应性的设计和体验。

起始页包括一个"运行方案"按钮，可将外接程序脚本部件部署到 web 部件库。以下代码示例构造了一个  **FileCreationInformationObject** 实例，其中包含.webpart 文件的内容，然后将新文件上载到 web 部件库中。请注意，当外接程序部件安装或作为网站集设置过程的一部分时，您还可以自动运行此代码。




```
var spContext = SharePointContextProvider.Current.GetSharePointContext(Context);
using (var clientContext = spContext.CreateUserClientContextForSPHost())
{
    var folder = clientContext.Web.Lists.GetByTitle("Web Part Gallery").RootFolder;
    clientContext.Load(folder);
    clientContext.ExecuteQuery();

    // Upload the OneDrive for Business Usage Guidelines.docx.
    using (var stream = System.IO.File.OpenRead(Server.MapPath("~/userprofileinformation.webpart")))
    {
        FileCreationInformation fileInfo = new FileCreationInformation();
        fileInfo.ContentStream = stream;
        fileInfo.Overwrite = true;
        fileInfo.Url = "userprofileinformation.webpart";
        File file = folder.Files.Add(fileInfo);
        clientContext.ExecuteQuery();
    }

    // Update the group for uplaoded web part.
    var list = clientContext.Web.Lists.GetByTitle("Web Part Gallery");
    CamlQuery camlQuery = CamlQuery.CreateAllItemsQuery(100);
    Microsoft.SharePoint.Client.ListItemCollection items = list.GetItems(camlQuery);
    clientContext.Load(items);
    clientContext.ExecuteQuery();
    foreach (var item in items)
    {
        // Random group name to differentiate it from the rest.
        if (item["FileLeafRef"].ToString().ToLowerInvariant() == "userprofileinformation.webpart")
        {
            item["Group"] = "add-in Script Part";
            item.Update();
            clientContext.ExecuteQuery();
        }
    }

    lblStatus.Text = string.Format("add-in script part has been added to web part gallery. You can find 'User Profile Information' script part under 'Add-in Script Part' group in the <a href='{0}'>host web</a>.", spContext.SPHostUrl.ToString());
}

```

完成此步骤后，您可以在 web 部件库的"外接程序脚本部件"新类别中找到"用户配置文件信息"外接程序脚本部件。将外接程序脚本部件添加到页面后，远程运行的 JavaScript 可控制页面上显示的信息。

当您在编辑模式下查看外接程序脚本部件时，可看到它嵌入远程运行的 JavaScript 文件。userprofileinformation.js 脚本将使用 JSOM 从主机网站获取用户配置文件信息。 




```
function sharePointReady() {
  clientContext = SP.ClientContext.get_current();

  var fileref = document.createElement('script');
  fileref.setAttribute("type", "text/javascript");
  fileref.setAttribute("src", "/_layouts/15/SP.UserProfiles.js");
  document.getElementsByTagName("head")[0].appendChild(fileref);

  SP.SOD.executeOrDelayUntilScriptLoaded(function () {

    //Get Instance of People Manager Class       
    var peopleManager = new SP.UserProfiles.PeopleManager(clientContext);

    //Get properties of the current user
    userProfileProperties = peopleManager.getMyProperties();
    clientContext.load(userProfileProperties);
    clientContext.executeQueryAsync(Function.createDelegate(this, function (sender, args) {
      var firstname = userProfileProperties.get_userProfileProperties()['FirstName'];
      var name = userProfileProperties.get_userProfileProperties()['PreferredName'];
      var title = userProfileProperties.get_userProfileProperties()['Title'];
      var aboutMe = userProfileProperties.get_userProfileProperties()['AboutMe'];
      var picture = userProfileProperties.get_userProfileProperties()['PictureURL'];

      var html = "<div><h2>Welcome " + firstname + "</h2></div><div><div style='float: left; margin-left:10px'><img style='float:left;margin-right:10px' src='" + picture + "' /><b>Name</b>: " + name + "<br /><b>Title</b>: " + title + "<br />" + aboutMe + "</div></div>";

      document.getElementById('UserProfileAboutMe').innerHTML = html;
    }), Function.createDelegate(this, function (sender, args) {
      console.log('The following error has occurred while loading user profile property: ' + args.get_message());
    }));
  }, 'SP.UserProfiles.js');
}

```


## 设置发布功能


[Provisioning.PublishingFeatures](https://github.com/OfficeDev/PnP/tree/dev/Scenarios/Provisioning.PublishingFeatures) 示例演示如何执行托管在 Office 365 上的发布网站的常见任务；例如，设置和使用页面布局、母版页和主题，或将 JavaScript 嵌入到页面布局中。该示例还演示如何应用筛选器控制可供子网站使用的网站模板和主机 web 上提供的页面布局。

提供程序托管的外接程序使用 CSOM 设置发布网站上常用的 UI 元素，并在可部署到发布网站的页面布局中使用 JavaScript 创建更多动态体验。该示例还演示了使用母版页和使用发布网站中的主题之间的差异。


 **重要信息**  若要让此示例中的功能正常工作，您需要激活网站上的发布功能。有关详细信息，请参阅 [启用发布功能](https://support.office.com/zh-cn/article/Enable-publishing-features-479677a6-8b33-4ac7-907d-071c1c7e4518?CorrelationId=22291615-2acd-46be-8813-9e6c48d01a32&amp;ui=en-US&amp;rs=en-US&amp;ad=US)。

示例起始页将为您提供用于自定义发布网站的 UI 的三种方案： 


- 部署页面布局。
    
- 部署母版页和主题。
    
- 筛选主机网站上的可用页面布局和网站模板。
    

### 方案 1：部署页面

方案 1 演示了如何部署自定义页面布局。图 11 中所示的"部署页面布局"按钮将创建新的页面布局和使用该布局的页面。


**图 11. 部署页面布局的按钮**

![部署页面布局的按钮](media/141b3b83-9a74-4528-825a-efd94183526d.png)您可以转到主机网站上新创建的演示页面来查看新页面，其中包含嵌入的 JavaScript 并显示用户配置文件信息。

此示例在页面上显示此用户的信息。它还添加了一个页面，新页面是使用  **PublishingPageInformation** 对象创建的。

此示例通过将文件上载到母版页样式库并对其分配页面布局内容类型，实现添加新的页面布局。以下代码使用 *.aspx 文件（您可以将其部署为 Visual Studio 2013 项目中的资源），并将其添加为母版页样式库中的页面布局。




```
// Get the path to the file that you are about to deploy.
            List masterPageGallery = web.GetCatalog((int)ListTemplateType.MasterPageCatalog);
            Folder rootFolder = masterPageGallery.RootFolder;
            web.Context.Load(masterPageGallery);
            web.Context.Load(rootFolder);
            web.Context.ExecuteQuery();

            var fileBytes = System.IO.File.ReadAllBytes(sourceFilePath);

            // Use CSOM to upload the file.
            FileCreationInformation newFile = new FileCreationInformation();
            newFile.Content = fileBytes;
            newFile.Url = UrlUtility.Combine(rootFolder.ServerRelativeUrl, fileName);
            newFile.Overwrite = true;

            Microsoft.SharePoint.Client.File uploadFile = rootFolder.Files.Add(newFile);
            web.Context.Load(uploadFile);
            web.Context.ExecuteQuery();

            // Check out the file if needed.
            if (masterPageGallery.ForceCheckout || masterPageGallery.EnableVersioning)
            {
                if (uploadFile.CheckOutType == CheckOutType.None)
                {
                    uploadFile.CheckOut();
                }
            }

            // Get content type for ID to assign associated content type information.
            ContentType associatedCt = web.GetContentTypeById(associatedContentTypeID);

            var listItem = uploadFile.ListItemAllFields;
            listItem["Title"] = title;
            listItem["MasterPageDescription"] = description;
            // Set the item as page layout.
            listItem["ContentTypeId"] = Constants.PAGE_LAYOUT_CONTENT_TYPE;
            // Set the associated content type ID property
            listItem["PublishingAssociatedContentType"] = string.Format(";#{0};#{1};#", associatedCt.Name, associatedCt.Id);
            listItem["UIVersion"] = Convert.ToString(15);
            listItem.Update();

            // Check in the page layout if needed.
            if (masterPageGallery.ForceCheckout || masterPageGallery.EnableVersioning)
            {
                uploadFile.CheckIn(string.Empty, CheckinType.MajorCheckIn);
                listItem.File.Publish(string.Empty);
            }
            web.Context.ExecuteQuery();

```

您可以前往主机网站的"页面"库，验证您的新页面是否正在使用新的页面布局。


### 方案 2：部署母版页和主题

方案 2 演示如何通过提供程序托管的外接程序为主机网站部署和设置母版页和主题。当您在示例起始页上选择"部署母版页并使用它"时，示例将为主机网站部署并应用自定义母版页。您可以转到网站的主页查看新的母版页。

此示例通过将 *.master 文件上载到母版页样式库并对其分配母版页内容类型，实现添加新的母版页。以下代码使用 *.master 文件（您可以将其部署为 Visual Studio 2013 项目中的资源），并将其添加为母版页样式库中的母版页。




```
string fileName = Path.GetFileName(sourceFilePath);

            // Get the path to the file that you are about to deploy.
            List masterPageGallery = web.GetCatalog((int)ListTemplateType.MasterPageCatalog);
            Folder rootFolder = masterPageGallery.RootFolder;
            web.Context.Load(masterPageGallery);
            web.Context.Load(rootFolder);
            web.Context.ExecuteQuery();

            // Get the file name from the provided path.
            var fileBytes = System.IO.File.ReadAllBytes(sourceFilePath);

            // Use CSOM to upload the file.
            FileCreationInformation newFile = new FileCreationInformation();
            newFile.Content = fileBytes;
            newFile.Url = UrlUtility.Combine(rootFolder.ServerRelativeUrl, fileName);
            newFile.Overwrite = true;

            Microsoft.SharePoint.Client.File uploadFile = rootFolder.Files.Add(newFile);
            web.Context.Load(uploadFile);
            web.Context.ExecuteQuery();


            var listItem = uploadFile.ListItemAllFields;
            if (masterPageGallery.ForceCheckout || masterPageGallery.EnableVersioning)
            {
                if (uploadFile.CheckOutType == CheckOutType.None)
                {
                    uploadFile.CheckOut();
                }
            }

            listItem["Title"] = title;
            listItem["MasterPageDescription"] = description;
            // Set content type as master page.
            listItem["ContentTypeId"] = Constants.MASTERPAGE_CONTENT_TYPE;
            listItem["UIVersion"] = uiVersion;
            listItem.Update();
            if (masterPageGallery.ForceCheckout || masterPageGallery.EnableVersioning)
            {
                uploadFile.CheckIn(string.Empty, CheckinType.MajorCheckIn);
                listItem.File.Publish(string.Empty);
            }
            web.Context.Load(listItem);
            web.Context.ExecuteQuery();

```

下一步将新母版页的 URL 设置为表示网站的  **Web** 对象的 **MasterUrl** 和 **CustomMasterUrl** 属性的值。此示例处理此操作的方法如下：提取母版页样式库中新母版页的 URL，并将该值分配给 **Web.MasterUrl** 和 **Web.CustomMasterUrl** 属性。




```
// Assign master page to the host web.
                clientContext.Web.SetMasterPagesForSiteByName("contoso.master", "contoso.master");

```

当您选择"部署主题并使用它"时，该示例将为主机网站部署并应用自定义主题。此示例通过使用这些值（您可以将这些值部署为您的 Visual Studio 2013 项目中的资源）将新主题添加到主题库，来设置调色板、背景图像和主题的字体方案。以下代码将创建新主题。




```
List themesOverviewList = web.GetCatalog((int)ListTemplateType.DesignCatalog);
           web.Context.Load(themesOverviewList);
           web.Context.ExecuteQuery(); 
                ListItemCreationInformation itemInfo = new ListItemCreationInformation();
                Microsoft.SharePoint.Client.ListItem item = themesOverviewList.AddItem(itemInfo);
                item["Name"] = themeName;
                item["Title"] = themeName;
                if (!string.IsNullOrEmpty(colorFileName))
                {
                    item["ThemeUrl"] = UrlUtility.Combine(rootWeb.ServerRelativeUrl, string.Format(Constants.THEMES_DIRECTORY, Path.GetFileName(colorFileName)));
                }
                if (!string.IsNullOrEmpty(fontFileName))
                {
                    item["FontSchemeUrl"] = UrlUtility.Combine(rootWeb.ServerRelativeUrl, string.Format(Constants.THEMES_DIRECTORY, Path.GetFileName(fontFileName)));
                }
                if (!string.IsNullOrEmpty(backgroundName))
                {
                    item["ImageUrl"] = UrlUtility.Combine(rootWeb.ServerRelativeUrl, string.Format(Constants.THEMES_DIRECTORY, Path.GetFileName(backgroundName)));
                }
                item["DisplayOrder"] = 11;
                item.Update();
                web.Context.ExecuteQuery();

               

```

下一步将此新主题设置为网站的主题。以下代码完成此操作的方法为：从主题库提取主题，然后将它的值应用到主机网站。




```
 CamlQuery query = new CamlQuery();
                // Find the theme by themeName.
                string camlString = string.Format(CAML_QUERY_FIND_BY_FILENAME, themeName);
                query.ViewXml = camlString;
                var found = themeList.GetItems(query);
                rootWeb.Context.Load(found);
                LoggingUtility.Internal.TraceVerbose("Getting theme: {0}", themeName);
                rootWeb.Context.ExecuteQuery();
                if (found.Count > 0)
                {
                    ListItem themeEntry = found[0];

                    / /Set the properties for applying the custom theme that was just uploaded.
                    string spColorURL = null;
                    if (themeEntry["ThemeUrl"] != null &amp;&amp; themeEntry["ThemeUrl"].ToString().Length > 0)
                    {
                        spColorURL = UrlUtility.MakeRelativeUrl((themeEntry["ThemeUrl"] as FieldUrlValue).Url);
                    }
                    string spFontURL = null;
                    if (themeEntry["FontSchemeUrl"] != null &amp;&amp; themeEntry["FontSchemeUrl"].ToString().Length > 0)
                    {
                        spFontURL = UrlUtility.MakeRelativeUrl((themeEntry["FontSchemeUrl"] as FieldUrlValue).Url);
                    }
                    string backGroundImage = null;
                    if (themeEntry["ImageUrl"] != null &amp;&amp; themeEntry["ImageUrl"].ToString().Length > 0)
                    {
                        backGroundImage = UrlUtility.MakeRelativeUrl((themeEntry["ImageUrl"] as FieldUrlValue).Url);
                    }

                    // Set theme for demonstration.
                    // TODO: Why is shareGenerated false? If deploying to root an inheriting, then maybe use shareGenerated = true.
                    web.ApplyTheme(spColorURL,
                                        spFontURL,
                                        backGroundImage,
                                        false);
                    web.Context.ExecuteQuery();
```


### 方案 3：筛选可用页面布局和网站模板

方案 3 演示当用户将模版应用到新网站、将布局应用到新页面时，如何限制为用户提供的选项。当您在示例起始页上选择"将筛选器应用到主机 web"时，该示例将自定义页面布局设置为默认项，将另一个页面布局设置为用户创建的任何新页面中可使用的仅有的另一个选项。当用户创建新的子网站时，此示例还减少为用户提供的可用选项的数量。图 12 显示网站模板选择框在应用筛选器之前和之后所呈现的外观。


**图 12. 应用示例筛选器之前和之后的网站模板选择**

![在应用示例筛选器之前和之后的网站模板选择](media/46c0e39d-dfec-45ed-9b72-ff3f6d5e1916.png)此示例通过将关联的 *.aspx 文件传递到扩展方法，来设置默认的和可用的页面布局，如代码中所示。




```
                List<string> pageLayouts = new List<string>();
                pageLayouts.Add("ContosoLinksBelow.aspx");
                pageLayouts.Add("ContosoLinksRight.aspx");
                clientContext.Web.SetAvailablePageLayouts(clientContext.Web, pageLayouts);

                // Set default page layout for the site
                clientContext.Web.SetDefaultPageLayoutForSite(clientContext.Web, "ContosoLinksBelow.aspx");

```

此示例通过执行类似操作来设置可用网站模板。在这种情况下，它将定义每个网站模板的  **WebTemplateEntity** 实例传递到名为 **SetAvailableWebTemplates** 的扩展方法。




```
List<WebTemplateEntity> templates = new List<WebTemplateEntity>();
                templates.Add(new WebTemplateEntity() { LanguageCode = "1035", TemplateName = "STS#0" });
                templates.Add(new WebTemplateEntity() { LanguageCode = "", TemplateName = "STS#0" });
                templates.Add(new WebTemplateEntity() { LanguageCode = "", TemplateName = "BLOG#0" });
                clientContext.Web.SetAvailableWebTemplates(templates);

```

 **SetAvailablePageLayouts**、 **SetDefaultPageLayoutForSite** 和 **SetAvailableWebTemplates** 这三种扩展方法的工作方式相同。它们创建 XML 文档，其中包含的键值对可定义可用的和默认的布局以及可用的模板。然后将这些文档传递到名为 **SetPropertyBagValue** 的另一个扩展方法。此方法在 [OfficeDevPnPCore 扩展](https://github.com/OfficeDev/PnP/tree/dev/OfficeDevPnP.Core)中实现。在设置了相应的属性包之后，这些属性包将用于筛选界面中的选项。

三种方法中， **SetAvailableWebTemplates** 可显示完整的模式。




```
public static void SetAvailableWebTemplates(this Web web, List<WebTemplateEntity> availableTemplates)
        {
            string propertyValue = string.Empty;

            LanguageTemplateHash languages = new LanguageTemplateHash();
            foreach (var item in availableTemplates)
            {
                AddTemplateToCollection(languages, item);
            }

            if (availableTemplates.Count > 0)
            {
                XmlDocument xd = new XmlDocument();
                XmlNode xmlNode = xd.CreateElement("webtemplates");
                xd.AppendChild(xmlNode);
                foreach (var language in languages)
                {
                    XmlNode xmlLcidNode = xmlNode.AppendChild(xd.CreateElement("lcid"));
                    XmlAttribute xmlAttribute = xd.CreateAttribute("id");
                    xmlAttribute.Value = language.Key;
                    xmlLcidNode.Attributes.SetNamedItem(xmlAttribute);

                    foreach (string item in language.Value)
                    {
                        XmlNode xmlWTNode = xmlLcidNode.AppendChild(xd.CreateElement("webtemplate"));
                        XmlAttribute xmlAttributeName = xd.CreateAttribute("name");
                        xmlAttributeName.Value = item;
                        xmlWTNode.Attributes.SetNamedItem(xmlAttributeName);
                    }
                }
                propertyValue = xmlNode.OuterXml;
            }
            // Save the XML entry to property bag.
            web.SetPropertyBagValue(AvailableWebTemplates, propertyValue);
            // Set that templates are not inherited.
            web.SetPropertyBagValue(InheritWebTemplates, "False");

```

 **InheritWebTemplates** 属性包可确保在创建子网站时，也会忽略通常继承自父网站的任何模板。


## 其他资源



- [SharePoint 2013 和 SharePoint Online 中的 UX 组件](ux-components-in-sharepoint-2013-and-sharepoint-online.md)
    
- [Provisioning.Pages](https://github.com/OfficeDev/PnP/tree/dev/Scenarios/Provisioning.Pages)
    
- [Branding.ApplyBranding](https://github.com/OfficeDev/PnP/tree/dev/Samples/Branding.ApplyBranding)
    
