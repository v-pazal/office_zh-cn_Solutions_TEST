
# 在 SharePoint 2013 和 SharePoint Online 中搜索解决方案
了解有关 SharePoint 搜索体系结构、搜索 API 和搜索外接程序的信息。

 **上次修改时间：** 2015年12月7日

 _ **适用范围：** Office 365?| SharePoint 2013?| SharePoint Add-ins?| SharePoint Online_

 **注释**  名称"SharePoint 相关应用程序"将更改为"SharePoint 外接程序"。在转换期间，某些 SharePoint 产品和 Visual Studio 工具的文档和 UI 仍可能使用术语"SharePoint 相关应用程序"。有关详细信息，请参阅 [Office 和 SharePoint 相关应用程序的新名称](05b07b04-6c8b-4b7e-bd86-e32c589dfead.md#bk_newname)。

SharePoint 2013 中的 Search 在单个企业搜索平台中同时实现了 FAST Search Server 的易配置和易部署性以及可伸缩性和可扩展性。
SharePoint 2013 包括搜索平台中帮助您针对不同场景对搜索进行自定义的常用模式。例如：

- 视频搜索和对话搜索作为现成的 [垂直搜索](https://technet.microsoft.com/zh-cn/library/dn794227.aspx)提供。
    
- 主题页面和"按内容搜索"增强了 Web 内容管理功能和场景，例如搜索驱动的网站和知识管理网站。
    
- "我的任务"将项目任务集中在一起，确保用户可以在一个中央位置（即 OneDrive for Business 网站）跟踪在多个网站中分配的任务。
    

## SharePoint 2013 搜索体系结构

SharePoint 2013 中的搜索体系结构包括协同工作的组件和数据库。 

 **SharePoint 2013 中的搜索组件**



|**组件**|**说明**|
|:-----|:-----|
|爬网 |对内容源进行爬网，以收集属性和元数据并将这些信息发送到内容处理组件。|
|内容处理 |转换已爬网的项目并将其发送到索引组件。此组件还会将已爬网的属性映射到托管属性。|
|分析处理 |执行搜索分析和使用情况分析。|
|索引 |从内容处理组件接收已处理的项并将其写入搜索索引。此组件还处理传入的查询、检索来自搜索索引的信息并将结果集发送回查询处理组件。|
|查询处理 |分析传入的查询。这有助于优化精确度、回调和相关性。查询将发送到索引组件，此组件将返回查询的搜索结果集。|
|搜索管理 |运行系统处理以供搜索，并添加和初始化搜索组件的新实例。|
 **SharePoint 2013 中的搜索数据库**



|**数据库**|**说明**|
|:-----|:-----|
|爬网 |存储有关已爬网项目的跟踪信息和历史信息，例如文档和 URL。它还会存储上次爬网期间的相关信息，例如上次爬网时间、上次爬网 ID 和更新类型（添加、更新、删除）。|
|链接 |存储内容处理组件提取的未处理信息以及关于搜索单击的信息。分析处理组件将分析此信息。|
|分析报告 |存储使用情况分析的结果。|
|搜索管理 |存储搜索配置数据。|

### 爬网和内容处理

爬网过程从不同的内容源（例如 HTTP、文件共享和 SharePoint）开始。对于要添加到索引的内容，爬网程序使用连接器来告诉爬网程序如何连接到内容源和访问源中的内容项。爬网程序找到内容项后，将使用适用的格式处理程序来分析内容。

检索内容之后, 爬网组件将已爬网项目传递到内容处理组件，后者将处理项目并将其发送到索引组件。这包括文档分析、将已爬网的属性映射到其关联的托管属性和语言处理，如语言检测和实体提取。内容处理组件还会将关于链接和 URL 的信息写入到链接数据库。


### 查询处理

查询处理组件分析并处理搜索查询以优化精确度、回调和相关性，包括执行单词分解和词干提取等语言处理。已处理的查询将提交到索引组件，这会向查询处理组件返回基于已处理查询的结果集，后者将会处理该结果集。


### 搜索分析

SharePoint 分析内容本身（搜索分析）以及用户与之交互的方式（使用情况分析），然后使用此信息来改进搜索。

搜索分析涉及从链接数据库提取信息，例如链接、项目被点击的次数、作者文本、与人员相关的数据和元数据。搜索分析是确定相关性的基础。另一方面，使用情况分析是指分析通过事件存储从前端接收的使用情况日志信息。使用情况分析是使用情况和分析报告的基础。

分析结果将添加到搜索索引中的项目。此外，使用情况分析结果存储在分析报告数据库中。


## 用于自定义搜索体验的构建块

SharePoint 2013 和 SharePoint Online 中的 Search 包括允许您自定义搜索体验的新功能和改进功能。很多改进功能不会要求您编写代码。SharePoint 搜索包括 CSOM 和 REST API，可在您需要编写自定义代码时或者想要创建 外接程序 以访问 SharePoint 外的 SharePoint 搜索结果时提供帮助。


### 搜索中心网站

搜索中心是设置用于搜索的 SharePoint 网站。它是一个门户网站，您可在其中搜索组织 Intranet 上的内容，并提供一个集中式和高度自定义的用户界面。本文介绍了搜索中心的页面和 Web 部件以及搜索配置设置，您可以修改这些设置以便在无需编写大量代码的情况下创建自定义搜索体验，或者创建自定义搜索应用程序。

创建搜索中心网站时，SharePoint 会创建一个默认的搜索主页和一个默认的搜索结果页。此外，还会创建多个称为垂直搜索的页面。垂直搜索是可自定义以搜索特定内容类型的搜索结果页面（例如"人员"和"视频"），它们会显示针对特定内容类型或类进行筛选和格式化的搜索结果。

在搜索中心网站集的"页面"库中创建以下页面：


-  **default.aspx** - 搜索中心主页，以及最终用户输入查询的页面。
    
-  **results.aspx** - 搜索中心的默认搜索结果页面。它还是"所有内容"垂直搜索的搜索结果页面。
    
-  **peopleresults.aspx** -"人员"垂直搜索的搜索结果页面。
    
-  **conversationresults.aspx** -"对话"垂直搜索的搜索结果页面。
    
-  **videoresults.aspx** -"视频"垂直搜索的搜索结果页面。
    
-  **advanced.aspx** - 最终用户可以对搜索短语应用限制的搜索页面 — 例如，将搜索限制到某个精确的短语。
    
所有垂直搜索页面均包含搜索结果 Web 部件，尽管每个垂直搜索的 Web 部件配置有所不同。对于每个垂直搜索，搜索结果 Web 部件中的查询指向适用于该垂直搜索的特定结果源。例如，peopleresults.aspx 页面上搜索结果 Web 部件中的查询限制为"本地人员结果"结果源。了解 SharePoint 2013 中的默认垂直搜索的配置方式有助于您创建自己的垂直搜索或自定义搜索中心。

以下是可帮助您与搜索中心协同工作的其他资源：


- [在 SharePoint 2013 中设置搜索中心](https://technet.microsoft.com/zh-cn/library/dn794206%28v=office.15%29.aspx)
    
- [如何在 SharePoint 2013 中创建搜索中心网站集和启用内容爬网](https://technet.microsoft.com/zh-cn/library/dn794245%28v=office.15%29.aspx)
    
- [在 SharePoint Server 2013 中创建搜索中心网站](https://technet.microsoft.com/zh-cn/library/hh582314%28v=office.15%29.aspx)
    
- [管理搜索中心](https://technet.microsoft.com/zh-cn/library/jj851144%28v=office.15%29.aspx)
    
- [在 SharePoint Online 中管理搜索中心](http://office.microsoft.com/zh-cn/office365-sharepoint-online-enterprise-help/manage-the-search-center-in-sharepoint-online-HA103994122.aspx)
    

### 搜索中心 web 部件

搜索中心页面包含四种类型的 Web 部件：搜索框、搜索结果、搜索导航和精简。 


#### 搜索框 Web 部件

搜索框 Web 部件显示一个文本框，用户可在其中输入要搜索的文本。默认情况下，搜索框 Web 部件用于搜索中心主页 (default.aspx) 以及所有默认搜索结果页面（results.aspx、peopleresults.aspx、conversationresults.aspx 和 videoresults.aspx）。

您可以通过在 Web 部件工具窗格中编辑属性来自定义搜索框 Web 部件。这样您就可以执行以下操作：


- 更改搜索结果的显示位置。例如，您可以在自定义搜索结果 Web 部件或自定义搜索结果页面上显示结果。
    
- 关闭查询建议和人员建议。
    
- 显示搜索首选项页面和高级搜索页面的链接。
    
- 更改 Web 部件的显示模板。
    
有关详细信息，请参阅 [在 SharePoint Server 2013 中配置搜索框 Web 部件的属性](https://technet.microsoft.com/zh-cn/library/gg576963%28v=office.15%29.aspx)以及 [如何在 SharePoint Server 2013 中更改搜索框 Web 部件中显示的文本](https://technet.microsoft.com/zh-cn/library/dn794238%28v=office.15%29.aspx)。


#### 搜索结果 Web 部件

搜索结果 Web 部件显示搜索查询的结果。默认情况下，搜索结果 Web 部件用于所有默认的垂直搜索页面（results.aspx、peopleresults.aspx、conversationresults.aspx 和 videoresults.aspx）。搜索结果 Web 部件还会将搜索结果发送到精简 Web 部件和搜索导航 Web 部件，因此搜索结果页面上必须有搜索结果 Web 部件，才能使其他搜索 Web 部件运行。

您可以在 Web 部件工具窗格中编辑搜索结果 Web 部件属性，以更改搜索查询并更改搜索结果页面上结果的行为和外观。通过更改属性值，您可以执行以下操作：


- 更改结果源以指定应搜索的内容。
    
- 添加查询变量或属性筛选器以自定义不同用户或用户组的搜索结果。
    
- 在搜索结果中将项目升级或降级。
    
- 更改搜索结果的排序。
    
- 更改显示模板。
    
有关搜索结果 Web 部件的详细信息，请参阅 [在 SharePoint Server 2013 中配置搜索结果 Web 部件的属性](https://technet.microsoft.com/zh-cn/library/gg549987.aspx)以及 [如何配置搜索结果 Web 部件以使用 SharePoint 2013 中的新结果源](https://technet.microsoft.com/zh-cn/library/dn794200%28v=office.15%29.aspx)。


#### 搜索导航 Web 部件

搜索导航 Web 部件显示使用户可以在不同的垂直搜索（所有内容、人员、对话和视频）之间快速移动的链接。搜索导航 Web 部件搜索结果 Web 部件的搜索结果，以便当用户选择某个垂直搜索链接时，搜索结果可根据垂直搜索的设置方式进行筛选和显示。通过在 Web 部件工具窗格中编辑搜索导航 Web 部件属性，您可以如下自定义 Web 部件：


- 指定要从其获取结果的不同 Web 部件。
    
- 更改要显示的垂直搜索链接的数量。
    
- 更改 Web 部件的外观和布局。
    
此外，在功能区中，您可以选择"网站设置">"搜索设置"以进行以下更改：


- 更改链接显示名称。
    
- 更改链接顺序。
    

#### 精简 Web 部件

精简 Web 部件按类别（称为精简条件）筛选搜索结果。用户可以选择这些精简条件以缩小搜索结果范围。精简条件是标记为  _Refinable_ 和 _Queryable_ 的托管属性。有关这些设置的信息，请参阅 [SharePoint Server 2013 中的搜索架构概述](https://technet.microsoft.com/zh-cn/library/jj219669%28v=office.15%29.aspx)中的"托管属性设置概述"。您可以在 Web 部件工具窗格中编辑精简 Web 部件属性以指定以下内容：


- 将从哪个搜索结果 Web 部件筛选搜索结果。
    
- 要用于精简 Web 部件的精简条件。
    
- 应用于每个精简条件的显示模板。
    
- 精简 Web 部件的外观、布局和行为。
    
默认情况下，精简 Web 部件不会显示每个精简条件值的结果数量。您可以通过修改精简条件的显示模板来添加精简条件计数。有关此功能的详细信息，请参阅 [在 SharePoint Server 2013 中配置精简 Web 部件的属性](https://technet.microsoft.com/zh-cn/library/gg549985%28v=office.15%29.aspx)中的"将精简条件计数添加到精简 Web 部件"。

有关精简 Web 部件和精简条件的详细信息，请参阅 [规划在 SharePoint 2013 中的搜索结果页上使用精简条件](https://technet.microsoft.com/zh-cn/library/dn794223%28v=office.15%29.aspx)和 [如何向您的 SharePoint 2013 搜索结果页添加精简条件](https://technet.microsoft.com/zh-cn/library/dn794243%28v=office.15%29.aspx)。


### 结果源

结果源将搜索限制到特定内容或部分搜索结果。您可以通过指定以下内容来定义结果源：


- 要从其中获取搜索结果的搜索提供程序或源 URL，例如本地 SharePoint Search 服务的搜索索引。
    
- 用于获取搜索结果的协议，例如 [OpenSearch](http://www.opensearch.org/Home) 协议。
    
- 查询转换，可以将结果从指定搜索提供程序或 URL 缩小到特定的结果子集，例如，缩小到具有特定内容类型的结果集。
    
SharePoint 2013 提供 16 个预配置的结果源，包括与当前用户相关的本地 SharePoint 结果、对话和项目。您可以从"管理结果源"页面（"网站设置">"搜索">"结果源"）查看关于结果源的详细信息。然后，您可以使用以下两种方式之一，从"管理结果源"页面创建新的结果源：


- 选择"新建结果源"并选择所需的结果源。（有关详细信息，请参阅 [在 SharePoint Server 2013 中配置搜索的结果源](https://technet.microsoft.com/zh-cn/library/jj683115%28v=office.15%29.aspx)。）
    
- 指向现有结果源旁边的箭头，选择"复制"，然后根据需要修改副本并使用新名称将其保存。
    
结果源指定用于获取搜索结果的四个协议协议。如果结果源使用除"本地 SharePoint"以外的协议，结果源还必须指定要从其获取搜索结果的 URL。 

 **结果源协议及其提供程序**



|**结果源协议**|**提供程序**|**URL**|
|:-----|:-----|:-----|
|本地 SharePoint|本地 Search Service 的搜索索引。|N/A|
|远程 SharePoint|在另一个服务器场中托管的 Search Service 的搜索索引。|远程 SharePoint 服务器场的根网站集的地址。 |
|OpenSearch 1.0/1.1|使用 OpenSearch 协议提供搜索结果的外部搜索提供程序（如远程搜索引擎或源）。|使用 OpenSearch 协议的搜索提供程序的 RSS 源的 URL。|
|Exchange|Exchange Web 服务 (EWS)。|EWS URL。|
有关详细信息，请参阅以下资源：


- [了解 SharePoint Server 2013 中用于搜索的结果源](https://technet.microsoft.com/zh-cn/library/dn186229%28v=office.15%29.aspx)
    
- [关于结果源和联合](https://technet.microsoft.com/zh-cn/library/jj219577%28v=office.15%29.aspx#Section12)
    
- [了解结果来源](http://office.microsoft.com/zh-cn/sharepoint-server-help/understanding-result-sources-HA102848849.aspx?CTT=1)
    
- [管理结果源](http://office.microsoft.com/zh-cn/office365-sharepoint-online-enterprise-help/manage-result-sources-HA103639370.aspx)
    

### 查询规则

使用查询规则可自定义对您的用户特别重要的查询的搜索体验。在查询规则中，您可指定上下文、条件和关联操作。然后在指定上下文中，当查询满足指定条件时，搜索将执行关联操作，以改进搜索结果的相关性。

关于上下文，您可以限制以下查询规则查询： 


- 在指定的结果源上执行。
    
- 从指定的主题类别。
    
- 由匹配指定用户区段的用户执行。
    
下表列出了您可以指定的、导致查询规则运行的条件。

 **查询规则条件**



|**条件**|**说明**|
|:-----|:-----|
|查询完全匹配关键字|当查询完全匹配您指定的单词或短语时应用查询规则。|
|查询包含操作术语|当查询包含指示用户正在尝试执行某些操作的单个单词或短语时应用查询规则。术语必须位于查询的开头或结尾，可能是一个动词、命令或筛选器。|
|查询完全匹配词典|当查询与词典条目完全匹配时应用查询规则。此条目可以是术语库中的术语或人员姓名词典中的条目。 |
|源中更常用的查询|当用户的查询在除当前结果源以外的结果源中更常执行时应用查询规则。此条件使用对用户在各种结果源中输入的查询的分析。|
|通常单击的结果类型|当查询通常以用户选择特定结果类型的结果时应用查询规则。当您创建新的结果类型时，您可以指示记录这些将在查询规则中使用的选择。|
|高级查询文本匹配|查询匹配正则表达式时应用查询规则。它还允许您使用上述关键字、操作术语和词典条件的变体，但需使用更高级的控件。|
查询规则可以指定三种类型的操作：


- 添加出现在排名结果上方的"升级结果"（以前称为"最佳匹配"）。例如，对于查询"病假"，查询规则可以指定特定的升级结果，例如包含关于下班时间的公司政策的网站的链接。
    
- 添加一个或多个结果组（称为结果块）。结果块包含在某个方面与查询相关的一小部分结果。与单个结果一样，您可以升级结果块，并将其与其他搜索结果一起排名。 
    
- 通过更改查询来更改结果的排名。例如，对于包含"下载工具箱"的查询，查询规则可能将单词"下载"识别为操作术语，并升级指向您的 Intranet 上某个下载网站的搜索结果。
    
有关查询规则的详细信息，请参阅 [在 SharePoint Server 2013 中管理查询规则](https://technet.microsoft.com/zh-cn/library/jj871676.aspx)。


### 查询转换

为了提供适合某个用户查询的搜索结果，有时需修改查询。您可以使用查询转换来执行此操作。SharePoint 2013 中包含的默认垂直搜索（例如视频、人员和对话）均包含预定义的查询转换，以优化该垂直搜索的搜索体验。

您可以在三个位置配置查询转换：


- 在 Web 部件中，例如搜索结果 Web 部件。 
    
- 在查询规则中，其中指定了仅当满足特定条件时才会执行的特定操作。 
    
- 在查询用于获取搜索结果的结果源中。
    
用户查询首先由 Web 部件转换，然后由任何适用的查询规则转换，最后由结果源转换。在结果源中配置转换时，您知道转换更改不会被丢弃或覆盖，因为结果源会最后转换查询。您可以在 Web 部件或结果块中重复使用结果源查询转换，并且可以创建仅适用于特定结果源中的结果的查询规则或结果类型。

您可以使用查询生成器来帮助您编写和测试查询转换。您可以通过设置查询变量的临时测试值、运行查询和预览搜索结果，从查询生成器内测试查询。有关查询转换的详细信息，请参阅 [在 SharePoint 2013 中规划查询转换和结果排序](https://technet.microsoft.com/zh-cn/library/60a1110d-27dc-45d0-86e2-cddc72d072b2%28v=office.15%29)。


### 结果类型和显示模板

SharePoint 2013 搜索包括新的结果框架，可以轻松地自定义搜索结果的显示方式。现在，无需写入自定义 XSLT 来更改搜索结果的显示方式，您可以通过使用显示模板和结果类型来定制重要结果类型的外观。


#### 结果类型

要以不同方式显示搜索结果，搜索结果必须分类到不同的结果类型。结果类型是将一个搜索结果与另一个搜索结果区分开来的搜索结果分类。它由以下内容的集合组成：


-  **规则** - 要将每个搜索结果与其进行比较的一个或多个特征或条件，例如搜索结果的结果源或内容类型。规则条件可与等号、比较和逻辑运算符结合使用。
    
-  **属性** - 搜索结果的托管属性列表。您必须先将托管属性添加到属性列表，然后才能将托管属性映射到显示模板。
    
-  **显示模板** - 控制满足条件的所有结果在搜索结果页面上的显示和行为方式。
    
SharePoint 搜索包括几种默认结果类型。要进行查看，请转到"网站设置">"网站集管理">"搜索结果类型"。您不能编辑任何默认结果类型。可以通过复制和修改现有类型来创建新的结果类型。有关 SharePoint 2013 中包含的默认结果类型的详细信息，请参阅 [用于在 SharePoint Server 2013 中显示搜索结果的结果类型和显示模板](https://technet.microsoft.com/zh-cn/library/dn386874%28v=office.15%29.aspx)。


#### 显示模板

显示模板定义搜索结果的可视布局和行为。它们控制哪些托管属性将显示在搜索结果中及其显示方式。SharePoint 将显示模板存储在母版页样式库的"显示模板"文件夹的"搜索"子文件夹中。每个显示模板由两个文件组成： 


- 您可以在 HTML 编辑器中编辑的 HTML 版本的显示模板。
    
- SharePoint 使用的 .js 文件。 
    
使用显示模板时，您可以修改 HTML 文件。.js 文件由 SharePoint 创建和修改，您完全无法编辑此文件。

显示模板有两种主要类型：


-  **控件显示模板** - 确定如何显示结果的整体结构。
    
-  **项目显示模板** - 确定集中的每个结果如何显示。
    
控件显示模板提供了 HTML，以构建希望的搜索结果显示方式的整体布局。例如，控件显示模板可能会提供列表标头及开头和结尾的 HTML。控件显示模板仅在 Web 部件中呈现一次。

项显示模板提供的 HTML 决定每个项目在结果集中的显示方式。例如，项显示模板可能会提供列表项的 HTML，其中包含的图片和三行文本映射到与项目关联的不同托管属性。每个项目的项显示模板在结果集中呈现一次。因此，如果结果集包含十个项目，则项显示模板会创建 HTML 部分十次。

有关显示模板及其结构的详细信息，请参阅 [SharePoint 2013 Design Manager 显示模板](https://msdn.microsoft.com/zh-cn/library/jj945138.aspx)以及 [SharePoint 2013 页面模型概述](https://msdn.microsoft.com/zh-cn/library/jj191506.aspx)中的 [搜索驱动 Web 部件和显示模板](https://msdn.microsoft.com/zh-cn/library/jj191506.aspx)。

有关 SharePoint 2013 中可用的显示模板的详细信息，请参阅 [在 SharePoint Server 2013 中显示模板引用](https://technet.microsoft.com/zh-cn/library/jj944947.aspx)。


#### 自定义显示模板

如果您想自定义 SharePoint 中包含的显示模板，请从您想修改的显示模板复制内容以创建一个新的，然后自定义新版本。从现有显示模板的副本开始也是创建新显示模板最简单的方式，因为这确保了您从所有所需元素开始。

使用显示模板的另一个提示是将网络驱动器映射到母版页样式库。有关详细信息，请参阅[如何：将网络驱动器映射到 SharePoint 2013 母版页样式库]()。

用于显示模板的 HTML 文件是一个完整的 HTML 文档，具有  `head` 和 `body` 元素。在 `head` 元素中，有一个指定显示模板的显示名称的 `title` 元素。此标记中的文本是当您在 SharePoint UI 中执行配置时显示的内容，例如，当您配置结果类型时。

 `title` 元素后面有一个自定义文档属性元素 `mso:CustomDocumentProperties`。在项目显示模板中，此元素包含  `mso:ManagedPropertyMapping` 元素，SharePoint 搜索使用的托管属性可在此处映射到显示模板使用的值。下面是相关语法： `<display template reference name>:<managed property name>`，如以下示例中所示。




```HTML
<mso:ManagedPropertyMapping msdt:dt="string">'Title':'Title','Path':'Path','Description':'Description'
```

在  `body` 元素中，有一个 `script` 元素，您可在其中包含外部资源，例如显示模板外部的 CSS 文件或 JavaScript 文件。有关说明如何在脚本元素中包含外部资源的示例，请参阅 [SharePoint 2013 Design Manager 显示模板](http://msdn.microsoft.com/zh-cn/library/jj945138.aspx)中的"脚本块"部分。

下一个元素是  `div` 元素。您可在其中放置您希望作为显示模板一部分的任何 HTML 或脚本。熟悉显示模板结构的一种好办法是下载搜索结果的默认显示模板的副本，即控件显示模板 Control_SearchResults.html 和项目显示模板 Item_Default.html。


#### 结果类型和显示模板的其他资源

下面是显示模板和结果类型的一些其他资源：


- [在 SharePoint 2013 中自定义搜索结果类型](https://technet.microsoft.com/zh-cn/library/dn135239%28v=office.15%29.aspx)
    
- [如何更改搜索结果在 SharePoint Server 2013 中的显示方式](https://technet.microsoft.com/zh-cn/library/dn794204%28v=office.15%29.aspx)
    
- [了解搜索结果在 SharePoint Server 2013 中的显示方式](https://technet.microsoft.com/zh-cn/library/dn794212%28v=office.15%29.aspx)
    
- [了解项目显示模板和搜索词突出显示在 SharePoint Server 2013 中的工作原理](https://technet.microsoft.com/zh-cn/library/dn794249%28v=office.15%29.aspx)
    
- [如何在 SharePoint Server 2013 中创建新的结果类型](https://technet.microsoft.com/zh-cn/library/dn794234%28v=office.15%29.aspx)
    
- [如何在 SharePoint Server 2013 的"搜索结果 - 选项 1"中显示自定义托管属性的值](https://technet.microsoft.com/zh-cn/library/dn794209%28v=office.15%29.aspx)
    
- [如何在 SharePoint Server 2013 的"搜索结果 - 选项 2"中显示自定义托管属性的值](https://technet.microsoft.com/zh-cn/library/dn794240%28v=office.15%29.aspx)
    
- [如何在 SharePoint Server 2013 悬停面板中显示自定义托管属性的值](https://technet.microsoft.com/zh-cn/library/dn794247%28v=office.15%29.aspx)
    

## 查询 API 和搜索加载项

SharePoint 2013 搜索包括 .NET 和 JavaScript 客户端对象模型以及 REST 服务，您可通过此服务访问联机、内部和移动开发的搜索结果。 

 **搜索查询 API**



|**API**|**类库或架构路径**|**示例**|
|:-----|:-----|:-----|
|.NET CSOM|Microsoft.SharePoint.Client.Search.dll [SharePoint Server 2013 客户端组件 SDK 下载](http://www.microsoft.com/en-us/download/details.aspx?id=35585)%ProgramFiles%\Common Files\Microsoft Shared\web server extensions\15\ISAPI [SharePoint Online 客户端组件 SDK 下载](http://www.microsoft.com/en-us/download/details.aspx?id=42038)%ProgramFiles%\Common Files\Microsoft Shared\web server extensions\16\ISAPI|[SharePoint 2013：查询搜索与托管客户端](http://code.msdn.microsoft.com/Query-Search-with-the-649f1bc1)[对象模型（代码块）](http://code.msdn.microsoft.com/Query-Search-with-the-649f1bc1)|
|JavaScript CSOM|SP.search.js%ProgramFiles%\SharePoint Client Components\Scripts|[查询搜索和 JavaScript 客户端对象模型](http://code.msdn.microsoft.com/SharePoint-2013-Querying-a629b53b)（代码库） |
|REST 服务|http://server/_api/search/queryhttp://server/_api/search/postqueryhttp://server/_api/search/suggest|[SharePoint 2013：从 SharePoint 加载项使用搜索 REST 服务](http://code.msdn.microsoft.com/sharepoint/SharePoint-2013-Perform-a-1bf3e87d)（代码库） |

### 搜索查询 .NET CSOM

要使用查询 .NET CSOM，请创建 [T:Microsoft.SharePoint.Client.ClientContext](https://msdn.microsoft.com/zh-cn/library/office/microsoft.sharepoint.client.clientcontext.aspx) 类的一个新实例，此类位于 Microsoft.SharePoint.Client.dll 中的 [Microsoft.SharePoint.Client](https://msdn.microsoft.com/zh-cn/library/office/microsoft.sharepoint.client.aspx) 命名空间。然后使用 [Microsoft.SharePoint.Search.Client.Query](https://msdn.microsoft.com/en-us/library/office/microsoft.sharepoint.search.query.aspx) 命名空间中的查询对象模型。下面是一个简单示例。


```C#
using Microsoft.SharePoint.Client; 
using Microsoft.SharePoint.Client.Search.Query;
…
using (ClientContext clientContext = new ClientContext("http://intranet.contoso.com"))
{
    KeywordQuery keywordQuery = new KeywordQuery(clientContext);
    keywordQuery.QueryText = "Argument";
    SearchExecutor searchExecutor = new SearchExecutor(clientContext);
    ClientResult<ResultTableCollection> results = searchExecutor.ExecuteQuery(keywordQuery);
    clientContext.ExecuteQuery();
}
 
```

现在您可循环访问搜索结果。下面的示例列出了每个结果的标题。




```C#
foreach (var row in results.Value[0].ResultRows) 
{ 
    Console.WriteLine(row["Title"]); 
}
```


### 搜索查询 REST 服务

搜索查询 REST 服务支持 HTTP  **POST** 和 **GET** 请求。调用搜索查询 REST 服务时，您可指定请求的查询参数，搜索将使用这些查询参数构建搜索查询。使用 **GET** 请求，您可指定 URL 中的查询参数。对于 **POST** 请求，您可以 JavaScript 对象表示法 (JSON) 格式在正文中传递查询参数。

 **JSON GET 和 POST 请求**


|||
|:-----|:-----|
|**GET** 请求| `http://server/_api/search/query`|
|**POST** 请求| `http://server/_api/search/postquery`|
 **搜索 REST 服务的示例 GET 请求**



|**请求类型**|**请求 URL**|
|:-----|:-----|
|关键字|http://server/site/_api/search/query?querytext='{KQL Query}‘|
|选择属性|http://server/site/_api/search/query?querytext='test'&amp;selectproperties='Title,Rank'|
|排序|http://server/site/_api/search/query?querytext='test'&amp;sortlist='LastModifiedTime:descending'http://server/site/_api/search/query?querytext='test'&amp;sortlist='LastModifiedTime:descending,Rank:ascending'|
有关可用查询参数的完整列表以及如何使用这些参数，请参阅 [SharePoint Search REST API 概述](http://msdn.microsoft.com/library/8a4f7863-e4c1-4099-9189-a1894db36930%28Office.15%29.aspx)。有关示例代码，请参阅 [SharePoint 2013：使用 SharePoint 加载项中的搜索 REST 服务](https://code.msdn.microsoft.com/sharepoint/SharePoint-2013-Perform-a-1bf3e87d)。


### 搜索外接程序

SharePoint 外接程序（以前称为 SharePoint 相关应用程序）是自包含的功能，可扩展 SharePoint 网站的功能。搜索外接程序（以前称为搜索应用程序）是使用搜索功能的 SharePoint 外接程序。在搜索外接程序中，您可以使用搜索查询 API 检索搜索结果。此外，您还可以使用它将搜索配置从一个 SharePoint 安装分发到另一个。

有关如何设置开发环境以创建搜索外接程序的信息，请参阅 [设置 SharePoint 加载项的本地开发环境](http://msdn.microsoft.com/library/b0878c12-27c9-4eea-ae3b-7e79e5a8838d%28Office.15%29.aspx)或 [在 Office 365 上设置 SharePoint 加载项的开发环境](http://msdn.microsoft.com/library/b22ce52a-ae9e-4831-9b68-c9210af6dc54%28Office.15%29.aspx)。


#### 权限

搜索外接程序仅需用户级别权限，其中属性值为  _QueryAsUserIgnoreAppPrincipal_。此权限允许您根据用户的权限查询搜索外接程序。这意味着可能将根据用户的 ACL 返回搜索结果。要授予外接程序的权限以使用搜索，请执行以下操作：


1. 在"解决方案资源管理器"中，打开"AppManifest.xml"。
    
2. 在"权限"选项卡上，选择"搜索作用域"，然后选择"QueryAsUserIgnoreAppPrincipal"。
    
有关详细信息，请参阅 [SharePoint 2013 中的外接程序权限](http://msdn.microsoft.com/library/5f7a8440-3c09-4cf8-83ec-c236bfa2d6c4%28Office.15%29.aspx)。


#### 查询 API

您可以使用 .NET CSOM、JavaScript CSOM 或搜索 REST 服务来检索搜索外接程序中的搜索结果。以下示例说明如何使用查询 .NET CSOM 来检索搜索外接程序中的搜索结果。


```C#
var spContext = SharePointContextProvider.Current.GetSharePointContext(Context);
using (var clientContext = spContext.CreateUserClientContextForSPHost())
{
    KeywordQuery keywordQuery = new KeywordQuery(clientContext);
    keywordQuery.QueryText = "Argument";
    SearchExecutor searchExecutor = new SearchExecutor(clientContext);
    ClientResult<ResultTableCollection> results = searchExecutor.ExecuteQuery(keywordQuery);
    clientContext.ExecuteQuery();
}

```


## 本节内容


- [搜索 SharePoint 的自定义项](search-customizations-for-sharepoint.md)
    

## 其他资源



- [SharePoint 外接程序](http://msdn.microsoft.com/library/cd1eda9e-8e54-4223-93a9-a6ea0d18df70%28Office.15%29.aspx)
    
- [SharePoint 2013 中的搜索外接程序](http://msdn.microsoft.com/library/21682e45-dd78-4f3c-8f1e-cdd48de3bde2%28Office.15%29.aspx)
    
- [将搜索功能添加到 SharePoint 加载项](http://blogs.msdn.com/b/officeapps/archive/2013/05/30/add-search-capabilities-to-your-apps-for-sharepoint.aspx)
    
