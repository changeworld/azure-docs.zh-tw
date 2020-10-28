---
title: 使用自訂的相應縮小原則搭配 Azure 虛擬機器擴展集
description: 瞭解如何搭配使用自動調整規模設定的 Azure 虛擬機器擴展集，來使用自訂的相應縮小原則來管理實例計數
services: virtual-machine-scale-sets
author: ju-shim
ms.author: jushiman
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: autoscale
ms.date: 02/26/2020
ms.reviewer: avverma
ms.custom: avverma, devx-track-azurecli
ms.openlocfilehash: 9ca6310705d54d563aae746ab2dbfe6cb412e6a9
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/27/2020
ms.locfileid: "92747805"
---
# <a name="use-custom-scale-in-policies-with-azure-virtual-machine-scale-sets"></a>使用自訂的相應縮小原則搭配 Azure 虛擬機器擴展集

虛擬機器擴展集部署可以根據計量陣列進行相應放大或相應縮小，包括平臺和使用者定義的自訂計量。 當相應放大根據擴展集模型建立新的虛擬機器時，相應縮小會影響執行中的虛擬機器，而該虛擬機器在擴展集工作負載演進時可能會有不同的設定和/或功能。 

相應縮小原則功能可讓使用者透過三個相應縮小設定，設定虛擬機器的相應縮小順序： 

1. Default
2. NewestVM
3. OldestVM

### <a name="default-scale-in-policy"></a>預設相應縮小原則

根據預設，虛擬機器擴展集會套用此原則，以決定將會相應縮小) 的 (。 使用 *預設* 原則時，會依下列順序選取要相應縮小的 vm：

1. 在可用性區域之間平衡虛擬機器 (如果擴展集部署在區域設定中) 
2. 平衡容錯網域之間的虛擬機器 (盡力) 
3. 刪除具有最高實例識別碼的虛擬機器

如果使用者只想要遵循預設排序，則不需要指定相應縮小原則。

請注意，跨可用性區域或容錯網域的平衡不會跨可用性區域或容錯網域移動實例。 藉由從不平衡的可用性區域或容錯網域刪除虛擬機器，直到虛擬機器的分配達到平衡，才能達成平衡。

### <a name="newestvm-scale-in-policy"></a>NewestVM 相應縮小原則

此原則會刪除擴展集中最新建立的虛擬機器，在跨可用性區域的 Vm 之間進行平衡， (進列區域部署) 。 啟用此原則需要在虛擬機器擴展集模型上進行設定變更。

### <a name="oldestvm-scale-in-policy"></a>OldestVM 相應縮小原則

此原則會刪除擴展集中最舊的已建立虛擬機器，並在跨可用性區域的 Vm 之間進行平衡， (進列區域部署) 。 啟用此原則需要在虛擬機器擴展集模型上進行設定變更。

## <a name="enabling-scale-in-policy"></a>啟用相應縮小原則

相應縮小原則會在虛擬機器擴展集模型中定義。 如上述各節所述，使用 ' NewestVM ' 和 ' OldestVM ' 原則時需要相應縮小原則定義。 如果擴展集模型中找不到任何擴充原則定義，虛擬機器擴展集就會自動使用「預設」相應縮小原則。 

您可以利用下列方式，在虛擬機器擴展集模型上定義相應縮小原則：

### <a name="azure-portal"></a>Azure 入口網站
 
下列步驟會在建立新的擴展集時定義相應縮小原則。 
 
1. 移至 **虛擬機器擴展集** 。
1. 選取 [ **+ 新增** ] 以建立新的擴展集。
1. 移至 [ **調整** ] 索引標籤。 
1. 找出相應 **縮小原則** 區段。
1. 從下拉式清單中選取相應縮小原則。
1. 當您完成建立新的擴展集時，請選取 [ **審核 + 建立** ] 按鈕。

### <a name="using-api"></a>使用 API

使用 API 2019-03-01 在虛擬機器擴展集上執行 PUT：

```
PUT
https://management.azure.com/subscriptions/<sub-id>/resourceGroups/<myRG>/providers/Microsoft.Compute/virtualMachineScaleSets/<myVMSS>?api-version=2019-03-01

{ 
"location": "<VMSS location>", 
    "properties": { 
        "scaleInPolicy": {  
            "rules": ["OldestVM"]  
        } 
    }    
} 
```
### <a name="azure-powershell"></a>Azure PowerShell

建立資源群組，然後建立新的擴展集，並將相應縮小原則設定為 *OldestVM* 。

```azurepowershell-interactive
New-AzResourceGroup -ResourceGroupName "myResourceGroup" -Location "<VMSS location>"
New-AzVmss `
  -ResourceGroupName "myResourceGroup" `
  -Location "<VMSS location>" `
  -VMScaleSetName "myScaleSet" `
  -ScaleInPolicy “OldestVM”
```

### <a name="azure-cli-20"></a>Azure CLI 2.0

下列範例會在建立新的擴展集時新增相應縮小原則。 首先，建立資源群組，然後建立新的擴展集，並將擴充原則設定為 *OldestVM* 。 

```azurecli-interactive
az group create --name <myResourceGroup> --location <VMSSLocation>
az vmss create \
  --resource-group <myResourceGroup> \
  --name <myVMScaleSet> \
  --image UbuntuLTS \
  --admin-username <azureuser> \
  --generate-ssh-keys \
  --scale-in-policy OldestVM
```

### <a name="using-template"></a>使用範本

在您的範本中，于 [屬性] 下新增下列內容：

```json
"scaleInPolicy": {  
      "rules": ["OldestVM"]  
}
```

上述區塊指定當透過自動調整或手動刪除) 來 (觸發相應縮小時，虛擬機器擴展集將會刪除區域平衡擴展集中最舊的 VM。

當虛擬機器擴展集未進列區域平衡時，擴展集會先刪除不平衡區域 (s) 的 Vm。 在不平衡區域中，擴展集將使用上面指定的相應縮小原則來判斷要縮減的 VM。 在此情況下，擴展集會在不平衡區域中選取要刪除之區域中最舊的 VM。

針對非區域的虛擬機器擴展集，此原則會選取橫跨整個擴展集的最舊 VM 以進行刪除。

使用上述相應縮小原則中的 ' NewestVM ' 時，會套用相同的進程。

## <a name="modifying-scale-in-policies"></a>修改相應縮小原則

修改相應縮小原則後，會遵循套用相應縮小原則的相同程式。 例如，如果在上述範例中，您想要將原則從 ' OldestVM ' 變更為 ' NewestVM '，您可以透過下列方式來執行此動作：

### <a name="azure-portal"></a>Azure 入口網站

您可以透過 Azure 入口網站修改現有擴展集的相應縮小原則。 
 
1. 在現有的虛擬機器擴展集中，從左側功能表中選取 [ **調整** ]。
1. 選取 [相應 **縮小原則** ] 索引標籤。
1. 從下拉式清單中選取相應縮小原則。
1. 完成之後，請選取 [儲存]  。 

### <a name="using-api"></a>使用 API

使用 API 2019-03-01 在虛擬機器擴展集上執行 PUT：

```
PUT
https://management.azure.com/subscriptions/<sub-id>/resourceGroups/<myRG>/providers/Microsoft.Compute/virtualMachineScaleSets/<myVMSS>?api-version=2019-03-01 

{ 
"location": "<VMSS location>", 
    "properties": { 
        "scaleInPolicy": {  
            "rules": ["NewestVM"]  
        } 
    }    
}
```
### <a name="azure-powershell"></a>Azure PowerShell

更新現有擴展集的相應縮小原則：

```azurepowershell-interactive
Update-AzVmss `
 -ResourceGroupName "myResourceGroup" `
 -VMScaleSetName "myScaleSet" `
 -ScaleInPolicy “OldestVM”
```

### <a name="azure-cli-20"></a>Azure CLI 2.0

以下是更新現有擴展集之相應縮小原則的範例： 

```azurecli-interactive
az vmss update \  
  --resource-group <myResourceGroup> \
  --name <myVMScaleSet> \
  --scale-in-policy OldestVM
```

### <a name="using-template"></a>使用範本

在您的範本中，于 [屬性] 底下修改範本，如下所示重新部署： 

```json
"scaleInPolicy": {  
      "rules": ["NewestVM"]  
} 
```

如果您決定將 ' NewestVM ' 變更為 ' Default ' 或 ' OldestVM '，將適用相同的程式

## <a name="instance-protection-and-scale-in-policy"></a>實例保護和相應縮小原則

虛擬機器擴展集提供兩種類型的 [實例保護](./virtual-machine-scale-sets-instance-protection.md#types-of-instance-protection)：

1. 防止相應縮小
2. 防止調整規模設定動作

無論套用的相應縮小原則為何，都不會透過相應縮小動作來刪除受保護的虛擬機器。 例如，如果 VM_0 (擴展集中的最舊 VM) 受到相應縮小保護，且擴展集已啟用「OldestVM」相應縮小原則，則 VM_0 不會被視為調整規模，即使是擴展集中最舊的 VM 也是一樣。 

無論擴展集上啟用的相應縮小原則為何，使用者隨時都可以手動刪除受保護的虛擬機器。 

## <a name="usage-examples"></a>使用範例 

下列範例示範當觸發相應縮小事件時，虛擬機器擴展集將如何選取要刪除的 Vm。 具有最高實例識別碼的虛擬機器會假設為擴展集中的最新 Vm，而具有最小實例識別碼的 Vm 則會假設為擴展集中最舊的 Vm。 

### <a name="oldestvm-scale-in-policy"></a>OldestVM 相應縮小原則

| 事件                 | 區域1中的實例識別碼  | 區域2中的實例識別碼  | 區域3中的實例識別碼  | 相應縮小選取範圍                                                                                                               |
|-----------------------|------------------------|------------------------|------------------------|----------------------------------------------------------------------------------------------------------------------------------|
| Initial               | 3、4、5、10            | 2、6、9、11            | 1、7、8                |                                                                                                                                  |
| 相應縮小              | 3、4、5、10            | **_2_* _、6、9、11      | 1、7、8                | 即使區域3具有最舊的 VM，還是可以選擇區域1和2。 從區域2刪除 VM2，因為它是該區域中最舊的 VM。   |
| 相應縮小              | _*_3_*_ 、4、5、10      | 6、9、11               | 1、7、8                | 即使區域3具有最舊的 VM，還是選擇區域1。 從區域1刪除 VM3，因為它是該區域中最舊的 VM。                  |
| 相應縮小              | 4、5、10               | 6、9、11               | _*_1_*_ 、7、8          | 區域已平衡。 刪除區域3中的 VM1，因為它是擴展集中最舊的 VM。                                               |
| 相應縮小              | _*_4_*_ 、5、10         | 6、9、11               | 7、8                   | 選擇區域1和區域2。 刪除區域1中的 VM4，因為這是在兩個區域中最舊的 VM。                              |
| 相應縮小              | 5、10                  | _*_6_*_ 、9、11         | 7、8                   | 即使區域1具有最舊的 VM，還是選擇區域2。 刪除區域1中的 VM6，因為它是該區域中最舊的 VM。                    |
| 相應縮小              | _*_5_*_ 、10            | 9、11                  | 7、8                   | 區域已平衡。 刪除區域1中的 VM5，因為它是擴展集中最舊的 VM。                                                |

針對非區域的虛擬機器擴展集，此原則會選取整個擴展集的最舊 VM 以進行刪除。 將略過任何「受保護」的 VM 以進行刪除。

### <a name="newestvm-scale-in-policy"></a>NewestVM 相應縮小原則

| 事件                 | 區域1中的實例識別碼  | 區域2中的實例識別碼  | 區域3中的實例識別碼  | 相應縮小選取範圍                                                                                                               |
|-----------------------|------------------------|------------------------|------------------------|----------------------------------------------------------------------------------------------------------------------------------|
| Initial               | 3、4、5、10            | 2、6、9、11            | 1、7、8                |                                                                                                                                  |
| 相應縮小              | 3、4、5、10            | 2、6、9、 _*_11_*_      | 1、7、8                | 在區域1和2之間選擇。 從區域2刪除 VM11，因為這是兩個區域中最新的 VM。                                |
| 相應縮小              | 3、4、5、 _*_10_*_      | 2、6、9                | 1、7、8                | 選擇區域1，因為它有比其他兩個區域更多的 Vm。 從區域1刪除 VM10，因為它是該區域中最新的 VM。          |
| 相應縮小              | 3、4、5                | 2、6、 _*_9_*_          | 1、7、8                | 區域已平衡。 刪除區域2中的且 PG2，因為它是擴展集中的最新 VM。                                                |
| 相應縮小              | 3、4、5                | 2，6                   | 1、7、 _*_8_*_          | 選擇區域1和區域3。 刪除區域3中的 VM8，因為它是該區域中最新的 VM。                                      |
| 相應縮小              | 3、4、 _*_5_*_          | 2，6                   | 1, 7                   | 即使區域3具有最新的 VM，還是選擇區域1。 刪除區域1中的 VM5，因為它是該區域中最新的 VM。                    |
| 相應縮小              | 3、4                   | 2，6                   | 1，_ *_7_**             | 區域已平衡。 刪除區域3中的 VM7，因為它是擴展集中的最新 VM。                                                |

針對非區域的虛擬機器擴展集，此原則會選取整個擴展集的最新 VM 以進行刪除。 將略過任何「受保護」的 VM 以進行刪除。 

## <a name="troubleshoot"></a>疑難排解

1. 如果您收到 ' BadRequest ' 錯誤，並出現錯誤訊息指出「在類型 ' properties ' 的物件上找不到成員 ' scaleInPolicy '」，則無法啟用 scaleInPolicy，然後檢查虛擬機器擴展集所使用的 API 版本。 這項功能需要 API 版本2019-03-01 或更高版本。

2. 針對相應縮小選取的 Vm 錯誤，請參閱上述範例。 如果您的虛擬機器擴展集是一個區域部署，則會先將相應縮小原則套用至不平衡區域，然後在整個擴展集進列區域平衡。 如果相應縮小順序與上述範例不一致，請向虛擬機器擴展集小組提出查詢，以進行疑難排解。

## <a name="next-steps"></a>後續步驟

了解如何在虛擬機器擴展集上[部署您的應用程式](virtual-machine-scale-sets-deploy-app.md)。
