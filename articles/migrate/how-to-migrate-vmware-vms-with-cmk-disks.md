---
title: 使用伺服器端加密將 VMware 虛擬機器遷移至 Azure (SSE) 和客戶管理的金鑰 (CMK) 使用 Azure Migrate Server 遷移
description: 瞭解如何使用伺服器端加密 (SSE) 和客戶管理的金鑰，將 VMware Vm 遷移至 Azure (CMK) 使用 Azure Migrate 伺服器遷移
author: anvar-ms
ms.author: anvar
ms.manager: bsiva
ms.topic: how-to
ms.date: 03/12/2020
ms.openlocfilehash: 8a174c3b2bfb390eb7d691ae1bdcb0e28dde9032
ms.sourcegitcommit: ea551dad8d870ddcc0fee4423026f51bf4532e19
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/07/2020
ms.locfileid: "96751082"
---
# <a name="migrate-vmware-vms-to-azure-vms-enabled-with-server-side-encryption-and-customer-managed-keys"></a>使用伺服器端加密和客戶管理的金鑰，將 VMware Vm 遷移至啟用的 Azure Vm

本文說明如何使用 Azure Migrate 伺服器遷移 (無代理程式複寫) ，將 VMware Vm 遷移至 Azure 虛擬機器，並使用伺服器端加密 (SSE) 搭配客戶管理的金鑰 (CMK) 。

Azure Migrate Server 遷移入口網站體驗可讓您 [使用無代理程式複寫將 VMware vm 遷移至 Azure。](tutorial-migrate-vmware.md) 入口網站體驗目前無法在 Azure 中為您的複寫磁片開啟具有 CMK 的 SSE 功能。 針對複寫的磁片開啟具有 CMK 的 SSE 的功能，目前只能透過 REST API 使用。 在本文中，您將瞭解如何建立和部署 [Azure Resource Manager 範本](../azure-resource-manager/templates/overview.md) 以複寫 VMware VM，並在 Azure 中設定複寫的磁片以搭配 CMK 使用 SSE。

本文中的範例會使用 [Azure PowerShell](/powershell/azure/new-azureps-module-az) 來執行建立和部署 Resource Manager 範本所需的工作。

[深入瞭解](../virtual-machines/disk-encryption.md) 使用客戶管理的金鑰 (SSE) 的伺服器端加密 (適用于受控磁片的 CMK) 。

## <a name="prerequisites"></a>先決條件

- 請參閱使用無代理程式複寫將 VMware Vm 遷移至 Azure[的教學](tutorial-migrate-vmware.md)課程，以瞭解工具需求。
- [遵循這些指示](./create-manage-projects.md) 來建立 Azure Migrate 專案，並將 **Azure Migrate：伺服器遷移** 工具新增至專案。
- [請遵循下列指示](how-to-set-up-appliance-vmware.md) ，在您的內部部署環境中設定適用于 VMware 的 Azure Migrate 設備，並進行完整探索。

## <a name="prepare-for-replication"></a>準備進行複寫

VM 探索完成之後，[伺服器遷移] 磚上的 [探索到的伺服器] 線會顯示裝置探索到的 VMware Vm 計數。

您必須先準備好複寫基礎結構，才能開始複寫 Vm。

1. 在目的地區域中建立服務匯流排實例。 內部部署 Azure Migrate 設備會使用服務匯流排來與伺服器遷移服務進行通訊，以協調複寫和遷移。
2. 建立儲存體帳戶，以從複寫傳送作業記錄。
3. 建立 Azure Migrate 設備上傳複寫資料的儲存體帳戶。
4. 建立 Key Vault，並將 Key Vault 設定為在步驟3和4中建立的儲存體帳戶上管理 blob 存取權的共用存取簽章權杖。
5. 針對步驟1中建立的服務匯流排產生共用存取簽章權杖，並在上一個步驟中建立的 Key Vault 建立權杖的秘密。
6. 建立 Key Vault 存取原則，以提供內部部署 Azure Migrate 設備 (使用設備 AAD 應用程式) 和伺服器遷移服務存取 Key Vault。
7. 建立複寫原則，並使用在上一個步驟中建立的複寫基礎結構詳細資料來設定伺服器遷移服務。

您必須在目標 Azure 區域中建立複寫基礎結構，才能進行遷移，並在要遷移 Vm 的目標 Azure 訂用帳戶中建立。

當您第一次在專案中複寫 VM 時，伺服器遷移入口網站體驗可自動為您完成複寫基礎結構的準備工作。 在本文中，我們假設您已使用入口網站體驗來複寫一或多個 Vm，且已建立複寫基礎結構。 我們將探討如何探索現有複寫基礎結構的詳細資料，以及如何使用這些詳細資料做為 Resource Manager 範本的輸入，此範本將用來設定 CMK 的複寫。

### <a name="identifying-replication-infrastructure-components"></a>識別複寫基礎結構元件

1. 在 [Azure 入口網站上，移至 [資源群組] 頁面，然後選取建立 Azure Migrate 專案的資源群組。
2. 從左側功能表中選取 [ **部署** ]，並搜尋開頭為 *"MigrateV2. VMwareV2EnableMigrate"* 字串的部署名稱。 您會看到入口網站體驗所建立的 Resource Manager 範本清單，以設定此專案中 Vm 的複寫。 我們將下載一個此類範本，並將其作為基礎，以準備使用 CMK 進行複寫的範本。
3. 若要下載範本，請在上一個步驟中選取符合字串模式的任何部署 > 從左側功能表中選取 [ **範本** ] > 按一下頂端功能表中的 [ **下載** ]。 將 template.js儲存在本機檔案上。 您將在最後一個步驟中編輯此範本檔案。

## <a name="create-a-disk-encryption-set"></a>建立磁片加密集

磁片加密集物件會將受控磁碟對應到 Key Vault，其中包含要用於 SSE 的 CMK。 若要使用 CMK 複寫 Vm，您將建立磁片加密集，並將它作為複寫作業的輸入傳遞。

遵循 [此處](../virtual-machines/windows/disks-enable-customer-managed-keys-powershell.md) 的範例，使用 Azure PowerShell 建立磁片加密集。 請確定已在目標訂用帳戶中建立要遷移 Vm 的磁片加密集，並在要遷移的目標 Azure 區域中建立磁片加密集。

磁片加密集可以設定為使用客戶管理的金鑰來加密受控磁片，或使用客戶管理的金鑰和平臺金鑰進行雙重加密。 若要使用雙重加密靜態選項，請依照 [此處](../virtual-machines/windows/disks-enable-double-encryption-at-rest-powershell.md)所述設定磁片加密集。

在如下所示的範例中，已將磁片加密集設定為使用客戶管理的金鑰。

```azurepowershell
$Location = "southcentralus"                           #Target Azure region for migration 
$TargetResourceGroupName = "ContosoMigrationTarget"
$KeyVaultName = "ContosoCMKKV"
$KeyName = "ContosoCMKKey"
$KeyDestination = "Software"
$DiskEncryptionSetName = "ContosoCMKDES"

$KeyVault = New-AzKeyVault -Name $KeyVaultName -ResourceGroupName $TargetResourceGroupName -Location $Location -EnableSoftDelete -EnablePurgeProtection

$Key = Add-AzKeyVaultKey -VaultName $KeyVaultName -Name $KeyName -Destination $KeyDestination

$desConfig = New-AzDiskEncryptionSetConfig -Location $Location -SourceVaultId $KeyVault.ResourceId -KeyUrl $Key.Key.Kid -IdentityType SystemAssigned

$des = New-AzDiskEncryptionSet -Name $DiskEncryptionSetName -ResourceGroupName $TargetResourceGroupName -InputObject $desConfig

Set-AzKeyVaultAccessPolicy -VaultName $KeyVaultName -ObjectId $des.Identity.PrincipalId -PermissionsToKeys wrapkey,unwrapkey,get

New-AzRoleAssignment -ResourceName $KeyVaultName -ResourceGroupName $TargetResourceGroupName -ResourceType "Microsoft.KeyVault/vaults" -ObjectId $des.Identity.PrincipalId -RoleDefinitionName "Reader"
```

## <a name="get-details-of-the-vmware-vm-to-migrate"></a>取得要遷移的 VMware VM 詳細資料

在此步驟中，您將使用 Azure PowerShell 來取得需要遷移之 VM 的詳細資料。 這些詳細資料將用來建立複寫 Resource Manager 範本。 具體而言，這兩個相關的屬性是：

- 探索到之 Vm 的電腦資源識別碼。
- VM 的磁片清單及其磁片識別碼。

```azurepowershell

$ProjectResourceGroup = "ContosoVMwareCMK"   #Resource group that the Azure Migrate Project is created in
$ProjectName = "ContosoVMwareCMK"            #Name of the Azure Migrate Project

$solution = Get-AzResource -ResourceGroupName $ProjectResourceGroup -ResourceType Microsoft.Migrate/MigrateProjects/solutions -ExpandProperties -ResourceName $ProjectName | where Name -eq "Servers-Discovery-ServerDis
covery"

# Displays one entry for each appliance in the project mapping the appliance to the VMware sites discovered through the appliance.
$solution.Properties.details.extendedDetails.applianceNameToSiteIdMapV2 | ConvertFrom-Json | select ApplianceName, SiteId
```
```Output
ApplianceName  SiteId
-------------  ------
VMwareApplianc /subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.OffAzure/VMwareSites/VMwareApplianca8basite
```

複製 SiteId 字串的值，其對應至 VM 所探索到的 Azure Migrate 設備。 在上面顯示的範例中，SiteId 是 *"/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.OffAzure/VMwareSites/VMwareApplianca8basite"*

```azurepowershell

#Replace value with SiteId from the previous step
$SiteId = "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.OffAzure/VMwareSites/VMwareApplianca8basite"
$SiteName = Get-AzResource -ResourceId $SiteId -ExpandProperties | Select-Object -ExpandProperty Name
$DiscoveredMachines = Get-AzResource -ResourceGroupName $ProjectResourceGroup -ResourceType Microsoft.OffAzure/VMwareSites/machines  -ExpandProperties -ResourceName $SiteName

#Get machine details
PS /home/bharathram> $MachineName = "FPL-W19-09"     #Replace string with VMware VM name of the machine to migrate
PS /home/bharathram> $machine = $Discoveredmachines | where {$_.Properties.displayName -eq $MachineName}
PS /home/bharathram> $machine.count   #Validate that only 1 VM was found matching this name.
```

針對要遷移的機器複製 ResourceId、名稱和磁片 uuid 值。
```Output
PS > $machine.Name
10-150-8-52-b090bef3-b733-5e34-bc8f-eb6f2701432a_50098f99-f949-22ca-642b-724ec6595210
PS > $machine.ResourceId
/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.OffAzure/VMwareSites/VMwareApplianca8basite/machines/10-150-8-52-b090bef3-b733-5e34-bc8f-eb6f2701432a_50098f99-f949-22ca-642b-724ec6595210

PS > $machine.Properties.disks | select uuid, label, name, maxSizeInBytes

uuid                                 label       name    maxSizeInBytes
----                                 -----       ----    --------------
6000C291-5106-2aac-7a74-4f33c3ddb78c Hard disk 1 scsi0:0    42949672960
6000C293-39a1-bd70-7b24-735f0eeb79c4 Hard disk 2 scsi0:1    53687091200
6000C29e-cbee-4d79-39c7-d00dd0208aa9 Hard disk 3 scsi0:2    53687091200

```

## <a name="create-resource-manager-template-for-replication"></a>建立複寫 Resource Manager 範本

- 在您選擇的編輯器中，開啟您在 **識別複寫基礎結構元件** 步驟中下載的 Resource Manager 範本檔案。
- 除了 *"az.recoveryservices/vault/replicationFabrics/replicationProtectionContainers/replicationMigrationItems"* 類型的資源之外，移除範本中的所有資源定義
- 如果有多個以上類型的資源定義，請移除所有的資源定義，但不包含一個。 從資源定義中移除任何 **dependsOn** 屬性定義。
- 在這個步驟結束時，您應該會有類似以下範例的檔案，而且具有相同的屬性集。

```
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "type": "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems",
            "apiVersion": "2018-01-10",
            "name": "ContosoMigration7371rsvault/VMware104e4replicationfabric/VMware104e4replicationcontainer/10-150-8-52-b090bef3-b733-5e34-bc8f-eb6f2701432a_500937f3-805e-9414-11b1-f22923456e08",
            "properties": {
                "policyId": "/Subscriptions/6785ea1f-ac40-4244-a9ce-94b12fd832ca/resourceGroups/ContosoMigration/providers/Microsoft.RecoveryServices/vaults/ContosoMigration7371rsvault/replicationPolicies/migrateVMware104e4sitepolicy",
                "providerSpecificDetails": {
                    "instanceType": "VMwareCbt",
                    "vmwareMachineId": "/subscriptions/6785ea1f-ac40-4244-a9ce-94b12fd832ca/resourceGroups/ContosoMigration/providers/Microsoft.OffAzure/VMwareSites/VMware104e4site/machines/10-150-8-52-b090bef3-b733-5e34-bc8f-eb6f2701432a_500937f3-805e-9414-11b1-f22923456e08",
                    "targetResourceGroupId": "/subscriptions/6785ea1f-ac40-4244-a9ce-94b12fd832ca/resourceGroups/PayrollRG",
                    "targetNetworkId": "/subscriptions/6785ea1f-ac40-4244-a9ce-94b12fd832ca/resourceGroups/PayrollRG/providers/Microsoft.Network/virtualNetworks/PayrollNW",
                    "targetSubnetName": "PayrollSubnet",
                    "licenseType": "NoLicenseType",
                    "disksToInclude": [
                        {
                            "diskId": "6000C295-dafe-a0eb-906e-d47cb5b05a1d",
                            "isOSDisk": "true",
                            "logStorageAccountId": "/subscriptions/6785ea1f-ac40-4244-a9ce-94b12fd832ca/resourceGroups/ContosoMigration/providers/Microsoft.Storage/storageAccounts/migratelsa1432469187",
                            "logStorageAccountSasSecretName": "migratelsa1432469187-cacheSas",
                            "diskType": "Standard_LRS"
                        }
                    ],
                    "dataMoverRunAsAccountId": "/subscriptions/6785ea1f-ac40-4244-a9ce-94b12fd832ca/resourceGroups/ContosoMigration/providers/Microsoft.OffAzure/VMwareSites/VMware104e4site/runasaccounts/b090bef3-b733-5e34-bc8f-eb6f2701432a",
                    "snapshotRunAsAccountId": "/subscriptions/6785ea1f-ac40-4244-a9ce-94b12fd832ca/resourceGroups/ContosoMigration/providers/Microsoft.OffAzure/VMwareSites/VMware104e4site/runasaccounts/b090bef3-b733-5e34-bc8f-eb6f2701432a",
                    "targetBootDiagnosticsStorageAccountId": "/subscriptions/6785ea1f-ac40-4244-a9ce-94b12fd832ca/resourceGroups/ContosoMigration/providers/Microsoft.Storage/storageAccounts/migratelsa1432469187",
                    "targetVmName": "PayrollWeb04"
                }
            }
        }
    ]
}
```

- 編輯資源定義中的 **名稱** 屬性。 將名稱屬性中最後一個 "/" 後面的字串取代為 $machine 的值 *。* 從上一個步驟)  ( 名稱。
- 將 **providerSpecificDetails. vmwareMachineId** 屬性的值變更為 $machine 的值 *。* 上一個步驟中的 ResourceId () 。
- 將 **targetResourceGroupId**、 **targetNetworkId**、 **targetSubnetName** 的值分別設定為目標資源群組識別碼、目標虛擬網路資源識別碼和目標子網名稱。
- 將 **licenseType** 的值設定為 "WindowsServer"，以套用此 VM 的 Azure Hybrid Benefit。 如果此 VM 不符合 Azure Hybrid Benefit 的資格，請將 **licenseType** 的值設定為 NoLicenseType。
- 將 **targetVmName** 屬性的值變更為所需的 Azure 虛擬機器名稱，以供遷移的 VM 之用。
- （選擇性）在 **targetVmName** 屬性底下加入名為 **targetVmSize** 的屬性。 將 **targetVmSize** 屬性的值設定為所遷移 VM 所需的 Azure 虛擬機器大小。
- **DisksToInclude** 屬性是用於複寫的磁片輸入清單，每個清單專案代表一個內部部署磁片。 建立多個清單專案作為內部部署 VM 上的磁片數目。 將清單專案中的 **diskId** 屬性取代為上一個步驟中所識別之磁片的 uuid。 將 VM 的 OS 磁片的 **isOSDisk** 值設定為 "true"，並將所有其他磁片的值設定為 "false"。 將 **logStorageAccountId** 和 **logStorageAccountSasSecretName** 屬性保持不變。 將 **diskType** 值設定為 Azure 受控磁片類型 (*Standard_LRS、Premium_LRS、StandardSSD_LRS*) 以用於磁片。 針對需要使用 CMK 加密的磁片，請新增名為 **diskEncryptionSetId** 的屬性，並將值設定為 ($des 所建立之磁片加密集的資源識別碼 **。***建立磁片加密集* 步驟中的識別碼) 
- 儲存編輯過的範本檔案。 在上述範例中，編輯過的範本檔案看起來如下：

```
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "type": "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems",
            "apiVersion": "2018-01-10",
            "name": "ContosoVMwareCMK00ddrsvault/VMwareApplianca8bareplicationfabric/VMwareApplianca8bareplicationcontainer/10-150-8-52-b090bef3-b733-5e34-bc8f-eb6f2701432a_50098f99-f949-22ca-642b-724ec6595210",
            "properties": {
                "policyId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.RecoveryServices/vaults/ContosoVMwareCMK00ddrsvault/replicationPolicies/migrateVMwareApplianca8basitepolicy",
                "providerSpecificDetails": {
                    "instanceType": "VMwareCbt",
                    "vmwareMachineId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.OffAzure/VMwareSites/VMwareApplianca8basite/machines/10-150-8-52-b090bef3-b733-5e34-bc8f-eb6f2701432a_50098f99-f949-22ca-642b-724ec6595210",
                    "targetResourceGroupId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoMigrationTarget",
                    "targetNetworkId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/cmkRTest/providers/Microsoft.Network/virtualNetworks/cmkvm1_vnet",
                    "targetSubnetName": "cmkvm1_subnet",
                    "licenseType": "NoLicenseType",
                    "disksToInclude": [
                        {
                            "diskId": "6000C291-5106-2aac-7a74-4f33c3ddb78c",
                            "isOSDisk": "true",
                            "logStorageAccountId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.Storage/storageAccounts/migratelsa1671875959",
                            "logStorageAccountSasSecretName": "migratelsa1671875959-cacheSas",
                            "diskEncryptionSetId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/CONTOSOMIGRATIONTARGET/providers/Microsoft.Compute/diskEncryptionSets/ContosoCMKDES",
                            "diskType": "Standard_LRS"
                        },
                        {
                            "diskId": "6000C293-39a1-bd70-7b24-735f0eeb79c4",
                            "isOSDisk": "false",
                            "logStorageAccountId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.Storage/storageAccounts/migratelsa1671875959",
                            "logStorageAccountSasSecretName": "migratelsa1671875959-cacheSas",
                            "diskEncryptionSetId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/CONTOSOMIGRATIONTARGET/providers/Microsoft.Compute/diskEncryptionSets/ContosoCMKDES",
                            "diskType": "Standard_LRS"
                        },
                        {
                            "diskId": "6000C29e-cbee-4d79-39c7-d00dd0208aa9",
                            "isOSDisk": "false",
                            "logStorageAccountId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.Storage/storageAccounts/migratelsa1671875959",
                            "logStorageAccountSasSecretName": "migratelsa1671875959-cacheSas",
                            "diskEncryptionSetId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/CONTOSOMIGRATIONTARGET/providers/Microsoft.Compute/diskEncryptionSets/ContosoCMKDES",
                            "diskType": "Standard_LRS"
                        }
                    ],
                    "dataMoverRunAsAccountId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.OffAzure/VMwareSites/VMwareApplianca8basite/runasaccounts/b090bef3-b733-5e34-bc8f-eb6f2701432a",
                    "snapshotRunAsAccountId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.OffAzure/VMwareSites/VMwareApplianca8basite/runasaccounts/b090bef3-b733-5e34-bc8f-eb6f2701432a",
                    "targetBootDiagnosticsStorageAccountId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.Storage/storageAccounts/migratelsa1671875959",
                    "performAutoResync": "true",
                    "targetVmName": "FPL-W19-09"
                }
            }
        }
    ]
}
```

## <a name="set-up-replication"></a>設定複寫

您現在可以將已編輯的 Resource Manager 範本部署至專案資源群組，以設定 VM 的複寫。 瞭解如何 [使用 Azure Resource Manager 範本和 Azure PowerShell 部署資源](../azure-resource-manager/templates/deploy-powershell.md)

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName $ProjectResourceGroup -TemplateFile "C:\Users\Administrator\Downloads\template.json"
```

```Output
DeploymentName          : template
ResourceGroupName       : ContosoVMwareCMK
ProvisioningState       : Succeeded
Timestamp               : 3/11/2020 8:52:00 PM
Mode                    : Incremental
TemplateLink            :
Parameters              :
Outputs                 :
DeploymentDebugLogLevel :

```

## <a name="next-steps"></a>後續步驟

透過入口網站體驗[監視](tutorial-migrate-vmware.md#track-and-monitor)複寫狀態，並執行測試遷移和遷移。