
# 适用于 SharePoint 2013 和 SharePoint Online 的复合企业外接程序
使用复合企业外接程序将您的 SharePoint 解决方案与业务流程和技术相集成。确定对于您的解决方案正确的选择是 SharePoint 托管的外接程序还是提供程序托管的外接程序。

 **上次修改时间：** 2015年8月12日

 _ **适用范围：** SharePoint 2013?| SharePoint Add-ins?| SharePoint Online_

 **注释**  名称"SharePoint 相关应用程序"将更改为"SharePoint 外接程序"。在转换期间，某些 SharePoint 产品和 Visual Studio 工具的文档和 UI 仍可能使用术语"SharePoint 相关应用程序"。有关详细信息，请参阅 [Office 和 SharePoint 相关应用程序的新名称](05b07b04-6c8b-4b7e-bd86-e32c589dfead.md#bk_newname)。

复合企业外接程序是与您的业务流程和业务线 (LOB) 技术（如数据库和 Web 服务）紧密集成的外接程序。这些外接程序通常包含与用户和其他技术的大量复杂交互。
本节中所述的示例复合企业外接程序提供了可用于将技术和流程与 SharePoint 2013 外接程序模型相集成的构建块。

 **注释**  您可以使用 [Office 365 开发人员模式和做法](https://github.com/OfficeDev/PnP)项目为这些示例 [做出贡献](https://github.com/OfficeDev/PnP/wiki/contributing-to-Office-365-developer-patterns-and-practices)。您还可以使用 GitHub 上的 [指导项目](https://github.com/OfficeDev/PnP-Guidance)对这些文章做出贡献。有关示例的完整列表，请参阅 [模式和做法开发人员中心](http://dev.office.com/patterns-and-practices)。 


## SharePoint 托管的外接程序与提供程序托管的外接程序

在创建复合企业外接程序之前，您需要首先确定外接程序将在何处托管。当您可以将要求限定在可以使用 JavaScript 处理的单站点实施时，SharePoint 托管的外接程序运行效果最佳。提供程序托管的外接程序更适用于更复杂的业务要求。

下表总结了在确定在何处托管外接程序时需要考虑的因素。



|**SharePoint 托管的外接程序**|**提供程序托管的外接程序**|
|:-----|:-----|
|使用 JavaScript 可以执行您需要的所有操作。|您需要使用除 JavaScript 以外的语言。|
|外接程序不需要跨多个网站执行任何工作，例如，团队日历外接程序和精选新闻循环推送器。|外接程序需要访问信息并跨多个网站执行操作。例如，网站集配置外接程序。|
|内容为敏感信息，需完全保存在 SharePoint 中并保持安全。|外接程序需与其他业务线技术集成。|
||外接程序需要提升的权限，可通过仅外接程序策略实现。|
||外接程序需要高度自定义的 UI。|

## 本节内容



|**文章**|**示例**|**介绍如何...**|
|:-----|:-----|:-----|
|[将 InfoPath 表单迁移到 SharePoint 2013](migrate-infopath-forms-to-sharepoint.md)||将您的 InfoPath 2013 表单迁移到其他受支持的技术。|
|[SharePoint Online 中的数据存储选项](data-storage-options-in-sharepoint-online.md)|[Core.DataStorageModels](https://github.com/OfficeDev/PnP/tree/dev/Samples/Core.DataStorageModels)|使用不同类型的存储模型来存储 SharePoint Online 数据。|
|[公司事件外接程序与 SharePoint 的集成](corporate-app-event-registration-with-sharepoint.md)|[BusinessApps.CorporateEventsApp ](https://github.com/OfficeDev/PnP/tree/dev/Solutions/BusinessApps.CorporateEventsApp)|使用提供程序托管的外接程序来实施复杂的业务任务。|
|[从 SharePoint 工作流调用 Web 服务](call-web-services-from-sharepoint-workflows.md)|[Workflow.CallCustomService](https://github.com/OfficeDev/PnP/tree/dev/Samples/Workflow.CallCustomService)[Workflow.CallServiceUpdateSPViaProxy](https://github.com/OfficeDev/PnP/tree/dev/Samples/Workflow.CallServiceUpdateSPViaProxy)[Workflow.AssociateToHostWeb](https://github.com/OfficeDev/PnP/tree/dev/Samples/Workflow.AssociateToHostWeb)|使用提供程序托管的外接程序调用包含业务数据的远程 Web 服务。|

## 其他资源



- [Office 365 开发模式和做法解决方案指南](office-365-development-patterns-and-practices-solution-guidance.md)
    
- [模式和做法开发人员中心](http://dev.office.com/patterns-and-practices)
    
- [GitHub 上的 Office 365 开发模式和做法](https://github.com/OfficeDev/PnP)
    
