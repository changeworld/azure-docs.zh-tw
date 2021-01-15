---
title: 建立、變更或刪除 Azure 網路安全性群組
titlesuffix: Azure Virtual Network
description: 瞭解何處可以找到安全性規則的相關資訊，以及如何建立、變更或刪除網路安全性群組。
services: virtual-network
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/13/2020
ms.author: kumud
ms.openlocfilehash: d42e0f5594fdde55f1b4183a806e388658e86dc3
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/14/2021
ms.locfileid: "98222933"
---
# <a name="create-change-or-delete-a-network-security-group"></a>建立、變更或刪除網路安全性群組

網路安全性群組中的安全性規則能讓您篩選可在虛擬網路子網路及網路介面中流入和流出的網路流量類型。 若要深入了解網路安全性群組，請參閱[網路安全性群組概觀](./network-security-groups-overview.md)。 接下來，請完成 [篩選網路流量](tutorial-filter-network-traffic.md) 教學課程，以獲得網路安全性群組的一些經驗。

## <a name="before-you-begin"></a>開始之前

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

如果您沒有帳戶，請使用有效的訂用帳戶來設定 Azure 帳戶。 [免費建立帳戶](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。 開始本文的其餘部分之前，請先完成下列其中一項工作：

- **入口網站使用者**：使用您的 Azure 帳戶登入 [Azure 入口網站](https://portal.azure.com) 。

- **PowerShell 使用者**：請在 [Azure Cloud Shell](https://shell.azure.com/powershell)中執行命令，或從您的電腦執行 powershell。 Azure Cloud Shell 是免費的互動式 Shell，可讓您用來執行本文中的步驟。 它具有預先安裝和設定的共用 Azure 工具，可與您的帳戶搭配使用。 在 Azure Cloud Shell 瀏覽器] 索引標籤中，尋找 [ **選取環境** ] 下拉式清單，然後挑選 **PowerShell** （若尚未選取）。

    如果您在本機執行 PowerShell，請使用 Azure PowerShell 模組1.0.0 版或更新版本。 執行 `Get-Module -ListAvailable Az.Network` 來了解安裝的版本。 如果您需要升級，請參閱[安裝 Azure PowerShell 模組](/powershell/azure/install-az-ps)。 執行 `Connect-AzAccount` 來建立與 Azure 的連線。

- **Azure 命令列介面 (CLI) 使用者**：請在 [Azure Cloud Shell](https://shell.azure.com/bash)中執行命令，或從您的電腦執行 cli。 如果您是在本機執行 Azure CLI，請使用 Azure CLI 版本2.0.28 版或更新版本。 執行 `az --version` 來了解安裝的版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI](/cli/azure/install-azure-cli)。 執行 `az login` 來建立與 Azure 的連線。

您登入或連線到 Azure 的帳戶必須指派給「[網路參與者」角色](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)，或指派給已獲指派 [[許可權](#permissions)] 中所列適當動作的[自訂角色](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。

## <a name="work-with-network-security-groups"></a>使用網路安全性群組

您可以建立網路安全性群組、[檢視所有網路安全性群組](#view-all-network-security-groups)[檢視網路安全性群組的詳細資料](#view-details-of-a-network-security-group)，以及[變更](#change-a-network-security-group)和[刪除](#delete-a-network-security-group)網路安全性群組。 您也可以讓網路安全性群組與網路介面或子網路[建立關聯或中斷關聯](#associate-or-dissociate-a-network-security-group-to-or-from-a-subnet-or-network-interface)。

### <a name="create-a-network-security-group"></a>建立網路安全性群組

您可以為每個 Azure 位置和訂用帳戶建立的網路安全性群組數目有所限制。 若要深入瞭解，請參閱 [Azure 訂用帳戶和服務限制、配額和條件約束](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)。

1. 在 [Azure 入口網站](https://portal.azure.com) 功能表上，或從 [首頁] 頁面，選取 [建立資源]。

2. 選取 **[網路]，然後** 選取 [ **網路安全性群組**]。

3. 在 [ **建立網路安全性群組** ] 頁面的 [ **基本** ] 索引標籤底下，設定下列設定的值：

    | 設定 | 動作 |
    | --- | --- |
    | **訂用帳戶** | 選擇您的訂用帳戶。 |
    | **資源群組** | 選擇現有的資源群組，或選取 [ **建立新** 的] 以建立新的資源群組。 |
    | **名稱** | 輸入資源群組內的唯一文字字串。 |
    | **區域** | 選擇您想要的位置。 |

4. 選取 [檢閱 + 建立]。

5. 當您看到 [ **通過驗證** ] 訊息之後，請選取 [ **建立**]。

#### <a name="commands"></a>命令

| 工具 | 命令 |
| ---- | ------- |
| Azure CLI | [az network nsg create](/cli/azure/network/nsg#az-network-nsg-create) |
| PowerShell | [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup) |

### <a name="view-all-network-security-groups"></a>檢視所有網路安全性群組

移至 [Azure 入口網站](https://portal.azure.com) 以查看您的網路安全性群組。 搜尋並選取 [ **網路安全性群組**]。 訂用帳戶的網路安全性群組清單隨即出現。

#### <a name="commands"></a>命令

| 工具 | 命令 |
| ---- | ------- |
| Azure CLI | [az network nsg list](/cli/azure/network/nsg#az-network-nsg-list) |
| PowerShell | [Get-AzNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup) |

### <a name="view-details-of-a-network-security-group"></a>檢視網路安全性群組的詳細資料

1. 移至 [Azure 入口網站](https://portal.azure.com) 以查看您的網路安全性群組。 搜尋並選取 [ **網路安全性群組**]。

2. 選取您的網路安全性群組名稱。

在網路安全性群組的功能表列中，您可以在 [ **設定**] 底下，查看網路安全性群組相關聯的 **輸入安全性規則**、 **輸出安全性規則**、 **網路介面** 和 **子網** 。

在 [ **監視**] 底下，您可以啟用或停用 **診斷設定**。 在 [ **支援 + 疑難排解**] 下，您可以看到 **有效的安全性規則**。 若要深入瞭解，請參閱 [網路安全性群組的診斷記錄](virtual-network-nsg-manage-log.md) ，以及 [診斷 VM 網路流量篩選問題](diagnose-network-traffic-filter-problem.md)。

若要深入了解列出的一般 Azure 設定，請參閱下列文章：

- [活動記錄檔](../azure-monitor/platform/platform-logs-overview.md)
- [ (IAM) 的存取控制 ](../role-based-access-control/overview.md)
- [Tags](../azure-resource-manager/management/tag-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json) (標籤)
- [鎖定](../azure-resource-manager/management/lock-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [自動化指令碼](../azure-resource-manager/templates/export-template-portal.md)

#### <a name="commands"></a>命令

| 工具 | 命令 |
| ---- | ------- |
| Azure CLI | [az network nsg show](/cli/azure/network/nsg#az-network-nsg-show) |
| PowerShell | [Get-AzNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup) |

### <a name="change-a-network-security-group"></a>變更網路安全性群組

1. 移至 [Azure 入口網站](https://portal.azure.com) 以查看您的網路安全性群組。 搜尋並選取 [ **網路安全性群組**]。

2. 選取您要變更的網路安全性群組名稱。

最常見的變更是 [新增安全性規則](#create-a-security-rule)、 [移除規則](#delete-a-security-rule)，以及 [將網路安全性群組與子網或網路介面建立關聯或中斷關聯](#associate-or-dissociate-a-network-security-group-to-or-from-a-subnet-or-network-interface)。

#### <a name="commands"></a>命令

| 工具 | 命令 |
| ---- | ------- |
| Azure CLI | [az network nsg update](/cli/azure/network/nsg#az-network-nsg-update) |
| PowerShell | [Set-AzNetworkSecurityGroup](/powershell/module/az.network/set-aznetworksecuritygroup) |

### <a name="associate-or-dissociate-a-network-security-group-to-or-from-a-subnet-or-network-interface"></a>讓網路安全性群組與子網路或網路介面建立關聯或中斷關聯

若要讓網路安全性群組與網路介面建立關聯或中斷關聯，請參閱[讓網路安全性群組與網路介面建立關聯或中斷關聯](virtual-network-network-interface.md#associate-or-dissociate-a-network-security-group)。 若要讓網路安全性群組與子網路建立關聯或中斷關聯，請參閱[變更子網路設定](virtual-network-manage-subnet.md#change-subnet-settings)。

### <a name="delete-a-network-security-group"></a>刪除網路安全性群組

如果網路安全性群組與任何子網或網路介面相關聯，則無法刪除。 請先將網路安全性群組與所有子網路和網路介面中斷關聯，再嘗試刪除它。

1. 移至 [Azure 入口網站](https://portal.azure.com) 以查看您的網路安全性群組。 搜尋並選取 [ **網路安全性群組**]。

2. 選取您要刪除的網路安全性群組名稱。

3. 在網路安全性群組的工具列中，選取 [ **刪除**]。 然後，選取確認對話方塊中的 [是]。

#### <a name="commands"></a>命令

| 工具 | 命令 |
| ---- | ------- |
| Azure CLI | [az network nsg delete](/cli/azure/network/nsg#az-network-nsg-delete) |
| PowerShell | [移除->new-aznetworksecuritygroup](/powershell/module/az.network/remove-aznetworksecuritygroup) |

## <a name="work-with-security-rules"></a>使用安全性規則

網路安全性群組包含零個或多個安全性規則。 您可以建立安全性規則、[檢視所有安全性規則](#view-all-security-rules)[檢視安全性規則的詳細資料](#view-details-of-a-security-rule)，以及[變更](#change-a-security-rule)和[刪除](#delete-a-security-rule)安全性規則。

### <a name="create-a-security-rule"></a>建立安全性規則

針對每個 Azure 位置和訂用帳戶，您可以為每個網路安全性群組建立的規則數目有所限制。 若要深入瞭解，請參閱 [Azure 訂用帳戶和服務限制、配額和條件約束](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)。

1. 移至 [Azure 入口網站](https://portal.azure.com) 以查看您的網路安全性群組。 搜尋並選取 [ **網路安全性群組**]。

2. 選取您要新增安全性規則的網路安全性群組名稱。

3. 在網路安全性群組的功能表列中，選擇 [ **輸入安全性規則** ] 或 [ **輸出安全性規則**]。

    列出數個現有的規則，包括您可能未新增的規則。 當您建立網路安全性群組時，會在其中建立數個預設安全性規則。 若要深入了解，請參閱[預設安全性規則](./network-security-groups-overview.md#default-security-rules)。  您無法刪除預設安全性規則，但是可以使用優先順序較高的規則來覆寫它們。

4. <a name="security-rule-settings"></a>選取 [ **新增**]。 選取或新增下列設定的值，然後選取 **[確定]**：

    | 設定 | 值 | 詳細資料 |
    | ------- | ----- | ------- |
    | **來源** | 值為下列其中之一：<ul><li>**任何**</li><li>**IP 位址**</li><li>**服務標記** (輸入安全性規則) 或 **VirtualNetwork** (輸出安全性規則) </li><li>**應用程式 &nbsp; 安全 &nbsp; 組**</li></ul> | <p>如果您選擇 **IP 位址**，您也必須指定 **來源 IP 位址/CIDR 範圍**。</p><p>如果您選擇 **服務** 標籤，也可以挑選 **來源服務標記**。</p><p>如果您選擇 [ **應用程式安全性群組**]，則也必須挑選現有的應用程式安全性群組。 如果您選擇 [ **應用程式安全性群組** ] 作為 **來源** 和 **目的地**，兩個應用程式安全性群組內的網路介面都必須位於相同的虛擬網路中。</p> |
    | **來源 IP 位址/CIDR 範圍** | 以逗號分隔的 IP 位址清單以及無類別網域間路由 (CIDR) 範圍 | <p>如果您將 **來源** 變更為 **IP 位址**，就會顯示此設定。 您必須指定單一值或以逗號分隔的多個值清單。 多個值的範例為 `10.0.0.0/16, 192.188.1.1` 。 您可以指定的值數目有所限制。 如需詳細資訊，請參閱 [Azure 限制](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)。</p><p>如果您指定的 IP 位址已指派給 Azure VM，請指定其私人 IP 位址，而非其公用 IP 位址。 Azure 會在將公用 IP 位址轉譯為輸入安全性規則的私人 IP 位址之後，但在將私人 IP 位址轉譯為輸出規則的公用 IP 位址之前，處理安全性規則。 若要深入了解 Azure 中的公用和私人 IP 位址，請參閱 [IP 位址類型](./public-ip-addresses.md)。</p> |
    | **來源服務標籤** | 下拉式清單中的服務標記 | 如果您針對輸入安全性規則設定 **來源** 對 **服務** 標籤，則會出現此選用設定。 服務標籤是為 IP 位址類別預先定義的識別碼。 若要深入瞭解可用的服務標籤，以及每個標記所代表的內容，請參閱 [服務標記](./network-security-groups-overview.md#service-tags)。 |
    | **來源應用程式安全性群組** | 現有的應用程式安全性群組 | 如果您將 **來源** 設定為 [ **應用程式安全性群組**]，則會出現此設定。 選取與網路介面位於相同區域中的應用程式安全性群組。 了解如何[建立應用程式安全性群組](#create-an-application-security-group)。 |
    | **來源連接埠範圍** | 值為下列其中之一：<ul><li>單一端口，例如 `80`</li><li>埠範圍，例如 `1024-65535`</li><li>單一端口及/或埠範圍的逗號分隔清單，例如 `80, 1024-65535`</li><li>星號 (`*`) 允許任何埠上的流量</li></ul> | 此設定會指定規則允許或拒絕流量的埠。 您可以指定的連接埠數目有所限制。 如需詳細資訊，請參閱 [Azure 限制](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)。 |
    | **目的地** | 值為下列其中之一：<ul><li>**任何**</li><li>**IP 位址**</li><li>**服務標記** (輸出安全性規則) 或 **VirtualNetwork** (輸入安全性規則) </li><li>**應用程式 &nbsp; 安全 &nbsp; 組**</li></ul> | <p>如果您選擇 [ **ip 位址**]，請同時指定 **目的地 IP 位址/CIDR 範圍**。</p><p>如果您選擇 **VirtualNetwork**，則會允許流量流向虛擬網路位址空間內的所有 IP 位址。 **VirtualNetwork** 是服務標記。</p><p>如果您選取 [ **應用程式安全性群組**]，則必須選取現有的應用程式安全性群組。 了解如何[建立應用程式安全性群組](#create-an-application-security-group)。</p> |
    | **目的地 IP 位址/CIDR 範圍** | IP 位址和 CIDR 範圍的逗號分隔清單 | <p>如果您將 **目的地** 變更為 **IP 位址**，就會顯示此設定。 類似于 **來源** 和 **來源 IP 位址/CIDR 範圍**，您可以指定單一或多個位址或範圍。 您可以指定的數目有所限制。 如需詳細資訊，請參閱 [Azure 限制](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)。</p><p>如果您指定的 IP 位址已指派給 Azure VM，請確定您指定其私人 IP，而不是其公用 IP 位址。 Azure 會在將公用 IP 位址轉譯為輸入安全性規則的私人 IP 位址之後，但在 Azure 將私人 IP 位址轉譯為輸出規則的公用 IP 位址之前，處理安全性規則。 若要深入了解 Azure 中的公用和私人 IP 位址，請參閱 [IP 位址類型](./public-ip-addresses.md)。</p> |
    | **目的地服務標記** | 下拉式清單中的服務標記 | 如果您將輸出安全性規則的 **目的地** 變更為 **服務** 標籤，則會出現此選用設定。 服務標籤是為 IP 位址類別預先定義的識別碼。 若要深入瞭解可用的服務標籤，以及每個標記所代表的內容，請參閱 [服務標記](./network-security-groups-overview.md#service-tags)。 |
    | **目的地應用程式安全性群組** | 現有的應用程式安全性群組 | 如果您將 **目的地** 設定為 [ **應用程式安全性群組**]，則會顯示此設定。 選取與網路介面位於相同區域中的應用程式安全性群組。 了解如何[建立應用程式安全性群組](#create-an-application-security-group)。 |
    | **目的地連接埠範圍** | 值為下列其中之一：<ul><li>單一端口，例如 `80`</li><li>埠範圍，例如 `1024-65535`</li><li>單一端口及/或埠範圍的逗號分隔清單，例如 `80, 1024-65535`</li><li>星號 (`*`) 允許任何埠上的流量</li></ul> | 如同 **來源埠範圍**，您可以指定單一或多個埠和範圍。 您可以指定的數目有所限制。 如需詳細資訊，請參閱 [Azure 限制](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)。 |
    | **通訊協定** | **Any**、 **TCP**、 **UDP** 或 **ICMP** | 您可以將規則限制為傳輸控制通訊協定 (TCP) 、使用者資料包協定 (UDP) 或網際網路控制訊息通訊協定 (ICMP) 。 預設值是適用于所有通訊協定的規則。 |
    | **動作** | **允許** 或 **拒絕** | 此設定會指定此規則是否允許或拒絕所提供來源和目的地設定的存取權。 |
    | **優先順序** | 介於100和4096之間的值，在網路安全性群組內的所有安全性規則中都是唯一的。 | Azure 會依優先權連續處理安全性規則。 編號愈低，優先順序愈高。 當您建立規則時，建議您在優先順序數位之間留出間距，例如100、200和300。 留出間距可讓您更輕鬆地在未來新增規則，讓您可以提供比現有規則更高或較低的優先順序。 |
    | **名稱** | 網路安全性群組中規則的唯一名稱 | 此名稱最多可有 80 個字元。 它必須以字母或數位開頭，且必須以字母、數位或底線結尾。 名稱只可包含字母、數位、底線、句點或連字號。 |
    | **描述** | 文字描述 | 您可以選擇性地指定安全性規則的文字描述。 描述的長度不能超過140個字元。 |

#### <a name="commands"></a>命令

| 工具 | 命令 |
| ---- | ------- |
| Azure CLI | [az network nsg rule create](/cli/azure/network/nsg/rule#az-network-nsg-rule-create) |
| PowerShell | [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig) |

### <a name="view-all-security-rules"></a>檢視所有安全性規則

網路安全性群組包含零或多個規則。 若要深入了解檢視規則時列出的資訊，請參閱[網路安全性群組概觀](./network-security-groups-overview.md)。

1. 移至 [Azure 入口網站](https://portal.azure.com) 以查看網路安全性群組的規則。 搜尋並選取 [ **網路安全性群組**]。

2. 選取您要查看其規則的網路安全性群組名稱。

3. 在網路安全性群組的功能表列中，選擇 [ **輸入安全性規則** ] 或 [ **輸出安全性規則**]。

此清單包含您已建立的任何規則，以及網路安全性群組的 [預設安全性規則](./network-security-groups-overview.md#default-security-rules)。

#### <a name="commands"></a>命令

| 工具 | 命令 |
| ---- | ------- |
| Azure CLI | [az network nsg rule list](/cli/azure/network/nsg/rule#az-network-nsg-rule-list) |
| PowerShell | [>new-aznetworksecurityruleconfig](/powershell/module/az.network/get-aznetworksecurityruleconfig) |

### <a name="view-details-of-a-security-rule"></a>檢視安全性規則的詳細資料

1. 移至 [Azure 入口網站](https://portal.azure.com) 以查看網路安全性群組的規則。 搜尋並選取 [ **網路安全性群組**]。

2. 選取您要查看其規則詳細資料的網路安全性群組名稱。

3. 在網路安全性群組的功能表列中，選擇 [ **輸入安全性規則** ] 或 [ **輸出安全性規則**]。

4. 選取您想要檢視其詳細資料的規則。 如需所有設定的說明，請參閱 [安全性規則設定](#security-rule-settings)。

    > [!NOTE]
    > 此程式只適用于自訂安全性規則。 如果您選擇預設安全性規則，則無法使用。

#### <a name="commands"></a>命令

| 工具 | 命令 |
| ---- | ------- |
| Azure CLI | [az network nsg rule show](/cli/azure/network/nsg/rule#az-network-nsg-rule-show) |
| PowerShell | [>new-aznetworksecurityruleconfig](/powershell/module/az.network/get-aznetworksecurityruleconfig) |

### <a name="change-a-security-rule"></a>變更安全性規則

1. 完成[檢視安全性規則的詳細資料](#view-details-of-a-security-rule)中的步驟。

2. 視需要變更設定，然後選取 [ **儲存**]。 如需所有設定的說明，請參閱 [安全性規則設定](#security-rule-settings)。

    > [!NOTE]
    > 此程式只適用于自訂安全性規則。 您不允許變更預設安全性規則。

#### <a name="commands"></a>命令

| 工具 | 命令 |
| ---- | ------- |
| Azure CLI | [az network nsg rule update](/cli/azure/network/nsg/rule#az-network-nsg-rule-update) |
| PowerShell | [Set-AzNetworkSecurityRuleConfig](/powershell/module/az.network/set-aznetworksecurityruleconfig) |

### <a name="delete-a-security-rule"></a>刪除安全性規則

1. 完成[檢視安全性規則的詳細資料](#view-details-of-a-security-rule)中的步驟。

2. 選取 [刪除]，然後選取 [是]。

    > [!NOTE]
    > 此程式只適用于自訂安全性規則。 您不允許刪除預設安全性規則。

#### <a name="commands"></a>命令

| 工具 | 命令 |
| ---- | ------- |
| Azure CLI | [az network nsg rule delete](/cli/azure/network/nsg/rule#az-network-nsg-rule-delete) |
| PowerShell | [移除->new-aznetworksecurityruleconfig](/powershell/module/az.network/remove-aznetworksecurityruleconfig) |

## <a name="work-with-application-security-groups"></a>使用應用程式安全性群組

應用程式安全性群組包含零個或多個網路介面。 若要深入了解，請參閱[應用程式安全性群組](./network-security-groups-overview.md#application-security-groups)。 應用程式安全性群組內的所有網路介面都必須存在於相同的虛擬網路中。 若要了解如何將網路介面新增至應用程式安全性群組，請參閱[將網路介面新增至應用程式安全性群組](virtual-network-network-interface.md#add-to-or-remove-from-application-security-groups)。

### <a name="create-an-application-security-group"></a>建立應用程式安全性群組

1. 在 [Azure 入口網站](https://portal.azure.com) 功能表上，或從 [首頁] 頁面，選取 [建立資源]。

2. 在搜尋方塊中，輸入 *應用程式安全性群組*。

3. 在 [ **應用程式安全性群組** ] 頁面中，選取 [ **建立**]。

4. 在 [ **建立應用程式安全性群組** ] 頁面的 [ **基本** ] 索引標籤底下，設定下列設定的值：

    | 設定 | 動作 |
    | --- | --- |
    | **訂用帳戶** | 選擇您的訂用帳戶。 |
    | **資源群組** | 選擇現有的資源群組，或選取 [ **建立新** 的] 以建立新的資源群組。 |
    | **名稱** | 輸入資源群組內的唯一文字字串。 |
    | **區域** | 選擇您想要的位置。 |

5. 選取 [檢閱 + 建立]。

6. 在 [ **審核 + 建立** ] 索引標籤下，當您看到 [ **通過驗證** ] 訊息之後，請選取 [ **建立**]。

#### <a name="commands"></a>命令

| 工具 | 命令 |
| ---- | ------- |
| Azure CLI | [az network asg create](/cli/azure/network/asg#az-network-asg-create) |
| PowerShell | [新 AzApplicationSecurityGroup](/powershell/module/az.network/new-azapplicationsecuritygroup) |

### <a name="view-all-application-security-groups"></a>檢視所有應用程式安全性群組

移至 [Azure 入口網站](https://portal.azure.com) 以查看您的應用程式安全性群組。 搜尋並選取 [ **應用程式安全性群組**]。 Azure 入口網站會顯示您的應用程式安全性群組清單。

#### <a name="commands"></a>命令

| 工具 | 命令 |
| ---- | ------- |
| Azure CLI | [az network asg list](/cli/azure/network/asg#az-network-asg-list) |
| PowerShell | [AzApplicationSecurityGroup](/powershell/module/az.network/get-azapplicationsecuritygroup) |

### <a name="view-details-of-a-specific-application-security-group"></a>檢視特定應用程式安全性群組的詳細資料

1. 移至 [Azure 入口網站](https://portal.azure.com) 以查看應用程式安全性群組。 搜尋並選取 [ **應用程式安全性群組**]。

2. 選取您要查看其詳細資料的應用程式安全性群組的名稱。

#### <a name="commands"></a>命令

| 工具 | 命令 |
| ---- | ------- |
| Azure CLI | [az network asg show](/cli/azure/network/asg#az-network-asg-show) |
| PowerShell | [AzApplicationSecurityGroup](/powershell/module/az.network/get-azapplicationsecuritygroup) |

### <a name="change-an-application-security-group"></a>變更應用程式安全性群組

1. 移至 [Azure 入口網站](https://portal.azure.com) 以查看應用程式安全性群組。 搜尋並選取 [ **應用程式安全性群組**]。

2. 選取您要變更的應用程式安全性群組的名稱。

3. 選取您要修改之設定旁的 [ **變更** ]。 例如，您可以新增或移除 **標記**，也可以變更 **資源群組** 或 **訂** 用帳戶。

    > [!NOTE]
    > 您無法變更位置。

    在功能表列中，您也可以選取 [ **存取控制] (IAM)**。 在 [ **存取控制 (IAM)** ] 頁面上，您可以指派或移除應用程式安全性群組的許可權。

#### <a name="commands"></a>命令

| 工具 | 命令 |
| ---- | ------- |
| Azure CLI | [az network asg update](/cli/azure/network/asg#az-network-asg-update) |
| PowerShell | 無 PowerShell Cmdlet |

### <a name="delete-an-application-security-group"></a>刪除應用程式安全性群組

如果應用程式安全性群組包含任何網路介面，您就無法刪除它。 若要從應用程式安全性群組移除所有網路介面，請變更網路介面設定或刪除網路介面。 若要深入瞭解，請參閱 [在應用程式安全性群組中新增或移除](virtual-network-network-interface.md#add-to-or-remove-from-application-security-groups) 或 [刪除網路介面](virtual-network-network-interface.md#delete-a-network-interface)。

1. 移至 [Azure 入口網站](https://portal.azure.com) 以管理您的應用程式安全性群組。 搜尋並選取 [ **應用程式安全性群組**]。

2. 選取您要刪除的應用程式安全性群組的名稱。

3. 選取 [刪除]，然後選取 [是] 刪除應用程式安全性群組。

#### <a name="commands"></a>命令

| 工具 | 命令 |
| ---- | ------- |
| Azure CLI | [az network asg delete](/cli/azure/network/asg#az-network-asg-delete) |
| PowerShell | [移除-AzApplicationSecurityGroup](/powershell/module/az.network/remove-azapplicationsecuritygroup) |

## <a name="permissions"></a>權限

若要對網路安全性群組、安全性規則及應用程式安全性群組執行工作，您的帳戶必須指派給「 [網路參與者](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) 」角色，或指派給已獲指派適當許可權的 [自訂角色](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ，如下表所列：

### <a name="network-security-group"></a>網路安全性群組

| 動作                                                        |   名稱                                                                |
|-------------------------------------------------------------- |   -------------------------------------------                         |
| Microsoft.Network/networkSecurityGroups/read                  |   取得網路安全性群組                                          |
| Microsoft.Network/networkSecurityGroups/write                 |   建立或更新網路安全性群組                             |
| Microsoft.Network/networkSecurityGroups/delete                |   刪除網路安全性群組                                       |
| Microsoft.Network/networkSecurityGroups/join/action           |   將網路安全性群組與子網路或網路介面建立關聯 

### <a name="network-security-group-rule"></a>網路安全性群組規則

| 動作                                                        |   名稱                                                                |
|-------------------------------------------------------------- |   -------------------------------------------                         |
| Microsoft.Network/networkSecurityGroups/securityRules/read            |   取得規則                                                            |
| Microsoft.Network/networkSecurityGroups/securityRules/write           |   建立或更新規則                                               |
| Microsoft.Network/networkSecurityGroups/securityRules/delete          |   刪除規則                                                         |

### <a name="application-security-group"></a>應用程式安全性群組

| 動作                                                                     | 名稱                                                     |
| --------------------------------------------------------------             | -------------------------------------------              |
| Microsoft.Network/applicationSecurityGroups/joinIpConfiguration/action     | 將 IP 設定加入至應用程式安全性群組|
| Microsoft.Network/applicationSecurityGroups/joinNetworkSecurityRule/action | 將安全性規則加入至應用程式安全性群組    |
| Microsoft.Network/applicationSecurityGroups/read                           | 取得應用程式安全性群組                        |
| Microsoft.Network/applicationSecurityGroups/write                          | 建立或更新應用程式安全性群組           |
| Microsoft.Network/applicationSecurityGroups/delete                         | 刪除應用程式安全性群組                     |

## <a name="next-steps"></a>後續步驟

- 使用[PowerShell](powershell-samples.md)或[Azure CLI](cli-samples.md)範例腳本或 Azure [Resource Manager 範本](template-samples.md)，建立網路或應用程式安全性群組
- 建立並指派虛擬網路的[Azure 原則定義](./policy-reference.md)