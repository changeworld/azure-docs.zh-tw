---
title: 設定容錯移轉叢集實例 VNN Azure Load Balancer
description: 瞭解如何設定 Azure Load Balancer，以將流量路由至虛擬網路名稱 (VNN) 適用于您的容錯移轉叢集實例 (FCI) 搭配 Azure Vm 上的 SQL Server，以提供高可用性和嚴重損壞修復 (HADR) 。
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
ms.date: 06/02/2020
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 8f8513746271fff0ab52603e31b75304d5ebc1bf
ms.sourcegitcommit: 419c8c8061c0ff6dc12c66ad6eda1b266d2f40bd
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/18/2020
ms.locfileid: "92168821"
---
# <a name="configure-azure-load-balancer-for-failover-cluster-instance-vnn"></a>設定容錯移轉叢集實例 VNN Azure Load Balancer
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

在 Azure 虛擬機器上，叢集使用負載平衡器來保存一次必須在一個叢集節點上的 IP 位址。 在此解決方案中，負載平衡器會保存虛擬網路名稱的 IP 位址， (VNN) 由 Azure 中的叢集資源使用。 

本文說明如何使用 Azure Load Balancer 服務來設定負載平衡器。 負載平衡器會將流量路由傳送至您的 [容錯移轉叢集實例， (FCI) ](failover-cluster-instance-overview.md) 與 Azure vm 上的 SQL Server，以取得高可用性和嚴重損壞修復 (HADR) 。 

如需 SQL Server 2019 CU2 和更新版本的替代連線選項，請考慮使用 [分散式網路名稱](failover-cluster-instance-distributed-network-name-dnn-configure.md) ，以進行簡化的設定和改善的容錯移轉。 


## <a name="prerequisites"></a>必要條件

建議先準備好下列項目，再完成本文中的步驟：

- 決定 Azure Load Balancer 是 [HADR 解決方案](hadr-cluster-best-practices.md#connectivity)的適當連線選項。
- 已設定您的 [可用性群組](availability-group-overview.md) 接聽程式或 [容錯移轉叢集實例](failover-cluster-instance-overview.md)。 
- 已安裝最新版本的 [PowerShell](/powershell/azure/install-az-ps)。 


## <a name="create-load-balancer"></a>建立負載平衡器

使用 [Azure 入口網站](https://portal.azure.com) 來建立負載平衡器：

1. 在 Azure 入口網站中，前往包含虛擬機器的資源群組。

1. 選取 [新增]。 搜尋 **Load Balancer**的 Azure Marketplace。 選取 [Load Balancer]。

1. 選取 [建立]。

1. 使用下列值設定負載平衡器：

   - 訂用帳戶：您的 Azure 訂用帳戶。
   - **資源群組**：包含虛擬機器的資源群組。
   - **Name**：能識別負載平衡器的名稱。
   - **區域**：包含虛擬機器的 Azure 位置。
   - **類型**：公用或私人。 私人負載平衡器可從相同的虛擬網路內存取。 大部分的 Azure 應用程式都能使用私人負載平衡器。 若應用程式需要直接透過網際網路存取 SQL Server，請使用公用負載平衡器。
   - **SKU**：標準。
   - **虛擬網路**：與虛擬機器相同的網路。
   - **IP 位址指派**：靜態。 
   - **私人 ip 位址**：指派給叢集網路資源的 ip 位址。

   下列映像會顯示 [建立負載平衡器] UI：

   ![設定負載平衡器](./media/failover-cluster-instance-premium-file-share-manually-configure/30-load-balancer-create.png)
   

## <a name="configure-backend-pool"></a>設定後端集區

1. 返回包含虛擬機器的 Azure 資源群組，並找到新的負載平衡器。 您可能需要在資源群組上重新整理檢視。 選取負載平衡器。

1. 選取 [後端集區]，然後選取 [新增]。

1. 將後端集區關聯至包含 VM 的可用性設定組。

1. 在 [目標網路 IP 設定] 下方，選取 [虛擬機器]，並選擇將作為叢集節點加入的虛擬機器。 請務必包含所有將裝載 FCI 或可用性群組的虛擬機器。

1. 選取 [確定] 以建立後端集區。

## <a name="configure-health-probe"></a>設定健康情況探查

1. 在 [負載平衡器] 窗格中，選取 [ **健康情況探查**]。

1. 選取 [新增]  。

1. 在 [**新增健康情況探查**] <span id="probe"> </span>窗格中，設定下列健康情況探查參數：

   - **Name**：健康情況探查的名稱。
   - **通訊協定**：TCP。
   - **埠**： [當您準備 VM 時](failover-cluster-instance-prepare-vm.md#uninstall-sql-server-1)，在防火牆中為健康情況探查建立的埠。 在本文中，範例使用 TCP 連接埠 `59999`。
   - **間隔**：5 秒。
   - **狀況不良閾值**：2 次連續失敗。

1. 選取 [確定]。

## <a name="set-load-balancing-rules"></a>設定負載平衡規則

1. 在 [負載平衡器] 窗格中，選取 [ **負載平衡規則**]。

1. 選取 [新增]  。

1. 設定負載平衡規則參數：

   - **名稱**：負載平衡規則的名稱。
   - **前端 ip 位址**： SQL Server FCI 或 AG 接聽程式的叢集網路資源的 ip 位址。
   - **埠**： SQL Server TCP 埠。 預設執行個體連接埠為 1433。
   - **後端埠**：當您啟用**浮動 IP (直接伺服器傳回) **時，與**埠**值相同的埠。
   - **後端集區**：先前設定的後端集區名稱。
   - **健康情況探查**：先前設定的健康狀態探查。
   - **工作階段持續性**：無。
   - **閒置逾時 (分鐘)** ：4.
   - **浮動 IP (伺服器直接回傳)** ：已啟用。

1. 選取 [確定]。

## <a name="configure-cluster-probe"></a>設定叢集探查

設定 PowerShell 中的叢集探查連接埠參數。

若要設定叢集探查連接埠參數，請將下列指令碼中的變數更新為環境中的值。 移除指令碼中的角括弧 (`<` 和 `>`)。

```powershell
$ClusterNetworkName = "<Cluster Network Name>"
$IPResourceName = "<SQL Server FCI / AG Listener IP Address Resource Name>" 
$ILBIP = "<n.n.n.n>" 
[int]$ProbePort = <nnnnn>

Import-Module FailoverClusters

Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ILBIP";"ProbePort"=$ProbePort;"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}
```

下表描述您需要更新的值：


|**ReplTest1**|**說明**|
|---------|---------|
|`Cluster Network Name`| 網路的 Windows Server 容錯移轉叢集名稱。 在 [容錯移轉叢集管理員] > [網路] 中，以滑鼠右鍵按一下網路，然後選取 [屬性]。 正確的值在 [一般] 索引標籤的 [名稱] 底下。|
|`SQL Server FCI/AG listener IP Address Resource Name`|SQL Server FCI 或 AG 接聽程式 IP 位址的資源名稱。 在 [容錯移轉叢集管理員] > [角色] 中，於 SQL Server FCI 角色下方的 [伺服器名稱] 下，以滑鼠右鍵按一下 IP 位址資源，然後選取 [屬性]。 正確的值在 [一般] 索引標籤的 [名稱] 底下。|
|`ILBIP`|內部負載平衡器的 IP 位址 (ILB) 。 此位址會在 Azure 入口網站中設定為 ILB 的前端位址。 這也是 SQL Server FCI 的 IP 位址。 您可以在 [容錯移轉叢集管理員] 中，您找到 `<SQL Server FCI/AG listener IP Address Resource Name>` 所在位置的相同內容頁面上找到該位址。|
|`nnnnn`|您在負載平衡器的健康情況探查中設定的探查埠。 任何未使用的 TCP 連接埠都有效。|
|SubnetMask| Cluster 參數的子網路遮罩。 它必須是 TCP IP 廣播位址： `255.255.255.255` 。| 


設定叢集探查之後，即可在 PowerShell 中查看所有叢集參數。 執行此指令碼︰

```powershell
Get-ClusterResource $IPResourceName | Get-ClusterParameter
```


## <a name="test-failover"></a>測試容錯移轉


測試叢集資源的容錯移轉，以驗證叢集功能。 

執行下列步驟：

1. 使用 RDP 連接到其中一個 SQL Server 叢集節點。
1. 開啟 [容錯移轉叢集管理員]。 選取 [角色]。 請注意哪個節點擁有 SQL Server FCI 角色。
1. 以滑鼠右鍵按一下 SQL Server FCI 角色。 
1. 選取 [移動]，然後選取 [最佳可行節點]。

[容錯移轉叢集管理員] 會顯示角色，其資源則會變成離線狀態。 接著資源會移動，並在另一個節點上線。


## <a name="test-connectivity"></a>測試連線能力

若要測試連線能力，請登入相同虛擬網路中的另一部虛擬機器。 開啟 [SQL Server Management Studio]，然後連接到 SQL Server FCI 名稱。 

>[!NOTE]
>若有需要，您可[下載 SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms)。



## <a name="next-steps"></a>後續步驟

若要深入瞭解 Azure 中 SQL Server HADR 功能，請參閱 [可用性群組](availability-group-overview.md) 和 [容錯移轉叢集實例](failover-cluster-instance-overview.md)。 您也可以瞭解設定環境以達到高可用性和嚴重損壞修復的 [最佳作法](hadr-cluster-best-practices.md) 。 



