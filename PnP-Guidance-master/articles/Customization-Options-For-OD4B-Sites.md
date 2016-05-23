
# 自定义 OneDrive for Business 网站品牌
根据您组织的要求，在 Office 365 中或使用外接程序模型自定义 OneDrive for Business 网站。OneDrive for Business 网站的自定义过程与 SharePoint 本地网站有所不同。

 **上次修改时间：** 2015年8月21日

 _ **适用范围：** Office 365?| SharePoint 2013?| SharePoint Add-ins?| SharePoint Online_

 **注释**  名称"SharePoint 相关应用程序"将更改为"SharePoint 外接程序"。在转换期间，某些 SharePoint 产品和 Visual Studio 工具的文档和 UI 仍可能使用术语"SharePoint 相关应用程序"。有关详细信息，请参阅 [Office 和 SharePoint 相关应用程序的新名称](05b07b04-6c8b-4b7e-bd86-e32c589dfead.md#bk_newname)。

OneDrive for Business 网站为 SharePoint 站点，您可以使用与 SharePoint 相同的方式对其进行自定义。在自定义 OneDrive for Business 网站之前，考虑这样做的短期和长期意义，并应用以下准则：

- 使用 Office 365 主题或 SharePoint 主题设置引擎来自定义 OneDrive for Business 网站的品牌。如果主题设置引擎还不够，您可以使用 CSS 应用自定义设置。 
    
- 避免使用自定义母版页来自定义 OneDrive for Business 网站。这可能会增加长期成本，因为当对 Office 365 应用更新时，您将需要再次应用更改。您可以使用主题设置引擎和 CSS 来满足几乎所有的自定义品牌需求。
    
- 使用嵌入式 JavaScript 来修改或隐藏网站功能。
    
- 使用客户端对象模型 (CSOM) 来控制 OneDrive for Business 网站中的语言或区域设置。 
    
- 不要在 OneDrive for Business 网站中使用内容类型和网站栏。OneDrive for Business 网站用于个人数据和文档。管理策略和元数据更适合应用于工作组网站和协作网站。
    
 **为改进此内容做贡献**
您可以获取最新的更新，或为改进 [GitHub 上的此文章](https://github.com/OfficeDev/PnP-Guidance/blob/master/articles/Customization-Options-For-OD4B-Sites.md)做贡献。您还可以为改进本示例以及 [GitHub 上的其他示例](https://github.com/OfficeDev/PnP)做贡献。我们欢迎您做出 [贡献](https://github.com/OfficeDev/PnP/wiki/contributing-to-Office-365-developer-patterns-and-practices)。 

## 自定义 OneDrive for Business 网站的挑战

OneDrive for Business 网站的体系结构与 SharePoint 2010 中的个人网站或我的网站相同。每个 OneDrive for Business 网站都是自己的网站集，这意味着没有一个集中位置可以应用品牌或其他自定义设置。

在 SharePoint 内部部署中，配置 OneDrive for Business 网站（包括我的网站或个人网站）涉及服务器场级别的功能。您将您的解决方案部署到 SharePoint 服务器场，并在每次创建我的网站时使用功能框架激活自定义功能。此方法在 Office 365 中不适用，因为它需要服务器场解决方案。 


## OneDrive for Business 网站自定义选项

您可以使用以下各项自定义 Office 365 中的 OneDrive for Business 网站： 




- Office 365 套件级别的设置（Office 365 主题和其他设置）。
    
-  具有用户上下文的隐藏外接程序部件。
    
-  预先创建和应用的配置。
    
- 基于用户配置文件更新的远程计时器作业。
    
    
    
每个选项都有优点和缺点。手动自定义是另一个选项，但如果您有大量的网站，这种方法并不实用。




### Office 365 套件级别的设置

Office 365 包括不基于 SharePoint 体系结构的其他服务。这意味着 Office 365 中的企业品牌和配置超出了 SharePoint 网站的整体最终用户体验范围，其涉及在不同服务之间提供一致的配置。

品牌是 Office 365 企业要求的一个示例。您可以使用 Office 365 中的主题设计来控制品牌。 

图 1 显示了可用于 Office 365 主题的设置。这些主题在所有 Office 365 服务中均适用。


**图 1. Office 365 主题**

![Office 365 主题设置的屏幕截图。](media/58bf8e9f-5c41-49aa-bf36-0e44eba229c2.png)默认情况下，Office 365 主题设置控制 OneDrive for Business 网站套件栏。您可以将此选项与其他选项一起使用，在 OneDrive for Business 网站之间提供正确的品牌元素。 


    
 **注释**  当您使用 Office 365 管理员工具更改 Office 365 主题设置时，可能需要一段时间才能将设置应用到 OneDrive for Business 网站。




### 具有用户上下文的隐藏外接程序部件

可以使用集中登录页面作为自定义过程的起始点。使用此方法，当用户启动浏览器时，将打开一个位置，如公司 Intranet 首页。这是中型和大型企业的典型行为，其中公司登录页面由 Active Directory 组策略设置控制。最终用户不能覆盖默认的欢迎页面。

集中登录页面上的一个隐藏外接程序部件启动了自动化过程。该隐藏外接程序部件也可负责 OneDrive for Business 网站创建，因为用户必须在网站创建过程启动之前访问 OneDrive for Business 网站。该隐藏外接程序部件还在 Microsoft Azure 中托管了提供程序托管的外接程序中的一个页面。该页面将启动自定义过程。

使用隐藏外接程序部件的过程如下：


1.  隐藏外接程序部件放置在最终用户访问的集中网站中 - 通常为公司 Intranet 首页。
    
2.  外接程序部件托管了应用程序托管的外接程序中的一个页面。服务器端代码将向 Azure 存储队列添加元数据，以启动自定义过程。这意味着该页面将接收自定义请求，但不应用任何更改，以尽量缩短处理时间。
    
3. Azure 存储队列接收发送到队列的邮件以进行处理。自定义过程以异步方式进行处理。这样，用户在 Intranet 页面上停留多长时间就没有影响。如果自定义过程是同步的，则用户需使浏览器保持打开到 Intranet 页面的状态，直至自定义过程完成，这并非最佳体验。 
    
4.  在存储队列中放置新项目时将调用 WebJob。此 WebJob 将接收排队邮件中的参数和元数据以访问网站集。WebJob 使用仅外接程序令牌，并且有权在租户级别操控网站集。
    
5.  当用户访问 Intranet 首页启动自定义过程时，自定义项将分别应用到用户的网站。
    
此过程是确保 OneDrive for Business 网站已自定义的可靠方式。您可以在此过程中增加自定义项版本控制，将更新应用到 OneDrive for Business 网站。此选项要求您为用户提供集中登录页面。


### 预先创建和应用的配置

您可以在用户访问 OneDrive for Business 网站之前，使用一个新的 API 预先进行创建，该 API 可以通过 CSOM 或 REST 分批为特定用户创建 OneDrive for Business 网站。您可以使用 Windows PowerShell 脚本启动代码或编写调用远程 API 的代码。 


**图 2. 预先创建的配置**

![显示管理员预创建 SharePoint 网站的图像](media/6aa97df3-e536-407b-b2e3-27b87501d866.png)这包括以下过程：



1. 作为脚本过程的一部分，管理员使用远程创建 API 为用户创建 OneDrive for Business 网站，并将自定义项应用到网站。
    
2.  已为特定用户创建 OneDrive for Business 网站并将其与用户配置文件相关联
    
此过程需要您手动管理新用户和更新，这比使用隐藏外接程序部件方法的工作量要大。如果您从其他文件共享解决方案迁移到 OneDrive for Business，并且不希望用户访问网站并启动网站创建过程，此选项非常有用。


### 基于用户配置文件更新的远程计时器作业

您可以扫描用户配置文件以识别为其创建 OneDrive for Business 网站的用户，然后根据需要将更改应用到网站。这涉及用于定期检查状态和应用自定义项、在 SharePoint 外部运行的已安排作业。已安排作业可以作为 Azure 中的 WebJob 或作为 Windows PowerShell 脚本运行。 


**图 3. 已安排的远程计时器工作**

![显示计划的远程计时器作业流程的图像](media/6200659d-896a-40b1-8245-bf8b7af7cd9f.png)这包括以下过程： 



1. 已计划任务可以访问已启动 OneDrive for Business 网站设置的用户的用户配置文件。
    
2. 网站将根据业务需求逐一进行自定义。
    
选择计划选项之前，考虑部署规模。此选项的一个缺点是用户可以在应用自定义项之前访问 OneDrive for Business 网站。您还可以使用此选项确保用户没有更改任何必需设置，或者确保 OneDrive for Business 网站内容符合公司策略。


## 其他资源


- [SharePoint 网站品牌和页面自定义解决方案](SharePoint-site-branding-and-page-customization-solutions.md)
    
- [使用外接程序模型自定义 OneDrive for Business 网站](http://blogs.msdn.com/b/vesku/archive/2015/01/01/customizing-onedrive-for-business-sites-with-app-model.aspx)
    
- [OneDrive 设置](https://github.com/OfficeDev/PnP/tree/master/Samples/Provisioning.OneDriveProvisioning)
    
