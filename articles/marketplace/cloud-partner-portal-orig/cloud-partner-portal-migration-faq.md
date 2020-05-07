---
title: 關於遷移至合作夥伴中心的常見問題 |Azure Marketplace
description: 本文說明將供應專案從 Cloud Partner 入口網站遷移至合作夥伴中心的常見問題。
author: anbene
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/10/2020
ms.author: mingshen
ms.openlocfilehash: 672153eba4aa2b739b67694f939c4796b39ac4c6
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/05/2020
ms.locfileid: "81274376"
---
# <a name="frequently-asked-questions-for-migrating-from-the-cloud-partner-portal-to-partner-center"></a>從 Cloud Partner 入口網站遷移至合作夥伴中心的常見問題

本文說明將供應專案從 Cloud Partner 入口網站遷移至合作夥伴中心的常見問題。

## <a name="what-does-offer-migration-mean"></a>提供遷移的意義為何？

我們會將您的供應專案資料從 Cloud Partner 入口網站移至合作夥伴中心，並在供應專案發佈和管理體驗中有所變更。

| 區域  | 變更  |
|-------|----------|
| **發佈和提供管理體驗** | 您將在合作夥伴中心使用直覺介面，獲得更好的使用者體驗。 如需詳細資訊，請參閱[合作夥伴中心與 Cloud Partner 入口網站之間的差異為何？](#what-are-the-differences-between-partner-center-and-the-cloud-partner-portal) |
| **您在 marketplace 中提供的供應專案** | 無變更。 如果您的供應專案在 marketplace 中上線，則在完成遷移的期間和之後，將會繼續上線。 |
| **新購買和部署** | 無變更。 您的客戶將可繼續購買及部署您的供應專案，而不會有任何中斷。 |
| **支出** | 在遷移期間或之後發生的任何購買和部署，都將繼續以一般方式向您收費。 |
|**與現有[Cloud Partner 入口網站 api](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-api-overview)整合的 api** | 在遷移之後，將會繼續支援現有的 Cloud Partner 入口網站 Api，而您現有的整合仍會繼續工作。 如需詳細資訊，請參閱[遷移後是否支援 CLOUD PARTNER 入口網站 REST api？](#will-the-cloud-partner-portal-rest-apis-be-supported-post-migration) |
| | |

## <a name="can-i-still-access-the-cloud-partner-portal-and-manage-my-offers-during-migration"></a>我仍然可以在遷移期間存取 Cloud Partner 入口網站並管理我的供應專案嗎？

供應專案將于5月13日起，遷移至合作夥伴中心。 在這段期間，您將能夠如往常般存取 Cloud Partner 入口網站，但您所提供的時間已排程進行遷移。
當您的供應專案正在進行遷移時，其狀態會是「已鎖定–移至合作夥伴中心」，如下列螢幕擷取畫面所示。 此遷移期間應小於24小時。 在這段期間，您將無法對供應專案進行任何更新。 當我們完成您的供應專案遷移之後，您會收到電子郵件通知。

:::image type="content" source="media/migration-faq/all-offers-1.png" alt-text="說明遷移供應專案的狀態。":::

在您的供應專案遷移完成後，就會在 Cloud Partner 入口網站中的**一段有限時間**內處於唯讀模式。 其狀態會顯示「已移至合作夥伴中心」，並在合作夥伴中心包含您供應專案的連結，如下列螢幕擷取畫面所示。 從這裡開始，您將會管理所有供應專案的更新，或透過合作夥伴中心專門建立新的供應專案。

:::image type="content" source="media/migration-faq/all-offers-2.png" alt-text="說明為已遷移至合作夥伴中心的供應專案提供的訊息":::

:::image type="content" source="media/migration-faq/offer-has-moved.png" alt-text="說明已遷移供應專案的 Cloud Partner 入口網站頁面。":::

## <a name="how-will-i-create-new-offers"></a>如何建立新的供應專案？

從 Cloud Partner 入口網站，系統會將您導向在合作夥伴中心建立新的供應專案

:::image type="content" source="media/migration-faq/create-new-offer-1.png" alt-text="說明用來在 Cloud Partner 入口網站中建立新供應專案的功能表":::

選取新的供應專案之後，您會看到一則訊息，如下所示。

:::image type="content" source="media/migration-faq/create-new-offer-2.png" alt-text="說明在 CPP 中建立新供應專案時所收到的訊息":::

## <a name="do-i-need-to-create-a-new-account-to-manage-offers-in-partner-center"></a>我是否需要在合作夥伴中心內建立新的帳戶來管理供應專案？

不需要。 您的基礎帳戶將會保留，而且您應該已經在合作夥伴中心進行管理。 這表示如果您是現有的合作夥伴，您可以使用現有的 Cloud Partner 入口網站帳號憑證來登入合作夥伴中心後置遷移。 只有供應專案和相關聯的管理體驗會從 Cloud Partner 入口網站移至合作夥伴中心。 我們要求您不要建立任何新帳戶，因為您的供應專案將不會與新帳戶相關聯。

## <a name="i-see-a-message-in-the-cloud-partner-portal-to-activate-my-account-what-does-this-mean"></a>我在 Cloud Partner 入口網站中看到一則用來啟用我的帳戶的訊息，這代表什麼意思？

我們需要您更多詳細資料，才能正確地將您的帳戶遷移至合作夥伴中心，並可讓您在供應專案遷移完成後，于合作夥伴中心管理您的優惠。 如需帳戶啟用的詳細資訊，請參閱[從 Cloud Partner 入口網站到合作夥伴中心的帳戶遷移](https://docs.microsoft.com/azure/marketplace/partner-center-portal/account-migration-from-cpp-to-pc)。

完成帳戶啟用所需的步驟會根據您的帳戶角色而有所不同。

| 帳戶角色 | 啟用步驟 |
|--------------|----------------|
|擁有者 | 移至 Cloud Partner 入口網站中的 [[發行者設定檔]](https://cloudpartner.azure.com/#profile)頁面，然後按一下橫幅中的連結以啟用。 系統會將您重新導向至合作夥伴中心，以完成帳戶啟用。 |
| 參與者 | 只有具有「擁有者」角色之帳戶中的使用者可以啟動帳戶。 請洽詢您的帳戶擁有者，以完成帳戶啟用。 您的帳戶擁有者應該會列在橫幅訊息中。 |
| | |

## <a name="im-having-trouble-logging-in-to-my-account-and-opening-a-support-ticket"></a>我無法登入我的帳戶並開啟支援票證

如果您無法登入您的帳戶，您可以開啟[支援票證](https://partner.microsoft.com/support/v2/?stage=1)。

## <a name="where-can-i-find-documentation-on-the-new-partner-center-publishing-experience"></a>哪裡可以找到有關新合作夥伴中心發佈經驗的檔？

前往[商業 marketplace 檔](https://docs.microsoft.com/azure/marketplace/)。 然後展開 [合作夥伴中心  > ] 中的 [**商業 Marketplace 入口網站**]，**建立新的供應**專案，以查看建立每種供應專案類型的說明主題。

:::image type="content" source="media/migration-faq/marketplace-help-topics.png" alt-text="說明合作夥伴中心的說明主題":::

### <a name="what-are-the-differences-between-partner-center-and-the-cloud-partner-portal"></a>合作夥伴中心與 Cloud Partner 入口網站之間有何差異？

您可能會注意到 Cloud Partner 入口網站和合作夥伴中心之間有下列差異。

### <a name="modular-publishing-capabilities"></a>模組化發行功能

合作夥伴中心提供模組化發行選項，可讓您選取要發佈的變更，而不是一律同時發佈所有更新。 例如，下列螢幕擷取畫面顯示選取要發佈的唯一變更是**屬性**和**供應專案清單**的變更。

:::image type="content" source="media/migration-faq/review-and-publish-migration.png" alt-text="說明 [預覽] 和 [發佈] 頁面":::

您未發佈的更新會儲存為草稿。 請先繼續使用您的供應專案預覽來驗證您的供應專案，再將其上線到公眾。

### <a name="rich-text-format"></a>Rtf 格式

使用 [供應專案清單] 和 [計畫清單] 頁面上的 Rtf 編輯器，增強您的供應專案和方案描述。

:::image type="content" source="media/migration-faq/rich-text-editor-migration.png" alt-text="說明 rtf 編輯器":::

### <a name="enhanced-preview-options"></a>增強的預覽選項

合作夥伴中心包含改良的篩選選項的[比較功能](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#compare-changes-to-marketplace-offers)。 這讓您能夠與供應專案的預覽和即時版本進行比較。

:::image type="content" source="media/migration-faq/enhanced-preview.png" alt-text="說明比較功能":::

### <a name="branding-and-navigation-changes"></a>品牌和導覽變更

您會注意到一些商標變更。 例如，「Sku」在合作夥伴中心的品牌為「方案」。

:::image type="content" source="media/migration-faq/plan-overview-migration.png" alt-text="說明計畫的總覽。":::

此外，您在 [ **Marketplace** ] 或 Cloud Partner 入口網站 [**torefront 詳細資料**] （諮詢服務、Power BI 應用程式）中用來提供的資訊，會在 [合作夥伴中心] 的 [**供應專案清單**] 頁面中收集。

:::image type="content" source="media/migration-faq/offer-listing-migration.png" alt-text="說明供應專案清單頁面。":::

您在 Cloud Partner 入口網站的單一頁面中提供的 Sku 所用的資訊，現在可能會在合作夥伴中心的數個頁面中收集：

* 方案設定頁面
* 計畫清單頁面
* 方案可用性頁面
* [規劃技術設定] 頁面，如下列螢幕擷取畫面所示。

:::image type="content" source="media/migration-faq/tech-config-migration.png" alt-text="說明 [方案技術設定] 頁面。":::

您的供應專案識別碼現在會顯示在供應專案的左側導覽列上。

:::image type="content" source="media/migration-faq/offer-id-offer-overview.png" alt-text="說明具有供應專案識別碼的左側導覽功能表。":::

### <a name="stop-selling-an-offer"></a>停止銷售供應專案

您可以要求在 marketplace 上直接從合作夥伴中心入口網站[停止銷售供應](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#stop-selling-an-offer-or-plan)專案。 您供應專案的供應專案**總覽**頁面上有提供此選項。

:::image type="content" source="media/migration-faq/stop-selling-migration.png" alt-text="說明 [供應專案總覽] 頁面與 [停止銷售] 選項。":::

## <a name="what-pages-in-partner-center-correspond-to-pages-i-used-in-the-cloud-partner-portal"></a>合作夥伴中心的哪些頁面會對應到我在 Cloud Partner 入口網站中使用的頁面？

下表顯示兩個入口網站之間的對應連結。

| 頁面 | Cloud Partner 入口網站連結 | 合作夥伴中心連結 |
|------|---------------------------|---------------------|
| **所有供應項目頁面** | https://cloudpartner.azure.com/#alloffers | https://partner.microsoft.com/dashboard/commercial-marketplace/overview |
| **所有發行者頁面** | https://cloudpartner.azure.com/#publishers | https://partner.microsoft.com/dashboard/account/v3/publishers/list |
| **發行者設定檔** | https://cloudpartner.azure.com/#profile | https://partner.microsoft.com/dashboard/account/management |
| **使用者分頁** | https://cloudpartner.azure.com/#users | https://partner.microsoft.com/dashboard/account/usermanagement |
| **歷程記錄頁面** | https://cloudpartner.azure.com/#history | 合作夥伴中心尚不支援歷程記錄功能。 |
| **深入資訊儀表板** | https://cloudpartner.azure.com/#insights | https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary |
| **支出報表** | https://cloudpartner.azure.com/#insights/payout | https://partner.microsoft.com/dashboard/payouts/reports/incentivepayments |
| | |

## <a name="will-the-cloud-partner-portal-rest-apis-be-supported-post-migration"></a>遷移後是否支援 Cloud Partner 入口網站 REST Api？

Cloud Partner 入口網站 Api 會與合作夥伴中心整合，並會在您的供應專案遷移至合作夥伴中心後繼續工作。 整合引進了微小的變更。 請檢查下表中的變更，以確保您的程式碼在遷移至合作夥伴中心後仍可繼續運作。

| **API** | **變更描述** | **影響** |
| ------- | ---------------------- | ---------- |
| 發行後，GoLive，取消 | 對於遷移的供應專案，回應標頭會有不同的格式，但會以相同的方式繼續運作，以表示抓取作業狀態的相對路徑。 | 傳送供應專案的任何對應 POST 要求時，位置標頭會有兩種格式的其中一種，視供應專案的遷移狀態而定：<ul><li>非遷移供應專案<br>`/api/operations/{PublisherId}${offerId}$2$preview?api-version=2017-10-31`</li><li>遷移的供應專案<br>`/api/publishers/{PublisherId}/offers/{offereId}/operations/408a4835-0000-1000-0000-000000000000?api-version=2017-10-31`</li> |
| 取得作業 | 對於先前在回應中支援「通知-電子郵件」欄位的供應專案，此欄位將會被取代，且不會再傳回給遷移的供應專案。 | 針對遷移的供應專案，我們不會再將通知傳送至要求中指定的電子郵件清單。 相反地，API 服務會與合作夥伴中心傳送電子郵件的通知電子郵件進程一致。 具體而言，通知會傳送到您在合作夥伴中心的帳戶設定的 [賣方連絡人資訊] 區段中設定的電子郵件地址，以通知您操作進度。<br><br>在合作夥伴中心的 [[帳戶設定](https://partner.microsoft.com/dashboard/account/management)] 中，檢查 [賣方連絡人資訊] 區段中設定的電子郵件地址，以確保提供正確的電子郵件給通知。  |
| | | |
