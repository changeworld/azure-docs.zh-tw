---
title: " (DNN) 設定分散式網路名稱"
description: 瞭解如何設定分散式網路名稱 (DNN) ，以將流量路由至 Azure VM 容錯移轉叢集實例上的 SQL Server (FCI) 。
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: jroth
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/02/2020
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 8eb9caf466148e43266c4be9cf1308da15fb67f2
ms.sourcegitcommit: c293217e2d829b752771dab52b96529a5442a190
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/15/2020
ms.locfileid: "88245531"
---
# <a name="configure-a-distributed-network-name-for-an-fci"></a>設定 FCI 的分散式網路名稱 
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

在 Azure 虛擬機器上， (DNN) 的分散式網路名稱會用來將流量路由傳送至適當的叢集資源。 它可讓您更輕鬆地連接到 SQL Server 容錯移轉叢集實例 (FCI) 與虛擬網路名稱 (VNN) 相比，而不需要 Azure Load Balancer。 這項功能目前為預覽狀態，僅適用于 SQL Server 2019 CU2 和更新版本以及 Windows Server 2016 及更新版本。 

本文說明如何設定 DNN，以使用 Azure Vm 上的 SQL Server 將流量路由至您的 Fci，以 (HADR) 進行高可用性和嚴重損壞修復。 

## <a name="prerequisites"></a>必要條件

建議先準備好下列項目，再完成本文中的步驟：

- 決定分散式網路名稱是 HADR 解決方案的適當 [連接選項](hadr-cluster-best-practices.md#connectivity)。
- 已設定您的 [容錯移轉叢集實例](failover-cluster-instance-overview.md)。 
- 已安裝最新版的 [PowerShell](/powershell/azure/install-az-ps)。 

## <a name="create-dnn-resource"></a>建立 DNN 資源 

DNN 資源會建立在與 SQL Server FCI 相同的叢集群組中。 使用 PowerShell 來建立 FCI 叢集群組內的 DNN 資源。 

下列 PowerShell 命令會將 DNN 資源新增至資源名稱為的 SQL Server FCI 叢集群組 `<dnnResourceName>` 。 資源名稱是用來唯一識別資源。 請使用對您有意義的，而且在整個叢集中是唯一的。 資源類型必須是 `Distributed Network Name` 。 

此 `-Group` 值必須是對應至您要新增分散式網路名稱的 SQL SERVER FCI 之叢集群組的名稱。 若是預設實例，一般的格式為 `SQL Server (MSSQLSERVER)` 。 


```powershell
Add-ClusterResource -Name <dnnResourceName> `
-ResourceType "Distributed Network Name" -Group "<WSFC role of SQL server instance>"
```

例如，若要建立 `dnn-demo` 預設 SQL SERVER FCI 的 DNN 資源，請使用下列 PowerShell 命令：

```powershell
Add-ClusterResource -Name dnn-demo `
-ResourceType "Distributed Network Name" -Group "SQL Server (MSSQLSERVER)"

```

## <a name="set-cluster-dnn-dns-name"></a>設定叢集 DNN DNS 名稱

設定叢集中 DNN 資源的 DNS 名稱。 然後，叢集會使用此值將流量路由傳送到目前裝載 SQL Server FCI 的節點。 
 
用戶端會使用 DNS 名稱來連接到 SQL Server FCI。 您可以選擇唯一的值。 或者，如果您已經有現有的 FCI，而且不想要更新用戶端連接字串，您可以將 DNN 設定為使用用戶端已在使用的目前 VNN。 若要這樣做，您必須在 DNS 中設定 DNN 之前，先 [重新命名 VNN](#rename-the-vnn) 。


使用此命令來設定 DNN 的 DNS 名稱： 

```powershell
Get-ClusterResource -Name <dnnResourceName> | `
Set-ClusterParameter -Name DnsName -Value <DNSName>
```

`DNSName`值是用戶端用來連接到 SQL SERVER FCI 的值。 例如，若要讓用戶端連接到 `FCIDNN` ，請使用下列 PowerShell 命令：

```powershell
Get-ClusterResource -Name dnn-demo | `
Set-ClusterParameter -Name DnsName -Value FCIDNN
```

用戶端現在會 `FCIDNN` 在連接到 SQL SERVER FCI 時，輸入其連接字串。 

   > [!WARNING]
   > 請勿 (VNN) 刪除目前的虛擬網路名稱，因為它是 FCI 基礎結構的必要元件。 


### <a name="rename-the-vnn"></a>重新命名 VNN 

如果您有現有的虛擬網路名稱，而且您想要用戶端繼續使用此值來連接到 SQL Server FCI，您必須將目前的 VNN 重新命名為預留位置值。 在重新命名目前的 VNN 之後，您可以將 DNN 的 DNS 名稱值設定為 VNN。 

有一些限制適用于重新命名 VNN。 如需詳細資訊，請參閱重新 [命名 FCI](/sql/sql-server/failover-clusters/install/rename-a-sql-server-failover-cluster-instance)。

如果您的企業不需要使用目前的 VNN，請略過本節。 在您重新命名 VNN 之後，請 [設定 CLUSTER DNN DNS 名稱](#set-cluster-dnn-dns-name)。 

   
## <a name="set-dnn-resource-online"></a>設定 DNN 資源上線

在您的 DNN 資源適當命名之後，而且您已在叢集中設定 DNS 名稱值後，請使用 PowerShell 將叢集中的 DNN 資源設定為線上： 

```powershell
Start-ClusterResource -Name <dnnResourceName>
```

例如，若要啟動您的 DNN 資源 `dnn-demo` ，請使用下列 PowerShell 命令： 

```powershell
Start-ClusterResource -Name dnn-demo
```

## <a name="configure-possible-owners"></a>設定可能的擁有者

根據預設，叢集會將 DNN DNS 名稱系結到叢集中的所有節點。 不過，叢集中不屬於 SQL Server FCI 的節點，應從 DNN 可能的擁有者清單中排除。 

若要更新可能的擁有者，請遵循下列步驟：

1. 移至容錯移轉叢集管理員中的 DNN 資源。 
1. 以滑鼠右鍵按一下 [DNN] 資源，然後選取 [ **屬性**]。 
   :::image type="content" source="media/hadr-distributed-network-name-dnn-configure/fci-dnn-properties.png" alt-text="DNN 資源的快捷方式功能表，並反白顯示 Properties 命令。":::
1. 針對未參與容錯移轉叢集實例的任何節點，清除該核取方塊。 DNN 資源的可能擁有者清單應符合 SQL Server 實例資源的可能擁有者清單。 例如，假設 Data3 不參與 FCI，下列影像是從 DNN 資源的可能擁有者清單中移除 Data3 的範例： 

   :::image type="content" source="media/hadr-distributed-network-name-dnn-configure/clear-check-for-nodes-not-in-fci.png" alt-text="針對 DNN 資源的可能擁有者，清除未參與 FCI 的節點旁的核取方塊":::

1. 選取 [確定]**** 儲存設定。 


## <a name="restart-sql-server-instance"></a>重新開機 SQL Server 實例 

使用容錯移轉叢集管理員重新開機 SQL Server 實例。 請遵循這些步驟：

1. 前往容錯移轉叢集管理員中的 SQL Server 資源。
1. 以滑鼠右鍵按一下 SQL Server 資源，並使其離線。 
1. 在所有相關聯的資源都離線之後，以滑鼠右鍵按一下 SQL Server 資源，然後重新上線。 

## <a name="update-connection-string"></a>更新連接字串

若要確保在容錯移轉時能夠快速連線，請在 `MultiSubnetFailover=True` SQL 用戶端版本早于4.6.1 時，將新增至連接字串。 

此外，如果 DNN 未使用原始 VNN，連接到 SQL Server FCI 的 SQL 用戶端就必須將其連接字串更新為 DNN DNS 名稱。 若要避免這項需求，您可以將 DNS 名稱值更新為 VNN 的名稱。 但是，您必須先以 [預留位置取代現有的 VNN](#rename-the-vnn) 。 

## <a name="test-failover"></a>測試容錯移轉

測試叢集資源的容錯移轉，以驗證叢集功能。 

若要測試容錯移轉，請遵循下列步驟： 

1. 使用 RDP 連接到其中一個 SQL Server 叢集節點。
1. 開啟 [容錯移轉叢集管理員]。 選取 [角色]。 請注意哪個節點擁有 SQL Server FCI 角色。
1. 以滑鼠右鍵按一下 SQL Server FCI 角色。 
1. 選取 [移動]，然後選取 [最佳可行節點]。

[容錯移轉叢集管理員] 會顯示角色，其資源則會變成離線狀態。 接著資源會移動，並在另一個節點上線。

## <a name="test-connectivity"></a>測試連線能力

若要測試連線能力，請登入相同虛擬網路中的另一部虛擬機器。 開啟 **SQL Server Management Studio** ，然後使用 DNN DNS 名稱連接到 SQL Server FCI。

若有需要，您可[下載 SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms)。

## <a name="avoid-ip-conflict"></a>避免 IP 衝突

這是一個選擇性步驟，可防止 FCI 資源所使用的虛擬 IP (VIP) 位址指派給 Azure 中的另一個資源重複。 

雖然客戶現在使用 DNN 來連線至 SQL Server FCI，但是虛擬網路名稱 (VNN) 和虛擬 IP 無法刪除，因為它們是 FCI 基礎結構的必要元件。 不過，因為已不再有負載平衡器保留 Azure 中的虛擬 IP 位址，所以會有一項風險，也就是虛擬網路上的另一個資源將被指派為與 FCI 所使用的虛擬 IP 位址相同的 IP 位址。 這可能會導致重複的 IP 衝突問題。 

設定 APIPA 位址或專用的網路介面卡，以保留 IP 位址。 

### <a name="apipa-address"></a>APIPA 位址

若要避免使用重複的 IP 位址，請將 APIPA 位址設定 (也稱為連結-本機位址) 。 若要這樣做，請執行下列命令：

```powershell
Get-ClusterResource "virtual IP address" | Set-ClusterParameter 
    –Multiple @{"Address”=”169.254.1.1”;”SubnetMask”=”255.255.0.0”;"OverrideAddressMatch"=1;”EnableDhcp”=0}
```

在此命令中，「虛擬 IP 位址」是叢集 VIP 位址資源的名稱，而「169.254.1.1) 指派」是為 VIP 位址選擇的 APIPA 位址。 選擇最適合您企業的位址。 設定 `OverrideAddressMatch=1` 為允許在任何網路上的 IP 位址，包括 APIPA 位址空間。 

### <a name="dedicated-network-adapter"></a>專用網路介面卡

或者，在 Azure 中設定網路介面卡，以保留虛擬 IP 位址資源所使用的 IP 位址。 不過，這會使用子網位址空間中的位址，而且會有額外的負荷來確保網路介面卡不會用於任何其他用途。

## <a name="limitations"></a>限制

- 目前，只有在 Windows Server 2016 上 SQL Server 2019 CU2 和更新版本才支援 DNN。 
- 目前，只有在 Azure Vm 上具有 SQL Server 的容錯移轉叢集實例，才支援 DNN。 將虛擬網路名稱與可用性群組接聽程式的 Azure Load Balancer 搭配使用。
- 當您使用其他 SQL Server 功能和具有 DNN 的 FCI 時，可能會有更多考慮。 如需詳細資訊，請參閱 [FCI WITH DNN 互通性](failover-cluster-instance-dnn-interoperability.md)。 

## <a name="next-steps"></a>後續步驟

若要深入瞭解 Azure 中 SQL Server HADR 功能，請參閱 [可用性群組](availability-group-overview.md) 和 [容錯移轉叢集實例](failover-cluster-instance-overview.md)。 您也可以瞭解設定環境以提供高可用性和嚴重損壞修復的 [最佳作法](hadr-cluster-best-practices.md) 。 

搭配 DNN 和 FCI 使用時，某些特定 SQL Server 功能可能會有額外的設定需求。 若要深入瞭解，請參閱 [FCI WITH DNN 互通性](failover-cluster-instance-dnn-interoperability.md) 。 

