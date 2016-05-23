
# 在 SharePoint 中上载 Web 部件
为您的用户部署预配置的标准 SharePoint Web 部件。

 **上次修改时间：** 2015年8月7日

 _ **适用范围：** SharePoint 2013?| SharePoint Add-ins?| SharePoint Online_

 **注释**  名称"SharePoint 相关应用程序"将更改为"SharePoint 外接程序"。在转换期间，某些 SharePoint 产品和 Visual Studio 工具的文档和 UI 仍可能使用术语"SharePoint 相关应用程序"。有关详细信息，请参阅 [Office 和 SharePoint 相关应用程序的新名称](05b07b04-6c8b-4b7e-bd86-e32c589dfead.md#bk_newname)。

您可以为用户上载预配置的标准 SharePoint Web 部件，以添加到其 SharePoint 网站。例如，您可以上载预配置的以下 Web 部件：

- 使用远程 web 上 JavaScript 文件的脚本编辑器 Web 部件。
    
- 内容搜索 Web 部件。
    
本文讨论预配置脚本编辑器 Web 部件，以使用远程 Web 上的 JavaScript 文件执行 UI 自定义操作。使用此解决方案可以执行以下操作：

- 使用 Web 部件的远程 web 中的脚本文件，而不是使用主机 web 的"网站资产"列表中的引用脚本。
    
- 部署自定义网站设置过程中的预配置的 Web 部件。例如，作为自定义网站设置过程的一部分，您可能想要在创建新网站时向用户显示网站使用策略信息。 
    
- 在您的 Web 部件中为用户自动加载已筛选的内容。例如，您的脚本文件可以显示从外部系统读取的本地新闻信息。
    
- 允许用户使用"Web 部件库"中的 Web 部件将其他功能添加到其网站。
    
 **为改进此内容做贡献**
您可以获取最新的更新，或为改进 [GitHub 上的此文章](https://github.com/OfficeDev/PnP-Guidance)做贡献。您还可以为改进本示例以及 [GitHub 上的其他示例](https://github.com/OfficeDev/PnP)做贡献。有关示例的完整列表，请参阅 [模式和做法开发人员中心](http://dev.office.com/patterns-and-practices)。我们欢迎您做出 [贡献](https://github.com/OfficeDev/PnP/wiki/contributing-to-Office-365-developer-patterns-and-practices)。 

## 开始之前

若要开始，请从 GitHub 上的 [Office 365 开发人员模式和做法](https://github.com/OfficeDev/PnP/tree/dev)项目下载 [Core.AppScriptPart](https://github.com/OfficeDev/PnP/tree/dev/Samples/Core.AppScriptPart) 示例外接程序。


## 使用 Core.AppScriptPart 外接程序

当您运行代码示例，并选择"运行方案"时：


1. 选择"返回站点"。
    
2. 选择"页面">"编辑">"插入">"Web 部件"。
    
3. 在"类别"中，选择"外接程序脚本部件"，然后选择"用户配置文件信息"。
    
4. 选择"添加"。
    
5. 在"用户配置文件信息"Web 部件的右上角的下拉列表中，选择"编辑 Web 部件"。
    
6. 选择"编辑代码段"。
    
7. 查看  **<SCRIPT>** 元素。
    
    请注意， **src** 属性链接到远程 web 上的 JavaScript 文件。 **<SCRIPT>** 元素由 Core.AppScriptPartWeb\userprofileinformation.webpart 中的 **Content** 属性进行设置，如以下示例所示。由 **src** 属性将 JavaScript 文件链接到 Core.AppScriptPartWeb\Scripts\userprofileinformation.js。Userprofileinformation.js 从用户配置文件服务读取当前用户的配置文件信息，然后将此信息显示在 Web 部件中。
    
     **注释**  本文中的代码按原样提供，不提供任何明示或暗示的担保，包括对特定用途适用性、适销性或不侵权的默示担保。



  ```XML
  <property name="Content" type="string">&amp;lt;script type="text/javascript" src="https://localhost:44361/scripts/userprofileinformation.js"&amp;gt;&amp;lt;/script&amp;gt;
&amp;lt;div id="UserProfileAboutMe"&amp;gt;&amp;lt;div&amp;gt;
</property>
  ```

8. 选择"取消"。
    
9. 选择"保存"。
    

 **注释**  如果未显示您的用户配置文件图像，请打开您的 OneDrive for Business 网站，然后返回到主机 web。

在 Core.AppScriptPartWeb\Pages\Default.aspx 中，"运行方案"运行"btnScenario_Click"，这将执行以下操作：


1. 获取对"Web 部件库"文件夹的引用。
    
2. 使用 [FileCreationInformation ](https://msdn.microsoft.com/library/office/microsoft.sharepoint.client.filecreationinformation.aspx) 创建要从提供程序托管的外接程序上载到"Web 部件库"的 userprofileinformation.webpart 文件。 **folder.Files.Add** 将文件添加到"Web 部件库"。
    
3. 检索"Web 部件库"中的所有列表项，然后搜索 userprofileinformation.webpart。
    
4. 如果找到 userprofileinformation.webpart，则将 Web 部件分配给名为"外接程序脚本部件"的自定义组。
    



```C#
protected void btnScenario_Click(object sender, EventArgs e)
        {
            var spContext = SharePointContextProvider.Current.GetSharePointContext(Context);
            using (var clientContext = spContext.CreateUserClientContextForSPHost())
            {
                var folder = clientContext.Web.Lists.GetByTitle("Web Part Gallery").RootFolder;
                clientContext.Load(folder);
                clientContext.ExecuteQuery();

                // Upload the "userprofileinformation.webpart" file.
                using (var stream = System.IO.File.OpenRead(
                                Server.MapPath("~/userprofileinformation.webpart")))
                {
                    FileCreationInformation fileInfo = new FileCreationInformation();
                    fileInfo.ContentStream = stream;
                    fileInfo.Overwrite = true;
                    fileInfo.Url = "userprofileinformation.webpart";
                    File file = folder.Files.Add(fileInfo);
                    clientContext.ExecuteQuery();
                }

                // Update the group that the Web Part belongs to. Start by getting all list items in the Web Part Gallery, and then find the Web Part that was just uploaded.
                var list = clientContext.Web.Lists.GetByTitle("Web Part Gallery");
                CamlQuery camlQuery = CamlQuery.CreateAllItemsQuery(100);
                Microsoft.SharePoint.Client.ListItemCollection items = list.GetItems(camlQuery);
                clientContext.Load(items);
                clientContext.ExecuteQuery();
                foreach (var item in items)
                {
                    // Create new group.
                    if (item["FileLeafRef"].ToString().ToLowerInvariant() == "userprofileinformation.webpart")
                    {
                        item["Group"] = "add-in Script Part";
                        item.Update();
                        clientContext.ExecuteQuery();
                    }
                }

                lblStatus.Text = string.Format("add-in script part has been added to Web Part Gallery. You can find 'User Profile Information' script part under 'App Script Part' group in the <a href='{0}'>host web</a>.", spContext.SPHostUrl.ToString());
            }
        }
```


## 其他资源



- [Office 365 开发模式和做法解决方案指南](https://msdn.microsoft.com/library/office/dn904529.aspx)
    
- [使用 JavaScript 自定义您的 SharePoint 网站 UI](https://msdn.microsoft.com/library/dn913116.aspx)
    
