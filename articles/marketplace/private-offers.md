---
title: 私人優惠 |Azure 應用商店
description: Azure Marketplace 中適用於應用程式和服務發行者的私人供應項目。
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/19/2020
ms.author: dsindona
ms.openlocfilehash: efda9fb66a65f1f2cd40de8bf32a8a3fa99aea7f
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/21/2020
ms.locfileid: "81681439"
---
# <a name="private-offers"></a>私人供應項目

[Microsoft Azure Marketplace](https://azuremarketplace.microsoft.com/) 上的私人供應項目可讓發行者建立僅向目標客戶顯示的 SKU。

## <a name="unlock-enterprise-deals-with-private-offers"></a>解鎖企業交易與私人優惠

有愈來愈多的企業客戶利用線上市集來尋找、試用及購買雲端解決方案。 現在透過私人供應項目，發行者可以利用市集私下與目標客戶分享自訂解決方案，其中包含企業所需的功能：

- 「交涉定價」** 可讓發行者延長公開上市供應項目的定價折扣。
- 「私人條款及條件」** 可讓發行者量身打造適合特定客戶的條款及條件。
- 「特製化的設定」** 可讓發行者依據個別客戶的需求，量身打造其虛擬機器、Azure 應用程式和 SaaS 應用程式供應項目。 此選項也可讓發行者提供新產品功能的預覽權限，再更廣泛地推出給所有客戶。

私人供應項目可讓發行者利用公用市集的規模和整體可用性，並享有交涉及達成自訂交易和設定所需的彈性和控制。 這些功能共同創造企業加強採用雲端市集的機會。 企業現在可以按照預期及所需的方式來進行買賣。

私人供應項目目前適用於虛擬機器、Azure 應用程式 (實作為解決方案範本或受控應用程式) 及 SaaS 應用程式供應項目。 

<!--- Like public offers, private offers can be created and managed via the [Cloud Partner Portal](./cloud-partner-portal-orig/cloud-partner-portal-azure-private-skus.md).  Customers can be granted or revoked access to private offers in minutes.
--->

## <a name="creating-private-offers-using-skus-and-plans"></a>使用 SKU 和計畫建立私人優惠

針對「具有公用 SKU 或方案的新供應項目或現有供應項目」**，發行者可以透過建立新的 SKU 或方案並將其標記為私人，輕鬆建立新的私人變體。  

<!--- [Private SKUs](./cloud-partner-portal-orig/cloud-partner-portal-azure-private-skus.md) ---> 

私人 SKU 和方案是供應項目的元件，而且只有目標客戶可以看到及購買。 私人 SKU 和方案可以重複使用已針對公用 SKU 或方案發行的基底映像及 (或) 供應項目中繼資料。 此選項可讓發行者建立公用供應項目的多種私人變體，而不需要發行相同基底映像和供應項目中繼資料的多種版本。 對於虛擬機和 Azure 應用程式,僅當專用 SKU 與公共 SKU 共用基本映射時,對產品/服務基本映射的任何更改都將使用該基本映射在所有公共和私有 SKU 中傳播。

針對「只包含私人 SKU 或方案的新供應項目」**，發行者可以像是任何其他供應項目一樣建立其供應項目，再將 SKU 或方案標記為私人。 只有私有 SKU 或計畫的產品/服務將不能通過 Microsoft 商業市場或[Azure 門戶](https://azure.microsoft.com/features/azure-portal/)被與產品/服務無關的客戶發現或訪問。

## <a name="targeting-customers-with-private-offers"></a>透過私人優惠定位客戶

針對新的和現有的私人供應項目，發行者可以使用訂用帳戶識別碼來設定目標客戶。 使用虛擬機器或 Azure 應用程式供應項目的發行者可以將私人 SKU 的可用性限制為個別 Azure 訂用帳戶識別碼，或上傳高達 20,000 個 Azure 訂用帳戶識別碼的 CSV。 使用 SaaS App 私人產品/服務時,發行者可以使用手動或 CSV 上載方法關聯租戶 ID 以限制私人計劃的可用性。

一旦供應項目經過認證並發行，即可使用「同步私人訂用帳戶」功能，只要幾分鐘就能從 SKU 或方案更新或移除客戶。 此功能使發佈商無需再次認證或發佈產品/服務,即可快速輕鬆地更新向其提交私有 SKU 或計劃的客戶清單。

## <a name="deploying-private-offers"></a>部署私人產品/服務

專用產品/服務只能通過 Azure[門戶](https://azure.microsoft.com/features/azure-portal/)進行發現,並且不會顯示在[Microsoft AppSource](https://appsource.microsoft.com/)或[Azure 應用商店](https://azuremarketplace.microsoft.com)中。 要瞭解有關發佈到不同商業市場店面的資訊,請參閱[確定發佈選項](./determine-your-listing-type.md)。

登入 Azure 入口網站之後，客戶可以選取 Marketplace 導覽項目來存取其私人供應項目。 私有產品/服務也會顯示在搜尋結果中,並且可以通過命令行和 Azure 資源管理器範本(如任何其他產品/服務)進行部署。

![[私人供應項目]](./media/marketplace-publishers-guide/private-offer.png)

私人供應項目也會出現在搜尋結果中。 只需查找"私人"徽章。

>[!Note]
>通過雲端解決方案供應商計畫 (CSP) 的轉銷商建立的訂閱不支援私人優惠。

<!---
## Next steps

To start using private offers, follow the steps in the [Private SKUs and Plans](./cloud-partner-portal-orig/cloud-partner-portal-azure-private-skus.md) guide.

--->