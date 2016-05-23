
# 在创建时修改 SharePoint 主机 Web 列表
修改创建列表时在主机 Web 中创建的 SharePoint 列表。

 **上次修改时间：** 2015年8月7日

 _ **适用范围：** Office 365?| SharePoint 2013?| SharePoint Add-ins?| SharePoint Online_

 **注释**  名称"SharePoint 相关应用程序"将更改为"SharePoint 外接程序"。在转换期间，某些 SharePoint 产品和 Visual Studio 工具的文档和 UI 仍可能使用术语"SharePoint 相关应用程序"。有关详细信息，请参阅 [Office 和 SharePoint 相关应用程序的新名称](05b07b04-6c8b-4b7e-bd86-e32c589dfead.md#bk_newname)。

 在主机 Web 中创建新的列表时，您可以使用 **ListAdded** 远程事件接收器修改该列表。例如，您可以启用版本控制，将内容类型添加到列表中，或者执行客户端对象模型 (CSOM) 实施的任何其他更改。
将列表添加到主机 Web 时，您需以编程方式附加事件接收器。 [Core.EventReceiversBasedModifications](https://github.com/OfficeDev/PnP/tree/dev/Scenarios/Core.EventReceiversBasedModifications) 示例说明如何使用提供程序承载的加载项执行此操作。安装加载项时，将发生 **AppInstalled** 事件，您可以使用此事件附加 **ListAdded** 事件。
 **为改进此内容做贡献**
您可以获取最新的更新，或为改进 [GitHub 上的此文章](https://github.com/OfficeDev/PnP-Guidance)做贡献。您还可以为改进本示例以及 [GitHub 上的其他示例](https://github.com/OfficeDev/PnP)做贡献。有关示例的完整列表，请参阅 [模式和做法开发人员中心](http://dev.office.com/patterns-and-practices)。我们欢迎您做出 [贡献](https://github.com/OfficeDev/PnP/wiki/contributing-to-Office-365-developer-patterns-and-practices)。 

## 开始之前

若要开始，请从 GitHub 上的 [Office 365 开发人员模式和做法](https://github.com/OfficeDev/PnP/tree/dev)项目下载 [Core.EventReceiversBasedModifications](https://github.com/OfficeDev/PnP/tree/dev/Scenarios/Core.EventReceiversBasedModifications) 示例加载项。


## 附加 ListAdded 事件

要实施加载项的事件处理程序，请显示 SharePoint 项目的属性，并将"处理加载项安装"和"处理加载项卸载"设置为"True"。

以下代码示例说明如何修改  **AppInstalled** 事件接收器以附加 **ListAdded** 事件接收器。


 **注释**  本文中的代码按原样提供，不提供任何明示或暗示的担保，包括对特定用途适用性、适销性或不侵权的默示担保。




```C#
bool rerExists = false;
cc.Load(cc.Web.EventReceivers);
cc.ExecuteQuery();

foreach (var rer in cc.Web.EventReceivers)
{
  if (rer.ReceiverName == RECEIVER_NAME)
  {
    rerExists = true;
  }
}

if (!rerExists)
{
  EventReceiverDefinitionCreationInformation receiver = new EventReceiverDefinitionCreationInformation();
  receiver.EventType = EventReceiverType.ListAdded;

  // Get WCF URL where this message was handled.
  OperationContext op = OperationContext.Current;
  Message msg = op.RequestContext.RequestMessage;
  receiver.ReceiverUrl = msg.Headers.To.ToString();
  receiver.ReceiverName = RECEIVER_NAME;
  receiver.Synchronization = EventReceiverSynchronization.Synchronous;
  cc.Web.EventReceivers.Add(receiver);
  cc.ExecuteQuery();
}

```


## 自定义已添加的列表

 **ListAdded** 事件处理程序触发时，将运行以下代码。


```C#
private void HandleListAdded(SPRemoteEventProperties properties)
{
  using (ClientContext cc = TokenHelper.CreateRemoteEventReceiverClientContext(properties))
  {
    if (cc != null)
    {
      try
        {
          if (properties.ListEventProperties.TemplateId == (int)ListTemplateType.DocumentLibrary)
          {
          //set versioning 
   cc.Web.GetListByTitle(properties.ListEventProperties.ListTitle).UpdateListVersioning(true, true);
          }
        }
         catch (Exception ex)
         {
           System.Diagnostics.Trace.WriteLine(ex.Message);
         }
       }
    }
  }
}

```


## 卸载事件接收器

卸载加载项时，事件接收器应该也会被删除。要在调试期间实现这一点，请转到"测试中的加载项"库并使用加载项上的"删除"选项。这会使用删除所创建远程事件处理程序的正确权限触发  **AppUninstalling** 事件。如果您仅关闭浏览器或从"网站内容"卸载加载项，则事件接收器永远不会触发，或者 **AppUninstalling** 事件接收器没有足够的权限删除 **ListAdded** 事件接收器。这是因为加载项在侧载时的部署方式有所不同，这是当您按 F5 时 Visual Studio 所执行的操作。


 **注释**  我们建议您在一个干净的开发人员网站中测试此示例。


## 其他资源



- [SharePoint 网站设置解决方案](sharepoint-site-provisioning-solutions.md)
    
- [Core.EventReceiversBasedModifications sample](https://github.com/OfficeDev/PnP/tree/dev/Scenarios/Core.EventReceiversBasedModifications)
    
