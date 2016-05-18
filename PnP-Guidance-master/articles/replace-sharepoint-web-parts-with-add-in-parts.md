
# 使用外接程序部件替换 SharePoint Web 部件
执行转换过程，使用 SharePoint 客户端对象模型 (CSOM) 将 Web 部件替换为外接程序部件。

 **上次修改时间：** 2015年8月11日

 _ **适用范围：** SharePoint 2013?| SharePoint Add-ins?| SharePoint Online_

 **注释**  名称"SharePoint 相关应用程序"将更改为"SharePoint 外接程序"。在转换期间，某些 SharePoint 产品和 Visual Studio 工具的文档和 UI 仍可能使用术语"SharePoint 相关应用程序"。有关详细信息，请参阅 [Office 和 SharePoint 相关应用程序的新名称](05b07b04-6c8b-4b7e-bd86-e32c589dfead.md#bk_newname)。

您可以根据转换过程，将页面上的 SharePoint Web 部件替换为外接程序部件，方法是使用 CSOM 查找和删除特定的 Web 部件，然后添加新的外接程序部件。

 **重要信息**  服务器场解决方案无法迁移到 SharePoint Online。通过应用本文中所述的技术和代码，您可以构建一个新的解决方案，该解决方案具有与您的服务器场解决方案提供的类似功能，稍后可将其部署到 SharePoint Online。应用这些技术后，您的页面将更新为使用外接程序部件，稍后可将其迁移到 SharePoint Online。 本文中的代码需要其他代码才能提供可完整运行的解决方案。例如，本文不讨论如何向 Office 365 进行身份验证，如何实施必需的异常处理，等等。有关其他代码示例，请参阅 [Office 365 发人员模式和做法项目](https://github.com/OfficeDev/PnP)。

 **为改进此内容做贡献**
您可以获取最新的更新，或为改进 [GitHub 上的此文章](https://github.com/OfficeDev/PnP-Guidance)做贡献。您还可以为改进本示例以及 [GitHub 上的其他示例](https://github.com/OfficeDev/PnP)做贡献。有关示例的完整列表，请参阅 [模式和做法开发人员中心](http://dev.office.com/patterns-and-practices)。我们欢迎您做出 [贡献](https://github.com/OfficeDev/PnP/wiki/contributing-to-Office-365-developer-patterns-and-practices)。 

## 开始之前

在执行本文中的步骤以将 Web 部件替换为外接程序部件之前，请确保您：


- 使用配置为支持 外接程序的 SharePoint 环境。SharePoint Online 配置为支持 外接程序。如果您使用 SharePoint Server 2013 本地部署，请参阅[配置适用于 SharePoint 外接程序 (SharePoint 2013) 的环境](https://technet.microsoft.com/library/fp161236%28v=office.15%29)。
    
- 已将新的外接程序部件部署到 SharePoint。
    
- 已在"Web"上分配对 外接程序的"FullControl"权限。有关详细信息，请参阅 [SharePoint 2013 上的外接程序权限](https://msdn.microsoft.com/library/office/fp142383.aspx)。
    

## 将 Web 部件替换为外接程序部件

要将 Web 部件替换为新的外接程序部件，请执行以下操作：


1. 导出新的外接程序部件。使用导出的外接程序部件获取外接程序部件定义。 
    
2. 查找具有要替换的 Web 部件的所有页面，然后删除 Web 部件。 
    
3. 使用外接程序部件定义在页面上创建新的外接程序部件。 
    
要使用 CSOM 将 Web 部件替换为外接程序部件，您需要导出外接程序部件以获取外接程序部件定义。要导出外接程序部件以获取外接程序部件的定义，请执行以下操作：


1. 打开您的 SharePoint 网站，选择"设置"或齿轮图标，然后选择"编辑页面"。
    
2. 在功能区中选择"插入">"外接程序部件"。
    
3. 选择您的外接程序部件，然后选择"添加"。
    
4. 将外接程序部件添加到您的页面后，在外接程序部件的右上角选择向下箭头，然后选择"编辑 Web 部件"。
    
5. 在"高级"下的"导出模式"中，选择"导出所有数据"，然后选择"确定"。
    
6. 返回到显示外接程序部件的编辑页面后，在外接程序部件的右上角选择向下箭头，然后选择"导出"。
    
7. 选择"保存"。
    
8. 下载完成后，选择"打开文件夹"。
    
9. 打开"笔记本"，然后选择"文件">"打开"。
    
10. 选择您下载的外接程序部件文件，然后选择"打开"查看外接程序部件定义。
    

 **注释**  本文中的代码按原样提供，不提供任何明示或暗示的担保，包括对特定用途适用性、适销性或不侵权的默示担保。




```XML
<webParts>
  <webPart xmlns="http://schemas.microsoft.com/WebPart/v3">
    <metaData>
      <type name="Microsoft.SharePoint.WebPartPages.ClientWebPart, Microsoft.SharePoint, Version=16.0.0.0, Culture=neutral, PublicKeyToken=71e9bce111e9429c" />
      <importErrorMessage>Cannot import this Web Part.</importErrorMessage>
    </metaData>
    <data>
      <properties>
        <property name="TitleIconImageUrl" type="string" />
        <property name="Direction" type="direction">NotSet</property>
        <property name="ExportMode" type="exportmode">All</property>
        <property name="HelpUrl" type="string" />
        <property name="Hidden" type="bool">False</property>
        <property name="Description" type="string">WelcomeAppPart Description</property>
        <property name="FeatureId" type="System.Guid, mscorlib, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b77a5c561934e089">0b846986-3474-4f1a-93cf-b7817ef057f9</property>
        <property name="CatalogIconImageUrl" type="string" />
        <property name="Title" type="string">WelcomeAppPart</property>
        <property name="AllowHide" type="bool">True</property>
        <property name="ProductWebId" type="System.Guid, mscorlib, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b77a5c561934e089">741c5404-f43e-4f01-acfb-fcd100fc7d24</property>
        <property name="AllowZoneChange" type="bool">True</property>
        <property name="TitleUrl" type="string" />
        <property name="ChromeType" type="chrometype">Default</property>
        <property name="AllowConnect" type="bool">True</property>
        <property name="Width" type="unit" />
        <property name="Height" type="unit" />
        <property name="WebPartName" type="string">WelcomeAppPart</property>
        <property name="HelpMode" type="helpmode">Navigate</property>
        <property name="AllowEdit" type="bool">True</property>
        <property name="AllowMinimize" type="bool">True</property>
        <property name="ProductId" type="System.Guid, mscorlib, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b77a5c561934e089">0b846986-3474-4f1a-93cf-b7817ef057f8</property>
        <property name="AllowClose" type="bool">True</property>
        <property name="ChromeState" type="chromestate">Normal</property>
      </properties>
    </data>
  </webPart>
</webParts>
```

要在 CSOM 代码中使用外接程序部件定义，请执行以下操作：


- 在外接程序部件定义中，将所有双引号 (") 替换为一对双引号 ("")。
    
- 将外接程序部件定义分配到将在 CSOM 代码中使用的一个字符串。
    



```C#
private const string appPartXml = @"<webParts>
  <webPart xmlns=""http://schemas.microsoft.com/WebPart/v3"">
    <metaData>
      <type name=""Microsoft.SharePoint.WebPartPages.ClientWebPart, Microsoft.SharePoint, Version=15.0.0.0, Culture=neutral, PublicKeyToken=71e9bce111e9429c"" />
      <importErrorMessage>Cannot import this Web Part.</importErrorMessage>
    </metaData>
    <data>
      <properties>
        <property name=""TitleIconImageUrl"" type=""string"" />
        <property name=""Direction"" type=""direction"">NotSet</property>
        <property name=""ExportMode"" type=""exportmode"">All</property>
        <property name=""HelpUrl"" type=""string"" />
        <property name=""Hidden"" type=""bool"">False</property>
        <property name=""Description"" type=""string"">WelcomeAppPart Description</property>
        <property name=""FeatureId"" type=""System.Guid, mscorlib, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b77a5c561934e089"">0b846986-3474-4f1a-93cf-b7817ef057f9</property>
        <property name=""CatalogIconImageUrl"" type=""string"" />
        <property name=""Title"" type=""string"">WelcomeAppPart Title</property>
        <property name=""AllowHide"" type=""bool"">True</property>
        <property name=""ProductWebId"" type=""System.Guid, mscorlib, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b77a5c561934e089"">717c00a1-08ea-41a5-a2b7-4c8f9c1ce770</property>
        <property name=""AllowZoneChange"" type=""bool"">True</property>
        <property name=""TitleUrl"" type=""string"" />
        <property name=""ChromeType"" type=""chrometype"">Default</property>
        <property name=""AllowConnect"" type=""bool"">True</property>
        <property name=""Width"" type=""unit"" />
        <property name=""Height"" type=""unit"" />
        <property name=""WebPartName"" type=""string"">WelcomeAppPart</property>
        <property name=""HelpMode"" type=""helpmode"">Navigate</property>
        <property name=""AllowEdit"" type=""bool"">True</property>
        <property name=""AllowMinimize"" type=""bool"">True</property>
        <property name=""ProductId"" type=""System.Guid, mscorlib, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b77a5c561934e089"">0b846986-3474-4f1a-93cf-b7817ef057f8</property>
        <property name=""AllowClose"" type=""bool"">True</property>
        <property name=""ChromeState"" type=""chromestate"">Normal</property>
      </properties>
    </data>
  </webPart>
</webParts>";
```

 **ReplaceWebPartsWithAppParts** 通过以下方式开始查找要替换的 Web 部件：


1. 从 [Web ](https://msdn.microsoft.com/library/office/microsoft.sharepoint.client.web.aspx ) 中获取一些属性以查找网站上的"页面"库。
    
2. 在"页面"库中，获取列表项以及与列表项关联的文件。
    
3. 对于从"页面"库返回的每个列表项，调用  **FindWebPartToReplace** 。
    



```C#
protected void ReplaceWebPartsWithAppParts(object sender, EventArgs e)
{
    var spContext = SharePointContextProvider.Current.GetSharePointContext(Context);
    using (var clientContext = spContext.CreateUserClientContextForSPHost())
    {
        Web web = clientContext.Web;
        // Get properties from the Web.
        clientContext.Load(web,
                            w => w.ServerRelativeUrl,
                            w => w.AllProperties);
        clientContext.ExecuteQuery();
        // Read the Pages library name from the Web properties.
        var pagesListName = web.AllProperties["__pageslistname"] as string;

        var list = web.Lists.GetByTitle(pagesListName);
        var items = list.GetItems(CamlQuery.CreateAllItemsQuery());
        // Get the file associated with each list item.
        clientContext.Load(items,
                            i => i.Include(
                                    item => item.File));
        clientContext.ExecuteQuery();

        // Iterate through all pages in the Pages list.
        foreach (var item in items)
        {
            FindWebPartForReplacement(item, clientContext, web);
        }
    }
}
```

 **FindWebPartToReplace** 通过以下方式查找应替换为新外接程序部件的 Web 部件：


1. 将  **page** 设置为与从"页面"库返回的列表项关联的文件。
    
2. 使用 [LimitedWebPartManager](https://msdn.microsoft.com/library/office/microsoft.sharepoint.client.webparts.limitedwebpartmanager.aspx) 查找特定页面上的所有 Web 部件。每个 Web 部件的标题也将在 lambda 表达式中返回。
    
3. 对于 Web 部件管理器的 [WebParts](https://msdn.microsoft.com/library/office/microsoft.sharepoint.client.webparts.limitedwebpartmanager.webparts.aspx) 属性中的每个 Web 部件，确定 Web 部件的标题和 **oldWebPartTitle** 变量（设置为要替换的 Web 部件的标题）是否相同。如果 Web 部件标题与 **oldWebPartTitle** 相同，调用 **ReplaceWebPart** ；否则，请继续执行 **foreach** 循环的下一次迭代。
    



```C#
private static void FindWebPartToReplace(ListItem item, ClientContext clientContext, Web web)
{
    File page = item.File;
    // Requires Full Control permissions on the Web.
    LimitedWebPartManager webPartManager = page.GetLimitedWebPartManager(PersonalizationScope.Shared);
    clientContext.Load(webPartManager,
                        wpm => wpm.WebParts,
                        wpm => wpm.WebParts.Include(
                                            wp => wp.WebPart.Title));
    clientContext.ExecuteQuery();

    foreach (var oldWebPartDefinition in webPartManager.WebParts)
    {
        var oldWebPart = oldWebPartDefinition.WebPart;
        // Modify the Web Part if we find an old Web Part with the same title.
        if (oldWebPart.Title != oldWebPartTitle) continue;

        ReplaceWebPart(web, item, webPartManager, oldWebPartDefinition, clientContext, page);
    }
}
```

 **ReplaceWebPart** 通过以下方式替换新的 Web 部件：


1. 使用 [LimitedWebPartManager.ImportWebPart ](https://msdn.microsoft.com/library/office/microsoft.sharepoint.client.webparts.limitedwebpartmanager.importwebpart.aspx) 将 **appPartXml** 中的 XML 字符串转换为 [WebPartDefinition](https://msdn.microsoft.com/library/office/microsoft.sharepoint.client.webparts.webpartdefinition.aspx)。
    
2. 使用 [LimitedWebPartManager.AddWebPart](https://msdn.microsoft.com/library/office/microsoft.sharepoint.client.webparts.limitedwebpartmanager.addwebpart.aspx) 将 Web 部件添加到特定 Web 部件区域中的某个页面。确保将 **zoneId** 传递到 **AddWebPart**，否则当运行  **ExecuteQuery** 时将抛出异常。
    
3. 使用 [WebPartDefinition.DeleteWebPart](https://msdn.microsoft.com/library/office/microsoft.sharepoint.client.webparts.webpartdefinition.deletewebpart.aspx) 从页面中删除旧的 Web 部件。
    



```C#
private static void ReplaceWebPart(Web web, ListItem item, LimitedWebPartManager webPartManager,
      WebPartDefinition oldWebPartDefinition, ClientContext clientContext, File page)
  {
     
      // Create a Web Part definition using the XML string.
      var definition = webPartManager.ImportWebPart(appPartXml);
      webPartManager.AddWebPart(definition.WebPart, "RightColumn", 0);

      // Delete the old Web Part from the page.
      oldWebPartDefinition.DeleteWebPart();
      clientContext.Load(page,
                          p => p.CheckOutType,
                          p => p.Level);

      clientContext.ExecuteQuery();
     
  }
```


## 其他资源



- [将服务器场解决方案转换为 SharePoint 外接程序模型](https://msdn.microsoft.com/library/dn986827.aspx)
    
- [Provisioning.Pages](https://github.com/OfficeDev/PnP/tree/master/Scenarios/Provisioning.Pages)
    
