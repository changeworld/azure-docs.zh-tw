---
title: 同步處理 SQL 受控執行個體虛擬叢集上的虛擬網路 DNS 伺服器設定
description: 瞭解如何同步處理 SQL 受控執行個體虛擬叢集上的虛擬網路 DNS 伺服器設定。
services: sql-database
ms.service: sql-managed-instance
author: srdan-bozovic-msft
ms.author: srbozovi
ms.topic: how-to
ms.date: 01/17/2021
ms.openlocfilehash: 0da38475c0e3c766cabbf765ea89dc5714a5b830
ms.sourcegitcommit: 3c8964a946e3b2343eaf8aba54dee41b89acc123
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/25/2021
ms.locfileid: "98747562"
---
# <a name="synchronize-virtual-network-dns-servers-setting-on-sql-managed-instance-virtual-cluster"></a>同步處理 SQL 受控執行個體虛擬叢集上的虛擬網路 DNS 伺服器設定
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

本文說明何時及如何同步處理 SQL 受控執行個體虛擬叢集上的虛擬網路 DNS 伺服器設定。

## <a name="when-to-synchronize-the-dns-setting"></a>何時同步處理 DNS 設定

在少數案例中 (例如，db 電子郵件，連結的伺服器連線至您雲端或混合式環境中的其他 SQL Server 執行個體)，必須從 SQL 受控執行個體解析私人主機名稱。 在此情況下，您需要在 Azure 內設定自訂 DNS 。 如需詳細資訊，請參閱 [設定 AZURE SQL 的自訂 DNS 受控執行個體](custom-dns-configure.md) 。

如果這項變更是在建立 [虛擬叢集](connectivity-architecture-overview.md#virtual-cluster-connectivity-architecture) 受控執行個體建立之後執行，您必須將虛擬叢集上的 DNS 伺服器設定與虛擬網路設定進行同步處理。

> [!IMPORTANT]
> 同步處理 DNS 伺服器設定將會影響虛擬叢集中裝載的所有受管理的實例。

## <a name="how-to-synchronize-the-dns-setting"></a>如何同步處理 DNS 設定

### <a name="azure-rbac-permissions-required"></a>需要 Azure RBAC 許可權

將 DNS 伺服器設定同步處理的使用者必須擁有下列其中一個 Azure 角色：

- 訂用帳戶擁有者角色，或
- 受控執行個體參與者角色，或
- 具有下列許可權的自訂角色：
  - `Microsoft.Sql/virtualClusters/updateManagedInstanceDnsServers/action`

### <a name="use-azure-powershell"></a>使用 Azure PowerShell

取得已更新 DNS 伺服器設定的虛擬網路。

```PowerShell
$ResourceGroup = 'enter resource group of virtual network'
$VirtualNetworkName = 'enter virtual network name'
$virtualNetwork = Get-AzVirtualNetwork -ResourceGroup $ResourceGroup -Name $VirtualNetworkName
```
使用 PowerShell 命令 [調用-AzResourceAction](/powershell/module/az.resources/invoke-azresourceaction) 來同步處理子網中所有虛擬叢集的 DNS 伺服器設定。

```PowerShell
Get-AzSqlVirtualCluster `
    | where SubnetId -match $virtualNetwork.Id `
    | select Id `
    | Invoke-AzResourceAction -Action updateManagedInstanceDnsServers -Force
```
### <a name="use-the-azure-cli"></a>使用 Azure CLI

取得已更新 DNS 伺服器設定的虛擬網路。

```Azure CLI
resourceGroup="auto-failover-group"
virtualNetworkName="vnet-fog-eastus"
virtualNetwork=$(az network vnet show -g $resourceGroup -n $virtualNetworkName --query "id" -otsv)
```

使用 Azure CLI 命令 [az resource invoke-action](/cli/azure/resource?view=azure-cli-latest#az_resource_invoke_action) 來同步處理子網中所有虛擬叢集的 DNS 伺服器設定。

```Azure CLI
az sql virtual-cluster list --query "[? contains(subnetId,'$virtualNetwork')].id" -o tsv \
    | az resource invoke-action --action updateManagedInstanceDnsServers --ids @-
```
## <a name="next-steps"></a>後續步驟

- 深入瞭解如何設定自訂 DNS [來設定 AZURE SQL 受控執行個體的自訂 dns](custom-dns-configure.md)。
- 如需總覽，請參閱 [什麼是 AZURE SQL 受控執行個體？](sql-managed-instance-paas-overview.md)。
