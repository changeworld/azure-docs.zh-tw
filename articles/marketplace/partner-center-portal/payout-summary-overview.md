---
title: 付款摘要總覽，Azure Marketplace
description: '[支付摘要] 會顯示透過供應項目所賺取金額的詳細資料。 此外，其還會告知何時會收到付款，以及將收到多少款項。'
ms.subservice: partnercenter-marketplace-publisher
ms.service: marketplace
ms.topic: article
author: mingshen-ms
ms.author: mingshen
ms.date: 09/09/2020
ms.openlocfilehash: 708fc2c0783bdefa4ac4fa4b73f10733bba0bc04
ms.sourcegitcommit: 3c66bfd9c36cd204c299ed43b67de0ec08a7b968
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/10/2020
ms.locfileid: "90006919"
---
# <a name="payout-summary-overview"></a>支付摘要概觀

[支付摘要](./payout-summary.md)會顯示透過 Microsoft 所賺取金額的詳細資料。 此外，其還會告知何時會收到付款，以及將收到多少款項。

如果您在 Azure Marketplace 銷售供應專案，也會在付款摘要中看到成功支出的資訊。 如需有關 Azure Marketplace 付款的詳細資訊，請參閱「 [在商業市場中取得款項](./get-paid.md) 」和「 [Microsoft 發行者合約](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RE4xqkx)」。

> [!NOTE]
> 若要符合支付資格，收益必須達到美金 $50 元的付款門檻。 如需詳細資訊，請參閱 [Microsoft 發行者合約](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RE4xqkx)。

若要查看支出詳細資料，請登入 [合作夥伴中心](https://partner.microsoft.com/dashboard/home) ，然後選取畫面右上角的付款圖示：

![說明合作夥伴中心入口網站右上角的支付圖示。](./media/payout-overview.png)

## <a name="roles-and-permissions"></a>角色和權限

這些是存取付款報表的角色和許可權：

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

## <a name="payment-schedules"></a>付款排程

如需付款排程的討論，包括保留期間、合作夥伴可見度，以及當客戶使用信用卡或發票時，請參閱付款**詳細資料**文章的[付款](./payout-policy-details.md#payment-schedules)排程一節。

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
| paymentCurrency | 由合作夥伴在付款設定檔中選擇的付款貨幣。 僅針對已送交付款顯示 |
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
