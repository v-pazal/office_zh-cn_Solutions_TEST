
# 在 SharePoint 外接程序中使用异步操作
使用 Microsoft Azure WebJobs 在 SharePoint 外接程序中实现异步操作。

 **上次修改时间：** 2015年8月7日

 _ **适用范围：** SharePoint 2013?| SharePoint Add-ins?| SharePoint Online_

 **注释**  名称"SharePoint 相关应用程序"将更改为"SharePoint 外接程序"。在转换期间，某些 SharePoint 产品和 Visual Studio 工具的文档和 UI 仍可能使用术语"SharePoint 相关应用程序"。有关详细信息，请参阅 [Office 和 SharePoint 相关应用程序的新名称](05b07b04-6c8b-4b7e-bd86-e32c589dfead.md#bk_newname)。

[Core.QueueWebJobUsage](https://github.com/OfficeDev/PnP/tree/dev/Samples/Core.QueueWebJobUsage) 示例向您演示如何在 Office 365 中使用提供程序托管的外接程序和 Azure WebJobs 创建和运行异步操作。
使用此解决方案可以执行以下操作：

- 提升远程事件接收器的性能。
    
- 迁移到 SharePoint Online，并实现与在 SharePoint 本地环境中使用的相同的计时器作业功能。 
    
- 实现您要针对 SharePoint 环境运行的长时间运行的操作。例如：
    
      -  **AppInstalled** 事件，它的运行时间超过其 30 秒的超时间隔。在外接程序事件处理程序中存在异步进程。有关详细信息，请参阅 [处理 SharePoint 外接程序中的事件](http://msdn.microsoft.com/library/c050d056-8548-4496-a053-016779d723d9%28Office.15%29.aspx)和 [在 SharePoint 外接程序中创建外接程序事件接收器](http://msdn.microsoft.com/library/f40c910f-12a2-4caa-8e91-c7a61ae540db%28Office.15%29.aspx)。
    
  - 自定义网站集设置。
    
  - 在 Office 365 和本地系统之间同步数据的操作。
    
  - 执行复杂计算的操作。
    
下图显示所需组件的高级体系结构，以及在执行异步操作时这些组件的处理流程。

![图表显示异步操作流。SharePoint 外接程序调用将消息添加到 Azure 存储队列的提供程序承载的外接程序。一个 Azure WebJob 处理消息并在 SharePoint 网站上执行操作。](media/a4abdcc1-504c-46d7-9f6d-ea209d994605.png)若要在提供程序托管的外接程序中使用 Azure WebJobs 实现异步操作，请执行以下操作：


1. 用户运行部署在 SharePoint Online 中的外接程序。
    
2. 提供程序托管的外接程序提供 Azure WebJob 所需的输入参数，然后将新邮件添加到 Azure 存储队列。
    
3. Azure 存储队列在连续运行的 Azure WebJob 中触发一个事件，开始处理新邮件。
    
4. Azure WebJob 将针对您的 SharePoint Online 网站运行自定义业务逻辑。 
    

    
 **注释**  将邮件添加到 Azure 存储队列使用的过程不同于运行 Azure WebJob 的过程。因此，您的外接程序可以实现异步操作，方法为：使用一个过程将新邮件添加到队列，然后使用 Azure WebJob 在另一个过程中处理这些邮件。 

 **为改进此内容做贡献**
您可以获取最新的更新，或为改进 [GitHub 上的此文章](https://github.com/OfficeDev/PnP-Guidance)做贡献。您还可以为改进本示例以及 [GitHub 上的其他示例](https://github.com/OfficeDev/PnP)做贡献。有关示例的完整列表，请参阅 [模式和做法开发人员中心](http://dev.office.com/patterns-and-practices)。我们欢迎您做出 [贡献](https://github.com/OfficeDev/PnP/wiki/contributing-to-Office-365-developer-patterns-and-practices)。 

## 开始之前

若要开始，请先从 GitHub 上的 [Office 365 开发人员模式和做法](https://github.com/OfficeDev/PnP/tree/dev)项目下载 [Core.QueueWebJobUsage](https://github.com/OfficeDev/PnP/tree/dev/Samples/Core.QueueWebJobUsage) 示例外接程序，然后创建 Azure 帐户，将详细信息添加到该帐户，并验证 Azure WebJob 是否正在运行。

若要创建 Azure 存储帐户以访问 Azure 存储队列，请执行以下操作：


1. 登录到您的 Microsoft [Azure 管理门户](https://manage.windowsazure.com)。
    
2. 选择"新建">"数据服务">"存储">"快速创建"。
    
3. 在"URL"中，输入您的域名。例如，输入  **contoso**。 
    
4. 在"位置/地缘组"中，选择一个合适的位置。
    
5. 在"复制"中，选择"地域冗余"。 
    
6. 选择"创建存储帐户"。
    
若要将详细信息添加到您新创建的存储帐户中，请执行以下操作：


1. 创建 Azure 存储帐户后，请选择"管理访问密钥"。
    
2. 在"管理访问密钥"中，复制"存储帐户名"和"主访问密钥"。
    
3. 将客户端 ID、客户端密码和您的 Azure 存储帐户信息应用到几个配置文件中。 
    
4. 在 Helper Project\Core.QueueWebJobUsage.Console.SendMessage 中，打开 Program.cs，并将您的网站 URL 输入"siteUrl"框中。
    
5. 在 Core.QueueWebJobUsage.Job 的"属性"中，将  **Microsoft.SharePoint.Client** 和 **Microsoft.SharePoint.Client.Runtime** 引用的"复制本地"设置为 **True**。将"复制本地"设置为  **True**会将引用的程序集复制到 Azure，以便 Azure WebJob 可以解析对这些程序集的引用。
    
6. 部署 Azure WebJob。有关详细信息，请参阅 [部署 WebJobs 项目](https://azure.microsoft.com/documentation/articles/websites-dotnet-deploy-webjobs/#deploy)。 
    
若要验证您的 Azure WebJob是否正在运行，请执行以下操作：


1. 登录到您的 [Microsoft Azure 管理门户](https://manage.windowsazure.com)。
    
2. 选择"web 应用"，然后选择您输入的 Microsoft Azure 网站。 
    
3. 选择"WEBJOBS"。
    
4. 验证您的 Azure WebJob 是否出现在列表中，并且是否已将"计划"设置为"连续运行"。
    
5. 选择"配置"。
    
6. 在"外接程序设置"中，为  **ClientId** 和 **ClientSecret** 创建新的外接程序设置。从 Core.QueueWebJobUsage.Job\app.config 文件复制 **ClientId** 和 **ClientSecret** 键值对。
    
7. 在"连接字符串"中，为  **AzureWebJobsDashboard** 和 **AzureWebJobsStorage** 创建新的连接字符串。从 Core.QueueWebJobUsage.Job\app.config 文件复制 **AzureWebJobsDashboard** 和 **AzureWebJobsStorage** 键（名称）和值对，然后将键入的值设置为"自定义"。
    
8. 选择"保存"。
    

### 应用配置设置

使用下表中的信息将配置设置应用于 Core.QueueWebJobUsage Visual Studio 解决方案。


|
|
|**文件位置**|**要更新的密钥**|**要更新的值信息**|
|:-----|:-----|:-----|
|Helper Project\Core.QueueWebJobUsage.Console.SendMessage\app.config|**StorageConnectionString**| 将 **[Your Account name]** 替换为从 Azure 管理门户复制的存储帐户名称。|
||| 将 **[Your Account Key]** 替换为从 Azure 管理门户复制的主访问密钥。|
|Core.QueueWebJobUsageWeb\web.config|**StorageConnectionString**| 将 **[YourAccountName]** 替换为从 Azure 管理门户复制的存储帐户名称。|
||| 将 **[YourAccountKey]** 替换为从 Azure 管理门户复制的主访问密钥。|
|Core.QueueWebJobUsage.Job\app.config|**StorageConnectionString**| 将 **[YourAccountName]** 替换为从 Azure 管理门户复制的存储帐户名称。|
||| 将 **[YourAccountKey]** 替换为从 Azure 管理门户复制的主访问密钥。|
||**ClientId**| 将 **[Your Add-in ID]** 替换为从 Core.QueueWebJobUsageWeb\web.config 复制的客户端 ID。|
||**ClientSecret**| 将 **[Your Add-in Secret]** 替换为从 Core.QueueWebJobUsageWeb\web.config 复制的客户端密码。|
||**AzureWebJobsDashboard**| 将 **[YourAccount]** 替换为从 Azure 管理门户复制的存储帐户名称。|
||| 将 **[YourKey]** 替换为从 Azure 管理门户复制的主访问密钥。|
||**AzureWebJobsStorage**| 将 **[YourAccount]** 替换为从 Azure 管理门户复制的存储帐户名称。|
||| 将 **[YourKey]** 替换为从 Azure 管理门户复制的主访问密钥。|

 **注释**  例如，如果更新了 Core.QueueWebJobUsageWeb 中的  **ClientId** 和 **ClientSecret** ，当您在 AppManifest.xml 中递增版本号时，请确保更新 Core.QueueWebJobUsage.Job\app.config 中的 **ClientId** 和 **ClientSecret** 。


## 使用 Core.QueueWebJobUsage 外接程序

下表介绍了 Core.QueueWebJobUsage 解决方案中的所有 Visual Studio 项目。


|
|
|**Visual Studio 项目**|**说明**|
|:-----|:-----|
|Core.QueueWebJobUsage|您的 SharePoint 外接程序 项目。以下权限是必需的：
<ul xmlns:xlink="http://www.w3.org/1999/xlink" xmlns:mtps="http://msdn2.microsoft.com/mtps" xmlns:mshelp="http://msdn.microsoft.com/mshelp" xmlns:ddue="http://ddue.schemas.microsoft.com/authoring/2003/5" xmlns:msxsl="urn:schemas-microsoft-com:xslt"><li><p><b>AllowAppOnlyPolicy</b> 。</p></li><li><p>"Web"上的 <b>FullControl</b>  权限。</p></li></ul>|
|Core.QueueWebJobUsage.Common|包含此解决方案的业务对象和业务逻辑代码（例如，将邮件添加到存储队列的方法）。添加此项目以共享不同项目之间的业务对象和业务逻辑。在您的实现过程中可能不需要这一项。|
|Core.QueueWebJobUsage.Job|在将新邮件添加到 Azure 存储队列时运行的 Azure WebJob。此项目包含您的自定义业务逻辑代码。 |
|Core.QueueWebJobUsageWeb|提供程序托管的外接程序，其中包含 Core.QueueWebJobUsage 项目的 UI。 |
|Helper Project\Core.QueueWebJobUsage.Console.SendMessage|一个帮助程序项目，可用于验证存储帐户信息和队列创建过程，并且可以将邮件发送至队列中进行处理，而无需设置本文中介绍的整个解决方案。 |
当您运行 Core.QueueWebJobUsage 代码示例时，将显示提供程序托管的外接程序，并显示两个按钮："同步操作"和"异步操作"。当您选择"同步操作"时，Pages\Default.aspx 中的  **btnSync_Click** 会模拟长时间运行的同步进程。在此代码示例中， **btnSync_Click** 会让当前线程休眠 10 秒钟，然后创建一个文档库。当您选择"异步操作"时，Pages\Default.aspx 中的 **btnAsync_Click** 将执行以下操作：


1. 获取当前用户。
    
2. 创建  **SiteModifyRequest** 业务对象，用于存储数据以将其加入要发送至 Azure 存储队列的邮件中。在此代码示例中，要发送的数据包括当前用户的名称和当前网站的 URL。
    
3. 调用  **SiteManager().AddAsyncOperationRequestToQueue** ，将邮件添加到 Azure 存储队列中。
    

 **注释**  本文中的代码按原样提供，不提供任何明示或暗示的担保，包括对特定用途适用性、适销性或不侵权的默示担保。




```C#
protected void btnAsync_Click(object sender, EventArgs e)
        {

            var spContext = SharePointContextProvider.Current.GetSharePointContext(Context);

            using (var clientContext = spContext.CreateUserClientContextForSPHost())
            {
                // Get the current user.
                var currUser = clientContext.Web.CurrentUser;
                clientContext.Load(currUser);
                clientContext.ExecuteQuery();

                // Create business object, and then add the request to the queue.
                SiteModifyRequest request = new SiteModifyRequest() { RequestorName = currUser.Title, SiteUrl = Page.Request["SPHostUrl"] };
                new SiteManager().AddAsyncOperationRequestToQueue(request,
                                                                  ConfigurationManager.AppSettings["StorageConnectionString"]);

                processViews.ActiveViewIndex = 1;
                lblStatus.Text = "Asynchronous operation to create document library started.";
            }
        }
```

在 SiteManager.cs 的 Core.QueueWebJobUsage.Common 中， **AddAsyncOperationRequestToQueue** 执行以下操作：


1. 使用 Core.QueueWebJobUsageWeb\web.config 文件中的  **AccountName** 和 **AccountKey** 配置信息创建 [CloudStorageAccount](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.cloudstorageaccount.aspx) 对象。
    
2. 使用 [CloudStorageAccount.CreateCloudQueueClient](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.cloudstorageaccount.createcloudqueueclient.aspx) 创建 Azure 存储队列客户端。
    
3. 使用 [CloudQueueClient.GetQueueReference](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.queue.cloudqueueclient.getqueuereference.aspx) 获取对 Azure 存储队列的引用，其中包含与 **SiteManager.StorageQueueName** 常量的值等同的名称。
    
4. 使用 [CloudQueue.CreateIfNotExists](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.queue.cloudqueue.createifnotexists.aspx) 创建 Azure 存储队列（如果不存在）。
    
5. 使用 [CloudQueue.AddMessage](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.queue.cloudqueue.addmessage.aspx) 将新邮件添加到 Azure 存储队列。将 **ModifyRequest** 业务对象序列化为 [CloudQueueMessage](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.queue.cloudqueuemessage.aspx) 对象（已添加到 Azure 存储队列）。
    



```C#
public void AddAsyncOperationRequestToQueue(SiteModifyRequest modifyRequest, 
                                                    string storageConnectionString)
        {
            CloudStorageAccount storageAccount =
                                CloudStorageAccount.Parse(storageConnectionString);

            // Get queue or create a new one if one does not exist.
            CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
            CloudQueue queue = queueClient.GetQueueReference(SiteManager.StorageQueueName);
            queue.CreateIfNotExists();

            // Add a message to queue.
            queue.AddMessage(new CloudQueueMessage(JsonConvert.SerializeObject(modifyRequest)));
        }
```

将邮件添加到 Azure 存储队列后，连续运行 Azure WebJob 将等待并处理新邮件。Azure WebJob 在 Core.QueueWebJobUsage.Job 中定义。当 Azure WebJob运行时，Core.QueueWebJobUsage.Job\Program.cs 中的 Main 创建一个新的  **JobHost** ，然后调用 **RunAndBlock** 。 **JobHost** 协调对使用 **QueueTrigger** 属性标记的方法的调用，并监视特定队列上的邮件。 **RunAndBlock** 可确保 Azure WebJob 持续运行并调用 **ProcessQueueMessage** ，这就是在将新邮件添加到 Azure 存储队列时将触发的方法。Azure WebJobs SDK 将 **Main** 线程与 **ProcessQueueMessage** 关联在一起。有关详细信息，请参阅 [在 Azure 外接程序服务中创建 .NET WebJob](https://azure.microsoft.com/documentation/articles/websites-dotnet-webjobs-sdk-get-started/)。




```C#
static void Main()
        {
            var host = new JobHost();
            // The following code ensures that the WebJob will run continuously.
            host.RunAndBlock();
        }
```

 **ProcessQueueMessage** 处理添加到 Azure 存储队列中的新邮件，方法如下：


1. 使用  **QueueTrigger** 属性指定当有新邮件写入到名称等同于 **SiteManager.StorageQueueName** 值的队列中时应触发 **ProcessQueueMessage** 。
    
2. 使用作为参数传递到  **ProcessQueueMessage** 的 **log** 变量写入 Azure WebJob 日志。
    
3. 调用  **SiteManager().PerformSiteModification** 在网站上执行长时间运行的业务进程。在此代码示例中，在线程休眠 10 秒钟后创建文档库。
    



```C#
public static void ProcessQueueMessage(
            [QueueTrigger(SiteManager.StorageQueueName)] 
            SiteModifyRequest modifyRequest, TextWriter log)
        {
            log.WriteLine(string.Format("{0} '{1}' {2} '{3}'.",
                            "Received new site modification request with URL", 
                            modifyRequest.SiteUrl, 
                            "from person named as ", 
                            modifyRequest.RequestorName));
            
            try
            {
                Uri targetSite = new Uri(modifyRequest.SiteUrl);
                
                // Get the realm for the URL.
                string realm = TokenHelper.GetRealmFromTargetUrl(targetSite);

                // Get the access token for the URL.  
                // Requires this add-in to be registered with the tenant.
                string accessToken = TokenHelper.GetAppOnlyAccessToken(
                                                    TokenHelper.SharePointPrincipal,
                                                    targetSite.Authority, realm).AccessToken;

                // Get client context with access token.
                using (var ctx =
                    TokenHelper.GetClientContextWithAccessToken(
                                                    targetSite.ToString(), accessToken))
                {
                    // Call business logic code.
                    new SiteManager().PerformSiteModification(ctx, modifyRequest);
                }
            }
            catch (Exception ex)
            {
                log.WriteLine(string.Format("Site modification to URL {0} failed with following details.", modifyRequest.SiteUrl));
                log.WriteLine(ex.ToString());
                throw;
            }
        }
```


## 其他资源



- [Office 365 开发模式和做法解决方案指南](https://msdn.microsoft.com/library/office/dn904529.aspx)。
    
- [使用 SharePoint 中的远程事件接收器](https://msdn.microsoft.com/library/dn957928.aspx)
    
- [使用 ChangeQuery 和 ChangeToken 查询 SharePoint 更改日志](https://msdn.microsoft.com/library/dn986828.aspx)
    
