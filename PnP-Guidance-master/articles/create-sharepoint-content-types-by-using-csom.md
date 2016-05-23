
# 使用 CSOM 创建 SharePoint 内容类型
使用 CSOM 创建 SharePoint 内容类型，使用 SharePoint 2013 SP1 中引入的功能进行本地化更改。

 **上次修改时间：** 2015年6月24日

 _ **适用范围：** Office 365?| SharePoint 2013?| SharePoint Add-ins?| SharePoint Online_

 **注释**  名称"SharePoint 相关应用程序"将更改为"SharePoint 外接程序"。在转换期间，某些 SharePoint 产品和 Visual Studio 工具的文档和 UI 仍可能使用术语"SharePoint 相关应用程序"。有关详细信息，请参阅 [Office 和 SharePoint 相关应用程序的新名称](05b07b04-6c8b-4b7e-bd86-e32c589dfead.md#bk_newname)。

您可以使用 [Core.SPD](https://github.com/OfficeDev/PnP/tree/dev/Samples/Core.SPD) 示例以编程方式创建网站栏和内容类型，并将它们链接在一起。您还可以使用 [SharePoint Server 2013 客户端组件 SDK](http://www.microsoft.com/en-us/download/details.aspx?id=35585) 中提供的 SharePoint 2013 SP1 CSOM API 来添加特定的内容类型标识符，本地化内容类型、列表和网站标题。
 **为改进此内容做贡献**
您可以获取最新的更新，或为改进 [GitHub 上的此文章](https://github.com/OfficeDev/PnP-Guidance)做贡献。您还可以为改进本示例以及 [GitHub 上的其他示例](https://github.com/OfficeDev/PnP)做贡献。有关示例的完整列表，请参阅 [模式和做法开发人员中心](http://dev.office.com/patterns-and-practices)。我们欢迎您做出 [贡献](https://github.com/OfficeDev/PnP/wiki/contributing-to-Office-365-developer-patterns-and-practices)。 

## 开始之前

若要开始，请从 GitHub 上的 [Office 365 开发人员模式和做法](https://github.com/OfficeDev/PnP/tree/dev)项目下载 [Core.SPD](https://github.com/OfficeDev/PnP/tree/dev/Samples/Core.SPD) 示例。


## 创建内容类型和网站栏

以下代码示例演示如何使用  **ContentTypeCreationInformation** 类（包括设置 ID）来创建内容类型。


 **注释**  本文中的代码按原样提供，不提供任何明示或暗示的担保，包括对特定用途适用性、适销性或不侵权的默示担保。


```C#
ContentTypeCollection contentTypes = web.ContentTypes;
cc.Load(contentTypes);
cc.ExecuteQuery();

foreach (var item in contentTypes)
{
  if (item.StringId == "0x0101009189AB5D3D2647B580F011DA2F356FB2")
    return;
}

// Create a Content Type Information object.
ContentTypeCreationInformation newCt = new ContentTypeCreationInformation();
// Set the name for the content type.
newCt.Name = "Contoso Document";
// Inherit from oob document - 0x0101 and assign. 
newCt.Id = "0x0101009189AB5D3D2647B580F011DA2F356FB2";
// Set content type to be available from specific group.
newCt.Group = "Contoso Content Types";
// Create the content type.
ContentType myContentType = contentTypes.Add(newCt);
cc.ExecuteQuery();

Using AddFieldAsXml you can add fields to the FieldCollection of a site collection:
FieldCollection fields = web.Fields;
cc.Load(fields);
cc.ExecuteQuery();

string FieldAsXML = @"<Field ID='{4F34B2ED-9CFF-4900-B091-4C0033F89944}' Name='ContosoString' DisplayName='Contoso String' Type='Text' Hidden='False' Group='Contoso Site Columns' Description='Contoso Text Field' />";
Field fld = fields.AddFieldAsXml(FieldAsXML, true, AddFieldOptions.DefaultValue);
cc.Load(fields);
cc.Load(fld);
cc.ExecuteQuery();

```

使用  **FieldLinkCollection** 和 **FieldLinkCreationInformation** 类将字段链接到内容类型。




```C#
FieldCollection fields = web.Fields;
Field fld = fields.GetByInternalNameOrTitle("ContosoString");
cc.Load(fields);
cc.Load(fld);
cc.ExecuteQuery();

FieldLinkCollection refFields = myContentType.FieldLinks;
cc.Load(refFields);
cc.ExecuteQuery();

foreach (var item in refFields)
{
  if (item.Name == "ContosoString")
    return;
  }

FieldLinkCreationInformation link = new FieldLinkCreationInformation();
link.Field = fld;
myContentType.FieldLinks.Add(link);
myContentType.Update(true);
cc.ExecuteQuery();

```


## 本地化内容类型、列表和网站标题

使用以下代码本地化网站标题和网站说明。


```C#
web.TitleResource.SetValueForUICulture("fi-FI", "Kielik??nn? minut");
web.DescriptionResource.SetValueForUICulture("fi-FI", "Kielik??nnetty saitti");

```

对于列表，可使用网站中相同的方法。




```C#
list.TitleResource.SetValueForUICulture("fi-FI", "Kielik??nn? minut");
list.DescriptionResource.SetValueForUICulture("fi-FI", "T?m? esimerkki n?ytt?? miten voit kielik??nt?? listoja.");

```

对于内容类型，您可以选择本地化名称和说明。对于字段，可以本地化标题和说明值。




```C#
myContentType.NameResource.SetValueForUICulture("fi-FI", "Contoso Dokumentti");
myContentType.DescriptionResource.SetValueForUICulture("fi-FI", "T?m? on geneerinen Contoso dokumentti.");

fld.TitleResource.SetValueForUICulture("fi-FI", "Contoso Teksti");
fld.DescriptionResource.SetValueForUICulture("fi-FI", "T?? on niiku Contoso metadatalle.");

```


## 创建文档内容类型和网站栏

以下示例演示如何创建文档内容类型，然后将文档模板与内容类型相关联。 

本示例将名为"Contoso 文档"的新内容类型添加到网站集。创建新文档后，此内容类型具有与其相关联的自定义模板。




```C#
ContentType ct = web.ContentTypes.GetById("0x0101009189AB5D3D2647B580F011DA2F356FB2");
            cc.Load(ct); cc.ExecuteQuery();
            string ctFolderServerRelativeURL = "_cts/" + ct.Name;
            Folder ctFolder = web.GetFolderByServerRelativeUrl(ctFolderServerRelativeURL);
            cc.Load(ctFolder);
            cc.ExecuteQuery();

            string path = @"C:\Data\Test Documents\Doc CT File.docx";
            string fileName = System.IO.Path.GetFileName(path);
            byte[] filecontent = System.IO.File.ReadAllBytes(path);

            using (System.IO.FileStream fs = new System.IO.FileStream(path, System.IO.FileMode.Open))
            {
                FileCreationInformation newFile = new FileCreationInformation();
                newFile.Content = filecontent;
                newFile.Url = ctFolderServerRelativeURL + "/" + fileName;

                Microsoft.SharePoint.Client.File uploadedFile = ctFolder.Files.Add(newFile);
                cc.Load(uploadedFile);
                cc.ExecuteQuery();
                //Microsoft.SharePoint.Client.File.SaveBinaryDirect(clientContext, ctFolderServerRelativeURL + "/" + fileName, fs, true);

                
                cc.Load(ct); cc.ExecuteQuery();
                ct.DocumentTemplate = fileName;
                ct.Update(false);
                cc.Load(ct); cc.ExecuteQuery();
                Console.WriteLine("Content type updates");
            }

```


## 其他资源



- [SharePoint 网站设置解决方案](sharepoint-site-provisioning-solutions.md)
    
- [FTC 到 CAM - 使用 CSO 创建具有特定 ID 的内容类型](http://blogs.msdn.com/b/vesku/archive/2014/02/28/ftc-to-cam-create-content-types-with-specific-ids-using-csom.aspx)
    
- [SharePoint Server 2013 客户端组件 SDK](http://www.microsoft.com/en-us/download/details.aspx?id=35585)
    
