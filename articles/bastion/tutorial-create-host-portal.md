---
title: 教學課程：建立 Azure Bastion 主機：Windows VM：入口網站
description: 在本文中，您將了解如何建立 Azure Bastion 主機並連線至 Windows VM。
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: tutorial
ms.date: 10/13/2020
ms.author: cherylmc
ms.openlocfilehash: daf3b6c36b191f52f0d7ac7638ef59695325f541
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/15/2020
ms.locfileid: "92078539"
---
# <a name="tutorial-create-an-azure-bastion-host-and-connect-to-a-windows-vm-through-a-browser"></a>教學課程：建立 Azure Bastion 主機並透過瀏覽器連線至 Windows VM

本教學課程說明如何使用 Azure Bastion 和 Azure 入口網站，透過瀏覽器連線至虛擬機器。 在 Azure 入口網站中，您可以將 Bastion 部署至您的虛擬網路。 部署 Bastion 之後，您可以使用 Azure 入口網站透過私人 IP 位址連線至 VM。 您的 VM 不需要公用 IP 位址或特殊軟體。 佈建服務後，相同虛擬網路中的所有虛擬機器都將可使用 RDP/SSH 體驗。 如需 Azure Bastion 的詳細資訊，請參閱[什麼是 Azure Bastion？](bastion-overview.md)。

在本教學課程中，您將了解如何：

> [!div class="checklist"]
> * 建立適用於 VNet 的堡壘主機
> * 連線至 Windows 虛擬機器

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 。

## <a name="prerequisites"></a>必要條件

* 虛擬網路。
* 虛擬網路中的 Windows 虛擬機器。
* 下列是必要角色：
  * 虛擬機器上的讀取者角色。
  * 虛擬機器的私人 IP 位址與 NIC 上的讀取者角色。
  * Azure Bastion 資源上的讀者角色。

* 連接埠：若要連線至 Windows VM，您必須在 Windows VM 上開啟下列連接埠：
  * 輸入連接埠：RDP (3389)

## <a name="sign-in-to-the-azure-portal"></a>登入 Azure 入口網站

登入 [Azure 入口網站](https://portal.azure.com)。

## <a name="create-a-bastion-host"></a><a name="createhost"></a>建立 Bastion 主機

本節將協助您在 VNet 中建立堡壘物件。 必須要有此物件，才能對 VNet 中的 VM 建立安全連線。

1. 在 [首頁] 中，選取 [+ 建立資源]。
1. 在 [新增] 頁面上的 [搜尋] 方塊中輸入 **Bastion**，然後選取 [輸入] 以取得搜尋結果。 在 **Bastion** 的結果上，確認發行者為 Microsoft。
1. 選取 [建立]。
1. 在 [建立 Bastion] 頁面上，設定新的 Bastion 資源。

   :::image type="content" source="./media/tutorial-create-host-portal/bastion-basics.png" alt-text="建立 Bastion 主機" lightbox="./media/tutorial-create-host-portal/bastion-basics.png":::

    * 訂用帳戶：您要用來建立新 Bastion 資源的 Azure 訂用帳戶。
    * **資源群組**：將在其中建立新 Bastion 資源的 Azure 資源群組。 如果您沒有現有資源群組，可以建立新的資源群組。
    * **名稱**：新 Bastion 資源的名稱。
    * **區域**：將在其中建立資源的 Azure 公用區域。
    * **虛擬網路**：將在其中建立 Bastion 資源的虛擬網路。 您可以在此程序執行期間於入口網站中建立新的虛擬網路，或使用現有的虛擬網路。 如果您使用現有的虛擬網路，請確定現有的虛擬網路有足夠的可用位址空間來容納 Bastion 子網路需求。 如果您在下拉式清單中看不到您的虛擬網路，請確定您已選取正確的資源群組。
    * **子網路**：在您建立或選取虛擬網路後，子網路欄位就會出現。 虛擬網路中將部署新 Bastion 主機的子網路。 此子網路將專門用於 Bastion 主機。 選取 [管理子網路設定]，然後建立 Azure Bastion 子網路。 選取 [+ 子網路]，並使用下列指導方針建立子網路：

         * 子網路必須命名為 **AzureBastionSubnet**。
         * 子網路至少必須是 /27 或更大。

      您不需要填寫其他欄位。 選取 [確定]，然後在頁面頂端選取 [建立 Bastion]，以返回 [Bastion 設定] 頁面。
    * **公用 IP 位址**：將存取 RDP/SSH (透過連接埠 443) 之 Bastion 資源的公用 IP。 建立新的公用 IP。 公用 IP 位址必須與您建立的 Bastion 資源位於相同的區域中。 此 IP 位址與您要連線到的任何 VM 都無關。 這是 Bastion 主機資源的公用 IP。
    * **公用 IP 位址名稱**：公用 IP 位址資源的名稱。 在本教學課程中，您可以保留預設值。
    * **公用 IP 位址 SKU**：依預設會為此設定預先填入 [標準]。 Azure Bastion 僅使用/支援標準公用 IP SKU。
    * **指派**：依預設會為此設定預先填入 [靜態]。

1. 當您完成設定的指定後，請選取 [檢閱 + 建立]。 值會受到驗證。 驗證通過後，您即可建立 Bastion 資源。
1. 選取 [建立]。
1. 您會看到一則訊息，得知您的部署正在進行中。 資源建立時，狀態會顯示在此頁面上。 建立和部署 Bastion 資源約需要 5 分鐘的時間。

## <a name="connect-to-a-vm"></a>連接到 VM

[!INCLUDE [Connect to a Windows VM](../../includes/bastion-vm-rdp.md)]

## <a name="clean-up-resources"></a>清除資源

如果您不打算繼續使用此應用程式，請使用下列步驟刪除您的資源：

1. 在入口網站頂端的 [搜尋] 方塊中，輸入資源群組的名稱。 當您在搜尋結果中看到資源群組時，請加以選取。
1. 選取 [刪除資源群組]。
1. 在 [輸入資源群組名稱:] 中，輸入您的資源群組名稱，然後選取 [刪除]。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已建立 Bastion 主機並將其與虛擬網路相關聯，然後連線至 Windows VM。 您可以選擇搭配使用網路安全性群組與 Azure Bastion 子網路。 若要這麼做，請參閱：

> [!div class="nextstepaction"]
> [使用 NSG](bastion-nsg.md)
