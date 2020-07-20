---
title: 從 Cloud Partner 入口網站轉換到合作夥伴中心的常見問題-Microsoft 商業 marketplace
description: 從 Cloud Partner 入口網站轉換供應專案到合作夥伴中心的常見問題解答。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: anbene
ms.author: mingshen
ms.date: 06/29/2020
ms.openlocfilehash: e708d9a32d63c306a2d1ee8d06b044652f108dde
ms.sourcegitcommit: f7e160c820c1e2eb57dc480b2a8fd6bef7053e91
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/10/2020
ms.locfileid: "86231874"
---
# <a name="frequently-asked-questions-about-transitioning-from-the-cloud-partner-portal-to-partner-center"></a>從 Cloud Partner 入口網站轉換到合作夥伴中心的常見問題

Cloud Partner 入口網站已轉換至合作夥伴中心。 合作夥伴中心提供簡化的整合式體驗，可讓您在[Microsoft AppSource](https://appsource.microsoft.com/)或[Azure Marketplace](https://azuremarketplace.microsoft.com/)上發佈新的供應專案，以及管理從 Cloud Partner 入口網站遷移的現有供應專案。 Cloud Partner 入口網站的所有功能都可在合作夥伴中心取得。 本文說明有關此問題的常見問題。

## <a name="what-does-the-transition-to-partner-center-mean-for-me"></a>轉換到合作夥伴中心對我有何意義？

您可以如往常般繼續在合作夥伴中心執行業務：

| 區域<img src="" width=200px> | 變更 |
| --- | --- |
| 帳戶 | 不需要建立新的合作夥伴中心帳戶;您可以使用現有的 Cloud Partner 入口網站認證登入合作夥伴中心，您現在會在其中管理您的帳戶、使用者、許可權和計費。 發行合約和公司設定檔資訊會遷移至新的合作夥伴中心帳戶，以及任何付款設定檔資訊、使用者帳戶和許可權，以及使用中的供應專案。 若要深入瞭解，請到[在合作夥伴中心管理您的商業 marketplace 帳戶](partner-center-portal/manage-account.md)。 |
| 提供發佈和供應專案管理體驗 | 我們已將您的供應專案資料從 Cloud Partner 入口網站移至合作夥伴中心。 您現在將會在合作夥伴中心存取您的供應專案，以提供改良的使用者體驗和直覺介面。 瞭解如何[在商業 Marketplace 中更新現有的供應](partner-center-portal/update-existing-offer.md)專案。 |
| 在商業 marketplace 中提供供應專案的可用性 | 無變更。 如果您的供應專案在商用 marketplace 中上線，則會繼續上線。 |
| 新購買和部署 | 無變更。 您的客戶可以繼續購買及部署您的供應專案，而不會有任何中斷。 |
| 支出 | 任何購買和部署都將繼續以一般方式向您收費。 深入瞭解如何[在商業 marketplace 中付費](partner-center-portal/get-paid.md)。 |
| 與現有 [Cloud Partner 入口網站 API](cloud-partner-portal-api-overview.md) \(部分機器翻譯\) 的 API 整合 | 仍然支援現有的 Cloud Partner 入口網站 Api，且您現有的整合仍可正常操作。 若要深入瞭解[，可支援 CLOUD PARTNER 入口網站 REST api 嗎？](#are-the-cloud-partner-portal-rest-apis-still-supported) |
| 分析 | 您可以藉由在合作夥伴中心觀看分析，繼續監視銷售、評估效能，以及優化商業 marketplace 中的供應專案。 若要深入瞭解，請[存取合作夥伴中心的商業 marketplace 分析報告](partner-center-portal/analytics.md)。 |
|||

## <a name="do-i-need-to-create-a-new-account-to-manage-my-offers-in-partner-center"></a>我是否需要在合作夥伴中心內建立新的帳戶來管理我的供應專案？

否，您的帳戶將會保留。 這表示如果您是現有的合作夥伴，您可以使用現有的 Cloud Partner 入口網站帳號憑證來登入合作夥伴中心。 請勿建立新的帳戶，或在 Cloud Partner 入口網站中建立並移至合作夥伴中心的任何供應專案，都不會與其相關聯。

## <a name="what-pages-in-partner-center-correspond-to-pages-i-used-in-the-cloud-partner-portal"></a>我在 Cloud Partner 入口網站中使用的頁面會對應到合作夥伴中心的哪些頁面？

以下是常用於 Cloud Partner 入口網站中的頁面合作夥伴中心連結。 如果您將 Cloud Partner 入口網站連結儲存為書簽，您會想要加以更新。

| Cloud Partner 入口網站頁面 <img src="" width=100px>| Cloud Partner 入口網站頁面連結 | 合作夥伴中心頁面連結 |
| --- | --- | --- |
| 所有供應項目頁面 | [https://cloudpartner.azure.com/#alloffers](https://cloudpartner.azure.com/#alloffers) | [https://partner.microsoft.com/dashboard/commercial-marketplace/overview](https://partner.microsoft.com/dashboard/commercial-marketplace/overview) |
| 所有發行者頁面 | [https://cloudpartner.azure.com/#publishers](https://cloudpartner.azure.com/#publishers) | [https://partner.microsoft.com/dashboard/account/v3/publishers/list](https://partner.microsoft.com/dashboard/account/v3/publishers/list) |
| 發行者設定檔 | [https://cloudpartner.azure.com/#profile](https://cloudpartner.azure.com/#profile) | [https://partner.microsoft.com/dashboard/account/management](https://partner.microsoft.com/dashboard/account/management) |
| 使用者分頁 | [https://cloudpartner.azure.com/#users](https://cloudpartner.azure.com/#users) | [https://partner.microsoft.com/dashboard/account/usermanagement](https://partner.microsoft.com/dashboard/account/usermanagement) |
| 記錄頁面 | [https://cloudpartner.azure.com/#history](https://cloudpartner.azure.com/#history) | 合作夥伴中心尚未支援 [記錄] 功能。 |
| 見解儀表板 | [https://cloudpartner.azure.com/#insights](https://cloudpartner.azure.com/#insights) | [https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary](https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary) |
| 支出報告 | [https://cloudpartner.azure.com/#insights/payout](https://cloudpartner.azure.com/#insights/payout) | [https://partner.microsoft.com/dashboard/payouts/reports/incentivepayments](https://partner.microsoft.com/dashboard/payouts/reports/incentivepayments) |
|||

## <a name="what-about-offers-i-published-in-the-cloud-partner-portal"></a>我在 Cloud Partner 入口網站中發佈了哪些供應專案？

供應專案已移至合作夥伴中心，在您登入合作夥伴中心之後，您就可以存取此供應專案，但 Dynamics Nav 受控服務和 Cortana 智慧供應專案除外。 如果您的供應專案在商用 marketplace 中上線，它會繼續上線，而您的客戶將繼續在不中斷的情況下購買及部署。 如需詳細資訊，請參閱下一個問題：**哪些供應專案已移至合作夥伴中心？**。

## <a name="what-offers-were-moved-to-partner-center"></a>哪些供應專案已移至合作夥伴中心？

合作夥伴中心支援 Cloud Partner 入口網站先前支援的所有供應專案類型，但 Dynamics Nav 受控服務和 Cortana 智慧供應專案除外。

針對合作夥伴中心所支援的供應專案類型，不論其狀態為何，所有優惠都會移動;草稿、已取消列出及僅限預覽的供應專案也已移動。

| 供應項目類型 <img src="" width=150px>| 已移至合作夥伴中心嗎？ <img src="" width=100px>| 後續步驟 |
| --- | --- | --- |
| SaaS | 是 | 登入合作夥伴中心以建立新的供應專案，並管理在 Cloud Partner 入口網站中建立的供應專案。 若要深入瞭解，請在[商業 marketplace 中建立新的 SaaS 供應](partner-center-portal/create-new-saas-offer.md)專案。 |
| 虛擬機器 | 是 | 登入合作夥伴中心以建立新的供應專案，並管理在 Cloud Partner 入口網站中建立的供應專案。 若要深入瞭解，請在[Azure Marketplace 上建立 Azure 虛擬機器供應](partner-center-portal/azure-vm-create-offer.md)專案。 |
| Azure 應用程式 | 是 | 登入合作夥伴中心以建立新的供應專案，並管理在 Cloud Partner 入口網站中建立的供應專案。 若要深入瞭解，請查看[建立 Azure 應用程式供應](partner-center-portal/create-new-azure-apps-offer.md)專案。 |
| Dynamics 365 Business Central | 是 | 登入合作夥伴中心以建立新的供應專案，並管理在 Cloud Partner 入口網站中建立的供應專案。 若要深入瞭解，請到[建立 Dynamics 365 Business Central 供應](partner-center-portal/create-new-business-central-offer.md)專案。 |
| Dynamics 365 for Customer Engagement & PowerApps | 是 | 登入合作夥伴中心以建立新的供應專案，並管理在 Cloud Partner 入口網站中建立的供應專案。 若要深入瞭解，請[& PowerApps 供應專案建立 Dynamics 365 For Customer Engagement](partner-center-portal/create-new-customer-engagement-offer.md)。 |
| Dynamics 365 for Operations | 是 | 登入合作夥伴中心以建立新的供應專案，並管理在 Cloud Partner 入口網站中建立的供應專案。 若要深入瞭解，請到[建立 Dynamics 365 For Operations 供應](partner-center-portal/create-new-operations-offer.md)專案。 |
| Power BI 應用程式 | 是 | 登入合作夥伴中心以建立新的供應專案，並管理在 Cloud Partner 入口網站中建立的供應專案。 若要深入瞭解，請[Power BI 建立適用于 AppSource 的應用程式](partner-center-portal/create-power-bi-app-offer.md)。 |
| IoT Edge 模組 | 是 | 登入合作夥伴中心以建立新的供應專案，並管理在 Cloud Partner 入口網站中建立的供應專案。 若要深入瞭解[，請在 Azure Marketplace 中建立、設定及發佈 IoT Edge 模組供應](partner-center-portal/azure-iot-edge-module-creation.md)專案。 |
| 容器 | 是 | 登入合作夥伴中心以建立新的供應專案，並管理在 Cloud Partner 入口網站中建立的供應專案。 若要深入瞭解，請查看[建立 Azure 容器供應](partner-center-portal/create-azure-container-offer.md)專案。 |
| 諮詢服務 | 是 | 登入合作夥伴中心以建立新的供應專案，並管理在 Cloud Partner 入口網站中建立的供應專案。 若要深入瞭解，請到[建立諮詢服務供應](partner-center-portal/create-consulting-service-offer.md)專案。 |
| 受控服務 | 是 | 登入合作夥伴中心以建立新的供應專案，並管理在 Cloud Partner 入口網站中建立的供應專案。 若要深入瞭解，請到[建立受控服務供應](partner-center-portal/create-new-managed-service-offer.md)專案。 |
| Dynamics Nav 受控服務 | 否 | Microsoft 已將 Dynamics NAV 受控服務演變為[dynamics 365 Business Central](https://docs.microsoft.com/dynamics365/business-central/)，因此我們已從 AppSource 中取消列出 Dynamics Nav 受控服務的即時供應專案。 這些供應專案已不再由客戶探索，而且尚未移至合作夥伴中心。 若要在 AppSource 中提供您的供應專案，請將其調整為 Dynamics 365 Business Central 供應專案，並在[合作夥伴中心](https://partner.microsoft.com/)提交。 若要深入瞭解，請到[建立 Dynamics 365 Business Central 供應](partner-center-portal/create-new-business-central-offer.md)專案。 |
| Cortana Intelligence | 否 | Microsoft 已發展 Cortana 情報的產品藍圖，因此我們已從 AppSource 中取消列出 Cortana 情報的現場優惠。 這些供應專案已不再由客戶探索，而且尚未移至合作夥伴中心。 若要在商業 marketplace 中提供您的供應專案，請將您的供應專案調整為軟體即服務 (SaaS) 供應專案，並在[合作夥伴中心](https://partner.microsoft.com/)提交這些產品。 若要深入瞭解，請到[合作夥伴中心的 SaaS 供應專案建立檢查清單](partner-center-portal/offer-creation-checklist.md)。 |

## <a name="i-cant-find-my-cloud-partner-portal-offers-in-partner-center"></a>我在合作夥伴中心找不到我的 Cloud Partner 入口網站供應專案

您在 [合作夥伴中心] 看到的內容取決於您註冊的程式、您所屬的帳戶，以及您已指派的使用者角色和許可權。 有許多合作夥伴中心方案可供使用，而且您可以在多個程式中註冊。 您也可以使用相同的使用者認證來存取多個帳戶。

您在 Cloud Partner 入口網站中建立的供應專案可在合作夥伴中心的**商業 Marketplace**方案下，以及用來建立優惠的帳戶下取得。 若要確定您正在查看正確的程式和正確的帳戶，請依照下列步驟進行。 如需其他疑難排解秘訣，請參閱[管理您的合作夥伴中心帳戶](https://docs.microsoft.com/partner-center/partner-center-account-setup)。

### <a name="access-the-right-program-in-partner-center"></a>在合作夥伴中心存取正確的程式

1. 使用用來登入 Cloud Partner 入口網站的相同認證來登入[合作夥伴中心](https://partner.microsoft.com/dashboard/commercial-marketplace/overview)。 左側的流覽窗格會顯示與您註冊的程式相關聯的選項。

    範例：假設您有三個程式的存取權： MPN 程式、參考計畫和商業 Marketplace 計畫。 當您登入合作夥伴中心時，您會在流覽窗格中看到這三個程式。

2. 選取 [**商用 Marketplace**  >  **總覽**] 以存取您的供應專案。

    如果您在左側的流覽窗格中沒有看到 [商業 Marketplace] 方案，可能是您的帳戶錯誤。 請遵循下一節中的步驟來存取正確的帳戶。

    [![](media/cpp-pc-faq/overview-menu.png "Shows the Partner Center Overview menu")](media/cpp-pc-faq/overview-menu.png#lightbox)

### <a name="access-the-right-account-in-partner-center"></a>在合作夥伴中心存取正確的帳戶

如果您是多個帳戶的一部分，在合作夥伴中心中，您會在左側的導覽功能表中看到以兩個箭號標示的帳戶選擇器按鈕。 選取 [帳戶選擇器] 按鈕，以查看您所屬帳戶的清單。 選取清單上的任何帳戶以切換至該帳戶，並查看與該帳戶相關的所有程式和資訊。 如果您在導覽功能表中看不到 [帳戶選擇器] 按鈕，則您是單一帳戶的成員。

[![](media/cpp-pc-faq/picker-button.png "Shows the Partner Center account picker button")](media/cpp-pc-faq/picker-button.png#lightbox)

## <a name="how-do-i-create-new-offers"></a>如何? 建立新的供應專案嗎？

存取[合作夥伴中心](https://partner.microsoft.com/dashboard/commercial-marketplace/overview)的商業 marketplace 方案，以建立新的供應專案。 在 [總覽] 頁面上，選取 [ **+ 新增供應**專案]。

[![](media/cpp-pc-faq/new-offer.png "Shows the Partner Center Overview menu")](media/cpp-pc-faq/new-offer.png#lightbox)

## <a name="i-cant-sign-in-and-need-to-open-a-support-ticket"></a>我無法登入，且需要開啟支援票證

如果您無法登入您的帳戶，可以在這裡開啟[支援票證](https://partner.microsoft.com/support/v2/?stage=1)。

## <a name="where-are-instructions-for-using-partner-center"></a>使用合作夥伴中心的指示在哪裡？

前往[商業 marketplace 檔](index.yml)，然後展開 [**合作夥伴中心] 中的 [商業 marketplace 入口網站**]。 若要查看在合作夥伴中心建立供應專案的說明文章，請展開 [**建立新的方案**]。

## <a name="what-are-the-publishing-and-offer-management-differences"></a>發佈和供應專案管理的差異為何？

以下是 Cloud Partner 入口網站和合作夥伴中心之間的一些差異。

### <a name="modular-publishing-capabilities"></a>模組化發佈功能

合作夥伴中心提供模組化的發佈選項，可讓您選取想要發佈的變更，而非一律同時發佈所有更新。 例如，下列畫面顯示選取要發佈的唯一變更是**屬性**和**供應專案清單**的變更。 您在預覽頁面中所做的變更將不會發行。

[![](media/cpp-pc-faq/review-page.png "Shows the Partner Center Review and publish page")](media/cpp-pc-faq/review-page.png#lightbox)

您未發佈的更新會儲存為草稿。 繼續使用您的供應項目預覽，以在將供應項目公開上線之前加以驗證。

### <a name="enhanced-preview-options"></a>強化的預覽選項

合作夥伴中心包含[比較功能](partner-center-portal/update-existing-offer.md#compare-changes-to-marketplace-offers) \(部分機器翻譯\)，其具備改善的篩選選項。 這可讓您比較供應項目的預覽及上線版本。

[![](media/cpp-pc-faq/compare.png "Shows the Partner Center compare feature")](media/cpp-pc-faq/compare.png#lightbox)

### <a name="branding-and-navigation-changes"></a>商標與導覽變更

您將會注意到一些商標上的變更。 例如， *sku*的品牌為合作夥伴中心的*計畫*：

[![](media/cpp-pc-faq/plans.png "Shows the Partner Center Plans page")](media/cpp-pc-faq/plans.png#lightbox)

此外，您先前在**Marketplace**或**店面詳細資料**中提供的資訊 (諮詢服務，Cloud Partner 入口網站中的 Power BI 應用程式) 頁面現在會在合作夥伴中心的**供應專案清單**頁面上收集：

[![](media/cpp-pc-faq/offer-listing.png "Shows the Partner Center Offer listing page")](media/cpp-pc-faq/offer-listing.png#lightbox)

您先前在 Cloud Partner 入口網站的單一頁面中提供的 Sku 資訊，現在可能會在合作夥伴中心的數個頁面中收集：

- [計劃設定] 頁面
- [計劃清單] 頁面
- 計劃 [可用性] 頁面
- [規劃技術設定] 頁面，如下所示： ![ ！ [] (media/cpp-pc-常見問題/technical-configuration.png」顯示合作夥伴中心的 [技術設定] 頁面 [) ](media/cpp-pc-faq/technical-configuration.png#lightbox)

您的供應專案識別碼現在會顯示在供應專案的左側導覽列上：

![顯示合作夥伴中心供應專案識別碼位置](media/cpp-pc-faq/offer-id.png)

### <a name="stop-selling-an-offer"></a>停止銷售供應項目

您可以直接從合作夥伴中心入口網站要求[停止在市集上銷售某個供應項目](partner-center-portal/update-existing-offer.md#stop-selling-an-offer-or-plan) \(部分機器翻譯\)。 該選項會在您供應項目的 [供應項目概觀] 頁面上提供。

[![](media/cpp-pc-faq/stop-sell.png "Shows the Partner Center page to stop selling an offer")](media/cpp-pc-faq/stop-sell.png#lightbox)
<br><br>

## <a name="are-the-cloud-partner-portal-rest-apis-still-supported"></a>是否仍支援 Cloud Partner 入口網站 REST Api？

Cloud Partner 入口網站 Api 會與合作夥伴中心整合，並會繼續工作。 轉換至合作夥伴中心會引進較小的變更。 請參閱下表，以確保您的程式碼在合作夥伴中心內繼續運作。

| API <img src="" width=100px>| 變更描述 | 影響 |
| --- | --- | --- |
| POST Publish、GoLive、Cancel | 針對已移轉的供應項目，回應標頭將會有不同的格式，但會繼續以相同的方式運作，表示用來擷取作業狀態的相對路徑。 | 傳送供應專案的任何對應 POST 要求時，位置標頭會有兩種格式的其中一種，視供應專案的遷移狀態而定： <ul><li>非遷移的供應專案：`/api/operations/{PublisherId}${offerId}$2$preview?api-version=2017-10-31`</li><li>遷移的供應專案：`/api/publishers/{PublisherId}/offers/{offereId}/operations/408a4835-0000-1000-0000-000000000000?api-version=2017-10-31`</li></ul>|
| GET 作業 | 對於先前在回應中支援「通知-電子郵件」欄位的供應專案，此欄位將會被取代，且不會再傳回給遷移的供應專案。 | 針對已移轉的供應項目，我們將不會繼續針對要求中所指定的電子郵件清單傳送通知。 相反地，API 服務將會對應到合作夥伴中心的通知電子郵件程序以傳送電子郵件。 具體而言，作業進度的通知會傳送至您在合作夥伴中心的帳戶設定的 [賣方連絡人資訊] 區段中設定的電子郵件地址。<br><br>確定在 [合作夥伴中心] 的 [[帳戶設定](https://partner.microsoft.com/dashboard/account/management)] 的 [賣方連絡人資訊] 區段中設定的電子郵件地址正確，可接收通知。 |
|||