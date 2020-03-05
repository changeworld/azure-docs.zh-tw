---
title: 檢閱 Microsoft 合作夥伴合約發票 - Azure
description: 了解如何檢閱您的帳單和資源使用量，以及確認您的 Microsoft 合作夥伴合約發票費用。
author: bandersmsft
ms.reviewer: judupont
tags: billing
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 02/13/2020
ms.author: banders
ms.openlocfilehash: 61d47c2f308555265ccabad4d7456026ee9a639c
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/05/2020
ms.locfileid: "78299307"
---
# <a name="tutorial-review-your-microsoft-partner-agreement-invoice"></a>教學課程：檢閱 Microsoft 合作夥伴合約發票

 在 Microsoft 合作夥伴合約的計費帳戶中，每個帳單設定檔都會按月產生發票。 發票中包含上個月的所有客戶費用。 您可以藉由在 Azure 入口網站中分析個別交易來了解發票上的費用。 您也可以在 Azure 入口網站中檢視您的發票，並比較費用與使用量詳細資料檔案。

如需詳細資訊，請參閱[如何從 Azure 入口網站下載發票](download-azure-invoice.md)。

本教學課程僅適用於具有 Microsoft 合作夥伴合約的 Azure 合作夥伴。

在本教學課程中，您會了解如何：

> [!div class="checklist"]
> * 檢閱 Azure 入口網站中已開立發票的交易
> * 檢閱待決費用以預估您的下一張發票
> * 分析您的 Azure 使用量費用

## <a name="prerequisites"></a>Prerequisites

您必須可以存取 Microsoft 合作夥伴合約的計費帳戶。

您訂閱 Azure 的天數必須超過 30 天。 Azure 會在您的發票期間結束時向您收取費用。

## <a name="sign-in-to-azure"></a>登入 Azure

- 登入 Azure 入口網站：[https://portal.azure.com](https://portal.azure.com)。

## <a name="check-access-to-a-microsoft-customer-agreement"></a>檢查 Microsoft 客戶合約的存取

檢查合約類型，以判斷您是否有權存取 Microsoft 合作夥伴合約的計費帳戶。

在 Azure 入口網站的搜尋方塊中輸入 [成本管理 + 計費]  ，然後選取 [成本管理 + 計費]  。

![顯示 Azure 入口網站搜尋的螢幕擷取畫面](./media/review-partner-agreement-bill/billing-search-cost-management-billing.png)

如果您只能存取一個計費範圍，請選取左側的 [屬性]  。 如果計費帳戶類型為 [Microsoft 合作夥伴合約]  ，您可以存取 Microsoft 合作夥伴合約的計費帳戶。

![在 [屬性] 頁面中顯示 Microsoft 合作夥伴合約的螢幕擷取畫面](./media/review-partner-agreement-bill/billing-account-properties-partner-agreement.png)

如果您有多個計費範圍的存取權，請檢查 [計費帳戶] 資料行中的類型。 如果任何範圍的計費帳戶類型為 [Microsoft 合作夥伴合約]  ，您就可以存取 Microsoft 合作夥伴合約的計費帳戶。

![在 [計費帳戶清單] 頁面中顯示 Microsoft 合作夥伴合約的螢幕擷取畫面](./media/review-partner-agreement-bill/mpa-in-the-list.png)

## <a name="review-invoiced-transactions-in-the-azure-portal"></a>檢閱 Azure 入口網站中已開立發票的交易

在 [成本管理 + 計費] 中，選取頁面左側的 [所有交易]  。 根據您的存取權，您可能必須選取計費帳戶、帳單設定檔或客戶，然後選取 [所有交易]  。

[所有交易] 頁面會顯示下列資訊：

![顯示計費交易清單的螢幕擷取畫面](./media/review-partner-agreement-bill/all-transactions.png)

|資料行  |定義  |
|---------|---------|
|Date     | 交易日期  |
|發票識別碼     | 交易計費所在發票的識別碼。 如果您提交支援要求，請將識別碼提供給 Azure 支援，以加速支援要求的處理 |
|交易類型     |  交易的類型，例如購買、取消和使用量費用  |
|產品系列     | 產品的類別，例如 Azure SQL Database 的虛擬機器或資料庫的計算|
|產品 SDKU     | 識別產品執行個體的唯一代碼 |
|Amount     |  交易的金額      |
|帳單設定檔     | 交易會顯示在此帳單設定檔的發票上 |

搜尋發票識別碼以篩選發票的交易。

## <a name="review-pending-charges-to-estimate-your-next-invoice"></a>檢閱待決費用以預估您的下一張發票

在開立發票之前，系統會預估費用並將其視為擱置。 您可以在 Azure 入口網站中檢視 Microsoft 合作夥伴合約帳單設定檔的擱置費用，以預估下一張發票。 待決費用會受到預估，且不包含稅金。 下一張發票上的實際費用會與待決費用不同。

### <a name="view-pending-transactions"></a>檢視擱置交易

當您識別出待決費用時，您可以藉由分析在費用中有佔比的個別交易來了解費用。 此時，待決的使用量費用不會顯示在 [所有交易] 頁面上。 您可以在 Azure 訂用帳戶頁面上檢視待決的使用量費用。

在 [成本管理 + 計費] 中，選取帳單設定檔。 視存取權之不同，您可能必須選取計費帳戶。 從計費帳戶中選取 [帳單設定檔]  ，然後選取帳單設定檔。

從頁面左側選取 [所有交易]  。

搜尋「擱置」  。 使用 [時間範圍]  篩選器來檢視當月或上個月的待決費用。

<!-- ![Screenshot that shows the pending transactions list](./media/billing-mpa-understand-your-bill/mpa-billing-profile-pending-transactions.png) -->

### <a name="view-pending-charges-by-customer"></a>檢視各客戶的擱置費用

在 [成本管理 + 計費] 中，選取帳單設定檔。 視存取權之不同，您可能必須選取計費帳戶。 從計費帳戶中選取 [帳單設定檔]  ，然後選取帳單設定檔。

選取頁面左側的 [客戶]  。

<!-- ![screenshot of billing profile customers list](./media/billing-mpa-understand-your-bill/mpa-billing-profile-customers.png) -->

[客戶] 頁面會針對與帳單設定檔相關聯的每個客戶，顯示當月和上個月的費用。 當月費用是當月的待決費用，會在當月的發票產生時計費。 如果上個月的發票尚未產生，則上個月的費用也會列為待決。

### <a name="view-pending-usage-charges"></a>檢視待決的使用量費用

在 [成本管理 + 計費] 中，選取帳單設定檔。 視存取權之不同，您可能必須選取計費帳戶。 從計費帳戶中選取 [帳單設定檔]  ，然後選取帳單設定檔。

選取頁面左側的 [Azure 訂用帳戶]  。 [Azure 訂用帳戶] 頁面會顯示帳單設定檔中每個訂用帳戶當月和上個月的費用。 當月費用是當月的待決費用，會在當月的發票產生時計費。 如果上個月的發票尚未產生，則上個月的費用也會列為待決。

<!--     ![Screenshot that shows the Azure subscriptions list for MPA billing profile](./media/billing-mpa-understand-your-bill/mpa-billing-profile-subscriptions-list.png) -->

## <a name="analyze-your-azure-usage-charges"></a>分析您的 Azure 使用量費用

您可以使用 Azure 使用量和費用 CSV 檔案，分析您以使用量為基礎的費用。 您可以篩選 Azure 使用量和費用檔案，以調節發票 pdf 中所列的每項產品的使用量費用。 若要檢視特定產品的詳細使用費用，請將 Azure 使用量和費用 CSV 檔案中的 **product**  資料行篩選為只包含該產品的名稱。

您也可以篩選 Azure 使用量和費用 CSV 檔案中的 **customerName**  資料行，以查看每個客戶的每日使用費用。 如果您想要依 Azure 訂用帳戶檢視每日使用量費用，請篩選 **subscriptionName**  資料行。

## <a name="pay-your-bill"></a>支付您的帳單

支付帳單的指示會顯示在發票底部。 您可以在發票日期的 60 天內經由電匯或支票付費。

如果您已支付帳單，可以在 Azure 入口網站的 [發票] 頁面上查看付款的狀態。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何：

> [!div class="checklist"]
> * 檢閱 Azure 入口網站中已開立發票的交易
> * 檢閱待決費用以預估您的下一張發票
> * 分析您的 Azure 使用量費用

了解如何使用適用於合作夥伴的 Azure 成本管理。

> [!div class="nextstepaction"]
> [適用於合作夥伴的 Azure 成本管理入門](../costs/get-started-partners.md)
