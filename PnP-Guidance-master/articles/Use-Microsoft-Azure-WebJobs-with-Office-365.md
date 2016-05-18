﻿
# Use Microsoft Azure WebJobs with Office 365
Use Azure WebJobs to implement timer jobs that can access SharePoint Online.

 **上次修改时间：** 2015年8月7日

 _ **适用范围：** SharePoint 2013?| SharePoint Add-ins?| SharePoint Online_

 **注释**  名称"SharePoint 相关应用程序"将更改为"SharePoint 外接程序"。在转换期间，某些 SharePoint 产品和 Visual Studio 工具的文档和 UI 仍可能使用术语"SharePoint 相关应用程序"。有关详细信息，请参阅 [Office 和 SharePoint 相关应用程序的新名称](05b07b04-6c8b-4b7e-bd86-e32c589dfead.md#bk_newname)。

 **Provided by:** ??
Tobias Zimmergren.
Implement timer job functionality using [Microsoft Azure WebJobs](http://azure.microsoft.com/documentation/articles/websites-webjobs-resources/) or Windows Task Scheduler to perform tasks in SharePoint Online. A timer job is a repetitive, scheduled, background process that runs in SharePoint to perform certain tasks. For example, you may want a timer job to copy data entered in a SharePoint list to a database. In SharePoint Online, you cannot deploy farm solutions, which is how timer jobs were deployed in the past. To implement similar timer job functionality in SharePoint Online, you need to run a console application as an Azure WebJob. The console application accesses SharePoint Online using the client-side object model (CSOM). This article presents the basic concepts involved in deploying console applications as Azure WebJobs to run and access your SharePoint Online sites and content.
 **Contribute to this content**
You can get the latest updates or contribute to this [article on Github](https://github.com/OfficeDev/PnP-Guidance/blob/master/articles/Use-Microsoft-Azure-WebJobs-with-Office-365.md). You can also contribute to this and other [samples on GitHub](https://github.com/OfficeDev/PnP). For a complete list of samples, see the [Patterns and Practices developer center](http://dev.office.com/patterns-and-practices). We welcome your [contributions](https://github.com/OfficeDev/PnP/wiki/contributing-to-Office-365-developer-patterns-and-practices). 

## Create and run a console application as an Azure WebJob

To set up your console application to run as an Azure WebJob, you need to:


1. Create an organization account for the Azure WebJob to use to access your SharePoint sites and content.
    
2. Create and set up the console application.
    
3. Add code to the console application.
    
4. Publish your console application as an Azure WebJob.
    
5. Run and verify your Azure WebJob.
    

## Create an organization account

You need to create an account for the Azure WebJob to use when accessing SharePoint sites and content. For more information, see [Add users individually to Office 365-Admin Help](https://support.office.microsoft.com/article/Add-users-individually-to-Office-365---Admin-Help-1970f7d6-03b5-442f-b385-5880b9c256ec). When the Azure WebJob runs, the  **Modified By** field stores and displays the value entered in **Display name** of the organization account. Ensure you choose a display name that your users can easily identify as the account used by the Azure WebJob for accessing SharePoint.


## Create and set up the console application

To create a console application to run as an Azure WebJob, perform the following steps:


1. Create a new console application project by:
    
      1. In Visual Studio, choose  **New Project** > **Visual C#** > **Console Application** > **OK**.
    
  2. After the console application is created, choose  **Tools** > **NuGet Package Manager** > **Manage NuGet Packages for Solution...** > **Online** > **All**.
    
  3. Search for  **Add-in for SharePoint Web Toolkit**.
    
  4. Choose  **Install**, then choose  **OK**.
    
  5. Choose  **Close**.
    
  6. Verify that SharePointContext.cs and TokenHelper.cs were added to the console application project.
    
2. Save your account information in the app.config by adding the  **appSettings** element shown. Change **SPOAccount** and **SPOPassword** to the user name and password of the organization account you created previously.
    
  ```XML
  <?xml version="1.0" encoding="utf-8" ?>
<configuration>
 <startup> 
   <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5" />
 </startup>
 <appSettings>
   <add key="SPOAccount" value="admin@contoso.onmicrosoft.com"/>
   <add key="SPOPassword" value="Contoso"/>
 </appSettings>
</configuration>

  ```


     >**警告**  App.config stores the organization account's username and password in clear text. This method is used for demonstration purposes only, and should not be used in your production deployment of your Azure WebJobs. We recommend encrypting the password, or authenticating using OAuth with access tokens. For more information, see Kirk Evans blog post on [Building a SharePoint Add-in as a Timer Job](http://blogs.msdn.com/b/kaevans/archive/2014/03/02/building-a-sharepoint-app-as-a-timer-job.aspx).

## Add code to the console application

In Program.cs, add the following code to the console application.


 **注释**  本文中的代码按原样提供，不提供任何明示或暗示的担保，包括对特定用途适用性、适销性或不侵权的默示担保。


1. Add  **using** statements.
    
  ```C#
  using Microsoft.SharePoint.Client;
using System.Security;
using System.Configuration; 
  ```

2. Add the following methods to your class:
    
      -  **Main** signs into your SharePoint site, and then uses the CSOM to perform tasks on your site or content. This code sample uses the CSOM to find a list and output the total number of items that are in the list to the console window. When using Azure WebJobs, you can see the console window output in the WebJob Run Details, which is explained in [Run and verify your Azure WebJob](#run-and-verify-your-azure-webjob).
    
  -  **GetSPOSecureStringPassword** reads your password from app.config.
    
  -  **GetSPOAccountName** reads your username from app.config.
    

  ```C#
   static void Main(string[] args)
        {
            using (ClientContext context = new ClientContext("https://contoso.sharepoint.com"))
            {
                // Use default authentication mode.
                context.AuthenticationMode = ClientAuthenticationMode.Default;                 
                context.Credentials = new SharePointOnlineCredentials(GetSPOAccountName(), GetSPOSecureStringPassword());

                // Add your CSOM code to perform tasks on your sites and content.

                try
                {
                    List objList = context.Web.Lists.GetByTitle("Docs");
                    context.Load(objList);
                    context.ExecuteQuery();

                    if (objList != null &amp;&amp; objList.ItemCount > 0)
                    {
                        Console.WriteLine(objList.Title.ToString() + " has " + objList.ItemCount + " items.");
                    }

                }
                catch (Exception ex)
                {
                    Console.WriteLine("ERROR: " + ex.Message);
                    Console.WriteLine("ERROR: " + ex.Source);
                    Console.WriteLine("ERROR: " + ex.StackTrace);
                    Console.WriteLine("ERROR: " + ex.InnerException);

                }
            }
                
        }

 private static SecureString GetSPOSecureStringPassword()
  {
      try
      {
          Console.WriteLine("Entered GetSPOSecureStringPassword.");
          var secureString = new SecureString();
          foreach (char c in ConfigurationManager.AppSettings["SPOPassword"])
          {
              secureString.AppendChar(c);
          }
          Console.WriteLine("Constructed the secure password.");

          return secureString;
      }
      catch
      {
          throw;
      }
  }

  private static string GetSPOAccountName()
  {
      try
      {
          Console.WriteLine("Entered GetSPOAccountName.");
          return ConfigurationManager.AppSettings ["SPOAccount"];
      }
      catch
      {
          throw;
      }
   }


  ```


## Publish your console application as an Azure WebJob

When you are finished developing your console application, you need to deploy the console application as an Azure WebJob. To deploy your console application as an Azure WebJob, you can:


- Upload your binaries to an Azure web app, and create an Azure WebJob using the [Microsoft Azure Portal](https://portal.azure.com/). The binaries for your Visual Studio project can be found in the bin/Debug or bin/Release folders of your Visual Studio project. To create the Azure web app and upload the binaries, follow the steps in [Create an ASP.NET web app in Azure Add-in Service](http://azure.microsoft.com/documentation/articles/web-sites-dotnet-get-started/). To create your on demand, continuous, or scheduled Azure WebJob, see [Run Background tasks with WebJobs](http://azure.microsoft.com/documentation/articles/web-sites-create-web-jobs/).
    
- Publish your Azure WebJob from Visual Studio. For more information, see [Enable WebJobs deployment without a web project](http://azure.microsoft.com/documentation/articles/websites-dotnet-deploy-webjobs/#convertnolink).
    

## Run and verify your Azure WebJob


After completing all the previous steps, your Azure WebJob should be running and performing tasks in your Office 365 subscription. At times, you may need to perform maintenance or troubleshoot your Azure WebJobs. To verify that your Azure WebJob is running:


- If your Azure WebJob updated a SharePoint item, like a list item, the  **Modified By** field displays the organization account that the Azure WebJob used to access SharePoint.
    
- Review the WebJob Details logs for your Azure WebJob. The WebJob Details logs lets you review when a job ran, the success or failure of a job run, any output from the WebJob (for example, when Console.WriteLine was called), and other details of the job run. For more information, see the section View the job history on [Run Background tasks with WebJobs](http://azure.microsoft.com/documentation/articles/web-sites-create-web-jobs/).
    

## 其他资源



- [Office 365 development patterns and practices solution guidance](https://msdn.microsoft.com/library/dn904529%28v=office.15%29.aspx).
    
- [Create a .NET WebJob in Azure Add-in Service](http://azure.microsoft.com/documentation/articles/websites-dotnet-webjobs-sdk-get-started/).
    
- [Azure WebJobs resources](http://azure.microsoft.com/documentation/articles/websites-webjobs-resources/).
    
- [Patterns and Practices developer center](http://dev.office.com/patterns-and-practices)
    
