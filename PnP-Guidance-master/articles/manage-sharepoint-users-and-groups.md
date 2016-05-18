
# 管理 SharePoint 用户和组
在 SharePoint 网站集中管理用户、组和权限。 

 **上次修改时间：** 2015年8月7日

 _ **适用范围：** Office 365?| SharePoint 2013?| SharePoint Add-ins?| SharePoint Online_

 **注释**  名称"SharePoint 相关应用程序"将更改为"SharePoint 外接程序"。在转换期间，某些 SharePoint 产品和 Visual Studio 工具的文档和 UI 仍可能使用术语"SharePoint 相关应用程序"。有关详细信息，请参阅 [Office 和 SharePoint 相关应用程序的新名称](05b07b04-6c8b-4b7e-bd86-e32c589dfead.md#bk_newname)。

本文介绍如何在给定网站集中添加或删除组和用户。本文中的代码示例添加用户和组，然后为它们提供访问 SharePoint 的权限级别。这些用户和组权限级别操作将通过 [Core.GroupManagement](https://github.com/OfficeDev/PnP/tree/dev/Scenarios/Core.GroupManagement) PnP 示例中的扩展方法来实现。
 **为改进此内容做贡献**
您可以获取最新的更新，或为改进 [GitHub 上的此文章](https://github.com/OfficeDev/PnP-Guidance)做贡献。您还可以为改进本示例以及 [GitHub 上的其他示例](https://github.com/OfficeDev/PnP)做贡献。有关示例的完整列表，请参阅 [模式和做法开发人员中心](http://dev.office.com/patterns-and-practices)。我们欢迎您做出 [贡献](https://github.com/OfficeDev/PnP/wiki/contributing-to-Office-365-developer-patterns-and-practices)。 

## 开始之前

若要开始，请从 GitHub 上的 [Office 365 开发人员模式和做法](https://github.com/OfficeDev/PnP/tree/dev)项目下载 [Core.GroupManagement](https://github.com/OfficeDev/PnP/tree/dev/Scenarios/Core.GroupManagement) 示例外接程序。


## 添加和删除组和用户

以下示例演示了如何添加组并向组添加用户。


 **注释**  本文中的代码按原样提供，不提供任何明示或暗示的担保，包括对特定用途适用性、适销性或不侵权的默示担保。


```C#
cc.Load(cc.Web, web => web.CurrentUser);
cc.ExecuteQuery();
Microsoft.SharePoint.Client.User currentUser = cc.Web.CurrentUser;

if (!cc.Web.GroupExists("Test"))
{
  Group group = cc.Web.AddGroup("Test", "Test group", true);
  cc.Web.AddUserToGroup("Test", currentUser.LoginName);
}

```

下一个示例将删除一个组。




```C#
if (cc.Web.GroupExists("Test"))
{
  cc.Web.RemoveGroup("Test");
}

```

下一个示例将从组中删除用户。




```C#
cc.Load(cc.Web, web => web.CurrentUser);
cc.ExecuteQuery();
Microsoft.SharePoint.Client.User currentUser = cc.Web.CurrentUser;
if (cc.Web.GroupExists("Test"))
{
  if (cc.Web.IsUserInGroup("Test", currentUser.LoginName))
  {
    cc.Web.RemoveUserFromGroup("Test", currentUser.LoginName);
  }
}

```


## 将权限级别添加到组或用户

以下示例将向组中添加权限级别。


```C#
if (cc.Web.GroupExists("Test"))
{
  cc.Web.AddPermissionLevelToGroup("Test", RoleType.Contributor);
}

```

下一个示例将向用户添加权限级别。




```C#
cc.Load(cc.Web, web => web.CurrentUser);
cc.ExecuteQuery();
Microsoft.SharePoint.Client.User currentUser = cc.Web.CurrentUser;
cc.Web.AddPermissionLevelToUser(currentUser.LoginName, RoleType.Reader);

```


## 删除组或用户的权限级别

以下示例将删除组的权限级别。


```C#
if (cc.Web.GroupExists("Test"))
{
  cc.Web.RemovePermissionLevelFromGroup("Test", RoleType.Reader);
}

```

下一个示例将删除用户的权限级别。




```C#
cc.Load(cc.Web, web => web.CurrentUser);
cc.ExecuteQuery();
Microsoft.SharePoint.Client.User currentUser = cc.Web.CurrentUser;
cc.Web.RemovePermissionLevelFromUser(currentUser.LoginName, RoleType.Reader);

```


## 其他资源



- [SharePoint 网站设置解决方案](sharepoint-site-provisioning-solutions.md)
    
- [SharePoint 2013 和 SharePoint Online 的品牌打造和网站设置解决方案](branding-and-site-provisioning-solutions-for-sharepoint.md)
    
