---
title: 設定容錯移轉叢集實例的 DNN
description: 瞭解如何設定分散式網路名稱 (DNN) ，以將流量路由傳送至 Azure VM 容錯移轉叢集實例上的 SQL Server (FCI) 。
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: jroth
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.subservice: hadr
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 10/07/2020
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 8549592ace00e712929ebc76045a32531b9db659
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/12/2020
ms.locfileid: "97358311"
---
# <a name="configure-a-dnn-for-failover-cluster-instance"></a>設定容錯移轉叢集實例的 DNN
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

在 Azure 虛擬機器上，分散式網路名稱 (DNN) 會將流量路由傳送至適當的叢集資源。 它可讓您更輕鬆地連接到 SQL Server 容錯移轉叢集實例， (FCI)  (VNN) 的虛擬網路名稱，而不需要 Azure Load Balancer。 

本文說明如何設定 DNN 資源，以使用 Azure Vm 上的 SQL Server 將流量路由傳送至您的容錯移轉叢集實例，以提供高可用性和嚴重損壞修復 (HADR) 。 

DNN 功能目前僅適用于 Windows Server 2016 和更新版本上的 SQL Server 2019 CU2 和更新版本。 

如果是替代的連線選項，請考慮使用 [虛擬網路名稱，並 Azure Load Balancer](failover-cluster-instance-vnn-azure-load-balancer-configure.md) 。 

## <a name="overview"></a>概觀

當與 [Always On vm 上的 SQL Server 容錯移轉叢集實例](failover-cluster-instance-overview.md)搭配使用時，分散式網路名稱 (DNN) 會將虛擬網路名稱取代 (VNN) 作為連接點。 這樣就不需要 Azure Load Balancer 將流量路由傳送至 VNN，以簡化部署、維護和改善容錯移轉。 

使用 FCI 部署時，VNN 仍然存在，但是用戶端會連線到 DNN DNS 名稱，而不是 VNN 名稱。 

## <a name="prerequisites"></a>必要條件 

建議先準備好下列項目，再完成本文中的步驟：

- 在 CU2 或更新版本上的 Windows Server 2016 和更新版本上的 SQL Server 2019
- 決定分散式網路名稱是適用 [于 HADR 解決方案](hadr-cluster-best-practices.md#connectivity)的適當連線選項。
- 已設定您的 [容錯移轉叢集實例](failover-cluster-instance-overview.md)。 
- 已安裝最新版本的 [PowerShell](/powershell/azure/install-az-ps)。 

## <a name="create-dnn-resource"></a>建立 DNN 資源 

DNN 資源會建立在與 SQL Server FCI 相同的叢集群組中。 使用 PowerShell 在 FCI 叢集群組內建立 DNN 資源。 

下列 PowerShell 命令會將 DNN 資源新增至資源名稱為的 SQL Server FCI 叢集群組 `<dnnResourceName>` 。 資源名稱會用來唯一識別資源。 使用對您有意義的，而且在整個叢集中是唯一的。 資源類型必須是 `Distributed Network Name` 。 

此 `-Group` 值必須是對應到您要新增分散式網路名稱 SQL SERVER FCI 的叢集組名。 若為預設實例，則一般格式為 `SQL Server (MSSQLSERVER)` 。 


```powershell
Add-ClusterResource -Name <dnnResourceName> `
-ResourceType "Distributed Network Name" -Group "<WSFC role of SQL server instance>"
```

例如，若要建立 `dnn-demo` 預設 SQL SERVER FCI 的 DNN 資源，請使用下列 PowerShell 命令：

```powershell
Add-ClusterResource -Name dnn-demo `
-ResourceType "Distributed Network Name" -Group "SQL Server (MSSQLSERVER)"

```

## <a name="set-cluster-dnn-dns-name"></a>設定 cluster DNN DNS 名稱

在叢集中設定 DNN 資源的 DNS 名稱。 叢集接著會使用這個值，將流量路由傳送到目前裝載 SQL Server FCI 的節點。 

用戶端會使用 DNS 名稱連接到 SQL Server FCI。 您可以選擇唯一值。 或者，如果您已經有現有的 FCI，而且不想要更新用戶端連接字串，您可以將 DNN 設定為使用目前用戶端已在使用中的 VNN。 若要這樣做，您必須先 [重新命名 VNN](#rename-the-vnn) ，然後才能在 DNS 中設定 DNN。

您可以使用此命令來設定 DNN 的 DNS 名稱： 

```powershell
Get-ClusterResource -Name <dnnResourceName> | `
Set-ClusterParameter -Name DnsName -Value <DNSName>
```

`DNSName`值是用戶端用來連接 SQL SERVER FCI 的值。 例如，若要讓用戶端連接到 `FCIDNN` ，請使用下列 PowerShell 命令：

```powershell
Get-ClusterResource -Name dnn-demo | `
Set-ClusterParameter -Name DnsName -Value FCIDNN
```

用戶端現在會 `FCIDNN` 在連接到 SQL SERVER FCI 時，進入其連接字串。 

   > [!WARNING]
   > 請勿刪除目前的虛擬網路名稱 (VNN) ，因為這是 FCI 基礎結構的必要元件。 


### <a name="rename-the-vnn"></a>重新命名 VNN 

如果您有現有的虛擬網路名稱，而您想要讓用戶端繼續使用此值來連接到 SQL Server FCI，您必須將目前的 VNN 重新命名為預留位置值。 重新命名目前的 VNN 之後，您可以將 DNN 的 DNS 名稱值設定為 VNN。 

某些限制適用于重新命名 VNN。 如需詳細資訊，請參閱重新 [命名 FCI](/sql/sql-server/failover-clusters/install/rename-a-sql-server-failover-cluster-instance)。

如果您的企業不需要使用目前的 VNN，請略過本節。 在您重新命名 VNN 之後，請 [設定 CLUSTER DNN DNS 名稱](#set-cluster-dnn-dns-name)。 

   
## <a name="set-dnn-resource-online"></a>設定 DNN 資源上線

當您的 DNN 資源已正確命名，且您已在叢集中設定 DNS 名稱值之後，請使用 PowerShell 在叢集中設定 DNN 資源的連線： 

```powershell
Start-ClusterResource -Name <dnnResourceName>
```

例如，若要啟動 DNN 資源 `dnn-demo` ，請使用下列 PowerShell 命令： 

```powershell
Start-ClusterResource -Name dnn-demo
```

## <a name="configure-possible-owners"></a>設定可能的擁有者

根據預設，叢集會將 DNN DNS 名稱系結至叢集中的所有節點。 但是，不屬於 SQL Server FCI 的叢集中的節點應從 DNN 可能的擁有者清單中排除。 

若要更新可能的擁有者，請遵循下列步驟：

1. 移至容錯移轉叢集管理員中的 DNN 資源。 
1. 以滑鼠右鍵按一下 DNN 資源，然後選取 [ **屬性**]。 

   :::image type="content" source="media/hadr-distributed-network-name-dnn-configure/fci-dnn-properties.png" alt-text="DNN 資源的快捷方式功能表，其中已反白顯示 [屬性] 命令。":::

1. 清除任何未參與容錯移轉叢集實例之節點的核取方塊。 DNN 資源的可能擁有者清單應符合 SQL Server 實例資源的可能擁有者清單。 例如，假設 Data3 並未參與 FCI，下圖是從 DNN 資源的可能擁有者清單中移除 Data3 的範例： 

   :::image type="content" source="media/hadr-distributed-network-name-dnn-configure/clear-check-for-nodes-not-in-fci.png" alt-text="清除未參與 FCI 的節點旁的核取方塊，以找出 DNN 資源的可能擁有者":::

1. 選取 [確定] 儲存設定。 


## <a name="restart-sql-server-instance"></a>重新開機 SQL Server 實例 

使用容錯移轉叢集管理員重新開機 SQL Server 實例。 請遵循這些步驟：

1. 移至容錯移轉叢集管理員中的 SQL Server 資源。
1. 以滑鼠右鍵按一下 SQL Server 資源，並讓它離線。 
1. 所有相關聯的資源都離線之後，請以滑鼠右鍵按一下 SQL Server 資源，然後重新上線。 

## <a name="update-connection-string"></a>更新連接字串

若要在容錯移轉時確保快速連線，請在 `MultiSubnetFailover=True` SQL 用戶端版本早于4.6.1 時，新增至連接字串。 

此外，如果 DNN 不是使用原始 VNN，則連接到 SQL Server FCI 的 SQL 用戶端必須將其連接字串更新為 DNN DNS 名稱。 若要避免此需求，您可以將 DNS 名稱值更新為 VNN 的名稱。 但是，您必須先將 [現有的 VNN 取代為預留位置](#rename-the-vnn) 。 

## <a name="test-failover"></a>測試容錯移轉


測試叢集資源的容錯移轉，以驗證叢集功能。 


若要測試容錯移轉，請遵循下列步驟： 

1. 使用 RDP 連接到其中一個 SQL Server 叢集節點。
1. 開啟 [容錯移轉叢集管理員]。 選取 [角色]。 請注意哪個節點擁有 SQL Server FCI 角色。
1. 以滑鼠右鍵按一下 SQL Server FCI 角色。 
1. 選取 [移動]，然後選取 [最佳可行節點]。

[容錯移轉叢集管理員] 會顯示角色，其資源則會變成離線狀態。 接著資源會移動，並在另一個節點上線。

## <a name="test-connectivity"></a>測試連線能力

若要測試連線能力，請登入相同虛擬網路中的另一部虛擬機器。 開啟 **SQL Server Management Studio** ，並使用 DNN DNS 名稱連線至 SQL Server FCI。

若有需要，您可[下載 SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms)。


## <a name="avoid-ip-conflict"></a>避免 IP 衝突

這是一個選擇性步驟，可防止 FCI 資源用來將虛擬 IP (VIP) 位址指派給 Azure 中的另一個資源，以做為重複項。 

雖然客戶現在使用 DNN 連接到 SQL Server FCI，但無法刪除虛擬網路名稱 (VNN) 和虛擬 IP，因為它們是 FCI 基礎結構的必要元件。 不過，由於在 Azure 中已不再保留虛擬 IP 位址的負載平衡器，因此虛擬網路上的另一項資源將會被指派相同的 IP 位址做為 FCI 所使用的虛擬 IP 位址。 這可能會導致重複的 IP 衝突問題。 

設定 APIPA 位址或專用的網路介面卡，以保留 IP 位址。 

### <a name="apipa-address"></a>APIPA 位址

若要避免使用重複的 IP 位址，請將 APIPA 位址 (也稱為連結-本機位址) 。 若要這樣做，請執行下列命令：

```powershell
Get-ClusterResource "virtual IP address" | Set-ClusterParameter 
    –Multiple @{"Address”=”169.254.1.1”;”SubnetMask”=”255.255.0.0”;"OverrideAddressMatch"=1;”EnableDhcp”=0}
```

在此命令中，「虛擬 IP 位址」是叢集 VIP 位址資源的名稱，而「169.254.1.1) 指派」是為 VIP 位址選擇的 APIPA 位址。 選擇最適合您企業的位址。 設定 `OverrideAddressMatch=1` 為允許在任何網路上的 IP 位址，包括 APIPA 位址空間。 

### <a name="dedicated-network-adapter"></a>專用的網路介面卡

或者，您也可以在 Azure 中設定網路介面卡，以保留虛擬 IP 位址資源所使用的 IP 位址。 不過，這會取用子網位址空間中的位址，而且會有額外的額外負荷，以確保網路介面卡不會用於任何其他用途。

## <a name="limitations"></a>限制

- 目前，只有在 Windows Server 2016 和更新版本上的 SQL Server 2019 CU2 和更新版本才支援具有 FCI 的 DNN。 
- 當您使用其他 SQL Server 功能和具有 DNN 的 FCI 時，可能會有更多的考慮。 如需詳細資訊，請參閱 [FCI 與 DNN 互通性](failover-cluster-instance-dnn-interoperability.md)。 

## <a name="next-steps"></a>後續步驟

若要深入瞭解 Azure 中 SQL Server HADR 功能，請參閱 [可用性群組](availability-group-overview.md) 和 [容錯移轉叢集實例](failover-cluster-instance-overview.md)。 您也可以瞭解設定環境以達到高可用性和嚴重損壞修復的 [最佳作法](hadr-cluster-best-practices.md) 。 


