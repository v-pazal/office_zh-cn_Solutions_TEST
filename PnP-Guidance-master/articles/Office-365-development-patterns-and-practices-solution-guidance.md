
# Office 365 开发模式和做法解决方案指南
Office 365 开发人员模式和做法程序提供了示例和文档，以帮助您将开发解决方案从完全信任代码转换为外接程序模型。

 **上次修改时间：** 2015年8月7日

 _ **适用范围：** Office 365?| SharePoint 2013?| SharePoint Add-ins?| SharePoint Online_

 **注释**  名称"SharePoint 相关应用程序"将更改为"SharePoint 外接程序"。在转换期间，某些 SharePoint 产品和 Visual Studio 工具的文档和 UI 仍可能使用术语"SharePoint 相关应用程序"。有关详细信息，请参阅 [Office 和 SharePoint 相关应用程序的新名称](05b07b04-6c8b-4b7e-bd86-e32c589dfead.md#bk_newname)。

您可以使用 [Office 365 开发人员模式和做法](https://github.com/OfficeDev/PnP)项目中的代码示例和指南，以帮助您为您的组织或客户开发业务解决方案。此项目包括代码示例，向您显示如何使用提供程序承载的外接程序应对关键方案，另外还包括说明外接程序模型开发的最佳做法的其他资源。本节中的文章将帮助您开始使用并引导您执行示例中显示的方案。

 **注释**  您可以使用 [Office 365 开发人员模式和做法](https://github.com/OfficeDev/PnP)项目为这些示例 [做出贡献](https://github.com/OfficeDev/PnP/wiki/contributing-to-Office-365-developer-patterns-and-practices)。您还可以使用 GitHub 上的 [指导项目](https://github.com/OfficeDev/PnP-Guidance)对这些文章做出贡献。有关示例的完整列表，请参阅 [模式和做法开发人员中心](http://dev.office.com/patterns-and-practices)。 


## 本节内容



|**阅读此内容**|**如果您要**|
|:-----|:-----|
|[品牌打造和网站设置](Branding-and-site-provisioning-solutions-for-SharePoint.md)|使用 SharePoint 外接程序设置和管理 SharePoint 网站品牌。|
|[复合企业加载项](Composite-buisness-apps-for-SharePoint.md)|使用复合企业外接程序将您的 SharePoint 解决方案与业务流程和技术相集成。 |
|[企业内容管理 (ECM) 解决方案](Enterprise-Content-Management-solutions-for-SharePoint-2013-and-SharePoint-Online.md)|执行常见的 ECM 任务，如设置网站策略、上载文件、同步术语组等。|
|[本地化解决方案](localization-solutions-for-sharepoint-2013-and-sharepoint-online.md)|本地化您的 SharePoint 网站内容和 UI 文本。|
|[搜索解决方案](search-solutions-in-sharepoint-2013-and-sharepoint-online.md)|了解有关 SharePoint 搜索体系结构、搜索 API 和搜索外接程序的信息。|
|[Transform farm solutions to the SharePoint Add-in model](Transform-farm-solutions-to-the-SharePoint-app-model.md)|将服务器场解决方案转换为 SharePoint 外接程序 模型。|
|[用户配置文件解决方案](user-profile-solutions-for-sharepoint.md)|使用 SharePoint 用户配置文件数据。|
|[将自定义功能区添加到 SharePoint 网站](Add-a-custom-ribbon-to-your-SharePoint-site.md)|添加或删除 SharePoint 网站上的自定义功能区，使用嵌入式 JavaScript 技术来处理自定义功能区的事件。|
|[在运行时使用 OAuth 授权提供程序托管的外接程序用户](authorize-provider-hosted-add-in-users-at-run-time-by-using-oauth.md)|运行时在提供程序承载的外接程序中使用 OAuth 提供对 SharePoint 资源的授权访问。|
|[连接 SharePoint 加载项部件](Connect-SharePoint-app-parts-by-using-SignalR.md)|使用 SignalR 在 SharePoint外接程序部件之间实施实时通信。 |
|[在 SharePoint 中创建远程计时器作业](create-remote-timer-jobs-in-sharepoint.md)|创建远程计时器作业，以执行后台任务来管理或控制您的环境。|
|[SharePoint 提供程序托管的外接程序中的跨域图像](cross-domain-images-in-sharepoint-provider-hosted-add-ins.md)|在外接程序承载的应用程序中跨域使用图像。|
|[自定义 SharePoint UI](Customize-your-SharePoint-site-UI-by-using-JavaScript.md)|使用 JavaScript 更新 SharePoint 网站的 UI。|
|[SharePoint 外接程序中的提升权限](elevated-privileges-in-sharepoint-add-ins.md)|使用仅应用程序策略或服务帐户来提升 SharePoint 外接程序中的特权。|
|[处理 SharePoint Online 限制](Handle-SharePoint-Online-throttling-by-using-exponential-back-off.md)|了解如何使用指数退避技术处理 SharePoint Online 中的限制。|
|[在 Office 365 中设置网站集的外部共享](Set-external-sharing-on-site-collections-in-Office-365.md)|允许外部用户访问您的网站集。|
|[使用 ChangeQuery 和 ChangeToken 查询 SharePoint 更改日志](query-sharepoint-change-log-with-changequery-and-changeToken.md)|查询 SharePoint 更改日志，查看对 SharePoint 内容数据库、网站集、网站或列表所做的更改。|
|[在 SharePoint 中上载 Web 部件](upload-web-parts-in-sharepoint.md)|为您的用户部署预配置的标准 SharePoint Web 部件。|
|[在 SharePoint 外接程序中使用异步操作](use-asynchronous-operations-in-sharepoint-add-ins.md)|在 SharePoint 外接程序中使用 Azure WebJobs 实施异步操作。|
|[Use Microsoft Azure WebJobs with Office 365](Use-Microsoft-Azure-WebJobs-with-Office-365.md)|使用 Azure WebJobs 实施可以访问 SharePoint Online 的计时器作业。|
|[使用远程事件接收器](Use-remote-event-receivers-in-SharePoint.md)|将提供程序承载的外接程序与远程事件接收器结合使用，以处理 AppInstalled 和 AppUninstalling 事件。 |



## 其他资源



- [Office 开发](https://msdn.microsoft.com/zh-cn/library/office/dn467914%28v=office.15%29.aspx)
    
- [模式和做法开发人员中心](http://dev.office.com/patterns-and-practices)
    
- [GitHub 上的 Office 365 开发模式和做法](https://github.com/OfficeDev/PnP)
    
- [在 Office 365 平台上开发](https://msdn.microsoft.com/zh-cn/office/office365/platform-development-overview)
    
