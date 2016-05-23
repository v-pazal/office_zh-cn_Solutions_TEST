
# SharePoint 外接程序中的提升权限
使用仅外接程序策略或服务帐户来提升 SharePoint 外接程序中的特权。

 **上次修改时间：** 2015年8月15日

 _ **适用范围：** SharePoint 2013?| SharePoint Add-ins?| SharePoint Online_

 **注释**  名称"SharePoint 相关应用程序"将更改为"SharePoint 外接程序"。在转换期间，某些 SharePoint 产品和 Visual Studio 工具的文档和 UI 仍可能使用术语"SharePoint 相关应用程序"。有关详细信息，请参阅 [Office 和 SharePoint 相关应用程序的新名称](05b07b04-6c8b-4b7e-bd86-e32c589dfead.md#bk_newname)。

使用不同的方法来以提升 SharePoint 外接程序和服务器场解决方案中的特权。服务器场解决方案使用  **RunWithElevatedPrivileges(SPSecurity.CodeToRunElevated)**（属于 SharePoint 服务器端对象模型）来提升特权。SharePoint 外接程序使用仅应用程序策略或服务帐户。
 在下列情况下，您可能想要在外接程序中使用已提升的特权：

- 您的外接程序为不具有足够的个人权限来完成操作的用户执行相关操作。由于权限级别过高，管理员可能没有对用户分配特定权限。 
    
    例如，您的组织可能会实现用户必须用于创建网站集的自定义网站集设置解决方案。您的组织可指定所有新网站集都必须具有特定列表、内容类型或与之相关联的字段。如果用户创建自己的网站集，也可能会记得或忘记在其新网站集上创建这些对象。在此方案中，用户使用外接程序创建网站集，但系统不会为这些用户单独分配创建网站集的权限。
    
- 您的外接程序不代表任何用户。例如，控制或管理流程。
    
 **为改进此内容做贡献**
您可以获取最新的更新，或为改进 [GitHub 上的此文章](https://github.com/OfficeDev/PnP-Guidance)做贡献。您还可以为改进本示例以及 [GitHub 上的其他示例](https://github.com/OfficeDev/PnP)做贡献。有关示例的完整列表，请参阅 [模式和做法开发人员中心](http://dev.office.com/patterns-and-practices)。我们欢迎您做出 [贡献](https://github.com/OfficeDev/PnP/wiki/contributing-to-Office-365-developer-patterns-and-practices)。 

## 仅应用程序策略授权

仅外接程序策略使用 OAuth 对您的外接程序进行身份验证。当外接程序使用仅外接程序策略时，SharePoint 将仅检查外接程序主体的权限。这些权限已对外接程序授予。如果无论与当前用户相关联的权限是什么，只要外接程序具有足够的权限来执行任务，授权都会成功。授权成功后，访问令牌将返回至您的外接程序。您的外接程序将使用此访问令牌来执行您的代码所需的任何操作。

有关详细信息，请参阅 [SharePoint 2013 中的外接程序授权策略类型](https://msdn.microsoft.com/library/office/fp179892.aspx)。


 **注释**  仅外接程序策略仅适用于提供程序托管的外接程序。可访问主机 web 的 SharePoint 托管的外接程序必须使用用户+外接程序策略。

在外接程序中使用仅外接程序策略的好处包括：


- 您无需授予单独的用户许可证。服务帐户需要单独的用户许可证。
    
- 与服务帐户相比，可获得更为具体的控制权。例如，您可以将  **FullControl** 权限应用到 Web，如果您使用的是服务帐户，则无法执行该操作。
    
不能将仅外接程序策略与以下 API 一起使用：


- 用户配置文件
    
- 搜索
    
- 分类（仅适用于写入托管元数据服务的方案）
    
 若要使用仅外接程序策略，您首先必须使用 appinv.aspx 向外接程序授予权限。AppManifest.xml 文件中的以下代码演示如何设置仅外接程序策略和外接程序的权限。




```XML
 <AppPermissionRequests AllowAppOnlyPolicy="true">
    <AppPermissionRequest Scope="http://sharepoint/content/sitecollection/web" Right="FullControl" />
  </AppPermissionRequests>
```

使用仅外接程序策略要求您的外接程序使用低信任或高信任授权。该策略不适用于 SharePoint 跨域 JavaScript 库，而这是获取访问 SharePoint 资源权限的第三种方式。


### 低信任授权

当使用 Microsoft Azure 访问控制服务 (ACS) 在提供程序托管的外接程序和您的 Office 365 网站或本地 SharePoint 服务器场之间建立信任时，您的外接程序可以使用低信任授权。您可以在 [SharePoint 外接程序的三个授权系统](http://msdn.microsoft.com/library/623fdab7-856e-4a89-9f5d-748a2ba1ef2e%28Office.15%29.aspx)中了解详细信息。若要获取对  **ClientContext** 对象的引用，您的外接程序应当：


1. 使用  **TokenHelper.GetAppOnlyAccessToken** 获取访问令牌。
    
2. 使用  **TokenHelper.GetClientContextWithAccessToken** 获取 **ClientContext** 对象。
    

 **注释**  TokenHelper 文件是由 Visual Studio Microsoft Office 开发人员工具 生成的源代码。没有相对应的参考文档，但  **TokenHelper** 类中存在很多注释。若要查看代码注释，请在 Visual Studio 中创建提供程序托管的外接程序。


 **注释**  本文中的代码按原样提供，不提供任何明示或暗示的担保，包括对特定用途适用性、适销性或不侵权的默示担保。


```C#
Uri siteUrl = new Uri(ConfigurationManager.AppSettings["MySiteUrl"]);
try
{
    // Connect to a site using an add-in-only token.
    string realm = TokenHelper.GetRealmFromTargetUrl(siteUrl);
    var token = TokenHelper.GetAppOnlyAccessToken(TokenHelper.SharePointPrincipal, siteUrl.Authority, realm).AccessToken;

    using (var ctx = TokenHelper.GetClientContextWithAccessToken(siteUrl.ToString(), token))
    {
        // Perform operations on the ClientContext object, which uses the add-in-only token. 
    }
}
catch (Exception ex)
{
    Console.WriteLine("Error in execution: " + ex.Message);
}
```


### 高信任授权

如果您的外接程序使用高信任授权系统（也称为 S2S 协议），它将调用不同的  **TokenHelper** 方法： **TokenHelper.GetS2SAccessTokenWithWindowsIdentity** 。


 **重要信息**   **TokenHelper.GetS2SAccessTokenWithWindowsIdentity** 用于仅外接程序和"用户+外接程序"调用。该方法（其中包含用户标识）的第二个参数确定使用哪个策略。传递 **null** 以使用仅外接程序策略。


## 服务帐户

 只有在仅外接程序策略提供的权限不足以完成任务的情况下，才使用服务帐户来为您的外接程序提升特权。此外，在某些情况下，必须使用用户帐户。例如，当您的代码用于以下任一 SharePoint 服务应用程序时，就需要使用服务帐户：


- 使用客户端对象模型 (CSOM) 的用户配置文件服务
    
- 托管的元数据服务
    
- 搜索
    
计划在您的外接程序中使用服务帐户时，请考虑以下事项：


- 服务帐户需要单个用户许可证。
    
- 针对每个外接程序创建一个服务帐户，或在 SharePoint 环境中将一个服务帐户用于所有的外接程序。
    
- 必须在授权过程中提供用户名和密码。确保已安全存储或检索服务帐户凭据。
    
- 在您的外接程序可以在网站上执行操作之前，首先必须向服务帐户授予访问网站所需的权限。
    

 **注释**  从 Office 商店购买的外接程序不能使用服务帐户。

以下代码演示如何使用  **SharePointOnlineCredentials** 通过服务帐户进行身份验证。




```C#
using (ClientContext context = new ClientContext("https://contoso.sharepoint.com"))
{

    // Use default authentication mode.
    context.AuthenticationMode = ClientAuthenticationMode.Default;  

    // Specify the credentials for the service account.
    context.Credentials = new SharePointOnlineCredentials("User Name", "Password");
}

```


## 其他资源



- [Office 365 开发模式和做法解决方案指南](Office-365-development-patterns-and-practices-solution-guidance.md)
    
- [SharePoint 2013 中的外接程序授权策略类型](http://msdn.microsoft.com/library/124879c7-a746-4c10-96a7-da76ad5327f0%28Office.15%29.aspx)
    
- [使用 Office 365 SharePoint 网站在本地 SharePoint 网站中对提供程序托管的外接程序进行授权](http://msdn.microsoft.com/library/2f65ba3f-b246-4064-b4fb-ad18399d387a%28Office.15%29.aspx)
    
