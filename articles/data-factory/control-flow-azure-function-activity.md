---
title: Azure Data Factory 中的 Azure 函式活動
description: 了解如何使用 Azure 函式活動在 Data Factory 管線中執行 Azure 函式
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/09/2019
ms.openlocfilehash: ee2e59e794cf34a8fd5043a56867a81c2537f1ae
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "81415315"
---
# <a name="azure-function-activity-in-azure-data-factory"></a>Azure Data Factory 中的 Azure 函式活動
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]
Azure 函式活動可讓您在 Data Factory 管線中執行 [Azure 函式](../azure-functions/functions-overview.md)。 若要執行 Azure 函式，您需要建立連結服務連線和可指定所打算執行 Azure 函式的活動。

如需此功能的簡介與示範，請觀看下列 8 分鐘長的影片：

> [!VIDEO https://channel9.msdn.com/shows/azure-friday/Run-Azure-Functions-from-Azure-Data-Factory-pipelines/player]

## <a name="azure-function-linked-service"></a>Azure 函式連結服務

Azure 函式的傳回型別必須是有效的 `JObject`。 (請記住，[JArray](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_Linq_JArray.htm)「不是」一個`JObject`)。除了 `JObject` 以外的任何傳回型別都會失敗，並引發使用者錯誤「回應內容不是有效的 JObject」。

| **屬性** | **說明** | **必要** |
| --- | --- | --- |
| type   | 類型屬性必須設定為：**AzureFunction** | 是 |
| 函式應用程式 URL | Azure 函式應用程式的 URL。 格式為 `https://<accountname>.azurewebsites.net`。 此 URL 是您在 Azure 入口網站中檢視函式應用程式時位於 [URL] 區段底下的值  | 是 |
| 函式金鑰 | Azure 函式的存取金鑰。 按一下個別函式的 [管理] 區段，然後複製 [函式金鑰] 或 [主機金鑰]。 前往此處深入了解：[Azure Functions HTTP 觸發程序和繫結](../azure-functions/functions-bindings-http-webhook-trigger.md#authorization-keys) | 是 |
|   |   |   |

## <a name="azure-function-activity"></a>Azure 函式活動

| **屬性**  | **說明** | **允許的值** | **必要** |
| --- | --- | --- | --- |
| NAME  | 管線中的活動名稱  | String | 是 |
| 類型  | 活動類型是 ‘AzureFunctionActivity’ | String | 是 |
| 連結服務 | 相對應 Azure 函式應用程式的 Azure 函式連結服務  | 連結服務參考 | 是 |
| 函數名稱  | 此活動會在 Azure 函式應用程式中呼叫的函式名稱 | String | 是 |
| method  | 函式呼叫的 REST API 方法 | 字串支援的類型： "GET"、"POST"、"PUT"   | 是 |
| header  | 傳送至要求的標頭。 例如，用來在要求上設定語言和類型："headers": { "Accept-Language": "en-us", "Content-Type": "application/json" } | 字串 (或含有字串之 resultType 的運算式) | 否 |
| body  | 與要求一起傳送至函式 API 方法的主體  | 字串 (或含有字串之 resultType 的運算式) 或物件。   | PUT/POST 方法的必要項 |
|   |   |   | |

請參閱 [要求裝載架構](control-flow-web-activity.md#request-payload-schema)一節中的要求承載的架構   。

## <a name="routing-and-queries"></a>路由和查詢

Azure 函式活動支援**路由**。 例如，如果您的 Azure 函式具有端點，則在 Azure 函式 `https://functionAPP.azurewebsites.net/api/<functionName>/<value>?code=<secret>` `functionName` 活動中使用的會是 `<functionName>/<value>` 。 您可以參數化此函式，以 `functionName` 在執行時間提供所需的。

Azure 函式活動也支援**查詢**。 查詢必須包含在中 `functionName` 。 例如，當函式名稱是 `HttpTriggerCSharp` ，而您想要包含的查詢是時 `name=hello` ，您可以在 Azure 函式活動中將當做建立 `functionName` 為 `HttpTriggerCSharp?name=hello` 。 此函式可以參數化，因此可以在執行時間決定值。

## <a name="timeout-and-long-running-functions"></a>Timeout 和長時間執行的函式

無論您在設定中設定的設定為何，230秒後 Azure Functions 次 `functionTimeout` 。 如需詳細資訊，請參閱[這篇文章](../azure-functions/functions-versions.md#timeout)。 若要解決此行為，請遵循非同步模式或使用 Durable Functions。 Durable Functions 的優點是，它們提供自己的狀態追蹤機制，因此您不需要自行執行。

若要深入瞭解 Durable Functions，請參閱[這篇文章](../azure-functions/durable/durable-functions-overview.md)。 您可以設定 Azure 函式活動來呼叫長期函式，這會傳回具有不同 URI 的回應，例如[此範例](../azure-functions/durable/durable-functions-http-features.md#http-api-url-discovery)。 因為會在函式 `statusQueryGetUri` 執行時傳回 HTTP 狀態202，所以您可以使用 Web 活動來輪詢函式的狀態。 只要設定 Web 活動，並將 `url` 欄位設定為 `@activity('<AzureFunctionActivityName>').output.statusQueryGetUri` 。 當長期函數完成時，函式的輸出將會是 Web 活動的輸出。


## <a name="sample"></a>範例

您可以在[這裡](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV2/UntarAzureFilesWithAzureFunction)找到使用 Azure 函式來解壓縮 tar 檔案內容的 Data Factory 範例。

## <a name="next-steps"></a>後續步驟

在 [Azure Data Factory 中的管線和活動](concepts-pipelines-activities.md)中深入了解 Data Factory 中的活動。
