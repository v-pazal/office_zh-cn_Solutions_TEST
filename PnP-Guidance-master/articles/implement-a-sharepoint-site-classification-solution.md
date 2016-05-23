
# 实现 SharePoint 网站分类解决方案
在 SharePoint 中实施网站分类解决方案。

 **上次修改时间：** 2015年8月7日

 _ **适用范围：** Office 365?| SharePoint 2013?| SharePoint Add-ins?| SharePoint Online_

 **注释**  名称"SharePoint 相关应用程序"将更改为"SharePoint 外接程序"。在转换期间，某些 SharePoint 产品和 Visual Studio 工具的文档和 UI 仍可能使用术语"SharePoint 相关应用程序"。有关详细信息，请参阅 [Office 和 SharePoint 相关应用程序的新名称](05b07b04-6c8b-4b7e-bd86-e32c589dfead.md#bk_newname)。

即使是在管理良好的情况下，SharePoint 网站也可能激增并失去控制。用户会在需要时创建网站，但很少删除网站。搜索爬网因为不使用的网站集而不堪重负，并且导致搜索产生过期且不相关的结果。通过网站分类，您可以确定并保留敏感数据。本文向您介绍如何使用 [Core.SiteClassification](https://github.com/OfficeDev/PnP/tree/dev/Scenarios/Core.SiteClassification) 示例实施网站分类解决方案，以及如何使用 SharePoint 网站策略强制执行删除操作。您可以将此解决方案集成到您现有的网站设置解决方案来更好地管理您的网站。
 **为改进此内容做贡献**
您可以获取最新的更新，或为改进 [GitHub 上的此文章](https://github.com/OfficeDev/PnP-Guidance)做贡献。您还可以为改进本示例以及 [GitHub 上的其他示例](https://github.com/OfficeDev/PnP)做贡献。有关示例的完整列表，请参阅 [模式和做法开发人员中心](http://dev.office.com/patterns-and-practices)。我们欢迎您做出 [贡献](https://github.com/OfficeDev/PnP/wiki/contributing-to-Office-365-developer-patterns-and-practices)。 

## 开始之前

若要开始，请从 GitHub 上的 [Office 365 开发人员模式和做法](https://github.com/OfficeDev/PnP/tree/dev)项目下载 [Core.SiteClassification](https://github.com/OfficeDev/PnP/tree/dev/Scenarios/Core.SiteClassification) 示例。


## 定义并设置网站策略

首先，您需要定义并设置将在您的所有网站集中可用的网站策略。Core.SiteClassification 示例适用于 SharePoint Online MT，但也可以在 SharePoint Online 专用版或内部部署 SharePoint 中使用。网站策略在内容类型集线器中设置，在 SharePoint Online MT 中位于  `https:// [tenanatname]/sites/contentTypeHub`。要设置网站策略，请转到"设置">"网站集管理">"网站策略">"创建"。将显示"新建网站策略"页面。有关网站策略选项的详细信息，请参阅 [SharePoint 2013 中的网站策略概述](https://technet.microsoft.com/zh-cn/library/jj219569%28v=office.15%29.aspx)。

在"新建网站策略"页面上的下列字段中进行输入：


-  **名称**：HBI
    
-  **描述**：这是超级秘密。
    
- 设置单选按钮"自动删除网站"。
    
- 对于"删除事件:"，使用网站创建日期 + 1 年。
    
- 选中"在删除之前提前以下时间向网站所有者发送电子邮件通知:"复选框，并将其设置为 1 个月。
    
- 选中"每隔以下时间发送跟进通知:"复选框，并将其设置为 14 天。
    
- 选中"所有者可以将即将发生的删除推迟以下时间:"复选框，并将其设置为 1 个月。
    
- 选中"网站集关闭后将为只读"复选框。
    
对于名称  **MBI** 和 **LBI** ，将这些步骤重复两次以上。对删除或保留策略使用不同的设置。完成后，您即可发布新策略。


## 插入自定义操作

您可以将网站分类的自定义操作插入到"设置"页面和"SharePoint 齿轮"图标。此操作仅对具有  **ManageWeb** 权限的用户可用。有关详细信息，请参阅 [默认自定义操作位置和 ID](https://msdn.microsoft.com/zh-cn/library/office/bb802730%28v=office.15%29.aspx)。


 **注释**  本文中的代码按原样提供，不提供任何明示或暗示的担保，包括对特定用途适用性、适销性或不侵权的默示担保。


```C#
/// <summary>
/// Adds a custom Action to a Site Collection.
/// </summary>
/// <param name="ctx">The Authenticated client context.</param>
/// <param name="hostUrl">The Provider hosted URL for the Application</param>

static void AddCustomAction(ClientContext ctx, string hostUrl)
{
    var _web = ctx.Web;
    ctx.Load(_web);
    ctx.ExecuteQuery();

    // You only want the action to show up if you have manage web permissions.
    BasePermissions _manageWebPermission = new BasePermissions();
    _manageWebPermission.Set(PermissionKind.ManageWeb);

    CustomActionEntity _entity = new CustomActionEntity()
    {
        Group = "SiteTasks",
        Location = "Microsoft.SharePoint.SiteSettings",
        Title = "Site Classification",
        Sequence = 1000,
        Url = string.Format(hostUrl, ctx.Url),
        Rights = _manageWebPermission,
    };

    CustomActionEntity _siteActionSC = new CustomActionEntity()
    {
        Group = "SiteActions",
        Location = "Microsoft.SharePoint.StandardMenu",
        Title = "Site Classification",
        Sequence = 1000,
        Url = string.Format(hostUrl, ctx.Url),
        Rights = _manageWebPermission
    };
    _web.AddCustomAction(_entity);
    _web.AddCustomAction(_siteActionSC);
}


```


## 自定义网站分类

您可以使用"编辑网站信息"页面选择以下特定分类选项：


-  **访问群体范围** ：设置为"企业"、"组织"或"团队"。
    
-  **安全分类** ：设置为您输入的分类级别之一，例如"LBI"。
    
-  **过期日期** ：覆盖默认的过期日期，基于以前输入的分类。
    
"访问群体范围"和"网站分类"均可搜索，并且在爬网发生后都将具有与其关联的托管属性。然后您可以使用这些属性，通过网站集中的自定义隐藏列表来搜索特定类型的网站。此列表在  **SiteManagerImpl** 类的 [Core.SiteClassification.Common](https://github.com/OfficeDev/PnP/tree/dev/Scenarios/Core.SiteClassification/Core.SiteClassification.Common) 项目中实施。




```C#
private void CreateSiteClassificationList(ClientContext ctx)
{
  var _newList = new ListCreationInformation()
    {
    Title = SiteClassificationList.SiteClassificationListTitle,
    Description = SiteClassificationList.SiteClassificationDesc,
    TemplateType = (int)ListTemplateType.GenericList,
    Url = SiteClassificationList.SiteClassificationUrl,
    QuickLaunchOption = QuickLaunchOptions.Off
    };

  if(!ctx.Web.ContentTypeExistsById(SiteClassificationContentType.SITEINFORMATION_CT_ID))
    {
    // Content type.
    ContentType _contentType = ctx.Web.CreateContentType(SiteClassificationContentType.SITEINFORMATION_CT_NAME,
    SiteClassificationContentType.SITEINFORMATION_CT_DESC,
    SiteClassificationContentType.SITEINFORMATION_CT_ID,
    SiteClassificationContentType.SITEINFORMATION_CT_GROUP);

    FieldLink _titleFieldLink = _contentType.FieldLinks.GetById(new Guid("fa564e0f-0c70-4ab9-b863-0177e6ddd247"));
    titleFieldLink.Required = false;
    contentType.Update(false);

    // Key Field.
    ctx.Web.CreateField(SiteClassificationFields.FLD_KEY_ID, 
      SiteClassificationFields.FLD_KEY_INTERNAL_NAME, 
      FieldType.Text, 
      SiteClassificationFields.FLD_KEY_DISPLAY_NAME, 
      SiteClassificationFields.FIELDS_GROUPNAME);

    // Value field.
    ctx.Web.CreateField(SiteClassificationFields.FLD_VALUE_ID, 
      SiteClassificationFields.FLD_VALUE_INTERNAL_NAME, 
      FieldType.Text, 
      SiteClassificationFields.FLD_VALUE_DISPLAY_NAME, 
      SiteClassificationFields.FIELDS_GROUPNAME);

    // Add Key Field to content type.
    ctx.Web.AddFieldToContentTypeById(SiteClassificationContentType.SITEINFORMATION_CT_ID, 
      SiteClassificationFields.FLD_KEY_ID.ToString(), 
      true);

    // Add Value Field to content type.
    ctx.Web.AddFieldToContentTypeById(SiteClassificationContentType.SITEINFORMATION_CT_ID,
      SiteClassificationFields.FLD_VALUE_ID.ToString(),
      true);
    }

  var _list = ctx.Web.Lists.Add(_newList);

  list.Hidden = true;
  list.ContentTypesEnabled = true;
  list.Update();
  ctx.Web.AddContentTypeToListById(SiteClassificationList.SiteClassificationListTitle,
    SiteClassificationContentType.SITEINFORMATION_CT_ID, true);
  this.CreateCustomPropertiesInList(_list);
  ctx.ExecuteQuery();
  this.RemoveFromQuickLaunch(ctx, SiteClassificationList.SiteClassificationListTitle);

}


```

默认情况下，当您创建现成可用的列表或使用 CSOM 创建列表时，列表将在"最近"菜单中可用。但是列表需为隐藏状态。以下代码会将项目从"最近"菜单中删除。




```C#
private void RemoveFromQuickLaunch(ClientContext ctx, string listName)
  {
  Site _site = ctx.Site;
  Web _web = _site.RootWeb;

  ctx.Load(_web, x => x.Navigation, x => x.Navigation.QuickLaunch);
  ctx.ExecuteQuery();

  var _vNode = from NavigationNode _navNode in _web.Navigation.QuickLaunch
      where _navNode.Title == "Recent"
      select _navNode;

  NavigationNode _nNode = _vNode.First<NavigationNode>();
  ctx.Load(_nNode.Children);
  ctx.ExecuteQuery();
  var vcNode = from NavigationNode cn in _nNode.Children
     where cn.Title == listName
     select cn;
  NavigationNode _cNode = vcNode.First<NavigationNode>();
 _cNode.DeleteObject();
  ctx.ExecuteQuery();    
  }

```

Core.SiteClassification 示例提供了让网站管理员或具有权限的人员可以删除新列表的可能性。访问此页面时，将再次创建列表，但示例不会重新设置属性。您可以通过扩展示例，将列表上的权限修改为仅网站集管理员可以访问来避免这一点。或者，您可以使用 [Core.SiteEnumeration](https://github.com/OfficeDev/PnP/tree/dev/Scenarios/Core.SiteEnumeration) PnP 示例在列表上进行检查并相应地通知网站管理员。

列表验证检查也可在  **SiteManagerImpl** 类的 **Initialize** 成员中实施。




```C#
internal void Initialize(ClientContext ctx)
{
try {
       	 var _web = ctx.Web;
         var lists = _web.Lists;
         ctx.Load(_web);
         ctx.Load(lists, lc => lc.Where(l => l.Title == SiteClassificationList.SiteClassificationListTitle));
         ctx.ExecuteQuery();
                
          if (lists.Count == 0) {
                this.CreateSiteClassificationList(ctx); 
          }
      }
      catch(Exception _ex)
         {

         }
     }
}

```


 **注释**  有关详细信息，请参阅 [安装 SharePoint 加载项后在主机 Web 中创建列表并将其从现有资料列表中删除](http://blogs.technet.com/b/speschka/archive/2014/05/07/create-a-list-in-the-host-web-when-your-sharepoint-app-is-installed-and-remove-it-from-the-recent-stuff-list.aspx)。


## 将分类指示器添加到网站页面

您可以在网站页面中添加指示器以显示其分类。Core.SiteClassificationsample 说明如何将显示通知的图片嵌入到 **网站标题** 旁边。在 SharePoint 的早期版本中，这通过服务器端委派控件执行。尽管您可以使用 JavaScript 的自定义母版页，但此示例使用嵌入式 JavaScript 模式。当您在"编辑网站信息"页面中更改"网站策略"时，这会将网站指示器更改为对每个网站分类选项使用不同的背景颜色来显示一个小框。

以下方法在 Core.SiteClassificationWeb 项目、脚本和 classifier.js 中定义。图片存储在 Microsoft Azure 网站中。您需要更改硬编码的 URL 以匹配您的环境。




```C#
function setClassifier() {
    if (!classified)
    {
        var clientContext = SP.ClientContext.get_current();
        var query = "<View><Query><Where><Eq><FieldRef Name='SC_METADATA_KEY'/><Value Type='Text'>sc_BusinessImpact</Value></Eq></Where></Query><ViewFields><FieldRef Name='ID'/><FieldRef Name='SC_METADATA_KEY'/><FieldRef Name='SC_METADATA_VALUE'/></ViewFields></View>";
        var list = clientContext.get_web().get_lists().getByTitle("Site Information");
        clientContext.load(list);
        var camlQuery = new SP.CamlQuery();
        camlQuery.set_viewXml(query);
        var listItems = list.getItems(camlQuery);
        clientContext.load(listItems);

        clientContext.executeQueryAsync(Function.createDelegate(this, function (sender, args) {
            var listItemInfo;
            var listItemEnumerator = listItems.getEnumerator();

            while (listItemEnumerator.moveNext()) {
                listItemInfo = listItemEnumerator.get_current().get_item('SC_METADATA_VALUE');
                
                var pageTitle = $('#pageTitle')[0].innerHTML;
                if (pageTitle.indexOf("img") > -1) {
                    classified = true;
                }
                else {
                    var siteClassification = listItemInfo;
                    if (siteClassification == "HBI") {
                        var img = $("<a href='http://insertyourpolicy' target=_blank><img id=classifer name=classifer src='https://spmanaged.azurewebsites.net/content/img/hbi.png' title='Site contains personally identifiable information (PII), or unauthorized release of information on this site would cause severe or catastrophic loss to Contoso.' alt='Site contains personally identifiable information (PII), or unauthorized release of information on this site would cause severe or catastrophic loss to Contoso.'></a>");
                        $('#pageTitle').prepend(img);
                        classified = true;
                    }
                    else if (siteClassification == "MBI") {
                        var img = $("<a href='http://insertyourpolicy' target=_blank><img id=classifer name=classifer src='https://spmanaged.azurewebsites.net/content/img/mbi.png' title='Unauthorized release of information on this site would cause severe impact to Contoso.' alt='Unauthorized release of information on this site would cause severe impact to Contoso.'></a>");
                        $('#pageTitle').prepend(img);
                        classified = true;
                    }
                    else if (siteClassification == "LBI") {
                        var img = $("<a href='http://insertyourpolicy' target=_blank><img id=classifer name=classifer src='https://spmanaged.azurewebsites.net/content/img/lbi.png' title='Limited or no impact to Contoso if publically released.' alt='Limited or no impact to Contoso if publically released.'></a>");
                        $('#pageTitle').prepend(img);
                        classified = true;
                    }
                }
            }
        }));
    }

```


## 替代方法

您可以使用 [OfficeDevPnP.Core](https://github.com/OfficeDev/PnP/tree/96eff6153389d6d21358480878de9cc8fa21abab/OfficeDevPnP.Core) 中 ObjectPropertyBagEntry.cs 文件中的扩展方法 **Web.AddIndexedPropertyBagKey** ，来将分类值存储在网站属性包而非列表中。此方法使属性包可爬网或可搜索。


## 其他资源



- [SharePoint 网站设置解决方案](sharepoint-site-provisioning-solutions.md)
    
- [OfficeDevPnP.Core 示例](https://github.com/OfficeDev/PnP/tree/96eff6153389d6d21358480878de9cc8fa21abab/OfficeDevPnP.Core)
    
- [Core.SiteEnumeration 示例](https://github.com/OfficeDev/PnP/tree/dev/Scenarios/Core.SiteEnumeration)
    
