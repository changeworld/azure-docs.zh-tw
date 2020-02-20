---
title: 檢視及下載 Azure 使用量和費用
description: 說明如何下載或檢視您的 Azure 每日使用量和費用資訊。
keywords: 計費使用量, 使用量費用, 使用量下載, 檢視使用量, azure 發票, azure 使用量
author: bandersmsft
ms.author: banders
tags: billing
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 02/13/2020
ms.openlocfilehash: 156f47e7a5571d37d836e68c7817352626fbd734
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/13/2020
ms.locfileid: "77200243"
---
# <a name="view-and-download-your-azure-usage-and-charges"></a>檢視及下載您的 Azure 使用量和費用

您可以在 Azure 入口網站中下載 Azure 使用量和費用的每日明細。 只有特定角色有權取得 Azure 使用量資訊，例如帳戶管理員或企業系統管理員。 若要深入了解取得計費資訊的存取權，請參閱[使用角色管理 Azure 計費的存取權](../manage/manage-billing-access.md)。

如果您有 Microsoft 客戶合約 (MCA)，您必須是帳單設定檔擁有者、參與者、讀者或發票管理員，才能檢視 Azure 使用量和費用。  如果您有 Microsoft 合作夥伴合約 (MPA)，則只有夥伴組織中的全域管理員或管理員代理人角色才能檢視及下載 Azure 使用量和費用。 [在 Azure 入口網站中檢查您的計費帳戶類型](#check-your-billing-account-type)。

## <a name="download-usage-from-the-azure-portal-csv"></a>從 Azure 入口網站下載使用量 (.csv)

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 搜尋 [成本管理 + 帳單]  。

    ![顯示 Azure 入口網站搜尋的螢幕擷取畫面](./media/download-azure-daily-usage/portal-cm-billing-search.png)

1. 視存取權之不同，您可能必須選取計費帳戶或帳單設定檔。
1. 在左側功能表中，選取 [計費]  之下的 [發票]  。
1. 在發票方格中，尋找對應至所要下載使用量的計費週期資料列。
1. 選取**下載圖示**或右側的省略符號 (`...`)。
1. [下載] 窗格會在右側開啟。 從 [使用量詳細資料]  區段中，選取 [下載]  。

## <a name="download-usage-for-ea-customers"></a>下載 EA 客戶的使用量

若要檢視及下載 EA 客戶的使用量資料，您必須是企業系統管理員、帳戶擁有者或部門系統管理員，並且已啟用檢視費用原則。

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 搜尋 [成本管理 + 帳單]  。

    ![顯示 Azure 入口網站搜尋的螢幕擷取畫面](./media/download-azure-daily-usage/portal-cm-billing-search.png)

1. 選取 [使用量 + 費用]  。
1. 針對您想要下載的月份，選取 [下載]  。

## <a name="download-usage-for-pending-charges"></a>下載待決費用的使用量

如果您有 Microsoft 客戶合約，您可以下載目前計費週期的當月使用量。 這些使用量費用尚未計費。

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 搜尋 [成本管理 + 帳單]  。
3. 選取帳單設定檔。 視存取權之不同，您可能必須先選取計費帳戶。
4. 在 [概觀]  區域中，尋找位於當月費用下方的下載連結。
5. 選取 [Azure 使用量和費用]  。

    ![顯示從概觀下載的螢幕擷取畫面](./media/download-azure-daily-usage/open-usage01.png)

## <a name="check-your-billing-account-type"></a>檢查您的計費帳戶類型
[!INCLUDE [billing-check-account-type](../../../includes/billing-check-account-type.md)]

## <a name="need-help-contact-us"></a>需要協助嗎？ 與我們連絡。

如果您有問題或需要協助，請[建立支援要求](https://go.microsoft.com/fwlink/?linkid=2083458)。

## <a name="next-steps"></a>後續步驟

若要深入了解您的發票和使用量費用，請參閱：

- [了解您 Microsoft Azure 詳細使用量上的字詞](understand-usage.md)
- [了解 Microsoft Azure 帳單](review-individual-bill.md)
- [檢視及下載您的 Microsoft Azure 發票](download-azure-invoice.md)
- [檢視及下載組織的 Azure 定價](../manage/ea-pricing.md)

如果您有 Microsoft 客戶合約，請參閱：

- [了解 Microsoft 客戶合約 Azure 詳細使用量的詞彙](mca-understand-your-usage.md)
- [了解 Microsoft 客戶合約發票上的費用](review-customer-agreement-bill.md)
- [檢視及下載您的 Microsoft Azure 發票](download-azure-invoice.md)
- [檢視及下載 Microsoft 客戶合約的稅務文件](mca-download-tax-document.md)
- [檢視及下載組織的 Azure 定價](../manage/ea-pricing.md)
