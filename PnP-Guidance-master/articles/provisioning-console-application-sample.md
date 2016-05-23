
# 设置控制台应用程序示例
了解使用 PnP 设置引擎执行创建和保留操作的基础知识，然后将设置模板应用到新的 SharePoint 网站集。

 **上次修改时间：** 2015年10月15日


 **注释**  名称"SharePoint 相关应用程序"将更改为"SharePoint 外接程序"。在转换期间，某些 SharePoint 产品和 Visual Studio 工具的文档和 UI 仍可能使用术语"SharePoint 相关应用程序"。有关详细信息，请参阅 [Office 和 SharePoint 相关应用程序的新名称](05b07b04-6c8b-4b7e-bd86-e32c589dfead.md#bk_newname)。


为支持新的外接程序模型，Office 365 开发人员模式和做法程序 (PnP) 引入了一个设置框架，用户只需指向网站模型并将该模型保留为设置模板即可创建自定义网站模板，然后根据需要，将自定义模板应用到新的或现有的网站集。

在此示例中，我们将创建基本控制台应用程序，在设置 PnP 核心库的过程中实现类，从而使 PnP 设置引擎能够完成以下基本设置任务： 

- 设计并构建网站自定义模型。可以设计新的网站，也可以指向现有网站并将其另存为设置模板。
    
- 保存并将网站模型保留为设置模板，以便您可以重复使用它。
    
- 将设置模板应用到新的或现有的网站集。
    

 **注释**  本示例演练是 GitHub 上目前提供的示例 [PnP 设置引擎入门](https://github.com/OfficeDev/PnP/tree/master/Samples/Provisioning.Framework.Console)的辅助内容。您可以下载示例代码 (Program.cs) 和解决方案文件。Microsoft 第 9 频道网站上还提供了此过程长达 20 分钟的视频演示文稿（代码稍有不同）： [PnP 设置引擎入门](https://channel9.msdn.com/blogs/OfficeDevPnP/Getting-Started-with-PnP-Provisioning-Engine)。


## 远程设置演练

若要开始，需要创建一个 Visual Studio 项目。本示例中，为了简单起见，我们创建一个基本的控制台应用程序，以便使用设置框架的 PnP 核心库实现 PnP 设置引擎。但是，若要支持示例解决方案，必须下载并安装设置框架 PnP 核心库。请按照以下说明执行操作。


### 创建并准备一个 Visual Studio 项目


1. 启动 Visual Studio，然后选择"文件">"新建">"项目"。
    
2. 在"新建项目"向导中，选择"Visual C#"，然后选择"控制台应用程序"。
    
3. 将项目命名为"程序"，然后单击"确定"。（您可以将项目命名为任何名称，但请注意，此演练将引用"程序"作为项目名称。）
    
4. 下载并安装此处作为 NuGet 程序包提供的 PnP 核心库： [OfficeDevPnP.Core 程序包](https://www.nuget.org/profiles/officedevpnp)。
    
     **注释**  核心库共有两个版本。一个版本是面向 SharePoint Online 和 Office 365 的  **OfficeDevPnP.Core** 库。另一个版本是面向 SharePoint 2013 本地环境的 **OfficeDevPnP.Core（本地）** 。以下是可用选项的屏幕截图。
![两个核心库下载选项](media/5b1adb8d-52e5-4c67-8792-6ef0ae41d655.png)

在此示例演练中，我们使用的是面向 SharePoint Online 的第一个选项。

      1. 若是如此，请通过以下方式安装 NuGet 客户端：转到 [NuGet 客户端安装程序](http://docs.nuget.org/consume/installing-nuget)。
    
  2. NuGet 客户端安装完毕后，请运行"NuGet 程序包管理器"。右键单击 Visual Studio"解决方案资源管理器"中的"引用"节点，然后选择"管理 NuGet 程序包"。
    
  3. 在程序包管理器中，依次选择"联机"、"EntityFramework"，然后输入搜索词"OfficeDev"可显示 OfficeDevPnP.Core 库。
    
  4. 请按照说明下载和安装  **OfficeDevPnP.Core** 库，按照给定的说明执行相关操作。
    
    将 PnP 核心库引用到您的 Visual Studio 项目后，现有对象实例（例如， **web** 和 **list** 实例）上的所有库成员将作为 [扩展方法](https://msdn.microsoft.com/zh-cn/library/bb383977.aspx)供您使用。
    
5. 确保 Program.cs 文件中包含以下所有  `using` 语句。
    
  ```
  using Microsoft.SharePoint.Client;
using OfficeDevPnP.Core.Framework.Provisioning.Connectors;
using OfficeDevPnP.Core.Framework.Provisioning.Model;
using OfficeDevPnP.Core.Framework.Provisioning.ObjectHandlers;
using OfficeDevPnP.Core.Framework.Provisioning.Providers.Xml;
using System;
using System.Net;
using System.Security;
using System.Threading;
  ```

 **完成项目设置后的操作**

项目设置完毕后，您可以创建网站自定义项。您可以手动执行此操作，也可以指向包含您想要使用其设计的网站。只需将所选的网站设计另存为设置模板。或者，您可以结合使用这两种方法。在本示例中，我们将只指向现有网站并将其组合外观和网站项目（而非其内容）另存为设置模板。

若要开始，需要连接到要将其构建为设置模版模型的网站。我们先来收集连接信息，包括用户名、密码和源 URL 等信息。


### 创建、提取和保留设置模板


1. 从用户处收集连接信息。请注意，在程序的  `Main` 例程中，只需执行三个简单的操作：收集连接信息、获取设置模板，以及应用设置模板。复杂的工作通过我们在下方定义的 **GetProvisioningTemplate** 和 **ApplyProvisioningTemplate** 方法来完成。
    
  ```
   static void Main(string[] args)
        {
            ConsoleColor defaultForeground = Console.ForegroundColor;

            // Collect information 
            string templateWebUrl = GetInput("Enter the URL of the template site: ", false, defaultForeground);
            string targetWebUrl = GetInput("Enter the URL of the target site: ", false, defaultForeground);
            string userName = GetInput("Enter your user name:", false, defaultForeground);
            string pwdS = GetInput("Enter your password:", true, defaultForeground);
            SecureString pwd = new SecureString();
            foreach (char c in pwdS.ToCharArray()) pwd.AppendChar(c);

            // GET the template from existing site and serialize
            // Serializing the template for later reuse is optional
            ProvisioningTemplate template = GetProvisioningTemplate(defaultForeground, templateWebUrl, userName, pwd);

            // APPLY the template to new site from 
            ApplyProvisioningTemplate(defaultForeground, targetWebUrl, userName, pwd, template);

            // Pause and modify the UI to indicate that the operation is complete
            Console.ForegroundColor = ConsoleColor.White;
            Console.WriteLine("We're done. Press Enter to continue.");
            Console.ReadLine();
        }
  ```

2. 创建和使用专用  **GetInput** 方法以获取所需的用户凭据。
    
  ```
  private static string GetInput(string label, bool isPassword, ConsoleColor defaultForeground)
        {
            Console.ForegroundColor = ConsoleColor.Green;
            Console.WriteLine("{0} : ", label);
            Console.ForegroundColor = defaultForeground;

            string value = "";

            for (ConsoleKeyInfo keyInfo = Console.ReadKey(true); keyInfo.Key != ConsoleKey.Enter; keyInfo = Console.ReadKey(true))
            {
                if (keyInfo.Key == ConsoleKey.Backspace)
                {
                    if (value.Length > 0)
                    {
                        value = value.Remove(value.Length - 1);
                        Console.SetCursorPosition(Console.CursorLeft - 1, Console.CursorTop);
                        Console.Write(" ");
                        Console.SetCursorPosition(Console.CursorLeft - 1, Console.CursorTop);
                    }
                }
                else if (keyInfo.Key != ConsoleKey.Enter)
                {
                    if (isPassword)
                    {
                        Console.Write("*");
                    }
                    else
                    {
                        Console.Write(keyInfo.KeyChar);
                    }
                    value += keyInfo.KeyChar;

                }

            }
            Console.WriteLine("");

            return value;
        }
  ```

3. 指向作为设置模板模型的网站。使用单行代码执行源网站上的 GET 操作时，请查看我们定义的 GET 方法  `GetProvisioningTemplate()`。 
    
  ```
  private static ProvisioningTemplate GetProvisioningTemplate(ConsoleColor defaultForeground, string webUrl, string userName, SecureString pwd)
        {
            using (var ctx = new ClientContext(webUrl))
            {
                // ctx.Credentials = new NetworkCredentials(userName, pwd);
                ctx.Credentials = new SharePointOnlineCredentials(userName, pwd);
                ctx.RequestTimeout = Timeout.Infinite;

                // Just to output the site details
                Web web = ctx.Web;
                ctx.Load(web, w => w.Title);
                ctx.ExecuteQueryRetry();

                Console.ForegroundColor = ConsoleColor.White;
                Console.WriteLine("Your site title is:" + ctx.Web.Title);
                Console.ForegroundColor = defaultForeground;

                ProvisioningTemplateCreationInformation ptci
                        = new ProvisioningTemplateCreationInformation(ctx.Web);

                // Create FileSystemConnector to store a temporary copy of the template 
                ptci.FileConnector = new FileSystemConnector(@"c:\temp\pnpprovisioningdemo", "");
                ptci.PersistComposedLookFiles = true;
                ptci.ProgressDelegate = delegate(String message, Int32 progress, Int32 total)
                {
                    // Only to output progress for console UI
                    Console.WriteLine("{0:00}/{1:00} - {2}", progress, total, message);
                };

                // Execute actual extraction of the template
                ProvisioningTemplate template = ctx.Web.GetProvisioningTemplate(ptci);

                // We can serialize this template to save and reuse it
                // Optional step 
                XMLTemplateProvider provider =
                        new XMLFileSystemTemplateProvider(@"c:\temp\pnpprovisioningdemo", "");
                provider.SaveAs(template, "PnPProvisioningDemo.xml");

                return template;
            }
        }
  ```


    在上面的代码段中，我们还定义了  **ProvisioningTemplateCreationInformation** 变量 **pcti**。可以选择使用此变量存储有关可以将其与模板一起保存的项目信息。
    
4. 创建文件系统连接器对象，以便我们可以存储将应用到其他网站的设置模板的临时副本。
    
     **注释**  此步骤是可选的。它不要求您将设置模板序列化为 XML。在此阶段，该模板只是 C# 代码。您不仅可以选择序列化，还可以使用您想要的任何序列化格式。
5. 只需使用此单行代码即可执行设置模板提取操作。 `ProvisioningTemplate template = ctx.Web.GetProvisioningTemplate(ptci);`
    
6. （可选）保存并存储设置模板的序列化版本，以便您可以重复使用它。可以采用任何您喜欢的格式序列化设置模版。本示例中，我们已序列化为名为  **PnPProvisioningDemo.xml** 的 XML 文件。文件本身是一个 **XMLFileSystemTemplateProvider** 对象，我们已为该文件提供文件系统位置。
    
 **提取、保存和保留设置模板后的操作**

提取、保存和保留设置模板后，下一步也是最后一步，就是使用  **ApplyProvisioningTemplate** 方法将该设置模板应用到新的 SharePoint 网站集。


### 将设置模板应用到新的或现有的网站


1. 获取访问目标网站的凭据。
    
  ```
  // ctx.Credentials = new NetworkCredentials(userName, pwd);
                ctx.Credentials = new SharePointOnlineCredentials(userName, pwd);
                ctx.RequestTimeout = Timeout.Infinite;
  ```

2. 使用辅助方法  **ProvisioningTemplateApplyingInformation** 捕获使用 **ProvisioningTemplateCreationInformation** 方法存储的网站项目。
    
  ```
  ProvisioningTemplateApplyingInformation ptai
                        = new ProvisioningTemplateApplyingInformation();
                ptai.ProgressDelegate = delegate(String message, Int32 progress, Int32 total)
                {
                    Console.WriteLine("{0:00}/{1:00} - {2}", progress, total, message);
                };
  ```

3. 获取资产到文件连接器的关联。
    
  ```
  // Associate file connector for assets
                FileSystemConnector connector = new FileSystemConnector(@"c:\temp\pnpprovisioningdemo", "");
                template.Connector = connector;

  ```

4. （可选）由于设置模板是一个对象实例，因此，我们可以编写代码来实时自定义网站项目。在此实例中，我们将添加一个新的"联系人"列表。
    
  ```
   // Since template is actual object, we can modify this using code as needed
                template.Lists.Add(new ListInstance()
                {
                    Title = "PnP Sample Contacts",
                    Url = "lists/PnPContacts",
                    TemplateType = (Int32)ListTemplateType.Contacts,
                    EnableAttachments = true
                });
  ```

5.  再次使用一行代码将设置模版应用到新网站。
    
  ```
  web.ApplyProvisioningTemplate(template, ptai);
  ```


## 其他资源



- [PnP 设置框架](pnp-provisioning-framework.md)
    
- [PnP 设置引擎和核心库](pnp-provisioning-engine-and-the-core-library.md)
    
