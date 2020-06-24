---
title: 表單辨識器的新功能
titleSuffix: Azure Cognitive Services
description: 瞭解表單辨識器 API 的最新變更。
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 05/19/2020
ms.author: pafarley
ms.openlocfilehash: 7898fcf9b149f50f8478576d18cd3c3b984eb937
ms.sourcegitcommit: 6fd28c1e5cf6872fb28691c7dd307a5e4bc71228
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/23/2020
ms.locfileid: "85209091"
---
# <a name="whats-new-in-form-recognizer"></a>表單辨識器的新功能

表單辨識器服務會持續更新。 使用這篇文章來掌握功能增強、修正和檔更新的最新狀態。

> [!NOTE]
> 表單辨識器的快速入門和指南一律會使用 API 的最新版本，除非有指定。

## <a name="june-2020"></a>2020 年 6 月

### <a name="new-features"></a>新功能
* **COPYMODEL API 已新增至用戶端 sdk**您現在可以使用用戶端 Sdk，將模型從一個訂用帳戶複製到另一個。 如需這項功能的一般資訊，請參閱[備份和復原模型](./disaster-recovery.md)。
* **Azure Active Directory 整合**您現在可以使用 AAD 認證來驗證 Sdk 中的表單辨識器用戶端物件。
* **SDK 特定的變更**這包括次要功能新增和重大變更。 如需詳細資訊，請參閱 SDK releases。
  * [C # SDK Preview 3 變更記錄](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/formrecognizer/Azure.AI.FormRecognizer/CHANGELOG.md)
  * [Python SDK Preview 3 變更記錄](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/formrecognizer/azure-ai-formrecognizer/CHANGELOG.md)
  * [JAVA SDK Preview 3 變更記錄](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/formrecognizer/azure-ai-formrecognizer/CHANGELOG.md)
  * [JavaScript SDK Preview 3 變更記錄](https://github.com/Azure/azure-sdk-for-js/blob/%40azure/ai-form-recognizer_1.0.0-preview.3/sdk/formrecognizer/ai-form-recognizer/CHANGELOG.md)

## <a name="april-2020"></a>2020 年 4 月

### <a name="new-features"></a>新功能
* **表單辨識器 API V2.0 公開預覽的 SDK 支援**本月份我們已擴充服務支援，以包含適用于表單辨識器 v2.0 （預覽）版本的預覽 SDK。 使用下列連結以開始使用您選擇的語言： 
   * [.NET SDK](https://docs.microsoft.com/dotnet/api/overview/azure/formrecognizer)
   * [Java SDK](https://docs.microsoft.com/java/api/overview/azure/formrecognizer)
   * [Python SDK](https://docs.microsoft.com/python/api/overview/azure/formrecognizer)
   * [JavaScript SDK](https://docs.microsoft.com/javascript/api/overview/azure/formrecognizer)

  新的 SDK 支援適用于表單辨識器的 v2.0 REST API 的所有功能。 例如，您可以將具有或不含標籤的模型定型，並從表單中解壓縮文字、索引鍵值組和資料表、使用預先建立的回條服務從回條解壓縮資料，以及使用您的檔中的版面佈建服務來解壓縮文字和資料表。 您可以透過[Sdk 意見反應表單](https://aka.ms/FR_SDK_v1_feedback)，在 sdk 上分享您的意見反應。
 
* **複製自訂模型**您現在可以使用新的「複製自訂模型」功能，在區域與訂用帳戶之間複製模型。 叫用複製自訂模型 API 之前，您必須先針對目標資源端點呼叫複製授權作業，以取得要複製到目標資源的授權。
   * [產生複製授權](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-previewoperations/CopyCustomFormModelAuthorization)REST API
   * [複製自訂模型](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-previewoperations/CopyCustomFormModel)REST API 

### <a name="security-improvements"></a>安全性改善

* 客戶管理的金鑰現在可供 FormRecognizer。 如需詳細資訊，請參閱[表單辨識器的待用資料加密](https://docs.microsoft.com/azure/cognitive-services/form-recognizer/form-recognizer-encryption-of-data-at-rest)。
* 使用受控識別，以 Azure Active Directory 存取 Azure 資源。 如需詳細資訊，請參閱[授權存取受控](https://docs.microsoft.com/azure/cognitive-services/authentication#authorize-access-to-managed-identities)識別。

## <a name="march-2020"></a>2020 年 3 月 

### <a name="new-features"></a>新功能

* **標記的實數值型別**您現在可以使用表單辨識器範例標籤工具來指定您要加上標籤的數值型別。 目前支援下列值類型和變化：
  * `string`
    * 預設值、`no-whitespaces`、`alphanumeric`
  * `number`
    * 預設值、`currency`
  * `date` 
    * 預設值、`dmy`、`mdy`、`ymd`
  * `time`
  * `integer`

  請參閱[範例標籤工具](./quickstarts/label-tool.md#specify-tag-value-types)指南，以瞭解如何使用此功能。


* **資料表視覺效果**範例標籤工具現在會顯示檔中已辨識的資料表。 這可讓您在標記和分析之前，先查看已經從檔中辨識和解壓縮的資料表。 您可以使用 [圖層] 選項來開啟/關閉這項功能。

  這是如何辨識和解壓縮資料表的範例：

  > [!div class="mx-imgBorder"]
  > ![使用範例標籤工具的資料表視覺效果](./media/whats-new/formre-table-viz.png)

    已解壓縮的資料表可在的 JSON 輸出中取得 `"pageResults"` 。

  > [!IMPORTANT]
  > 不支援標記資料表。 如果無法辨識資料表並自動 extrated，您只能將它們標示為索引鍵/值組。 將資料表標記為索引鍵/值組時，會將每個資料格標記為唯一值。

### <a name="extraction-enhancements"></a>提取增強功能

此版本包含解壓縮增強功能和精確度改善，特別是在同一行文字中標記和解壓縮多個索引鍵/值組的功能。 
 
### <a name="sample-labeling-tool-is-now-open-source"></a>範例圖章工具現在已開放原始碼

表單辨識器範例圖章工具現在以開放原始碼專案的形式提供。 您可以將它整合到您的解決方案中，並進行客戶特定變更以符合您的需求。

如需表單辨識器範例圖章工具的詳細資訊，請參閱[GitHub](https://github.com/microsoft/OCR-Form-Tools/blob/master/README.md)上提供的檔。

### <a name="tls-12-enforcement"></a>TLS 1.2 強制執行

現在對於此服務的所有 HTTP 要求都會強制執行 TLS 1.2。 如需詳細資訊，請參閱 [Azure 認知服務安全性](../cognitive-services-security.md)。

## <a name="january-2020"></a>2020 年 1 月

此版本導入了表單辨識器2.0 （預覽）。 在下列各節中，您可以找到有關新功能、增強功能和變更的詳細資訊。 

### <a name="new-features"></a>新功能

* **自訂模型**
  * **使用標籤進行定型**您現在可以使用手動加上標籤的資料來定型自訂模型。 這會造就表現更佳的模型，並可產生可搭配複雜表單或含有值 (但不含索引鍵) 的表單使用的模型。
  * **非同步 API**您可以使用非同步 API 呼叫來定型和分析大型資料集和檔案。
  * **TIFF 檔案支援**您現在可以使用 TIFF 檔來定型和解壓縮資料。
  * **提取精確度改進**

* **預先建置的回條模型**
  * **秘訣數量**您現在可以將秘訣數量和其他手寫值解壓縮。
  * **明細專案解壓縮**您可以從收據中解壓縮行專案值。
  * **信賴值**您可以針對每個已解壓縮的值，查看模型的信賴度。
  * **提取精確度改進**

* **版面配置解壓縮**您現在可以使用版面配置 API，從您的表單中解壓縮文字資料和資料表資料。

### <a name="custom-model-api-changes"></a>自訂模型 API 變更

定型和使用自訂模型的所有 Api 都已重新命名，而某些同步方法現在是非同步。 以下是主要變更：

* 定型模型的程式現在是非同步。 您會透過 **/Custom/models** API 呼叫來起始訓練。 此呼叫會傳回作業識別碼，您可以將其傳遞至**自訂/模型/{modelID}** 以傳回定型結果。
* **/Custom/models/{modelID}/analyze** API 呼叫現在會起始索引鍵/值的提取。 此呼叫會傳回作業識別碼，您可以將其傳遞至**自訂/模型/{modelID}/analyzeResults/{resultID}** ，以返回提取結果。
* 定型作業的作業識別碼現在可以在 HTTP 回應的**位置**標頭中找到，而不是在作業 **-位置**標頭中。

### <a name="receipt-api-changes"></a>接收 API 變更

用於讀取銷售收據的 Api 已重新命名。

* **/Prebuilt/receipt/analyze** API 呼叫現在會起始接收資料提取。 此呼叫會傳回作業識別碼，您可以將其傳遞至 **/prebuilt/receipt/analyzeResults/{resultID}** ，以傳回提取結果。

### <a name="output-format-changes"></a>輸出格式變更

所有 API 呼叫的 JSON 回應都有新的格式。 某些索引鍵和值已加入、移除或重新命名。 如需目前 JSON 格式的範例，請參閱快速入門。

## <a name="next-steps"></a>後續步驟

請完成[快速入門](quickstarts/curl-train-extract.md)來開始使用[表單辨識器 API](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-previewoperations/AnalyzeWithCustomForm)。