---
title: '設定可用性群組 (Azure 入口網站) '
description: 使用 Azure 入口網站來建立 Windows 容錯移轉叢集、可用性群組接聽程式，以及 Azure 中 SQL Server VM 上的內部負載平衡器。
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.subservice: hadr
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 08/20/2020
ms.author: mathoma
ms.reviewer: jroth
ms.custom: seo-lt-2019, devx-track-azurecli
ms.openlocfilehash: 14760b4244d42e57aaed7f7d96f487a66147a554
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/12/2020
ms.locfileid: "97359501"
---
# <a name="use-azure-portal-to-configure-an-availability-group-preview-for-sql-server-on-azure-vm"></a>使用 Azure 入口網站為 Azure VM 上的 SQL Server 設定可用性群組 (預覽)  
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

本文說明如何使用 [Azure 入口網站](https://portal.azure.com) 在 Azure vm 上設定 SQL Server 的可用性群組。 

使用 Azure 入口網站建立新的叢集，或將現有的叢集上線，然後建立可用性群組、接聽程式和內部負載平衡器。 

此功能目前為預覽狀態。 

雖然本文使用 Azure 入口網站來設定可用性群組環境，但您也可以使用 [PowerShell 或 Azure CLI](availability-group-az-commandline-configure.md)、 [Azure 快速入門範本](availability-group-quickstart-template-configure.md)或 [手動](availability-group-manually-configure-tutorial.md) 方式來這麼做。 


## <a name="prerequisites"></a>Prerequisites

若要使用 Azure 入口網站設定 Always On 可用性群組，您必須具備下列必要條件： 

- [Azure 訂用帳戶](https://azure.microsoft.com/free/)。
- 具有網域控制站的資源群組。 
- 在 Azure 中執行的一或多個已加入網域的 [vm SQL Server 2016 (或更新版本](./create-sql-vm-portal.md) ，在 *相同* 的可用性設定組或 *不同* 的可用性區域中) Enterprise edition，並 [以完整管理模式向 SQL IaaS 代理程式延伸模組註冊](sql-agent-extension-manually-register-single-vm.md) ，並且在每個 VM 上使用相同的網域帳戶來進行 SQL Server 服務。
- 兩個可用 (未由任何實體使用) 的 IP 位址。 一個用於內部負載平衡器。 另一個用於與可用性群組位於相同子網路內的可用性群組接聽程式。 如果您是使用現有的負載平衡器，可用性群組接聽程式只需要一個可用的 IP 位址。 

## <a name="permissions"></a>權限

您需要下列帳戶許可權，才能使用 Azure 入口網站來設定可用性群組： 

- 在網域中擁有 **建立電腦物件** 權限的現有網域使用者帳戶。 例如，網域系統管理員帳戶通常會有足夠的權限 (例如：account@domain.com)。 _此帳戶也應該屬於建立叢集的每個 VM 上的本機系統管理員群組一部分。_
- 控制 SQL Server 的網域使用者帳戶。 這應該是您想要新增至可用性群組之每個 SQL Server VM 的相同帳戶。 

## <a name="configure-cluster"></a>設定叢集

使用 Azure 入口網站來設定叢集。 您可以建立新的叢集，或者如果您已經有現有的叢集，您可以將其上架到 SQL IaaS 代理程式延伸模組，以便進行入口網站管理。


### <a name="create-a-new-cluster"></a>建立新叢集

如果您已經有叢集，請略過本節，並改為移至現有叢集的上 [架](#onboard-existing-cluster) 。 

如果您還沒有現有的叢集，請使用 Azure 入口網站中的下列步驟建立它：

1. 登入 [Azure 入口網站](https://portal.azure.com)。 
1. 流覽至您的 [SQL 虛擬機器](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.SqlVirtualMachine%2FSqlVirtualMachines) 資源。 
1. 在 [**設定**] 下選取 [**高可用性**]。 
1. 選取 [ **+ 新增 Windows Server 容錯移轉** 叢集] 以開啟 [ **設定 windows 容錯移轉** 叢集] 頁面。  

   :::image type="content" source="media/availability-group-az-portal-configure/create-new-cluster.png" alt-text="在入口網站中選取 + 新增叢集以建立新的叢集":::

1. 命名您的叢集，並提供用來作為雲端見證的儲存體帳戶。 使用現有的儲存體帳戶，或選取 [ **建立新** 的] 以建立新的儲存體帳戶。 儲存體帳戶名稱長度必須介於 3 到 24 個字元，而且只能使用數字和小寫字母。

   :::image type="content" source="media/availability-group-az-portal-configure/configure-new-cluster-1.png" alt-text="提供叢集的名稱、儲存體帳戶和認證":::

1. 展開 [ **Windows Server 容錯移轉叢集認證** ]，以提供 SQL Server 服務帳戶的 [認證](/rest/api/sqlvm/sqlvirtualmachinegroups/createorupdate#wsfcdomainprofile) ，以及叢集操作員和啟動程式帳戶（如果它們與用於 SQL Server 服務的帳戶不同）。 

   :::image type="content" source="media/availability-group-az-portal-configure/configure-new-cluster-2.png" alt-text="提供 SQL 服務帳戶、叢集操作員帳戶和叢集啟動程式帳戶的認證":::

1. 選取您要新增至叢集的 SQL Server Vm。 請注意，是否需要重新開機，並謹慎進行。 只有在「完整管理」模式下以「SQL IaaS 代理程式擴充功能」註冊的 Vm，以及位於與主要 SQL Server VM 相同的位置、網域和相同的虛擬網路上，才會顯示這些 Vm。 
1. 選取 **[** 套用] 以建立叢集。 您可以在 **活動記錄** 中檢查部署的狀態，您可以從上方導覽列中的鐘圖示存取該記錄。 
1. 若要讓 Microsoft 支援容錯移轉叢集，必須通過叢集驗證。 使用您慣用的方法 (（例如遠端桌面通訊協定 (RDP) # A3）連線至 VM，並驗證您的叢集通過驗證後再繼續進行。 若未這麼做，則會讓您的叢集處於不支援的狀態。 您可以使用容錯移轉叢集管理員 (FCM) 或下列 PowerShell 命令來驗證叢集：

    ```powershell
    Test-Cluster –Node ("<node1>","<node2>") –Include "Inventory", "Network", "System Configuration"
    ```
    


### <a name="onboard-existing-cluster"></a>上架現有叢集

如果您已在 SQL Server VM 環境中設定叢集，您可以從 Azure 入口網站將它上架。

若要這樣做，請依照下列步驟執行：

1. 登入 [Azure 入口網站](https://portal.azure.com)。 
1. 流覽至您的 [SQL 虛擬機器](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.SqlVirtualMachine%2FSqlVirtualMachines) 資源。 
1. 在 [**設定**] 下選取 [**高可用性**]。 
1. 選取 [上 **架現有的 Windows Server 容錯移轉** 叢集]，以開啟 [上線 **Windows server 容錯移轉** 叢集] 頁面。 

   :::image type="content" source="media/availability-group-az-portal-configure/onboard-existing-cluster.png" alt-text="從您的 SQL 虛擬機器資源上的高可用性頁面上架現有叢集":::

1. 檢查叢集的設定。 
1. 選取 **[** 套用] 以上線您的叢集，然後在出現提示時選取 **[是]** 。

## <a name="create-availability-group"></a>建立可用性群組

建立或上線您的叢集之後，請使用 Azure 入口網站建立可用性群組。 若要這樣做，請依照下列步驟執行：

1. 登入 [Azure 入口網站](https://portal.azure.com)。 
1. 流覽至您的 [SQL 虛擬機器](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.SqlVirtualMachine%2FSqlVirtualMachines) 資源。 
1. 在 [**設定**] 下選取 [**高可用性**]。 
1. 選取 [ **+ 新增 Always On 可用性群組** ] 以開啟 [ **建立可用性群組** ] 頁面。

   :::image type="content" source="media/availability-group-az-portal-configure/create-new-availability-group.png" alt-text="選取 [新的 always on 可用性群組] 以開啟 [建立可用性群組] 頁面。":::

1. 輸入可用性群組的名稱。 
1. 選取 [設定接聽程式 **]** 以開啟 [ **設定可用性群組** 接聽程式] 頁面。 

   :::image type="content" source="media/availability-group-az-portal-configure/create-availability-group.png" alt-text="提供可用性群組的名稱，並設定接聽程式":::

1. 填妥值，並使用現有的負載平衡器，或選取 [ **建立新** 的] 以建立新的負載平衡器。  選取 **[** 套用] 以儲存您的設定，並建立接聽程式和負載平衡器。 

   :::image type="content" source="media/availability-group-az-portal-configure/configure-new-listener.png" alt-text="填寫表單中的值，以建立新的接聽程式和負載平衡器":::

1. 選擇 [ **+ 選取複本** ] 以開啟 [ **設定可用性群組複本** ] 頁面。
1. 選取您要新增至可用性群組的虛擬機器，然後選擇最符合您業務需求的可用性群組設定。 選取 [套用] 以儲存 **您的設定** 。 

   :::image type="content" source="media/availability-group-az-portal-configure/add-replicas.png" alt-text="選擇要新增至可用性群組的 Vm，並設定適用于您企業的設定":::

1. 確認您的可用性群組設定，然後 **選取 [** 套用] 以建立您的可用性群組。 

您可以在 **活動記錄** 中檢查部署的狀態，您可以從上方導覽列中的鐘圖示存取該記錄。 

  > [!NOTE]
  > 在您將資料庫加入至可用性 **群組之前，** Azure 入口網站的 [**高可用性**] 頁面上的同步處理 **健全狀況** 會顯示為 [狀況不良]。 


## <a name="add-database-to-availability-group"></a>將資料庫加入至可用性群組

部署完成之後，將您的資料庫新增至可用性群組。 下列步驟會使用 SQL Server Management Studio (SSMS) 但您也可以使用 [transact-sql 或 PowerShell](/sql/database-engine/availability-groups/windows/availability-group-add-a-database) 。 

若要使用 SQL Server Management Studio 將資料庫新增至可用性群組，請遵循下列步驟：

1. 使用您慣用的方法（例如遠端桌面連線 (RDP) ）連接到其中一個 SQL Server Vm。 
1. 開啟 SQL Server Management Studio (SSMS)。
1. 連接到您的 SQL Server 實例。 
1. 在 **物件總管** 中展開 **Always On 高可用性**。
1. 展開 [ **可用性群組**]，以滑鼠右鍵按一下您的可用性群組，然後選擇 [ **新增資料庫 ...**]。

   :::image type="content" source="media/availability-group-az-portal-configure/add-database.png" alt-text="在 [物件瀏覽器] 中，以滑鼠右鍵按一下可用性群組，然後選擇 [新增資料庫]。":::

1. 遵循提示來選取您要新增至可用性群組) 的資料庫 (。 
1. 選取 **[確定]** 以儲存您的設定，並將您的資料庫新增至可用性群組。 
1. 新增資料庫之後，請重新整理 **物件總管** ，以確認資料庫的狀態為 `synchronized` 。 

新增資料庫之後，您可以在 Azure 入口網站中檢查可用性群組的狀態： 

:::image type="content" source="media/availability-group-az-portal-configure/healthy-availability-group.png" alt-text="在同步處理資料庫之後，從 Azure 入口網站的高可用性頁面檢查可用性群組的狀態":::

## <a name="add-more-vms"></a>新增更多 Vm

若要將更多 SQL Server Vm 新增至叢集，請遵循下列步驟： 

1. 登入 [Azure 入口網站](https://portal.azure.com)。 
1. 流覽至您的 [SQL 虛擬機器](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.SqlVirtualMachine%2FSqlVirtualMachines) 資源。 
1. 在 [**設定**] 下選取 [**高可用性**]。 
1. 選取 [ **設定 Windows Server 容錯移轉** 叢集] 以開啟 [ **設定 Windows server 容錯移轉** 叢集] 頁面。 

   :::image type="content" source="media/availability-group-az-portal-configure/configure-existing-cluster.png" alt-text="選取 [設定 Windows Server 容錯移轉叢集]，將 Vm 新增至您的叢集中。":::

1. 展開 [ **Windows Server 容錯移轉叢集認證** ]，然後輸入用於 SQL Server 服務、叢集操作員和叢集啟動程式帳戶的帳戶。 
1. 選取您要新增至叢集的 SQL Server Vm。 
1. 選取 [套用]。 

您可以在 **活動記錄** 中檢查部署的狀態，您可以從上方導覽列中的鐘圖示存取該記錄。 


## <a name="modify-availability-group"></a>修改可用性群組 


您可以在可用性群組旁邊選取省略號 ( ... ) ，**將更多複本加入** 至可用性群組、**設定** 接聽程式，或從 Azure 入口網站的 [**高可用性**] 頁面中 **刪除** 接聽程式： 

:::image type="content" source="media/availability-group-az-portal-configure/configure-listener.png" alt-text="選取可用性群組旁邊的省略號，然後選取 [加入複本]，將更多複本加入至可用性群組。":::

## <a name="remove-cluster"></a>移除叢集

移除叢集中的所有 SQL Server Vm 以終結它，然後從 SQL IaaS 代理程式擴充功能移除叢集中繼資料。 您可以使用最新版的 [Azure CLI](/cli/azure/install-azure-cli) 或 PowerShell 來這麼做。 

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

首先，從叢集中移除所有 SQL Server Vm。 這會從叢集中移除節點，並摧毀叢集：  

```azurecli-interactive
# Remove the VM from the cluster metadata
# example: az sql vm remove-from-group --name SQLVM2 --resource-group SQLVM-RG

az sql vm remove-from-group --name <VM1 name>  --resource-group <resource group name>
az sql vm remove-from-group --name <VM2 name>  --resource-group <resource group name>
```

如果這些是叢集中的唯一 Vm，則叢集將會損毀。 如果叢集中有任何其他 Vm 與已移除的 SQL Server Vm 分開，將不會移除其他 Vm，且不會終結叢集。 

接下來，從 SQL IaaS 代理程式擴充功能中移除叢集中繼資料： 

```azurecli-interactive
# Remove the cluster from the SQL VM RP metadata
# example: az sql vm group delete --name Cluster --resource-group SQLVM-RG

az sql vm group delete --name <cluster name> --resource-group <resource group name>
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

首先，從叢集中移除所有 SQL Server Vm。 這會從叢集中移除節點，並摧毀叢集： 

```powershell-interactive
# Remove the SQL VM from the cluster
# example: $sqlvm = Get-AzSqlVM -Name SQLVM3 -ResourceGroupName SQLVM-RG
#  $sqlvm. SqlVirtualMachineGroup = ""
#  Update-AzSqlVM -ResourceId $sqlvm -SqlVM $sqlvm

$sqlvm = Get-AzSqlVM -Name <VM Name> -ResourceGroupName <Resource Group Name>
   $sqlvm. SqlVirtualMachineGroup = ""
   
   Update-AzSqlVM -ResourceId $sqlvm -SqlVM $sqlvm
```

如果這些是叢集中的唯一 Vm，則叢集將會損毀。 如果叢集中有任何其他 Vm 與已移除的 SQL Server Vm 分開，將不會移除其他 Vm，且不會終結叢集。 


接下來，從 SQL IaaS 代理程式擴充功能中移除叢集中繼資料： 

```powershell-interactive
# Remove the cluster metadata
# example: Remove-AzSqlVMGroup -ResourceGroupName "SQLVM-RG" -Name "Cluster"

Remove-AzSqlVMGroup -ResourceGroupName "<resource group name>" -Name "<cluster name>"
```

---

## <a name="troubleshooting"></a>疑難排解

如果您遇到問題，您可以檢查部署歷程記錄，並檢查常見的錯誤及其解決方法。 

### <a name="check-deployment-history"></a>檢查部署歷程記錄

透過入口網站對叢集和可用性群組所做的變更是透過部署完成。 如果建立或上線叢集或建立可用性群組有問題，部署歷程記錄可以提供更詳細的資料。

若要查看部署的記錄，並檢查部署歷程記錄，請遵循下列步驟：

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 流覽至您的資源群組。
1. 選取 [設定] 底下的 [部署]。
1. 選取感興趣的部署，以深入瞭解部署。 


   :::image type="content" source="media/availability-group-az-portal-configure/failed-deployment.png" alt-text="選取您想要深入瞭解的部署。" :::

### <a name="common-errors"></a>常見錯誤

請參閱下列常見的錯誤及其解決方法。 

#### <a name="the-account-which-is-used-to-start-up-sql-service-is-not-a-domain-account"></a>用來啟動 sql 服務的帳戶不是網域帳戶

這表示資源提供者無法使用所提供的認證存取 SQL Server 服務。 一些常見的解決方式：
- 確定您的網域控制站正在執行。
- 驗證入口網站中提供的認證符合 SQL Server 服務的認證。 


## <a name="next-steps"></a>後續步驟


如需可用性群組的詳細資訊，請參閱：

- [可用性群組概觀](/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server)
* [可用性群組的管理](/sql/database-engine/availability-groups/windows/administration-of-an-availability-group-sql-server)   
* [監視可用性群組 &#40;SQL Server&#41;](/sql/database-engine/availability-groups/windows/monitoring-of-availability-groups-sql-server)
* [Availability group Transact-sql 語句 ](/sql/database-engine/availability-groups/windows/transact-sql-statements-for-always-on-availability-groups)   
* [可用性群組 PowerShell 命令](/sql/database-engine/availability-groups/windows/overview-of-powershell-cmdlets-for-always-on-availability-groups-sql-server)  

如需 SQL Server Vm 的詳細資訊，請參閱： 

* [SQL Server VM 概觀](sql-server-on-azure-vm-iaas-what-is-overview.md)
* [SQL Server VM 版本資訊](../../database/doc-changes-updates-release-notes.md)
* [SQL Server VM 常見問題集](frequently-asked-questions-faq.md)
