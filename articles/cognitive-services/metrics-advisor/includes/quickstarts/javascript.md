---
title: Metric Advisor JavaScript 快速入門
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: include
ms.date: 11/09/2020
ms.author: mbullwin
ms.openlocfilehash: 668255486b1c53c062907aba9a679cf7a84bc3ca
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/28/2021
ms.locfileid: "98948079"
---
[參考文件](/java/api/overview/azure/ai-metricsadvisor-readme) | [程式庫來源程式碼](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/metricsadvisor/ai-metrics-advisor/README.md) | [套件 (npm)](https://www.npmjs.com/package/@azure/ai-metrics-advisor) | [範例](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/metricsadvisor/ai-metrics-advisor/samples)

## <a name="prerequisites"></a>必要條件

* Azure 訂用帳戶 - [建立免費帳戶](https://azure.microsoft.com/free/cognitive-services/)
* 最新版的 [Node.js](https://nodejs.org/)
* 擁有 Azure 訂用帳戶之後，在 Azure 入口網站中<a href="https://go.microsoft.com/fwlink/?linkid=2142156"  title="建立 Metrics Advisor 資源"  target="_blank">建立 Metrics Advisor 資源<span class="docon docon-navigate-external x-hidden-focus"></span></a>，以部署您的 Metrics Advisor 執行個體。  
* 包含時間序列資料的 SQL 資料庫。
  
> [!TIP]
> * 您可以在 [GitHub](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/metricsadvisor/ai-metrics-advisor/samples) 上找到 JavaScript Metrics Advisor 範例。
> * 可能需要 10 到 30 分鐘，Metrics Advisor 資源才會將服務執行個體部署完成供您使用。 一旦成功部署，請按一下 [移至資源]。 部署之後，您即可開始透過入口網站和 REST API 使用 Metrics Advisor 執行個體。 
> * 您可以經由 Azure 入口網站，在資源的 [概觀] 區段中找到 REST API 的 URL。 其看起來將會像下面這樣：
>    * `https://<instance-name>.cognitiveservices.azure.com/`
    
## <a name="setting-up"></a>設定

### <a name="create-a-new-nodejs-application"></a>建立新的 Node.js 應用程式

在主控台視窗 (例如 cmd、PowerShell 或 Bash) 中，為您的應用程式建立新的目錄，並瀏覽至該目錄。 

```console
mkdir myapp && cd myapp
```

執行命令 `npm init`，以使用 `package.json` 檔案建立節點應用程式。 

```console
npm init
```

### <a name="install-the-client-library"></a>安裝用戶端程式庫

安裝 `@azure/ai-metrics-advisor` NPM 套件：

```console
npm install @azure/ai-metrics-advisor@1.0.0-beta.2
```

您應用程式的 `package.json` 檔案會隨著相依性而更新。

建立名為 `index.js` 的檔案，並匯入下列程式庫：

> [!TIP]
> 想要立刻檢視整個快速入門程式碼檔案嗎？ 您可以在 [GitHub](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/metricsadvisor/ai-metrics-advisor/samples/javascript) 上找到該檔案，其中包含本快速入門中的程式碼範例。

為資源的 Azure 端點和金鑰建立變數。 

> [!IMPORTANT]
> 前往 Azure 入口網站。 如果您在 [必要條件] 區段中建立的 Metrics Advisor 資源成功部署，請按一下 [後續步驟] 底下的 [前往資源] 按鈕。 您可以在資源的 [金鑰和端點] 頁面中 (位於 [資源管理] 底下) 找到訂用帳戶金鑰和端點。 <br><br>若要擷取您的 API 金鑰，您必須移至 [https://metricsadvisor.azurewebsites.net](https://metricsadvisor.azurewebsites.net)。 選取適當的：資源 [目錄]、[訂用帳戶] 和 [工作區]，然後選擇 [開始使用]。 接著，您將能夠從 [https://metricsadvisor.azurewebsites.net/api-key](https://metricsadvisor.azurewebsites.net/api-key) 擷取您的 API 金鑰。   
>
> 完成時，請記得從程式碼中移除金鑰，且不要公開張貼金鑰。 在生產環境中，請考慮使用安全的方式來儲存及存取您的認證。 如需詳細資訊，請參閱認知服務[安全性](../../../cognitive-services-security.md)一文。

```javascript
subscriptionKey = "<paste-your-metrics-advisor-key-here>";
apiKey ="<paste-your-metrics-advisor-api-key-here>";
endpoint = "<paste-your-metrics-advisor-endpoint-here>";
```

## <a name="object-model"></a>物件模型

下列類別和介面會處理 Metrics Advisor JavaScript SDK 的一些主要功能。

|名稱|描述|
|---|---|
| MetricsAdvisorClient | **用於**： <br> - 列出事件 <br> - 列出事件的根本原因 <br> - 擷取原始時間序列資料和由服務擴充的時間序列資料。 <br> - 列出警示 <br> - 新增意見反應以調整您的模型 |
| MetricsAdvisorAdministrationClient | **可讓您：** <br> - 管理資料摘要 <br> - 建立、設定、擷取、列出和刪除異常警示設定 <br> - 管理攔截  |
| DataFeed | **Metrics Advisor 從您的資料來源擷取的項目。`DataFeed` 包含下列各項的資料列：** <br> - 時間戳記 <br> - 零或多個維度 <br> - 一或多個量值  |
| DataFeedMetric | `DataFeedMetric` 是可量化的量值，用以監視及評估特定商務程序的狀態。 此量值可由分割成維度的多個時間序列值組合而成。 例如，Web 健康情況計量可包含使用者計數和美國市場的維度。 |

## <a name="code-examples"></a>程式碼範例

這些程式碼片段說明如何使用適用於 JavaScript 的 Metrics Advisor 用戶端程式庫來執行下列工作：

* [驗證用戶端](#authenticate-the-client)
* [新增資料摘要](#add-a-data-feed)
* [檢查內嵌狀態](#check-ingestion-status)
* [設定異常偵測](#configure-anomaly-detection)
* [建立攔截](#create-a-hook)
* [建立警示設定](#create-an-alert-configuration)
* [查詢警示](#query-the-alert)

## <a name="authenticate-the-client"></a>驗證用戶端

取得兩個金鑰和端點位址之後，您就可以使用 MetricsAdvisorKeyCredential 類別來驗證用戶端，如下所示：

```javascript
const {
  MetricsAdvisorKeyCredential,
  MetricsAdvisorClient,
  MetricsAdvisorAdministrationClient
} = require("@azure/ai-metrics-advisor");

const credential = new MetricsAdvisorKeyCredential(subscriptionKey, apiKey);
const client = new MetricsAdvisorClient(endpoint, credential);
const adminClient = new MetricsAdvisorAdministrationClient(endpoint, credential);
```

## <a name="add-a-data-feed"></a>新增資料摘要

Metrics Advisor 支援連接不同類型的資料來源。 以下是從 SQL Server 擷取資料的範例。

將 `sql_server_connection_string` 取代為您自己的 SQL Server 連接字串，並將 `query` 取代為以單一時間戳記傳回資料的查詢。 您也必須根據自訂資料調整 `metric` 和 `dimension` 值。

> [!IMPORTANT]
> 在每個時間戳記上，查詢對於每個維度組合最多僅應傳回一筆記錄。 而且，查詢所傳回的所有記錄必須具有相同的時間戳記。 Metrics Advisor 會對每個時間戳記執行此查詢，以擷取您的資料。 如需詳細資訊和範例，請參閱[查詢的常見問題集一節](../../faq.md#how-do-i-write-a-valid-query-for-ingesting-my-data)。 

```javascript
const {
  MetricsAdvisorKeyCredential,
  MetricsAdvisorAdministrationClient
} = require("@azure/ai-metrics-advisor");

async function main() {
  subscriptionKey = "<paste-your-metrics-advisor-key-here>";
  apiKey ="<paste-your-metrics-advisor-api-key-here>";
  endpoint = "<paste-your-metrics-advisor-endpoint-here>";
  const sqlServerConnectionString ="<sql_server_connection_string>";
  const sqlServerQuery ="<query>";
  const credential = new MetricsAdvisorKeyCredential(subscriptionKey, apiKey);

  const adminClient = new MetricsAdvisorAdministrationClient(endpoint, credential);

  const created = await createDataFeed(adminClient, sqlServerConnectionString, sqlServerQuery);
  console.log(`Data feed created: ${created.id}`);
}

async function createDataFeed(adminClient, sqlServerConnectionString, sqlServerQuery) {
  console.log("Creating Datafeed...");
  const dataFeed = {
    name: "test_datafeed_" + new Date().getTime().toString(),
    source: {
      dataSourceType: "SqlServer",
      dataSourceParameter: {
        connectionString: sqlServerConnectionString,
        query: sqlServerQuery
      }
    },
    granularity: {
      granularityType: "Daily"
    },
    schema: {
      metrics: [
        {
          name: "revenue",
          displayName: "revenue",
          description: "Metric1 description"
        },
        {
          name: "cost",
          displayName: "cost",
          description: "Metric2 description"
        }
      ],
      dimensions: [
        { name: "city", displayName: "city display" },
        { name: "category", displayName: "category display" }
      ],
      timestampColumn: null
    },
    ingestionSettings: {
      ingestionStartTime: new Date(Date.UTC(2020, 5, 1)),
      ingestionStartOffsetInSeconds: 0,
      dataSourceRequestConcurrency: -1,
      ingestionRetryDelayInSeconds: -1,
      stopRetryAfterInSeconds: -1
    },
    rollupSettings: {
      rollupType: "AutoRollup",
      rollupMethod: "Sum",
      rollupIdentificationValue: "__CUSTOM_SUM__"
    },
    missingDataPointFillSettings: {
      fillType: "SmartFilling"
    },
    accessMode: "Private",
    adminEmails: ["xyz@example.com"]
  };
  const result = await adminClient.createDataFeed(dataFeed);

  return result;
}
```

## <a name="check-ingestion-status"></a>檢查內嵌狀態

開始進行資料擷取之後，我們可以檢查擷取狀態。

```javascript
const {
  MetricsAdvisorKeyCredential,
  MetricsAdvisorAdministrationClient
} = require("@azure/ai-metrics-advisor");

async function main() {
  // You will need to set these environment variables or edit the following values
  const endpoint = "<service endpoint>";
  const subscriptionKey = "<subscription key>";
  const apiKey = "<api key>";
  const dataFeedId = "<data feed id>";
  const credential = new MetricsAdvisorKeyCredential(subscriptionKey, apiKey);

  const adminClient = new MetricsAdvisorAdministrationClient(endpoint, credential);
  await checkIngestionStatus(
    adminClient,
    dataFeedId,
    new Date(Date.UTC(2020, 8, 1)),
    new Date(Date.UTC(2020, 8, 12))
  );
}

async function checkIngestionStatus(adminClient, datafeedId, startTime, endTime) {
  // This shows how to use for-await-of syntax to list status
  console.log("Checking ingestion status...");
  const iterator = adminClient.listDataFeedIngestionStatus(datafeedId, startTime, endTime);
  for await (const status of iterator) {
    console.log(`  [${status.timestamp}] ${status.status} - ${status.message}`);
  }
}
```

## <a name="configure-anomaly-detection"></a>設定異常偵測

我們需要異常偵測設定，以判斷時間序列中的某個點是否異常。 雖然預設偵測設定會自動套用至每個計量，但您可以調整資料所使用的偵測模式，只要建立自訂的異常偵測設定即可。

```javascript
const {
  MetricsAdvisorKeyCredential,
  MetricsAdvisorAdministrationClient
} = require("@azure/ai-metrics-advisor");

async function main() {
  const endpoint = "<service endpoint>";
  const subscriptionKey = "<subscription key>";
  const apiKey = "<api key>";
  const metricId =  "<metric id>";
  const credential = new MetricsAdvisorKeyCredential(subscriptionKey, apiKey);

  const adminClient = new MetricsAdvisorAdministrationClient(endpoint, credential);

  const detectionConfig = await configureAnomalyDetectionConfiguration(adminClient, metricId);
  console.log(`Detection configuration created: ${detectionConfig.id}`);
}

async function configureAnomalyDetectionConfiguration(adminClient, metricId) {
  console.log(`Creating an anomaly detection configuration on metric '${metricId}'...`);
  const detectionConfig = {
    name: "test_detection_configuration" + new Date().getTime().toString(),
    metricId,
    wholeSeriesDetectionCondition: {
      smartDetectionCondition: {
        sensitivity: 100,
        anomalyDetectorDirection: "Both",
        suppressCondition: {
          minNumber: 1,
          minRatio: 1
        }
      }
    },
    description: "Detection configuration description"
  };
  return await adminClient.createDetectionConfig(detectionConfig);
}
```

### <a name="create-a-hook"></a>建立攔截

我們會使用攔截來訂閱即時警示。 在此範例中，我們會為 Metrics Advisor 服務建立 Webhook，以將警示張貼到該處。

```javascript

const {
  MetricsAdvisorKeyCredential,
  MetricsAdvisorAdministrationClient
} = require("@azure/ai-metrics-advisor");

async function main() {
  // You will need to set these environment variables or edit the following values
  const endpoint = "<service endpoint>";
  const subscriptionKey = "<subscription key>";
  const apiKey = "<api key>";
  const credential = new MetricsAdvisorKeyCredential(subscriptionKey, apiKey);

  const adminClient = new MetricsAdvisorAdministrationClient(endpoint, credential);
  const hook = await createWebhookHook(adminClient);
  console.log(`Webhook hook created: ${hook.id}`);
}

async function createWebhookHook(adminClient) {
  console.log("Creating a webhook hook");
  const hook = {
    hookType: "Webhook",
    name: "web hook " + new Date().getTime().toFixed(),
    description: "description",
    hookParameter: {
      endpoint: "https://example.com/handleAlerts", // you must enter a valid webhook url to post the alert payload
      username: "username",
      password: "password"
      // certificateKey: "certificate key",
      // certificatePassword: "certificate password"
    }
  };

  return await adminClient.createHook(hook);
}
```

##  <a name="create-an-alert-configuration"></a>建立警示設定

此範例將說明如何設定觸發警示所需的條件，以及要使用哪些攔截作為警示的目的地。

```javascript
const {
  MetricsAdvisorKeyCredential,
  MetricsAdvisorAdministrationClient
} = require("@azure/ai-metrics-advisor");

async function main() {
  // You will need to set these environment variables or edit the following values
  const endpoint = "<service endpoint>";
  const subscriptionKey = "<subscription key>";
  const apiKey = "<api key>";
  const detectionConfigId = "<detection id>";
  const hookId = "<hook id>";
  const credential = new MetricsAdvisorKeyCredential(subscriptionKey, apiKey);

  const adminClient = new MetricsAdvisorAdministrationClient(endpoint, credential);
  const alertConfig = await configureAlertConfiguration(adminClient, detectionConfigId, [hookId]);
  console.log(`Alert configuration created: ${alertConfig.id}`);
}

async function configureAlertConfiguration(adminClient, detectionConfigId, hookIds) {
  console.log("Creating a new alerting configuration...");
  const anomalyAlertConfig = {
    name: "test_alert_config_" + new Date().getTime().toString(),
    crossMetricsOperator: "AND",
    metricAlertConfigurations: [
      {
        detectionConfigurationId: detectionConfigId,
        alertScope: {
          scopeType: "All"
        },
        alertConditions: {
          severityCondition: { minAlertSeverity: "Medium", maxAlertSeverity: "High" }
        },
        snoozeCondition: {
          autoSnooze: 0,
          snoozeScope: "Metric",
          onlyForSuccessive: true
        }
      }
    ],
    hookIds,
    description: "Alerting config description"
  };
  return await adminClient.createAlertConfig(anomalyAlertConfig);
}
```

## <a name="query-the-alert"></a>查詢警示

```javascript
const { MetricsAdvisorKeyCredential, MetricsAdvisorClient } = require("@azure/ai-metrics-advisor");

async function main() {
  // You will need to set these environment variables or edit the following values
  const endpoint = "<service endpoint>";
  const subscriptionKey = "<subscription key>";
  const apiKey = "<api key>";
  const alertConfigId = "<alert config id>";
  const credential = new MetricsAdvisorKeyCredential(subscriptionKey, apiKey);

  const client = new MetricsAdvisorClient(endpoint, credential);

  const alertIds = await queryAlerts(
    client,
    alertConfigId,
    new Date(Date.UTC(2020, 8, 1)),
    new Date(Date.UTC(2020, 8, 12))
  );

  if (alertIds.length > 1) {
    // query anomalies using an alert id.
    await queryAnomaliesByAlert(client, alertConfigId, alertIds[0]);
  } else {
    console.log("No alerts during the time period");
  }
}

async function queryAlerts(client, alertConfigId, startTime, endTime) {
  let alerts = [];
  const iterator = client.listAlerts(alertConfigId, startTime, endTime, "AnomalyTime");
  for await (const alert of iterator) {
    alerts.push(alert);
  }

  return alerts;
}

async function queryAnomaliesByAlert(client, alert) {
  console.log(
    `Listing anomalies for alert configuration '${alert.alertConfigId}' and alert '${alert.id}'`
  );
  const iterator = client.listAnomalies(alert);
  for await (const anomaly of iterator) {
    console.log(
      `  Anomaly ${anomaly.severity} ${anomaly.status} ${anomaly.seriesKey.dimension} ${anomaly.timestamp}`
    );
  }
}
```

### <a name="run-the-application"></a>執行應用程式

使用快速入門檔案上使用 `node` 命令執行應用程式。

```console
node index.js
```
