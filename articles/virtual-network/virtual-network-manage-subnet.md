---
title: 加入、變更或刪除 Azure 虛擬網路子網路
titlesuffix: Azure Virtual Network
description: 了解如何加入、變更或刪除 Azure 中的虛擬網路子網路。
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/20/2020
ms.author: kumud
ms.openlocfilehash: e5b78969f6b4315bb02b3f4152c6eeab94adddb8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80246937"
---
# <a name="add-change-or-delete-a-virtual-network-subnet"></a>加入、變更或刪除虛擬網路子網路

了解如何加入、變更或刪除虛擬網路子網路。 所有部署到虛擬網路的 Azure 資源都會部署到虛擬網路內的子網路。 如果您是虛擬網路的新增功能，則可以在[虛擬網路概述](virtual-networks-overview.md)或完成[快速入門](quick-create-portal.md)中瞭解有關它們的詳細資訊。 要瞭解有關管理虛擬網路的更多資訊，請參閱[創建、更改或刪除虛擬網路](manage-virtual-network.md)。

## <a name="before-you-begin"></a>開始之前

如果沒有，則使用活動訂閱設置 Azure 帳戶。 [免費建立帳戶](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。 然後，在本文任何部分中啟動步驟之前完成這些任務之一： 

- **門戶使用者**：使用 Azure 帳戶登錄到[Azure 門戶](https://portal.azure.com)。

- **PowerShell 使用者**：在[Azure 雲殼](https://shell.azure.com/powershell)中運行命令，或者從電腦運行 PowerShell。 Azure Cloud Shell 是免費的互動式 Shell，可讓您用來執行本文中的步驟。 它具有預先安裝和設定的共用 Azure 工具，可與您的帳戶搭配使用。 在 Azure 雲殼瀏覽器選項卡中，查找 **"選擇環境**"下拉清單，如果尚未選擇 **"PowerShell"，請選擇 PowerShell。**

    如果在本地運行 PowerShell，請使用 Azure PowerShell 模組版本 1.0.0 或更高版本。 執行 `Get-Module -ListAvailable Az.Network` 來了解安裝的版本。 如果您需要升級，請參閱[安裝 Azure PowerShell 模組](/powershell/azure/install-az-ps)。 還要運行`Connect-AzAccount`以創建與 Azure 的連接。

- **Azure 命令列介面 （CLI） 使用者**：在 Azure[雲外殼](https://shell.azure.com/bash)中運行命令，或從電腦運行 CLI。 如果您在本地運行 Azure CLI，請使用 Azure CLI 版本 2.0.31 或更高版本。 執行 `az --version` 來了解安裝的版本。 如果需要安裝或升級，請參閱[安裝 Azure CLI](/cli/azure/install-azure-cli)。 還要運行`az login`以創建與 Azure 的連接。

必須將登錄到或連接到 Azure 的帳戶分配給[網路參與者角色](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)或已分配[許可權](#permissions)中列出的相應操作的[自訂角色](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。

## <a name="add-a-subnet"></a>新增子網路

1. 轉到[Azure 門戶](https://portal.azure.com)以查看虛擬網路。 搜索並選擇**虛擬網路**。

2. 選擇要向其添加子網的虛擬網路的名稱。

3. 從 **"設置"** 中，選擇**子** > **網子網**。

4. 在 **"添加子網"** 對話方塊中，輸入以下設置的值：

    | 設定 | 描述 |
    | --- | --- |
    | **名稱** | 名稱在虛擬網路內必須是唯一的。 如需與其他 Azure 服務的最大相容性，我們建議使用字母作為名稱的第一個字元。 例如，Azure 應用程式閘道不會部署到名稱以數字開頭的子網路。 |
    | **位址範圍** | <p>此範圍必須是虛擬網路位址空間內的唯一範圍。 範圍不能與虛擬網路中的其他子網位址範圍重疊。 位址空間必須以「無類別網域間路由選擇」(CIDR) 標記法來指定。</p><p>例如，在位址空間*為 10.0.0.0/16*的虛擬網路中，可以定義*10.0.0.0/22*的子網位址空間。 可以指定的最小範圍是 */29*，它為子網提供八個 IP 位址。 為了符合通訊協定的規定，Azure 會保留每個子網路中的第一個和最後一個位址。 Azure 還會保留三個位址供 Azure 服務使用。 因此，使用 */29*位址範圍定義子網會導致子網中出現三個可用的 IP 位址。</p><p>如果您打算將虛擬網路連線至 VPN 閘道，則必須建立一個閘道子網路。 深入了解[閘道子網路位址範圍的具體考量](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md?toc=%2fazure%2fvirtual-network%2ftoc.json#gwsub)。 若符合特定條件，您可以在子網路加入後變更其位址範圍。 若要了解如何變更子網路的位址範圍，請參閱[變更子網路設定](#change-subnet-settings)。</p> |
    | **網路安全性群組** | 要篩選子網的入站和出站網路流量，可以將現有網路安全性群組關聯到子網。 網路安全性群組必須與虛擬網路位於相同的訂用帳戶和位置當中。 深入了解[網路安全性群組](security-overview.md)與[如何建立網路安全性群組](tutorial-filter-network-traffic.md)。 |
    | **路由表** | 要控制網路流量路由到其他網路，可以選擇將現有路由表關聯到子網。 路由表必須與虛擬網路位於相同的訂用帳戶和位置當中。 詳細瞭解[Azure 路由](virtual-networks-udr-overview.md)[以及如何創建路由表](tutorial-create-route-table-portal.md)。 |
    | **服務終結點** | <p>子網可以選擇為其啟用一個或多個服務終結點。 若要啟用服務的服務端點，請選取服務或從 [服務]**** 清單中選取您想要啟用服務端點的服務。 Azure 自動設定終結點的位置。 預設情況下，Azure 配置虛擬網路區域的服務終結點。 為了支援區域容錯移轉方案，Azure 會自動將終結點配置為[Azure 配對區域](../best-practices-availability-paired-regions.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-paired-regions)，以便進行 Azure 存儲。</p><p>若要移除服務端點，請取消選取您想要移除其服務端點的服務。 要瞭解有關服務終結點及其可為其啟用的服務的更多資訊，請參閱[虛擬網路服務終結點](virtual-network-service-endpoints-overview.md)。 一旦您啟用服務的服務端點，您也必須為以服務建立的資源啟用子網路的網路存取權。 例如，如果您啟用 **Microsoft.Storage** 的服務端點，您也必須對想要授與網路存取權的所有 Azure 儲存體帳戶啟用網路存取權。 要啟用對啟用服務終結點的子網的網路訪問，請參閱為服務終結點啟用的各個服務的文檔。</p><p>若要驗證是否已為子網路啟用服務端點，請查看子網路中任何網路介面的[有效路由](diagnose-network-routing-problem.md)。 配置終結點時，您將看到一個*預設*路由，其中帶有服務的位址首碼，另一個躍點類型的**虛擬網路服務終結點**。 要瞭解有關路由的更多資訊，請參閱[虛擬網路流量路由](virtual-networks-udr-overview.md)。</p> |
    | **子網委派** | 子網可以選擇啟用一個或多個代表團。 子網委派向服務授予明確權限，用於在服務部署期間使用唯一識別碼在子網中創建特定于服務的資源。 若要為服務進行委派，請從 [服務]**** 清單選取您要委派到的服務。 |

5. 若要在所選虛擬網路中新增子網路，請選取 [確定]****。

### <a name="commands"></a>命令

| 工具 | Command |
| ---- | ------- |
| Azure CLI | [az network vnet subnet create](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-create) |
| PowerShell | [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig) |

## <a name="change-subnet-settings"></a>變更子網路設定

1. 轉到[Azure 門戶](https://portal.azure.com)以查看虛擬網路。 搜索並選擇**虛擬網路**。

2. 選擇包含要更改的子網的虛擬網路的名稱。

3. 從 **"設置"** 中，選擇**子網**。

4. 在子網路清單中，選取您想要變更設定的子網路。

5. 在子網頁中，更改以下任一設置：

    | 設定 | 描述 |
    | --- | --- |
    | **位址範圍** | 如果子網路內沒有部署資源，您可以變更位址範圍。 如果子網路中有任何資源存在，您必須將資源移至另一個子網路，或先從子網路中刪除。 不同資源的資源移動或刪除步驟也各異。 要瞭解如何移動或刪除子網中的資源，請閱讀每種資源類型的文檔。 請參閱[添加子網](#add-a-subnet)的步驟 4 中**的位址範圍**的約束。 |
    | **使用者** | 您可以使用內建角色或自有的自訂角色來控制子網路的存取。 要瞭解有關分配角色和使用者以訪問子網的更多內容，請參閱[添加角色指派](../role-based-access-control/role-assignments-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-a-role-assignment)。 |
    | **網路安全性群組**和**路由表** | 請參閱[添加子網](#add-a-subnet)的步驟 4。 |
    | **服務終結點** | <p>請參閱[添加子網](#add-a-subnet)的步驟 4 中的服務終結點。 當啟用現有子網路的服務端點時，請確定子網路中的任何資源上都沒有重要的工作正在執行。 服務終結點在子網中的每個網路介面上切換路由。 服務終結點從使用具有*0.0.0/0*位址首碼和下一個躍點類型的*Internet*的預設路由，到使用具有服務位址首碼和下一個躍點類型的*虛擬網路服務終結點*。</p><p>在切換期間，任何開啟的 TCP 連接都可能會終止。 在使用新路由更新所有網路介面的服務流量之前，不會啟用服務終結點。 要瞭解有關路由的更多資訊，請參閱[虛擬網路流量路由](virtual-networks-udr-overview.md)。</p> |
    | **子網委派** | 請參閱[添加子網](#add-a-subnet)的步驟 4 中的服務終結點。 子網路委派可以修改為有零或多個為它啟用的委派。 如果服務的資源已部署在子網中，則在刪除服務的所有資源之前，無法添加或刪除子網委派。 若要為不同的服務進行委派，請從 [服務]**** 清單選取您要委派到的服務。 |

6. 選取 [儲存]****。

### <a name="commands"></a>命令

| 工具 | Command |
| ---- | ------- |
| Azure CLI | [az network vnet subnet update](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-update) |
| PowerShell | [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig) |

## <a name="delete-a-subnet"></a>刪除子網路

您只能刪除未包含任何資源的子網路。 如果資源位於子網中，則必須刪除這些資源，然後才能刪除子網。 不同資源的資源刪除步驟也各異。 要瞭解如何刪除子網中的資源，請閱讀每種資源類型的文檔。

1. 轉到[Azure 門戶](https://portal.azure.com)以查看虛擬網路。 搜索並選擇**虛擬網路**。

2. 選擇包含要刪除的子網的虛擬網路的名稱。

3. 從 **"設置"** 中，選擇**子網**。

4. 在子網清單中，選擇要刪除的子網。

5. 選擇 **"刪除**"，然後在確認對話方塊中選擇 **"是**"。

### <a name="commands"></a>命令

| 工具 | Command |
| ---- | ------- |
| Azure CLI | [az 網路 vnet 子網刪除](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-delete) |
| PowerShell | [刪除-Az 虛擬網路子網路設定](/powershell/module/az.network/remove-azvirtualnetworksubnetconfig?toc=%2fazure%2fvirtual-network%2ftoc.json) |

## <a name="permissions"></a>權限

要在子網上執行任務，您的帳戶必須分配給[網路參與者角色](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)或已在下表中分配相應操作的[自訂角色](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json)：

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
- 為虛擬網路建立及套用 [Azure 原則](policy-samples.md)
