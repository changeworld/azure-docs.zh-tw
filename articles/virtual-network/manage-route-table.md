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
ms.openlocfilehash: 79310ddf121d6ada10755b198b515fdc9c1114d6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80247056"
---
# <a name="create-change-or-delete-a-route-table"></a>建立、變更或刪除路由表

Azure 會自動路由傳送 Azure 子網路、虛擬網路及內部部署網路之間的流量。 如果您想要變更任何 Azure 的預設路由，您可藉由建立路由表來執行此動作。 如果您是虛擬網路中路由的新功能，則可以在[虛擬網路流量路由](virtual-networks-udr-overview.md)或完成[教程](tutorial-create-route-table-portal.md)中瞭解有關它的更多資訊。

## <a name="before-you-begin"></a>開始之前

如果沒有，則使用活動訂閱設置 Azure 帳戶。 [免費建立帳戶](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。 然後，在本文任何部分中啟動步驟之前完成這些任務之一：

- **門戶使用者**：使用 Azure 帳戶登錄到[Azure 門戶](https://portal.azure.com)。

- **PowerShell 使用者**：在[Azure 雲殼](https://shell.azure.com/powershell)中運行命令，或者從電腦運行 PowerShell。 Azure Cloud Shell 是免費的互動式 Shell，可讓您用來執行本文中的步驟。 它具有預先安裝和設定的共用 Azure 工具，可與您的帳戶搭配使用。 在 Azure 雲殼瀏覽器選項卡中，查找 **"選擇環境**"下拉清單，如果尚未選擇 **"PowerShell"，請選擇 PowerShell。**

    如果在本地運行 PowerShell，請使用 Azure PowerShell 模組版本 1.0.0 或更高版本。 執行 `Get-Module -ListAvailable Az.Network` 來了解安裝的版本。 如果您需要升級，請參閱[安裝 Azure PowerShell 模組](/powershell/azure/install-az-ps)。 還要運行`Connect-AzAccount`以創建與 Azure 的連接。

- **Azure 命令列介面 （CLI） 使用者**：在 Azure[雲外殼](https://shell.azure.com/bash)中運行命令，或從電腦運行 CLI。 如果您在本地運行 Azure CLI，請使用 Azure CLI 版本 2.0.31 或更高版本。 執行 `az --version` 來了解安裝的版本。 如果需要安裝或升級，請參閱[安裝 Azure CLI](/cli/azure/install-azure-cli)。 還要運行`az login`以創建與 Azure 的連接。

必須將您登錄或連接到 Azure 的帳戶分配給[網路參與者角色](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)或已分配[許可權](#permissions)中列出的相應操作的[自訂角色](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。

## <a name="create-a-route-table"></a>建立路由表

每個 Azure 位置和訂閱可以創建多少個路由表是有限制的。 有關詳細資訊，請參閱[網路限制 - Azure 資源管理器](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)。

1. 在[Azure 門戶](https://portal.azure.com)功能表或**主頁**上，選擇 **"創建資源**"。

1. 在搜尋方塊中，輸入「路由表」**。 當搜尋結果中出現**路由表**出時加以選取。

1. 在 [路由表]**** 頁面中，選取 [建立]****。

1. 在 **"創建路由表"** 對話方塊中：

    1. 輸入路由表**的名稱**。
    1. 選擇您的**訂閱**。
    1. 選擇現有**資源組**或選擇 **"新建**"以創建新資源組。
    1. 選擇 [位置] ****。
    1. 如果計畫將路由表關聯到通過 VPN 閘道連接到本地網路的虛擬網路中的子網，並且不希望將本地路由傳播到子網中的網路介面，請將**虛擬網路閘道路由傳播**設置為 **"已禁用"。**

1. 選擇 **"創建**"以創建新的路由表。

### <a name="create-route-table---commands"></a>創建路由表 - 命令

| 工具 | Command |
| ---- | ------- |
| Azure CLI | [az network route-table create](/cli/azure/network/route-table#az-network-route-table-create) |
| PowerShell | [New-AzRouteTable](/powershell/module/az.network/new-azroutetable) |

## <a name="view-route-tables"></a>檢視路由表

轉到[Azure 門戶](https://portal.azure.com)以管理虛擬網路。 搜索並選擇**路由表**。 列出的是存在訂用帳戶的路由表。

### <a name="view-route-table---commands"></a>查看路由表 - 命令

| 工具 | Command |
| ---- | ------- |
| Azure CLI | [az 網路路由表清單](/cli/azure/network/route-table#az-network-route-table-list) |
| PowerShell | [Get-AzRouteTable](/powershell/module/az.network/get-azroutetable) |

## <a name="view-details-of-a-route-table"></a>檢視路由表的詳細資料

1. 轉到[Azure 門戶](https://portal.azure.com)以管理虛擬網路。 搜索並選擇**路由表**。

1. 在工藝路線表清單中，選擇要查看其詳細資訊的路由表。

1. 在工藝路線表頁中，**在"設置"** 下，查看路由表中的**路由**或路由表關聯的**子網**。

若要深入了解 Azure 的一般設定，請參閱下列資訊：

- [活動日誌](../azure-monitor/platform/platform-logs-overview.md)
- [存取控制 （IAM）](../role-based-access-control/overview.md)
- [標記](../azure-resource-manager/management/tag-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [鎖](../azure-resource-manager/management/lock-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [自動化指令碼](../azure-resource-manager/templates/export-template-portal.md)

### <a name="view-details-of-route-table---commands"></a>查看路由表的詳細資訊 - 命令

| 工具 | Command |
| ---- | ------- |
| Azure CLI | [az 網路路由表顯示](/cli/azure/network/route-table#az-network-route-table-show) |
| PowerShell | [Get-AzRouteTable](/powershell/module/az.network/get-azroutetable) |

## <a name="change-a-route-table"></a>變更路由表

1. 轉到[Azure 門戶](https://portal.azure.com)以管理虛擬網路。 搜索並選擇**路由表**。

1. 在工藝路線表清單中，選擇要更改的路由表。

最常見的更改是[添加](#create-a-route)路由、[刪除](#delete-a-route)路由、將路由表[關聯](#associate-a-route-table-to-a-subnet)到子網或從子網[分離](#dissociate-a-route-table-from-a-subnet)路由表。

### <a name="change-a-route-table---commands"></a>更改路由表 - 命令

| 工具 | Command |
| ---- | ------- |
| Azure CLI | [az 網路路由表更新](/cli/azure/network/route-table#az-network-route-table-update) |
| PowerShell | [Set-AzRouteTable](/powershell/module/az.network/set-azroutetable) |

## <a name="associate-a-route-table-to-a-subnet"></a>建立路由表與子網路的關聯

您可以選擇將路由表關聯到子網。 路由表可以關聯到零個或多個子網。 由於路由表未與虛擬網路關聯，因此必須將路由表關聯到要關聯的路由表的每個子網。 如果虛擬網路連接到 Azure 虛擬網路閘道（ExpressRoute 或 VPN），則 Azure 會路由基於您在路由表、[預設路由](virtual-networks-udr-overview.md#default)和從本地網路傳播的路由離開子網的所有流量。 您只能在以路由表形式存在於相同的 Azure 位置和訂用帳戶的虛擬網路中，將路由表與子網路建立關聯。

1. 轉到[Azure 門戶](https://portal.azure.com)以管理虛擬網路。 搜索並選擇**虛擬網路**。

1. 在虛擬網路清單中，選擇包含要將路由表關聯的子網的虛擬網路。

1. 在虛擬網路功能表列中，選擇**子網**。

1. 選取您要和路由表建立關聯的子網路。

1. 在**路由表中**，選擇要關聯到子網的路由表。

1. 選取 [儲存]****。

如果您的虛擬網路已連接到 Azure VPN 閘道，請不要將路由表關聯到閘道[子網，該子網](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md?toc=%2fazure%2fvirtual-network%2ftoc.json#gwsub)包含目標*為 0.0.0.0/0*的路由。 這麼做會讓閘道無法正常運作。 有關在路由中使用*0.0.0.0/0*的詳細資訊，請參閱[虛擬網路流量路由](virtual-networks-udr-overview.md#default-route)。

### <a name="associate-a-route-table---commands"></a>關聯路由表 - 命令

| 工具 | Command |
| ---- | ------- |
| Azure CLI | [az network vnet subnet update](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-update) |
| PowerShell | [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig) |

## <a name="dissociate-a-route-table-from-a-subnet"></a>從子網路中斷與路由表的關聯

當您從子網路中斷與路由表的關聯時，Azure 會根據其[預設路由](virtual-networks-udr-overview.md#default)來路由傳送流量。

1. 轉到[Azure 門戶](https://portal.azure.com)以管理虛擬網路。 搜索並選擇**虛擬網路**。

1. 在虛擬網路清單中，選擇包含要從路由表分離的子網的虛擬網路。

1. 在虛擬網路功能表列中，選擇**子網**。

1. 選取您要從中與路由表中斷關聯的子網路。

1. 在 **"路由"表中**，選擇 **"無**"。

1. 選取 [儲存]****。

### <a name="dissociate-a-route-table---commands"></a>分離路由表 - 命令

| 工具 | Command |
| ---- | ------- |
| Azure CLI | [az network vnet subnet update](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-update) |
| PowerShell | [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig) |

## <a name="delete-a-route-table"></a>刪除路由表

不能刪除與任何子閘道聯的路由表。 請從所有子網路與路由表[中斷關聯](#dissociate-a-route-table-from-a-subnet)之後，再嘗試予以刪除。

1. 轉到[Azure 門戶](https://portal.azure.com)以管理路由表。 搜索並選擇**路由表**。

1. 在工藝路線表清單中，選擇要刪除的路由表。

1. 選擇 **"刪除**"，然後在確認對話方塊中選擇 **"是**"。

### <a name="delete-a-route-table---commands"></a>刪除路由表 - 命令

| 工具 | Command |
| ---- | ------- |
| Azure CLI | [az 網路路由表刪除](/cli/azure/network/route-table#az-network-route-table-delete) |
| PowerShell | [刪除-阿茲路由表](/powershell/module/az.network/remove-azroutetable) |

## <a name="create-a-route"></a>建立路由

每個路由表每個 Azure 位置和訂閱可以創建多少路由是有限制的。 有關詳細資訊，請參閱[網路限制 - Azure 資源管理器](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)。

1. 轉到[Azure 門戶](https://portal.azure.com)以管理路由表。 搜索並選擇**路由表**。

1. 在工藝路線表清單中，選擇要向其添加路由的路由表。

1. 從路由表功能表列中，選擇 **"路徑** > **添加**"。

1. 在工藝路線表中輸入路由的唯一**路由名稱**。

1. 輸入**位址首碼**，在要將流量路由到的無類域間路由 （CIDR） 標記法中。 首碼不能在工藝路線表中的多個路由中複製，儘管首碼可以位於另一個首碼中。 例如，如果將*10.0.0.0/16*定義為一個路由中的首碼，則仍可以使用*10.0.0.0/22*位址首碼定義另一個路由。 Azure 會根據最長首碼比對來為流量選取路由。 要瞭解詳細資訊，請參閱[Azure 如何選擇路由](virtual-networks-udr-overview.md#how-azure-selects-a-route)。

1. 選擇下一個**躍點類型**。 要瞭解有關下一個躍點類型的更多資訊，請參閱[虛擬網路流量路由](virtual-networks-udr-overview.md)。

1. 如果選擇了 **"下一個躍點"類型的****虛擬裝置**，請輸入**下一個躍點位址**的 IP 位址。

1. 選取 [確定]****。

### <a name="create-a-route---commands"></a>創建路由 - 命令

| 工具 | Command |
| ---- | ------- |
| Azure CLI | [az network route-table route create](/cli/azure/network/route-table/route#az-network-route-table-route-create) |
| PowerShell | [New-AzRouteConfig](/powershell/module/az.network/new-azrouteconfig) |

## <a name="view-routes"></a>檢視路由

路由表包含零個或多個路由。 要瞭解有關查看路由時列出的資訊的詳細資訊，請參閱[虛擬網路流量路由](virtual-networks-udr-overview.md)。

1. 轉到[Azure 門戶](https://portal.azure.com)以管理路由表。 搜索並選擇**路由表**。

1. 在工藝路線表清單中，選擇要為其查看路由的路由表。

1. 在工藝路線表功能表列中，選擇 **"路由"** 以查看路由清單。

### <a name="view-routes---commands"></a>查看路由 - 命令

| 工具 | Command |
| ---- | ------- |
| Azure CLI | [az 網路路由表路由清單](/cli/azure/network/route-table/route#az-network-route-table-route-list) |
| PowerShell | [獲取阿茲拉西康菲](/powershell/module/az.network/get-azrouteconfig) |

## <a name="view-details-of-a-route"></a>檢視路由的詳細資料

1. 轉到[Azure 門戶](https://portal.azure.com)以管理路由表。 搜索並選擇**路由表**。

1. 在工藝路線表清單中，選擇包含要查看其詳細資訊的路由表。

1. 在工藝路線表功能表列中，選擇 **"路由"** 以查看路由清單。

1. 選取您想要檢視其詳細資料的路由。

### <a name="view-details-of-a-route---commands"></a>查看路由的詳細資訊 - 命令

| 工具 | Command |
| ---- | ------- |
| Azure CLI | [az 網路路由表路由顯示](/cli/azure/network/route-table/route#az-network-route-table-route-show) |
| PowerShell | [獲取阿茲拉西康菲](/powershell/module/az.network/get-azrouteconfig) |

## <a name="change-a-route"></a>變更路由

1. 轉到[Azure 門戶](https://portal.azure.com)以管理路由表。 搜索並選擇**路由表**。

1. 在工藝路線表清單中，選擇包含要更改的路由的路由表。

1. 在工藝路線表功能表列中，選擇 **"路由"** 以查看路由清單。

1. 選擇要更改的路線。

1. 將現有的設定變更為新設定，然後選取 [儲存]****。

### <a name="change-a-route---commands"></a>更改路由 - 命令

| 工具 | Command |
| ---- | ------- |
| Azure CLI | [az 網路路由表路由更新](/cli/azure/network/route-table/route#az-network-route-table-route-update) |
| PowerShell | [設置-阿茲拉西康菲](/powershell/module/az.network/set-azrouteconfig) |

## <a name="delete-a-route"></a>刪除路由

1. 轉到[Azure 門戶](https://portal.azure.com)以管理路由表。 搜索並選擇**路由表**。

1. 在工藝路線表清單中，選擇包含要刪除的路由的路由表。

1. 在工藝路線表功能表列中，選擇 **"路由"** 以查看路由清單。

1. 選擇要刪除的路線。

1. 選擇 **"刪除**"，然後在確認對話方塊中選擇 **"是**"。

### <a name="delete-a-route---commands"></a>刪除路由 - 命令

| 工具 | Command |
| ---- | ------- |
| Azure CLI | [az 網路路由表路由刪除](/cli/azure/network/route-table/route#az-network-route-table-route-delete) |
| PowerShell | [刪除-阿茲拉西康菲](/powershell/module/az.network/remove-azrouteconfig) |

## <a name="view-effective-routes"></a>檢視有效的路由

每個 VM 連接的網路介面的有效路由是已創建的路由表、Azure 的預設路由以及通過邊界閘道協定 （BGP） 通過 Azure 虛擬網路從本地網路傳播的任何路由的組合閘道。 了解網路介面的有效路由，在對路由問題進行疑難排解時會很有幫助。 您可以查看連接到正在運行的 VM 的任何網路介面的有效路由。

1. 轉到[Azure 門戶](https://portal.azure.com)以管理 VM。 搜索並選擇**虛擬機器**。

1. 在虛擬機器清單中，選擇要查看有效路由的 VM。

1. 在 VM 功能表列中，選擇 **"網路**"。

1. 選取網路介面的名稱。

1. 在網路介面功能表列中，選擇**有效路由**。

1. 查看有效路由的清單，查看是否存在要將流量路由到的位置的正確路由。 詳細瞭解您在[虛擬網路流量路由](virtual-networks-udr-overview.md)中在此清單中看到的下一個躍點類型。

### <a name="view-effective-routes---commands"></a>查看有效路由 - 命令

| 工具 | Command |
| ---- | ------- |
| Azure CLI | [az 網路 nic 顯示有效路由表](/cli/azure/network/nic#az-network-nic-show-effective-route-table) |
| PowerShell | [Get-AzEffectiveRouteTable](/powershell/module/az.network/get-azeffectiveroutetable) |

## <a name="validate-routing-between-two-endpoints"></a>驗證兩個端點之間的路由

您可以判斷虛擬機器與另一個 Azure 資源的 IP 位址、內部部署資源或網路網路上資源之間的下一個躍點類型。 對路由問題進行疑難排解時，判斷 Azure 的路由會很有幫助。 要完成此任務，您必須具有現有的網路觀察程式。 如果沒有現有的網路觀察程式，請通過完成["創建網路觀察程式"實例](../network-watcher/network-watcher-create.md?toc=%2fazure%2fvirtual-network%2ftoc.json)中的步驟來創建一個。

1. 轉到[Azure 門戶](https://portal.azure.com)以管理網路觀察程式。 搜索並選擇**網路觀察程式**。

1. 在網路觀察程式功能表列中，選擇 **"下一個躍點**"。

1. 在**網路觀察程式中 |下一個躍點**頁面：

    1. 選擇要從中驗證路由的**訂閱**和資源**組**。

    1. 選擇連接到 VM 的**虛擬機器****和網路介面**。
    
    1. 輸入分配給要驗證路由的網路介面的**源 IP 位址**。

    1. 輸入要驗證路由**到的目標 IP 位址**。

1. 選取 [下一個躍點]****。

稍等片刻後，Azure 會告訴您下一個躍點類型以及路由流量的路由的 ID。 詳細瞭解您在[虛擬網路流量路由](virtual-networks-udr-overview.md)中返回的下一個躍點類型。

### <a name="validate-routing-between-two-endpoints---commands"></a>驗證兩個終結點之間的路由 - 命令

| 工具 | Command |
| ---- | ------- |
| Azure CLI | [az 網路觀察程式顯示下一跳](/cli/azure/network/watcher#az-network-watcher-show-next-hop) |
| PowerShell | [獲取阿茲網路觀察者下一個霍普](/powershell/module/az.network/get-aznetworkwatchernexthop) |

## <a name="permissions"></a>權限

要執行路由表和路由上的任務，您的帳戶必須分配給[網路參與者角色](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)或已分配下表中列出的相應操作的[自訂角色](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json)：

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

- 使用[PowerShell](powershell-samples.md)或[Azure CLI](cli-samples.md)示例腳本或 Azure[資源管理器範本](template-samples.md)創建路由表
- 為虛擬網路建立及套用 [Azure 原則](policy-samples.md)
