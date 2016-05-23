
# PnP 设置架构


 **上次修改时间：** 2015年10月15日


 **注释**  名称"SharePoint 相关应用程序"将更改为"SharePoint 外接程序"。在转换期间，某些 SharePoint 产品和 Visual Studio 工具的文档和 UI 仍可能使用术语"SharePoint 相关应用程序"。有关详细信息，请参阅 [Office 和 SharePoint 相关应用程序的新名称](05b07b04-6c8b-4b7e-bd86-e32c589dfead.md#bk_newname)。


您已从我们的 [PnP 设置框架](pnp-provisioning-framework.md)和其他任何位置了解到，设置模板的格式已脱离永久性格式，因此您可以使用您喜欢的任何格式。不过，由于将 XML 设置架构用于保留模板的情况比较常见，我们将提供有关如何使用 XML 架构来序列化和保存设置模板的一些其他信息。


 **重要信息**  设置架构显然支持设置模板的 XML 序列化，同时它还以 JSON 格式提供序列化结构。更常见的是，架构还提供了用于定义设置结构的模型。


## 设置架构资源

您可以在 GitHub 上获取设置架构及其支持文件： [PnP-Provisioning-Schema](https://github.com/OfficeDev/PnP-Provisioning-Schema)

第 9 频道提供了对设置架构展开讨论的长达 20 分钟的视频： [深入探究 PnP 设置引擎架构](https://channel9.msdn.com/blogs/OfficeDevPnP/Deep-dive-to-PnP-provisioning-engine-schema)。

GitHub 提供了示例架构： [PnP-Provisioning-Schema/Samples](https://github.com/OfficeDev/PnP-Provisioning-Schema/tree/master/Samples)。

以下代码块显示该架构的根元素和根的直接子元素。您可以在 GitHub 上查找设置架构文档： [PnP 设置架构](https://github.com/OfficeDev/PnP-Sites-Core/blob/dev/Core/Tools/OfficeDevPnP.Core.Tools.DocsGenerator/OfficeDevPnP.Core.Tools.DocsGenerator/ProvisioningSchema-2015-08.md)。




```
<pnp:ProvisioningTemplate
           xmlns:pnp="http://schemas.dev.office.com/PnP/2015/08/ProvisioningSchema"
           ID="xsd:ID"
           Version="xsd:decimal"
           ImagePreviewUrl="xsd:string"
           DisplayName="xsd:string"
           Description="xsd:string">
   <pnp:Properties />
   <pnp:SitePolicy />
   <pnp:RegionalSettings />
   <pnp:SupportedUILanguages />
   <pnp:AuditSettings />
   <pnp:PropertyBagEntries />
   <pnp:Security />
   <pnp:SiteFields />
   <pnp:ContentTypes />
   <pnp:Lists />
   <pnp:Features />
   <pnp:CustomActions />
   <pnp:Files />
   <pnp:Pages />
   <pnp:TermGroups />
   <pnp:ComposedLook />
   <pnp:Workflows />
   <pnp:SearchSettings />
   <pnp:Publishing />
   <pnp:AddIns />
   <pnp:Providers />
</pnp:ProvisioningTemplate>
```


## 其他资源



- [深入探究 PnP 设置引擎架构](https://channel9.msdn.com/blogs/OfficeDevPnP/Deep-dive-to-PnP-provisioning-engine-schema)
    
- [PnP 设置架构](https://github.com/OfficeDev/PnP-Sites-Core/blob/dev/Core/Tools/OfficeDevPnP.Core.Tools.DocsGenerator/OfficeDevPnP.Core.Tools.DocsGenerator/ProvisioningSchema-2015-08.md)
    
- GitHub 上的 [PnP-Provisioning-Schema/Samples](https://github.com/OfficeDev/PnP-Provisioning-Schema/tree/master/Samples)
    
