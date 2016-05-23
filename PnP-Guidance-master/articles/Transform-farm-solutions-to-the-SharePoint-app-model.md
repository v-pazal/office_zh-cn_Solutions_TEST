
# Transform farm solutions to the SharePoint Add-in model
Transform or convert your 服务器场解决方案 to the SharePoint 外接程序模型. Learn how to convert web parts, page layouts, master pages, timer jobs, and so on to the SharePoint 外接程序模型.

 **上次修改时间：** 2015年8月7日

 _ **适用范围：** SharePoint 2013?| SharePoint Add-ins?| SharePoint Online_

 **注释**  名称"SharePoint 相关应用程序"将更改为"SharePoint 外接程序"。在转换期间，某些 SharePoint 产品和 Visual Studio 工具的文档和 UI 仍可能使用术语"SharePoint 相关应用程序"。有关详细信息，请参阅 [Office 和 SharePoint 相关应用程序的新名称](05b07b04-6c8b-4b7e-bd86-e32c589dfead.md#bk_newname)。

If you have extended your SharePoint environment by using 服务器场解决方案 and you want to migrate your extensions to the SharePoint 外接程序模型 to make your transition to SharePoint Online easier, you need to transform your 服务器场解决方案 to the SharePoint 外接程序模型. Transforming your 服务器场解决方案 to the SharePoint 外接程序模型 involves analyzing your existing extensions, designing and developing your new SharePoint 外接程序 , and then testing and deploying your 外接程序 in your production environment. This article describes the process and best practices to use when you transform your 服务器场解决方案 to the SharePoint 外接程序模型.
 **Contribute to this content**
You can get the latest updates or contribute to this [article on Github](https://github.com/OfficeDev/PnP-Guidance/blob/master/articles/Transform-farm-solutions-to-the-SharePoint-app-model.md). You can also contribute to this and other [samples on GitHub](https://github.com/OfficeDev/PnP). For a complete list of samples, see the [Patterns and Practices developer center](http://dev.office.com/patterns-and-practices). We welcome your [contributions](https://github.com/OfficeDev/PnP/wiki/contributing-to-Office-365-developer-patterns-and-practices). 

## Plan the transformation process

 When you transform your 服务器场解决方案 to the SharePoint 外接程序模型, you want to ensure that the impact on your users is minimal. Carefully analyze your current 服务器场解决方案, and then design your new SharePoint 外接程序 to meet the needs of your organization. We recommend the following process to ensure a successful transformation.


1. Readiness. Learn about:
    
      - The SharePoint 外接程序模型, different kinds of 外接程序, and hosting options. For more information, see [Overview of SharePoint Add-ins](https://msdn.microsoft.com/library/office/fp179930.aspx).
    
  - Remote access technologies for accessing your on-premises data.
    
2. Solution assessment. Analyze the functional and business requirements by: 
    
      - Identifying deployed 服务器场解决方案 in your current environment. Consider using third-party tools to help identify deployed extensions. Perform a detailed analysis of each 服务器场解决方案 identified.
    
  - Reviewing requirements with your users. Consider asking your users to demonstrate how they use the existing 服务器场解决方案 to perform their daily work.
    
  - Identifying, documenting, and designing new functionality to include in the new SharePoint 外接程序 . Consider reviewing your list of new feature requests from your users for additional ideas.
    
  - Identifying unused features, and agreeing with your users to omit this functionality from the new SharePoint 外接程序 . 
    
  - For each 服务器场解决方案, determining whether to replace it with a SharePoint 外接程序 . Some solutions, such as SharePoint administration extensions, cannot be duplicated in the SharePoint 外接程序模型. For more information, see [SharePoint 加载项与 SharePoint 解决方案比较](http://msdn.microsoft.com/library/0e9efadb-aaf2-4c0d-afd5-d6cf25c4e7a8%28Office.15%29.aspx) and [在 SharePoint 加载项和 SharePoint 解决方案之间做出决定](http://msdn.microsoft.com/library/8459e265-b8fd-4bf8-911e-d63cae8bf96f%28Office.15%29.aspx).
    
3.  Solution planning. Design the new application using the SharePoint 外接程序模型 based on:
    
      - The requirements gathered in step 2.
    
  - Your analysis of the existing code. During your code analysis, consider identifying portions of the code that can be dropped (for example, the code is no longer being used, or the requirements have changed).
    
4. Develop and test the SharePoint 外接程序模型 version of your application. This is usually the most time-consuming step in the transformation process. 
    
5. Deploy your new 外接程序. Depending on your requirements, you might decide to keep the 服务器场解决方案 running in parallel to the new SharePoint 外接程序 , or you may retract the 服务器场解决方案 and only allow users to use the new SharePoint 外接程序 . In either scenario, ensure that your deployment is stable, and send appropriate communication to your users. If your content in existing site collections depended on your 服务器场解决方案 (for example, if content was created by using a content type), before you fully retract the 服务器场解决方案, you need to transform your existing content to use your new SharePoint 外接程序模型 solution. Ensure that you allow enough time to complete this task because it can be time consuming and difficult.
    

## Transformation approaches to deploy your new SharePoint Add-in

After you finish development and unit testing of your new SharePoint 外接程序 , start transforming your 服务器场解决方案 to the new SharePoint 外接程序 by using one of the transformation approaches listed in the following table.


|
|
|**Transformation approach**|**Description**|**Advantages**|**Disadvantages**|
|:-----|:-----|:-----|:-----|
|In-place|Deploy your new SharePoint 外接程序 into your existing SharePoint environment. You need to ensure your site is using the new SharePoint 外接程序 before retracting the 服务器场解决方案.|
<ul xmlns:xlink="http://www.w3.org/1999/xlink" xmlns:mtps="http://msdn2.microsoft.com/mtps" xmlns:mshelp="http://msdn.microsoft.com/mshelp" xmlns:ddue="http://ddue.schemas.microsoft.com/authoring/2003/5" xmlns:msxsl="urn:schemas-microsoft-com:xslt"><li><p>Less overall user impact.</p></li><li><p>Less resources needed because you are using your existing SharePoint environment.</p></li><li><p>No need for third-party tools.</p></li><li><p>Minimal site downtime.</p></li><li><p>Upgrade one site collection at a time, rather than upgrading the entire farm all at once.</p></li><li><p>URLs do not change.</p></li></ul>|
<ul xmlns:xlink="http://www.w3.org/1999/xlink" xmlns:mtps="http://msdn2.microsoft.com/mtps" xmlns:mshelp="http://msdn.microsoft.com/mshelp" xmlns:ddue="http://ddue.schemas.microsoft.com/authoring/2003/5" xmlns:msxsl="urn:schemas-microsoft-com:xslt"><li><p>Difficult to track completion progress of all affected assets on a site.</p></li><li><p>Increased chance of creating orphans. When an asset points to a file on the file system which does not exist, this is referred to as an orphan.</p></li></ul>|
|Swing or content migration| Extract your content from your existing site collections where your 服务器场解决方案 are currently deployed, and deploy the content in a new site collection which uses the new SharePoint 外接程序 . When you migrate content to SharePoint Online, this process is normally used.|
<ul xmlns:xlink="http://www.w3.org/1999/xlink" xmlns:mtps="http://msdn2.microsoft.com/mtps" xmlns:mshelp="http://msdn.microsoft.com/mshelp" xmlns:ddue="http://ddue.schemas.microsoft.com/authoring/2003/5" xmlns:msxsl="urn:schemas-microsoft-com:xslt"><li><p>Clean SharePoint environment with no previous 服务器场解决方案 dependencies.</p></li><li><p>The new site collection is isolated from your production environment. Release the updated site collection when ready.
</p></li></ul>|
<ul xmlns:xlink="http://www.w3.org/1999/xlink" xmlns:mtps="http://msdn2.microsoft.com/mtps" xmlns:mshelp="http://msdn.microsoft.com/mshelp" xmlns:ddue="http://ddue.schemas.microsoft.com/authoring/2003/5" xmlns:msxsl="urn:schemas-microsoft-com:xslt"><li><p>Requires third-party tools to help with the content migration.</p></li><li><p>Requires an additional SharePoint environment.</p></li><li><p>Site downtime required.</p></li><li><p>URLs might change if you keep both sites running in parallel for a period of time.</p></li></ul>|

## Best practices for specific farm solutions

Apply the following best practices when transforming specific solutions:


- Page layouts and master pages. Custom page layouts and master pages might exist in publishing sites, or team sites with the publishing features turned on. To replace page layouts and master pages:
    
      1. Upload the new page layout or master page to your site. Upload new master pages and page layouts to your site collection either manually or by using remote APIs. Remote APIs include the client-side object model (CSOM) or REST. This ensures that the master pages and page layouts do not have dependencies on a 服务器场解决方案. 
    
  2. Configure your site to use the new page layouts and master pages.
    
  3. Retract the previous version of the page layouts and master pages.
    
- Web parts and controls. To replace web parts and controls:
    
      1. Scan all your existing pages to determine which pages have web parts.
    
  2. (Optional) Review out-of-the-box web parts to determine if any can replace your custom web part.
    
  3. Replace existing web parts with 外接程序 part instances, or using other techniques (such as embedded JavaScript in pages or page layouts) to achieve the same functionality.
    
     **注释**  To replace your existing web parts with 外接程序 parts, you need to:
  4. Use embedded JavaScript to manipulate UI elements.
    
- Page manipulation. You might need to implement page manipulation during your custom site provisioning process. The [Provisioning.Pages](https://github.com/OfficeDev/PnP/tree/dev/Scenarios/Provisioning.Pages) code sample shows page manipulation techniques, including creating a wiki page, adding HTML content to the page, creating a promoted links list, creating pages with different layouts, adding out-of-the-box web parts to the page, and removing the page.
    
- Site columns, list definitions, and content types. If your site columns, list definitions, and content types were created using the Feature framework elements, which were deployed using farm solutions, then you must use the swing or content migration transformation approach. This does not apply to Feature framework elements deployed using sandbox solutions. To use the content migration transformation approach, you must use third-party tools to remove the 服务器场解决方案 dependencies.
    
- Modules or Feature framework. Modules use pointers to files, which means that the files are not customized and are deployed on the file system. If your 服务器场解决方案 use modules, customize the files by deploying alternate versions of the same files to the content database, scan and update your solutions to point to the new files stored in the content database, and then retract the 服务器场解决方案 that pointed to files stored on the file system.
    
- Site templates and web templates. You should focus on transforming Feature framework elements deployed by the site template or web template. For example, ensure that the default.aspx page of the site is not replaced when retracting the 服务器场解决方案.
    
- Timer jobs. If you are using SharePoint Online, you cannot create and manage timer jobs. Instead, you can create a console application that uses Windows Task Scheduler or an [Azure Web Job](http://azure.microsoft.com/documentation/articles/web-sites-create-web-jobs/) to schedule and run the console application remotely. When creating a custom timer job, determine whether you need to use a specific account or an OAuth-based 外接程序-only token. The [Core.TimerJobs.Samples](https://github.com/OfficeDev/PnP/tree/dev/Solutions/Core.TimerJobs.Samples) code sample shows how to create your own custom timer job.
    
     **注释**  If your timer job uses server-side code, you will have to redesign your timer job to use the CSOM or another method.

## In this section



|**Article**|**Shows you how to...**|
|:-----|:-----|
|[替换 SharePoint 内容类型和网站栏](replace-sharepoint-content-types-and-site-columns.md)|Use CSOM to replace content types and site columns, add site columns to new content types, and replace content types with new content types.|
|[替换使用 SharePoint 服务器场解决方案中的模块部署的文件](replace-files-deployed-using-modules-in-sharepoint-farm-solutions.md)|Replace files, such as master pages and page layouts, that were deployed using modules in farm solutions.|
|[替换根据列表定义创建的 SharePoint 列表](replace-sharepoint-lists-created-from-list-definitions.md)|Replace lists and libraries that were created by using list definitions. |
|[使用外接程序部件替换 SharePoint Web 部件](replace-sharepoint-web-parts-with-add-in-parts.md)|Replace Web Parts with 外接程序部件 by using CSOM.|



## 其他资源



- [Office 365 development patterns and practices solution guidance](https://msdn.microsoft.com/library/office/dn904529.aspx)
    
- [Patterns and Practices developer center](http://dev.office.com/patterns-and-practices)
    
- [Build SharePoint Add-ins](https://msdn.microsoft.com/library/jj163230.aspx)
    
