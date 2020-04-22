---
title: 設定客戶潛在客戶 |Azure 應用商店
description: 在商業市場中配置客戶潛在客戶。
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: dsindona
ms.openlocfilehash: a1ec89dfd2dda91a10f2cc00b6ca4d9d7abbf032
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/21/2020
ms.locfileid: "81731148"
---
# <a name="customer-leads-from-your-marketplace-offer"></a>來自您的市集供應項目的潛在客戶

潛在顧客是有興趣的客戶,或從[Azure 應用商店](https://azuremarketplace.microsoft.com)或[AppSource](https://appsource.microsoft.com)部署您的產品/服務。 產品/服務發佈到市場後,您將收到客戶潛在客戶。 本文將解釋:

* 您的市場報價如何產生客戶線索,確保您不會錯過商機。 
* 將 CRM 連接到您的產品/服務,以便您可以在一個中心位置管理潛在顧客。
* 瞭解我們向您發送的潛在客戶數據,以便追蹤與您聯繫到的客戶。

## <a name="generate-customer-leads"></a>組建客戶串列

以下是產生潛在顧客的位置:

1. 當客戶在從市場中選擇"與我聯繫"后同意共用其資訊時。 此潛在顧客是**初始興趣**線索,我們在此分享有關已表示有興趣獲得您的產品的客戶的資訊。 潛在客戶位居分享購買漏斗的最頂端。

      ![動態 365 聯繫我](./media/commercial-marketplace-get-customer-leads/dynamics-365-contact-me.png)

2. 當客戶選擇「立即獲取」或「立即獲取」(在 Azure[入口](https://portal.azure.com/)中)以獲取產品/服務時,此潛在顧客是**一個活動線索**,在此中,我們將共用有關已開始部署產品的客戶的資訊。

    ![SQL 立即取得](./media/commercial-marketplace-get-customer-leads/sql-get-it-now.png)

    ![建立視窗伺服器](./media/commercial-marketplace-get-customer-leads/windows-server-create.png)

3. 客戶採用「試駕」或開始「免費試用」您的優惠。 測試驅動器或免費試用是您與潛在客戶即時共用業務(沒有任何進入障礙)的加速機會。

    ![動態 365 試駕](./media/commercial-marketplace-get-customer-leads/dynamics-365-test-drive.png)

    ![動態 365 試駕](./media/commercial-marketplace-get-customer-leads/dynamics-365-free-trial.png)

## <a name="connect-to-your-crm-system"></a>連線到 CRM 系統

[!INCLUDE [Test drive content](./includes/connect-lead-management.md)]

## <a name="understand-lead-data"></a>瞭解潛在客戶資料

您在客戶擷取程序期間收到的每位潛在客戶，在特定欄位中都有資料。 要尋找的第一個字欄位`LeadSource`是欄位,該欄位遵循此格式:**來源操作** | **。**

**來源**: 這個欄位的值以生成潛在顧客的市場填充。 可能的值為 `"AzureMarketplace"`、`"AzurePortal"` 和 `"AppSource (SPZA)"`。

**操作**:此欄位的值根據客戶在市場中執行的操作填充,該操作生成了潛在顧客。 

可能的值包括：

- "INS" -- 安裝。 當客戶購買您的產品時，會在 Azure Marketplace 或 AppSource 上進行此動作。
- "PLT" -- 代表合作夥伴主導的試用版。 當客戶使用 [與我連絡] 選項時，會在 AppSource 上進行此動作。
- "DNC" -- 請勿連絡。 當交叉列於您的應用程式頁面的合作夥伴收到連絡要求時，會在 AppSource 上進行此動作。 我們分享此客戶交叉列於您的應用程式的通知，但無須連絡他們。
- "創建" - 此操作僅在 Azure 門戶中,並在客戶向其帳戶購買產品/服務時生成。
- "StartTestDrive" -- 此動作僅限試用產品，並當客戶開始進行產品試用時產生。

**優惠**: 您可能在市場上有多個優惠。 此欄位的值基於生成潛在顧客的報價填充。 發行者 ID 和產品/服務 ID 都在此欄位中發送,是您向市場發佈產品/服務時提供的值。

以下範例以預期格式`publisherid.offerid`顯示範例值: 

1. `checkpoint.check-point-r77-10sg-byol`
1. `bitnami.openedxcypress`
1. `docusign.3701c77e-1cfa-4c56-91e6-3ed0b622145`

## <a name="customer-info"></a>客戶資訊

客戶的資訊通過多個字段發送。 下面的範例顯示了潛在顧客中包含的客戶資訊。

- 名字：John
- 姓氏：Smith
- 電子郵件： jsmith\@microsoft.com
- 電話：1234567890
- 國家/地區：美國
- 公司：Microsoft
- 職稱：技術長

>[!Note]
>上述範例中，並非所有資料一律可供每位潛在客戶使用。 由於您將從「客戶潛在顧客」部分中提到的多個步驟中獲得潛在顧客,因此處理潛在顧客的最佳方法是取消複製記錄並個人化後續操作。 透過這個方式，每位客戶都能獲得適當的訊息，而您能打造獨特的客戶關係。

## <a name="best-practices-for-lead-management"></a>潛在客戶管理的最佳實作

1. *流程*- 定義明確的銷售流程,具有里程碑、KPI 和明確的團隊擁有權。
2. *資格 -* 定義先決條件,指示潛在顧客是否已完全合格。 在將銷售或行銷代表接管整個銷售流程之前,確保仔細鑒定潛在顧客。
3. *跟進*- 不要忘記跟進,預計典型的交易需要 5 到 12 次跟進電話
4. *培育*- 培養您的潛在客戶,以便讓您獲得更高的獲利率。

## <a name="leads-frequently-asked-questions"></a>領導常見問題

### <a name="where-can-i-get-help-in-setting-up-my-lead-destination"></a>可以在哪裡取得設定我潛在客戶目的地的說明？

您可以[在此處](#connect-to-your-crm-system)尋找文件或https://partner.microsoft.com/support/v2/?stage=1透過選擇 **「產品/服務建立」** 或 **「****潛在客戶管理設定」** 來查找文件或提交支援票證。

### <a name="am-i-required-to-configure-a-lead-destination-in-order-to-publish-an-offer-on-the-marketplace"></a>我是否需要配置潛在顧客目標才能在市場上發佈產品/服務?

答案取決於您要發佈的產品/服務類型。 SaaS 和 Dynamics 365 客戶參與產品優惠將列為"與我聯繫"、所有 Dynamics 365 運營優惠、所有 Dynamics 365 商務中心優惠以及所有諮詢服務優惠都需要連接到潛在顧客目的地。 如果未列出您的要約類型,則不是必需的。 但是,建議您配置潛在顧客目標,這樣您就不會錯過商機。

### <a name="how-can-i-find-the-test-lead"></a>如何找到測試潛在客戶？

在潛在`"MSFT_TEST"`顧客目標中搜索,下面是來自 Microsoft 的範例測試線索:

```
company = MSFT_TEST_636573304831318844
country = US
description = MSFT_TEST_636573304831318844
email = MSFT_TEST_636573304831318844@test.com
encoding = UTF-8
encoding = UTF-8
first_name = MSFT_TEST_636573304831318844
last_name = MSFT_TEST_636573304831318844
lead_source = MSFT_TEST_636573304831318844-MSFT_TEST_636573304831318844|<Offer Name>
oid = 00Do0000000ZHog
phone = 1234567890
title = MSFT_TEST_636573304831318844
```

### <a name="i-have-a-live-offer-but-im-not-seeing-any-leads"></a>我有一個現場報價,但沒有看到任何線索?

確保與潛在顧客目標的連接有效。 在合作夥伴中心發佈您的優惠後,我們將向您發送測試線索。 如果看到測試引線,則連接有效。 您還可以通過在預覽步驟中嘗試獲取優惠預覽來測試潛在顧客連接,按一下市場上上市的"立即獲取""、"聯繫我"或"免費試用"。

此外,請確保您正在尋找正確的數據。 本文檔[瞭解潛在顧客數據](#understand-lead-data)「部分的內容描述了我們發送到潛在顧客目標的風險數據。

### <a name="i-have-configured-azure-blob-as-my-lead-destination-why-dont-i-see-the-lead"></a>我已將 Azure BLOB 設定為我的潛在客戶目的地，為何沒有看到潛在客戶？

Azure Blob 潛在顧客目標不再受支援,因此您缺少產品/服務生成的任何客戶潛在顧客。 切換到任何其他[潛在客戶目標選項](./commercial-marketplace-get-customer-leads.md)。 

### <a name="i-received-an-email-from-marketplace-why-cant-i-find-the-lead-in-my-crm"></a>我已從 Marketplace 收到一封電子郵件，為什麼在我的 CRM 中找不到潛在客戶？

終端使用者的電子郵件網域可能來自 .edu。 出於隱私原因,我們不會從 .edu 域傳遞私人身份資訊。 通過https://partner.microsoft.com/support/v2/?stage=1提交支援票證。

### <a name="i-have-configured-azure-table-as-my-lead-destination-how-can-i-view-the-leads"></a>我已經將 Azure 表配置為我的潛在顧客目標,如何查看潛在顧客?

可以從 Azure 門戶存取記憶體在 Azure 表中的潛在資料,也可以免費下載和安裝[Azure 儲存資源管理員](https://azure.microsoft.com/features/storage-explorer/)以查看 Azure 儲存帳戶的表數據。

### <a name="i-have-configured-azure-table-as-my-lead-destination-can-i-get-notified-whenever-a-new-lead-is-sent-by-marketplace"></a>我已設定 Azure 資料表作為我的潛在客戶目的地，是否可以在 Marketplace 傳送新的潛在客戶時隨時收到通知？

可以,按照說明設置 Microsoft 流,如果[此處](./commercial-marketplace-lead-management-instructions-azure-table.md)的文檔將潛在顧客添加到 Azure 表,則該流將發送電子郵件。

### <a name="i-have-configured-salesforce-as-my-lead-destination-why-cant-i-find-the-leads"></a>我已設定 Salesforce 作為我的潛在客戶目的地，為什麼找不到潛在客戶？

檢查"Web 到潛在顧客"表單是否是基於選取清單的必填欄位。 如果是，請將欄位切換為非必要文字欄位。

### <a name="there-was-an-issue-with-my-lead-destination-and-i-missed-some-leads-can-i-have-them-sent-to-me-in-an-email"></a>我的潛在客戶目的地發生問題，而我遺漏了一些潛在客戶。 是否可以在電子郵件中將這些潛在客戶傳送給我？

由於私人可識別資訊政策,我們無法通過不安全的電子郵件共享潛在顧客資訊。

### <a name="i-have-configured-azure-table-as-my-lead-destination-how-much-will-it-cost"></a>我已經將 Azure 表配置為我的潛在顧客目標,它將花費多少?

潛在客戶的一般資料很小 (幾乎所有的發行者都 < 1 GB)。 售價取決於所收到的潛在客戶數量，如果在一個月內收到 1,000 個潛在客戶，則成本大約 50 分。 如需儲存體定價的詳細資訊，請參閱[儲存體定價](https://azure.microsoft.com/pricing/details/storage/)。

如果您的問題仍未得到解答,請通過aka.ms/marketplacepublishersupport聯繫支援人員,然後選擇 **"產品/服務創建"+****您的產品/服務類型**和 **"潛在客戶管理配置"。** 

### <a name="i-am-receiving-email-notifications-when-new-customer-leads-are-received-how-can-i-configure-who-to-receive-these-emails"></a>收到新客戶線索時,我收到電子郵件通知。 如何配置接收這些電子郵件的人員?

在合作夥伴中心存取您的優惠,並導航到**產品/服務設定**頁面 ->**潛在顧客管理** -> **編輯**。 更新**聯絡人電子郵件**「欄位下的電子郵件位址。

## <a name="next-steps"></a>後續步驟

一旦技術設置到位,您應該將這些潛在顧客納入您當前的銷售&行銷戰略和運營流程。 我們有興趣進一步了解您的整體銷售流程，並想與您密切合作，為您提供高品質潛在客戶及足夠的資料，讓您獲致成功。 我們歡迎您針對我們如何能最佳化及改善我們傳送給您的潛在客戶，及提供額外資料協助這些客戶達成您的成就，提出您的意見反應。 如果您有興趣[提供反饋](mailto:AzureMarketOnboard@microsoft.com)和建議,使您的銷售團隊在市場線索方面更加成功,請告訴我們。
