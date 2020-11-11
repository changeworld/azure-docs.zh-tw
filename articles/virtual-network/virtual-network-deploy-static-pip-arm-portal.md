---
title: 建立具有靜態公用 IP 位址的 VM-Azure 入口網站
description: 了解如何使用 Azure 入口網站建立具有靜態公用 IP 位址的 VM。
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
ms.service: virtual-network
ms.subservice: ip-services
ms.devlang: ''
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/12/2020
ms.author: allensu
ms.openlocfilehash: 1ae0b869b24c4e05c88b936eceb1b9b1db3a9405
ms.sourcegitcommit: 4bee52a3601b226cfc4e6eac71c1cb3b4b0eafe2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/11/2020
ms.locfileid: "94506306"
---
# <a name="create-a-virtual-machine-with-a-static-public-ip-address-using-the-azure-portal"></a>使用 Azure 入口網站建立具有靜態公用 IP 位址的虛擬機器

公用 IP 位址可讓您從網際網路與虛擬機器通訊。 

指派靜態公用 IP 位址 (而非動態位址)，以確保位址永遠不會變更。   

## <a name="sign-in-to-azure"></a>登入 Azure

登入 [Azure 入口網站](https://portal.azure.com)。

## <a name="create-a-virtual-machine"></a>建立虛擬機器

1. 在入口網站的左上角，選取 [建立資源] > [計算] > [虛擬機器] 或在搜尋方塊中搜尋 [虛擬機器]。
   
2. 在 [建立虛擬機器] 中，輸入或選取 [基本資訊] 索引標籤中的值：

    | 設定 | 值                                          |
    |-----------------------|----------------------------------|
    | **專案詳細資料** |  |
    | 訂用帳戶 | 選取您的 Azure 訂用帳戶 |
    | 資源群組 | 選取 [建立新的]。 </br> 在 [ **名稱** ] 中，輸入 **myResourceGroup** 。 </br> 選取 [確定]。 |
    | **執行個體詳細資料** |  |
    | 虛擬機器名稱 | 輸入 myVM |
    | 區域 | 選取 [美國東部] |
    | 可用性選項 | 選取 [不需要基礎結構備援] |
    | 映像 | 選取 [Windows Server 2019 Datacenter - Gen1] |
    | Azure Spot 執行個體 | 選取 [否] |
    | 大小 | 選擇 VM 大小或接受預設設定 |
    | **系統管理員帳戶** |  |
    | 使用者名稱 | 輸入使用者名稱 |
    | 密碼 | 輸入密碼 |
    | 確認密碼 | 再次輸入密碼 |

3. 選取 [網路] 索引標籤，或選取 **[下一步：磁碟]** ，然後選取 **[下一步：網路]** 。
  
4. 在 [網路功能] 索引標籤中，選取或輸入：

    | 設定 | 值 |
    |-|-|
    | **網路介面** |  |
    | 虛擬網路 | 接受預設網路名稱。 |
    | 子網路 | 接受預設的子網設定。 |
    | 公用 IP | 選取 [建立新的]。 </br> 在 [ **建立公用 IP 位址** ] 的 [名稱] 中輸入 **myPublicIP** 。 </br> 針對 **SKU** ，請選取 [ **標準** ]。 </br> **指派** ，選取 [ **靜態** ]。 </br> 選取 [確定]。  |
    | NIC 網路安全性群組 | 選取 [基本]|
    | 公用輸入連接埠 | 選取 [允許選取的連接埠]。 |
    | 選取輸入連接埠 | 選取 **RDP (3389)** |

    > [!WARNING]
    > 選取了連接埠 3389，就能夠從網際網路遠端存取 Windows Server 虛擬機器。 不建議在網際網路上開啟埠3389來管理生產工作負載。 </br> 若要安全地存取 Azure 虛擬機器，請參閱 **[什麼是 azure 防禦？](/azure/bastion/bastion-overview)**
   
5. 選取 [檢閱 + 建立]。 
  
6. 檢閱設定，然後選取 [建立]。

## <a name="clean-up-resources"></a>清除資源

當不再需要資源群組時，請將資源群組及其包含的所有資源刪除：

1. 在入口網站頂端的 [搜尋] 方塊中，輸入 myResourceGroup。 當您在搜尋結果中看到 myResourceGroup 時，請加以選取。
2. 選取 [刪除資源群組]。
3. 針對 [輸入資源群組名稱:] 輸入 myResourceGroup，然後選取 [刪除]。

## <a name="next-steps"></a>後續步驟

請參閱 [新增、變更或移除 IP 位址](virtual-network-network-interface-addresses.md)：

* 將公用 IP 位址從動態變更為靜態。
* 使用私人 IP 位址。

公用 IP 位址具有 [名義費用](https://azure.microsoft.com/pricing/details/ip-addresses)。 您可以針對每個訂用帳戶使用的公用 IP 位址數目有 [限制](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) 。

虛擬機器的公用 IP 位址 SKU 必須符合新增至後端集區時 Azure Load Balancer 的公用 IP SKU。 如需詳細資訊，請參閱 [Azure Load Balancer](../load-balancer/skus.md)。

您可以針對 Azure [公開](https://www.microsoft.com/download/details.aspx?id=56519)、[US Gov](https://www.microsoft.com/download/details.aspx?id=57063)、[中國](https://www.microsoft.com/download/details.aspx?id=57062)及[德國](https://www.microsoft.com/download/details.aspx?id=57064)雲端，下載範圍 (前置詞) 清單。

- 深入了解[靜態公用 IP 位址](virtual-network-ip-addresses-overview-arm.md#allocation-method)。
- 深入了解 Azure 中的[公用 IP 位址](virtual-network-ip-addresses-overview-arm.md#public-ip-addresses)。
- 深入了解所有[公用 IP 位址設定](virtual-network-public-ip-address.md#create-a-public-ip-address)。
- 深入瞭解 [私人 ip 位址](virtual-network-ip-addresses-overview-arm.md#private-ip-addresses) ，並將 [靜態私人 ip 位址](virtual-network-network-interface-addresses.md#add-ip-addresses) 指派給 Azure 虛擬機器。
