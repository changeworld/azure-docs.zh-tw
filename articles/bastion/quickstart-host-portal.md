---
title: 快速入門：使用私人 IP 位址連線至虛擬機器：Azure Bastion
description: 在本文中，您將了解如何從虛擬機器建立 Azure Bastion 主機，並使用私人 IP 位址進行安全連線。
services: bastion
author: charwen
ms.service: bastion
ms.topic: quickstart
ms.date: 03/11/2020
ms.author: charwen
ms.openlocfilehash: b0155ae92e3179918273d6a19773aa15b67949ea
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/05/2020
ms.locfileid: "90985595"
---
# <a name="quickstart-connect-to-a-virtual-machine-using-a-private-ip-address-and-azure-bastion"></a>快速入門：使用私人 IP 位址和 Azure Bastion 連線至虛擬機器

本快速入門文章說明如何使用私人 IP 位址連線至虛擬機器。 透過 Bastion 連線時，虛擬機器不需要公用 IP 位址。 本文中的步驟可協助您透過入口網站中的虛擬機器，將 Azure Bastion 部署至您的虛擬網路。 佈建服務後，相同虛擬網路中的所有虛擬機器都將可使用 RDP/SSH 體驗。

## <a name="prerequisites"></a><a name="prereq"></a>必要條件

* Azure 虛擬網路。
* 有 Azure 虛擬機器位於虛擬網路中，且連接埠 3389 已開啟。

### <a name="example-values"></a>範例值

|**名稱** | **ReplTest1** |
| --- | --- |
| 名稱 |  VNet1Bastion |
| 區域 | eastus |
| 虛擬網路 |  VNet1 |
| + 子網路名稱 | AzureBastionSubnet |
| AzureBastionSubnet 位址 |  10.1.254.0/27 |
| 公用 IP 位址 |  新建 |
| 公用 IP 位址名稱 | VNet1BastionPIP  |
| 公用 IP 位址 SKU |  標準  |
| 指派  | 靜態 |

## <a name="create-a-bastion-host"></a><a name="createvmset"></a>建立 Bastion 主機

當您使用現有的虛擬機器在入口網站中建立堡壘主機時，各種設定將自動預設為對應至您的虛擬機器和/或虛擬網路。

1. 開啟 [Azure 入口網站](https://portal.azure.com)。 移至您的虛擬機器，然後按一下 [連線]****。

   ![虛擬機器設定](./media/quickstart-host-portal/vm-settings.png)
1. 從下拉式清單中，選取 [Bastion]****。
1. 在 [連線] 頁面上，選取 [使用 Bastion]****。

   ![選取 Bastion](./media/quickstart-host-portal/select-bastion.png)

1. 在 [Bastion] 頁面上，填寫下列設定欄位：

   * **Name**：為堡壘主機命名
   * **子網路**：虛擬網路中將部署 Bastion 資源的子網路。 此子網路必須以名稱 **AzureBastionSubnet** 建立。 此名稱可讓 Azure 知道要將 Bastion 資源部署到哪一個子網路。 這與閘道子網路不同。 請使用至少 /27 或更大的子網路 (/27、/26、/25 等)。
   
      * 選取 [管理子網路設定]****，然後選取 [+ 子網路]****。
      * 在 [新增子網路] 頁面上，輸入 **AzureBastionSubnet**。
      * 以 CIDR 標記法指定位址範圍。 例如 10.1.254.0/27。
      * 選取 [確定]**** 以建立子網路。 在頁面頂端，瀏覽回 [Bastion] 以完成其餘設定。

         ![瀏覽至堡壘設定](./media/quickstart-host-portal/navigate-bastion.png)
   * **公用 IP 位址**：這是將存取 RDP/SSH (透過連接埠 443) 之 Bastion 資源的公用 IP。 建立新的公用 IP 或使用現有 IP。 公用 IP 位址必須與您建立的 Bastion 資源位於相同的區域中。
   * **公用 IP 位址名稱**：公用 IP 位址資源的名稱。
1. 在 [驗證] 畫面上，按一下 [建立]****。 請等候約 5 分鐘，以建立和部署 Bastion 資源。

   ![建立堡壘主機](./media/quickstart-host-portal/bastion-settings.png)

## <a name="connect"></a><a name="connect"></a>連線

將 Bastion 部署至虛擬網路後，畫面會變更為 [連線] 頁面。

1. 輸入虛擬機器的使用者名稱和密碼。 然後，選取 [連線]****。

   ![螢幕擷取畫面顯示 [使用 Azure Bastion 連線] 對話方塊，該對話方塊會提示您輸入使用者名稱和密碼。](./media/quickstart-host-portal/connect.png)
1. 透過 Bastion 的這個虛擬機器 RDP 連線，會使用連接埠 443 和 Bastion 服務直接在 Azure 入口網站中開啟 (透過 HTML5)。

   ![RDP 連線](./media/quickstart-host-portal/443-rdp.png)

## <a name="clean-up-resources"></a>清除資源

當您使用完虛擬網路與虛擬機器時，請刪除資源群組以及其包含的所有資源：

1. 在入口網站頂端的 [搜尋]**** 方塊中輸入 *TestRG1*，然後從搜尋結果中選取 [TestRG1]****。

2. 選取 [刪除資源群組]****。

3. 針對 [輸入資源群組名稱]**** 輸入 TestRG1**，然後選取 [刪除]****。

## <a name="next-steps"></a>後續步驟

在本快速入門中，您已建立虛擬網路的 Bastion 主機，然後透過 Bastion 主機安全地連線至虛擬機器。

* 若要深入了解 Azure Bastion，請閱讀 [Bastion 概觀](bastion-overview.md)和 [Bastion 常見問題集](bastion-faq.md)。
* 若要搭配使用網路安全性群組與 Azure Bastion子網路，請參閱[使用 NSG](bastion-nsg.md)。
* 如需包含 Azure Bastion主機設定說明的指示，請參閱[教學課程](bastion-create-host-portal.md)。
* 若要連線至虛擬機器擴展集，請參閱[使用 Azure Bastion 連線至虛擬機器擴展集](bastion-connect-vm-scale-set.md)。