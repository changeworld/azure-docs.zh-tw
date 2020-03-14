---
title: 快速入門：使用私人 IP 位址連接到虛擬機器： Azure 防禦
description: 在本文中，您將瞭解如何從虛擬機器建立 Azure 防禦主機，並使用私人 IP 位址安全地連接。
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: cherylmc
ms.openlocfilehash: 0b4f0a1fa4f9cce05aa544bb24e045209a6d24ef
ms.sourcegitcommit: 05a650752e9346b9836fe3ba275181369bd94cf0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/12/2020
ms.locfileid: "79137453"
---
# <a name="quickstart-connect-to-a-virtual-machine-using-a-private-ip-address-and-azure-bastion"></a>快速入門：使用私人 IP 位址和 Azure 防禦連接到虛擬機器

本快速入門文章說明如何使用私人 IP 位址連接到虛擬機器。 當您透過防禦進行連線時，您的虛擬機器不需要公用 IP 位址。 這篇文章中的步驟可協助您透過入口網站中的虛擬機器，將防禦部署至您的虛擬網路。 布建服務之後，就可以在相同虛擬網路中的所有虛擬機器使用 RDP/SSH 體驗。

## <a name="prereq"></a>必要條件

* Azure 虛擬網路。
* 位於虛擬網路中的 Azure 虛擬機器，其埠3389已開啟。

### <a name="example-values"></a>範例值

|**名稱** | **ReplTest1** |
| --- | --- |
| 名稱 |  VNet1Bastion |
| 區域 | eastus |
| 虛擬網路 |  VNet1 |
| + 子網名稱 | AzureBastionSubnet |
| AzureBastionSubnet 位址 |  10.1.254.0/27 |
| 公用 IP 位址 |  新建 |
| 公用 IP 位址名稱 | VNet1BastionPIP  |
| 公用 IP 位址 SKU |  標準  |
| 指派  | 靜態 |

## <a name="createvmset"></a>建立防禦主機

當您使用現有的虛擬機器在入口網站中建立防禦主機時，會自動將各種設定設為對應至您的虛擬機器和/或虛擬網路。

1. 開啟 [Azure 入口網站](https://portal.azure.com)。 移至您的虛擬機器，然後按一下 **[連線]** 。

   ![虛擬機器設定](./media/quickstart-host-portal/vm-settings.png)
1. 從下拉式清單**中選取 [** 防禦]。
1. 在 [連線] 頁面上，選取 [**使用**防禦]。

   ![選取防禦](./media/quickstart-host-portal/select-bastion.png)

1. 在 [防禦] 頁面上，填寫下列設定欄位：

   * **名稱**：為防禦主機命名
   * **子網**：虛擬網路內部將用來部署防禦資源的子網。 子網必須以名稱**AzureBastionSubnet**建立。 此名稱可讓 Azure 知道要將防禦資源部署到哪一個子網。 這與閘道子網不同。 使用至少為/27 或更大（/27、/26、/25 等）的子網。
   
      * 選取 [**管理子網**設定]，然後選取 [ **+ 子網**]。
      * 在 [新增子網] 頁面上，輸入**AzureBastionSubnet**。
      * 以 CIDR 標記法指定位址範圍。 例如，10.1.254.0/27。
      * 選取 **[確定]** 以建立子網。 在頁面頂端，流覽回到 [防禦] 以完成其餘設定。

         ![流覽至防禦設定](./media/quickstart-host-portal/navigate-bastion.png)
   * **公用 ip 位址**：這是將存取 RDP/SSH 的防禦資源公用 ip （透過埠443）。 建立新的公用 IP，或使用現有的。 公用 IP 位址必須與您建立的防禦資源位於相同的區域中。
   * **公用 ip 位址名稱**：公用 ip 位址資源的名稱。
1. 在 [驗證] 畫面上，按一下 [**建立**]。 請等候約5分鐘，以建立和部署防禦資源。

   ![建立防禦主機](./media/quickstart-host-portal/bastion-settings.png)

## <a name="connect"></a>到

將防禦部署至虛擬網路之後，畫面會變更為 [連接] 頁面。

1. 輸入虛擬機器的使用者名稱和密碼。 然後，選取 [連線]。

   ![連線](./media/quickstart-host-portal/connect.png)
1. 透過防禦的此虛擬機器的 RDP 連線，會使用埠443和防禦服務，直接在 Azure 入口網站（透過 HTML5）中開啟。

   ![RDP 連接](./media/quickstart-host-portal/443-rdp.png)

## <a name="clean-up-resources"></a>清除資源

當您完成使用虛擬網路和虛擬機器時，請刪除資源群組及其包含的所有資源：

1. 在入口網站頂端的**搜尋**方塊中輸入*TestRG1* ，然後從搜尋結果中選取 [ **TestRG1** ]。

2. 選取 [刪除資源群組]。

3. 針對 [輸入**資源組名**] 輸入*TestRG1* ，然後選取 [**刪除**]。

## <a name="next-steps"></a>後續步驟

在本快速入門中，您已建立虛擬網路的防禦主機，然後透過防禦主機安全地連線到虛擬機器。

* 若要深入瞭解 Azure 防禦，請閱讀防禦[總覽](bastion-overview.md)和防禦[常見問題](bastion-faq.md)。
* 若要搭配使用網路安全性群組與 Azure 防禦子網，請參閱使用[nsg](bastion-nsg.md)。
* 如需包含 Azure 防禦主機設定說明的指示，請參閱[教學](bastion-create-host-portal.md)課程。
* 若要連線至虛擬機器擴展集，請參閱[使用 Azure 防禦連線到虛擬機器擴展集](bastion-connect-vm-scale-set.md)。