---
title: 適用于 Vm 的 Ultra 磁片-Azure 受控磁片
description: 瞭解 Azure Vm 的 ultra 磁片
author: roygara
ms.service: virtual-machines
ms.topic: how-to
ms.date: 05/11/2020
ms.author: rogarana
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: 4f49d4e78cd58d16627a0b6fda1957baa9f1f085
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/22/2020
ms.locfileid: "88752077"
---
# <a name="using-azure-ultra-disks"></a>使用 Azure ultra 磁片

本文說明如何部署和使用 ultra 磁片、有關 ultra 磁片的概念資訊，請參閱 [Azure 中有哪些可用的磁片類型？](disks-types.md#ultra-disk)。

Azure ultra 磁片針對 (Vm) 的 Azure IaaS 虛擬機器提供高輸送量、高 IOPS 和一致的低延遲磁片儲存體。 這個新的供應項目可提供絕佳的效能，同時保有我們現有磁碟供應項目的相同可用性層級。 Ultra 磁片的一個主要優點是能夠以動態方式變更 SSD 的效能以及您的工作負載，而不需要重新開機 Vm。 Ultra 磁碟適用於處理大量資料的工作負載 (例如 SAP Hana)、最上層資料庫，以及高交易量的工作負載。

## <a name="ga-scope-and-limitations"></a>GA 範圍和限制

[!INCLUDE [managed-disks-ultra-disks-GA-scope-and-limitations](../../includes/managed-disks-ultra-disks-GA-scope-and-limitations.md)]

## <a name="determine-vm-size-and-region-availability"></a>判斷 VM 大小和區域可用性

### <a name="vms-using-availability-zones"></a>使用可用性區域的 Vm

若要利用 ultra 磁片，您必須判斷您所在的可用性區域。 並非每個區域都支援使用 ultra 磁片的每個 VM 大小。 若要判斷您的區域、區域和 VM 大小是否支援 ultra 磁片，請執行下列其中一個命令，請務必先取代 **區域**、 **vmSize**和 **訂** 用帳戶值：

#### <a name="cli"></a>CLI

```azurecli
subscription="<yourSubID>"
# example value is southeastasia
region="<yourLocation>"
# example value is Standard_E64s_v3
vmSize="<yourVMSize>"

az vm list-skus --resource-type virtualMachines  --location $region --query "[?name=='$vmSize'].locationInfo[0].zoneDetails[0].Name" --subscription $subscription
```

#### <a name="powershell"></a>PowerShell

```powershell
$region = "southeastasia"
$vmSize = "Standard_E64s_v3"
(Get-AzComputeResourceSku | where {$_.Locations.Contains($region) -and ($_.Name -eq $vmSize) -and $_.LocationInfo[0].ZoneDetails.Count -gt 0})[0].LocationInfo[0].ZoneDetails
```

回應將類似下面的表單，其中 X 是要用於在您選擇的區域中部署的區域。 X 可能是 1、2 或 3。

保留 **區域** 值，它代表您的可用性區域，而您將需要它，才能部署 Ultra 磁片。

|ResourceType  |名稱  |位置  |區域  |限制  |功能  |值  |
|---------|---------|---------|---------|---------|---------|---------|
|disks     |UltraSSD_LRS         |eastus2         |X         |         |         |         |

> [!NOTE]
> 如果命令沒有回應，則所選區域中的 ultra 磁片不支援選取的 VM 大小。

既然您已經知道要部署至哪一個區域，請遵循本文中的部署步驟，部署已連接 ultra 磁片的 VM，或將 ultra 磁片連結至現有的 VM。

### <a name="vms-with-no-redundancy-options"></a>沒有冗余選項的 Vm

在美國西部部署的 Ultra 磁片必須立即部署，而不需要任何多餘的選項。 不過，並非每個支援 ultra 磁片的磁片大小都可能在此區域中。 若要判斷美國西部支援 ultra 磁片的專案，您可以使用下列其中一個程式碼片段。 請務必 `vmSize` 先取代和 `subscription` 值：

```azurecli
subscription="<yourSubID>"
region="westus"
# example value is Standard_E64s_v3
vmSize="<yourVMSize>"

az vm list-skus --resource-type virtualMachines  --location $region --query "[?name=='$vmSize'].capabilities" --subscription $subscription
```

```azurepowershell
$region = "westus"
$vmSize = "Standard_E64s_v3"
(Get-AzComputeResourceSku | where {$_.Locations.Contains($region) -and ($_.Name -eq $vmSize) })[0].Capabilities
```

回應將類似下列表單， `UltraSSDAvailable   True` 指出 VM 大小是否支援此區域中的 ultra 磁片。

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

## <a name="deploy-an-ultra-disk-using-azure-resource-manager"></a>使用 Azure Resource Manager 部署 ultra 磁片

首先，判斷要部署的 VM 大小。 如需支援的 VM 大小清單，請參閱 [GA 範圍和限制](#ga-scope-and-limitations) 一節。

如果您想要建立具有多個 ultra 磁片的 VM，請參閱範例 [建立具有多個 ultra 磁片的 vm](https://aka.ms/ultradiskArmTemplate)。

如果您想要使用自己的範本，請確定**apiVersion** `Microsoft.Compute/virtualMachines` `Microsoft.Compute/Disks` 已將 apiVersion 設定為 `2018-06-01` (或更新版本) 。

將磁片 sku 設定為 **UltraSSD_LRS**，然後設定磁片容量、IOPS、可用性區域和輸送量（以 MBps 為單位）來建立 ultra 磁片。

一旦佈建 VM，您就可以將資料磁碟分割和格式化，並針對你的工作負載設定它們。


## <a name="deploy-an-ultra-disk-using-the-azure-portal"></a>使用 Azure 入口網站部署 ultra 磁片

本節說明如何將配備 ultra 磁片的虛擬機器部署為數據磁片。 它會假設您已熟悉部署虛擬機器，如果沒有，請參閱我們 [的快速入門：在 Azure 入口網站中建立 Windows 虛擬機器](~/articles/virtual-machines/windows/quick-create-portal.md)。

- 登入 [Azure 入口網站](https://portal.azure.com/) ，然後流覽至 (VM) 部署虛擬機器。
- 請務必選擇支援的 [VM 大小和區域](#ga-scope-and-limitations)。
- 選取**可用性選項**中的**可用性區域**。
- 使用您選擇的選項填入其餘專案。
- 選取 [磁碟]。

![create-ultra-disk-enabled-vm.png](media/virtual-machines-disks-getting-started-ultra-ssd/create-ultra-disk-enabled-vm.png)

- 在 [磁片] 分頁上，選取 **[是]** 以 **啟用 Ultra 磁片相容性**。
- 選取 [ **建立並連結新的磁片** ]，立即連接 ultra 磁片。

![enable-and-attach-ultra-disk.png](media/virtual-machines-disks-getting-started-ultra-ssd/enable-and-attach-ultra-disk.png)

- 在 [ **建立新的磁片** ] 分頁上，輸入名稱，然後選取 [ **變更大小**]。
- 將 **帳戶類型** 變更為 **Ultra 磁片**。
- 將 **自訂磁片大小的值 (GiB) **、 **磁片 IOPS**和 **磁片輸送量** 變更為您選擇的選項。
- 在兩個 blade 中選取 **[確定]** 。
- 繼續進行 VM 部署，它會與您部署任何其他 VM 相同。

![create-ultra-disk.png](media/virtual-machines-disks-getting-started-ultra-ssd/create-ultra-disk.png)

## <a name="attach-an-ultra-disk-using-the-azure-portal"></a>使用 Azure 入口網站連接 ultra 磁片

或者，如果您現有的 VM 位於能夠使用 ultra 磁片的區域/可用性區域中，您就可以利用 ultra 磁片，而不需要建立新的 VM。 在現有的 VM 上啟用 ultra 磁片，然後將它們連接為數據磁片。

- 流覽至您的 VM，然後選取 [ **磁片**]。
- 選取 [編輯]。

![options-selector-ultra-disks.png](media/virtual-machines-disks-getting-started-ultra-ssd/options-selector-ultra-disks.png)

- 選取 **[是]** 可 **啟用 Ultra 磁片相容性**。

![ultra-options-yes-enable.png](media/virtual-machines-disks-getting-started-ultra-ssd/ultra-options-yes-enable.png)

- 選取 [儲存]。
- 選取 [ **新增資料磁片** ]，然後在 [ **名稱** ] 的下拉式清單中選取 [ **建立磁片**]。

![create-and-attach-new-ultra-disk.png](media/virtual-machines-disks-getting-started-ultra-ssd/create-and-attach-new-ultra-disk.png)

- 填入新磁片的名稱，然後選取 [ **變更大小**]。
- 將 **帳戶類型** 變更為 **Ultra 磁片**。
- 將 **自訂磁片大小的值 (GiB) **、 **磁片 IOPS**和 **磁片輸送量** 變更為您選擇的選項。
- 選取 **[確定]** ，然後選取 [ **建立**]。

![making-a-new-ultra-disk.png](media/virtual-machines-disks-getting-started-ultra-ssd/making-a-new-ultra-disk.png)

- 回到磁片的分頁之後，請選取 [ **儲存**]。

![saving-and-attaching-new-ultra-disk.png](media/virtual-machines-disks-getting-started-ultra-ssd/saving-and-attaching-new-ultra-disk.png)

### <a name="adjust-the-performance-of-an-ultra-disk-using-the-azure-portal"></a>使用 Azure 入口網站來調整 ultra 磁片的效能

Ultra 磁片提供獨特的功能，可讓您調整其效能。 您可以根據磁片本身的 Azure 入口網站進行這些調整。

- 流覽至您的 VM，然後選取 [ **磁片**]。
- 選取您要修改效能的 ultra 磁片。

![selecting-ultra-disk-to-modify.png](media/virtual-machines-disks-getting-started-ultra-ssd/selecting-ultra-disk-to-modify.png)

- 選取 [設定] **，然後進行** 修改。
- 選取 [儲存]。

![configuring-ultra-disk-performance-and-size.png](media/virtual-machines-disks-getting-started-ultra-ssd/configuring-ultra-disk-performance-and-size.png)

## <a name="deploy-an-ultra-disk-using-cli"></a>使用 CLI 部署 ultra 磁片

首先，判斷要部署的 VM 大小。 如需支援的 VM 大小清單，請參閱 [GA 範圍和限制](#ga-scope-and-limitations) 一節。

您必須建立能夠使用 ultra 磁片的 VM，才能連接 ultra 磁片。

以您自己的值取代或設定 **$vmname**、 **$rgname**、 **$diskname**、 **$location**、 **$password**、 **$user** 變數。 將 **$zone**  設定為您從本文 [開頭](#determine-vm-size-and-region-availability)取得的可用性區域值。 然後執行下列 CLI 命令，以建立啟用 ultra 的 VM：

```azurecli-interactive
az vm create --subscription $subscription -n $vmname -g $rgname --image Win2016Datacenter --ultra-ssd-enabled true --zone $zone --authentication-type password --admin-password $password --admin-username $user --size Standard_D4s_v3 --location $location
```

### <a name="enable-ultra-disk-compatibility-on-an-existing-vm"></a>在現有的 VM 上啟用 ultra 磁片相容性

如果您的 VM 符合 [GA 範圍和限制](#ga-scope-and-limitations) 中所述的需求，而且在 [您帳戶的適當區域](#determine-vm-size-and-region-availability)中，則您可以在 VM 上啟用 ultra 磁片相容性。

若要啟用 ultra 磁片相容性，您必須停止 VM。 停止 VM 之後，您可以啟用相容性、連接 ultra 磁片，然後重新開機 VM：

```azurecli
az vm deallocate -n $vmName -g $rgName
az vm update -n $vmName -g $rgName --ultra-ssd-enabled true
az vm start -n $vmName -g $rgName
```

### <a name="create-an-ultra-disk-using-cli"></a>使用 CLI 建立 ultra 磁片

現在您已有能夠連接 ultra 磁片的 VM，您可以建立 ultra 磁片並將其連接到該 VM。

```azurecli-interactive
location="eastus2"
subscription="xxx"
rgname="ultraRG"
diskname="ssd1"
vmname="ultravm1"
zone=123

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

## <a name="attach-an-ultra-disk-to-a-vm-using-cli"></a>使用 CLI 將 ultra 磁片連結至 VM

或者，如果您現有的 VM 位於能夠使用 ultra 磁片的區域/可用性區域中，您就可以利用 ultra 磁片，而不需要建立新的 VM。

```azurecli
rgName="<yourResourceGroupName>"
vmName="<yourVMName>"
diskName="<yourDiskName>"
subscriptionId="<yourSubscriptionID>"

az vm disk attach -g $rgName --vm-name $vmName --disk $diskName --subscription $subscriptionId
```

### <a name="adjust-the-performance-of-an-ultra-disk-using-cli"></a>使用 CLI 調整 ultra 磁片的效能

Ultra 磁片提供獨特的功能，可讓您調整其效能，下列命令說明如何使用這項功能：

```azurecli-interactive
az disk update `
--subscription $subscription `
--resource-group $rgname `
--name $diskName `
--set diskIopsReadWrite=80000 `
--set diskMbpsReadWrite=800
```

## <a name="deploy-an-ultra-disk-using-powershell"></a>使用 PowerShell 部署 ultra 磁片

首先，判斷要部署的 VM 大小。 如需支援的 VM 大小清單，請參閱 [GA 範圍和限制](#ga-scope-and-limitations) 一節。

若要使用 ultra 磁片，您必須建立能夠使用 ultra 磁片的 VM。 以您自己的值取代或設定 **$resourcegroup** 和 **$vmName** 變數。 將 **$zone** 設定為您從本文 [開頭](#determine-vm-size-and-region-availability)取得的可用性區域值。 然後執行下列 [new-azvm](/powershell/module/az.compute/new-azvm) 命令，以建立啟用 ULTRA 的 VM：

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

### <a name="enable-ultra-disk-compatibility-on-an-existing-vm"></a>在現有的 VM 上啟用 ultra 磁片相容性

如果您的 VM 符合 [GA 範圍和限制](#ga-scope-and-limitations) 中所述的需求，而且在 [您帳戶的適當區域](#determine-vm-size-and-region-availability)中，則您可以在 VM 上啟用 ultra 磁片相容性。

若要啟用 ultra 磁片相容性，您必須停止 VM。 停止 VM 之後，您可以啟用相容性、連接 ultra 磁片，然後重新開機 VM：

```azurepowershell
#stop the VM
$vm1 = Get-AzureRMVM -name $vmName -ResourceGroupName $rgName
Update-AzureRmVM -ResourceGroupName $rgName -VM $vm1 -UltraSSDEnabled 1
#start the VM
```

### <a name="create-an-ultra-disk-using-powershell"></a>使用 PowerShell 建立 ultra 磁片

現在您有一個能夠使用 ultra 磁片的 VM，您可以建立 ultra 磁片並將其連接到該 VM：

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

## <a name="attach-an-ultra-disk-to-a-vm-using-powershell"></a>使用 PowerShell 將 ultra 磁片連結至 VM

或者，如果您現有的 VM 位於能夠使用 ultra 磁片的區域/可用性區域中，您就可以利用 ultra 磁片，而不需要建立新的 VM。

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

### <a name="adjust-the-performance-of-an-ultra-disk-using-powershell"></a>使用 PowerShell 調整 ultra 磁片的效能

Ultra 磁片具有獨特的功能，可讓您調整其效能，下列命令是調整效能的範例，而不需要卸離磁片：

```powershell
$diskupdateconfig = New-AzDiskUpdateConfig -DiskMBpsReadWrite 2000
Update-AzDisk -ResourceGroupName $resourceGroup -DiskName $diskName -DiskUpdate $diskupdateconfig
```
## <a name="next-steps"></a>後續步驟

請參閱 [Azure Kubernetes Service (preview) 上使用 Azure ultra 磁片 ](../aks/use-ultra-disks.md)。
