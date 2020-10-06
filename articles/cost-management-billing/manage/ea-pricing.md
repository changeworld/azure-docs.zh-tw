---
title: 檢視及下載組織的 Azure 定價
description: 了解如何檢視和下載定價，或利用組織的定價來估計成本。
author: bandersmsft
ms.reviewer: amberb
tags: billing
ms.service: cost-management-billing
ms.subservice: enterprise
ms.topic: conceptual
ms.date: 08/20/2020
ms.author: banders
ms.custom: seodec18
ms.openlocfilehash: 63e648681a63daafe1eaeb1d2e8e5a6cd607aa70
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91446328"
---
# <a name="view-and-download-your-organizations-azure-pricing"></a>檢視及下載組織的 Azure 定價

具有 Azure Enterprise 合約 (EA)、Microsoft 客戶合約 (MCA) 或 Microsoft 合作夥伴合約 (MPA) 的 Azure 客戶可在 Azure 入口網站中檢視及下載其定價。 [了解如何檢查您的計費帳戶類型](#check-your-billing-account-type)。

## <a name="download-pricing-for-an-enterprise-agreement"></a>下載 Enterprise 合約的訂價

根據企業系統管理員為組織所設定的原則，只有特定的系統管理角色可以提供組織 EA 價格資訊的存取權。 如需詳細資訊，請參閱[了解 Azure 中的 Azure Enterprise 合約系統管理角色](understand-ea-roles.md)。

1. 以企業系統管理員身分登入 [Azure 入口網站](https://portal.azure.com/)。
1. 搜尋 [成本管理 + 帳單]  。

   ![顯示 Azure 入口網站搜尋的螢幕擷取畫面。](./media/ea-pricing/portal-cm-billing-search.png)

1. 在帳單帳戶下方，選取 [使用方式 + 費用]  。

   ![在帳單下方顯示使用方式和費用的螢幕擷取畫面](./media/ea-pricing/ea-pricing-usage-charges-nav.png)

1. 選取![下載圖示。](./media/ea-pricing/download-icon.png) 針對月份選取 [下載]。

1. 在 [價位表]**** 下方，選取 [下載 csv]****。

   ![顯示 [下載使用量] + [費用] 選項的螢幕擷取畫面。](./media/ea-pricing/download-ea-price-sheet.png)

## <a name="download-pricing-for-an-mca-or-mpa-account"></a>下載 MCA 或 MPA 帳戶的定價

如果您有 MCA，您必須是帳單設定檔擁有者、參與者、讀者或發票管理員，才能檢視及下載定價資訊。 如果您有 MPA，您必須擁有夥伴組織中的全域管理員和管理員代理人角色，才能檢視和下載定價。

### <a name="download-price-sheets-for-billed-charges"></a>下載計費費用的價位表

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 搜尋 [成本管理 + 帳單]  。
1. 選取帳單設定檔。 視存取權之不同，您可能必須先選取計費帳戶。
1. 選取 [發票]****。
1. 在發票方格中，尋找與您要下載的價位表相對應的發票資料列。
1. 按一下資料列結尾處的省略符號 (`...`)。
![顯示已選取省略符號的螢幕擷取畫面](./media/ea-pricing/billingprofile-invoicegrid-new.png)

1. 如果您想要查看所選發票中各項服務的價格，請選取 [發票價位表]****。
1. 如果您想要查看所有 Azure 服務在指定計費期間內的價格，請選取 [Azure 價位表]****。

![顯示操作功能表中包含價位表的螢幕擷取畫面](./media/ea-pricing/contextmenu-pricesheet01.png)

### <a name="download-price-sheets-for-the-current-billing-period"></a>下載目前計費期間的價位表

如果您有 MCA，即可下載目前計費週期的定價。

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 搜尋 [成本管理 + 帳單]  。
1. 選取帳單設定檔。 視存取權之不同，您可能必須先選取計費帳戶。
1. 在 [概觀]**** 區域中，尋找位於當月費用下方的下載連結。
1. 選取 [Azure 價位表]****。
![顯示從概觀下載的螢幕擷取畫面](./media/ea-pricing/open-pricing01.png)

## <a name="estimate-costs-with-the-azure-pricing-calculator"></a>使用 Azure 定價計算機來預估成本

您也可以利用 Azure 定價計算機，使用組織的定價來估計成本。

1. 移至 [Azure 價格計算機](https://azure.microsoft.com/pricing/calculator)。
1. 在右上方，選取 [登入]****。
1. 在 [程式和供應項目] > [授權方案] 下方，選取 [Enterprise 合約 (EA)]。
1. 在 [程式和供應項目] > [選取的合約] 下方，選取 [未選取任何項目]。

    ![顯示可用程式和供應項目的螢幕擷取畫面。](./media/ea-pricing/ea-pricing-calculator-estimate.png)

1. 選擇組織。
1. 選取 [套用]。
1. 搜尋產品並新增至您的預估。
1. 顯示的預估價格取決於您針對組織所選取的價格。

## <a name="check-your-billing-account-type"></a>檢查您的計費帳戶類型
[!INCLUDE [billing-check-account-type](../../../includes/billing-check-account-type.md)]

## <a name="next-steps"></a>後續步驟

如果您是 EA 客戶，請參閱：

- [管理對 Azure EA 帳單資訊的存取](manage-billing-access.md)
- [檢視及下載您的 Microsoft Azure 發票](../understand/download-azure-invoice.md)
- [檢視及下載您的 Microsoft Azure 使用量和費用](../understand/download-azure-daily-usage.md)
- [了解 Enterprise 合約客戶的帳單](../understand/review-enterprise-agreement-bill.md)

如果您有 Microsoft 客戶合約，請參閱：

- [了解您 Microsoft 客戶合約價位表中的詞彙](mca-understand-pricesheet.md)
- [檢視及下載您的 Microsoft Azure 發票](../understand/download-azure-invoice.md)
- [檢視及下載您的 Microsoft Azure 使用量和費用](../understand/download-azure-daily-usage.md)
- [檢視及下載帳單設定檔的稅務文件](../understand/mca-download-tax-document.md)
- [了解帳單設定檔發票上的費用](../understand/review-customer-agreement-bill.md)
