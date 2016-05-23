
# SharePoint 页面和页面模型
SharePoint 页面模型包括母版页、内容页、SharePoint 页面部件和默认页面的文件类型。 

 **上次修改时间：** 2015年8月11日

 _ **适用范围：** SharePoint 2013?| SharePoint Add-ins?| SharePoint Online_

 **注释**  名称"SharePoint 相关应用程序"将更改为"SharePoint 外接程序"。在转换期间，某些 SharePoint 产品和 Visual Studio 工具的文档和 UI 仍可能使用术语"SharePoint 相关应用程序"。有关详细信息，请参阅 [Office 和 SharePoint 相关应用程序的新名称](05b07b04-6c8b-4b7e-bd86-e32c589dfead.md#bk_newname)。

所呈现的 SharePoint 页面是三个页面类型的组合： 

- 母版页，控制内容的布局和外观。
    
- 内容页，包含页字段控件。
    
- 用户友好型创作页，用户在此处添加内容。
    
本文提供 SharePoint 页面模型的概述，包括页面类型、在 SharePoint 2013 和 SharePoint Online 中可用的默认页面文件，以及页面处理方式的信息。 

## 有关 SharePoint 页面模型的关键术语和概念



|**术语或概念**|**定义**|**访问方式**|**详细信息**|
|:-----|:-----|:-----|:-----|
|协作网站|团队网站。|||
|内容占位符|母版页中为那些将来能够以编程方式替换的控件或内容保留空间的条目。|所有 SharePoint 母版页|内容占位符是 SharePoint 母版页的构建基块。|
|母版页|标准化 SharePoint 页面左侧和顶部导航元素的行为和表现的页面。|SharePoint 文件系统母版页样式库||
|母版页样式库|SharePoint 2013 中特定的文档库，其中所有的品牌元素 - 母版页、页面布局、JavaScript 文件、CSS 和图像 - 都以默认方式进行存储。每个网站都有自己的母版页样式库。|"设置">"网站设置">"母版页和页面布局"|母版页样式库中包含存储品牌资产（如母版页和 CSS 文件）的目录。 
 >>**提示**  创建自定义品牌元素时，将自定义的资产存储在默认的母版页样式库的文件结构中。

[SharePoint 2013 中的母版页、母版页样式库和页面布局](http://msdn.microsoft.com/library/80b9a360-bc2e-46c6-b0ca-1bc487b73db6%28Office.15%29.aspx)|
|最少下载策略 (MDS)|当用户从一个 SharePoint 页面导航到另一个页面时，能够减少浏览器必须下载的数据量的策略。|网站设置|当 MDS 处于活动状态时，SharePoint 通过  `/_layouts/15/start.aspx` 传递所有页面请求，并检查在新的页面请求和之前所上载的页面之间是否存在视觉差异。 [优化 SharePoint 2013 中的页面性能](http://msdn.microsoft.com/library/262caeef-64fd-4e02-b947-d772faf01159%28Office.15%29.aspx)[最少下载策略概述](http://msdn.microsoft.com/library/9caa7d99-1e74-4889-96c7-ba5a10772ad7%28Office.15%29.aspx)|
|导航|使用户能够在 SharePoint 网站的信息架构中来回移动的功能。SharePoint 中的导航元素包括搜索、树控件、按钮、功能区、超链接、选项卡、菜单和分类。||[Navigation 类](https://msdn.microsoft.com/zh-cn/library/office/microsoft.sharepoint.client.navigation.aspx)[NavigationNode 类](https://msdn.microsoft.com/zh-cn/library/office/microsoft.sharepoint.client.navigationnode.aspx)|
|Oslo 母版|SharePoint 2013 中的默认母版页。|SharePoint 文件系统母版页样式库|与 seattle.master 母版页不同，当前导航与顶部导航区域位于同一位置。|
|页面内容控件|位于可在其中添加 Web 部件的发布网站上的控件。|||
|页面布局|适用于强制执行内容呈现一致性的发布页面的模板。|SharePoint 文件系统母版页样式库|[如何：在 SharePoint 2013 中创建页面布局](http://msdn.microsoft.com/library/5447e6a1-2f14-4667-81d0-7514b468be80%28Office.15%29.aspx)|
|页面模型|在 SharePoint 页面中出现的文件、内容和互动同样会在浏览器中呈现给用户。||[SharePoint 2013 页面模型概述](http://msdn.microsoft.com/library/808b1af3-89ab-4f02-89cc-ea86cb1f9a6e%28Office.15%29.aspx)|
|发布页面|在发布网站中的 .aspx 页面。||[PublishingPage 类](https://msdn.microsoft.com/zh-cn/library/office/microsoft.sharepoint.publishing.publishingpage.aspx)|
|发布网站|可以访问发布网站和页面的 SharePoint 网站，其中包括页面布局、分类、托管的导航，以及其他 Web 内容管理和企业内容管理功能。 ||[PublishingWeb 类](https://msdn.microsoft.com/zh-cn/library/office/microsoft.sharepoint.publishing.publishingweb.aspx)[SharePoint 2013 网站开发的新增内容](https://msdn.microsoft.com/zh-cn/library/office/jj163942.aspx)|
|Seattle.master|SharePoint 2013 中的默认母版页。|SharePoint 文件系统母版页样式库||
|团队网站|为用户协作处理文档、wiki、观点、过程等等而设计的网站。|||
|文本布局|定义出现在 Wiki 网页上的内容区域。|||
|文本布局控件|能够包含文本、图像、Web 部件和应用程序部件的 wiki 网页控件。|||
|首要网站|由服务器提供的默认的首要网站。||[创建 SharePoint 网站](https://support.office.com/zh-cn/article/Create-a-SharePoint-site-4b1c153a-ec2b-45df-9dd9-e31d25563d1b?CorrelationId=ad2fc24e-9e3e-4da4-be0f-500d2e89fc64&amp;ui=en-US&amp;rs=en-US&amp;ad=US)|
|Web 部件|运行在网站页面上下文中的服务器端控件。||[SharePoint 应用程序中的自定义操作和属性包条目](http://blogs.msdn.com/b/vesku/archive/2013/10/02/ftc-to-cam-custom-actions-and-property-bag-entries.aspx)|
|Web 部件页|由 Web 部件区域组成的内容页，其中可以包含 Web 部件。在 Web 部件页上，Web 部件用 [WebPartDefinition](https://msdn.microsoft.com/zh-cn/library/office/microsoft.sharepoint.client.webparts.webpartdefinition.aspx) 对象来表示。||[Microsoft.SharePoint.Client.WebParts 命名空间](https://msdn.microsoft.com/zh-cn/library/office/microsoft.sharepoint.client.webparts.aspx)|
|Web 部件区域|可在其中添加 Web 部件的页面区域。|||
|Wiki 网页|使用企业 Wiki 网站模板的内容页面。||[Provisioning.Pages 示例](https://github.com/OfficeDev/PnP/tree/master/Scenarios/Provisioning.Pages)|

## SharePoint 母版页

母版页是一个扩展名为 .master 的 ASP.NET 文件。它包括一个  `<%@ Master` 指令，并定义了首要 HTML 元素，如 **HTML**、 **Head** 和 **Form**。它首先列出控件和程序集，然后声明  **DOCTYPE** 的文档类型定义，从而告诉浏览器如何呈现 HTML。SharePoint 2013 可调整到与 **XHTML 1.0** 和 **HTML5** DOCTYPE 配合使用的最佳工作状态。

SharePoint 在默认情况下可包含多个母版页。 这些母版页为给定的 SharePoint 页面提供默认的结构和部件版式，适合用于 SKU 和网站类型 - 特别适合用在页面的顶部和左侧。表 2 列出了默认的 SharePoint 2013 和 SharePoint Online 母版页。

 **表 2. 默认的 SharePoint 母版页**



|**母版页**|**说明**|
|:-----|:-----|
|Custom.master|系统页面，如表单和视图。供所有 SharePoint 2013 和 SharePoint Online SKU 使用。|
|Default.master|发布网站中的网站页面。包括在所有 SharePoint 2013 和 SharePoint Online SKU 之中。当激活发布功能时可用。|
|Application.master|某些系统页面，如 scope.aspx 和 keyword.aspx。包括在所有 SharePoint 2013 和 SharePoint Online SKU 之中。|
|Minimal.master|在所有 SharePoint 2013 SKU 中可供选择的默认母版页选项。|
|Seattle.master|在所有 SharePoint 2013 和 SharePoint Online SKU 中可供选择的默认母版页选项。|
|Oslo.master|在所有 SharePoint 2013 和 SharePoint Online SKU 中可供选择的默认母版页选项。|
|Kyoto.master|可在 SharePoint Online 中使用的母版页。 |
|Berlin.master|可在 SharePoint Online 中使用的母版页。 |
|Lyon.master|可在 SharePoint Online 中使用的母版页。 |
|Mysite15.master|OneDrive for Business 网站（之前为：我的网站、个人网站或 OneDrive Pro 网站）。|
每个默认的 SharePoint 母版页中都包含常用网络编程技术（如 HTML、CSS 和 JavaScript）在 SharePoint 中执行操作所需的控件。

内容占位符保留内容页中定义的信息位置。内容占位符对应页面中的区域。.master 页面的每个区域都定义有几个至几百个数量不等的内容占位符。

SharePoint 母版页结合使用了 ASP.NET ( `<asp:`) 和 SharePoint ( `<SharePoint:`) 声明。声明中冒号后的文本定义控件的功能；例如， `SharePoint:PlaceholderGlobalNavigation` 将 SharePoint 页面的全局导航注入到该页面上的相关 HTML 标签中。母版页中的内容控件将内容占位符绑定到带有 **ContentPlaceHolderID** 的内容。

SharePoint 提供两种类型的母版页：系统母版页和网站母版页。系统母版页适用于 SharePoint 网站上所有形式的网页和视图网页。在另一方面，网站母版页可供发布网站中的所有页面使用。您可以通过打开 .master 页面文件和查看  **Page** 指令指出网站正在使用的是哪种母版页。系统母版页有如下一个页面指令： `~masterurl/default.master`。网站母版页有以下页面指令： `~masterurl/custom.master`。

您可以使用 CSOM 代码设置母版页属性 - 主要基于 [Web](https://msdn.microsoft.com/zh-cn/library/office/microsoft.sharepoint.client.web.aspx) 对象编写代码。使用其 [MasterUrl](https://msdn.microsoft.com/zh-cn/library/office/microsoft.sharepoint.client.web.masterurl.aspx) 属性更改系统母版页，使用该对象的 [CustomMasterUrl](https://msdn.microsoft.com/zh-cn/library/office/microsoft.sharepoint.client.web.custommasterurl.aspx) 属性更改网站母版页。

内容占位符通常包括动态令牌，这些都是形成 SharePoint 页面 URL 一部分的重要代码段。SharePoint 根据定义超文本信息如何在服务器和 SharePoint 页面之间进行转移的协议规则（如 HTTP）解析 URL 字符串。通常情况下，一个指向 CSS 或主题控件的内容占位符将使用相对 URL，这在 SharePoint 服务器端对象模型中表示为  `~SPUrl`。

SharePoint 使用动态令牌将母版页绑定到内容页面，这在 .master 页面代码的  `<asp:content>` 声明中进行定义。表 3 列出了可在 SharePoint 母版页中找到的动态令牌，以及在处理页面时替换它们所用的 CSOM 属性或 SharePoint 呈现内容占位符所采用的 URL 字符串形式。

 **表 3. 使用属性值替换的母版页中的动态令牌**



|**动态令牌**|**替换为**|
|:-----|:-----|
|~masterurl/default.master|SPWeb.MasterUrl|
|~masterurl/custom.master|SPWeb.CustomMasterUrl|
|~site/<xyz>.master|http://<siteColl>/<subsite1>/<subsite2>/<xyz>.master|
|~sitecollection/<abc>.master|http://<siteColl>/<abc>.master|

 **注释**  内容占位符中的动态令牌对应于服务器端 API 属性和方法。在使用远程资源配置时，请在 CSOM 或 REST 中编写代码。若要了解有关动态令牌和 SharePoint URL 的详细信息，请参阅 [SharePoint 2013 中的 URL 和标记](http://msdn.microsoft.com/library/161418d7-8123-4c4e-91a1-97e43c17f0e6%28Office.15%29.aspx)。SharePoint 外接程序使用一些适用于网站 URL 的令牌。


## Web 部件页和 Wiki 网页

Web 部件页面可以包含结构化和非结构化的信息。它们由 Web 部件区域组成。放置在 Web 部件区域中的 Web 部件可以显示列表、搜索结果和查询中的数据，并能呈现多个来源中的数据自定义视图。Web 部件页中包含了大部分可作为标准 SharePoint 团队网站的相同元素。标题栏可以包含一个标题、描述、说明、公司徽标或其他图像。Web 部件页增加了以下元素：


- 可用于添加或修改 Web 部件、设计页面布局和在个人和共享视图之间进行切换的 Web 部件页菜单。
    
- 一个用于查找和添加 Web 部件和编辑与 Web 部件和 Web 部件页相关的属性的工具窗格。
    
相比于 Web 部件页，wiki 网页的结构性稍差。因为它们采用半结构化到非结构化的形式，因此让用户可以很容易创建内容和互相协作。默认情况下，SharePoint 在您第一次查看新的团队网站时显示 wiki 网页。

企业 wiki 的功能在 SharePoint 的所有版本中均可使用。企业 Wiki 模板可以创建并使用带有 wiki 网页的页面布局。当您编辑 wiki 网页时，Web 部件、文本和其他内容显示在文本布局中。文本布局将内容区域排列在 wiki 网页上。

您可以使用远程配置模式创建 wiki 网页。 [WikiPageCreationInformation](https://msdn.microsoft.com/zh-cn/library/office/microsoft.sharepoint.client.utilities.wikipagecreationinformation.aspx) 类为您提供可用于创建 wiki 网页的方法，而 **WikiHtmlContent** 属性则获取并设置页面上的 HTML 内容。 [Utility](https://msdn.microsoft.com/zh-cn/library/office/microsoft.sharepoint.client.utilities.utility.aspx) 类包括 **CreateWikiPageInContextWeb** 方法，SharePoint 使用该方法利用 **WikiPageCreationInformation** 类中的参数在客户端运行时上下文中创建 wiki 网页。


## 页面布局

页面布局选择用于发布网站的内容页。页面布局是通过自定义页面的正文结构来定义 SharePoint 网站中不同类型页面（如文章）的模板。就像 Web 部件页是一个可以排列页面上 Web 部件区域和 Web 部件的模板一样，页面布局可以排列页面上的字段。在页面布局中定义的字段控件将包含作者创建的内容，并且该内容的结构将以该页面的布局为基础。


 **注释**  页面布局可以包含 Web 部件区域。

设计人员可以将样式应用于页面字段控件。这使设计人员可以控制 CSS 应用到每个字段的方式和呈现形式，也允许用户创建和管理每个页面字段中的内容。

在 SharePoint 中，内容类型是可定义特定项和文档的元数据（也称为列）和行为的可重复使用集合。例如，您可能想要创建一种内容，其外观和行为都是按照联机杂志文章的方式来表现的。内容类型使您可以执行此操作。您可能还需要创建其他唯一类型的内容，但可以在其他内容类型中重复使用和共享某一种内容类型的特征。每个页面布局都完全基于一种内容类型。每个内容类型分配唯一一个 [内容类型 ID](https://msdn.microsoft.com/zh-cn/library/office/aa543822%28v=office.14%29.aspx)。

若要了解有关内容类型的详细信息，请参阅 [内容类型简介](https://msdn.microsoft.com/zh-cn/library/office/ms472236%28v=office.14%29.aspx)、 [列](https://msdn.microsoft.com/zh-cn/library/office/ms196085%28v=office.14%29.aspx)和 [内容类型中的自定义信息](https://msdn.microsoft.com/zh-cn/library/office/ms468437%28v=office.14%29.aspx)。


 **重要信息**   目前，您可以使用远程设置模式将现成的页面布局应用到 SharePoint 网站。虽然您可以在网站上通过自定义 SharePoint 外接程序代码使用 CSOM 代码来设置自定义内容类型，并且 SharePoint Online 中支持通过 CSOM 设置自定义 **ContentTypeId**，但是目前尚不支持通过在本地 SharePoint 网站上进行远程设置来为自定义内容类型设置 ContentTypeId。若要了解详细信息，请参阅 [如何：在 SharePoint 2013 中创建页面布局](http://msdn.microsoft.com/library/5447e6a1-2f14-4667-81d0-7514b468be80%28Office.15%29.aspx)。


## SharePoint 页面处理模型

SharePoint 是一个基于模板的页面呈现系统，它将母版页、内容页和创作内容结合在一起来显示页面。页面呈现系统称为 [网页处理模型](https://msdn.microsoft.com/zh-cn/library/office/ms498550%28v=office.14%29.aspx)。母版页可供应用它们的网站中的所有页面实例使用，内容页可供基于该内容页的网页中所有实例使用。

页面处理模型解释并运行用户代理（如 Web 浏览器）向服务器提出的所有请求。例如，考虑用户请求一个名为 contoso.aspx 的页面。为了完成该请求，ASP.NET 引擎检索两个页面：与 contoso.aspx 相关的内容页，和该文件提供程序与 SharePoint 网站相关联的母版页。该引擎还检索字段控件和字段中的 Web 部件，并将它们呈现在页面上。


 **注释**  团队网站和 网站的页面处理逻辑类似于发布网页的页面处理逻辑。 


### 页面处理

当 SharePoint 用户加载 Web 部件页时，SharePoint 通过解析到其模板、网页内容和上下文的路径来获得该部件页。它还设置与 Web 部件页相关的 Web 部件，并向该页分配一个 [WebPartCollection](http://msdn2.microsoft.com/ZH-CN/library/k41e9930) 实例，并向 Web 部件页及其 Web 部件填充内容。

当 SharePoint 用户加载 wiki 网页（通过在团队网站或发布网站上使用企业 Wiki 模板）时，SharePoint 通过解析到其模板、网页内容和上下文的路径来获得该页面。它还设置与 wiki 网页相关的文本布局控件，并向企业 wiki 网页和其文本布局填充内容。若要了解有关如何使用远程设置模式设置 wiki 网页的详细信息，请参阅 [Provisioning.Pages](https://github.com/OfficeDev/PnP/tree/master/Scenarios/Provisioning.Pages) 示例。


### 最少下载策略和 <AjaxDelta> 控件

在 SharePoint 中，最少下载策略功能管理在页面呈现前要刷新母版页上的哪些特定内容。当启用该策略时，与母版页上包裹在  `<SharePoint:AjaxDelta>` 标签中的内容占位符相关的内容会在页面呈现前进行刷新。相反，未包裹在 `<SharePoint:AjaxDelta>` 标签中的内容占位符在启用最少下载策略时是不会呈现出来的。

您可以通过中央网站管理或使用 SharePoint 客户端对象模型 (CSOM) 启用或禁用最少下载策略。您可以通过使用 [EnableMinimalDownload](https://msdn.microsoft.com/zh-cn/library/office/microsoft.sharepoint.client.web.enableminimaldownload.aspx) 属性激活该功能。有关详细信息，请参阅 [最少下载策略概述](http://msdn.microsoft.com/library/9caa7d99-1e74-4889-96c7-ba5a10772ad7%28Office.15%29.aspx)。有关如何优化母版页以更好地使用最少下载策略的详细信息，请参阅 [为 MDS 修改 SharePoint 组件](http://msdn.microsoft.com/library/c967be7c-f29f-481a-9ce2-915ead315dcd%28Office.15%29.aspx)。

最少下载策略功能在 SharePoint 团队网站上是默认启用的，在 SharePoint 发布网站和在启用发布功能的 SharePoint 团队网站上是默认禁用的。


### 基于 seattle.master 创建自定义母版页

您可以使用远程设置来设置网站品牌元素（如网站主题），并可以使用 CSS 或 JavaScript 来显示或隐藏元素或页面控件。自定义母版页上提供控制页面结构的其他级别控件。创建自定义母版页时，不要编辑完之后使用默认名称（例如，seattle.master）保存默认的母版页。相反，要复制您想要修改的默认母版页，并将其重命名。


 **重要信息**   因为存在持续支持成本和维护的长期潜在影响，我们建议不要更改新母版页的结构。您可以更改支持不会影响结构的品牌的母版页，如更改标题中的颜色、将彩色背景添加到页面的特定元素，或显示和隐藏网站徽标。如果您正在使用默认的 .master 页面不包括您想要包括在页面上结构元素（如页脚），那么请使用现成的不同母版页。

为了帮助在自定义母版页中保持一致性，请遵从现有的编码模式。例如，在使用表格的页面区域中，使用表格来加强编码模式。在使用  `<DIV>` 标签或 HTML5 的区域，将所有自定义代码与 `<DIV>` 标签或 HTML5 进行匹配。从长远来看，这可以使您必须创建的所有自定义母版页更易于维护，因此，成本也更低。


## 其他资源



- [SharePoint 2013 和 SharePoint Online 的品牌打造和网站设置解决方案](Branding-and-site-provisioning-solutions-for-SharePoint.md)
    
- [SharePoint 2013 中的母版页、母版页样式库和页面布局](http://msdn.microsoft.com/library/80b9a360-bc2e-46c6-b0ca-1bc487b73db6%28Office.15%29.aspx)
    
- [如何：在 SharePoint 2013 中创建页面布局](http://msdn.microsoft.com/library/5447e6a1-2f14-4667-81d0-7514b468be80%28Office.15%29.aspx)
    
- [最少下载策略概述](http://msdn.microsoft.com/library/9caa7d99-1e74-4889-96c7-ba5a10772ad7%28Office.15%29.aspx)
    
- [内容类型简介](https://msdn.microsoft.com/zh-cn/library/office/ms472236%28v=office.14%29.aspx)
    
