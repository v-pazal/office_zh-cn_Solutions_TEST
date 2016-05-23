
# 修改 SharePoint 网站权限并获取外部共享状态
使用 CSOM 修改网站集管理员的属性。获取网站集或租户的外部共享状态和外部用户。

 **上次修改时间：** 2015年8月12日

 _ **适用范围：** Office 365?| SharePoint 2013?| SharePoint Add-ins?| SharePoint Online_

 **注释**  名称"SharePoint 相关应用程序"将更改为"SharePoint 外接程序"。在转换期间，某些 SharePoint 产品和 Visual Studio 工具的文档和 UI 仍可能使用术语"SharePoint 相关应用程序"。有关详细信息，请参阅 [Office 和 SharePoint 相关应用程序的新名称](05b07b04-6c8b-4b7e-bd86-e32c589dfead.md#bk_newname)。

您可以使用 [Core.SitePermissions](https://github.com/OfficeDev/PnP/tree/dev/Scenarios/Core.SitePermissions) 示例来修改任何网站集上的网站集管理员，包括 Office 365 租户上 OneDrive for Business 的网站集管理员。本示例还演示了如何获取 Office 365 多租户安装的外部共享状态。
您可以使用控制台应用程序创建  **ClientContext** 对象，以获取访问列表和/或修改管理员的权限，并获取外部共享状态。您还可以使用 OAuth 令牌创建已注册的外接程序。
 **为改进此内容做贡献**
您可以获取最新的更新，或为改进 [GitHub 上的此文章](https://github.com/OfficeDev/PnP-Guidance)做贡献。您还可以为改进本示例以及 [GitHub 上的其他示例](https://github.com/OfficeDev/PnP)做贡献。有关示例的完整列表，请参阅 [模式和做法开发人员中心](http://dev.office.com/patterns-and-practices)。我们欢迎您做出 [贡献](https://github.com/OfficeDev/PnP/wiki/contributing-to-Office-365-developer-patterns-and-practices)。 

## 开始之前

若要开始，请从 GitHub 上的 [Office 365 开发人员模式和做法](https://github.com/OfficeDev/PnP/tree/dev)项目下载 [Core.SitePermissions](https://github.com/OfficeDev/PnP/tree/dev/Scenarios/Core.SitePermissions) 示例外接程序。


## 与网站集管理员协同工作

若要更新网站集管理员，您必须是网站集的管理员。第一步是由具有适当权限的用户创建  **ClientContext** 对象。


 **注释**  本文中的代码按原样提供，不提供任何明示或暗示的担保，包括对特定用途适用性、适销性或不侵权的默示担保。


```C#
ClientContext cc = new AuthenticationManager().GetSharePointOnlineAuthenticatedContextTenant(String.Format("https://{0}.sharepoint.com/sites/{1}", tenantName, siteName), String.Format("{0}@{1}.onmicrosoft.com", userName, tenantName), password); 
```

您可以使用  **ClientContext** 对象获取当前网站集管理员列表或更新网站集管理员，如以下示例所示。




```C#
List<UserEntity> admins = cc.Web.GetAdministrators();

List<UserEntity> adminsToAdd = new List<UserEntity>();
adminsToAdd.Add(new UserEntity() { LoginName = "i:0#.f|membership|user@domain" });

cc.Web.AddAdministrators(adminsToAdd);

UserEntity adminToRemove = new UserEntity() { LoginName = "i:0#.f|membership|user@domain" };
cc.Web.RemoveAdministrator(adminToRemove);

```

通过使用已注册的外接程序创建  **ClientContext** 对象，您可以为您尚未成为网站集管理员的网站集设置网站集管理员。此处， **ClientContext** 对象基于具有租户级权限的 OAuth 令牌。




```C#
// Use (Get-MsolCompanyInformation).ObjectID to obtain Target/Tenant realm: <guid>
//
// Manually register an add-in via the appregnew.aspx page and generate an add-in ID and 
// add-in secret. The add-in title and add-in domain can be a simple string like "MyAddin".
//
// Update the add-in ID in your worker role settings.
//
// Add the add-in secret in your worker role settings. 
//
// Manually set the permission XML for you add-in via the appinv.aspx page:
// 1. Look up your add-in via its add-in ID.
// 2. Paste the permission XML and choose create.
//
// Sample permission XML:
// <AppPermissionRequests AllowAppOnlyPolicy="true">
//   <AppPermissionRequest Scope="http://sharepoint/content/tenant" Right="FullControl" />
// </AppPermissionRequests>
//
// Because you're granting tenant-wide full control to an add-in, the add-in secret is as important
// as the password from your SharePoint administration account.

```

该操作完成后，您可以使用以下代码来获取此外接程序的  **ClientContext** 对象。




```C#
ClientContext cc = new AuthenticationManager().GetAppOnlyAuthenticatedContext("https://tenantname-my.sharepoint.com/personal/user2", "<your tenant realm>", "<appID>", "<appsecret>");
```


## 使用外部共享（仅 Office 365 MT）

本方案演示如何获取网站集的外部共享状态，并获取特定网站集的或整个租户的外部用户列表。由于此操作需要使用租户 CSOM 库，您需要针对租户管理网站集创建  **ClientContext**。用户帐户必须是租户管理员帐户。


```C#
ClientContext ccTenant = new AuthenticationManager().GetSharePointOnlineAuthenticatedContextTenant(String.Format("https://{0}-admin.sharepoint.com/", tenantName), String.Format("{0}@{1}.onmicrosoft.com", userName, tenantName), password);
```

 **ClientContext** 准备就绪后，您可以使用以下代码来获取外部共享状态和外部用户列表。




```C#
ccTenant.Web.GetSharingCapabilitiesTenant(new Uri(String.Format("https://{0}.sharepoint.com/sites/{1}", tenantName, siteName)))

List<ExternalUserEntity> externalUsers = ccTenant.Web.GetExternalUsersForSiteTenant(new Uri(String.Format("https://{0}.sharepoint.com/sites/{1}", tenantName, siteName)));

List<ExternalUserEntity> externalUsers = ccTenant.Web.GetExternalUsersTenant();

```


## 其他资源



- [SharePoint 网站设置解决方案](sharepoint-site-provisioning-solutions.md)
    
- [Provisioning.Pages 示例](https://github.com/OfficeDev/PnP/tree/dev/Scenarios/Provisioning.Pages)
    
