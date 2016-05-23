
# 在 SharePoint 提供程序托管的外接程序中提高性能
通过限制远程调用提升 SharePoint 提供程序托管的外接程序的性能。

 **上次修改时间：** 2015年7月31日

 _ **适用范围：** Office 365?| SharePoint 2013?| SharePoint Online_

 **注释**  名称"SharePoint 相关应用程序"将更改为"SharePoint 外接程序"。在转换期间，某些 SharePoint 产品和 Visual Studio 工具的文档和 UI 仍可能使用术语"SharePoint 相关应用程序"。有关详细信息，请参阅 [Office 和 SharePoint 相关应用程序的新名称](05b07b04-6c8b-4b7e-bd86-e32c589dfead.md#bk_newname)。

您可以通过限制对 SharePoint 进行远程调用的数量和频率来提升 SharePoint 提供程序托管的外接程序的性能。对主机网站的调用数量过多会降低性能。若要限制远程调用的数量，您可以执行 HTTP cookie 或 HTML5 本地存储。
[Performance.Caching](https://github.com/OfficeDev/PnP/tree/dev/Samples/Performance.Caching) 示例演示如何使用 HTTP cookie 和 HTML5 本地存储来缓存数据。此示例包括两个提供程序托管的外接程序，允许您查看您的用户配置文件的"关于我"部分、添加数据，并将其保存以供以后使用。外接程序不会更新用户配置文件信息；而是对其进行缓存，以便以后使用。一个示例使用 HTTP cookie 来缓存数据，另一个示例则使用 HTML5 本地存储。

## 使用 HTTP cookie 进行缓存

HTTP cookie 的起始页示例显示了文本框中用户配置文件的"关于我"部分中的信息。第二个文本框告诉您是否已创建新的 cookie 以及现有的 cookie 过期时间。Cookie 中存储的信息不能超过 4095 字节。


**图 1. HTTP Cookie 缓存中呈现的数据示例**

![在 HTTP Cookie 缓存示例中呈现的数据](media/c9427295-4242-48df-9aa8-392b58d7f4c6.png)位于 web 项目的脚本文件夹中的 app.js 文件定义"保存以供日后使用"按钮的行为。代码首先验证浏览器中是否已通过设置测试 cookie 来启用 cookie。如果已启用 cookie，代码将确定用户配置文件信息是否已存储在 cookie 中。如果未启用，则使用 JSOM 来查找"关于我"信息，将其存储在 cookie 中，然后在浏览器中显示此信息。

以下函数设置 cookie 及其过期日期。




```
function setCookie(key, value, expiry, path, domain, secure) {
    var todaysDate = new Date();
    todaysDate.setTime(todaysDate.getTime());

    if (expiry == "") { expiry = "1"; }

    // The following line sets for n number of days. For hours, remove * 24. For minutes, remove * 60 * 24.
    if (expiry) {
        expiry = expiry * 1000 * 60 * 60 * 24;
    }

    var newExpiry = new Date(todaysDate.getTime() + (expiry));

    document.cookie = key + "=" + escape(value) +
        ( ( expiry ) ? ";expires=" + newExpiry : "" ) +
        ( ( path ) ? ";path=" + path : "" ) +
        ( ( domain ) ? ";domain=" + domain : "" ) +
        ((secure) ? ";secure" : "");

    cachingStatus += "\n" + "Creating http cookie for AboutMe data...";
    cachingStatus += "\n" + "Cookie will expire " + newExpiry;
    $('#status').text(cachingStatus);
}

```


## 使用 HTML5 本地存储进行缓存

HTML5 本地存储的起始页示例显示您的用户配置文件信息的"关于我"部分中有关缓存数据的信息。文本框显示此信息和缓存信息的过期时间（如果有）。 


**图 2. 在 HTML 5 本地存储缓存中呈现的数据示例**
位于 web 项目的脚本文件夹中的 app.js 文件定义"保存以供日后使用"按钮的行为。外接程序首先会使用以下函数验证是否已启用本地存储。




```
isHtml5StorageSupported = function () {
    try {
        return 'localStorage' in window &amp;&amp; window['localStorage'] !== null;
    } catch (e) {
        return false;
    }
    return false;
}

```

如果支持本地存储，函数将确定该位置是否已存储用户配置文件信息。如果不支持，则使用 JSOM 查找"关于我"信息，将其存储在本地，然后在浏览器中显示相关信息。下面的代码将"关于我"信息存储在名为"aboutMeValue"的项中。




```
var aboutMeValue = personProperties.get_userProfileProperties()['AboutMe'];
    $('#aboutMeText').val(aboutMeValue);

    // Add to local storage.
    localStorage.setItem("aboutMeValue", aboutMeValue);
    setLocalStorageKeyExpiry("aboutMeValue");

    cachingStatus += "\n" + "Populated local storage with profile properties...";
    $('#status').val(cachingStatus);

```

"清除缓存"按钮将删除该项，查询用户的配置文件中的"关于我"信息，并创建新的本地存储项来存储该信息。默认情况下，外接程序不会设置过期时间，但 app.js 文件包含以下函数，该函数可设置缓存数据的过期时间。




```
function setLocalStorageKeyExpiry(key) {

    // Check for expiration config values.
    var expiryConfig = localStorage.getItem(expiryConfigKey);
    
    // Check for existing expiration stamp.
    var existingStamp = localStorage.getItem(key + expiryKeySuffix);    

    // Override cached setting if a user has entered a value that is different than what is stored.
    if (expiryConfig != null) {
                
        var currentTime = Math.floor((new Date().getTime()) / 1000);
        expiryConfig = parseInt(expiryConfig);
        
        var newStamp = Math.floor((currentTime + expiryConfig));
        localStorage.setItem(key + expiryKeySuffix, newStamp);
        
        // Log status to window.        
        cachingStatus += "\n" + "Setting expiration for the " + key + " key...";
        $('#status').val(cachingStatus);
    }    
    else {
       
    }
}

```

在查找本地存储项中存储的信息之前，该代码将使用  **isKeyExpired** 函数来确定该项是否已过期。有关详细信息，请参阅 [使用 SharePoint 提供程序托管的外接程序自定义用户体验](customize-the-ux-by-using-sharepoint-provider-hosted-add-ins.md)。


## 其他资源



- [SharePoint 2013 和 SharePoint Online 中的 UX 组件](ux-components-in-sharepoint-2013-and-sharepoint-online.md)
    
- [使用 SharePoint 提供程序托管的外接程序自定义用户体验](customize-the-ux-by-using-sharepoint-provider-hosted-add-ins.md)
    
- [Branding.UIElementPersonalization](https://github.com/OfficeDev/PnP/tree/dev/Samples/Branding.UIElementPersonalization)
    
- [Performance.Caching](https://github.com/OfficeDev/PnP/tree/dev/Samples/Performance.Caching)
    
