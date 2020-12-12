---
title: Azure 表單辨識器的嚴重損壞修復指導方針
titleSuffix: Azure Cognitive Services
description: 瞭解如何使用複製模型 API 來備份您的表單辨識器資源。
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: how-to
ms.date: 05/27/2020
ms.author: pafarley
ms.openlocfilehash: 168dcf994d00ba1cb2070665ada5a55cf86cfa4a
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/12/2020
ms.locfileid: "97359807"
---
# <a name="back-up-and-recover-your-form-recognizer-models"></a>備份和復原您的表單辨識器模型

當您在 Azure 入口網站中建立表單辨識器資源時，您會指定一個區域。 然後，您的資源和其所有作業都會與該特定的 Azure 伺服器區域相關聯。 這種情況很罕見，但不可能發生，因此會遇到網路問題，而無法達到整個區域。 如果您的解決方案必須一律可供使用，則您應該將其設計為容錯移轉至另一個區域，或將工作負載分割在兩個或更多個區域之間。 這兩種方法都需要在不同區域中至少有兩個表單辨識器資源，並且能夠跨區域同步處理 [自訂模型](./quickstarts/curl-train-extract.md) 。

複製應用程式開發介面可讓您將自訂模型從某個表單辨識器帳戶複製到其他的表單辨識器帳戶，或是可存在於任何支援的地理區域中，藉此實現 本指南說明如何搭配使用複製 REST API 與捲曲。 您也可以使用 HTTP 要求服務（例如 Postman）發出要求。

## <a name="business-scenarios"></a>商務案例

如果您的應用程式或業務相依于使用表單辨識器自訂模型，建議您將模型複製到另一個區域中的另一個表單辨識器帳戶。 如果發生區域性中斷，您就可以在複製的區域中存取您的模型。

##  <a name="prerequisites"></a>Prerequisites

1. 不同 Azure 區域中的兩個表單辨識器 Azure 資源。 如果您沒有這些專案，請移至 Azure 入口網站並 <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer" title=" 建立新的表單辨識器資源，以 " target="_blank"> 建立新的表單辨識器資源 <span class="docon docon-navigate-external x-hidden-focus"></span> </a> 。
1. 表單辨識器資源的訂用帳戶金鑰、端點 URL 和訂用帳戶識別碼。 您可以在 Azure 入口網站上資源的 [ **總覽** ] 索引標籤中找到這些值。


## <a name="copy-api-overview"></a>複製 API 總覽

複製自訂模型的套裝程式含下列步驟：

1. 首先，將複製授權要求發出至目標資源，也 &mdash; 就是將接收所複製模型的資源。 您會取得新建立之目標模型的 URL，以接收復制的資料。
1. 接下來，您會將 &mdash; 包含要複製之模型的資源傳送至來源資源。 您將會取回可查詢的 URL，以追蹤作業的進度。
1. 您將使用來源資源認證來查詢進度 URL，直到作業成功為止。 您也可以查詢目標資源中的新模型識別碼，以取得新模型的狀態。

> [!CAUTION]
> 複製 API 目前不支援用來 [撰寫自訂模型](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/Compose)的模型識別碼。 模型撰寫是在 2.1-preview. 2 preview 中的預覽功能。 

## <a name="generate-copy-authorization-request"></a>產生複製授權要求

下列 HTTP 要求會從您的目標資源取得複製授權。 您必須輸入目標資源的端點和金鑰作為標頭。

```
POST https://{TARGET_FORM_RECOGNIZER_RESOURCE_ENDPOINT}/formrecognizer/v2.0/custom/models/copyAuthorization HTTP/1.1
Ocp-Apim-Subscription-Key: {TARGET_FORM_RECOGNIZER_RESOURCE_API_KEY}
```

您將會 `201\Created` `modelId` 在主體中取得具有值的回應。 這個字串是新建立 (空白) 模型的識別碼。 `accessToken`API 需要將資料複製到此資源，且 `expirationDateTimeTicks` 值為權杖的到期日。 將這三個值全部儲存至安全的位置。

```
HTTP/1.1 201 Created
Location: https://{TARGET_FORM_RECOGNIZER_RESOURCE_ENDPOINT}/formrecognizer/v2.0/custom/models/33f4d42c-cd2f-4e74-b990-a1aeafab5a5d
{"modelId":"<your model ID>","accessToken":"<your access token>","expirationDateTimeTicks":637233481531659440}
```

## <a name="start-copy-operation"></a>開始複製作業

下列 HTTP 要求會在來源資源上啟動複製作業。 您必須輸入來源資源的端點和金鑰作為標頭。 請注意，要求 URL 包含您想要複製之來源模型的模型識別碼。

```
POST https://{SOURCE_FORM_RECOGNIZER_RESOURCE_ENDPOINT}/formrecognizer/v2.0/custom/models/<your model ID>/copy HTTP/1.1
Ocp-Apim-Subscription-Key: {SOURCE_FORM_RECOGNIZER_RESOURCE_API_KEY}
```

要求的主體必須具有下列格式。 您必須輸入目標資源的資源識別碼和區功能變數名稱稱。 您可以在 Azure 入口網站資源的 [ **屬性** ] 索引標籤中找到您的資源識別碼，也可以在 [ **金鑰和端點** ] 索引標籤上找到區功能變數名稱稱。您也需要從上一個步驟複製的模型識別碼、存取權杖和到期日值。

```json
{
   "targetResourceId": "{TARGET_AZURE_FORM_RECOGNIZER_RESOURCE_ID}",  
   "targetResourceRegion": "{TARGET_AZURE_FORM_RECOGNIZER_RESOURCE_REGION_NAME}",
   "copyAuthorization": {"modelId":"<your model ID>","accessToken":"<your access token>","expirationDateTimeTicks":637233481531659440}
}
```

> [!NOTE]
> 複製 API 會以透明的方式支援 [AEK/CMK](https://msazure.visualstudio.com/Cognitive%20Services/_wiki/wikis/Cognitive%20Services.wiki/52146/Customer-Managed-Keys) 功能。 這並不需要任何特殊處理，但請注意，如果您要在未加密的資源之間複製到加密資源，則必須包含要求標頭 `x-ms-forms-copy-degrade: true` 。 如果未包含此標頭，則複製作業會失敗並傳回 `DataProtectionTransformServiceError` 。

您將會收到 `202\Accepted` Operation-Location 標頭的回應。 此值是您將用來追蹤作業進度的 URL。 將它複製到暫存位置以供下一個步驟使用。

```
HTTP/1.1 202 Accepted
Operation-Location: https://{SOURCE_FORM_RECOGNIZER_RESOURCE_ENDPOINT}/formrecognizer/v2.0/custom/models/eccc3f13-8289-4020-ba16-9f1d1374e96f/copyresults/02989ba8-1296-499f-aaf4-55cfff41b8f1
```

### <a name="common-errors"></a>常見錯誤

|錯誤|解決方案|
|:--|:--|
| 400/不正確的要求 `"code:" "1002"` | 指出驗證錯誤或格式不正確的複製要求。 常見的問題包括：) 無效或已修改的承載 `copyAuthorization` 。 b) 權杖的過期值 `expirationDateTimeTicks` (承載 `copyAuhtorization` 的有效時間為24小時) 。 c) 無效或不受支援 `targetResourceRegion` 。 d) 無效或格式錯誤 `targetResourceId` 的字串。
|

## <a name="track-copy-progress"></a>追蹤複製進度

藉由查詢來源資源端點的「 **取得複本模型」結果** API 來追蹤您的進度。

```
GET https://{SOURCE_FORM_RECOGNIZER_RESOURCE_ENDPOINT}/formrecognizer/v2.0/custom/models/eccc3f13-8289-4020-ba16-9f1d1374e96f/copyresults/02989ba8-1296-499f-aaf4-55cfff41b8f1 HTTP/1.1
Ocp-Apim-Subscription-Key: {SOURCE_FORM_RECOGNIZER_RESOURCE_API_KEY}
```

您的回應會依作業的狀態而有所不同。 尋找 `"status"` JSON 主體中的欄位。 如果您要在腳本中自動化此 API 呼叫，建議您每秒查詢一次該作業。

```
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
{"status":"succeeded","createdDateTime":"2020-04-23T18:18:01.0275043Z","lastUpdatedDateTime":"2020-04-23T18:18:01.0275048Z","copyResult":{}}
```

### <a name="common-errors"></a>常見錯誤

|錯誤|解決方案|
|:--|:--|
|「錯誤」： [{"code"： "AuthorizationError"，<br>"message"： "授權失敗，原因為 <br>授權宣告遺失或無效。」}]   | 當從 API 傳回的內容修改內容時，就會發生 `copyAuthorization` `copyAuthorization` 。 請確定承載與先前呼叫所傳回的內容完全相同 `copyAuthorization` 。|
|「錯誤」： [{"code"： "AuthorizationError"，<br>"message"： "無法取出授權 <br>元。 如果此問題持續發生，請使用不同的 <br>要複製到其中的目標模型。」}] | 指出 `copyAuthorization` 正在使用複製要求來重複使用裝載。 成功的複製要求將不允許任何其他使用相同承載的要求 `copyAuthorization` 。 如果您引發個別的錯誤 (如下所述) ，然後再以相同的授權承載重試複製，則會引發此錯誤。 解決方法是產生新的承載 `copyAuthorization` ，然後重新發出複製要求。|
|「錯誤」： [{"code"： "DataProtectionTransformServiceError"，<br>"message"：「不允許資料傳輸要求 <br>因為它會降級為較不安全的資料保護配置。 請參閱檔或洽詢服務系統管理員 <br>以取得詳細資料。 "}]    | 在 `AEK` 已啟用的資源之間複製到未 `AEK` 啟用的資源時發生。 若要允許將加密的模型複製到目標做為未加密的，請指定 `x-ms-forms-copy-degrade: true` 具有複製要求的標頭。|
|「錯誤」： [{"code"： "ResourceResolverError"，<br>"message"： "無法為識別碼為 ' ... ' 的認知資源提取資訊。 請確認資源有效，且存在於指定的區域 ' westus2 ' 中」}] | 指出由指出的 Azure 資源 `targetResourceId` 不是有效的認知資源或不存在。 請確認並重新發出複製要求以解決此問題。|


### <a name="optional-track-the-target-model-id"></a>參數追蹤目標模型識別碼 

您也可以使用「 **取得自訂模型** API」來追蹤作業的狀態，方法是查詢目標模型。 使用您在第一個步驟中複製的目標模型識別碼來呼叫此 API。

```
GET https://{TARGET_FORM_RECOGNIZER_RESOURCE_ENDPOINT}/formrecognizer/v2.0/custom/models/33f4d42c-cd2f-4e74-b990-a1aeafab5a5d HTTP/1.1
Ocp-Apim-Subscription-Key: {TARGET_FORM_RECOGNIZER_RESOURCE_API_KEY}
```

在回應主體中，您會看到模型的相關資訊。 查看 `"status"` 模型狀態的欄位。

```
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
{"modelInfo":{"modelId":"33f4d42c-cd2f-4e74-b990-a1aeafab5a5d","status":"ready","createdDateTime":"2020-02-26T16:59:28Z","lastUpdatedDateTime":"2020-02-26T16:59:34Z"},"trainResult":{"trainingDocuments":[{"documentName":"0.pdf","pages":1,"errors":[],"status":"succeeded"},{"documentName":"1.pdf","pages":1,"errors":[],"status":"succeeded"},{"documentName":"2.pdf","pages":1,"errors":[],"status":"succeeded"},{"documentName":"3.pdf","pages":1,"errors":[],"status":"succeeded"},{"documentName":"4.pdf","pages":1,"errors":[],"status":"succeeded"}],"errors":[]}}
```

## <a name="curl-sample-code"></a>捲曲的範例程式碼

下列程式碼片段會使用捲曲來進行上述步驟中所述的 API 呼叫。 您仍然需要填入您專屬資源專屬的模型識別碼和訂用帳戶資訊。

### <a name="generate-copy-authorization-request"></a>產生複製授權要求

```bash
curl -i -X POST "https://{TARGET_FORM_RECOGNIZER_RESOURCE_ENDPOINT}/formrecognizer/v2.0/custom/models/copyAuthorization" -H "Ocp-Apim-Subscription-Key: {TARGET_FORM_RECOGNIZER_RESOURCE_API_KEY}" 
```

### <a name="start-copy-operation"></a>開始複製作業

```bash
curl -i -X POST "https://{TARGET_FORM_RECOGNIZER_RESOURCE_ENDPOINT}/formrecognizer/v2.0/custom/models/copyAuthorization" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {TARGET_FORM_RECOGNIZER_RESOURCE_API_KEY}" --data-ascii "{ \"targetResourceId\": \"{TARGET_AZURE_FORM_RECOGNIZER_RESOURCE_ID}\",   \"targetResourceRegion\": \"{TARGET_AZURE_FORM_RECOGNIZER_RESOURCE_REGION_NAME}\", \"copyAuthorization\": "{\"modelId\":\"33f4d42c-cd2f-4e74-b990-a1aeafab5a5d\",\"accessToken\":\"1855fe23-5ffc-427b-aab2-e5196641502f\",\"expirationDateTimeTicks\":637233481531659440}"}"
```

### <a name="track-copy-progress"></a>追蹤複製進度

```bash
curl -i GET "https://<SOURCE_FORM_RECOGNIZER_RESOURCE_ENDPOINT>/formrecognizer/v2.0/custom/models/{SOURCE_MODELID}/copyResults/{RESULT_ID}" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {SOURCE_FORM_RECOGNIZER_RESOURCE_API_KEY}"
```

## <a name="next-steps"></a>後續步驟

在本指南中，您已瞭解如何使用複製 API 將自訂模型備份至次要表單辨識器資源。 接下來，探索 API 參考檔，以瞭解表單辨識器的其他用途。
* [REST API 參考文件](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeWithCustomForm)
