---
title: Azure 表單辨識器的嚴重損壞修復指引
titleSuffix: Azure Cognitive Services
description: 瞭解如何使用複製模型 API 來備份您的表單辨識器資源。
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: how-to
ms.date: 05/27/2020
ms.author: pafarley
ms.openlocfilehash: 2e5b32421a04e09bd32d2bba21ff4faf920d84dd
ms.sourcegitcommit: 12f23307f8fedc02cd6f736121a2a9cea72e9454
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/30/2020
ms.locfileid: "84221862"
---
# <a name="back-up-and-recover-your-form-recognizer-models"></a>備份和復原您的表單辨識器模型

當您在 Azure 入口網站中建立表單辨識器資源時，您可以指定區域。 從之後開始，您的資源及其所有作業都會與該特定的 Azure 伺服器區域相關聯。 這種情況很罕見，但並不不可能發生，因此會遇到網路問題而無法達到整個區域。 如果您的解決方案必須一律可供使用，您應該將它設計為故障轉換至另一個區域，或在兩個或多個區域之間分割工作負載。 這兩種方法都需要在不同的區域中至少有兩個表單辨識器資源，以及跨區域同步處理[自訂模型](./quickstarts/curl-train-extract.md)的能力。

複製 API 可讓您將自訂模型從一個表單辨識器帳戶或其他專案（可能存在於任何支援的地理區域）複製到其他人，藉此達成此案例。 本指南說明如何搭配使用複製 REST API 與捲曲。 您也可以使用 HTTP 要求服務（例如 Postman）來發出要求。

## <a name="business-scenarios"></a>商務案例

如果您的應用程式或業務取決於使用表單辨識器自訂模型，我們建議您將模型複製到另一個區域中的另一個表單辨識器帳戶。 如果發生區域性中斷，您可以接著在其複製所在的區域中存取您的模型。

##  <a name="prerequisites"></a>Prerequisites

1. 不同 Azure 區域中的兩個表單辨識器 Azure 資源。 如果您沒有這些專案，請移至 Azure 入口網站，並 <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer" title=" 建立新的表單辨識器資源 " target="_blank"> 建立新的表單辨識器資源 <span class="docon docon-navigate-external x-hidden-focus"></span> </a> 。
1. 您的表單辨識器資源的訂用帳戶金鑰、端點 URL 和訂用帳戶識別碼。 您可以在 [Azure 入口網站上資源的 **[總覽**] 索引標籤中找到這些值。


## <a name="copy-api-overview"></a>複製 API 總覽

複製自訂模型的套裝程式含下列步驟：

1. 首先，您會對目標資源發出複製授權要求 &mdash; ，也就是將接收所複製模型的資源。 您會取回新建立之目標模型的 URL，這會接收復制的資料。
1. 接下來，您會將複製要求傳送至來源資源， &mdash; 此資源包含要複製的模型。 您將取得可查詢的 URL，以追蹤作業的進度。
1. 您將使用來源資源認證來查詢進度 URL，直到作業成功為止。 您也可以在目標資源中查詢新的模型識別碼，以取得新模型的狀態。

## <a name="generate-copy-authorization-request"></a>產生複製授權要求

下列 HTTP 要求會從您的目標資源取得複製授權。 您必須將目標資源的端點和金鑰輸入為標頭。

```
POST https://{TARGET_FORM_RECOGNIZER_RESOURCE_ENDPOINT}/formrecognizer/v2.0-preview/custom/models/copyAuthorization HTTP/1.1
Ocp-Apim-Subscription-Key: {TARGET_FORM_RECOGNIZER_RESOURCE_API_KEY}
```

您會 `201\Created` `modelId` 在本文中收到具有值的回應。 這個字串是新建立（空白）模型的識別碼。 `accessToken`API 需要有，才能將資料複製到此資源，而 `expirationDateTimeTicks` 值為權杖的到期日。 將這三個值全部儲存到安全的位置。

```
HTTP/1.1 201 Created
Location: https://{TARGET_FORM_RECOGNIZER_RESOURCE_ENDPOINT}/formrecognizer/v2.0-preview/custom/models/33f4d42c-cd2f-4e74-b990-a1aeafab5a5d
{"modelId":"33f4d42c-cd2f-4e74-b990-a1aeafab5a5d","accessToken":"1855fe23-5ffc-427b-aab2-e5196641502f","expirationDateTimeTicks":637233481531659440}
```

## <a name="start-copy-operation"></a>開始複製作業

下列 HTTP 要求會在來源資源上啟動複製作業。 您必須輸入來源資源的端點和金鑰做為標頭。 請注意，要求 URL 包含您要複製之來源模型的模型識別碼。

```
POST https://{SOURCE_FORM_RECOGNIZER_RESOURCE_ENDPOINT}/formrecognizer/v2.0-preview/custom/models/eccc3f13-8289-4020-ba16-9f1d1374e96f/copy HTTP/1.1
Ocp-Apim-Subscription-Key: {SOURCE_FORM_RECOGNIZER_RESOURCE_API_KEY}
```

您的要求主體必須具有下列格式。 您必須輸入目標資源的資源識別碼和區功能變數名稱稱。 您也需要從上一個步驟複製的 [模型識別碼]、[存取權杖] 和 [到期] 值。

```json
{
   "targetResourceId": "{TARGET_AZURE_FORM_RECOGNIZER_RESOURCE_ID}",  
   "targetResourceRegion": "{TARGET_AZURE_FORM_RECOGNIZER_RESOURCE_REGION_NAME}",
   "copyAuthorization": {"modelId":"33f4d42c-cd2f-4e74-b990-a1aeafab5a5d","accessToken":"1855fe23-5ffc-427b-aab2-e5196641502f","expirationDateTimeTicks":637233481531659440}
}
```

您會收到 `202\Accepted` 操作-位置標頭的回應。 這個值是您將用來追蹤作業進度的 URL。 將它複製到暫存位置以進行下一個步驟。

```
HTTP/1.1 202 Accepted
Operation-Location: https://{SOURCE_FORM_RECOGNIZER_RESOURCE_ENDPOINT}/formrecognizer/v2.0-preview/custom/models/eccc3f13-8289-4020-ba16-9f1d1374e96f/copyresults/02989ba8-1296-499f-aaf4-55cfff41b8f1
```

## <a name="track-copy-progress"></a>追蹤複製進度

藉由查詢來源資源端點的「**取得複本模型」結果**API，來追蹤您的進度。

```
GET https://{SOURCE_FORM_RECOGNIZER_RESOURCE_ENDPOINT}/formrecognizer/v2.0-preview/custom/models/eccc3f13-8289-4020-ba16-9f1d1374e96f/copyresults/02989ba8-1296-499f-aaf4-55cfff41b8f1 HTTP/1.1
Ocp-Apim-Subscription-Key: {SOURCE_FORM_RECOGNIZER_RESOURCE_API_KEY}
```

您的回應會隨著作業的狀態而有所不同。 尋找 `"status"` JSON 主體中的欄位。 如果您要在腳本中自動化此 API 呼叫，我們建議每秒查詢一次作業。

```
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
{"status":"succeeded","createdDateTime":"2020-04-23T18:18:01.0275043Z","lastUpdatedDateTime":"2020-04-23T18:18:01.0275048Z","copyResult":{}}
```

### <a name="optional-track-the-target-model-id"></a>選擇性追蹤目標模型識別碼 

您也可以藉由查詢目標模型，使用**取得自訂模型**API 來追蹤作業的狀態。 使用您在第一個步驟中複製的目標模型識別碼來呼叫此 API。

```
GET https://{TARGET_FORM_RECOGNIZER_RESOURCE_ENDPOINT}/formrecognizer/v2.0-preview/custom/models/33f4d42c-cd2f-4e74-b990-a1aeafab5a5d HTTP/1.1
Ocp-Apim-Subscription-Key: {TARGET_FORM_RECOGNIZER_RESOURCE_API_KEY}
```

在回應主體中，您會看到模型的相關資訊。 檢查 `"status"` 模型狀態的欄位。

```
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
{"modelInfo":{"modelId":"33f4d42c-cd2f-4e74-b990-a1aeafab5a5d","status":"ready","createdDateTime":"2020-02-26T16:59:28Z","lastUpdatedDateTime":"2020-02-26T16:59:34Z"},"trainResult":{"trainingDocuments":[{"documentName":"0.pdf","pages":1,"errors":[],"status":"succeeded"},{"documentName":"1.pdf","pages":1,"errors":[],"status":"succeeded"},{"documentName":"2.pdf","pages":1,"errors":[],"status":"succeeded"},{"documentName":"3.pdf","pages":1,"errors":[],"status":"succeeded"},{"documentName":"4.pdf","pages":1,"errors":[],"status":"succeeded"}],"errors":[]}}
```

## <a name="curl-sample-code"></a>捲曲範例程式碼

下列程式碼片段會使用捲曲來進行上述步驟中所述的 API 呼叫。 您仍然需要填入您自己資源特有的模型識別碼和訂用帳戶資訊。

### <a name="generate-copy-authorization-request"></a>產生複製授權要求

```bash
curl -i -X POST "https://{TARGET_FORM_RECOGNIZER_RESOURCE_ENDPOINT}/formrecognizer/v2.0-preview/custom/models/copyAuthorization" -H "Ocp-Apim-Subscription-Key: {TARGET_FORM_RECOGNIZER_RESOURCE_API_KEY}" 
```

### <a name="start-copy-operation"></a>開始複製作業

```bash
curl -i -X POST "https://{TARGET_FORM_RECOGNIZER_RESOURCE_ENDPOINT}/formrecognizer/v2.0-preview/custom/models/copyAuthorization" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {TARGET_FORM_RECOGNIZER_RESOURCE_API_KEY}" --data-ascii "{ \"targetResourceId\": \"{TARGET_AZURE_FORM_RECOGNIZER_RESOURCE_ID}\",   \"targetResourceRegion\": \"{TARGET_AZURE_FORM_RECOGNIZER_RESOURCE_REGION_NAME}\", \"copyAuthorization\": "{\"modelId\":\"33f4d42c-cd2f-4e74-b990-a1aeafab5a5d\",\"accessToken\":\"1855fe23-5ffc-427b-aab2-e5196641502f\",\"expirationDateTimeTicks\":637233481531659440}"}"
```

### <a name="track-copy-progress"></a>追蹤複製進度

```bash
curl -i GET "https://<SOURCE_FORM_RECOGNIZER_RESOURCE_ENDPOINT>/formrecognizer/v2.0-preview/custom/models/{SOURCE_MODELID}/copyResults/{RESULT_ID}" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {SOURCE_FORM_RECOGNIZER_RESOURCE_API_KEY}"
```

## <a name="next-steps"></a>後續步驟

在本指南中，您已瞭解如何使用複製 API，將您的自訂模型備份至次要表單辨識器資源。 接下來，探索 API 參考檔，以瞭解您可以使用表單辨識器執行的其他工作。
* [REST API 參考文件](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/AnalyzeWithCustomForm)