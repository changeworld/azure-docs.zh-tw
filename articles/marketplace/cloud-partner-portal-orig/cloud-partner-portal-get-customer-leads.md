---
title: 設定潛在客戶 |Azure Marketplace
description: 在 Cloud Partner 入口網站中設定潛在客戶。
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/14/2018
ms.author: dsindona
ms.openlocfilehash: 56012fb2a907a6db6f87554660ee36b99a3dcbf9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80280315"
---
<a name="get-customer-leads"></a>取得潛在客戶
==================

此文章說明如何使用 Cloud Partner 入口網站建立潛在客戶。 您可以將這些潛在客戶連線到您的 CRM 系統，並將它們整合到您的準銷售案源。

## <a name="leads"></a>潛在客戶

潛在客戶是對您的產品感興趣的客戶，或正在從 [Azure Marketplace](https://azuremarketplace.microsoft.com/) 或 [AppSource](https://appsource.microsoft.com) 部署您產品的客戶。

### <a name="azure-marketplace"></a>Azure Marketplace

1.  客戶「試用」您的供應項目。 試用產品是一個很好的機會，能讓您加快立即與潛在客戶分享商務產品的腳步，且試用過程毫無任何障礙。 所有試用產品會產生潛在客戶，這些潛在客戶有興趣進一步了解您的產品。 在[Azure Marketplace 試用](https://azuremarketplace.azureedge.net/documents/azure-marketplace-test-drive-program.pdf)產品深入瞭解試用產品。

    ![Marketplace 試用產品範例](./media/cloud-partner-portal-get-customer-leads/test-drive-offer.png)
 

<!-- -->

1. 客戶選取 [立即取得] 後，即同意分享他們的資訊。 此類型潛在客戶是**初始興趣**潛在客戶，我們會在其中分享對取得您的產品感到有興趣之客戶的相關資訊。 潛在客戶位居分享購買漏斗的最頂端。

   ![[立即取得] 選項](./media/cloud-partner-portal-get-customer-leads/get-it-now-button.png)

1. 客戶在 [Azure 入口網站](https://portal.azure.com/)中選取 [購買] 以取得您的產品。 此類型潛在客戶是**主動**潛在客戶，我們會在其中分享已開始部署您的產品之客戶的相關資訊。

   ![購買選項](./media/cloud-partner-portal-get-customer-leads/purchase-button.png)


### <a name="appsource"></a>AppSource

1.  客戶「試用」您的供應項目。 試用產品是一個很好的機會，能讓您加快立即與潛在客戶分享商務產品的腳步，且試用過程毫無任何障礙。 所有試用產品將產生潛在客戶，這些潛在客戶有興趣進一步了解您的產品。 在 [AppSource 試用產品](https://appsource.microsoft.com/blogs/want-to-try-an-app-take-a-test-drive)深入了解試用產品。

    ![試用產品範例](./media/cloud-partner-portal-get-customer-leads/test-drive-offer-2.png)

2.  客戶選取 [立即取得] 後，即同意分享他們的資訊。 此類型潛在客戶是**初始興趣**潛在客戶，我們會在其中分享對取得您的產品感到有興趣之客戶的相關資訊。 潛在客戶位居分享購買漏斗的最頂端。

      ![[立即取得] 選項](./media/cloud-partner-portal-get-customer-leads/get-it-now-button-2.png)


3.  客戶在您的供應項目中選取 [與我連絡]。 此類型潛在客戶是**主動**潛在客戶，我們會在其中分享主動要求持續追蹤您的產品之客戶的相關資訊。

    ![[與我連絡] 選項](./media/cloud-partner-portal-get-customer-leads/contact-me-image.png)

<a name="lead-data"></a>潛在客戶資料
---------

您在客戶擷取程序期間收到的每位潛在客戶，在特定欄位中都有資料。 由於您從多個步驟獲得潛在客戶，處理這些潛在客戶的最佳方法是刪除重複資料，並個人化後續動作。 透過這個方式，每位客戶都能獲得適當的訊息，而您能打造獨特的客戶關係。

### <a name="lead-source"></a>潛在客戶來源

潛在客戶來源的格式為**來源**-**動作** |  **供應**專案

**來源**："AzureMarketplace"、"AzurePortal"、"TestDrive"，和 "AppSource (SPZA)"

**動作**：
- "INS" -- 安裝。 當客戶購買您的產品時，會在 Azure Marketplace 或 AppSource 上進行此動作。
- "PLT" -- 代表合作夥伴主導的試用版。 當客戶使用 [與我連絡] 選項時，會在 AppSource 上進行此動作。
- "DNC" -- 請勿連絡。 當交叉列於您的應用程式頁面的合作夥伴收到連絡要求時，會在 AppSource 上進行此動作。 我們分享此客戶交叉列於您的應用程式的通知，但無須連絡他們。
- "Create" -- 此動作僅位於 Azure 入口網站內，並當客戶在其帳戶中購買您的供應項目時產生。
- "StartTestDrive" -- 此動作僅限試用產品，並當客戶開始進行產品試用時產生。

**供應項目**

下列範例會顯示指派給發行者和特定供應專案的唯一識別碼： checkpoint。 r77-10sg-byol，bitnami. openedxcypress，and docusign. 3701c77e-1cfa-4c56-91e6-3ed0b622145a。


### <a name="customer-info"></a>客戶資訊

下列範例中的欄位顯示了潛在客戶中包含的客戶資訊。
- 名字：John
- 姓氏：Smith
- 電子郵件： jsmith\@microsoft.com
- 電話：1234567890
- 國家/地區：美國
- 公司：Microsoft
- 職稱：技術長

>[!Note]
>上述範例中，並非所有資料一律可供每位潛在客戶使用。

我們目前正積極改善潛在客戶功能，因此，如果您在這裡沒有看到一些您想要的資料欄位，請[傳送您的意見反映給我們](mailto:AzureMarketOnboard@microsoft.com)。

<a name="how-to-connect-your-crm-system-with-the-cloud-partner-portal"></a>如何連接 CRM 系統與 Cloud Partner 入口網站
------------------------------------------------------------

若要開始獲得潛在客戶，我們在 Cloud Partner 入口網站上建立潛在客戶管理連接器，如此您可以輕易導入您的 CRM 資訊，讓我們為您進行連接。 現在您無須耗費浩大的工程心力來整合外部系統，便可以輕鬆地運用由 Marketplace 產生的潛在客戶。

![潛在客戶管理連接器](./media/cloud-partner-portal-get-customer-leads/lead-management-connector.png)

我們可以將潛在客戶寫入各種 CRM 系統，或直接寫入 Azure 儲存體資料表，您可以在資料表中隨心所欲管理潛在客戶。 下列每個連結都提供了連接到可能的潛在客戶目的地的說明：

-   [Dynamics CRM Online](./cloud-partner-portal-lead-management-instructions-dynamics.md)，取得如何設定 Dynamics CRM Online 來獲得潛在客戶的說明。
-   [Marketo](./cloud-partner-portal-lead-management-instructions-marketo.md)，取得如何設定 Marketo Lead 組態來獲得潛在客戶的說明。
-    [Salesforce](./cloud-partner-portal-lead-management-instructions-salesforce.md)，取得如何設定 Salesforce 執行個體來獲得潛在客戶的說明。
-    [Azure 資料表](./cloud-partner-portal-lead-management-instructions-azure-table.md)，取得如何設定 Azure 儲存體帳戶，並在 Azure 資料表獲得潛在客戶的說明。
-   [Https 端點](./cloud-partner-portal-lead-management-instructions-https.md)，取得有關設定 Https 端點以獲得潛在客戶的說明。

設定潛在客戶目的地並發行您的供應項目之後，我們會驗證連線，並將測試潛在客戶傳送給您。 在上架前檢視供應項目時，您也可以試著在預覽環境中自行取得供應項目，藉此測試您的潛在客戶連線。 關鍵是，請務必確定您的潛在客戶設定保持在最新狀態，如此您將不會流失潛在客戶；因此您的商務若有變更，請記得更新這些連接。

<a name="what-next"></a>接下來呢？
----------

技術設定完成後，您應該將這些潛在客戶整合至您目前的銷售與行銷策略，以及營運流程當中。 我們非常感興趣進一步了解您的整體銷售流程，並想與您密切合作，為您提供高品質潛在客戶及足夠的資料，讓您得以成功。 我們歡迎您針對我們如何能最佳化及改善我們傳送給您的潛在客戶，及提供額外資料協助這些客戶達成您的成就，提出您的意見反應。 如果您有興趣[提供意見](mailto:AzureMarketOnboard@microsoft.com)反應和建議，讓您的銷售小組能夠更成功地使用 Marketplace 潛在客戶，請告訴我們。
