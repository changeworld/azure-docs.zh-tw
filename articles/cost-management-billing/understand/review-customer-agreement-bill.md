---
title: 檢閱 Microsoft 客戶合約的帳單 - Azure
description: 了解如何檢閱您的帳單和資源使用量，以及確認您的 Microsoft 客戶合約發票費用。
author: bandersmsft
manager: jureid
tags: billing
ms.service: cost-management-billing
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/22/2019
ms.author: banders
ms.openlocfilehash: 3cbc4ab4f0e2ad18c7d1d430ed5501e23b5f5f4d
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/31/2020
ms.locfileid: "75983723"
---
# <a name="tutorial-review-your-microsoft-customer-agreement-invoice"></a>教學課程：檢閱 Microsoft 客戶合約發票

您可以藉由分析個別交易來檢閱發票上的費用。 在 Microsoft 客戶合約的計費帳戶中，每個帳單設定檔都會按月產生發票。 發票中包含上個月的所有費用。 您可以在 Azure 入口網站中檢視您的發票，並比較費用與使用量詳細資料檔案。

本教學課程僅適用於具有 Microsoft 客戶合約的 Azure 客戶。

在本教學課程中，您會了解如何：

> [!div class="checklist"]
> * 檢閱 Azure 入口網站中已開立發票的交易
> * 檢閱待決費用以預估您的下一張發票
> * 分析您的 Azure 使用量費用

## <a name="prerequisites"></a>Prerequisites

您必須有 Microsoft 客戶合約的計費帳戶。

您必須有 Microsoft 客戶合約的存取權。 您必須是帳單設定檔擁有者、參與者、讀者或發票管理員，才能檢視計費和使用量資訊。 若要深入了解 Microsoft 客戶合約的計費角色，請參閱[帳單設定檔角色和工作](../manage/understand-mca-roles.md#billing-profile-roles-and-tasks)。

您訂閱 Azure 的天數必須超過 30 天。 Azure 會在您的發票期間結束時向您收取費用。

## <a name="sign-in-to-azure"></a>登入 Azure

- 在 https://portal.azure.com 登入 Azure 入口網站。

## <a name="check-access-to-a-microsoft-customer-agreement"></a>檢查 Microsoft 客戶合約的存取

檢查合約類型，以判斷您是否有權存取 Microsoft 客戶合約的計費帳戶。

在 Azure 入口網站的搜尋方塊中輸入 [成本管理 + 計費]  ，然後選取 [成本管理 + 計費]  。

![顯示在 Azure 入口網站中搜尋 [成本管理 + 帳單] 的螢幕擷取畫面](./media/review-customer-agreement-bill/billing-search-cost-management-billing.png)

如果您只能存取一個計費範圍，請選取左側的 [屬性]  。 如果帳單帳戶類型為 [Microsoft 客戶合約]  ，您可以存取 Microsoft 客戶合約的計費帳戶。

![在 [屬性] 頁面中顯示 Microsoft 客戶合約的螢幕擷取畫面](./media/review-customer-agreement-bill/billing-mca-property.png)

如果您有多個計費範圍的存取權，請檢查 [計費帳戶] 資料行中的類型。 如果任何範圍的計費帳戶類型為 [Microsoft 客戶合約]  ，您就可以存取 Microsoft 客戶合約的計費帳戶。

![在 [計費帳戶清單] 頁面中顯示 Microsoft 客戶合約的螢幕擷取畫面](./media/review-customer-agreement-bill/billing-mca-in-the-list.png)

## <a name="review-invoiced-transactions-in-the-azure-portal"></a>檢閱 Azure 入口網站中已開立發票的交易

在 Azure 入口網站中，從頁面左側選取 [所有交易]  。 根據您的存取權，您可能必須選取計費帳戶、帳單設定檔或發票區段，然後選取 [所有交易]  。

[所有交易] 頁面會顯示下列資訊：

![顯示計費交易清單的螢幕擷取畫面](./media/review-customer-agreement-bill/mca-billed-transactions-list.png)

|資料行  |定義  |
|---------|---------|
|Date     | 交易日期  |
|發票識別碼     | 交易計費所在發票的識別碼。 如果您提交支援要求，請將識別碼提供給 Azure 支援，以加速支援要求的處理 |
|交易類型     |  交易的類型，例如購買、取消和使用量費用  |
|產品系列     | 產品的類別，例如 Azure SQL Database 的虛擬機器或資料庫的計算|
|產品 SKU     | 識別產品執行個體的唯一代碼 |
|Amount     |  交易的金額      |
|發票區段     | 交易會顯示在帳單設定檔的這個發票區段上 |
|帳單設定檔     | 交易會顯示在此帳單設定檔的發票上 |

搜尋發票識別碼以篩選發票的交易。

### <a name="view-transactions-by-invoice-sections"></a>依發票區段檢視交易

發票區段可協助您彙整帳單設定檔的發票成本。 如需詳細資訊， 發票產生時，帳單設定檔中所有區段的費用都會顯示在發票上。

下圖顯示範例發票上的會計部門發票區段的費用。

![依發票區段資訊顯示詳細資料的範例影像](./media/review-customer-agreement-bill/invoicesection-details.png)

當您識別出發票區段的費用時，您可以在 Azure 入口網站中檢視交易以了解費用。

移至 Azure 入口網站中的 [所有交易] 頁面，以檢視發票的交易。

依發票區段名稱篩選以檢視交易。

## <a name="review-pending-charges-to-estimate-your-next-invoice"></a>檢閱待決費用以預估您的下一張發票

在 Microsoft 客戶合約的計費帳戶中，未開立發票的費用都是估計金額，且處於待處理狀態。 您可以在 Azure 入口網站中檢視待決費用，以預估下一張發票。 待決費用會受到預估，且不包含稅金。 下一張發票上的實際費用會與待決費用不同。

### <a name="view-summary-of-pending-charges"></a>檢視待決費用的摘要

登入 [Azure 入口網站](https://portal.azure.com)。

選取帳單設定檔。 視存取權之不同，您可能必須選取計費帳戶。 從計費帳戶中選取 [帳單設定檔]  ，然後選取帳單設定檔。

從畫面頂端選取 [摘要]  索引標籤。

費用區段會顯示當月費用和上個月的費用。

![顯示在 Azure 入口網站中搜尋 [成本管理 + 帳單] 的螢幕擷取畫面](./media/review-customer-agreement-bill/mca-billing-profile-summary.png)

當月費用是當月的待決費用，會在當月的發票產生時計費。 如果上個月的發票尚未產生，則上個月的費用也會列為待決，且會顯示在下一張發票上。

### <a name="view-pending-transactions"></a>檢視擱置交易

當您識別出待決費用時，您可以藉由分析在費用中有佔比的個別交易來了解費用。 此時，待決的使用量費用不會顯示在 [所有交易] 頁面上。 您可以在 Azure 訂用帳戶頁面上檢視待決的使用量費用。

登入 [Azure 入口網站](https://portal.azure.com)。

在 Azure 入口網站的搜尋方塊中輸入 [成本管理 + 計費]  ，然後選取 [成本管理 + 計費]  。

選取帳單設定檔。 視存取權之不同，您可能必須選取計費帳戶。 從計費帳戶中選取 [帳單設定檔]  ，然後選取帳單設定檔。

從頁面左側選取 [所有交易]  。

搜尋「擱置」  。 使用 [時間範圍]  篩選器來檢視當月或上個月的待決費用。

![顯示擱置交易清單的螢幕擷取畫面](./media/review-customer-agreement-bill/mca-pending-transactions-list.png)

### <a name="view-pending-usage-charges"></a>檢視待決的使用量費用

登入 [Azure 入口網站](https://portal.azure.com)。

在 Azure 入口網站的搜尋方塊中輸入 [成本管理 + 計費]  ，然後選取 [成本管理 + 計費]  。

選取帳單設定檔。 視存取權之不同，您可能必須選取計費帳戶。 從計費帳戶中選取 [帳單設定檔]  ，然後選取帳單設定檔。

選取頁面左側的 [所有訂用帳戶]  。

[Azure 訂用帳戶] 頁面會顯示帳單設定檔中每個訂用帳戶當月和上個月的費用。 當月費用是當月的待決費用，會在當月的發票產生時計費。 如果上個月的發票尚未產生，則上個月的費用也會列為待決。

![顯示帳單設定檔的 Azure 訂用帳戶清單的螢幕擷取畫面](./media/review-customer-agreement-bill/mca-billing-profile-subscriptions-list.png)

## <a name="analyze-your-azure-usage-charges"></a>分析您的 Azure 使用量費用

您可以使用 Azure 使用量和費用 CSV 檔案，分析您以使用量為基礎的費用。 您可以下載發票或待決費用的檔案。

### <a name="download-your-invoice-and-usage-details"></a>下載您的發票和使用量詳細資料

根據您的存取權，您可能必須在 [成本管理 + 計費] 中選取計費帳戶或帳單設定檔。 在左側功能表中，選取 [計費]  之下的 [發票]  。 在發票方格中，尋找您要下載之發票的資料列。 按一下下載圖示或資料列結尾處的省略符號 (...)。 從 [下載]  方塊中下載使用量詳細資料檔案和發票。

### <a name="view-detailed-usage-by-invoice-section"></a>依發票區段檢視詳細使用量

您可以篩選 Azure 使用量和費用檔案，以協調發票區段的使用量費用。

下列資訊會逐步引導您協調會計部門發票區段的計算費用：

![依發票區段資訊顯示詳細資料的範例影像](./media/review-customer-agreement-bill/invoicesection-details.png)

| 發票 PDF | Azure 使用量和費用 CSV |
| --- | --- |
|會計部門 |invoiceSectionName |
|使用量費用 - Microsoft Azure 方案 |productOrderName |
|計算 |serviceFamily |

將 CSV 檔案中的 **invoiceSectionName** 資料行篩選到 [會計部門]  。然後，將 CSV 檔案中的 **productOrderName** 資料行篩選到 [Microsoft Azure 方案]  。 接下來，將 CSV 檔案中的 **serviceFamily** 資料行篩選到 [Microsoft.Compute]  。

![顯示依發票區段篩選的使用量和費用檔案的螢幕擷取畫面](./media/review-customer-agreement-bill/billing-usage-file-filtered-by-invoice-section.png)

### <a name="view-detailed-usage-by-subscription"></a>依訂用帳戶檢視詳細使用量

您可以篩選 Azure 使用量和費用 CSV 檔案，以協調訂用帳戶的使用量費用。 當您識別出訂用帳戶的費用時，請使用 Azure 使用量和費用 CSV 檔案來分析費用。

下圖顯示 Azure 入口網站中的訂用帳戶清單。

![顯示帳單設定檔的 Azure 訂用帳戶清單的螢幕擷取畫面](./media/review-customer-agreement-bill/mca-billing-profile-subscriptions-list-highlighted.png)

將 Azure 使用量和費用 CSV 檔案中的 **subscriptionName** 資料行篩選到 **WA_Subscription**，以檢視 WA_Subscription 的詳細使用量費用。

![顯示依訂用帳戶篩選的使用量和費用檔案的螢幕擷取畫面](./media/review-customer-agreement-bill/billing-usage-file-filtered-by-subscription.png)

## <a name="pay-your-bill"></a>支付您的帳單

支付帳單的指示會顯示在發票底部。 [了解如何支付](mca-understand-your-invoice.md#how-to-pay)。

如果您已支付帳單，可以在 Azure 入口網站的 [發票] 頁面上查看付款的狀態。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何：

> [!div class="checklist"]
> * 檢閱 Azure 入口網站中已開立發票的交易
> * 檢閱待決費用以預估您的下一張發票
> * 分析您的 Azure 使用量費用

完成快速入門以開始使用成本分析。

> [!div class="nextstepaction"]
> [開始分析成本](../costs/quick-acm-cost-analysis.md)
