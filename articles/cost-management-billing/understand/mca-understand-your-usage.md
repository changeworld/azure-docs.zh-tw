---
title: Microsoft 客戶合約 Azure 使用量和費用檔案術語
description: 了解如何閱讀並看懂帳單設定檔的 Azure 使用量和費用 CSV 區段。
author: bandersmsft
manager: amberb
tags: billing
ms.service: cost-management-billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/10/2020
ms.author: banders
ms.openlocfilehash: 009a69baa68ed4a826d6a78c0df5d19d34bff006
ms.sourcegitcommit: f718b98dfe37fc6599d3a2de3d70c168e29d5156
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/11/2020
ms.locfileid: "77134589"
---
# <a name="terms-in-the-azure-usage-and-charges-file-for-a-microsoft-customer-agreement"></a>Microsoft 客戶合約的 Azure 使用量和費用檔案術語

本文適用於 Microsoft 客戶合約的計費帳戶。 請[確認您是否有 Microsoft 客戶合約的存取權](#check-access-to-a-microsoft-customer-agreement)。

Azure 使用量和費用 CSV 檔案會包含目前計費週期的每日及計量層級使用量費用。

若要取得 Azure 使用量和費用檔案，請參閱[檢視和下載 Microsoft 客戶合約的 Azure 使用量和費用](download-azure-daily-usage.md)。 此檔案以逗號分隔值 (.csv) 檔案格式提供，您可以使用試算表應用程式開啟這個檔案。

使用量費用是訂用帳戶的**每月**總費用。 使用量費用不會將任何信用額度或折扣列入考慮。

## <a name="changes-from-azure-ea-usage-and-charges"></a>來自 Azure EA 使用量和費用的變更

如果您以前是 EA 客戶，您會注意到 Azure 帳單設定檔使用量 CSV 檔案中的術語與 Azure EA 使用量 CSV 檔案中的術語不同。 以下是 EA 使用量術語與帳單設定檔使用量術語的對應：

| Azure EA 使用量 CSV | Microsoft 客戶合約 Azure 使用量和費用 CSV |
| --- | --- |
| Date | date |
| Month| date |
| Day | date |
| Year | date |
| Products | product |
| 計量識別碼 | meterID |
| MeterCategory | meterCategory |
| MeterSubCategory | meterSubCategory |
| MeterRegion | meterRegion |
| MeterName | meterName |
| ConsumedQuantity | quantity |
| ResourceRate | effectivePrice |
| ExtendedCost | cost |
| ResourceLocation | resourceLocation |
| ConsumedService | consumedService |
| InstanceId | instanceId |
| ServiceInfo1 | serviceInfo1 |
| ServiceInfo2 | serviceInfo2 |
| AdditionalInfo | additionalInfo |
| Tags | tags |
| StoreServiceIdentifier | N/A |
| DepartmentName | invoiceSection |
| CostCenter | costCenter |
| UnitOfMeasure | unitofMeasure |
| ResourceGroup | resourceGroup |
| ChargesBilledSeparately | isAzureCreditEligible |

## <a name="detailed-terms-and-descriptions"></a>詳細術語和說明

下列術語會顯示在 Azure 使用量和費用檔案中。

詞彙 | 描述
--- | ---
invoiceId | 發票 PDF 中所列出的唯一文件識別碼
previousInvoiceId | 如果此明細項目為退款，則為原始發票的參考
billingAccountName | 計費帳戶的名稱
billingAccountId | 根計費帳戶的唯一識別碼
billingProfileId | 產生發票費用的帳單設定檔名稱
billingProfileName | 產生發票費用的帳單設定檔唯一識別碼
invoiceSectionId | 發票區段的唯一識別碼
invoiceSectionName | 發票區段的名稱
costCenter | 在訂用帳戶上所定義來追蹤成本的成本中心 (僅適用於開放計費週期)
billingPeriodStartDate | 所產生發票適用計費週期的開始日期
billingPeriodEndDate | 所產生發票適用計費週期的結束日期
servicePeriodStartDate | 費率期間的開始日期，已定義和鎖定所取用或所購買服務的定價
servicePeriodEndDate | 費率期間的結束日期，已定義和鎖定所取用或所購買服務的定價
date | 針對以 Azure 和 Marketplace 使用量為基礎的費用，這是費率日期。 針對一次性購買 (保留、Marketplace) 或固定的週期性費用 (支援供應項目)，這是購買日期。
serviceFamily | 服務所屬的服務系列
productOrderId | 產品訂單的唯一識別碼
productOrderName | 產品訂單的唯一名稱
consumedService | 所取用服務的名稱
meterId | 計量的唯一識別碼
meterName | 計量的名稱
meterCategory | 計量的分類類別名稱。 例如，雲端服務  、網路  等等。
meterSubCategory | 計量的子分類類別名稱
meterRegion | 可使用服務計量的區域名稱。 針對根據資料中心位置定價的某些服務，識別資料中心的位置。
供應項目 | 所購買供應項目的名稱
productId | 產生費用產品的唯一識別碼
product | 產生費用產品的名稱
訂用帳戶識別碼 | 產生費用訂用帳戶的唯一識別碼
subscriptionName | 產生費用訂用帳戶的名稱
reservationId | 所購買保留執行個體的唯一識別碼
reservationName | 所購買保留執行個體的名稱
publisherType | 發行者的類型 (值：firstParty、thirdPartyReseller、thirdPartyAgency)
publisherName | Marketplace 服務的發行者
resourceGroupId | 資源所關聯資源群組的唯一識別碼
resourceGroupName | 資源所關聯資源群組的名稱
resourceId | 資源執行個體的唯一識別碼
resourceType | 資源執行個體的類型
resourceLocation | 識別正在執行資源的資料中心所在位置。
location | 如果針對相同區域設定了不同的資源位置，則為資源的正規化位置
quantity | 所購買或取用的單位數
unitOfMeasure | 服務的計費測量單位。 例如，計算服務會依小時計費。
chargeType | 費用的類型。 值： <ul><li>AsCharged-Usage：根據 Azure 服務使用量而產生的費用。 這包括因保留執行個體而不計費的 VM 使用量。</li><li>AsCharged-PurchaseMarketplace：來自 Marketplace 購買的一次性費用或固定週期性費用</li><li>AsCharged-UsageMarketplace：根據取用單位數而計費的 Marketplace 服務費用</li></ul>
isAzureCreditEligible | 此旗標會指出服務費用是否有資格使用 Azure 點數來支付 (值：True、False)
serviceInfo1 | 服務專屬的中繼資料
serviceInfo2 | 舊版欄位，可擷取選擇性的服務特定中繼資料
additionalInfo | 其他的服務專屬中繼資料。
tags | 您指派給資源的標記

### <a name="make-sure-that-charges-are-correct"></a>請確定費用正確

如果您想要確定詳細使用量檔案中的費用是否正確，您可以進行驗證。 請參閱[了解帳單設定檔發票上的費用](review-customer-agreement-bill.md)

## <a name="check-access-to-a-microsoft-customer-agreement"></a>檢查 Microsoft 客戶合約的存取
[!INCLUDE [billing-check-mca](../../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>需要協助嗎？ 與我們連絡。

如果您有問題或需要協助，請[建立支援要求](https://go.microsoft.com/fwlink/?linkid=2083458)。

## <a name="next-steps"></a>後續步驟

- [檢視及下載您的 Microsoft Azure 發票](download-azure-invoice.md)
- [檢視及下載您的 Microsoft Azure 使用量和費用](download-azure-daily-usage.md)
