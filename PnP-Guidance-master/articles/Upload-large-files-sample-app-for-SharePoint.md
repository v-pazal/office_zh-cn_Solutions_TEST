
# 上载大文件示例 SharePoint 外接程序
使用 SaveBinaryDirect、ContentStream、StartUpload、ContinueUpload 和 FinishUpload 将大于 2MB 的文件上载到 SharePoint 和 SharePoint Online。

 **上次修改时间：** 2015年8月7日

 _ **适用范围：** SharePoint 2013?| SharePoint Add-ins?| SharePoint Online_

 **注释**  名称"SharePoint 相关应用程序"将更改为"SharePoint 外接程序"。在转换期间，某些 SharePoint 产品和 Visual Studio 工具的文档和 UI 仍可能使用术语"SharePoint 相关应用程序"。有关详细信息，请参阅 [Office 和 SharePoint 相关应用程序的新名称](05b07b04-6c8b-4b7e-bd86-e32c589dfead.md#bk_newname)。

[Core.LargeFileUpload](https://github.com/OfficeDev/PnP/tree/dev/Samples/Core.LargeFileUpload) 示例说明如何使用提供程序托管的外接程序将大文件上载到 SharePoint，以及如何绕过 2 MB 的文件上载限制。如果您想将大于 2 MB 的文件上载到 SharePoint，请使用此选项。本示例作为控制台应用程序运行，可使用以下方法之一将大文件上载到文档库：

-  **Microsoft.SharePoint.Client.File** 类上的 **[SaveBinaryDirect](https://msdn.microsoft.com/library/office/ee538285.aspx)** 方法。
    
-  **FileCreationInformation** 类上的 **[ContentStream](https://msdn.microsoft.com/library/office/microsoft.sharepoint.client.filecreationinformation.contentstream.aspx)** 属性。
    
-  **File** 类上的 **[StartUpload](https://msdn.microsoft.com/library/office/microsoft.sharepoint.client.file.startupload.aspx)** 、 **[ContinueUpload](https://msdn.microsoft.com/library/office/microsoft.sharepoint.client.file.continueupload.aspx)** 和 **[FinishUpload](https://msdn.microsoft.com/library/office/microsoft.sharepoint.client.file.finishupload.aspx)** 方法。
    
下表列出了可用的文件上载方法并介绍何时使用每种方法。

**文件上载选项**

|
|
|**文件上载选项**|**注意事项**|**何时应使用此选项？**|**支持的平台**|
|:-----|:-----|:-----|:-----|
|**FileCreationInformation** 类上的 [Content](https://msdn.microsoft.com/library/office/microsoft.sharepoint.client.filecreationinformation.content.aspx) 属性。|可以上载的最大文件大小为 2 MB。30 分钟后将发生超时。|仅用于上载小于 2 MB 的文件。|SharePoint Server 2013、SharePoint Online|
|**File** 类上的 **SaveBinaryDirect** 方法。|没有文件大小限制。30 分钟后将发生超时。|如果您使用仅用户身份验证策略，请仅使用此方法。仅用户身份验证策略在 SharePoint 相关外接程序中不可用，但可以在本机设备外接程序、Windows PowerShell 和 Windows 控制台应用程序中使用。|SharePoint Server 2013、SharePoint Online|
|**FileCreationInformation** 类上的 **ContentStream** 属性。|没有文件大小限制。30 分钟后将发生超时。|建议用于：
<ul xmlns:xlink="http://www.w3.org/1999/xlink" xmlns:mtps="http://msdn2.microsoft.com/mtps" xmlns:mshelp="http://msdn.microsoft.com/mshelp" xmlns:ddue="http://ddue.schemas.microsoft.com/authoring/2003/5" xmlns:msxsl="urn:schemas-microsoft-com:xslt"><li><p>SharePoint Server 2013。</p></li><li><p>SharePoint Online，当文件小于 10 MB 时。</p></li></ul>|SharePoint Server 2013、SharePoint Online|
|在  **File** 类上使用 **StartUpload** 、 **ContinueUpload** 和 **FinishUpload** 方法，将单个文件作为一系列块上载。|没有文件大小限制。30 分钟后将发生超时。每个文件块都必须在前一个文件块完成后 30 分钟内上载，以免发生超时。|当文件大于 10 MB 时，建议用于 SharePoint Online。|SharePoint Online|
 **为改进此内容做贡献**
您可以获取最新的更新，或为改进 [GitHub 上的此文章](https://github.com/OfficeDev/PnP-Guidance/blob/master/articles/Upload-large-files-sample-app-for-SharePoint.md)做贡献。您还可以为改进本示例以及 [GitHub 上的其他示例](https://github.com/OfficeDev/PnP)做贡献。有关示例的完整列表，请参阅 [模式和做法开发人员中心](http://dev.office.com/patterns-and-practices)。我们欢迎您做出 [贡献](https://github.com/OfficeDev/PnP/wiki/contributing-to-Office-365-developer-patterns-and-practices)。

## 开始之前

若要开始，请从 GitHub 上的 [Office 365 开发人员模式和做法](https://github.com/OfficeDev/PnP/tree/dev)项目下载 [Core.LargeFileUpload](https://github.com/OfficeDev/PnP/tree/dev/Samples/Core.LargeFileUpload) 示例外接程序。


## 使用 Core.LargeFileUpload 示例外接程序

当您启动此代码示例时，控制台应用程序将显示。您必须提供 Office 365 的 SharePoint Online 网站集 URL 和登录凭据。通过身份验证后，控制台应用程序显示异常。当 FileUploadService.cs 中的  **UploadDocumentContent** 方法尝试使用 **FileCreationInformation.Content** 属性上载大于 2 MB 的文件时，将发生此异常。 **UploadDocumentContent** 还会创建一个名为"Docs"的文档库（如果不存在）。"Docs"文档库稍后将在此代码示例中使用。


 **注释**  本文中的代码按原样提供，不提供任何明示或暗示的担保，包括对特定用途适用性、适销性或不侵权的默示担保。


```C#
public void UploadDocumentContent(ClientContext ctx, string libraryName, string filePath)
        {
            Web web = ctx.Web;

            // Ensure that target library exists. Create if it is missing.
            if (!LibraryExists(ctx, web, libraryName))
            {
                CreateLibrary(ctx, web, libraryName);
            }

            FileCreationInformation newFile = new FileCreationInformation();

		 // The next line of code causes an exception to be thrown for files larger than 2 MB.
            newFile.Content = System.IO.File.ReadAllBytes(filePath);
            newFile.Url = System.IO.Path.GetFileName(filePath);

            // Get instances to the given library.
            List docs = web.Lists.GetByTitle(libraryName);
            
 // Add file to the library.
            Microsoft.SharePoint.Client.File uploadFile = docs.RootFolder.Files.Add(newFile);
            ctx.Load(uploadFile);
            ctx.ExecuteQuery();
        } 

```

在 FileUploadService.cs 中，此代码示例提供了可用于将大文件上载到文档库的三个选项：


-  **File.SaveBinaryDirect** 方法。
    
-  **FileCreationInformation.ContentStream** 属性。
    
-  **File** 类上的 **StartUpload** 、 **ContinueUpload** 和 **FinishUpload** 方法。
    
在 FileUploadService.cs 中， **SaveBinaryDirect** 使用具有 **FileStream** 对象的 **Microsoft.SharePoint.Client.File.SaveBinaryDirect** 方法将文件上载到文档库。




```C#
public void SaveBinaryDirect(ClientContext ctx, string libraryName, string filePath)
        {
            Web web = ctx.Web;
            // Ensure that the target library exists. Create it if it is missing.
            if (!LibraryExists(ctx, web, libraryName))
            {
                CreateLibrary(ctx, web, libraryName);
            }

            using (FileStream fs = new FileStream(filePath, FileMode.Open))
            {
                Microsoft.SharePoint.Client.File.SaveBinaryDirect(ctx, string.Format("/{0}/{1}", libraryName, System.IO.Path.GetFileName(filePath)), fs, true);
            }

        } 

```

在 FileUploadService.cs 中， **UploadDocumentContentStream** 使用具有 **FileStream** 对象的 **FileCreationInformation.ContentStream** 属性将文件上载到文档库。




```C#
public void UploadDocumentContentStream(ClientContext ctx, string libraryName, string filePath)
        {

            Web web = ctx.Web;
            // Ensure that the target library exists. Create it if it is missing.
            if (!LibraryExists(ctx, web, libraryName))
            {
                CreateLibrary(ctx, web, libraryName);
            }

            using (FileStream fs = new FileStream(filePath, FileMode.Open))
            {
                FileCreationInformation flciNewFile = new FileCreationInformation();

                // This is the key difference for the first case - using ContentStream property
                flciNewFile.ContentStream = fs;
                flciNewFile.Url = System.IO.Path.GetFileName(filePath);
                flciNewFile.Overwrite = true;

                List docs = web.Lists.GetByTitle(libraryName);
                Microsoft.SharePoint.Client.File uploadFile = docs.RootFolder.Files.Add(flciNewFile);

                ctx.Load(uploadFile);
                ctx.ExecuteQuery();
            }
        }

```

在 FileUploadService.cs 中， **UploadFileSlicePerSlice** 将大文件上载到文档库，作为块或片段集。 **UploadFileSlicePerSlice** 执行以下任务：


1. 获取一个新的 GUID。若要以块的形式上载文件，必须使用唯一的 GUID。
    
2. 计算块的大小（以字节为单位）。要计算以字节为单位的块大小， **UploadFileSlicePerSlice** 使用 **fileChunkSizeInMB** ，这将指定单个块的大小（以 MB 为单位）。
    
3. 测试要上载的文件大小 ( **fileSize** ) 是否小于或等于块大小 ( **blockSize** )。
    
      1. 如果  **fileSize** 小于或等于块大小，示例将确保文件仍可使用 **FileCreationInformation.ContentStream** 属性上载。请记住，建议的块大小为 10 MB 或更大。
    
  2. 如果  **fileSize** 大于块大小：
    
      1. 文件块将读取到 **缓冲区** 中。
    
  2. 如果块大小等于文件大小，将读取整个文件。块将复制到  **lastBuffer** ，然后 **lastBuffer** 使用 **File.FinishUpload** 上载块。
    
  3. 如果块大小不等于文件大小，则需从文件中读取多个块。调用  **File.StartUpload** 以上载第一个块。用作下一个块的起点的 **fileoffset** 将设置为从第一个块上载的字节数。读取下一个块时，如果尚未达到最后一个块，将调用 **File.ContinueUpload** 以上载下一个文件块。此过程一直重复到读取完最后一个块。读取完最后一个块之后， **File.FinishUpload** 将上载最后一个块并提交文件。此方法完成后，将更改文件内容。
    

 **注释**  考虑下列最佳做法：




```
 public Microsoft.SharePoint.Client.File UploadFileSlicePerSlice(ClientContext ctx, string libraryName, string fileName, int fileChunkSizeInMB = 3)
        {
            // Each sliced upload requires a unique ID.
            Guid uploadId = Guid.NewGuid();

            // Get the name of the file.
            string uniqueFileName = Path.GetFileName(fileName);

            // Ensure that target library exists, and create it if it is missing.
            if (!LibraryExists(ctx, ctx.Web, libraryName))
            {
                CreateLibrary(ctx, ctx.Web, libraryName);
            }
            // Get the folder to upload into. 
            List docs = ctx.Web.Lists.GetByTitle(libraryName);
            ctx.Load(docs, l => l.RootFolder);
            // Get the information about the folder that will hold the file.
            ctx.Load(docs.RootFolder, f => f.ServerRelativeUrl);
            ctx.ExecuteQuery();

            // File object.
            Microsoft.SharePoint.Client.File uploadFile;

            // Calculate block size in bytes.
            int blockSize = fileChunkSizeInMB * 1024 * 1024;

            // Get the information about the folder that will hold the file.
            ctx.Load(docs.RootFolder, f => f.ServerRelativeUrl);
            ctx.ExecuteQuery();


            // Get the size of the file.
            long fileSize = new FileInfo(fileName).Length;

            if (fileSize <= blockSize)
            {
                // Use regular approach.
                using (FileStream fs = new FileStream(fileName, FileMode.Open))
                {
                    FileCreationInformation fileInfo = new FileCreationInformation();
                    fileInfo.ContentStream = fs;
                    fileInfo.Url = uniqueFileName;
                    fileInfo.Overwrite = true;
                    uploadFile = docs.RootFolder.Files.Add(fileInfo);
                    ctx.Load(uploadFile);
                    ctx.ExecuteQuery();
                    // Return the file object for the uploaded file.
                    return uploadFile;
                }
            }
            else
            {
                // Use large file upload approach.
                ClientResult<long> bytesUploaded = null;

                FileStream fs = null;
                try
                {
                    fs = System.IO.File.Open(fileName, FileMode.Open, FileAccess.Read, FileShare.ReadWrite);
                    using (BinaryReader br = new BinaryReader(fs))
                    {
                        byte[] buffer = new byte[blockSize];
                        Byte[] lastBuffer = null;
                        long fileoffset = 0;
                        long totalBytesRead = 0;
                        int bytesRead;
                        bool first = true;
                        bool last = false;

                        // Read data from file system in blocks. 
                        while ((bytesRead = br.Read(buffer, 0, buffer.Length)) > 0)
                        {
                            totalBytesRead = totalBytesRead + bytesRead;

                            // You've reached the end of the file.
                            if (totalBytesRead == fileSize)
                            {
                                last = true;
                                // Copy to a new buffer that has the correct size.
                                lastBuffer = new byte[bytesRead];
                                Array.Copy(buffer, 0, lastBuffer, 0, bytesRead);
                            }

                            if (first)
                            {
                                using (MemoryStream contentStream = new MemoryStream())
                                {
                                    // Add an empty file.
                                    FileCreationInformation fileInfo = new FileCreationInformation();
                                    fileInfo.ContentStream = contentStream;
                                    fileInfo.Url = uniqueFileName;
                                    fileInfo.Overwrite = true;
                                    uploadFile = docs.RootFolder.Files.Add(fileInfo);

                                    // Start upload by uploading the first slice. 
                                    using (MemoryStream s = new MemoryStream(buffer))
                                    {
                                        // Call the start upload method on the first slice.
                                        bytesUploaded = uploadFile.StartUpload(uploadId, s);
                                        ctx.ExecuteQuery();
                                        // fileoffset is the pointer where the next slice will be added.
                                        fileoffset = bytesUploaded.Value;
                                    }

                                    // You can only start the upload once.
                                    first = false;
                                }
                            }
                            else
                            {
                                // Get a reference to your file.
                                uploadFile = ctx.Web.GetFileByServerRelativeUrl(docs.RootFolder.ServerRelativeUrl + System.IO.Path.AltDirectorySeparatorChar + uniqueFileName);

                                if (last)
                                {
                                    // Is this the last slice of data?
                                    using (MemoryStream s = new MemoryStream(lastBuffer))
                                    {
                                        // End sliced upload by calling FinishUpload.
                                        uploadFile = uploadFile.FinishUpload(uploadId, fileoffset, s);
                                        ctx.ExecuteQuery();

                                        // Return the file object for the uploaded file.
                                        return uploadFile;
                                    }
                                }
                                else
                                {
                                    using (MemoryStream s = new MemoryStream(buffer))
                                    {
                                        // Continue sliced upload.
                                        bytesUploaded = uploadFile.ContinueUpload(uploadId, fileoffset, s);
                                        ctx.ExecuteQuery();
                                        // Update fileoffset for the next slice.
                                        fileoffset = bytesUploaded.Value;
                                    }
                                }
                            }

                        } // while ((bytesRead = br.Read(buffer, 0, buffer.Length)) > 0)
                    }
                }
                finally
                {
                    if (fs != null)
                    {
                        fs.Dispose();
                    }
                }
            }

            return null;
        }
```

完成代码示例后，在 Office 365 网站中，您可以转到"Docs"文档库，方法是选择"最近的文档">"Docs"。确认"Docs"文档库中包含三个大文件。


## 其他资源



- [适用于 SharePoint 2013 和 SharePoint Online 的企业内容管理解决方案](Enterprise-Content-Management-solutions-for-SharePoint-2013-and-SharePoint-Online.md)
    
    
    
- [Core.BulkDocumentUploader 示例](https://github.com/OfficeDev/PnP/tree/dev/Samples/Core.BulkDocumentUploader)
    
