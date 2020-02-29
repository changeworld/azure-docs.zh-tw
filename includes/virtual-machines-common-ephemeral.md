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
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/28/2020
ms.locfileid: "78155389"
---
暫時 OS 磁片會建立在本機虛擬機器（VM）存放區上，而不會儲存至遠端 Azure 儲存體。 暫時 OS 磁片適用于無狀態工作負載，其中應用程式可容忍個別 VM 失敗，但會受到 VM 部署時間的影響，或重新製作個別 VM 實例的映射。 有了暫時的 OS 磁片，您可以取得 OS 磁片的讀取/寫入延遲較低，並加快 VM 重新安裝映射的速度。 
 
暫時磁片的主要功能如下： 
- 適用于無狀態應用程式。
- 它們可以同時用於 Marketplace 和自訂映射。
- 能夠將 Vm 和擴展集實例快速重設或重新安裝為原始開機狀態。  
- 延遲較低，類似于暫存磁片。 
- 暫時的 OS 磁片是免費的，作業系統磁片不會產生任何儲存成本。
- 它們會在所有 Azure 區域中提供。 
- [共用映射資源庫](/azure/virtual-machines/linux/shared-image-galleries)支援暫時 OS 磁片。 
 

 
持續性和暫時 OS 磁片之間的主要差異：

|                             | 持續性作業系統磁片                          | 暫時性 OS 磁碟                              |    |
|-----------------------------|---------------------------------------------|------------------------------------------------|
| OS 磁片的大小限制      | 2 TiB                                                                                        | VM 大小或2TiB 的快取大小，以較小者為准。 如需**GiB 中**的快取大小，請參閱[DS](../articles/virtual-machines/linux/sizes-general.md)、 [ES](../articles/virtual-machines/linux/sizes-memory.md)、 [M](../articles/virtual-machines/linux/sizes-memory.md)、 [FS](../articles/virtual-machines/linux/sizes-compute.md)和[GS](/azure/virtual-machines/linux/sizes-previous-gen#gs-series)              |
| 支援的 VM 大小          | 全部                                                                                          | DSv1，DSv2，DSv3，Esv3，Fs，FsV2，GS，M                                               |
| 磁片類型支援           | 受控和非受控 OS 磁片                                                                | 僅限受控 OS 磁片                                                               |
| 區域支援              | 所有區域                                                                                  | 所有區域                              |
| 資料持續性            | 寫入 OS 磁片的 OS 磁片資料會儲存在 Azure 儲存體                                  | 寫入 OS 磁片的資料會儲存至本機 VM 儲存體，而且不會保存到 Azure 儲存體。 |
| 停止-解除配置狀態      | Vm 和擴展集實例可以停止-解除配置，並從停止解除配置的狀態重新開機 | 無法停止/解除配置 Vm 和擴展集實例                                  |
| 特殊 OS 磁片支援 | 是                                                                                          | 否                                                                                 |
| OS 磁片大小調整              | 在建立 VM 期間，以及在停止配置 VM 之後支援                                | 僅在 VM 建立期間支援                                                  |
| 調整為新 VM 大小   | 保留 OS 磁片資料                                                                    | 作業系統磁片上的資料已刪除，作業系統已重新布建                                      |

## <a name="size-requirements"></a>大小需求

您可以將 VM 和實例映射部署到 VM 快取的大小。 例如，來自 marketplace 的標準 Windows Server 映射大約是 127 GiB，這表示您需要的 VM 大小必須大於 127 GiB 的快取。 在此情況下， [Standard_DS2_v2](~/articles/virtual-machines/dv2-dsv2-series.md)的快取大小為 86 GiB，這不夠大。 Standard_DS3_v2 的快取大小為 172 GiB，這夠大。 在此情況下，Standard_DS3_v2 是可與此影像搭配使用之 DSv2 系列中的最小大小。 Marketplace 中的基本 Linux 映射和以 `[smallsize]` 表示的 Windows Server 映射通常約為 30 GiB，而且可以使用大部分可用的 VM 大小。

暫時磁片也需要 VM 大小支援 Premium 儲存體。 通常（但不一定）的大小會有 `s` 的名稱，例如 DSv2 和 EsV3。 如需詳細資訊，請參閱[AZURE VM 大小](../articles/virtual-machines/linux/sizes.md)，以取得支援高階儲存體的大小詳細資料。

## <a name="powershell"></a>PowerShell

若要使用暫時磁片進行 PowerShell VM 部署，請在您的 VM 設定中使用[set-azvmosdisk](/powershell/module/az.compute/set-azvmosdisk) 。 將 `-DiffDiskSetting` 設定為 `Local`，並 `-Caching` `ReadOnly`。     

```powershell
Set-AzVMOSDisk -DiffDiskSetting Local -Caching ReadOnly
```

針對擴展集部署，請在您的設定中使用[AzVmssStorageProfile](/powershell/module/az.compute/set-azvmssstorageprofile)指令程式。 將 `-DiffDiskSetting` 設定為 `Local`，並 `-Caching` `ReadOnly`。


```powershell
Set-AzVmssStorageProfile -DiffDiskSetting Local -OsDiskCaching ReadOnly
```

## <a name="cli"></a>CLI

若要使用暫時磁片進行 CLI VM 部署，請將[az VM create](/cli/azure/vm#az-vm-create)中的 `--ephemeral-os-disk` 參數設定為 `true`，並將 `--os-disk-caching` 參數設為 `ReadOnly`。

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

針對擴展集，您可以針對[az-vmss-create](/cli/azure/vmss#az-vmss-create)使用相同的 `--ephemeral-os-disk true` 參數，並將 `--os-disk-caching` 參數設定為 `ReadOnly`。

## <a name="portal"></a>入口網站   

在 Azure 入口網站中，您可以在部署 VM 時選擇使用暫時磁片，方法是開啟 [**磁片**] 索引標籤的 [ **Advanced** ] 區段。針對 [**使用暫時 OS 磁片**]，選取 **[是]** 。

![顯示選擇使用暫時 OS 磁片之選項按鈕的螢幕擷取畫面](./media/virtual-machines-common-ephemeral/ephemeral-portal.png)

如果使用暫時磁片的選項呈現灰色，表示您所選取的 VM 大小沒有大於 OS 映射或不支援高階儲存體的快取大小。 返回 [**基本**] 頁面，然後嘗試選擇另一個 VM 大小。

您也可以使用入口網站來建立具有暫時 OS 磁片的擴展集。 請確定您選取的 VM 大小有足夠的快取大小，然後在 [**使用暫時的 OS 磁片**] 中選取 **[是]** 。

![顯示選項按鈕的螢幕擷取畫面，供您選擇使用擴展集的暫時 OS 磁片](./media/virtual-machines-common-ephemeral/scale-set.png)

## <a name="scale-set-template-deployment"></a>擴展集範本部署  
建立使用暫時 OS 磁片之擴展集的程式，是將 `diffDiskSettings` 屬性新增至範本中的 `Microsoft.Compute/virtualMachineScaleSets/virtualMachineProfile` 資源類型。 此外，快取原則必須設定為暫時 OS 磁片的 `ReadOnly`。 


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
您可以使用範本來部署具有暫時 OS 磁片的 VM。 建立使用暫時 OS 磁片之 VM 的程式，是將 `diffDiskSettings` 屬性新增至範本中的 virtualMachines 資源類型。 此外，快取原則必須設定為暫時 OS 磁片的 `ReadOnly`。 

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


## <a name="reimage-a-vm-using-rest"></a>使用 REST 重新製作 VM 的映射
您可以在 VM 的 [總覽] 窗格中，使用 REST API，如下所述，透過 Azure 入口網站重新安裝具有暫時 OS 磁片的虛擬機器實例的映射。 針對擴展集，您已可透過 Powershell、CLI 和入口網站使用重新安裝映射。

```
POST https://management.azure.com/subscriptions/{sub-
id}/resourceGroups/{rgName}/providers/Microsoft.Compute/VirtualMachines/{vmName}/reimage?a pi-version=2018-06-01" 
```
 
## <a name="frequently-asked-questions"></a>常見問題集

**問：本機 OS 磁片的大小為何？**

答：我們支援平臺和自訂映射，最高可達 VM 快取大小，其中 OS 磁片的所有讀取/寫入都會與虛擬機器位於相同節點上的本機位置。 

**問：是否可以重設暫時的 OS 磁片大小？**

答：否，一旦布建暫時 OS 磁片之後，就無法調整 OS 磁片的大小。 

**問：我可以將受控磁碟附加至暫時 VM 嗎？**

答：是的，您可以將受控資料磁片連結至使用暫時 OS 磁片的 VM。 

**問：是否支援暫時 OS 磁片的所有 VM 大小？**

答：否，除了 B 系列、N 系列和 H 系列大小以外，所有進階儲存體的 VM 大小都受到支援（DS、ES、FS、GS 和 M）。  
 
**問：暫時 OS 磁片可以套用至現有的 Vm 和擴展集嗎？**

答：否，暫時 OS 磁片只能在 VM 和擴展集建立期間使用。 

**問：您可以在擴展集中混用暫時和正常的 OS 磁片嗎？**

答：否，您不能在相同的擴展集內混用暫時和持續的 OS 磁片實例。 

**問：是否可以使用 Powershell 或 CLI 建立暫時的 OS 磁片？**

答：是的，您可以使用 REST、範本、PowerShell 和 CLI 來建立具有暫時 OS 磁片的 Vm。

**問：暫時 OS 磁片不支援哪些功能？**

答：暫時磁片不支援：
- 捕獲 VM 映射
- 磁碟快照集 
- Azure 磁碟加密 
- Azure 備份
- Azure Site Recovery  
- OS 磁片交換 
