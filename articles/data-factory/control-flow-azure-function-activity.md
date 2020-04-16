---
title: Azure 資料工廠中的 Azure 函數活動
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
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81415315"
---
# <a name="azure-function-activity-in-azure-data-factory"></a>Azure Data Factory 中的 Azure 函式活動
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]
Azure 函式活動可讓您在 Data Factory 管線中執行 [Azure 函式](../azure-functions/functions-overview.md)。 若要執行 Azure 函式，您需要建立連結服務連線和可指定所打算執行 Azure 函式的活動。

如需此功能的簡介與示範，請觀看下列 8 分鐘長的影片：

> [!VIDEO https://channel9.msdn.com/shows/azure-friday/Run-Azure-Functions-from-Azure-Data-Factory-pipelines/player]

## <a name="azure-function-linked-service"></a>Azure 函式連結服務

Azure 函式的傳回型別必須是有效的 `JObject`。 (請記住[,JArray](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_Linq_JArray.htm) *不是*`JObject`. )任何返回類型,除了`JObject`失敗和引發使用者錯誤*回應內容不是有效的JObject。*

| **屬性** | **說明** | **必要** |
| --- | --- | --- |
| type   | 類型屬性必須設定**為 :Azure 函數** | 是 |
| 函式應用程式 URL | Azure 函式應用程式的 URL。 格式為 `https://<accountname>.azurewebsites.net`。 此 URL 是您在 Azure 入口網站中檢視函式應用程式時位於 [URL]**** 區段底下的值  | 是 |
| 函式金鑰 | Azure 函式的存取金鑰。 按一下個別函式的 [管理]**** 區段，然後複製 [函式金鑰]**** 或 [主機金鑰]****。 在此處瞭解更多資訊[:Azure 函數 HTTP 觸發器和綁定](../azure-functions/functions-bindings-http-webhook-trigger.md#authorization-keys) | 是 |
|   |   |   |

## <a name="azure-function-activity"></a>Azure 函式活動

| **屬性**  | **說明** | **允許的值** | **必要** |
| --- | --- | --- | --- |
| NAME  | 管線中的活動名稱  | String | 是 |
| type  | 活動類型是 ‘AzureFunctionActivity’ | String | 是 |
| 連結服務 | 相對應 Azure 函式應用程式的 Azure 函式連結服務  | 連結服務參考 | 是 |
| 函數名稱  | 此活動會在 Azure 函式應用程式中呼叫的函式名稱 | String | 是 |
| method  | 函式呼叫的 REST API 方法 | 字串支援類型:"GET"、"POST"、"PUT"   | 是 |
| 頁首  | 傳送至要求的標頭。 例如，用來在要求上設定語言和類型："headers": { "Accept-Language": "en-us", "Content-Type": "application/json" } | 字串 (或含有字串之 resultType 的運算式) | 否 |
| body  | 與要求一起傳送至函式 API 方法的主體  | 字串 (或含有字串之 resultType 的運算式) 或物件。   | PUT/POST 方法的必要項 |
|   |   |   | |

請參考 [要求有效負載架構部分的要求有效負載的架構](control-flow-web-activity.md#request-payload-schema) 。

## <a name="routing-and-queries"></a>路由與查詢

Azure 函式活動支援**路由**。 例如,如果 Azure 函數具有`https://functionAPP.azurewebsites.net/api/<functionName>/<value>?code=<secret>`終結點`functionName`,則要在 Azure`<functionName>/<value>`函數活動中使用的 是 。 您可以參數化此函數,以在運行時提供`functionName`所需的功能。

Azure 函式活動也支援**查詢**。 查詢必須作為的一部份包含在中`functionName`。 例如,當函數名稱`HttpTriggerCSharp`為,並且要包括的查詢`name=hello`為 時,可以在 Azure`functionName`函數活動`HttpTriggerCSharp?name=hello`中建構為 。 可以參數化此函數,以便在運行時確定該值。

## <a name="timeout-and-long-running-functions"></a>逾時與長時間執行功能

Azure 函數在 230 秒`functionTimeout`後超時 ,而不管您在設置中配置了何種設置。 如需詳細資訊，請參閱 [本篇文章](../azure-functions/functions-versions.md#timeout)。 要解決此問題,請遵循非同步模式或使用持久函數。 持久函數的好處是,它們提供了自己的狀態跟蹤機制,因此您不必實現自己的狀態跟蹤機制。

在本文中瞭解有關持久函數[的詳細資訊](../azure-functions/durable/durable-functions-overview.md)。 可以設定 Azure 函數活動以呼叫持久函數,它將傳回具有其他 URI 的回應,例如[此範例](../azure-functions/durable/durable-functions-http-features.md#http-api-url-discovery)。 由於`statusQueryGetUri`在函數運行時返回 HTTP 狀態 202,因此可以使用 Web 活動輪詢函數的狀態。 只要設定網頁活動`url`,欄位設定為`@activity('<AzureFunctionActivityName>').output.statusQueryGetUri`。 持久函數完成後,函數的輸出將是 Web 活動的輸出。


## <a name="sample"></a>範例

您可以在此處找到使用 Azure 函數提取 tar[檔內容](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV2/UntarAzureFilesWithAzureFunction)的資料工廠的範例。

## <a name="next-steps"></a>後續步驟

在 [Azure Data Factory 中的管線和活動](concepts-pipelines-activities.md)中深入了解 Data Factory 中的活動。
