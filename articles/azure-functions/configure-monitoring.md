---
title: 設定 Azure Functions 的監視
description: 瞭解如何將您的函數應用程式連接至 Application Insights 進行監視，以及如何設定資料收集。
ms.date: 8/31/2020
ms.topic: how-to
ms.custom: contperfq2
ms.openlocfilehash: 50705eeedf9c985a053600a8c0b27c823231e9a3
ms.sourcegitcommit: 8d8deb9a406165de5050522681b782fb2917762d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92217179"
---
# <a name="how-to-configure-monitoring-for-azure-functions"></a>如何設定 Azure Functions 的監視

Azure Functions 與 Application Insights 整合，讓您能夠監視函數應用程式。 Application Insights 是 Azure 監視器的功能，它是可延伸的應用程式效能管理 (APM) 服務，可收集您的函式應用程式所產生的資料，包括應用程式寫入至記錄檔的資訊。 建立函數應用程式時，通常會啟用 Application Insights 整合。 如果您的應用程式未設定檢測金鑰，您必須先 [啟用 Application Insights 整合](#enable-application-insights-integration)。 

您可以使用 Application Insights，而不需任何自訂設定。 預設設定可能會導致大量資料。 如果您使用 Visual Studio Azure 訂用帳戶，可能就會達到 Application Insights 適用的資料上限。 若要深入瞭解 Application Insights 成本，請參閱 [管理 Application Insights 的使用量和成本](../azure-monitor/app/pricing.md)。

您將在本文稍後了解如何設定及自訂您的函式傳送到 Application Insights 的資料。 針對函式應用程式，記錄會設定於 [host.json] 檔案中。 

> [!NOTE]
> 您可以使用特別設定的應用程式設定，來代表特定環境之檔案 host.js中的特定設定。 這可讓您有效地變更設定的 host.js，而不必重新發佈專案中的 host.js檔案。 若要深入瞭解，請參閱覆 [寫值的 host.js](functions-host-json.md#override-hostjson-values)。

## <a name="configure-categories"></a>設定類別

Azure Functions 記錄器包括每個記錄的「類別」。 類別指出寫入記錄的是哪個部分的執行階段程式碼或函式程式碼。 版本1.x 和更新版本之間的分類不同。 下圖說明執行階段所建立記錄的主要類別。 

# <a name="v2x"></a>[v2. x +](#tab/v2)

| 類別 | Table | 描述 |
| ----- | ----- | ----- |
| **`Function.<YOUR_FUNCTION_NAME>`** | **依賴**| 某些服務會自動收集相依性資料。 針對成功的執行，這些記錄位於 `Information` 層級。 若要深入瞭解， [請參閱相依](functions-monitoring.md#dependencies)性。 例外狀況會記錄在 `Error` 層級。 運行 `Warning` 時間也會建立層級記錄，例如將佇列訊息傳送至 [有害佇列](functions-bindings-storage-queue-trigger.md#poison-messages)的時間。 | 
| **`Function.<YOUR_FUNCTION_NAME>`** | **customMetrics**<br/>**customEvents** | C # 和 JavaScript Sdk 可讓您收集自訂計量和記錄自訂事件。 若要深入瞭解，請參閱 [自訂遙測資料](functions-monitoring.md#custom-telemetry-data)。|
| **`Function.<YOUR_FUNCTION_NAME>`** | **traces**| 包含特定函式執行的函式啟動和完成記錄。 針對成功的執行，這些記錄位於 `Information` 層級。 例外狀況會記錄在 `Error` 層級。 運行 `Warning` 時間也會建立層級記錄，例如將佇列訊息傳送至 [有害佇列](functions-bindings-storage-queue-trigger.md#poison-messages)的時間。 | 
| **`Function.<YOUR_FUNCTION_NAME>.User`** | **traces**| 使用者產生的記錄檔，可能是任何記錄層級。 若要深入瞭解如何寫入您函式中的記錄，請參閱 [寫入記錄](functions-monitoring.md#writing-to-logs)檔。 | 
| **`Host.Aggregator`** | **customMetrics** | 這些執行時間產生的記錄 [可在可](#configure-the-aggregator) 設定的時間內，提供函式呼叫的計數和平均值。 預設期間為 30 秒或 1,000 個結果，視何者較早達到而定。 範例包括執行次數、成功率和持續時間。 所有這些記錄都會在 `Information` 層級寫入。 如果您在 `Warning` 或以上層級進行篩選，就不會看到此資料。 |
| **`Host.Results`** | **requests** | 這些執行時間產生的記錄檔表示函式成功或失敗。 所有這些記錄都會在 `Information` 層級寫入。 如果您在 `Warning` 或以上層級進行篩選，就不會看到此資料。 |
| **`Microsoft`** | **traces** | 反映主機叫用之 .NET 執行時間元件的完整記錄類別。  |
| **`Worker`** | **traces** | Non-.NET 語言的語言工作者進程所產生的記錄。 語言背景工作記錄檔也可以記錄在 `Microsoft.*` 類別中，例如 `Microsoft.Azure.WebJobs.Script.Workers.Rpc.RpcFunctionInvocationDispatcher` 。 這些記錄會以 `Information` 層級寫入。|

# <a name="v1x"></a>[v1.x](#tab/v1)

| 類別 | Table | 描述 |
| ----- | ----- | ----- |
| **`Function`** | **traces**| 使用者產生的記錄檔，可能是任何記錄層級。 若要深入瞭解如何寫入您函式中的記錄，請參閱 [寫入記錄](functions-monitoring.md#writing-to-logs)檔。 | 
| **`Host.Aggregator`** | **customMetrics** | 這些執行時間產生的記錄 [可在可](#configure-the-aggregator) 設定的時間內，提供函式呼叫的計數和平均值。 預設期間為 30 秒或 1,000 個結果，視何者較早達到而定。 範例包括執行次數、成功率和持續時間。 所有這些記錄都會在 `Information` 層級寫入。 如果您在 `Warning` 或以上層級進行篩選，就不會看到此資料。 |
| **`Host.Executor`** | **traces** | 針對特定函式的執行，包括 **已啟動** 的函式和函式 **完成** 的記錄。 針對成功執行，這些記錄均為 `Information` 層級。 例外狀況會記錄於 `Error` 層級。 運行 `Warning` 時間也會建立層級記錄，例如將佇列訊息傳送至 [有害佇列](functions-bindings-storage-queue-trigger.md#poison-messages)的時間。  |
| **`Host.Results`** | **requests** | 這些執行時間產生的記錄檔表示函式成功或失敗。 所有這些記錄都會在 `Information` 層級寫入。 如果您在 `Warning` 或以上層級進行篩選，就不會看到此資料。 |

---

[ **資料表** ] 資料行會指出寫入記錄 Application Insights 中的哪個資料表。 

## <a name="configure-log-levels"></a>設定記錄層級

[!INCLUDE [functions-log-levels](../../includes/functions-log-levels.md)]

針對每個類別，您可以指出要傳送的最小記錄層級。 host.js設定會視函式 [執行階段版本](functions-versions.md)而異。 

下列範例會根據下列規則定義記錄：

+ 若為或的記錄 `Host.Results` `Function` ，則只會在 `Error` 或更高層級記錄事件。 
+ 若為的記錄 `Host.Aggregator` ，請將所有產生的計量記錄 (`Trace`) 。
+ 對於所有其他記錄（包括使用者記錄），只記錄 `Information` 層級和更高的事件。

# <a name="v2x"></a>[v2. x +](#tab/v2)

```json
{
  "logging": {
    "fileLoggingMode": "always",
    "logLevel": {
      "default": "Information",
      "Host.Results": "Error",
      "Function": "Error",
      "Host.Aggregator": "Trace"
    }
  }
}
```

# <a name="v1x"></a>[v1.x](#tab/v1) 

```json
{
  "logger": {
    "categoryFilter": {
      "defaultLevel": "Information",
      "categoryLevels": {
        "Host.Results": "Error",
        "Function": "Error",
        "Host.Aggregator": "Trace"
      }
    }
  }
}
```

---

如果 [host.json] 包含多個以相同字串開頭的記錄，則會先比對所定義的記錄檔。 請考慮下列範例，此範例會在執行時間中記錄所有內容，但 `Host.Aggregator` `Error` 層級除外：

# <a name="v2x"></a>[v2. x +](#tab/v2)

```json
{
  "logging": {
    "fileLoggingMode": "always",
    "logLevel": {
      "default": "Information",
      "Host": "Error",
      "Function": "Error",
      "Host.Aggregator": "Information"
    }
  }
}
```

# <a name="v1x"></a>[v1.x](#tab/v1) 

```json
{
  "logger": {
    "categoryFilter": {
      "defaultLevel": "Information",
      "categoryLevels": {
        "Host": "Error",
        "Function": "Error",
        "Host.Aggregator": "Information"
      }
    }
  }
}
```

---

您可以使用的記錄層級設定， `None` 防止針對某個類別撰寫任何記錄。 

## <a name="configure-the-aggregator"></a>設定彙總工具

如前一節所述，執行階段經過一段時間就會彙總有關函式執行的資料。 預設期間為 30 秒或 1,000 個執行，視何者較早達到而定。 您可以在 [host.json] 檔案中設定此設定。  以下是範例：

```json
{
    "aggregator": {
      "batchSize": 1000,
      "flushTimeout": "00:00:30"
    }
}
```

## <a name="configure-sampling"></a>設定取樣

Application Insights 具有[取樣](../azure-monitor/app/sampling.md)功能，可以提供保護，以避免在尖峰負載期間完成的執行中產生過多的遙測資料。 當傳入執行速率超過指定的閾值時，Application Insights 就會開始隨機忽略某些傳入執行。 每秒執行次數上限的預設值為 20 (在 1.x 版中為五)。 您可以在 [host.json](./functions-host-json.md#applicationinsights) 中設定取樣。  以下是範例：

# <a name="v2x"></a>[v2. x +](#tab/v2)

```json
{
  "logging": {
    "applicationInsights": {
      "samplingSettings": {
        "isEnabled": true,
        "maxTelemetryItemsPerSecond" : 20,
        "excludedTypes": "Request;Exception"
      }
    }
  }
}
```


您可以從取樣中排除特定類型的遙測。 在此範例中，會將型別和的資料 `Request` `Exception` 從取樣中排除。 這可確保會記錄 *所有* 的函式執行 (要求) 和例外狀況，而其他類型的遙測仍受限於取樣。 

# <a name="v1x"></a>[v1.x](#tab/v1)  

```json
{
  "applicationInsights": {
    "sampling": {
      "isEnabled": true,
      "maxTelemetryItemsPerSecond" : 5
    }
  }
}
```
---

若要深入瞭解，請參閱 [Application Insights 中的取樣](../azure-monitor/app/sampling.md)。

## <a name="configure-scale-controller-logs"></a>設定調整控制器記錄

_這項功能目前為預覽狀態。_ 

您可以讓 [Azure Functions 的調整控制器](./functions-scale.md#runtime-scaling) 發出記錄至 Application Insights 或 Blob 儲存體，以進一步瞭解調整控制器針對您的函數應用程式所做的決策。

若要啟用這項功能，請將名為的應用程式設定新增 `SCALE_CONTROLLER_LOGGING_ENABLED` 至函數應用程式設定。 這項設定的值必須是以下格式的格式 `<DESTINATION>:<VERBOSITY>` ：

[!INCLUDE [functions-scale-controller-logging](../../includes/functions-scale-controller-logging.md)]

例如，下列 Azure CLI 命令會開啟從調整控制器到 Application Insights 的詳細資訊記錄：

```azurecli-interactive
az functionapp config appsettings set --name <FUNCTION_APP_NAME> \
--resource-group <RESOURCE_GROUP_NAME> \
--settings SCALE_CONTROLLER_LOGGING_ENABLED=AppInsights:Verbose
```

在此範例中， `<FUNCTION_APP_NAME>` 請 `<RESOURCE_GROUP_NAME>` 分別以函式應用程式的名稱和資源組名取代。 

下列 Azure CLI 命令會將詳細資訊設定為，以停用記錄 `None` ：

```azurecli-interactive
az functionapp config appsettings set --name <FUNCTION_APP_NAME> \
--resource-group <RESOURCE_GROUP_NAME> \
--settings SCALE_CONTROLLER_LOGGING_ENABLED=AppInsights:None
```

您也可以 `SCALE_CONTROLLER_LOGGING_ENABLED` 使用下列 Azure CLI 命令移除設定，以停用記錄：

```azurecli-interactive
az functionapp config appsettings delete --name <FUNCTION_APP_NAME> \
--resource-group <RESOURCE_GROUP_NAME> \
--setting-names SCALE_CONTROLLER_LOGGING_ENABLED
```

## <a name="enable-application-insights-integration"></a>啟用 Application Insights 整合

針對將資料傳送至 Application Insights 的函式應用程式，它需要知道 Application Insights 資源的檢測金鑰。 金鑰必須在名為 **APPINSIGHTS_INSTRUMENTATIONKEY** 的應用程式設定中。

當您在 [Azure 入口網站](functions-create-first-azure-function.md)中，使用 [Azure Functions Core Tools](functions-create-first-azure-function-azure-cli.md) 或使用 [Visual Studio Code](functions-create-first-function-vs-code.md)，從命令列建立函式應用程式時，預設會啟用 Application Insights 整合。 Application Insights 資源的名稱與您的函式應用程式相同，而且其會建立於相同區域或最近的區域中。

### <a name="new-function-app-in-the-portal"></a>入口網站中新的函式應用程式

若要檢閱所建立的 Application Insights 資源，請加以選取以展開 [Application Insights] 視窗。 您可以變更**新資源名稱**，或者在您希望儲存資料的 [Azure 地理位置](https://azure.microsoft.com/global-infrastructure/geographies/)中，選擇不同的**位置**。

![建立函式應用程式時啟用 Application Insights](media/functions-monitoring/enable-ai-new-function-app.png)

當您選擇 [建立] 時，即會使用您的函式應用程式來建立 Application Insights 資源，其已在應用程式設定中設定了 `APPINSIGHTS_INSTRUMENTATIONKEY`。 一切都已準備就緒。

<a id="manually-connect-an-app-insights-resource"></a>
### <a name="add-to-an-existing-function-app"></a>新增至現有的函式應用程式 

如果未使用函數應用程式建立 Application Insights 資源，請使用下列步驟來建立資源。 然後，您可以從該資源新增檢測金鑰，以作為函式應用程式中的[應用程式設定](functions-how-to-use-azure-function-app-settings.md#settings)。

1. 在 [ [Azure 入口網站](https://portal.azure.com)] 中，搜尋並選取 [ **函數應用程式**]，然後選擇您的函數應用程式。 

1. 選取視窗頂端的 [未設定 Application Insights] 橫幅。 如果您看不到此橫幅，則您的應用程式可能已經啟用 Application Insights。

    :::image type="content" source="media/configure-monitoring/enable-application-insights.png" alt-text="從入口網站啟用 Application Insights":::

1. 使用下表中指定的設定，展開 [變更您的資源] 並建立 Application Insights 資源。  

    | 設定      | 建議的值  | 描述                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **新資源名稱** | 唯一的應用程式名稱 | 最簡單的方式是使用與您函式應用程式一樣的名稱，而這必須是您訂用帳戶中唯一的名稱。 | 
    | **位置** | 西歐 | 可能的話，請使用與函式應用程式相同的[區域](https://azure.microsoft.com/regions/)，或該區域附近的區域。 |

    :::image type="content" source="media/configure-monitoring/ai-general.png" alt-text="從入口網站啟用 Application Insights":::

1. 選取 [套用]。 

   Application Insights 資源會建立在函式應用程式所在的資源群組和訂用帳戶。 建立資源之後，請關閉 Application Insights 視窗。

1. 在您的函式應用程式中，選取 [設定] 之下的 [組態]，然後選取 [應用程式設定]。 如果您看到名為 `APPINSIGHTS_INSTRUMENTATIONKEY` 的設定，表示 Azure 中執行的函式應用程式已啟用 Application Insights 整合。 如果基於某些原因，此設定不存在，請使用您的 Application Insights 檢測金鑰作為值加以新增。

> [!NOTE]
> 舊版 Functions 會使用內建的監視功能 (不再建議使用)。 針對這類函式應用程式啟用 Application Insights 整合時，您也必須[停用內建記錄](#disable-built-in-logging)。  

## <a name="disable-built-in-logging"></a>停用內建記錄

當您啟用 Application Insights，請停用使用 Azure 儲存體的內建記錄。 內建記錄適合用來測試少量的工作負載，但不適用於負載繁重的實際執行環境。 若要監視實際執行環境，建議使用 Application Insights。 如果將內建記錄用於實際執行環境，記錄內容可能會因為 Azure 儲存體的頻寬節流設定而變得不完整。

若要停用內建記錄，請刪除 `AzureWebJobsDashboard` 應用程式設定。 如需在 Azure 入口網站中刪除應用程式設定的相關資訊，請參閱[如何管理函式應用程式](functions-how-to-use-azure-function-app-settings.md#settings)的**應用程式設定**。 刪除應用程式設定之前，確定相同函式應用程式中目前沒有任何函式會使用適用於 Azure 儲存體觸發程序或繫結的設定。

## <a name="next-steps"></a>後續步驟

若要深入瞭解監視，請參閱：

+ [監視 Azure Functions](functions-monitoring.md)
+ [在 Application Insights 中分析 Azure Functions 遙測資料](analyze-telemetry-data.md)
+ [Application Insights](/azure/application-insights/)


[host.json]: functions-host-json.md
