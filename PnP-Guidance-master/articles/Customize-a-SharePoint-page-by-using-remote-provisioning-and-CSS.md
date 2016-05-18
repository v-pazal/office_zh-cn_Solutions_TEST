
# 使用远程设置和 CSS 自定义 SharePoint 页面
使用 CSS 自定义 SharePoint 格式文本字段和 Web 部件区域。

 **上次修改时间：** 2015年8月7日

 _ **适用范围：** SharePoint 2013?| SharePoint Add-ins?| SharePoint Online_

 **注释**  名称"SharePoint 相关应用程序"将更改为"SharePoint 外接程序"。在转换期间，某些 SharePoint 产品和 Visual Studio 工具的文档和 UI 仍可能使用术语"SharePoint 相关应用程序"。有关详细信息，请参阅 [Office 和 SharePoint 相关应用程序的新名称](05b07b04-6c8b-4b7e-bd86-e32c589dfead.md#bk_newname)。

您可以使用级联样式表 (CSS) 自定义 SharePoint 格式文本字段和 Web 部件区域。若要自定义格式文本字段，可以直接在您当前编辑的页面上执行此操作。对于 Web 部件区域，可以使用脚本编辑 Web 部件添加 HTML 或脚本，或者关联一个 CSS 样式表。
有关与本文关联的代码示例，请参阅 GitHub 上的 [Office 365 开发人员模式和做法](https://github.com/OfficeDev/PnP)中的 [Branding.AlternateCSSAndSiteLogo](https://github.com/OfficeDev/PnP/tree/master/Samples/Branding.AlternateCSSAndSiteLogo)。 
 **为改进此内容做贡献**
您可以获取最新的更新，或为改进 [GitHub 上的此文章](https://github.com/OfficeDev/PnP-Guidance)做贡献。您还可以为改进本示例以及 [GitHub 上的其他示例](https://github.com/OfficeDev/PnP)做贡献。有关示例的完整列表，请参阅 [模式和做法开发人员中心](http://dev.office.com/patterns-and-practices)。我们欢迎您做出 [贡献](https://github.com/OfficeDev/PnP/wiki/contributing-to-Office-365-developer-patterns-and-practices)。 

## 自定义格式文本字段

您可以直接在页面编辑器中使用 CSS 自定义格式文本字段：


1. 在您的 SharePoint 页上，选择"编辑"以打开页面编辑器。
    
2. 从功能区中选择"插入">"嵌入代码"。
    
您现在可以为格式文本字段添加或修改 CSS 元素。


## 自定义 Web 部件区域

若要使用 CSS 自定义 Web 部件区域，可使用脚本编辑器 Web 部件。有关详细信息，请参阅 [如何使用 SharePoint 2013 中的脚本编辑器 Web 部件](http://community.bamboosolutions.com/blogs/sharepoint-2013/archive/2013/05/20/how-to-use-script-editor-web-part-in-sharepoint-2013.aspx)。


 **注释**  如果使用 SharePoint Online 和 NoScript 功能，将禁用脚本编辑器 Web 部件。 

以下代码示例将自定义 CSS 上载到资源库、使用自定义操作将引用应用到 CSS URL，然后创建自定义操作构建指向新 CSS 文件的链接。


 **注释**  本文中的代码按原样提供，不提供任何明示或暗示的担保，包括对特定用途适用性、适销性或不侵权的默示担保。




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

                        // First, upload the CSS files to the Asset Library.
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
                        
                        // Clean up existing actions that we may have deployed.
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

                        // Build a custom action to write a link to your new CSS file.
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



- [SharePoint 网站品牌和页面自定义解决方案](sharepoint-site-branding-and-page-customization-solutions.md)
    
- [如何使用 SharePoint 2013 中的脚本编辑器 Web 部件](http://community.bamboosolutions.com/blogs/sharepoint-2013/archive/2013/05/20/how-to-use-script-editor-web-part-in-sharepoint-2013.aspx)
    
- [ScriptEditorWebPart 类](https://msdn.microsoft.com/zh-cn/library/office/microsoft.sharepoint.webpartpages.scripteditorwebpart.aspx)
    
