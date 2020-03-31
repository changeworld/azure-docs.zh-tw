---
title: Azure 資料工廠中的 Azure 函式活動
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
ms.openlocfilehash: ffb610634399594788afcb9b600ba00c6803dfdd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77207020"
---
# <a name="azure-function-activity-in-azure-data-factory"></a>Azure Data Factory 中的 Azure 函式活動

Azure 函式活動可讓您在 Data Factory 管線中執行 [Azure 函式](../azure-functions/functions-overview.md)。 若要執行 Azure 函式，您需要建立連結服務連線和可指定所打算執行 Azure 函式的活動。

如需此功能的簡介與示範，請觀看下列 8 分鐘長的影片：

> [!VIDEO https://channel9.msdn.com/shows/azure-friday/Run-Azure-Functions-from-Azure-Data-Factory-pipelines/player]

## <a name="azure-function-linked-service"></a>Azure 函式連結服務

Azure 函式的傳回型別必須是有效的 `JObject`。 （請記住[，JArray](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_Linq_JArray.htm) *不是* `JObject`. ）任何返回類型，除了`JObject`失敗和引發使用者錯誤*回應內容不是有效的JObject。*

| **屬性** | **描述** | **必要** |
| --- | --- | --- |
| type   | 類型屬性必須設置為 **：Azure 函數** | 是 |
| 函式應用程式 URL | Azure 函式應用程式的 URL。 格式為 `https://<accountname>.azurewebsites.net`。 此 URL 是您在 Azure 入口網站中檢視函式應用程式時位於 [URL]**** 區段底下的值  | 是 |
| 函式金鑰 | Azure 函式的存取金鑰。 按一下個別函式的 [管理]**** 區段，然後複製 [函式金鑰]**** 或 [主機金鑰]****。 在此處瞭解更多資訊[：Azure 函數 HTTP 觸發器和綁定](../azure-functions/functions-bindings-http-webhook-trigger.md#authorization-keys) | 是 |
|   |   |   |

## <a name="azure-function-activity"></a>Azure 函式活動

| **屬性**  | **描述** | **允許的值** | **必要** |
| --- | --- | --- | --- |
| NAME  | 管線中的活動名稱  | String | 是 |
| type  | 活動類型是 ‘AzureFunctionActivity’ | String | 是 |
| 連結服務 | 相對應 Azure 函式應用程式的 Azure 函式連結服務  | 連結服務參考 | 是 |
| 函數名稱  | 此活動會在 Azure 函式應用程式中呼叫的函式名稱 | String | 是 |
| method  | 函式呼叫的 REST API 方法 | 字串支援類型："GET"、"POST"、"PUT"   | 是 |
| 頁首  | 傳送至要求的標頭。 例如，用來在要求上設定語言和類型："headers": { "Accept-Language": "en-us", "Content-Type": "application/json" } | 字串 (或含有字串之 resultType 的運算式) | 否 |
| body  | 與要求一起傳送至函式 API 方法的主體  | 字串 (或含有字串之 resultType 的運算式) 或物件。   | PUT/POST 方法的必要項 |
|   |   |   | |

請參閱 [請求有效負載架構部分中的請求有效負載的架構](control-flow-web-activity.md#request-payload-schema) 。

## <a name="routing-and-queries"></a>路由和查詢

Azure 函式活動支援**路由**。 例如，如果 Azure 函數具有終結點`https://functionAPP.azurewebsites.net/api/<functionName>/<value>?code=<secret>`，則`functionName`要在 Azure 函式活動中使用`<functionName>/<value>`的 是 。 您可以參數化此函數，以在運行時提供`functionName`所需的功能。

Azure 函式活動也支援**查詢**。 查詢必須作為 的一部分包含在 中`functionName`。 例如，當函數名稱`HttpTriggerCSharp`為，並且要包括的查詢為`name=hello`時，可以在 Azure 函式活動`functionName`中構造 為`HttpTriggerCSharp?name=hello`。 可以參數化此函數，以便在運行時確定該值。

## <a name="timeout-and-long-running-functions"></a>超時和長時間運行功能

Azure 函數在 230 秒後超時`functionTimeout`，而不管您在設置中配置了何種設置。 如需詳細資訊，請參閱 [本篇文章](../azure-functions/functions-versions.md#timeout)。 要解決此問題，請遵循非同步模式或使用持久函數。 持久函數的好處是，它們提供了自己的狀態跟蹤機制，因此您不必實現自己的狀態跟蹤機制。

在本文中瞭解有關持久函數[的更多詳細資訊](../azure-functions/durable/durable-functions-overview.md)。 可以設置 Azure 函式活動以調用持久函數，它將返回具有其他 URI 的回應，例如[此示例](../azure-functions/durable/durable-functions-http-features.md#http-api-url-discovery)。 由於`statusQueryGetUri`在函數運行時返回 HTTP 狀態 202，因此可以使用 Web 活動輪詢函數的狀態。 只需設置一個 Web 活動`url`，欄位設置為`@activity('<AzureFunctionActivityName>').output.statusQueryGetUri`。 持久函數完成後，函數的輸出將是 Web 活動的輸出。


## <a name="sample"></a>範例

您可以在此處找到使用 Azure 函數提取 tar[檔內容](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV2/UntarAzureFilesWithAzureFunction)的資料工廠的示例。

## <a name="next-steps"></a>後續步驟

在 [Azure Data Factory 中的管線和活動](concepts-pipelines-activities.md)中深入了解 Data Factory 中的活動。
