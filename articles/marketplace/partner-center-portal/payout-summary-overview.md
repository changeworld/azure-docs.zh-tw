---
title: 支出摘要總覽-Azure Marketplace
description: '[付款摘要] 會顯示您在供應專案中所獲得之金錢的詳細資料。 也可以讓您了解何時會收到付款與付款金額。'
author: mingshen
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/24/2020
ms.openlocfilehash: 48644f2f8148a7aa1974be0d7f761e9b3a55612d
ms.sourcegitcommit: 31236e3de7f1933be246d1bfeb9a517644eacd61
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/04/2020
ms.locfileid: "82783509"
---
# <a name="payout-summary-overview"></a>支出摘要概觀

[付款[摘要](./payout-summary.md)] 會顯示您在 Microsoft 所獲得之金錢的詳細資料。 此外，它也可讓您知道何時會收到付款，以及您要支付多少費用。

如果您在 Azure Marketplace 中銷售供應專案，您也會在付款摘要中看到成功支出的資訊。 如需 Azure Marketplace 付款的詳細資訊，請參閱[Azure Marketplace 參與原則](https://docs.microsoft.com/legal/marketplace/participation-policy)和[Microsoft Azure Marketplace 發行者合約](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RE3ypvt)。

> [!NOTE]
> 若要符合付款資格，您的繼續作業必須達到 $50 的[付款閾值](./payment-thresholds-methods-timeframes.md)。 如需付款閾值的詳細資訊，請參閱[Microsoft Azure Marketplace 發行者合約](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RE3ypvt)。

在合作夥伴中心的 [分析] 區段中，可以使用 [交易發行] 選項的所有報告和深入解析，並在入口網站右上角以此圖示存取：

![說明合作夥伴中心入口網站右上角的支出圖示。](./media/payout-overview.png)

## <a name="roles-and-permissions"></a>角色和權限

這些是存取支出報表的角色和許可權：

| 報表/頁面 | 帳戶擁有者 | Manager | 開發人員 | 商務參與者 | 財務參與者 | 行銷人員 |
| --- | --- | --- | --- | --- | --- | --- |
| 取得報告（包括近乎即時的資料） | 可檢視 | 可檢視 | 沒有存取權 | 沒有存取權 | 可檢視 | 沒有存取權 |
| 意見反應報告/回應 | 可檢視和傳送意見反應 | 可檢視和傳送意見反應 | 可檢視和傳送意見反應 | 沒有存取權 | 沒有存取權 | 可檢視和傳送意見反應 |
| --- | --- | --- | --- | --- | --- | --- |
| 健康情況報告（包括近乎即時的資料） | 可檢視 | 可檢視 | 可檢視 | 可檢視 | 沒有存取權 | 沒有存取權 |
| 使用方式報告 | 可檢視 | 可檢視 | 可檢視 | 可檢視 | 沒有存取權 | 沒有存取權 |
| 支付帳戶 | 可更新 | 沒有存取權 | 沒有存取權 | 沒有存取權 | 可更新 | 沒有存取權 |
| 稅金設定檔 | 可更新 | 沒有存取權 | 沒有存取權 | 沒有存取權 | 可更新 | 沒有存取權 |
| 支出摘要 | 可檢視 | 沒有存取權 | 沒有存取權 | 沒有存取權 | 可檢視 | 沒有存取權  |
| | | | | | | |

## <a name="payout-report-differences"></a>支出報表差異

這是 Cloud Partner 入口網站（舊）和合作夥伴中心（新）之間的支出報告差異：

| 雲端合作夥伴入口網站 | 合作夥伴中心 |
| --- | --- |
| **連結**：https://cloudpartner.azure.com/ | **連結**： https://partner.microsoft.com/dashboard/payouts/reports/transactionhistory和https://partner.microsoft.com/dashboard/payouts/reports/incentivepayments |
| **導覽**：深入解析支出中提供的支出報告 | **導覽**：合作夥伴中心提供的支出報告–支出圖示 |
| **範圍**：<ul><li>針對 [收集進行中]、[已收集] 和 [付費]，可以看到每行專案的交易。</li><li>報表-顯示訂單建立後的所有明細專案，包括進行中的收集和進行中的帳單，以及尚未符合資格的收集狀態和明細專案。</li></ul> | **範圍**：<ul><li>會在專案被視為合格收益後顯示。</li><li>客戶必須先支付給 Microsoft，然後 Isv 才會看到付款報告開始。</li><li>支出報表不會顯示進行中的收集和計費。</li></ul> |
| **交易尚未就緒，無法**付款：進行中計費 | **交易尚未就緒，無法**支付費用：下一次預估付款：支出狀態為 [未處理] 狀態。 |
| **支出狀態**： n/a | **支出狀態**：<ul><li>未處理：收益符合付款資格。</li><li>即將推出：在下個月的支出中，將會傳送給發行者。</li><li>已傳送：付款已傳送到您的銀行。</li></ul> |
| | |

## <a name="payment-schedules"></a>付款排程

如需付款排程的討論，包括保留期間、合作夥伴可見度，以及客戶使用信用卡或發票的時間，請參閱付款**詳細資料**主題的[付款](./payout-policy-details.md#payment-schedules)排程一節。

## <a name="transaction-history-download-export"></a>交易記錄下載匯出

此選項會提供您在 [交易歷程記錄] 頁面上看到的每個收益明細專案的下載。 這包括賺取類型、日期、相關聯的交易金額、客戶、產品，以及與獎勵計畫相關的其他交易詳細資料。

| 資料行名稱 | 描述 |
| --- | --- |
| earningId | 每個收益的唯一識別碼 |
| participantId | 方案下合作夥伴賺取的主要身分識別 |
| participantIdType | 獎勵計畫和賣方的計畫識別碼（如果程式適用于商店程式和 Azure Marketplace） |
| participantName | 收益合作夥伴的名稱 |
| partnerCountryCode | 賺取合作夥伴的地點/國家/地區 |
| programName | 獎勵/商店計畫名稱 |
| transactionId | 交易的唯一識別碼 |
| transactionCurrency | 原始客戶交易發生的貨幣（不是合作夥伴位置貨幣） |
| transactionDate | 交易的日期。 適用于許多交易貢獻給一個收益的程式 |
| transactionExchangeRate | 用來顯示對應交易美元金額的匯率 |
| transactionAmount | 原始交易貨幣中的交易數量（根據產生的收益） |
| transactionAmountUSD | 以美元為單位的交易金額（美元） |
| 向上 | 收益的商務規則 |
| earningRate | 交易金額所套用的獎勵率，以產生收益 |
| quantity | 交易式程式的計費數量。 根據程式而有所不同 |
| quantityType | 數量的類型，例如：計費數量、每月作用中使用者（MAU） |
| earningType | 指出是否為費用、退款、合作行銷、銷售等 |
| earningAmount | 原始交易貨幣中的賺取金額 |
| earningAmountUSD | 賺取金額（美元） |
| earningDate | 賺取的日期 |
| calculationDate | 在系統中計算賺取的日期 |
| earningExchangeRate | 用來顯示對應美元量的匯率 |
| exchangeRateDate | 用來計算 EarningAmount 美元的匯率日期 |
| paymentAmountWOTax | 僅限已傳送&quot; &quot;付款的預付金額（不含稅） |
| paymentCurrency | 付款設定檔中由合作夥伴選擇的貨幣付款。 僅針對已傳送的付款顯示 |
| paymentExchangeRate | 使用 ExchangeRateDate 計算付款貨幣中 paymentAmountWOTax 的匯率 |
| paymentId | 付款的唯一識別碼。 這個數位會顯示在您的 bank 語句中 |
| paymentStatus | [付款狀態] |
| paymentStatusDescription | 付款狀態的描述 |
| customerId | 永遠為空白 |
| customerName | 永遠為空白 |
| partNumber | 永遠為空白 |
| productName | 連結至交易的產品名稱 |
| productId | 唯一的產品識別碼 |
| parentProductId | 唯一的父系產品識別碼。 如果交易沒有父產品，則為 [父系產品識別碼 = 產品識別碼]。 |
| parentProductName | 父系產品的名稱。 如果交易沒有父產品，則為 [父系產品名稱 = 產品名稱]。 |
| productType | 產品的類型（例如應用程式、附加元件和遊戲） |
| invoiceNumber | 發票編號（僅適用于 Enterprise 合約） |
| resellerId | 轉售商識別碼 |
| resellerName | 轉售商名稱 |
| transactionType | 交易類型（例如購買、退款、沖銷和退款） |
| localProviderSeller | 記錄的本機提供者/賣方 |
| taxRemitted | 免稅金額 (銷售、使用或 VAT/GST 稅額)。 |
| taxRemitModel | 負責代繳稅額之一方 (銷售、使用或 VAT/GST 稅額)。 |
| storeFee | Microsoft 保留的金額，讓應用程式或附加元件可在商業 marketplace 中使用。 |
| transactionPaymentMethod | 用於交易的客戶付款條件（例如卡、行動貨運公司帳單和 PayPal） |
| tpan | 協力廠商 ad 網路 |
| Customercountrycomparer | 客戶國家/地區 |
| customerCity | 客戶城市 |
| customerState | 客戶狀態 |
| customerZip | 客戶郵遞區號 |
| TenantID | 租使用者的識別碼 |
| externalReferenceId | 程式的唯一識別碼 |
| externalReferenceIdLabel | 唯一識別碼標籤 |
| transactionCountryCode | 發生交易的國家/地區代碼 |
| taxCountry | 客戶的國家/地區 |
| taxState | 客戶的州 |
| taxCity | 客戶的城市 |
| taxZipCode | 客戶的郵遞區號 |
| LicensingProgramName | 授權方案的名稱 |
| 程式碼 | 要與程式名稱對應的字串 |
| earningAmountInLastPaymentCurrency | 上一個付款貨幣的收益金額（如果未支付先前付款，欄位將會空白） |
| lastPaymentCurrency | [上次付款貨幣] （如果未支付先前付款，欄位會是空的） |
| AssetId | Marketplace 服務客戶訂單的唯一識別碼。 它代表採購明細專案。 可以有多個資產。 |
| OrderId | 與客戶的發票相關 |
| LineItemId | 客戶發票中的個別線條 |
| 客戶國家/地區 | 客戶提供的國家/地區名稱。 這可能與客戶的 Azure 訂用帳戶中的國家/地區不同。 |
| 客戶 EmailAddress | 客戶所提供的電子郵件地址。 這可能與客戶的 Azure 訂用帳戶中的電子郵件地址不同。 |
| SkuId | 發佈期間定義的 SKU 識別碼。 供應項目可以有多個 SKU，但 SKU 只能與單一供應項目相關聯。 |

> [!NOTE]
> 您可以在合作夥伴中心的分析區段中找到交易發行選項的所有報告和深入解析。

## <a name="billing-questions-and-support"></a>計費問題和支援
如需帳單支援，請聯絡商業 marketplace[發行者支援](https://partner.microsoft.com/support/v2/?stage=1)。

## <a name="next-step"></a>後續步驟

- [支出摘要](./payout-summary.md)
