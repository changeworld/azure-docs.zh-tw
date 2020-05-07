---
title: 支出報表-Microsoft 商業 marketplace
description: 支出報表會向您顯示您在供應專案中所獲得的款項的詳細資料，包括付款金額和支付費用。
author: mingshen
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/24/2020
ms.openlocfilehash: 0380330c2cfd4b0a72dcc158a0d6e051d5547b31
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/06/2020
ms.locfileid: "82857010"
---
# <a name="payout-summaries"></a>支出摘要

[付款摘要] 會顯示您在 Microsoft 所獲得之金錢的詳細資料。 也可以讓您了解何時會收到付款與付款金額。

如果您在 Azure Marketplace 銷售產品，您也會在 \[支付摘要\] 中看到成功支付的相關資訊。 如需詳細資訊，請參閱[Microsoft Azure Marketplace 參與原則](https://docs.microsoft.com/legal/marketplace/participation-policy)和[Microsoft Azure Marketplace 發行者合約](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RE3ypvt)。

> [!NOTE]
> 若要符合付款資格，您的繼續作業必須達到 $50 的[付款閾值](payment-thresholds-methods-timeframes.md)。 如需付款閾值的詳細資訊，請參閱此頁面並審查應用程式開發人員合約。

> [!NOTE]
> 如果您要尋找有關支出的支援，包括設定付款帳戶、遺失支出、在保存支出或任何其他專案，請在[這裡](https://developer.microsoft.com/windows/support)聯絡支援。

## <a name="access-the-payout-summary-pages"></a>存取支出摘要頁面

若要開啟其中一個支出摘要頁面：

1. 選取右上角的支出圖示。
2. 選取 [交易歷程記錄]、[付款] 或 [匯出資料]。

## <a name="transaction-history-page"></a>交易記錄頁面

此頁面會顯示您所有的個人收益，包括每個的日期、類型和收益。 您可以選取要查看的時間週期，也可以依註冊識別碼、程式、付款識別碼、賺取類型、杠杆和狀態進行篩選。 資料適用于目前的會計年度（7月1日至6月30日）和前兩個會計年度。

若要查看收益的更多詳細資料，請選取頁面右側的向下箭號。 這會顯示 [杠杆]、[收益金額] 和 [產品]。 如果此資料有任何一項無法使用，但您需要存取它，請聯絡[支援](https://developer.microsoft.com/windows/support)人員。 如果收益是調整的結果，而不是交易，則不會顯示 [產品] 欄位。

若要匯出此頁面上的任何交易資料，請使用 [**匯出資料**] 頁面。

## <a name="payments-page"></a>付款頁面

此頁面上的總計代表您參與的所有程式。 您可以依 [參與者識別碼]、[程式]、[付款識別碼] 和 [賺取類型] 進行篩選。 金額會以美元為單位來提供。 付費值也會以 [付費貨幣] 顯示。

| 區域                   | 描述                                                                                |
|------------------------|---------------------------------------------------------------------------------------------|
| 今年總付費   | 所有程式的總金額（以美元為單位）       |
| 下一個估計付款 | 您的單一下一期付款（即使有其他人即將推出），以美元為單位 |
| 上次付款           | 您最近付款的數量（以美元為單位）、計畫名稱和計畫           |
| 依來源的付款     | 過去12個月內，程式所代表的付款金額（以美元為單位）           |
| 付款               | 選取 [**付費**] 或 [**暫**止]，然後依需要排序。 如需特定付款的其他詳細資料，請選取 [**查看**]。 若要下載付款匯款語句的複本，請選取 [**下載**]。 因為交易記錄資料最多可能需要24小時才會出現，所以您可能不會立即看到相關的收益。 |
|||

若要匯出此頁面上的任何資料，請**選取 [匯出]** ，並遵循 [匯出資料] 頁面上的指示。

## <a name="transaction-history-page"></a>交易記錄頁面

此頁面會顯示您所有的個人收益，包括每個的日期、類型和收益。 您可以選取要查看的時段，而且可以依註冊識別碼、程式、付款識別碼、賺取類型、杠杆和狀態進行篩選。 資料適用于目前的會計年度（7月1日至6月30日）和前兩個會計年度。

若要查看收益的更多詳細資料，請選取頁面右側的向下箭號。 這會顯示 [杠杆]、[收益金額] 和 [產品]。 如果此資料有任何一項無法使用，但您需要存取它，請聯絡[支援](https://developer.microsoft.com/windows/support)人員。 如果收益是調整的結果，而不是交易，則不會顯示 [產品] 欄位。

若要匯出此頁面上的任何交易資料，請**選取 [匯出]** ，並遵循 [匯出資料] 頁面上的指示。 從 [交易記錄] 頁面匯出的檔案會顯示交易貨幣中的資料、交易貨幣和美元的收益，以及付費貨幣值。

## <a name="payment-status"></a>[付款狀態]

| 賺取狀態           | 原因                                                                                                                                      | 需要合作夥伴動作嗎？                                   |
|--------------------------|---------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------|
| 尚未              | 收益符合付款資格。 如獎勵計畫的程式指南中所定義，它會維持在此狀態的冷卻期間。 | 否                                                         |
| 提交                 | 付款順序在處理付款前產生擱置內部評論                                                               | 否                                                         |
| 待決的稅務發票      | 您的稅務發票不完整或無效                                                                                                  | 您必須先更新您的稅務發票，才能支付費用 |
| 審查期間拒絕   | 付款于審查期間遭到拒絕                                                                                                     | 請洽詢[Microsoft 支援服務](https://developer.microsoft.com/windows/support)以取得詳細資料                      |
| Failed                   | 付款因 Microsoft 系統錯誤而失敗                                                                                         | 請洽詢[Microsoft 支援服務](https://developer.microsoft.com/windows/support)以取得詳細資料                      |
| 進行中              | 付款進行中                                                                                                                 | 否                                                         |
| 不正確的付款        | 付款 recouping 正在進行中                                                                                                       | 否                                                         |
| 已傳送                     | 付款已傳送到您的銀行                                                                                                     | 否                                                         |
| 重新處理             | 付款發生 Microsoft 系統錯誤，正在重新處理                                                                  | 否                                                         |
| Reversed                 | 付款已由您的銀行反轉，並將在下一個付款週期再次傳送                                                     | 否                                                         |
| 已拒絕稅務發票     | 您的稅務發票在審查期間遭到拒絕。 所有暫止的付款都會保留，直到稅務發票審核完成為止。                 | 請洽詢[Microsoft 支援服務](https://developer.microsoft.com/windows/support)以取得詳細資料                      |
| 審查下的稅務發票 | 正在審核您的稅務發票。 當稅務發票核准之後，您的付款就會釋出。                                   | 否                                                         |
| 已拒絕                 | 您的銀行已拒絕付款                                                                                                      | 如需詳細資訊，請洽詢您的銀行。                             |
|||

## <a name="export-data-page"></a>[匯出資料] 頁面

請遵循這些指示來匯出資料。

[匯出資料] 頁面不會自行重新整理。 您可能需要手動重新整理頁面，才能看到最新的資料。

您的篩選可能會導致**資料無法使用**錯誤。 這可能表示您已保留在三個月內選取的預設時間週期，然後從該期間外的收益中選取付款識別碼。 請展開您的時間週期，然後再試一次。

## <a name="payments"></a>付款

![匯出付款](./media/pc-export-payments.png)

此選項可讓您針對指定的程式、相關聯的稅金和匯總的賺取金額，下載您在銀行內收到的付款。 這份報告適用于許多合作夥伴中心方案，因此某些資料行可能會不適用至您的報表。 這些資料行標示如下。

| 資料行名稱              | 描述                                                                                                                               |
|--------------------------|-----------------------------------------------------------------------------------------------------------------------------------------  |
| participantID            | 方案下合作夥伴賺取的主要身分識別                                                                             |
| participantIDType        | 適用于商店計畫的獎勵計畫和賣方識別碼的程式識別碼通常為                                                                |
| participantName          | 收益合作夥伴的名稱                                                                                                               |
| programName              | 獎勵/商店計畫名稱                                                                                                              |
| 盈餘                   | 該方案/participantID 的預付貨幣金額                                                                       |
| earnedUSD                | 方案/參與者識別碼所獲的金額（以美元為單位）                                                                                      |
| withheldTax              | 方案/participantID 的付費貨幣所扣的稅金金額                                                               |
| salesTax                 | 方案/participantID 的總銷售稅額（僅適用于獎勵方案）                   |
| serviceFeeTax            | 方案/participantID 的 serviceFeeTax 總金額（僅適用于商店程式和 Azure Marketplace） |
| totalPayment             | 以當地貨幣為單位的總付款，不包括預繳稅金，並包含方案/participantID 的銷售稅額（如果適用）   |
| currencyCode             | 支付貨幣代碼                                                                                                                      |
| paymentMethod            | 用來支付合作夥伴的方法，例如電子銀行轉帳、點數注意事項                                                     |
| paymentID                | 付款的唯一識別碼。 這個數位通常會顯示在您的 bank 語句中（僅適用于 SAP 付款）。              |
| paymentStatus            | [付款狀態]                                                                                                                            |
| paymentStatusDescription | 付款狀態的易記描述                                                                                                    |
| paymentDate              | 從 Microsoft 傳送的日期付款                                                                                                      |
|||

## <a name="transaction-history"></a>交易記錄

![匯出交易歷程記錄](./media/pc-export-transaction.png)

此選項提供您在 [交易歷程記錄] 頁面中看到的每個收益明細專案的下載、賺取類型、日期、相關聯的交易金額、客戶、產品，以及適用于您程式的其他交易詳細資料。

| 資料行名稱                    | 描述                                                                                                                              | 獎勵/Store/Azure Marketplace 的適用性           |
|--------------------------------|------------------------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------|
| earningId                      | 每個收益的唯一識別碼                                                                                                       | 全部                                                            |
| participantId                  | 方案下合作夥伴賺取的主要身分識別                                                                            | 全部                                                            |
| participantIdType              | 如果是商店程式和 Azure Marketplace，則大多是獎勵計畫和賣方的程式識別碼                                          | 全部                                                            |
| participantName                | 收益合作夥伴的名稱                                                                                                              | 全部                                                            |
| partnerCountryCode             | 賺取合作夥伴的地點/國家/地區                                                                                                  | 全部                                                            |
| programName                    | 獎勵/商店計畫名稱                                                                                                             | 全部                                                            |
| transactionId                  | 交易的唯一識別碼                                                                                                    | 全部                                                            |
| transactionCurrency            | 原始客戶交易發生的貨幣（這不是合作夥伴位置貨幣）                                     | 全部                                                            |
| transactionDate                | 交易的日期。 適用于許多交易貢獻給一個收益的程式                                           | 全部                                                            |
| transactionExchangeRate        | 用來顯示對應交易美元金額的匯率日期                                                                 | 全部                                                            |
| transactionAmount              | 原始交易貨幣中的交易數量（根據產生的收益）                                              | 全部                                                            |
| transactionAmountUSD           | 交易金額（美元）                                                                                                                | 全部                                                            |
| 向上                          | 表示獲得收益的商務規則                                                                                                  | 全部                                                            |
| earningRate                    | 交易金額所套用的獎勵率，以產生收益                                                                      | 全部                                                            |
| quantity                       | 根據程式而有所不同。 表示交易式程式的計費數量                                                            | 全部                                                            |
| quantityType                   | 表示數量的類型，例如，計費的數量、MAU                                                                             | 全部                                                            |
| earningType                    | 指出是否為費用、退款、共同作業、銷售等等                                                                                          | 全部                                                            |
| earningAmount                  | 原始交易貨幣中的賺取金額                                                                                      | 全部                                                            |
| earningAmountUSD               | 賺取金額（美元）                                                                                                                    | 全部                                                            |
| earningDate                    | 賺取的日期                                                                                                                      | 全部                                                            |
| calculationDate                | 在系統中計算賺取的日期                                                                                            | 全部                                                            |
| earningExchangeRate            | 用來顯示對應美元量的匯率                                                                                  | 全部                                                            |
| exchangeRateDate               | 用來計算 EarningAmount 美元的匯率日期                                                                                   | 全部                                                            |
| paymentAmountWOTax             | 僅限「已傳送」付款的貨幣金額（不含稅）                                                                 | 全部                                                            |
| paymentCurrency                | 付款設定檔中由合作夥伴選擇的貨幣付款。 僅針對已傳送的付款顯示                                                   | 全部                                                            |
| paymentExchangeRate            | 使用 ExchangeRateDate 計算付款貨幣中 paymentAmountWOTax 的匯率                                            | 全部                                                            |
| claimId                        | 宣告的唯一識別碼                                                                                                              | 獎勵-僅限某些程式                                |
| planId                         | 方案的唯一識別碼                                                                                                               | 獎勵-僅限某些程式                                |
| paymentId                      | 付款的唯一識別碼。 此數位通常會顯示在您的 bank 語句中                                                 | 僅限 SAP 付款                                              |
| paymentStatus                  | [付款狀態]                                                                                                                           | 全部                                                            |
| paymentStatusDescription       | 付款狀態的易記描述                                                                                                   | 全部                                                            |
| customerId                     | 永遠為空白                                                                                                                     | 僅獎勵方案（例外狀況： OEM）和 Azure Marketplace |
| customerName                   | 永遠為空白                                                                                                                     | 僅獎勵方案（例外狀況： OEM）和 Azure Marketplace |
| partNumber                     | 永遠為空白                                                                                                                     | 一些獎勵和商店程式和 Azure Marketplace        |
| productName                    | 連結至交易的產品名稱                                                                                                       | 全部                                                            |
| productId                      | 唯一的產品識別碼                                                                                                                | 儲存和 Azure Marketplace                                    |
| parentProductId                | 唯一的父系產品識別碼。 如果交易沒有父產品，則為 [父系產品識別碼 = 產品識別碼]。 | 儲存和 Azure Marketplace                                    |
| parentProductName              | 父系產品的名稱。 如果交易沒有父產品，則為 [父系產品名稱 = 產品名稱]。   | 儲存和 Azure Marketplace                                    |
| productType                    | 產品類型，例如應用程式、附加元件或遊戲                                                                                        | 儲存和 Azure Marketplace                                    |
| invoiceNumber                  | 發票編號（僅適用于 EA）                                                                                                  | 獎勵和 Azure Marketplace-僅限某些程式           |
| subscriptionId                 | 與客戶相關聯的訂用帳戶識別碼                                                                                         | 獎勵-僅限某些程式                                 |
| And subscription.subscriptionstartdate          | [訂閱開始日期]                                                                                                                  | 獎勵-僅限某些程式                                 |
| Subscription.subscriptionenddate            | [訂閱結束日期]                                                                                                                    | 獎勵-僅限某些程式                                 |
| resellerId                     | 轉售商識別碼                                                                                                                      | 獎勵-僅限某些程式                                 |
| resellerName                   | 轉售商名稱                                                                                                                            | 獎勵-僅限某些程式                                 |
| distributorId                  | 散發者識別碼                                                                                                                   | 獎勵-僅限某些程式                                 |
| distributorName                | 散發者名稱                                                                                                                         | 獎勵-僅限某些程式                                 |
| agreementNumber                | 合約編號                                                                                                                         | 獎勵-僅限某些程式                                 |
| agreementStartDate             | [合約開始日期]                                                                                                                     | 獎勵-僅限某些程式                                 |
| agreementEndDate               | [合約結束日期]                                                                                                                       | 獎勵-僅限某些程式                                 |
| workload                       | 工作負載                                                                                                                                 | 獎勵-僅限某些程式                                 |
| transactionType                | 交易類型，例如購買、退款、反轉或退款                                                               | 儲存和 Azure Marketplace                                    |
| localProviderSeller            | 記錄的本機提供者/賣方                                                                                                          | 僅限存放區                                                     |
| taxRemitted                    | 已匯款的稅額（銷售、使用或 加值稅/GST 稅金）                                                                                   | 儲存和 Azure Marketplace                                    |
| taxRemitModel                  | 負責支付稅金的當事人（銷售、使用或 加值稅/GST 稅金）                                                                    | 僅限存放區                                                     |
| storeFee                       | Microsoft 保留的金額，讓應用程式或附加元件可以在商店中使用                                           | 僅限存放區                                                     |
| transactionPaymentMethod       | 用於交易的客戶付款條件，例如卡、行動貨運公司帳單或 PayPal                                | 儲存和 Azure Marketplace                                    |
| tpan                           | 表示協力廠商 ad 網路                                                                                                     | Store-僅廣告                                               |
| Customercountrycomparer                | 客戶國家/地區                                                                                                                         | 儲存和 Azure Marketplace                                    |
| customerCity                   | 客戶城市                                                                                                                            | 儲存和 Azure Marketplace                                    |
| customerState                  | 客戶狀態                                                                                                                           | 儲存和 Azure Marketplace                                    |
| customerZip                    | 客戶郵遞區號                                                                                                                 | 儲存和 Azure Marketplace                                    |
| purchaseTypeCode               | 永遠為空白                                                                                                                     | 獎勵計畫-CRI                                        |
| purchaseOrderType              | 永遠為空白                                                                                                                     | 獎勵計畫-CRI                                        |
| purchaseOrderCoverageStartDate | 永遠為空白                                                                                                                     | 獎勵計畫-CRI                                        |
| purchaseOrderCoverageEndDate   | 永遠為空白                                                                                                                     | 獎勵計畫-CRI                                        |
| programOfferingLevel           |                                                                                                                                          | 獎勵計畫-CRI                                        |
| TenantID                       |                                                                                                                                          | 獎勵計畫                                             |
| externalReferenceId            | 程式的唯一識別碼                                                                                                        | 直接付費方案（獎勵與商店）                      |
| externalReferenceIdLabel       | 唯一識別碼標籤                                                                                                                  | 直接付費方案（獎勵與商店）                      |
|||

## <a name="historical-statements"></a>歷程記錄語句

![匯出歷程記錄語句](./media/pc-export-statements.png)

1 2019 年7月之前的交易記錄會分開處理。 語句會使用下欄欄位，而不是目前的欄位。

> [!NOTE]
> 舊版交易記錄具有名為「已保留」的資料行，其對應于新式歷程記錄中的「收益」資料行，不同之處在于它會排除狀態 = 「已傳送付款」的所有收益。

> [!NOTE]
> 3M、6M 或12M 等篩選準則不會套用到歷程**記錄語句**區段。

| 欄位名稱              | 描述                                                                                                                                                             |
|-------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 收入來源          | 根據交易發生的來源，例如 Microsoft Store、Windows Phone 商店、Windows Store 8 或廣告                  |
| 訂單識別碼                | 唯一的訂單識別碼。 此識別碼可讓您使用其各自的非購買交易（例如退款或退款）來識別購買交易。 兩者具有相同的訂單識別碼。 此外，在有多個付款條件用於單一購買的分割費用的情況下，它可讓您連結購買交易。 |
| Transaction ID          | 唯一的交易識別碼。                                                                                                                                          |
| 交易日期時間   | 發生該筆交易的日期與時間 (UTC)。                                                                                                                       |
| 父系產品識別碼       | 唯一的父系產品識別碼。 如果交易沒有父產品，則為 [父系產品識別碼 = 產品識別碼]。                                |
| Product ID              | 唯一的產品識別碼。                                                                                                                                              |
| 父系產品名稱     | 父系產品的名稱。 如果交易沒有父產品，則為 [父系產品名稱 = 產品名稱]。                                  |
| 產品名稱            | 產品的名稱                                                                                                                                                    |
| 產品類型            | 產品類型，例如應用程式、附加元件或遊戲                                                                                                                       |
| 數量                | 當收入來源為商務用 Microsoft Store 時，數量代表購買的授權數目。 針對所有其他的收入來源，數量永遠都會是 1。 即使單一交易分成兩個明細專案，因為使用了兩種不同的付款條件，每個明細專案都會顯示一個數量為1。 |
| 交易類型        | 交易類型，例如購買、退款、反轉或退款                                                                                              |
| 付款方式          | 用於交易的客戶付款條件，例如卡、行動貨運公司帳單或 PayPal                                                               |
| 國家/地區        | 發生交易的國家/地區                                                                                                                          |
| 當地提供者/賣方 | 記錄的本機提供者/賣方                                                                                                                                        |
| [交易貨幣]    | 交易的貨幣                                                                                                                                            |
| [交易金額]      | 交易的金額                                                                                                                                              |
| 免稅            | 已匯款的稅額（銷售、使用或 加值稅/GST 稅金）                                                                                                                  |
| 淨收入            | 交易金額較少匯款                                                                                                                                   |
| 市集費用               | Microsoft 保留的淨回條百分比，代表將應用程式或附加元件提供給市集中使用的費用                                                      |
| App 收益            | 淨收據減去商店費用                                                                                                                                       |
| 代扣稅額          | 預繳稅金的金額（包含在**保留**的 CSV 檔案中）                                                                                                |
| 付款                 | App 收益減去任何適用的代扣稅額收入 ([交易貨幣] 中顯示的金額)。 不包含在**保留**的 CSV 檔案中。                               |
| FX 匯率                 | 用來將交易貨幣轉換為付款貨幣的外匯匯率                                                                                         |
| 付款貨幣        | 付款的建立貨幣                                                                                                                                       |
| 已轉換付款       | 使用 FX 費率轉換為付款貨幣的付款金額                                                                                                         |
| 代繳稅模型         | 負責支付稅金的當事人（銷售、使用或 加值稅/GST 稅金）                                                                                                   |
| 資格日期時間   | 交易收益變成符合支付 (UTC) 資格的日期和時間。 建立付款時，它會在付款建立日期之前（僅包含在**保留**的 CSV 檔案中），繼續進行符合資格日期時間的交易。 |
| Charges                 | 顯示匯總于 [交易金額] 資料行中的所有費用明細明細（僅包含在 Azure Marketplace 中，不包含在**保留**的 CSV 檔案中）。 |
|||

## <a name="next-step"></a>後續步驟

- [支出原則詳細資料](./payout-policy-details.md)
