---
title: 建立、變更或刪除 Azure 網路安全性群組
titlesuffix: Azure Virtual Network
description: 了解如何建立、變更或刪除網路安全性群組。
services: virtual-network
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/13/2020
ms.author: kumud
ms.openlocfilehash: a22adef5510e24c2dc07ffb39c9687d500644f8a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80066443"
---
# <a name="create-change-or-delete-a-network-security-group"></a>建立、變更或刪除網路安全性群組

網路安全性群組中的安全性規則能讓您篩選可在虛擬網路子網路及網路介面中流入和流出的網路流量類型。 若要深入了解網路安全性群組，請參閱[網路安全性群組概觀](security-overview.md)。 接下來，完成["篩選網路流量](tutorial-filter-network-traffic.md)教程"，以獲得網路安全性群組的一些經驗。

## <a name="before-you-begin"></a>開始之前

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

如果沒有，則使用活動訂閱設置 Azure 帳戶。 [免費建立帳戶](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。 在開始本文的其餘部分之前完成這些任務之一：

- **門戶使用者**：使用 Azure 帳戶登錄到[Azure 門戶](https://portal.azure.com)。

- **PowerShell 使用者**：在[Azure 雲殼](https://shell.azure.com/powershell)中運行命令，或者從電腦運行 PowerShell。 Azure Cloud Shell 是免費的互動式 Shell，可讓您用來執行本文中的步驟。 它具有預先安裝和設定的共用 Azure 工具，可與您的帳戶搭配使用。 在 Azure 雲殼瀏覽器選項卡中，查找 **"選擇環境**"下拉清單，如果尚未選擇**PowerShell，請選擇 PowerShell。**

    如果在本地運行 PowerShell，請使用 Azure PowerShell 模組版本 1.0.0 或更高版本。 執行 `Get-Module -ListAvailable Az.Network` 來了解安裝的版本。 如果您需要升級，請參閱[安裝 Azure PowerShell 模組](/powershell/azure/install-az-ps)。 執行 `Connect-AzAccount` 來建立與 Azure 的連線。

- **Azure 命令列介面 （CLI） 使用者**：在 Azure[雲外殼](https://shell.azure.com/bash)中運行命令，或從電腦運行 CLI。 如果您在本地運行 Azure CLI，請使用 Azure CLI 版本 2.0.28 或更高版本。 執行 `az --version` 來了解安裝的版本。 如果需要安裝或升級，請參閱[安裝 Azure CLI](/cli/azure/install-azure-cli)。 執行 `az login` 來建立與 Azure 的連線。

必須將您登錄或連接到 Azure 的帳戶分配給[網路參與者角色](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)或已分配[許可權](#permissions)中列出的相應操作的[自訂角色](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。

## <a name="work-with-network-security-groups"></a>使用網路安全性群組

您可以建立網路安全性群組、[檢視所有網路安全性群組](#view-all-network-security-groups)[檢視網路安全性群組的詳細資料](#view-details-of-a-network-security-group)，以及[變更](#change-a-network-security-group)和[刪除](#delete-a-network-security-group)網路安全性群組。 您也可以讓網路安全性群組與網路介面或子網路[建立關聯或中斷關聯](#associate-or-dissociate-a-network-security-group-to-or-from-a-subnet-or-network-interface)。

### <a name="create-a-network-security-group"></a>建立網路安全性群組

可以為每個 Azure 位置和訂閱創建多少個網路安全性群組是有限制的。 要瞭解更多資訊，請參閱[Azure 訂閱和服務限制、配額和約束](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)。

1. 在[Azure 門戶](https://portal.azure.com)功能表或**主頁**上，選擇 **"創建資源**"。

2. 選擇 **"網路**"，然後選擇**網路安全性群組**。

3. 在"**創建網路安全性群組"** 頁中，在 **"基本"** 選項卡下，為以下設置設置值：

    | 設定 | 動作 |
    | --- | --- |
    | **訂閱** | 選擇您的訂用帳戶。 |
    | **資源組** | 選擇現有資源組，或選擇 **"新建**"以創建新資源組。 |
    | **名稱** | 在資源組中輸入唯一的文本字串。 |
    | **地區** | 選擇您想要的位置。 |

4. 選擇 **"審閱" = 創建**。

5. 看到**驗證傳遞**的消息後，選擇 **"創建**"。

#### <a name="commands"></a>命令

| 工具 | Command |
| ---- | ------- |
| Azure CLI | [az network nsg create](/cli/azure/network/nsg#az-network-nsg-create) |
| PowerShell | [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup) |

### <a name="view-all-network-security-groups"></a>檢視所有網路安全性群組

轉到[Azure 門戶](https://portal.azure.com)以查看網路安全性群組。 搜索並選擇**網路安全性群組**。 為您的訂閱顯示網路安全性群組的清單。

#### <a name="commands"></a>命令

| 工具 | Command |
| ---- | ------- |
| Azure CLI | [az 網路 nsg 清單](/cli/azure/network/nsg#az-network-nsg-list) |
| PowerShell | [Get-AzNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup) |

### <a name="view-details-of-a-network-security-group"></a>檢視網路安全性群組的詳細資料

1. 轉到[Azure 門戶](https://portal.azure.com)以查看網路安全性群組。 搜索並選擇**網路安全性群組**。

2. 選擇網路安全性群組的名稱。

在網路安全性群組的功能表列中，在 **"設置"** 下，您可以查看網路安全性群組關聯的**入站安全規則**、**出站安全規則**、**網路介面**和**子網**。

在 **"監視"** 下，您可以啟用或禁用**診斷設置**。 在 **"支援 + 故障排除"下**，您可以查看**有效安全規則**。 要瞭解更多資訊，請參閱[網路安全性群組的診斷日誌記錄](virtual-network-nsg-manage-log.md)和診斷 VM[網路流量篩選器問題](diagnose-network-traffic-filter-problem.md)。

若要深入了解列出的一般 Azure 設定，請參閱下列文章：

- [活動日誌](../azure-monitor/platform/platform-logs-overview.md)
- [存取控制 （IAM）](../role-based-access-control/overview.md)
- [標記](../azure-resource-manager/management/tag-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [鎖](../azure-resource-manager/management/lock-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [自動化指令碼](../azure-resource-manager/templates/export-template-portal.md)

#### <a name="commands"></a>命令

| 工具 | Command |
| ---- | ------- |
| Azure CLI | [az 網路 nsg 顯示](/cli/azure/network/nsg#az-network-nsg-show) |
| PowerShell | [Get-AzNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup) |

### <a name="change-a-network-security-group"></a>變更網路安全性群組

1. 轉到[Azure 門戶](https://portal.azure.com)以查看網路安全性群組。 搜索並選擇**網路安全性群組**。

2. 選擇要更改的網路安全性群組的名稱。

最常見的更改是[添加安全規則](#create-a-security-rule)、[刪除規則](#delete-a-security-rule)，並將[網路安全性群組關聯或脫離子網或網路介面](#associate-or-dissociate-a-network-security-group-to-or-from-a-subnet-or-network-interface)。

#### <a name="commands"></a>命令

| 工具 | Command |
| ---- | ------- |
| Azure CLI | [az 網路 nsg 更新](/cli/azure/network/nsg#az-network-nsg-update) |
| PowerShell | [Set-AzNetworkSecurityGroup](/powershell/module/az.network/set-aznetworksecuritygroup) |

### <a name="associate-or-dissociate-a-network-security-group-to-or-from-a-subnet-or-network-interface"></a>讓網路安全性群組與子網路或網路介面建立關聯或中斷關聯

若要讓網路安全性群組與網路介面建立關聯或中斷關聯，請參閱[讓網路安全性群組與網路介面建立關聯或中斷關聯](virtual-network-network-interface.md#associate-or-dissociate-a-network-security-group)。 若要讓網路安全性群組與子網路建立關聯或中斷關聯，請參閱[變更子網路設定](virtual-network-manage-subnet.md#change-subnet-settings)。

### <a name="delete-a-network-security-group"></a>刪除網路安全性群組

如果網路安全性群組與任何子網或網路介面相關聯，則無法將其刪除。 請先將網路安全性群組與所有子網路和網路介面中斷關聯，再嘗試刪除它。

1. 轉到[Azure 門戶](https://portal.azure.com)以查看網路安全性群組。 搜索並選擇**網路安全性群組**。

2. 選擇要刪除的網路安全性群組的名稱。

3. 在網路安全性群組的工具列中，選擇 **"刪除**"。 然後，選取確認對話方塊中的 [是]****。

#### <a name="commands"></a>命令

| 工具 | Command |
| ---- | ------- |
| Azure CLI | [az 網路 nsg 刪除](/cli/azure/network/nsg#az-network-nsg-delete) |
| PowerShell | [刪除-阿茲網路安全性群組](/powershell/module/az.network/remove-aznetworksecuritygroup) |

## <a name="work-with-security-rules"></a>使用安全性規則

網路安全性群組包含零個或多個安全性規則。 您可以建立安全性規則、[檢視所有安全性規則](#view-all-security-rules)[檢視安全性規則的詳細資料](#view-details-of-a-security-rule)，以及[變更](#change-a-security-rule)和[刪除](#delete-a-security-rule)安全性規則。

### <a name="create-a-security-rule"></a>建立安全性規則

每個網路安全性群組可以為每個 Azure 位置和訂閱創建多少規則是有限制的。 要瞭解更多資訊，請參閱[Azure 訂閱和服務限制、配額和約束](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)。

1. 轉到[Azure 門戶](https://portal.azure.com)以查看網路安全性群組。 搜索並選擇**網路安全性群組**。

2. 選擇要向其添加安全規則的網路安全性群組的名稱。

3. 在網路安全性群組的功能表列中，選擇**入站安全規則**或**出站安全規則**。

    列出了幾個現有規則，包括您可能尚未添加的一些規則。 創建網路安全性群組時，會創建多個預設安全規則。 若要深入了解，請參閱[預設安全性規則](security-overview.md#default-security-rules)。  您無法刪除預設安全性規則，但是可以使用優先順序較高的規則來覆寫它們。

4. <a name="security-rule-settings"></a>選擇 **"添加**"。 選擇或添加以下設置的值，然後選擇 **"確定**" ：

    | 設定 | 值 | 詳細資料 |
    | ------- | ----- | ------- |
    | **來源** | 值為下列其中之一：<ul><li>**任何**</li><li>**IP 位址**</li><li>**服務標記**（入站安全規則）或**虛擬網路**（出站安全規則）</li><li>**應用程式&nbsp;安全&nbsp;組**</li></ul> | <p>如果選擇**IP 位址**，還必須指定**源 IP 位址/CIDR 範圍**。</p><p>如果選擇**服務標記**，還可以選擇**源服務標記**。</p><p>如果選擇**應用程式安全性群組**，還必須選擇現有的應用程式安全性群組。 如果同時為 **"源"****和目標**選擇**應用程式安全性群組**，則兩個應用程式安全性群組中的網路介面必須位於同一虛擬網路中。</p> |
    | **源 IP 位址/CIDR 範圍** | IP 位址和無類域間路由 （CIDR） 範圍的逗號分隔清單 | <p>如果將**源**更改為**IP 位址**，則將顯示此設置。 必須指定多個值的單個值或逗號分隔清單。 多個值的一個示例`10.0.0.0/16, 192.188.1.1`是 。 您可以指定的值數目有所限制。 有關詳細資訊，請參閱[Azure 限制](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)。</p><p>如果指定的 IP 位址分配給 Azure VM，請指定其私人 IP 位址，而不是其公共 IP 位址。 Azure 將公共 IP 位址轉換為入站安全規則的私人 IP 位址後，但在將私人 IP 位址轉換為出站規則的公共 IP 位址之前，將安全規則進行處理。 若要深入了解 Azure 中的公用和私人 IP 位址，請參閱 [IP 位址類型](virtual-network-ip-addresses-overview-arm.md)。</p> |
    | **源服務標記** | 下拉清單中的服務標記 | 如果為入站安全規則設置了 **"源**到**服務標記"，** 則會出現此可選設置。 服務標籤是為 IP 位址類別預先定義的識別碼。 要瞭解有關可用服務標記以及每個標記表示的內容的更多內容，請參閱[服務標記](security-overview.md#service-tags)。 |
    | **源應用程式安全性群組** | 現有應用程式安全性群組 | 如果將 **"源"** 設置為**應用程式安全性群組**，則將顯示此設置。 選擇與網路介面位於同一區域中的應用程式安全性群組。 了解如何[建立應用程式安全性群組](#create-an-application-security-group)。 |
    | **源埠範圍** | 值為下列其中之一：<ul><li>單個埠，例如`80`</li><li>一系列埠，例如`1024-65535`</li><li>單個埠和/或埠範圍的逗號分隔清單，例如`80, 1024-65535`</li><li>星號 （`*`） 允許任何埠上的流量</li></ul> | 此設置指定規則允許或拒絕流量的埠。 您可以指定的連接埠數目有所限制。 有關詳細資訊，請參閱[Azure 限制](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)。 |
    | **目的地** | 值為下列其中之一：<ul><li>**任何**</li><li>**IP 位址**</li><li>**服務標記**（出站安全規則）或**虛擬網路**（入站安全規則）</li><li>**應用程式&nbsp;安全&nbsp;組**</li></ul> | <p>如果選擇**IP 位址**，則還要指定**目標 IP 位址/CIDR 範圍**。</p><p>如果選擇 **"虛擬網路"，** 則允許流量訪問虛擬網路位址空間中的所有 IP 位址。 **虛擬網路**是服務標記。</p><p>如果選擇**應用程式安全性群組**，則必須選擇現有應用程式安全性群組。 了解如何[建立應用程式安全性群組](#create-an-application-security-group)。</p> |
    | **目標 IP 位址/CIDR 範圍** | IP 位址和 CIDR 範圍的逗號分隔清單 | <p>如果將 **"目標"** 更改為 IP**位址**，則將顯示此設置。 與**源**和**源 IP 位址/CIDR 範圍**類似，您可以指定單個或多個位址或範圍。 您可以指定的數位有限制。 有關詳細資訊，請參閱[Azure 限制](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)。</p><p>如果指定的 IP 位址已分配給 Azure VM，請確保指定其專用 IP，而不是其公共 IP 位址。 Azure 將公共 IP 位址轉換為入站安全規則的私人 IP 位址後，但在 Azure 將私人 IP 位址轉換為出站規則的公共 IP 位址之前，將安全規則進行處理。 若要深入了解 Azure 中的公用和私人 IP 位址，請參閱 [IP 位址類型](virtual-network-ip-addresses-overview-arm.md)。</p> |
    | **目的地服務標記** | 下拉清單中的服務標記 | 如果為出站安全規則更改 **"目標**到**服務標記"，** 則將顯示此可選設置。 服務標籤是為 IP 位址類別預先定義的識別碼。 要瞭解有關可用服務標記以及每個標記表示的內容的更多內容，請參閱[服務標記](security-overview.md#service-tags)。 |
    | **目標應用程式安全性群組** | 現有應用程式安全性群組 | 如果將 **"目標"** 設置為**應用程式安全性群組**，則將顯示此設置。 選擇與網路介面位於同一區域中的應用程式安全性群組。 了解如何[建立應用程式安全性群組](#create-an-application-security-group)。 |
    | **目標埠範圍** | 值為下列其中之一：<ul><li>單個埠，例如`80`</li><li>一系列埠，例如`1024-65535`</li><li>單個埠和/或埠範圍的逗號分隔清單，例如`80, 1024-65535`</li><li>星號 （`*`） 允許任何埠上的流量</li></ul> | 與**Source 埠範圍**一樣，您可以指定單個或多個埠和範圍。 您可以指定的數位有限制。 有關詳細資訊，請參閱[Azure 限制](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)。 |
    | **協定** | **任何** **、TCP、UDP**或**ICMP** **UDP** | 您可以將規則限制為傳輸控制協定 （TCP）、使用者資料圖協定 （UDP） 或 Internet 控制訊息協定 （ICMP）。 預設值是規則應用於所有協定。 |
    | **動作** | **允許**或**拒絕** | 此設置指定此規則是否允許或拒絕對提供的源和目標配置的訪問。 |
    | **優先順序** | 100 和 4096 之間的值，對於網路安全性群組中的所有安全規則都是唯一的 | Azure 按優先順序連續處理安全規則。 編號愈低，優先順序愈高。 我們建議您在創建規則（如 100、200 和 300）時，在優先順序數位之間留出間隙。 留下間隙可以更輕鬆地在將來添加規則，以便您可以給予它們比現有規則更高或更低的優先順序。 |
    | **名稱** | 網路安全性群組中規則的唯一名稱 | 此名稱最多可有 80 個字元。 它必須以字母或數位開頭，並且必須以字母、數位或底線結尾。 名稱可能僅包含字母、數位、底線、句點或連字號。 |
    | **描述** | 文本說明 | 您可以選擇為安全規則指定文本說明。 |

#### <a name="commands"></a>命令

| 工具 | Command |
| ---- | ------- |
| Azure CLI | [az network nsg rule create](/cli/azure/network/nsg/rule#az-network-nsg-rule-create) |
| PowerShell | [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig) |

### <a name="view-all-security-rules"></a>檢視所有安全性規則

網路安全性群組包含零個或多個規則。 若要深入了解檢視規則時列出的資訊，請參閱[網路安全性群組概觀](security-overview.md)。

1. 轉到[Azure 門戶](https://portal.azure.com)以查看網路安全性群組的規則。 搜索並選擇**網路安全性群組**。

2. 選擇要為其查看規則的網路安全性群組的名稱。

3. 在網路安全性群組的功能表列中，選擇**入站安全規則**或**出站安全規則**。

該清單包含您創建的任何規則以及網路安全性群組的[預設安全規則](security-overview.md#default-security-rules)。

#### <a name="commands"></a>命令

| 工具 | Command |
| ---- | ------- |
| Azure CLI | [az network nsg rule list](/cli/azure/network/nsg/rule#az-network-nsg-rule-list) |
| PowerShell | [獲取-阿茲網路安全規則配置](/powershell/module/az.network/get-aznetworksecurityruleconfig) |

### <a name="view-details-of-a-security-rule"></a>檢視安全性規則的詳細資料

1. 轉到[Azure 門戶](https://portal.azure.com)以查看網路安全性群組的規則。 搜索並選擇**網路安全性群組**。

2. 選擇要為其查看規則詳細資訊的網路安全性群組的名稱。

3. 在網路安全性群組的功能表列中，選擇**入站安全規則**或**出站安全規則**。

4. 選取您想要檢視其詳細資料的規則。 有關所有設置的說明，請參閱[安全規則設置](#security-rule-settings)。

    > [!NOTE]
    > 此過程僅適用于自訂安全規則。 如果選擇預設安全規則，則不起作用。

#### <a name="commands"></a>命令

| 工具 | Command |
| ---- | ------- |
| Azure CLI | [az 網路 nsg 規則顯示](/cli/azure/network/nsg/rule#az-network-nsg-rule-show) |
| PowerShell | [獲取-阿茲網路安全規則配置](/powershell/module/az.network/get-aznetworksecurityruleconfig) |

### <a name="change-a-security-rule"></a>變更安全性規則

1. 完成[檢視安全性規則的詳細資料](#view-details-of-a-security-rule)中的步驟。

2. 根據需要更改設置，然後選擇 **"保存**"。 有關所有設置的說明，請參閱[安全規則設置](#security-rule-settings)。

    > [!NOTE]
    > 此過程僅適用于自訂安全規則。 不允許更改預設安全規則。

#### <a name="commands"></a>命令

| 工具 | Command |
| ---- | ------- |
| Azure CLI | [az network nsg rule update](/cli/azure/network/nsg/rule#az-network-nsg-rule-update) |
| PowerShell | [Set-AzNetworkSecurityRuleConfig](/powershell/module/az.network/set-aznetworksecurityruleconfig) |

### <a name="delete-a-security-rule"></a>刪除安全性規則

1. 完成[檢視安全性規則的詳細資料](#view-details-of-a-security-rule)中的步驟。

2. 選取 [刪除]****，然後選取 [是]****。

    > [!NOTE]
    > 此過程僅適用于自訂安全規則。 不允許刪除預設安全規則。

#### <a name="commands"></a>命令

| 工具 | Command |
| ---- | ------- |
| Azure CLI | [az 網路 nsg 規則刪除](/cli/azure/network/nsg/rule#az-network-nsg-rule-delete) |
| PowerShell | [刪除-阿茲網路安全規則配置](/powershell/module/az.network/remove-aznetworksecurityruleconfig) |

## <a name="work-with-application-security-groups"></a>使用應用程式安全性群組

應用程式安全性群組包含零個或多個網路介面。 若要深入了解，請參閱[應用程式安全性群組](security-overview.md#application-security-groups)。 應用程式安全性群組內的所有網路介面都必須存在於相同的虛擬網路中。 若要了解如何將網路介面新增至應用程式安全性群組，請參閱[將網路介面新增至應用程式安全性群組](virtual-network-network-interface.md#add-to-or-remove-from-application-security-groups)。

### <a name="create-an-application-security-group"></a>建立應用程式安全性群組

1. 在[Azure 門戶](https://portal.azure.com)功能表或**主頁**上，選擇 **"創建資源**"。

2. 在搜索框中，輸入*應用程式安全性群組*。

3. 在 **"應用程式安全性群組"** 頁中，選擇 **"創建**"。

4. 在"**創建應用程式安全性群組"** 頁中，在 **"基本"** 選項卡下，為以下設置設置值：

    | 設定 | 動作 |
    | --- | --- |
    | **訂閱** | 選擇您的訂用帳戶。 |
    | **資源組** | 選擇現有資源組，或選擇 **"新建**"以創建新資源組。 |
    | **名稱** | 在資源組中輸入唯一的文本字串。 |
    | **地區** | 選擇您想要的位置。 |

5. 選擇 **"審閱" = 創建**。

6. 在 **"審核 + 創建**"選項卡下，在看到 **"驗證傳遞"** 消息後，選擇 **"創建**"。

#### <a name="commands"></a>命令

| 工具 | Command |
| ---- | ------- |
| Azure CLI | [az 網路 asg 創建](/cli/azure/network/asg#az-network-asg-create) |
| PowerShell | [新應用安全性群組](/powershell/module/az.network/new-azapplicationsecuritygroup) |

### <a name="view-all-application-security-groups"></a>檢視所有應用程式安全性群組

轉到[Azure 門戶](https://portal.azure.com)以查看應用程式安全性群組。 搜索並選擇**應用程式安全性群組**。 Azure 門戶顯示應用程式安全性群組的清單。

#### <a name="commands"></a>命令

| 工具 | Command |
| ---- | ------- |
| Azure CLI | [az 網路 asg 清單](/cli/azure/network/asg#az-network-asg-list) |
| PowerShell | [獲取應用安全性群組](/powershell/module/az.network/get-azapplicationsecuritygroup) |

### <a name="view-details-of-a-specific-application-security-group"></a>檢視特定應用程式安全性群組的詳細資料

1. 轉到[Azure 門戶](https://portal.azure.com)以查看應用程式安全性群組。 搜索並選擇**應用程式安全性群組**。

2. 選擇要查看其詳細資訊的應用程式安全性群組的名稱。

#### <a name="commands"></a>命令

| 工具 | Command |
| ---- | ------- |
| Azure CLI | [az 網路 asg 顯示](/cli/azure/network/asg#az-network-asg-show) |
| PowerShell | [獲取應用安全性群組](/powershell/module/az.network/get-azapplicationsecuritygroup) |

### <a name="change-an-application-security-group"></a>變更應用程式安全性群組

1. 轉到[Azure 門戶](https://portal.azure.com)以查看應用程式安全性群組。 搜索並選擇**應用程式安全性群組**。

2. 選擇要更改的應用程式安全性群組的名稱。

3. 選擇要修改的設置旁邊的**更改**。 例如，您可以添加或刪除**標記**，也可以更改**資源組**或**訂閱**。

    > [!NOTE]
    > 您無法更改位置。

    在功能表列中，您還可以選擇**存取控制 （IAM）。** 在**Access 控制項 （IAM）** 頁中，您可以向應用程式安全性群組分配或刪除許可權。

#### <a name="commands"></a>命令

| 工具 | Command |
| ---- | ------- |
| Azure CLI | [az 網路 asg 更新](/cli/azure/network/asg#az-network-asg-update) |
| PowerShell | 無電源外殼 Cmdlet |

### <a name="delete-an-application-security-group"></a>刪除應用程式安全性群組

如果應用程式安全性群組包含任何網路介面，則無法刪除它。 要從應用程式安全性群組中刪除所有網路介面，請更改網路介面設置或刪除網路介面。 要瞭解更多資訊，請參閱[從應用程式安全性群組添加或刪除](virtual-network-network-interface.md#add-to-or-remove-from-application-security-groups)或[刪除網路介面](virtual-network-network-interface.md#delete-a-network-interface)。

1. 轉到[Azure 門戶](https://portal.azure.com)以管理應用程式安全性群組。 搜索並選擇**應用程式安全性群組**。

2. 選擇要刪除的應用程式安全性群組的名稱。

3. 選取 [刪除]****，然後選取 [是]**** 刪除應用程式安全性群組。

#### <a name="commands"></a>命令

| 工具 | Command |
| ---- | ------- |
| Azure CLI | [az 網路 asg 刪除](/cli/azure/network/asg#az-network-asg-delete) |
| PowerShell | [刪除-應用安全性群組](/powershell/module/az.network/remove-azapplicationsecuritygroup) |

## <a name="permissions"></a>權限

要執行網路安全性群組、安全規則和應用程式安全性群組的任務，您的帳戶必須分配給[網路參與者](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)角色或已分配相應許可權的[自訂角色](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json)，如下表中列出：

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
| Microsoft.Network/networkSecurityGroups/rules/read            |   取得規則                                                            |
| Microsoft.Network/networkSecurityGroups/rules/write           |   建立或更新規則                                               |
| Microsoft.Network/networkSecurityGroups/rules/delete          |   刪除規則                                                         |

### <a name="application-security-group"></a>應用程式安全性群組

| 動作                                                                     | 名稱                                                     |
| --------------------------------------------------------------             | -------------------------------------------              |
| Microsoft.Network/applicationSecurityGroups/joinIpConfiguration/action     | 將 IP 設定加入至應用程式安全性群組|
| Microsoft.Network/applicationSecurityGroups/joinNetworkSecurityRule/action | 將安全性規則加入至應用程式安全性群組    |
| Microsoft.Network/applicationSecurityGroups/read                           | 取得應用程式安全性群組                        |
| Microsoft.Network/applicationSecurityGroups/write                          | 建立或更新應用程式安全性群組           |
| Microsoft.Network/applicationSecurityGroups/delete                         | 刪除應用程式安全性群組                     |

## <a name="next-steps"></a>後續步驟

- 使用[PowerShell](powershell-samples.md)或[Azure CLI](cli-samples.md)示例腳本或 Azure[資源管理器範本](template-samples.md)創建網路或應用程式安全性群組
- 為虛擬網路建立及套用 [Azure 原則](policy-samples.md)
