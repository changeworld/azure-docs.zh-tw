---
title: 建立、變更或刪除 Azure 路由表
titlesuffix: Azure Virtual Network
description: 了解如何建立、變更或刪除路由表。
services: virtual-network
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/19/2020
ms.author: kumud
ms.openlocfilehash: b30c912319104726069ae98920f0bc825d7358cb
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "82182849"
---
# <a name="create-change-or-delete-a-route-table"></a>建立、變更或刪除路由表

Azure 會自動路由傳送 Azure 子網路、虛擬網路及內部部署網路之間的流量。 如果您想要變更任何 Azure 的預設路由，您可藉由建立路由表來執行此動作。 如果您不熟悉虛擬網路中的路由，您可以在[虛擬網路流量路由](virtual-networks-udr-overview.md)或完成[教學](tutorial-create-route-table-portal.md)課程中深入瞭解。

## <a name="before-you-begin"></a>開始之前

如果您沒有，請使用有效的訂用帳戶來設定 Azure 帳戶。 [免費建立帳戶](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。 然後在本文的任何一節中開始執行步驟之前，先完成下列其中一項工作：

- **入口網站使用者**：使用您的 Azure 帳戶登入[Azure 入口網站](https://portal.azure.com)。

- **PowerShell 使用者**：在[Azure Cloud Shell](https://shell.azure.com/powershell)中執行命令，或從您的電腦執行 PowerShell。 Azure Cloud Shell 是免費的互動式 Shell，可讓您用來執行本文中的步驟。 它具有預先安裝和設定的共用 Azure 工具，可與您的帳戶搭配使用。 在 Azure Cloud Shell 瀏覽器] 索引標籤中，尋找 [**選取環境**] 下拉式清單，然後選擇 [ **PowerShell** ] （如果尚未選取）。

    如果您是在本機執行 PowerShell，請使用 Azure PowerShell 模組1.0.0 版或更新版本。 執行 `Get-Module -ListAvailable Az.Network` 來了解安裝的版本。 如果您需要升級，請參閱[安裝 Azure PowerShell 模組](/powershell/azure/install-az-ps)。 也會`Connect-AzAccount`執行以建立與 Azure 的連線。

- **Azure 命令列介面（CLI）使用者**：在[Azure Cloud Shell](https://shell.azure.com/bash)中執行命令，或從您的電腦執行 CLI。 如果您是在本機執行 Azure CLI，請使用 Azure CLI version 2.0.31 版或更新版本。 執行 `az --version` 來了解安裝的版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI](/cli/azure/install-azure-cli)。 也會`az login`執行以建立與 Azure 的連線。

您登入或連線到 Azure 的帳戶必須指派為[網路參與者角色](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)，或為已指派 [[許可權](#permissions)] 中所列適當動作的[自訂角色](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。

## <a name="create-a-route-table"></a>建立路由表

針對每個 Azure 位置和訂用帳戶，您可以建立的路由表數目有限制。 如需詳細資訊，請參閱[網路限制-Azure Resource Manager](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)。

1. 從 [Azure 入口網站](https://portal.azure.com)功能表或 [首頁]  頁面，選取 [建立資源]  。

1. 在搜尋方塊中，輸入「路由表」  。 當搜尋結果中出現**路由表**出時加以選取。

1. 在 [路由表]  頁面中，選取 [建立]  。

1. 在 [**建立路由表**] 對話方塊中：

    1. 輸入路由表的**名稱**。
    1. 選擇您的**訂**用帳戶。
    1. 選擇現有的**資源群組**，或選取 **[新建] 以建立**新的資源群組。
    1. 選擇 [位置] ****。
    1. 如果您打算將路由表關聯至虛擬網路中透過 VPN 閘道連線到內部部署網路的子網，而且不想將內部部署路由傳播到子網中的網路介面，請將 [**虛擬網路閘道路由傳播**] 設定為 [**已停用**]。

1. 選取 [**建立**] 以建立新的路由表。

### <a name="create-route-table---commands"></a>建立路由表-命令

| 工具 | Command |
| ---- | ------- |
| Azure CLI | [az network route-table create](/cli/azure/network/route-table#az-network-route-table-create) |
| PowerShell | [New-AzRouteTable](/powershell/module/az.network/new-azroutetable) |

## <a name="view-route-tables"></a>檢視路由表

移至 [Azure 入口網站](https://portal.azure.com)，以管理您的虛擬網路。 搜尋並選取 [路由表]  。 列出的是存在訂用帳戶的路由表。

### <a name="view-route-table---commands"></a>View route table-命令

| 工具 | Command |
| ---- | ------- |
| Azure CLI | [az network route-資料表清單](/cli/azure/network/route-table#az-network-route-table-list) |
| PowerShell | [Get-AzRouteTable](/powershell/module/az.network/get-azroutetable) |

## <a name="view-details-of-a-route-table"></a>檢視路由表的詳細資料

1. 移至 [Azure 入口網站](https://portal.azure.com)，以管理您的虛擬網路。 搜尋並選取 [路由表]  。

1. 在 [路由表] 清單中，選擇您想要查看詳細資料的路由表。

1. 在 [路由表] 頁面的 [**設定**] 下，查看路由**表中的路由或**路由表所關聯的**子網**。

若要深入了解 Azure 的一般設定，請參閱下列資訊：

- [活動記錄檔](../azure-monitor/platform/platform-logs-overview.md)
- [存取控制（IAM）](../role-based-access-control/overview.md)
- [標記](../azure-resource-manager/management/tag-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [鎖定](../azure-resource-manager/management/lock-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [自動化指令碼](../azure-resource-manager/templates/export-template-portal.md)

### <a name="view-details-of-route-table---commands"></a>查看路由表的詳細資料-命令

| 工具 | Command |
| ---- | ------- |
| Azure CLI | [az network route-資料表顯示](/cli/azure/network/route-table#az-network-route-table-show) |
| PowerShell | [Get-AzRouteTable](/powershell/module/az.network/get-azroutetable) |

## <a name="change-a-route-table"></a>變更路由表

1. 移至 [Azure 入口網站](https://portal.azure.com)，以管理您的虛擬網路。 搜尋並選取 [路由表]  。

1. 在 [路由表] 清單中，選擇您想要變更的路由表。

最常見的變更是[新增](#create-a-route)路由、[移除](#delete-a-route)路由、將路由表關聯至子網，[或從](#dissociate-a-route-table-from-a-subnet)子網中斷與路由表的[關聯](#associate-a-route-table-to-a-subnet)。

### <a name="change-a-route-table---commands"></a>變更路由表-命令

| 工具 | Command |
| ---- | ------- |
| Azure CLI | [az network route-資料表更新](/cli/azure/network/route-table#az-network-route-table-update) |
| PowerShell | [Set-AzRouteTable](/powershell/module/az.network/set-azroutetable) |

## <a name="associate-a-route-table-to-a-subnet"></a>建立路由表與子網路的關聯

您可以選擇性地將路由表與子網建立關聯。 路由表可以與零個或多個子網建立關聯。 由於路由表不會與虛擬網路相關聯，因此您必須將路由表關聯至您想要與之路由表相關聯的每個子網。 如果虛擬網路連線至 Azure 虛擬網路閘道（ExpressRoute 或 VPN），Azure 會根據您在路由表、[預設路由](virtual-networks-udr-overview.md#default)和從內部部署網路傳播的路由中所建立的路由，來路由傳送子網的所有流量。 您只能在以路由表形式存在於相同的 Azure 位置和訂用帳戶的虛擬網路中，將路由表與子網路建立關聯。

1. 移至 [Azure 入口網站](https://portal.azure.com)，以管理您的虛擬網路。 搜尋並選取 [虛擬網路]  。

1. 在 [虛擬網路] 清單中，選擇包含您想要與路由表建立關聯之子網的虛擬網路。

1. 在 [虛擬網路] 功能表列中，選擇 [**子網**]。

1. 選取您要和路由表建立關聯的子網路。

1. 在 [**路由表**] 中，選擇您想要與子網產生關聯的路由表。

1. 選取 [儲存]  。

如果您的虛擬網路已連線到 Azure VPN 閘道，請勿將路由表關聯至包含目的地為*0.0.0.0/0*之路由的[閘道子網](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md?toc=%2fazure%2fvirtual-network%2ftoc.json#gwsub)。 這麼做會讓閘道無法正常運作。 如需在路由中使用*0.0.0.0/0*的詳細資訊，請參閱[虛擬網路流量路由](virtual-networks-udr-overview.md#default-route)。

### <a name="associate-a-route-table---commands"></a>關聯路由表-命令

| 工具 | Command |
| ---- | ------- |
| Azure CLI | [az network vnet subnet update](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-update) |
| PowerShell | [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig) |

## <a name="dissociate-a-route-table-from-a-subnet"></a>從子網路中斷與路由表的關聯

當您從子網路中斷與路由表的關聯時，Azure 會根據其[預設路由](virtual-networks-udr-overview.md#default)來路由傳送流量。

1. 移至 [Azure 入口網站](https://portal.azure.com)，以管理您的虛擬網路。 搜尋並選取 [虛擬網路]  。

1. 在 [虛擬網路] 清單中，選擇包含您想要與路由表中斷關聯之子網的虛擬網路。

1. 在 [虛擬網路] 功能表列中，選擇 [**子網**]。

1. 選取您要從中與路由表中斷關聯的子網路。

1. 在 [**路由表**] 中，選擇 [**無**]。

1. 選取 [儲存]  。

### <a name="dissociate-a-route-table---commands"></a>中斷路由表的關聯-命令

| 工具 | Command |
| ---- | ------- |
| Azure CLI | [az network vnet subnet update](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-update) |
| PowerShell | [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig) |

## <a name="delete-a-route-table"></a>刪除路由表

您無法刪除與任何子網相關聯的路由表。 請從所有子網路與路由表[中斷關聯](#dissociate-a-route-table-from-a-subnet)之後，再嘗試予以刪除。

1. 請移至[Azure 入口網站](https://portal.azure.com)以管理您的路由表。 搜尋並選取 [路由表]  。

1. 在 [路由表] 清單中，選擇您想要刪除的路由表。

1. 選取 [**刪除**]，然後在確認對話方塊中選取 **[是]** 。

### <a name="delete-a-route-table---commands"></a>刪除路由表-命令

| 工具 | Command |
| ---- | ------- |
| Azure CLI | [az network route-資料表刪除](/cli/azure/network/route-table#az-network-route-table-delete) |
| PowerShell | [移除-AzRouteTable](/powershell/module/az.network/remove-azroutetable) |

## <a name="create-a-route"></a>建立路由

每個 Azure 位置和訂用帳戶的每個路由表可以建立的路由數目有限制。 如需詳細資訊，請參閱[網路限制-Azure Resource Manager](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)。

1. 請移至[Azure 入口網站](https://portal.azure.com)以管理您的路由表。 搜尋並選取 [路由表]  。

1. 在 [路由表] 清單中，選擇您想要新增路由的路由表。

1. 從 [路由表] 功能表列中，選擇 [**路由** > ] [**新增**]。

1. 為路由表中的路由輸入唯一的**路由名稱**。

1. 在 [無類別網域間路由（CIDR）標記法] 中，輸入您想要路由傳送流量的**位址首碼**。 在路由表內的多個路由中，前置詞不能重複，但前置詞可以在另一個前置詞中。 例如，如果您在一個路由中將*10.0.0.0/16*定義為前置詞，您仍然可以使用*10.0.0.0/22*位址首碼來定義另一個路由。 Azure 會根據最長首碼比對來為流量選取路由。 若要深入瞭解，請參閱[Azure 如何選取路由](virtual-networks-udr-overview.md#how-azure-selects-a-route)。

1. 選擇 **[下一個躍點類型]**。 若要深入瞭解下一個躍點類型，請參閱[虛擬網路流量路由](virtual-networks-udr-overview.md)。

1. 如果您選擇 [**虛擬裝置** **] 的下一個躍點類型**，請輸入 **[下一個躍點位址]** 的 IP 位址。

1. 選取 [確定]  。

### <a name="create-a-route---commands"></a>建立路由-命令

| 工具 | Command |
| ---- | ------- |
| Azure CLI | [az network route-table route create](/cli/azure/network/route-table/route#az-network-route-table-route-create) |
| PowerShell | [New-AzRouteConfig](/powershell/module/az.network/new-azrouteconfig) |

## <a name="view-routes"></a>檢視路由

路由表包含零個或多個路由。 若要深入瞭解查看路由時所列的資訊，請參閱[虛擬網路流量路由](virtual-networks-udr-overview.md)。

1. 請移至[Azure 入口網站](https://portal.azure.com)以管理您的路由表。 搜尋並選取 [路由表]  。

1. 在 [路由表] 清單中，選擇您想要用來查看路由的路由表。

1. 在 [路由表] 功能表列中，選擇 [**路由**] 以查看路由清單。

### <a name="view-routes---commands"></a>查看路由-命令

| 工具 | Command |
| ---- | ------- |
| Azure CLI | [az network route-資料表路由清單](/cli/azure/network/route-table/route#az-network-route-table-route-list) |
| PowerShell | [AzRouteConfig](/powershell/module/az.network/get-azrouteconfig) |

## <a name="view-details-of-a-route"></a>檢視路由的詳細資料

1. 請移至[Azure 入口網站](https://portal.azure.com)以管理您的路由表。 搜尋並選取 [路由表]  。

1. 在 [路由表] 清單中，選擇包含您想要查看其詳細資料之路由的路由表。

1. 在 [路由表] 功能表列中，選擇 [**路由**] 以查看路由清單。

1. 選取您想要檢視其詳細資料的路由。

### <a name="view-details-of-a-route---commands"></a>查看路由的詳細資料-命令

| 工具 | Command |
| ---- | ------- |
| Azure CLI | [az network route-table route show](/cli/azure/network/route-table/route#az-network-route-table-route-show) |
| PowerShell | [AzRouteConfig](/powershell/module/az.network/get-azrouteconfig) |

## <a name="change-a-route"></a>變更路由

1. 請移至[Azure 入口網站](https://portal.azure.com)以管理您的路由表。 搜尋並選取 [路由表]  。

1. 在 [路由表] 清單中，選擇包含您要變更之路由的路由表。

1. 在 [路由表] 功能表列中，選擇 [**路由**] 以查看路由清單。

1. 選擇您想要變更的路由。

1. 將現有的設定變更為新設定，然後選取 [儲存]****。

### <a name="change-a-route---commands"></a>變更路由-命令

| 工具 | Command |
| ---- | ------- |
| Azure CLI | [az network route-table route update](/cli/azure/network/route-table/route#az-network-route-table-route-update) |
| PowerShell | [設定-AzRouteConfig](/powershell/module/az.network/set-azrouteconfig) |

## <a name="delete-a-route"></a>刪除路由

1. 請移至[Azure 入口網站](https://portal.azure.com)以管理您的路由表。 搜尋並選取 [路由表]  。

1. 在 [路由表] 清單中，選擇包含您想要刪除之路由的路由表。

1. 在 [路由表] 功能表列中，選擇 [**路由**] 以查看路由清單。

1. 選擇您想要刪除的路由。

1. 選取 [**刪除**]，然後在確認對話方塊中選取 **[是]** 。

### <a name="delete-a-route---commands"></a>刪除路由-命令

| 工具 | Command |
| ---- | ------- |
| Azure CLI | [az network route-資料表路由刪除](/cli/azure/network/route-table/route#az-network-route-table-route-delete) |
| PowerShell | [移除-AzRouteConfig](/powershell/module/az.network/remove-azrouteconfig) |

## <a name="view-effective-routes"></a>檢視有效的路由

每個 VM 連接的網路介面的有效路由是您已建立的路由表、Azure 的預設路由，以及透過 Azure 虛擬網路閘道的邊界閘道協定（BGP）傳播到內部部署網路的任何路由的組合。 了解網路介面的有效路由，在對路由問題進行疑難排解時會很有幫助。 您可以針對任何連結至執行中 VM 的網路介面，查看其有效路由。

1. 請移至[Azure 入口網站](https://portal.azure.com)來管理您的 vm。 搜尋並選取 [虛擬機器]  。

1. 在 [虛擬機器] 清單中，選擇您想要查看其有效路由的 VM。

1. 在 VM 功能表列中，選擇 [**網路**]。

1. 選取網路介面的名稱。

1. 在 [網路介面] 功能表列中，選取 [**有效路由**]。

1. 檢查有效路由的清單，查看是否有路由流量的正確路由存在。 深入瞭解您在[虛擬網路流量路由](virtual-networks-udr-overview.md)的此清單中看到的下一個躍點類型。

### <a name="view-effective-routes---commands"></a>查看有效路由-命令

| 工具 | Command |
| ---- | ------- |
| Azure CLI | [az network nic show-有效率-route-資料表](/cli/azure/network/nic#az-network-nic-show-effective-route-table) |
| PowerShell | [Get-AzEffectiveRouteTable](/powershell/module/az.network/get-azeffectiveroutetable) |

## <a name="validate-routing-between-two-endpoints"></a>驗證兩個端點之間的路由

您可以判斷虛擬機器與另一個 Azure 資源的 IP 位址、內部部署資源或網路網路上資源之間的下一個躍點類型。 對路由問題進行疑難排解時，判斷 Azure 的路由會很有幫助。 若要完成這項工作，您必須擁有現有的網路監看員。 如果您沒有現有的網路監看員，請完成[建立網路監看員實例](../network-watcher/network-watcher-create.md?toc=%2fazure%2fvirtual-network%2ftoc.json)中的步驟來建立一個。

1. 請移至[Azure 入口網站](https://portal.azure.com)來管理您的網路監看員。 搜尋並選取 [**網路監看員**]。

1. 在 [網路監看員] 功能表列中，選擇 **[下一個躍點]**。

1. 在 [**網路監看員 |下一個躍點**頁面：

    1. 選擇您的**訂**用帳戶，以及您想要驗證路由的來源 VM**資源群組**。

    1. 選擇**虛擬機器**和連接至 VM 的**網路介面**。
    
    1. 輸入指派給您要驗證路由之網路介面的**來源 IP 位址**。

    1. 輸入您想要驗證路由的**目的地 IP 位址**。

1. 選取 [下一個躍點]****。

在短暫的等候之後，Azure 會告訴您下一個躍點類型，以及路由傳送流量的路由識別碼。 深入瞭解您在[虛擬網路流量路由](virtual-networks-udr-overview.md)中所看到傳回的下一個躍點類型。

### <a name="validate-routing-between-two-endpoints---commands"></a>驗證兩個端點之間的路由-命令

| 工具 | Command |
| ---- | ------- |
| Azure CLI | [az network 監看員顯示下一個躍點](/cli/azure/network/watcher#az-network-watcher-show-next-hop) |
| PowerShell | [AzNetworkWatcherNextHop](/powershell/module/az.network/get-aznetworkwatchernexthop) |

## <a name="permissions"></a>權限

若要對路由表和路由執行工作，您的帳戶必須指派為[網路參與者角色](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)，或為已指派下表所列適當動作的[自訂角色](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json)：

| 動作                                                          |   名稱                                                  |
|--------------------------------------------------------------   |   -------------------------------------------           |
| Microsoft.Network/routeTables/read                              |   讀取路由表                                    |
| Microsoft.Network/routeTables/write                             |   建立或更新路由表                        |
| Microsoft.Network/routeTables/delete                            |   刪除路由表                                  |
| Microsoft.Network/routeTables/join/action                       |   建立路由表與子網路的關聯                   |
| Microsoft.Network/routeTables/routes/read                       |   讀取路由                                          |
| Microsoft.Network/routeTables/routes/write                      |   建立或更新路由                              |
| Microsoft.Network/routeTables/routes/delete                     |   刪除路由                                        |
| Microsoft.Network/networkInterfaces/effectiveRouteTable/action  |   取得網路介面的有效路由表 |
| Microsoft.Network/networkWatchers/nextHop/action                |   從 VM 取得下一個躍點                           |

## <a name="next-steps"></a>後續步驟

- 使用[PowerShell](powershell-samples.md)或[Azure CLI](cli-samples.md)範例腳本或 Azure [Resource Manager 範本](template-samples.md)建立路由表
- 建立和指派虛擬網路的[Azure 原則定義](policy-samples.md)
