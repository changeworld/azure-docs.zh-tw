---
title: 包含檔案
description: 包含檔案
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 07/08/2019
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: d848b92da5d4181832adff8499b3531d020c30c9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78155389"
---
臨時 OS 磁片是在本地虛擬機器 （VM） 存儲上創建的，不會保存到遠端 Azure 存儲。 臨時 OS 磁片適用于無狀態工作負載，其中應用程式能夠容忍單個 VM 故障，但受 VM 部署時間或重新映射單個 VM 實例的影響更大。 使用臨時作業系統磁片，您可以降低作業系統磁片的讀取/寫入延遲，並更快地重新映射 VM。 
 
臨時磁片的主要功能包括： 
- 非常適合無狀態應用。
- 它們既可與應用商店映射和自訂映射一起使用，也可用於應用商店和自訂映射。
- 能夠快速重置或重新映射 VM 並將設置實例縮放到原始啟動狀態。  
- 更低的延遲，類似于臨時磁片。 
- 臨時 OS 磁片是免費的，您無需支付 OS 磁片的存儲成本。
- 它們在所有 Azure 區域都可用。 
- [共用映射庫](/azure/virtual-machines/linux/shared-image-galleries)支援臨時 OS 磁片。 
 

 
持久性 OS 磁片和臨時 OS 磁片之間的主要區別：

|                             | 持久性作業系統磁片                          | 暫時性 OS 磁碟                              |    |
|-----------------------------|---------------------------------------------|------------------------------------------------|
| OS 磁片的大小限制      | 2 TiB                                                                                        | VM 大小或 2TiB 的緩存大小（以較小者為准）。 有關**GiB 中的緩存大小**，請參閱 DS、ES、M、FS 和[GS](/azure/virtual-machines/linux/sizes-previous-gen#gs-series) [DS](../articles/virtual-machines/linux/sizes-general.md) [ES](../articles/virtual-machines/linux/sizes-memory.md) [M](../articles/virtual-machines/linux/sizes-memory.md) [FS](../articles/virtual-machines/linux/sizes-compute.md)              |
| 支援 VM 大小          | 全部                                                                                          | DSv1、 DSv2、 DSv3、 Esv3、 Fs、 FsV2、 GS、 M                                               |
| 磁片類型支援           | 託管和非託管 OS 磁片                                                                | 僅限託管 OS 磁片                                                               |
| 區域支援              | 所有區域                                                                                  | 所有區域                              |
| 資料持續性            | 寫入 OS 磁片的 OS 磁片資料存儲在 Azure 存儲中                                  | 寫入 OS 磁片的資料將存儲到本地 VM 存儲，並且不會保存到 Azure 存儲。 |
| 停止交易狀態      | VM 和縮放集實例可以停止交易，並從停止交易位置狀態重新開機 | VM 和縮放集實例不能停止交易                                  |
| 專用 OS 磁片支援 | 是                                                                                          | 否                                                                                 |
| 作業系統磁片調整大小              | 在 VM 創建期間和 VM 停止交易後受支援                                | 僅在 VM 創建期間受支援                                                  |
| 調整大小以新的 VM 大小   | 保留 OS 磁片資料                                                                    | 作業系統磁片上的資料被刪除，作業系統被重新預配                                      |

## <a name="size-requirements"></a>尺寸要求

您可以部署 VM 和實例映射，以達 VM 緩存的大小。 例如，來自市場的標準 Windows 伺服器映射約為 127 GiB，這意味著您需要緩存大於 127 GiB 的 VM 大小。 在這種情況下[，Standard_DS2_v2](~/articles/virtual-machines/dv2-dsv2-series.md)的緩存大小為 86 GiB，這不夠大。 Standard_DS3_v2的緩存大小為 172 GiB，足夠大。 在這種情況下，Standard_DS3_v2是 DSv2 系列中可用於此圖像的最小大小。 應用商店和 Windows 伺服器映射中的基本 Linux 映射（表示`[smallsize]`形式）通常約為 30 GiB，可以使用大多數可用的 VM 大小。

臨時磁片還要求 VM 大小支援高級存儲。 大小通常（但並非總是）的名稱有 a，`s`如 DSv2 和 EsV3。 有關詳細資訊，請參閱[Azure VM 大小](../articles/virtual-machines/linux/sizes.md)，瞭解有關支援高級存儲的大小的詳細資訊。

## <a name="powershell"></a>PowerShell

要將臨時磁片用於 PowerShell VM 部署，請使用 VM 配置中的[Set-AzVMOSDisk。](/powershell/module/az.compute/set-azvmosdisk) 將`-DiffDiskSetting`和`Local``-Caching`設置為`ReadOnly`和 設置為 。     

```powershell
Set-AzVMOSDisk -DiffDiskSetting Local -Caching ReadOnly
```

對於規模集部署，請使用配置中的["設置-AzVms存儲設定檔](/powershell/module/az.compute/set-azvmssstorageprofile)"Cmdlet。 將`-DiffDiskSetting`和`Local``-Caching`設置為`ReadOnly`和 設置為 。


```powershell
Set-AzVmssStorageProfile -DiffDiskSetting Local -OsDiskCaching ReadOnly
```

## <a name="cli"></a>CLI

要對 CLI VM 部署使用臨時磁片，請將 az `--ephemeral-os-disk` [vm 中的](/cli/azure/vm#az-vm-create)參數設置為`true`，`--os-disk-caching`並將參數`ReadOnly`設置為 。

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

對於比例集，對`--ephemeral-os-disk true`[az-vms 創建](/cli/azure/vmss#az-vmss-create)使用相同的參數，`--os-disk-caching`並將參數設置為`ReadOnly`。

## <a name="portal"></a>入口網站   

在 Azure 門戶中，可以通過打開 **"磁片"** 選項卡的 **"高級**"部分，選擇在部署 VM 時使用臨時磁片。對於**使用臨時 OS 磁片**，請選擇 **"是**"。

![顯示選擇使用臨時 OS 磁片的選項按鈕的螢幕截圖](./media/virtual-machines-common-ephemeral/ephemeral-portal.png)

如果使用臨時磁片的選項已灰顯，則可能選擇了緩存大小不大於 OS 映射或不支援高級存儲的 VM 大小。 返回 **"基本"** 頁並嘗試選擇另一個 VM 大小。

您還可以使用門戶使用臨時 OS 磁片創建比例集。 只需確保選擇具有足夠大緩存大小的 VM 大小，然後在**使用臨時 OS 磁片**中選擇 **"是**"。

![顯示用於選擇為規模集使用臨時 OS 磁片的選項按鈕的螢幕截圖](./media/virtual-machines-common-ephemeral/scale-set.png)

## <a name="scale-set-template-deployment"></a>縮放集範本部署  
創建使用臨時 OS 磁片的比例集的過程是將`diffDiskSettings`屬性添加到範本中的`Microsoft.Compute/virtualMachineScaleSets/virtualMachineProfile`資源類型。 此外，必須為`ReadOnly`臨時 OS 磁片設置緩存策略。 


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
您可以使用範本使用臨時 OS 磁片部署 VM。 創建使用臨時 OS 磁片的 VM 的過程是將`diffDiskSettings`屬性添加到範本中的 Microsoft.Compute/虛擬機器資源類型。 此外，必須為`ReadOnly`臨時 OS 磁片設置緩存策略。 

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


## <a name="reimage-a-vm-using-rest"></a>使用 REST 重新映射 VM
您可以使用 REST API 使用臨時 OS 磁片重新映射虛擬機器實例，如下所述，並通過 Azure 門戶訪問 VM 的"概述"窗格。 對於縮放集，可以通過 Powershell、CLI 和門戶提供重新映射。

```
POST https://management.azure.com/subscriptions/{sub-
id}/resourceGroups/{rgName}/providers/Microsoft.Compute/VirtualMachines/{vmName}/reimage?a pi-version=2018-06-01" 
```
 
## <a name="frequently-asked-questions"></a>常見問題集

**問：本地 OS 磁片的大小是多少？**

答：我們支援平臺和自訂映射，在 VM 緩存大小前，作業系統磁片的所有讀取/寫入都將與虛擬機器在同一節點上本地。 

**問：臨時 OS 磁片是否可以調整大小？**

答：否，一旦預配臨時 OS 磁片，作業系統磁片將無法調整大小。 

**問：是否可以將託管磁片附加到臨時 VM？**

答：是的，您可以將託管資料磁片附加到使用臨時 OS 磁片的 VM。 

**問：臨時 OS 磁片是否會支援所有 VM 大小？**

答：否，除 B 系列、N 系列和 H 系列尺寸外，所有高級存儲 VM 大小都受支援（DS、ES、FS、GS 和 M）。  
 
**問：臨時作業系統磁片是否可以應用於現有 VM 和縮放集？**

答：沒有，臨時 OS 磁片只能在 VM 和縮放集創建期間使用。 

**問：是否可以將臨時磁片和普通 OS 磁片混合在比例集中？**

答：不，不能在同一規模集中混合臨時性和持久性 OS 磁片實例。 

**問：是否可以使用 Powershell 或 CLI 創建臨時 OS 磁片？**

答：是的，您可以使用 REST、範本、PowerShell 和 CLI 使用臨時作業系統磁片創建 VM。

**問：臨時 OS 磁片不支援哪些功能？**

答：臨時磁片不支援：
- 捕獲 VM 映射
- 磁碟快照集 
- Azure 磁碟加密 
- Azure 備份
- Azure Site Recovery  
- 作業系統磁片交換 
