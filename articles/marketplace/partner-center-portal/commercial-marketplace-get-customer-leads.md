---
title: 管理 Microsoft 商業市集中的潛在客戶
description: 瞭解如何從您的 Microsoft AppSource 和 Azure Marketplace 供應項目產生及接收潛在客戶
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: trkeya
ms.author: trkeya
ms.date: 10/01/2020
ms.openlocfilehash: 1d5f2248d94796d5e3ee76301642a95abddebfe4
ms.sourcegitcommit: b4880683d23f5c91e9901eac22ea31f50a0f116f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/11/2020
ms.locfileid: "94489330"
---
# <a name="customer-leads-from-your-commercial-marketplace-offer"></a>來自您的商業市集供應項目的潛在客戶

潛在客戶是部署您的 [Microsoft AppSource](https://appsource.microsoft.com) 和 [Azure Marketplace](https://azuremarketplace.microsoft.com) 供應項目或對之有興趣的客戶。 您的供應項目發佈到商業市集之後，您就可以收到潛在客戶。 本文說明下列潛在客戶管理概念：

* 您的商業市集供應項目如何產生潛在客戶，以確保您不會錯過商機。 
* 如何將您的客戶關係管理 (CRM) 系統連接到您的供應項目，讓您可以在單一集中位置管理您的潛在客戶。
* 我們傳送給您的潛在客戶資料，讓您可以追蹤與您聯繫的客戶。

## <a name="generate-customer-leads"></a>產生潛在客戶

以下是潛在客戶的產生之處：

- 客戶在商業市集中選取 [聯絡我] 之後，同意分享他們的資訊。 這個潛在客戶是 *初始興趣* 潛在客戶。 我們會與您分享表達有意了解如何取得您產品的客戶資訊。 潛在客戶位居分享購買漏斗的最頂端。

    ![Dynamics 365 聯絡我](./media/commercial-marketplace-get-customer-leads/dynamics-365-contact-me.png)

- 客戶選取 [立即取得] (或選取 [Azure 入口網站](https://portal.azure.com/) 中的 [建立]) 以取得您的供應項目。 這是 *主動* 潛在客戶。 我們會與您分享已開始部署您產品之客戶的資訊。

    ![SQL 立即取得按鈕](./media/commercial-marketplace-get-customer-leads/sql-get-it-now.png)

    ![Windows Server 建立按鈕](./media/commercial-marketplace-get-customer-leads/windows-server-create.png)

- 客戶選取 [試用產品] 或 [免費試用版] 想試用您的供應項目。 試用產品或免費試用是一個很好的機會，能讓您加快立即與潛在客戶分享商務產品的腳步，毫無任何進入障礙。

    ![Dynamics 365 試用產品按鈕](./media/commercial-marketplace-get-customer-leads/dynamics-365-test-drive.png)

    ![Dynamics 365 免費試用按鈕](./media/commercial-marketplace-get-customer-leads/dynamics-365-free-trial.png)

## <a name="connect-to-your-crm-system"></a>連線到您的 CRM 系統

[!INCLUDE [Links to lead configuration for different CRM systems](./includes/connect-lead-management.md)]

## <a name="understand-lead-data"></a>瞭解潛在客戶資料

您在客戶擷取程序期間收到的每位潛在客戶，在特定欄位中都有資料。 第一個要查看的欄位是 `LeadSource` 欄位，其格式為： **來源-動作** | **供應項目** 。

**來源** ：此欄位的值會根據產生該潛在客戶的市集來填入。 可能的值是 `"AzureMarketplace"`、`"AzurePortal"`、`"AppSource (SPZA)"`。

**動作** ：此欄位的值會根據客戶在產生潛在客戶的市集中所採取的動作來填入。

可能的值包括：

- **"INS"** ：代表安裝。 此動作發生在 Azure Marketplace 或 AppSource 中，當客戶購買您的產品時。
- **"PLT"** ：代表合作夥伴主導的試用版。 此動作發生在 AppSource 中，當客戶選取 [與我連絡] 選項時。
- **"DNC"** ：代表請勿連絡。 此動作發生在 AppSource 中，當交叉列於您應用程式頁面的合作夥伴收到連絡要求時。 我們會分享一則通知，表明此客戶交叉列於您的應用程式，但無須連絡他們。
- **"Create"** ：此動作僅發生在 Azure 入口網站中，當客戶在其帳戶中購買您的供應項目時產生。
- **"StartTestDrive"** ：此動作僅會發生在 [試用產品] 選項，當客戶開始進行產品試用時產生。

**供應項目** ：您在商業市集中可能有多個供應項目。 此欄位的值會根據產生該潛在客戶的供應項目來填入。 發行者識別碼和供應項目識別碼都是在此欄位中傳送，而且是您將供應項目發佈至市集時提供的值。

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

- **程序** ：定義清楚的銷售流程，包含里程碑、分析、清楚的小組擁有權。
- **資格** ：定義必要條件，指出潛在客戶是否完全符合資格。 請確定銷售或行銷代表會謹慎地界定潛在客戶，再將其納入完整的銷售流程。
- **後續** 操作：別忘了在24小時內進行後續追蹤。 客戶部署試用產品之後，您將會立即取得您的 CRM 選擇：以電子郵件寄送電子郵件，但仍然暖。 要求排程電話以進一步瞭解您的產品是否為其問題的良好解決方案。 預期一般交易需要進行許多後續的呼叫。
- **加強關係** ：培養您的潛在客戶，讓您獲得更高的收益。 簽入，但不要 bombard 它們。 建議您先傳送電子郵件給潛在客戶至少幾次，再關閉它們。第一次嘗試之後，請不要放棄。 請記住，這些客戶會直接與您的產品聯繫，並將時間花在免費試用;這些都是絕佳的潛在客戶。

技術設定完成後，將這些潛在客戶整合至您目前的銷售與行銷策略以及營運流程當中。 我們有興趣進一步了解您的整體銷售流程，並想與您密切合作，為您提供高品質潛在客戶及足夠的資料，讓您獲致成功。 我們歡迎您針對我們如何能最佳化及改善我們傳送給您的潛在客戶，及提供額外資料協助這些客戶達成您的成就，提出您的意見反應。 如果您有興趣[提供意見反應](mailto:AzureMarketOnboard@microsoft.com)及建議，讓我們的銷售團隊能推出更成功的商業市集潛在客戶，請告訴我們。

## <a name="next-steps"></a>後續步驟

- [潛在客戶管理常見問題和疑難排解](../lead-management-faq.md)