---
title: 檢視及下載 Azure 發票
description: 說明如何檢視及下載您的 Azure 發票。
keywords: 帳單發票,發票下載,azure 發票,azure 使用量
author: bandersmsft
ms.reviewer: amberb
tags: billing
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 03/27/2020
ms.author: banders
ms.openlocfilehash: 16534343a831f0802a60a9214f567742153360e0
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/01/2020
ms.locfileid: "80479004"
---
# <a name="view-and-download-your-microsoft-azure-invoice"></a>檢視及下載您 Microsoft Azure 發票

您可以在 [Azure 入口網站](https://portal.azure.com/)下載發票，或透過電子郵件來傳送。 如果您是 Azure Enterprise 合約客戶 (EA 客戶)，則無法下載您的組織發票。 相反地，發票會傳送給已設定接收註冊發票的人員。

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="when-invoices-are-generated"></a>會產生發票的狀況

系統會根據您的計費帳戶類型來產生發票。 系統會針對 Microsoft 線上服務方案 (MOSP)、Microsoft 客戶合約 (MCA) 和 Microsoft 合作夥伴合約 (MPA) 計費帳戶來建立發票。 還會產生 Enterprise 合約 (EA) 計費帳戶的發票。 不過，Azure 入口網站中不會顯示 EA 計費帳戶的發票。

若要深入了解計費帳戶及識別您的計費帳戶類型，請參閱[在 Azure 入口網站中檢視計費帳戶](../manage/view-all-accounts.md)。

## <a name="invoices-for-mosp-billing-accounts"></a>MOSP 計費帳戶的發票

當您透過 Azure 網站註冊 Azure 時所建立的 MOSP 計費帳戶。 例如，當您註冊 [Azure 免費帳戶](https://azure.microsoft.com/offers/ms-azr-0044p/)、[採用隨用隨付費率的帳戶](https://azure.microsoft.com/offers/ms-azr-0003p/)，或以 [Visual studio 訂閱者](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/)身分註冊時。

精選區域中透過 Azure 網站註冊[採用隨用隨付費率的帳戶](https://azure.microsoft.com/offers/ms-azr-0003p/)或 [Azure 免費帳戶](https://azure.microsoft.com/offers/ms-azr-0044p/)的客戶，可能也有 MCA 的計費帳戶。

如果您不確定計費帳戶類型，在遵循本文中的指示之前，請參閱[檢查您的計費帳戶類型](../manage/view-all-accounts.md#check-the-type-of-your-account)。 

MOSP 計費帳戶可能有下列發票：

**Azure 服務費用** - 每個 Azure 訂用帳戶都會產生發票，當中包含訂用帳戶所使用的 Azure 資源。 發票包含計費週期的費用。 計費週期取決於建立訂用帳戶當月的日期。

例如，John 在 3 月 5 日建立 Azure sub 01  ，並在 3 月 10 日建立 Azure sub 02  。 Azure sub 01  的發票將會收取從當月第五天到下個月第四天的費用。 Azure sub 02  的發票將會收取從當月第十天到下個月第九天的費用。 所有 Azure 訂用帳戶的發票通常會在建立帳戶當月的日期產生，但最多可能會在兩天後產生。 在此範例中，如果 John 在 2 月 2 日建立帳戶，則 Azure sub 01  和 Azure sub 02  的發票通常會在每個月的第二天產生，但最多可能會在兩天後才產生。

**Azure Marketplace、保留和 Spot VM** - 系統會針對使用訂用帳戶購買的保留、市集產品和 Spot VM 虛擬機器產生發票。 發票會顯示上個月的個別費用。 例如，John 在 3 月 1 日購買了一份保留，然後在 3 月 30 日購買了另一份保留。 系統在 4 月份會針對兩份保留產生一張發票。 Azure Marketplace、保留和 Spot VM 的發票一律會在當月的第九天左右產生。

如果您使用信用卡支付 Azure 費用並購買保留，Azure 會產生立即發票。 不過，按發票計費時，會在下一個月的發票中向您收取保留費用。

**Azure 支援方案** - 每月會針對您的支援方案訂用帳戶來產生發票。 第一張發票會在購買日或最多兩天後產生。 後續的支援方案發票通常會在建立帳戶當月的相同日期產生，但最多可能會在兩天後才產生。

## <a name="download-your-mosp-azure-subscription-invoice"></a>下載您的 MOSP Azure 訂用帳戶發票

只有屬於 MOSP 計費帳戶的訂用帳戶才會產生發票。 [檢查您對 MOSP 帳戶的存取權](../manage/view-all-accounts.md#check-the-type-of-your-account)。 

您必須擁有訂用帳戶的帳戶管理員角色，才能下載其發票。 具有擁有者、參與者或讀者角色的使用者可以下載其發票 (如果帳戶管理員已授與他們許可權)。 如需詳細資訊，請參閱[允許使用者下載發票](../manage/manage-billing-access.md#opt-in)。

1. 在 Azure 入口網站的 [[訂用帳戶] 頁面](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)中選取您的訂用帳戶。
1. 從計費區段中選取 [發票]  。  
    ![顯示使用者選取訂用帳戶發票選項的螢幕擷取畫面](./media/download-azure-invoice/select-subscription-invoice.png)
1. 選取 [下載]  來下載 PDF 版本的發票，然後選取發票區段下的 [下載]  。  
    [![顯示計費期間、下載發票和每個計費期間總費用的螢幕擷取畫面](./media/download-azure-invoice/download-invoice-subscription.png)](./media/download-azure-invoice/download-invoice-subscription-zoomed.png#lightbox)
1. 您也可以選取使用量詳細資料區段下的 [下載]  ，來下載已耗用數量的每日明細和費用。 準備 CSV 檔案可能需要幾分鐘的時間。  
    ![顯示 [下載發票和使用量] 的螢幕擷取畫面](./media/download-azure-invoice/usage-and-invoice-subscription.png)

如需發票的詳細資訊，請參閱[了解 Microsoft Azure 帳單](../understand/review-individual-bill.md)。 如需管理成本的說明，請參閱[使用 Azure 計費與成本管理避免非預期的成本](../manage/getting-started.md)。

## <a name="download-your-mosp-support-plan-invoice"></a>下載您的 MOSP 支援方案發票

只有屬於 MOSP 計費帳戶的支援方案訂用帳戶才會產生發票。 [檢查您對 MOSP 帳戶的存取權](../manage/view-all-accounts.md#check-the-type-of-your-account)。 

您必須擁有支援方案訂用帳戶的帳戶管理員角色，才能下載其發票。

1.  登入 [Azure 入口網站](https://portal.azure.com)。
1.  搜尋 [成本管理 + 帳單]  。  
    ![顯示在入口網站中搜尋 [成本管理 + 帳單] 的螢幕擷取畫面](./media/download-azure-invoice/search-cmb.png)
1.  從左側選取 [發票]  。
1.  選取您的支援方案訂用帳戶，然後選取 [下載]  。  
    [![顯示帳單設定檔清單的螢幕擷取畫面](./media/download-azure-invoice/cmb-invoices.png)](./media/download-azure-invoice/cmb-invoices-zoomed-in.png#lightbox)
1.  選取 [下載]  來下載 PDF 版本的發票。  
    ![顯示計費期間、下載發票和每個計費期間總費用的螢幕擷取畫面](./media/download-azure-invoice/download-invoice-support-plan.png)

## <a name="allow-others-to-download-an-mosp-subscription-invoice"></a>允許其他人下載 MOSP 訂用帳戶發票

PDF 版本的發票包含關於帳戶管理員的個人資訊，因此這個訂用帳戶的帳戶管理員必須授與其他使用者下載發票的許可權。 授與許可權之後，下列具備適當角色的人員和使用者就可以下載訂用帳戶發票：

- 使用者
- 群組
- 具有擁有者的服務主體
- 參與者
- 讀取者
- 使用者存取管理員
- 帳單讀者
- 共同管理員
- 服務管理員

若要下載發票：

1.  以訂用帳戶的帳戶管理員身分登入 [Azure 入口網站](https://portal.azure.com)。
1.  搜尋 [成本管理 + 帳單]  。  
1.  從左側選取 [發票]  。
1.  選取您的 Azure 訂用帳戶，然後選取 [存取發票]  。  
    [![顯示選取存取發票的螢幕擷取畫面](./media/download-azure-invoice/cmb-select-access-to-invoice.png)](./media/download-azure-invoice/cmb-select-access-to-invoice-zoomed-in.png#lightbox)
1.  在頁面頂端選取 [開啟]  然後選取 [儲存]  。  
    ![顯示選取存取發票開啟的螢幕擷取畫面](./media/download-azure-invoice/cmb-access-to-invoice.png)

## <a name="get-mosp-subscription-invoice-in-email"></a>取得電子郵件中的 MOSP 訂用帳戶發票

您必須擁有訂用帳戶的帳戶管理員角色或支援方案，才能加入並透過電子郵件接收其發票。 電子郵件發票僅適用於訂用帳戶和支援方案，並不適用於保留或 Azure Marketplace 購買。 加入之後，您就可以新增其他的收件者，讓他們也能透過電子郵件接收發票。

1.  登入 [Azure 入口網站](https://portal.azure.com)。
1.  搜尋 [成本管理 + 帳單]  。  
1.  從左側選取 [發票]  。
1.  選取您的 Azure 訂用帳戶或支援方案訂用帳戶，然後選取 [電子郵件發票]  。  
    [![顯示帳單設定檔清單的螢幕擷取畫面](./media/download-azure-invoice/cmb-email-invoice.png)](./media/download-azure-invoice/cmb-email-invoice-zoomed-in.png#lightbox)
1. 選取 [加入]  並接受條款。  
    ![顯示加入流程步驟 2 的螢幕擷取畫面](./media/download-azure-invoice/invoice-article-step02.png)
1. 發票會傳送到您偏好的通訊電子郵件地址。 您可以在[計費帳戶的連絡人資訊](https://portal.azure.com/#blade/Microsoft_Azure_GTM/ModernBillingMenuBlade/Properties)中更新電子郵件地址。  
    ![顯示加入流程步驟 3 的螢幕擷取畫面](./media/download-azure-invoice/invoice-article-step03-verify-email.png)

## <a name="share-your-mosp-invoices-with-others-by-email"></a>透過電子郵件與其他人共用您的 MOSP 發票

您可能想要每個月與您的會計小組共用訂用帳戶和支援方案的發票，或將發票傳送到您其中一個其他的電子郵件地址。

1. 請遵循[在電子郵件中取得您訂用帳戶和支援方案的發票](#get-mosp-subscription-invoice-in-email)中的步驟，然後選取 [設定收件者]  。  
    ![顯示選取 [設定收件者] 之使用者的螢幕擷取畫面](./media/download-azure-invoice/invoice-article-step03.png)
1. 輸入電子郵件地址，然後選取 [新增收件者]  。 您可以新增多個電子郵件地址。  
    ![顯示新增其他收件者之使用者的螢幕擷取畫面](./media/download-azure-invoice/invoice-article-step04.png)
1. 新增所有電子郵件地址之後，請從畫面底部選取 [完成]  。

## <a name="invoices-for-mca-and-mpa-billing-accounts"></a>MCA 和 MPA 計費帳戶的發票

當貴組織與 Microsoft 代表合作簽署 MCA 時，就會建立 MCA 計費帳戶。 精選區域中某些透過 Azure 網站註冊[採用隨用隨付費率的帳戶](https://azure.microsoft.com/offers/ms-azr-0003p/)或 [Azure 免費帳戶](https://azure.microsoft.com/offers/ms-azr-0044p/)的客戶，可能也有 MCA 的計費帳戶。 如需詳細資訊，請參閱[開始使用 MCA 計費帳戶](../understand/mca-overview.md)。

MPA 計費帳戶是針對雲端解決方案提供者 (CSP) 合作夥伴所建立的，可供其在新的商務體驗中管理客戶。 合作夥伴必須至少有一個客戶具有 [Azure 方案](https://docs.microsoft.com/partner-center/purchase-azure-plan)，才能在 Azure 入口網站中管理其計費帳戶。 如需詳細資訊，請參閱[開始使用 MPA 計費帳戶](../understand/mpa-overview.md)。

您帳戶中的每個帳單設定檔都會在月初產生每月發票。 發票會分別包含所有 Azure 訂用帳戶以及上個月的其他購買所產生的個別費用。 例如，John 在 3 月 5 日建立 Azure sub 01  ，並在 3 月 10 日建立 Azure sub 02  。 他在 3 月 28 日使用「帳單設定檔 01」  購買了「Azure 支援 01」  訂用帳戶。 John 會在 4 月初取得一份發票，其中包含 Azure 訂用帳戶和支援方案的費用。

##  <a name="download-an-mca-or-mpa-billing-profile-invoice"></a>下載 MCA 或 MPA 帳單設定檔發票

您必須具有帳單設定檔的擁有者、參與者、讀者或發票管理員角色，才能在 Azure 入口網站下載其發票。 具有計費帳戶的擁有者、參與者或讀者角色的使用者，可以在帳戶中下載所有帳單設定檔的發票。

1.  登入 [Azure 入口網站](https://portal.azure.com)。
1.  搜尋 [成本管理 + 帳單]  。  
1. 從左側選取 [發票]  。
    [![顯示 MCA 計費帳戶之發票頁面的螢幕擷取畫面](./media/download-azure-invoice/mca-billing-profile-invoices.png)](./media/download-azure-invoice/mca-billing-profile-invoices-zoomed.png#lightbox)
1. 在發票資料表中，選取您想要下載的發票。
1. 選取頁面頂端的 [下載發票 pdf]  。  
    ![顯示 [下載發票 pdf] 的螢幕擷取畫面](./media/download-azure-invoice/mca-billing-profile-download-invoice.png)
1. 您也可以選取 [下載 Azure 使用量]  ，來下載包含已耗用數量的每日明細和預估費用的 CSV 檔案。 準備 CSV 檔案可能需要幾分鐘的時間。

## <a name="get-your-billing-profiles-invoice-in-email"></a>在電子郵件中取得帳單設定檔的發票

您必須具有帳單設定檔或其帳單帳戶的擁有者或參與者角色，才能更新其電子郵件發票喜好設定。 在您加入之後，具有帳單設定檔的擁有者、參與者、讀者和發票管理者角色的所有使用者，都會透過電子郵件取得其發票。 

1.  登入 [Azure 入口網站](https://portal.azure.com)。
1.  搜尋 [成本管理 + 帳單]  。  
1.  從左側窗格中選取 [發票]  ，然後從頁面頂端選取 [電子郵件發票]  。  
    [![顯示 MCA 計費帳戶之發票頁面的螢幕擷取畫面](./media/download-azure-invoice/mca-billing-profile-select-email-invoice.png)](./media/download-azure-invoice/mca-billing-profile-select-email-invoice-zoomed.png#lightbox)
1.  如果您有多個帳單設定檔，請選取帳單設定檔，然後選取 [加入]  。  
    ![顯示 MCA 計費帳戶之發票頁面的螢幕擷取畫面](./media/download-azure-invoice/mca-billing-profile-email-invoice.png)
1.  選取 [更新]  。

## <a name="give-others-access-to-mca-or-mpa-invoices"></a>授與其他人對 MCA 或 MPA 發票的存取權

您可以將 MCA 或 MPA 帳單設定檔的發票管理者角色指派給其他人，使其有權檢視、下載和支付發票。 如果您已加入透過電子郵件取得發票，則使用者也會透過電子郵件取得發票。

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 搜尋 [成本管理 + 帳單]  。  
1. 從左側選取 [帳單設定檔]  。 從 [帳單設定檔] 清單中，選取您要為其指派發票管理者角色的帳單設定檔。  
   ![顯示帳單設定檔清單的螢幕擷取畫面](./media/download-azure-invoice/mca-select-profile-zoomed-in.png)
1. 從左側選取 [存取控制 (IAM)]  ，然後從頁面頂端選取 [新增]  。  
    ![顯示存取控制頁面的螢幕擷取畫面](./media/download-azure-invoice/mca-select-access-control-zoomed-in.png)
1. 在 [角色] 下拉式清單中，選取 [發票管理者]  。 輸入您要授與存取權之使用者的電子郵件地址。 選取 [儲存]  以指派角色。  
   ![顯示將使用者新增為發票管理者的螢幕擷取畫面](./media/download-azure-invoice/mca-added-invoice-manager.png)

## <a name="why-you-might-not-see-an-invoice"></a><a name="noinvoice"></a> 為什麼您可能會看不到發票

您沒有看到發票的可能原因如下︰

- 您的 Azure 訂閱不滿 30 天。 
- Azure 會在您計費週期結束後的幾天內向您收取費用。 因此，發票可能尚未產生。
- 您沒有檢視發票的權限。
    如果您有 MCA 或 MPA 計費帳戶，您必須擁有帳單設定檔的擁有者、參與者、讀者或發票管理員角色，或是計費帳戶的擁有者、參與者或讀者角色，才能檢視發票。 對於其他訂用帳戶，如果您不是帳戶管理員，則可能看不到發票。 若要深入了解取得計費資訊的存取權，請參閱[使用角色管理 Azure 計費的存取權](../manage/manage-billing-access.md)。
- 如果您有 MOSP 帳單帳戶，而且您已註冊 Azure 免費帳戶或具有每月信用額度的訂用帳戶，則只有在您超過每月點數金額時，才會收到發票。 如果您有 MCA 或 MPA 的計費帳戶，則一律會收到發票。

## <a name="need-help-contact-us"></a>需要協助嗎？ 與我們連絡。

如果您有問題或需要協助，請[建立支援要求](https://go.microsoft.com/fwlink/?linkid=2083458)。

## <a name="next-steps"></a>後續步驟

若要深入了解您的發票和費用，請參閱：

- [檢視及下載您的 Microsoft Azure 使用量和費用](download-azure-daily-usage.md)
- [了解 Microsoft Azure 帳單](review-individual-bill.md)
- [了解 Azure 發票上的詞彙](understand-invoice.md)

如果您有 MCA，請參閱：

- [了解帳單設定檔發票上的費用](review-customer-agreement-bill.md)
- [了解帳單設定檔發票上的詞彙](mca-understand-your-invoice.md)
- [了解帳單設定檔的 Azure 使用量和費用檔案](mca-understand-your-usage.md)


