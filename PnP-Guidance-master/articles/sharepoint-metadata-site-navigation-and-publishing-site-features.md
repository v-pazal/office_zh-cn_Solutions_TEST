
# SharePoint 元数据、 网站导航和发布网站功能
使用 SharePoint Online 元数据管理功能、网站导航和发布网站功能支持 SharePoint 发布网站。

 **上次修改时间：** 2015年8月12日

 _ **适用范围：** SharePoint 2013?| SharePoint Add-ins?| SharePoint Online_

 **注释**  名称"SharePoint 相关应用程序"将更改为"SharePoint 外接程序"。在转换期间，某些 SharePoint 产品和 Visual Studio 工具的文档和 UI 仍可能使用术语"SharePoint 相关应用程序"。有关详细信息，请参阅 [Office 和 SharePoint 相关应用程序的新名称](05b07b04-6c8b-4b7e-bd86-e32c589dfead.md#bk_newname)。

本文介绍如何在 SharePoint Online 中使用外接程序模型管理元数据和自定义网站导航及发布网站功能。本文还包括使用通用 Web 编程模式和库帮助自定义 SharePoint 发布网站品牌的方式。
 **为改进此内容做贡献**
您可以获取最新的更新，或为改进 [GitHub 上的此文章](https://github.com/OfficeDev/PnP-Guidance)做贡献。您还可以为改进本示例以及 [GitHub 上的其他示例](https://github.com/OfficeDev/PnP)做贡献。有关示例的完整列表，请参阅 [模式和做法开发人员中心](http://dev.office.com/patterns-and-practices)。我们欢迎您做出 [贡献](https://github.com/OfficeDev/PnP/wiki/contributing-to-Office-365-developer-patterns-and-practices)。 

## 重要的元数据、导航和发布术语和概念



|**术语或概念**|**说明和指南**|
|:-----|:-----|
|内容搜索 Web 部件|SharePoint Web 部件，它能够采用您可以轻松设置格式的方法来显示页面上的动态搜索内容。有关详细信息，请参阅：
<ul xmlns:xlink="http://www.w3.org/1999/xlink" xmlns:mtps="http://msdn2.microsoft.com/mtps" xmlns:mshelp="http://msdn.microsoft.com/mshelp" xmlns:ddue="http://ddue.schemas.microsoft.com/authoring/2003/5" xmlns:msxsl="urn:schemas-microsoft-com:xslt"><li><p><a href="https://technet.microsoft.com/zh-cn/library/jj679900(v=office.15).aspx" target="_blank">在 SharePoint Server 2013 中配置搜索 Web 部件</a></p></li><li><p><a href="https://support.office.com/zh-cn/article/Configure-a-Content-Search-Web-Part-in-SharePoint-0dc16de1-dbe4-462b-babb-bf8338c36c9a?CorrelationId=5024de0f-f4f4-436a-9c4a-b578b74b4764&amp;ui=en-US&amp;rs=en-US&amp;ad=US" target="_blank">在 SharePoint 中配置内容搜索 Web 部件</a></p></li><li><p><a href="https://technet.microsoft.com/zh-cn/library/sharepoint-online-search-service-description.aspx" target="_blank">SharePoint Online 中的搜索功能</a></p></li></ul>|
|ContentTypeId|用于递归的内容类型的唯一标识符。有关详细信息，请参阅 [ContentTypeId 类](https://msdn.microsoft.com/zh-cn/library/office/microsoft.sharepoint.client.contenttypeid.aspx) (CSOM)。|
|内容类型|元数据（列）、工作流、行为的集中式可重用的集合和信息类别的其他设置。有关信息，请参阅：
<ul xmlns:xlink="http://www.w3.org/1999/xlink" xmlns:mtps="http://msdn2.microsoft.com/mtps" xmlns:mshelp="http://msdn.microsoft.com/mshelp" xmlns:ddue="http://ddue.schemas.microsoft.com/authoring/2003/5" xmlns:msxsl="urn:schemas-microsoft-com:xslt"><li><p><a href="https://msdn.microsoft.com/zh-cn/library/office/ms196085(v=office.14).aspx" target="_blank">列</a></p></li><li><p><a href="https://msdn.microsoft.com/zh-cn/library/office/ms460224(v=office.14).aspx" target="_blank">创建内容类型</a></p></li><li><p><a href="https://msdn.microsoft.com/zh-cn/library/office/ms468437(v=office.14).aspx" target="_blank">内容类型中的自定义信息</a></p></li></ul>|
|内容类型中心|托管元数据服务应用程序有一部分是向其他网站集发布内容类型的中心网站。可允许您从某个中心位置发布、重新发布和取消发布内容类型。有关信息，请参阅：
<ul xmlns:xlink="http://www.w3.org/1999/xlink" xmlns:mtps="http://msdn2.microsoft.com/mtps" xmlns:mshelp="http://msdn.microsoft.com/mshelp" xmlns:ddue="http://ddue.schemas.microsoft.com/authoring/2003/5" xmlns:msxsl="urn:schemas-microsoft-com:xslt"><li><p><a href="https://support.office.com/zh-cn/article/Publish-a-content-type-from-a-content-publishing-hub-58081155-118d-4e7a-9cc5-d43b5dbb7d02?CorrelationId=49ebfe9f-6cf8-4f04-86a3-4d3fe23324fe&amp;ui=en-US&amp;rs=en-US&amp;ad=US" target="_blank">从内容发布中心发布内容类型</a></p></li><li><p><a href="https://support.office.com/zh-cn/article/View-error-logs-for-content-type-publishing-fa8bc6d4-f03e-4ce7-94de-5fd1c38fe32c?CorrelationId=3aaa4607-0888-417a-a0ff-8c1e44ce8167&amp;ui=en-US&amp;rs=en-US&amp;ad=US" target="_blank">查看内容类型发布的错误日志</a></p></li></ul>|
|设备通道|通过使用针对多个设备的不同设计，以多种方式呈现发布网站页面的方法。有关详细信息，请参阅 [如何：在 SharePoint 2013 中添加设备通道面板代码段](http://msdn.microsoft.com/library/612780a8-6267-49f6-a32d-33600bb5f6b4%28Office.15%29.aspx)。|
|显示模板|在使用搜索技术的 Web 部件中使用，显示模板控制对搜索索引进行查询的结果呈现。显示模板可用于所有搜索 Web 部件。有关详细信息，请参阅 [在 SharePoint Server 2013 中显示模板引用](https://technet.microsoft.com/zh-cn/library/jj944947%28v=office.15%29.aspx)。|
|托管导航|由 SharePoint 托管元数据服务（分类）驱动的网站导航。使用它来生成派生自托管元数据分类的网站导航。托管导航通常最适用于产品目录。|
|托管元数据|您可以用于定义的集中托管术语和 SharePoint 中的属性项的分层集合。您可以使用托管元数据来管理内容类型发布和创建分类。在 SharePoint 2013 和 SharePoint Online 中，托管的元数据服务可用于创建托管导航 - 由分类驱动的网站导航。有关详细信息，请参阅：
<ul xmlns:xlink="http://www.w3.org/1999/xlink" xmlns:mtps="http://msdn2.microsoft.com/mtps" xmlns:mshelp="http://msdn.microsoft.com/mshelp" xmlns:ddue="http://ddue.schemas.microsoft.com/authoring/2003/5" xmlns:msxsl="urn:schemas-microsoft-com:xslt"><li><p><a href="https://msdn.microsoft.com/zh-cn/library/office/bb897739(v=office.14).aspx" target="_blank">在 SharePoint Server 2010 (ECM) 中自定义导航控件和提供程序</a></p></li><li><p><a href="https://support.office.com/zh-cn/article/Introduction-to-managed-metadata-a180fa28-6405-4679-9ec3-81d2028c4efc?CorrelationId=2a4dbdb0-0199-40e2-ac58-568ca0a3f099&amp;ui=en-US&amp;rs=en-US&amp;ad=US" target="_blank">托管元数据简介</a></p></li><li><p><a href="https://support.office.com/zh-cn/article/Introduction-to-managed-metadata-in-SharePoint-Server-2010-b324aebd-67ab-45a8-933d-ceedb2d909ea?CorrelationId=9152b80c-9fe4-43c5-bacc-178cc49d0e8f&amp;ui=en-US&amp;rs=en-US&amp;ad=US" target="_blank">SharePoint Server 2010 中的托管元数据简介</a></p></li><li><p><a href="https://technet.microsoft.com/zh-cn/library/ee530393(v=office.14).aspx" target="_blank">托管元数据管理 (SharePoint Server 2010)</a></p></li><li><p><a href="http://msdn.microsoft.com/library/b66d4ec1-a2ef-49cc-8ca5-a6b516bff02e(Office.15).aspx" target="_blank">SharePoint 2013 中的托管元数据和导航</a></p></li><li><p><a href="http://msdn.microsoft.com/library/c9da5011-3c73-4b83-8e00-e7a03a71ed02(Office.15).aspx" target="_blank">SharePoint 2013 中的托管导航</a></p></li><li><p><a href="https://msdn.microsoft.com/zh-cn/library/office/hh147179(v=office.14).aspx" target="_blank">在 SharePoint Server 2010 中迁移托管元数据</a></p></li></ul>|
|[导航命名空间](https://msdn.microsoft.com/zh-cn/library/office/microsoft.sharepoint.client.publishing.navigation.aspx)|包含客户端对象模型 (CSOM) 类，该类对发布网站支持网站导航。 |
|[分类命名空间](https://msdn.microsoft.com/zh-cn/library/office/microsoft.sharepoint.client.taxonomy.aspx)|包含支持分类功能的 CSOM 类。有关详细信息，请参阅 [同步术语组示例 SharePoint 外接程序](Synchronize-term-groups-sample-app-for-SharePoint.md)。|
|固定|类似于重复使用术语，固定维护源术语和重复使用实例之间的共享关系。例如，共享关系可以跨越跨网站发布方案中的网站集。无论术语固定在哪里，可随时添加或删除术语，跨层次结构对操作进行更新。有关详细信息，请参阅 [如何：在 SharePoint 2013 中使用代码将术语固定到导航术语集](http://msdn.microsoft.com/library/4a2811dc-25fd-4eb2-b0ab-1edded64c556%28Office.15%29.aspx)。|
|产品目录|有关信息，请参阅以下文章：
<ul xmlns:xlink="http://www.w3.org/1999/xlink" xmlns:mtps="http://msdn2.microsoft.com/mtps" xmlns:mshelp="http://msdn.microsoft.com/mshelp" xmlns:ddue="http://ddue.schemas.microsoft.com/authoring/2003/5" xmlns:msxsl="urn:schemas-microsoft-com:xslt"><li><p><a href="http://msdn.microsoft.com/library/33f49e69-c1d3-4a6e-8887-5df683cba022(Office.15).aspx" target="_blank">SharePoint 2013 中的跨站点发布</a></p></li><li><p><a href="https://technet.microsoft.com/zh-cn/library/jj656774(v=office.15).aspx" target="_blank">在 SharePoint Server 2013 中配置跨网站发布</a></p></li></ul>|
|代码片段|您可以将 HTML 功能片段添加到 [设计管理器](http://msdn.microsoft.com/library/29834b3f-3815-4347-91d3-296387663114%28Office.15%29.aspx)生成的 HTML 文件中。例如，您可能想要将设备通道面板添加到您的发布页 - 设有相关的代码片段。以下文章提供了一些示例：
<ul xmlns:xlink="http://www.w3.org/1999/xlink" xmlns:mtps="http://msdn2.microsoft.com/mtps" xmlns:mshelp="http://msdn.microsoft.com/mshelp" xmlns:ddue="http://ddue.schemas.microsoft.com/authoring/2003/5" xmlns:msxsl="urn:schemas-microsoft-com:xslt"><li><p><a href="http://msdn.microsoft.com/library/612780a8-6267-49f6-a32d-33600bb5f6b4(Office.15).aspx" target="_blank">如何：在 SharePoint 2013 中添加设备通道面板代码段</a></p></li><li><p><a href="http://msdn.microsoft.com/library/7583b217-200c-4569-8f88-fe975c8ebd72(Office.15).aspx" target="_blank">如何：在 SharePoint 2013 中添加 Web 部件区域代码段</a></p></li><li><p><a href="http://msdn.microsoft.com/library/4beaab08-760b-408a-b768-906312779379(Office.15).aspx" target="_blank">如何：在 SharePoint 2013 中添加安全修整代码段</a></p></li></ul>|
|结构化导航|有关使用结构化导航的信息，请参阅[如何：在 SharePoint Server 2010 (ECM) 中自定义导航](https://msdn.microsoft.com/zh-cn/library/office/ms558975%28v=office.14%29.aspx)|

## 将 CSOM 用于品牌发布网站的先决条件

默认情况下，发布到面向公众的 SharePoint 本地网站的 Web 内容可供匿名用户使用。默认情况下，CSOM 和 REST 对匿名用户不可用。


 **重要信息**  此方案向本地 SharePoint 网站呈现一个潜在的严重威胁。在使用 [使用远程设置打造 SharePoint 页面品牌](Use-remote-provisioning-to-brand-SharePoint-pages.md)中所述的远程设置模型来设置发布网站品牌之前，请确保已正确地设置您网站的安全性和权限，并考虑了匿名访问的安全隐患。

如果网站管理员创建的新 Web 应用程序包括使用发布模板并且启用了匿名访问功能的网站集，则当应用程序上载到外接程序目录中时，网站的每个用户都将可以使用匿名访问。由于对本地 SharePoint 发布网站启用了匿名访问，那么如果未经过身份验证的用户导航到网站，会发生什么情况？

如果您创建 SharePoint 托管的外接程序并将客户端外接程序部件添加到项目中，请导航到"管理中心">"外接程序">"管理外接程序目录"，创建 Web 应用程序的外接程序目录，发布使用 Visual Studio 创建外接程序包的 SharePoint 托管的外接程序，然后将外接程序包上载到外接程序目录。此时，网站集管理员可以将外接程序添加到发布网站。现在，外接程序可被添加到发布网站上的页面。

如果您编辑发布网站的主页，并将外接程序发布到其上，则外接程序可按预期方式工作。选择外接程序的链接标题加载完整页面体验。如果 SharePoint 出错，就意味着匿名用户没有使用 CSOM 的权限。这是有用处的，因为默认情况下 CSOM 和 REST 对匿名用户不可用。


### 请注意，禁用"使用远程界面"权限会带来安全风险

在"网站权限"中，"需要使用远程界面权限"复选框在默认情况下处于选中状态。您可以清除"使用远程界面权限"复选框，以使匿名用户有权使用 CSOM 和 REST。这会让用户失去授予访问 SOAP、WebDAV 和 CSOM 权限的"使用远程界面权限"。清除该复选框也会删除使用 SharePoint Designer 的功能。

您可能多次希望删除使用 SharePoint Designer 的功能，但仍允许使用 CSOM。"使用远程界面权限"复选框可以让您允许匿名用户使用 CSOM 而不要求他们具有"使用远程界面权限"。当清除了"使用远程界面权限"复选框，并选择外接程序的链接标题来加载完整页面体验时，SharePoint 不会引发错误。基本错误处理代码将这种情况视为匿名用户。


 >**警告**  


### 使用 ViewFormPagesLockdown 功能

若要阻止用户访问面向公众 SharePoint 网站中的窗体页（例如，Pages/Forms/AllItems.aspx），请使用"ViewFormPagesLockdown"功能。它旨在防止用户看到"创建者"和"修改者"信息。此功能将删除对"查看应用程序页面"或"使用远程界面"的权限。当此功能处于活动状态时，用户不能转到 Pages/Forms/AllItems.aspx 并查看该库中的项目。

如果 CSOM 和 REST 可供所有匿名用户使用（假如已清除"使用远程界面权限"复选框），虽然匿名用户仍看不到浏览器中的"创建者"和"修改者"信息，但他们可以使用 CSOM 或 REST 来访问该信息。


### 安全修整

通过配置对 Web 应用程序的匿名访问，您可以指定匿名策略："拒绝写入 - 没有写入权限"。这意味着匿名访问的用户无法写入站点 - 即使使用 CSOM 或 REST 代码也无法进行。如果已对站点配置了匿名访问，则匿名用户只能看到授予他们可见的信息。 

未发布的页面在默认情况下对匿名用户不可见。匿名用户只能看到授予他们可见的列表。 


 **重要信息**  如果"使用远程界面权限"复选框处于清除状态，请使用权限模型和其他预防措施来保证匿名用户无法访问不应让他们访问的内容。


### 阻止受到拒绝服务攻击

没有通过 CSOM 进行缓存。这意味着，恶意攻击者可以同时查询列表中的数千个项目，所有这些项目都处在默认列表视图阈值下且负责 SharePoint 数据库。这可能会不断扩展甚至升级到整个拒绝服务攻击。 


### 使用仅应用策略

您可以将仅应用策略与提供程序托管的外接程序一起使用。仅应用程序策略允许外接程序执行当前用户无权执行的操作。例如，具有只读权限的用户要想写入列表，可以使用外接程序和仅应用权限对列表进行写入。

有关详细信息，请参阅 [SharePoint 外接程序的授权和身份验证](http://msdn.microsoft.com/library/bde5647a-fff1-4b51-b67b-2139de79ce4a%28Office.15%29.aspx)和 [了解 SharePoint 外接程序和 Office 外接程序的身份验证和权限](http://channel9.msdn.com/Events/Build/2013/3-603)（第 9 频道视频）。


### 实现 SSL

当您使用外接程序模型时，使用安全套接字层 (SSL) 协议来管理 Internet 上的邮件传输安全性。SSL 通过远程网站发送 HTTP 头中的访问令牌而工作，授权包含一个持有者 + base64 编码（未加密）的字符串的值。


 **重要信息**  SSL 保护您的 SharePoint 发布网站免受想要获取授权令牌并利用该访问权限的攻击者实施的攻击。


## 远程配置和发布网站

您可以使用 [远程设置实践](Use-remote-provisioning-to-brand-SharePoint-pages.md)对 SharePoint 发布网站设置品牌和其他自定义。

发布网站依赖于内容类型和将内容类型链接到页面布局和显示模板的  **ContentTypeId**。自定义和设置 SharePoint 发布页面内容依赖于此功能。而自定义发布网站设置行为的其他方面（如托管的元数据服务和托管的导航）则不依赖于  **ContentTypeId**，并在 CSOM 中受到完全支持。

其他发布网站自定义选项（如设备通道和显示模板）不需要自定义 CSOM。它们依赖于设计管理器功能、CSS 和 HTML。它们是您从头生成且不需要迁移的后设置的自定义项。


## 托管元数据

首次在 SharePoint 2010 中引入的托管元数据功能，使您可以定义可在 SharePoint 中使用的元数据标记的自定义层次结构或分类。如果您想要创建自定义网站导航，您可以使用托管的导航功能，它是基于托管的元数据基础结构而生成。

分类是对那些可依据相似性进行分组的字词、标签或术语的层次分类。 SharePoint 分类中的最小单位是术语。术语可以分组为术语集。术语集可以按相关性分组为更大的组。

有关对 SharePoint 中分类的简介信息，请参阅 [针对 SharePoint 2010 中企业托管元数据的简介](https://msdn.microsoft.com/zh-cn/library/office/ee832800%28v=office.14%29.aspx)和 [针对 SharePoint 2010 中托管元数据的简介](https://support.office.com/zh-cn/article/Introduction-to-managed-metadata-in-SharePoint-Server-2010-b324aebd-67ab-45a8-933d-ceedb2d909ea?CorrelationId=2251ec3f-51ef-4924-89cc-d828b5068851&amp;ui=en-US&amp;rs=en-US&amp;ad=US)。

SharePoint 2013 向托管元数据功能集引入了新的 API 和功能。


### 托管元数据的编程模型

对于 SharePoint 2013，托管元数据的 .NET 服务器编程模型定义在下列命名空间集中： 


- [分类命名空间](https://msdn.microsoft.com/zh-cn/library/office/microsoft.sharepoint.taxonomy.aspx)
    
- [ContentTypeSync 命名空间](https://msdn.microsoft.com/zh-cn/library/office/microsoft.sharepoint.taxonomy.contenttypesync.aspx)
    
- [Generic 命名空间](https://msdn.microsoft.com/zh-cn/library/office/microsoft.sharepoint.taxonomy.generic.aspx)
    
- [CodeBehind 命名空间](https://msdn.microsoft.com/zh-cn/library/office/microsoft.sharepoint.taxonomy.om.codebehind.aspx)
    
- [升级命名空间](https://msdn.microsoft.com/zh-cn/library/office/microsoft.sharepoint.taxonomy.upgrade.aspx)
    
- [WebServices 空间 ](https://msdn.microsoft.com/zh-cn/library/office/microsoft.sharepoint.taxonomy.webservices.aspx)
    
等效的 CSOM 类都位于 [Client.Taxonomy 命名空间](https://msdn.microsoft.com/zh-cn/library/office/microsoft.sharepoint.client.taxonomy.aspx)命名空间。

与 SharePoint 对象模型的其他方面不同，对于托管元数据，.NET 服务器编程模型类和成员与 CSOM 类和成员之间有密切的相关性。以下是一些重要区别：


- CSOM 不支持内容类型同步。
    
-  **Group** 类，该类表示 [TermStore](https://msdn.microsoft.com/zh-cn/library/office/microsoft.sharepoint.taxonomy.termstore.aspx) 类中的组织的最上层，仅在 .NET 服务器对象模型中可用。CSOM 中对应的类是 [TermGroup](https://msdn.microsoft.com/zh-cn/library/office/microsoft.sharepoint.client.taxonomy.termgroup.aspx) 类。
    
- [GroupCollection 类](https://msdn.microsoft.com/zh-cn/library/office/microsoft.sharepoint.taxonomy.groupcollection.aspx)表示 Group 对象的集合，仅在 .NET 服务器对象模型中可用。CSOM 中对应的类是 [TermGroupCollection 类](https://msdn.microsoft.com/zh-cn/library/office/microsoft.sharepoint.client.taxonomy.termgroupcollection.aspx)类。 
    
- CSOM 包括 [CustomPropertyMatchInformation](https://msdn.microsoft.com/zh-cn/library/office/microsoft.sharepoint.client.taxonomy.custompropertymatchinformation.aspx) 和 [LabelMatchInformation](https://msdn.microsoft.com/zh-cn/library/office/microsoft.sharepoint.client.taxonomy.labelmatchinformation.aspx) 类，它们将自定义属性和标签数据同步保留在服务器中。
    
下表比较了 .NET 服务器对象模型和 CSOM 对象模型中的类。



|**.NET 服务器对象模型**|**等效的 CSOM API**|
|:-----|:-----|
|[Microsoft.SharePoint.Taxonomy.ChangedGroup](https://msdn.microsoft.com/zh-cn/library/office/microsoft.sharepoint.taxonomy.changedgroup.aspx)|[Microsoft.SharePoint.Client.Taxonomy.ChangedGroup](https://msdn.microsoft.com/zh-cn/library/office/microsoft.sharepoint.client.taxonomy.changedgroup.aspx)|
|[Microsoft.SharePoint.Taxonomy.ChangedItem](https://msdn.microsoft.com/zh-cn/library/office/microsoft.sharepoint.taxonomy.changeditem.aspx)|[Microsoft.SharePoint.Client.Taxonomy.ChangedItem](https://msdn.microsoft.com/zh-cn/library/office/microsoft.sharepoint.client.taxonomy.changeditem.aspx)|
|[Microsoft.SharePoint.Taxonomy.ChangedItemCollection](https://msdn.microsoft.com/zh-cn/library/office/microsoft.sharepoint.taxonomy.changeditemcollection.aspx)|[Microsoft.SharePoint.Client.Taxonomy.ChangedItemCollection](https://msdn.microsoft.com/zh-cn/library/office/microsoft.sharepoint.client.taxonomy.changeditemcollection.aspx)|
|[Microsoft.SharePoint.Taxonomy.ChangedItemType](https://msdn.microsoft.com/zh-cn/library/office/microsoft.sharepoint.taxonomy.changeditemtype.aspx)|[Microsoft.SharePoint.Client.Taxonomy.ChangedItemType](https://msdn.microsoft.com/zh-cn/library/office/microsoft.sharepoint.client.taxonomy.changeditemtype.aspx)|
|[Microsoft.SharePoint.Taxonomy.ChangedOperationType](https://msdn.microsoft.com/zh-cn/library/office/microsoft.sharepoint.taxonomy.changedoperationtype.aspx)|[Microsoft.SharePoint.Client.Taxonomy.ChangedOperationType](https://msdn.microsoft.com/zh-cn/library/office/microsoft.sharepoint.client.taxonomy.changedoperationtype.aspx)|
|[Microsoft.SharePoint.Taxonomy.ChangedSite](https://msdn.microsoft.com/zh-cn/library/office/microsoft.sharepoint.taxonomy.changedsite.aspx)|[Microsoft.SharePoint.Client.Taxonomy.ChangedSite](https://msdn.microsoft.com/zh-cn/library/office/microsoft.sharepoint.client.taxonomy.changedsite.aspx)|
|[Microsoft.SharePoint.Taxonomy.ChangedTerm](https://msdn.microsoft.com/zh-cn/library/office/microsoft.sharepoint.taxonomy.changedterm.aspx)|[Microsoft.SharePoint.Client.Taxonomy.ChangedTerm](https://msdn.microsoft.com/zh-cn/library/office/microsoft.sharepoint.client.taxonomy.changedterm.aspx)|
|[Microsoft.SharePoint.Taxonomy.ChangedTermSet](https://msdn.microsoft.com/zh-cn/library/office/microsoft.sharepoint.taxonomy.changedtermset.aspx)|[Microsoft.SharePoint.Client.Taxonomy.ChangedTermSet](https://msdn.microsoft.com/zh-cn/library/office/microsoft.sharepoint.client.taxonomy.changedtermset.aspx)|
|[Microsoft.SharePoint.Taxonomy.ChangedTermStore](https://msdn.microsoft.com/zh-cn/library/office/microsoft.sharepoint.taxonomy.changedtermstore.aspx)|[Microsoft.SharePoint.Client.Taxonomy.ChangedTermStore](https://msdn.microsoft.com/zh-cn/library/office/microsoft.sharepoint.client.taxonomy.changedtermstore.aspx)|
|不适用。|[Microsoft.SharePoint.Client.Taxonomy.ChangeInformation](https://msdn.microsoft.com/zh-cn/library/office/microsoft.sharepoint.client.taxonomy.changeinformation.aspx)|
|不适用。|[Microsoft.SharePoint.Client.Taxonomy.CustomPropertyMatchInformation](https://msdn.microsoft.com/zh-cn/library/office/microsoft.sharepoint.client.taxonomy.custompropertymatchinformation.aspx)|
|[Microsoft.SharePoint.Taxonomy.FeatureIds](https://msdn.microsoft.com/zh-cn/library/office/microsoft.sharepoint.taxonomy.featureids.aspx)|不适用。|
|[Microsoft.SharePoint.Taxonomy.Group](https://msdn.microsoft.com/zh-cn/library/office/microsoft.sharepoint.taxonomy.group.aspx)|不适用。|
|[Microsoft.SharePoint.Taxonomy.HiddenListFullSyncJobDefinition](https://msdn.microsoft.com/zh-cn/library/office/microsoft.sharepoint.taxonomy.hiddenlistfullsyncjobdefinition.aspx)|不适用。|
|[Microsoft.SharePoint.Taxonomy.ImportManager](https://msdn.microsoft.com/zh-cn/library/office/microsoft.sharepoint.taxonomy.importmanager.aspx)|不适用。|
|[Microsoft.SharePoint.Taxonomy.Label](https://msdn.microsoft.com/zh-cn/library/office/microsoft.sharepoint.taxonomy.label.aspx)|[Microsoft.SharePoint.Client.Taxonomy.Label](https://msdn.microsoft.com/zh-cn/library/office/microsoft.sharepoint.client.taxonomy.label.aspx)|
|[Microsoft.SharePoint.Taxonomy.LabelCollection](https://msdn.microsoft.com/zh-cn/library/office/microsoft.sharepoint.taxonomy.labelcollection.aspx)|[Microsoft.SharePoint.Client.Taxonomy.LabelCollection](https://msdn.microsoft.com/zh-cn/library/office/microsoft.sharepoint.client.taxonomy.labelcollection.aspx)|
|不适用。|[Microsoft.SharePoint.Client.Taxonomy.LabelMatchInformation]()|
|[Microsoft.SharePoint.Taxonomy.MobileTaxonomyField](https://msdn.microsoft.com/zh-cn/library/office/microsoft.sharepoint.taxonomy.mobiletaxonomyfield.aspx)|[Microsoft.SharePoint.Client.Taxonomy.MobileTaxonomyField](https://msdn.microsoft.com/zh-cn/library/office/microsoft.sharepoint.client.taxonomy.mobiletaxonomyfield.aspx)|
|[Microsoft.SharePoint.Taxonomy.StringMatchOption](https://msdn.microsoft.com/zh-cn/library/office/microsoft.sharepoint.taxonomy.stringmatchoption.aspx)|[Microsoft.SharePoint.Client.Taxonomy.StringMatchOption]()|
|[Microsoft.SharePoint.Taxonomy.TaxonomyField](https://msdn.microsoft.com/zh-cn/library/office/microsoft.sharepoint.taxonomy.taxonomyfield.aspx)|[Microsoft.SharePoint.Client.Taxonomy.TaxonomyField](https://msdn.microsoft.com/zh-cn/library/office/microsoft.sharepoint.client.taxonomy.stringmatchoption.aspx)|
|[Microsoft.SharePoint.Taxonomy.TaxonomyFieldControl](https://msdn.microsoft.com/zh-cn/library/office/microsoft.sharepoint.taxonomy.taxonomyfieldcontrol.aspx)|不适用。|
|[Microsoft.SharePoint.Taxonomy.TaxonomyFieldEditor](https://msdn.microsoft.com/zh-cn/library/office/microsoft.sharepoint.taxonomy.taxonomyfieldeditor.aspx)|不适用。|
|[Microsoft.SharePoint.Taxonomy.TaxonomyFieldValue](https://msdn.microsoft.com/zh-cn/library/office/microsoft.sharepoint.taxonomy.taxonomyfieldvalue.aspx)|[Microsoft.SharePoint.Client.Taxonomy.TaxonomyFieldValue](https://msdn.microsoft.com/zh-cn/library/office/microsoft.sharepoint.client.taxonomy.taxonomyfieldvalue.aspx)|
|[Microsoft.SharePoint.Taxonomy.TaxonomyFieldValueCollection](https://msdn.microsoft.com/zh-cn/library/office/microsoft.sharepoint.taxonomy.taxonomyfieldvaluecollection.aspx)|[Microsoft.SharePoint.Client.Taxonomy.TaxonomyFieldValueCollection](https://msdn.microsoft.com/zh-cn/library/office/microsoft.sharepoint.client.taxonomy.taxonomyfieldvaluecollection.aspx)|
|[Microsoft.SharePoint.Taxonomy.TaxonomyItem](https://msdn.microsoft.com/zh-cn/library/office/microsoft.sharepoint.taxonomy.taxonomyitem.aspx)|[Microsoft.SharePoint.Client.Taxonomy.TaxonomyItem](https://msdn.microsoft.com/zh-cn/library/office/microsoft.sharepoint.client.taxonomy.taxonomyitem.aspx)|
|[Microsoft.SharePoint.Taxonomy.TaxonomyItemPicker](https://msdn.microsoft.com/zh-cn/library/office/microsoft.sharepoint.taxonomy.taxonomyitempicker.aspx)|不适用。|
|[Microsoft.SharePoint.Taxonomy.TaxonomyRights](https://msdn.microsoft.com/zh-cn/library/office/microsoft.sharepoint.taxonomy.taxonomyrights.aspx)|不适用。|
|[Microsoft.SharePoint.Taxonomy.TaxonomySession](https://msdn.microsoft.com/zh-cn/library/office/microsoft.sharepoint.taxonomy.taxonomysession.aspx)|[Microsoft.SharePoint.Client.Taxonomy.TaxonomySession](https://msdn.microsoft.com/zh-cn/library/office/microsoft.sharepoint.client.taxonomy.taxonomysession.aspx)|
|[Microsoft.SharePoint.Taxonomy.TaxonomyWebTaggingControl](https://msdn.microsoft.com/zh-cn/library/office/microsoft.sharepoint.taxonomy.taxonomywebtaggingcontrol.aspx)|不适用。|
|[Microsoft.SharePoint.Taxonomy.Term](https://msdn.microsoft.com/zh-cn/library/office/microsoft.sharepoint.taxonomy.term.aspx)|[Microsoft.SharePoint.Client.Taxonomy.Term](https://msdn.microsoft.com/zh-cn/library/office/microsoft.sharepoint.client.taxonomy.term.aspx)|
|[Microsoft.SharePoint.Taxonomy.TermCollection](https://msdn.microsoft.com/zh-cn/library/office/microsoft.sharepoint.taxonomy.termcollection.aspx)|[Microsoft.SharePoint.Client.Taxonomy.TermCollection](https://msdn.microsoft.com/zh-cn/library/office/microsoft.sharepoint.client.taxonomy.termcollection.aspx)|
|不适用。|[Microsoft.SharePoint.Client.Taxonomy.TermGroup](https://msdn.microsoft.com/zh-cn/library/office/microsoft.sharepoint.client.taxonomy.termgroup.aspx)|
|不适用。|[Microsoft.SharePoint.Client.Taxonomy.TermGroupCollection](https://msdn.microsoft.com/zh-cn/library/office/microsoft.sharepoint.client.taxonomy.termgroupcollection.aspx)|
|[Microsoft.SharePoint.Taxonomy.TermProperty](https://msdn.microsoft.com/zh-cn/library/office/microsoft.sharepoint.taxonomy.termproperty.aspx)|不适用。|
|[Microsoft.SharePoint.Taxonomy.TermPropertyToolPart](https://msdn.microsoft.com/zh-cnlibrary/office/microsoft.sharepoint.taxonomy.termpropertytoolpart.aspx)|不适用。|
|[Microsoft.SharePoint.Taxonomy.TermSet](https://msdn.microsoft.com/zh-cn/library/office/microsoft.sharepoint.taxonomy.termset.aspx)|[Microsoft.SharePoint.Client.Taxonomy.TermSet](https://msdn.microsoft.com/zh-cn/library/office/microsoft.sharepoint.client.taxonomy.termset.aspx)|
|[Microsoft.SharePoint.Taxonomy.TermSetCollection](https://msdn.microsoft.com/zh-cn/library/office/microsoft.sharepoint.taxonomy.termsetcollection.aspx)|[Microsoft.SharePoint.Client.Taxonomy.TermSetCollection](https://msdn.microsoft.com/zh-cn/library/office/microsoft.sharepoint.client.taxonomy.termsetcollection.aspx)|
|[Microsoft.SharePoint.Taxonomy.TermSetItem](https://msdn.microsoft.com/zh-cn/library/office/microsoft.sharepoint.taxonomy.termsetitem.aspx)|[Microsoft.SharePoint.Client.Taxonomy.TermSetItem](https://msdn.microsoft.com/zh-cn/library/office/microsoft.sharepoint.client.taxonomy.termsetitem.aspx)|
|[Microsoft.SharePoint.Taxonomy.TermStore](https://msdn.microsoft.com/zh-cn/library/office/microsoft.sharepoint.taxonomy.termstore.aspx)|[Microsoft.SharePoint.Client.Taxonomy.TermStore](https://msdn.microsoft.com/zh-cn/library/office/microsoft.sharepoint.client.taxonomy.termstore.aspx)|
|[Microsoft.SharePoint.Taxonomy.TermStoreCollection](https://msdn.microsoft.com/zh-cn/library/office/microsoft.sharepoint.taxonomy.termstorecollection.aspx)|[Microsoft.SharePoint.Client.Taxonomy.TermStoreCollection](https://msdn.microsoft.com/zh-cn/library/office/microsoft.sharepoint.client.taxonomy.termstorecollection.aspx)|
|[Microsoft.SharePoint.Taxonomy.TermStoreOperationException](https://msdn.microsoft.com/zh-cn/library/office/microsoft.sharepoint.taxonomy.termstoreoperationexception.aspx)|不适用。|
|[Microsoft.SharePoint.Taxonomy.TreeControl](https://msdn.microsoft.com/zh-cn/library/office/microsoft.sharepoint.taxonomy.treecontrol.aspx)|不适用。|

## 页面布局

对于发布网站，页面布局定义特定类别页面的布局。它还使用内容占位符定义可自定义的页面区域，这些占位符会由页面布局上匹配区域中的内容进行填充。页面布局通常基于自定义内容类型。内容类型定义您想要在页面上显示的自定义内容字段。通常，您将创建一个自定义内容类型，其中包含一些将映射到您或您的设计团队预先规划的页面设计的字段。

自定义字段控件可以包括文本、图像、视频或其他类型的内容。SharePoint 提供了为文章、目录、欢迎页面等内容类型。通过转至"网站设置">"网站内容类型"访问页面布局内容类型。这些默认页面布局内容类型可用作您创建的自定义内容类型的父内容类型。

所有页面布局内容类型均继承自页面内容类型。创建自定义内容类型之后，SharePoint 将显示新内容类型从页面内容类型继承的列。您可以添加新网站栏来表示您想要在您的页面布局中显示的新自定义字段。指定每个网站栏的类型。一种类型是一个值，如"single line of text"或"Full HTML"。在指定类型时，请考虑诸如应向用户提供的对字段的描述程度或控件之类的因素。

创建完将内容存储在您的页面布局中的所有字段后，可以在设计管理器中创建页面布局。有关详细信息，请参阅 [如何：在 SharePoint 2013 中创建页面布局](http://msdn.microsoft.com/library/5447e6a1-2f14-4667-81d0-7514b468be80%28Office.15%29.aspx)。创建页面布局后，可对其进行发布，方法为：前往"网站设置">"母版页和页面布局"。存在一个 HTML 文件和一个 ASPX 文件。HTML 文件是主文件，您可以使用任何 HTML 编辑器对其进行编辑。保存该文件并将其发布后，设计管理器会合并更改，并将更新后的 HTML 文件转换为 ASPX 格式，SharePoint 将使用这种格式来呈现页面。若要发布页面布局，请选择 HTML 文件并在功能区中选择"发布"。

若要创建基于新布局的页面，请转到"新页面">"页面">"页面布局"，在可用的页面布局的列表中查看新的页面布局。选择新的页面布局时，您应看到您在创建新的页面布局的新内容类型时指定的所有新字段。如果您查看页面，发现 HTML 未按您需要的方式进行呈现，则您可以编辑 HTML，然后使用设计管理器上载已更新的文件。 


## 网站导航

有四种网站导航的类型： 


- 全局导航
    
- 本地导航
    
- 结构化导航
    
- 托管导航
    
全局导航指的是导航元素，它可帮助用户从一个 SharePoint 网站移动到另一个。本地导航指的是某个 SharePoint 网站内部的导航。结构化和托管导航则有点复杂。


### 结构化导航

结构化导航是一种在 SharePoint 网站上实现导航的静态方法。它通常要匹配公司的结构，这就需要重新构建 SharePoint 网站导航。重新构建工作通常意味着移动子网站和/或页面，并刷新指向新目标的链接。

如果您公司的结构（也可以说是网站结构）长时间保持稳定的状态，那么结构化导航可能就足以用于简单浅显的网站结构。对于更深入、更复杂的网站结构，以及需要动态扩展和更改具有发布网站导航结构的公司，托管导航可能是更好的选择。有关结构化导航的详细信息，请参阅 [如何：在 SharePoint Server 2010 中自定义导航](https://msdn.microsoft.com/zh-cn/library/office/ms558975%28v=office.14%29.aspx)。


### 托管导航

托管导航是在核心发布网站和分类基础结构的基础上生成的。托管导航绑定到单个网站集。它使用术语集和术语定义全局和本地导航。例如，您可以创建整体定义全局导航的术语集，然后将术语添加到该术语集中，用于全局导航中特定的导航元素。

您可以在以下文章找到有关托管导航的详细信息：


- [SharePoint 2013 中的托管导航](http://msdn.microsoft.com/library/c9da5011-3c73-4b83-8e00-e7a03a71ed02%28Office.15%29.aspx)
    
- [SharePoint Server 2013 中的托管导航概述](https://technet.microsoft.com/zh-cn/library/dn194311%28v=office.15%29.aspx)
    
下面是发布网站的 SharePoint 导航可扩展性模型中有关类、方法和属性的一些高级要点：


-  **NavigationTerm** 和 **NavigationTermSet** 类添加特定于托管导航的属性和方法。其他状态存储在 **NavigationTerm** 类的 **CustomProperties** 属性中。
    
-  **NavigationTerm** 和 **NavigationTermSet** 类有两种模式：可编辑和只读。在 .NET 服务器对象模型中， **NavigationTerm** 对象存储在分类导航缓存中，仅可由 **TaxonomyNavigation** 静态类中的函数对其进行访问。
    
- .NET 服务器对象模型中的  **PortalSiteMapNodeProvider** 对象提供数据驱动的网站导航功能与站点地图数据源之间的接口。通常，您编写自定义站点地图节点提供程序，以将站点地图存储为 XML 文件或存储为默认情况下不受 SharePoint 支持的数据格式。
    
CSOM 包括某些唯一的类和枚举：


-  **NavigationLinkType** 枚举定义导航树中的导航节点类型。您可以指定某个节点为根节点、友好 URL 或标准链接。
    
-  **StandardNavigationScheme** 枚举可将导航标识为全局或本地导航。
    
-  **StandardNavigationSource** 枚举包括用于全局导航和本地导航的三个选项。每个选项表示对应于基本提供程序的配置的一个状态。
    
-  **StandardNavigationSettings** 类管理全局和本地导航方案。
    
下表列出了服务器对象模式及其 CSOM 等效模式中的发布导航类。



|**服务器对象模型**|**CSOM**|
|:-----|:-----|
|[Microsoft.SharePoint.Publishing.Navigation.CachedObjectSiteMapNode](https://msdn.microsoft.com/zh-cn/library/office/microsoft.sharepoint.publishing.navigation.cachedobjectsitemapnode.aspx)|不适用。|
|[Microsoft.SharePoint.Publishing.Navigation.NavigationComparer](https://msdn.microsoft.com/zh-cn/library/office/microsoft.sharepoint.publishing.navigation.navigationcomparer.aspx)|不适用。|
|不适用。|[Microsoft.SharePoint.Client.Publishing.Navigation.NavigationLinkType](https://msdn.microsoft.com/zh-cn/library/office/microsoft.sharepoint.client.publishing.navigation.navigationlinktype.aspx)|
|[Microsoft.SharePoint.Publishing.Navigation.NavigationTerm](https://msdn.microsoft.com/zh-cn/library/office/microsoft.sharepoint.publishing.navigation.navigationterm.aspx)|[Microsoft.SharePoint.Client.Publishing.Navigation.NavigationTerm](https://msdn.microsoft.com/zh-cn/library/office/microsoft.sharepoint.client.publishing.navigation.navigationterm.aspx)|
|不适用。|[Microsoft.SharePoint.Client.Publishing.Navigation.NavigationTermCollection](https://msdn.microsoft.com/zh-cn/library/office/microsoft.sharepoint.client.publishing.navigation.navigationtermcollection.aspx)|
|[Microsoft.SharePoint.Publishing.Navigation.NavigationTermSet](https://msdn.microsoft.com/zh-cn/library/office/microsoft.sharepoint.publishing.navigation.navigationtermset.aspx)|[Microsoft.SharePoint.Client.Publishing.Navigation.NavigationTermSet](https://msdn.microsoft.com/zh-cn/library/office/microsoft.sharepoint.client.publishing.navigation.navigationtermset.aspx)|
|[Microsoft.SharePoint.Publishing.Navigation.NavigationTermSetItem](https://msdn.microsoft.com/zh-cn/library/office/microsoft.sharepoint.publishing.navigation.navigationtermsetitem.aspx)|[Microsoft.SharePoint.Client.Publishing.Navigation.NavigationTermSetItem](https://msdn.microsoft.com/zh-cn/library/office/microsoft.sharepoint.client.publishing.navigation.navigationtermsetitem.aspx)|
|[Microsoft.SharePoint.Publishing.Navigation.NavigationTermSetView](https://msdn.microsoft.com/zh-cn/library/office/microsoft.sharepoint.publishing.navigation.navigationtermsetview.aspx)|[Microsoft.SharePoint.Client.Publishing.Navigation.NavigationTermSetView](https://msdn.microsoft.com/zh-cn/library/office/microsoft.sharepoint.client.publishing.navigation.navigationtermsetview.aspx)|
|[Microsoft.SharePoint.Publishing.Navigation.PortalHierarchicalDataSourceView](https://msdn.microsoft.com/zh-cn/library/office/microsoft.sharepoint.publishing.navigation.portalhierarchicaldatasourceview.aspx)|不适用。|
|[Microsoft.SharePoint.Publishing.Navigation.PortalHierarchicalEnumerable](https://msdn.microsoft.com/zh-cn/library/office/microsoft.sharepoint.publishing.navigation.portalhierarchicalenumerable.aspx)|不适用。|
|[Microsoft.SharePoint.Publishing.Navigation.PortalHierarchyData](https://msdn.microsoft.com/zh-cn/library/office/microsoft.sharepoint.publishing.navigation.portalhierarchydata.aspx)|不适用。|
|[Microsoft.SharePoint.Publishing.Navigation.PortalListItemSiteMapNode](https://msdn.microsoft.com/zh-cn/library/office/microsoft.sharepoint.publishing.navigation.portallistitemsitemapnode.aspx)|不适用。|
|[Microsoft.SharePoint.Publishing.Navigation.PortalListSiteMapNode](https://msdn.microsoft.com/zh-cn/library/office/microsoft.sharepoint.publishing.navigation.portallistsitemapnode.aspx)|不适用。|
|[Microsoft.SharePoint.Publishing.Navigation.PortalNavigation](https://msdn.microsoft.com/zh-cn/library/office/microsoft.sharepoint.publishing.navigation.portalnavigation.aspx)|不适用。|
|[Microsoft.SharePoint.Publishing.Navigation.PortalSiteMapDataSource](https://msdn.microsoft.com/zh-cn/library/office/microsoft.sharepoint.publishing.navigation.portalsitemapdatasource.aspx)|不适用。|
|[Microsoft.SharePoint.Publishing.Navigation.PortalSiteMapDataSourceSwitch](https://msdn.microsoft.com/zh-cn/library/office/microsoft.sharepoint.publishing.navigation.portalsitemapdatasourceswitch.aspx)|不适用。|
|[Microsoft.SharePoint.Publishing.Navigation.PortalSiteMapNode](https://msdn.microsoft.com/zh-cn/library/office/microsoft.sharepoint.publishing.navigation.portallistsitemapnode.aspx)|不适用。|
|[Microsoft.SharePoint.Publishing.Navigation.PortalSiteMapProvider](https://msdn.microsoft.com/zh-cn/library/office/microsoft.sharepoint.publishing.navigation.portalsitemapprovider.aspx)|不适用。|
|[Microsoft.SharePoint.Publishing.Navigation.PortalWebSiteMapNode](https://msdn.microsoft.com/zh-cn/library/office/microsoft.sharepoint.publishing.navigation.portalwebsitemapnode.aspx)|不适用。|
|[Microsoft.SharePoint.Publishing.Navigation.ProxySiteMapNode](https://msdn.microsoft.com/zh-cn/library/office/microsoft.sharepoint.publishing.navigation.proxysitemapnode.aspx)|不适用。|
|[Microsoft.SharePoint.Publishing.Navigation.SiteNavigationSettings](https://msdn.microsoft.com/zh-cn/library/office/microsoft.sharepoint.publishing.navigation.sitenavigationsettings.aspx)|不适用。|
|[Microsoft.SharePoint.Publishing.Navigation.SiteNavigationSettingsWriter](https://msdn.microsoft.com/zh-cn/library/office/microsoft.sharepoint.publishing.navigation.sitenavigationsettingswriter.aspx)|不适用。|
|[Microsoft.SharePoint.Publishing.Navigation.SPNavigationSiteMapNode](https://msdn.microsoft.com/zh-cn/library/office/microsoft.sharepoint.publishing.navigation.spnavigationsitemapnode.aspx)|不适用。|
|不适用。|[Microsoft.SharePoint.Client.Publishing.Navigation.StandardNavigationSource](https://msdn.microsoft.com/zh-cn/library/office/microsoft.sharepoint.client.publishing.navigation.standardnavigationsource.aspx)|
|不适用。|[Microsoft.SharePoint.Client.Publishing.Navigation.StandardNavigationSettings](https://msdn.microsoft.com/zh-cn/library/office/microsoft.sharepoint.client.publishing.navigation.standardnavigationsettings.aspx)|
|[Microsoft.SharePoint.Publishing.Navigation.TaxonomyNavigation](https://msdn.microsoft.com/zh-cn/library/office/microsoft.sharepoint.publishing.navigation.taxonomynavigation.aspx)|[Microsoft.SharePoint.Client.Publishing.Navigation.TaxonomyNavigation](https://msdn.microsoft.com/zh-cn/library/office/microsoft.sharepoint.client.publishing.navigation.taxonomynavigation.aspx)|
|[Microsoft.SharePoint.Publishing.Navigation.TaxonomyNavigationCacheConfig](https://msdn.microsoft.com/zh-cn/library/office/microsoft.sharepoint.publishing.navigation.taxonomynavigationcacheconfig.aspx)|不适用。|
|[Microsoft.SharePoint.Publishing.Navigation.TaxonomyNavigationCacheStatistics](https://msdn.microsoft.com/zh-cn/library/office/microsoft.sharepoint.publishing.navigation.taxonomynavigationcachestatistics.aspx)|不适用。|
|[Microsoft.SharePoint.Publishing.Navigation.TaxonomyNavigationContext](https://msdn.microsoft.com/zh-cn/library/office/microsoft.sharepoint.publishing.navigation.taxonomynavigationcontext.aspx)|不适用。|
|[Microsoft.SharePoint.Publishing.Navigation.TaxonomySiteMapNode](https://msdn.microsoft.com/zh-cn/library/office/microsoft.sharepoint.publishing.navigation.taxonomysitemapnode.aspx)|不适用。|
|[Microsoft.SharePoint.Publishing.Navigation.TaxonomySiteMapProvider](https://msdn.microsoft.com/zh-cn/library/office/microsoft.sharepoint.publishing.navigation.taxonomysitemapprovider.aspx)|不适用。|
|[Microsoft.SharePoint.Publishing.Navigation.WebNavigationSettings](https://msdn.microsoft.com/zh-cn/library/office/microsoft.sharepoint.publishing.navigation.webnavigationsettings.aspx)|[Microsoft.SharePoint.Client.Publishing.Navigation.WebNavigationSettings](https://msdn.microsoft.com/zh-cn/library/office/microsoft.sharepoint.client.publishing.navigation.webnavigationsettings.aspx)|

## 发布网站功能

SharePoint 2013 和 SharePoint Online 包括特定于 SharePoint 发布网站的几个功能：


- 设备通道使您可以将单个发布网站设计应用于多个设备和浏览器。
    
- 显示模板可以实现品牌化和自定义与搜索相关的 Web 部件的外观。
    
- 图像呈现形式定义用于在 SharePoint 2013 发布网站的网页上显示图像的尺寸。
    
您可以通过使用 CSOM 编程模型的 [发布](https://msdn.microsoft.com/zh-cn/library/office/microsoft.sharepoint.client.publishing.aspx)命名空间中的类来实现这些功能。 


### 设备通道和设备通道模板

您可以使用设备通道和设备通道面板来优化您的电话和平板电脑的网站。通过为您要针对的每个设备创建唯一通道，您可以以多种方式呈现 SharePoint 发布网站。您可以一次创作单个网站，然后将该网站和其内容映射到不同的母版页和样式表，以适应不同的目标。

通过使用 [设计管理器](http://msdn.microsoft.com/library/29834b3f-3815-4347-91d3-296387663114%28Office.15%29.aspx)创建通道。创建通道后，可以使用传入设备的用户代理字符串将其映射到移动设备或浏览器中。

设备可以归属于多个通道。在这种情况下，可以对设备所属的通道进行排名。例如，如果您创建针对智能手机的通道和针对特定设备配置的通道，您可以对这些通道进行排名，以便具有该特定配置的设备能够获取它们的通道，并且所有其他智能手机能够获取智能手机通道。

您可以在设计管理器中创建最多 10 个通道（包括默认通道）。默认通道将捕获其他通道中未捕获的所有流量。当您创建新通道时，请完成下表中列出的字段。



|**字段**|**必需还是可选？**|**说明**|
|:-----|:-----|:-----|
|名称|必需|标识要将其与其他通道区分开来的通道。|
|通道别名|必需|代码、查询字符串、cookie 或设备通道面板如何区分通道之间的差异。|
|设备包含规则|必需|直接从设备（每个设备应位于不同行上）请求的用户代理子字符串。|
|说明|可选|描述此通道所执行的操作。|
|可用|可选|当选中时，该通道使用通道的相关资产直接通信。否则，查询字符串  `?DeviceChannel=<alias>` 将预览通道中的网站。|
 **设备通道面板**

创建设备通道后，将母版页映射到每个设备通道。由于母版页的自定义越来越少，这通常是默认的母版页 (seattle.master)。如果您为一个或多个设备通道创建唯一母版页，您可以引用不同于母版页的 CSS 文件用于默认通道。您创建的页面布局将使用您创建的每个通道。设备通道面板控件可用于区分通道之间的页面布局设计。

有关详细信息，请参阅 [SharePoint 2013 设计管理器设备通道](http://msdn.microsoft.com/library/a924bd7b-a5e3-41bf-b0a7-3e43945fa951%28Office.15%29.aspx)。

设备通道面板是映射到一个或多个通道的容器控件。您可以将设备通道面板控件添加到页面布局中，该面板将控制每个通道中呈现的内容。当呈现页面时有一个或多个通道处于活动状态，那么将呈现该设备通道面板中的所有内容。设备通道面板可用于确定何时包括一个或多个特定通道的特定内容。

请考虑包含十个字段的页面布局。这些字段中有一些可用于所有通道，有一些则只在特定通道中呈现。例如，考虑仅在智能手机上呈现的移动标题横幅字段，或只呈现在台式机和平板电脑上的大型自定义边栏。

通过添加通道特定的 CSS，设备通道面板还可用于更改页面上内容的样式和位置。有关详细信息，请参阅 [如何：在 SharePoint 2013 中添加设备通道面板代码段](http://msdn.microsoft.com/library/612780a8-6267-49f6-a32d-33600bb5f6b4%28Office.15%29.aspx)和 [如何：在 SharePoint 2013 中使用 CSS 标记代码段](http://msdn.microsoft.com/library/d18d07b6-1a6b-4589-a65c-932b67cef595%28Office.15%29.aspx)。

 **用户代理字符串和设备通道**

用户代理字符串是标识浏览器的数据的一个小的字符串。此信息可以发送到服务器，用于标识用户代理。设备通道将根据用户浏览使用的设备（或浏览器）的用户代理字符串（或子字符串）向对应的设备通道分配一个请求。前端 Web 开发人员创建并设置用来捕获流量的通道。有关详细信息，请参阅 [Windows Internet Explorer 会将什么内容报告为用户代理字符串？](https://msdn.microsoft.com/zh-cn/library/cc817582.aspx)

 **设备通道的解析顺序**

当您创建多个通道时，按照您要处理的顺序对它们进行排序。使用其中包括匹配用户代理字符串的设备包含规则的第一个通道。下表显示了此规则的一个示例。



|**通道**|**顺序 1**|**顺序 2**|
|:-----|:-----|:-----|
|1|Windows Phone 8|Windows Phone|
|2|Windows Phone|Windows Phone 8|
|3|默认值|默认值|
如果顺序 1 处于活动状态，则从 Windows 8 电话请求页面的用户将收到标记为 Windows Phone 8 的设备通道 1。使用其他 Windows 手机的用户将使用通道 2，其他所有用户将使用通道 3。但是，使用顺序 2，从 Windows 8 电话请求页面的用户将始终收到标记为 Windows Phone 的设备通道 1，并且不会使用为其指定的设备通道。

定义和排序设备通道后，您可以将不同母版页应用于每个通道中。默认情况下，所有通道都将使用默认通道的母版页。 


 **注释**  CSOM 不包括用于操作设备通道和设备通道面板的公用 API。


### 显示模板

SharePoint 发布网站使用显示模板来控制在搜索结果中显示哪些托管属性，以及它们在 Web 部件中显示的方式。仅搜索 Web 部件使用显示模板；内容查询 Web 部件不是一个搜索 Web 部件，且不使用显示模板。

下表列出显示模板的类型，SharePoint 将以该顺序应用这些模板类型。 



|**显示模板**|**备注**|
|:-----|:-----|
|控件显示模板|将应用于整个 Web 部件中，因此首先是 SharePoint 应用它，并且只执行一次。它提供 HTML，可用于构建显示搜索结果的整体布局。例如，控件显示模板可能会提供用于标题和列表开头和结尾的 HTML。此模板仅在 Web 部件中呈现一次。 |
|组显示模板|应用第二个，每个组应用一次，用于搜索结果 Web 部件。 |
|项目显示模板|如果没有应用筛选器显示模板，则应用最后一个。将项目显示模板应用于每个项目。此模板确定结果集中每个项目在 Web 部件中的显示方式。例如，它可能提供相关 HTML 用于纯文本列表项、包含图片的列表项，或那些设置其他链接和摘要说明信息的块格式以帮助提供更多搜索结果上下文的列表项。 |
SharePoint 将显示模板存储在母版页样式库的显示模板文件夹中。每个显示模板与母版页样式库中的内容类型相关联。若要在使用映射的驱动器时标识每个显示模板文件的内容类型，请使用文件名称。

转换并将母版页和页面布局从 HTML 更新到 JavaScript 的事件接收器还会将显示模板从 HTML 转换到 Javascript。转换和正在进行的同步是单向进行的；它不会从 JavaScript 转换回 HTML。


 **注释**  CSOM 不包括用于操作显示模板的公用 API。

 **显示模板结构**

每个显示模板包含： 


- 一个标题。
    
- 一个包含由  `<mso:CustomDocumentProperties>` 标记绑定的自定义元素的标头。
    
- 一个包含脚本块的  `<body>` 标记。
    
- 一个  `<div>` 标记。
    
自定义文档属性向 SharePoint 提供有关显示模板的重要信息。每个显示模板与由  `<ContenTypeId>` 标识的内容类型相关联。您可以设置其他属性，以确定是否在 Web 部件的可用显示模板列表中隐藏或显示模板、用于 JavaScript 托管属性映射的 HTML，及在其中使用显示模板的上下文；确定 .js 文件当前是否与显示模板 HTML 相关联、是否成功从 HTML 转换到 JavaScript，或是否产生了警告和错误。

从  `<script>` 标记内部，您可以引用外部 CSS 或主显示模板 HTML 文件以外的 JavaScript 文件。

如果母版页样式库中要求内容审批，则所有 CSS、JavaScript 和其他资源文件必须首先进行发布，然后才能供母版页和页面布局使用。有关详细信息，请参阅 [要求审批网站列表或库中的项目](https://support.office.com/zh-cn/article/Require-approval-of-items-in-a-site-list-or-library-cd0761c4-8c3f-4ea2-9435-13c28aa23d08?CorrelationId=4efce7db-3017-4f50-854d-1f07eaf6bc16&amp;ui=en-US&amp;rs=en-US&amp;ad=US)。 `<div>` 标记包含与显示模板 HTML 文件的名称匹配的 ID。将 CSS 或您要包含的可自定义如何显示此 Web 部件的 JavaScript 放置在 `<div>` 块中。

 **显示模板处理**

SharePoint 定义 HTML 文件中的显示模板和 JavaScript。如果在设计管理器中更改包含显示模板定义的 HTML 文件并保存更改，SharePoint 会将更改编译到同名的一个 JavaScript 文件中。SharePoint 使用此 JavaScript 文件在页面上呈现 Web 部件。


 **重要信息**  不要编辑包含显示模板定义的 JavaScript 文件。仅更新 HTML 文件。转换过程需要 HTML 文件与 XML 兼容。例如，使用  **<br>**，而不是  **<br/>**。有关详细信息，请参阅 [如何：在 SharePoint 2013 中将 HTML 文件转换为母版页](http://msdn.microsoft.com/library/a76ab289-3256-45de-ac63-d5112a74e3c7%28Office.15%29.aspx)。

 **创建新的显示模板**

创建新的显示模板最简单的方法是修改现有模板。不同的显示模板更改不同的与搜索相关的 Web 部件的外观，包括内容搜索 Web 部件、精简 Web 部件、分类精简 Web 部件和搜索结果 Web 部件。有关详细信息，请参阅：


- [SharePoint 2013 Design Manager 显示模板](http://msdn.microsoft.com/library/1a782bac-48ee-4baf-8751-0f943a306e0f%28Office.15%29.aspx)
    
- [在 SharePoint Server 2013 中配置搜索 Web 部件](https://technet.microsoft.com/zh-cn/library/jj679900%28v=office.15%29.aspx)
    
- [在 SharePoint Server 2013 中配置精简 Web 部件的属性](https://technet.microsoft.com/zh-cn/library/gg549985%28v=office.15%29.aspx)
    
- [在 SharePoint Server 2013 中显示模板引用](https://technet.microsoft.com/zh-cn/library/jj944947%28v=office.15%29.aspx)
    
 **可以在显示模板中使用的属性**

在开始确定在显示模板中可以使用的属性之前，找到您想要由此生成的现有显示模板，然后使用新名称将其保存。显示模板代码位于  `<mso:ManagedPropertyMapping>` 标记中。




```
<mso:ManagedPropertyMapping msdt:dt="string">'Picture URL'{Picture URL}:'PublishingImage;PictureURL;PictureThumbnailURL','Link URL'{Link URL}:'Path','Line 1'{Line 1}:'Title','Line 2'{Line 2}:'Description','Line 3'{Line3}:'','SecondaryFileExtension','ContentTypeId'</mso:ManagedPropertyMapping>
```

接下来，打开"网站设置">"搜索架构"，然后搜索您想要包含在显示模板中的托管属性筛选器框中的列名称。选择托管属性，然后编辑并复制属性名称。 




```
<mso:ManagedPropertyMapping msdt:dt="string">'Picture URL'{Picture URL}:'PublishingImage;PictureURL;PictureThumbnailURL','Link URL'{Link URL}:'Path','Line 1'{Line 1}:'Title','Line 2'{Line 2}:'Description','Line 3'{Line3}:'','owsTXTPrice','owsTXTColor'</mso:ManagedPropertyMapping>
```


 **注释**  在此示例中，当搜索在获取有关  **PublishingImage**、 **PictureURL** 或 **PictureThumbnailURL** 的结果时， **PictureURL** 将映射到现存的第一个托管属性。


### 图像呈现形式

图像呈现形式定义用于在 SharePoint 2013 发布网站的网页上显示图像的尺寸。可以使用 CSOM 来实例化和操作图像呈现形式。您可以通过使用 [ImageRendition](https://msdn.microsoft.com/zh-cn/library/office/microsoft.sharepoint.publishing.imagerendition.aspx) 类，指定元数据，如高度、宽度、名称和图像呈现形式的版本。您可以使用 [SiteImageRenditions](https://msdn.microsoft.com/zh-cn/library/office/microsoft.sharepoint.client.publishing.siteimagerenditions.aspx) 类中的方法和属性，从网站集读取和写入图像呈现形式。

有关详细信息，请参阅 [SharePoint 2013 设计管理器图像呈现形式](http://msdn.microsoft.com/library/d08a74c0-5674-4f26-8646-11ea1f081c85%28Office.15%29.aspx)。


## SharePoint 和常见的 Web 编程技术

在设计发布网站时，SharePoint 设计人员和开发人员经常需要在 SharePoint 中使用标准 Web 编程技术。您可以使用快速响应设计、自适应设计或设备通道和快速响应设计一起使用。


### 快速响应设计

利用设备通道，可以一次构建网站，然后将其指定到多个设备和浏览器。Web 开发社区通常使用反应迅速的设计和"流体网格"方法来管理布局的呈现方式并设计正确呈现在任何浏览器或设备上的网站。在快速响应设计中，页面上的元素将重新排列自身来满足用户的设备和屏幕方向。

快速响应设计基于 CSS3 中的媒体查询功能。它使用媒体查询来匹配设备的显示宽度，然后将样式应用在客户端上来呈现内容。媒体查询让设计人员能够指定特定的网站属性，如屏幕宽度。可以使用媒体查询创建灵活的布局和图像，并有条件地调用 CSS 文件的备选方法。

有关详细信息和示例，请参阅 [响应 SharePoint](http://responsivesharepoint.codeplex.com/) 和 [在 SharePoint 2013 上实现您的快速响应设计](http://blogs.msdn.com/b/sharepointdev/archive/2013/04/01/implementing-your-responsive-designs-on-sharepoint-2013.aspx)。


### 自适应设计

自适应 Web 设计（有时称为自适应 Web 传递）类似于快速响应 Web 设计。自适应设计侦听设备或浏览器，并且选择最佳的方式呈现页面。 

SharePoint 2013 中的设备通道功能是一种自适应设计。它基于页面布局、每个设备通道的规格和定义在设备通道面板中的顺序，将自适应布局传递到每个设备。 


### 设备通道与快速响应设计在一起

您可以结合使用设备通道和快速响应 Web 设计技术来构建快速响应的面向公众的 SharePoint 发布网站。请考虑为设备（如手机和平板电脑）创建一个自定义母版页，并另外创建一个用于 Web 浏览器，然后将每一个都与一个设备通道相关联。之后，使用流畅的网格、灵活的图像和 CSS3 媒体查询，为您的网站需要支持的每个设备和浏览器打造出最佳查看体验。


## 将 jQuery 添加到 SharePoint 网站

您可以在网站级别、页面级别，或页面的各部分（如某个 SharePoint 页面区域或已添加到页面布局的 Web 部件）中将 jQuery 添加到 SharePoint 网站。

您可以使用自定义操作，从文档库中加载 jQuery。如果您需要使 jQuery 对 SharePoint 网站中的所有页均可用，则执行此操作。此方法非常灵活，但难于控制，并且会影响到的网站设计人员和管理员。您可以在文档库中存储和维护 JavaScript文件，但它们也会意外被修改或删除。为此，我们不建议此方法。

您也可以使用 [ScriptLinkControl](https://msdn.microsoft.com/zh-cn/library/office/microsoft.sharepoint.webcontrols.scriptlink.aspx) 从 SharePoint 根加载 jQuery。您可以使用控件来插入运行在远程网站上的脚本，并修改这些脚本，而不必接触 SharePoint 安装。当您想要在应用程序页上或显示在页面上的 Web 部件中使用 jQuery， **ScriptLinkControl** 方法会很有用。虽然使用此选项进行设置速度太慢并且还会影响性能，因为一次只能将 jQuery 添加到一个页面，但是将 jQuery 文件部署到 SharePoint 规则可绕过其他旧版的要求。这将有助于您将您的 SharePoint 2013 完全信任代码 (FTC) 解决方案迁移到 CSOM，并且该迁移中包括了移动和重构自定义 JavaScript 和 jQuery 行为。

最后，您可以使用内容编辑器 Web 部件从内容交付网络 (CDN) 中加载 jQuery。这有助于您将 jQuery 添加到一个或几个页面，包括 Wiki 和 Web 部件页。因为您正在从 CDN 加载 jQuery 文件，所以您不需要将额外的文件存储在 SharePoint 服务器上，并且用户可获得 jQuery 文件的分布式缓存版本的好处。SharePoint 从 CDN 调用 jQuery 文件，您可以将您创作的自定义 jQuery 代码添加到内容编辑器 Web 部件中。 


## 使用 ASP.NET MVC 5 构建 SharePoint 提供程序托管的外接程序

您可以使用 SharePoint 中的模型-视图-控制器 (MVC) 模式构建自定义提供程序托管的外接程序。此模型可将应用程序分为三个互连的部分。这将依据用户查看和接受的方式对信息的内部表示形式进行划分。模型表示软件、视图（通常为 UI 元素）和控制器（它们是连接模型和视图的类）的基础结构。 

您可以在 SharePoint 网站母版页内容中对 ASP.NET MVC 内容进行换行。事实上，您可以使用 Office 365 API 和 ASP.NET MVC 5 创建一个 SharePoint 2013 外接程序。 

用于 SharePoint 开发的 MVC 的 API 定义在  `Filters\SharePointContextFilterAttribute.cs` 和 `SharePointContext.cs` 中。这些 API 自动换行 Web 项目为在单个调用中实现与 SharePoint 的无缝通信所采取的步骤，从而简化了您需要实现的逻辑。

当从 SharePoint 重定向到您的远程 Web 应用程序（如主机 Web URL）时，SharePoint 上下文筛选器属性执行额外的处理，以获取标准的信息。它还可以确定外接程序是否需要被重定向到 SharePoint，以供用户进行登录（例如，书签）。您可以将此筛选器应用到控制器或到视图。SharePoint 上下文类封装所有 SharePoint 中的信息，以便您可以创建外接程序 Web 和主机 Web 的特定上下文并与 SharePoint 进行通信。

有关详细信息，请参阅 [了解 ASP.NET MVC](http://www.asp.net/mvc/overview) 和 [引入对 SharePoint 外接程序的 MVC 支持](http://blogs.msdn.com/b/officeapps/archive/2013/07/09/introducing-mvc-support-for-apps-for-sharepoint.aspx)。


## 其他资源



- [SharePoint 2013 和 SharePoint Online 的品牌打造和网站设置解决方案](Branding-and-site-provisioning-solutions-for-SharePoint.md)
    
- [如何：在 SharePoint 2013 中将样式应用到页面字段](http://msdn.microsoft.com/library/e227613d-0e4d-4312-924d-bb55e1fe4293%28Office.15%29.aspx)
    
- [如何：在 SharePoint 2013 中创建页面布局](http://msdn.microsoft.com/library/5447e6a1-2f14-4667-81d0-7514b468be80%28Office.15%29.aspx)
    
- [如何：在 SharePoint 2013 中为基于目录的网站定制页面布局](http://msdn.microsoft.com/library/21d8db99-73b3-4429-b6cb-04e375af9f55%28Office.15%29.aspx)
    
- [如何：将网络驱动器映射到 SharePoint 2013 母版页样式库](http://msdn.microsoft.com/library/7d416f6e-2471-4d03-97ae-4e8d907784c6%28Office.15%29.aspx)
    
