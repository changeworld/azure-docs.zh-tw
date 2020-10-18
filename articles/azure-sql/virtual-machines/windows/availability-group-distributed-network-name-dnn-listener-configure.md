---
title: 設定可用性群組的 DNN 接聽程式
description: 瞭解如何設定分散式網路名稱 (DNN) 接聽程式來取代您的虛擬網路名稱 (VNN) 接聽程式，並將流量路由傳送至 Azure VM 上 Always On 上的 SQL Server 可用性群組。
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: jroth
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 10/07/2020
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: abfcd6a13bc5e8ad262fe47111eb680ad00a34df
ms.sourcegitcommit: 419c8c8061c0ff6dc12c66ad6eda1b266d2f40bd
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/18/2020
ms.locfileid: "92168818"
---
# <a name="configure-a-dnn-listener-for-an-availability-group"></a>設定可用性群組的 DNN 接聽程式
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

透過 Azure Vm 上的 SQL Server， (DNN 的分散式網路名稱) 會將流量路由傳送至適當的叢集資源。 它可讓您更輕鬆地連接到 Always On 可用性群組 (AG) 與虛擬網路名稱 (VNN) 接聽程式，而不需要 Azure Load Balancer。 

本文說明如何設定 DNN 接聽程式，以使用 Azure Vm 上的 SQL Server 來取代 VNN 接聽程式，並將流量路由傳送至您的可用性群組，以提供高可用性和嚴重損壞修復 (HADR) 。 

DNN 接聽程式功能目前只能從 Windows Server 2016 和更新版本上的 SQL Server 2019 CU8 開始使用。 

如果是替代的連線選項，請考慮使用 VNN 接聽程式， [並 Azure Load Balancer](availability-group-vnn-azure-load-balancer-configure.md) 。 

## <a name="overview"></a>概觀

分散式網路名稱 (DNN) 接聽程式會在與 Always On 可用性群組 [vm 上的 SQL Server](availability-group-overview.md)搭配使用時，取代傳統的虛擬網路名稱 (VNN) 可用性群組接聽程式。 這樣就不需要 Azure Load Balancer 來路由傳送流量、簡化部署、維護和改善容錯移轉。 

您可以使用 DNN 接聽程式來取代現有的 VNN 接聽程式，或將其與現有的 VNN 接聽程式搭配使用，讓您的可用性群組有兩個不同的連接點：一個使用 VNN 接聽程式名稱， (和埠（如果非預設) ），另一個使用 DNN 接聽程式名稱和埠。 

## <a name="prerequisites"></a>必要條件

建議先準備好下列項目，再完成本文中的步驟：

- 在 CU8 或更新版本上的 Windows Server 2016 和更新版本上的 SQL Server 2019
- 決定分散式網路名稱是適用 [于 HADR 解決方案](hadr-cluster-best-practices.md#connectivity)的適當連線選項。
- 已設定您的 [Always On 可用性群組](availability-group-overview.md)。 
- 已安裝最新版本的 [PowerShell](/powershell/azure/install-az-ps)。 


## <a name="create-script"></a>建立腳本

使用 PowerShell 來建立分散式網路名稱 (DNN) 資源，並將其與您的可用性群組建立關聯。 

若要這樣做，請依照下列步驟執行： 

1. 開啟文字編輯器，例如 [記事本]。 
1. 複製並貼上下列指令碼： 

   ```powershell
   param (
      [Parameter(Mandatory=$true)][string]$Ag,
      [Parameter(Mandatory=$true)][string]$Dns,
      [Parameter(Mandatory=$true)][string]$Port
   )
   
   Write-Host "Add a DNN listener for availability group $Ag with DNS name $Dns and port $Port"
   
   $ErrorActionPreference = "Stop"
   
   # create the DNN resource with the port as the resource name
   Add-ClusterResource -Name $Port -ResourceType "Distributed Network Name" -Group $Ag 
   
   # set the DNS name of the DNN resource
   Get-ClusterResource -Name $Port | Set-ClusterParameter -Name DnsName -Value $Dns 
   
   # start the DNN resource
   Start-ClusterResource -Name $Port
   
   
   $Dep = Get-ClusterResourceDependency -Resource $Ag
   if ( $Dep.DependencyExpression -match '\s*\((.*)\)\s*' )
   {
   $DepStr = "$($Matches.1) or [$Port]"
   }
   else
   {
   $DepStr = "[$Port]"
   }
   
   Write-Host "$DepStr"
   
   # add the Dependency from availability group resource to the DNN resource
   Set-ClusterResourceDependency -Resource $Ag -Dependency "$DepStr"
   
   
   #bounce the AG resource
   Stop-ClusterResource -Name $Ag
   Start-ClusterResource -Name $Ag
   ```

1. 將腳本儲存為檔案 `.ps1` ，例如 `add_dnn_listener.ps1` 。 


## <a name="execute-script"></a>執行腳本

若要建立 DNN 接聽程式，請針對可用性群組、接聽程式名稱和埠的名稱，執行傳遞參數的腳本。 

例如，假設的可用性組名、接聽程式 `ag1` 名稱以及接聽程式 `dnnlsnr` 埠，請遵循下列 `6789` 步驟： 

1. 開啟命令列介面工具，例如命令提示字元或 PowerShell。 
1. 流覽至您儲存腳本的位置 `.ps1` ，例如 c:\Documents。 
1. 執行腳本： ```add_dnn_listener.ps1 <ag name> <listener-name> <listener port>``` 。 例如： 

   ```console
   c:\Documents> add_dnn_listener.ps1 ag1 dnnlsnr 6789
   ```

## <a name="verify-listener"></a>確認接聽程式

使用 SQL Server Management Studio 或 Transact-sql 來確認已成功建立您的 DNN 接聽程式。 

### <a name="sql-server-management-studio"></a>SQL Server Management Studio

展開[SQL Server Management Studio (SSMS) ](/sql/ssms/download-sql-server-management-studio-ssms)中的**可用性群組**接聽程式，以查看您的 DNN 接聽程式： 

:::image type="content" source="media/availability-group-distributed-network-name-dnn-listener-configure/dnn-listener-in-ssms.png" alt-text="在 SQL Server Management Studio (SSMS) 的可用性群組接聽程式下，查看 DNN 接聽程式 ":::

### <a name="transact-sql"></a>Transact-SQL

使用 Transact-sql 來查看 DNN 接聽程式的狀態： 

```sql
SELECT * FROM SYS.AVAILABILITY_GROUP_LISTENERS
```

的值 `1` 表示接聽 `is_distributed_network_name` 程式是分散式網路名稱 (DNN) 接聽程式： 

:::image type="content" source="media/availability-group-distributed-network-name-dnn-listener-configure/dnn-listener-tsql.png" alt-text="在 SQL Server Management Studio (SSMS) 的可用性群組接聽程式下，查看 DNN 接聽程式 ":::


## <a name="update-connection-string"></a>更新連接字串

更新應用程式的連接字串，使其連接至 DNN 接聽程式。 若要確保在容錯移轉時能快速連線，請在 `MultiSubnetFailover=True` SQL 用戶端支援連接字串時加入連接字串。 

## <a name="test-failover"></a>測試容錯移轉

測試可用性群組的容錯移轉，以確保功能。 

若要測試容錯移轉，請遵循下列步驟： 

1. 使用 [SQL Server Management Studio (SSMS) ](/sql/ssms/download-sql-server-management-studio-ssms)連接到 DNN 接聽程式或其中一個複本。 
1. 在**物件總管**中展開**Always On 可用性群組**。 
1. 以滑鼠右鍵按一下可用性群組，然後選擇 [ **容錯移轉** ]，開啟 [ **容錯移轉]**。 
1. 遵循提示來選擇容錯移轉目標，並將可用性群組容錯移轉至次要複本。 
1. 確認新的主要複本上的資料庫處於已同步處理的狀態。 
1.  (選擇性) 容錯回復到原始的主要複本或另一個次要複本。 

## <a name="test-connectivity"></a>測試連線能力

使用下列步驟來測試 DNN 接聽程式的連線能力：

1. 開啟 [SQL Server Management Studio]  。
1. 連接到您的 DNN 接聽程式。 
1. 開啟新的查詢視窗，並執行以檢查您所連線的複本 `SELECT @@SERVERNAME` 。 
1. 將可用性群組容錯移轉至另一個複本。
1. 經過一段合理的時間之後，請執行 `SELECT @@SERVERNAME` 以確認您的可用性群組現在裝載于另一個複本。 


## <a name="limitations"></a>限制

- 目前，只有在 Windows Server 2016 和更新版本上的 SQL Server 2019 CU8 和更新版本才支援可用性群組的 DNN 接聽程式。 
- 當您使用其他 SQL Server 功能和具有 DNN 的可用性群組時，可能會有其他考慮。 如需詳細資訊，請參閱 [具有 DNN 互通性的 AG](availability-group-dnn-interoperability.md)。 

## <a name="next-steps"></a>後續步驟

若要深入瞭解 Azure 中 SQL Server HADR 功能，請參閱 [可用性群組](availability-group-overview.md) 和 [容錯移轉叢集實例](failover-cluster-instance-overview.md)。 您也可以瞭解設定環境以達到高可用性和嚴重損壞修復的 [最佳作法](hadr-cluster-best-practices.md) 。 


