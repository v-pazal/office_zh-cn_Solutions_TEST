
# 在 SharePoint 中创建远程计时器作业
在 SharePoint 中创建远程计时器作业，以执行后台任务来管理或控制您的环境。

 **上次修改时间：** 2015年8月7日

 _ **适用范围：** SharePoint 2013?| SharePoint Add-ins?| SharePoint Online_

 **注释**  名称"SharePoint 相关应用程序"将更改为"SharePoint 外接程序"。在转换期间，某些 SharePoint 产品和 Visual Studio 工具的文档和 UI 仍可能使用术语"SharePoint 相关应用程序"。有关详细信息，请参阅 [Office 和 SharePoint 相关应用程序的新名称](05b07b04-6c8b-4b7e-bd86-e32c589dfead.md#bk_newname)。

通过监视并对 SharePoint 数据执行操作，创建远程计时器作业来管理 SharePoint。远程计时器作业不会在您的 SharePoint 服务器上运行。相反，远程计时器作业会计划在其他服务器上运行的任务。计时器作业使用方法的示例包括如下内容：

- 执行管理任务，例如，在不满足特定条件时在网站上显示消息，或强制执行保留策略。
    
- 运行占用大量处理器资源的计划的进程。
    
 **为改进此内容做贡献**
您可以获取最新的更新，或为改进 [GitHub 上的此文章](https://github.com/OfficeDev/PnP-Guidance)做贡献。您还可以为改进本示例以及 [GitHub 上的其他示例](https://github.com/OfficeDev/PnP)做贡献。有关示例的完整列表，请参阅 [模式和做法开发人员中心](http://dev.office.com/patterns-and-practices)。我们欢迎您做出 [贡献](https://github.com/OfficeDev/PnP/wiki/contributing-to-Office-365-developer-patterns-and-practices)。 

## 在开始创建远程计时器作业之前

若要开始，请从 GitHub 上的 [Office 365 开发人员模式和做法](https://github.com/OfficeDev/PnP/tree/dev)项目下载 [Core.TimerJobs.Samples ](https://github.com/OfficeDev/PnP/tree/dev/Solutions/Core.TimerJobs.Samples) 示例外接程序。

若要开始使用 Core.TimerJobs.Samples 解决方案，您需要选择一个启动项目，以 SimpleJob 项目为例，打开（右键单击） **Core.TimerJobs.Samples.SimpleJobsolution** 的快捷菜单，然后选择"设置为启动项目"。


 **注释**  当您在 Visual Studio 中创建新项目时，若要开始构建新的远程计时器作业，请将  **OfficeDevPnP.Core** NuGet 包添加到项目中。在 Visual Studio 中，选择"工具">"NuGet 包管理器">"管理解决方案的 NuGet 包"。


## 计划远程计时器作业

可计划计时器作业运行一次或执行重复作业。若要在生产环境中计划远程计时器作业，则需要将代码编译为 .exe 文件，然后使用 Windows 任务计划程序或 Microsoft Azure WebJob 运行此 .exe 文件。您可以在 [计时器作业部署选项](https://github.com/OfficeDev/PnP/blob/master/OfficeDevPnP.Core/TimerJob%20Framework.md#timer-job-deployment-options)中了解详细信息。


## 使用 Core.TimerJobs.Samples.SimpleJob 外接程序

在 Core.TimerJobs.Samples.SimpleJob 中，Program.cs 中的  **Main** 执行以下步骤：


1. 创建 SimpleJob 对象，该对象继承自 [OfficeDevPnP.Core.Framework.TimerJobs.TimerJob](https://github.com/OfficeDev/PnP/blob/dev/OfficeDevPnP.Core/OfficeDevPnP.Core/Framework/TimerJobs/TimerJob.cs) 基类。
    
2. 在使用  **TimerJob.UseOffice365Authentication** 运行计时器作业时，设置要使用的 Office 365 用户凭据。用户凭据必须对网站集具有相应的访问权限。您可以在 [身份验证](https://github.com/OfficeDev/PnP/blob/master/OfficeDevPnP.Core/TimerJob%20Framework.md#authentication)中了解详细信息。
    
3. 使用  **TimerJob.AddSite** 添加计时器作业应在其上执行任务的网站。您可以选择重复 **TimerJob.AddSite** 语句来添加多个网站，或使用通用符 ***** 在特定 URL 下添加所有网站。例如，http://contoso.sharepoint.com/sites/* 会在"网站"托管的路径下的所有网站上运行计时器作业。
    
4. 使用  **PrintJobSettingsAndRunJob** 打印计时器作业信息和运行计时器作业。
    

 **注释**  本文中的代码按原样提供，不提供任何明示或暗示的担保，包括对特定用途适用性、适销性或不侵权的默示担保。


```C#
 static void Main(string[] args)
        {
            SimpleJob simpleJob = new SimpleJob();
            
            // The user credentials must have access to the site collections you supply.
            simpleJob.UseOffice365Authentication(User, Password);

            // Use the following code if you are using SharePoint Server 2013 on-premises. 
            //simpleJob.UseNetworkCredentialsAuthentication(User, Password, Domain);
            
            // Add one or more sites that the timer job should work with.
            simpleJob.AddSite("https://contoso.sharepoint.com/sites/dev");
            
            // Prints timer job information and then calls Run().
            PrintJobSettingsAndRunJob(simpleJob);
        }
```

在实例化  **simpleJob** 对象时， **SimpleJob** 构造函数会执行以下操作：


1. 调用 TimerJob 基类构造函数。 
    
2. 分配  **SimpleJob_TimerJobRun** 事件处理程序以处理 **TimerJobRun** 事件。在对 **TimerJob.Run** 进行调用时，将运行 **SimpleJob_TimerJobRun** ，本文稍后将对此进行详细说明。
    



```C#
public SimpleJob() : base("SimpleJob")
        {
            TimerJobRun += SimpleJob_TimerJobRun;
        }
```

运行  **PrintJobSettingsAndRunJob** 时，会将 TimerJob 的输出写入控制台窗口，然后调用 **TimerJob.Run** 。




```C#
 private static void PrintJobSettingsAndRunJob(TimerJob job)
        {
            Console.ForegroundColor = ConsoleColor.Yellow;
            Console.WriteLine("************************************************");
            Console.WriteLine("Job name: {0}", job.Name);
            Console.WriteLine("Job version: {0}", job.Version);
            Console.WriteLine("Use threading: {0}", job.UseThreading);
            Console.WriteLine("Maximum threads: {0}", job.MaximumThreads);
            Console.WriteLine("Expand sub sites: {0}", job.ExpandSubSites);
            Console.WriteLine("Authentication type: {0}", job.AuthenticationType);
            Console.WriteLine("Manage state: {0}", job.ManageState);
            Console.WriteLine("SharePoint version: {0}", job.SharePointVersion);
            Console.WriteLine("************************************************");
            Console.ForegroundColor = ConsoleColor.Gray;

            // Run job.
            job.Run();
        }
```

 **TimerJob.Run** 引发 **TimerJobRun** 事件。 **TimerJob.Run** 调用 SimpleJob.cs 中的 **SimpleJob_TimerJobRun** ，您将其设置为事件处理程序来处理 **SimpleJob** 构造函数中的 **TimerJobRun** 事件。在 **SimpleJob_TimerJobRun** 中，您可以在计时器作业运行时添加想要计时器作业执行的自定义代码。 **SimpleJob_TimerJobRun** 使用 Program.cs 中的 **TimerJob.AddSite** 在网站上运行您的自定义代码。在此代码示例中， **SimpleJob_TimerJobRun** 使用 **TimerJob** 的 **ClientContext** 将网站的标题写入控制台窗口。如果使用 **TimerJob.AddSite** 添加了多个网站，则针对每个网站调用 **SimpleJob_TimerJobRun** 。




```C#
 void SimpleJob_TimerJobRun(object sender, TimerJobRunEventArgs e)
        {
            e.WebClientContext.Load(e.WebClientContext.Web, p => p.Title);
            e.WebClientContext.ExecuteQueryRetry();
            Console.WriteLine("Site {0} has title {1}", e.Url, e.WebClientContext.Web.Title);
        }
```


## 示例：内容类型强制保留计时器作业

Core.TimerJobs.Samples.ContentTypeRetentionEnforcementJob 项目演示如何使用 SharePoint 计时器作业来强制对内容类型执行保留策略。使用 app.config 中的  **ContentTypeRetentionPolicyPeriod** 元素指定以下内容：


-  **key** ，这是应用保持期的内容类型的内容类型 ID。
    
-  **value** ，这是保持期，以天为单位。保持期将应用于使用 **key** 中指定的内容类型所创建的所有列表项。
    

```XML
<ContentTypeRetentionPolicyPeriod>
    <!-- Key is the content type ID, and value is the retention period in days -->
    <!-- Specifies an audio content type should be kept for 183 days -->
    <add key="0x0101009148F5A04DDD49cbA7127AADA5FB792B006973ACD696DC4858A76371B2FB2F439A" value="183" />
    <!-- Specifies a document content type should be kept for 365 days -->   
    <add key="0x0101" value="365" />
  </ContentTypeRetentionPolicyPeriod>
```

将  **ContentTypeRetentionEnforcementJob_TimerJobRun** 设置为 **TimerJobRun** 事件的事件处理程序。在 Program.cs 中调用 **TimerJob.Run** 时， **ContentTypeRetentionEnforcementJob_TimerJobRun** 在使用 Program.cs 中的 **TimerJob.AddSite** 添加的每个网站上执行以下步骤：


1. 获取网站上的所有文档库。
    
2. 针对网站上的每个文档库，读取 app.config 的  **ContentTypeRetentionPolicyPeriod** 中指定的配置信息。针对从 app.config 读取的每个内容类型 ID 和保持期配对，调用 **ApplyRetentionPolicy** 。
    



```C#
 void ContentTypeRetentionEnforcementJob_TimerJobRun(object sender, TimerJobRunEventArgs e)
        {
            try
            {
                Log.Info("ContentTypeRetentionEnforcementJob", "Scanning web {0}", e.Url);

                // Get all document libraries. Lists are excluded.
                var documentLibraries = GetAllDocumentLibrariesInWeb(e.WebClientContext, e.WebClientContext.Web);

                // Iterate through all document libraries.
                foreach (var documentLibrary in documentLibraries)
                {
                    Log.Info("ContentTypeRetentionEnforcementJob", "Scanning library {0}", documentLibrary.Title);

                    // Iterate through configured content type retention policies specified in app.config.
                    foreach (var contentTypeName in configContentTypeRetentionPolicyPeriods.Keys)
                    {
                        var retentionPeriods = configContentTypeRetentionPolicyPeriods.GetValues(contentTypeName as string);
                        if (retentionPeriods != null)
                        {
                            var retentionPeriod = int.Parse(retentionPeriods[0]);
                            ApplyRetentionPolicy(e.WebClientContext, documentLibrary, contentTypeName, retentionPeriod);
                        }
                    }
                }
            }
            catch(Exception ex)
            {
                Log.Error("ContentTypeRetentionEnforcementJob", "Exception processing site {0}. Exception is {1}", e.Url, ex.Message);
            }
        }
```

 **ApplyRetentionPolicy** 通过以下方式强制执行您的自定义保留策略操作：


1. 计算  **validationDate** 。 **ApplyRetentionPolicy** 方法强制在 **validationDate** 之前对已修改的文档执行保留策略操作。然后将 **validationDate** 的格式设置为 CAML 日期，并分配给 **camlDate** 。
    
2. 运行 CAML 查询，根据内容类型 ID 筛选文档库中的文档，该内容类型 ID 已在 app.config 中指定，其中"修改者"日期要早于  **camlDate** 。
    
3. 针对每个列表项，使用自定义代码应用要对文档执行的自定义保留操作。
    



```C#
private void ApplyRetentionPolicy(ClientContext clientContext, List documentLibrary, object contentTypeId, int retentionPeriodDays)
        {
            // Calculate validation date. You need to enforce the retention policy on any document modified before validation date.
            var validationDate = DateTime.Now.AddDays(-retentionPeriodDays);
            var camlDate = validationDate.ToString("yyyy-MM-ddTHH:mm:ssZ");

            // Get old documents in the library that match the content type.
            if (documentLibrary.ItemCount > 0)
            {
                var camlQuery = new CamlQuery();
                
                camlQuery.ViewXml = String.Format(
                    @"<View>
                        <Query>
                            <Where><And>
                                <BeginsWith><FieldRef Name='ContentTypeId'/><Value Type='ContentTypeId'>{0}</Value></BeginsWith>
                                <Lt><FieldRef Name='Modified' /><Value Type='DateTime'>{1}</Value></Lt>
                            </And></Where>
                        </Query>
                    </View>", contentTypeId, camlDate);

                var listItems = documentLibrary.GetItems(camlQuery);
                clientContext.Load(listItems,
                    items => items.Include(
                        item => item.Id,
                        item => item.DisplayName,
                        item => item.ContentType));

                clientContext.ExecuteQueryRetry(); 

                foreach (var listItem in listItems)
                {
                    Log.Info("ContentTypeRetentionEnforcementJob", "Document '{0}' has been modified earlier than {1}. Retention policy will be applied.", listItem.DisplayName, validationDate);
                    Console.WriteLine("Document '{0}' has been modified earlier than {1}. Retention policy will be applied.", listItem.DisplayName, validationDate);
                    
                    // Apply your custom retention actions here. For example, archiving documents, or starting a disposition workflow.
                }
            }
        }
```


## 示例：监管计时器作业

Core.TimerJobs.Samples.GovernanceJob 项目使用计时器作业来确保向您的网站集分配两名管理员，如果未分配两名管理员，则在网站上显示通知消息。

将  **SiteGovernanceJob_TimerJobRun** 设置为 **TimerJobRun** 事件的事件处理程序。在 Program.cs 中调用 **TimerJob.Run** 时， **SiteGovernanceJob_TimerJobRun** 在使用 Program.cs 中的 **TimerJob.AddSite** 添加的每个网站集上执行以下步骤：


1. 使用作为 [OfficeDevPnP.Core](https://github.com/OfficeDev/PnP/tree/master/OfficeDevPnP.Core) 一部分的扩展方法 [GetAdministrators](https://github.com/OfficeDev/PnP/blob/master/OfficeDevPnP.Core/OfficeDevPnP.Core/AppModelExtensions/SecurityExtensions.cs) 来获取分配给网站集的管理员数量。
    
2. 使用作为 [OfficeDevPnP.Core](https://github.com/OfficeDev/PnP/tree/master/OfficeDevPnP.Core) 一部分的 [UploadFile](https://github.com/OfficeDev/PnP/blob/master/OfficeDevPnP.Core/OfficeDevPnP.Core/AppModelExtensions/FileFolderExtensions.cs) 将 JavaScript 文件上载到 SiteAssets 或样式库列表。
    
3. 如果网站的管理员少于两名， [AddJSLink](https://github.com/OfficeDev/PnP/blob/master/OfficeDevPnP.Core/OfficeDevPnP.Core/AppModelExtensions/JavaScriptExtensions.cs) 会使用 JavaScript 向网站添加一个通知消息。您可以在 [使用 JavaScript 自定义您的 SharePoint 网站 UI](https://msdn.microsoft.com/ZH-CN/library/office/dn913116.aspx) 中了解详细信息。
    
4. 如果网站具有两个或多个管理员，则使用 [DeleteJsLink](https://github.com/OfficeDev/PnP/blob/master/OfficeDevPnP.Core/OfficeDevPnP.Core/AppModelExtensions/JavaScriptExtensions.cs) 删除通知消息。
    



```C#
void SiteGovernanceJob_TimerJobRun(object o, TimerJobRunEventArgs e)
        {
            try
            {
                string library = "";

                // Get the number of administrators.
                var admins = e.WebClientContext.Web.GetAdministrators();

                Log.Info("SiteGovernanceJob", "ThreadID = {2} | Site {0} has {1} administrators.", e.Url, admins.Count, Thread.CurrentThread.ManagedThreadId);

                // Get a reference to the list.
                library = "SiteAssets";
                List list = e.WebClientContext.Web.GetListByUrl(library);

                if (!e.GetProperty("ScriptFileVersion").Equals("1.0", StringComparison.InvariantCultureIgnoreCase))
                {
                    if (list == null)
                    {
                        // Get a reference to the list.
                        library = "Style%20Library";
                        list = e.WebClientContext.Web.GetListByUrl(library);
                    }

                    if (list != null)
                    {
                        // Upload js file to list.
                        list.RootFolder.UploadFile("sitegovernance.js", "sitegovernance.js", true);

                        e.SetProperty("ScriptFileVersion", "1.0");
                    }
                }

                if (admins.Count < 2)
                {
                    // Show notification message because you need at least two site collection administrators.
                    e.WebClientContext.Site.AddJsLink(SiteGovernanceJobKey, BuildJavaScriptUrl(e.Url, library));
                    Console.WriteLine("Site {0} marked as incompliant!", e.Url);
                    e.SetProperty("SiteCompliant", "false");
                }
                else
                {
                    // Remove the notification message because two administrators are assigned.
                    e.WebClientContext.Site.DeleteJsLink(SiteGovernanceJobKey);
                    Console.WriteLine("Site {0} is compliant", e.Url);
                    e.SetProperty("SiteCompliant", "true");
                }

                e.CurrentRunSuccessful = true;
                e.DeleteProperty("LastError");
            }
            catch(Exception ex)
            {
                Log.Error("SiteGovernanceJob", "Error while processing site {0}. Error = {1}", e.Url, ex.Message);
                e.CurrentRunSuccessful = false;
                e.SetProperty("LastError", ex.Message);
            }
        }
```


## 其他资源



- [Office 365 开发模式和做法解决方案指南](https://msdn.microsoft.com/library/office/dn904529.aspx)
    
- [计时器作业框架使用指南](https://github.com/OfficeDev/PnP/blob/master/OfficeDevPnP.Core/TimerJob%20Framework.md)
    
- [计时器作业框架](https://github.com/OfficeDev/PnP/tree/dev/Solutions/Core.TimerJobs.Samples)
    
