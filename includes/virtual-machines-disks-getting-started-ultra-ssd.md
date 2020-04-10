---
title: 包含檔案
description: 包含檔案
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 04/08/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: dfb094bc9f84e7129a3e1c733a054c5f6cd96372
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/10/2020
ms.locfileid: "81008619"
---
Azure 超級磁碟為 Azure IaaS 虛擬機器 (VM) 提供高輸送量、高 IOPS 和一致的低延遲磁碟儲存。 這個新的供應項目可提供絕佳的效能，同時保有我們現有磁碟供應項目的相同可用性層級。 超磁碟的一個主要好處是能夠動態更改 SSD 的性能以及您的工作負載,而無需重新啟動 VM。 Ultra 磁碟適用於處理大量資料的工作負載 (例如 SAP Hana)、最上層資料庫，以及高交易量的工作負載。

## <a name="ga-scope-and-limitations"></a>GA 範圍和限制

[!INCLUDE [managed-disks-ultra-disks-GA-scope-and-limitations](managed-disks-ultra-disks-GA-scope-and-limitations.md)]

## <a name="determine-vm-size-and-region-availability"></a>確定 VM 大小與地區可用性

### <a name="vms-using-availability-zones"></a>使用可用性區域的 VM

要利用超磁碟,您需要確定您位於哪個可用性區域。 並不是每個區域都支援每個 VM 大小與超磁碟。 要確定區域、區域和 VM 大小是否支援超磁碟,請執行以下任一指令,請確保首先取代**區域****、vmSize**和**訂閱**值:

#### <a name="cli"></a>CLI

```azurecli
$subscription = "<yourSubID>"
# example value is southeastasia
$region = "<yourLocation>"
# example value is Standard_E64s_v3
$vmSize = "<yourVMSize>"

az vm list-skus --resource-type virtualMachines  --location $region --query "[?name=='$vmSize'].locationInfo[0].zoneDetails[0].Name" --subscription $subscription
```

#### <a name="powershell"></a>PowerShell

```powershell
$region = "southeastasia"
$vmSize = "Standard_E64s_v3"
(Get-AzComputeResourceSku | where {$_.Locations.Contains($region) -and ($_.Name -eq $vmSize) -and $_.LocationInfo[0].ZoneDetails.Count -gt 0})[0].LocationInfo[0].ZoneDetails
```

回應將類似於下面的表單,其中 X 是用於在所選區域中部署的區域。 X 可能是 1、2 或 3。

保留 **「區域」** 值,它表示可用性區域,您需要它才能部署 Ultra 磁碟。

|ResourceType  |名稱  |Location  |區域  |限制  |功能  |值  |
|---------|---------|---------|---------|---------|---------|---------|
|disks     |UltraSSD_LRS         |eastus2         |X         |         |         |         |

> [!NOTE]
> 如果命令沒有回應,則所選區域中的超磁碟不支援所選 VM 大小。

現在,您已經瞭解了要部署到哪個區域,請按照本文中的部署步驟部署附加超磁碟的 VM 或將超磁碟附加到現有 VM。

### <a name="vms-with-no-redundancy-options"></a>沒有冗餘選項的 VM

目前,部署在美國西部部署的超磁碟必須沒有任何冗餘選項。 但是,並非所有支援超磁碟的磁碟大小都位於此區域。 要確定美國西部的哪些磁碟支援超磁碟,可以使用以下代碼段之一。 請確定要取代與`vmSize``subscription`值:

```azurecli
$subscription = "<yourSubID>"
$region = "westus"
# example value is Standard_E64s_v3
$vmSize = "<yourVMSize>"

az vm list-skus --resource-type virtualMachines  --location $region --query "[?name=='$vmSize'].capabilities" --subscription $subscription
```

```azurepowershell
$region = "westus"
$vmSize = "Standard_E64s_v3"
(Get-AzComputeResourceSku | where {$_.Locations.Contains($region) -and ($_.Name -eq $vmSize) })[0].Capabilities
```

回應將類似於以下形式,`UltraSSDAvailable   True`指示 VM 大小是否支援此區域中的超磁碟。

```
Name                                         Value
----                                         -----
MaxResourceVolumeMB                          884736
OSVhdSizeMB                                  1047552
vCPUs                                        64
HyperVGenerations                            V1,V2
MemoryGB                                     432
MaxDataDiskCount                             32
LowPriorityCapable                           True
PremiumIO                                    True
VMDeploymentTypes                            IaaS
vCPUsAvailable                               64
ACUs                                         160
vCPUsPerCore                                 2
CombinedTempDiskAndCachedIOPS                128000
CombinedTempDiskAndCachedReadBytesPerSecond  1073741824
CombinedTempDiskAndCachedWriteBytesPerSecond 1073741824
CachedDiskBytes                              1717986918400
UncachedDiskIOPS                             80000
UncachedDiskBytesPerSecond                   1258291200
EphemeralOSDiskSupported                     True
AcceleratedNetworkingEnabled                 True
RdmaEnabled                                  False
MaxNetworkInterfaces                         8
UltraSSDAvailable                            True
```

## <a name="deploy-an-ultra-disk-using-azure-resource-manager"></a>使用 Azure 資源管理員部署超磁碟

首先,確定要部署的 VM 大小。 有關支援的 VM 大小清單,請參閱[GA 範圍和限制](#ga-scope-and-limitations)部分。

如果要創建具有多個超磁碟的 VM,請參閱範例[「創建具有多個超磁碟的 VM」。。](https://aka.ms/ultradiskArmTemplate)

如果要使用自己的範本,請確保**apiVersion**`Microsoft.Compute/virtualMachines``Microsoft.Compute/Disks``2018-06-01`設置為 (或更高版本)。

將磁碟 sKU 設置為**UltraSSD_LRS**,然後在 MBps 中設置磁碟容量、IOPS、可用性區域和輸送量以創建超磁碟。

一旦佈建 VM，您就可以將資料磁碟分割和格式化，並針對你的工作負載設定它們。


## <a name="deploy-an-ultra-disk-using-the-azure-portal"></a>使用 Azure 門戶部署超磁碟

本節介紹部署配備超磁碟作為數據磁碟的虛擬機。 它假定您熟悉部署虛擬機器(如果不熟悉),請參閱我們的[快速入門:在 Azure 門戶中創建 Windows 虛擬機器](../articles/virtual-machines/windows/quick-create-portal.md)。

- 登錄到 Azure[門戶](https://portal.azure.com/)並導航以部署虛擬機器 (VM)。
- 請確定選擇[支援的 VM 大小與區域](#ga-scope-and-limitations)。
- 可用**性選項**中選擇**可用性區域**。
- 使用您選擇的選項填寫其餘條目。
- 選取 [磁碟]****。

![建立開啟超磁碟的 vm.png](media/virtual-machines-disks-getting-started-ultra-ssd/create-ultra-disk-enabled-vm.png)

- 在磁碟邊列選項卡上,選擇 **「****是「以開啟超磁碟相容性**。
- 選擇 **「立即建立並附加新磁碟**以連接超磁碟」。

![開啟與附加超磁碟.png](media/virtual-machines-disks-getting-started-ultra-ssd/enable-and-attach-ultra-disk.png)

- 在「**創建新磁碟**邊欄選項卡」 上,輸入名稱,然後選擇 **「更改大小**」 。
- 將**帳號型態**變更為**超級磁碟**。
- 將**自訂磁碟大小 (GiB)**,**磁碟 IOPS**和**磁碟輸送量**的值更改為您選擇的值。
- 在兩個刀片中選擇 **「確定**」。
- 繼續 VM 部署,它將與部署任何其他 VM 相同。

![建立超磁碟.png](media/virtual-machines-disks-getting-started-ultra-ssd/create-ultra-disk.png)

## <a name="attach-an-ultra-disk-using-the-azure-portal"></a>使用 Azure 門戶連線超磁碟

或者,如果現有 VM 位於能夠使用超磁碟的區域/可用性區域中,則可以使用超磁碟,而無需創建新 VM。 通過在現有 VM 上啟用超磁碟,然後將它們附加到數據磁碟。

- 瀏覽到 VM 並選擇**磁碟**。
- 選取 [編輯]****。

![選項-選擇器-超磁碟.png](media/virtual-machines-disks-getting-started-ultra-ssd/options-selector-ultra-disks.png)

- 選擇 **「以****開啟硬碟相容性**。

![超選項為啟用.png](media/virtual-machines-disks-getting-started-ultra-ssd/ultra-options-yes-enable.png)

- 選取 [儲存]  。
- 選擇 **「新增資料磁碟**」,然後在**名稱**選擇「**建立磁碟**」的下拉下清單中選擇。

![建立與附加-新超磁碟.png](media/virtual-machines-disks-getting-started-ultra-ssd/create-and-attach-new-ultra-disk.png)

- 填寫新磁碟的名稱,然後選擇 **「更改大小**」。
- 將**帳號型態**變更為**超級磁碟**。
- 將**自訂磁碟大小 (GiB)**,**磁碟 IOPS**和**磁碟輸送量**的值更改為您選擇的值。
- 選擇 **「 確定」 然後**選擇 **「 建立**」 。

![製作新超磁碟.png](media/virtual-machines-disks-getting-started-ultra-ssd/making-a-new-ultra-disk.png)

- 返回到磁碟的邊欄選項卡后,選擇 **「儲存**」。

![儲存及附加-新超磁碟.png](media/virtual-machines-disks-getting-started-ultra-ssd/saving-and-attaching-new-ultra-disk.png)

### <a name="adjust-the-performance-of-an-ultra-disk-using-the-azure-portal"></a>使用 Azure 門戶調整超磁碟的效能

超磁碟具有獨特的功能,允許您調整其性能。 可以從 Azure 門戶、磁碟本身進行這些調整。

- 瀏覽到 VM 並選擇**磁碟**。
- 選擇要修改性能的超磁碟。

![選擇超磁碟到修改.png](media/virtual-machines-disks-getting-started-ultra-ssd/selecting-ultra-disk-to-modify.png)

- 選擇 **「設定**」,然後進行修改。
- 選取 [儲存]  。

![設定超磁碟性能和大小.](media/virtual-machines-disks-getting-started-ultra-ssd/configuring-ultra-disk-performance-and-size.png)

## <a name="deploy-an-ultra-disk-using-cli"></a>使用 CLI 部署超磁碟

首先,確定要部署的 VM 大小。 有關支援的 VM 大小的清單,請參閱[GA 範圍和限制](#ga-scope-and-limitations)部分。

您必須創建能夠使用超磁碟的 VM,以便附加超磁碟。

用您自己的值替換或設置 **$vmname**$vmname、$rgname、$diskname、$location、$password、$user變數。 **$rgname** **$diskname** **$location** **$password** **$user** $zone**集**到[從本文開頭](#determine-vm-size-and-region-availability)獲得的可用性區域的值。 然後執行以下 CLI 指令以建立啟用的超啟用 VM:

```azurecli-interactive
az vm create --subscription $subscription -n $vmname -g $rgname --image Win2016Datacenter --ultra-ssd-enabled true --zone $zone --authentication-type password --admin-password $password --admin-username $user --size Standard_D4s_v3 --location $location
```

### <a name="enable-ultra-disk-compatibility-on-an-existing-vm"></a>在現有 VM 上啟用超磁碟相容性

如果您的 VM 滿足[GA 範圍和限制](#ga-scope-and-limitations)中概述的要求,並且位於[帳戶的相應區域](#determine-vm-size-and-region-availability)中,則可以在 VM 上啟用超磁碟相容性。

要啟用超磁碟相容性,必須停止 VM。 停止 VM 後,可以啟用相容性、附加超磁碟,然後重新啟動 VM:

```azurecli
az vm deallocate -n $vmName -g $rgName
az vm update -n $vmName -g $rgName --ultra-ssd-enabled true
az vm start -n $vmName -g $rgName
```

### <a name="create-an-ultra-disk-using-cli"></a>使用 CLI 建立磁碟

現在,您擁有了能夠附加超磁碟的 VM,您可以創建超級磁碟並將其附加到它。

```azurecli-interactive
$location="eastus2"
$subscription="xxx"
$rgname="ultraRG"
$diskname="ssd1"
$vmname="ultravm1"
$zone=123

#create an ultra disk
az disk create `
--subscription $subscription `
-n $diskname `
-g $rgname `
--size-gb 4 `
--location $location `
--zone $zone `
--sku UltraSSD_LRS `
--disk-iops-read-write 1000 `
--disk-mbps-read-write 50
```

## <a name="attach-an-ultra-disk-to-a-vm-using-cli"></a>使用 CLI 將超磁碟連接到 VM

或者,如果現有 VM 位於能夠使用超磁碟的區域/可用性區域中,則可以使用超磁碟,而無需創建新 VM。

```azurecli
$rgName = "<yourResourceGroupName>"
$vmName = "<yourVMName>"
$diskName = "<yourDiskName>"
$subscriptionId = "<yourSubscriptionID>"

az vm disk attach -g $rgName --vm-name $vmName --disk $diskName --subscription $subscriptionId
```

### <a name="adjust-the-performance-of-an-ultra-disk-using-cli"></a>使用 CLI 調整超磁碟的效能

Ultra 磁碟提供獨特的功能,允許您調整其效能,以下指令描述如何使用此功能:

```azurecli-interactive
az disk update `
--subscription $subscription `
--resource-group $rgname `
--name $diskName `
--set diskIopsReadWrite=80000 `
--set diskMbpsReadWrite=800
```

## <a name="deploy-an-ultra-disk-using-powershell"></a>使用 PowerShell 部署超磁碟

首先,確定要部署的 VM 大小。 有關支援的 VM 大小的清單,請參閱[GA 範圍和限制](#ga-scope-and-limitations)部分。

要使用超磁碟,必須創建能夠使用超磁碟的 VM。 用您自己的值替換或設定 **$resourcegroup$vmName**變數 **$resourcegroup**。 $zone**集**到[從本文開頭](#determine-vm-size-and-region-availability)獲得的可用性區域的值。 然後執行以下[New-AzVm](/powershell/module/az.compute/new-azvm)指令以建立啟用的超啟用 VM:

```powershell
New-AzVm `
    -ResourceGroupName $resourcegroup `
    -Name $vmName `
    -Location "eastus2" `
    -Image "Win2016Datacenter" `
    -EnableUltraSSD $true `
    -size "Standard_D4s_v3" `
    -zone $zone
```

### <a name="enable-ultra-disk-compatibility-on-an-existing-vm"></a>在現有 VM 上啟用超磁碟相容性

如果您的 VM 滿足[GA 範圍和限制](#ga-scope-and-limitations)中概述的要求,並且位於[帳戶的相應區域](#determine-vm-size-and-region-availability)中,則可以在 VM 上啟用超磁碟相容性。

要啟用超磁碟相容性,必須停止 VM。 停止 VM 後,可以啟用相容性、附加超磁碟,然後重新啟動 VM:

```azurepowershell
#stop the VM
$vm1 = Get-AzureRMVM -name $vmName -ResourceGroupName $rgName
Update-AzureRmVM -ResourceGroupName $rgName -VM $vm1 -UltraSSDEnabled 1
#start the VM
```

### <a name="create-an-ultra-disk-using-powershell"></a>使用 PowerShell 建立磁碟

現在,您擁有了能夠使用超磁碟的 VM,您可以創建超級磁碟並將其附加到它:

```powershell
$diskconfig = New-AzDiskConfig `
-Location 'EastUS2' `
-DiskSizeGB 8 `
-DiskIOPSReadWrite 1000 `
-DiskMBpsReadWrite 100 `
-AccountType UltraSSD_LRS `
-CreateOption Empty `
-zone $zone;

New-AzDisk `
-ResourceGroupName $resourceGroup `
-DiskName 'Disk02' `
-Disk $diskconfig;
```

## <a name="attach-an-ultra-disk-to-a-vm-using-powershell"></a>使用 PowerShell 會超磁碟連接到 VM

或者,如果現有 VM 位於能夠使用超磁碟的區域/可用性區域中,則可以使用超磁碟,而無需創建新 VM。

```powershell
# add disk to VM
$subscription = "<yourSubscriptionID>"
$resourceGroup = "<yourResourceGroup>"
$vmName = "<yourVMName>"
$diskName = "<yourDiskName>"
$lun = 1
Login-AzureRMAccount -SubscriptionId $subscription
$vm = Get-AzVM -ResourceGroupName $resourceGroup -Name $vmName
$disk = Get-AzDisk -ResourceGroupName $resourceGroup -Name $diskName
$vm = Add-AzVMDataDisk -VM $vm -Name $diskName -CreateOption Attach -ManagedDiskId $disk.Id -Lun $lun
Update-AzVM -VM $vm -ResourceGroupName $resourceGroup
```

### <a name="adjust-the-performance-of-an-ultra-disk-using-powershell"></a>使用 PowerShell 調整硬碟的效能

Ultra 磁碟具有獨特的功能,允許您調整其性能,以下命令是一個無需分離磁碟即可調整性能的範例:

```powershell
$diskupdateconfig = New-AzDiskUpdateConfig -DiskMBpsReadWrite 2000
Update-AzDisk -ResourceGroupName $resourceGroup -DiskName $diskName -DiskUpdate $diskupdateconfig
```