---
title: Microsoft 商業 marketplace 中的私用優惠
description: Microsoft 商業 marketplace 中適用于應用程式和服務發行者的私人供應專案。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: vikrambmsft
ms.author: vikramb
ms.date: 04/19/2020
ms.openlocfilehash: b558cbc53ba09a86f7277df0ee5d45a9bdac56e3
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/08/2020
ms.locfileid: "86121005"
---
# <a name="private-offers-in-the-microsoft-commercial-marketplace"></a>Microsoft 商業 marketplace 中的私人優惠

[Microsoft Azure Marketplace](https://azuremarketplace.microsoft.com/)中的私用供應專案可讓發行者建立只有目標客戶才看得見的方案。 在本文中，您將瞭解私用供應專案的選項和優點。

## <a name="unlock-enterprise-deals-with-private-offers"></a>使用私人供應專案解除鎖定企業交易

有愈來愈多的企業客戶利用線上市集來尋找、試用及購買雲端解決方案。 現在透過私人供應項目，發行者可以利用市集私下與目標客戶分享自訂解決方案，其中包含企業所需的功能：

- 「交涉定價」** 可讓發行者延長公開上市供應項目的定價折扣。
- 「私人條款及條件」** 可讓發行者量身打造適合特定客戶的條款及條件。
- 「特製化的設定」** 可讓發行者依據個別客戶的需求，量身打造其虛擬機器、Azure 應用程式和 SaaS 應用程式供應項目。 此選項也可讓發行者提供新產品功能的預覽權限，再更廣泛地推出給所有客戶。

私人供應項目可讓發行者利用公用市集的規模和整體可用性，並享有交涉及達成自訂交易和設定所需的彈性和控制。 這些功能共同創造企業加強採用雲端市集的機會。 企業現在可以按照預期及所需的方式來進行買賣。

私人供應項目目前適用於虛擬機器、Azure 應用程式 (實作為解決方案範本或受控應用程式) 及 SaaS 應用程式供應項目。 

<!--- Like public offers, private offers can be created and managed via the [Cloud Partner Portal](./cloud-partner-portal-orig/cloud-partner-portal-azure-private-skus.md).  Customers can be granted or revoked access to private offers in minutes.
--->

## <a name="creating-private-offers-using-skus-and-plans"></a>使用 Sku 和方案建立私用供應專案

針對「具有公用 SKU 或方案的新供應項目或現有供應項目」**，發行者可以透過建立新的 SKU 或方案並將其標記為私人，輕鬆建立新的私人變體。  

<!--- [Private SKUs](./cloud-partner-portal-orig/cloud-partner-portal-azure-private-skus.md) ---> 

私人 SKU 和方案是供應項目的元件，而且只有目標客戶可以看到及購買。 私人 SKU 和方案可以重複使用已針對公用 SKU 或方案發行的基底映像及 (或) 供應項目中繼資料。 此選項可讓發行者建立公用供應項目的多種私人變體，而不需要發行相同基底映像和供應項目中繼資料的多種版本。 僅針對虛擬機器和 Azure 應用程式供應專案，當私人 SKU 與公用 SKU 共用基底映射時，對供應專案基底映射所做的任何變更，都會使用該基底映射傳播到所有公用和私人 Sku 上。

針對「只包含私人 SKU 或方案的新供應項目」**，發行者可以像是任何其他供應項目一樣建立其供應項目，再將 SKU 或方案標記為私人。 只有私人 Sku 或方案的供應專案，將無法透過 Microsoft 商業 marketplace 或未與供應專案相關聯的客戶[Azure 入口網站](https://azure.microsoft.com/features/azure-portal/)來探索或存取。

## <a name="targeting-customers-with-private-offers"></a>以私用供應專案為目標的客戶

針對新的和現有的私人供應項目，發行者可以使用訂用帳戶識別碼來設定目標客戶。 使用虛擬機器或 Azure 應用程式供應項目的發行者可以將私人 SKU 的可用性限制為個別 Azure 訂用帳戶識別碼，或上傳高達 20,000 個 Azure 訂用帳戶識別碼的 CSV。 使用 SaaS 應用程式私用供應專案時，發行者可以使用手動或 CSV 上傳方法，讓租使用者識別碼與私人方案的可用性產生關聯。

一旦供應項目經過認證並發行，即可使用「同步私人訂用帳戶」功能，只要幾分鐘就能從 SKU 或方案更新或移除客戶。 這項功能可讓發行者快速且輕鬆地更新提供私人 SKU 或方案的客戶清單，而不需再次驗證或發佈供應專案。

## <a name="deploying-private-offers"></a>部署私用供應專案

私人供應專案只能透過[Azure 入口網站](https://azure.microsoft.com/features/azure-portal/)進行探索，而不會顯示在[Microsoft AppSource](https://appsource.microsoft.com/)或[Azure Marketplace](https://azuremarketplace.microsoft.com)中。 若要深入瞭解如何發行至不同的商業 marketplace 店面，請參閱[判斷您的發行選項](./determine-your-listing-type.md)。

登入 Azure 入口網站之後，客戶可以選取 Marketplace 導覽項目來存取其私人供應項目。 私用供應專案也會出現在搜尋結果中，而且可以透過命令列和 Azure Resource Manager 範本來部署，就像任何其他供應專案一樣。

![[私人供應項目]](./media/marketplace-publishers-guide/private-offer.png)

私人供應項目也會出現在搜尋結果中。 只要尋找「私用」徽章。

>[!Note]
>透過雲端解決方案提供者方案（CSP）的轉銷商所建立的訂用帳戶，不支援私用供應專案。

<!---
## Next steps

To start using private offers, follow the steps in the [Private SKUs and Plans](./cloud-partner-portal-orig/cloud-partner-portal-azure-private-skus.md) guide.

--->