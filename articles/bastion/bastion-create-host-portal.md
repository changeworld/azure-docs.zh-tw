---
title: 建立 Azure 防禦主機 |Microsoft Docs
description: 在本文中，您將瞭解如何建立 Azure 防禦主機
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: cherylmc
ms.openlocfilehash: f907dcb4427fd07a2c212e5de91ccce5e8198960
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/04/2020
ms.locfileid: "76990414"
---
# <a name="create-an-azure-bastion-host"></a>建立 Azure 防禦主機

本文說明如何使用 Azure 入口網站建立 Azure 防禦主機。 一旦在您的虛擬網路中布建 Azure 防禦服務，相同虛擬網路中的所有 Vm 都可以使用順暢的 RDP/SSH 體驗。 Azure 防禦部署是針對每個虛擬網路，而不是每個訂用帳戶/帳戶或虛擬機器。

您可以在入口網站中建立新的防禦主機資源，方法是手動指定所有設定，或使用與現有 VM 對應的設定。 （選擇性）您可以使用[Azure Powershell](bastion-create-host-powershell.md)來建立 azure 防禦主機。

## <a name="before-you-begin"></a>開始之前

防禦功能適用于下列 Azure 公用區域：

[!INCLUDE [available regions](../../includes/bastion-regions-include.md)]

## <a name="createhost"></a>建立防禦主機-指定設定

本節可協助您從 Azure 入口網站建立新的 Azure 防禦資源。

1. 在 [ [Azure 入口網站](https://portal.azure.com)] 功能表或 [**首頁**] 頁面上，選取 [**建立資源**]。

1. 在 [**新增**] 頁面的 [*搜尋 Marketplace* ] 欄位中，**輸入防禦**，然後按**Enter 鍵**以取得搜尋結果。

1. 從結果中 **，按一下 [** 防禦]。 確定發行者為 *Microsoft*，且類別 [網路]。

1. 在 [**防禦**] 頁面上，按一下 [**建立**] 以開啟 [**建立**防禦] 頁面。

1. 在 [**建立**防禦] 頁面上，設定新的防禦資源。 指定防禦資源的設定。

    ![建立防禦](./media/bastion-create-host-portal/settings.png)

    * **訂**用帳戶：您想要用來建立新防禦資源的 Azure 訂用帳戶。
    * **資源群組**：將在其中建立新防禦資源的 Azure 資源群組。 如果您沒有現有資源群組，可以建立新的資源群組。
    * **名稱**：新防禦資源的名稱
    * **區域**：將在其中建立資源的 Azure 公用區域。
    * **虛擬網路**：將在其中建立防禦資源的虛擬網路。 在此程式期間，您可以在入口網站中建立新的虛擬網路，或使用現有的虛擬網路。 如果您使用現有的虛擬網路，請確定現有的虛擬網路有足夠的可用位址空間來容納防禦子網需求。
    * **子網**：虛擬網路中將部署新防禦主機資源的子網。 您必須使用 [名稱] 值**AzureBastionSubnet**來建立子網。 此值可讓 Azure 知道要將防禦資源部署到哪一個子網。 這與閘道子網不同。 您必須使用至少/27 或更大（/27、/26 等）的子網。
    
       建立不含任何路由表或委派的**AzureBastionSubnet** 。 如果您在**AzureBastionSubnet**上使用網路安全性群組，請參閱[使用 nsg](bastion-nsg.md)一文。
    * **公用 ip 位址**：將存取 RDP/SSH 之防禦資源的公用 ip （透過埠443）。 建立新的公用 IP，或使用現有的。 公用 IP 位址必須與您建立的防禦資源位於相同的區域中。
    * **公用 ip 位址名稱**：公用 ip 位址資源的名稱。
    * **公用 IP 位址 SKU**：此設定預設為 [**標準**]。 Azure 防禦僅使用/支援標準公用 IP SKU。
    * **指派**：此設定預設會預先填入**靜態**。

1. 當您完成指定設定後，請按一下 [審核] [ **+ 建立**]。 這會驗證這些值。 通過驗證之後，您就可以開始建立程式。
1. 在 [**建立**防禦] 頁面上，按一下 [**建立**]。
1. 您會看到一則訊息，告訴您您的部署正在進行中。 當資源建立時，狀態會顯示在此頁面上。 建立和部署防禦資源需要大約5分鐘的時間。

## <a name="createvmset"></a>建立防禦主機-使用 VM 設定

如果您使用現有的 VM 在入口網站中建立防禦主機，則會自動將各種設定設為對應至您的虛擬機器和/或虛擬網路。

1. 開啟 [Azure 入口網站](https://portal.azure.com)。 移至您的虛擬機器，然後按一下 **[連線]** 。

   ![VM 連接](./media/bastion-create-host-portal/vmsettings.png)
1. 在右側提要**欄位中，按一下 [** 防禦]，然後**使用**[防禦]。

   ![Bastion](./media/bastion-create-host-portal/vmbastion.png)
1. 在 [防禦] 頁面上，填寫下列設定欄位：

   * **名稱**：您想要建立的防禦主機的名稱。
   * **子網**：虛擬網路內部將用來部署防禦資源的子網。 子網必須以名稱**AzureBastionSubnet**建立。 這可讓 Azure 知道要將防禦資源部署到哪一個子網。 這與閘道子網不同。 您必須使用至少/27 或更大（/27、/26 等）的子網。 建立不含任何網路安全性群組、路由表或委派的子網。 如果您稍後選擇使用**AzureBastionSubnet**上的網路安全性群組，請參閱使用[nsg](bastion-nsg.md)。
   
     按一下 [**管理子網**設定] 以建立**AzureBastionSubnet**。  按一下 [**建立**] 以建立子網，然後繼續進行下一設定。
   * **公用 ip 位址**：將存取 RDP/SSH 之防禦資源的公用 ip （透過埠443）。 建立新的公用 IP，或使用現有的。 公用 IP 位址必須與您建立的防禦資源位於相同的區域中。
   * **公用 ip 位址名稱**：公用 ip 位址資源的名稱。
1. 在 [驗證] 畫面上，按一下 [**建立**]。 請等候約5分鐘，以建立和部署防禦資源。

## <a name="next-steps"></a>後續步驟

* 如需其他資訊，請閱讀防禦[常見問題](bastion-faq.md)。

* 若要搭配使用網路安全性群組與 Azure 防禦子網，請參閱使用[nsg](bastion-nsg.md)。