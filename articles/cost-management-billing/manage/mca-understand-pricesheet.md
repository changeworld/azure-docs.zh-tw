---
title: 您 Microsoft 客戶合約價位表中的詞彙 - Azure
description: 學習如何閱讀並了解 Microsoft 客戶合約的使用量和帳單。
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
ms.openlocfilehash: 46d0f259d30f9864c371f8bd3dd01f593004c808
ms.sourcegitcommit: f718b98dfe37fc6599d3a2de3d70c168e29d5156
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/11/2020
ms.locfileid: "77133576"
---
# <a name="terms-in-your-microsoft-customer-agreement-price-sheet"></a>您 Microsoft 客戶合約價位表中的詞彙

本文適用於 Microsoft 客戶合約的 Azure 計費帳戶。 請[確認您是否有 Microsoft 客戶合約的存取權](#check-access-to-a-microsoft-customer-agreement)。

如果您是帳單設定檔的擁有者、參與者、讀者或發票管理員，則可以從 Azure 入口網站下載組織的價位表。 請參閱[檢視及下載組織的定價](ea-pricing.md)。

## <a name="terms-and-descriptions-in-your-price-sheet"></a>價位表中的詞彙和說明

下一節將說明您的 Microsoft 客戶合約價位表中顯示的重要詞彙。

| **欄位名稱**   | **說明**   |
| --- | --- |
| basePrice  | 客戶登入時的市場價格，或服務計量在登入後啟動時的市場價格。   |
| billingAccountId  | 計費帳戶的唯一識別碼。   |
| billingAccountName  | 計費帳戶的名稱。  |
| billingCurrency | 將費用過帳的貨幣 |
| billingProfileId  | 帳單設定檔的唯一識別碼。   |
| billingProfileName  | 設定用來接收發票的帳單設定檔名稱。 價位表中的價格會與此帳單設定檔相關聯。 |
| 貨幣 | 反映所有價格的貨幣。 |
| discount | 針對分級層、免費層、內含數量或協商折扣所提供的價格折扣 (適用的話)。 以百分比表示。 |
| effectiveEndDate  | 有效價格的結束日期。 |
| effectiveStartDate  | 價格生效的開始日期。 |
| includedQuantity | 客戶有權取用的特定服務數量 (不需累加費用)。 |
| marketPrice | 所指定服務目前的主要市場價格。 |
| meterId  | 計量的唯一識別碼。 |
| meterCategory  | 計量的分類類別名稱。 例如，雲端服務  、網路  等等。 |
| meterName  | 計量的名稱。 計量代表 Azure 服務的可部署資源。 |
| meterSubCategory  | 計量的子分類類別名稱。  |
| meterType  |  計量類型的名稱。 |
| meterRegion  | 可使用服務計量的區域名稱。 針對根據資料中心位置定價的某些服務，識別資料中心的位置。    |
| Products  | 產生費用的產品名稱。例如：基本 SQL DB 與標準 SQL DB  |
| productId  | 已耗用計量之產品的唯一識別碼。 |
| productOrderName  | 購買的產品方案名稱。 |
| serviceFamily  | Azure 服務的類型。例如：計算、分析、安全性 |
| tierMinimumUnits  | 定義已定義價格之階層範圍的下限。 例如，如果範圍是 0 到 100，則 tierMinimumUnits 會是 0。  |
| unitOfMeasure  | 識別服務的計費測量單位。 例如，計算服務會依小時計費。 |
| unitPrice  | 計費時與計量和產品訂單名稱相關的每單位價格 (不是有效混合價格)。  注意:如果服務在各層之間的價格有所差異，則單位價格與使用量詳細資料下載中的有效價格會不相同。  如果服務採用多層式定價，則有效價格會是各層間的混合費率，而不會顯示特定層的單位價格。 混合價格或有效價格是跨多層之取用數量的淨價格 (每一層都有特定的單位價格)。 |


## <a name="check-access-to-a-microsoft-customer-agreement"></a>檢查 Microsoft 客戶合約的存取
[!INCLUDE [billing-check-mca](../../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>需要協助嗎？ 與我們連絡。

如果您有問題或需要協助，請[建立支援要求](https://go.microsoft.com/fwlink/?linkid=2083458)。

## <a name="next-steps"></a>後續步驟

- [檢視及下載組織的定價](ea-pricing.md)
