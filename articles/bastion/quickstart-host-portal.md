---
title: 快速入門：從 VM 建立 Bastion 主機並透過私人 IP 位址連線
titleSuffix: Azure Bastion
description: 在本快速入門文章中，您將了解如何從虛擬機器建立 Azure Bastion 主機，並使用私人 IP 位址進行安全連線。
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: quickstart
ms.date: 10/12/2020
ms.author: cherylmc
ms.openlocfilehash: 6f451e7b115c00bc7b2cf350e00b9f704ab1d29f
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/14/2020
ms.locfileid: "92019047"
---
# <a name="quickstart-connect-to-a-virtual-machine-using-a-private-ip-address-and-azure-bastion"></a>快速入門：使用私人 IP 位址和 Azure Bastion 連線至虛擬機器

本快速入門文章說明如何使用 Azure Bastion 和 Azure 入口網站，透過瀏覽器連線至虛擬機器。 在 Azure 入口網站中，您可以從 Azure VM 將 Bastion 部署至您的虛擬網路。 部署 Bastion 之後，您可以使用 Azure 入口網站透過其私人 IP 位址連線到 VM。 您的 VM 不需要公用 IP 位址或特殊軟體。 直接從您的 VM 為 VNet 建立 Bastion 主機的優點之一，就是會為您預先填入許多設定。

佈建服務後，相同虛擬網路中的所有虛擬機器都將可使用 RDP/SSH 體驗。 如需 Azure Bastion 的詳細資訊，請參閱[什麼是 Azure Bastion？](bastion-overview.md)。

## <a name="prerequisites"></a><a name="prereq"></a>必要條件

* 虛擬網路。
* 虛擬網路中的 Windows 虛擬機器。
* 下列是必要角色：
  * 虛擬機器上的讀取者角色。
  * 虛擬機器的私人 IP 位址與 NIC 上的讀取者角色。

* 連接埠：若要連線到 VM，您必須在 VM 上開啟下列連接埠：
  * 輸入連接埠：RDP (3389)

### <a name="example-values"></a>範例值

|**名稱** | **ReplTest1** |
| --- | --- |
| 名稱 |  TestVNet1-bastion |
| 虛擬網路 |  TestVNet1 (以 VM 為基礎) |
| + 子網路名稱 | AzureBastionSubnet |
| AzureBastionSubnet 位址 |  10.1.254.0/27 |
| 公用 IP 位址 |  新建 |
| 公用 IP 位址名稱 | VNet1BastionPIP  |
| 公用 IP 位址 SKU |  標準  |
| 指派  | 靜態 |

## <a name="create-a-bastion-host"></a><a name="createvmset"></a>建立 Bastion 主機

當您使用現有的虛擬機器在 Azure 入口網站中建立堡壘主機時，各種設定將自動預設為對應至您的虛擬機器和/或虛擬網路。

1. 開啟 [Azure 入口網站](https://portal.azure.com)。 移至您的虛擬機器，然後選取 [連線]。

   :::image type="content" source="./media/quickstart-host-portal/vm-settings.png" alt-text="虛擬機器設定" lightbox="./media/quickstart-host-portal/vm-settings.png":::
1. 從下拉式清單中，選取 [Bastion]****。
1. 在 [TestVM | 連線頁面] 上，選取 [使用 Bastion]。

   :::image type="content" source="./media/quickstart-host-portal/select-bastion.png" alt-text="虛擬機器設定" border="false":::

1. 在 [Bastion] 頁面上，填寫下列設定欄位：

   * **名稱**：為堡壘主機命名。
   * **子網路**：虛擬網路中將部署 Bastion 資源的子網路。 此子網路必須以名稱 **AzureBastionSubnet** 建立。 此名稱可讓 Azure 知道要將 Bastion 資源部署到哪一個子網路。 這與閘道子網路不同。 請使用至少 /27 或更大的子網路 (/27、/26、/25 等)。
   
      * 選取 [管理子網路組態]。
      * 選取 [AzureBastionSubnet]。
      * 如有需要，以 CIDR 標記法調整位址範圍。 例如 10.1.254.0/27。
      * 請勿調整任何其他設定。 選取 [確定] 以接受並儲存子網路變更，如果您不想進行任何變更，請選取頁面頂端的 **x**。
1. 按一下瀏覽器上的上一頁按鈕，瀏覽回到 [Bastion] 頁面，然後繼續指定值。
   * **公用 IP 位址名稱**：公用 IP 位址資源的名稱。
   * **公用 IP 位址**：這是將存取 RDP/SSH (透過連接埠 443) 之 Bastion 資源的公用 IP。 建立新的公用 IP。
1. 選取 [建立] 以建立堡壘主機。 Azure 會驗證您的設定，然後建立主機。 主機及其資源需要大約 5 分鐘的時間來建立和部署。

   :::image type="content" source="./media/quickstart-host-portal/validate.png" alt-text="虛擬機器設定":::

## <a name="connect"></a><a name="connect"></a>連線

將 Bastion 部署至虛擬網路後，畫面會變更為 [連線] 頁面。

1. 輸入虛擬機器的使用者名稱和密碼。 然後，選取 [連線]****。

   :::image type="content" source="./media/quickstart-host-portal/connect-vm.png" alt-text="虛擬機器設定":::
1. 透過 Bastion 的這個虛擬機器 RDP 連線，會使用連接埠 443 和 Bastion 服務直接在 Azure 入口網站中開啟 (透過 HTML5)。

   :::image type="content" source="./media/quickstart-host-portal/connected.png" alt-text="虛擬機器設定":::

## <a name="clean-up-resources"></a>清除資源

當您使用完虛擬網路與虛擬機器時，請刪除資源群組以及其包含的所有資源：

1. 在入口網站頂端的 [搜尋] 方塊中輸入您的資源群組，然後從搜尋結果中進行選取。

1. 選取 [刪除資源群組]。

1. 針對 [輸入資源群組名稱] 輸入您的資源群組，然後選取 [刪除]。

## <a name="next-steps"></a>後續步驟

在本快速入門中，您已建立虛擬網路的 Bastion 主機，然後透過 Bastion 主機安全地連線至虛擬機器。 接下來，如果您想要連線至虛擬機器擴展集，可以繼續進行下列步驟。

> [!div class="nextstepaction"]
> [使用 Azure Bastion 連線到虛擬機器擴展集](bastion-connect-vm-scale-set.md)
