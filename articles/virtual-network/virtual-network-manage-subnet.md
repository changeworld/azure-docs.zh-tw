---
title: 加入、變更或刪除 Azure 虛擬網路子網路
titlesuffix: Azure Virtual Network
description: 瞭解如何在 Azure 中尋找虛擬網路的相關資訊，以及如何新增、變更或刪除虛擬網路子網。
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/20/2020
ms.author: kumud
ms.openlocfilehash: 15fe5d6d16948875253d65e70d9d440214a4a2e8
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/25/2020
ms.locfileid: "95995653"
---
# <a name="add-change-or-delete-a-virtual-network-subnet"></a>加入、變更或刪除虛擬網路子網路

了解如何加入、變更或刪除虛擬網路子網路。 所有部署到虛擬網路的 Azure 資源都會部署到虛擬網路內的子網路。 如果您不熟悉虛擬網路，您可以在 [虛擬網路總覽](virtual-networks-overview.md) 或完成 [快速入門](quick-create-portal.md)中深入瞭解。 若要深入瞭解如何管理虛擬網路，請參閱 [建立、變更或刪除虛擬網路](manage-virtual-network.md)。

## <a name="before-you-begin"></a>開始之前

如果您沒有帳戶，請使用有效的訂用帳戶來設定 Azure 帳戶。 [免費建立帳戶](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。 然後，在啟動本文任何章節的步驟之前，先完成下列其中一項工作： 

- **入口網站使用者**：使用您的 Azure 帳戶登入 [Azure 入口網站](https://portal.azure.com) 。

- **PowerShell 使用者**：請在 [Azure Cloud Shell](https://shell.azure.com/powershell)中執行命令，或從您的電腦執行 powershell。 Azure Cloud Shell 是免費的互動式 Shell，可讓您用來執行本文中的步驟。 它具有預先安裝和設定的共用 Azure 工具，可與您的帳戶搭配使用。 在 Azure Cloud Shell 瀏覽器] 索引標籤中，尋找 [ **選取環境** ] 下拉式清單，然後選擇 [ **PowerShell** ] （如果尚未選取）。

    如果您在本機執行 PowerShell，請使用 Azure PowerShell 模組1.0.0 版或更新版本。 執行 `Get-Module -ListAvailable Az.Network` 來了解安裝的版本。 如果您需要升級，請參閱[安裝 Azure PowerShell 模組](/powershell/azure/install-az-ps)。 也 `Connect-AzAccount` 可執行以建立與 Azure 的連線。

- **Azure 命令列介面 (CLI) 使用者**：請在 [Azure Cloud Shell](https://shell.azure.com/bash)中執行命令，或從您的電腦執行 cli。 如果您是在本機執行 Azure CLI，請使用 Azure CLI 版本2.0.31 版或更新版本。 執行 `az --version` 來了解安裝的版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI](/cli/azure/install-azure-cli)。 也 `az login` 可執行以建立與 Azure 的連線。

您用來登入或連線到 Azure 的帳戶必須指派給「[網路參與者」角色](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)角色，或指派給已獲指派 [[許可權](#permissions)] 中所列適當動作的[自訂角色](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。

## <a name="add-a-subnet"></a>新增子網路

1. 移至 [Azure 入口網站](https://portal.azure.com) 以查看您的虛擬網路。 搜尋並選取 [虛擬網路]。

2. 選取您要新增子網的虛擬網路名稱。

3. 在 [**設定**] 中，選取 [**子** 網  >  **子網**]。

4. 在 [ **新增子網** ] 對話方塊中，輸入下列設定的值：

    | 設定 | 描述 |
    | --- | --- |
    | **名稱** | 名稱在虛擬網路內必須是唯一的。 如需與其他 Azure 服務的最大相容性，我們建議使用字母作為名稱的第一個字元。 例如，Azure 應用程式閘道不會部署到名稱以數字開頭的子網路。 |
    | **位址範圍** | <p>此範圍必須是虛擬網路位址空間內的唯一範圍。 此範圍不能與虛擬網路內的其他子網位址範圍重迭。 位址空間必須以「無類別網域間路由選擇」(CIDR) 標記法來指定。</p><p>例如，在位址空間為 *10.0.0.0/16* 的虛擬網路中，您可能會定義 *10.0.0.0/22* 的子網位址空間。 您可以指定的最小範圍為 */29*，可為子網提供八個 IP 位址。 為了符合通訊協定的規定，Azure 會保留每個子網路中的第一個和最後一個位址。 Azure 還會保留三個位址供 Azure 服務使用。 因此，使用 */29* 位址範圍定義子網會導致子網中有三個可用的 IP 位址。</p><p>如果您打算將虛擬網路連線至 VPN 閘道，則必須建立一個閘道子網路。 深入了解[閘道子網路位址範圍的具體考量](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md?toc=%2fazure%2fvirtual-network%2ftoc.json#gwsub)。 若符合特定條件，您可以在子網路加入後變更其位址範圍。 若要了解如何變更子網路的位址範圍，請參閱[變更子網路設定](#change-subnet-settings)。</p> |
    | **網路安全性群組** | 若要篩選子網的輸入和輸出網路流量，您可以將現有的網路安全性群組與子網產生關聯。 網路安全性群組必須與虛擬網路位於相同的訂用帳戶和位置當中。 深入了解[網路安全性群組](security-overview.md)與[如何建立網路安全性群組](tutorial-filter-network-traffic.md)。 |
    | **路由表** | 若要控制路由至其他網路的網路流量，您可以選擇性地將現有的路由表與子網產生關聯。 路由表必須與虛擬網路位於相同的訂用帳戶和位置當中。 深入瞭解 [Azure 路由](virtual-networks-udr-overview.md) 及 [如何建立路由表](tutorial-create-route-table-portal.md)。 |
    | **服務端點** | <p>子網可能會選擇性地為它啟用一或多個服務端點。 若要啟用服務的服務端點，請選取服務或從 [服務] 清單中選取您想要啟用服務端點的服務。 Azure 會自動設定端點的位置。 根據預設，Azure 會設定虛擬網路區域的服務端點。 為了支援區域性容錯移轉案例，Azure 會自動將端點設定為 [Azure 配對的區域](../best-practices-availability-paired-regions.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-paired-regions) 以進行 Azure 儲存體。</p><p>若要移除服務端點，請取消選取您想要移除其服務端點的服務。 若要深入瞭解服務端點，以及可以啟用的服務端點，請參閱 [虛擬網路服務端點](virtual-network-service-endpoints-overview.md)。 一旦您啟用服務的服務端點，您也必須為以服務建立的資源啟用子網路的網路存取權。 例如，如果您啟用 **Microsoft.Storage** 的服務端點，您也必須對想要授與網路存取權的所有 Azure 儲存體帳戶啟用網路存取權。 若要啟用服務端點啟用之子網的網路存取，請參閱您啟用服務端點之個別服務的檔。</p><p>若要驗證是否已為子網路啟用服務端點，請查看子網路中任何網路介面的[有效路由](diagnose-network-routing-problem.md)。 當您設定端點時，您會看到 *預設* 路由，其中包含服務的位址前置詞，以及下一個躍點類型為 **VirtualNetworkServiceEndpoint**。 若要深入瞭解路由，請參閱 [虛擬網路流量路由](virtual-networks-udr-overview.md)。</p> |
    | **子網路委派** | 子網可能會選擇性地為它啟用一或多個委派。 子網委派提供明確的許可權給服務，以在服務部署期間使用唯一識別碼在子網中建立服務特定資源。 若要為服務進行委派，請從 [服務] 清單選取您要委派到的服務。 |

5. 若要在所選虛擬網路中新增子網路，請選取 [確定]。

### <a name="commands"></a>命令

| 工具 | 命令 |
| ---- | ------- |
| Azure CLI | [az network vnet subnet create](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-create) |
| PowerShell | [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig) |

## <a name="change-subnet-settings"></a>變更子網路設定

1. 移至 [Azure 入口網站](https://portal.azure.com) 以查看您的虛擬網路。 搜尋並選取 [虛擬網路]。

2. 選取包含您要變更之子網的虛擬網路名稱。

3. 在 [ **設定**] 中，選取 [ **子網**]。

4. 在子網路清單中，選取您想要變更設定的子網路。

5. 在 [子網] 頁面中，變更下列任何設定：

    | 設定 | 描述 |
    | --- | --- |
    | **位址範圍** | 如果子網路內沒有部署資源，您可以變更位址範圍。 如果子網路中有任何資源存在，您必須將資源移至另一個子網路，或先從子網路中刪除。 不同資源的資源移動或刪除步驟也各異。 若要瞭解如何移動或刪除子網中的資源，請閱讀這些資源類型的相關檔。 請參閱 [新增子網](#add-a-subnet)的步驟4中的 **位址範圍** 條件約束。 |
    | **使用者** | 您可以使用內建角色或自有的自訂角色來控制子網路的存取。 若要深入瞭解如何指派角色和使用者以存取子網，請參閱 [新增角色指派](../role-based-access-control/role-assignments-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-a-role-assignment)。 |
    | **網路安全性群組** 和 **路由表** | 請參閱 [新增子網](#add-a-subnet)的步驟4。 |
    | **服務端點** | <p>請參閱 [新增子網](#add-a-subnet)的步驟4中的服務端點。 當啟用現有子網路的服務端點時，請確定子網路中的任何資源上都沒有重要的工作正在執行。 服務端點會在子網中的每個網路介面上切換路由。 服務端點會使用具有 *0.0.0.0/0* 位址首碼和下一個躍點類型 *網際網路* 的預設路由，以使用新的路由，其具有服務的位址前置詞和 *VirtualNetworkServiceEndpoint* 的下一個躍點類型。</p><p>在切換期間，任何開啟的 TCP 連接都可能會終止。 除非已使用新的路由更新所有網路介面的流量流向服務，否則不會啟用服務端點。 若要深入瞭解路由，請參閱 [虛擬網路流量路由](virtual-networks-udr-overview.md)。</p> |
    | **子網路委派** | 請參閱 [新增子網](#add-a-subnet)的步驟4中的服務端點。 子網路委派可以修改為有零或多個為它啟用的委派。 如果已在子網中部署服務的資源，則在移除服務的所有資源之前，無法新增或移除子網委派。 若要為不同的服務進行委派，請從 [服務] 清單選取您要委派到的服務。 |

6. 選取 [儲存]。

### <a name="commands"></a>命令

| 工具 | 命令 |
| ---- | ------- |
| Azure CLI | [az network vnet subnet update](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-update) |
| PowerShell | [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig) |

## <a name="delete-a-subnet"></a>刪除子網路

您只能刪除未包含任何資源的子網路。 如果資源位於子網中，您必須先刪除這些資源，才能刪除子網。 不同資源的資源刪除步驟也各異。 若要瞭解如何刪除子網中的資源，請閱讀這些資源類型的相關檔。

1. 移至 [Azure 入口網站](https://portal.azure.com) 以查看您的虛擬網路。 搜尋並選取 [虛擬網路]。

2. 選取包含您要刪除之子網的虛擬網路名稱。

3. 在 [ **設定**] 中，選取 [ **子網**]。

4. 在子網清單中，選取您要刪除的子網。

5. 選取 [ **刪除**]，然後在確認對話方塊中選取 **[是** ]。

### <a name="commands"></a>命令

| 工具 | 命令 |
| ---- | ------- |
| Azure CLI | [az network vnet subnet delete](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-delete) |
| PowerShell | [移除->new-azvirtualnetworksubnetconfig](/powershell/module/az.network/remove-azvirtualnetworksubnetconfig?toc=%2fazure%2fvirtual-network%2ftoc.json) |

## <a name="permissions"></a>權限

若要在子網上執行工作，您的帳戶必須指派給「 [網路參與者」角色](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) ，或指派給已在下表中指派適當動作的 [自訂角色](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ：

|動作                                                                   |   名稱                                       |
|-----------------------------------------------------------------------  |   -----------------------------------------  |
|Microsoft.Network/virtualNetworks/subnets/read                           |   讀取虛擬網路子網路              |
|Microsoft.Network/virtualNetworks/subnets/write                          |   建立或更新虛擬網路子網路  |
|Microsoft.Network/virtualNetworks/subnets/delete                         |   刪除虛擬網路子網路            |
|Microsoft.Network/virtualNetworks/subnets/join/action                    |   加入虛擬網路                     |
|Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action  |   啟用子網路的服務端點     |
|Microsoft.Network/virtualNetworks/subnets/virtualMachines/read           |   取得子網路中的虛擬機器       |

## <a name="next-steps"></a>後續步驟

- 使用 [PowerShell](powershell-samples.md) 或 [Azure CLI](cli-samples.md) 範例指令碼，或使用 Azure [Resource Manager 範本](template-samples.md)建立虛擬網路和子網路
- 建立並指派虛擬網路的[Azure 原則定義](policy-samples.md)
