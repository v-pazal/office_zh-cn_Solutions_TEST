
# 使用指数退避处理 SharePoint Online 限制
了解如何使用指数退避技术处理 SharePoint Online 中的限制。 

 **上次修改时间：** 2015年5月21日

 _ **适用范围：** Office 365?| SharePoint Add-ins?| SharePoint Online?| SharePoint Server 2013_

 **注释**  名称"SharePoint 相关应用程序"将更改为"SharePoint 外接程序"。在转换期间，某些 SharePoint 产品和 Visual Studio 工具的文档和 UI 仍可能使用术语"SharePoint 相关应用程序"。有关详细信息，请参阅 [Office 和 SharePoint 相关应用程序的新名称](05b07b04-6c8b-4b7e-bd86-e32c589dfead.md#bk_newname)。

SharePoint Online 使用限制来阻止用户过度消耗资源。如果用户运行 CSOM 或 REST 代码时超过使用限制，SharePoint Online 会限制用户在一段时间内无法再发送任何请求。 
[Office 365 开发人员模式和做法存储库](https://github.com/OfficeDev/PnP)中的 [Core.Throttling](https://github.com/OfficeDev/PnP/tree/master/Samples/Core.Throttling) 代码示例演示了如何实现指数退避技术来处理 SharePoint Online 中的限制。如果您在 SharePoint Online 中受限，指数退避技术会等待更长一段时间（逐渐增加），然后再重试受限的代码。
有关在 SharePoint Online 中受限制（例如，原因、限制等）以及 Core.Throttling 代码示例的说明的详细信息，请参阅 [如何：避免在 SharePoint Online 中受限制或被阻止](https://msdn.microsoft.com/library/office/dn889829.aspx)。 
此外，在 [ClientContextExtensions.cs](https://github.com/OfficeDev/PnP/blob/master/OfficeDevPnP.Core/OfficeDevPnP.Core/AppModelExtensions/ClientContextExtensions.cs) 示例中，签出 **ExecuteQueryImplementation** 扩展方法。 **ExecuteQueryImplementation** 包含在 [OfficeDevPnP.Core ](https://github.com/OfficeDev/PnP/tree/master/OfficeDevPnP.Core) 中
 **为改进此内容做贡献**
您可以获取最新的更新，或为改进 [GitHub 上的此文章](https://github.com/OfficeDev/PnP-Guidance/blob/master/articles/Handle-SharePoint-Online-throttling-by-using-exponential-back-off.md)做贡献。您还可以为改进本示例以及 [GitHub 上的其他示例](https://github.com/OfficeDev/PnP)做贡献。有关示例的完整列表，请参阅模式和做法开发人员中心。我们欢迎您做出 [贡献](https://github.com/OfficeDev/PnP/wiki/contributing-to-Office-365-developer-patterns-and-practices)。 

## 其他资源



- [Office 365 开发模式和做法解决方案指南](office-365-development-patterns-and-practices-solution-guidance.md)
    
- [ClientContextExtensions.cs 示例](https://github.com/OfficeDev/PnP/blob/master/OfficeDevPnP.Core/OfficeDevPnP.Core/AppModelExtensions/ClientContextExtensions.cs)
    
- [如何：避免在 SharePoint Online 中受限制或被阻止](https://msdn.microsoft.com/library/office/dn889829.aspx)
    
- [模式和做法开发人员中心](http://dev.office.com/patterns-and-practices)
    
