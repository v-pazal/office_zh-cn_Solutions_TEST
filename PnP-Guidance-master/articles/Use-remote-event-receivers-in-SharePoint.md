
# 使用 SharePoint 中的远程事件接收器
使用远程事件接收器来处理 SharePoint 外接程序模型中的事件。使用 AppInstalled 和 AppUninstalling 事件来设置或删除 SharePoint 对象和外接程序所需的其他事件接收器。

 **上次修改时间：** 2015年8月7日

 _ **适用范围：** SharePoint 2013?| SharePoint Add-ins?| SharePoint Online_

 **注释**  名称"SharePoint 相关应用程序"将更改为"SharePoint 外接程序"。在转换期间，某些 SharePoint 产品和 Visual Studio 工具的文档和 UI 仍可能使用术语"SharePoint 相关应用程序"。有关详细信息，请参阅 [Office 和 SharePoint 相关应用程序的新名称](05b07b04-6c8b-4b7e-bd86-e32c589dfead.md#bk_newname)。

[Core.EventReceivers](https://github.com/OfficeDev/PnP/tree/dev/Samples/Core.EventReceivers) 示例说明如何使用带有远程事件接收器的提供程序托管的外接程序来处理 AppInstalled 和 AppUninstalling 事件。AppInstalled 和 AppUninstalling 事件设置和删除此外接程序运行时所使用的 SharePoint 对象。此外，AppInstalled 事件处理程序还将 ItemAdded 事件处理程序添加到列表。如果您需要执行以下操作，请使用此解决方案：

- 使用 AppInstalled 事件设置与您的外接程序配合使用的各种 SharePoint 对象或其他事件接收器，以便在首次运行外接程序时进行配置。
    
- 使用完全信任的代码解决方案替换实现的事件接收器。在完全信任的代码解决方案中，您可以在 SharePoint 服务器上运行事件接收器。在新的 SharePoint 外接程序模型中，由于您无法在 SharePoint 服务器上运行事件接收器，则需要在 Web 服务器上实现一个远程事件接收器。
    
- 接收 SharePoint 中出现的更改通知。例如，在新项目被添加到某个列表时，您想要执行一个任务。
    
- 补充您的更改日志解决方案。使用带有更改日志模式的远程事件接收器提供更为可靠的体系结构，以便处理对 SharePoint 内容数据库、网站集、网站或列表所做的所有更改。远程事件接收器立即运行，但是因为它们在远程服务器上运行，您可能会遇到通信故障。此更改日志模式可以确保所有更改都可用于进行处理，但是此应用程序对更改的处理通常按照计划运行（例如，计时器作业）。这意味着不会立即处理更改。如果同时使用这两种模式，请确保您使用某种机制来防止对同一更改处理两次。有关详细信息，请参阅 [使用 ChangeQuery 和 ChangeToken 查询 SharePoint 更改日志](query-sharepoint-change-log-with-changequery-and-changetoken.md)。
    

 **注释**  SharePoint 托管的外接程序不支持远程事件接收器。若要使用远程事件接收器，您需要使用提供程序托管的外接程序。对于同步方案或长时间运行的进程，您不应使用远程事件接收器。有关详细信息，请参阅 [如何：创建外接程序事件接收器](https://msdn.microsoft.com/library/office/jj220052.aspx)。

 **为改进此内容做贡献**
您可以获取最新的更新，或为改进 [GitHub 上的此文章](https://github.com/OfficeDev/PnP-Guidance/blob/master/articles/Use-remote-event-receivers-in-SharePoint.md)做贡献。您还可以为改进本示例以及 [GitHub 上的其他示例](https://github.com/OfficeDev/PnP)做贡献。有关示例的完整列表，请参阅 [模式和做法开发人员中心](http://dev.office.com/patterns-and-practices)。我们欢迎您做出 [贡献](https://github.com/OfficeDev/PnP/wiki/contributing-to-Office-365-developer-patterns-and-practices)。 

## 开始之前

若要开始，请从 GitHub 上的 [Office 365 开发人员模式和做法](https://github.com/OfficeDev/PnP/tree/dev)项目下载 [Core.EventReceivers](https://github.com/OfficeDev/PnP/tree/dev/Samples/Core.EventReceivers) 示例外接程序。

在运行此外接程序之前，请执行以下操作：


1. 在 Core.EventReceivers 项目的属性中，确认"处理已安装的外接程序"和"处理外接程序卸载"设置为  **True** 。将"处理已安装的外接程序"和"处理外接程序卸载"设置为 **True** 会创建用于为 **AppInstalled** 和 **AppUninstalling** 事件定义事件处理程序的 WCF 服务。在 Core.EventReceivers 中，打开 AppManifest.xml 上的快捷菜单（右键单击），并选择"属性"。 **InstalledEventEndpoint** 和 **UninstallingEventEndpoint** 指向处理 **AppInstalled** 和 **AppUninstalling** 事件的远程事件接收器。
    
2. 将远程事件接收器附加到主机 Web 中的对象通常仅需要对该对象的"管理"权限。例如，在将事件接收器附加到现有列表时，此外接程序仅需要对此"列表"的"管理"权限。本代码示例需要对"Web"的"管理"权限，因为本代码示例会在主机 Web 上添加一个列表并激活某项功能。若要设置 Web 上的管理权限，请执行以下操作：
    
      1. 双击 Core.EventReceivers\AppManifest.xml。
    
  2. 选择"权限"。
    
  3. 确认"作用域"设置为"Web"，且"权限"设置为"管理"。
    
3. 若要运行此代码示例，您需要一个 Azure 订阅。若要注册试用版，请参阅 [免费试用一个月](http://azure.microsoft.com/zh-cn/pricing/free-trial/)。
    
4. 通过 ACS 支持创建 Azure 服务总线命名空间。
    
      1. 安装 Azure PowerShell。有关详细信息，请参阅 [如何：安装 Azure PowerShell](http://azure.microsoft.com/zh-cn/documentation/articles/powershell-install-configure/#Install)。
    
  2. 启动"Azure PowerShell"。
    
  3. 输入"Add-AzureAccount"。
    
  4. 在"登录到 Windows Azure"对话框中输入您的电子邮件地址，然后选择"继续"。
    
  5. 输入您的密码，然后选择"登录"。
    
  6. 使用下面的 PowerShell cmdlet 创建新的服务总线命名空间。
    
  ```
  New-AzureSBNamespace NamespaceNameRegion  -CreateACSNamespace $true -NamespaceType Messaging

  ```


    其中：
    
      -  _NamespaceName_ 是您的 Azure 服务总线命名空间的名称。
    
  -  _Region_ 是离您最近的区域。例如，您可以输入 **"美国西部"** 。 必须将区域名称放在双引号内。
    
  7. 返回到您的 Azure 管理门户。依次选择"服务总线"和您输入的命名空间名称。
    
  8. 选择"管理连接字符串"，然后在"ACS 连接字符串"中选择复制按钮。
    
  9. 返回到 Visual Studio。
    
  10. 右键单击 Core.EventReceivers，选择"属性">"SharePoint"。
    
  11. 选择"通过 Microsoft Azure 服务总线启用调试"。
    
  12. 在"Microsoft Azure 服务总线连接字符串"中，粘贴此 ACS 连接字符串。
    
  13. 选择"保存"。
    
5. 运行此代码示例，并执行下面的其他步骤：
    
      - 在"向外接程序授予权限"窗口中单击"信任它"。
    
  - 关闭"向外接程序授予权限"窗口。
    
  - 完成此外接程序和 WCF 服务的安装后，您的浏览器将会打开。
    
  - 登录到您的 Office 365 网站。将显示 Core.EventReceivers 外接程序的起始页。
    

## 使用 Core.EventReceivers 外接程序

若要查看 Core.EventReceivers 代码示例的演示，请执行以下操作：


1. 运行此示例，并在起始页上选择"返回到网站"。
    
2. 选择"网站内容"。
    
3. 选择"远程事件接收器作业"。
    
4. 选择"新建项目"。
    
5. 在"标题"中，输入  **Contoso** ，并在"说明"中输入 **Contoso 测试** 。
    
6. 返回到列表并刷新页面。
    
7. 确认新添加的项目的说明已更新为 **由 ReR 192336 更新的 Contoso 测试** ，其中"192336"是时间戳。
    
在 Services/AppEventReceiver.cs 中， **AppEventReceiver** 实现 **IRemoteEventService** 接口。此代码示例可以实现 **ProcessEvent** 方法，因为它使用同步事件。如果您使用异步事件，则需要实现 **ProcessOneWayEvent** 方法。

 **ProcessEvent** 处理以下 **SPRemoteEventType** 远程事件：


- 安装外接程序时发生的  **AppInstalled** 事件。发生 **AppInstalled** 事件时， **ProcessEvent** 调用 **HandleAppInstalled** 。
    
- 卸载外接程序时发生的  **AppUninstalling** 事件。 发生 **AppUninstalling** 事件时， **ProcessEvent** 调用 **HandleAppUninstalling** 。 **AppUninstalling** 事件仅在用户通过以下两种方法完全删除该外接程序时运行：从网站回收站删除该外接程序（适用于最终用户），或从"测试中的外接程序"列表删除该外接程序（适用于开发人员）。
    
-  将某个项目添加到列表时发生的 **ItemAdded** 事件。在发生 **ItemAdded** 事件时， **ProcessEvent** 调用 **HandleItemAdded** 。
    

 **注释**  在 Core.EventReceiver 项目属性中，只有"处理已安装的外接程序"和"处理外接程序卸载"属性可用。本代码示例说明在外接程序安装过程中如何使用  **AppInstalled** 事件将 **ItemAdded** 事件处理程序添加到主机 Web 上的列表。


 **注释**  本文中的代码按原样提供，不提供任何明示或暗示的担保，包括对特定用途适用性、适销性或不侵权的默示担保。




```C#
public SPRemoteEventResult ProcessEvent(SPRemoteEventProperties properties)
        {

            SPRemoteEventResult result = new SPRemoteEventResult();

            switch (properties.EventType)
            {
                case SPRemoteEventType.AppInstalled:
                    HandleAppInstalled(properties);
                    break;
                case SPRemoteEventType.AppUninstalling:
                    HandleAppUninstalling(properties);
                    break;
                case SPRemoteEventType.ItemAdded:
                    HandleItemAdded(properties);
                    break;
            }


            return result;
        }
```

在 RemoteEventReceiverManager.cs 中， **HandleAppInstalled** 调用 **RemoteEventReceiverManager.AssociateRemoteEventsToHostWeb** 。




```C#
 private void HandleAppInstalled(SPRemoteEventProperties properties)
        {
            using (ClientContext clientContext =
                TokenHelper.CreateAppEventClientContext(properties, false))
            {
                if (clientContext != null)
                {
                    new RemoteEventReceiverManager().AssociateRemoteEventsToHostWeb(clientContext);
                }
            }
        }
```

 **AssociateRemoteEventsToHostWeb** 创建或启用 Core.EventReceivers 外接程序所使用的各种 SharePoint 对象。您的要求可能会有所不同。 **AssociateRemoteEventsToHostWeb** 将执行以下操作：


- 使用  **Web.Features.Add** 启用推送通知功能。
    
- 使用  **clientContext** 对象搜索某个列表。如果此列表不存在， **CreateJobsList** 会创建此列表。如果此列表存在，会使用 **List.EventReceivers** 来搜索名为 **ItemAddedEvent** 的现有事件接收器。
    
- 如果  **ItemAddedEvent** 事件接收器不存在：
    
      - 实例化一个新的  **EventReceiverDefinitionCreationInformation** 对象来创建新远程事件接收器。 **ItemAdded** 事件接收器类型将添加到 **EventReceiverDefinitionCreationInformation.EventType** 。
    
  - 将  **EventReceiverDefinitionCreationInformation.ReceiverURL** 设置为 **AppInstalled** 远程事件接收器的 URL。
    
  - 使用  **List.EventReceivers.Add** 将新的事件接收器添加到此列表。
    



```C#
public void AssociateRemoteEventsToHostWeb(ClientContext clientContext)
        {
            // Add Push Notification feature to host web.
            // Not required but it is included here to show you
            // how to activate features.
            clientContext.Web.Features.Add(
                     new Guid("41e1d4bf-b1a2-47f7-ab80-d5d6cbba3092"),
                     true, FeatureDefinitionScope.None);


            // Get the Title and EventReceivers lists.
            clientContext.Load(clientContext.Web.Lists,
                lists => lists.Include(
                    list => list.Title,
                    list => list.EventReceivers).Where
                        (list => list.Title == LIST_TITLE));

            clientContext.ExecuteQuery();

            List jobsList = clientContext.Web.Lists.FirstOrDefault();

            bool rerExists = false;
            if (null == jobsList)
            {
                // List does not exist, create it. 
                jobsList = CreateJobsList(clientContext);

            }
            else
            {
                foreach (var rer in jobsList.EventReceivers)
                {
                    if (rer.ReceiverName == RECEIVER_NAME)
                    {
                        rerExists = true;
                        System.Diagnostics.Trace.WriteLine("Found existing ItemAdded receiver at "
                            + rer.ReceiverUrl);
                    }
                }
            }

            if (!rerExists)
            {
                EventReceiverDefinitionCreationInformation receiver =
                    new EventReceiverDefinitionCreationInformation();
                receiver.EventType = EventReceiverType.ItemAdded;
                
                // Get WCF URL where this message was handled.
                OperationContext op = OperationContext.Current;
                Message msg = op.RequestContext.RequestMessage;
                receiver.ReceiverUrl = msg.Headers.To.ToString();

                receiver.ReceiverName = RECEIVER_NAME;
                receiver.Synchronization = EventReceiverSynchronization.Synchronous;

                // Add the new event receiver to a list in the host web.
                jobsList.EventReceivers.Add(receiver);
                clientContext.ExecuteQuery();

                System.Diagnostics.Trace.WriteLine("Added ItemAdded receiver at " + receiver.ReceiverUrl);
            }
        }
```

将某个项目添加到"远程事件接收器作业"列表时，AppEventReceiver.svc.cs 中的  **ProcessEvent** 处理 **ItemAdded** 事件，然后调用 **HandleItemAdded** 。 **HandleItemAdded** 调用 **RemoteEventReceiverManager.ItemAddedToListEventHandler** 。 **ItemAddedToListEventHandler** 获取已添加的列表项，并将字符串"由 ReR 更新"添加到列表项的说明。




```C#
 public void ItemAddedToListEventHandler(ClientContext clientContext, Guid listId, int listItemId)
        {
            try
            {
                List photos = clientContext.Web.Lists.GetById(listId);
                ListItem item = photos.GetItemById(listItemId);
                clientContext.Load(item);
                clientContext.ExecuteQuery();

                item["Description"] += "\nUpdated by RER " +
                    System.DateTime.Now.ToLongTimeString();
                item.Update();
                clientContext.ExecuteQuery();
            }
            catch (Exception oops)
            {
                System.Diagnostics.Trace.WriteLine(oops.Message);
            }

        }
```


## 其他资源



- [Office 365 开发模式和做法解决方案指南](https://msdn.microsoft.com/library/office/dn904529.aspx)
    
- [Core.AppEvents.HandlerDelegation ](https://github.com/OfficeDev/PnP/tree/dev/Samples/Core.AppEvents.HandlerDelegation)
    
- [模式和做法开发人员中心](http://dev.office.com/patterns-and-practices)
    
