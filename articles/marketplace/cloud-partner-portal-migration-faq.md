---
title: 移轉到合作夥伴中心的相關常見問題 - Microsoft 商業市集
description: 將供應項目從 Cloud Partner 入口網站移轉到合作夥伴中心之相關常見問題的解答。
author: anbene
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 05/19/2020
ms.author: mingshen
ms.openlocfilehash: c4fdde94ea703d194e2de27a8df429f62ea374f4
ms.sourcegitcommit: 6fd8dbeee587fd7633571dfea46424f3c7e65169
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/21/2020
ms.locfileid: "83727476"
---
# <a name="common-questions-about-migrating-from-the-cloud-partner-portal-to-partner-center"></a>從 Cloud Partner 入口網站移轉到合作夥伴中心的相關常見問題

此文章會針對將供應項目從 Cloud Partner 入口網站移轉到合作夥伴中心的相關常見問題提供解答。

## <a name="what-does-offer-migration-mean"></a>供應項目移轉是什麼意思？

我們會將您的供應項目資料從 Cloud Partner 入口網站移轉到合作夥伴中心，而合作夥伴中心在供應項目的發佈及管理體驗上將有所變更。

| 區域  | 變更  |
|-------|----------|
| **發佈及供應項目管理體驗** | 您在合作夥伴中心將能透過直覺的介面獲得改善的使用者體驗。 如需詳細資料，請參閱[合作夥伴中心和 Cloud Partner 入口網站之間有何差異？](#what-are-the-differences-between-partner-center-and-the-cloud-partner-portal) |
| **您供應項目在市集中的可用性** | 無變更。 如果您的供應項目已在市集中上線，其將會在移轉期間及移轉完成後繼續上線。 |
| **新購買和部署** | 無變更。 您的客戶將能繼續購買及部署您的供應項目，而不會有任何中斷。 |
| **支出** | 在移轉期間及移轉後發生的任何購買及部署，都將會繼續以一般方式向您付款。 |
|**與現有 [Cloud Partner 入口網站 API](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-api-overview) \(部分機器翻譯\) 的 API 整合** | 在移轉後將會繼續支援現有的 Cloud Partner 入口網站 API，且您現有的整合將能繼續運作。 如需詳細資料，請參閱[在移轉後是否會繼續支援 Cloud Partner 入口網站 REST API？](#will-the-cloud-partner-portal-rest-apis-be-supported-post-migration) |
| | |

## <a name="can-i-still-access-the-cloud-partner-portal-and-manage-my-offers-during-migration"></a>我在移轉期間是否仍然可以存取 Cloud Partner 入口網站並管理我的供應項目？

所有供應項目皆已移轉到合作夥伴中心。 每個供應項目的移轉期間應該都會少於 24 小時。 在您的供應項目移轉完成後，您應該會收到電子郵件通知。

在您的供應項目移轉完成後，其在 Cloud Partner 入口網站中將會於**一段有限的時間內**處於唯讀模式。 其狀態將會顯示「已移至合作夥伴中心」，並包含您供應項目於合作夥伴中心的連結，如下列螢幕擷取畫面所示。 從此之後，您將完全透過合作夥伴中心管理所有供應項目的更新，或是建立新的供應項目。

:::image type="content" source="media/migration-faq/all-offers-2.png" alt-text="顯示系統針對已移轉到合作夥伴中心的供應項目提供的訊息":::

:::image type="content" source="media/migration-faq/offer-has-moved.png" alt-text="顯示已移轉供應項目的 Cloud Partner 入口網站頁面。":::

## <a name="how-will-i-create-new-offers"></a>我要如何建立新的供應項目？

從 Cloud Partner 入口網站，系統將會引導前往合作夥伴中心建立新的供應項目

:::image type="content" source="media/migration-faq/create-new-offer-1.png" alt-text="顯示 Cloud Partner 入口網站建立新供應項目的功能表":::

您在選取新的供應項目之後便會看到一則訊息，如下所示。

:::image type="content" source="media/migration-faq/create-new-offer-2.png" alt-text="顯示在 CPP 中建立新供應項目時所接收到的訊息":::

## <a name="do-i-need-to-create-a-new-account-to-manage-offers-in-partner-center"></a>我是否需要建立新帳戶以在合作夥伴中心管理供應項目？

否。 系統會保留您的基礎帳戶，且您應該已經能在合作夥伴中心加以管理。 這表示如果您是現有的合作夥伴，便可以在移轉後使用現有的 Cloud Partner 入口網站帳戶認證來登入合作夥伴中心。 只有供應項目和相關的管理體驗會從 Cloud Partner 入口網站移至合作夥伴中心。 請不要建立任何新的帳戶，因為您的供應項目將不會與新帳戶建立關聯。

## <a name="i-see-a-message-in-the-cloud-partner-portal-to-activate-my-account-what-does-this-mean"></a>我在 Cloud Partner 入口網站中看見啟用帳戶的訊息，這代表什麼？

我們需要您提供一些額外的詳細資料，以適當地將您的帳戶移轉到合作夥伴中心，並讓您在移轉完成後可以於合作夥伴中心管理您的供應項目。 如需帳戶啟用的詳細資料，請參閱[將帳戶從 Cloud Partner 入口網站移轉到合作夥伴中心](https://docs.microsoft.com/azure/marketplace/partner-center-portal/account-migration-from-cpp-to-pc)。

完成帳戶啟用的必要步驟會根據您的帳戶角色而有所不同。

| 帳戶角色 | 啟用步驟 |
|--------------|----------------|
|擁有者 | 移至 Cloud Partner 入口網站的[發行者設定檔](https://cloudpartner.azure.com/#profile)頁面，然後按一下橫幅中的連結以啟用。 系統會將您重新導向到合作夥伴中心以完成帳戶啟用。 |
| 參與者 | 帳戶中只有具有擁有者角色的使用者才可以啟用帳戶。 請連絡您的帳戶擁有者以完成帳戶啟用。 系統應該會在橫幅訊息中列出您的帳戶擁有者。 |
| | |

## <a name="im-having-trouble-logging-in-to-my-account-and-opening-a-support-ticket"></a>我無法登入我的帳戶並想要建立支援票證

如果您無法登入帳戶，則可以建立[支援票證](https://partner.microsoft.com/support/v2/?stage=1)。

## <a name="where-can-i-find-documentation-on-the-new-partner-center-publishing-experience"></a>我在哪裡可以找到新合作夥伴中心發佈體驗的相關文件？

請移至[商業市集文件](https://docs.microsoft.com/azure/marketplace/)。 然後展開 [合作夥伴中心中的商業市集]  > [建立新的供應項目]，以查看建立每種供應項目類型的說明主題。

:::image type="content" source="media/migration-faq/marketplace-help-topics.png" alt-text="顯示適用於合作夥伴中心的說明主題":::

### <a name="what-are-the-differences-between-partner-center-and-the-cloud-partner-portal"></a>合作夥伴中心和 Cloud Partner 入口網站之間有何差異？

您可能會在 Cloud Partner 入口網站和合作夥伴中心之間注意到下列差異。

### <a name="modular-publishing-capabilities"></a>模組化發佈功能

合作夥伴中心提供模組化的發佈選項，可讓您選取想要發佈的變更，而非一律同時發佈所有更新。 例如，下列螢幕擷取畫面顯示所選取要發佈的變更只有針對 [屬性] 和 [供應項目清單] 的變更。

:::image type="content" source="media/migration-faq/review-and-publish-migration.png" alt-text="顯示 [檢閱並發佈] 頁面":::

您未發佈的更新會儲存為草稿。 繼續使用您的供應項目預覽，以在將供應項目公開上線之前加以驗證。

### <a name="rich-text-format"></a>RTF 格式

在 [供應項目清單] 和 [計劃清單] 頁面上，使用 RTF 編輯器來強化您的供應項目及計劃描述。

:::image type="content" source="media/migration-faq/rich-text-editor-migration.png" alt-text="顯示 RTF 編輯器":::

### <a name="enhanced-preview-options"></a>強化的預覽選項

合作夥伴中心包含[比較功能](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#compare-changes-to-marketplace-offers) \(部分機器翻譯\)，其具備改善的篩選選項。 這可讓您比較供應項目的預覽及上線版本。

:::image type="content" source="media/migration-faq/enhanced-preview.png" alt-text="顯示比較功能":::

### <a name="branding-and-navigation-changes"></a>商標與導覽變更

您將會注意到一些商標上的變更。 例如，"SKU" 在合作夥伴中心會稱為「計劃」。

:::image type="content" source="media/migration-faq/plan-overview-migration.png" alt-text="顯示 [計劃概觀]。":::

此外，您先前在 Cloud Partner 入口網站的 [市集] 或 [店面詳細資料] \([諮詢服務]，Power BI 應用程式\) 頁面中所提供的資訊，現在已收集到合作夥伴中心的 [供應項目清單] 頁面中。

:::image type="content" source="media/migration-faq/offer-listing-migration.png" alt-text="顯示 [供應項目清單] 頁面。":::

您先前在 Cloud Partner 入口網站的單一頁面中針對 SKU 所提供的資訊，現在可能會收集到合作夥伴中心的數個頁面上：

* [計劃設定] 頁面
* [計劃清單] 頁面
* 計劃 [可用性] 頁面
* 計劃 [技術設定] 頁面，如此螢幕擷取畫面所示。

:::image type="content" source="media/migration-faq/tech-config-migration.png" alt-text="顯示計劃 [技術設定] 頁面。":::

您的 [供應項目識別碼] 現在會顯示在供應項目的左側導覽列上。

:::image type="content" source="media/migration-faq/offer-id-offer-overview.png" alt-text="顯示具有 [供應項目識別碼] 的左側導覽功能表。":::

### <a name="stop-selling-an-offer"></a>停止銷售供應項目

您可以直接從合作夥伴中心入口網站要求[停止在市集上銷售某個供應項目](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#stop-selling-an-offer-or-plan) \(部分機器翻譯\)。 該選項會在您供應項目的 [供應項目概觀] 頁面上提供。

:::image type="content" source="media/migration-faq/stop-selling-migration.png" alt-text="顯示具有 [停止銷售] 選項的 [供應項目概觀] 頁面。":::

## <a name="what-pages-in-partner-center-correspond-to-pages-i-used-in-the-cloud-partner-portal"></a>我在 Cloud Partner 入口網站中使用的頁面會對應到合作夥伴中心的哪些頁面？

下表顯示兩個入口網站之間的對應連結。

| 頁面 | Cloud Partner 入口網站連結 | 合作夥伴中心連結 |
|------|---------------------------|---------------------|
| **所有供應項目分頁** | https://cloudpartner.azure.com/#alloffers | https://partner.microsoft.com/dashboard/commercial-marketplace/overview |
| **所有發行者分頁** | https://cloudpartner.azure.com/#publishers | https://partner.microsoft.com/dashboard/account/v3/publishers/list |
| **發行者設定檔** | https://cloudpartner.azure.com/#profile | https://partner.microsoft.com/dashboard/account/management |
| **使用者分頁** | https://cloudpartner.azure.com/#users | https://partner.microsoft.com/dashboard/account/usermanagement |
| **記錄頁面** | https://cloudpartner.azure.com/#history | 合作夥伴中心尚未支援 [記錄] 功能。 |
| **見解儀表板** | https://cloudpartner.azure.com/#insights | https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary |
| **支出報告** | https://cloudpartner.azure.com/#insights/payout | https://partner.microsoft.com/dashboard/payouts/reports/incentivepayments |
| | |

## <a name="will-the-cloud-partner-portal-rest-apis-be-supported-post-migration"></a>在移轉後是否會繼續支援 Cloud Partner 入口網站 REST API？

Cloud Partner 入口網站 API 已和合作夥伴中心整合，並會在將供應項目移轉到合作夥伴中心後繼續運作。 該整合會引進些微的變更。 請檢閱下表中的變更，以確保您的程式碼能在移轉到合作夥伴中心後繼續運作。

| **API** | **變更描述** | **影響** |
| ------- | ---------------------- | ---------- |
| POST Publish、GoLive、Cancel | 針對已移轉的供應項目，回應標頭將會有不同的格式，但會繼續以相同的方式運作，表示用來擷取作業狀態的相對路徑。 | 針對供應項目傳送任何對應的 POST 要求時，視該供應項目的移轉狀態而定，位置標頭將會具有兩種格式的其中一種：<ul><li>未移轉的供應項目<br>`/api/operations/{PublisherId}${offerId}$2$preview?api-version=2017-10-31`</li><li>已移轉的供應項目<br>`/api/publishers/{PublisherId}/offers/{offereId}/operations/408a4835-0000-1000-0000-000000000000?api-version=2017-10-31`</li> |
| GET 作業 | 針對先前在回應中支援 'notification-email' 欄位的供應項目，此欄位將會淘汰且不再針對已移轉的供應項目傳回。 | 針對已移轉的供應項目，我們將不會繼續針對要求中所指定的電子郵件清單傳送通知。 相反地，API 服務將會對應到合作夥伴中心的通知電子郵件程序以傳送電子郵件。 具體而言，系統會將通知傳送到您在合作夥伴中心 [帳戶設定] 的 [賣方連絡資訊] 區段中所設定的電子郵件地址，以向您通知作業進度。<br><br>請檢閱在合作夥伴中心 [[帳戶設定](https://partner.microsoft.com/dashboard/account/management)] 的 [賣方連絡資訊] 區段中所設定的電子郵件地址，以確保您已針對通知提供正確的電子郵件。  |
| | | |
