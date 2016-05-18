
# SharePoint 开发和设计工具及实践
使用 SharePoint 设计和开发工具将品牌打造应用到 SharePoint 网站。

 **上次修改时间：** 2015年8月11日

 _ **适用范围：** SharePoint 2013?| SharePoint Add-ins?| SharePoint Online_

 **注释**  名称"SharePoint 相关应用程序"将更改为"SharePoint 外接程序"。在转换期间，某些 SharePoint 产品和 Visual Studio 工具的文档和 UI 仍可能使用术语"SharePoint 相关应用程序"。有关详细信息，请参阅 [Office 和 SharePoint 相关应用程序的新名称](05b07b04-6c8b-4b7e-bd86-e32c589dfead.md#bk_newname)。

本文提供了有关在 SharePoint 中可用的开发和设计选项的信息。您还可以找到有关如何使用远程配置模式将品牌资产应用到 SharePoint 网站的信息。

## 关键的 SharePoint 开发和设计术语和概念





|**术语或概念**|**定义**|**详细信息**|
|:-----|:-----|:-----|
|设计管理器|在启用了发布功能的 SharePoint 发布网站或团队网站中激活的一种功能，可将它用于导入和管理网站品牌资产并将它们导出到一个设计包中。|使用设计管理器将在其他工具（如 Adobe Photoshop 或 Adobe Dreamweaver）中创建的品牌资产导入到 SharePoint 中。SharePoint Designer 设计不适用于未启用发布功能的 OneDrive for Business 或 SharePoint 团队网站。|
|设计包|专为使用 SharePoint 2013 发布网站而设计，包含存储在设计管理器中的品牌资产。|[SharePoint 2013 设计管理器设计包](http://msdn.microsoft.com/library/85ad1993-4d75-4806-9097-b934865a899a%28Office.15%29.aspx)|
|远程设置|在提供程序托管的外接程序中，通过使用运行在 SharePoint 之外的模板和代码来设置网站的模型。|[SharePoint 2013 中的网站设置技术和远程设置](http://blogs.msdn.com/b/vesku/archive/2013/08/23/site-provisioning-techniques-and-remote-provisioning-in-sharepoint-2013.aspx)[使用 SharePoint 2013 外接程序的自助服务网站设置](http://blogs.msdn.com/b/richard_dizeregas_blog/archive/2013/04/04/self-service-site-provisioning-using-apps-for-sharepoint-2013.aspx)|
|根网站|网站集内的第一个网站。根网站有时也称为"Web 应用程序根"。||
|沙盒解决方案|包含程序集、其他非编译组件和 XML 清单文件的 .wsp 文件。沙盒解决方案使用部分信任代码。|[沙盒解决方案](https://msdn.microsoft.com/zh-cn/library/ff798382.aspx)|
|SharePoint Designer 2013|一个用于管理 SharePoint 中品牌元素的 HTML 设计器和设计资产管理工具。在 SharePoint 2013 中，SharePoint Designer主要支持自定义工作流。|[SharePoint Designer 2013 中的更改内容](https://msdn.microsoft.com/zh-cn/library/office/jj728659.aspx)[SharePoint 2013 网站开发的新增内容](https://msdn.microsoft.com/zh-cn/library/office/jj163942.aspx)|
|.wsp 文件|SharePoint 的解决方案文件。.wsp 是一个可对网站资产进行分类并通过 manifest.xml 文件进行整理的 .cab 文件。|[解决方案概述](https://msdn.microsoft.com/zh-cn/library/office/aa543214%28v=office.14%29.aspx)|

## 开发选项

当您使用 SharePoint 2013 作为开发平台时，您需要创建可用于开发、测试、生成和部署您内容的环境。有关开发选项的信息，请参阅 [SharePoint Server 2013 应用程序生命周期管理](http://msdn.microsoft.com/library/caaf9a09-2e6a-49e3-a8d6-aaf7f93a842a%28Office.15%29.aspx)。 

 **SharePoint 开发、测试和验收相关选项**



|**选项**|**注意事项**|
|:-----|:-----|
|Team Foundation Server|
<ul xmlns:xlink="http://www.w3.org/1999/xlink" xmlns:mtps="http://msdn2.microsoft.com/mtps" xmlns:mshelp="http://msdn.microsoft.com/mshelp" xmlns:ddue="http://ddue.schemas.microsoft.com/authoring/2003/5" xmlns:msxsl="urn:schemas-microsoft-com:xslt"><li><p>位于 Visual Studio Online 上以方便访问。</p></li><li><p>包括集中的源代码和生命周期管理系统。</p></li></ul>|
|云测试和验收环境|
<ul xmlns:xlink="http://www.w3.org/1999/xlink" xmlns:mtps="http://msdn2.microsoft.com/mtps" xmlns:mshelp="http://msdn.microsoft.com/mshelp" xmlns:ddue="http://ddue.schemas.microsoft.com/authoring/2003/5" xmlns:msxsl="urn:schemas-microsoft-com:xslt"><li><p>使用单独的租户进行验收测试。</p></li><li><p>用于本地测试的单独测试环境。</p></li></ul>|
|本地测试和验收环境|
<ul xmlns:xlink="http://www.w3.org/1999/xlink" xmlns:mtps="http://msdn2.microsoft.com/mtps" xmlns:mshelp="http://msdn.microsoft.com/mshelp" xmlns:ddue="http://ddue.schemas.microsoft.com/authoring/2003/5" xmlns:msxsl="urn:schemas-microsoft-com:xslt"><li><p>用于本地 SharePoint 部署。</p></li><li><p>由本地或 Microsoft Azure 中的客户托管。</p></li></ul>|
在大多数情况下，虽然可以根据您的要求有所不同，但您至少需要以下租户：


-  **开发人员租户**。作为最佳实践，请设置并使用您自己的开发人员网站。这样做可以避免使您的数据与生产环境相混。 
    
-  **集成/测试租户**。使用此网站确保，新的外接程序和功能能够跨多个网站集运行，且针对生产环境中的服务和数据运行。配置环境以包含在预览状态下的功能。（为此，请在您的租户管理控制台中，选择"服务设置"，然后在"更新"设置下选择"首次发布"。）您可以使用 Visual Studio Online 运行自动测试和任何其他持续性集成测试。
    
-  **生产租户**。发布测试、验收和批准的外接程序到此租户。您可以在此租户上创建一个开发人员网站，以开发和测试影响范围较小或产生单独影响的外接程序。一般情况下，要避免混淆您的开发环境和生产环境。
    

## 设计工具

使用标准的 Web 设计和开发工具（如 HTML、图像、CSS 文件和 JavaScript 文件）创建 SharePoint 网站的品牌资产。例如，您可以使用 Adobe Dreamweaver 和 Adobe Photoshop 设计 HTML、CSS、JavaScript 以及图像文件，将它们用于您的标识您的 SharePoint 网站品牌。或者，您可以使用 SharePoint Designer 2013 创建、管理和自定义品牌资产，或创建 Visual Studio 2013 中的自定义解决方案。


### SharePoint 设计包和 .wsp 文件

设计包是由设计管理器创建的并遵循打包设计资产可预测规则的 .wsp 文件。它们实质上是沙盒解决方案。如果您使用另一种工具将品牌资产打包在 .wsp 文件中，您的品牌资产将处于不固定但可预测的状态。

该设计包中包括所有经过自定义的文件。例如，如果您创建一个使用自定义内容类型的网页布局，设计包中包括页面布局、它所使用的自定义内容类型，以及所有自定义网站栏。该设计包中还包括一些与已应用到您的 SharePoint 网站的各种组合外观相关的文件，包括上传到以下位置的文件：


- 网站资产库
    
- 样式库
    
- 母版页样式库
    
如果您在应用自定义品牌之前，已对网站应用了组合外观，则设计包中将包括带有 .themedcss 和 .themedpng 文件扩展名的文件。若要将设计包中的品牌资产应用到 SharePoint 网站，请导出该设计包，并使用远程配置模式应用设计包中的内容。

SharePoint 2013 中包括了可用于配合使用设计包的 API。如果您在使用 SSOM、CSOM 或 JSOM，则您可以使用 [DesignPackage](https://msdn.microsoft.com/zh-cn/library/office/microsoft.sharepoint.client.publishing.designpackage.aspx) 类或 [DesignPackageInfo](https://msdn.microsoft.com/zh-cn/library/office/microsoft.sharepoint.publishing.designpackageinfo.aspx) 类。


#### 使用设计包 CSOM 将设计软件包的内容应用到 SharePoint 网站

以下示例演示了如何在远程配置模式下使用设计包 API 将设计包中的内容应用到 SharePoint 网站。

此代码专为用于发布网站而设计。虽然可以使用设计包 API 将品牌应用到已启用发布功能的团队网站，但是这可能会带来长期支持问题。


 **注释**  本文中的代码按原样提供，不提供任何明示或暗示的担保，包括对特定用途适用性、适销性或不侵权的默示担保。




```
using Microsoft.SharePoint.Client;
using System;
using System.Collections.Generic;
using System.Linq;
using System.Web;
using System.Web.UI;
using System.Web.UI.WebControls;
using Microsoft.SharePoint.Client.Publishing;
namespace ProviderSharePointAppWeb
{
    public partial class Default : System.Web.UI.Page
    {
        protected void Page_PreInit(object sender, EventArgs e)
        {
            Uri redirectUrl;
            switch (SharePointContextProvider.CheckRedirectionStatus(Context, out redirectUrl))
            {
                case RedirectionStatus.Ok:
                    return;
                case RedirectionStatus.ShouldRedirect:
                    Response.Redirect(redirectUrl.AbsoluteUri, endResponse: true);
                    break;
                case RedirectionStatus.CanNotRedirect:
                    Response.Write("An error occurred while processing your request.");
                    Response.End();
                    break;
            }
        }

        protected void Page_Load(object sender, EventArgs e)
        {
            // Use TokenHelper to get the client context and Title property.
            // To access other properties, the add-in might need to request permissions
            // on the host web.
            var spContext = SharePointContextProvider.Current.GetSharePointContext(Context);
            
            // Publishing feature GUID to use the infrastructure for publishing. 
            Guid PublishingFeature = Guid.Parse("f6924d36-2fa8-4f0b-b16d-06b7250180fa");

            // The site-relative URL of the design package to install.
            // This sandbox design package should be uploaded to a document library.
            // For practical purposes, this can be a configuration setting in web.config.
            string fileRelativePath = @"/sites/devsite/brand/Dev.wsp";

            //string fileUrl = @"https://SPXXXXX.com/sites/devsite/brand/Dev.wsp";
            
        
            using (var clientContext = spContext.CreateUserClientContextForSPHost())
            {
                // Load the site context explicitly or while installing the API, the path for
// the package will not be resolved.
                // If the package cannot be found, an exception is thrown. 
                var site = clientContext.Site;
                clientContext.Load(site);
                clientContext.ExecuteQuery();
              
                // Validate whether the Publishing feature is active. 
                if (IsSiteFeatureActivated(clientContext,PublishingFeature))
                {
                    DesignPackageInfo info = new DesignPackageInfo()
                    {
                        PackageGuid = Guid.Empty,
                        MajorVersion = 1,
                        MinorVersion = 1,
                        PackageName = "Dev"
                    };
                    Console.WriteLine("Installing design package ");
                    
                    DesignPackage.Install(clientContext, clientContext.Site, info, fileRelativePath);
                    clientContext.ExecuteQuery();

                    Console.WriteLine("Applying design package");
                    DesignPackage.Apply(clientContext, clientContext.Site, info);
                    clientContext.ExecuteQuery();
                }
            }
        }
        public  bool IsSiteFeatureActivated( ClientContext context, Guid guid)
        {
            var features = context.Site.Features;
            context.Load(features);
            context.ExecuteQuery();

            foreach (var f in features)
            {
                if (f.DefinitionId.Equals(guid))
                    return true;
            }
            return false;
        }
 
    }
}

```


#### 使用 FileCreationInformation 将品牌资产和母版页上载到团队网站

您可以使用 SharePoint 2013 CSOM 功能安装和卸载设计包，并将设计包导出到 SharePoint Online 网站。例如，使用 [SP.Publishing.DesignPackage.install Method (sp.publishing)](http://msdn.microsoft.com/library/26500127-210f-6c52-c0de-cf2894939a91%28Office.15%29.aspx) 将设计包安装到网站上，如以下示例所示。


```
public static void Install(
    	ClientRuntimeContext context,
	    Site site,
	    DesignPackageInfo info,
    	string path
)

```

[DesignPackageInfo](https://msdn.microsoft.com/zh-cn/library/office/microsoft.sharepoint.publishing.designpackageinfo.aspx) 类指定描述要安装的设计包内容的元数据。使用 [卸载](https://msdn.microsoft.com/zh-cn/library/office/microsoft.sharepoint.client.publishing.designpackage.uninstall.aspx)方法卸载网站中的设计包，如以下示例所示。 




```
public static void UnInstall(
	    ClientRuntimeContext context,
	    Site site,
    	DesignPackageInfo info
)

```

如果您需要为启用了发布功能的团队网站标识品牌，或为 SharePoint Online 上的发布网站标识品牌，您可以使用 [ExportEnterprise](https://msdn.microsoft.com/zh-cn/library/office/microsoft.sharepoint.client.publishing.designpackage.exportenterprise.aspx) 方法或 [ExportSmallBusiness](https://msdn.microsoft.com/zh-cn/library/office/microsoft.sharepoint.client.publishing.designpackage.exportsmallbusiness.aspx) 方法将网站模板设计包导出到解决方案库。使用带小型企业网站模板的 **ExportSmallBusiness** 方法，并使用适于所有其他网站模板的 **ExportEnterprise** 方法，如以下示例所示。在该示例中，注意 _packageName_ 是表示设计软件包名称的字符串。




```
public static ClientResult<DesignPackageInfo> ExportEnterprise(
	    ClientRuntimeContext context,
	    Site site,
	    bool includeSearchConfiguration
)

```

当使用此方法时，您可以将搜索配置包含在设计包中，如下一个示例所示。请注意所有的设计包方法均是在网站集的级别上操作。




```
public static ClientResult<DesignPackageInfo> ExportSmallBusiness(
	    ClientRuntimeContext context,
	    Site site,
	    string packageName,
	    bool includeSearchConfiguration
)

```


## SharePoint Online 的设计工具选项

您用于打造 SharePoint Online 网站的品牌所使用的工具取决于您的 SharePoint Online 版本和您要生成的网站类型。例如，小型企业版本包括一个团队网站和一个公共网站。它不包括发布网站。您可以使用 SharePoint Online 中的网站生成器外接程序来自定义公共网站的品牌。

企业版包括一个在根 Web 应用程序的团队网站集，适用于不包括发布的域。该版本不包括公共网站。使用设计管理器来管理 SharePoint Online 企业版中发布网站的 SharePoint 网站品牌元素。


## 其他资源



- [SharePoint 2013 和 SharePoint Online 的品牌打造和网站设置解决方案](branding-and-site-provisioning-solutions-for-sharepoint.md)
    
- [SharePoint Server 2013 应用程序生命周期管理](http://msdn.microsoft.com/library/caaf9a09-2e6a-49e3-a8d6-aaf7f93a842a%28Office.15%29.aspx)
    
- [沙盒解决方案](https://msdn.microsoft.com/zh-cn/library/ff798382.aspx)
    
- [SharePoint 2013 中的网站设置技术和远程设置](http://blogs.msdn.com/b/vesku/archive/2013/08/23/site-provisioning-techniques-and-remote-provisioning-in-sharepoint-2013.aspx)
    
- [SharePoint 2013 设计管理器设计包](http://msdn.microsoft.com/library/85ad1993-4d75-4806-9097-b934865a899a%28Office.15%29.aspx)
    
