
# 在 Office 365 中设置网站集的外部共享
 您可以控制 Office 365 中的 SharePoint 网站集的外部共享设置，允许外部用户（不具备您的 Office 365 订阅中的组织帐户的用户）访问您的网站集。

 **上次修改时间：** 2015年8月7日

 _ **适用范围：** Office 365?| SharePoint Add-ins?| SharePoint Online_

 **注释**  名称"SharePoint 相关应用程序"将更改为"SharePoint 外接程序"。在转换期间，某些 SharePoint 产品和 Visual Studio 工具的文档和 UI 仍可能使用术语"SharePoint 相关应用程序"。有关详细信息，请参阅 [Office 和 SharePoint 相关应用程序的新名称](05b07b04-6c8b-4b7e-bd86-e32c589dfead.md#bk_newname)。

[Core.ExternalSharing](https://github.com/OfficeDev/PnP/tree/dev/Samples/Core.ExternalSharing) 代码示例演示了如何控制 SharePoint 网站集的外部共享设置。使用此解决方案可以：

- 在网站设置过程中控制外部共享设置。
    
- 使网站集准备好与外部用户共享。
    

 **注释**  外部共享设置仅在 Office 365 中可用。

 **为改进此内容做贡献**
您可以获取最新的更新，或为改进 [GitHub 上的此文章](https://github.com/OfficeDev/PnP-Guidance/blob/master/articles/Set-external-sharing-on-site-collections-in-Office-365.md)做贡献。您还可以为改进本示例以及 [GitHub 上的其他示例](https://github.com/OfficeDev/PnP)做贡献。有关示例的完整列表，请参阅 [模式和做法开发人员中心](http://dev.office.com/patterns-and-practices)。我们欢迎您做出 [贡献](https://github.com/OfficeDev/PnP/wiki/contributing-to-Office-365-developer-patterns-and-practices)。 

## 开始之前

若要开始，请从 GitHub 上的 [Office 365 开发人员模式和做法](https://github.com/OfficeDev/PnP/tree/dev)项目下载 [Core.ExternalSharing](https://github.com/OfficeDev/PnP/tree/dev/Samples/Core.ExternalSharing) 示例外接程序。


## 使用 Core.ExternalSharing 外接程序

确认您的 Office 365 订阅允许外部共享。为此，请执行以下操作：


1. 打开您的"Office 365 管理中心"。
    
2. 在左侧导航菜单上，选择"外部共享"。
    
3. 选择"共享概述"。
    
4. 在"网站"中，确保"允许外部人员访问网站"为"开启"状态。
    
验证 SharePoint 网站集的外部网站设置。为此，请执行以下操作：


1. 打开您的"Office 365 管理中心"。
    
2. 在左侧导航菜单上，选择"SharePoint"以打开"SharePoint 管理中心"。
    
3. 选择您要在其上验证外部共享设置的网站集 URL 旁边的复选框。
    
4. 在功能区上，选择"共享"。
    
5. 在"共享"对话框中查看外部共享设置。运行此代码示例后，返回到"共享"对话框以确认外部共享设置已更改。
    
运行此代码示例时，Program.cs 中的  **Main** 将执行以下任务：


- 获取 Office 365 管理中心 URL。
    
- 获取要配置外部共享设置的网站集 URL。
    
- 获取您的 Office 365 管理员凭据。
    
- 调用  **GetInputSharing** ，这将提示用户选择一种外部共享设置 ([SharingCapabilities](https://msdn.microsoft.com/library/office/microsoft.online.sharepoint.tenantmanagement.sharingcapabilities.aspx)) 应用到网站集。外部共享设置选项包括：
    
      -  **Disabled** ，禁用网站的外部共享。
    
  -  **ExternalUserAndGuestSharing** ，启用网站的外部用户和来宾共享。
    
  -  **ExternalUserSharingOnly** ，仅启用外部用户共享。
    
- 调用  **SetSiteSharing** 。
    

 **注释**  本文中的代码按原样提供，不提供任何明示或暗示的担保，包括对特定用途适用性、适销性或不侵权的默示担保。




```C#
 static void Main(string[] args)
        {
           
            /* Prompt for your Office 365 admin center URL*/
            Console.WriteLine("Enter your Tenant Admin URL for your Office 365 subscription:");
            string tenantAdminURL = GetSite();

            /* End Program if no Office 365 admin center URL is supplied*/
            if (string.IsNullOrEmpty(tenantAdminURL))
            {
                Console.WriteLine("Hmm, i tried to work on it but you didn't supply your admin tenant url:");
                return;
            }
               
            // Prompt the user for an Office365 site collection 
            Console.WriteLine("Enter your Office 365 Site Collection URL:");
            string siteUrl = GetSite();

            /* Prompt for Credentials */
            Console.WriteLine("Enter Credentials for your Office 365 Site Collection {0}:", siteUrl);

            string userName = GetUserName();
            SecureString pwd = GetPassword();

            /* End program if no credentials are entered */
            if (string.IsNullOrEmpty(userName) || (pwd == null))
            {
                Console.WriteLine("Hmm, i tried to work on it but you didn't supply your credentials:");
                return;
            }

            try 
            {
                SharingCapabilities _sharingSettingToApply = GetInputSharing(siteUrl);
                using (ClientContext cc = new ClientContext(tenantAdminURL))
                { 
                    cc.AuthenticationMode = ClientAuthenticationMode.Default;
                    cc.Credentials = new SharePointOnlineCredentials(userName, pwd);
                    SetSiteSharing(cc, siteUrl, _sharingSettingToApply);
                }
            }
            catch(Exception ex)
            {
                Console.WriteLine("Oops, Mistakes can happen to anyone. An Error occured : {0}", ex.Message);
               
            }

            Console.WriteLine("Hit Enter to exit.");
            Console.Read();

        
        }
```

 **SetSiteSharing** 将执行以下操作：


-  使用 **Tenant.GetSitePropertiesByUrl** 检索网站集上的 **SiteProperties** 。
    
- 使用  **Tenant.SharingCapability** 确定是否已启用 Office 365 订阅的外部共享。
    
-  如果 Office 365 订阅已启用共享，则将 **SiteProperties.SharingCapability** 设置为用户输入的外部共享设置。
    



```C#
public static void SetSiteSharing(ClientContext adminCC, string siteCollectionURl, SharingCapabilities shareSettings)
        {
            var _tenantAdmin = new Tenant(adminCC);
            SiteProperties _siteprops = _tenantAdmin.GetSitePropertiesByUrl(siteCollectionURl, true);
            adminCC.Load(_tenantAdmin);
            adminCC.Load(_siteprops);
            adminCC.ExecuteQuery();

            SharingCapabilities _tenantSharing = _tenantAdmin.SharingCapability;
            var _currentShareSettings = _siteprops.SharingCapability;
            bool _isUpdatable = false;

            if(_tenantSharing == SharingCapabilities.Disabled)
            {
                Console.WriteLine("Sharing is currently disabled in your tenant! I am unable to work on it.");
            }
            else
            {  
                if(shareSettings == SharingCapabilities.Disabled)
                { _isUpdatable = true; }
                else if(shareSettings == SharingCapabilities.ExternalUserSharingOnly)
                {
                    _isUpdatable = true;   
                }
                else if (shareSettings == SharingCapabilities.ExternalUserAndGuestSharing)
                {
                    if (_tenantSharing == SharingCapabilities.ExternalUserAndGuestSharing)
                    {
                        _isUpdatable = true;
                    }
                    else
                    {
                        Console.WriteLine("ExternalUserAndGuestSharing is currently disabled in your tenant! I am unable to work on it.");
                    }
                }
            }
            if (_currentShareSettings != shareSettings &amp;&amp; _isUpdatable)
            {
                _siteprops.SharingCapability = shareSettings;
                _siteprops.Update();
                adminCC.ExecuteQuery();
                Console.WriteLine("Set Sharing on site {0} to {1}.", siteCollectionURl, shareSettings);
            }
        }
```


## 其他资源



- [Office 365 开发模式和做法解决方案指南](https://msdn.microsoft.com/library/office/dn904529.aspx)
    
- [管理您的 SharePoint Online 环境的外部共享](https://support.office.com/article/Manage-external-sharing-for-your-SharePoint-Online-environment-C8A462EB-0723-4B0B-8D0A-70FEAFE4BE85)
    
- [模式和做法开发人员中心](http://dev.office.com/patterns-and-practices)
    
