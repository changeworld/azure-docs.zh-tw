---
title: 從您的 Microsoft 商業 marketplace 供應專案設定潛在客戶
description: 在 Microsoft 商業 marketplace 中設定客戶領導。
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: dsindona
ms.openlocfilehash: 54c67656d7847b44c8fc83b33a4e03be3838cf76
ms.sourcegitcommit: edccc241bc40b8b08f009baf29a5580bf53e220c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/24/2020
ms.locfileid: "82131111"
---
# <a name="customer-leads-from-your-marketplace-offer"></a>來自您的市集供應項目的潛在客戶

潛在客戶有興趣或從 Microsoft [AppSource](https://appsource.microsoft.com)和[Azure Marketplace](https://azuremarketplace.microsoft.com)部署您的優惠。 在您的供應專案發佈至 marketplace 之後，您將會收到客戶商機。 本文說明：

* 您的 marketplace 供應專案如何產生潛在客戶，以確保您不會錯過商機。 
* 如何將您的客戶關係管理（CRM）系統連接到您的供應專案，以便您可以在單一集中位置管理您的潛在客戶。
* 我們傳送給您的潛在客戶資料，讓您可以追蹤與您聯繫的客戶。

## <a name="generate-customer-leads"></a>產生潛在客戶

以下是產生潛在客戶的地方：

- 客戶在從商業市場選取 [**聯絡我**] 之後，同意分享他們的資訊。 這個潛在客戶是*初步*的潛在客戶。 我們會與您分享關於如何取得您的產品的客戶相關資訊。 潛在客戶位居分享購買漏斗的最頂端。

    ![Dynamics 365 聯絡我](./media/commercial-marketplace-get-customer-leads/dynamics-365-contact-me.png)

- 客戶選取 [**立即取得**] （或選取[Azure 入口網站](https://portal.azure.com/)中的 [**建立**]）來取得您的供應專案。 這個潛在客戶是*主動*潛在客戶。 我們會與您分享有關已開始部署產品之客戶的資訊。

    ![[SQL 立即取得] 按鈕](./media/commercial-marketplace-get-customer-leads/sql-get-it-now.png)

    ![Windows Server [建立] 按鈕](./media/commercial-marketplace-get-customer-leads/windows-server-create.png)

- 客戶**選取試用**產品或**免費試用版**，以試用您的供應專案。 試用產品或免費試用版是加速的機會，可讓您立即與潛在客戶分享您的業務，而不會有任何進入障礙。

    ![Dynamics 365 試用產品按鈕](./media/commercial-marketplace-get-customer-leads/dynamics-365-test-drive.png)

    ![Dynamics 365 免費試用按鈕](./media/commercial-marketplace-get-customer-leads/dynamics-365-free-trial.png)

## <a name="connect-to-your-crm-system"></a>連接到您的 CRM 系統

[!INCLUDE [Test drive content](./includes/connect-lead-management.md)]

## <a name="understand-lead-data"></a>瞭解潛在客戶資料

您在客戶擷取程序期間收到的每位潛在客戶，在特定欄位中都有資料。 要查看的第一個欄位是欄位， `LeadSource`其遵循此格式： [**來源-動作** | ] [**供應**專案]。

**來源**：此欄位的值會根據產生潛在客戶的 marketplace 填入。 可能的值為 `"AzureMarketplace"`、`"AzurePortal"` 和 `"AppSource (SPZA)"`。

**動作**：此欄位的值會根據客戶在產生潛在客戶之 marketplace 中所採取的動作來填入。

可能的值包括：

- **"INS"**：代表*安裝*。 當客戶購買您的產品時，此動作會在 Azure Marketplace 或 AppSource 中。
- **"PLT"**：代表合作夥伴導向的*試用版*。 當客戶選取 [**連絡人**] 選項時，此動作會在 AppSource 中。
- **"DNC"**：代表「*不要聯絡*」。 當您的應用程式頁面上已交叉列出的合作夥伴要求聯繫時，此動作會在 AppSource 中。 我們會分享此客戶在您的應用程式上交叉列出的通知，但不需要聯繫。
- 「**建立**」：此動作只會在 Azure 入口網站內，並且會在客戶向其帳戶購買您的供應專案時產生。
- **"StartTestDrive"**：此動作僅適用于 [**試用**產品] 選項，而且會在客戶啟動其試用產品時產生。

供應**專案：您**在商業 marketplace 中可能有多個供應專案。 此欄位的值會根據產生潛在客戶的供應專案而填入。 發行者識別碼和供應專案識別碼都是在此欄位中傳送，而且是您將供應專案發佈至 marketplace 時提供的值。

下列範例會顯示預期格式`publisherid.offerid`的值： 

- `checkpoint.check-point-r77-10sg-byol`
- `bitnami.openedxcypress`
- `docusign.3701c77e-1cfa-4c56-91e6-3ed0b622145`

## <a name="customer-information"></a>客戶資訊

客戶的資訊會透過多個欄位傳送。 下列範例會顯示潛在客戶中包含的客戶資訊：

- 名字：John
- 姓氏：Smith
- 電子郵件： jsmith\@microsoft.com
- 電話：1234567890
- 國家/地區：美國
- 公司：Microsoft
- 職稱：技術長

>[!NOTE]
>上述範例中，並非所有資料一律可供每位潛在客戶使用。 因為您會從「產生客戶組長」一節中所述的多個步驟中取得潛在客戶，所以處理潛在客戶的最佳方式就是刪除記錄，並將後續步驟個人化。 如此一來，每個客戶都會取得適當的訊息，而您會建立唯一的關聯性。

## <a name="best-practices-for-lead-management"></a>潛在客戶管理的最佳做法

- **處理**：定義清楚的銷售程式，包含里程碑、分析和清楚的小組擁有權。
- **限定**性：定義必要條件，以指出潛在客戶是否為完整名稱。 請確定銷售或行銷代表會謹慎地限定潛在客戶，再透過完整的銷售程式取得。
- **後續**操作：別忘了後續追蹤。 預期一般交易需要5到12個後續呼叫。
- **培養**：培養您的潛在客戶，以取得更高收益的方式。

## <a name="leads-frequently-asked-questions"></a>潛在客戶常見問題

### <a name="where-can-i-get-help-in-setting-up-my-lead-destination"></a>可以在哪裡取得設定我潛在客戶目的地的說明？

請遵循連線[到您的 CRM 系統](#connect-to-your-crm-system)一節中的步驟，或透過合作夥伴中心的 [說明[及支援](https://partner.microsoft.com/support/v2/?stage=1)] 提交支援票證。 然後選取 [供應專案] [**建立** > **您的供應** > 專案**潛在客戶管理**設定類型]。

### <a name="am-i-required-to-configure-a-lead-destination-in-order-to-publish-an-offer-in-the-commercial-marketplace"></a>我是否必須設定潛在客戶目的地，才能在商業 marketplace 中發佈供應專案？

答案取決於您要發佈的供應專案類型。 軟體即服務（SaaS）和 Dynamics 365 Customer Engagement 使用「**聯絡我**」列出所有 dynamics 365 for 金融和營運供應專案、所有 Dynamics 365 Business Central 供應專案，以及所有諮詢服務供應專案。 因此，他們需要連接到潛在客戶目的地。 如果未列出您的供應專案類型，則不需要連接到潛在客戶目的地。 我們建議您設定潛在客戶目的地，讓您不會錯過商機。

### <a name="how-can-i-find-the-test-lead"></a>如何找到測試潛在客戶？

`"MSFT_TEST"`在您的潛在客戶目的地中搜尋。 以下是 Microsoft 的範例測試潛在客戶：

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

### <a name="i-have-a-live-offer-but-why-am-i-not-seeing-any-leads"></a>我有上線的供應專案，但為什麼看不到任何潛在客戶？

請確定您的潛在客戶目的地連接有效。 當您在合作夥伴中心的供應專案上選取 [**發佈**] 之後，我們會將測試潛在客戶傳送給您。 如果您看到測試潛在客戶，連接就會是有效的。 您也可以嘗試在預覽步驟中取得供應專案預覽，以測試您的潛在客戶連線。 選取 [**立即取得**]、[**聯絡我**]，或 [商業 marketplace] 清單中的 [**免費試用**]。

此外，請確定您要尋找正確的資料。 本文的[瞭解潛在客戶資料](#understand-lead-data)一節中的內容說明我們傳送到潛在客戶目的地的潛在客戶資料。

### <a name="i-configured-azure-blob-storage-as-my-lead-destination-but-why-dont-i-see-the-lead"></a>我已將 Azure Blob 儲存體設定為我的潛在客戶目的地，但為什麼看不到潛在客戶？

Azure Blob 儲存體已不再支援為潛在客戶目的地，因此您會遺失您的供應專案所產生的任何客戶潛在客戶。 切換至其他任何[潛在客戶目的地選項](./commercial-marketplace-get-customer-leads.md)。 

### <a name="i-received-an-email-from-the-commercial-marketplace-but-why-cant-i-find-the-lead-in-my-crm"></a>我收到來自商用 marketplace 的電子郵件，但為什麼在我的 CRM 中找不到潛在客戶？

終端使用者的電子郵件網域可能來自 .edu。 基於隱私權考慮，我們不會從 edu 網域傳遞個人識別資訊。 透過合作夥伴中心的 [說明[及支援](https://partner.microsoft.com/support/v2/?stage=1)] 提交支援票證。

### <a name="i-configured-an-azure-table-as-my-lead-destination-how-can-i-view-the-leads"></a>我已將 Azure 資料表設定為我的潛在客戶目的地。 如何才能查看潛在客戶？

您可以從 Azure 入口網站存取儲存在 Azure 資料表中的潛在客戶資料。 您也可以免費下載並安裝[Azure 儲存體總管](https://azure.microsoft.com/features/storage-explorer/)，以查看您的 Azure 儲存體帳戶的資料表資料。

### <a name="i-configured-an-azure-table-as-my-lead-destination-can-i-get-notified-whenever-a-new-commercial-marketplace-lead-is-sent"></a>我已將 Azure 資料表設定為我的潛在客戶目的地。 每當傳送新的商業 marketplace 潛在客戶時，是否可以收到通知？

是。 請遵循[使用 Azure 資料表設定潛在客戶管理](./commercial-marketplace-lead-management-instructions-azure-table.md)中的指示，來設定 Microsoft flow，以在潛在客戶新增至 Azure 資料表時傳送電子郵件。

### <a name="i-configured-salesforce-as-my-lead-destination-but-why-cant-i-find-the-leads"></a>我將 Salesforce 設定為我的潛在客戶目的地，但為什麼找不到潛在客戶？

根據挑選清單，檢查 web 對潛在客戶表單是否為必要欄位。 如果是，請將欄位切換到 nonmandatory 文字欄位。

### <a name="there-was-an-issue-with-my-lead-destination-and-i-missed-some-leads-can-i-have-them-sent-to-me-in-an-email"></a>我的潛在客戶目的地發生問題，而我遺漏了一些潛在客戶。 是否可以在電子郵件中將這些潛在客戶傳送給我？

由於個人識別資訊原則的緣故，我們無法透過不安全的電子郵件共用潛在客戶資訊。

### <a name="i-configured-an-azure-table-as-my-lead-destination-how-much-will-it-cost"></a>我已將 Azure 資料表設定為我的潛在客戶目的地。 它需要多少費用？

潛在客戶產生資料很低。 幾乎所有發行者都小於 1 GB。 成本取決於收到的潛在客戶數目。 例如，如果每個月收到1000個潛在客戶，則成本大約是50美分。 如需儲存體價格的詳細資訊，請參閱[Azure 儲存體總覽定價](https://azure.microsoft.com/pricing/details/storage/)。

如果您的問題未獲得解答，請透過合作夥伴中心的 [說明[及支援](https://aka.ms/marketplacepublishersupport)] 來聯絡 Microsoft 支援服務。 然後選取 [供應專案] [**建立** > **您的供應** > 專案**潛在客戶管理**設定類型]。

### <a name="im-receiving-email-notifications-when-new-customer-leads-are-received-how-can-i-configure-someone-else-to-receive-these-emails"></a>當收到新的客戶潛在客戶時，我會收到電子郵件通知。 如何設定其他人接收這些電子郵件？

在合作夥伴中心存取您的供應專案，並移至 [**供應專案設定**] 頁面 > [**潛在客戶管理** > ] [**編輯**]。 更新 [**連絡人電子郵件**] 欄位底下的電子郵件地址。

## <a name="next-steps"></a>後續步驟

技術設定就緒之後，請將這些潛在客戶納入目前的銷售和行銷策略和營運程式中。 我們有興趣進一步瞭解您的整體銷售流程，並想要與您密切合作，以提供高品質的潛在客戶及足夠的資料，讓您成功。 我們歡迎您針對我們如何能最佳化及改善我們傳送給您的潛在客戶，及提供額外資料協助這些客戶達成您的成就，提出您的意見反應。 請讓我們知道您是否有興趣[提供意見](mailto:AzureMarketOnboard@microsoft.com)反應和建議，讓您的銷售團隊能夠更成功地使用商業 marketplace 潛在客戶。
