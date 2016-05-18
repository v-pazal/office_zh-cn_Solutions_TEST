
# 将 InfoPath 表单迁移到 SharePoint 2013
将您的 SharePoint 外接程序中的 InfoPath 表单迁移到其他受支持的解决方案，例如 Access 应用程序、沙盒解决方案或外接程序模型。

 **上次修改时间：** 2015年8月7日

 _ **适用范围：** SharePoint 2013?| SharePoint Add-ins?| SharePoint Online_

 **注释**  名称"SharePoint 相关应用程序"将更改为"SharePoint 外接程序"。在转换期间，某些 SharePoint 产品和 Visual Studio 工具的文档和 UI 仍可能使用术语"SharePoint 相关应用程序"。有关详细信息，请参阅 [Office 和 SharePoint 相关应用程序的新名称](05b07b04-6c8b-4b7e-bd86-e32c589dfead.md#bk_newname)。

如果您使用 InfoPath 作为在外接程序中创建表单的基础，那么现在应该开始考虑将表单迁移到其他解决方案。虽然 InfoPath 当前受支持，但 InfoPath 2013 是 InfoPath 桌面客户端的最后一个版本，SharePoint 2013 中的 InfoPath Forms Services 是 InfoPath Forms Services 的最后一个版本。截至到 2023 年，SharePoint 2013 中的 InfoPath Forms Services 客户端版本和本地部署版本完全受支持。至少截至到 Office 的下一主要版本，表单服务在 Office 365 中受支持。
要替换您的 InfoPath 表单，您可以选择以下备选方法之一：

- 使用 Access 应用程序
    
- 将 InfoPath 表单转换为沙盒解决方案。
    
- 将复杂的行为转移到外接程序模型。
    
我们建议使用前两个解决方案，这样，不知道如何编写和部署基于代码的备选方法的信息工作人员也可以实施这两个解决方案。下表介绍了每种备选方法最适合的方案。
 **SharePoint 2013 中的 InfoPath 的备选方法**


|**备选方法**|**方案**|
|:-----|:-----|
|Access 应用程序|此选项支持处理多个 Access 表、Excel 表和/或 SharePoint 列表包含的关系数据的多个表单。|
|将 InfoPath 转换为沙盒解决方案|如果您的表单很简单，不需要任何服务器组件，并且不会调用任何外部 Web 服务，这就很可行。|
|转换为外接程序模型|您可以将由扩展代码驱动的复杂表单转换为提供程序托管的外接程序。此选项需要具有开发人员资源。|
 **为改进此内容做贡献**
您可以获取最新的更新，或为改进 [GitHub 上的此文章](https://github.com/OfficeDev/PnP-Guidance/blob/master/articles/Migrate-InfoPath-forms-to-SharePoint.md)做贡献。您还可以为改进本示例以及 [GitHub 上的其他示例](https://github.com/OfficeDev/PnP)做贡献。有关示例的完整列表，请参阅 [模式和做法开发人员中心](http://dev.office.com/patterns-and-practices)。我们欢迎您做出 [贡献](https://github.com/OfficeDev/PnP/wiki/contributing-to-Office-365-developer-patterns-and-practices)。 

## 其他资源



- [Office 365 开发模式和做法解决方案指南](office-365-development-patterns-and-practices-solution-guidance.md)
    
- [GitHub 上的 Office 365 开发模式和做法](https://github.com/OfficeDev/PnP)
    
