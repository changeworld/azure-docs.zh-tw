---
title: 支出報告 - Microsoft 商業市集
description: 支出報告會顯示您透過供應項目所獲得的款項詳細資料，包括付款金額以及付款的時間。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: mingshen-ms
ms.author: mingshen
ms.date: 09/09/2020
ms.openlocfilehash: 62de9380578ca5a97f7b72f9939d5c2dd7392c0d
ms.sourcegitcommit: 3c66bfd9c36cd204c299ed43b67de0ec08a7b968
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/10/2020
ms.locfileid: "90004216"
---
# <a name="payout-summaries"></a>支出摘要

支出摘要會顯示您透過 Microsoft 獲得的款項詳細資料。 此外，其還會告知何時會收到付款，以及將收到多少款項。

如果您在 Azure Marketplace 中銷售產品，則將會在支出摘要中看到成功的支出資訊。 如需有關 Azure Marketplace 付款的詳細資訊，請參閱「 [在商業市場中取得款項](./get-paid.md) 」和「 [Microsoft 發行者合約](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RE4xqkx)」。

> [!NOTE]
> 若要符合支付資格，收益必須達到美金 $50 元的付款門檻。 如需詳細資訊，請參閱 [Microsoft 發行者合約](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RE4xqkx)。

> [!NOTE]
> 若正在尋找與支出相關的支援，包括設定支出帳戶、遺漏支出、暫停支出等，請在[這裡](https://developer.microsoft.com/windows/support)連絡支援人員。

## <a name="access-the-payout-summary-pages"></a>存取支出摘要頁面

開啟其中一個支出摘要頁面：

1. 選取位於右上角的支出圖示。
2. 選取 **交易記錄**、 **付款**或 **匯出資料**。

## <a name="payments-page"></a>[付款] 頁面

此頁面上總計代表參與的所有計劃。 您可透過參與者識別碼、計劃、付款識別碼和收益類型進行篩選。 金額的單位是美金。 付款值也會以付款貨幣顯示。

| 區域  | 描述 |
| --- | --- |
| 今年總付款   | 今年您所有計劃合併之後的付款總金額，單位為美金       |
| 下一個預估付款 | 即將支付給下一個單一付款 (即使還有其他付款也一樣)，單位為美金 |
| 上一次付款           | 您最近一次付款的金額、計劃名稱和計劃，單位為美金           |
| 付款 (依來源)     | 以過去 12 個月內計劃所代表的付款金額，單位為美金           |
| 付款               | 選取 [已支付] 或 [暫止]，然後依需要進行排序。 如需特定付款的其他詳細資料，請選取 [檢視]。 若要下載付款匯款單的複本，請選取 [下載]。 因為交易記錄資料最多可能會需要 24 小時才會出現，所以可能不會立即看到相關聯的收益。 |
|||

若要匯出此頁面上的任何資料，請選取 [匯出]，然後遵循 [匯出資料] 頁面上的指示。

## <a name="transaction-history-page"></a>[交易記錄] 頁面

此頁面會顯示所有的個人收益，包括日期、類型和每個項目的收益。 您可選取要檢視的時間間隔，也可以透過註冊識別碼、計劃、付款識別碼、收益類型、控制桿和狀態等進行篩選。 其中提供目前會計年度 (7 月 1 日到 6 月 30 日) 及前兩個會計年度的資料。

若要查看收益的詳細資料，請選取位於頁面右側的向下箭號。 這將會顯示控制桿、收入金額和產品。 若其中任何一項資料無法使用，但您仍需要進行存取，請連絡[支援人員](https://developer.microsoft.com/windows/support)。 若收益是調整的結果而非交易結果，則不會顯示產品欄位。

若要匯出此頁面上的任何交易資料，請選取 [匯出] 並遵循 [匯出資料] 頁面上的指示。 從 [交易記錄] 頁面匯出的檔案會顯示交易貨幣中資料、兩種交易貨幣和美金收益，以及付款貨幣的付款值。

## <a name="payment-status"></a>付款狀態

| 收益狀態 | 原因 | 需要合作夥伴動作嗎？ |
| --- | --- | --- |
| 尚未處理              | 收益符合付款資格。 其會在獎勵計劃的計劃指南中所定義的冷卻期間內處於此狀態。 | 否                                                         |
| 即將付款                 | 所產生的付款訂單正在等待內部檢閱，之後才能處理付款                                                               | 否                                                         |
| 稅務發票正在暫止      | 稅務發票不完整或無效                                                                                                  | 您需要更新稅務發票才能接收付款 |
| 檢閱期間遭到拒絕   | 付款在檢閱期間遭到拒絕                                                                                                     | 請連絡 [Microsoft 支援服務](https://developer.microsoft.com/windows/support)以取得詳細資料                      |
| 失敗                   | 付款因 Microsoft 系統錯誤而失敗                                                                                         | 請連絡 [Microsoft 支援服務](https://developer.microsoft.com/windows/support)以取得詳細資料                      |
| 進行中              | 正在進行付款                                                                                                                 | 否                                                         |
| 付款不正確        | 正在取回付款                                                                                                       | 否                                                         |
| 已傳送                     | 付款已傳送至銀行                                                                                                     | 否                                                         |
| 正在重新處理             | 付款發生 Microsoft 系統錯誤，目前正在重新整理                                                                  | 否                                                         |
| Reversed                 | 付款已由銀行反轉，將會在下一個付款週期內再次傳送                                                     | 否                                                         |
| 稅務發票遭拒     | 稅務發票在檢閱期間遭到拒絕。 所有暫止的付款都將會暫停，直到稅務發票檢閱完成。                 | 請連絡 [Microsoft 支援服務](https://developer.microsoft.com/windows/support)以取得詳細資料                      |
| 正在檢閱稅務發票 | 正在檢閱稅務發票。 付款將會在稅務發票獲得核准後發出。                                   | 否                                                         |
| 已拒絕                 | 付款遭到銀行拒絕                                                                                                      | 請連絡銀行以了解詳細資料。                             |
|||

## <a name="export-data-page"></a>[匯出資料] 頁面

請遵循這些指示來匯出資料。

[匯出資料] 頁面不會自行重新整理。 您可能需要手動重新整理頁面，才能看到最新的資料。

篩選條件可能會導致**無可用資料**錯誤。 這表示您可能將預設時間間隔維持在選取三個月的狀態，然後選取了位於該期間之外收益的付款識別碼。 請展開時間間隔，然後再試一次。

## <a name="payments"></a>付款

![匯出付款](./media/pc-export-payments.png)

此選項可為指定計劃、關聯稅務和彙總金額提供您在銀行內所收到的付款下載。 這份報告可用於許多合作夥伴中心計劃，因此有些資料行可能不適用於報告。 這些資料行標記如下。

| 資料行名稱 | 描述 |
| --- | --- |
| participantID            | 根據該計劃獲取收益的合作夥伴主要身分識別                                                                             |
| participantIDType        | 通常是獎勵計劃的計劃識別碼和 Microsoft Store 計劃的賣方識別碼                                                                |
| participantName          | 收益合作夥伴的名稱                                                                                                               |
| programName              | 獎勵/Microsoft Store 計劃名稱                                                                                                              |
| 收益                   | 該計劃/participantID 的付款貨幣收益金額                                                                       |
| earnedUSD                | 方案/participantID 的收益金額，單位為美金                                                                                      |
| withheldTax              | 方案/participantID 的付款貨幣收益金額中扣除的稅務金額                                                               |
| salesTax                 | 方案/participantID 付款貨幣中營業稅的總金額 (僅適用於獎勵計劃)                   |
| serviceFeeTax            | 方案/participantID 付款貨幣中的 serviceFeeTax 總金額 (僅適用於 Microsoft Store 計劃和 Azure Marketplace) |
| totalPayment             | 方案/participantID 以當地貨幣計算的總付款，排除扣除的稅額並包含營業稅 (若適用的話)   |
| currencyCode             | 付款貨幣代碼                                                                                                                      |
| paymentMethod            | 用來向合作夥伴進行支付的方法，例如電子銀行轉帳、折讓單                                                     |
| paymentID                | 付款的唯一識別碼。 這個數字通常可在銀行對帳單上看到 (僅適用於 SAP 付款)。              |
| paymentStatus            | 付款狀態                                                                                                                            |
| paymentStatusDescription | 付款狀態的易記描述                                                                                                    |
| paymentDate              | 從 Microsoft 傳送付款時的日期                                                                                                      |
|||

## <a name="transaction-history"></a>交易記錄

![匯出交易記錄](./media/pc-export-transaction.png)

此選項可為您在 [交易記錄] 頁面中所看到的每一個收益明細項目提供下載，包含收益類型、日期、相關聯的交易金額、客戶、產品，以及其他適用計劃的交易詳細資料。

| 資料行名稱 | 描述 | 獎勵/Microsoft Store/Azure Marketplace 的適用性 |
| --- | --- | --- |
| earningId                      | 每一項收益的唯一識別碼                                                                                                       | 全部                                                            |
| participantId                  | 根據該計劃獲取收益的合作夥伴主要身分識別                                                                            | 全部                                                            |
| participantIdType              | 大多數是獎勵計劃的計劃識別碼，以及 Microsoft Store 計劃和 Azure Marketplace 的賣方識別碼                                          | 全部                                                            |
| participantName                | 收益合作夥伴的名稱                                                                                                              | 全部                                                            |
| partnerCountryCode             | 收益合作夥伴的位置/國家/地區                                                                                                  | 全部                                                            |
| programName                    | 獎勵/Microsoft Store 計劃名稱                                                                                                             | 全部                                                            |
| transactionId                  | 交易的唯一識別碼                                                                                                    | 全部                                                            |
| transactionCurrency            | 進行原始客戶交易時所使用的貨幣 (不是合作夥伴位置的貨幣)                                     | 全部                                                            |
| transactionDate                | 交易的日期。 對於將許多交易貢獻給一項收益的計劃相當實用                                           | 全部                                                            |
| transactionExchangeRate        | 用來顯示對應交易美金金額的匯率日期                                                                 | 全部                                                            |
| transactionAmount              | 原始交易貨幣 (據以產生收益) 的交易金額                                              | 全部                                                            |
| transactionAmountUSD           | 以美金為單位的交易金額                                                                                                                | 全部                                                            |
| lever                          | 指出收益的商務規則                                                                                                  | 全部                                                            |
| earningRate                    | 套用到交易金額以產生收益的獎勵率                                                                      | 全部                                                            |
| quantity                       | 根據計劃而有所不同。 指出交易計劃的計費數量                                                            | 全部                                                            |
| quantityType                   | 指出數量類型 (例如：計費數量、MAU)                                                                             | 全部                                                            |
| earningType                    | 指出其是費用、退款、合作，還是銷售等                                                                                          | 全部                                                            |
| earningAmount                  | 以原始交易貨幣為單位的收益金額                                                                                      | 全部                                                            |
| earningAmountUSD               | 以美金為單位的收益金額                                                                                                                    | 全部                                                            |
| earningDate                    | 收益的日期                                                                                                                      | 全部                                                            |
| calculationDate                | 在系統中計算收益的日期                                                                                            | 全部                                                            |
| earningExchangeRate            | 用來顯示對應美金金額的匯率                                                                                  | 全部                                                            |
| exchangeRateDate               | 用來計算 EarningAmount (以美金為單位) 的匯率日期                                                                                   | 全部                                                            |
| paymentAmountWOTax             | 以付款貨幣為單位的收益金額 (不含稅)，僅適用於「已傳送」的付款                                                                 | 全部                                                            |
| paymentCurrency                | 由合作夥伴在付款設定檔中選擇的付款貨幣。 只會針對已傳送的付款顯示                                                   | 全部                                                            |
| paymentExchangeRate            | 使用 ExchangeRateDate 以付款貨幣計算 paymentAmountWOTax 的匯率                                            | 全部                                                            |
| claimId                        | 宣告的唯一識別碼                                                                                                              | 獎勵 - 僅限部分計劃                                |
| planId                         | 計劃的唯一識別碼                                                                                                               | 獎勵 - 僅限部分計劃                                |
| paymentId                      | 付款的唯一識別碼。 這個數字通常會顯示在銀行對帳單中                                                 | 僅限 SAP 付款                                              |
| paymentStatus                  | 付款狀態                                                                                                                           | 全部                                                            |
| paymentStatusDescription       | 付款狀態的易記描述                                                                                                   | 全部                                                            |
| customerId                     | 一律為空白                                                                                                                     | 僅限獎勵計劃 (例外：OEM) 及 Azure Marketplace |
| customerName                   | 一律為空白                                                                                                                     | 僅限獎勵計劃 (例外：OEM) 及 Azure Marketplace |
| partNumber                     | 一律為空白                                                                                                                     | 部分獎勵和 Microsoft Store 計劃及 Azure Marketplace        |
| productName                    | 連結至交易的產品名稱                                                                                                       | 全部                                                            |
| productId                      | 唯一產品識別碼                                                                                                                | Microsoft Store 與 Azure Marketplace                                    |
| parentProductId                | 唯一的父產品識別碼。 如果該交易沒有父產品，則父產品識別碼 = 產品識別碼。 | Microsoft Store 與 Azure Marketplace                                    |
| parentProductName              | 父產品的名稱。 如果該交易沒有父產品，則父產品名稱 = 產品名稱。   | Microsoft Store 與 Azure Marketplace                                    |
| productType                    | 產品的類型 (例如應用程式、附加元件或遊戲)                                                                                        | Microsoft Store 與 Azure Marketplace                                    |
| invoiceNumber                  | 發票編號 (僅適用於 EA)                                                                                                  | 獎勵及 Azure Marketplace- 僅限部分計劃           |
| subscriptionId                 | 與客戶建立關聯的訂閱識別碼                                                                                         | 獎勵 - 僅限部分計劃                                 |
| subscriptionStartDate          | 訂閱開始日期                                                                                                                  | 獎勵 - 僅限部分計劃                                 |
| subscriptionEndDate            | 訂閱結束日期                                                                                                                    | 獎勵 - 僅限部分計劃                                 |
| resellerId                     | 轉銷商識別碼                                                                                                                      | 獎勵 - 僅限部分計劃                                 |
| resellerName                   | 轉銷商名稱                                                                                                                            | 獎勵 - 僅限部分計劃                                 |
| distributorId                  | 散發者識別碼                                                                                                                   | 獎勵 - 僅限部分計劃                                 |
| distributorName                | 散發者名稱                                                                                                                         | 獎勵 - 僅限部分計劃                                 |
| agreementNumber                | 合約編號                                                                                                                         | 獎勵 - 僅限部分計劃                                 |
| agreementStartDate             | 合約開始日期                                                                                                                     | 獎勵 - 僅限部分計劃                                 |
| agreementEndDate               | 合約結束日期                                                                                                                       | 獎勵 - 僅限部分計劃                                 |
| workload                       | 工作負載                                                                                                                                 | 獎勵 - 僅限部分計劃                                 |
| transactionType                | 交易的類型 (例如購買、退貨、沖銷或退款)                                                               | Microsoft Store 與 Azure Marketplace                                    |
| localProviderSeller            | 記錄的本地提供者/賣方                                                                                                          | 僅限 Microsoft Store                                                     |
| taxRemitted                    | 已繳交稅金的金額 (營業稅、使用稅或加值稅/貨物及服務稅)                                                                                   | Microsoft Store 與 Azure Marketplace                                    |
| taxRemitModel                  | 負責繳交稅金 (營業稅、使用稅或加值稅/貨物及服務稅) 的合作對象                                                                    | 僅限 Microsoft Store                                                     |
| storeFee                       | Microsoft 所保留的金額，作為在 Microsoft Store 中提供應用程式或附加元件的費用                                           | 僅限 Microsoft Store                                                     |
| transactionPaymentMethod       | 用於交易的客戶付款方式 (例如卡片、行動裝置電信業者代扣或 PayPal)                                | Microsoft Store 與 Azure Marketplace                                    |
| tpan                           | 指出協力廠商廣告網路                                                                                                     | Microsoft Store - 僅限廣告                                               |
| customerCountry                | 客戶國家/地區                                                                                                                         | Microsoft Store 與 Azure Marketplace                                    |
| customerCity                   | 客戶縣/市                                                                                                                            | Microsoft Store 與 Azure Marketplace                                    |
| customerState                  | 客戶州/省                                                                                                                           | Microsoft Store 與 Azure Marketplace                                    |
| customerZip                    | 客戶郵遞區號                                                                                                                 | Microsoft Store 與 Azure Marketplace                                    |
| purchaseTypeCode               | 一律為空白                                                                                                                     | 獎勵計劃 - CRI                                        |
| purchaseOrderType              | 一律為空白                                                                                                                     | 獎勵計劃 - CRI                                        |
| purchaseOrderCoverageStartDate | 一律為空白                                                                                                                     | 獎勵計劃 - CRI                                        |
| purchaseOrderCoverageEndDate   | 一律為空白                                                                                                                     | 獎勵計劃 - CRI                                        |
| programOfferingLevel           |                                                                                                                                          | 獎勵計劃 - CRI                                        |
| TenantID                       |                                                                                                                                          | 獎勵計劃                                             |
| externalReferenceId            | 計劃的唯一識別碼                                                                                                        | 直接付款計劃 (獎勵和 Microsoft Store)                      |
| externalReferenceIdLabel       | 唯一識別碼標籤                                                                                                                  | 直接付款計劃 (獎勵和 Microsoft Store)                      |
|||

## <a name="historical-statements"></a>歷史對帳單

![匯出歷史對帳單](./media/pc-export-statements.png)

2019 年 7 月 1 日以前的交易記錄會分開處理。 對帳單會使用下列欄位，而非目前的欄位。

> [!NOTE]
> 舊版交易記錄包含稱為「保留」的資料行，該資料行對應到新式交易記錄中的「收益」資料行，但其差別在於後者會排除所有狀態為「付款已傳送」的收益。

> [!NOTE]
> 3M、6M 或 12M 等篩選不會套用到**歷史對帳單**區段。

| 欄位名稱 | 描述 |
| --- | --- |
| 收益來源          | 根據交易發生位置 (例如 Microsoft Store 或廣告) 決定的收入來源                  |
| 訂單識別碼                | 唯一的訂單識別碼。 此識別碼可供透過購買交易的個別非購買交易 (例如退貨或退款) 來識別購買交易。 這兩者都會具備相同的訂單識別碼。 此外，在針對單一購買使用多種付款方式的分割費用案例中，其可供連結購買交易。 |
| Transaction ID          | 唯一的交易識別碼。                                                                                                                                          |
| 交易日期時間   | 發生交易的日期和時間 (UTC)。                                                                                                                       |
| 父產品識別碼       | 唯一的父產品識別碼。 如果該交易沒有父產品，則父產品識別碼 = 產品識別碼。                                |
| Product ID              | 唯一的產品識別碼。                                                                                                                                              |
| 父產品名稱     | 父產品的名稱。 如果該交易沒有父產品，則父產品名稱 = 產品名稱。                                  |
| 產品名稱            | 產品的名稱                                                                                                                                                    |
| 產品類型            | 產品的類型 (例如應用程式、附加元件或遊戲)                                                                                                                       |
| 數量                | 當收入來源是商務用 Microsoft Store 時，Quantity 代表購買的授權數。 針對其他所有的收入來源，Quantity 一律為 1。 即使單一交易因為使用了兩種不同付款方式而分成兩個明細項目，每個明細項目仍會顯示 Quantity 為 1。 |
| 交易類型        | 交易的類型 (例如購買、退貨、沖銷或退款)                                                                                              |
| 付款方式          | 用於交易的客戶付款方式 (例如卡片、行動裝置電信業者代扣或 PayPal)                                                               |
| 國家/地區        | 發生交易的國家/地區                                                                                                                          |
| 本地提供者/賣方 | 記錄的本地提供者/賣方                                                                                                                                        |
| 交易貨幣    | 交易的貨幣                                                                                                                                            |
| 交易金額      | 交易的金額                                                                                                                                              |
| 已繳交稅金            | 已繳交稅金的金額 (營業稅、使用稅或加值稅/貨物及服務稅)                                                                                                                  |
| 淨收入            | 扣除已繳交稅金的交易金額                                                                                                                                   |
| Microsoft Store 費用               | Microsoft 所保留的淨收入百分比，作為在 Microsoft Store 中提供應用程式或附加元件的費用                                                      |
| 應用程式收入            | 扣除 Microsoft Store 費用後的淨收入                                                                                                                                       |
| 已預繳稅金          | 預繳的所得稅金額 (不包含在**已保留** CSV 檔案中)                                                                                                |
| 付款                 | 扣除任何適用已預繳所得稅之後的應用程式收入 (以交易貨幣顯示) 不包含在**已保留** CSV 檔案中。                               |
| FX 率                 | 用來將交易貨幣轉換成付款貨幣的外幣匯率                                                                                         |
| 付款貨幣        | 用來進行付款的貨幣                                                                                                                                       |
| 已轉換付款       | 使用 FX 率轉換成付款貨幣的付款金額                                                                                                         |
| 稅務匯款模型         | 負責繳交稅金 (營業稅、使用稅或加值稅/貨物及服務稅) 的合作對象                                                                                                   |
| 資格日期時間   | 交易收入符合支出資格的日期和時間 (UTC)。 建立支出時，其會包含資格日期時間在支出建立日期之前的交易收入 (僅包含在**已保留** CSV 檔案 中)。 |
| Charges                 | 顯示彙總在交易金額資料行中所有費用詳細資料的明細 (僅會針對 Azure Marketplace 包含，不會包含在**已保留** CSV 檔案中)。 |
|||

## <a name="next-step"></a>後續步驟

- [支出原則詳細資料](./payout-policy-details.md)
