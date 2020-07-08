---
title: 支付摘要概觀 - Azure Marketplace
description: '[支付摘要] 會顯示透過供應項目所賺取金額的詳細資料。 此外，其還會告知何時會收到付款，以及將收到多少款項。'
author: mingshen
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/24/2020
ms.openlocfilehash: a872331238946de0d57e6d42164f1ce7fb1c7357
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.contentlocale: zh-TW
ms.lasthandoff: 05/21/2020
ms.locfileid: "83746196"
---
# <a name="payout-summary-overview"></a>支付摘要概觀

[支付摘要](./payout-summary.md)會顯示透過 Microsoft 所賺取金額的詳細資料。 此外，其還會告知何時會收到付款，以及將收到多少款項。

如果在 Azure Marketplace 中銷售供應項目，則也會在 [支付摘要] 中看到成功支付的資訊。 如需 Azure Marketplace 付款的詳細資訊，請參閱 [Azure Marketplace 參與原則](https://docs.microsoft.com/legal/marketplace/participation-policy) (英文) 和 [Microsoft Azure Marketplace 發行者合約](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RE3ypvt) (英文)。

> [!NOTE]
> 若要符合支付資格，收益必須達到美金 $50 元的[付款門檻](./payment-thresholds-methods-timeframes.md)。 如需付款門檻的詳細資料，請參閱 [Microsoft Azure Marketplace 發行者合約](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RE3ypvt) (英文)。

您可在合作夥伴中心的 [分析] 區段中找到交易發行選項的所有報告和見解，使用入口網站右上角的這個圖示即可進行存取：

![說明合作夥伴中心入口網站右上角的支付圖示。](./media/payout-overview.png)

## <a name="roles-and-permissions"></a>角色和權限

這些是可存取支付報告的角色和權限：

| 報告/頁面 | 帳戶擁有者 | Manager | 開發人員 | 商務參與者 | 財務參與者 | 行銷人員 |
| --- | --- | --- | --- | --- | --- | --- |
| 擷取報告 (包括近即時資料) | 可檢視 | 可檢視 | 無存取權 | 無存取權 | 可檢視 | 無存取權 |
| 意見反應報告/回應 | 可檢視並傳送意見反應 | 可檢視並傳送意見反應 | 可檢視並傳送意見反應 | 無存取權 | 無存取權 | 可檢視並傳送意見反應 |
| --- | --- | --- | --- | --- | --- | --- |
| 健康狀態報告 (包括近乎即時的資料) | 可檢視 | 可檢視 | 可檢視 | 可檢視 | 無存取權 | 無存取權 |
| 使用量報告 | 可檢視 | 可檢視 | 可檢視 | 可檢視 | 無存取權 | 無存取權 |
| 支付帳戶 | 可更新 | 無存取權 | 無存取權 | 無存取權 | 可更新 | 無存取權 |
| 稅金設定檔 | 可更新 | 無存取權 | 無存取權 | 無存取權 | 可更新 | 無存取權 |
| 支付摘要 | 可檢視 | 無存取權 | 無存取權 | 無存取權 | 可檢視 | 無存取權  |
| | | | | | | |

## <a name="payout-report-differences"></a>支付報告差異

以下是 Cloud Partner 入口網站 (舊) 與合作夥伴中心 (新) 之間的支付報告差異：

| 雲端合作夥伴入口網站 | 合作夥伴中心 |
| --- | --- |
| **連結**： https://cloudpartner.azure.com/ | **連結**： https://partner.microsoft.com/dashboard/payouts/reports/transactionhistory 和 https://partner.microsoft.com/dashboard/payouts/reports/incentivepayments |
| **Navigation**：見解支付中提供的支付報告 | **Navigation**：合作夥伴中心提供的支付報告 – 支付圖示 |
| **範圍**：<ul><li>針對進行中、已收取和已支付的收款，可看到每個明細項目的交易。</li><li>報表 – 顯示建立訂購單後的所有明細項目，包括進行中的收款和計費，以及收款狀態和尚未符合付費資格的明細項目。</li></ul> | **範圍**：<ul><li>顯示在視為合格收益後的明細項目。</li><li>客戶必須先支付給 Microsoft，然後 ISV 才會看到支付報告開始。</li><li>支付報告不會顯示進行中的收款和進行中的計費。</li></ul> |
| **尚未準備支付的交易**：進行中的計費 | **尚未準備支付的交易**：下一個預估支付：支付狀態為尚未處理狀態。 |
| **支付狀態**：無 | **支付狀態**：<ul><li>尚未處理：收益符合付款資格。</li><li>近期：收益將在下次每月支付中送交至「發佈者」。</li><li>已送交：付款已送交至銀行。</li></ul> |
| | |

## <a name="payment-schedules"></a>付款排程

如需付款排程的討論，包括保留期間、合作夥伴可見度，以及客戶使用信用卡或發票的時機，請參閱**支付詳細資料**主題的＜[付款排程](./payout-policy-details.md#payment-schedules)＞一節。

## <a name="transaction-history-download-export"></a>交易歷程記錄下載匯出

此選項可用來下載在 [交易歷程記錄] 頁面上看到的每個收益明細項目。 這包括收益類型、日期、相關聯的交易金額、客戶、產品，以及與獎勵計畫相關的其他交易詳細資料。

| 資料行名稱 | 描述 |
| --- | --- |
| earningId | 每項收益的唯一識別碼 |
| participantId | 根據該計畫獲取收益的合作夥伴主要身分識別 |
| participantIdType | 如果是獎勵計畫，則為計畫識別碼；如果該計畫適用於商店計畫和 Azure Marketplace，則為賣方 |
| participantName | 收益合作夥伴的名稱 |
| partnerCountryCode | 收益合作夥伴的位置/國家/地區 |
| programName | 獎勵/商店計畫名稱 |
| transactionId | 交易的唯一識別碼 |
| transactionCurrency | 進行原始客戶交易時所使用的貨幣 (不是合作夥伴位置的貨幣) |
| transactionDate | 交易的日期。 對於將許多交易貢獻給一項收益的計畫很有用 |
| transactionExchangeRate | 用來顯示對應交易美金金額的匯率 |
| transactionAmount | 原始交易貨幣 (據以產生收益) 的交易金額 |
| transactionAmountUSD | 以美金 (USD) 為單位的交易金額 |
| lever | 收益的商務規則 |
| earningRate | 套用到交易金額以產生收益的獎勵率 |
| quantity | 交易計畫的計費數量。 根據計畫而有所不同 |
| quantityType | 數量的類型，例如：計費數量、每月作用中使用者 (MAU) |
| earningType | 指出其是否為費用、回饋、合作、銷售等 |
| earningAmount | 原始交易貨幣的收益金額 |
| earningAmountUSD | 以美金為單位的收益金額 |
| earningDate | 收益的日期 |
| calculationDate | 在系統中計算收益的日期 |
| earningExchangeRate | 用來顯示對應美金金額的匯率 |
| exchangeRateDate | 用來計算 EarningAmountUSD 的匯率日期 |
| paymentAmountWOTax | 收款人貨幣的收益金額 (不含稅)，僅適用於&quot;已送交&quot;付款 |
| paymentCurrency | 付款設定檔中由合作夥伴選擇的收款人貨幣。 僅針對已送交付款顯示 |
| paymentExchangeRate | 使用 ExchangeRateDate 以付款貨幣計算 paymentAmountWOTax 的匯率 |
| paymentId | 付款的唯一識別碼。 這個數字會顯示在銀行對帳單中 |
| paymentStatus | 付款狀態 |
| paymentStatusDescription | 付款狀態的描述 |
| customerId | 一律為空白 |
| customerName | 一律為空白 |
| partNumber | 一律為空白 |
| productName | 連結至交易的產品名稱 |
| productId | 唯一的產品識別碼 |
| parentProductId | 唯一的父產品識別碼。 如果該交易沒有父產品，則父系產品識別碼 = 產品識別碼。 |
| parentProductName | 父產品的名稱。 如果該交易沒有父產品，則父系產品名稱 = 產品名稱。 |
| productType | 產品的類型 (例如應用程式、附加元件以及遊戲) |
| invoiceNumber | 發票編號 (僅適用於 Enterprise 合約) |
| resellerId | 轉銷商識別碼 |
| resellerName | 轉銷商名稱 |
| transactionType | 交易的類型 (例如購買、退貨、沖銷和退款) |
| localProviderSeller | 記錄的本地提供者/賣方 |
| taxRemitted | 已繳交稅金的金額 (營業稅、使用稅或增值稅/貨物及服務稅)。 |
| taxRemitModel | 負責繳交稅金 (營業稅、使用稅或增值稅/貨物及服務稅) 的合作對象。 |
| storeFee | Microsoft 所保留的金額，作為在商業市集中提供應用程式或附加元件的費用。 |
| transactionPaymentMethod | 用於交易的客戶付款方式 (例如卡片、行動裝置電信業者代扣和 PayPal) |
| tpan | 協力廠商 AD 網路 |
| customerCountry | 客戶國家/地區 |
| customerCity | 客戶縣/市 |
| customerState | 客戶州/省 |
| customerZip | 客戶郵遞區號 |
| TenantID | 租用戶的識別碼 |
| externalReferenceId | 計畫的唯一識別碼 |
| externalReferenceIdLabel | 唯一識別碼標籤 |
| transactionCountryCode | 交易發生的國家/地區代碼 |
| taxCountry | 客戶的國家/地區 |
| taxState | 客戶的州/省 |
| taxCity | 客戶的縣/市 |
| taxZipCode | 客戶的郵遞區號 |
| LicensingProgramName | 授權方案的名稱 |
| Program Code | 要與方案名稱對應的字串 |
| earningAmountInLastPaymentCurrency | 上次付款貨幣的收益金額 (如果之前沒有付款，欄位將會是空白) |
| lastPaymentCurrency | 上次付款貨幣 (如果之前沒有付款，欄位將會是空白) |
| AssetId | Marketplace 服務客戶訂單的唯一識別碼。 其代表購買明細項目。 可有多個資產。 |
| OrderId | 與客戶的發票相關 |
| LineItemId | 客戶發票中的個別明細項目 |
| Customer Country/Region | 客戶提供的國家/地區名稱。 這可能與客戶 Azure 訂用帳戶中的國家/地區不同。 |
| Customer EmailAddress | 客戶提供的電子郵件地址。 這可能與客戶 Azure 訂用帳戶中的電子郵件地址不同。 |
| SkuId | 發佈期間定義的 SKU 識別碼。 供應項目可有多個 SKU，但一個 SKU 只能與單一供應項目建立關聯。 |

> [!NOTE]
> 您可在合作夥伴中心的 [分析] 區段中找到交易發行選項的所有報告和見解。

## <a name="billing-questions-and-support"></a>計費問題和支援
如需帳務支援，請連絡商業市集[發佈者支援](https://partner.microsoft.com/support/v2/?stage=1)。

## <a name="next-step"></a>後續步驟

- [支付摘要](./payout-summary.md)
