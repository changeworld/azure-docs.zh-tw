---
title: 從 Cloud Partner 入口網站轉換至合作夥伴中心-Microsoft 商業 marketplace 的常見問題
description: 從 Cloud Partner 入口網站轉換供應專案到合作夥伴中心的常見問題解答。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: mingshen-ms
ms.author: mingshen
ms.date: 07/14/2020
ms.openlocfilehash: 8bedecd4a050c445af0ab0ae0250197369b9173b
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93131406"
---
# <a name="frequently-asked-questions-about-transitioning-from-the-cloud-partner-portal-to-partner-center"></a>從 Cloud Partner 入口網站轉換到合作夥伴中心的常見問題

Cloud Partner 入口網站已轉換為合作夥伴中心。 合作夥伴中心提供簡化、整合的體驗，讓您能夠在 [Microsoft AppSource](https://appsource.microsoft.com/) 或 [Azure Marketplace](https://azuremarketplace.microsoft.com/) 上發佈新的供應專案，以及管理從 Cloud Partner 入口網站遷移的現有供應專案。 Cloud Partner 入口網站的所有功能都可在合作夥伴中心中使用。 本文說明有關此問題的常見問題。

## <a name="what-does-the-transition-to-partner-center-mean-for-me"></a>合作夥伴中心的轉換是什麼意思？

您可以繼續合作夥伴中心中的業務：

| 區域<img src="" width=200px> | 變更 |
| --- | --- |
| 帳戶 | 不需要建立新的合作夥伴中心帳戶;您可以使用現有的 Cloud Partner 入口網站認證登入合作夥伴中心您現在將在其中管理您的帳戶、使用者、許可權和帳單。 發佈合約和公司設定檔資訊會遷移至新的合作夥伴中心帳戶，以及任何付款設定檔資訊、使用者帳戶和許可權，以及使用中的供應專案。 若要深入瞭解，請參閱 [合作夥伴中心中的「管理您的商業市場」帳戶](partner-center-portal/manage-account.md)。 |
| 提供發佈和供應專案管理體驗 | 我們已將您的供應專案資料從 Cloud Partner 入口網站移至合作夥伴中心。 您現在會在合作夥伴中心中存取您的供應專案，以提供改良的使用者體驗和直覺化介面。 瞭解如何 [更新商業 marketplace 中的現有供應](partner-center-portal/update-existing-offer.md)專案。 |
| 商業 marketplace 中供應專案的可用性 | 無變更。 如果您的供應專案在商業 marketplace 中，它將會繼續上線。 |
| 新購買和部署 | 無變更。 您的客戶可以繼續購買及部署您的供應專案，而不會中斷。 |
| 支出 | 任何購買和部署都會繼續以正常的方式支付給您。 深入瞭解如何 [在商業 marketplace 中付費](/partner-center/marketplace-get-paid?context=/azure/marketplace/context/context)。 |
| 與現有 [Cloud Partner 入口網站 API](cloud-partner-portal-api-overview.md) \(部分機器翻譯\) 的 API 整合 | 仍然支援現有的 Cloud Partner 入口網站 Api，但您現有的整合仍可運作。 若要深入瞭解 [CLOUD PARTNER 入口網站 REST api 的支援嗎？](#are-the-cloud-partner-portal-rest-apis-still-supported) |
| 分析 | 您可以藉由在合作夥伴中心中觀看分析，來繼續監視銷售、評估效能，以及優化您在商業市場中的優惠。 在 CPP 和合作夥伴中心中，分析報告的顯示方式有所差異。 例如，CPP 中的 **Seller Insights** 有一個 [ **訂單] & [使用量** ] 索引標籤，其中會顯示以使用量為基礎的供應專案和非使用量型供應專案的資料，而在合作夥伴中心 **訂單** ] 頁面的 SaaS 供應專案則有個別的索引標籤。 [在合作夥伴中心中深入瞭解商用 marketplace 的存取分析報表](partner-center-portal/analytics.md)。 |
|||

## <a name="do-i-need-to-create-a-new-account-to-manage-my-offers-in-partner-center"></a>我是否需要在合作夥伴中心中建立新帳戶來管理我的供應專案？

否，您的帳戶將會保留。 這表示，如果您是現有的合作夥伴，您可以使用現有的 Cloud Partner 入口網站帳號憑證來登入合作夥伴中心。 請勿建立新的帳戶，或在 Cloud Partner 入口網站中建立並移至合作夥伴中心不會與其相關聯的任何供應專案。

## <a name="what-pages-in-partner-center-correspond-to-pages-i-used-in-the-cloud-partner-portal"></a>我在 Cloud Partner 入口網站中使用的頁面會對應到合作夥伴中心的哪些頁面？

以下是 Cloud Partner 入口網站常用頁面的合作夥伴中心連結。 如果您將 Cloud Partner 入口網站連結儲存為書簽，您會想要加以更新。

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

## <a name="payout-report-differences"></a>支付報告差異

這些是已淘汰的 Cloud Partner 入口網站與目前合作夥伴中心之間的付款報告差異：

| 雲端合作夥伴入口網站 | 合作夥伴中心 |
| --- | --- |
| **連結** ： https://cloudpartner.azure.com/ | **連結** ： https://partner.microsoft.com/dashboard/payouts/reports/transactionhistory 和 https://partner.microsoft.com/dashboard/payouts/reports/incentivepayments |
| **Navigation** ：見解支付中提供的支付報告 | **Navigation** ：合作夥伴中心提供的支付報告 – 支付圖示 |
| **範圍** ：<ul><li>針對進行中、已收取和已支付的收款，可看到每個明細項目的交易。</li><li>報表 – 顯示建立訂購單後的所有明細項目，包括進行中的收款和計費，以及收款狀態和尚未符合付費資格的明細項目。</li></ul> | **範圍** ：<ul><li>顯示在視為合格收益後的明細項目。</li><li>客戶必須先支付給 Microsoft，然後 ISV 才會看到支付報告開始。</li><li>支付報告不會顯示進行中的收款和進行中的計費。</li></ul> |
| **尚未準備支付的交易** ：進行中的計費 | **尚未準備支付的交易** ：下一個預估支付：支付狀態為尚未處理狀態。 |
| **支付狀態** ：無 | **支付狀態** ：<ul><li>尚未處理：收益符合付款資格。</li><li>近期：收益將在下次每月支付中送交至「發佈者」。</li><li>已送交：付款已送交至銀行。</li></ul> |
|||

## <a name="what-about-offers-i-published-in-the-cloud-partner-portal"></a>我在 Cloud Partner 入口網站中發佈的優惠如何？

這些供應專案已移至合作夥伴中心，當您登入合作夥伴中心之後，將可供您存取，但 Dynamics Nav 受控服務和 Cortana 情報提供的功能除外。 如果您的供應專案已在商用市集中上線，則會持續上線，而且您的客戶將繼續在不中斷的情況下購買及部署。 如需詳細資訊，請參閱下一個問題， **哪些供應專案已移至合作夥伴中心？** 。

## <a name="what-offers-were-moved-to-partner-center"></a>哪些供應專案已移至合作夥伴中心？

Cloud Partner 入口網站先前支援的所有供應專案類型都可在合作夥伴中心中支援，但 Dynamics Nav 受控服務和 Cortana 情報提供的功能除外。

針對合作夥伴中心中支援的供應專案類型，不論其狀態為何，都會移動所有供應專案;草稿、已取消列出和僅限預覽的供應專案也已移動。

| 供應項目類型 <img src="" width=150px>| 移至合作夥伴中心？ <img src="" width=100px>| 後續步驟 |
| --- | --- | --- |
| SaaS | 是 | 登入合作夥伴中心來建立新的供應專案，以及管理在 Cloud Partner 入口網站中建立的供應專案。 深入瞭解 [商用 marketplace 的 SaaS 供應專案方案](plan-saas-offer.md)。 |
| 虛擬機器 | 是 | 登入合作夥伴中心來建立新的供應專案，以及管理在 Cloud Partner 入口網站中建立的供應專案。 若要深入瞭解，請參閱 [虛擬機器供應](marketplace-virtual-machines.md)專案。 |
| Azure 應用程式 | 是 | 登入合作夥伴中心來建立新的供應專案，以及管理在 Cloud Partner 入口網站中建立的供應專案。 深入瞭解如何 [建立 Azure 應用程式供應](partner-center-portal/create-new-azure-apps-offer.md)專案。 |
| Dynamics 365 Business Central | 是 | 登入合作夥伴中心來建立新的供應專案，以及管理在 Cloud Partner 入口網站中建立的供應專案。 深入瞭解如何 [建立 Dynamics 365 Business Central 供應](partner-center-portal/create-new-business-central-offer.md)專案。 |
| Dynamics 365 for Customer Engagement & PowerApps | 是 | 登入合作夥伴中心來建立新的供應專案，以及管理在 Cloud Partner 入口網站中建立的供應專案。 深入瞭解如何 [建立 Dynamics 365 For Customer Engagement & PowerApps 供應專案](partner-center-portal/create-new-customer-engagement-offer.md)。 |
| Dynamics 365 for Operations | 是 | 登入合作夥伴中心來建立新的供應專案，以及管理在 Cloud Partner 入口網站中建立的供應專案。 深入瞭解如何 [建立適用于作業的 Dynamics 365 供應](partner-center-portal/create-new-operations-offer.md)專案。 |
| Power BI 應用程式 | 是 | 登入合作夥伴中心來建立新的供應專案，以及管理在 Cloud Partner 入口網站中建立的供應專案。 深入瞭解如何 [建立適用于 AppSource 的 Power BI 應用程式](partner-center-portal/create-power-bi-app-offer.md)。 |
| IoT Edge 模組 | 是 | 登入合作夥伴中心來建立新的供應專案，以及管理在 Cloud Partner 入口網站中建立的供應專案。 若要深入瞭解，請參閱 [Azure Marketplace 中的建立、設定及發佈 IoT Edge 模組供應](partner-center-portal/azure-iot-edge-module-creation.md)專案。 |
| 容器 | 是 | 登入合作夥伴中心來建立新的供應專案，以及管理在 Cloud Partner 入口網站中建立的供應專案。 深入瞭解如何 [建立 Azure 容器供應](partner-center-portal/create-azure-container-offer.md)專案。 |
| 諮詢服務 | 是 | 登入合作夥伴中心來建立新的供應專案，以及管理在 Cloud Partner 入口網站中建立的供應專案。 若要深入瞭解，請參閱 [建立諮詢服務供應](partner-center-portal/create-consulting-service-offer.md)專案。 |
| 受管理的服務 | 是 | 登入合作夥伴中心來建立新的供應專案，以及管理在 Cloud Partner 入口網站中建立的供應專案。 深入瞭解如何 [建立受控服務供應](partner-center-portal/create-new-managed-service-offer.md)專案。 |
| Dynamics Nav 受控服務 | 否 | Microsoft 已將 Dynamics NAV 受控服務發展為 [dynamics 365 Business Central](/dynamics365/business-central/)，因此我們從 AppSource 中移除了 Dynamics Nav 受控服務的即時優惠。 這些供應專案不再由客戶探索，也未移至合作夥伴中心。 若要在 AppSource 中提供您的供應專案，請將其調整為 Dynamics 365 Business Central 供應專案，並在 [合作夥伴中心](https://partner.microsoft.com/)中提交。 深入瞭解如何 [建立 Dynamics 365 Business Central 供應](partner-center-portal/create-new-business-central-offer.md)專案。 |
| Cortana Intelligence | 否 | Microsoft 已發展出 Cortana 情報的產品藍圖，因此我們從 AppSource 中移除了 Cortana 情報的即時優惠。 這些供應專案不再由客戶探索，也未移至合作夥伴中心。 若要讓您的供應專案可在商業市集中使用，請將您的供應專案調整為「軟體即服務」 (SaaS) 優惠，並在 [合作夥伴中心](https://partner.microsoft.com/)中提交。 在 [合作夥伴中心中深入瞭解 SaaS 供應專案建立檢查清單](./plan-saas-offer.md)。 |

## <a name="i-cant-find-my-cloud-partner-portal-offers-in-partner-center"></a>我在合作夥伴中心中找不到我的 Cloud Partner 入口網站供應專案

您在合作夥伴中心中看到的內容，取決於您註冊的程式、您所屬的帳戶，以及您已指派的使用者角色和許可權。 有許多合作夥伴中心程式可供使用，而且您可以在多個程式中註冊。 您也可以使用相同的使用者認證來存取多個帳戶。

您在 Cloud Partner 入口網站中建立的供應專案，可在 **商業 Marketplace** 方案下的合作夥伴中心，以及用來建立供應專案的帳戶下取得。 若要確定您正在查看正確的程式和正確的帳戶，請遵循下列步驟。 如需其他疑難排解秘訣，請參閱 [管理您的合作夥伴中心帳戶](/partner-center/partner-center-account-setup)。

### <a name="access-the-right-program-in-partner-center"></a>存取合作夥伴中心中的正確程式

1. 以用來登入 Cloud Partner 入口網站的相同認證登入 [合作夥伴中心](https://partner.microsoft.com/dashboard/commercial-marketplace/overview) 。 左邊的導覽窗格會顯示與您註冊的程式相關聯的選項。

    範例：假設您有三個程式的存取權： MPN 方案、參考計畫和商用 Marketplace 方案。 當您登入合作夥伴中心時，您會在導覽窗格中看到這三個程式。

2. 選取 [ **商業 Marketplace**  >  **總覽** ] 以存取您的供應專案。

    如果您在左側的流覽窗格中看不到 [商業 Marketplace] 方案，則您可能是在錯誤的帳戶中。 遵循下一節中的步驟，以存取正確的帳戶。

    [![顯示合作夥伴中心總覽功能表的螢幕擷取畫面](media/cpp-pc-faq/overview-menu.png "顯示合作夥伴中心總覽] 功能表")](media/cpp-pc-faq/overview-menu.png#lightbox)

### <a name="access-the-right-account-in-partner-center"></a>在合作夥伴中心中存取正確的帳戶

如果您是多個帳戶的一部分，則在 [合作夥伴中心] 中，您會在左側導覽功能表中看到以兩個箭號標記的帳戶選擇器按鈕。 選取 [帳戶選擇器] 按鈕，以查看您所屬的所有帳戶清單。 選取清單上的任何帳戶來切換至該帳戶，並查看與該帳戶相關的所有程式和資訊。 如果您在 [導覽] 功能表中沒有看到 [帳戶選擇器] 按鈕，您就是單一帳戶的成員。

[![螢幕擷取畫面顯示合作夥伴中心帳戶選擇器按鈕。](media/cpp-pc-faq/picker-button.png "顯示合作夥伴中心帳戶選擇器按鈕")](media/cpp-pc-faq/picker-button.png#lightbox)

## <a name="how-do-i-create-new-offers"></a>如何? 建立新的供應專案？

存取 [合作夥伴中心](https://partner.microsoft.com/dashboard/commercial-marketplace/overview) 中的商用 marketplace 方案，以建立新的供應專案。 在 [總覽] 頁面上，選取 [ **+ 新增供應** 專案]。

[![螢幕擷取畫面顯示合作夥伴中心總覽] 功能表。](media/cpp-pc-faq/new-offer.png "顯示合作夥伴中心總覽] 功能表")](media/cpp-pc-faq/new-offer.png#lightbox)

## <a name="i-cant-sign-in-and-need-to-open-a-support-ticket"></a>我無法登入，且必須開啟支援票證

如果您無法登入您的帳戶，您可以在這裡開啟 [支援票證](https://partner.microsoft.com/support/v2/?stage=1) 。

## <a name="where-are-instructions-for-using-partner-center"></a>使用合作夥伴中心的指示在哪裡？

移至 [商用 marketplace 檔](index.yml)，然後展開 **合作夥伴中心中的 [商用 marketplace 入口網站** ]。 若要查看合作夥伴中心中建立供應專案的說明文章，請展開 [ **建立新供應** 專案]。

## <a name="what-are-the-publishing-and-offer-management-differences"></a>發佈和供應專案管理有何不同？

以下是 Cloud Partner 入口網站和合作夥伴中心之間的一些差異。

### <a name="modular-publishing-capabilities"></a>模組化發佈功能

合作夥伴中心提供模組化的發佈選項，可讓您選取想要發佈的變更，而非一律同時發佈所有更新。 例如，下列畫面顯示選取要發行的唯一變更是 **屬性** 和  **供應專案清單** 的變更。 您在 [預覽] 頁面中所做的變更將不會發行。

[![螢幕擷取畫面顯示 [合作夥伴中心審核] 和 [發佈] 頁面。](media/cpp-pc-faq/review-page.png "顯示合作夥伴中心審核和發佈頁面")](media/cpp-pc-faq/review-page.png#lightbox)

您未發佈的更新會儲存為草稿。 繼續使用您的供應項目預覽，以在將供應項目公開上線之前加以驗證。

### <a name="enhanced-preview-options"></a>強化的預覽選項

合作夥伴中心包含[比較功能](partner-center-portal/update-existing-offer.md#compare-changes-to-your-offer) \(部分機器翻譯\)，其具備改善的篩選選項。 這可讓您比較供應項目的預覽及上線版本。

[![螢幕擷取畫面顯示合作夥伴中心的比較功能。](media/cpp-pc-faq/compare.png "顯示合作夥伴中心比較功能")](media/cpp-pc-faq/compare.png#lightbox)

### <a name="branding-and-navigation-changes"></a>商標與導覽變更

您將會注意到一些商標上的變更。 例如，在合作夥伴中心中， *sku* 會標示為 *方案* ：

[![螢幕擷取畫面：顯示 [合作夥伴中心方案] 頁面。](media/cpp-pc-faq/plans.png "顯示合作夥伴中心方案] 頁面")](media/cpp-pc-faq/plans.png#lightbox)

此外，您先前在 **Marketplace** 或 **店面詳細資料**  中提供的資訊 (諮詢服務、Cloud Partner 入口網站中 Power BI 的應用程式) 頁面，現在會在合作夥伴中心中的 **供應專案清單** 頁面上收集：

[![螢幕擷取畫面顯示合作夥伴中心供應專案清單頁面]。 (媒體/cpp-pc-常見問題/offer-listing.png](media/cpp-pc-faq/offer-listing.png#lightbox)

您先前在 Cloud Partner 入口網站的單一頁面中提供的 Sku 資訊，現在可能會在合作夥伴中心的數個頁面中收集：

- 規劃設定頁面
- [計劃清單] 頁面
- 計劃 [可用性] 頁面
- 規劃技術配置頁面，如下所示：

[![顯示合作夥伴中心技術配置] 頁面。](media/cpp-pc-faq/technical-configuration.png)](media/cpp-pc-faq/technical-configuration.png#lightbox)

供應專案識別碼現在會顯示在供應專案的左側導覽列中：

![顯示合作夥伴中心供應專案識別碼位置](media/cpp-pc-faq/offer-id.png)

### <a name="stop-selling-an-offer"></a>停止銷售供應項目

您可以直接從合作夥伴中心入口網站要求[停止在市集上銷售某個供應項目](partner-center-portal/update-existing-offer.md#stop-selling-an-offer-or-plan) \(部分機器翻譯\)。 該選項會在您供應項目的 [供應項目概觀] 頁面上提供。

[![螢幕擷取畫面顯示停止銷售供應專案的合作夥伴中心頁面。](media/cpp-pc-faq/stop-sell.png "顯示停止銷售供應專案的合作夥伴中心頁面")](media/cpp-pc-faq/stop-sell.png#lightbox)
<br><br>

## <a name="are-the-cloud-partner-portal-rest-apis-still-supported"></a>是否仍支援 Cloud Partner 入口網站 REST Api？

Cloud Partner 入口網站 Api 會與合作夥伴中心整合，並且會繼續運作。 轉換至合作夥伴中心導入了少量變更。 請參閱下表，以確保您的程式碼在合作夥伴中心中繼續運作。

| API <img src="" width=100px>| 變更描述 | 影響 |
| --- | --- | --- |
| POST Publish、GoLive、Cancel | 針對已移轉的供應項目，回應標頭將會有不同的格式，但會繼續以相同的方式運作，表示用來擷取作業狀態的相對路徑。 | 傳送供應專案的任何對應 POST 要求時，Location 標頭會有兩種格式的其中一種，視供應專案的遷移狀態而定： <ul><li>未遷移的優惠： `/api/operations/{PublisherId}${offerId}$2$preview?api-version=2017-10-31`</li><li>已遷移的優惠： `/api/publishers/{PublisherId}/offers/{offereId}/operations/408a4835-0000-1000-0000-000000000000?api-version=2017-10-31`</li></ul>|
| GET 作業 | 針對先前在回應中支援「通知-電子郵件」欄位的供應專案，此欄位將會被取代，而且不會再傳回給已遷移的供應專案。 | 針對已移轉的供應項目，我們將不會繼續針對要求中所指定的電子郵件清單傳送通知。 相反地，API 服務將會對應到合作夥伴中心的通知電子郵件程序以傳送電子郵件。 具體來說，作業進度的通知將會傳送至您在合作夥伴中心中帳戶設定的 [賣方連絡人資訊] 區段中設定的電子郵件地址。<br><br>請確定 [合作夥伴中心中 [帳戶設定](https://partner.microsoft.com/dashboard/account/management) 的 [賣方連絡人資訊] 區段中所設定的電子郵件地址是否正確，以接收通知。 |
|||