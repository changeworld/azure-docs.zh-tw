---
title: 暫時性 OS 磁碟
description: 深入瞭解 Azure Vm 的暫時作業系統磁片。
author: cynthn
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 07/23/2020
ms.author: cynthn
ms.subservice: disks
ms.openlocfilehash: a79a030c4f57c3dabdd14c01aa2062cab7026cd3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91611515"
---
# <a name="ephemeral-os-disks-for-azure-vms"></a>Azure Vm 的暫時作業系統磁片

暫時 OS 磁片會在本機虛擬機器上建立 (VM) 儲存體，而不會儲存到遠端 Azure 儲存體。 暫時 OS 磁片適用于無狀態工作負載，其中應用程式可容忍個別 VM 失敗，但會受到 VM 部署時間的影響，或重新安裝個別 VM 實例的映射。 使用暫時性 OS 磁片時，您會獲得較低的 OS 磁片讀取/寫入延遲，並加快 VM 重新安裝映射的速度。 
 
暫時磁片的主要功能如下： 
- 適用于無狀態應用程式。
- 它們可搭配 Marketplace 和自訂映射使用。
- 能夠將 Vm 和擴展集實例快速重設或重新安裝映射，使其成為原始開機狀態。  
- 延遲較低，類似于暫存磁片。 
- 暫時 OS 磁片是免費的，您不會因為 OS 磁片而產生任何儲存體成本。
- 這些都可在所有 Azure 區域中使用。 
- [共用映射庫](./linux/shared-image-galleries.md)支援暫時 OS 磁片。 
 

 
持續性和暫時性 OS 磁片之間的主要差異：

|                             | 持續性 OS 磁片                          | 暫時性 OS 磁碟                              |
|-----------------------------|---------------------------------------------|------------------------------------------------|
| **OS 磁片的大小限制**      | 2 TiB                                                                                        | VM 大小或2TiB 的快取大小，以較小者為准。 如需 **GiB 中**的快取大小，請參閱 [DS](sizes-general.md)、 [ES](sizes-memory.md)、 [M](sizes-memory.md)、 [FS](sizes-compute.md)和 [GS](sizes-previous-gen.md#gs-series)              |
| **支援的 VM 大小**          | 全部                                                                                          | 支援 Premium 儲存體的 VM 大小，例如 DSv1、DSv2、DSv3、Esv3、Fs、FsV2、GS、M                                               |
| **磁片類型支援**           | 受控和非受控 OS 磁片                                                                | 僅限受控 OS 磁片                                                               |
| **區域支援**              | 所有區域                                                                                  | 所有區域                              |
| **資料持續性**            | 寫入 OS 磁片的 OS 磁片資料會儲存在 Azure 儲存體                                  | 寫入 OS 磁片的資料會儲存在本機 VM 儲存體中，且不會保存至 Azure 儲存體。 |
| **停止-解除配置狀態**      | Vm 和擴展集實例可以停止/解除配置，並從停止配置的狀態重新開機 | Vm 和擴展集實例無法停止-解除配置                                  |
| **特製化 OS 磁片支援** | 是                                                                                          | 否                                                                                 |
| **OS 磁片調整大小**              | 在 VM 建立期間以及在 VM 停止後解除配置時支援                                | 僅在 VM 建立期間支援                                                  |
| **調整為新的 VM 大小**   | 保留作業系統磁片資料                                                                    | 作業系統磁片上的資料已刪除，作業系統已重新布建                                      |

## <a name="size-requirements"></a>大小需求

您可以將 VM 和實例映射部署到 VM 快取的大小。 例如，來自 marketplace 的標準 Windows Server 映射大約是 127 GiB，這表示您需要的虛擬機器大小必須大於 127 GiB。 在此情況下， [Standard_DS2_v2](dv2-dsv2-series.md) 的快取大小為 86 GiB，但不夠大。 Standard_DS3_v2 的快取大小為 172 GiB，夠大。 在此情況下，Standard_DS3_v2 是可用於此映射的 DSv2 系列中最小的大小。 市集中的基本 Linux 映射以及表示的 Windows Server 映射， `[smallsize]` 通常約為 30 GiB，而且可以使用大部分可用的 VM 大小。

暫時磁片也需要 VM 大小支援 Premium 儲存體。 大小通常 (但不一定) 具有 `s` 名稱，例如 DSv2 和 EsV3。 如需詳細資訊，請參閱 [AZURE VM 大小](sizes.md) 以取得支援 Premium 儲存體的大小詳細資料。

## <a name="preview---ephemeral-os-disks-can-now-be-stored-on-temp-disks"></a>預覽-暫時作業系統磁片現在可以儲存在暫存磁片上
暫時 OS 磁片除了 VM 快取之外，現在還可以儲存在 VM 暫存/資源磁片上。 因此，現在您可以搭配沒有快取或沒有快取的 VM 使用暫時性 OS 磁片，但具有暫存/資源磁片來儲存暫時的 OS 磁片，例如 Dav3、Dav4、Eav4 和 Eav3。 如果 VM 有足夠的快取和暫存空間，您現在也可以使用名為 [DiffDiskPlacement](/rest/api/compute/virtualmachines/list#diffdiskplacement)的新屬性來指定您要儲存暫時 OS 磁片的位置。 使用這項功能時，在布建 Windows VM 時，我們會將分頁檔設定為位於 OS 磁片上。 此功能目前為預覽狀態。 此預覽版本是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 若要開始使用，請 [要求存取權](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR6cQw0fZJzdIsnbfbI13601URTBCRUZPMkQwWFlCOTRIMFBSNkM1NVpQQS4u)。

## <a name="powershell"></a>PowerShell

若要針對 PowerShell VM 部署使用暫時磁片，請在您的 VM 設定中使用 [>set-azvmosdisk](/powershell/module/az.compute/set-azvmosdisk) 。 將設定 `-DiffDiskSetting` 為 `Local` ，並將設定 `-Caching` 為 `ReadOnly` 。     

```powershell
Set-AzVMOSDisk -DiffDiskSetting Local -Caching ReadOnly
```

針對擴展集部署，請在您的設定中使用 [set-azvmosdisk set-azvmssstorageprofile](/powershell/module/az.compute/set-azvmssstorageprofile) Cmdlet。 將設定 `-DiffDiskSetting` 為 `Local` ，並將設定 `-Caching` 為 `ReadOnly` 。


```powershell
Set-AzVmssStorageProfile -DiffDiskSetting Local -OsDiskCaching ReadOnly
```

## <a name="cli"></a>CLI

若要使用暫時磁片進行 CLI VM 部署，請將 `--ephemeral-os-disk` [az vm create](/cli/azure/vm#az-vm-create) 中的參數設定為， `true` 並將參數設定 `--os-disk-caching` 為 `ReadOnly` 。

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --image UbuntuLTS \
  --ephemeral-os-disk true \
  --os-disk-caching ReadOnly \
  --admin-username azureuser \
  --generate-ssh-keys
```

針對擴展集，您可以 `--ephemeral-os-disk true` 針對 [az-vmss-create](/cli/azure/vmss#az-vmss-create) 使用相同的參數，並將 `--os-disk-caching` 參數設定為 `ReadOnly` 。

## <a name="portal"></a>入口網站   

在 Azure 入口網站中，您可以選擇在部署 VM 時使用暫時磁片，方法是開啟 [**磁片**] 索引標籤的 [ **Advanced** ] 區段。若是**使用暫時性 OS 磁片**，請選取 **[是]**。

![顯示選擇使用暫時性 OS 磁片之選項按鈕的螢幕擷取畫面](./media/virtual-machines-common-ephemeral/ephemeral-portal.png)

如果使用暫時磁片的選項呈現灰色，表示您可能選取的 VM 大小沒有大於 OS 映射的快取大小，或不支援 Premium 儲存體的 VM 大小。 返回到 [ **基本** ] 頁面，然後嘗試選擇其他 VM 大小。

您也可以使用入口網站來建立具有暫時 OS 磁片的擴展集。 只要確定您選取的 VM 大小具有夠大的快取大小，然後 **使用暫時性 OS 磁片** ，請選取 **[是]**。

![顯示選項按鈕的螢幕擷取畫面，可供選擇針對擴展集使用暫時的 OS 磁片](./media/virtual-machines-common-ephemeral/scale-set.png)

## <a name="scale-set-template-deployment"></a>擴展集範本部署  
建立使用暫時性 OS 磁片之擴展集的程式，是將 `diffDiskSettings` 屬性新增至 `Microsoft.Compute/virtualMachineScaleSets/virtualMachineProfile` 範本中的資源類型。 此外，還必須將快取原則設定為 `ReadOnly` 暫時 OS 磁片。 


```json
{ 
  "type": "Microsoft.Compute/virtualMachineScaleSets", 
  "name": "myScaleSet", 
  "location": "East US 2", 
  "apiVersion": "2018-06-01", 
  "sku": { 
    "name": "Standard_DS2_v2", 
    "capacity": "2" 
  }, 
  "properties": { 
    "upgradePolicy": { 
      "mode": "Automatic" 
    }, 
    "virtualMachineProfile": { 
       "storageProfile": { 
        "osDisk": { 
          "diffDiskSettings": { 
                "option": "Local" 
          }, 
          "caching": "ReadOnly", 
          "createOption": "FromImage" 
        }, 
        "imageReference":  { 
          "publisher": "Canonical", 
          "offer": "UbuntuServer", 
          "sku": "16.04-LTS", 
          "version": "latest" 
        } 
      }, 
      "osProfile": { 
        "computerNamePrefix": "myvmss", 
        "adminUsername": "azureuser", 
        "adminPassword": "P@ssw0rd!" 
      } 
    } 
  } 
}  
```

## <a name="vm-template-deployment"></a>VM 範本部署 
您可以使用範本部署具有暫時 OS 磁片的 VM。 建立使用暫時性 OS 磁片的 VM 的程式，是在 `diffDiskSettings` 範本中將屬性新增至 Microsoft. Compute/virtualMachines 資源類型。 此外，還必須將快取原則設定為 `ReadOnly` 暫時 OS 磁片。 

```json
{ 
  "type": "Microsoft.Compute/virtualMachines", 
  "name": "myVirtualMachine", 
  "location": "East US 2", 
  "apiVersion": "2018-06-01", 
  "properties": { 
       "storageProfile": { 
            "osDisk": { 
              "diffDiskSettings": { 
                "option": "Local" 
              }, 
              "caching": "ReadOnly", 
              "createOption": "FromImage" 
            }, 
            "imageReference": { 
                "publisher": "MicrosoftWindowsServer", 
                "offer": "WindowsServer", 
                "sku": "2016-Datacenter-smalldisk", 
                "version": "latest" 
            }, 
            "hardwareProfile": { 
                 "vmSize": "Standard_DS2_v2" 
             } 
      }, 
      "osProfile": { 
        "computerNamePrefix": "myvirtualmachine", 
        "adminUsername": "azureuser", 
        "adminPassword": "P@ssw0rd!" 
      } 
    } 
 } 
```


## <a name="reimage-a-vm-using-rest"></a>使用 REST 重新安裝 VM 映射
您可以使用 REST API （如下所述），並透過 Azure 入口網站，前往 VM 的 [總覽] 窗格，使用暫時的 OS 磁片重新安裝虛擬機器實例的映射。 針對擴展集，已可透過 Powershell、CLI 和入口網站使用重新安裝映射。

```
POST https://management.azure.com/subscriptions/{sub-
id}/resourceGroups/{rgName}/providers/Microsoft.Compute/VirtualMachines/{vmName}/reimage?a pi-version=2018-06-01" 
```
 
## <a name="frequently-asked-questions"></a>常見問題集

**問：本機 OS 磁片的大小為何？**

答：我們支援平臺和自訂映射，最多可達 VM 快取大小，在此情況下，OS 磁片的所有讀取/寫入都會在與虛擬機器相同的節點上。 

**問：是否可以調整暫時 OS 磁片的大小？**

答：否，布建暫時性 OS 磁片之後，便無法調整 OS 磁片的大小。 

**問：我可以將受控磁碟附加至暫時 VM 嗎？**

答：是的，您可以將受控資料磁片連接到使用暫時性 OS 磁片的 VM。 

**問：是否支援暫時 OS 磁片的所有 VM 大小？**

答：否，大部分進階儲存體 VM 大小都支援 (DS、ES、FS、GS、M 等 ) 。 若要知道特定的 VM 大小是否支援暫時 OS 磁片，您可以：

呼叫 `Get-AzComputeResourceSku` PowerShell Cmdlet
```azurepowershell-interactive
 
$vmSizes=Get-AzComputeResourceSku | where{$_.ResourceType -eq 'virtualMachines' -and $_.Locations.Contains('CentralUSEUAP')} 

foreach($vmSize in $vmSizes)
{
   foreach($capability in $vmSize.capabilities)
   {
       if($capability.Name -eq 'EphemeralOSDiskSupported' -and $capability.Value -eq 'true')
       {
           $vmSize
       }
   }
}
```
 
**問：是否可以將暫時 OS 磁片套用至現有的 Vm 和擴展集？**

答：不可以，只有在建立 VM 和擴展集時，才能使用暫時性 OS 磁片。 

**問：是否可以在擴展集中混用暫時性和一般作業系統磁片？**

答：否，您無法在相同擴展集內混用暫時性和持續性的 OS 磁片實例。 

**問：是否可以使用 Powershell 或 CLI 來建立暫時 OS 磁片？**

答：是的，您可以使用 REST、範本、PowerShell 和 CLI 來建立具有暫時性 OS 磁片的 Vm。

**問：暫時 OS 磁片不支援哪些功能？**

答：暫時磁片不支援：
- 捕獲 VM 映射
- 磁碟快照集 
- Azure 磁碟加密 
- Azure 備份
- Azure Site Recovery  
- OS 磁片交換 
 
## <a name="next-steps"></a>後續步驟
您可以使用 [Azure CLI](/cli/azure/vm#az-vm-create)建立具有暫時 OS 磁片的 VM。
