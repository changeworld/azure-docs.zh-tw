---
title: 什麼是表單辨識器？
titleSuffix: Azure Cognitive Services
description: Azure 表單辨識器服務可讓您從表單文件識別並擷取索引鍵/值組和資料表資料，以及從銷售收據和名片擷取主要資訊。
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: overview
ms.date: 11/23/2020
ms.author: pafarley
ms.custom: cog-serv-seo-aug-2020
keywords: 自動化資料處理, 文件處理, 自動化資料輸入, 表單處理
ms.openlocfilehash: e1e5a4abf8eab96af62b160e28f98d95cf527eaf
ms.sourcegitcommit: 8dd8d2caeb38236f79fe5bfc6909cb1a8b609f4a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/08/2021
ms.locfileid: "98044759"
---
# <a name="what-is-form-recognizer"></a>什麼是表單辨識器？

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

Azure 表單辨識器是一種認知服務，可讓您使用機器學習技術來建置自動化的資料處理軟體。 從您的文件中識別並擷取文字、索引鍵/值組、選取標記、資料表和結構&mdash;此服務會輸出結構化資料，資料中包含原始檔案、週框方塊、信賴度等項目中的關聯性。 您可以快速取得針對特定內容量身打造的精確結果，而不需要大量手動操作或廣泛的資料科學專業知識。 使用表單辨識器將應用程式中的資料輸入自動化，並豐富您的文件搜尋功能。

表單辨識器包含自訂的文件處理模型；針對發票、收據和名片所預建的模型；以及版面配置模型。 您可以使用 REST API 或用戶端程式庫 SDK 來呼叫表單辨識器模型，以降低複雜度並將其整合至工作流程或應用程式。

表單辨識器由下列服務組成：
* **[版面配置 API](#layout-api)** - 從文件中擷取文字、選取標記和資料表結構，以及其週框方塊座標。
* **[自訂模型](#custom-models)** - 從表單中擷取文字、索引鍵/值組、選取標記和資料表資料。 這些模型會使用您自己的資料進行定型，因此會針對您的表單量身打造。
* **[預建模型](#prebuilt-models)** - 使用預建模型從唯一的表單類型擷取資料。 目前可用的預建模型如下
    * [發票](./concept-invoices.md)
    * [銷售收據](./concept-receipts.md)
    * [名片](./concept-business-cards.md)


## <a name="try-it-out"></a>試做

若要試用表單辨識器服務，請移至線上範例 UI 工具：


# <a name="v20"></a>[v2.0](#tab/v2-0)
> [!div class="nextstepaction"]
> [試用表單辨識器](https://fott.azurewebsites.net/)

# <a name="v21-preview"></a>[v2.1 預覽](#tab/v2-1)
> [!div class="nextstepaction"]
> [試用表單辨識器](https://fott-preview.azurewebsites.net/)

---

您需要有 Azure 訂用帳戶 ([免費建立一個](https://azure.microsoft.com/free/cognitive-services)) 和[表單辨識器資源](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer)端點和金鑰，才能試用表單辨識器服務。

## <a name="layout-api"></a>版面配置 API

表單辨識器可以使用高畫質的光學字元辨識 (OCR) 和增強的深度學習模型，從文件中擷取文字、選取標記和資料表結構 (與文字相關聯的資料列和資料行編號)。 如需詳細資訊，請參閱[版面配置](./concept-layout.md)概念指南。

:::image type="content" source="./media/tables-example.jpg" alt-text="資料表範例" lightbox="./media/tables-example.jpg":::

## <a name="custom-models"></a>自訂模型

表單辨識器自訂模型可根據您自己的資料來定型，而您只需要五個輸入表單範例即可開始。 已定型的文件處理模型可以輸出在原始表單文件中包含關聯性的結構化資料。 定型模型後，您就可以測試及重新定型模型，並於最終根據您的需求用此模型來從更多的表單可靠地擷取資料。

當您將自訂模型定型時，可以選擇下列選項：使用標記資料和不使用標記資料進行定型。

### <a name="train-without-labels"></a>不使用標籤進行定型

根據預設，表單辨識器會使用不受監督的學習來了解表單中欄位與項目之間的版面配置和關聯性。 當您提交輸入表單時，演算法會依類型將表單群集、探索目前有哪些索引鍵和資料表，並將值與索引鍵以及將項目與資料表相關聯。 這不需要手動資料標記或大量編碼和維護，因此建議您先嘗試此方法。

如需如何收集定型文件的秘訣，請參閱[建立定型資料集](./build-training-data-set.md)。

### <a name="train-with-labels"></a>使用標籤進行定型

當您使用標記資料進行定型時，此模型會使用您提供的標記表單，進行受監督式學習來擷取感興趣的值。 這會造就表現更佳的模型，並可產生可搭配複雜表單或含有值 (但不含索引鍵) 的表單使用的模型。

表單辨識器會使用[版面配置 API](#layout-api) 來學習所列印和手寫文字元素的預期大小和位置。 然後，其會使用使用者指定的標籤來學習文件中的索引鍵/值關聯。 建議您使用相同類型 (相同結構) 的五個手動標記表單，以便在定型新模型時開始使用，並視需要新增更多標記資料，以改善模型精確度。

[開始使用標籤進行定型](./quickstarts/label-tool.md)


> [!VIDEO https://channel9.msdn.com/Shows/Docs-Azure/Azure-Form-Recognizer/player]


## <a name="prebuilt-models"></a>預先建置的模型

表單辨識器也包含預建模型，用於唯一表單類型的自動化資料處理。

### <a name="prebuilt-invoice-model"></a>預建發票模型
預建發票模型會從各種格式的發票中擷取資料，並傳回結構化資料。 此模型會擷取重要資訊，例如發票識別碼、客戶詳細資料、廠商詳細資料、送貨地址、帳單地址、總金額、稅金、小計等等。 此外，預建發票模型也已定型，可辨識並傳回發票上的所有文字和資料表。 如需詳細資訊，請參閱[發票](./concept-invoices.md)概念指南。

:::image type="content" source="./media/overview-invoices.jpg" alt-text="發票範例" lightbox="./media/overview-invoices.jpg":::

### <a name="prebuilt-receipt-model"></a>預建的收據模型

預建的收據模型可用來讀取來自澳大利亞、加拿大、英國、印度和美國的英文銷售收據&mdash;這是餐廳、加油站、零售商店等商家所使用的類型。 此模型會擷取索引鍵資訊，例如交易的時間和日期、商家資訊、稅金、明細項目和總計等等。 此外，預先建置的收據模型已定型為用來辨識及傳回收據上所有文字。 如需詳細資訊，請參閱[收據](./concept-receipts.md)概念指南。

:::image type="content" source="./media/overview-receipt.jpg" alt-text="範例收據" lightbox="./media/overview-receipt.jpg":::

### <a name="prebuilt-business-cards-model"></a>預建的名片模型

名片模型可讓您從英文的名片中，擷取個人姓名、職稱、地址、電子郵件、公司和電話號碼等資訊。 如需詳細資訊，請參閱[名片](./concept-business-cards.md)概念指南。

:::image type="content" source="./media/overview-business-card.jpg" alt-text="範例名片" lightbox="./media/overview-business-card.jpg":::


## <a name="get-started"></a>開始使用

使用[範例表單辨識器工具](https://fott.azurewebsites.net/)或遵循快速入門，以開始從表單中擷取資料。 當您學習技術時，我們建議您使用免費的服務。 請記住，免費的頁數限制為每個月 500 頁。

* [用戶端程式庫/REST API 快速入門](./quickstarts/client-library.md) (所有語言，多個案例)
* Web UI 快速入門
  * [使用標籤進行定型 - 範例標籤工具](quickstarts/label-tool.md)
* REST 範例 (GitHub)
 * 從文件中擷取文字、選取標記和資料表結構
    * [擷取版面配置資料 - Python](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/FormRecognizer/rest/python-layout.md)
  * 定型自訂模型和擷取表單資料
    * [不使用標籤進行定型 - Python](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/FormRecognizer/rest/python-train-extract.md)
    * [使用標籤進行定型 - Python](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/FormRecognizer/rest/python-labeled-data.md)
  * 從發票中擷取資料
    * [擷取發票資料 - Python](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/FormRecognizer/rest/python-invoices.md)
  * 從銷售收據中擷取資料
    * [擷取收據資料 - Python](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/FormRecognizer/rest/python-receipts.md)
  * 從名片擷取資料
    * [擷取名片資料 - Python](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/FormRecognizer/rest/python-business-cards.md)

### <a name="review-the-rest-apis"></a>檢閱 REST API

您將使用下列 API 來定型模型及擷取表單中的結構化資料。

|名稱 |描述 |
|---|---|
| **分析版面配置** | 分析以資料流形式傳入的文件，以從文件中擷取文字、選取標記、資料表和結構 |
| **定型自訂模型**| 使用相同類型的 5 個表單來定型新模型，以分析您的表單。 將 _useLabelFile_ 參數設定為 `true`，以手動標記的資料進行定型。 |
| **分析表單** |分析以資料流形式傳入的表單，以使用自訂模型從表單擷取文字、索引鍵/值組和資料表。  |
| **分析發票** | 分析發票以擷取重要資訊、資料表和其他發票文字。|
| **分析收據** | 分析收據文件，以擷取重要資訊和其他收據文字。|
| **分析名片** | 分析名片以擷取重要資訊和文字。|


# <a name="v20"></a>[v2.0](#tab/v2-0)
若要了解更多，請瀏覽 [REST API 參考文件](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeWithCustomForm)。 如果您熟悉舊版的 API，請參閱[新功能](./whats-new.md)一文，以了解最近的變更。

# <a name="v21"></a>[v2.1](#tab/v2-1)
若要了解更多，請瀏覽 [REST API 參考文件](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/AnalyzeWithCustomForm)。 如果您熟悉舊版的 API，請參閱[新功能](./whats-new.md)一文，以了解最近的變更。

---

## <a name="input-requirements"></a>輸入需求

[!INCLUDE [input requirements](./includes/input-requirements.md)]

## <a name="deploy-on-premises-using-docker-containers"></a>使用 Docker 容器在內部部署環境進行部署

[使用表單辨識器容器 (預覽)](form-recognizer-container-howto.md) 在內部部署環境部署 API 功能。 此 Docker 容器可讓服務更加契合您的資料，以實現合規性、安全性或其他操作性原因。 

## <a name="service-availability-and-redundancy"></a>服務可用性與備援能力

### <a name="is-form-recognizer-service-zone-resilient"></a>表單辨識器服務是否具有區域復原性？

是。 依預設，表單辨識器服務具有區域復原性。

### <a name="how-do-i-configure-the-form-recognizer-service-to-be-zone-resilient"></a>如何將表單辨識器服務設定為具有區域復原能力？

不需要客戶設定即可啟用區域復原。 依預設可以使用表單辨識器資源的區域復原功能，並由服務本身進行管理。


## <a name="data-privacy-and-security"></a>資料隱私權和安全性

和所有認知服務一樣，使用表單辨識器服務的開發人員應該要了解 Microsoft 對於客戶資料的政策。 請參閱 Microsoft 信任中心上的[認知服務頁面](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices)，以進行深入了解。

## <a name="next-steps"></a>後續步驟

完成[快速入門](quickstarts/client-library.md)，開始以您選擇的語言搭配表單辨識器，來撰寫表單處理應用程式。
