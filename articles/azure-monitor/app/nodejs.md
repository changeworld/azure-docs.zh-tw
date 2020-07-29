---
title: 使用 Azure Application Insights 監視 Node.js 服務 | Microsoft Docs
description: 使用 Application Insights 監視 Node.js 服務的效能和診斷問題。
ms.topic: conceptual
ms.date: 06/01/2020
ms.custom: devx-track-javascript
ms.openlocfilehash: c6a1a030829f128c4369e99efcd56a416390afc6
ms.sourcegitcommit: f353fe5acd9698aa31631f38dd32790d889b4dbb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/29/2020
ms.locfileid: "87371612"
---
# <a name="monitor-your-nodejs-services-and-apps-with-application-insights"></a>使用 Application Insights 監視 Node.js 服務和應用程式

[Application Insights](./app-insights-overview.md)在部署後監視您的後端服務和元件，以協助您探索並快速診斷效能和其他問題。 您可以將 Application Insights 用於您的資料中心、Azure Vm 和 web 應用程式，甚至是其他公用雲端中裝載的 Node.js 服務。

若要接收、儲存和探索您的監視資料，請在您的程式碼中包含 SDK，然後在 Azure 中設定對應的 Application Insights 資源。 SDK 會將資料傳送至該資源，進行進一步的分析和探索。

Node.js SDK 可以自動監視傳入和傳出 HTTP 要求、例外狀況、和一些系統計量。 從0.20 版開始，SDK 也可以監視一些常見[的協力廠商套件](https://github.com/microsoft/node-diagnostic-channel/tree/master/src/diagnostic-channel-publishers#currently-supported-modules)，例如 MongoDB、MySQL 和 Redis。 與傳入 HTTP 要求相關的所有事件都會相互關聯，以進行快速疑難排解。

您可以使用 TelemetryClient API 手動檢測和監視您的應用程式及系統的其他層面。 我們將在本文稍後更詳細說明 TelemetryClient API。

## <a name="get-started"></a>開始使用

完成下列工作來設定應用程式或服務的監視。

### <a name="prerequisites"></a>先決條件

開始之前，請確定您有 Azure 訂用帳戶或[免費取得一個新訂用帳戶][azure-free-offer]。 如果您的組織已經有 Azure 訂用帳戶，系統管理員可以依照 [這些指示][add-aad-user] 將您新增至該訂用帳戶。

[azure-free-offer]: https://azure.microsoft.com/free/
[add-aad-user]: ../../active-directory/fundamentals/add-users-azure-active-directory.md

### <a name="set-up-an-application-insights-resource"></a><a name="resource"></a>設定 Application Insights 資源

1. 登入 [Azure 入口網站][portal]。
2. [建立 Application Insights 資源](create-new-resource.md)

### <a name="set-up-the-nodejs-sdk"></a><a name="sdk"></a> 設定 Node.js SDK

在您的應用程式中包含 SDK，以便蒐集資料。

1. 從新建立的資源複製資源的檢測金鑰（也稱為*ikey*）。 Application Insights 使用 ikey 來將資料對應至您的 Azure 資源。 在 SDK 可以使用您的 ikey 之前，您必須在環境變數或程式碼中指定 ikey。  

   ![複製檢測金鑰](./media/nodejs/instrumentation-key-001.png)

2. 接著透過 package.json，將 Node.js SDK 程式庫新增至您應用程式的相依性。 從您應用程式的根資料夾，執行︰

   ```bash
   npm install applicationinsights --save
   ```

    > [!NOTE]
    > 如果您使用 TypeScript，請勿安裝個別的 "typings" 套件。 此 NPM 套件有內建 typings。

3. 在您的程式碼中明確地載入此程式庫。 因為 SDK 會將檢測插入其他許多程式庫中，請儘早載入程式庫，甚至插入在其他 `require` 陳述式之前。

   ```javascript
   let appInsights = require('applicationinsights');
   ```
4.  您也可以透過環境變數提供 ikey `APPINSIGHTS_INSTRUMENTATIONKEY` ，而不是以手動方式將它傳遞至 `setup()` 或 `new appInsights.TelemetryClient()` 。 這種做法可讓您將 ikeys 保留在認可的原始程式碼之外，而您可以針對不同的環境指定不同的 ikey。 若要設定手動呼叫 `appInsights.setup('[your ikey]');` 。

    如需其他組態選項，請參閱下列各節。

    藉由設定 `appInsights.defaultClient.config.disableAppInsights = true`，您可以嘗試 SDK，而不需要傳送遙測。

5. 藉由呼叫來開始自動收集和傳送資料 `appInsights.start();` 。

### <a name="monitor-your-app"></a><a name="monitor"></a>監視您的應用程式

SDK 會自動收集有關 Node.js 執行時間和一些常見協力廠商模組的遙測。 使用您的應用程式來產生一些資料。

然後，在 [Azure 入口網站][portal]中，前往您稍早建立的 Application Insights 資源。 在**概觀時間表**中，尋找您的前幾個資料點。 若要查看更詳細的資料，請在圖表中選取不同的元件。

若要查看針對您的應用程式探索到的拓撲，您可以使用[應用程式對應](app-map.md)。

#### <a name="no-data"></a>無資料

因為 SDK 會分批提交資料，所以項目可能會延遲顯示在入口網站中。 如果未在您的資源中看到資料，請嘗試以下一些修正方式：

* 繼續使用應用程式。 採取更多動作以產生更多遙測。
* 按一下入口網站資源檢視中的 [重新整理]****。 圖表會自行定期重新整理，但手動重新整理會強制圖表立即重新整理。
* 確認[所需的連出連接埠](./ip-addresses.md)已開啟。
* 使用 [搜尋](./diagnostic-search.md) 來尋找特定的事件。
* 請查看[常見問題][FAQ]。

## <a name="basic-usage"></a>基本使用方式

針對預設的 HTTP 要求集合、熱門的協力廠商程式庫事件、未處理的例外狀況和系統計量：

```javascript

let appInsights = require("applicationinsights");
appInsights.setup("[your ikey]").start();

```

> [!NOTE]
> 如果在環境變數中設定檢測金鑰 `APPINSIGHTS_INSTRUMENTATIONKEY` ， `.setup()` 可以不使用引數呼叫。 這可讓您輕鬆地針對不同的環境使用不同的 ikey。

載入 `require("applicationinsights")` 其他封裝之前，請儘早在您的腳本中載入 Application Insights 程式庫。 這是必要的，因此 Application Insights 程式庫可以準備後續的封裝以進行追蹤。 如果您在執行類似準備的其他程式庫時遇到衝突，請嘗試在這些程式庫之後載入 Application Insights 程式庫。

由於 JavaScript 處理回呼的方式，需要額外的工作來追蹤外部相依性和之後回呼的要求。 預設會啟用此額外的追蹤;藉由呼叫來停用它， `setAutoDependencyCorrelation(false)` 如下列設定一節[所](#sdk-configuration)述。

## <a name="migrating-from-versions-prior-to-022"></a>從0.22 之前的版本進行遷移

版本0.22 之前和之後的版本之間有重大變更。 這些變更的設計目的是要與其他 Application Insights Sdk 保持一致，並允許未來的擴充性。

一般來說，您可以使用下列各項進行遷移：

- 將的參考取代為 `appInsights.client` `appInsights.defaultClient` 。
- 將的參考取代為 `appInsights.getClient()``new appInsights.TelemetryClient()`
- 將所有引數取代為 client. track * 方法，並將包含指名屬性的單一物件當做引數。 查看 IDE 的內建類型提示或[TelemetryTypes](https://github.com/Microsoft/ApplicationInsights-node.js/tree/develop/Declarations/Contracts/TelemetryTypes) ，以取得每個遙測類型的例外物件。

如果您存取 SDK 設定函式，但未將它們連結至 `appInsights.setup()` ，您現在可以在 `appInsights.Configurations` （例如，）找到這些函式 `appInsights.Configuration.setAutoCollectDependencies(true)` 。 在下一節中，檢查預設設定的變更。

## <a name="sdk-configuration"></a>SDK 組態

`appInsights`物件提供一些設定方法。 它們會以其預設值列在下列程式碼片段中。

```javascript
let appInsights = require("applicationinsights");
appInsights.setup("<instrumentation_key>")
    .setAutoDependencyCorrelation(true)
    .setAutoCollectRequests(true)
    .setAutoCollectPerformance(true, true)
    .setAutoCollectExceptions(true)
    .setAutoCollectDependencies(true)
    .setAutoCollectConsole(true)
    .setUseDiskRetryCaching(true)
    .setSendLiveMetrics(false)
    .setDistributedTracingMode(appInsights.DistributedTracingModes.AI)
    .start();
```

若要使服務中的事件完全相互關聯，請務必設定 `.setAutoDependencyCorrelation(true)`。 利用設定這個選項，可讓 SDK 追蹤 Node.js 中所有非同步回呼的內容。

請參閱 IDE 內建型別提示中的說明，或[applicationinsights](https://github.com/microsoft/ApplicationInsights-node.js/blob/develop/applicationinsights.ts) ，以取得這些控制項的詳細資訊，以及選擇性的次要引數。

> [!NOTE]
>  根據預設， `setAutoCollectConsole` 會設定為*排除*對 `console.log` （和其他主控台方法）的呼叫。 只會收集支援的協力廠商記錄器的呼叫（例如，winston 和 bunyan）。 您可以使用來變更此行為，以包含對 `console` 方法的呼叫 `setAutoCollectConsole(true, true)` 。

### <a name="sampling"></a>取樣

根據預設，SDK 會將所有收集的資料傳送至 Application Insights 服務。 如果您收集大量資料，您可能會想要啟用取樣以減少傳送的資料量。 `samplingPercentage`在用戶端的物件上設定欄位 `config` 以完成此動作。 將設定 `samplingPercentage` 為100（預設值）表示會傳送所有資料，0表示不會傳送任何內容。

如果您使用自動相互關聯，所有與單一要求相關聯的資料將會以一個單位的形式包含或排除。

新增下列程式碼以啟用取樣：

```javascript
const appInsights = require("applicationinsights");
appInsights.setup("<instrumentation_key>");
appInsights.defaultClient.config.samplingPercentage = 33; // 33% of all telemetry will be sent to Application Insights
appInsights.start();
```

### <a name="multiple-roles-for-multi-components-applications"></a>多重元件應用程式的多個角色

如果您的應用程式是由多個元件所組成，而您想要使用相同的檢測金鑰來檢測全部，而且仍然在入口網站中以個別的單位看到這些元件，如同使用個別的檢測金鑰（例如，應用程式對應上的個別節點），您可能需要手動設定 [使用者識別碼] 欄位，以區分將資料傳送至 Application Insights 資源的其他元件

使用下列各項來設定 [擁有權] 欄位：

```javascript
const appInsights = require("applicationinsights");
appInsights.setup("<instrumentation_key>");
appInsights.defaultClient.context.tags[appInsights.defaultClient.context.keys.cloudRole] = "MyRoleName";
appInsights.start();
```

### <a name="automatic-third-party-instrumentation"></a>自動協力廠商檢測

為了追蹤非同步呼叫之間的內容，在協力廠商程式庫（例如 MongoDB 和 Redis）中需要進行一些變更。 根據預設，Application Insights 會使用 [`diagnostic-channel-publishers`](https://github.com/Microsoft/node-diagnostic-channel/tree/master/src/diagnostic-channel-publishers) 來修補其中一些程式庫。 您可以藉由設定環境變數來停用此功能 `APPLICATION_INSIGHTS_NO_DIAGNOSTIC_CHANNEL` 。

> [!NOTE]
> 藉由設定該環境變數，事件可能無法再正確地與正確的作業相關聯。

 個別的猴子-藉由將 `APPLICATION_INSIGHTS_NO_PATCH_MODULES` 環境變數設定為要停用的套件清單（以逗號分隔），即可停用修補程式（例如， `APPLICATION_INSIGHTS_NO_PATCH_MODULES=console,redis` ）以避免修補 `console` 和 `redis` 封裝。

目前有九個已檢測的封裝： `bunyan` 、 `console` 、 `mongodb` 、 `mongodb-core` 、 `mysql` 、、、 `redis` `winston` `pg` 和 `pg-pool` 。 請流覽[診斷通道-發行者的讀我檔案](https://github.com/Microsoft/node-diagnostic-channel/blob/master/src/diagnostic-channel-publishers/README.md)，以取得這些套件的確切版本的相關資訊。

`bunyan`、 `winston` 和 `console` 修補程式會根據是否啟用，來產生 Application Insights 的追蹤事件 `setAutoCollectConsole` 。 其餘部分會根據是否啟用來產生 Application Insights 相依性事件 `setAutoCollectDependencies` 。

### <a name="live-metrics"></a>即時計量    

若要啟用將即時計量從您的應用程式傳送至 Azure，請使用 `setSendLiveMetrics(true)` 。 目前不支援在入口網站中篩選即時計量。

### <a name="extended-metrics"></a>擴充計量

> [!NOTE]
> 版本1.4.0 中已新增傳送擴充原生計量的功能

若要啟用將擴充原生計量從您的應用程式傳送至 Azure，請安裝個別的原生計量套件。 SDK 將會在安裝時自動載入，並開始收集 Node.js 的原生計量。

```bash
npm install applicationinsights-native-metrics
```

目前，原生計量套件會執行垃圾收集 CPU 時間、事件迴圈滴答和堆積使用的 autocollection：

- **垃圾收集**：每個垃圾收集類型所花費的 CPU 時間量，以及每個類型的出現次數。
- **事件迴圈**：發生的滴答數，以及總花費了多少 CPU 時間。
- **堆積與非堆積**：您的應用程式記憶體使用量在堆積或非堆積中的大小。

### <a name="distributed-tracing-modes"></a>分散式追蹤模式

根據預設，SDK 會傳送由 Application Insights SDK 檢測的其他應用程式/服務所瞭解的標頭。 除了現有的 AI 標頭以外，您可以選擇性地啟用傳送/接收[W3C 追蹤內容](https://github.com/w3c/trace-context)標頭，因此不會中斷與任何現有舊版服務的相互關聯。 啟用 W3C 標頭可讓您的應用程式與其他未使用 Application Insights 檢測的服務相互關聯，但採用此 W3C 標準。

```Javascript
const appInsights = require("applicationinsights");
appInsights
  .setup("<your ikey>")
  .setDistributedTracingMode(appInsights.DistributedTracingModes.AI_AND_W3C)
  .start()
```

## <a name="telemetryclient-api"></a>TelemetryClient API

如需 TelemetryClient API 的完整說明，請參閱[自訂事件和度量的 Application Insights API](./api-custom-events-metrics.md)。

您可以使用 Application Insights Node.js SDK 來追蹤任何要求、事件、計量或例外狀況。 下列程式碼範例示範您可以使用的一些 API：

```javascript
let appInsights = require("applicationinsights");
appInsights.setup().start(); // assuming ikey in env var. start() can be omitted to disable any non-custom data
let client = appInsights.defaultClient;
client.trackEvent({name: "my custom event", properties: {customProperty: "custom property value"}});
client.trackException({exception: new Error("handled exceptions can be logged with this method")});
client.trackMetric({name: "custom metric", value: 3});
client.trackTrace({message: "trace message"});
client.trackDependency({target:"http://dbname", name:"select customers proc", data:"SELECT * FROM Customers", duration:231, resultCode:0, success: true, dependencyTypeName: "ZSQL"});
client.trackRequest({name:"GET /customers", url:"http://myserver/customers", duration:309, resultCode:200, success:true});

let http = require("http");
http.createServer( (req, res) => {
  client.trackNodeHttpRequest({request: req, response: res}); // Place at the beginning of your request handler
});
```

### <a name="track-your-dependencies"></a>追蹤相依項目

您可以使用下列程式碼來追蹤相依項目：

```javascript
let appInsights = require("applicationinsights");
let client = new appInsights.TelemetryClient();

var success = false;
let startTime = Date.now();
// execute dependency call here....
let duration = Date.now() - startTime;
success = true;

client.trackDependency({target:"http://dbname", name:"select customers proc", data:"SELECT * FROM Customers", duration:duration, resultCode:0, success: true, dependencyTypeName: "ZSQL"});;
```

範例公用程式 `trackMetric` ，使用來測量事件迴圈排程所需的時間：  

```javascript
function startMeasuringEventLoop() {
  var startTime = process.hrtime();
  var sampleSum = 0;
  var sampleCount = 0;

  // Measure event loop scheduling delay
  setInterval(() => {
    var elapsed = process.hrtime(startTime);
    startTime = process.hrtime();
    sampleSum += elapsed[0] * 1e9 + elapsed[1];
    sampleCount++;
  }, 0);

  // Report custom metric every second
  setInterval(() => {
    var samples = sampleSum;
    var count = sampleCount;
    sampleSum = 0;
    sampleCount = 0;

    if (count > 0) {
      var avgNs = samples / count;
      var avgMs = Math.round(avgNs / 1e6);
      client.trackMetric({name: "Event Loop Delay", value: avgMs});
    }
  }, 1000);
}
```

### <a name="add-a-custom-property-to-all-events"></a>將自訂屬性新增至所有事件

您可以使用下列程式碼來將自訂屬性新增至所有事件：

```javascript
appInsights.defaultClient.commonProperties = {
  environment: process.env.SOME_ENV_VARIABLE
};
```

### <a name="track-http-get-requests"></a>追蹤 HTTP GET 要求

使用下列程式碼手動追蹤 HTTP GET 要求：

> [!NOTE]
> 預設會追蹤所有要求。 若要停用自動收集，請在呼叫 start （）之前呼叫 setAutoCollectRequests （false）。

```javascript
appInsights.defaultClient.trackRequest({name:"GET /customers", url:"http://myserver/customers", duration:309, resultCode:200, success:true});
```

或者，您可以使用方法來追蹤要求 `trackNodeHttpRequest` ：

```javascript
var server = http.createServer((req, res) => {
  if ( req.method === "GET" ) {
      appInsights.defaultClient.trackNodeHttpRequest({request:req, response:res});
  }
  // other work here....
  res.end();
});
```

### <a name="track-server-startup-time"></a>追蹤伺服器啟動時間

您可以使用下列程式碼來追蹤伺服器的啟動時間：

```javascript
let start = Date.now();
server.on("listening", () => {
  let duration = Date.now() - start;
  appInsights.defaultClient.trackMetric({name: "server startup time", value: duration});
});
```

### <a name="preprocess-data-with-telemetry-processors"></a>使用遙測處理器前置處理資料

您可以使用*遙測處理器*來處理和篩選收集的資料，然後再將它傳送以進行保留。 遙測處理器會依其在遙測專案傳送至雲端之前新增的順序逐一呼叫。

```javascript
public addTelemetryProcessor(telemetryProcessor: (envelope: Contracts.Envelope, context: { http.RequestOptions, http.ClientRequest, http.ClientResponse, correlationContext }) => boolean)
```

如果遙測處理器傳回 false，則不會傳送該遙測專案。

所有遙測處理器都會接收遙測資料及其信封以進行檢查和修改。 它們也會接收內容物件。 `contextObjects`呼叫手動追蹤的遙測的追蹤方法時，參數會定義此物件的內容。 針對自動收集的遙測，此物件會填入可用的要求資訊，以及所提供的持續性要求內容 `appInsights.getCorrelationContext()` （如果已啟用自動相依性相互關聯）。

遙測處理器的 TypeScript 類型為：

```javascript
telemetryProcessor: (envelope: ContractsModule.Contracts.Envelope, context: { http.RequestOptions, http.ClientRequest, http.ClientResponse, correlationContext }) => boolean;
```

例如，從例外狀況中移除堆疊追蹤資料的處理器，可能會以下列方式寫入並新增：

```javascript
function removeStackTraces ( envelope, context ) {
  if (envelope.data.baseType === "Microsoft.ApplicationInsights.ExceptionData") {
    var data = envelope.data.baseData;
    if (data.exceptions && data.exceptions.length > 0) {
      for (var i = 0; i < data.exceptions.length; i++) {
        var exception = data.exceptions[i];
        exception.parsedStack = null;
        exception.hasFullStack = false;
      }
    }
  }
  return true;
}

appInsights.defaultClient.addTelemetryProcessor(removeStackTraces);
```

## <a name="use-multiple-instrumentation-keys"></a>使用多個檢測金鑰

您可以使用個別的檢測金鑰（"ikey"）來建立多個 Application Insights 資源，並將不同的資料傳送至每個資源。

 例如：

```javascript
let appInsights = require("applicationinsights");

// configure auto-collection under one ikey
appInsights.setup("_ikey-A_").start();

// track some events manually under another ikey
let otherClient = new appInsights.TelemetryClient("_ikey-B_");
otherClient.trackEvent({name: "my custom event"});
```

## <a name="advanced-configuration-options"></a>進階組態選項

用戶端物件包含 `config` 屬性，其具有許多適用于 advanced 案例的選擇性設定。 這些可設定如下：

```javascript
client.config.PROPERTYNAME = VALUE;
```

這些屬性是用戶端特定的，因此您可以 `appInsights.defaultClient` 與使用建立的用戶端分開設定 `new appInsights.TelemetryClient()` 。

| 屬性                        | 說明                                                                                                |
| ------------------------------- |------------------------------------------------------------------------------------------------------------|
| instrumentationKey              | Application Insights 資源的識別碼。                                                      |
| endpointUrl                     | 要用來傳送遙測承載的內嵌端點。                                                      |
| quickPulseHost                  | 要傳送即時計量遙測的即時計量資料流主機。                                            |
| proxyHttpUrl                    | SDK HTTP 流量的 proxy 伺服器（選擇性，從環境變數提取的預設值 `http_proxy` ）。     |
| proxyHttpsUrl                   | 適用于 SDK HTTPS 流量的 proxy 伺服器（選擇性，從環境變數提取的預設值 `https_proxy` ）。   |
| HTTPAgent                       | Http。用於 SDK HTTP 流量的代理程式（選擇性，預設為未定義）。                                   |
| HTTPsAgent                      | Https。用於 SDK HTTPS 流量的代理程式（選擇性，預設為未定義）。                                 |
| maxBatchSize                    | 要在裝載端點的承載中包含的遙測專案數上限（預設值 `250` ）。   |
| maxBatchIntervalMs              | 要等待承載到達 maxBatchSize 的最大時間量（預設值 `15000` ）。               |
| disableAppInsights              | 表示是否已停用遙測傳輸的旗標（預設值 `false` ）。                                 |
| samplingPercentage              | 應傳輸之遙測專案的百分比（預設值 `100` ）。                      |
| correlationIdRetryIntervalMs    | 重試取得跨元件相互關聯的識別碼之前，所要等待的時間（預設值 `30000` ）。     |
| correlationHeaderExcludedDomains| 要從跨元件相互關聯標頭插入排除的網域清單（預設請參閱[Config. ts](https://github.com/Microsoft/ApplicationInsights-node.js/blob/develop/Library/Config.ts)）。|

## <a name="next-steps"></a>後續步驟

* [在入口網站中監視遙測](./overview-dashboard.md)
* [[寫您的遙測的分析查詢](../log-query/get-started-portal.md)

<!--references-->

[portal]: https://portal.azure.com/
[FAQ]: ../faq.md

