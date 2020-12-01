---
title: 發票-表單辨識器
titleSuffix: Azure Cognitive Services
description: 瞭解使用表單辨識器 API 的發票分析相關概念-使用方式與限制。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 11/18/2020
ms.author: pafarley
ms.openlocfilehash: 5c8af8ddb7a0870de37b73cbe09965ee63c88ba1
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/30/2020
ms.locfileid: "96353743"
---
# <a name="form-recognizer-prebuilt-invoice-model"></a>表單辨識器預建發票模型

Azure 表單辨識器可以使用其預先建立的發票模型，分析和解壓縮銷售發票的資訊。 發票 API 可讓客戶以各種不同的格式取得發票，並傳回結構化資料，以自動化發票處理。 它結合了強大的 [光學字元辨識 (OCR) ](../computer-vision/concept-recognizing-text.md) 功能，以及瞭解深度學習模型，以英文的發票來解壓縮重要資訊。 它會將文字、資料表和資訊（例如客戶、供應商、發票識別碼、發票到期日、總計、發票金額到期、稅額、出貨、帳單等等）解壓縮。 預先建立的發票 API 可在表單辨識器2.1 版 preview 中公開使用。

## <a name="what-does-the-invoice-service-do"></a>發票服務的用途為何？

發票 API 會從發票中解壓縮索引鍵欄位，並以組織的結構化 JSON 回應傳回這些欄位。 發票可來自各種不同的格式和品質，包括電話捕獲的影像、掃描的檔和數位 Pdf。 發票 API 將會從這些發票中解壓縮出結構化的輸出。 

![Contoso 發票範例](./media/invoice-example.jpg)

## <a name="try-it-out"></a>試做

若要試用表單辨識器發票服務，請移至線上範例 UI 工具：

> [!div class="nextstepaction"]
> [試用預建模型](https://fott-preview.azurewebsites.net/)

您將需要 Azure 訂用帳戶 ([建立免費](https://azure.microsoft.com/free/cognitive-services)) 的 [表單，以及 Recognzier 資源](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer) 端點和金鑰的表單，以試用表單辨識器發票服務。 

![已分析發票範例](./media/analyze-invoice.png)


### <a name="input-requirements"></a>輸入需求 

[!INCLUDE [input reqs](./includes/input-requirements-receipts.md)]

## <a name="the-analyze-invoice-operation"></a>分析發票作業

「 [分析發票](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/AnalyzeInvoiceAsync) 」作業會以發票的影像或 PDF 作為輸入，並將感興趣的值解壓縮。 呼叫會傳回稱為的回應標頭欄位 `Operation-Location` 。 `Operation-Location`值是包含要在下一個步驟中使用之結果識別碼的 URL。

|回應標頭| 結果 URL |
|:-----|:----|
|Operation-Location | `https://cognitiveservice/formrecognizer/v2.1-preview.2/prebuilt/invoice/analyzeResults/49a36324-fc4b-4387-aa06-090cfbf0064f` |

## <a name="the-get-analyze-invoice-result-operation"></a>取得分析發票結果作業

第二個步驟是呼叫「 [取得分析發票結果](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/GetAnalyzeInvoiceResult) 」作業。 這項作業會以「分析發票」作業所建立的結果識別碼作為輸入。 它會傳回 JSON 回應，其中包含具有下列可能值的 **狀態** 欄位。 您會反復呼叫此操作，直到它傳回 **成功** 的值為止。 使用3到5秒的間隔，以避免每秒超過要求 (RPS) 速率。

|欄位| 類型 | 可能值 |
|:-----|:----:|:----|
|status | 字串 | notStarted：分析作業尚未啟動。<br /><br />正在執行：分析作業正在進行中。<br /><br />失敗：分析作業已失敗。<br /><br />成功：分析作業已成功。|

當 [ **狀態** ] 欄位的值為 [ **成功** ] 時，JSON 回應會包含發票理解結果、已解壓縮的資料表，以及選擇性的文字辨識結果（如有要求）。 發票理解結果會組織為命名域值的字典，其中每個值都包含已解壓縮的文字、正規化值、周框方塊、信賴度和對應的單字元素。 文字辨識結果會組織成行和單字的階層，其中包含文字、周框方塊和信賴資訊。

### <a name="sample-json-output"></a>範例 JSON 輸出

「取得分析發票結果」作業的回應將會是已解壓縮所有資訊之發票的結構化標記法。 請參閱這裡以取得 [範例發票](./media/sample-invoice.jpg) 檔案和其結構化輸出 [範例發票輸出](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/tree/master/curl/form-recognizer/sample-invoice-output.json)。

JSON 輸出有3個部分： 
* `"readResults"` 節點包含所有已辨識的文字和選取專案標記。 文字會依頁面彙整，然後依文字行，再依個別字組彙整。 
* `"pageResults"` 節點包含使用其周框方塊、信賴度和 "readResults" 中的行和單字的參考所解壓縮的資料表和資料格。
* `"documentResults"` 節點包含模型所發現的特定發票值。 您可以在這裡找到發票中的所有欄位，例如發票識別碼、貨物出貨、帳單給客戶、總計和其他許多欄位。

## <a name="example-output"></a>範例輸出

發票服務將會將文字、資料表和26張發票欄位解壓縮。 以下是從 JSON 輸出回應中的發票解壓縮的欄位 (下列輸出使用此 [範例發票](./media/sample-invoice.jpg))   

|名稱| 類型 | 說明 | 文字 | 值 (標準化輸出)  |
|:-----|:----|:----|:----| :----|
| CustomerName | 字串 | 客戶的發票 | Microsoft Corp |  |
| CustomerId | 字串 | 客戶的參考識別碼 | CID-12345 |  |
| PurchaseOrder | 字串 | 採購單參考編號 | PO-3333 | |  |
| InvoiceId | 字串 | 此特定發票的識別碼 (通常是「發票號碼」 )  | 庫存-100 | |  |
| InvoiceDate | date | 發票的發行日期 | 11/15/2019 | 
| DueDate | date | 此發票的付款日期為逾期 | 12/15/2019 | 2019-12-15 | 2019-11-15 |
| VendorName | 字串 | 已建立此發票的廠商 | CONTOSO 公司。 | |
| VendorAddress | 字串 | 廠商的郵寄地址 | 123 456th 聖紐約，紐約州10001 | |
| VendorAddressRecipient | 字串 | 與 VendorAddress 相關聯的名稱 | Contoso 總部 | |
| CustomerAddress | 字串 | 客戶的郵寄地址 | 123 Other 聖、Redmond WA、98052 | |
| CustomerAddressRecipient | 字串 | 與 CustomerAddress 相關聯的名稱 | Microsoft Corp | |
| BillingAddress | 字串 | 客戶的明確帳單位址 | 123 Bill St、Redmond WA、98052 | |
| BillingAddressRecipient | 字串 | 與 BillingAddress 相關聯的名稱 | Microsoft 服務 | |
| ShippingAddress | 字串 | 客戶的明確交貨位址 | 123出貨 St、Redmond WA、98052 | |
| ShippingAddressRecipient | 字串 | 與 ShippingAddress 相關聯的名稱 | Microsoft 傳遞 | |
| SubTotal | 數目 | 此發票上識別的小計欄位 | $100.00 | 100 | 
| TotalTax | 數目 | 此發票上識別的稅額欄位總計 | $10.00 | 10 |
| InvoiceTotal | 數目 | 與此發票相關聯的新費用總計 | $110.00 | 110 |
| AmountDue |  數目 | 由廠商所應付的總金額 | $610.00 | 610 |
| Serviceaddress.uri | 字串 | 客戶的明確服務位址或屬性位址 | 123服務 St、Redmond WA、98052 | |
| ServiceAddressRecipient | 字串 | 與 Serviceaddress.uri 相關聯的名稱 | Microsoft 服務 | |
| RemittanceAddress | 字串 | 客戶的明確匯款或付款位址 | 123匯款聖紐約、紐約州、10001 |  |
| RemittanceAddressRecipient | 字串 | 與 RemittanceAddress 相關聯的名稱 | Contoso 帳單 |  |
| ServiceStartDate | date | 服務期間的第一個日期 (例如，公用程式帳單服務期間)  | 2019/10/14 | 2019-10-14 |
| ServiceEndDate | date | 服務期間的結束日期 (例如，公用程式帳單服務期間)  | 11/14/2019 | 2019-11-14 |
| PreviousUnpaidBalance | 數目 | 明確的先前未付款餘額 | $500.00 | 500 |


## <a name="next-steps"></a>後續步驟

- 在 [表單辨識器範例 UI](https://fott-preview.azurewebsites.net/)中，試用您自己的發票和範例。
- 完成 [表單辨識器用戶端程式庫快速入門](quickstarts/client-library.md) ，以使用您所選語言的表單辨識器來開始撰寫發票處理應用程式。
- 或者，遵循「 [解壓縮發票資料](./quickstarts/python-invoices.md) 」快速入門，使用 Python 和 REST API 來執行發票資料解壓縮。
## <a name="see-also"></a>請參閱

* [什麼是表單辨識器？](./overview.md)
* [REST API 參考檔](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/AnalyzeInvoiceAsync)