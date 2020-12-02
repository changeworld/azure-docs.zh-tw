---
title: 什麼是試用產品？ Microsoft 商業 marketplace
description: Marketplace 試用產品功能的說明
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: trkeya
ms.author: trkeya
ms.date: 06/19/2020
ms.openlocfilehash: 193e7bd78046a1d73cb55171c15c9cb6a7278297
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/02/2020
ms.locfileid: "96490035"
---
# <a name="what-is-a-test-drive"></a>什麼是試用產品？

試用產品是向潛在客戶展示供應專案的絕佳方式，方法是讓他們在購買之前先試用，產生高度合格的潛在客戶並產生更高的轉換。 試用產品可讓您的產品在真實世界的實施案例中保持生活。 試用您的產品的客戶會示範如何購買類似的解決方案。 您可以藉由遵循更先進的潛在客戶，來使用這項優點。

您的客戶也受益于試用產品。 藉由讓他們先試用您的產品，您就能減少購買程式的分歧。 此外，也會預先布建試用產品，亦即，客戶不需要下載、設定或設定產品。

## <a name="how-does-it-work"></a>運作方式

試用產品是受控實例，可依需求針對要求的客戶啟動您的解決方案或應用程式。 一旦指派了試用產品實例之後，該客戶就可在一段設定的期間內使用該實例。 期限結束後，就會將其刪除，為另一個客戶建立空間。

您可以使用「發行者」來管理和設定合作夥伴中心中的「試用產品」設定。 技術設定詳細資料會根據供應專案類型而有所不同。 如需詳細指引，請參閱 [試用產品技術](./test-drive-technical-configuration.md)設定。

潛在客戶探索您的試用產品，以 CTA 您在 [AppSource](https://appsource.microsoft.com/en-US/)上的供應專案。 他們會提供他們的連絡人資訊，並同意您供應專案的條款和隱私權原則，然後取得您預先設定的環境，以在一段固定期間內試用。 客戶可獲得實際操作的自我引導式試用產品的主要功能和優點，並獲得重要的潛在客戶。

## <a name="types-of-test-drives"></a>試用產品的類型

根據產品、案例和您所在的 marketplace 類型而定，商用 marketplace 上有不同的試用產品可供選擇：

- Azure Resource Manager
    - Azure 應用程式
    - SaaS
    - 虛擬機器
- 託管的試用產品
    - 目前不支援 Business Central (的 Dynamics 365) 
    - Dynamics 365 for Customer Engagement
    - Dynamics 365 for Operations
- 邏輯應用程式只在支援模式下 () 
- Power BI

如需設定其中一個試用產品的詳細資訊，請參閱 [試用產品技術](./test-drive-technical-configuration.md)設定。 

### <a name="azure-resource-manager-test-drive"></a>Azure Resource Manager 試用產品

此部署範本包含所有組成解決方案的 Azure 資源。 適用於此案例的產品為僅使用 Azure 資源的產品。 Azure Resource Manager 試用產品適用于下列供應專案類型： 

- Azure 應用程式
- SaaS
- 虛擬機器

>[!NOTE]
>這是虛擬機器和 Azure 應用程式供應專案的唯一試用產品選項。

### <a name="hosted-test-drive-recommended"></a> (建議的託管試用產品) 

託管的試用產品可讓 Microsoft 主機和維護執行試用產品使用者布建及取消布建的服務，以免除安裝程式的複雜性。 如果您有 Microsoft AppSource 的供應專案，請建立您的試用產品以與 Dynamics AX/CRM 實例連接。 您可以使用下列 AppSource 提供類型：

- 針對 Customer Engagement 系統（例如銷售、服務、專案服務和現場服務）使用 [Dynamics 365 For Customer engagement](partner-center-portal/create-new-customer-engagement-offer.md) 。
- 針對財務和營運企業資源規劃系統（例如財務、營運和製造業、供應鏈）使用 [Dynamics 365 進行作業](partner-center-portal/create-new-operations-offer.md) 。

### <a name="logic-app-test-drive"></a>邏輯應用程式試用產品

這種類型的試用產品並非由 Microsoft 所裝載，而且會針對 Dynamics AX/CRM 供應專案類型使用 Azure Resource Manager (ARM) 範本。 您將需要執行 ARM 範本，才能在您的 Azure 訂用帳戶中建立必要的資源。 邏輯應用程式試用產品目前僅支援支援模式，Microsoft 不建議您如需有關設定邏輯應用程式試用產品的詳細資訊，請參閱 [試用產品技術](./test-drive-technical-configuration.md)設定。

### <a name="power-bi-test-drive"></a>Power BI 試用產品

這只是自訂建立儀表板的內嵌連結。 任何只示範互動式 Power BI 視覺效果的產品，都應該使用此類型的試用產品。

## <a name="transforming-examples"></a>轉換範例

將資源架構轉換為試用產品的流程可能會很困難。 請參閱這些範例以瞭解如何以最佳方式轉換目前的架構。

[將網站範本轉換為試用產品](https://github.com/Azure/AzureTestDrive/wiki/Transforming-Website-Deployment-Template-for-Test-Drive)

[將虛擬機器範本轉換為試用產品](https://github.com/Azure/AzureTestDrive/wiki/Transforming-Virtual-Machine-Deployment-Template-for-Test-Drive)

[將現有的 Resource Manager 範本轉換為試用產品](https://github.com/Azure/AzureTestDrive/wiki/Deploying-Existing-Solutions)

## <a name="generate-leads-from-your-test-drive"></a>從您的試用產品產生潛在客戶

商用 marketplace 試用產品是行銷人員的絕佳工具。 我們建議您在推出時將其納入進入市場，以產生更多潛在客戶給您的業務。 如需詳細指引，請參閱 [您的商業 marketplace 供應專案客戶潛在客戶](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/marketplace/partner-center-portal/commercial-marketplace-get-customer-leads.md)。

如果您關閉了試用產品潛在客戶的交易，請務必在 [Microsoft 合作夥伴銷售 Connect](https://support.microsoft.com/help/3155788/getting-started-with-microsoft-partner-sales-connect)註冊。 此外，我們也很樂意聽到您的客戶在試用產品扮演角色的地方獲勝。

## <a name="other-resources"></a>其他資源

其他試用產品資源：

- [試用產品的最佳作法](https://github.com/Azure/AzureTestDrive/wiki/Test-Drive-Best-Practices)
- [概觀](https://assetsprod.microsoft.com/mpn/azure-marketplace-appsource-test-drives.pdf) (PDF；請確定您已關閉快顯封鎖程式)

## <a name="next-step"></a>後續步驟

- [試用產品技術設定](test-drive-technical-configuration.md)