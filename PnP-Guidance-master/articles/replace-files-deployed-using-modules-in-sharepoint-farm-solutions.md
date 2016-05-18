
# 替换使用 SharePoint 服务器场解决方案中的模块部署的文件
通过上载和更新对使用新文件的引用，替换之前使用服务器场解决方案中的模块部署的文件，如 SharePoint 中的母版页和页面布局。

 **上次修改时间：** 2015年8月7日

 _ **适用范围：** SharePoint 2013?| SharePoint Add-ins?| SharePoint Online_

 **注释**  名称"SharePoint 相关应用程序"将更改为"SharePoint 外接程序"。在转换期间，某些 SharePoint 产品和 Visual Studio 工具的文档和 UI 仍可能使用术语"SharePoint 相关应用程序"。有关详细信息，请参阅 [Office 和 SharePoint 相关应用程序的新名称](05b07b04-6c8b-4b7e-bd86-e32c589dfead.md#bk_newname)。

如果您在服务器场解决方案中使用模块以声明性方式部署文件，则应了解如何将其转换为新解决方案，以更新对文件的引用并使用客户端对象模型(CSOM) 提供类似功能。过去，模块用于部署母版页和页面布局等文件。本文介绍如何使用转换过程执行以下操作：

1. 上载新的母版页和页面布局。
    
2. 更新引用以使用新的母版页和页面布局文件。
    
在以下情况下，使用此转换过程：

- 您的现有服务器场解决方案之前使用模块部署文件。
    
-  您想将服务器场解决方案中的母版页和页面布局替换为新的母版页和页面布局，以便可以迁移到 SharePoint Online。
    
- 您已以声明性方式在服务器场的母版页或页面布局上设置文档内容类型。
    

 **重要信息**  服务器场解决方案无法迁移到 SharePoint Online。通过应用本文中所述的技术和代码，您可以构建一个新的解决方案（该解决方案具有与您的服务器场解决方案提供的类似功能），更新对文件的引用，稍后可将其部署到 SharePoint Online。然后您可以禁用此功能并撤销之前的服务器场解决方案。本文中的代码需要其他代码才能提供可完整运行的解决方案。例如，本文不讨论如何向 Office 365 进行身份验证，如何实施必需的异常处理，等等。有关其他代码示例，请参阅 [Office 365 开发人员模式和做法项目](https://github.com/OfficeDev/PnP)。

 **为改进此内容做贡献**
您可以获取最新的更新，或为改进 [GitHub 上的此文章](https://github.com/OfficeDev/PnP-Guidance)做贡献。您还可以为改进本示例以及 [GitHub 上的其他示例](https://github.com/OfficeDev/PnP)做贡献。有关示例的完整列表，请参阅 [模式和做法开发人员中心](http://dev.office.com/patterns-and-practices)。我们欢迎您做出 [贡献](https://github.com/OfficeDev/PnP/wiki/contributing-to-Office-365-developer-patterns-and-practices)。 

## 开始之前



理想情况下，您应审核现有的服务器场解决方案，了解本文中所述的技术，然后计划如何将这些技术应用到您的方案中。如果您不熟悉服务器场解决方案或者没有可使用的现有服务器场解决方案，执行以下操作可能会很有帮助：


- 下载 [Contoso.Intranet solution](https://github.com/OfficeDev/PnP/tree/master/Reference%20Material/Contoso.Intranet)。阅读 [替换在完全信任解决方案中通过模块设置的文件](https://github.com/OfficeDev/TrainingContent/blob/master/O3658/10%20Transformation%20guidance%20from%20farm%20solutions%20to%20app%20model/10-1%20Replacement%20of%20files%20deployed%20via%20Modules/Lab.md)中的练习 1，快速了解在过去是如何构建母版页和页面布局的。
    
- 了解 服务器场解决方案。有关详细信息，请参阅 [SharePoint 2010 体系结构概述](https://msdn.microsoft.com/zh-cn/library/office/gg552610%28v=office.14%29.aspx)和 [在 SharePoint 2013 中构建服务器场解决方案](https://msdn.microsoft.com/library/jj163902.aspx)。
    
在运行您的代码示例之前，通过以下过程启用网站集和网站上的发布功能：


1. 要启用网站集上的"SharePoint Server 发布基础架构"功能，请执行以下操作：
    
      1. 打开您的 SharePoint 网站并转到"设置">"网站设置"。
    
  2. 在"网站集管理"中，选择"网站集功能"。
    
  3. 在"SharePoint Server 发布基础架构"上，选择"激活"。
    
2. 要启用网站上的"SharePoint Server 发布"功能，请执行以下操作：
    
      1. 打开您的 SharePoint 网站并转到"设置">"网站设置"。
    
  2. 在"网站操作"中，选择"管理网站功能"。
    
  3. 在"SharePoint Server 发布"上，选择"激活"。
    
要设置 Visual Studio 项目，请执行以下操作：


1. 下载 [示例项目](https://github.com/OfficeDev/TrainingContent/blob/master/O3658/10%20Transformation%20guidance%20from%20farm%20solutions%20to%20app%20model/Student.zip)。选择"查看原始项目"开始下载示例项目，从 zip 文件中提取文件，然后浏览到 Module9/ModuleReplacement 文件夹。
    
2. 打开 ModuleReplacement.sln。
    
3. 打开 settings.xml。查看并更新以下属性，以满足您的要求：
    
      1.  **masterpage** 元素 - 使用 **file** 属性指定要部署到母版页库的新母版页， **replaces** 属性指定母版页库中的现有母版页。
    
  2.  **pagelayout** 元素 - 使用 **file** 属性指定新的母版页文件，使用 **replaces** 属性指定现有的母版页文件，并使用多个其他属性指定其他页面布局信息。
    

     **注释**  本文中的代码按原样提供，不提供任何明示或暗示的担保，包括对特定用途适用性、适销性或不侵权的默示担保。

  ```XML
  	<?xml version="1.0" encoding="utf-8" ?>
		<branding>
		  <masterPages>
		       <masterPage file="contoso_app.master" replaces="contoso.master"/>
		  </masterPages>
		  <pageLayouts>
		       <pageLayout file="ContosoWelcomeLinksApp.aspx" replaces="ContosoWelcomeLinks.aspx" title="Contoso Welcome Links add-in" associatedContentTypeName="Contoso Web Page" defaultLayout="true" />
		  </pageLayouts>
		</branding>

  ```

4. 在 MasterPageGalleryFiles.cs 中， **MasterPageGalleryFile** 和 **LayoutFile** 类定义了业务对象，其中存储了关于要更新到 SharePoint 的新母版页和页面布局的信息。
    

## 上载母版页并更新对母版页的引用

要在 SharePoint 网站上载新母版页并更新对新母版页的引用，请执行以下操作：


1. 在 Program.cs 中，添加以下  **using** 语句。
    
  ```C#
  	using System.Security;

  ```

2. 在 Program.cs 中，添加以下方法以执行到 Office 365 的身份验证。
    
  ```C#
  static SecureString GetPassword()
        {
            SecureString sStrPwd = new SecureString();
            try
            {
                Console.Write("Password: ");

                for (ConsoleKeyInfo keyInfo = Console.ReadKey(true); keyInfo.Key != ConsoleKey.Enter; keyInfo = Console.ReadKey(true))
                {
                    if (keyInfo.Key == ConsoleKey.Backspace)
                    {
                        if (sStrPwd.Length > 0)
                        {
                            sStrPwd.RemoveAt(sStrPwd.Length - 1);
                            Console.SetCursorPosition(Console.CursorLeft - 1, Console.CursorTop);
                            Console.Write(" ");
                            Console.SetCursorPosition(Console.CursorLeft - 1, Console.CursorTop);
                        }
                    }
                    else if (keyInfo.Key != ConsoleKey.Enter)
                    {
                        Console.Write("*");
                        sStrPwd.AppendChar(keyInfo.KeyChar);
                    }

                }
                Console.WriteLine("");
            }
            catch (Exception e)
            {
                sStrPwd = null;
                Console.WriteLine(e.Message);
            }

            return sStrPwd;
        }

        static string GetUserName()
        {
            string strUserName = string.Empty;
            try
            {
                Console.Write("Username: ");
                strUserName = Console.ReadLine();
            }
            catch (Exception e)
            {
                Console.WriteLine(e.Message);
                strUserName = string.Empty;
            }
            return strUserName;
        }

  ```

3. 在 Program.cs 中，将以下代码添加到  **Main** 方法，此代码将执行以下任务：
    
      1. 加载 settings.xml 文件。
    
  2. 使用 [GetCatalog](https://msdn.microsoft.com/library/office/microsoft.sharepoint.client.web.getcatalog.aspx)(116) 获取对母版页库的引用。母版页将上载到母版页库。母版页库的列表模板的标识符为 116。有关详细信息，请参阅 [ListTemplate 元素 (Site)](https://msdn.microsoft.com/library/ms439434.aspx)。已加载关于母版页库的其他详细信息，包括使用 [List.ContentTypes](https://msdn.microsoft.com/library/microsoft.sharepoint.client.list.contenttypes.aspx) 获取与母版页相关的内容类型。
    
  3. 加载 Web 对象的属性，包括 Web.ContentTypes、Web.MasterUrl 和 Web.CustomMasterUrl。
    
  4. 将  **parentMasterPageContentTypeId** 设置为母版页的内容类型 ID。有关详细信息，请参阅 [基本内容类型层次结构](https://msdn.microsoft.com/library/ms452896%28v=office.14%29.aspx)。
    
  5. 从母版页库中获取母版页的内容类型。此内容类型用于稍后在本文中设置新母版页的内容类型。 
    
  ```C#
  static void Main(string[] args)
        { 
	        string userName = GetUserName();
	        SecureString pwd = GetPassword();
			
         // End program if no credentials were entered.
	        if (string.IsNullOrEmpty(userName) || (pwd == null))
	          return;
						           
	        using (var clientContext = new ClientContext("http://sharepoint.contoso.com"))
            {
							
	            clientContext.AuthenticationMode = ClientAuthenticationMode.Default;
	            clientContext.Credentials = new SharePointOnlineCredentials(userName, pwd);
					
                XDocument settings = XDocument.Load("settings.xml");

                // Get a reference to the master page gallery which will be used to upload new master pages. 
			 
                Web web = clientContext.Web;
                List gallery = web.GetCatalog(116);
                Folder folder = gallery.RootFolder;
			
	             // Load additional master page gallery properties.
                clientContext.Load(folder);
                clientContext.Load(gallery,
                                    g => g.ContentTypes,
                                    g => g.RootFolder.ServerRelativeUrl);
        
		        // Load the content types and master page information from the web.
                clientContext.Load(web,
                                    w => w.ContentTypes,
                                    w => w.MasterUrl,
                                    w => w.CustomMasterUrl);
                clientContext.ExecuteQuery();

                // Get the content type for the master page from the master page gallery using the content type ID for masterpages (0x010105).
                const string parentMasterPageContentTypeId = "0x010105";  
                ContentType masterPageContentType = gallery.ContentTypes.FirstOrDefault(ct => ct.StringId.StartsWith(parentMasterPageContentTypeId));
                UploadAndSetMasterPages(web, folder, clientContext, settings, masterPageContentType.StringId);
    					     
	        }
	
	    }
  ```

4. 在 Program.cs 中，添加  **UploadAndSetMasterPages** 方法，此方法将通过以下方式创建 **MasterPageGalleryFile** 业务对象的列表：
    
      1. 读取 settings.xml 中定义的所有  **masterPage** 元素。
    
  2. 对于每个  **masterPage** 元素（指定要上载到 SharePoint 的母版页），将属性值加载到 **MasterPageGalleryFile** 业务对象。
    
  3. 对于列表中的每个  **MasterPageGalleryFile** 业务对象，调用 **UploadAndSetMasterPage** 。
    
  ```C#
  	
private static void UploadAndSetMasterPages(Web web, Folder folder, ClientContext clientContext, XDocument settings, string contentTypeId)
{
    IList<MasterPageGalleryFile> masterPages = (from m in settings.Descendants("masterPage")
                                                select new MasterPageGalleryFile
                                                {
                                                    File = (string)m.Attribute("file"),
                                                    Replaces = (string)m.Attribute("replaces"),
                                                    ContentTypeId = contentTypeId
                                                }).ToList();
    foreach (MasterPageGalleryFile masterPage in masterPages)
    {
        UploadAndSetMasterPage(web, folder, clientContext, masterPage);
    }
}

  ```

5. 在 Program.cs 中，添加  **UploadAndSetMasterPage** 方法，此方法将执行以下任务：
    
      1. 签出母版页。
    
  2. 使用 [FileCreationInformation](https://msdn.microsoft.com/library/microsoft.sharepoint.client.filecreationinformation.aspx) 上载新文件。
    
  3. 获取与新上载的文件关联的列表项。
    
  4. 设置列表项上的各种属性，包括将列表项的内容类型 ID 设置为母版页的内容类型 ID。
    
  5. 签入、发布和批准新的母版页。 
    
  6. 如果当前网站的母版页或自定义母版页 URL 设置为旧的母版页，请将 [Web.MasterUrl](https://msdn.microsoft.com/library/microsoft.sharepoint.client.web.masterurl.aspx) 或 [Web.CustomMasterUrl](https://msdn.microsoft.com/library/microsoft.sharepoint.client.web.custommasterurl.aspx) 更新为使用新上载的母版页 URL。
    
  ```C#
  private static void UploadAndSetMasterPage(Web web, Folder folder, ClientContext clientContext, MasterPageGalleryFile masterPage)
{
    using (var fileReadingStream = System.IO.File.OpenRead(masterPage.File))
    {
        // If necessary, ensure that the master page is checked out.
        PublishingHelper.CheckOutFile(web, masterPage.File, folder.ServerRelativeUrl);

// Use the FileCreationInformation class to upload the new file.
        var fileInfo = new FileCreationInformation();
        fileInfo.ContentStream = fileReadingStream;
        fileInfo.Overwrite = true;
        fileInfo.Url = masterPage.File;
        File file = folder.Files.Add(fileInfo);

// Get the list item associated with the newly uploaded master page file.
        ListItem item = file.ListItemAllFields;
        clientContext.Load(file.ListItemAllFields);
        clientContext.Load(file,
                            f => f.CheckOutType,
                            f => f.Level,
                            f => f.ServerRelativeUrl);
        clientContext.ExecuteQuery();

item["ContentTypeId"] = masterPage.ContentTypeId;
        item["UIVersion"] = Convert.ToString(15);
        item["MasterPageDescription"] = "Master Page Uploaded using CSOM";
        item.Update();
        clientContext.ExecuteQuery();

// If necessary, check in, publish, and approve the new master page file. 
        PublishingHelper.CheckInPublishAndApproveFile(file);

// On the current site, update the master page references to use the new master page. 
        if (web.MasterUrl.EndsWith("/" + masterPage.Replaces))
        {
            web.MasterUrl = file.ServerRelativeUrl;
        }
        if (web.CustomMasterUrl.EndsWith("/" + masterPage.Replaces))
        {
            web.CustomMasterUrl = file.ServerRelativeUrl;
        }
        web.Update();
        clientContext.ExecuteQuery();
    }
}

  ```


## 上载页面布局并更新对页面布局的引用


 **注释**  本节中的代码示例在本文前一节的代码示例基础上构建。 

要替换之前使用服务器场解决方案中的模块部署的页面布局，请通过以下方式上载并更新引用以使用新的页面布局：


1. 使用以下代码更新  **Main** 方法。此代码包含上载页面布局文件并更新对页面布局文件的引用的其他步骤，包括：
    
      1. 将  **parentPageLayoutContentTypeId** 设置为页面布局的内容类型 ID。
    
  2.  从母版页库中获取与 **parentPageLayoutContentTypeId** 匹配的内容类型。
    
  3. 调用  **UploadPageLayoutsAndUpdateReferences** 。
    
  ```C#
  			static void Main(string[] args)
        { 
	        string userName = GetUserName();
	        SecureString pwd = GetPassword();
			
         // End program if no credentials were entered.
	        if (string.IsNullOrEmpty(userName) || (pwd == null))
	          return;
						           
	        using (var clientContext = new ClientContext("http://sharepoint.contoso.com"))
            {
							
	            clientContext.AuthenticationMode = ClientAuthenticationMode.Default;
	            clientContext.Credentials = new SharePointOnlineCredentials(userName, pwd);
					
                XDocument settings = XDocument.Load("settings.xml");

                // Get a reference to the master page gallery, which will be used to upload new master pages. 
			 
                Web web = clientContext.Web;
                List gallery = web.GetCatalog(116);
                Folder folder = gallery.RootFolder;
			
	             // Load additional master page gallery properties. 
                clientContext.Load(folder);
                clientContext.Load(gallery,
                                    g => g.ContentTypes,
                                    g => g.RootFolder.ServerRelativeUrl);
        
		        // Load the content types and master page information from the web.
                clientContext.Load(web,
                                    w => w.ContentTypes,
                                    w => w.MasterUrl,
                                    w => w.CustomMasterUrl);
                clientContext.ExecuteQuery();

                // Get the content type for the master page from the master page gallery using the content type ID for masterpages (0x010105).
                const string parentMasterPageContentTypeId = "0x010105";  
                ContentType masterPageContentType = gallery.ContentTypes.FirstOrDefault(ct => ct.StringId.StartsWith(parentMasterPageContentTypeId));
                UploadAndSetMasterPages(web, folder, clientContext, settings, masterPageContentType.StringId);
    					     

               // Get the content type ID for the page layout, and then update references to the page layouts.
               const string parentPageLayoutContentTypeId = "0x01010007FF3E057FA8AB4AA42FCB67B453FFC100E214EEE741181F4E9F7ACC43278EE811"; 
               ContentType pageLayoutContentType = gallery.ContentTypes.FirstOrDefault(ct => ct.StringId.StartsWith(parentPageLayoutContentTypeId));
               UploadPageLayoutsAndUpdateReferences(web, folder, clientContext, settings, pageLayoutContentType.StringId);
	        }
	
	    }
			
			

  ```

2. 在 Program.cs 中，添加  **UploadPageLayoutsAndUpdateReferences** 方法，此方法将执行以下步骤：
    
      1. 读取页面布局替换信息，方法是从 settings.xml 读取  **pagelayout** 元素、将页面布局替换信息存储在 **LayoutFile** 业务对象中，然后将所有业务对象添加到列表中。
    
  2. 对于要替换的每个页面布局：
    
      1. 使用 [Web.ContentTypes](https://msdn.microsoft.com/library/microsoft.sharepoint.client.web.contenttypes.aspx) 查询网站的内容类型，以查找匹配的内容类型，即网站的内容类型名称等于新页面布局的 settings.xml 中指定的 **associatedContentTypeName** 。
    
  2. 调用  **UploadPageLayout** 。
    
  3. 调用  **UpdatePages** ，将现有页面更新为使用新页面布局。
    

  ```C#
  		private static void UploadPageLayoutsAndUpdateReferences(Web web, Folder folder, ClientContext clientContext, XDocument settings, string contentTypeId)
{
    // Read the replacement settings stored in pageLayout elements in settings.xml.
    IList<LayoutFile> pageLayouts = (from m in settings.Descendants("pageLayout")
                                 select new LayoutFile
                                 {
                                     File = (string)m.Attribute("file"),
                                     Replaces = (string)m.Attribute("replaces"),
                                     Title = (string)m.Attribute("title"),
                                     ContentTypeId = contentTypeId,
                                     AssociatedContentTypeName = (string)m.Attribute("associatedContentTypeName"),
                                     DefaultLayout = m.Attribute("defaultLayout") != null &amp;&amp; (bool)m.Attribute("defaultLayout")
                                 }).ToList();

// Update the content type association.
		    foreach (LayoutFile pageLayout in pageLayouts)
    {
        ContentType associatedContentType =
            web.ContentTypes.FirstOrDefault(ct => ct.Name == pageLayout.AssociatedContentTypeName);
        pageLayout.AssociatedContentTypeId = associatedContentType.StringId;                
        UploadPageLayout(web, folder, clientContext, pageLayout);
    }
		    
		    UpdatePages(web, clientContext, pageLayouts);
		}

  ```

3.  在 Program.cs 中，添加 **UploadPageLayout** 方法，此方法将执行以下任务：
    
      1. 签出页面布局文件。
    
  2. 使用 [FileCreationInformation](https://msdn.microsoft.com/library/microsoft.sharepoint.client.filecreationinformation.aspx) 上载新文件。
    
  3. 获取与新上载的文件关联的列表项。
    
  4. 设置列表项上的各种属性，包括  **ContentTypeId** 和 **PublishingAssociatedContentType** 。
    
  5. 签入、发布和批准新的页面布局文件。
    
  6.  要删除对旧页面布局文件的引用，请调用 **PublishingHelper.UpdateAvailablePageLayouts** ，以更新存储在当前网站的 **PageLayouts** 属性中的 XML。
    
  7. 如果 settings.xml 中新上载的页面布局文件的  **defaultLayout** 属性设置为 **true** ，请使用 **PublishingHelper.SetDefaultPageLayout** 更新存储在当前网站的 **DefaultPageLayout** 属性中的 XML。
    

  ```C#
  private static void UploadPageLayout(Web web, Folder folder, ClientContext clientContext, LayoutFile pageLayout)
{
    using (var fileReadingStream = System.IO.File.OpenRead(pageLayout.File))
    {
        PublishingHelper.CheckOutFile(web, pageLayout.File, folder.ServerRelativeUrl);
        // Use FileCreationInformation to upload the new page layout file.
        var fileInfo = new FileCreationInformation();
        fileInfo.ContentStream = fileReadingStream;
        fileInfo.Overwrite = true;
        fileInfo.Url = pageLayout.File;
        File file = folder.Files.Add(fileInfo);
        // Get the list item associated with the newly uploaded file.
        ListItem item = file.ListItemAllFields;
        clientContext.Load(file.ListItemAllFields);
        clientContext.Load(file,
                            f => f.CheckOutType,
                            f => f.Level,
                            f => f.ServerRelativeUrl);
        clientContext.ExecuteQuery();

item["ContentTypeId"] = pageLayout.ContentTypeId;
        item["Title"] = pageLayout.Title;
        item["PublishingAssociatedContentType"] = string.Format(";#{0};#{1};#", pageLayout.AssociatedContentTypeName, pageLayout.AssociatedContentTypeId);
        item.Update();
        clientContext.ExecuteQuery();

PublishingHelper.CheckInPublishAndApproveFile(file);

PublishingHelper.UpdateAvailablePageLayouts(web, clientContext, pageLayout, file);

if (pageLayout.DefaultLayout)
        {
            PublishingHelper.SetDefaultPageLayout(web, clientContext, file);
        }
    }
}

  ```

4. 在 Program.cs 中，添加  **UpdatePages** 方法，此方法将执行以下任务：
    
      1. 获取  **Pages** 库，然后获取 **Pages** 库中的所有列表项。
    
  2. 对于每个列表项，使用列表项的  **PublishingPageLayout** 字段查找要更新的匹配页面布局，此页面布局之前在 settings.xml 中指定，现在存储在 **pagelayouts** 中。如果需要更新列表项的 **PublishingPageLayout** 字段以引用新的页面布局，请执行以下操作：
    
      1. 使用  **PublishingHelper.CheckOutFile** 签出列表项的文件。
    
  2. 将页面布局的 URL 更新为引用新的页面布局文件，然后更新列表项的  **PublishingPageLayout** 字段。
    
  3. 签入、发布和批准列表项引用的文件。
    

  ```C#
  private static void UpdatePages(Web web, ClientContext clientContext, IList<LayoutFile> pageLayouts)
{
    // Get the Pages Library, and then get all the list items in it.
    List pagesList = web.Lists.GetByTitle("Pages");
    var allItemsQuery = CamlQuery.CreateAllItemsQuery();
    ListItemCollection items = pagesList.GetItems(allItemsQuery);
    clientContext.Load(items);
    clientContext.ExecuteQuery();
    foreach (ListItem item in items)
    {
        // Only update those pages that are using a page layout which is being replaced.
        var pageLayout = item["PublishingPageLayout"] as FieldUrlValue;
        if (pageLayout != null)
        {
            LayoutFile matchingLayout = pageLayouts.FirstOrDefault(p => pageLayout.Url.EndsWith("/" + p.Replaces));
            if (matchingLayout != null)
            {
                // Check out the page so that we can update the page layout. 
                PublishingHelper.CheckOutFile(web, item);

// Update the pageLayout reference.
                pageLayout.Url = pageLayout.Url.Replace(matchingLayout.Replaces, matchingLayout.File);
                item["PublishingPageLayout"] = pageLayout;
                item.Update();
                File file = item.File;

// Get the file and other attributes so that you can check in the file.
                clientContext.Load(file,
                    f => f.Level,
                    f => f.CheckOutType);
                clientContext.ExecuteQuery();


                PublishingHelper.CheckInPublishAndApproveFile(file);
            }
        }
    }
}

  ```


## 其他资源



- [将服务器场解决方案转换为 SharePoint 外接程序模型](https://msdn.microsoft.com/library/dn986827.aspx)
    
- [SharePoint 2013](https://msdn.microsoft.com/library/office/jj162979.aspx)
    
