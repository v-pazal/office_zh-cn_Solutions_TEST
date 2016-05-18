
# SharePoint 网站品牌和页面自定义解决方案
使用 SharePoint 页面模型和组合外观、SharePoint 2013 主题引擎和用于打造您 SharePoint 网站和页面品牌的 CSS。

 **上次修改时间：** 2015年8月7日

 _ **适用范围：** SharePoint 2013?| SharePoint Add-ins?| SharePoint Online_

 **注释**  名称"SharePoint 相关应用程序"将更改为"SharePoint 外接程序"。在转换期间，某些 SharePoint 产品和 Visual Studio 工具的文档和 UI 仍可能使用术语"SharePoint 相关应用程序"。有关详细信息，请参阅 [Office 和 SharePoint 相关应用程序的新名称](05b07b04-6c8b-4b7e-bd86-e32c589dfead.md#bk_newname)。

您可以通过两种方式自定义 SharePoint 网站的外观：

- 通过使用主题引擎来创建自定义主题（SharePoint 2013 和 SharePoint Online 中的组合外观）。至少，主题定义颜色。完整主题定义颜色、字体、背景图像和关联的母版页以及定义 .master 页预览方式的 .preview 文件。可以使用远程设置模式将主题应用于网站。
    
- 通过创建自定义级联样式表 (CSS) 以应用到 SharePoint Online 网站。您可以使用 SharePoint 外接程序 和远程设置模式来设置 SharePoint 网站以使用自定义 CSS。 
    
品牌更改涉及内容从低成本、简单化到高成本、复杂化。用户可以使用 UI 来应用组合外观，包括背景图像、调色板、字体、母版页和与母版页相关联的 .preview 文件。您可以使用 SharePoint 2013 主题引擎设计组合外观和设置网站，您还可以创建自定义的 CSS 来修改您的网站及其元素的外观。

 **重要信息**  尽管您可以创建自定义母版页和其他结构元素作为自定义品牌项目的一部分，但支持自定义母版页和其他自定义结构元素的长期成本很高。自定义品牌打造会以更高昂的成本为您的组织应用升级，并提供持续支持。

此部分基于您的知识和 [设计工具和设计打包选项](sharepoint-development-and-design-tools-and-practices.md)，向您演示如何自定义 SharePoint 网站的外观。
 **为改进此内容做贡献**
您可以获取最新的更新，或为改进 [GitHub 上的此文章](https://github.com/OfficeDev/PnP-Guidance)做贡献。您还可以为改进本示例以及 [GitHub 上的其他示例](https://github.com/OfficeDev/PnP)做贡献。有关示例的完整列表，请参阅 [模式和做法开发人员中心](http://dev.office.com/patterns-and-practices)。我们欢迎您做出 [贡献](https://github.com/OfficeDev/PnP/wiki/contributing-to-Office-365-developer-patterns-and-practices)。 

## 与 SharePoint 品牌相关的关键术语和概念



|**术语或概念**|**定义**|**详细信息**|
|:-----|:-----|:-----|
|备用 CSS|您可以将除默认文件之外的 CSS 文件应用到您网站的外观。|使用备用 CSS 将自定义 CSS 应用到网站及其所有子网站中。 |
|CSS|一种告诉浏览器如何呈现 HTML 或 XML 文档样式的语言。CSS 将文档内容（HTML 或 XML）从内容表现形式中分离出来。||
|组合外观|是一种将字体、调色板、背景图像和应用于与网站相关联的母版页组合在一起的形式。字体方案和颜色图像是可选的。以下是组合外观的默认文件位置：Theme Gallery\15 folder|组合外观是一种能够更改网站外观，但又不用对网站结构进行任何更改的简便方法。SharePoint 2013 在默认情况下提供几种组合外观。当用户应用一种组合外观时，SharePoint 会将所有与组合外观相关联的设计元素都应用到网站上。|
|内容搜索 Web 部件 (CSWP)|基于指定查询显示搜索结果中的内容。|[SharePoint 2013 中的内容搜索 Web 部件](http://social.technet.microsoft.com/wiki/contents/articles/15843.content-search-web-part-in-sharepoint-2013.aspx) (TechNet)[SharePoint 2013 中的内容搜索 Web 部件](http://msdn.microsoft.com/library/6fb4bf41-0846-4dca-ad9e-906afdfd3d2b%28Office.15%29.aspx) (MSDN)|
|corev15.css|这是包含了 SharePoint 的大部分主要功能的 CSS 文件。以下是默认文件位置：_layouts\15 folder||
|CSSRegistration|母版页（如 seattle.master）中的引用，其中加载了大部分应用到大多数默认 UI 的 CSS。|在母版页中使用"CSSRegistration"控件来覆盖默认 CSS。 |
|自定义操作|您可以用来自定义主机 web 上的列表和功能区并与其进行交互的操作。|[创建自定义操作以部署 SharePoint 外接程序](http://msdn.microsoft.com/library/bbd11f94-1798-453e-bbb0-e5eb0df8dc75%28Office.15%29.aspx)|
|设备通道|通过对呈现在特定设备上的目标内容使用唯一通道，从而以多种方式呈现单个 SharePoint 发布网站。|[SharePoint 2013 设计管理器设备通道](http://msdn.microsoft.com/library/a924bd7b-a5e3-41bf-b0a7-3e43945fa951%28Office.15%29.aspx)|
|显示模板|"搜索 Web 部件"用于显示搜索索引查询结果的模板。|[SharePoint 2013 Design Manager 显示模板](http://msdn.microsoft.com/library/1a782bac-48ee-4baf-8751-0f943a306e0f%28Office.15%29.aspx)|
|图像呈现形式|在发布网站上显示同源图像的不同尺寸版本。|[SharePoint 2013 设计管理器图像呈现形式](http://msdn.microsoft.com/library/d08a74c0-5674-4f26-8646-11ea1f081c85%28Office.15%29.aspx)|
|托管元数据| 是 SharePoint 中的功能，使您能够定义术语、术语集、组和术语标签。有时也称为分类。在 SharePoint 2013 中，托管的元数据系统是托管导航的基础。|[SharePoint 2013 中的托管元数据和导航](http://msdn.microsoft.com/library/b66d4ec1-a2ef-49cc-8ca5-a6b516bff02e%28Office.15%29.aspx)|
|托管导航|基于托管元数据而生成的用于发布网站的导航。该导航是从术语库的指定术语集中生成的。 |[SharePoint 2013 中的托管导航](http://msdn.microsoft.com/library/c9da5011-3c73-4b83-8e00-e7a03a71ed02%28Office.15%29.aspx)|
|母版页|这是标准化 SharePoint 页面的左侧和顶部导航区域的行为和表现的页面。|[SharePoint 2013 中的母版页、母版页样式库和页面布局](http://msdn.microsoft.com/library/80b9a360-bc2e-46c6-b0ca-1bc487b73db6%28Office.15%29.aspx)|
|母版页样式库|SharePoint 2013 中特定的文档库，其中所有的品牌元素 - 母版页、页面布局、JavaScript 文件、CSS 和图像 - 都以默认方式进行存储。每个网站都有自己的母版页样式库。创建自定义品牌元素时，我们建议您将自定义的资产存储在默认的母版页样式库的文件结构中。|[SharePoint 2013 中的母版页、母版页样式库和页面布局](http://msdn.microsoft.com/library/80b9a360-bc2e-46c6-b0ca-1bc487b73db6%28Office.15%29.aspx)|
|Oslo.master|SharePoint 2013 中的母版页。|将当前导航移至顶部导航区域相同的位置。 |
|页面内容控件|位于可在其中添加 Web 部件的发布网站上的控件。||
|页面布局|SharePoint 发布网站页面的模板，让用户能够以一致的方式布局在页面上的信息。||
|快速启动|管理协作网站页面左侧的导航元素。|您可以添加标题链接到组导航项。|
|REST|无状态的体系结构样式，提取体系结构元素并使用 HTTP 谓词从包含 XML 文件的网页读取和写入数据。|[了解 SharePoint 2013 REST 服务](http://msdn.microsoft.com/library/2de035a0-ac75-43bd-9665-5c5a59c4c590%28Office.15%29.aspx)|
|根网站|网站集中的第一个 Web 页。|根网站有时也称为"Web 应用程序根"。 |
|Seattle.master|适用于 SharePoint 2013 团队网站和发布网站的默认 .master 页。||
|网站布局|请参阅母版页。|网站布局将主题的 .master 页面与其相应的 .preview 文件结合在一起。|
|结构化导航|基于发布网站的网站层次结构建立的适用于发布网站的导航结构。 您可以添加标题和链接，实现手动更换或自定义 SharePoint 自动生成的结构化导航。|[如何：自定义 SharePoint Server 2010 (ECM) 中的导航](https://msdn.microsoft.com/zh-cn/library/office/ms558975%28v=office.14%29.aspx)|
|主题|一种将光品牌应用到 SharePoint 网站上的简单方法。主题的默认文件位置是网站的 _themes 文件夹。|主题是一种将自定义品牌应用到 SharePoint 网站的简单方法。 [SharePoint 2013 主题概述](http://msdn.microsoft.com/library/ae585dd3-82fe-46bb-ac93-065edc0a16f4%28Office.15%29.aspx)[如何：在 SharePoint 2013 中部署自定义主题](http://msdn.microsoft.com/library/f703df24-8e56-4e6a-bc37-95acbb3c83e8%28Office.15%29.aspx)|
|主题引擎|一组文件和功能，用于定义与组合外观相关联的外观、感觉、行为和文件。||
|用户代理字符串|浏览器向网站传递的信息，该网站能够识别从服务器发出请求的软件。|[SharePoint 2013 设计管理器设备通道](http://msdn.microsoft.com/library/a924bd7b-a5e3-41bf-b0a7-3e43945fa951%28Office.15%29.aspx)|
|用户自定义操作|能够返回有关网站、列表或网站集的自定义操作集合的 CSOM 属性。默认文件位置如下： 15\TEMPLATE\FEATURES例如：


```
<HideCustomAction
	GroupId="Galleries"
	HideActionId="Themes"
	Location="Microsoft.SharePoint.SiteSettings">

```

|[UserCustomAction 类](https://msdn.microsoft.com/zh-cn/library/office/microsoft.sharepoint.client.usercustomaction.aspx)[创建自定义操作以部署 SharePoint 外接程序](http://msdn.microsoft.com/library/bbd11f94-1798-453e-bbb0-e5eb0df8dc75%28Office.15%29.aspx)[如何：使用用户自定义操作](https://msdn.microsoft.com/zh-cn/library/office/ee538686%28v=office.14%29.aspx)[默认自定义操作位置和 ID](http://msdn.microsoft.com/library/6889686e-f6e6-4da0-bfd4-099878614980%28Office.15%29.aspx)|

## 本节内容



|**文章**|**介绍如何...**|
|:-----|:-----|
|[使用组合外观打造 SharePoint 网站品牌](use-composed-looks-to-brand-sharepoint-sites.md)|将组合外观（包括颜色、字体和背景图像）应用到您的 SharePoint 2013 和 SharePoint Online 网站。|
|[使用远程设置打造 SharePoint 页面品牌](use-remote-provisioning-to-brand-sharepoint-pages.md)|使用远程设置与 SharePoint 中的主题进行交互。|
|[使用 CSS 打造 SharePoint 页面品牌](use-css-to-brand-pages.md)|使用远程设置与 SharePoint 中的主题进行交互。|
|[使用远程设置和 CSS 自定义 SharePoint 页面](customize-a-sharepoint-page-by-using-remote-provisioning-and-css.md)|使用 CSS 自定义 SharePoint 格式文本字段和 Web 部件区域。|
|[更新现有 SharePoint 网站和页面区域的品牌](update-the-branding-of-existing-sharepoint-sites-and-page-regions.md)|自定义之后，刷新 SharePoint 页面的现有网站和区域的品牌。|
|[自定义 OneDrive for Business 网站品牌](customization-options-for-od4b-sites.md)|在 Office 365 中或使用 外接程序模型自定义 OneDrive for Business 网站。|

## 其他资源



- [SharePoint 2013 和 SharePoint Online 的品牌打造和网站设置解决方案](branding-and-site-provisioning-solutions-for-sharepoint.md)
    
- [SharePoint 页面和页面模型](sharepoint-pages-and-the-page-model.md)
    
- [SharePoint 开发和设计工具及实践](sharepoint-development-and-design-tools-and-practices.md)
    
