
# 更新现有 SharePoint 网站和页面区域的品牌
自定义之后，刷新 SharePoint 页面现有 SharePoint 网站或区域的品牌，包括功能区、网站导航、设置菜单、树视图和页面内容。

 **上次修改时间：** 2015年8月12日

 _ **适用范围：** SharePoint 2013?| SharePoint Add-ins?| SharePoint Online_

 **注释**  名称"SharePoint 相关应用程序"将更改为"SharePoint 外接程序"。在转换期间，某些 SharePoint 产品和 Visual Studio 工具的文档和 UI 仍可能使用术语"SharePoint 相关应用程序"。有关详细信息，请参阅 [Office 和 SharePoint 相关应用程序的新名称](05b07b04-6c8b-4b7e-bd86-e32c589dfead.md#bk_newname)。

您可以刷新现有 SharePoint 网站和网站集上的品牌，并自定义 SharePoint 页面的区域。例如，当您更新到网站的新版本时，可能想要执行此操作。 
 **为改进此内容做贡献**
您可以获取最新的更新，或为改进 [GitHub 上的此文章](https://github.com/OfficeDev/PnP-Guidance)做贡献。您还可以为改进本示例以及 [GitHub 上的其他示例](https://github.com/OfficeDev/PnP)做贡献。有关示例的完整列表，请参阅 [模式和做法开发人员中心](http://dev.office.com/patterns-and-practices)。我们欢迎您做出 [贡献](https://github.com/OfficeDev/PnP/wiki/contributing-to-Office-365-developer-patterns-and-practices)。 

## 刷新现有网站和子网站的品牌

GitHub 上的 [Office 365 开发人员模式和实践](https://github.com/OfficeDev/PnP/tree/dev) 项目中的 [Branding.Refresh](https://github.com/OfficeDev/PnP/tree/dev/Scenarios/Branding.Refresh) 示例向您演示如何使用 OfficeDevPnP.Core 库来循环访问现有网站和子网站，以验证并更新已应用的品牌。该示例演示如何更新网站品牌，但是相同的概念可以用于执行其他操作，例如将新库部署到网站列表，或更新在设置过程中已部署的自定义操作。您可以使用相同的过程将现有网站移动到较新版本中。

该操作包括两个步骤：


- 获取您想要更新的网站。
    
- 更新网站。
    

### 步骤 1：获取您想要更新的网站

首先，获取您想要更新的网站和子网站的列表。此示例演示如何使用搜索功能执行此操作，但其他选项包括读取网站目录，或提供管理员可以在其中指定列表的管理 UI。下面的示例演示了如何使用搜索功能来生成列表。


```
// Get a list of sites. Search is one way to get this list, an alternative can be a site directory.
List<SiteEntity> sites = cc.Web.SiteSearchScopedByUrl("https://bertonline.sharepoint.com");

// Generic settings (apply changes on all webs or just root web).
bool applyChangesToAllWebs = true;

// Optionally further refine the list of returned site collections.
var filteredSites = from p in sites
                    where p.Url.Contains("13003")
                    select p;

List<SiteEntity> sitesAndSubSites = new List<SiteEntity>();
if (applyChangesToAllWebs)
{
  // You want to update all sites, so the list of sites is extended to all subsites.
  foreach (SiteEntity site in filteredSites)
  {
    sitesAndSubSites.Add(new SiteEntity() { Url = site.Url, 
                                            Title = site.Title, 
                                            Template = site.Template });
    GetSubSites(cc, site.Url, ref sitesAndSubSites);
  }
  sites = sitesAndSubSites;
}

```

调用  **GetSubSites** 是递归形式的，所以可提取子网站树。提取树后，验证所返回的数字是否正确，然后再继续进行。


### 第 2 步：更新品牌

选择要处理的网站之后，您可以使用 OfficeDevPnP.Core 方法操作该网站。此示例演示如何为网站品牌执行此操作，但您可以以相同方式处理任何类型的更改。

此示例使用一种模式，即利用 Web 属性包来存储有关当前设置的信息。代码首先读取 Web 属性包值，然后执行适用于每个值的操作。




```
// Verify that you have a property bag entry.
string themeName = cc.Web.GetPropertyBagValueString(BRANDING_THEME, "");

if (!String.IsNullOrEmpty(themeName))
{
  // If no theme property bag entry, assume no theme has been applied.
  if (themeName.Equals(currentThemeName, StringComparison.InvariantCultureIgnoreCase))
  {
    // The applied theme matches to the theme you want to update.
    int? brandingVersion = cc.Web.GetPropertyBagValueInt(BRANDING_VERSION, 0);
    if (brandingVersion < currentBrandingVersion)
    {
      DeployTheme(cc, currentThemeName);
      // Set the web propertybag entries
      cc.Web.SetPropertyBagValue(BRANDING_THEME, currentThemeName);
      cc.Web.SetPropertyBagValue(BRANDING_VERSION, currentBrandingVersion);
    }
  }
  else
  {
    if (forceBranding)
    {
      DeployTheme(cc, currentThemeName);
      // Set the web propertybag entries.
      cc.Web.SetPropertyBagValue(BRANDING_THEME, currentThemeName);
      cc.Web.SetPropertyBagValue(BRANDING_VERSION, currentBrandingVersion);
    }
  }
}

```

之后，更新该主题的代码变得十分简单，它所基于的是 OfficeDevPnP.Core 方法。




```
string themeRoot = Path.Combine(AppRootPath, String.Format(@"Themes\{0}", themeName));
string spColorFile = Path.Combine(themeRoot, string.Format("{0}.spcolor", themeName));
string spFontFile = Path.Combine(themeRoot, string.Format("{0}.spfont", themeName));
string backgroundFile = Path.Combine(themeRoot, string.Format("{0}bg.jpg", themeName));
string logoFile = Path.Combine(themeRoot, string.Format("{0}logo.png", themeName));

if (IsThisASubSite(cc.Url))
{
  // Retrieve the context of the root site of the site collection.
  using (ClientContext ccParent = new ClientContext(GetRootSite(cc.Url)))
  {
    ccParent.Credentials = cc.Credentials;
    cc.Web.DeployThemeToSubWeb(ccParent.Web, themeName, spColorFile, spFontFile, backgroundFile, "");
    cc.Web.SetThemeToSubWeb(ccParent.Web, themeName);
  }
}
else
{
  cc.Web.DeployThemeToWeb(themeName, spColorFile, spFontFile, backgroundFile, "");
  cc.Web.SetThemeToWeb(themeName);
}

```


## 自定义 SharePoint 页面的区域

如果您的目标是自定义 SharePoint 页面的区域，您可以使用与页面区域相关的文件上的远程设置和自定义级联样式表 (CSS) 的组合。那么，最初，您必须知道哪些 SharePoint 文件与 SharePoint 页面的各个区域相关联。 

 **表 1. SharePoint 页面区域和相关联的文件**



|**页面区域**|**关联的文件 **|**详细信息**|
|:-----|:-----|:-----|
|功能区|任何默认的母版页。 相应的 CSS：
<ul xmlns:xlink="http://www.w3.org/1999/xlink" xmlns:mtps="http://msdn2.microsoft.com/mtps" xmlns:mshelp="http://msdn.microsoft.com/mshelp" xmlns:ddue="http://ddue.schemas.microsoft.com/authoring/2003/5" xmlns:msxsl="urn:schemas-microsoft-com:xslt"><li><p>主体 - 正文：#s4-workspace</p></li><li><p>套件栏 - 左侧：#suiteBarLeft</p></li><li><p>套件栏 - 右侧：#suiteBarRight</p></li><li><p>功能区容器：#globalNavBox</p></li></ul>|可以通过"关注内容"按钮被隐藏。|
|套件导航|任何默认的母版页。 |可以通过"关注内容"按钮被隐藏。|
|套件链接||可以通过"关注内容"按钮被隐藏。|
|欢迎菜单|.master|可以通过"关注内容"按钮被隐藏。|
|设置菜单或齿轮|.master|有关示例，请参阅 [FTC 至 CAM - 自定义 SP 外接程序中的操作和属性包条目](http://blogs.msdn.com/b/vesku/archive/2013/10/02/ftc-to-cam-custom-actions-and-property-bag-entries.aspx)。|
|帮助|.master||
|功能区上下文选项卡|任何默认母版页。|有关示例，请参阅以下文章：
<ul xmlns:xlink="http://www.w3.org/1999/xlink" xmlns:mtps="http://msdn2.microsoft.com/mtps" xmlns:mshelp="http://msdn.microsoft.com/mshelp" xmlns:ddue="http://ddue.schemas.microsoft.com/authoring/2003/5" xmlns:msxsl="urn:schemas-microsoft-com:xslt"><li><p><a href="https://social.msdn.microsoft.com/Forums/sharepoint/zh-cn/df1e4e32-ef58-4b51-8ac8-a8c3690e648b/sharepoint-2013-duplicate-contextual-tabs?forum=sharepointdevelopment" target="_blank">SharePoint 2013 重复上下文选项卡</a></p></li><li><p><a href="https://social.msdn.microsoft.com/Forums/sharepoint/zh-cn/a3640d58-afe1-41d0-ac83-bd7886c37355/hide-a-contextual-ribbon-tab?forum=crmdevelopment" target="_blank">隐藏上下文相关功能区选项卡</a></p></li><li><p><a href="https://social.msdn.microsoft.com/Forums/sharepoint/zh-cn/201306cf-5874-4778-b773-f870c67cee94/hideshow-contextual-tab-on-click-event-of-subgrid?forum=crmdevelopment" target="_blank">单击子网格事件隐藏/显示上下文选项卡</a></p></li></ul>|
|快速访问工具栏|.master|可以通过"关注内容"按钮被隐藏。|
|网站徽标|.master相应的 CSS：.ms sitelcon img||
|顶部导航|导航 CSOM/JSOM.master相应的 CSS（不在编辑模式下）：
<ul xmlns:xlink="http://www.w3.org/1999/xlink" xmlns:mtps="http://msdn2.microsoft.com/mtps" xmlns:mshelp="http://msdn.microsoft.com/mshelp" xmlns:ddue="http://ddue.schemas.microsoft.com/authoring/2003/5" xmlns:msxsl="urn:schemas-microsoft-com:xslt"><li><p>已选择的新项目：.ms-core-listMenu-horizontalBox li.static > .ms-core.listMenu-selected</p></li><li><p>新项目悬停：.mscore-listMenu-horizontalBox li.static > a.ms-core-listMenu-item:hover</p></li><li><p>浮出控件箭头：.ms-core-listMenu-horizontalBox .dynamic-children.additional-background</p></li><li><p>导航项（对应于顶级菜单项）：.ms-core-listMenu-horizontalBox li.static > .ms-core-listMenu-item</p></li><li><p>浮出控件项：ul.dynamic .ms-core-listMenu-item</p></li><li><p>浮出控件容器：ul.dynamic</p></li><li><p>编辑链接：.ms-navedit-editLinksText > span> .ms-metadata</p></li></ul>相应的 CSS（在编辑模式下）：
<ul xmlns:xlink="http://www.w3.org/1999/xlink" xmlns:mtps="http://msdn2.microsoft.com/mtps" xmlns:mshelp="http://msdn.microsoft.com/mshelp" xmlns:ddue="http://ddue.schemas.microsoft.com/authoring/2003/5" xmlns:msxsl="urn:schemas-microsoft-com:xslt"><li><p>导航编辑模式链接：.ms-core-listMenu-horizontalBox .ms-core-listMenuEdit > tr> .msnavedit-linkCell > .ms-core-listMenu-item</p></li><li><p>添加链接：.ms-core-listMenu-horizontalBox a.ms-navedit-addNewLink</p></li></ul>||
|页面标题|相应的 CSS（在编辑模式下）：
<ul xmlns:xlink="http://www.w3.org/1999/xlink" xmlns:mtps="http://msdn2.microsoft.com/mtps" xmlns:mshelp="http://msdn.microsoft.com/mshelp" xmlns:ddue="http://ddue.schemas.microsoft.com/authoring/2003/5" xmlns:msxsl="urn:schemas-microsoft-com:xslt"><li><p>页面标题和带有链接的页面标题：.ms-core-pageTitle, .ms-core-pageTitle a</p></li><li><p>说明按钮：#ms-pageDescriptionDiv</p></li><li><p>说明框：.js-callout-mainElement</p></li><li><p>说明框箭头：.js-callout-beak</p></li><li><p>说明文本：.js-callout-body</p></li></ul>||
|搜索框|导航 CSOM/JSOM.master相应的 CSS：
<ul xmlns:xlink="http://www.w3.org/1999/xlink" xmlns:mtps="http://msdn2.microsoft.com/mtps" xmlns:mshelp="http://msdn.microsoft.com/mshelp" xmlns:ddue="http://ddue.schemas.microsoft.com/authoring/2003/5" xmlns:msxsl="urn:schemas-microsoft-com:xslt"><li><p>搜索框的边框：.ms-srch-sb-border</p></li><li><p>搜索框的边框悬停：.ms-srch-sb-border: hover</p></li><li><p>单击时的搜索框边框：.ms-srch-sb-borderFocused</p></li><li><p>搜索框的输入文本框：.ms-srch-sb-borderFocused</p></li><li><p>搜索框正文：.ms-srch-sb</p></li><li><p>搜索框的输入文本框：.ms-srch-sb-searching</p></li><li><p>搜索</p></li></ul>||
|左侧导航|导航 CSOM/JSOM.master|
<ul xmlns:xlink="http://www.w3.org/1999/xlink" xmlns:mtps="http://msdn2.microsoft.com/mtps" xmlns:mshelp="http://msdn.microsoft.com/mshelp" xmlns:ddue="http://ddue.schemas.microsoft.com/authoring/2003/5" xmlns:msxsl="urn:schemas-microsoft-com:xslt"><li><p><a href="https://msdn.microsoft.com/zh-cn/library/office/ms558975(v=office.14).aspx" target="_blank">如何：自定义 SharePoint Server 中的导航</a></p></li><li><p><a href="http://msdn.microsoft.com/library/c9da5011-3c73-4b83-8e00-e7a03a71ed02(Office.15).aspx" target="_blank">SharePoint 2013 中的托管导航</a></p></li></ul>|
|树视图|.master||
|页面内容|页面布局/内容页面Web 部件区域/Web 部件相应的 CSS（Web 部件区域和 Web 部件）：
<ul xmlns:xlink="http://www.w3.org/1999/xlink" xmlns:mtps="http://msdn2.microsoft.com/mtps" xmlns:mshelp="http://msdn.microsoft.com/mshelp" xmlns:ddue="http://ddue.schemas.microsoft.com/authoring/2003/5" xmlns:msxsl="urn:schemas-microsoft-com:xslt"><li><p>Web 部件区域：.ms-webpart-zone</p></li><li><p>Web 部件容器：.ms-webpartzone-cell</p></li><li><p>Web 部件标题：.ms-webpart-titleText</p></li><li><p>带有链接的 Web 部件标题：.ms-webpart-titleText > a</p></li><li><p>Web 部件正文：.ms-WPBody</p></li></ul>|[如何：在 SharePoint 2013 中创建页面布局](http://msdn.microsoft.com/library/5447e6a1-2f14-4667-81d0-7514b468be80%28Office.15%29.aspx)|
有关自定义页面区域的示例，请参阅以下 GitHub 上的 [Office 365 开发人员模式和实践](https://github.com/OfficeDev/PnP/tree/dev) 项目中的内容：


- [Branding.AlternateCSSAndSiteLogo](https://github.com/OfficeDev/PnP/tree/master/Samples/Branding.AlternateCSSAndSiteLogo)
    
- [Branding.Themes](https://github.com/Lauragra/PnP/tree/master/Scenarios/Branding.Themes)
    

### 默认的 SharePoint 母版页中所需的"最低限度"的内容占位符

SharePoint .master 页面要求您使用内容占位符，它可呈现出 SharePoint 页面支持页面生命周期所需的基本内容和结构元素。表 2 列出并说明这些内容占位符。

 **表 2. 对 SharePoint 母版页的内容占位符的最低要求**



|**内容占位符**|**保留的内容**|
|:-----|:-----|
|PlaceHolderAdditionalPageHead|页面 <head> 部分中的其他项目。|
|PlaceHolderBodyAreaClass|页面标头中的其他样式。|
|PlaceHolderBodyLeftBorder|页面正文的左边框元素。|
|PlaceHolderBodyRightBorder|页面正文的右边框元素。|
|PlaceHolderCalendarNavigator|当日历显示在页面上时，用于在日历中进行导航的日期选取器。|
|PlaceHolderFormDigest|"窗体摘要"安全控件。|
|PlaceHolderGlobalNavigation|全局导航痕迹导航（顶部导航）。|
|PlaceHolderGlobalNavigationSiteMap|全局导航中的站点地图（顶部导航）。|
|PlaceHolderHorizontalNav|页面的顶部导航菜单（顶部导航）。|
|PlaceHolderLeftActions|左下角导航区域（左侧导航）。|
|PlaceHolderLeftNavBar|左侧导航区域（左侧导航）。|
|PlaceHolderLeftNavBarDataSource|左侧导航菜单（左导航） 的数据源。|
|PlaceHolderLeftNavBarTop|左上角导航区域（左侧导航）。|
|PlaceHolderMain|页面的主要内容（页面内容）。|
|PlaceHolderMiniConsole|页面级别命令，如企业 wiki 网页上的"编辑页面"、"历史记录"和"传入链接"。|
|PlaceHolderNavSpacer|左侧导航区域（左侧导航）的宽度。|
|PlaceHolderPageDescription|页面内容的说明。|
|PlaceHolderPageImage|页面左上角的页面图标（功能区）。|
|PlaceHolderPageTitle|页面标题 (<title>)（页面标题）显示在浏览器页面的标题栏中。|
|PlaceHolderPageTitleInTitle|立即显示在痕迹导航下方的页面标题（页面标题）。 |
|PlaceHolderQuickLaunchBottom|快速启动导航（顶部导航）的底部。|
|PlaceHolderQuickLaunchTop|快速启动导航（顶部导航）的顶部。|
|PlaceHolderSearchArea|搜索框控件显示的区域（搜索框）。|
|PlaceHolderSiteName|站点名称（套件导航）。|
|PlaceHolderTitleAreaClass|页面的标题区域（套件导航）。|
|PlaceHolderTitleAreaSeparator|标题区域（套件导航）中的阴影。|
|PlaceHolderTitleBreadCrumb|标题痕迹导航的内容区域。 |
|PlaceHolderTitleLeftBorder|标题区域（套件导航）的左边框。|
|PlaceHolderTitleRightMargin|标题区域（套件导航）的右边距。|
|PlaceHolderTopNavBar|顶部导航区域（顶部导航）。|
|PlaceHolderUtilities|必须显示在页面底部的其他内容（页面内容）。|
|SPNavigation|包括与导航相关的操作。|
|WSSDesignConsole|页面处于"编辑"模式时的页面编辑控件。|
如果您从 SharePoint 的 .master 页删除表 2 中列出的某个内容占位符，SharePoint 将弹出一个错误。您可以添加隐藏可见性的内容占位符，它将隐藏不让最终用户看到的内容。

有关详细信息，请参阅 [Windows SharePoint Services 默认母版页](https://msdn.microsoft.com/zh-cn/library/office/ms467402%28v=office.12%29.aspx)（此文章介绍 SharePoint Services 3 中的功能，但内容仍适用）。请参阅 [使用内容占位符控件](https://support.office.com/zh-cn/article/Working-with-content-placeholder-controls-d8b87b85-d1ef-409d-a5c7-044890f89288?CorrelationId=517ec37c-89ef-40d9-b70e-54aa63ac994f&amp;ui=en-US&amp;rs=en-US&amp;ad=US)。

默认的 SharePoint 母版页（如 seattle.master 和 oslo.master）包括比 SharePoint 所需更多的内容占位符。例如，这些母版页包括  `<SharePoint:Themes runat="server">` 和 `<SharePoint.CssRegistration runat="server">` 控件。

 **Themes** 和 **CssRegistration** 控件同时运行在页面生命周期过程中。通过使用远程设置模式，您可以使用自定义操作添加服务器控件来注册自定义 CSS。

不可见的内容占位符仍然按预期正常工作，但它们生成的内容将从浏览器识别的 HTML 源代码中被省略。 `Visible="false"` 这样的内容占位符处于隐藏状态。


 **重要信息**  请勿在存在于现成 .master 页面（如 Seattle.master 和 Oslo.master）中的自定义母版页中创建自定义占位符。这会导致出现灾难性异常状况。


### SharePoint Online 套件导航控件

SharePoint Online 引入了用于"套件导航"控件的新母版页标记，由此可呈现顶部导航。"套件导航"控件的默认标记会有所不同，具体取决于网站是内部网还是面向公众的网站。若要了解有关"套件导航"控件的详细信息，并查看内部网或面向公众的网站中能够添加到您的母版页中的代码示例，请参阅 [SharePoint Online Suite Navigation 控件](http://msdn.microsoft.com/library/ba93e5c0-e591-48d0-a716-a08ec7ef6cea%28Office.15%29.aspx)。


### 使用 CSOM 自定义 SharePoint 页面区域

一般情况下，我们建议您使用 [UserCustomAction](https://msdn.microsoft.com/zh-cn/library/office/microsoft.sharepoint.client.usercustomaction.aspx) 类添加或删除链接等元素。这是一种使用 [CustomAction](https://msdn.microsoft.com/zh-cn/library/office/ms460194.aspx) 元素的 SharePoint 变体，您如果熟悉完全信任的代码模型，就有可能将其视为功能框架的一部分。虽然没有在客户端对象模型 (CSOM) 中对 **CustomAction** 元素和特征框架设置模式进行专门的支持，但可用于 **CustomAction** 元素的同样的位置标识符也可以用在 CSOM 代码中。


```
<CustomAction
  RequiredAdmin = "Delegated | Farm | Machine"
  ControlAssembly = "Text"
  ControlClass = "Text"
  ControlSrc = "Text"
  Description = "Text"
  FeatureId = "Text"
  GroupId = "Text"
  Id = "Text"
  ImageUrl = "Text"
  Location = "Text"
  RegistrationId = "Text"
  RegistrationType = "Text"
  RequireSiteAdministrator = "TRUE" | "FALSE"
  Rights = "Text"
  RootWebOnly = "TRUE" | "FALSE"
  ScriptSrc = "Text"
  ScriptBlock = "Text"
  Sequence = "Integer"
  ShowInLists = "TRUE" | "FALSE"
  ShowInReadOnlyContentTypes = "TRUE" | "FALSE"
  ShowInSealedContentTypes = "TRUE" | "FALSE"
  Title = "Text"
  UIVersion = "Integer">
</CustomAction>

```


### 自定义 SharePoint 功能区

当您自定义功能区时，服务器呈现的 HTML 将分配给您为自定义样式分配的类名称。若要使用此功能，请转到样式库，并为每个您想要添加到功能区的样式创建新的 CSS 文件。您可以将自定义样式添加到功能区的两个部分："页面元素"和"文本样式"。将以下语法用于您添加的样式：


- 对于"页面元素"部分： `span.ms-rteElement-<yourowndefinedname>`。或者，您可以使用 H1、H2、H3 或 H4 这类将要把应用该样式的文本包裹起来的样式。
    
- 对于"文本样式"部分： `.ms-rteEStyle-<yourowndefinedname>`。
    
然后，在 CSS 类定义中，添加以下行： 

 `-ms-name:"The name visual in the ribbon for your style";`

现在，您可以在您的自定义 .css 文件中完成对自定义 CSS 类的细节定义。


### 自定义 Web 部件页上的套件导航

在 SharePoint 2013 中，UI 具有基于页面磁贴的现代外观。例如，在默认情况下，动态磁贴会出现在默认的 SharePoint 2013 页面上。顶部导航让用户能够更容易地查看和访问社交内容和 OneDrive for Business。您可以结合使用 CSS 和 JavaScript 自定义这些区域的外观。

创建 Web 部件页之后，将脚本编辑器 Web 部件 (SEWP) 添加到可用的 Web 部件区域。您可以使用此 Web 部件将 JavaScript 添加到您的页面。您可以将 JavaScript 代码通过其  **ElementId** 标识添加到可识别顶部导航栏的 SEWP，然后通过将其可见性属性设置为隐藏来隐藏它。


### 自定义"设置"菜单或齿轮

您可以使用 [UserCustomAction](https://msdn.microsoft.com/zh-cn/library/office/microsoft.sharepoint.client.usercustomaction.aspx) 类和属性包条目来自定义任何 SharePoint 网站的设置菜单，如以下代码示例所示。


```
public void AddCustomActions(ClientContext clientContext)
{
    // Add a site settings link if it doesn't already exist.
    if (!CustomActionAlreadyExists(clientContext, "Sample_CustomSiteSetting"))
    {
        // Add a site settings link.
        UserCustomAction siteSettingLink = clientContext.Web.UserCustomActions.Add();
        siteSettingLink.Group = "SiteTasks";
        siteSettingLink.Location = "Microsoft.SharePoint.SiteSettings";
        siteSettingLink.Name = "Sample_CustomSiteSetting";
        siteSettingLink.Sequence = 1000;
        siteSettingLink.Url = string.Format(DeployManager.appUrl, clientContext.Url);
        siteSettingLink.Title = "Modify Site Metadata";
        siteSettingLink.Update();
        clientContext.ExecuteQuery();
    }

    // Add a site actions link, if it doesn't already exist.
    if (!CustomActionAlreadyExists(clientContext, "Sample_CustomAction"))
    {
        UserCustomAction siteAction = clientContext.Web.UserCustomActions.Add();
        siteAction.Group = "SiteActions";
        siteAction.Location = "Microsoft.SharePoint.StandardMenu";
        siteAction.Name = "Sample_CustomAction";
        siteAction.Sequence = 1000;
        siteAction.Url = string.Format(DeployManager.appUrl, clientContext.Url); ;
        siteAction.Title = "Modify Site Metadata";
        siteAction.Update();
        clientContext.ExecuteQuery();
    }
}

```


### 自定义树视图

若要修改树视图的宽度，请在 .master 页面的树标签周围添加一个  `<div>` 标签，并将带有样式宽度属性的 CSS 类分配到 `<div>`。您可以通过将以下样式定义添加到 .css 文件中来增加"快速启动"导航的宽度。


```
.ms-nav {
  width: 220 px;
}

```


### 自定义页面内容

对自定义页面内容的要求取决于您要在页面中包含的内容。对于自定义"网站操作"菜单，您可以使用 [UserCustomAction](https://msdn.microsoft.com/zh-cn/library/office/microsoft.sharepoint.client.usercustomaction.aspx) 对象将品牌设置到 Web 部件中。

如果您正在生成一个发布网站，请参阅 [如何：在 SharePoint 2013 中创建页面布局](http://msdn.microsoft.com/library/5447e6a1-2f14-4667-81d0-7514b468be80%28Office.15%29.aspx)来学习基础知识。页面布局取决于 CSOM 中的 [ContentTypeId](https://msdn.microsoft.com/zh-cn/library/office/microsoft.sharepoint.client.contenttypeid.aspx) 类的可用性。对于 CSOM 中不可用的其他成员，您可以使用 Windows Communication Foundation (WCF) 服务作为一个临时的替代方法来使用 **ContentTypeId**。 


## 其他资源



- [SharePoint 网站品牌和页面自定义解决方案](SharePoint-site-branding-and-page-customization-solutions.md)
    
- [SharePoint 2013 和 SharePoint Online 的品牌打造和网站设置解决方案](Branding-and-site-provisioning-solutions-for-SharePoint.md)
    
