
# SharePoint 网站设置解决方案
将您的 SharePoint Online 和 SharePoint 2013 网站设置解决方案从完全信任代码转换为加载项模型。 

 **上次修改时间：** 2015年8月7日

 _ **适用范围：** Office 365?| SharePoint 2013?| SharePoint Add-ins?| SharePoint Online_

 **注释**  名称"SharePoint 相关应用程序"将更改为"SharePoint 外接程序"。在转换期间，某些 SharePoint 产品和 Visual Studio 工具的文档和 UI 仍可能使用术语"SharePoint 相关应用程序"。有关详细信息，请参阅 [Office 和 SharePoint 相关应用程序的新名称](05b07b04-6c8b-4b7e-bd86-e32c589dfead.md#bk_newname)。

本节中所述的示例将介绍如何使用提供程序承载的加载项来执行常规的网站设置任务，例如网站分类、创建内容类型以及管理权限和用户。本节中的文章将帮助您开始使用并引导您执行每个示例演示的主要方案。 

 **注释**  您可以使用 [Office 365 开发人员模式和做法](https://github.com/OfficeDev/PnP)项目为这些示例 [做出贡献](https://github.com/OfficeDev/PnP/wiki/contributing-to-Office-365-developer-patterns-and-practices)。您还可以使用 GitHub 上的 [指导项目](https://github.com/OfficeDev/PnP-Guidance)对这些文章做出贡献。有关示例的完整列表，请参阅 [模式和做法开发人员中心](http://dev.office.com/patterns-and-practices)。 


## 本节内容



|**文章**|**示例**|**介绍如何**|
|:-----|:-----|:-----|
|[实现 SharePoint 网站分类解决方案](implement-a-sharepoint-site-classification-solution.md)|[Core.SiteClassification](https://github.com/OfficeDev/PnP/tree/dev/Scenarios/Core.SiteClassification)|实施网站分类解决方案并利用网站策略来改进搜索优化。 |
|[在创建时修改 SharePoint 主机 Web 列表](modify-sharepoint-host-web-lists-at-creation-time.md)|[Core.EventReceiversBasedModifications](https://github.com/OfficeDev/PnP/tree/dev/Scenarios/Core.EventReceiversBasedModifications)|修改创建列表时在主机 Web 中创建的 SharePoint 列表。|
|[使用 CSOM 创建 SharePoint 内容类型](create-sharepoint-content-types-by-using-csom.md)|[Core.SPD](https://github.com/OfficeDev/PnP/tree/dev/Samples/Core.SPD)|使用继承的内容类型标识符在 Office 365 平台上创建新的内容类型，以保留所有现有属性。|
|[修改 SharePoint 网站权限并获取外部共享状态](modify-sharepoint-site-permissions-and-get-external-sharing-status.md)|[Core.SitePermissions](https://github.com/OfficeDev/PnP/tree/dev/Scenarios/Core.SitePermissions)|使用 CSOM 代码修改网站集管理员的属性，并获取网站集或租户的外部共享状态和外部用户。|
|[管理 SharePoint 用户和组](manage-sharepoint-users-and-groups.md)|[Core.GroupManagement](https://github.com/OfficeDev/PnP/tree/dev/Scenarios/Core.GroupManagement)|管理 SharePoint 网站集内的用户、组和权限。|

## 其他资源



- [SharePoint 2013 和 SharePoint Online 的品牌打造和网站设置解决方案](Branding-and-site-provisioning-solutions-for-SharePoint.md)
    
- [模式和做法开发人员中心](http://dev.office.com/patterns-and-practices)
    
- [GitHub 上的 Office 365 开发模式和做法](https://github.com/OfficeDev/PnP)
    
