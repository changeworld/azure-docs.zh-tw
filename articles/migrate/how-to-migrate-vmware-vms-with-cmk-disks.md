---
title: 使用伺服器端加密 （SSE） 和客戶管理的金鑰 （CMK） 使用 Azure 遷移伺服器遷移將 VMware 虛擬機器遷移到 Azure
description: 瞭解如何使用伺服器端加密 （SSE） 和客戶管理的金鑰 （CMK） 使用 Azure 遷移伺服器遷移將 VMware VM 遷移到 Azure
author: bsiva
ms.service: azure-migrate
ms.manager: carmonm
ms.topic: article
ms.date: 03/12/2020
ms.author: raynew
ms.openlocfilehash: c6b791fda43a018a26204b2b43dc1e581ff3a945
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79269480"
---
# <a name="migrate-vmware-vms-to-azure-vms-enabled-with-server-side-encryption-and-customer-managed-keys"></a>使用伺服器端加密和客戶管理的金鑰將 VMware VM 遷移到啟用的 Azure VM

本文介紹如何使用 Azure 遷移伺服器遷移（無代理複製）將 VMware VM 遷移到使用伺服器端加密 （SSE） 使用客戶託管金鑰 （CMK） 加密磁片的 Azure 虛擬機器。

Azure 遷移伺服器遷移門戶體驗允許您[使用無代理複製將 VMware VM 遷移到 Azure。](tutorial-migrate-vmware.md) 門戶體驗當前無法提供在 Azure 中為複製磁片打開帶有 CMK 的 SSE 的能力。 目前只能通過 REST API 使用 CMK 打開 SSE 以用於複製磁片。 在本文中，您將瞭解如何創建和部署[Azure 資源管理器範本](../azure-resource-manager/templates/overview.md)以複製 VMware VM，並將 Azure 中複製的磁片配置為將 SSE 與 CMK 一起使用。

本文中的示例使用[Azure PowerShell](/powershell/azure/new-azureps-module-az)執行創建和部署資源管理器範本所需的任務。

[詳細瞭解](../virtual-machines/windows/disk-encryption.md)使用託管磁片的客戶託管金鑰 （CMK） 的伺服器端加密 （SSE）。

## <a name="prerequisites"></a>Prerequisites

- [查看有關](tutorial-migrate-vmware.md)使用無代理複製將 VMware VM 遷移到 Azure 的教程，以瞭解工具要求。
- [按照這些說明](how-to-add-tool-first-time.md)創建 Azure 遷移專案，並將**Azure 遷移：伺服器遷移**工具添加到專案。
- [請按照這些說明](how-to-set-up-appliance-vmware.md)在本地環境中為 VMware 設置 Azure 遷移設備並完成發現。

## <a name="prepare-for-replication"></a>準備進行複寫

VM 發現完成後，"伺服器遷移"磁貼上的"發現伺服器"行將顯示裝置發現的 VMware VM 計數。

在開始複製 VM 之前，需要準備複製基礎結構。

1. 在目的地區域中創建服務匯流排實例。 本地 Azure 遷移設備使用服務匯流排與伺服器遷移服務通信以協調複製和遷移。
2. 創建存儲帳戶，用於從複製傳輸動作記錄。
3. 創建 Azure 遷移設備將複製資料上載到的存儲帳戶。
4. 創建金鑰保存庫並配置金鑰保存庫，以管理步驟 3 和 4 中創建的存儲帳戶上的 Blob 訪問的共用訪問簽名權杖。
5. 為步驟 1 中創建的服務匯流排生成共用訪問簽名權杖，並為在上一步中創建的金鑰保存庫中的權杖創建機密。
6. 創建金鑰保存庫訪問策略，使本地 Azure 遷移設備（使用設備 AAD 應用）和伺服器遷移服務對金鑰保存庫的訪問。
7. 創建複寫原則，並將伺服器遷移服務配置為伺服器遷移服務，並包含上一步驟中創建的複製基礎結構的詳細資訊。

複製基礎結構必須在遷移的目標 Azure 區域中創建，並且必須在要遷移到 VM 的目標 Azure 訂閱中創建。

伺服器遷移門戶體驗通過在專案中首次複製 VM 時自動為您執行此操作，從而簡化了複製基礎結構的準備。 在本文中，我們將假定您已經使用門戶體驗複製了一個或多個 VM，並且複製基礎結構已經創建。 我們將瞭解如何發現現有複製基礎結構的詳細資訊，以及如何將這些詳細資訊用作資源管理器範本的輸入，該範本將用於使用 CMK 設置複製。

### <a name="identifying-replication-infrastructure-components"></a>標識複製基礎結構元件

1. 在 Azure 門戶上，轉到資源組頁面並選擇在其中創建 Azure 遷移專案的資源組。
2. 從左側功能表**中選擇"部署"，** 然後搜索以字串 *"Microsoft.MigrateV2.VMwareV2EnableMigrate"* 開頭的部署名稱。 您將看到門戶體驗為在此專案中為 VM 設置複製而創建的資源管理器範本的清單。 我們將下載一個這樣的範本，並將其用作準備使用 CMK 複製的範本的基礎。
3. 要下載範本，請選擇與上一步中字串模式匹配的任何部署>從左側功能表中選擇 **"範本**>從頂部功能表按一下 **"下載**"。 在本地保存範本.json 檔。 您將在最後一步中編輯此範本檔。

## <a name="create-a-disk-encryption-set"></a>創建磁片加密集

磁片加密集物件將託管磁片映射到金鑰保存庫，該金鑰保存庫包含要用於 SSE 的 CMK。 要使用 CMK 複製 VM，您將創建一個磁片加密集，並將其作為輸入傳遞給複製操作。

請[按照此處](../virtual-machines/windows/disk-encryption.md#powershell)的示例創建使用 Azure PowerShell 的磁片加密集。 確保磁片加密集是在要遷移到 VM 的目標訂閱中創建的，以及遷移的目標 Azure 區域中創建的。

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

## <a name="get-details-of-the-vmware-vm-to-migrate"></a>獲取要遷移的 VMware VM 的詳細資訊

在此步驟中，您將使用 Azure PowerShell 獲取需要遷移的 VM 的詳細資訊。 這些詳細資訊將用於構造用於複製的資源管理器範本。 具體來說，感興趣的兩個屬性是：

- 已發現的 VM 的電腦資源識別碼。
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

複製與發現 VM 的 Azure 遷移設備對應的 SiteId 字串的值。 在上面的示例中，SiteId 是 *"/訂閱/509099b2-9d2c-4636-b43e-bd5cafb6be69/資源組/ContosoVMwareCMK/供應商/微軟。OffAzure/VMwareSites/VMwaresites/VMwareasites/VMwareasites/VMwareasites/VMwareaca8basite"*

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

複製要遷移的電腦的資源 Id、名稱和磁片 uuid 值。
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

## <a name="create-resource-manager-template-for-replication"></a>創建用於複製的資源管理器範本

- 在您選擇的編輯器中打開您在 **"識別複製基礎結構元件**"步驟中下載的資源管理器範本檔。
- 從範本中刪除所有資源定義，但類型為 *"Microsoft.恢復服務/保存庫/複製結構/禁止複製容器/複製遷移專案"* 的資源除外
- 如果上述類型的多個資源定義，請刪除除一個之外的所有資源定義。 從資源定義中刪除任何**依賴On**屬性定義。
- 在此步驟的末尾，應該有一個檔，它類似于下面的示例，並且具有相同的屬性集。

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

- 編輯資源定義中**的名稱**屬性。 將名稱屬性中最後一個 "/"後面的字串替換為 *$machine的值。名稱*（從上一步）。
- 更改**屬性的值.提供程式特定詳細資訊.vmwareMachineId**屬性的值為 *$machine。資源 Id（* 從上一步開始）。
- 分別為目標**資源**組**ID、****目標子網名稱、目標子網名稱**、目標虛擬網路資源識別碼 和目標子網名稱設置目標資源組 ID 的值。
- 將**許可證類型**的值設置為"WindowsServer"，以便為此 VM 應用 Azure 混合權益。 如果此 VM 不符合 Azure 混合權益條件，則將**許可證類型**的值設置為"無許可證類型"。
- 將**目標 VmName**屬性的值更改為遷移 VM 所需的 Azure 虛擬機器名稱。
- 可以選擇在**目標 VmName**屬性下方添加名為**targetVmSize**的屬性。 將**目標 VmSize**屬性的值設置為遷移 VM 所需的 Azure 虛擬機器大小。
- **磁片到Include**屬性是用於複製的磁片輸入的清單，每個清單項代表一個本地磁片。 創建與本地 VM 上的磁片數一樣多的清單項。 將清單項中的**diskId**屬性替換為上一步驟中標識的磁片的 uuid。 將**isOSDisk**值設置為 VM 的 OS 磁片的"true"，將所有其他磁片的"false"設置為"false"。 保持**日誌存儲帳戶 Id**和**日誌存儲帳戶Sas秘密名稱**屬性不變。 將**diskType**值設置為 Azure 託管磁片類型 *（Standard_LRS、Premium_LRS、StandardSSD_LRS*）以用於磁片。 對於需要使用 CMK 加密的磁片，添加名為**disk，Setid**的屬性，並將該值設置為創建的磁片加密集的資源**ID（$des。id**） 在*創建磁片加密集*步驟中
- 保存已編輯的範本檔。 對於上面的示例，編輯的範本檔如下所示：

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

現在，您可以將編輯的資源管理器範本部署到專案資源組，以設置 VM 的複製。 瞭解如何使用[Azure 資源管理器範本和 Azure PowerShell 部署資源](../azure-resource-manager/templates/deploy-powershell.md)

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

通過門戶體驗[監視複製](tutorial-migrate-vmware.md#track-and-monitor)狀態，並執行測試遷移和遷移。
