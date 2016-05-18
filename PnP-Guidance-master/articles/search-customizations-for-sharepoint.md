
# 搜索 SharePoint 的自定义项
通过使用基于搜索的网站目录、个性化的搜索或搜索配置的可移植性来创建自定义的 SharePoint 2013 和 SharePoint Online 搜索方案。 

 **上次修改时间：** 2015年8月11日

 _ **适用范围：** Office 365?| SharePoint 2013?| SharePoint Add-ins?| SharePoint Online_

 **注释**  名称"SharePoint 相关应用程序"将更改为"SharePoint 外接程序"。在转换期间，某些 SharePoint 产品和 Visual Studio 工具的文档和 UI 仍可能使用术语"SharePoint 相关应用程序"。有关详细信息，请参阅 [Office 和 SharePoint 相关应用程序的新名称](05b07b04-6c8b-4b7e-bd86-e32c589dfead.md#bk_newname)。


## 基于搜索的网站目录

您可以通过 SharePoint 搜索创建基于搜索的网站目录，而无需编写任何自定义代码。 

创建网站目录：


1. 创建网站目录显示模板。
    
2. 定义网站目录结果类型。
    
3. 创建结果页。
    
4. 编辑结果 Web 部件属性。
    
创建网站目录显示模板：


 **注释**  此过程使用与网站相关的显示模板而无需进行修改。如果您想要更改网站目录结果的显示方式，请修改您创建的显示模板。


1. 打开到"母版页样式库"的映射网络驱动器。有关详细信息，请参阅 [如何：将网络驱动器映射到 SharePoint 2013 母版页样式库](http://msdn.microsoft.com/zh-cn/library/office/jj733519%28v=office.15%29.aspx)。
    
2. 复制能够最好地映射您尝试执行的操作的显示模板 HTML 文件。对于网站目录方案，文件为 Item_Site.html 和 Item_Site_HoverPanel.html。这两个文件都位于映射网络驱动器的  `\Display Templates\Search` 文件夹中。
    
3. 重命名您复制的 Item_SiteDirectory.html 和 Item_SiteDirectory_HoverPanel.html 文件副本，如下所示。
    
    **图 1. 网站目录显示模版**

    ![网站目录显示模板](media/4c084d31-bad4-45f0-950b-ba214f9487f7.png)

4. 打开 Item_SiteDirectory.html 文件，并进行以下更改：
    
      - 将  `<title>` 标记值从"Site Item"更改为"Site Directory"。
    
  - 将  `<body>` 开始标记后的第一个 `<div>` 标记从 `<div id="Item_Site">` 更改为 `<div id="Item_SiteDirectory">`。
    
  - 将悬停面板显示模板 JavaScript 文件名从  `var hoverUrl = "~sitecollection/_catalogs/masterpage/Display Templates/Search/Item_Site_HoverPanel.js";` 更改为 `var hoverUrl = "~sitecollection/_catalogs/masterpage/Display Templates/Search/Item_SiteDirectory_HoverPanel.js";`
    
5. 打开 Item_SiteDirectory_HoverPanel.html 文件，并进行以下更改：
    
      - 将  `<body>` 开始标记后的 `<div>` 标记从 `<title>Site Hover Panel Test</title>`更改为 `<title>Site Directory Hover Panel</title>`
    
  - 将  `<title>` 标记从： `<div id="Item_Site_HoverPanel">`更改为： `<div id="Item_SiteDirectory_HoverPanel">`
    
定义网站目录结果类型：


1. 转到"网站设置">"搜索">"结果类型"，然后选择"新结果类型"。
    
2. 将新结果类型命名为"基本网站目录"。
    
3. 在"怎样显示这些结果"框中，选择"网站目录"。
    
    **图 2. 网站结果配置**

    ![示例网站结果配置](media/4f42a7c4-326b-4508-b9a7-e030ef34cd33.png)

4. 选择"保存"。
    
创建结果页：


1. 在"网站设置"菜单中，选择"网站内容"。
    
2. 选择"页面"。
    
3. 在"页面"库中，选择"文件">"新建文档">"页面"。
    
4. 在"创建页面"页上，为"标题"指定"网站目录"，为"URL 名称"指定"sitedirectory"。
    
5. 选择"创建"。
    
编辑结果 Web 部件属性：


1. 在"网站目录"页上，选择"设置">"编辑页面"。
    
2. 在"搜索结果 Web 部件"中，选择"Web 部件"菜单，然后选择"编辑 Web 部件"。
    
    **图 3. Web 部件菜单**

    ![图 3](media/1b01ac59-3bc1-42eb-a63d-61c2ad15cee4.png)

3. 在 Web 部件工具窗格中，选择"更改查询"以打开查询生成器。
    
4. 在"查询文本"字段中，输入以下命令： `ContentClass:STS_Web OR ContentClass:STS_Site path:http://<YourServer>`
    
5. 选择"测试查询"以确认语法正确。"搜索结果预览"窗格应显示在"查询文本"中为  _path_ 指定的站点内的子网站 。
    
    **图 4. 搜索结果 Web 部件查询生成器**

    ![搜索结果 Web 部件查询生成器](media/7b55c821-4772-4874-bbcb-c757e2723599.png)

6. 选择"确定"以关闭查询生成器。
    
7. 在"显示模板"中，选择"使用结果类型显示项目"。
    
8. 在"项目的结果类型"下拉列表中选择"基本网站目录"。
    
9. 在"外观"部分中，将"标题"更改为"我有权访问的网站"。
    
10. 选择"确定"，将更改保存到 Web 部件并关闭 Web 部件工具窗格。下图显示基于搜索的网站目录页的示例。
    
    **图 5. 基于 Contoso 搜索的网站目录示例**

    ![基于 Contoso 搜索的网站目录示例](media/5d1317d5-2e82-4819-b5a4-5bb515898a7b.png)


## 个性化的搜索结果

个性化搜索是针对提交搜索请求的用户显示搜索结果的功能。本部分介绍一些个性化的搜索方案以及如何实现这些方案。


### 您的新闻方案

在此方案中，您将创建一个搜索外接程序，以显示相关的内容，如面向用户的新闻和事件。


**图 6. 您的新闻个性化搜索方案**

![您的新闻个性化的搜索方案](media/188af4d1-b8bb-4212-bfdb-c29a13f30286.png)若要实现新闻方案，请使用 SharePoint 搜索结果 Web 部件和默认的显示模板，以显示新闻信息，包括标题、说明和滚动图像。显示前 10 个新闻项。当用户选择滚动图像、标题或"了解更多"链接时，将加载新闻文章页面。

或者，您可以使用查询 API（CSOM 或 REST）创建搜索外接程序。可以使用搜索外接程序属性使要显示的新闻项的数目变得可配置。

 另一种方法是使用查询 API 添加直接对页面布局检索搜索结果的查询 API 代码。

显示特定于用户的新闻和事件信息：



1. 基于用户配置文件属性（如业务部门、区域和语言）修改查询，以筛选新闻和事件的结果。
    
2. 检索标题、说明、滚动图像和新闻或事件项的 URL 属性。
    
3. 根据  **LastModifiedDate** 属性对组合性的新闻和事件实现排序逻辑。
    

### 即将发生的事件方案

在此方案中，搜索外接程序显示面向用户的相关事件。


**图 7. 即将发生的事件的个性化搜索方案**

![即将推出的事件个性化的搜索方案](media/35afac63-b4e9-4d94-abc1-a5fa11cc2dbf.png)若要实现此方案，您可以配置 SharePoint 搜索结果 Web 部件，将查询更改为仅检索即将发生的事件信息。为此，请对 Web 部件的查询文本指定  `ContentClass:STS_ListItem_Events`。若要更改事件结果的显示方式，请创建呈现事件信息的自定义显示模板。

您可以修改项目显示模板，以便当用户选择图像、标题或"了解更多"链接时，将加载事件信息页面。您还可以修改控件显示模板，以便当用户选择"查看更多"时，在 Web 部件中显示 10 个后续事件结果。

您还可以创建一个搜索外接程序，使其可以使用查询 API 来检索结果。默认情况下，您可以配置搜索外接程序以仅显示 10 个最新即将发生的事件，但可通过搜索外接程序属性将此设置变为可配置状态。 


### 特色新闻方案

在此方案中，搜索外接程序为您处于公司 Intranet 和部门登录页等位置的用户将搜索结果显示为特色内容。您可以使用外接程序部件（其中包含 HTML 的 jQuery 插件，其使用搜索 REST 服务或查询 CSOM 从 SharePoint 获取搜索结果并显示结果）来实现此方案。


### 个性化搜索的代码示例

[SharePoint 2013：个性化 SharePoint 外接程序中的搜索结果](http://code.msdn.microsoft.com/SharePoint-2013-Personalizi-fb6ddcf9)示例演示了一个基本搜索示例和一个使用搜索查询 CSOM 的个性化搜索结果的示例。基本搜索示例使用户能够提供要用于租户范围搜索的搜索筛选器。基于该用户提供的筛选器对站点进行搜索。

该示例将首先使用  **SharePointContextProvider** 类获取 SharePoint 上下文。




```
var spContext = SharePointContextProvider.Current.GetSharePointContext(Context);
```

接下来，它基于用户输入的内容生成相关查询。它将查询限制为网站集，然后调用  **ProcessQuery** 方法并在方法调用中传递上下文和查询。然后，将返回 **ProcessQuery** 结果作为结果表，使用 **FormatResults** 方法进行分析。




```
using (var clientContext = spContext.CreateUserClientContextForSPHost())
{
	string query = searchtext.Text + " contentclass:\"STS_Site\"";
	ClientResult<ResultTableCollection> results = ProcessQuery(clientContext, query);
	lblStatus1.Text = FormatResults(results);
}

```

 **ProcessQuery** 方法生成表示搜索查询的 **KeywordQuery** 对象。




```
KeywordQuery keywordQuery = new KeywordQuery(ctx);
keywordQuery.QueryText = keywordQueryValue;
keywordQuery.RowLimit = 500;
keywordQuery.StartRow = 0;
keywordQuery.SelectProperties.Add("Title");
keywordQuery.SelectProperties.Add("SPSiteUrl");
keywordQuery.SelectProperties.Add("Description");
keywordQuery.SelectProperties.Add("WebTemplate");
keywordQuery.SortList.Add("SPSiteUrl", Microsoft.SharePoint.Client.Search.Query.SortDirection.Ascending);

```

然后，通过调用  **ExecuteQuery_Client(Query)** 方法，将搜索查询提交到 SharePoint。结果将返回到 **ClientResult<T>** 对象。




```
SearchExecutor searchExec = new SearchExecutor(ctx);
ClientResult<ResultTableCollection> results = searchExec.ExecuteQuery(keywordQuery);
ctx.ExecuteQuery();

```

 **FormatResults** 方法循环访问结果并构造 HTML 表来显示结果值。




```
string responseHtml = "<h3>Results</h3>";
responseHtml += "<table>";
responseHtml += "<tr><th>Title</th><th>Site URL</th><th>Description</th><th>Template</th></tr>";
if (results.Value[0].RowCount > 0)
{
 foreach (var row in results.Value[0].ResultRows)
 {
   responseHtml += "<tr>";
   responseHtml += string.Format("<td>{0}</td>", row["Title"] != null ? row["Title"].ToString() : "");
   responseHtml += string.Format("<td>{0}</td>", row["SPSiteUrl"] != null ? row["SPSiteUrl"].ToString() : "");
   responseHtml += string.Format("<td>{0}</td>", row["Description"] != null ? row["Description"].ToString() : "");
   responseHtml += string.Format("<td>{0}</td>", row["WebTemplate"] != null ? row["WebTemplate"].ToString() : "");
   responseHtml += "</tr>";
 }
}
responseHtml += "</table>";

```

 **ResolveAdditionalFilter** 方法会检查"Apptest"。如果找到，将在搜索结果中返回任何类型的网站模板列表。如果找不到，则搜索结果中仅返回 STS web 模板。




```
private string ResolveAdditionalFilter(string aboutMeValue)
{
	if (!aboutMeValue.Contains("AppTest"))
	{
		return "WebTemplate=STS";
	}
	return "";
}

```

然后，该示例将构造查询，并调用  **ProcessQuery** 和 **FormatResults** 方法对搜索结果进行检索、设置格式和显示。




```
string query = "contentclass:\"STS_Site\" " + templateFilter;
ClientResult<ResultTableCollection> results = ProcessQuery(clientContext, query);
lblStatus2.Text = FormatResults(results);

```

在下图中，您可以看到此示例的 UI。


**图 8. 个性化的搜索结果示例 UI**

![个性化的搜索结果示例 UI](media/8e1c412b-71a7-42e2-b9f3-b7d045df3bde.png)


## 搜索配置可移植性

在 SharePoint 2013 和 SharePoint Online 中，您可以在网站集和网站之间导出和导入自定义的搜索配置设置。只能在 Search Service 应用程序 (SSA) 级别导出自定义的搜索配置设置，并且您必须使用搜索 API 以编程方式执行此操作。SharePoint UI 中不提供导出选项。

[SharePoint 2013： SharePoint Online 的导入和导出搜索设置](http://code.msdn.microsoft.com/SharePoint-2013-Import-and-6287b5ac)示例演示如何在控制台应用程序中使用搜索 CSOM 导入和导出对 SharePoint Online 网站的搜索设置。


### 可移植的配置设置

当您导出自定义的搜索配置设置时， SharePoint 2013 将以 XML 格式创建一个搜索配置文件。此搜索配置文件中包含您开始导出操作的 SSA、网站集或网站级别的所有可导出的自定义搜索配置设置。网站集的搜索配置文件不包含网站集中各个网站的搜索配置设置。

当您导入搜索配置文件时，SharePoint 2013 将创建并启用您开始导入操作的网站集或网站中的每个自定义的搜索配置设置。

表 1 列出了可以导出和导入的设置以及其他自定义的搜索配置设置上的所有依赖项。如果自定义的搜索配置设置依赖于在不同级别的自定义的搜索配置设置，则您必须导出和导入所有相关级别的设置。

 **表 1. 可以导出和导入的搜索设置**



|**配置设置**|**依赖项**|
|:-----|:-----|
|查询规则，包括结果块、已升级的结果和用户区段|结果源、结果类型、搜索架构、排名模型|
|结果源|搜索架构|
|结果类型|搜索架构、结果源、显示模板|
|搜索架构|无|
|排名模型|搜索架构|
可以从 SSA 导出自定义的搜索配置设置，并将这些设置导入网站集和网站中。但您不能将自定义的搜索配置设置导入到 SSA。您也不能导出默认的搜索配置设置。

在网站或网站集级别，您可以使用 SharePoint UI 导出或导入搜索配置设置。这些设置位于"网站设置"页的"搜索"部分中。 


**网站设置 - 搜索**

![网站设置 - 搜索](media/ada14bf4-d721-4974-ad50-a1f8ce01933f.png)在"网站集管理"部分也可进行这些设置。或者，您可以使用 SharePoint 2013 搜索 CSOM 以编程方式导入和导出这些设置。


### 搜索配置文件

下表列出了支持搜索配置的架构文件。有关架构格式的信息，请参阅 [SharePoint 搜索设置的可移植性架构](http://msdn.microsoft.com/zh-cn/library/office/dn627953%28v=office.15%29.aspx)。


 **注释**  您可以从 [http://download.microsoft.com/download/1/2/2/12204CDE-56A6-4B2F-9719-4EA25FDA7743/SP15_search_settings_portability_schema.zip](http://download.microsoft.com/download/1/2/2/12204CDE-56A6-4B2F-9719-4EA25FDA7743/SP15_search_settings_portability_schema.zip) 下载架构文件。

 **表 2. 搜索设置的可移植性架构**



|**架构**|**说明**|
|:-----|:-----|
|[SPS15XSDSearchSet1](http://msdn.microsoft.com/zh-cn/library/office/dn639116%28v=office.15%29.aspx)|指定表示结果源的 XML。|
|[SPS15XSDSearchSet2](http://msdn.microsoft.com/zh-cn/library/office/dn639118%28v=office.15%29.aspx)|指定表示用于管理 SSA 搜索实例的管理类型和成员的 XML。这包括结果项类型和属性规则设置。|
|[SPS15XSDSearchSet3](http://msdn.microsoft.com/zh-cn/library/office/dn639120%28v=office.15%29.aspx)|指定表示包括查询规则、结果源、托管属性、已爬网属性和排名模型的设置的 XML。|
|[SPS15XSDSearchSet4](http://msdn.microsoft.com/zh-cn/library/office/dn639117%28v=office.15%29.aspx)|指定表示用于其他架构中的枚举的 XML。|
|[SPS15XSDSearchSet5](http://msdn.microsoft.com/zh-cn/library/office/dn639119%28v=office.15%29.aspx)|指定表示用于其他架构中的枚举（如  **ResultType**）的 XML。|
|[SPS15XSDSearchSet6](http://msdn.microsoft.com/zh-cn/library/office/dn639115%28v=office.15%29.aspx)|指定表示用于  **Microsoft.Office.Server.Search.Administration** 架构中的枚举的 XML。|

### 将 CSOM 用于端口配置设置

您导入和导出搜索配置设置所需的 CSOM API 位于  **Microsoft.SharePoint.Client.Search.Portability** 命名空间的 **SearchConfigurationPortability** 类中。

以下代码示例演示如何导出网站的搜索配置设置。




```
private static void ExportSearchSettings(ClientContext context, string settingsFile)
{
   SearchConfigurationPortability sconfig = new SearchConfigurationPortability(context);
   SearchObjectOwner owner = new SearchObjectOwner(context, SearchObjectLevel.SPWeb);
   ClientResult<string> configresults = sconfig.ExportSearchConfiguration(owner);
   context.ExecuteQuery();
   string results = configresults.Value;
   System.IO.File.WriteAllText(settingsFile, results);
}

```

以下代码演示如何导入网站的搜索配置设置。




```
private static void ImportSearchSettings(ClientContext context, string settingsFile)
{
   SearchConfigurationPortability sconfig = new SearchConfigurationPortability(context);
   SearchObjectOwner owner = new SearchObjectOwner(context, SearchObjectLevel.SPWeb);
   sconfig.ImportSearchConfiguration(owner, System.IO.File.ReadAllText(settingsFile));
   context.ExecuteQuery();            
}

```


## 其他资源



- [在 SharePoint 2013 和 SharePoint Online 中搜索解决方案](search-solutions-in-sharepoint-2013-and-sharepoint-online.md)
    
- [SharePoint 2013：SharePoint Online 的导入和导出搜索设置](http://code.msdn.microsoft.com/SharePoint-2013-Import-and-6287b5ac)
    
- [SharePoint 2013：SharePoint 外接程序中的个性化搜索结果](http://code.msdn.microsoft.com/SharePoint-2013-Personalizi-fb6ddcf9)
    
