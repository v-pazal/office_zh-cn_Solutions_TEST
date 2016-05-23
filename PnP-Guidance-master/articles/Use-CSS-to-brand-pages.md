
# 使用 CSS 打造 SharePoint 页面品牌
使用 CSS 支持 SharePoint 中的品牌打造和网站设计。了解有关母版页中的 CSS、corev15.css 文件以及自定义品牌打造中的组合外观。

 **上次修改时间：** 2015年8月7日

 _ **适用范围：** SharePoint 2013?| SharePoint Add-ins?| SharePoint Online_

 **注释**  名称"SharePoint 相关应用程序"将更改为"SharePoint 外接程序"。在转换期间，某些 SharePoint 产品和 Visual Studio 工具的文档和 UI 仍可能使用术语"SharePoint 相关应用程序"。有关详细信息，请参阅 [Office 和 SharePoint 相关应用程序的新名称](05b07b04-6c8b-4b7e-bd86-e32c589dfead.md#bk_newname)。

级联样式表 (CSS) 在 SharePoint 品牌打造中起着非常重要的作用。若要在 SharePoint 2013 和 SharePoint Online 中成功自定义网站设计，需要熟悉 SharePoint 使用 CSS 的方式，这将非常有用。
 **为改进此内容做贡献**
您可以获取最新的更新，或为改进 [GitHub 上的此文章](https://github.com/OfficeDev/PnP-Guidance)做贡献。您还可以为改进本示例以及 [GitHub 上的其他示例](https://github.com/OfficeDev/PnP)做贡献。有关示例的完整列表，请参阅 [模式和做法开发人员中心](http://dev.office.com/patterns-and-practices)。我们欢迎您做出 [贡献](https://github.com/OfficeDev/PnP/wiki/contributing-to-Office-365-developer-patterns-and-practices)。 

## CSS 品牌打造概述

您在创建 SharePoint 网站集时，SharePoint 将创建一个母版页样式库 (_catalogs/masterpage)，其中存储了大部分品牌资产，包括 .master, .css、图像、HTML 和 JavaScript 文件。

在 SharePoint 2013 中，SharePoint 针对团队网站、发布网站以及启用了"发布"的团队网站默认使用 seattle.master 页。针对 OneDrive for Business 网站使用 mysite15.master。每个 .master 指的是 corev15.css 文件，此文件使用随 SharePoint 提供的各种现成的 .css 文件构建而成。

所有默认的母版页在处理样式时都使用 corev15.css，并依赖于 CSS 级联和 CSS 文件共享，以分析将哪些样式应用到页面区域中的特定控件和元素。还会组合多个 CSS 文件以构建 oslo.css 文件，此文件与 oslo.master 母版页共同使用。


## 母版页中的 CSS

与服务器端对象模型中的 [WebControls.CssRegistration](https://msdn.microsoft.com/zh-cn/library/office/microsoft.sharepoint.webcontrols.cssregistration.aspx) 类对应的 `<SharePoint:CssRegistration>` 内容占位符。与对母版页的引用类似，SharePoint 在处理页面后将替换母版页中的标记。 [WebControls.CssLink](https://msdn.microsoft.com/zh-cn/library/office/microsoft.sharepoint.webcontrols.csslink.aspx) 类将读取母版页的注册并在生成的结果 HTML 文件中插入 `<link>` 标记。

请考虑以下示例。




```
<SharePoint:CssRegistration name="<% $SPUrl:~SiteCollection/Style Library/~language/Core Styles/contoso.css%>" runat="server"/>
```

在运行时，按以下方式呈现代码。




```
<link rel="stylesheet" type="text/css" href="/sites/nopub/Style%20Library/en-US/Core%20Styles/contoso.css">
```

呈现页面时， **CSSLink** 类呈现所有样式表。如果您在自定义 .css 文件中定义的样式同时也在 corev15.css 中进行了定义，则它们将被覆盖。


## Corev15.css 文件

SharePoint 默认应用了大量 CSS。corev15.css 文件是 SharePoint 中的样式的主要来源。此文件存储在服务器上的 SharePoint 15 文件夹 ( ` \TEMPLATE\LAYOUTS\1033\STYLES\Themable\COREV15.CSS`) 中，而不是根网站和主页的母版页样式库中。

若要了解 SharePoint 如何使用现成的 CSS，请使用 Web 浏览器中的开发人员工具查看 corev15.css 文件。在 Internet Explorer 中，使用 Internet Explorer 开发人员工具栏（按  **F12** 进入）并选择"CSS"选项卡查看 corev15.css。

corev15.css 中定义的样式使用 .ms- 和 .s4- 前缀，指示由 Microsoft. Corev15.css 创建的样式同样使用大量子级和后代选择器。

您在查看文件时，会注意到大量以下格式的注释： `/* [ReplaceFont ( themeFont:"body")] */`。应用组合外观后，SharePoint 将读取这些注释。这些注释提示 SharePoint 更改注释后面的 CSS 的属性。应用组合外观可能会更改应用的大量默认的颜色、字体和背景图片，并随后更新 corev15.css 中的设置。


 **注释**  按这种方式选择 corev15.css 文件将加载呈现的 CSS，而不是保存的 CSS。有时您可能会发现这两者之间存在差异。浏览器等用户代理也可以更改呈现以响应用户的操作。


 **重要信息**  不要登录服务器并编辑或自定义 SharePoint 根中的核心 SharePoint CSS 文件，因为这样会对支持和升级产生负面影响。切勿直接编辑 corev15.css，而是始终创建一个副本，对其重命名并编辑新文件。编辑 corev15.css 会将品牌打造应用到服务器上的所有 Web 应用程序。


### 创建 SharePoint 的自定义样式表


1. 创建 corev15.css 的一个副本，并将其命名为 contosov15.css。
    
2. 打开 contosov15.css 并将 CssRegistration 行修改为从 corev15.css 指向 contosov15.css，如以下示例中所示。
    
  ```
  <SharePoint:CssRegistration Name="Themable/contoso.css" runat="server" />
  ```

3. 在 CssRegistration 行下面，添加以下内容。
    
  ```
  <SharePoint:CssRegistration name="/_catalogs/masterpage/contoso/contoso.css" 
runat="server">

  ```


## 自定义品牌打造中的组合外观

从母版页调用 CSS 时，您可以使用自定义品牌打造中的组合外观。CSS 文件存储在以下位置之一的 SharePoint 文件系统中：


-  `15\TEMPLATE\LAYOUTS\{LCID}\STYLES\Themable`
    
-  `/Style Library/Themable/`
    
-  `/Style Library/{culture}/Themable/`
    
您可以将自定义品牌打造文件置于  `/Style Library/Themable/` 和 `/Style Library/{culture}/Themable/` 中，但 `15\TEMPLATE\LAYOUTS\{LCID}\STYLES\Themable` 不可编辑，因此您无法将自定义文件存储在该位置。


 **注释**  如果这些位置默认情况下不存在，可以手动创建，SharePoint 会将它们识别为可主题化。


## 将自定义 CSS 应用到 SharePoint 页

您可以将自定义 CSS 添加到格式文本字段和 Web 部件区域。要将 CSS 添加到格式文本字段，可以将页面置于编辑模式下，并从功能区中选择"插入">"嵌入代码"。对于 Web 部件区域，使用脚本编辑 Web 部件添加 HTML、脚本或内部样式表。您可以使用此方法隐藏默认 SharePoint UI 中的快速启动导航。如果您使用的是 SharePoint Online 以及 NoScript 功能，NoScript 将禁用脚本编辑器 Web 部件。

使用外部样式表并在 SharePoint 页面的  `<head>` 标记内部的 `<link>` 标记中加入对样式表的引用，将 CSS 应用到 SharePoint 网站。

以下代码示例演示了如何将自定义 CSS 上载到资源库、使用自定义操作将引用应用到 CSS URL，然后创建自定义操作以构建指向新 CSS 文件的链接。此代码是 GitHub 上的 [Branding.AlternateCSSAndSiteLogo](https://github.com/OfficeDev/PnP/tree/master/Samples/Branding.AlternateCSSAndSiteLogo) 示例的一部分。




```C#
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;

using System.IO;
using Microsoft.SharePoint.Client;
using Microsoft.SharePoint.Client.EventReceivers;

namespace AlternateCSSAppAutohostedWeb.Services
{
    public class AppEventReceiver : IRemoteEventService
    {
        public SPRemoteEventResult ProcessEvent(SPRemoteEventProperties properties)
        {
            SPRemoteEventResult result = new SPRemoteEventResult();

            try
            {
                using (ClientContext clientContext = TokenHelper.CreateAppEventClientContext(properties, false))
                {
                    if (clientContext != null)
                    {
                        Web hostWebObj = clientContext.Web;

                        List assetLibrary = hostWebObj.Lists.GetByTitle("Site Assets");
                        clientContext.Load(assetLibrary, l => l.RootFolder);

                        // First, upload the CSS files to the Asset library.
                        DirectoryInfo themeDir = new DirectoryInfo(System.Web.Hosting.HostingEnvironment.ApplicationPhysicalPath + "CSS");
                        foreach (var themeFile in themeDir.EnumerateFiles())
                        {
                            FileCreationInformation newFile = new FileCreationInformation();
                            newFile.Content = System.IO.File.ReadAllBytes(themeFile.FullName);
                            newFile.Url = themeFile.Name;
                            newFile.Overwrite = true;

                            Microsoft.SharePoint.Client.File uploadAsset = assetLibrary.RootFolder.Files.Add(newFile);
                            clientContext.Load(uploadAsset);
                            break;
                        }
                        
                        string actionName = "SampleCSSLink";

                        // Now, apply a reference to the CSS URL via a custom action.
                        
                        // Clean up existing actions that you might have deployed.
                        var existingActions = hostWebObj.UserCustomActions;
                        clientContext.Load(existingActions);

                        // Run uploads and initialize the existing Actions collection.
                        clientContext.ExecuteQuery();

                        // Clean up existing actions.
                        foreach (var existingAction in existingActions)
                        {
                            if(existingAction.Name.Equals(actionName, StringComparison.InvariantCultureIgnoreCase))
                                existingAction.DeleteObject();
                        }
                        clientContext.ExecuteQuery();

                        // Build a custom action to write a link to our new CSS file.
                        UserCustomAction cssAction = hostWebObj.UserCustomActions.Add();
                        cssAction.Location = "ScriptLink";
                        cssAction.Sequence = 100;
                        cssAction.ScriptBlock = @"document.write('<link rel=""stylesheet"" href=""" + assetLibrary.RootFolder.ServerRelativeUrl + @"/cs-style.css"" />');";
                        cssAction.Name = actionName;
                        
                        // Apply.
                        cssAction.Update();
                        clientContext.ExecuteQuery();
                    }
                    result.Status = SPRemoteEventServiceStatus.Continue;
                    return result;
                }
            }
            catch (Exception ex)
            {
                result.Status = SPRemoteEventServiceStatus.CancelWithError;
                result.ErrorMessage = ex.Message;
                return result;
            }
            
        }

        public void ProcessOneWayEvent(SPRemoteEventProperties properties)
        {
            // This method is not used by add-in events.
        }
    }
}

```


## 其他资源



- [SharePoint 网站品牌和页面自定义解决方案](SharePoint-site-branding-and-page-customization-solutions.md)
    
- [Branding.AlternateCSSAndSiteLogo 示例](https://github.com/OfficeDev/PnP/tree/master/Samples/Branding.AlternateCSSAndSiteLogo)
    
