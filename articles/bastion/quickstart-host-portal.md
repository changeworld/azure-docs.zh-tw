---
title: 快速入門：設定 Azure Bastion 並透過私人 IP 位址和瀏覽器連線至 VM
titleSuffix: Azure Bastion
description: 在本快速入門文章中，您將了解如何從虛擬機器建立 Azure Bastion 主機，並透過瀏覽器使用私人 IP 位址安全地連線到 VM。
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: quickstart
ms.date: 10/15/2020
ms.author: cherylmc
ms.openlocfilehash: 325f39b695d80c14ed7097d071380b937458546c
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/25/2020
ms.locfileid: "96021481"
---
# <a name="quickstart-connect-to-a-vm-securely-through-a-browser-via-private-ip-address"></a>快速入門：透過瀏覽器使用私人 IP 位址安全地連線到 VM

您可以使用 Azure 入口網站和 Azure Bastion，透過瀏覽器連線到虛擬機器 (VM)。 本快速入門文章說明如何根據您的 VM 設定來設定 Azure Bastion，然後透過入口網站連線到您的 VM。 VM 不需要公用 IP 位址、用戶端軟體、代理程式或特殊設定。 佈建服務後，相同虛擬網路中的所有虛擬機器都將可使用 RDP/SSH 體驗。 如需 Azure Bastion 的詳細資訊，請參閱[什麼是 Azure Bastion？](bastion-overview.md)。

## <a name="prerequisites"></a><a name="prereq"></a>必要條件

* 具有有效訂用帳戶的 Azure 帳戶。 如果您沒有帳戶，請[免費建立一個](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。 若要能夠透過瀏覽器使用 Bastion 連線到 VM，您必須能夠登入 Azure 入口網站。

* 虛擬網路中的 Windows 虛擬機器。 如果您沒有 VM，請建立一個，方法是使用[快速入門：建立 VM](../virtual-machines/windows/quick-create-portal.md)。

  * 如果您需要範例值，請參閱提供的[範例值](#values)。
  * 如果您已經有虛擬網路，請務必在建立 VM 時，於 [網路] 索引標籤上加以選取。
  * 如果您還沒有虛擬網路，則可以在建立 VM 的同時建立虛擬網路。
  * 您不需要有此 VM 的公用 IP 位址，就能透過 Azure Bastion 進行連線。

* 所需的 VM 角色：
  * 虛擬機器上的讀取者角色。
  * 虛擬機器的私人 IP 位址與 NIC 上的讀取者角色。
  
* 所需的 VM 連接埠：
  * 輸入連接埠：RDP (3389)

### <a name="example-values"></a><a name="values"></a>範例值

在建立此設定時，您可以使用下列範例值，也可以替換為自己的值。

**基本的 VNet 和 VM 值：**

|**名稱** | **ReplTest1** |
| --- | --- |
| 虛擬機器| TestVM |
| 資源群組 | TestRG |
| 區域 | 美國東部 |
| 虛擬網路 | TestVNet1 |
| 位址空間 | 10.0.0.0/16 |
| 子網路 | FrontEnd：10.0.0.0/24 |

**Azure Bastion 值：**

|**名稱** | **ReplTest1** |
| --- | --- |
| 名稱 | TestVNet1-bastion |
| + 子網路名稱 | AzureBastionSubnet |
| AzureBastionSubnet 位址 | VNet 位址空間內具有 /27 子網路遮罩的子網路。 例如，10.0.1.0/27。  |
| 公用 IP 位址 |  新建 |
| 公用 IP 位址名稱 | VNet1BastionPIP  |
| 公用 IP 位址 SKU |  標準  |
| 指派  | 靜態 |

## <a name="create-a-bastion-host"></a><a name="createvmset"></a>建立 Bastion 主機

有幾種不同的方式可設定堡壘主機。 在下列步驟中，您將直接從您的 VM 在 Azure 入口網站中建立堡壘主機。 當您從 VM 建立主機時，系統會自動填入與您的虛擬機器及/或虛擬網路相對應的各種設定。

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 瀏覽至您想要連線的 VM，然後選取 [連線]。

   :::image type="content" source="./media/quickstart-host-portal/vm-settings.png" alt-text="虛擬機器設定" lightbox="./media/quickstart-host-portal/vm-settings.png":::
1. 從下拉式清單中，選取 [Bastion]。
1. 在 [TestVM | 連線頁面] 上，選取 [使用 Bastion]。

   :::image type="content" source="./media/quickstart-host-portal/select-bastion.png" alt-text="選取 Bastion" border="false":::

1. 在 [Bastion] 頁面上，填寫下列設定欄位：

   * **名稱**：為堡壘主機命名。
   * **子網路**：這個虛擬網路位址空間會是 Bastion 資源的部署目的地。 此子網路必須以名稱 **AzureBastionSubnet** 建立。 請使用至少 /27 或更大的子網路 (/27、/26、/25 等)。
   * 選取 [管理子網路組態]。
1. 在 [子網路] 頁面上，選取 [+子網路]。

   :::image type="content" source="./media/quickstart-host-portal/subnet.png" alt-text="+ 子網路":::
    
1. 在 [新增子網路] 頁面上，於 [名稱] 中輸入 **AzureBastionSubnet**。
   * 在 [子網路位址範圍] 中，選擇位於虛擬網路位址空間內的子網路位址。
   * 請勿調整任何其他設定。 選取 [確定] 以接受並儲存子網路變更。

   :::image type="content" source="./media/quickstart-host-portal/add-subnet.png" alt-text="新增子網路":::
1. 按一下瀏覽器上的 [上一頁] 按鈕，瀏覽回到 [Bastion] 頁面，然後繼續指定值。
   * **公用 IP 位址**：維持 [新建]。
   * **公用 IP 位址名稱**：公用 IP 位址資源的名稱。
   * **指派**：預設為 [靜態]。 您無法為 Azure Bastion 使用 [動態] 指派。
   * **資源群組**：與 VM 相同的資源群組。

   :::image type="content" source="./media/quickstart-host-portal/validate.png" alt-text="建立堡壘主機":::
1. 選取 [建立] 以建立堡壘主機。 Azure 會驗證您的設定，然後建立主機。 主機及其資源需要大約 5 分鐘的時間來建立和部署。

## <a name="connect"></a><a name="connect"></a>連線

將 Bastion 部署至虛擬網路後，畫面會變更為 [連線] 頁面。

1. 輸入虛擬機器的使用者名稱和密碼。 然後，選取 [連線]。

   :::image type="content" source="./media/quickstart-host-portal/connect-vm.png" alt-text="螢幕擷取畫面顯示 [使用 Azure Bastion 進行連線] 對話方塊。":::
1. 這個虛擬機器的 RDP 連線會使用連接埠 443 和 Bastion 服務，直接在 Azure 入口網站中開啟 (透過 HTML5)。

   :::image type="content" source="./media/quickstart-host-portal/connected.png" alt-text="RDP 連線":::

## <a name="clean-up-resources"></a>清除資源

當您使用完虛擬網路與虛擬機器時，請刪除資源群組以及其包含的所有資源：

1. 在入口網站頂端的 [搜尋] 方塊中輸入您的資源群組，然後從搜尋結果中進行選取。

1. 選取 [刪除資源群組]。

1. 針對 [輸入資源群組名稱] 輸入您的資源群組，然後選取 [刪除]。

## <a name="next-steps"></a>後續步驟

在本快速入門中，您已建立虛擬網路的堡壘主機，然後透過 Bastion 安全地連線至虛擬機器。 接下來，如果您想要連線至虛擬機器擴展集，可以繼續進行下列步驟。

> [!div class="nextstepaction"]
> [使用 Azure Bastion 連線到虛擬機器擴展集](bastion-connect-vm-scale-set.md)
