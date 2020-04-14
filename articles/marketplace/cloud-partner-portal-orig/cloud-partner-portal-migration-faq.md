---
title: 有關遷移到合作夥伴中心的常見問題 |Azure 應用商店
description: 本文解決了有關產品/服務從雲合作夥伴門戶遷移到合作夥伴中心的常見問題。
author: anbene
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/10/2020
ms.author: mingshen
ms.openlocfilehash: 672153eba4aa2b739b67694f939c4796b39ac4c6
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/14/2020
ms.locfileid: "81274376"
---
# <a name="frequently-asked-questions-for-migrating-from-the-cloud-partner-portal-to-partner-center"></a>從雲合作夥伴門戶遷移到合作夥伴中心的常見問題

本文解決了有關產品/服務從雲合作夥伴門戶遷移到合作夥伴中心的常見問題。

## <a name="what-does-offer-migration-mean"></a>提供遷移意味著什麼?

我們將您的產品/服務數據從雲合作夥伴門戶移動到合作夥伴中心,並更改產品/服務發佈和管理經驗。

| 區域  | 變更  |
|-------|----------|
| **發佈並提供管理經驗** | 合作夥伴中心中的直觀介面將改善用戶體驗。 有關詳細資訊,請參閱[合作夥伴中心和雲合作夥伴門戶之間的區別?](#what-are-the-differences-between-partner-center-and-the-cloud-partner-portal) |
| **您的產品/服務在市場上可用性** | 無變更。 如果您的優惠是活在市場中,它將在遷移完成期間和之後繼續直播。 |
| **新的購買和部署** | 無變更。 您的客戶將繼續能夠購買和部署您的產品/服務,而不會中斷。 |
| **支出** | 遷移期間或之後發生的任何購買和部署將繼續照常支付給您。 |
|**API 整合現有[雲端合作夥伴門戶 API](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-api-overview)** | 遷移后,將繼續支援現有雲合作夥伴門戶 API,並且您的現有集成將繼續工作。 有關詳細資訊,請參閱[遷移后雲合作夥伴門戶 REST API 是否受支援?](#will-the-cloud-partner-portal-rest-apis-be-supported-post-migration) |
| | |

## <a name="can-i-still-access-the-cloud-partner-portal-and-manage-my-offers-during-migration"></a>我能否在遷移期間訪問雲合作夥伴門戶並管理我的優惠?

優惠將於 4 月 13 日起從 5 月 13 日起遷移到合作夥伴中心。 在此期間,您可以像往常一樣訪問雲合作夥伴門戶,但您提供的時間安排進行遷移的時間除外。
遷移優惠時,其狀態為"鎖定 - 移動到合作夥伴中心",如下圖所示。 此遷移期應小於 24 小時。 在此期間,您將無法對優惠進行任何更新。 在我們完成您的優惠遷移後,您將收到電子郵件通知。

:::image type="content" source="media/migration-faq/all-offers-1.png" alt-text="說明遷移的優惠狀態。":::

遷移產品/服務后,將在雲合作夥伴門戶中處於唯讀模式,時間**有限**。 其狀態將顯示「移動到合作夥伴中心」,並在合作夥伴中心中包含指向您的優惠的連結,如以下螢幕截圖所示。 從此時起,您將管理所有優惠的更新,或通過合作夥伴中心創建新的優惠,

:::image type="content" source="media/migration-faq/all-offers-2.png" alt-text="說明為已遷移到合作夥伴中心的報價提供的消息":::

:::image type="content" source="media/migration-faq/offer-has-moved.png" alt-text="演示遷移產品的雲合作夥伴門戶頁面。":::

## <a name="how-will-i-create-new-offers"></a>如何創建新優惠?

從雲合作夥伴門戶,您將被引導在合作夥伴中心創建新優惠

:::image type="content" source="media/migration-faq/create-new-offer-1.png" alt-text="說明在雲合作夥伴門戶中創建新產品/服務功能表":::

選擇新產品/服務后,您將看到一條消息,如下所示。

:::image type="content" source="media/migration-faq/create-new-offer-2.png" alt-text="說明在 CPP 中建立新產品/服務時收到的消息":::

## <a name="do-i-need-to-create-a-new-account-to-manage-offers-in-partner-center"></a>我需要創建新帳戶來管理合作夥伴中心的報價嗎?

否。 您的基礎帳戶將被保留,您應該已經在合作夥伴中心管理它。 這意味著,如果您是現有合作夥伴,則可以使用現有的雲合作夥伴門戶帳戶憑據在遷移後登錄合作夥伴中心。 只有優惠和相關管理體驗從雲合作夥伴門戶遷移到合作夥伴中心。 我們要求您不要創建任何新帳戶,因為您的優惠不會與新帳戶關聯。

## <a name="i-see-a-message-in-the-cloud-partner-portal-to-activate-my-account-what-does-this-mean"></a>我在雲合作夥伴門戶中看到一條消息以啟動我的帳戶,這意味著什麼?

我們需要您提供更多詳細資訊,以便將您的帳戶正確遷移到合作夥伴中心,並使您能夠在產品/服務遷移完成後在合作夥伴中心管理您的優惠。 有關帳戶啟動的更多詳細資訊,請參閱[從雲合作夥伴門戶向合作夥伴中心進行帳戶遷移](https://docs.microsoft.com/azure/marketplace/partner-center-portal/account-migration-from-cpp-to-pc)。

完成帳戶啟動所需的步驟因您的帳戶角色而異。

| 帳戶角色 | 啟用步驟 |
|--------------|----------------|
|擁有者 | 轉到雲合作夥伴門戶中的[發佈伺服器配置文件](https://cloudpartner.azure.com/#profile)頁面,然後單擊橫幅中的連結以啟動。 您將被重定向到合作夥伴中心以完成帳戶啟動。 |
| 參與者 | 只有具有擁有者角色的帳戶中的使用者可以啟動該帳戶。 與您的帳戶所有者聯繫以完成帳戶啟動。 您的帳戶擁有者應列在橫幅消息中。 |
| | |

## <a name="im-having-trouble-logging-in-to-my-account-and-opening-a-support-ticket"></a>登錄我的帳戶和打開支援票證時遇到問題

如果您無法登入您的帳號,您可以開啟[支援票證](https://partner.microsoft.com/support/v2/?stage=1)。

## <a name="where-can-i-find-documentation-on-the-new-partner-center-publishing-experience"></a>在哪裡可以找到有關新合作夥伴中心發佈體驗的文檔?

跳到[商業市場文件](https://docs.microsoft.com/azure/marketplace/)。 然後  > 展開**合作夥伴中心中的商業市場門戶****創建新產品/服務**以查看創建每種產品/服務的幫助主題。

:::image type="content" source="media/migration-faq/marketplace-help-topics.png" alt-text="展示合作夥伴中心的說明主題":::

### <a name="what-are-the-differences-between-partner-center-and-the-cloud-partner-portal"></a>合作夥伴中心和雲合作夥伴門戶之間的區別是什麼?

您可能會注意到雲端合作夥伴門戶和合作夥伴中心之間的以下差異。

### <a name="modular-publishing-capabilities"></a>模組化發佈功能

合作夥伴中心提供了一個模組化發佈選項,允許您選擇要發表的更改,而不是始終一次發表所有更新。 例如,以下螢幕截圖顯示,選擇要發佈的唯一更改**是屬性和****要約清單的**更改。

:::image type="content" source="media/migration-faq/review-and-publish-migration.png" alt-text="說明預覽和發佈頁面":::

不發佈的更新將另存為草稿。 繼續使用您的優惠預覽,以驗證您的報價,然後再向公眾直播。

### <a name="rich-text-format"></a>富文字格式

使用「優惠清單」和「計劃清單」頁上的「豐富文字編輯器」增強您的優惠和計畫說明。

:::image type="content" source="media/migration-faq/rich-text-editor-migration.png" alt-text="說明富文字編輯器":::

### <a name="enhanced-preview-options"></a>增強的預覽選項

合作夥伴中心包括一個[比較功能](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#compare-changes-to-marketplace-offers)和改進的篩選選項。 這使您可以與優惠的預覽版和即時版本進行比較。

:::image type="content" source="media/migration-faq/enhanced-preview.png" alt-text="說明比較功能":::

### <a name="branding-and-navigation-changes"></a>品牌和導覽變更

您會注意到一些品牌更改。 例如,"SKU"在合作夥伴中心中稱為"計劃"。

:::image type="content" source="media/migration-faq/plan-overview-migration.png" alt-text="說明計劃概述。":::

此外,您用於在雲合作夥伴門戶中的 **「應用商店**」或「S**翻頁詳細資訊**」(諮詢服務、Power BI 應用)頁面提供的資訊,在合作夥伴中心的產品 **/服務列表**頁中收集。

:::image type="content" source="media/migration-faq/offer-listing-migration.png" alt-text="說明產品/服務清單頁。":::

您用於在雲端合作夥伴門戶中的單個頁面中為 SKU 提供的資訊現在可能會在合作夥伴中心中的多個頁面中收集:

* 計畫設定頁面
* 排程清單頁
* 排程可用性頁面
* 規劃技術配置頁面,如此螢幕截圖所示。

:::image type="content" source="media/migration-faq/tech-config-migration.png" alt-text="說明計劃技術配置頁面。":::

您的優惠 ID 現在顯示在優惠的左導航欄上。

:::image type="content" source="media/migration-faq/offer-id-offer-overview.png" alt-text="使用產品/服務 ID 說明左側導航功能表。":::

### <a name="stop-selling-an-offer"></a>停止銷售要約

您可以直接從合作夥伴中心門戶請求[停止在市場上銷售產品/服務](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#stop-selling-an-offer-or-plan)。 該選項可在優惠**優惠概覽**頁面上使用。

:::image type="content" source="media/migration-faq/stop-selling-migration.png" alt-text="使用止損選項說明"產品/服務概述"頁面。":::

## <a name="what-pages-in-partner-center-correspond-to-pages-i-used-in-the-cloud-partner-portal"></a>合作夥伴中心中的哪些頁面對應於我在雲合作夥伴門戶中使用的頁面?

下表顯示了兩個門戶之間的相應連結。

| 頁面 | 雲合作夥伴門戶連結 | 合作夥伴中心連結 |
|------|---------------------------|---------------------|
| **所有供應項目頁面** | https://cloudpartner.azure.com/#alloffers | https://partner.microsoft.com/dashboard/commercial-marketplace/overview |
| **所有發行者頁面** | https://cloudpartner.azure.com/#publishers | https://partner.microsoft.com/dashboard/account/v3/publishers/list |
| **發佈者設定檔** | https://cloudpartner.azure.com/#profile | https://partner.microsoft.com/dashboard/account/management |
| **使用者分頁** | https://cloudpartner.azure.com/#users | https://partner.microsoft.com/dashboard/account/usermanagement |
| **歷史記錄頁** | https://cloudpartner.azure.com/#history | 合作夥伴中心中尚不支援歷史記錄。 |
| **見解儀錶板** | https://cloudpartner.azure.com/#insights | https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary |
| **付款報告** | https://cloudpartner.azure.com/#insights/payout | https://partner.microsoft.com/dashboard/payouts/reports/incentivepayments |
| | |

## <a name="will-the-cloud-partner-portal-rest-apis-be-supported-post-migration"></a>遷移后雲合作夥伴門戶 REST API 是否會得到支援?

雲合作夥伴門戶 API 與合作夥伴中心集成,在您的產品/服務遷移到合作夥伴中心後將繼續工作。 集成引入了小更改。 查看下表中的更改,以確保代碼在遷移到合作夥伴中心後繼續工作。

| **API** | **變更描述** | **影響** |
| ------- | ---------------------- | ---------- |
| POST 發佈、GoLive、取消 | 對於遷移的要約,回應標頭將具有不同的格式,但將繼續以相同的方式工作,表示檢索操作狀態的相對路徑。 | 送出任何相應的 POST 請求為產品/服務時,位置標頭將具有兩種格式之一,具體取決於產品/服務的狀態:<ul><li>非遷移優惠<br>`/api/operations/{PublisherId}${offerId}$2$preview?api-version=2017-10-31`</li><li>遷移優惠<br>`/api/publishers/{PublisherId}/offers/{offereId}/operations/408a4835-0000-1000-0000-000000000000?api-version=2017-10-31`</li> |
| 取得動作 | 對於回應中以前支援"通知電子郵件"欄位的優惠,此欄位將被棄用,並且不再為遷移的優惠返回。 | 對於遷移的優惠,我們將不再向請求中指定的電子郵件清單發送通知。 相反,API 服務將與合作夥伴中心中的通知電子郵件進程一致發送電子郵件。 具體來說,通知將發送到合作夥伴中心中帳戶設置的賣家聯繫資訊部分中設置的電子郵寄地址,以通知您操作進度。<br><br>查看合作夥伴中心[「帳戶」設置](https://partner.microsoft.com/dashboard/account/management)中的「賣家聯繫資訊」部分中設置的電子郵件位址,以確保為通知提供正確的電子郵件。  |
| | | |
