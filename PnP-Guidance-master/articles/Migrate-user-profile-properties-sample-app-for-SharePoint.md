
# 迁移用户配置文件属性示例 SharePoint 外接程序
您可以使用提供程序托管的外接程序迁移并导入 SharePoint 用户配置文件数据。

 **上次修改时间：** 2015年8月7日

 _ **适用范围：** SharePoint 2013?| SharePoint Add-ins?| SharePoint Online_

 **注释**  名称"SharePoint 相关应用程序"将更改为"SharePoint 外接程序"。在转换期间，某些 SharePoint 产品和 Visual Studio 工具的文档和 UI 仍可能使用术语"SharePoint 相关应用程序"。有关详细信息，请参阅 [Office 和 SharePoint 相关应用程序的新名称](05b07b04-6c8b-4b7e-bd86-e32c589dfead.md#bk_newname)。

[Core.ProfileProperty.Migration](https://github.com/OfficeDev/PnP/tree/dev/Samples/Core.ProfileProperty.Migration) 示例外接程序介绍了如何将用户配置文件数据从 SharePoint Server 2010 或 SharePoint Server 2013 迁移到 SharePoint Online 中。
本示例包含两个控制台应用程序。这两个应用程序均使用 userprofileservice.asmx Web 服务将单值和多值用户配置文件数据提取到 XML 文件，并将提取的数据导入 SharePoint Online 中的用户配置文件服务。
如果您需要执行以下操作，请使用此代码示例：

- 提取 SharePoint Server 2010 或 SharePoint Server 2013 中的用户配置文件数据。
    
- 将用户配置文件数据导入 SharePoint Online。
    
 **为改进此内容做贡献**
您可以获取最新的更新，或为改进 [GitHub 上的此文章](https://github.com/OfficeDev/PnP-Guidance/blob/master/articles/Migrate-user-profile-properties-sample-app-for-SharePoint.md)做贡献。您还可以为改进本示例以及 [GitHub 上的其他示例](https://github.com/OfficeDev/PnP)做贡献。有关示例的完整列表，请参阅 [模式和做法开发人员中心](http://dev.office.com/patterns-and-practices)。我们欢迎您做出 [贡献](https://github.com/OfficeDev/PnP/wiki/contributing-to-Office-365-developer-patterns-and-practices)。 

## 开始之前

若要开始，请从 GitHub 上的 [Office 365 开发人员模式和做法](https://github.com/OfficeDev/PnP/tree/dev)项目下载 [Core.ProfileProperty.Migration](https://github.com/OfficeDev/PnP/tree/dev/Samples/Core.ProfileProperty.Migration) 示例外接程序。此代码示例包含两个项目。

对于  **Contoso.ProfileProperty.Migration.Extract** 项目：


- 由于此代码示例使用的是服务器端对象模型，请确保您是在安装了 SharePoint Server 2010 或 SharePoint Server 2013 的服务器上运行项目。
    
- 使用具有 SharePoint 场管理员权限的帐户。
    
- 使用表 1 中所列的配置信息编辑 App.config 文件。
    
- 对于所有用户，确保"工作电子邮件"用户配置文件属性不为空。如果"工作电子邮件"用户配置文件属性为空，提取过程将提前终止。
    
- 此代码示例将从 SharePoint Server 2010 中提取用户配置文件。如果您是从 SharePoint Server 2013 中提取用户配置文件，请执行以下操作：
    
      1. 打开"Contoso.ProfileProperty.Migration.Extract"的快捷菜单（右键单击）>"属性"。
    
  2. 在"应用程序"下的"目标框架"中，选择".NET Framework 4"。
    
  3. 选择"确定"，然后选择"保存"。
    
 **表 1. App.Config 文件的配置设置**


****


|**配置设置名称**|**说明**|**示例**|
|:-----|:-----|:-----|
|**MYSITEHOSTURL**|源 SharePoint Server 2010 或 SharePoint Server 2013 场上的"我的网站"URL。|http://my.contoso.com|
|**PROPERTYSEPERATOR**|用于分隔多值用户配置文件属性中的多个值的字符。|||
|**USERPROFILESSTORE**|用于写入提取的用户配置文件数据的 XML 文件路径。|C:\temp\ProfileData.xml|
|**LOGFILE**|用于写入提取的用户配置文件数据的 XML 文件路径。|C:\temp\Extract.log|
|**ENABLELOGGING**|启用磁盘日志记录。|True。|
|**TESTRUN**|执行测试提取操作，确认 App.Config 中的配置设置是正确的。 |如果您正在执行测试提取操作，请设置  `TESTRUN=true`。测试运行仅会从用户配置文件服务中提取一个用户。如果您要从用户配置文件服务中提取所有用户，请设置  `TESTRUN=false`。 |
对于  **Contoso.ProfileProperty.Migration.Import** 项目


- 确保 Office 365 中存在用户配置文件。 
    
- 确保本地 SharePoint Server 2013 和 Office 365 用户配置文件服务具有相同的用户"工作电子邮件"地址。
    
- 在 App.config 文件中，更改  **Contoso_ProfileProperty_Migration_Import_UPSvc_UserProfileService** 设置的 **value** 元素，加入对 SharePoint Online 管理中心中的用户配置文件服务的引用，如下面的示例中所示。
    
  ```XML
  <applicationSettings>
<Contoso.ProfileProperty.Migration.Import.Properties.Settings>
<setting name="Contoso_ProfileProperty_Migration_Import_UPSvc_UserProfileService" serializeAs="String">
<value>https://contoso-admin.sharepoint.com/_vti_bin/userprofileservice.asmx</value>
</setting>
</Contoso.ProfileProperty.Migration.Import.Properties.Settings>
</applicationSettings>

  ```

- 使用表 2 中所列的配置设置编辑 App.config 文件。
    
 **表 2. App.config 文件配置设置**


****


|**配置设置名称**|**说明**|**示例**|
|:-----|:-----|:-----|
|**tenantName**|这是您的租户名称。 |如果您的租户 URL 为 http://contoso.onmicrosoft.com，则输入 contoso 作为您的租户名称。|
|**PROPERTYSEPERATOR**|用于分隔多值用户配置文件属性中的单独值的字符。|||
|**USERPROFILESSTORE**|用于读取提取的用户配置文件数据的 XML 文件。|C:\temp\ProfileData.xml|
|**LOGFILE**|用于事件日志记录的日志文件。|C:\temp\Extract.log|
|**ENABLELOGGING**|启用磁盘日志记录。|True|
|**SPOAdminUserName**|Office 365 管理员用户名。|不适用。|
|**SPOAdminPassword**|Office 365 管理员密码。|不适用。|

## 使用 Core.ProfileProperty.Migration 外接程序

此代码示例将作为控制台应用程序运行。代码示例运行后，Program.cs 中的  **Main** 函数将执行以下任务：


- 连接到"我的网站"主机，并使用  **UserProfileManager** 连接用户配置文件服务。 **UserProfileManager** 属于 **Microsoft.Office.Server.UserProfiles.dll** 程序集。
    
- 创建一个名为  **pData** 的列表，以存储提取的用户配置文件数据。
    
- 对于用户配置文件服务中的所有用户，执行以下操作：
    
      - 使用  **GetSingleValuedProperty** 将 **WorkEmail** 和 **AboutMe** 用户配置文件属性复制到名为 **userData** 的 **UserProfileData** 对象中。
    
  - 使用  **GetMultiValuedProperty** 将 **SPS-Responsibility** 用户配置文件属性复制到 **userData**。
    
- 使用  **UserProfileCollection.Save** 将 **userData** 序列化为 XML 文件。该 XML 文件保存在您在 App.config 中指定的文件路径下。
    

 **注释**  本文中的代码按原样提供，不提供任何明示或暗示的担保，包括对特定用途适用性、适销性或不侵权的默示担保。


```C#
static void Main(string[] args)
        {
            int userCount = 1;

            try
            {

                if (Convert.ToBoolean(ConfigurationManager.AppSettings["TESTRUN"]))
                {
                    LogMessage(string.Format("******** RUNNING IN TEST RUN MODE **********"), LogLevel.Debug);
                }
                
                LogMessage(string.Format("Connecting to My Site Host: '{0}'...", ConfigurationManager.AppSettings["MYSITEHOSTURL"]), LogLevel.Info);
                using (SPSite mySite = new SPSite(ConfigurationManager.AppSettings["MYSITEHOSTURL"]))
                {
                    LogMessage(string.Format("Connecting to My Site Host: '{0}'...Done!", ConfigurationManager.AppSettings["MYSITEHOSTURL"]), LogLevel.Info);

                    LogMessage(string.Format("getting Service Context..."), LogLevel.Info);
                    SPServiceContext svcContext = SPServiceContext.GetContext(mySite);
                    LogMessage(string.Format("getting Service Context...Done!"), LogLevel.Info);

                    LogMessage(string.Format("Connecting to Profile Manager..."), LogLevel.Info);
                    UserProfileManager profileManager = new UserProfileManager(svcContext);
                    LogMessage(string.Format("Connecting to Profile Manager...Done!"), LogLevel.Info);

                    // Size of the List is set to the number of profiles.
                    List<UserProfileData> pData = new List<UserProfileData>(Convert.ToInt32(profileManager.Count));
                    
                    // Initialize Serialization Class.
                    UserProfileCollection ups = new UserProfileCollection();

                    foreach (UserProfile spUser in profileManager)
                    {
                        // Get Profile Information.
                        LogMessage(string.Format("processing user '{0}' of {1}...", userCount,profileManager.Count),LogLevel.Info);                       
                        UserProfileData userData = new UserProfileData();
                        
                        userData.UserName = GetSingleValuedProperty(spUser, "WorkEmail");
                        
                        if (userData.UserName != string.Empty)
                        {
                            userData.AboutMe = GetSingleValuedProperty(spUser, "AboutMe");
                            userData.AskMeAbout = GetMultiValuedProperty(spUser, "SPS-Responsibility");
                            pData.Add(userData);
                            // Add to Serialization Class List of Profiles.
                            ups.ProfileData = pData;
                        }
                        
                        LogMessage(string.Format("processing user '{0}' of {1}...Done!", userCount++, profileManager.Count), LogLevel.Info);

                        // Only process the first item if we are in test mode.
                        if (Convert.ToBoolean(ConfigurationManager.AppSettings["TESTRUN"]))
                        {
                            break;
                        }

                    }
                    
                    // Serialize profiles to disk.
                    ups.Save();

                }
            }
            catch(Exception ex)
            {
                LogMessage("Exception trying to get profile properties:\n" + ex.Message, LogLevel.Error);
            }
 
```

请注意， **GetSingleValuedProperty** 方法使用 userprofileservice.asmx 检索单值用户配置文件属性。 **GetSingleValuedProperty** 将执行以下操作，如下一个代码示例中所示：


- 使用  **spuser[userProperty]** 获取要提取数据的属性对象。
    
- 如果值不为  **null**，则返回  **UserProfileValueCollection** 中的第一个值。
    



```C#
private static string GetSingleValuedProperty(UserProfile spUser,string userProperty)
        {
            string returnString = string.Empty;
            try
            {
                UserProfileValueCollection propCollection = spUser[userProperty];

                if (propCollection[0] != null)
                {
                    returnString = propCollection[0].ToString();
                }
                else
                {
                    LogMessage(string.Format("User '{0}' does not have a value in property '{1}'", spUser.DisplayName, userProperty), LogLevel.Warning);                       
                }
            }
            catch 
            {
                LogMessage(string.Format("User '{0}' does not have a value in property '{1}'", spUser.DisplayName, userProperty), LogLevel.Warning);                       
            }


            return returnString;
            
        }

```

请注意， **GetMultiValuedProperty** 方法将使用 userprofileservice.asmx 检索多值用户配置文件属性。 **GetMultiValuedProperty** 将执行以下操作，如下一个代码示例中所示：


- 使用  **spuser[userProperty]** 获取要更新的用户配置文件属性对象。
    
- 构建由 App.config 文件中指定的  **PROPERTYSEPARATOR** 分隔的用户配置文件属性值的字符串。
    



```C#
private static string GetMultiValuedProperty(UserProfile spUser, string userProperty)
        {
            StringBuilder sb = new StringBuilder("");
            string seperator = ConfigurationManager.AppSettings["PROPERTYSEPERATOR"];

            string returnString = string.Empty;
            try
            {

                UserProfileValueCollection propCollection = spUser[userProperty];

                if (propCollection.Count > 1)
                {
                    for (int i = 0; i < propCollection.Count; i++)
                    {
                        if (i == propCollection.Count - 1) { seperator = ""; }
                        sb.AppendFormat("{0}{1}", propCollection[i], seperator);
                    }
                }
                else if (propCollection.Count == 1)
                {
                    sb.AppendFormat("{0}", propCollection[0]);
                }

            }
            catch
            {
                LogMessage(string.Format("User '{0}' does not have a value in property '{1}'", spUser.DisplayName, userProperty), LogLevel.Warning);
            }

            return sb.ToString();

        }

```


## 使用 Contoso.ProfileProperty.Migration.Import

此代码示例将作为控制台应用程序运行。代码示例运行后，Program.cs 中的  **Main** 方法将执行以下操作：


- 使用  **InitializeConfiguration** 和 **InitializeWebService** 初始化控制台应用程序。
    
- 反序列化包含提取的用户配置文件数据的 XML 文件。
    
- 对于 XML 文件中的所有用户，执行以下操作：
    
      - 从 XML 文件中提取  **UserName** 属性。
    
  - 使用  **SetSingleMVProfileProperty** 设置用户配置文件上的 **SPS-Responsibility**。 
    
  - 使用  **SetSingleMVProfileProperty** 设置用户配置文件上的 **AboutMe**。
    
 **InitializeWebService** 将连接到 SharePoint Online，并设置用户配置文件服务对实例变量的引用。本代码示例中的另一个方法使用此实例变量将这些值写入用户配置文件属性。若要管理用户配置文件，此代码示例将使用 SharePoint Online 管理中心中的 userprofileservice.asmx Web 服务。




```C#
static bool InitializeWebService()
        {
            try
            {
                string webServiceExt = "_vti_bin/userprofileservice.asmx";
                string adminWebServiceUrl = string.Empty;
                
                if (_profileSiteUrl.EndsWith("/"))
                    adminWebServiceUrl = _profileSiteUrl + webServiceExt;
                else
                    adminWebServiceUrl = _profileSiteUrl + "/" + webServiceExt;

                LogMessage("Initializing SPO web service " + adminWebServiceUrl, LogLevel.Information);

                SecureString securePassword = GetSecurePassword(_sPoAuthPasword);
                SharePointOnlineCredentials onlineCred = new SharePointOnlineCredentials(_sPoAuthUserName, securePassword);

                string authCookie = onlineCred.GetAuthenticationCookie(new Uri(_profileSiteUrl));

                CookieContainer authContainer = new CookieContainer();
                authContainer.SetCookies(new Uri(_profileSiteUrl), authCookie);

                // Setting up the user profile web service.
                _userProfileService = new UPSvc.UserProfileService();
                _userProfileService.Url = adminWebServiceUrl;

                // Assign previously created auth container to admin profile web service. 
                _userProfileService.CookieContainer = authContainer;
                return true;
            }
            catch (Exception ex)
            {
                LogMessage("Error initiating connection to profile web service in SPO " + ex.Message, LogLevel.Error);
                return false;

            }
            
        }

```

通过执行以下步骤， **SetSingleMVProfileProperty** 方法将设置一个多值用户配置文件属性，如 **SPS-Responsibility**：


- 将  **PropertyValue** 拆分为一个名为 **arrs** 的字符串数组，以存储用户配置文件属性值。使用在 App.Config 中指定的 **PROPERTYSEPERATOR** 配置设置拆分字符串。
    
- 将  **arrs** 的值分配给用户配置文件服务上的 **ValueData** 数组。
    
- 在用户配置文件服务上创建一个  **PropertyData** 数组。用户配置文件属性和 **ValueData** 数组的名称将传递给 **PropertyData** 对象上的属性。此数组仅包含一个元素，因为仅会导入一个多值用户配置文件属性。
    
通过使用 SharePoint Online 管理中心中的  **userprofileservice.asmx** Web 服务上的 **ModifyUserPropertyByAccountName** 将数据写入用户配置文件服务。运行此代码示例的用户必须是 Office 365 管理员。




```C#
static void SetSingleMVProfileProperty(string UserName, string PropertyName, string PropertyValue)
        {

            try
            {
                string[] arrs = PropertyValue.Split(ConfigurationManager.AppSettings["PROPERTYSEPERATOR"][0]);
                
               UPSvc.ValueData[] vd = new UPSvc.ValueData[arrs.Count()];
               
               for (int i=0;i<=arrs.Count()-1;i++)
               {
                    vd[i] = new UPSvc.ValueData();
                    vd[i].Value = arrs[i];
                }
               
                UPSvc.PropertyData[] data = new UPSvc.PropertyData[1];
                data[0] = new UPSvc.PropertyData();
                data[0].Name = PropertyName;
                data[0].IsValueChanged = true;
                data[0].Values = vd;
                               
                _userProfileService.ModifyUserPropertyByAccountName(string.Format(@"i:0#.f|membership|{0}", UserName), data);

            }
            catch (Exception ex)
            {
                LogMessage("Exception trying to update profile property " + PropertyName + " for user " + UserName + "\n" + ex.Message, LogLevel.Error);
            }

        }

```

 **SetSingleValuedProperty** 方法将设置单值用户配置文件属性，例如， **AboutMe**。 **SetSingleValuedProperty** 将实现与 **SetSingleMVProfileProperty** 相同的技术，但使用的 **ValueData** 数组仅包含一个元素。




```C#
static void SetSingleProfileProperty(string UserName, string PropertyName, string PropertyValue)
        {

            try
            {
                UPSvc.PropertyData[] data = new UPSvc.PropertyData[1];
                data[0] = new UPSvc.PropertyData();
                data[0].Name = PropertyName;
                data[0].IsValueChanged = true;
                data[0].Values = new UPSvc.ValueData[1];
                data[0].Values[0] = new UPSvc.ValueData();
                data[0].Values[0].Value = PropertyValue;
                _userProfileService.ModifyUserPropertyByAccountName(UserName, data);
            }
            catch (Exception ex)
            {
                LogMessage("Exception trying to update profile property " + PropertyName + " for user " + UserName + "\n" + ex.Message, LogLevel.Error);
            }

        }

```


## 其他资源



- [适用于 SharePoint 2013 和 SharePoint Online 的用户配置文件解决方案](user-profile-solutions-for-sharepoint.md)
    
- [Core.ProfilePictureUploader](https://github.com/OfficeDev/PnP/tree/dev/Samples/Core.ProfilePictureUploader)
    
- [UserProfile.Manipulation.CSOM](https://github.com/OfficeDev/PnP/tree/dev/Samples/UserProfile.Manipulation.CSOM)
    
- [UserProfile.Manipulation.CSOM.Console](https://github.com/OfficeDev/PnP/tree/dev/Samples/UserProfile.Manipulation.CSOM.Console)
    
