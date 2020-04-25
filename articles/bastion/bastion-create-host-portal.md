---
title: 建立 Azure 防禦主機：入口網站
description: 在本文中，您將瞭解如何使用入口網站建立 Azure 防禦主機
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 04/24/2020
ms.author: cherylmc
ms.openlocfilehash: 1fa4b6a33b055f2042c9bf941a33ae03ead6ebde
ms.sourcegitcommit: f7fb9e7867798f46c80fe052b5ee73b9151b0e0b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/24/2020
ms.locfileid: "82148345"
---
# <a name="create-an-azure-bastion-host-using-the-portal"></a>使用入口網站建立 Azure 防禦主機

本文說明如何使用 Azure 入口網站建立 Azure 防禦主機。 一旦在您的虛擬網路中布建 Azure 防禦服務，相同虛擬網路中的所有 Vm 都可以使用順暢的 RDP/SSH 體驗。 Azure Bastion 部署依虛擬網路來進行，而非以訂用帳戶/帳戶或虛擬機器為依據。

您可以在入口網站中建立新的防禦主機資源，方法是手動指定所有設定，或使用與現有 VM 對應的設定。 若要使用 VM 設定來建立防禦主機，請參閱[快速入門](quickstart-host-portal.md)文章。 （選擇性）您可以使用[Azure PowerShell](bastion-create-host-powershell.md)來建立 Azure 防禦主機。

## <a name="before-you-begin"></a>在您開始前

防禦功能適用于下列 Azure 公用區域：

[!INCLUDE [available regions](../../includes/bastion-regions-include.md)]

## <a name="create-a-bastion-host"></a><a name="createhost"></a>建立 Bastion 主機

本節可協助您從 Azure 入口網站建立新的 Azure 防禦資源。

1. 從 [Azure 入口網站](https://portal.azure.com)功能表或 [首頁]  頁面，選取 [建立資源]  。

1. 在 [**新增**] 頁面的 [*搜尋 Marketplace* ] 欄位中，**輸入防禦**，然後按**Enter 鍵**以取得搜尋結果。

1. 從結果中 **，按一下 [** 防禦]。 確定發行者為 *Microsoft*，且類別 [網路]**。

1. 在 [**防禦**] 頁面上，按一下 [**建立**] 以開啟 [**建立**防禦] 頁面。

1. 在 [**建立**防禦] 頁面上，設定新的防禦資源。 指定防禦資源的設定。

    ![建立防禦](./media/bastion-create-host-portal/settings.png)

    * **訂**用帳戶：您想要用來建立新防禦資源的 Azure 訂用帳戶。
    * **資源群組**：將在其中建立新防禦資源的 Azure 資源群組。 如果您沒有現有的資源群組，您可以建立一個新的。
    * **名稱**：新防禦資源的名稱
    * **區域**：將在其中建立資源的 Azure 公用區域。
    * **虛擬網路**：將在其中建立防禦資源的虛擬網路。 在此程式期間，您可以在入口網站中建立新的虛擬網路，或使用現有的虛擬網路。 如果您使用現有的虛擬網路，請確定現有的虛擬網路有足夠的可用位址空間來容納防禦子網需求。
    * **子網**：虛擬網路中將部署新防禦主機的子網。 子網將專門用於防禦主機，而且必須命名為**AzureBastionSubnet**。 此子網必須至少為/27 或更大。
    
       **AzureBastionSubnet**不支援[使用者定義的路由](../virtual-network/virtual-networks-udr-overview.md#custom-routes)，但支援[網路安全性群組](bastion-nsg.md)。
    * **公用 ip 位址**：將存取 RDP/SSH 之防禦資源的公用 ip （透過埠443）。 建立新的公用 IP，或使用現有的。 公用 IP 位址必須與您建立的防禦資源位於相同的區域中。
    * **公用 ip 位址名稱**：公用 ip 位址資源的名稱。
    * **公用 IP 位址 SKU**：此設定預設為 [**標準**]。 Azure 防禦僅使用/支援標準公用 IP SKU。
    * **指派**：此設定預設會預先填入**靜態**。

1. 當您完成指定設定後，請按一下 [審核] [ **+ 建立**]。 這會驗證這些值。 通過驗證之後，您就可以開始建立程式。
1. 在 [**建立**防禦] 頁面上，按一下 [**建立**]。
1. 您會看到一則訊息，告訴您您的部署正在進行中。 當資源建立時，狀態會顯示在此頁面上。 建立和部署防禦資源需要大約5分鐘的時間。

## <a name="next-steps"></a>後續步驟

* 如需其他資訊，請閱讀防禦[常見問題](bastion-faq.md)。

* 若要搭配使用網路安全性群組與 Azure 防禦子網，請參閱使用[nsg](bastion-nsg.md)。
