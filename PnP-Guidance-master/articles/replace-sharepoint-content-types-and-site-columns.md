
# 替换 SharePoint 内容类型和网站栏
使用 CSOM 替换 SharePoint 内容类型和网站栏，将网站栏添加到新内容类型，并使用新内容类型替换内容类型。

 **上次修改时间：** 2015年8月7日

 _ **适用范围：** SharePoint 2013?| SharePoint Add-ins?| SharePoint Online_

 **注释**  名称"SharePoint 相关应用程序"将更改为"SharePoint 外接程序"。在转换期间，某些 SharePoint 产品和 Visual Studio 工具的文档和 UI 仍可能使用术语"SharePoint 相关应用程序"。有关详细信息，请参阅 [Office 和 SharePoint 相关应用程序的新名称](05b07b04-6c8b-4b7e-bd86-e32c589dfead.md#bk_newname)。

了解替换内容类型和网站栏，将网站栏添加到新内容类型，然后使用 SharePoint 客户端对象模型 (CSOM) 将以前的内容类型替换为新内容类型时的转换过程。

 **重要信息**  服务器场解决方案无法迁移到 SharePoint Online。通过应用本文中所述的技术和代码，您可以构建一个新的解决方案，该解决方案使用更新后的内容类型和网站栏，并向您的服务器场解决方案或声明性沙盒解决方案提供类似的功能。该新解决方案可以部署到 SharePoint Online。本文中的代码需要其他代码才能提供可完整运行的解决方案。例如，本文不讨论如何向 Office 365 进行身份验证，如何实施必需的异常处理，等等。有关其他代码示例，请参阅 [Office 365 开发人员模式和做法项目](https://github.com/OfficeDev/PnP)。

 **为改进此内容做贡献**
您可以获取最新的更新，或为改进 [GitHub 上的此文章](https://github.com/OfficeDev/PnP-Guidance)做贡献。您还可以为改进本示例以及 [GitHub 上的其他示例](https://github.com/OfficeDev/PnP)做贡献。有关示例的完整列表，请参阅 [模式和做法开发人员中心](http://dev.office.com/patterns-and-practices)。我们欢迎您做出 [贡献](https://github.com/OfficeDev/PnP/wiki/contributing-to-Office-365-developer-patterns-and-practices)。 

## 替换内容类型和网站栏

要使用 CSOM 替换内容类型和网站栏，请执行以下操作：


1. 创建新的内容类型。 
    
2. 创建新的网站栏（也称为字段）。
    
3. 将新的网站栏添加到新的内容类型。
    
4. 使用新的内容类型替换旧的内容类型引用。
    
在以下代码中， **Main** 显示使用 CSOM 替换内容类型和网站栏需执行的操作的顺序。


 **注释**  本文中的代码按原样提供，不提供任何明示或暗示的担保，包括对特定用途适用性、适销性或不侵权的默示担保。




```C#
static void Main(string[] args)
{
    using (var clientContext = new ClientContext("http://contoso.sharepoint.com"))
    {

        Web web = clientContext.Web;
        
        CreateContentType(clientContext, web);
        CreateSiteColumn(clientContext, web);
        AddSiteColumnToContentType(clientContext, web);

        // Replace the old content type with the new content type.
        ReplaceContentType(clientContext, web);
    }

}
```

在以下代码中， **GetContentTypeByName** 使用以下方式从当前网站获取内容类型：


1. 使用 [Web.ContentTypes](https://msdn.microsoft.com/library/office/microsoft.sharepoint.client.web.contenttypes.aspx) 属性获取 [ContentTypeCollection](https://msdn.microsoft.com/library/office/microsoft.sharepoint.client.contenttypecollection.aspx)，即当前网站上的内容类型集合。
    
2. 通过将网站内容类型名称与  **name** 参数提交的现有内容类型名称相匹配，从网站查找并返回内容类型。
    



```C#
private static ContentType GetContentTypeByName(ClientContext cc, Web web, string name)
{
    ContentTypeCollection contentTypes = web.ContentTypes;
    cc.Load(contentTypes);
    cc.ExecuteQuery();
    return contentTypes.FirstOrDefault(o => o.Name == name);
}
```

在以下代码中， **CreateContentType** 通过以下方式创建新的内容类型：


1. 创建一个名为  **contentTypeName** 的常量，以存储内容类型的名称。新内容类型的名称将设置为之前内容类型的名称。
    
2. 调用  **GetContentTypeByName** 以在网站上查找匹配的内容类型。
    
3. 如果内容类型已存在，不需要执行任何进一步的操作，当调用  **return** 时，控制权将传回 **Main** 。如果内容类型不存在，内容类型属性将使用名为 **newCt** 的 [ContentTypeCreationInformation](https://msdn.microsoft.com/library/office/microsoft.sharepoint.client.contenttypecreationinformation.aspx) 对象进行设置。新内容类型 ID 将使用基文档内容类型 ID **0x0101** 分配给 **newCt.Id** 。有关详细信息，请参阅 [基内容类型层次结构](https://msdn.microsoft.com/library/office/ms452896%28v=office.14%29.aspx)。
    
4. 使用 [ContentTypeCollection.Add](https://msdn.microsoft.com/library/office/microsoft.sharepoint.client.contenttypecollection.add.aspx) 添加新的内容类型。
    



```C#
private static void CreateContentType(ClientContext cc, Web web)
{
    // The new content type will be created using this name.
    const string contentTypeName = "ContosoDocumentByCSOM";

    // Determine whether the content type already exists.
    var contentType = GetContentTypeByName(cc, web, contentTypeName);

    // The content type exists already. No further action required.
    if (contentType != null) return;

    // Create the content type using the ContentTypeInformation object.
    ContentTypeCreationInformation newCt = new ContentTypeCreationInformation();
    newCt.Name = "ContosoDocumentByCSOM";

    // Create the new content type based on the out-of-the-box document (0x0101) and assign the ID to the new content type.
    newCt.Id = "0x0101009189AB5D3D2647B580F011DA2F356FB2";

    // Assign the content type to a specific content type group.
    newCt.Group = "Contoso Content Types";

    ContentType myContentType = web.ContentTypes.Add(newCt);
    cc.ExecuteQuery();
}
```

在以下代码中， **CreateSiteColumn** 通过以下方式创建新的网站栏：


1. 创建一个名为  **fieldName** 的常量，以存储字段的名称。新字段的名称将设置为之前字段的名称。
    
2. 使用 [Web.Fields](https://msdn.microsoft.com/library/office/microsoft.sharepoint.client.web.fields.aspx) 属性获取网站上定义的网站栏。
    
3. 通过将网站上的字段名称与  **fieldName** 相匹配来查找网站上的匹配字段。如果字段已存在，不需要执行任何进一步的操作，当调用 **return** 时，控制权将传回 **Main** 。如果字段不存在，将向 **FieldAsXML** 分配一个指定字段架构的 CAML 字符串，然后使用 [FieldCollection.AddFieldAsXml](https://msdn.microsoft.com/library/office/microsoft.sharepoint.client.fieldcollection.addfieldasxml.aspx) 创建字段。
    



```C#
private static void CreateSiteColumn(ClientContext cc, Web web)
{
    // The new field will be created using this name.
    const string fieldName = "ContosoStringCSOM";

    // Load the list of fields on the site.
    FieldCollection fields = web.Fields;
    cc.Load(fields);
    cc.ExecuteQuery();

    // Check fields on the site for a match.
    var fieldExists = fields.Any(f => f.InternalName == fieldName);

     // The field exists already. No further action required.    
    if (fieldExists) return;

    // Field does not exist, so create the new field.
    string FieldAsXML = @"<Field ID='{CB8E24F6-E1EE-4482-877B-19A51B4BE319}' 
                                Name='" + fieldName + @"' 
                                DisplayName='Contoso String by CSOM' 
                                Type='Text' 
                                Hidden='False' 
                                Group='Contoso Site Columns' 
                                Description='Contoso Text Field' />";
    Field fld = fields.AddFieldAsXml(FieldAsXML, true, AddFieldOptions.DefaultValue);
    cc.ExecuteQuery();
}
```

在以下代码中， **AddSiteColumnToContentType** 通过以下方式在内容类型和字段之间创建关联：


1. 使用 [ContentType.FieldLinks](https://msdn.microsoft.com/library/office/microsoft.sharepoint.client.contenttype.fieldlinks.aspx) 属性加载内容类型，然后加载该内容类型中的字段引用。
    
2. 加载字段。
    
3. 使用  **contentType.FieldLinks.Any(f => f.Name == fieldName)** 匹配字段名称，以确定内容类型是否引用字段。
    
4.  如果内容类型已经引用字段，不需要执行任何进一步的操作，当调用 **return** 时，控制权将传回 **Main** 。如果内容类型不引用字段，则说明字段引用属性是在 [FieldLinkCreationInformation](https://msdn.microsoft.com/library/office/microsoft.sharepoint.client.fieldlinkcreationinformation.aspx) 对象上设置的。
    
5. 将  **FieldLinkCreationInformation** 对象添加到 **ContentType.FieldLinks** 属性。
    



```C#
private static void AddSiteColumnToContentType(ClientContext cc, Web web)
{
    // The name of the content type. 
    const string contentTypeName = "ContosoDocumentByCSOM";
    // The field name
    const string fieldName = "ContosoStringCSOM";

    // Load the content type.
    var contentType = GetContentTypeByName(cc, web, contentTypeName);
    if (contentType == null) return; // content type was not found

    // Load field references in the content type.
    cc.Load(contentType.FieldLinks);
    cc.ExecuteQuery();

    // Load the new field.
    Field fld = web.Fields.GetByInternalNameOrTitle(fieldName);
    cc.Load(fld);
    cc.ExecuteQuery();

    // Determine whether the content type refers to the field.
    var hasFieldConnected = contentType.FieldLinks.Any(f => f.Name == fieldName);

    // A reference exists already, no further action is required.
    if (hasFieldConnected) return;

    // The reference does not exist, so we have to create the reference.
    FieldLinkCreationInformation link = new FieldLinkCreationInformation();
    link.Field = fld;
    contentType.FieldLinks.Add(link);
    contentType.Update(true);
    cc.ExecuteQuery();
}
```

在以下代码中， **ReplaceContentType** 通过以下方式检查所有库中的所有项，查找引用旧内容类型的内容，然后将这些引用替换为新内容类型 ( **ContosoDocumentByCSOM** )：


1. 将旧的内容类型 ID 分配给一个常量。
    
2. 使用  **GetContentTypeByName** 获取新的内容类型。
    
3. 使用 [Web.Lists](https://msdn.microsoft.com/zh-cn/library/office/microsoft.sharepoint.client.web.lists.aspx) 获取网站上的所有列表。
    
4. 使用  **cc.Load(lists, l => l.Include(list => list.ContentTypes)** 加载网站上的所有列表以及每个列表的所有内容类型。
    
5. 对于每个返回的列表，使用  **list.ContentTypes.Any(c => c.StringId.StartsWith(oldContentTypeId))** 搜索列表中的内容类型，将内容类型与旧的内容类型 ID 相匹配。如果找到匹配，包含旧内容类型的列表将添加到 **listsWithContentType** 。
    
6. 对于  **listsWithContentType** 中的每个列表：
    
      1. 确定新内容类型是否已附加到列表中。如果新内容类型未附加到列表中，使用 [ContentTypeCollection.AddExistingContentType ](https://msdn.microsoft.com/library/office/microsoft.sharepoint.client.contenttypecollection.addexistingcontenttype.aspx) 将新内容类型附加到列表中。
    
  2. 获取列表中的所有列表项。
    
  3. 对于每个列表项，获取列表项的内容类型 ID。确定列表项的内容类型 ID 是否与旧内容类型 ID 相同。如果不同，跳至下一个列表项。如果相同，使用 [ContentType.StringId](https://msdn.microsoft.com/library/office/microsoft.sharepoint.client.contenttype.stringid.aspx) 将新的内容类型 ID 分配至该列表项。
    

 **注释**  旧的内容类型仍在列表中，但不再使用。现在您可以从列表中删除旧的内容类型，然后将其撤消。本文介绍如何仅替换文档内容类型。如果替换页面布局上的内容类型，确保在网站集中的每个页面布局上更新 [AssociatedContentType](https://msdn.microsoft.com/library/office/microsoft.sharepoint.publishing.pagelayout.associatedcontenttype.aspx) 属性。




```C#
private static void ReplaceContentType(ClientContext cc, Web web)
{
    // The old content type. 
    const string oldContentTypeId = "0x010100C32DDAB6381C44868DCD5ADC4A5307D6";
    // The new content type name
    const string newContentTypeName = "ContosoDocumentByCSOM";

    // Get the new content type and lists on the site.
    ContentType newContentType = GetContentTypeByName(cc, web, newContentTypeName);
    ListCollection lists = web.Lists;
    
    // Load the new content type and the content types on all lists on the site. 
    cc.Load(newContentType);
    cc.Load(lists,
            l => l.Include(list => list.ContentTypes));
    cc.ExecuteQuery();
    var listsWithContentType = new List<List>();
    foreach (List list in lists)
    {
        bool hasOldContentType = list.ContentTypes.Any(c => c.StringId.StartsWith(oldContentTypeId));
        if (hasOldContentType)
        {
            listsWithContentType.Add(list);
        }
    }
    foreach (List list in listsWithContentType)
    {
        // Determine whether the new content type is already attached to the list.
        var listHasContentTypeAttached = list.ContentTypes.Any(c => c.Name == newContentTypeName);
        if (!listHasContentTypeAttached)
        {
            // Attach content type to list.
            list.ContentTypes.AddExistingContentType(newContentType);
            cc.ExecuteQuery();
        }
        // Get all list items.
        CamlQuery query = CamlQuery.CreateAllItemsQuery();
        ListItemCollection items = list.GetItems(query);
        cc.Load(items);
        cc.ExecuteQuery();

        // For each list item, determine whether the old content type is used, and then update to the new content type. 
        foreach (ListItem listItem in items)
        {
            // Get the current content type for this list item.
            var currentContentTypeId = listItem["ContentTypeId"] + "";
            var isOldContentTypeAssigned = currentContentTypeId.StartsWith(oldContentTypeId);

            // This item does not use the old content type - skip to next list item.
            if (!isOldContentTypeAssigned) continue;

            // Update the list item content type to the new content type.
            listItem["ContentTypeId"] = newContentType.StringId; // new content type Id;
            listItem.Update();
        }
        // Save all changes.
        cc.ExecuteQuery();
    }
}
```


## 其他资源



- [将服务器场解决方案转换为 SharePoint 外接程序模型](https://msdn.microsoft.com/library/dn986827.aspx)
    
- [SharePoint 2013](https://msdn.microsoft.com/library/office/jj162979.aspx)
    
