
# 替换根据列表定义创建的 SharePoint 列表
替换使用 SharePoint 中的列表定义创建的列表和库。 

 **上次修改时间：** 2015年8月7日

 _ **适用范围：** SharePoint 2013?| SharePoint Add-ins?| SharePoint Online_

 **注释**  名称"SharePoint 相关应用程序"将更改为"SharePoint 外接程序"。在转换期间，某些 SharePoint 产品和 Visual Studio 工具的文档和 UI 仍可能使用术语"SharePoint 相关应用程序"。有关详细信息，请参阅 [Office 和 SharePoint 相关应用程序的新名称](05b07b04-6c8b-4b7e-bd86-e32c589dfead.md#bk_newname)。

如果您使用列表定义在服务器场解决方案中创建列表，了解如何使用客户端对象模型 (CSOM) 将其转换为提供类似功能的新解决方案。本文介绍如何使用客户端对象模型 (CSOM) 执行以下操作：

- 查找使用列表定义创建的列表和库。
    
- 创建和配置新的列表。
    
- 添加视图或从列表中删除视图。
    
- 将内容从原始列表迁移到新列表。
    

 **重要信息**  服务器场解决方案无法迁移到 SharePoint Online。通过应用本文中所述的技术和代码，您可以构建一个新的解决方案，该解决方案具有与您的服务器场解决方案提供的类似功能，稍后可将其部署到 SharePoint Online。如果您使用就地转换方法，您可能需要将新解决方案部署到 SharePoint Online。如果您使用 Swing 或内容迁移方法，第三方工具可为您创建列表。有关详细信息，请参阅 [部署新的 SharePoint 外接程序的转换过程](https://msdn.microsoft.com/library/dn986827.aspx#sectionSection1)。本文中的代码需要其他代码才能提供可完整运行的解决方案。例如，本文不讨论如何向 Office 365 进行身份验证，如何实施必需的异常处理，等等。有关其他代码示例，请参阅 [Office 365 开发人员模式和做法项目](https://github.com/OfficeDev/PnP)。使用本文中所述的技术，一次仅更新几个列表。此外，查找要更新的列表时，您不应该按列表类型筛选列表。

 **为改进此内容做贡献**
您可以获取最新的更新，或为改进 [GitHub 上的此文章](https://github.com/OfficeDev/PnP-Guidance)做贡献。您还可以为改进本示例以及 [GitHub 上的其他示例](https://github.com/OfficeDev/PnP)做贡献。有关示例的完整列表，请参阅 [模式和做法开发人员中心](http://dev.office.com/patterns-and-practices)。我们欢迎您做出 [贡献](https://github.com/OfficeDev/PnP/wiki/contributing-to-Office-365-developer-patterns-and-practices)。 

## 开始之前

理想情况下，您应审核现有的服务器场解决方案，了解本文中所述的技术，然后计划如何将这些技术应用到现有的服务器场解决方案。如果您不熟悉服务器场解决方案或者没有可使用的现有服务器场解决方案，执行以下操作可能会很有帮助：


1. 下载 [Contoso.Intranet 解决方案](https://github.com/OfficeDev/PnP/tree/master/Reference%20Material/Contoso.Intranet)。阅读 [检查用于替换的网站和库的初始状态](https://github.com/OfficeDev/TrainingContent/blob/master/O3658/10%20Transformation%20guidance%20from%20farm%20solutions%20to%20app%20model/10-2%20Replacing%20Lists%20Created%20from%20Custom%20Templates/Lab.md#examine-the-initial-state-of-the-site-and-library-for-replacement)，快速了解如何使用列表定义以声明性方式创建了列表。
    
     **注释**  在 Contoso.Intranet 中，在 SP\ListTemplate\LTContosoLibrary 的 elements.xml 文件中，自定义列表模板的 ID 为 10003。您将使用此 ID 标识用于创建列表的模板。此外，请检查列表上定义的配置设置和视图。
2. 了解服务器场解决方案。有关详细信息，请参阅 [SharePoint 2010 体系结构概述](https://msdn.microsoft.com/zh-cn/library/office/gg552610%28v=office.14%29.aspx)和 [在 SharePoint 2013 中构建服务器场解决方案](https://msdn.microsoft.com/library/jj163902.aspx)。
    

## 替换根据列表定义创建的列表

要替换根据列表定义创建的列表，请执行以下操作：


1. 查找使用特定的基本模板创建的列表。
    
2. 使用现成的列表定义创建新列表。 
    
3. 配置列表设置。
    
4. 根据原始列表上设置的内容类型设置新列表上的内容类型。
    
5. （可选）添加视图。 
    
6. （可选）删除视图。
    
7. 将内容从原始列表迁移到新列表。
    
在以下代码中，此方法演示如何查找使用特定基本模板创建的列表。此方法：


1. 使用 [ClientContext](https://msdn.microsoft.com/library/microsoft.sharepoint.client.clientcontext.aspx) 获取使用 [Web.Lists](https://msdn.microsoft.com/library/office/microsoft.sharepoint.client.web.lists.aspx) 的当前 Web 中的所有列表。lambda 表达式中使用 Include 语句返回列表集合。返回的列表必须全都指定了 [BaseTemplate](https://msdn.microsoft.com/library/microsoft.sharepoint.client.list.basetemplate.aspx)、 [BaseType](https://msdn.microsoft.com/library/microsoft.sharepoint.client.list.basetype.aspx) 和 [Title](https://msdn.microsoft.com/library/microsoft.sharepoint.client.list.title.aspx) 的值。
    
2. 对于返回的列表集合中的每个列表，如果  **List.BaseTemplate** 等于 10003，请将列表添加到要替换的列表集合（称为 **listsToReplace** ）请记住，10003 是我们在 Contoso.Intranet 示例中看到的自定义列表模板的标识符。
    

 **注释**  本文中的代码按原样提供，不提供任何明示或暗示的担保，包括对特定用途适用性、适销性或不侵权的默示担保。




```C#
static void Main(string[] args)
{
    using (var clientContext = new ClientContext("http://w15-sp/sites/ftclab"))
    {
        Web web = clientContext.Web;
        ListCollection listCollection = web.Lists;
        clientContext.Load(listCollection,
                            l => l.Include(list => list.BaseTemplate,
                                            list => list.BaseType,
                                            list => list.Title));
        clientContext.ExecuteQuery();
        var listsToReplace = new List<List>();
        foreach (List list in listCollection)
        {
            // 10003 is the custom list template ID of the list template you're replacing.
            if (list.BaseTemplate == 10003)
            {
                listsToReplace.Add(list);
            }
        }
        foreach (List list in listsToReplace)
        {
            ReplaceList(clientContext, listCollection, list);
        }
    }
}
```


 **重要信息**  在前面的代码中，您首先循环访问 ListCollection 以选择需要修改的列表，然后调用 ReplaceList 开始修改列表。在循环访问集合的同时修改集合内容将抛出异常，因此必须使用此模式。

标识应替换的列表后， **ReplaceList** 将显示替换列表要执行的操作的顺序。




```C#
private static void ReplaceList(ClientContext clientContext, ListCollection listCollection, List listToBeReplaced)
{
    var newList = CreateReplacementList(clientContext, listCollection, listToBeReplaced);

    SetListSettings(clientContext, listToBeReplaced, newList);

    SetContentTypes(clientContext, listToBeReplaced, newList);

    AddViews(clientContext, listToBeReplaced, newList);

    RemoveViews(clientContext, listToBeReplaced, newList);

    MigrateContent(clientContext, listToBeReplaced, newList);
}
```

为了创建新列表， **CreateReplacementList** 使用 [ListCreationInformation](https://msdn.microsoft.com/library/office/microsoft.sharepoint.client.listcreationinformation.aspx)。新列表的标题设置为现有列表的标题，并附加外接程序。 [ListTemplateType](https://msdn.microsoft.com/zh-cn/library/office/microsoft.sharepoint.client.listtemplatetype.aspx) 枚举用于将列表的模板类型设置为文档库。如果您将根据其他模板类型创建列表，请务必使用正确的模板类型。例如，如果您要创建日历列表，请使用 **ListTemplateType.Events** ，而不是 **ListTemplateType.DocumentLibrary** 。




```C#
private static List CreateReplacementList(ClientContext clientContext, ListCollection lists,List listToBeReplaced)
{
    var creationInformation = new ListCreationInformation
    {
        Title = listToBeReplaced.Title + "add-in",
        TemplateType = (int) ListTemplateType.DocumentLibrary,
    };
    List newList = lists.Add(creationInformation);
    clientContext.ExecuteQuery();
    return newList;
}
```

 **SetListSettings** 通过以下方式将原始列表设置应用到新列表：


1. 从原始列表中获取各种列表设置。
    
2. 将原始列表的列表设置应用到新列表。
    



```C#
private static void SetListSettings(ClientContext clientContext, List listToBeReplaced, List newList)
{
    clientContext.Load(listToBeReplaced, 
                        l => l.EnableVersioning, 
                        l => l.EnableModeration, 
                        l => l.EnableMinorVersions,
                        l => l.DraftVersionVisibility );
    clientContext.ExecuteQuery();
    newList.EnableVersioning = listToBeReplaced.EnableVersioning;
    newList.EnableModeration = listToBeReplaced.EnableModeration;
    newList.EnableMinorVersions= listToBeReplaced.EnableMinorVersions;
    newList.DraftVersionVisibility = listToBeReplaced.DraftVersionVisibility;
    newList.Update();
    clientContext.ExecuteQuery();
}
```


 **注释**  根据您的要求，您的原始列表的列表设置可能有所不同。查看列表设置并更改  **SetListSettings** ，确保您的原始列表设置已应用到新列表。

 **SetContentTypes** 通过以下方式设置新列表的内容类型：


1. 从原始列表中获取内容类型信息。
    
2. 从新列表中获取内容类型信息。
    
3. 确定原始列表是否启用了内容类型。如果原始列表未启用内容类型， **SetContentTypes** 将退出。如果原始列表已启用内容类型， **SetContentTypes** 将使用 **newList.ContentTypesEnabled = true** 启用新列表上的内容类型。
    
4. 对于原始列表中的每个内容类型，根据内容类型的名称，使用  **newList.ContentTypes.Any(ct => ct.Name == contentType.Name)** 搜索新列表上的内容类型以查找匹配的内容类型。如果内容类型不在新列表中，将其添加到列表。
    
5. 再次加载  **newList** ，因为 [AddExistingContentType](https://msdn.microsoft.com/library/office/microsoft.sharepoint.client.contenttypecollection.addexistingcontenttype.aspx) 可能已更改内容类型。
    
6. 对于  **newList** 中的每种内容类型，根据 [ContentType.Name](https://msdn.microsoft.com/library/office/microsoft.sharepoint.client.contenttype.name.aspx)，使用  **listToBeReplaced.ContentTypes.Any(ct => ct.Name == contentType.Name)** 确定内容类型是否与原始列表中的内容类型相匹配。如果未找到匹配，内容类型将添加到将从新列表删除的 **contentTypesToDelete** 中。
    
7. 通过调用 [ContentType.DeleteObject](https://msdn.microsoft.com/library/office/microsoft.sharepoint.client.contenttype.deleteobject.aspx) 删除内容类型。
    

 **注释**  如果您使用就地转换方法，且您的内容类型已使用功能框架以声明方式部署，则您需要执行以下操作： 




```C#
private static void SetContentTypes(ClientContext clientContext, List listToBeReplaced, List newList)
{
    clientContext.Load(listToBeReplaced,
                        l => l.ContentTypesEnabled,
                        l => l.ContentTypes);
    clientContext.Load(newList,
                        l => l.ContentTypesEnabled,
                        l => l.ContentTypes);
    clientContext.ExecuteQuery();

    // If the original list doesn't use content types, do not proceed any further.
    if (!listToBeReplaced.ContentTypesEnabled) return;

    newList.ContentTypesEnabled = true;
    newList.Update();
    clientContext.ExecuteQuery();
    foreach (var contentType in listToBeReplaced.ContentTypes)
    {
        if (!newList.ContentTypes.Any(ct => ct.Name == contentType.Name))
        {
            // Current content type needs to be added to the new list. Note that the content type is added to the list, not the site.           
            newList.ContentTypes.AddExistingContentType(contentType.Parent);
            newList.Update();
            clientContext.ExecuteQuery();
        }
    }
    // Reload the content types on newList because they might have changed when AddExistingContentType was called. 
    clientContext.Load(newList, l => l.ContentTypes);
    clientContext.ExecuteQuery();
    // Remove any content types that are not needed.
    var contentTypesToDelete = new List<ContentType>();
    foreach (var contentType in newList.ContentTypes)
    {
        if (!listToBeReplaced.ContentTypes.Any(ct => ct.Name == contentType.Name))
        {
            // Current content type needs to be removed from new list.
            contentTypesToDelete.Add(contentType);
        }
    }
    foreach (var contentType in contentTypesToDelete)
    {
        contentType.DeleteObject();
    }
    newList.Update();
    clientContext.ExecuteQuery();
}
```


 **注释**  在这种情况下，新的列表可以接受来自原始列表的内容。您还可以选择添加和删除视图。 

用户可以添加或删除在列表中定义的视图，以满足其业务需求。因此，您可能需要添加或删除新列表中的视图。 **AddViews** 通过以下方式将视图从原始列表添加到新列表：


1. 使用 [List.Views](https://msdn.microsoft.com/library/office/microsoft.sharepoint.client.list.views.aspx) 获取原始列表上的所有视图。
    
2. 使用 lambda 表达式在  **views** 集合上加载各种视图属性。
    
3. 对于原始列表中的每个视图，使用 [ViewCreationInformation](https://msdn.microsoft.com/library/office/microsoft.sharepoint.client.viewcreationinformation.aspx) 创建视图。各种属性根据原始视图中的视图属性在新列表上设置。调用 **GetViewType** 帮助程序方法以确定视图的 [ViewType](https://msdn.microsoft.com/library/office/microsoft.sharepoint.client.viewtype.aspx)，然后新视图将添加到名为  **viewsToCreate** 的视图列表。
    
4. 在列表的视图集合中使用  **Add** 方法将视图添加到 **List.Views** 集合。
    



```C#
private static void AddViews(ClientContext clientContext, List listToBeReplaced, List newList)
{
    ViewCollection views = listToBeReplaced.Views;
    clientContext.Load(views,
                        v => v.Include(view => view.Paged,
                            view => view.PersonalView,
                            view => view.ViewQuery,
                            view => view.Title,
                            view => view.RowLimit,
                            view => view.DefaultView,
                            view => view.ViewFields,
                            view => view.ViewType));
    clientContext.ExecuteQuery();

    // Build a list of views which exist on the original list only.
    var viewsToCreate = new List<ViewCreationInformation>();
    foreach (View view in listToBeReplaced.Views)
    {
      var createInfo = new ViewCreationInformation
      {
          Paged = view.Paged,
          PersonalView = view.PersonalView,
          Query = view.ViewQuery,
          Title = view.Title,
          RowLimit = view.RowLimit,
          SetAsDefaultView = view.DefaultView,
          ViewFields = view.ViewFields.ToArray(),
          ViewTypeKind = GetViewType(view.ViewType),
      };
      viewsToCreate.Add(createInfo);
    }
    
    foreach (ViewCreationInformation newView in viewsToCreate)
    {
        newList.Views.Add(newView);
    }
    newList.Update();
}

private static ViewType GetViewType(string viewType)
{
    switch (viewType)
    {
        case "HTML":
            return ViewType.Html;
        case "GRID":
            return ViewType.Grid;
        case "CALENDAR":
            return ViewType.Calendar;
        case "RECURRENCE":
            return ViewType.Recurrence;
        case "CHART":
            return ViewType.Chart;
        case "GANTT":
            return ViewType.Gantt;
        default:
            return ViewType.None;
    }
}
```

在以下代码中， **RemoveViews** 通过以下方式从新列表中删除视图：


1. 使用  **List.Views** 属性获取新列表上的列表视图
    
2. 对于新列表中的每个视图，通过使用  **!listToBeReplaced.Views.Any(v => v.Title == view.Title** 与视图标题相匹配，来确定视图在原始列表中是否存在。如果新列表中的某个视图在原始列表中没有匹配视图，请将视图添加到 **viewsToRemove** 。
    
3. 使用 [View.DeleteObject](https://msdn.microsoft.com/library/office/microsoft.sharepoint.client.view.deleteobject.aspx) 删除 **viewsToRemove** 中的所有视图。
    



```C#
private static void RemoveViews(ClientContext clientContext, List listToBeReplaced, List newList)
{
    // Get the list of views on the new list.
    clientContext.Load(newList, l => l.Views);
    clientContext.ExecuteQuery();

    var viewsToRemove = new List<View>();
    foreach (View view in newList.Views)
    {
        if (!listToBeReplaced.Views.Any(v => v.Title == view.Title))
        {
            // If there is no matching view in the source list, add the view to the list of views to be deleted.
            viewsToRemove.Add(view);
        }
    }
    foreach (View view in viewsToRemove)
    {
        view.DeleteObject();
    }
    newList.Update();
    clientContext.ExecuteQuery();
}
```

 **MigrateContent** 通过以下方式将内容从原始列表迁移到新列表：


1. 使用 [List.RootFolder](https://msdn.microsoft.com/library/office/microsoft.sharepoint.client.list.rootfolder.aspx) 检索目标以将文件复制到新列表的根文件夹。目标列表文件夹的服务器相对 URL 使用 [Folder.ServerRelativeUrl](https://msdn.microsoft.com/library/office/microsoft.sharepoint.client.folder.serverrelativeurl.aspx) 进行检索。
    
2. 使用  **List.RootFolder** 检索文件源或原始列表的根文件夹。列表文件夹的服务器相对 URL 和源的根文件夹中的所有文件均使用 **clientContext** 对象进行加载。
    
3. 对于源中的每个文件，创建存储文件的新 URL 的  **newUrl** 。 **newUrl** 通过将源根文件夹替换为目标根文件夹进行创建。
    
4. 使用 [File.CopyTo](https://msdn.microsoft.com/library/office/microsoft.sharepoint.client.file.copyto.aspx) 将文件复制到目标根文件夹的 URL。您也可以选择使用 [File.MoveTo](https://msdn.microsoft.com/library/office/microsoft.sharepoint.client.file.moveto.aspx) 方法将文件移至目标 URL。
    

 **注释**  以下代码将返回所有列表项。在您的生产环境中，考虑通过实施循环优化以下代码，并使用多次迭代迁移少量列表项。




```C#
private static void MigrateContent(ClientContext clientContext, List listToBeReplaced, List newList)
{
    ListItemCollection items = listToBeReplaced.GetItems(CamlQuery.CreateAllItemsQuery());
    Folder destination = newList.RootFolder;
    Folder source = listToBeReplaced.RootFolder;
    clientContext.Load(destination,
                        d => d.ServerRelativeUrl);
    clientContext.Load(source,
                        s => s.Files,
                        s => s.ServerRelativeUrl);
    clientContext.Load(items,
                        i => i.IncludeWithDefaultProperties(item => item.File));
    clientContext.ExecuteQuery();


    foreach (File file in source.Files)
    {
        string newUrl = file.ServerRelativeUrl.Replace(source.ServerRelativeUrl, destination.ServerRelativeUrl);
          file.CopyTo(newUrl, true);
          //file.MoveTo(newUrl, MoveOperations.Overwrite);
    }
    clientContext.ExecuteQuery();
}
```


 **注释**  上面的代码演示如何迁移存储在列表的根文件夹中的文件。如果您的列表具有子文件夹，您需要添加额外代码，以迁移子文件夹及其内容。如果您的列表使用工作流，则需要其他代码以便将工作流关联到新列表。


## 其他资源



- [将服务器场解决方案转换为 SharePoint 外接程序模型](https://msdn.microsoft.com/library/dn986827.aspx)
    
- [SharePoint 2013](https://msdn.microsoft.com/library/office/jj162979.aspx)
    
