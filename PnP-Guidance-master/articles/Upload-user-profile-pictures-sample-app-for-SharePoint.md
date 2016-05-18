
# 上载用户配置文件图片示例 SharePoint 外接程序
您可以使用提供程序托管的外接程序来从文件共享或 SharePoint Online URL 执行用户配置文件数据的批量上载。

 **上次修改时间：** 2015年8月7日

 _ **适用范围：** SharePoint 2013?| SharePoint Add-ins?| SharePoint Online_

 **注释**  名称"SharePoint 相关应用程序"将更改为"SharePoint 外接程序"。在转换期间，某些 SharePoint 产品和 Visual Studio 工具的文档和 UI 仍可能使用术语"SharePoint 相关应用程序"。有关详细信息，请参阅 [Office 和 SharePoint 相关应用程序的新名称](05b07b04-6c8b-4b7e-bd86-e32c589dfead.md#bk_newname)。

[Core.ProfilePictureUploader](https://github.com/OfficeDev/PnP/tree/dev/Samples/Core.ProfilePictureUploader) 示例外接程序显示如何从文件共享或 SharePoint Online URL 执行用户配置文件数据的批量上载，以及如何将用户配置文件属性链接到已上载的图像。
使用此示例了解如何：

- 将用户的配置文件图片从本地 SharePoint Server 2013 迁移到 SharePoint Online。
    
- 修复 Azure Active Directory 同步工具（目录同步）无法将用户的配置文件图片同步到 SharePoint Online 时所发生的问题。
    
- 替换 SharePoint Online 中质量较差的用户配置文件图片。
    
本示例使用控制台应用程序执行以下操作：

- 从用户映射文件读取用户名和图像文件路径或 URL。
    
- 提取一个或三个图像并将其上载到"我的网站宿主"上的图片库。 
    
- 设置用户配置文件属性，以将已上载的图像链接到用户的配置文件。
    
- 更新其他（可选）的用户配置文件属性。
    
 **为改进此内容做贡献**
您可以获取最新的更新，或为改进 [GitHub 上的此文章](https://github.com/OfficeDev/PnP-Guidance/blob/master/articles/Upload-user-profile-pictures-sample-app-for-SharePoint.md)做贡献。您还可以为改进本示例以及 [GitHub 上的其他示例](https://github.com/OfficeDev/PnP)做贡献。有关示例的完整列表，请参阅 [模式和做法开发人员中心](http://dev.office.com/patterns-and-practices)。我们欢迎您做出 [贡献](https://github.com/OfficeDev/PnP/wiki/contributing-to-Office-365-developer-patterns-and-practices)。 

## 开始之前

若要开始，请从 GitHub 上的 [Office 365 开发人员模式和做法](https://github.com/OfficeDev/PnP/tree/dev)项目下载 [Core.ProfilePictureUploader](https://github.com/OfficeDev/PnP/tree/dev/Samples/Core.ProfilePictureUploader) 示例外接程序。

运行此代码示例之前：


- 存储想要上载到文件共享或 Web 服务器上的 SharePoint Online 的用户图像。 
    
- 编辑 userlist.csv 文件以包括以下内容：
    
      - 包含值"UserPrincipalName"、"SourceURL"的标题行。
    
  - 针对每个用户，添加一个新行，其中包含用户组织帐户（或用户主体名称）和文件路径或要上载的图像的 URL。 
    
- 若要从 Visual Studio 运行本示例，请使用以下命令行参数配置  **Core.ProfilePictureUploader** 项目： `–SPOAdmin Username –SPOAdminPassword Password –Configuration filepath`
    
    其中：
    
      -  _Username_ 是您的 Office 365 管理员的用户名。
    
  -  _Password_ 是您的 Office 365 管理员的密码。
    
  -  _Filepath_ 是 configuration.xml 文件的文件路径。
    
- 若要在 Core.ProfilePictureUploader 项目上设置命令行参数，请执行以下操作：
    
      - 在"解决方案资源管理器"中，打开"Core.ProfilePictureUploader"项目 > "属性"的快捷菜单（右键单击）。
    
  - 选择"调试"。
    
  - 在"命令行参数"中，输入前面列出的命令行参数。
    
- 若要配置上载过程以满足您的要求，请输入以下值来编辑 configuration.xml 文件：
    
      -  **tenantName** 元素中 Office 365 租户的名称。例如： `<tenantName>contoso.onmicrosoft.com</tenantName>`
    
  -  **pictureSourceCsv** 元素中用户的映射文件路径。例如： `<pictureSourceCsv>C:\temp\userlist.csv</pictureSourceCsv>`
    
  - 使用  **thumbs** 元素的图像上载说明。编辑 thumbs 元素中的以下属性：
    
      -  **aupload3Thumbs** – 如果您想要为每个用户上载三个图像，请将其设置为 **true**，如果您只想上载一个图像，请将其设置为  **false**。
    
  -  **createSMLThumbs** – 如果想创建源图像的三个不同大小的图像（小、中和大），请将其设置为 **true**，或者如果您想上载三个相同大小的图像，请将其设置为  **false**。
    
  - 使用  **additionalProfilePropties** 元素在用户配置文件上设置的其他属性。例如，以下 XML 指定当代码示例运行时应在用户配置文件上设置为零的名为 **SPS-PictureExchangeSyncState** 的其他用户配置文件属性。
    
  ```
  <additionalProfileProperties>
     <property name="SPS-PictureExchangeSyncState" value="0"/>
</additionalProfileProperties>
  ```

  -  **logfile** 元素中的日志文件路径，如以下示例中所示。 `<logFile path="C:\temp\log.txt" enableLogging="true" loggingLevel="verbose" />`
    
  - 使用  **uploadDelay** 元素上载不同图像文件之间的上载延迟，以毫秒为单位。 **uploadDelay** 的推荐设置为 500 毫秒。
    
- 在 App.config 文件中，更改  **ProfilePictureUploader_UPSvc_UserProfileService** 设置的 **value** 元素，以在您的 SharePoint Online 管理中心中包含对用户配置文件服务的引用，如以下示例中所示。
    
  ```XML
  
<Contoso.Core.ProfilePictureUploader.Properties.Settings>
      <setting name="ProfilePictureUploader_UPSvc_UserProfileService"
            serializeAs="String">
            <value>https://contoso-admin.sharepoint.com/_vti_bin/userprofileservice.asmx</value>
      </setting>
 </Contoso.Core.ProfilePictureUploader.Properties.Settings>

  ```


 **重要信息**  连接到 SharePoint Online 管理中心上的 userprofileservice.asmx Web 服务能使您更新其他用户的用户配置文件属性。在运行本代码示例时，请使用具有管理用户配置文件权限的 Office 365 管理员帐户。 




## 使用 Core.ProfilePictureUploader 外接程序

本代码示例作为控制台应用程序运行。当代码示例运行时，Program.cs 中的  **Main** 方法执行以下操作：


- 使用  **SetupArguments** 和 **InitializeConfiguration** 初始化控制台应用程序。
    
- 调用  **InitializeWebService** 以连接到SharePoint Online 中的用户配置文件服务。
    
- 循环访问 userlist.csv 文件，以读取用户的用户主体名称 (UPN) 和用户图像文件的位置。 
    
- 使用  **GetImagefromHTTPUrl** 中的 **WebRequest** 和 **WebResponse** 对象提取用户的图像。
    
- 调用  **UploadImageToSpo** 以将用户图像上载到 SharePoint Online。
    
- 调用  **SetMultipleProfileProperties** 以设置用户的 **PictureURL** 和 **SPS-PicturePlaceholderState** 用户配置文件属性。
    
- 调用  **SetAdditionalProfileProperties**，以在上载图像文件后设置用户配置文件的其他属性。 
    

 **注释**  本文中的代码按原样提供，不提供任何明示或暗示的担保，包括对特定用途适用性、适销性或不侵权的默示担保。


```C#
static void Main(string[] args)
        {
            int count = 0;

            if (SetupArguments(args)) // Checks if args passed are valid 
            {

                if (InitializeConfiguration()) // Check that the configuration file is valid
                {
                    if (InitializeWebService()) // Initialize the web service end point for the SharePoint Online user profile service.
                    {
                        
                        using (StreamReader readFile = new StreamReader(_appConfig.PictureSourceCsv))
                        {
                            string line;
                            string[] row;
                            string sPoUserProfileName;
                            string sourcePictureUrl;

                            while ((line = readFile.ReadLine()) != null)
                            {
                                if (count > 0)
                                {
                                    row = line.Split(',');
                                    sPoUserProfileName = row[0]; 
                                    sourcePictureUrl = row[1]; 

                                    LogMessage("Begin processing for user " + sPoUserProfileName, LogLevel.Warning);

                                    // Get source picture from source image path.
                                    using (MemoryStream picturefromExchange = GetImagefromHTTPUrl(sourcePictureUrl))
                                    {
                                        if (picturefromExchange != null) // if we got image, upload to SharePoint Online
                                        {
                                            // Create SharePoint naming convention for image file.
                                            string newImageNamePrefix = sPoUserProfileName.Replace("@", "_").Replace(".", "_");
                                            // Upload source image to SharePoint Online.
                                            string spoImageUrl = UploadImageToSpo(newImageNamePrefix, picturefromExchange);
                                            if (spoImageUrl.Length > 0)// If upload worked.
                                            {
                                                string[] profilePropertyNamesToSet = new string[] { "PictureURL", "SPS-PicturePlaceholderState" };
                                                string[] profilePropertyValuesToSet = new string[] { spoImageUrl, "0" };
                                                // Set these two required user profile properties - path to uploaded image, and pictureplaceholder state.
                                                SetMultipleProfileProperties(_sPOProfilePrefix + sPoUserProfileName, profilePropertyNamesToSet, profilePropertyValuesToSet);
                                                // Set additional user profile properties based on your requirements.
                                                SetAdditionalProfileProperties(_sPOProfilePrefix + sPoUserProfileName);
                                            }
                                        }
                                    }

                                    LogMessage("End processing for user " + sPoUserProfileName, LogLevel.Warning);

                                    int sleepTime = _appConfig.UploadDelay;
                                    System.Threading.Thread.Sleep(sleepTime); // A pause between uploads is recommended. 
                                }
                                count++;
                            }
                        }
                    }
                }
            }


            LogMessage("Processing finished for " + count + " user profiles", LogLevel.Information);
         } 

```

 **InitializeWebService** 连接到 SharePoint Online，并将用户配置文件服务的引用设置为实例变量。本代码示例中的其他方法使用此实例变量来将更新应用到用户配置文件属性。若要管理用户配置文件，本代码示例使用 SharePoint Online 管理中心中的 userprofileservice.asmx Web 服务。




```C#
static bool InitializeWebService()
        {
            try
            {
                string webServiceExt = "_vti_bin/userprofileservice.asmx";
                string adminWebServiceUrl = string.Empty;

                // Append the web service (ASMX) URL onto the admin website URL.
                if (_profileSiteUrl.EndsWith("/"))
                    adminWebServiceUrl = _profileSiteUrl + webServiceExt;
                else
                    adminWebServiceUrl = _profileSiteUrl + "/" + webServiceExt;

                LogMessage("Initializing SPO web service " + adminWebServiceUrl, LogLevel.Information);

                // Get secure password from clear text password.
                SecureString securePassword = GetSecurePassword(_sPoAuthPasword);

                // Set credentials from SharePoint Client API, used later to extract authentication cookie, so can replay to web services.
                SharePointOnlineCredentials onlineCred = new SharePointOnlineCredentials(_sPoAuthUserName, securePassword);

                // Get the authentication cookie by passing the URL of the admin website. 
                string authCookie = onlineCred.GetAuthenticationCookie(new Uri(_profileSiteUrl));

                // Create a CookieContainer to authenticate against the web service. 
                CookieContainer authContainer = new CookieContainer();

                // Put the authenticationCookie string in the container. 
                authContainer.SetCookies(new Uri(_profileSiteUrl), authCookie);

                // Setting up the user profile web service. 
                _userProfileService = new UPSvc.UserProfileService();

                // Assign the correct URL to the admin profile web service. 
                _userProfileService.Url = adminWebServiceUrl;

                // Assign previously created authentication container to admin profile web service. 
                _userProfileService.CookieContainer = authContainer;
               // LogMessage("Finished creating service object for SharePoint Online Web Service " + adminWebServiceUrl, LogLevel.Information);
                return true;
            }
            catch (Exception ex)
            {
                LogMessage("Error initiating connection to profile web service in SPO " + ex.Message, LogLevel.Error);
                return false;

            }

            
        }

```

Program.cs 中的  **Main** 方法调用 **UploadImageToSpo** 来将用户的配置文件图片上载到 SharePoint Online。所有的用户配置文件图片都存储在"我的网站宿主"上的图片库中。 **UploadImageToSpo** 执行以下任务：


- 连接到 SharePoint Online。
    
- 确定要为每个用户上载的图像数量。 
    
      - 如果您配置此应用程序为每个用户上载一个图像，则将图像上载到图片库。 
    
  - 如果您配置此应用程序为每个用户上载三个图像，则应用程序检查配置文件中  **createSMLThumbs** 的值，以确定是否需要三个不同大小的图像。如果需要三个不同大小的图像，则应用程序使用 **ResizeImageSmall** 和 **ResizeImageLarge** 来从源图像创建三个不同大小的图像。然后，此应用程序将调整好的图像上载到图片库。如果不需要三个不同大小的图像，则应用程序将三个相同大小的图像上载到图片库。
    



```C#
static string UploadImageToSpo(string PictureName, Stream ProfilePicture)
        {
            try
            {

                string spPhotoPathTempate = "/User Photos/Profile Pictures/{0}_{1}Thumb.jpg"; // Path template to picture library on the My Site Host.
                string spImageUrl = string.Empty;

                // Create SharePoint Online Client context to My Site Host.
                ClientContext mySiteclientContext = new ClientContext(_mySiteUrl);
                SecureString securePassword = GetSecurePassword(_sPoAuthPasword);
                // Provide authentication credentials using Office 365 authentication.
                mySiteclientContext.Credentials = new SharePointOnlineCredentials(_sPoAuthUserName, securePassword);
                                
                if (!_appConfig.Thumbs.Upload3Thumbs) // Upload a single input image only to picture library, no resizing necessary.
                {
                    spImageUrl = string.Format(spPhotoPathTempate, PictureName, "M");
                    LogMessage("Uploading single image, no resize, to " + spImageUrl, LogLevel.Information);
                    Microsoft.SharePoint.Client.File.SaveBinaryDirect(mySiteclientContext, spImageUrl, ProfilePicture, true);
                }
                else if (_appConfig.Thumbs.Upload3Thumbs &amp;&amp; !_appConfig.Thumbs.CreateSMLThumbs)// Upload three images of the same size. 
                {
                    // The following code is not optimal. Upload the same source image three times with different names.
                    // No resizing of images necessary.
                    LogMessage("Uploading threes image to SPO, no resize", LogLevel.Information);

                    spImageUrl = string.Format(spPhotoPathTempate, PictureName, "M");
                    LogMessage("Uploading medium image to " + spImageUrl, LogLevel.Information);
                    Microsoft.SharePoint.Client.File.SaveBinaryDirect(mySiteclientContext, spImageUrl, ProfilePicture, true);

                    ProfilePicture.Seek(0, SeekOrigin.Begin);
                    spImageUrl = string.Format(spPhotoPathTempate, PictureName, "L");
                    LogMessage("Uploading large image to " + spImageUrl, LogLevel.Information);
                    Microsoft.SharePoint.Client.File.SaveBinaryDirect(mySiteclientContext, spImageUrl, ProfilePicture, true);
                    
                    ProfilePicture.Seek(0, SeekOrigin.Begin);
                    spImageUrl = string.Format(spPhotoPathTempate, PictureName, "S");
                    LogMessage("Uploading small image to " + spImageUrl, LogLevel.Information);
                    Microsoft.SharePoint.Client.File.SaveBinaryDirect(mySiteclientContext, spImageUrl, ProfilePicture, true);

                    
                }
                else if (_appConfig.Thumbs.Upload3Thumbs &amp;&amp; _appConfig.Thumbs.CreateSMLThumbs) //generate 3 different sized images
                {
                    LogMessage("Uploading threes image to SPO, with resizing", LogLevel.Information);
                    // Create three images based on recommended sizes for SharePoint Online.
                    // Create small-sized image.
                    using (Stream smallThumb = ResizeImageSmall(ProfilePicture, _smallThumbWidth))
                    {
                        if (smallThumb != null)
                        {
                            spImageUrl = string.Format(spPhotoPathTempate, PictureName, "S");
                            LogMessage("Uploading small image to " + spImageUrl, LogLevel.Information);
                            Microsoft.SharePoint.Client.File.SaveBinaryDirect(mySiteclientContext, spImageUrl, smallThumb, true);                            
                        }
                    }

                    // Create medium-sized image.
                    using (Stream mediumThumb = ResizeImageSmall(ProfilePicture, _mediumThumbWidth))
                    {
                        if (mediumThumb != null)
                        {
                            spImageUrl = string.Format(spPhotoPathTempate, PictureName, "M");
                            LogMessage("Uploading medium image to " + spImageUrl, LogLevel.Information);
                            Microsoft.SharePoint.Client.File.SaveBinaryDirect(mySiteclientContext, spImageUrl, mediumThumb, true);
                           
                        }
                    }

                    // Create large-sized image. This image is shown when you open the user’s OneDrive for Business. 
                    using (Stream largeThumb = ResizeImageLarge(ProfilePicture, _largeThumbWidth))
                    {
                        if (largeThumb != null)
                        {

                            spImageUrl = string.Format(spPhotoPathTempate, PictureName, "L");
                            LogMessage("Uploading large image to " + spImageUrl, LogLevel.Information);
                            Microsoft.SharePoint.Client.File.SaveBinaryDirect(mySiteclientContext, spImageUrl, largeThumb, true);
                            
                        }
                    }
                  
                   
                }
                // Return URL of the medium-sized image to set properties on the user profile.
                return _mySiteUrl + string.Format(spPhotoPathTempate, PictureName, "M");                
                
            }
            catch (Exception ex)
            {
                LogMessage("User Error: Failed to upload thumbnail picture to SPO for " + PictureName + " " + ex.Message, LogLevel.Error);
                return string.Empty;
            }

        }

```

 **SetMultipleProfileProperties** 在单个方法调用中设置多个用户配置文件属性。在本代码示例中， **SetMultipleProfileProperties** 设置用户的下列用户配置文件属性：


-  **PictureURL** – 在"我的网站宿主"上的图片库中，将其设置为中型上载图像的 URL。
    
-  **SPS-PicturePlaceholderState** – 设置为零，以指示 SharePoint Online 应显示此用户的上载图片。
    



```C#
static void SetMultipleProfileProperties(string UserName, string[] PropertyName, string[] PropertyValue)
        {

            LogMessage("Setting multiple SPO user profile properties for " + UserName, LogLevel.Information);

            try
            {
                int arrayCount = PropertyName.Count();

                UPSvc.PropertyData[] data = new UPSvc.PropertyData[arrayCount];
                for (int x = 0; x < arrayCount; x++)
                {
                    data[x] = new UPSvc.PropertyData();
                    data[x].Name = PropertyName[x];
                    data[x].IsValueChanged = true;
                    data[x].Values = new UPSvc.ValueData[1];
                    data[x].Values[0] = new UPSvc.ValueData();
                    data[x].Values[0].Value = PropertyValue[x];
                }

                _userProfileService.ModifyUserPropertyByAccountName(UserName, data);
                // LogMessage("Finished setting multiple SharePoint Online user profile properties for " + UserName, LogLevel.Information);

            }
            catch (Exception ex)
            {
                LogMessage("User Error: Exception trying to update profile properties for user " + UserName + "\n" + ex.Message, LogLevel.Error);
            }
        }

```

 **SetAdditionalProfileProperties** 设置想要在上载图像文件之后进行更新的任何其他用户配置文件属性。您可以在 configuration.xml 文件中指定要更新的其他属性。




```C#
static void SetAdditionalProfileProperties(string UserName)
        {
            if (_appConfig.AdditionalProfileProperties.Properties == null) // If there are no additional properties to update. 
                return;

            int propsCount = _appConfig.AdditionalProfileProperties.Properties.Count();
            if (propsCount > 0)
            {
                string[] profilePropertyNamesToSet = new string[propsCount];
                string[] profilePropertyValuesToSet = new string[propsCount];
                // Loop through each property in configuration file.
                for (int i = 0; i < propsCount; i++)
                {
                    profilePropertyNamesToSet[i] = _appConfig.AdditionalProfileProperties.Properties[i].Name;
                    profilePropertyValuesToSet[i] = _appConfig.AdditionalProfileProperties.Properties[i].Value;
                }

                // Set all properties in a single call.
                SetMultipleProfileProperties(UserName, profilePropertyNamesToSet, profilePropertyValuesToSet);

            }
        }

```


## 其他资源



- [适用于 SharePoint 2013 和 SharePoint Online 的用户配置文件解决方案](user-profile-solutions-for-sharepoint.md)
    
- [Core.ProfilePictureUploader 外接程序](https://github.com/OfficeDev/PnP/tree/dev/Samples/Core.ProfilePictureUploader)
    
- [UserProfile.Manipulation.CSOM](https://github.com/OfficeDev/PnP/tree/dev/Samples/UserProfile.Manipulation.CSOM)
    
- [Core.ProfileProperty.Migration](https://github.com/OfficeDev/PnP/tree/dev/Samples/Core.ProfileProperty.Migration)
    
- [UserProfile.Manipulation.CSOM.Console](https://github.com/OfficeDev/PnP/tree/dev/Samples/UserProfile.Manipulation.CSOM.Console)
    
