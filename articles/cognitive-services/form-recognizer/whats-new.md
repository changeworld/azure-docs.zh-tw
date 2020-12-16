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
ms.openlocfilehash: 3f71cef19d25a7f987af4147ae0a889280e49bd1
ms.sourcegitcommit: 77ab078e255034bd1a8db499eec6fe9b093a8e4f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/16/2020
ms.locfileid: "97563155"
---
# <a name="whats-new-in-form-recognizer"></a>表單辨識器的新功能

表單辨識器服務會持續更新。 使用這篇文章隨時掌握最新的功能增強功能、修正及檔更新。

## <a name="november-2020"></a>2020 年 11 月

### <a name="new-features"></a>新功能

**表單辨識器2.1 版公開 preview 2 現已推出。** 2.1-preview. 2 已發行，包含下列功能： 

- **新的預** 建發票模型-新的預建發票模型可讓客戶以各種不同的格式取得發票，並傳回結構化資料，以自動化發票處理。 它結合了強大的光學字元辨識 (OCR) 功能，以及瞭解深度學習模型，以英文的發票來解壓縮重要資訊。 它會將文字、資料表和資訊（例如客戶、供應商、發票識別碼、發票到期日、總計、到期日、稅額、出貨至帳單等等）解壓縮。

  > [深入瞭解預建發票模型](concept-invoices.md)

  :::image type="content" source="./media/invoice-example.jpg" alt-text="發票範例" lightbox="./media/invoice-example.jpg":::

- **增強型資料表解壓縮** -表單辨識器現在提供增強的資料表解壓縮，其結合了強大的光學字元辨識 (OCR) 功能與深度學習資料表的提取模型。 表單辨識器可以從資料表中提取資料，包括合併資料行、資料列、無框線等的複雜資料表。 
 
  :::image type="content" source="./media/tables-example.jpg" alt-text="資料表範例" lightbox="./media/tables-example.jpg":::

 
  > [深入瞭解版面配置解壓縮](concept-layout.md)

- **用戶端程式庫更新** -適用于 .Net、Python、JAVA 和 JavaScript 的最新 [用戶端程式庫](quickstarts/client-library.md) 版本支援表單辨識器 2.1 API。
- **支援的新語言：日文** -現在支援下列新語言：適用于 `AnalyzeLayout` 和 `AnalyzeCustomForm` ：日文 (`ja`) 。 [語言支援](language-support.md)
- **文字行樣式指示 (手寫/其他)  (僅限拉丁語言)** 表單辨識器現在 `appearance` 會輸出物件，以分類每個文字行是否為手寫樣式，以及信賴分數。 這項功能僅支援拉丁語言。
- **品質改進** -解壓縮改進，包括單一數位的解壓縮改進。
- 使用表單辨識器範例標籤工具，**在表單辨識器範例和標籤工具中新的**「立即試用」功能：試用預建的發票、收據和名片模型，以及使用表單辨識器範例標籤工具的版面配置 API。 瞭解如何將資料解壓縮，而不需要撰寫任何程式碼。

  > [試用表單辨識器範例工具](https://fott-preview.azurewebsites.net/)

  ![FOTT 範例](./media/ui-preview.jpg)
  
- **意見反應迴圈** -透過範例標籤工具分析檔案時，您現在也可以將它新增至定型集，並視需要調整標籤，並定型以改善模型。
- **自動標籤檔** -根據專案中先前加上標籤的檔，自動標記其他檔。

## <a name="august-2020"></a>2020 年 8 月

### <a name="new-features"></a>新功能

**表單辨識器2.1 版公開預覽版現已推出。** 2.1-preview. 1 已發行，包含下列功能： 


- **REST API 參考可供使用** -請參閱 [2.1-preview。1參考](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-1/operations/AnalyzeBusinessCardAsync) 
- **除了英文以外，還支援的新語言**，現在支援下列 [語言](language-support.md) ：適用于 `Layout` 和 `Train Custom Model` ：英文 (`en`) 、中文 (簡體)  () `zh-Hans` 、荷蘭文 (`nl`) 、法文 (`fr`) 、德文 (`de`) 、義大利文 () 、葡萄牙文 () `it` `pt` 和西班牙 `es` 文 () 。
- **核取方塊/選取標記偵測** -表單辨識器支援偵測和解壓縮選取標記，例如核取方塊和選項按鈕。 選取標記會在中解壓縮 `Layout` ，您現在也可以使用標籤來標記和定型， `Train Custom Model`  -  以將選取專案標記的索引鍵值組解壓縮。 
- **模型撰寫** -允許以單一模型識別碼撰寫和呼叫多個模型。 提交檔以使用撰寫的模型識別碼進行分析時，會先執行分類步驟，將其路由至正確的自訂模型。 模型撰寫適用于 `Train Custom Model`  -  _使用標籤進行定型_。
- **模型名稱** -將易記名稱新增至自訂模型，以方便管理及追蹤。
- **[新的智慧卡預先建立的模型](concept-business-cards.md)** ，用來解壓縮英文、語言名片的一般欄位。
- 新的地區設定（除了 EN-US 以外）**[預先建立的新地區](concept-receipts.md)** 設定，現在可供 EN-US、半 CA、EN-GB、en-us
- 的 **品質改進** `Layout` 、 `Train Custom Model`  -  _無標籤定型_，以及 _使用標籤定型_。


**2.0 版** 包含下列更新：

- NET、Python、JAVA 和 JavaScript 的 [用戶端程式庫](quickstarts/client-library.md) 已進入正式運作狀態。 


您可以在 GitHub 上取得 **新的範例**。 
- [知識提取食譜-Forms](https://github.com/microsoft/knowledge-extraction-recipes-forms)腳本會從真實的表單辨識器客戶參與收集最佳作法，並提供用於開發這些專案的可用程式碼範例、檢查清單和範例管線。 
- [範例標籤工具](https://github.com/microsoft/OCR-Form-Tools)已更新，可支援新的2.1 版功能。 請參閱本 [快速入門](quickstarts/label-tool.md) 以瞭解如何開始使用此工具。 
- [智慧型 Kiosk](https://github.com/microsoft/Cognitive-Samples-IntelligentKiosk/blob/master/Documentation/FormRecognizer.md)表單辨識器範例會示範如何 `Analyze Receipt` 在 `Train Custom Model`  -  _沒有標籤的情況下_ 進行整合和定型。



## <a name="july-2020"></a>2020 年 7 月

### <a name="new-features"></a>新功能

* **2.0 版參考可用** -請參閱 V2.0 [API 參考](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeWithCustomForm) ，以及適用于 [.net](/dotnet/api/overview/azure/ai.formrecognizer-readme?view=azure-dotnet)、 [Python](/python/api/overview/azure/?view=azure-python)、 [JAVA](/java/api/overview/azure/ai-formrecognizer-readme?view=azure-java-preview)和 [JavaScript](/javascript/api/overview/azure/?view=azure-node-latest)的更新 sdk。
* **資料表增強功能和解壓縮增強功能** -包括精確度改良和資料表提取增強功能，特別是在 _不含標籤的自訂定型_ 中學習資料表標頭和結構的功能。 

* **貨幣支援** -全域貨幣符號的偵測和解壓縮。
* **Azure Gov** -表單辨識器現在也可在 azure Gov 中使用。
* **增強的安全性功能**： 
   * **攜帶您自己的金鑰** 表單辨識器會在保存到雲端時自動加密您的資料，以保護它，並協助您符合組織的安全性和合規性承諾。 根據預設，您的訂用帳戶會使用由 Microsoft 管理的加密金鑰。 您現在也可以使用自己的加密金鑰來管理您的訂用帳戶。 [客戶管理的金鑰（也稱為「攜帶您自己的金鑰」 (BYOK) ](./form-recognizer-encryption-of-data-at-rest.md)）提供更大的彈性來建立、輪替、停用及撤銷存取控制。 您也可稽核用來保護資料的加密金鑰。  
   * **私人端點** –可讓您在虛擬網路上 (VNet) ，以透過 [Private Link 安全地存取資料。](../../private-link/private-link-overview.md)


## <a name="june-2020"></a>2020 年 6 月

### <a name="new-features"></a>新功能
* **COPYMODEL API 已新增至用戶端 sdk** -您現在可以使用用戶端 sdk，將模型從某個訂用帳戶複製到另一個訂用帳戶。 如需這項功能的一般資訊，請參閱 [備份和復原模型](./disaster-recovery.md) 。
* **Azure Active Directory 整合** -您現在可以使用 Azure AD 認證來驗證 sdk 中的表單辨識器用戶端物件。
* **SDK 特定的變更** ，包括次要功能的新增和重大變更。 如需詳細資訊，請參閱 SDK 變更記錄。
  * [C # SDK Preview 3 變更記錄](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/formrecognizer/Azure.AI.FormRecognizer/CHANGELOG.md)
  * [Python SDK Preview 3 變更記錄](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/formrecognizer/azure-ai-formrecognizer/CHANGELOG.md)
  * [JAVA SDK Preview 3 變更記錄](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/formrecognizer/azure-ai-formrecognizer/CHANGELOG.md)
  * [JavaScript SDK Preview 3 變更記錄](https://github.com/Azure/azure-sdk-for-js/blob/%40azure/ai-form-recognizer_1.0.0-preview.3/sdk/formrecognizer/ai-form-recognizer/CHANGELOG.md)

## <a name="april-2020"></a>2020 年 4 月

### <a name="new-features"></a>新功能
* **SDK 支援表單辨識器 API V2.0 公開預覽** -本月我們已擴充我們的服務支援，以包含適用于表單辨識器 v2.0 的預覽 SDK (預覽版) 版本。 使用下列連結來開始使用您選擇的語言： 
   * [.NET SDK](/dotnet/api/overview/azure/ai.formrecognizer-readme?view=azure-dotnet)
   * [Java SDK](/java/api/overview/azure/ai-formrecognizer-readme?view=azure-java-preview)
   * [Python SDK](/python/api/overview/azure/ai-formrecognizer-readme?view=azure-python-preview)
   * [JavaScript SDK](/javascript/api/overview/azure/ai-form-recognizer-readme?view=azure-node-preview)

  新的 SDK 支援表單辨識器2.0 版 REST API 的所有功能。 例如，您可以使用或不使用標籤來定型模型，並從表單中將文字、索引鍵值組和資料表解壓縮、使用預先建立的回條服務將收據中的資料解壓縮，並使用您檔中的版面佈建服務來將文字和資料表解壓縮。 您可以透過 [Sdk 意見反應表單](https://aka.ms/FR_SDK_v1_feedback)，在 sdk 上分享您的意見反應。
 
* **複製自訂模型** 您現在可以使用新的 [複製自訂模型] 功能，在區域和訂用帳戶之間複製模型。 在叫用複製自訂模型 API 之前，您必須先針對目標資源端點呼叫複製授權作業，以取得要複製到目標資源的授權。
   * [產生複製授權](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/CopyCustomFormModelAuthorization) REST API
   * [複製自訂模型](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/CopyCustomFormModel) REST API 

### <a name="security-improvements"></a>安全性改善

* Customer-Managed 金鑰現在可供 FormRecognizer。 如需詳細資訊，請參閱 [表單辨識器的待用資料加密](./form-recognizer-encryption-of-data-at-rest.md)。
* 使用受控識別搭配 Azure Active Directory 存取 Azure 資源。 如需詳細資訊，請參閱 [授權存取受控](../authentication.md#authorize-access-to-managed-identities)識別。

## <a name="march-2020"></a>2020 年 3 月 

### <a name="new-features"></a>新功能

* **標記的數值型別** 您現在可以使用表單辨識器範例標籤工具來指定您要加上標籤的數值型別。 目前支援下列值類型和變化：
  * `string`
    * 預設值、`no-whitespaces`、`alphanumeric`
  * `number`
    * 預設值、`currency`
  * `date` 
    * 預設值、`dmy`、`mdy`、`ymd`
  * `time`
  * `integer`

  請參閱 [範例標籤工具](./quickstarts/label-tool.md#specify-tag-value-types) 指南，以瞭解如何使用這項功能。


* **資料表視覺效果** 範例標籤工具現在會顯示檔中已辨識的資料表。 這項功能可讓您在標記和分析之前，先從檔中查看已辨識和解壓縮的資料表。 您可以使用 [圖層] 選項來開啟/關閉這項功能。

  下圖是如何辨識和解壓縮資料表的範例：

  > [!div class="mx-imgBorder"]
  > ![使用範例標籤工具的資料表視覺效果](./media/whats-new/formre-table-viz.png)

    已解壓縮的資料表可在的 JSON 輸出中使用 `"pageResults"` 。

  > [!IMPORTANT]
  > 不支援標記資料表。 如果資料表無法辨識並自動 extrated，您只能將它們標記為索引鍵/值組。 將資料表標記為索引鍵/值組時，將每個資料格標示為唯一值。

### <a name="extraction-enhancements"></a>解壓縮增強功能

此版本包含解壓縮增強功能和精確度的改進，尤其是在同一行文字中標記和解壓縮多個索引鍵/值組的功能。 
 
### <a name="sample-labeling-tool-is-now-open-source"></a>範例標籤工具現在是開放原始碼

表單辨識器範例標籤工具現在以開放原始碼專案的形式提供。 您可以將它整合到您的解決方案中，並進行客戶特定的變更以符合您的需求。

如需表單辨識器範例標籤工具的詳細資訊，請參閱 [GitHub](https://github.com/microsoft/OCR-Form-Tools/blob/master/README.md)上提供的檔。

### <a name="tls-12-enforcement"></a>TLS 1.2 強制執行

現在對於此服務的所有 HTTP 要求都會強制執行 TLS 1.2。 如需詳細資訊，請參閱 [Azure 認知服務安全性](../cognitive-services-security.md)。

## <a name="january-2020"></a>2020 年 1 月

此版本引進表單辨識器 2.0 (preview) 。 在下列各節中，您將找到有關新功能、增強功能和變更的詳細資訊。 

### <a name="new-features"></a>新功能

* **自訂模型**
  * **使用標籤進行定型** 您現在可以使用手動加上標籤的資料來定型自訂模型。 這個方法會產生效能較佳的模型，而且可以產生可搭配複雜表單或表單使用的模型，其中包含沒有索引鍵的值。
  * **非同步 API** 您可以使用非同步 API 呼叫來訓練和分析大型資料集和檔案。
  * **TIFF 檔案支援** 您現在可以使用 TIFF 檔來定型和解壓縮資料。
  * **提取精確度改進**

* **預建的收據模型**
  * **秘訣數量** 您現在可以將秘訣數量和其他手寫值解壓縮。
  * **明細專案解壓縮** 您可以從收據中取出行專案值。
  * **信賴價值** 您可以針對每個已解壓縮的值來查看模型的信賴度。
  * **提取精確度改進**

* **版面配置解壓縮** 您現在可以使用版面配置 API，從表單中解壓縮文字資料和資料表資料。

### <a name="custom-model-api-changes"></a>自訂模型 API 變更

定型和使用自訂模型的所有 Api 都已重新命名，而某些同步方法現在是非同步。 以下是重大變更：

* 定型模型的程式現在是非同步。 您可以透過 **/Custom/models** API 呼叫來起始訓練。 此呼叫會傳回作業識別碼，您可以將其傳遞至 **自訂/模型/{modelID}** 來傳回定型結果。
* **/Custom/models/{modelID}/analyze** API 呼叫現在會起始索引鍵/值解壓縮。 此呼叫會傳回作業識別碼，您可以將其傳遞至 **自訂/模型/{modelID}/analyzeResults/{resultid 而言}** 來傳回解壓縮結果。
* 定型作業的作業識別碼現在可在 HTTP 回應的 **Location** 標頭中找到，而不是在 **操作位置** 標頭中。

### <a name="receipt-api-changes"></a>接收 API 變更

讀取銷售收據的 Api 已重新命名。

* **/Prebuilt/receipt/analyze** API 呼叫現在會起始接收資料解壓縮。 此呼叫會傳回作業識別碼，您可以將其傳遞至 **/prebuilt/receipt/analyzeResults/{resultID}** 以傳回解壓縮結果。

### <a name="output-format-changes"></a>輸出格式變更

所有 API 呼叫的 JSON 回應都有新的格式。 已新增、移除或重新命名某些索引鍵和值。 如需目前 JSON 格式的範例，請參閱快速入門。

## <a name="next-steps"></a>後續步驟

完成[用戶端程式庫快速入門](quickstarts/client-library.md)，開始以您選擇的語言搭配表單辨識器，來撰寫表單處理應用程式。

## <a name="see-also"></a>請參閱

* [什麼是表單辨識器？](./overview.md)
