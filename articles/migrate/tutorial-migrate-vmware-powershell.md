---
title: 將 VMware VM 遷移至 Azure (無代理程式) - PowerShell
description: 了解如何使用 Azure Migrate 透過 PowerShell 執行 VMware VM 的無代理程式移轉。
author: rahulg1190
ms.author: rahugup
manager: bsiva
ms.topic: tutorial
ms.date: 10/1/2020
ms.openlocfilehash: ce712736c25c0757f5b654e4442a4f08acdf15e2
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/10/2020
ms.locfileid: "97029782"
---
# <a name="migrate-vmware-vms-to-azure-agentless---powershell"></a>將 VMware VM 遷移至 Azure (無代理程式) - PowerShell

在本文中，您將了解如何使用 Azure PowerShell，透過無代理程式方法遷移探索到的 VMware VM ([[Azure Migrate：伺服器移轉]](migrate-services-overview.md#azure-migrate-server-migration-tool) 中)。 

您會了解如何：

> [!div class="checklist"]
> * 在 Azure Migrate 專案中擷取探索到的 VMware VM。
> * 開始複寫 VM。
> * 更新複寫 VM 的屬性。
> * 監視複寫。
> * 執行測試移轉，確定一切都沒問題。
> * 執行完整的 VM 移轉。

> [!NOTE]
> 教學課程將會針對案例示範最簡單的部署路徑，讓您可以快速設定概念證明。 教學課程在情況允許時都會使用預設選項，且不會顯示所有可能的設定與路徑。

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="prerequisites"></a>Prerequisites

開始進行本教學課程之前，您必須：

1. [完成探索教學課程](tutorial-discover-vmware.md)，準備 Azure 和 VMware 以進行移轉。
2. 建議您先完成第二個教學課程以[評估 VMware VM](./tutorial-assess-vmware-azure-vm.md)，再將其遷移至 Azure。
3. 您有 Azure PowerShell `Az` 模組。 如果您需要安裝或升級 Azure PowerShell，請依照此[安裝和設定 Azure PowerShell 的指南](/powershell/azure/install-az-ps)操作

## <a name="install-azure-migrate-powershell-module"></a>安裝 Azure Migrate PowerShell 模組

Azure Migrate PowerShell 模組可供預覽。 您必須使用下列命令來安裝 PowerShell 模組。 

```azurepowershell
Install-Module -Name Az.Migrate 
```

## <a name="sign-in-to-your-microsoft-azure-subscription"></a>登入您的 Microsoft Azure 訂用帳戶

使用 `Connect-AzAccount` Cmdlet 登入您的 Azure 訂用帳戶。

```azurepowershell
Connect-AzAccount
```

選取 Azure 訂用帳戶。 您可以使用 `Get-AzSubscription` Cmdlet 取得您有權存取的 Azure 訂用帳戶清單。 使用 `Set-AzContext` Cmdlet，選取具有您 Azure Migrate 專案的 Azure 訂用帳戶。

```azurepowershell
Set-AzContext -SubscriptionId "xxxx-xxxx-xxxx-xxxx"
```

## <a name="retrieve-the-azure-migrate-project"></a>擷取 Azure Migrate 專案

Azure Migrate 專案可用來儲存從您評估或遷移的環境中收集到的探索、評量和移轉中繼資料。
在專案中，您可以追蹤探索到的資產、協調評量及執行移轉。

作為必要條件的一部分，您已建立 Azure Migrate 專案。 請使用 `Get-AzMigrateProject` Cmdlet 擷取 Azure Migrate 專案的詳細資料。 您必須指定 Azure Migrate 專案的名稱 (`Name`)，以及 Azure Migrate 專案的資源群組名稱 (`ResourceGroupName`)。

```azurepowershell
# Get resource group of the Azure Migrate project
$ResourceGroup = Get-AzResourceGroup -Name "MyResourceGroup"

# Get details of the Azure Migrate project
$MigrateProject = Get-AzMigrateProject -Name "MyMigrateProject" -ResourceGroupName $ResourceGroup.ResourceGroupName

# View Azure Migrate project details
$MigrateProject | ConvertTo-JSON
```

## <a name="retrieve-discovered-vms-in-an-azure-migrate-project"></a>在 Azure Migrate 專案中擷取探索到的 VM

Azure Migrate 會使用輕量型 [Azure Migrate 設備](migrate-appliance-architecture.md)。 作為必要條件的一部分，您已將 Azure Migrate 設備部署為 VMware VM。

若要在 Azure Migrate 專案中擷取特定的 VMware VM，請指定 Azure Migrate 專案的名稱 (`ProjectName`)、Azure Migrate 專案的資源群組 (`ResourceGroupName`)，以及 VM 名稱 (`DisplayName`)。 

> [!NOTE]
> **VM 名稱 (`DisplayName`) 參數值區分大小寫**。

```azurepowershell
# Get a specific VMware VM in an Azure Migrate project
$DiscoveredServer = Get-AzMigrateDiscoveredServer -ProjectName $MigrateProject.Name -ResourceGroupName $ResourceGroup.ResourceGroupName -DisplayName "MyTestVM"

# View discovered server details
$DiscoveredServer | ConvertTo-JSON
```
我們會在本教學課程中遷移此 VM。

您也可以使用 `ProjectName` 和 `ResourceGroupName` 參數，擷取 Azure Migrate 專案中的所有 VMware VM。

```azurepowershell
# Get all VMware VMs in an Azure Migrate project
$DiscoveredServers = Get-AzMigrateDiscoveredServer -ProjectName $MigrateProject.Name -ResourceGroupName $ResourceGroup.ResourceGroupName 
```
如果 Azure Migrate 專案中有多個設備，您可以使用 `ProjectName`、`ResourceGroupName` 和 `ApplianceName` 參數擷取使用特定 Azure Migrate 設備探索到的所有 VM。 

```azurepowershell
# Get all VMware VMs discovered by an Azure Migrate Appliance in an Azure Migrate project
$DiscoveredServers = Get-AzMigrateDiscoveredServer -ProjectName $MigrateProject.Name -ResourceGroupName $ResourceGroup.ResourceGroupName -ApplianceName "MyMigrateAppliance" |Format-Table DisplayName, Name, Type

```

## <a name="initialize-replication-infrastructure"></a>初始化複寫基礎結構

[Azure Migrate：伺服器移轉](migrate-services-overview.md#azure-migrate-server-migration-tool)會利用多項 Azure 資源來遷移 VM。 「伺服器移轉」會在與專案相同的資源群組中佈建下列資源。

- **服務匯流排**：伺服器移轉會使用服務匯流排將複寫協調訊息傳送至設備。
- **閘道儲存體帳戶**：伺服器移轉會使用閘道儲存體帳戶來儲存所複寫 VM 的相關狀態資訊。
- **記錄儲存體帳戶**︰Azure Migrate 設備會將 VM 的複寫記錄上傳至記錄儲存體帳戶。 Azure Migrate 會將複寫資訊套用到複本受控磁碟。
- **金鑰保存庫**：Azure Migrate 設備會使用金鑰保存庫來管理服務匯流排的連接字串，以及複寫中所用儲存體帳戶的存取金鑰。

在複寫 Azure Migrate 專案中的第一個 VM 之前，請執行下列指令碼以佈建複寫基礎結構。 此指令碼會佈建和設定上述資源，讓您可以開始遷移 VMware VM。

> [!NOTE]
> 一個 Azure Migrate 專案僅支援遷移至一個 Azure 區域。 執行此指令碼之後，您將無法變更 VMware VM 要遷移的目標區域。
> 如果您在 Azure Migrate 專案中設定新的設備，則必須執行 `Initialize-AzMigrateReplicationInfrastructure` 指令碼。 

在本文中，我們將初始化複寫基礎結構，以便將 VM 遷移至 `Central US` 區域。 您可以從 GitHub 存放庫[下載檔案](https://github.com/Azure/azure-docs-powershell-samples/tree/master/azure-migrate/migrate-at-scale-vmware-agentles)，或使用下列程式碼片段加以執行。 

```azurepowershell
# Download the script from Azure Migrate GitHub repository 
Invoke-WebRequest https://raw.githubusercontent.com/Azure/azure-docs-powershell-samples/master/azure-migrate/migrate-at-scale-vmware-agentles/Initialize-AzMigrateReplicationInfrastructure.ps1 -OutFile .\AzMigrateReplicationinfrastructure.ps1

# Run the script for initializing replication infrastructure for the current Migrate project
.\AzMigrateReplicationInfrastructure.ps1 -ResourceGroupName $ResourceGroup.ResourceGroupName -ProjectName $MigrateProject.Name -Scenario agentlessVMware -TargetRegion "CentralUS" 
```


## <a name="replicate-vms"></a>複寫 VM

完成探索並初始化複寫基礎結構之後，您就可以開始將 VMware VM 複寫至 Azure。 您可以同時執行多達 300 個複寫作業。

您可以指定複寫屬性，如下所示。

- **目標訂用帳戶和資源群組** - 使用 `TargetResourceGroupId` 參數提供資源群組識別碼，以指定 VM 應遷移到的訂用帳戶和資源群組。 
- **目標虛擬網路和子網路** - 使用 `TargetNetworkId` 和 `TargetSubnetName` 參數，分別指定 VM 應遷移到的 Azure 虛擬網路的識別碼和子網路名稱。 
- **目標 VM 名稱** - 使用 `TargetVMName` 參數，指定要建立的 Azure VM 名稱。
- **目標 VM 大小** - 使用 `TargetVMSize` 參數，指定要用於複寫 VM 的 Azure VM 大小。 例如，若要將 VM 遷移至 Azure 中的 D2_v2 VM，請將 `TargetVMSize` 的值指定為 "Standard_D2_v2"。  
- **授權** - 若要將 Azure Hybrid Benefit 用於有效的軟體保證或 Windows Server 訂用帳戶所涵蓋的 Windows Server 機器，請將 `LicenseType` 參數的值指定為 "WindowsServer"。 否則，請將 `LicenseType` 參數的值指定為 "NoLicenseType"。
- **OS 磁碟** - 指定具有作業系統開機載入器和安裝程式之磁碟的唯一識別碼。 要使用的磁碟識別碼是使用 `Get-AzMigrateServer` Cmdlet 擷取之磁碟的唯一識別碼 (UUID) 屬性。
- **磁碟類型** - 指定 `DiskType` 參數的值，如下所示。
    - 若要使用進階受控磁碟，請指定 "Premium_LRS" 作為 `DiskType` 參數的值。 
    - 若要使用標準 SSD 磁碟，請指定 "StandardSSD_LRS" 作為 `DiskType` 參數的值。 
    - 若要使用標準 HDD 磁碟，請指定 "Standard_LRS" 作為 `DiskType` 參數的值。 
- **基礎結構重複** - 指定基礎結構備援性選項，如下所示。 
    - 可用性區域，將已遷移的機器釘選到該區域中特定的可用性區域。 使用此選項可將形成多節點應用程式層的伺服器散發到可用性區域。 只有選取要移轉的目標區域支援可用性區域時，才可以使用此選項。 若要使用可用性區域，請指定 `TargetAvailabilityZone` 參數的可用性區域值。
    - 可用性設定組，可將遷移的電腦放在可用性設定組中。 選取的目標資源群組必須有一或多個可用性設定組，才能使用此選項。 若要使用可用性設定組，請指定 `TargetAvailabilitySet` 參數的可用性設定組識別碼。 

### <a name="replicate-vms-with-all-disks"></a>使用所有磁碟複寫 VM
在本教學課程中，我們將複寫探索到的 VM 的所有磁碟，並為 Azure 中的 VM 指定新的名稱。 我們會將探索到的伺服器的第一個磁碟指定為 OS 磁碟，並以標準 HDD 的形式遷移所有磁碟。 OS 磁碟是具有作業系統開機載入器和安裝程式的磁碟。 此 Cmdlet 會傳回作業，此作業可追蹤以監視作業狀態。 

```azurepowershell
# Retrieve the resource group that you want to migrate to
$TargetResourceGroup = Get-AzResourceGroup -Name "MyTargetResourceGroup"

# Retrieve the Azure virtual network and subnet that you want to migrate to
$TargetVirtualNetwork = Get-AzVirtualNetwork -Name "MyVirtualNetwork"

# Start replication for a discovered VM in an Azure Migrate project
$MigrateJob =  New-AzMigrateServerReplication -InputObject $DiscoveredServer -TargetResourceGroupId $TargetResourceGroup.ResourceId -TargetNetworkId $TargetVirtualNetwork.Id -LicenseType "NoLicenseType" -OSDiskID $DiscoveredServer.Disk[0].Uuid -TargetSubnetName $TargetVirtualNetwork.Subnets[0].Name -DiskType "Standard_LRS" -TargetVMName "MyMigratedTestVM" -TargetVMSize "Standard_DS2_v2"

# Track job status to check for completion
while (($MigrateJob.State -eq "InProgress") -or ($MigrateJob.State -eq "NotStarted")){
        #If the job hasn't completed, sleep for 10 seconds before checking the job status again
        sleep 10;
        $MigrateJob = Get-AzMigrateJob -InputObject $MigrateJob
}
#Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded"
Write-Output $MigrateJob.State
```

### <a name="replicate-vms-with-select-disks"></a>使用選取的磁碟複寫 VM
您也可以使用 `New-AzMigrateDiskMapping` Cmdlet，並將其提供作為 `New-AzMigrateServerReplication` Cmdlet 中 `DiskToInclude` 參數的輸入，以選擇性地複寫探索到的 VM 的磁碟。 您也可以使用 `New-AzMigrateDiskMapping` Cmdlet，為每個要複寫的個別磁碟指定不同的目標磁碟類型。 

指定 `New-AzMigrateDiskMapping` Cmdlet 的下列參數值。

- **DiskId** - 為要遷移的磁碟指定唯一識別碼。 要使用的磁碟識別碼是使用 `Get-AzMigrateServer` Cmdlet 擷取之磁碟的唯一識別碼 (UUID) 屬性。  
- **IsOSDisk** - 如果要遷移的磁碟是 VM 的 OS 磁碟，請指定 "true"，否則指定 "false"。
- **DiskType** - 指定要在 Azure 中使用的磁碟類型。 

在下列範例中，我們只會為探索到的 VM 複寫其兩個磁碟。 我們將指定 OS 磁碟，並且為每個要複寫的磁碟使用不同的磁碟類型。 此 Cmdlet 會傳回作業，此作業可追蹤以監視作業狀態。 

```azurepowershell
# View disk details of the discovered server
$DiscoveredServer.Disk | ConvertTo-JSON

# Create a new disk mapping for the disks to be replicated
$DisksToReplicate = @()
$OSDisk = New-AzMigrateDiskMapping -DiskID $DiscoveredServer.Disk[0].Uuid -DiskType "StandardSSD_LRS" -IsOSDisk "true"
$DataDisk = New-AzMigrateDiskMapping -DiskID $DiscoveredServer.Disk[1].Uuid -DiskType "Premium_LRS" -IsOSDisk "false"

$DisksToReplicate += $OSDisk
$DisksToReplicate += $DataDisk 

# Retrieve the resource group that you want to migrate to
$TargetResourceGroup = Get-AzResourceGroup -Name "MyTargetResourceGroup"

# Retrieve the Azure virtual network and subnet that you want to migrate to
$TargetVirtualNetwork = Get-AzVirtualNetwork -Name "MyVirtualNetwork"

# Start replication for the VM
$MigrateJob =  New-AzMigrateServerReplication -InputObject $DiscoveredServer -TargetResourceGroupId $TargetResourceGroup.ResourceId -TargetNetworkId $TargetVirtualNetwork.Id -LicenseType "NoLicenseType" -DiskToInclude $DisksToReplicate -TargetSubnetName $TargetVirtualNetwork.Subnets[0].Name -TargetVMName "MyMigratedTestVM" -TargetVMSize "Standard_DS2_v2"

# Track job status to check for completion
while (($MigrateJob.State -eq "InProgress") -or ($MigrateJob.State -eq "NotStarted")){
        #If the job hasn't completed, sleep for 10 seconds before checking the job status again
        sleep 10;
        $MigrateJob = Get-AzMigrateJob -InputObject $MigrateJob
}
#Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded"
Write-Output $MigrateJob.State
```

## <a name="monitor-replication"></a>監視複寫 

複寫會如下所示進行︰

- 當「啟動複寫」作業順利完成後，機器就會開始進行對 Azure 的初始複寫。
- 在初始複寫期間，系統會建立 VM 快照集。 快照集內的磁碟資料會複寫至 Azure 中的複本受控磁碟。
- 初始複寫完成後，就會開始進行差異複寫。 對內部部署磁碟的累加變更會定期複寫至 Azure 中的複本磁碟。

使用 `Get-AzMigrateServerReplication` Cmdlet 追蹤複寫的狀態。 

> [!NOTE]
> 探索到的 VM 識別碼和複寫 VM 識別碼是兩個不同的唯一識別碼。 這兩個識別碼都可以用來擷取複寫伺服器的詳細資料。  

### <a name="monitor-replication-using-discovered-vm-identifier"></a>使用探索到的 VM 識別碼來監視複寫
```azurepowershell
# Retrieve the replicating VM details by using the discovered VM identifier
$ReplicatingServer = Get-AzMigrateServerReplication -DiscoveredMachineId $DiscoveredServer.ID
```

### <a name="monitor-replication-using-replicating-vm-identifier"></a>使用複寫 VM 識別碼來監視複寫

```azurepowershell
# List all replicating VMs in an Azure Migrate project and filter the result for selecting the replication VM. This cmdlet will not return all properties of the replicating VM.
$ReplicatingServer = Get-AzMigrateServerReplication -ProjectName $MigrateProject.Name -ResourceGroupName $ResourceGroup.ResourceGroupName | where MachineName -eq $DiscoveredServer.DisplayName

# Retrieve replicating VM details using replicating VM identifier
$ReplicatingServer = Get-AzMigrateServerReplication -TargetObjectID $ReplicatingServer.Id 
```

您可以在輸出中追蹤 [移轉狀態] 和 [移轉狀態描述] 屬性。 
- 進行初始複寫時，[移轉狀態] 和 [移轉狀態描述] 屬性的值分別會是 [InitialSeedingInProgress] 和 [初始複寫]。 
- 進行差異複寫期間，[遷移狀態] 和 [移轉狀態描述] 屬性的值分別會是 [複寫中] 和 [已準備好要遷移]。
- 完成遷移之後，[遷移狀態] 和 [移轉狀態描述] 屬性的值分別會是 [移轉成功] 和 [已遷移]。

```Output
AllowedOperation            : {DisableMigration, TestMigrate, Migrate}
CurrentJobId                : /Subscriptions/xxx/resourceGroups/xxx/providers/Micr
                              osoft.RecoveryServices/vaults/xxx/replicationJobs/None
CurrentJobName              : None
CurrentJobStartTime         : 1/1/1753 1:01:01 AM
EventCorrelationId          : 9d435c55-4660-41a5-a8ed-dd74213d85fa
Health                      : Normal
HealthError                 : {}
Id                          : /Subscriptions/xxx/resourceGroups/xxx/providers/Micr
                              osoft.RecoveryServices/vaults/xxx/replicationFabrics/xxx/replicationProtectionContainers/xxx/
                              replicationMigrationItems/10-150-8-52-b090bef3-b733-5e34-bc8f-eb6f2701432a_5009e941-3e40-
                              39b2-1e14-f90584522703
LastTestMigrationStatus     :
LastTestMigrationTime       :
Location                    :
MachineName                 : MyTestVM
MigrationState              : InitialSeedingInProgress
MigrationStateDescription   : Initial replication
Name                        : 10-150-8-52-b090bef3-b733-5e34-bc8f-eb6f2701432a_5009e941-3e40-39b2-1e14-f90584522703
PolicyFriendlyName          : xxx
PolicyId                    : /Subscriptions/xxx/resourceGroups/xxx/providers/Micr
                              osoft.RecoveryServices/vaults/xxx/replicationPolicies/xxx
ProviderSpecificDetail      : Microsoft.Azure.PowerShell.Cmdlets.Migrate.Models.Api20180110.VMwareCbtMigrationDetails
TestMigrateState            : None
TestMigrateStateDescription : None
Type                        : Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems
```

如需複寫進度的詳細資訊，請執行下列 Cmdlet。

```azurepowershell
$replicatingserver.ProviderSpecificDetail | convertto-json
```
您可以使用輸出中的 [初始植入進度百分比] 屬性來追蹤初始複寫進度。

```output
    "DataMoverRunAsAccountId": "/subscriptions/xxx/resourceGroups/xxx/providers/Microsoft.OffAzure/VMwareSites/xxx/runasaccounts/xxx",
    "FirmwareType":  "BIOS",
    "InitialSeedingProgressPercentage": 20,
    "InstanceType":  "VMwareCbt",
    "LastRecoveryPointReceived":  "\/Date(1601733591427)\/",
    "LicenseType":  "NoLicenseType",
    "MigrationProgressPercentage":  null,
    "MigrationRecoveryPointId":  null,
    "OSType":  "Windows",
    "PerformAutoResync":  "true",
```

複寫會如下所示進行︰

- 當「啟動複寫」作業順利完成後，機器就會開始進行對 Azure 的初始複寫。
- 在初始複寫期間，系統會建立 VM 快照集。 快照集內的磁碟資料會複寫至 Azure 中的複本受控磁碟。
- 初始複寫完成後，就會開始進行差異複寫。 對內部部署磁碟的累加變更會定期複寫至 Azure 中的複本磁碟。

## <a name="retrieve-the-status-of-a-job"></a>取出作業的狀態

您可以使用 `Get-AzMigrateJob` Cmdlet 來監視作業的狀態。 

```azurepowershell
# Retrieve the updated status for a job
$job = Get-AzMigrateJob -InputObject $job
```

## <a name="update-properties-of-a-replicating-vm"></a>更新複寫 VM 的屬性

[Azure Migrate：伺服器移轉](migrate-services-overview.md#azure-migrate-server-migration-tool)可讓您變更複寫 VM 的目標屬性，例如名稱、大小、資源群組、NIC 設定等等。 此 Cmdlet 會傳回作業，此作業可追蹤以監視作業狀態。 

```azurepowershell
# Retrieve the replicating VM details by using the discovered VM identifier
$ReplicatingServer = Get-AzMigrateServerReplication -DiscoveredMachineId $DiscoveredServer.ID

# View NIC details of the replicating server
Write-Output $ReplicatingServer.ProviderSpecificDetail.VMNic 
```
您可以更新 VM 的下列屬性。

- **VM 名稱** - 使用 `TargetVMName` 參數，指定要建立的 Azure VM 名稱。
- **VM 大小** - 使用 `TargetVMSize` 參數，指定要用於複寫 VM 的 Azure VM 大小。 例如，若要將 VM 遷移至 Azure 中的 D2_v2 VM，請將 `TargetVMSize` 的值指定為 "Standard_D2_v2"。  
- **虛擬網路** - 使用 `TargetNetworkId` 參數，指定 VM 應遷移到的 Azure 虛擬網路的識別碼。 
- **資源群組** - 使用 `TargetResourceGroupId` 參數提供資源群組識別碼，以指定 VM 應遷移到的資源群組識別碼。
- **網路介面** - 可以使用 `New-AzMigrateNicMapping` Cmdlet 指定 NIC 設定。 接著，物件會傳入 `Set-AzMigrateServerReplication` Cmdlet 中作為 `NicToUpdate` 參數的輸入。 

    - **變更 IP 配置** - 若要指定 NIC 的靜態 IP，請使用 `TargetNicIP` 參數提供 IPv4 位址，以作為 VM 的靜態 IP。 若要動態指派 NIC 的 IP，請提供「自動」作為 `TargetNicIP` 參數的值。
    - 對 `TargetNicSelectionType` 參數使用 "Primary"、"Secondary" 或 "DoNotCreate" 值，以指定 NIC 應為主要 NIC、次要 NIC，還是不會在已遷移的 VM 上建立。 您只能將一個 NIC 指定為 VM 的主要 NIC。 
    - 若要將某個 NIC 設為主要，您也必須指定其他應設為次要的 NIC，或不會在已遷移的 VM 上建立的 NIC。  
    - 若要變更 NIC 的子網路，請使用 `TargetNicSubnet` 參數指定子網路的名稱。

 - **可用性區域** - 若要使用可用性區域，請指定 `TargetAvailabilityZone` 參數的可用性區域值。
 - **可用性設定組** - 若要使用可用性設定組，請指定 `TargetAvailabilitySet` 參數的可用性設定組識別碼。

在下列範例中，我們會將第一個 NIC 設為主要，並為其指派靜態 IP，以更新 NIC 設定。 我們會捨棄第二個 NIC 而不加以遷移，並更新目標 VM 名稱和大小。 

```azurepowershell
# Specify the NIC properties to be updated for a replicating VM. 
$NicMapping = @()
$NicMapping1 = New-AzMigrateNicMapping -NicId $ReplicatingServer.ProviderSpecificDetail.VMNic[0].NicId -TargetNicIP "xxx.xxx.xxx.xxx" -TargetNicSelectionType "Primary"
$NicMapping2 = New-AzMigrateNicMapping -NicId $ReplicatingServer.ProviderSpecificDetail.VMNic[1].NicId -TargetNicSelectionType "DoNotCreate"

$NicMapping += $NicMapping1
$NicMapping += $NicMapping2

# Update the name, size and NIC configuration of a replicating server
$UpdateJob = Set-AzMigrateServerReplication -InputObject $ReplicatingServer -TargetVMSize "Standard_DS13_v2" -TargetVMName "MyMigratedVM" -NicToUpdate $NicMapping

# Track job status to check for completion
while (($UpdateJob.State -eq "InProgress") -or ($UpdateJob.State -eq "NotStarted")){
        #If the job hasn't completed, sleep for 10 seconds before checking the job status again
        sleep 10;
        $UpdateJob = Get-AzMigrateJob -InputObject $UpdateJob
}
#Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded"
Write-Output $UpdateJob.State
```

您也可以列出 Azure Migrate 專案中的所有複寫伺服器，然後使用複寫 VM 識別碼來更新 VM 屬性。

```azurepowershell
# List all replicating VMs in an Azure Migrate project and filter the result for selecting the replication VM. This cmdlet will not return all properties of the replicating VM.
$ReplicatingServer = Get-AzMigrateServerReplication -ProjectName $MigrateProject.Name -ResourceGroupName $ResourceGroup.ResourceGroupName | where MachineName -eq $DiscoveredServer.DisplayName

# Retrieve replicating VM details using replicating VM identifier
$ReplicatingServer = Get-AzMigrateServerReplication -TargetObjectID $ReplicatingServer.Id 
```


## <a name="run-a-test-migration"></a>執行測試移轉

在差異複寫開始後，您可以在執行對 Azure 的完整移轉之前，為 VM 執行測試移轉。 強烈建議您對每個機器都至少執行一次測試移轉，然後再遷移機器。 此 Cmdlet 會傳回作業，此作業可追蹤以監視作業狀態。 

- 執行測試移轉會檢查移轉是否如預期運作。 測試移轉不會影響內部部署機器，這類機器仍可運作，且會繼續進行複寫。 
- 測試移轉會使用複寫的資料建立 Azure VM，來模擬移轉 (通常會移轉至 Azure 訂用帳戶中的非生產 VNet)。
- 您可以使用複寫的測試 Azure VM 來驗證移轉、執行應用程式測試以及解決任何問題，然後再進行完整移轉。

使用 `TestNetworkID` 參數指定虛擬網路的識別碼，以選取要用於測試的 Azure 虛擬網路。

```azurepowershell
# Retrieve the Azure virtual network created for testing 
$TestVirtualNetwork = Get-AzVirtualNetwork -Name MyTestVirtualNetwork

# Start test migration for a replicating server
$TestMigrationJob = Start-AzMigrateTestMigration -InputObject $ReplicatingServer -TestNetworkID $TestVirtualNetwork.Id

# Track job status to check for completion
while (($TestMigrationJob.State -eq "InProgress") -or ($TestMigrationJob.State -eq "NotStarted")){
        #If the job hasn't completed, sleep for 10 seconds before checking the job status again
        sleep 10;
        $TestMigrationJob = Get-AzMigrateJob -InputObject $TestMigrationJob
}
#Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded"
Write-Output $TestMigrationJob.State
```

測試完成後，請使用 `Start-AzMigrateTestMigrationCleanup` Cmdlet 清除測試移轉。 此 Cmdlet 會傳回作業，此作業可追蹤以監視作業狀態。 

```azurepowershell
# Clean-up test migration for a replicating server
$CleanupTestMigrationJob = Start-AzMigrateTestMigrationCleanup -InputObject $ReplicatingServer

# Track job status to check for completion
while (($CleanupTestMigrationJob.State -eq "InProgress") -or ($CleanupTestMigrationJob.State -eq "NotStarted")){
        #If the job hasn't completed, sleep for 10 seconds before checking the job status again
        sleep 10;
        $CleanupTestMigrationJob = Get-AzMigrateJob -InputObject $CleanupTestMigrationJob
}
#Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded"
Write-Output $CleanupTestMigrationJob.State
```

## <a name="migrate-vms"></a>移轉 VM

確認測試移轉如預期運作之後，您就可以使用下列 Cmdlet 遷移複寫伺服器。 此 Cmdlet 會傳回作業，此作業可追蹤以監視作業狀態。 

如果您不想要關閉來源伺服器，請不要使用 `TurnOffSourceServer` 參數。

```azurepowershell
# Start migration for a replicating server and turn off source server as part of migration
$MigrateJob = Start-AzMigrateServerMigration -InputObject $ReplicatingServer -TurnOffSourceServer 

# Track job status to check for completion
while (($MigrateJob.State -eq "InProgress") -or ($MigrateJob.State -eq "NotStarted")){
        #If the job hasn't completed, sleep for 10 seconds before checking the job status again
        sleep 10;
        $MigrateJob = Get-AzMigrateJob -InputObject $MigrateJob
}
#Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded"
Write-Output $MigrateJob.State
```

## <a name="complete-the-migration"></a>完成移轉

1. 完成遷移之後，請使用下列 Cmdlet 停止內部部署機器的複寫，並清除 VM 的複寫狀態資訊。 此 Cmdlet 會傳回作業，此作業可追蹤以監視作業狀態。 

```azurepowershell
# Stop replication for a migrated server
$StopReplicationJob = Remove-AzMigrateServerReplication -InputObject $ReplicatingServer 

# Track job status to check for completion
while (($StopReplicationJob.State -eq "InProgress") -or ($StopReplicationJob.State -eq "NotStarted")){
        #If the job hasn't completed, sleep for 10 seconds before checking the job status again
        sleep 10;
        $StopReplicationJob = Get-AzMigrateJob -InputObject $StopReplicationJob
}
#Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded"
Write-Output $StopReplicationJob.State
```

2. 如果電腦採用 Linux OS，請在移轉的機器上安裝 [Linux](../virtual-machines/extensions/agent-linux.md) 代理程式。 我們會在遷移期間自動安裝適用於 Windows VM 的 VM 代理程式。
3. 執行任何移轉後應用程式調整，例如更新資料庫連接字串和 Web 伺服器設定。
4. 在現在於 Azure 中執行的已移轉應用程式上，執行最終的應用程式和移轉接受度測試。
5. 將流量完全移轉至已遷移的 Azure VM 執行個體。
6. 從您的本機 VM 清查中移除內部部署 VM。
7. 從本機備份中移除內部部署 VM。
8. 更新任何內部文件，以顯示 Azure VM 的新位置和 IP 位址。 

## <a name="post-migration-best-practices"></a>移轉後的最佳做法

- 針對提升復原能力：
    - 使用「Azure 備份」服務來備份 Azure VM 以維護資料安全。 [深入了解](../backup/quick-backup-vm-portal.md)。
    - 使用 Site Recovery 將 Azure VM 複寫至次要區域，讓工作負載保持執行且持續可供使用。 [深入了解](../site-recovery/azure-to-azure-tutorial-enable-replication.md)。
- 針對提升安全性：
    - 使用 [Azure 資訊安全中心 - Just In Time 系統管理](../security-center/security-center-just-in-time.md)來鎖定並限制輸入流量存取。
    - 使用[網路安全性群組](../virtual-network/network-security-groups-overview.md)來限制傳送至管理端點的網路流量。
    - 部署 [Azure 磁碟加密](../security/fundamentals/azure-disk-encryption-vms-vmss.md)以協助保護磁碟，以及防止資料遭到竊取和受到未經授權的存取。
    - 深入了解如何[保護 IaaS 資源](https://azure.microsoft.com/services/virtual-machines/secure-well-managed-iaas/)，並瀏覽 [Azure 資訊安全中心](https://azure.microsoft.com/services/security-center/)。
- 針對監視及管理：
-  可考慮部署 [Azure 成本管理](../cost-management-billing/cloudyn/overview.md)來監視資源使用情況和花費。