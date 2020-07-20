---
title: 管理 Microsoft 商業市集中的潛在客戶
description: 瞭解如何從您的 Microsoft AppSource 和 Azure Marketplace 供應項目產生及接收潛在客戶
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: keferna
ms.author: keferna
ms.date: 03/30/2020
ms.openlocfilehash: ca6e9a66d6c4daa4706813f945850b51505adeca
ms.sourcegitcommit: e995f770a0182a93c4e664e60c025e5ba66d6a45
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/08/2020
ms.locfileid: "86133437"
---
# <a name="customer-leads-from-your-commercial-marketplace-offer"></a>來自您的商業市集供應項目的潛在客戶

潛在客戶是部署您的 [Microsoft AppSource](https://appsource.microsoft.com) 和 [Azure Marketplace](https://azuremarketplace.microsoft.com) 供應項目或對之有興趣的客戶。 您的供應項目發佈到商業市集之後，您就可以收到潛在客戶。 本文說明下列潛在客戶管理概念：

* 您的商業市集供應項目如何產生潛在客戶，以確保您不會錯過商機。 
* 如何將您的客戶關係管理 (CRM) 系統連接到您的供應項目，讓您可以在單一集中位置管理您的潛在客戶。
* 我們傳送給您的潛在客戶資料，讓您可以追蹤與您聯繫的客戶。

## <a name="generate-customer-leads"></a>產生潛在客戶

以下是潛在客戶的產生之處：

- 客戶在商業市集中選取 [聯絡我] 之後，同意分享他們的資訊。 這個潛在客戶是*初始興趣*潛在客戶。 我們會與您分享表達有意了解如何取得您產品的客戶資訊。 潛在客戶位居分享購買漏斗的最頂端。

    ![Dynamics 365 聯絡我](./media/commercial-marketplace-get-customer-leads/dynamics-365-contact-me.png)

- 客戶選取 [立即取得] (或選取 [Azure 入口網站](https://portal.azure.com/) 中的 [建立]) 以取得您的供應項目。 這是*主動*潛在客戶。 我們會與您分享已開始部署您產品之客戶的資訊。

    ![SQL 立即取得按鈕](./media/commercial-marketplace-get-customer-leads/sql-get-it-now.png)

    ![Windows Server 建立按鈕](./media/commercial-marketplace-get-customer-leads/windows-server-create.png)

- 客戶選取 [試用產品] 或 [免費試用版] 想試用您的供應項目。 試用產品或免費試用是一個很好的機會，能讓您加快立即與潛在客戶分享商務產品的腳步，毫無任何進入障礙。

    ![Dynamics 365 試用產品按鈕](./media/commercial-marketplace-get-customer-leads/dynamics-365-test-drive.png)

    ![Dynamics 365 免費試用按鈕](./media/commercial-marketplace-get-customer-leads/dynamics-365-free-trial.png)

## <a name="connect-to-your-crm-system"></a>連線到您的 CRM 系統

[!INCLUDE [Test drive content](./includes/connect-lead-management.md)]

## <a name="understand-lead-data"></a>瞭解潛在客戶資料

您在客戶擷取程序期間收到的每位潛在客戶，在特定欄位中都有資料。 第一個要查看的欄位是 `LeadSource` 欄位，其格式為：**來源-動作** | **供應項目**。

**來源**：此欄位的值會根據產生該潛在客戶的市集來填入。 可能的值是 `"AzureMarketplace"`、`"AzurePortal"`、`"AppSource (SPZA)"`。

**動作**：此欄位的值會根據客戶在產生潛在客戶的市集中所採取的動作來填入。

可能的值包括：

- **"INS"** ：代表安裝。 此動作發生在 Azure Marketplace 或 AppSource 中，當客戶購買您的產品時。
- **"PLT"** ：代表合作夥伴主導的試用版。 此動作發生在 AppSource 中，當客戶選取 [與我連絡] 選項時。
- **"DNC"** ：代表請勿連絡。 此動作發生在 AppSource 中，當交叉列於您應用程式頁面的合作夥伴收到連絡要求時。 我們會分享一則通知，表明此客戶交叉列於您的應用程式，但無須連絡他們。
- **"Create"** ：此動作僅發生在 Azure 入口網站中，當客戶在其帳戶中購買您的供應項目時產生。
- **"StartTestDrive"** ：此動作僅會發生在 [試用產品] 選項，當客戶開始進行產品試用時產生。

**供應項目**：您在商業市集中可能有多個供應項目。 此欄位的值會根據產生該潛在客戶的供應項目來填入。 發行者識別碼和供應項目識別碼都是在此欄位中傳送，而且是您將供應項目發佈至市集時提供的值。

下列範例會以預期格式 `publisherid.offerid` 顯示值： 

- `checkpoint.check-point-r77-10sg-byol`
- `bitnami.openedxcypress`
- `docusign.3701c77e-1cfa-4c56-91e6-3ed0b622145`

## <a name="customer-information"></a>客戶資訊

客戶的資訊會透過多個欄位傳送。 下列範例顯示了潛在客戶中包含的客戶資訊：

- 名字：John
- 姓氏：Smith
- 電子郵件： jsmith\@microsoft.com
- 電話：1234567890
- 國家/地區：US
- 公司：Microsoft
- 標題：CTO

>[!NOTE]
>上述範例中，並非所有資料一律可供每位潛在客戶使用。 如同「產生潛在客戶」一節所述，因為您會從多個步驟取得潛在客戶，所以處理潛在客戶的最佳方式就是刪除重複，並將後續操作個人化。 如此一來，每個客戶都會得到適當的訊息，而您會建立唯一的關聯性。

## <a name="best-practices-for-lead-management"></a>潛在客戶管理的最佳做法

以下是透過您的銷售週期推動潛在客戶的一些建議：

- **程序**：定義清楚的銷售流程，包含里程碑、分析、清楚的小組擁有權。
- **資格**：定義必要條件，指出潛在客戶是否完全符合資格。 請確定銷售或行銷代表會謹慎地界定潛在客戶，再將其納入完整的銷售流程。
- **後續**操作：別忘了在24小時內繼續追蹤。 客戶部署試用產品之後，您會立即在您選擇的 CRM 中取得潛在客戶;當他們仍然暖時，將其以電子郵件傳送給他們。 要求排定電話通話，以進一步瞭解您的產品是否為其問題的絕佳解決方案。 一般交易預期需要進行許多後續的呼叫。
- **加強關係**：培養您的潛在客戶，讓您獲得更高的收益。 簽入，但不要 bombard 它們。 我們建議您在關閉電子郵件之前，先將其寄送到至少幾次，第一次嘗試之後，請不要放棄。 請記住，這些客戶會直接與您的產品接洽，並花時間進行免費試用;這些都是絕佳的潛在客戶。

## <a name="common-questions-about-lead-management"></a>潛在客戶管理的常見問題

### <a name="where-can-i-get-help-in-setting-up-my-lead-destination"></a>可以在哪裡取得設定我潛在客戶目的地的說明？

遵循[連線到您的 CRM 系統](#connect-to-your-crm-system)一節中的步驟，或透過[合作夥伴中心說明及支援](https://aka.ms/marketplacepublishersupport)提交支援票證。 然後選取 [供應項目建立]  >  [您的供應項目類型]  >  [潛在客戶管理設定]。

### <a name="am-i-required-to-configure-a-lead-destination-in-order-to-publish-an-offer-in-the-commercial-marketplace"></a>我是否必須設定潛在客戶目的地，才能在商業市集中發佈供應項目？

答案取決於您要發佈的供應項目類型。 軟體即服務 (SaaS) 和 Dynamics 365 Customer Engagement 使用**聯絡我**來列出所有 Dynamics 365 的財務和營運供應項目、所有 Dynamics 365 Business Central 供應項目、所有諮詢服務供應項目。 因此，其需要連線到潛在客戶目的地。 如果未列出您的供應項目類型，則不需要連線到潛在客戶目的地。 我們建議您設定潛在客戶目的地，讓您不會錯過商機。

### <a name="how-can-i-find-the-test-lead"></a>如何找到測試潛在客戶？

在您的潛在客戶目的地中搜尋 `"MSFT_TEST"`。 以下是 Microsoft 的範例測試潛在客戶：

```
{
    "UserDetails": {
      "FirstName": "MSFT_TEST_636573304831318844",
      "LastName": "MSFT_TEST_636573304831318844",
      "Email": "MSFT_TEST_636573304831318844@test.com",
      "Phone": "1234567890",
      "Country": "US",
      "Company": "MSFT_TEST_636573304831318844",
      "Title": "MSFT_TEST_636573304831318844"
    },
    "LeadSource": "AzureMarketplace",
    "ActionCode": "INS",
    "OfferTitle": "Contoso Test"
    "Description": "MSFT_TEST_636573304831318844"
}
```

### <a name="i-have-a-live-offer-but-why-am-i-not-seeing-any-leads"></a>我有作用中的供應項目，但為什麼沒看到任何潛在客戶？

請確定您的潛在客戶目的地連線有效。 您在 [合作夥伴中心] 中選取 [發佈] 您的供應項目後，我們會將測試潛在客戶傳送給您。 如果您看到測試潛在客戶，表示連線有效。 您也可以嘗試在預覽步驟中取得供應項目預覽，以測試您的潛在客戶連線。 選取商業市集中列出的 [立即取得]、[與我聯繫] 或 [免費試用]。

此外，請確定您尋找的是正確的資料。 本文的[瞭解潛在客戶資料](#understand-lead-data)一節說明我們傳送到潛在客戶目的地的潛在客戶資料。

### <a name="i-configured-azure-blob-storage-as-my-lead-destination-but-why-dont-i-see-the-lead"></a>我已將 Azure Blob 儲存體設定為我的潛在客戶目的地，為何沒有看到潛在客戶？

Azure Blob 儲存體已不再支援做為潛在客戶目的地，因此您會遺失您的供應項目所產生的任何潛在客戶。 切換至任何其他[潛在客戶目的地選項](./commercial-marketplace-get-customer-leads.md)。 

### <a name="i-received-an-email-from-the-commercial-marketplace-but-why-cant-i-find-the-lead-in-my-crm"></a>我收到來自商業市集的電子郵件，但為什麼在我的 CRM 中找不到潛在客戶？

終端使用者的電子郵件網域可能來自 .edu。 基於隱私權考慮，我們不會從 .edu 網域傳遞個人資訊。 請透過[合作夥伴中心說明及支援](https://aka.ms/marketplacepublishersupport)提交支援票證。

### <a name="i-configured-an-azure-table-as-my-lead-destination-how-can-i-view-the-leads"></a>我已將 Azure 資料表設定為我的潛在客戶目的地。 如何才能檢視潛在客戶？

您可以從 Azure 入口網站存取儲存在 Azure 資料表中的潛在客戶資料。 您也可以免費下載並安裝 [Azure 儲存體總管](https://azure.microsoft.com/features/storage-explorer/)，以檢視 Azure 儲存體帳戶的資料表資料。

### <a name="i-configured-an-azure-table-as-my-lead-destination-can-i-get-notified-whenever-a-new-commercial-marketplace-lead-is-sent"></a>我已將 Azure 資料表設定為我的潛在客戶目的地。 是否可以在每次傳送新的商業市集潛在客戶時收到通知？

是。 依照 [使用 Azure 資料表來設定潛在客戶管理](./commercial-marketplace-lead-management-instructions-azure-table.md)中的指示設定 Microsoft 流程，在潛在客戶新增至 Azure 資料表時傳送電子郵件。

### <a name="i-configured-salesforce-as-my-lead-destination-but-why-cant-i-find-the-leads"></a>我已設定 Salesforce 做為我的潛在客戶目的地，但為什麼找不到潛在客戶？

根據挑選清單檢查潛在客戶表單的 Web 是否為必要欄位。 如果是，請將該欄位切換為必要文字欄位。

### <a name="there-was-an-issue-with-my-lead-destination-and-i-missed-some-leads-can-i-have-them-sent-to-me-in-an-email"></a>我的潛在客戶目的地發生問題，我遺漏了一些潛在客戶。 是否可以在電子郵件中將這些潛在客戶傳送給我？

由於個人資訊原則的緣故，我們無法透過不安全的電子郵件分享潛在客戶資訊。

### <a name="i-configured-an-azure-table-as-my-lead-destination-how-much-will-it-cost"></a>我已將 Azure 資料表設定為我的潛在客戶目的地。 需要花費多少成本？

潛在客戶產生資料很少。 幾乎對所有發行者來說都小於 1 GB。 成本取決於收到的潛在客戶數目。 例如，如果一個月收到 1000 個潛在客戶，則成本大約是 50 美分。 如需儲存體定價的詳細資訊，請參閱 [Azure 儲存體概觀定價](https://azure.microsoft.com/pricing/details/storage/)。

如果您的問題未獲得解答，請透過[合作夥伴中心說明及支援](https://aka.ms/marketplacepublishersupport)聯絡 Microsoft 支援服務。 然後選取 [供應項目建立]  >  [您的供應項目類型]  >  [潛在客戶管理設定]。

### <a name="im-receiving-email-notifications-when-new-customer-leads-are-received-how-can-i-configure-someone-else-to-receive-these-emails"></a>當我收到新的潛在客戶時，也會收到電子郵件通知。 如何設定其他人收到這些電子郵件？

在合作夥伴中心存取您的供應項目，移至 [供應項目設定] 頁面 > [潛在客戶管理]  >  [編輯]。 更新 [連絡人電子郵件] 欄位底下的電子郵件地址。

## <a name="next-steps"></a>後續步驟

技術設定完成後，將這些潛在客戶整合至您目前的銷售與行銷策略以及營運流程當中。 我們有興趣進一步了解您的整體銷售流程，並想與您密切合作，為您提供高品質潛在客戶及足夠的資料，讓您獲致成功。 我們歡迎您針對我們如何能最佳化及改善我們傳送給您的潛在客戶，及提供額外資料協助這些客戶達成您的成就，提出您的意見反應。 如果您有興趣[提供意見反應](mailto:AzureMarketOnboard@microsoft.com)及建議，讓我們的銷售團隊能推出更成功的商業市集潛在客戶，請告訴我們。
