---
title: 包含檔案
description: 包含檔案
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 11/14/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 0d081a8cec088f4743bd0dc7d3cc37a9fade61d1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80117027"
---
Azure 超級磁片為 Azure IaaS 虛擬機器 （VM） 提供高輸送量、高 IOPS 和一致的低延遲磁片存儲。 這個新的供應項目可提供絕佳的效能，同時保有我們現有磁碟供應項目的相同可用性層級。 超磁片的一個主要好處是能夠動態更改 SSD 的性能以及您的工作負載，而無需重新開機 VM。 Ultra 磁碟適用於處理大量資料的工作負載 (例如 SAP Hana)、最上層資料庫，以及高交易量的工作負載。

## <a name="ga-scope-and-limitations"></a>GA 範圍和限制

[!INCLUDE [managed-disks-ultra-disks-GA-scope-and-limitations](managed-disks-ultra-disks-GA-scope-and-limitations.md)]

## <a name="determine-vm-size-and-region-availability"></a>確定 VM 大小和地區可用性

要利用超磁片，您需要確定您位於哪個可用性區域。 並不是每個區域都支援每個 VM 大小與超磁片。 要確定區域、區域和 VM 大小是否支援超磁片，請運行以下任一命令，請確保首先替換**區域****、vmSize**和**訂閱**值：

CLI：

```azurecli
$subscription = "<yourSubID>"
# example value is southeastasia
$region = "<yourLocation>"
# example value is Standard_E64s_v3
$vmSize = "<yourVMSize>"

az vm list-skus --resource-type virtualMachines  --location $region --query "[?name=='$vmSize'].locationInfo[0].zoneDetails[0].Name" --subscription $subscription
```

PowerShell：

```powershell
$region = "southeastasia"
$vmSize = "Standard_E64s_v3"
(Get-AzComputeResourceSku | where {$_.Locations.Contains($region) -and ($_.Name -eq $vmSize) -and $_.LocationInfo[0].ZoneDetails.Count -gt 0})[0].LocationInfo[0].ZoneDetails
```

回應將類似于下面的表單，其中 X 是用於在所選區域中部署的區域。 X 可能是 1、2 或 3。

保留 **"區域"** 值，它表示可用性區域，您需要它才能部署 Ultra 磁片。

|ResourceType  |名稱  |Location  |區域  |限制  |功能  |值  |
|---------|---------|---------|---------|---------|---------|---------|
|disks     |UltraSSD_LRS         |eastus2         |X         |         |         |         |

> [!NOTE]
> 如果命令沒有回應，則所選區域中的超磁片不支援所選 VM 大小。

現在，您已經瞭解了要部署到哪個區域，請按照本文中的部署步驟部署附加超磁片的 VM 或將超磁片附加到現有 VM。

## <a name="deploy-an-ultra-disk-using-azure-resource-manager"></a>使用 Azure 資源管理器部署超磁片

首先，確定要部署的 VM 大小。 有關支援的 VM 大小清單，請參閱[GA 範圍和限制](#ga-scope-and-limitations)部分。

如果要創建具有多個超磁片的 VM，請參閱示例["創建具有多個超磁片的 VM"。](https://aka.ms/ultradiskArmTemplate)

如果要使用自己的範本，請確保**apiVersion**設置為`Microsoft.Compute/virtualMachines` `Microsoft.Compute/Disks` `2018-06-01` （或更高版本）。

將磁片 sKU 設置為**UltraSSD_LRS**，然後在 MBps 中設置磁片容量、IOPS、可用性區域和輸送量以創建超磁片。

一旦佈建 VM，您就可以將資料磁碟分割和格式化，並針對你的工作負載設定它們。


## <a name="deploy-an-ultra-disk-using-the-azure-portal"></a>使用 Azure 門戶部署超磁片

本節介紹部署配備超磁片作為資料磁片的虛擬機器。 它假定您熟悉部署虛擬機器（如果不熟悉），請參閱我們的[快速入門：在 Azure 門戶中創建 Windows 虛擬機器](../articles/virtual-machines/windows/quick-create-portal.md)。

- 登錄到 Azure[門戶](https://portal.azure.com/)並導航以部署虛擬機器 （VM）。
- 請確保選擇[受支援的 VM 大小和區域](#ga-scope-and-limitations)。
- 在**可用性選項**中選擇**可用性區域**。
- 使用您選擇的選項填寫其餘條目。
- 選取 [磁碟]****。

![創建啟用超磁片的 vm.png](media/virtual-machines-disks-getting-started-ultra-ssd/create-ultra-disk-enabled-vm.png)

- 在磁片邊欄選項卡上，選擇 **"是****"以啟用超磁片相容性**。
- 選擇 **"立即創建並附加新磁片**以連接超磁片"。

![啟用和附加超磁片.png](media/virtual-machines-disks-getting-started-ultra-ssd/enable-and-attach-ultra-disk.png)

- 在"**創建新磁片**邊欄選項卡"上，輸入名稱，然後選擇 **"更改大小**"。
- 將**帳戶類型**更改為**超級磁片**。
- 將**自訂磁片大小 （GiB）**、**磁片 IOPS**和**磁片輸送量**的值更改為您選擇的值。
- 在兩個刀片中選擇 **"確定**"。
- 繼續 VM 部署，它將與部署任何其他 VM 相同。

![創建超磁片.png](media/virtual-machines-disks-getting-started-ultra-ssd/create-ultra-disk.png)

## <a name="attach-an-ultra-disk-using-the-azure-portal"></a>使用 Azure 門戶連接超磁片

或者，如果現有 VM 位於能夠使用超磁片的區域/可用性區域中，則可以使用超磁片，而無需創建新 VM。 通過在現有 VM 上啟用超磁片，然後將它們附加到資料磁片。

- 導航到 VM 並選擇**磁片**。
- 選取 [編輯]****。

![選項-選擇器-超磁片.png](media/virtual-machines-disks-getting-started-ultra-ssd/options-selector-ultra-disks.png)

- 選擇 **"是****"以啟用超磁片相容性**。

![超選項-是啟用.png](media/virtual-machines-disks-getting-started-ultra-ssd/ultra-options-yes-enable.png)

- 選取 [儲存]****。
- 選擇 **"添加資料磁片**"，然後在**名稱**選擇"**創建磁片**"的下拉下清單中選擇。

![創建和附加-新超磁片.png](media/virtual-machines-disks-getting-started-ultra-ssd/create-and-attach-new-ultra-disk.png)

- 填寫新磁片的名稱，然後選擇 **"更改大小**"。
- 將**帳戶類型**更改為**超級磁片**。
- 將**自訂磁片大小 （GiB）**、**磁片 IOPS**和**磁片輸送量**的值更改為您選擇的值。
- 選擇 **"確定"，** 然後選擇 **"創建**"。

![製作新超磁片.png](media/virtual-machines-disks-getting-started-ultra-ssd/making-a-new-ultra-disk.png)

- 返回到磁片的邊欄選項卡後，選擇 **"保存**"。

![保存和附加-新超磁片.png](media/virtual-machines-disks-getting-started-ultra-ssd/saving-and-attaching-new-ultra-disk.png)

### <a name="adjust-the-performance-of-an-ultra-disk-using-the-azure-portal"></a>使用 Azure 門戶調整超磁片的性能

超磁片具有獨特的功能，允許您調整其性能。 可以從 Azure 門戶、磁片本身進行這些調整。

- 導航到 VM 並選擇**磁片**。
- 選擇要修改性能的超磁片。

![選擇超磁片到修改.png](media/virtual-machines-disks-getting-started-ultra-ssd/selecting-ultra-disk-to-modify.png)

- 選擇 **"配置**"，然後進行修改。
- 選取 [儲存]****。

![配置超磁片性能和大小.](media/virtual-machines-disks-getting-started-ultra-ssd/configuring-ultra-disk-performance-and-size.png)

## <a name="deploy-an-ultra-disk-using-cli"></a>使用 CLI 部署超磁片

首先，確定要部署的 VM 大小。 有關支援的 VM 大小的清單，請參閱[GA 範圍和限制](#ga-scope-and-limitations)部分。

您必須創建能夠使用超磁片的 VM，以便附加超磁片。

用您自己的值替換或設置 **$vmname**$vmname、$rgname、$diskname、$location、$password、$user變數。 **$rgname** **$diskname** **$location** **$password** **$user** $zone**集**到[從本文開頭](#determine-vm-size-and-region-availability)獲得的可用性區域的值。 然後運行以下 CLI 命令以創建啟用的超啟用 VM：

```azurecli-interactive
az vm create --subscription $subscription -n $vmname -g $rgname --image Win2016Datacenter --ultra-ssd-enabled true --zone $zone --authentication-type password --admin-password $password --admin-username $user --size Standard_D4s_v3 --location $location
```

### <a name="create-an-ultra-disk-using-cli"></a>使用 CLI 創建超磁片

現在，您擁有了能夠附加超磁片的 VM，您可以創建超級磁片並將其附加到它。

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

## <a name="attach-an-ultra-disk-to-a-vm-using-cli"></a>使用 CLI 將超磁片連接到 VM

或者，如果現有 VM 位於能夠使用超磁片的區域/可用性區域中，則可以使用超磁片，而無需創建新 VM。

```azurecli
$rgName = "<yourResourceGroupName>"
$vmName = "<yourVMName>"
$diskName = "<yourDiskName>"
$subscriptionId = "<yourSubscriptionID>"

az vm disk attach -g $rgName --vm-name $vmName --disk $diskName --subscription $subscriptionId
```

### <a name="adjust-the-performance-of-an-ultra-disk-using-cli"></a>使用 CLI 調整超磁片的性能

Ultra 磁片提供了獨特的功能，允許您調整其性能，以下命令描述了如何使用此功能：

```azurecli-interactive
az disk update `
--subscription $subscription `
--resource-group $rgname `
--name $diskName `
--set diskIopsReadWrite=80000 `
--set diskMbpsReadWrite=800
```

## <a name="deploy-an-ultra-disk-using-powershell"></a>使用 PowerShell 部署超磁片

首先，確定要部署的 VM 大小。 有關支援的 VM 大小的清單，請參閱[GA 範圍和限制](#ga-scope-and-limitations)部分。

要使用超磁片，必須創建能夠使用超磁片的 VM。 用您自己的值替換或設置 **$resourcegroup$vmName**變數。 **$resourcegroup** $zone**集**到[從本文開頭](#determine-vm-size-and-region-availability)獲得的可用性區域的值。 然後運行以下[New-AzVm](/powershell/module/az.compute/new-azvm)命令以創建啟用的超啟用 VM：

```powershell
New-AzVm `
    -ResourceGroupName $resourcegroup `
    -Name $vmName `
    -Location "eastus2" `
    -Image "Win2016Datacenter" `
    -EnableUltraSSD `
    -size "Standard_D4s_v3" `
    -zone $zone
```

### <a name="create-an-ultra-disk-using-powershell"></a>使用 PowerShell 創建超磁片

現在，您擁有了能夠使用超磁片的 VM，您可以創建超級磁片並將其附加到它：

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

## <a name="attach-an-ultra-disk-to-a-vm-using-powershell"></a>使用 PowerShell 將超磁片連接到 VM

或者，如果現有 VM 位於能夠使用超磁片的區域/可用性區域中，則可以使用超磁片，而無需創建新 VM。

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

### <a name="adjust-the-performance-of-an-ultra-disk-using-powershell"></a>使用 PowerShell 調整超磁片的性能

Ultra 磁片具有獨特的功能，允許您調整其性能，以下命令是一個無需分離磁片即可調整性能的示例：

```powershell
$diskupdateconfig = New-AzDiskUpdateConfig -DiskMBpsReadWrite 2000
Update-AzDisk -ResourceGroupName $resourceGroup -DiskName $diskName -DiskUpdate $diskupdateconfig
```

## <a name="next-steps"></a>後續步驟

如果要嘗試新的磁片類型[請求訪問使用此調查](https://aka.ms/UltraDiskSignup)。
