
# SharePoint 提供程序托管的外接程序中的跨域图像
在提供程序托管的外接程序中跨域使用图像。

 **上次修改时间：** 2015年7月16日

 _ **适用范围：** SharePoint 2013?| SharePoint Add-ins?| SharePoint Online_

 **注释**  名称"SharePoint 相关应用程序"将更改为"SharePoint 外接程序"。在转换期间，某些 SharePoint 产品和 Visual Studio 工具的文档和 UI 仍可能使用术语"SharePoint 相关应用程序"。有关详细信息，请参阅 [Office 和 SharePoint 相关应用程序的新名称](05b07b04-6c8b-4b7e-bd86-e32c589dfead.md#bk_newname)。

您可能想要在提供程序托管的外接程序中显示 SharePoint 网站中的图像。由于提供程序托管的外接程序运行在远程 web 上，因此，您的提供程序托管的外接程序的域和您的 SharePoint 网站的域是不同的。例如，您的外接程序可能会运行在 Microsoft Azure 上，而您正在尝试显示 Office 365 中的图像。由于您的提供程序托管的外接程序要跨越多个域来访问该图像，因此，SharePoint 需要具有用户授权才能让提供程序托管的外接程序显示该图像。
[Core.CrossDomainImages](https://github.com/OfficeDev/PnP/tree/dev/Samples/Core.CrossDomainImages) 代码示例显示提供程序托管的外接程序如何使用 SharePoint 访问令牌和 REST 服务从 SharePoint 检索图像。REST 服务返回 Base64 编码的字符串，该字符串用于在浏览器中显示图像。本示例中使用此方案，通过使用服务器端或客户端代码，在提供程序托管的外接程序中显示存储在 SharePoint 中的图像。

 **注释**  由于此示例使用 REST 终结点，因此，您可以使用服务器端或客户端代码检索您的图像。

 **为改进此内容做贡献**
您可以获取最新的更新，或为改进 [GitHub 上的此文章](https://github.com/OfficeDev/PnP-Guidance)做贡献。您还可以为改进本示例以及 [GitHub 上的其他示例](https://github.com/OfficeDev/PnP)做贡献。有关示例的完整列表，请参阅 [模式和做法开发人员中心](http://dev.office.com/patterns-and-practices)。我们欢迎您做出 [贡献](https://github.com/OfficeDev/PnP/wiki/contributing-to-Office-365-developer-patterns-and-practices)。 

## 开始之前

若要开始，请从 GitHub 上的 [Office 365 开发人员模式和做法](https://github.com/OfficeDev/PnP/tree/dev)项目下载 [Core.CrossDomainImages](https://github.com/OfficeDev/PnP/tree/dev/Samples/Core.CrossDomainImages) 示例外接程序。


## 使用 Core.CrossDomainImages 代码示例

运行此示例时，Default.aspx 将尝试加载以下内容：


- 图像 1，通过使用绝对 URL。 
    
- 图像 2，通过对返回 Base64 编码的字符串的 REST 终结点使用服务器端调用。
    
- 图像 3，通过对返回 Base64 编码的字符串的 REST 终结点使用客户端调用。
    

 **注释**  图 1 不会呈现，因为外接程序需要跨越多个域来获取 SharePoint 中的图像。需要注意的是，提供程序托管的外接程序的 URL 运行在本地主机上。打开图 1 的快捷菜单（右键单击），然后选择"属性"。请注意，"地址(URL)"正在尝试从外接程序 web（而非本地主机）检索图像。由于提供程序托管的外接程序需要跨越多个域到达外接程序 web，因此，需要进行身份验证才能访问图像。验证通过复制"地址(URL)"并将其粘贴到新的浏览器窗口，直接访问图像 1 的 URL （而不是在提供程序托管的外接程序中进行跨域调用）是否可以准确无误地进行解析。请注意，图 1 显示没有出现错误。将图像 1 的源与图像 2 的源进行比较。请注意，"地址(URL)"指向 Base64 编码的字符串。

Default.aspx 加载时， **Page_Load** 将运行并执行以下操作：


1. 将 Image1.ImageUrl 设置为图像的绝对路径。
    
2. 实例化 ImgService。ImgService 是运行在与提供程序托管的外接程序相同的域中的 REST 终结点。
    
3. 将 Image2.ImageUrl 设置为  **ImgService.GetImage** 返回的 Base64 编码的字符串。访问令牌、网站、文件夹和文件名将作为参数传递到 **ImgService.GetImage** 。
    

 **注释**  本文中的代码按原样提供，不提供任何明示或暗示的担保，包括对特定用途适用性、适销性或不侵权的默示担保。




```C#
 protected void Page_Load(object sender, EventArgs e)
        {
            var spContext = SharePointContextProvider.Current.GetSharePointContext(Context);
            using (var clientContext = spContext.CreateUserClientContextForSPAppWeb())
            {
                // Set the access token in a hidden field for client-side code to use.
                hdnAccessToken.Value = spContext.UserAccessTokenForSPAppWeb;

                // Set the URLs to the images.
                Image1.ImageUrl = spContext.SPAppWebUrl + "AppImages/O365.png";
                Services.ImgService svc = new Services.ImgService();
                Image2.ImageUrl = svc.GetImage(spContext.UserAccessTokenForSPAppWeb, spContext.SPAppWebUrl.ToString(), "AppImages", "O365.png");
            }
        }
```

 **GetImage** 将执行以下操作：


1. 使用  **URL** 来存储将用于从 SharePoint 检索图像的 GetFolderByServerRelativeUrl REST 终结点 URI。您可以参阅 [文件和文件夹 REST API 引用](http://msdn.microsoft.com/library/2c3d2545-1cd7-497e-b535-12199d8edfbb%28Office.15%29.aspx)了解详细信息。
    
2. 使用  **URL** 实例化 [HttpWebRequest](https://msdn.microsoft.com/library/system.net.httpwebrequest.aspx) 对象。
    
3. 将授权标头添加到包含访问令牌的 HttpWebRequest 对象中。 
    
对终结点 URI 执行了 GET 调用后，返回的数据流是 Base64 编码的字符串。将返回的字符串设置为图像的  **src** 属性。




```C#
 public string GetImage(string accessToken, string site, string folder, string file)
        {
            // Create the HttpWebRequest to call the REST endpoint.
            string url = String.Format("{0}_api/web/GetFolderByServerRelativeUrl('{1}')/Files('{2}')/$value", site, folder, file);
            HttpWebRequest request = WebRequest.Create(url) as HttpWebRequest;
            request.Headers.Add("Authorization", "Bearer" + " " + accessToken);
            using (HttpWebResponse response = request.GetResponse() as HttpWebResponse)
            {
                using (var sourceStream = response.GetResponseStream())
                {
                    using (var newStream = new MemoryStream())
                    {
                        sourceSteam.CopyTo(newStream);
                        byte[] bytes = newStream.ToArray();
                        return "data:image/png;base64, " + Convert.ToBase64String(bytes);
                    }
                }
            }
        }
```

 **Page_Load** 运行完之后，Default.aspx 将运行 Default.aspx 中的客户端代码，这将加载图像 3。客户端代码使用 jQuery Ajax 调用 **GetImage** 。在 **GetImage** 成功返回 Base64 编码的字符串后，图像 3 上的 **src** 属性将设置为返回的字符串。




```
  ...

	              $.ajax({
                url: '../Services/ImgService.svc/GetImage?accessToken=' + $('#hdnAccessToken').val() + '&amp;site=' + encodeURIComponent(appWebUrl + '/') + '&amp;folder=AppImages&amp;file=O365.png',
                dataType: 'json',
                success: function (data) {
                    $('#Image3').attr('src', data.d);
                },
                error: function (err) {
                    alert('error occurred');
                }
            });

           ...

```


## 其他资源



- [Office 365 开发模式和做法解决方案指南](https://msdn.microsoft.com/library/office/dn904529.aspx)
    
