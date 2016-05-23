
# PnP 设置引擎和核心库
在较高层次了解远程设置过程，包括进一步了解 OfficeDevPnP.Core 库。

 **上次修改时间：** 2015年10月15日


 **注释**  名称"SharePoint 相关应用程序"将更改为"SharePoint 外接程序"。在转换期间，某些 SharePoint 产品和 Visual Studio 工具的文档和 UI 仍可能使用术语"SharePoint 相关应用程序"。有关详细信息，请参阅 [Office 和 SharePoint 相关应用程序的新名称](05b07b04-6c8b-4b7e-bd86-e32c589dfead.md#bk_newname)。


PnP 设置引擎是设置框架的核心，它本质上是 OfficeDevPnP.Core 库。设置引擎是核心库的一部分，它在执行设置任务的过程中利用核心库扩展。核心库由 SharePoint CSOM/REST 对象模型的多个扩展方法组成，启用了如枚举和获取设置模板、存储并将模板应用到新的和现有的网站等设置任务。它还允许您自动设置任务，并将编码的逻辑引入您的设置例程。


 **注释**  若要查看有关创建和保留，以及应用设置模板的视频演练，请转到 [PnP 设置引擎入门](https://channel9.msdn.com/blogs/OfficeDevPnP/Getting-Started-with-PnP-Provisioning-Engine)。


## PnP 设置引擎

在执行和自动远程设置任务的过程中，PnP 设置引擎是核心库中类的结构化实现。从以 SharePoint 外接程序模型为特征的全新 Microsoft 角度来看，SharePoint Online、SharePoint 2013 和 Office 365 的设置解决方案目前使用客户端对象模型 (CSOM) 和设置框架来设置网站项目。

借助 PnP 设置引擎，您可以构建网站栏、 内容类型、列表定义、组合外观和页面的设计模型。您可以通过指向现有网站设计功能、手动创建设计，或结合使用这两种方法来设计这些功能及更多功能。然后，可以选择将设计保留为可保存和重复使用的设置模板。

您可以使用两种方法中的任意一种将您的网站设计提取为设置模板。可以使用由核心库 (OfficeDevPnP.Core) 提供的用于实现扩展方法的 CSOM/REST 代码，或使用 Windows PowerShell 脚本 (OfficeDevPnP.PowerShell)。


### 使用 Windows PowerShell 脚本提取设置模板

若要将 Windows PowerShell 脚本用于设置引擎，首先必须下载并安装 OfficeDevPnP.PowerShell 命令。GitHub 上的 [OfficeDevPnP.PowerShell 命令](https://github.com/OfficeDev/PnP-PowerShell)存储库提供了运行 Windows PowerShell，包括下载和安装说明以及 Windows PowerShell 命令文档所需的所有操作。


 **注释**  OfficeDevPnP.PowerShell 命令存储库中包含两个版本的 Windows PowerShell 命令 MSI。一个用于本地环境，另一个用于 SharePoint Online。

您可以使用 OfficeDevPnP.PowerShell 命令存储库中的内容构建适用于 SharePoint Online 的 Windows PowerShell 命令库。这些命令使用 CSOM，并针对 SharePoint Online 和 SharePoint 本地环境运行，具体取决于您安装了哪个 MSI 包。

此外，简短的 [第 9 频道](https://channel9.msdn.com/)视频也介绍了 [PnP PowerShell Cmdlet](https://channel9.msdn.com/blogs/OfficeDevPnP/Introduction-to-PnP-PowerShell-Cmdlets)，并对安装 Windows PowerShell 程序包和连接到您的 Office 365 网站展开了讨论。除了安装和连接活动之外，该视频还涵盖了有关将 Windows PowerShell 用于远程设置的其他有价值的信息。


### 使用 CSOM 代码提取设置模板

若要使用 CSOM/REST 代码来提取设置模板，您只需使用 Visual Studio 或其他开发环境来创建开发项目。例如，创建控制台、Windows 应用程序或 SharePoint 外接程序等任何类型的项目。创建开发项目后，必须安装核心库（可用作 NuGet 程序包）。


 **注释**  有关查找和安装核心库 NuGet 程序包的说明，以及有关远程设置示例控制台应用程序的演示，可在 [设置控制台应用程序示例](provisioning-console-application-sample.md)中获取。请注意，核心库有两个版本：一个面向 SharePoint Online、SharePoint 2013 和 Office 365；另一个面向 SharePoint 2013 本地环境。

有关使用 CSOM 的详细说明，请参阅 [设置控制台应用程序示例](provisioning-console-application-sample.md)，一般概述如下：


1. 创建与 Office 365 的连接。
    
2. 创建  **ClientContext** 实例并检索对 **Web** 对象的引用。
    
3. 使用核心库扩展方法  **GetProvisioningTemplate** 提取 **ProvisioningTemplate** 对象。
    
4. 使用模板提供程序和序列化格式化程序将设置模板实例保存到您选择的某个文件位置。
    
     **注释**  由于可以自定义模板提供程序和序列化格式化程序对象，因此，您可以实现所需的任何持久性存储和序列化格式。现成的 PnP 设置引擎提供了对文件系统、SharePoint 和 Azure blob 存储模板提供程序的支持。它还支持 XML 和 JSON 序列化格式化程序。
您可以查看 XML 序列化输出示例并了解 [PnP 设置架构](pnp-provisioning-schema.md)文章中有关 XML 序列化架构的详细信息。还可以从 GitHub 上获取架构及其文档： [OfficeDev/PnP-Provisioning-Schema](https://github.com/OfficeDev/Pnp-Provisioning-Schema/)。此外，还可以参阅 [第 9 频道](https://channel9.msdn.com/)的视频： [深入探究 PnP 设置引擎架构](https://channel9.msdn.com/blogs/OfficeDevPnP/Deep-dive-to-PnP-provisioning-engine-schema)，该视频对架构进行了介绍并展开了讨论。

但是，请务必注意，设置引擎支持与序列化格式无关的域模型。实际上，设置引擎已完全脱离任何序列化格式。您需要手动定义  **ProvisioningTemplate** 实例。方法可以是指向模型站点或撰写验证 PnP 设置架构的 XML 文档，也可以是编写 .NET 代码和构建对象层次结构。您甚至可以结合使用这些方法。您还可以使用模型站点设计设置模板，然后将其保存为 XML 文件，在处理代码中的 **ProvisioningTemplate** 实例时，可以执行某些内存自定义操作。


#### 使用 GetProvisioningTemplate 提取模板的示例代码

[设置控制台应用程序示例](provisioning-console-application-sample.md)文章中演示的示例举例说明了如何使用  **GetProvisioningTemplate** 方法。在示例中，导出设置模板的过程发生在以下代码块中。


```
ProvisioningTemplateCreationInformation ptci = new ProvisioningTemplateCreationInformation(ctx.Web);

// Create FileSystemConnector, so that we can store composed files somewhere temporarily 
ptci.FileConnector = new FileSystemConnector(@"c:\temp\pnpprovisioningdemo", "");
ptci.PersistComposedLookFiles = true;
ptci.ProgressDelegate = delegate (String message, Int32 progress, Int32 total)
{

// Use this to simply output progress to the console application UI
Console.WriteLine("{0:00}/{1:00} - {2}", progress, total, message);
};

// Execute actual extraction of the tepmplate
ProvisioningTemplate template = ctx.Web.GetProvisioningTemplate(ptci);

```


### 应用设置模板

与提取设置模板类似，您可以使用 CSOM/REST 代码或 Windows PowerShell 命令来应用自定义  **ProvisioningTemplate** 对象实例。可以从 GitHub 上的存储库下载 [OfficeDevPnP.PowerShell 命令](https://github.com/OfficeDev/PnP-PowerShell)。

如果使用核心库的扩展方法来应用设置模板，您将拥有一个类似于此处示例的代码块。在示例中，通过使用  **Web** 类型的 **ApplyProvisioningTemplate** 扩展方法将该模板应用到目标站点。




```
using (var context = new ClientContext(destinationUrl))
{
  context.Credentials = new SharePointOnlineCredentials(userName, password);
  Web web = context.Web;
  context.Load(web, w => w.Title);
  context.ExecuteQueryRetry();

  // Configure the XML file system provider
  XMLTemplateProvider provider =
  new XMLFileSystemTemplateProvider(
    String.Format(@"{0}\..\..\",
    AppDomain.CurrentDomain.BaseDirectory),
    "");

  // Load the template from the XML stored copy
  ProvisioningTemplate template = provider.GetTemplate(
    "<template name>.xml");

  // Apply the template to another site
  Console.WriteLine("Start: {0:hh.mm.ss}", DateTime.Now);

  // We can also use Apply-SPOProvisioningTemplate
  web.ApplyProvisioningTemplate(template);

  Console.WriteLine("End: {0:hh.mm.ss}", DateTime.Now);
}
```

使用 Windows PowerShell 命令创建到 SharePoint Online 的连接后，就可以使用如下所示的  **Apply-SPOProvisioningTemple** cmdlet。




```
Apply-SPOProvisioningTemplate -Path "PnP-Provisioning-File.xml"
```

请注意， `-Path` 参数指到您保留至文件系统的设置模板文件的文件路径。在上面的代码块中，保留的文件是一个 XML 文件，您可以采用您想要的任何序列化格式保留这些模板文件。


## PnP 核心库

核心库 (OfficeDevPnP.Core) 是一个 CSOM/REST 对象模型，它支持 PnP 设置框架并启用 PnP 设置引擎。它包含多个命名空间，其中包括一组 [扩展方法](https://msdn.microsoft.com/zh-cn/library/bb383977.aspx)。这些方法将扩展 SharePoint 对象模型，以支持远程设置，并支持用于处理实体、计时器作业、设置模板的对象以及整个设置框架。表 1 列出了核心库中的命名空间。

 **表 1. OfficeDevPnP.Core 库中的命名空间**



|**命名空间**|**说明**|
|:-----|:-----|
|OfficeDevPnP.Core||
|OfficeDevPnP.Core.AppModelExtensions|使用其他方法扩展现有类型的 .NET 类。|
|OfficeDevPnP.Core.Entities|用于提供和从 AppModelExtensions 中的扩展方法检索更加复杂的对象的类。|
|OfficeDevPnP.Core.Enums|一组支持设置操作的枚举。|
|OfficeDevPnP.Core.Extensions|不与 SharePoint 关联的扩展方法，如帮助执行字符串操作的方法。|
|OfficeDevPnP.Core.Framework.ObjectHandlers.TokenDefinitions|用于替换模板对象中特定于网站的内容的标记。|
|OfficeDevPnP.Core.Framework.Provisioning.Connectors|用于连接存储了模板和资产的不同数据源的连接器。|
|OfficeDevPnP.Core.Framework.Provisioning.Extensibility|扩展性提供程序代码。扩展性提供程序可用于添加引擎本身不支持的功能。|
|OfficeDevPnP.Core.Framework.Provisioning.Model|模板数据模型对象。可对模板执行提取操作或将其反序列化为实际 C# 代码。此命名空间包含用于此结构的类。|
|OfficeDevPnP.Core.Framework.Provisioning.ObjectHandlers|支持模板提取和创建的不同 SharePoint 元素的处理程序。|
|OfficeDevPnP.Core.Framework.Provisioning.Providers|基本模板提供程序的命名空间。模板提供程序用于将基于代码的模型序列化或反序列化为特定的格式。|
|OfficeDevPnP.Core.Framework.Provisioning.Providers.Json|用于将基于代码的模板序列化为 JSON 格式或从 JSON 格式进行反序列化的 JSON 模板提供程序。|
|OfficeDevPnP.Core.Framework.Provisioning.Providers.Xml|用于将基于代码的模板序列化为 XML 格式或从 XML 格式进行反序列化的 XML 模板提供程序。|
|OfficeDevPnP.Core.Framework.Provisioning.Providers.Xml.V201503|v201503 版本的架构自动生成的架构文件。|
|OfficeDevPnP.Core.Framework.Provisioning.Providers.Xml.V201505|v201505 版本的架构自动生成的架构文件。|
|OfficeDevPnP.Core.Framework.Provisioning.Providers.Xml.V201508|v201508 版本的架构自动生成的架构文件。|

## 其他资源



- [设置控制台应用程序示例](provisioning-console-application-sample.md)
    
- [OfficeDevPnP.PowerShell 命令](https://github.com/OfficeDev/PnP-PowerShell)
    
- [设置控制台应用程序示例](provisioning-console-application-sample.md)
    
- [OfficeDev/PnP-Provisioning-Schema](https://github.com/OfficeDev/Pnp-Provisioning-Schema/)
    
- [扩展方法](https://msdn.microsoft.com/zh-cn/library/bb383977.aspx)
    
