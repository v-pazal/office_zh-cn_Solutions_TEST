
# 使用 ChangeQuery 和 ChangeToken 查询 SharePoint 更改日志
使用  **ChangeQuery** 和 **ChangeToken** 查询对 SharePoint 内容数据库、网站集、网站或列表进行的更改的 SharePoint 更改日志。

 **上次修改时间：** 2015年8月7日

 _ **适用范围：** SharePoint 2013?| SharePoint Add-ins?| SharePoint Online_

 **注释**  名称"SharePoint 相关应用程序"将更改为"SharePoint 外接程序"。在转换期间，某些 SharePoint 产品和 Visual Studio 工具的文档和 UI 仍可能使用术语"SharePoint 相关应用程序"。有关详细信息，请参阅 [Office 和 SharePoint 相关应用程序的新名称](05b07b04-6c8b-4b7e-bd86-e32c589dfead.md#bk_newname)。

您可以通过使用 [ChangeQuery](https://msdn.microsoft.com/library/office/microsoft.sharepoint.client.changequery.aspx) 和 [ChangeToken](https://msdn.microsoft.com/zh-cn/library/office/microsoft.sharepoint.client.changetoken.aspx) 查找和处理对 SharePoint 内容数据库、网站集、网站或列表所做的更改，来查询 SharePoint 更改日志。
[Core.ListItemChangeMonitor](https://github.com/OfficeDev/PnP/tree/dev/Samples/Core.ListItemChangeMonitor) 代码示例说明如何使用 SharePoint 的更改日志来查找和处理对 SharePoint 列表所做的更改。请使用此代码示例执行以下操作：

- 监控 SharePoint 以便获取对列表、网站、网站集或内容数据库的更改。
    
- 在对列表中的某个项目做出更改后，启动一个业务流程。
    
- 补充您的远程事件接收器。使用带有远程事件接收器模式的更改日志模式提供更为可靠的体系结构，以便处理对 SharePoint 内容数据库、网站集、网站或列表所做的所有更改。远程事件接收器立即运行，但是因为它们在远程服务器上运行，您可能会遇到通信故障。此更改日志模式可以确保所有更改都可用于进行处理，但是此应用程序对更改的处理通常按照计划运行（例如，计时器作业）。这意味着不会立即处理更改。如果同时使用这两种模式，请确保您使用某种机制来防止对同一更改处理两次。有关详细信息，请参阅 [使用 SharePoint 中的远程事件接收器](Use-remote-event-receivers-in-SharePoint.md)。
    
 **为改进此内容做贡献**
您可以获取最新的更新，或为改进 [Github 上的此文章](https://github.com/OfficeDev/PnP-Guidance/blob/master/articles/Query-SharePoint-change-log-with-ChangeQuery-and-Change-Tokens.md)做贡献。您还可以为改进本示例以及 [GitHub 上的其他示例](https://github.com/OfficeDev/PnP)做贡献。有关示例的完整列表，请参阅 [模式和做法开发人员中心](http://dev.office.com/patterns-and-practices)。我们欢迎您做出 [贡献](https://github.com/OfficeDev/PnP/wiki/contributing-to-Office-365-developer-patterns-and-practices)。 

## 开始之前

若要开始，请从 GitHub 上的 [Office 365 开发人员模式和做法](https://github.com/OfficeDev/PnP/tree/dev)项目下载 [Core.ListItemChangeMonitor](https://github.com/OfficeDev/PnP/tree/dev/Samples/Core.ListItemChangeMonitor) 示例外接程序。

在运行此代码示例之前，请执行以下操作：


1. 登录到您想要创建列表的 Office 365 网站。
    
2. 选择"网站内容"。
    
3. 选择"添加外接程序"。
    
4. 选择"自定义列表"。
    
5. 在"名称"中输入  **TestList** 。
    
6. 选择"创建"。
    
若要查看此代码示例的演示，请执行以下步骤：


1. 在 Visual Studio 中选择"启动"。
    
2. 输入您的 Office 365 网站的 URL、列表的名称 ( **TestList** ) 以及您的 Office 365 凭据。控制台应用程序现在等待要对 **TestList** 所做的更改。默认情况下，控制台应用程序将检查更改日志并每隔 30 秒更新显示。
    
3. 将新项目添加到"TestList"：
    
      1. 打开您的 Office 365 网站并转到"网站内容">"TestList"。
    
  2. 选择"新建项目"。
    
  3. 在"标题"中输入  **MyTitle** ，然后选择"保存"。
    
4. 确认您的更改在控制台应用程序中显示。您可以通过在控制台应用程序中输入  **r** 来强制控制台应用程序读取 SharePoint 的更改日志。
    

## 使用 Core.ListItemChangeMonitor 外接程序

在 Program.cs 中， **Main** 调用 **DoWork** 来读取和处理 SharePoint 的更改日志：


1. 创建  **ChangeQuery** 对象来访问 SharePoint 的更改日志。
    
2. 通过  **cq.Item = true** ，使用更改日志返回对项目的更改。这些更改包括：
    
      - 使用  **cq.Add= true** 添加的新项目。
    
  - 使用  **cq.DeleteObject = true** 删除的项目。
    
  - 使用  **cq.Update=true** 修改的项目。
    
3. 创建  **ChangeToken** 对象来从某个时间点的更改日志中读取更改。
    
4. 通过版本号、更改作用域、 **TestList** 的 GUID、更改发生的日期和时间，以及 ChangeToken 的更改项值（初始化的值为 -1）来设置 [ChangeToken.StringValue](https://msdn.microsoft.com/library/office/microsoft.sharepoint.client.changetoken.stringvalue.aspx)。本代码示例通过使用  **DateTime.Now.AddDays(-2).ToUniversalTime().Ticks.ToString()** ，将更改发生的日期和时间初始化为更改发生的前两天，来限制从更改日志读取的更改量。
    
5.  通过以下两种方式来读取更改日志：每隔 30 秒读取一次（这是由常量 **WaitSeconds** 设置的默认等待时间），或者由用户输入 **r** 。在读取更改日志时，控制台应用程序执行以下步骤：
    
      1.  使用 [List.GetChanges](https://msdn.microsoft.com/library/office/microsoft.sharepoint.client.list.getchanges.aspx) 返回 [ChangeCollection](https://msdn.microsoft.com/library/office/microsoft.sharepoint.client.changecollection.aspx)，由于已对上次的更改进行了处理，所返回的就是对列表所做的更改集合。
    
  2. 调用  **DisplayChanges** 以显示 **ChangeCollection** 对象中的更改。
    
  3. 设置新的时间点以从更改日志读取更改。如果对列表进行了更改（在  **coll** 中返回），则将 **ChangeTokenStart** 设置为上次更改的日期和时间。
    

 **注释**  本文中的代码按原样提供，不提供任何明示或暗示的担保，包括对特定用途适用性、适销性或不侵权的默示担保。


```C#
private static void DoWork()
        {
            Console.WriteLine();
            Console.WriteLine("Url: " + url);
            Console.WriteLine("User name: " + userName);
            Console.WriteLine("List name: " + listName);
            Console.WriteLine();
            try
            {

                Console.WriteLine(string.Format("Connecting to {0}", url));
                Console.WriteLine();
                ClientContext cc = new ClientContext(url);
                cc.AuthenticationMode = ClientAuthenticationMode.Default;
                cc.Credentials = new SharePointOnlineCredentials(userName, password);

                ListCollection lists = cc.Web.Lists;
                IEnumerable<List> results = cc.LoadQuery<List>(lists.Where(lst => lst.Title == listName));
                cc.ExecuteQuery();
                List list = results.FirstOrDefault();
                if (list == null)
                {

                    Console.WriteLine("A list named \"{0}\" does not exist. Press any key to exit...", listName);
                    Console.ReadKey();
                    return;
                }

                nextRunTime = DateTime.Now;

                ChangeQuery cq = new ChangeQuery(false, false);
                cq.Item = true;
                cq.DeleteObject = true;
                cq.Add = true;
                cq.Update = true;

                // Set the ChangeTokenStart to two days ago to reduce how much data is returned from the change log. Depending on your requirements, you might want to change this value. 
                // The value of the string assigned to ChangeTokenStart.StringValue is semicolon delimited, and takes the following parameters in the order listed:
                // Version number. 
                // The change scope (0 – Content Database, 1 – site collection, 2 – site, 3 – list).
                // GUID of the item the scope applies to (for example, GUID of the list). 
                // Time (in UTC) from when changes occurred.
                // Initialize the change item on the ChangeToken using a default value of -1.

                cq.ChangeTokenStart = new ChangeToken();
                cq.ChangeTokenStart.StringValue = string.Format("1;3;{0};{1};-1", list.Id.ToString(), DateTime.Now.AddDays(-2).ToUniversalTime().Ticks.ToString());

                Console.ForegroundColor = ConsoleColor.Red;
                Console.WriteLine(string.Format("Ctrl+c to exit. Press \"r\" key to force the console application to read the change log without waiting {0} seconds.", WaitSeconds));
                Console.WriteLine();
                Console.ResetColor();
                do
                {
                    do
                    {
                        if (Console.KeyAvailable &amp;&amp; Console.ReadKey(true).KeyChar == 'r') { break; }
                    }
                    while (nextRunTime > DateTime.Now);

                    Console.WriteLine(string.Format("Looking for items modified after {0} UTC", GetDateStringFromChangeToken(cq.ChangeTokenStart)));

                    
                    ChangeCollection coll = list.GetChanges(cq);
                    cc.Load(coll);
                    cc.ExecuteQuery();


                    DisplayChanges(coll, cq.ChangeTokenStart);
                    // If there are changes to the list (which was returned in coll), set ChangeTokenStart to the last change's date and time. This will be used as the starting point for the next read from the change log.                      
                    cq.ChangeTokenStart = coll.Count > 0 ? coll.Last().ChangeToken : cq.ChangeTokenStart;

                    nextRunTime = DateTime.Now.AddSeconds(WaitSeconds);

                } while (true);
            }
            catch (Exception e)
            {
                Console.WriteLine(e.Message);
                Console.WriteLine("Press any key to exit...");
                Console.ReadKey();

            }
        }
```


## 其他资源



- [Office 365 开发模式和做法解决方案指南](https://msdn.microsoft.com/library/office/dn904529.aspx)
    
- [使用 SharePoint 中的远程事件接收器](https://msdn.microsoft.com/library/dn957928.aspx)
    
- [ChangeQuery 成员](https://msdn.microsoft.com/library/office/microsoft.sharepoint.client.changequery_members.aspx)
    
- [模式和做法开发人员中心](http://dev.office.com/patterns-and-practices)
    
