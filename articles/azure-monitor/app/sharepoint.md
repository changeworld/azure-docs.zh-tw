---
title: 使用 Application Insights 監視 SharePoint 網站
description: 開始使用新的檢測金鑰監視新的應用程式
ms.topic: conceptual
ms.date: 07/11/2018
ms.openlocfilehash: 392c0e0ee46e8acd540d498cbda1d240611d182e
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87326523"
---
# <a name="monitor-a-sharepoint-site-with-application-insights"></a>使用 Application Insights 監視 SharePoint 網站

Azure Application Insights 會監視應用程式的可用性、效能和使用情況。 您將在這裡深入了解如何針對 SharePoint 網站進行設定。

> [!NOTE]
> 基於安全性考慮，您無法直接將本文所述的腳本新增至 SharePoint 現代化 UX 中的網頁。 或者，您可以使用[Sharepoint Framework （SPFx）](/sharepoint/dev/spfx/extensions/overview-extensions)來建立自訂延伸模組，以便用來在 SharePoint 網站上安裝 Application Insights。 若要深入瞭解，請參閱[如何建立已從頭安裝 AppInsights 的 SPFx 延伸模組解決方案](https://github.com/microsoft/ApplicationInsights-JS/tree/master/SPO#how-to-create-a-spfx-extension-solution-with-appinsights-installed-from-scratch)或[觀看範例](https://github.com/microsoft/ApplicationInsights-JS/tree/master/SPO/AppInsightsExtensionSolutionSample)。 

## <a name="create-an-application-insights-resource"></a>建立 Application Insights 資源
在 [Azure 入口網站](https://portal.azure.com)中，建立新的 Application Insights 資源。 選擇 ASP.NET 做為應用程式類型。

![按一下 [屬性]，選取金鑰，然後按下 CTRL+C](./media/sharepoint/001.png)

開啟的視窗是您要查看您的應用程式效能和使用量資料的位置。 若要在下次登入 Azure 時回到此位置，您應該會在開始畫面上發現它的磚。 或者按一下 [瀏覽] 以尋找它。

## <a name="add-the-script-to-your-web-pages"></a>將指令碼新增至您的網頁

```HTML
<!-- 
To collect user behavior analytics tools about your application, 
insert the following script into each page you want to track.
Place this code immediately before the closing </head> tag,
and before any other scripts. Your first data will appear 
automatically in just a few seconds.
-->
<script type="text/javascript">
var sdkInstance="appInsightsSDK";window[sdkInstance]="appInsights";var aiName=window[sdkInstance],aisdk=window[aiName]||function(n){var o={config:n,initialize:!0},t=document,e=window,i="script";setTimeout(function(){var e=t.createElement(i);e.src=n.url||"https://az416426.vo.msecnd.net/scripts/b/ai.2.min.js",t.getElementsByTagName(i)[0].parentNode.appendChild(e)});try{o.cookie=t.cookie}catch(e){}function a(n){o[n]=function(){var e=arguments;o.queue.push(function(){o[n].apply(o,e)})}}o.queue=[],o.version=2;for(var s=["Event","PageView","Exception","Trace","DependencyData","Metric","PageViewPerformance"];s.length;)a("track"+s.pop());var r="Track",c=r+"Page";a("start"+c),a("stop"+c);var u=r+"Event";if(a("start"+u),a("stop"+u),a("addTelemetryInitializer"),a("setAuthenticatedUserContext"),a("clearAuthenticatedUserContext"),a("flush"),o.SeverityLevel={Verbose:0,Information:1,Warning:2,Error:3,Critical:4},!(!0===n.disableExceptionTracking||n.extensionConfig&&n.extensionConfig.ApplicationInsightsAnalytics&&!0===n.extensionConfig.ApplicationInsightsAnalytics.disableExceptionTracking)){a("_"+(s="onerror"));var p=e[s];e[s]=function(e,n,t,i,a){var r=p&&p(e,n,t,i,a);return!0!==r&&o["_"+s]({message:e,url:n,lineNumber:t,columnNumber:i,error:a}),r},n.autoExceptionInstrumented=!0}return o}(
{
  instrumentationKey:"INSTRUMENTATION_KEY"
}
);(window[aiName]=aisdk).queue&&0===aisdk.queue.length&&aisdk.trackPageView({});
</script>
```

將腳本插入 &lt; &gt; 您要追蹤之每個頁面的/head 標記之前。如果您的網站有主版頁面，您可以將腳本放在該處。 例如，在 ASP.NET MVC 專案中，可放在 View\Shared\_Layout.cshtml 中

指令碼包含檢測金鑰，會將遙測導向您的 Application Insights 資源。

### <a name="add-the-code-to-your-site-pages"></a>將程式碼加入至網站頁面
#### <a name="on-the-master-page"></a>在主要頁面上
如果您可以編輯網站的主要頁面，將會提供網站中每一頁面的監視。

簽出主要頁面，並且使用 SharePoint Designer 或任何其他編輯器來編輯。

![螢幕擷取畫面：顯示如何使用 Sharepoint 設計工具或其他編輯器編輯主版頁面。](./media/sharepoint/03-master.png)

將程式碼新增至 </head> 的相片或視訊。 

![螢幕擷取畫面：顯示要將程式碼新增至網站頁面的位置。](./media/sharepoint/04-code.png)

#### <a name="or-on-individual-pages"></a>或在個別的頁面上
若要監視一組有限的頁面，將指令碼個別加入至每個頁面。 

插入網頁組件並在其中嵌入程式碼片段。

![螢幕擷取畫面：顯示新增腳本以監視有限的一組頁面。](./media/sharepoint/05-page.png)

## <a name="view-data-about-your-app"></a>檢視應用程式相關的資料
重新部署您的應用程式。

返回 [Azure 入口網站](https://portal.azure.com)中的應用程式刀鋒視窗。

前幾個事件將出現在搜尋中。 

![螢幕擷取畫面：顯示您可以在應用程式中查看的新資料。](./media/sharepoint/09-search.png)

如果您預期有更多資料，請在幾秒之後按一下 [重新整理]。

## <a name="capturing-user-id"></a>擷取使用者識別碼
標準網頁程式碼片段不會從 SharePoint 擷取使用者識別碼，但只要稍做修改就能執行此作業。

1. 從 Application Insights 中的 [Essentials] 下拉式清單複製您應用程式的檢測金鑰。 

    ![螢幕擷取畫面：顯示如何從 Application Insights 中的 [基本功能] 下拉式清單複製應用程式的檢測。](./media/sharepoint/02-props.png)

1. 使用檢測金鑰替換下列程式碼片段中的 'XXXX'。 
2. 在您的 SharePoint 應用程式中內嵌指令碼，而非您從入口網站取得的程式碼片段。

```


<SharePoint:ScriptLink ID="ScriptLink1" name="SP.js" runat="server" localizable="false" loadafterui="true" /> 
<SharePoint:ScriptLink ID="ScriptLink2" name="SP.UserProfiles.js" runat="server" localizable="false" loadafterui="true" /> 

<script type="text/javascript"> 
var personProperties; 

// Ensure that the SP.UserProfiles.js file is loaded before the custom code runs. 
SP.SOD.executeOrDelayUntilScriptLoaded(getUserProperties, 'SP.UserProfiles.js'); 

function getUserProperties() { 
    // Get the current client context and PeopleManager instance. 
    var clientContext = new SP.ClientContext.get_current(); 
    var peopleManager = new SP.UserProfiles.PeopleManager(clientContext); 

    // Get user properties for the target user. 
    // To get the PersonProperties object for the current user, use the 
    // getMyProperties method. 

    personProperties = peopleManager.getMyProperties(); 

    // Load the PersonProperties object and send the request. 
    clientContext.load(personProperties); 
    clientContext.executeQueryAsync(onRequestSuccess, onRequestFail); 
} 

// This function runs if the executeQueryAsync call succeeds. 
function onRequestSuccess() { 
var appInsights=window.appInsights||function(config){
function s(config){t[config]=function(){var i=arguments;t.queue.push(function(){t[config].apply(t,i)})}}var t={config:config},r=document,f=window,e="script",o=r.createElement(e),i,u;for(o.src=config.url||"//az416426.vo.msecnd.net/scripts/a/ai.0.js",r.getElementsByTagName(e)[0].parentNode.appendChild(o),t.cookie=r.cookie,t.queue=[],i=["Event","Exception","Metric","PageView","Trace"];i.length;)s("track"+i.pop());return config.disableExceptionTracking||(i="onerror",s("_"+i),u=f[i],f[i]=function(config,r,f,e,o){var s=u&&u(config,r,f,e,o);return s!==!0&&t["_"+i](config,r,f,e,o),s}),t
    }({
        instrumentationKey:"XXXX"
    });
    window.appInsights=appInsights;
    appInsights.trackPageView(document.title,window.location.href, {User: personProperties.get_displayName()});
} 

// This function runs if the executeQueryAsync call fails. 
function onRequestFail(sender, args) { 
} 
</script> 


```



## <a name="next-steps"></a>後續步驟
* [Web 測試](./monitor-web-app-availability.md) 。
* [Application Insights](./app-insights-overview.md) 。

<!--Link references-->

