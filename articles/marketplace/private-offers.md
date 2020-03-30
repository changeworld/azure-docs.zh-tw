---
title: 私人優惠 |Azure 應用商店
description: Azure Marketplace 中適用於應用程式和服務發行者的私人供應項目。
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/1/2018
ms.author: dsindona
ms.openlocfilehash: 67aba077304117ad357d4e004ce7bdb25ac58352
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80285039"
---
# <a name="private-offers"></a>私人供應項目

[Microsoft Azure Marketplace](https://azuremarketplace.microsoft.com/) 上的私人供應項目可讓發行者建立僅向目標客戶顯示的 SKU。

## <a name="unlock-enterprise-deals-with-private-offers"></a>透過私人供應項目達成企業交易

有愈來愈多的企業客戶利用線上市集來尋找、試用及購買雲端解決方案。 現在透過私人供應項目，發行者可以利用市集私下與目標客戶分享自訂解決方案，其中包含企業所需的功能：

- 「交涉定價」** 可讓發行者延長公開上市供應項目的定價折扣。
- 「私人條款及條件」** 可讓發行者量身打造適合特定客戶的條款及條件。
- 「特製化的設定」** 可讓發行者依據個別客戶的需求，量身打造其虛擬機器、Azure 應用程式和 SaaS 應用程式供應項目。 此選項也可讓發行者提供新產品功能的預覽權限，再更廣泛地推出給所有客戶。

私人供應項目可讓發行者利用公用市集的規模和整體可用性，並享有交涉及達成自訂交易和設定所需的彈性和控制。 這些功能共同創造企業加強採用雲端市集的機會。  企業現在可以按照預期及所需的方式來進行買賣。

私人供應項目目前適用於虛擬機器、Azure 應用程式 (實作為解決方案範本或受控應用程式) 及 SaaS 應用程式供應項目。 如同公用供應項目，您可以透過 [Cloud Partner 入口網站](./cloud-partner-portal-orig/cloud-partner-portal-azure-private-skus.md)建立及管理私人供應項目。  只要幾分鐘，就能授與或撤銷客戶對私人供應項目的存取。

## <a name="creating-private-offers-using-skus-and-plans"></a>建立使用 SKU 和方案的私人供應項目

針對「具有公用 SKU 或方案的新供應項目或現有供應項目」**，發行者可以透過建立新的 SKU 或方案並將其標記為私人，輕鬆建立新的私人變體。  [私人 SKU](./cloud-partner-portal-orig/cloud-partner-portal-azure-private-skus.md) 和方案是供應項目的元件，而且只有目標客戶可以看到及購買。 私人 SKU 和方案可以重複使用已針對公用 SKU 或方案發行的基底映像及 (或) 供應項目中繼資料。 此選項可讓發行者建立公用供應項目的多種私人變體，而不需要發行相同基底映像和供應項目中繼資料的多種版本。 對於虛擬機器和 Azure 應用程式，僅當專用 SKU 與公共 SKU 共用基本映射時，對產品/服務基本映射的任何更改都將使用該基本映射在所有公共和私有 SKU 中傳播。

針對「只包含私人 SKU 或方案的新供應項目」**，發行者可以像是任何其他供應項目一樣建立其供應項目，再將 SKU 或方案標記為私人。 與供應項目無關的客戶將無法透過 [Azure Marketplace](https://azuremarketplace.microsoft.com) 或 [Azure 入口網站](https://azure.microsoft.com/features/azure-portal/)探索或存取只包含私人 SKU 或方案的供應項目。

## <a name="targeting-customers-with-private-offers"></a>設定私人供應項目的目標客戶
針對新的和現有的私人供應項目，發行者可以使用訂用帳戶識別碼來設定目標客戶。 使用虛擬機器或 Azure 應用程式供應項目的發行者可以將私人 SKU 的可用性限制為個別 Azure 訂用帳戶識別碼，或上傳高達 20,000 個 Azure 訂用帳戶識別碼的 CSV。 使用 SaaS 應用程式私人供應項目時，發行者可以手動或使用 CSV 上傳方法來與 Azure 訂用帳戶識別碼或租用戶識別碼相關聯，藉此限制私人方案的可用性。

一旦供應項目經過認證並發行，即可使用「同步私人訂用帳戶」功能，只要幾分鐘就能從 SKU 或方案更新或移除客戶。 這項功能可讓發行者快速輕鬆地更新要對其顯示私人 SKU 或方案的客戶清單，而不需要重新認證或重新發行供應項目。

## <a name="deploying-private-offers"></a>部署私人供應項目

私人供應項目只能透過 [Azure 入口網站](https://azure.microsoft.com/features/azure-portal/)探索，不會透過 [Azure Marketplace](https://azuremarketplace.microsoft.com) 呈現。 登入 Azure 入口網站之後，客戶可以選取 Marketplace 導覽項目來存取其私人供應項目。 私人供應項目也會出現在搜尋結果中，並可透過命令列和 Azure Resource Manager 範本部署，就像是任何其他供應項目一樣。

![[私人供應項目]](./media/marketplace-publishers-guide/private-offer.png)

私人供應項目也會出現在搜尋結果中。 只要留意"私人"徽章。

>[!Note]
>通過雲解決方案供應商計畫 （CSP） 的轉銷商建立的訂閱不支援私人優惠。

## <a name="next-steps"></a>後續步驟

要開始使用私人優惠，請按照["私人 SKU"和"計畫](./cloud-partner-portal-orig/cloud-partner-portal-azure-private-skus.md)"指南中的步驟操作。
