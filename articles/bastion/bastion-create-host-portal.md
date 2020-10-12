---
title: 建立 Azure 堡壘主機：入口網站
description: 在本文中，您將瞭解如何建立 Azure 堡壘主機，以提供對虛擬網路中所有虛擬機器的 RDP/SSH 連線能力。
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: how-to
ms.date: 04/24/2020
ms.author: cherylmc
ms.openlocfilehash: 1e0bc9a17a12afac6d91867b19aba0f90b7f6edb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "88270745"
---
# <a name="create-an-azure-bastion-host-using-the-portal"></a>使用入口網站建立 Azure 堡壘主機

本文說明如何使用 Azure 入口網站建立 Azure 堡壘主機。 當您在虛擬網路中布建 Azure 防禦服務之後，就可以在相同虛擬網路中的所有 Vm 上使用順暢的 RDP/SSH 體驗。 Azure Bastion 部署依虛擬網路來進行，而非以訂用帳戶/帳戶或虛擬機器為依據。

您可以在入口網站中建立新的防禦主機資源，方法是手動指定所有設定，或使用對應至現有 VM 的設定。 若要使用 VM 設定建立堡壘主機，請參閱 [快速入門](quickstart-host-portal.md) 文章。 （選擇性）您可以使用 [Azure PowerShell](bastion-create-host-powershell.md) 來建立 Azure 堡壘主機。

## <a name="before-you-begin"></a>開始之前

防禦功能可在下列 Azure 公用區域中取得：

[!INCLUDE [available regions](../../includes/bastion-regions-include.md)]

## <a name="create-a-bastion-host"></a><a name="createhost"></a>建立 Bastion 主機

本節可協助您從 Azure 入口網站建立新的 Azure 防禦資源。

1. 在 [Azure 入口網站](https://portal.azure.com) 功能表上，或從 [首頁] 頁面，選取 [建立資源]。

1. 在 [ **新增** ] 頁面的 [ *搜尋 Marketplace* ] 欄位中， **輸入 [** 防禦]，然後按一下 [ **輸入** ] 以取得搜尋結果。

1. 從結果中 **，按一下 [** 防禦]。 確定發行者為 *Microsoft*，且類別 [網路]**。

1. 在 [防禦 **] 頁面上，按一下 [** **建立** ] 以開啟 [ **建立** 防禦] 頁面。

1. 在 [ **建立** 防禦] 頁面上，設定新的防禦資源。 指定防禦資源的設定。

    ![建立防禦](./media/bastion-create-host-portal/settings.png)

    * **訂**用帳戶：您要用來建立新防禦資源的 Azure 訂用帳戶。
    * **資源群組**：將在其中建立新防禦資源的 Azure 資源群組。 如果您沒有現有的資源群組，您可以建立一個新的資源群組。
    * **名稱**：新的防禦資源名稱
    * **區域**：將在其中建立資源的 Azure 公用區域。
    * **虛擬網路**：將在其中建立防禦資源的虛擬網路。 您可以在此程式期間于入口網站中建立新的虛擬網路，或使用現有的虛擬網路。 如果您使用現有的虛擬網路，請確定現有的虛擬網路具有足夠的可用位址空間，以容納防禦子網的需求。
    * **子網**：虛擬網路中將部署新的防禦主機的子網。 子網將專門用於防禦主機，而且必須命名為 **AzureBastionSubnet**。 此子網必須至少為/27 或更大。
    
       **AzureBastionSubnet** 不支援 [使用者定義的路由](../virtual-network/virtual-networks-udr-overview.md#custom-routes)，但支援 [網路安全性群組](bastion-nsg.md)。
    * **公用 ip 位址**：透過埠 443)  (RDP/SSH 存取的防禦資源的公用 ip。 建立新的公用 IP 或使用現有 IP。 公用 IP 位址必須與您建立的 Bastion 資源位於相同的區域中。
    * **公用 IP 位址名稱**：公用 IP 位址資源的名稱。
    * **公用 IP 位址 SKU**：此設定預設會預先填入 **標準**。 Azure 防禦使用/僅支援標準的公用 IP SKU。
    * **指派**：此設定預設會預先填入 **靜態**。

1. 當您完成指定設定之後，請按一下 [ **審核 + 建立**]。 這會驗證值。 通過驗證之後，您就可以開始建立程式。
1. 在 [ **建立** 防禦] 頁面上，按一下 [ **建立**]。
1. 您會看到一則訊息，讓您知道您的部署正在進行中。 當建立資源時，狀態將會顯示在此頁面上。 建立及部署防禦資源大約需要5分鐘的時間。

## <a name="next-steps"></a>後續步驟

* 如需其他資訊，請參閱防禦 [常見問題](bastion-faq.md) 。

* 若要搭配使用網路安全性群組與 Azure Bastion子網路，請參閱[使用 NSG](bastion-nsg.md)。
