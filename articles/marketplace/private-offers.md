---
title: Microsoft 商業 marketplace 中的私人優惠
description: Microsoft 商用 marketplace 中適用于應用程式和服務發行者的私人優惠。
ms.subservice: partnercenter-marketplace-publisher
ms.service: marketplace
ms.topic: article
author: vikrambmsft
ms.author: vikramb
ms.date: 07/01/2020
ms.openlocfilehash: 2808f0d3b99257d52b2d400424f256f980005863
ms.sourcegitcommit: d661149f8db075800242bef070ea30f82448981e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/19/2020
ms.locfileid: "88607194"
---
# <a name="private-offers-in-the-microsoft-commercial-marketplace"></a>Microsoft 商業 marketplace 中的私人優惠

[Microsoft Azure Marketplace](https://azuremarketplace.microsoft.com/)中的私用供應專案可讓發行者建立只有目標客戶可以看見的方案。 本文討論私用供應專案的選項和優點。

## <a name="unlock-enterprise-deals-with-private-offers"></a>使用私人優惠來解除企業交易的鎖定

有愈來愈多的企業客戶利用線上市集來尋找、試用及購買雲端解決方案。 現在透過私人供應項目，發行者可以利用市集私下與目標客戶分享自訂解決方案，其中包含企業所需的功能：

- 「交涉定價」** 可讓發行者延長公開上市供應項目的定價折扣。
- 「私人條款及條件」** 可讓發行者量身打造適合特定客戶的條款及條件。
- 「特製化的設定」** 可讓發行者依據個別客戶的需求，量身打造其虛擬機器、Azure 應用程式和 SaaS 應用程式供應項目。 此選項也可讓發行者提供新產品功能的預覽權限，再更廣泛地推出給所有客戶。

私人供應項目可讓發行者利用公用市集的規模和整體可用性，並享有交涉及達成自訂交易和設定所需的彈性和控制。 這些功能共同創造企業加強採用雲端市集的機會。 企業現在可以按照預期及所需的方式來進行買賣。

私人供應項目目前適用於虛擬機器、Azure 應用程式 (實作為解決方案範本或受控應用程式) 及 SaaS 應用程式供應項目。

<!--- Like public offers, private offers can be created and managed via the [Cloud Partner Portal](./cloud-partner-portal-orig/cloud-partner-portal-azure-private-skus.md). Customers can be granted or revoked access to private offers in minutes.
--->

## <a name="creating-private-offers-using-plans"></a>使用方案建立私人優惠

若為 *新的或現有的方案*，發行者可以建立新的方案， (先前稱為 sku) ，並將其標示為私用，以輕鬆建立新的私用變數。 每個供應專案最多可有45個私人方案。

<!--- [Private SKUs](./cloud-partner-portal-orig/cloud-partner-portal-azure-private-skus.md) --->

私用計畫是供應專案的元件，只由目標客戶看到及可購買。 私人方案可以重複使用已為公用方案發佈的基底映射及/或供應專案中繼資料。 此選項可讓發行者建立公用供應項目的多種私人變體，而不需要發行相同基底映像和供應項目中繼資料的多種版本。 針對虛擬機器和 Azure 應用程式供應專案，當私人方案與公用方案共用基礎映射時，對供應專案基底映射所做的任何變更，都會使用該基底映射傳播到所有公用和私人方案。

針對 *只包含私人方案的新*供應專案，發行者可以建立其供應專案做為任何其他供應專案，然後將方案標示為私用。 只有私人方案的供應專案將無法透過 Microsoft 商業 marketplace 或未與供應專案相關聯的客戶 [Azure 入口網站](https://azure.microsoft.com/features/azure-portal/) 來探索或存取。

>[!NOTE]
>公用 Azure Marketplace 或 AppSource 中不會顯示僅包含私人方案的供應專案。

## <a name="targeting-customers-with-private-offers"></a>以私用供應專案為客戶提供目標

針對新的和現有的私人供應項目，發行者可以使用訂用帳戶識別碼來設定目標客戶。 使用虛擬機器或 Azure 應用程式供應專案的發行者可以將私人方案的可用性限制為個別的 Azure 訂用帳戶識別碼，或上傳最多20000個 Azure 訂用帳戶識別碼的 CSV。 使用 SaaS 應用程式私用供應專案時，發行者可以使用手動或 CSV 上傳方法，將租使用者識別碼與私人方案的可用性進行關聯。

一旦供應專案通過認證併發布後，就可以使用「同步私用訂閱」功能，在幾分鐘內從方案中更新或移除客戶。 這項功能可讓發行者快速且輕鬆地更新提供私人方案的客戶清單，而不需再次驗證或發佈供應專案。

## <a name="deploying-private-offers"></a>部署私人優惠

私人供應專案只能透過 [Azure 入口網站](https://azure.microsoft.com/features/azure-portal/) 探索，且不會顯示在 [Microsoft AppSource](https://appsource.microsoft.com/) 或 [Azure Marketplace](https://azuremarketplace.microsoft.com)中。 若要深入瞭解如何發行至不同的商業 marketplace 線上商店，請參閱 [決定您的發佈選項](./determine-your-listing-type.md)。

登入 Azure 入口網站之後，客戶可以選取 Marketplace 導覽元素來存取其私用供應專案。 私人供應專案也會出現在搜尋結果中，並可透過命令列和 Azure Resource Manager 範本部署，就像任何其他供應專案一樣。

![[私人供應項目]](./media/marketplace-publishers-guide/private-offer.png)

私人供應項目也會出現在搜尋結果中。 請尋找 **私** 用徽章。

>[!Note]
>透過雲端解決方案提供者 (CSP) 計畫的轉銷商所建立的訂用帳戶不支援私人優惠。


<!---
## Next steps

To start using private offers, follow the steps in the [Private SKUs and Plans](./cloud-partner-portal-orig/cloud-partner-portal-azure-private-skus.md) guide.
--->
