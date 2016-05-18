
# 在运行时使用 OAuth 授权提供程序托管的外接程序用户
运行时在提供程序承载的 外接程序中使用 OAuth 提供对 SharePoint 资源的授权访问。

 **上次修改时间：** 2015年8月7日

 _ **适用范围：** SharePoint 2013?| SharePoint Add-ins?| SharePoint Online_

 **注释**  名称"SharePoint 相关应用程序"将更改为"SharePoint 外接程序"。在转换期间，某些 SharePoint 产品和 Visual Studio 工具的文档和 UI 仍可能使用术语"SharePoint 相关应用程序"。有关详细信息，请参阅 [Office 和 SharePoint 相关应用程序的新名称](05b07b04-6c8b-4b7e-bd86-e32c589dfead.md#bk_newname)。

通常，您的用户可通过以下方式访问 SharePoint?外接程序：打开 SharePoint 网站，选择"网站内容"，然后选择 外接程序。SharePoint 会将用户重定向到您的提供程序承载的 外接程序 运行所在的远程 Web。由于用户是从 SharePoint 访问 外接程序，用户需得到 SharePoint 授权，然后才能访问 外接程序。
或者，如果您的用户直接转到您的提供程序承载的 外接程序的 URL，外接程序必须在运行时使用 OAuth 对其授权。在这种情况下，提供程序承载的 外接程序必须处理授权，因为您的用户没有首先获得 SharePoint 授权。 [Core.DynamicPermissions ](https://github.com/OfficeDev/PnP/tree/dev/Samples/Core.DynamicPermissions) 示例说明如何使用 OAuth 动态地从网站请求权限。
使用此解决方案可执行以下操作：

- 授权用户直接导航到您的提供程序承载的 外接程序，而非从 SharePoint 访问您的 外接程序。例如，您可能不希望用户使用 SharePoint UI。相反，您的用户可能使用提供程序承载的 外接程序，其中显示了从 SharePoint 检索的相关数据。
    
- 构建可以使用 OAuth 对用户进行身份验证，且可以通过 Office 商店销售的提供程序承载的 外接程序。
    
 **为改进此内容做贡献**
您可以获取最新的更新，或为改进 [GitHub 上的此文章](https://github.com/OfficeDev/PnP-Guidance)做贡献。您还可以为改进本示例以及 [GitHub 上的其他示例](https://github.com/OfficeDev/PnP)做贡献。有关示例的完整列表，请参阅 [模式和做法开发人员中心](http://dev.office.com/patterns-and-practices)。我们欢迎您做出 [贡献](https://github.com/OfficeDev/PnP/wiki/contributing-to-Office-365-developer-patterns-and-practices)。 

## 开始之前

若要开始，请从 GitHub 上的 [Office 365 开发人员模式和做法](https://github.com/OfficeDev/PnP/tree/dev)项目下载 [Core.DynamicPermissions ](https://github.com/OfficeDev/PnP/tree/dev/Samples/Core.DynamicPermissions) 示例 外接程序。

运行此代码示例之前： 


- 确保您在网站上具有"管理"权限。有关详细信息，请参阅 [了解权限级别](https://support.office.com/article/Understanding-permission-levels-87ECBB0E-6550-491A-8826-C075E4859848)。
    
- 使用 AppRegNew.aspx 在 SharePoint 网站上注册 外接程序： 
    
      1. 导航到 SharePoint 网站上的 appregnew.aspx。例如，如果您希望在 contoso.sharepoint.com 网站上使用您的 外接程序，请导航到 http://contoso.sharepoint.com/_layouts/15/appregnew.aspx。
    
  2. 选择"生成"以生成一个新的"客户端 ID"。
    
  3. 选择"生成"以生成一个新的"客户端密钥"。 
    
  4. 在"标题"中为您的 外接程序输入一个标题。
    
  5. 在"加载项域"中，输入您的提供程序承载的 外接程序的 URL，例如，输入 localhost。 
    
  6. 在"重定向 URI"中，输入您的提供程序承载的 外接程序的 URL。在获得授权和同意之后，SharePoint 会将您的 外接程序重定向到此 URL。例如，输入 https://localhost:44363/Home/Callback。您可以从 Visual Studio 中的 Core.DynamicPermissionsWeb 项目的"SSL URL"属性中获取域名和端口号。
    
  7. 选择"创建"。 
    
- 将客户端 ID 和客户端密钥复制到 Core.DynamicPermissionsWeb\web.config 中的  **appSettings** 元素。
    

## 使用 Core.DynamicPermissions 加载项

运行此代码示例时：


1. 在"连接到 Office 365"中，输入您在其上注册您的 外接程序的 SharePoint 网站的 URL，然后选择"连接"。例如，输入 https://contoso.sharepoint.com。
    
2. 登录到您 Office 365 网站。
    
3. 如果系统请求您同意 外接程序所申请的权限，选择"信任它"。请注意，您将被重定向到 /_layouts/15/OAuthAuthorize.aspx 页面。 
    
     **注释**  您的用户必须具有"管理"权限，以同意 外接程序所申请的权限。有关详细信息，您可参阅 [SharePoint 外接程序的身份验证代码 OAuth 流](http://msdn.microsoft.com/library/e89e91c7-ea39-49b9-af5a-7f047a7e2ab7%28Office.15%29.aspx)。
4. 在"已成功连接到 Contoso"中，输入要创建的新列表的名称，然后选择"创建列表"。
    
5. 确认显示属于 Contoso 网站的所有列表的"Contoso 列表"中显示您的新列表。 
    
在"连接到 Office 365"上选择"连接"时，将调用 Controllers\HomeController.cs 中的  **Connect** ，然后它会调用 **TokenRepository.Connect** 。用户在"连接到 Office 365"上输入的 URL 将作为 **hostUrl** 传递到 **TokenRepository.Connect** 。


 **注释**  本文中的代码按原样提供，不提供任何明示或暗示的担保，包括对特定用途适用性、适销性或不侵权的默示担保。




```C#
 public ActionResult Connect(string hostUrl)
        {
            TokenRepository repository = new TokenRepository(Request, Response);
            repository.Connect(hostUrl);
            return View();            
        }
```

 **TokenRepository.Connect** 调用 **TokenHelper.GetAuthorizationUrl** 。 **TokenHelper.GetAuthorizationUrl** 使用 **hostUrl** 以及对 SharePoint 资源的所需权限将重定向 URL 返回到 OAuthAuthorize.aspx。OAuthAuthorize.aspx 用于使用 OAuth 对用户授权。重定向到 OAuthorize.aspx 时，用户必须登录到 Office 365，然后同意 外接程序所申请的权限，或者信任 外接程序。对 SharePoint 资源的所需权限是 **Web.Manage** 。用户授权后，代码示例将在 SharePoint 网站上创建列表。要在 SharePoint 网站上创建列表，用户必须具有 **Web.Manage** 权限。


 **注释**   **TokenHelper.GetAuthorizationUrl** 返回 **https://contoso.sharepoint.com/_layouts/15/OAuthAuthorize.aspx?IsDlg=1&amp;client_id=<Client ID>&amp;scope=Web.Manage&amp;response_type=code** 形式的 URL，其中 **<Client ID>** 是 外接程序的客户端 ID。如果您的 外接程序通过卖家面板注册，则任何 Office 365 网站都可以安装 外接程序。如果您的 外接程序不是通过卖家面板注册，您必须使用 appregnew.aspx 注册您的 外接程序，然后更新 Core.DynamicPermissionsWeb\web.config。要了解详细信息，请参阅 [注册 SharePoint 2013 外接程序](http://msdn.microsoft.com/library/be41a5dc-2af9-4fd9-bf4e-ad6dfa849524%28Office.15%29.aspx)。




```C#
 public void Connect(string hostUrl)
        {
            if (!IsConnectedToO365)
            {
                HttpCookie spHostUrlCookie = new HttpCookie("SPHostUrl");
                spHostUrlCookie.Value = hostUrl;
                spHostUrlCookie.Expires = DateTime.Now.AddYears(5);
                _response.Cookies.Add(spHostUrlCookie);
                _response.Redirect(TokenHelper.GetAuthorizationUrl(hostUrl, "Web.Manage"));
            }
        }
```

授权后，外接程序将在 Controllers\HomeController.cs 中重定向到  **Callback** ，这是在 appregnew.aspx 上指定的重定向 URI。 **TokenHelper** 将授权代码 **code** 传递到 **Callback** 。要授予本文稍后所述的访问令牌，必须将授权代码 **code** 返回到 **Callback** 。然后 **Callback** 将调用 **TokenRepository.Callback** 。




```C#
 public ActionResult Callback(string code)
        {
            TokenRepository repository = new TokenRepository(Request, Response);
            repository.Callback(code);
            return RedirectToAction("Index");
        }
```

 **TokenRepository.Callback** 调用 **TokenCache.UpdateCacheWithCode** ，后者使用 **TokenHelper.GetAccessToken** 以便基于授权代码 **code** 获取 OAuth 访问令牌。




```C#
public void Callback(string code)
        {
            HttpCookie spHostUrlCookie = _request.Cookies["SPHostUrl"];
            if (null != spHostUrlCookie)
            {
                Uri sharePointSiteUrl = new Uri(spHostUrlCookie.Value);
                TokenCache.UpdateCacheWithCode(_request, _response, sharePointSiteUrl);
            }
        }
```




```C#
 public static void UpdateCacheWithCode(HttpRequestBase request, HttpResponseBase response, Uri targetUri)
        {
            string refreshToken = TokenHelper.GetAccessToken(request.QueryString["code"], "00000003-0000-0ff1-ce00-000000000000", targetUri.Authority, TokenHelper.GetRealmFromTargetUrl(targetUri), new Uri(request.Url.GetLeftPart(UriPartial.Path))).RefreshToken;
            SetRefreshTokenCookie(response.Cookies, refreshToken);
            SetRefreshTokenCookie(request.Cookies, refreshToken);
        }
```

现在您的 外接程序具有此用户的访问令牌，可以继续在 SharePoint 网站上创建列表。 


## 其他资源



- [Office 365 开发模式和做法解决方案指南](https://msdn.microsoft.com/library/office/dn904529.aspx)。
    
