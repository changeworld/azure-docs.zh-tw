---
title: 了解詳細使用量和費用
description: 了解如何讀取並了解詳細的使用量和費用檔案。 檢視檔案中使用的詞彙和說明清單。
author: bandersmsft
ms.reviewer: micflan
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 08/20/2020
ms.author: banders
ms.openlocfilehash: 559fe43bbd9b8951a97733ae1250046f65f10404
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/17/2020
ms.locfileid: "92149362"
---
# <a name="understand-the-terms-in-your-azure-usage-and-charges-file"></a>了解 Azure 使用量和費用檔案中的詞彙

詳細的使用量和費用檔案包含在指定的期間內以協商費率、購買 (例如，保留、Marketplace 費用) 和退費為基礎的每日額定使用量。
費用不包括信用額度、稅金，或其他費用或折扣。
下表涵蓋每種帳戶類型所包含的費用。

帳戶類型 | Azure 使用情況 | Marketplace 使用情況 | 購買 | 退費
--- | --- | --- | --- | ---
Enterprise 合約 (EA) | 是 | 是 | 是 | 否
Microsoft 客戶合約 (MCA) | 是 | 是 | 是 | 是
隨用隨付 (PAYG) | 是 | 是 | 否 | 否

若要深入了解 Marketplace 訂單 (也稱為外部服務)，請參閱[了解您的 Azure 外部服務費用](understand-azure-marketplace-charges.md)。

若要下載指示，請參閱[如何取得您的 Azure 帳單發票和每日使用量資料](../manage/download-azure-invoice-daily-usage-date.md)。
您可以在 Microsoft Excel 或其他試算表應用程式中開啟您的使用量和費用 CSV 檔案。

## <a name="list-of-terms-and-descriptions"></a>詞彙和說明清單

下表說明最新版 Azure 使用量和費用檔案中使用的重要詞彙。
此清單涵蓋「隨用隨付」(PAYG)、Enterprise 合約 (EA) 和 Microsoft 客戶合約 (MCA) 帳戶。

詞彙 | 帳戶類型 | 說明
--- | --- | ---
AccountName | EA、PAYG | EA 註冊帳戶或 PAYG 計費帳戶的顯示名稱。
AccountOwnerId<sup>1</sup> | EA、PAYG | EA 註冊帳戶或 PAYG 計費帳戶的唯一識別碼。
AdditionalInfo | 全部 | 服務專屬的中繼資料。 例如，虛擬機器的影像類型。
BillingAccountId<sup>1</sup> | 全部 | 根計費帳戶的唯一識別碼。
BillingAccountName | 全部 | 計費帳戶的名稱。
BillingCurrency | 全部 | 與計費帳戶相關聯的貨幣。
BillingPeriod | EA、PAYG | 費用的計費週期。
BillingPeriodEndDate | 全部 | 計費週期的結束日期。
BillingPeriodStartDate | 全部 | 計費週期的開始日期。
BillingProfileId<sup>1</sup> | 全部 | EA 註冊、PAYG 訂用帳戶、MCA 帳單設定檔或 AWS 合併帳戶的唯一識別碼。
BillingProfileName | 全部 | EA 註冊、PAYG 訂用帳戶、MCA 帳單設定檔或 AWS 合併帳戶的名稱。
ChargeType | 全部 | 指出費用是否代表使用量 (**使用量**)、購買 (**購買**) 或退費 (**退費**)。
ConsumedService | 全部 | 與費用相關聯的服務名稱。
CostCenter<sup>1</sup> | EA、MCA | 針對訂用帳戶所定義來追蹤成本的成本中心 (僅適用於 MCA 帳戶的開放計費週期)。
成本 | EA、PAYG | 請參閱 CostInBillingCurrency。
CostInBillingCurrency | MCA | 在信用額度或稅金前的費用成本 (以計費貨幣計價)。
CostInPricingCurrency | MCA | 在信用額度或稅金前的費用成本 (以定價貨幣計價)。
貨幣 | EA、PAYG | 請參閱 BillingCurrency。
Date<sup>1</sup> | 全部 | 費用的使用量或購買日期。
EffectivePrice | 全部 | 期間的混合單位價格。 混合價格會平均任何單價波動，例如累進階層處理，這可在數量隨時間增加時降低價格。
ExchangeRateDate | MCA | 匯率的建立日期。
ExchangeRatePricingToBilling | MCA | 用來將以定價貨幣計價的成本轉換成計費貨幣的匯率。
頻率 | 全部 | 指出是否預計會重複收費。 費用可以發生一次 (**OneTime**)、每月或每年重複 (**Recurring**)，或以使用量為基礎 (**UsageBased**)。
InvoiceId | PAYG、MCA | 發票 PDF 中所列出的唯一文件識別碼。
InvoiceSection | MCA | 請參閱 InvoiceSectionName。
InvoiceSectionId<sup>1</sup> | EA、MCA | EA 部門或 MCA 發票區段的唯一識別碼。
InvoiceSectionName | EA、MCA | EA 部門或 MCA 發票區段的名稱。
IsAzureCreditEligible | 全部 | 指出費用是否有資格使用 Azure 點數來支付 (值：True、False)。
Location | MCA | 正在執行資源的資料中心位置。
MeterCategory | 全部 | 計量的分類類別名稱。 例如，「雲端服務」和「網路」。
MeterId<sup>1</sup> | 全部 | 計量的唯一識別碼。
MeterName | 全部 | 計量的名稱。
MeterRegion | 全部 | 根據位置定價之服務的資料中心位置名稱。 請參閱 Location。
MeterSubCategory | 全部 | 計量的子分類類別名稱。
OfferId<sup>1</sup> | 全部 | 所購買供應項目的名稱。
PayGPrice | 全部 | 資源的零售價格。
PartNumber<sup>1</sup> | EA、PAYG | 用來取得特定計量定價的識別碼。
PlanName | EA、PAYG | Marketplace 方案名稱。
PreviousInvoiceId | MCA | 如果此明細項目為退費，則為原始發票的參考。
PricingCurrency | MCA | 根據協商價格額定時所使用的貨幣。
PricingModel | 全部 | 指出計量如何定價的識別碼。 (值：隨選、保留、位置)
Products | 全部 | 產品的名稱。
ProductId<sup>1</sup> | MCA | 產品的唯一識別碼。
ProductOrderId | 全部 | 產品訂單的唯一識別碼。
ProductOrderName | 全部 | 產品訂單的唯一名稱。
PublisherName | 全部 | Marketplace 服務的發行者。
PublisherType | 全部 | 發行者的類型 (值：**Azure**、**AWS**、**Marketplace**)。
數量 | 全部 | 所購買或取用的單位數。
ReservationId | EA、MCA | 所購買保留執行個體的唯一識別碼。
ReservationName | EA、MCA | 所購買保留執行個體的名稱。
ResourceGroup | 全部 | 資源所在[資源群組](../../azure-resource-manager/management/overview.md)的名稱。 並非所有費用都來自於部署至資源群組的資源。 沒有資源群組的費用會顯示為 Null/空白、[其他] 或 [不適用]。
ResourceId<sup>1</sup> | 全部 | [AzureResource Manager](/rest/api/resources/resources) 資源的唯一識別碼。
ResourceLocation | 全部 | 正在執行資源的資料中心位置。 請參閱 Location。
ResourceName | EA、PAYG | 資源名稱。 並非所有費用都來自於已部署的資源。 沒有資源類型的費用會顯示為 Null/空白、[其他] 或 [不適用]。
ResourceType | MCA | 資源執行個體的類型。 並非所有費用都來自於已部署的資源。 沒有資源類型的費用會顯示為 Null/空白、[其他] 或 [不適用]。
ServiceFamily | MCA | 服務所屬的服務系列。
ServiceInfo1 | 全部 | 服務專屬的中繼資料。
ServiceInfo2 | 全部 | 含有選擇性服務特定中繼資料的舊版欄位。
ServicePeriodEndDate | MCA | 費率期間的結束日期，已定義和鎖定所取用或所購買服務的定價。
ServicePeriodStartDate | MCA | 費率期間的開始日期，已定義和鎖定所取用或所購買服務的定價。
SubscriptionId<sup>1</sup> | 全部 | Azure 訂用帳戶的唯一識別碼。
SubscriptionName | 全部 | Azure 訂用帳戶的名稱。
Tags<sup>1</sup> | 全部 | 指派給資源的標記。 不包含資源群組標記。 可用來分組或分配內部退款的成本。 如需詳細資訊，請參閱[使用標記組織您的 Azure 資源](https://azure.microsoft.com/updates/organize-your-azure-resources-with-tags/)。
詞彙 | 全部 | 顯示供應項目有效性的詞彙。 例如：在保留執行個體的情況下，它會顯示 12 個月作為期限。 若為一次性購買或週期性購買，期限為 1 個月 (SaaS、Marketplace 支援)。 這不適用於 Azure 使用量。
UnitOfMeasure | 全部 | 服務的計費測量單位。 例如，計算服務會依小時計費。
UnitPrice | EA、PAYG | 費用的每單位價格。

_<sup>**1**</sup> 用於針對單一成本記錄建立唯一識別碼的欄位。_

請注意，有些欄位在不同帳戶類型之間可能有不同的大小寫和間距。
舊版隨用隨付使用量檔案有適用於對帳單和每日使用量的個別區段。

### <a name="list-of-terms-from-older-apis"></a>舊版 API 的詞彙清單
下表將舊版 API 中使用的詞彙對應到新的詞彙。 請參閱上表中的說明。

舊詞彙 | 新詞彙
--- | ---
ConsumedQuantity | 數量
IncludedQuantity | N/A
InstanceId | ResourceId
費率 | EffectivePrice
單位 | UnitOfMeasure
UsageDate | Date
UsageEnd | Date
UsageStart | Date


## <a name="ensure-charges-are-correct"></a>確保費用正確

若要深入了解詳細的使用量和費用，請閱讀如何了解您的[隨用隨付](review-individual-bill.md)或 [Microsoft 客戶合約](review-customer-agreement-bill.md)發票。

## <a name="need-help-contact-us"></a>需要協助嗎？ 與我們連絡。

如果您有問題或需要協助，請[建立支援要求](https://go.microsoft.com/fwlink/?linkid=2083458)。

## <a name="next-steps"></a>後續步驟

- [檢視及下載您的 Microsoft Azure 發票](download-azure-invoice.md)
- [檢視及下載您的 Microsoft Azure 使用量和費用](download-azure-daily-usage.md)