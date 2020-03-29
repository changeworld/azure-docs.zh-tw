---
title: 將自訂擴展策略與 Azure 虛擬機器縮放集一起使用
description: 瞭解如何將自訂擴展策略與使用自動縮放配置來管理實例計數的 Azure 虛擬機器縮放集一起使用
services: virtual-machine-scale-sets
author: avirishuv
manager: vashan
tags: azure-resource-manager
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm
ms.topic: conceptual
ms.date: 02/26/2020
ms.author: avverma
ms.openlocfilehash: ffcdaf76bdd08ee5505ddbeff6a6698e231b6171
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77919833"
---
# <a name="use-custom-scale-in-policies-with-azure-virtual-machine-scale-sets"></a>將自訂擴展策略與 Azure 虛擬機器縮放集一起使用

虛擬機器規模集部署可以基於一系列指標（包括平臺和使用者定義的自訂指標）進行橫向擴展或放大。 雖然橫向擴展基於規模集模型創建新的虛擬機器，但擴展影響正在運行的虛擬機器，這些虛擬機器可能具有不同的配置和/或功能，因為規模集工作負載會發生變化。 

擴展策略功能為使用者提供了一種通過三個擴展配置來配置虛擬機器的放大順序的方法： 

1. 預設
2. 最新VM
3. 最老的VM

### <a name="default-scale-in-policy"></a>預設放大縮小字體功能 放大縮小字體功能

預設情況下，虛擬機器規模集應用此策略來確定將縮放的實例。 使用 *"預設"* 策略，按以下順序選擇 VM 進行放大：

1. 跨可用性區域平衡虛擬機器（如果規模集部署在區域配置中）
2. 平衡跨容錯域的虛擬機器（盡最大努力）
3. 刪除具有最高實例 ID 的虛擬機器

如果使用者只想遵循預設順序，則無需指定擴展策略。

請注意，跨可用性區域或容錯域進行平衡不會跨可用性區域或容錯域移動實例。 平衡是通過從不平衡的可用性區域或容錯域中刪除虛擬機器來實現的，直到虛擬機器的分佈達到平衡。

### <a name="newestvm-scale-in-policy"></a>最新的 VM 擴展策略

此策略將在跨可用性區域（區域部署）平衡 VM後，刪除規模集中的最新創建的虛擬機器。 啟用此策略需要在虛擬機器規模集模型上更改配置。

### <a name="oldestvm-scale-in-policy"></a>最老的 VM 擴展策略

此策略將在跨可用性區域（區域部署）平衡 VM後，刪除規模集中創建的最舊的虛擬機器。 啟用此策略需要在虛擬機器規模集模型上更改配置。

## <a name="enabling-scale-in-policy"></a>啟用擴展策略

在虛擬機器規模集模型中定義了擴展策略。 如上節所述，使用"最新VM"和"最舊VM"策略時，需要擴展策略定義。 如果在規模集模型上找不到"擴展"策略定義，虛擬機器規模集將自動使用"預設"擴展策略。 

可以在虛擬機器規模集模型上通過以下方式定義擴展策略：

### <a name="azure-portal"></a>Azure 入口網站
 
以下步驟定義創建新比例集時的縮減策略。 
 
1. 轉到**虛擬機器縮放集**。
1. 選擇 **+ 添加**以創建新的比例集。
1. 轉到 **"縮放"** 選項卡。 
1. 查找 **"放大縮小字體功能 放大縮小字體功能**
1. 從下拉清單中選擇"放大"策略。
1. 創建新比例集後，選擇 **"查看 + 創建**"按鈕。

### <a name="using-api"></a>使用 API

使用 API 2019-03-01 在虛擬機器規模集中執行 PUT：

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

創建資源組，然後創建一個新規模集，其中放大縮小字體策略集為 *"最舊VM"。*

```azurepowershell-interactive
New-AzResourceGroup -ResourceGroupName "myResourceGroup" -Location "<VMSS location>"
New-AzVmss `
  -ResourceGroupName "myResourceGroup" `
  -Location "<VMSS location>" `
  -VMScaleSetName "myScaleSet" `
  -ScaleInPolicy “OldestVM”
```

### <a name="azure-cli-20"></a>Azure CLI 2.0

下面的示例在創建新的縮放集時添加擴展策略。 首先創建資源組，然後創建一個將擴展策略為 *"最舊VM"* 的新比例集。 

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

在範本中，在"屬性"下添加以下內容：

```json
"scaleInPolicy": {  
      "rules": ["OldestVM"]  
}
```

上述塊指定，虛擬機器縮放集將在觸發縮放時（通過自動縮放或手動刪除）在區域平衡比例集中刪除最舊的 VM。

當虛擬機器縮放集不平衡時，比例集將首先刪除不平衡區域的 VM。 在不平衡區域內，比例集將使用上面指定的擴展策略來確定要在哪個 VM 中擴展。 在這種情況下，在不平衡區域中，比例集將選擇要刪除的該區域中最早的 VM。

對於非區域虛擬機器規模集，策略選擇規模集中最舊的 VM 進行刪除。

在上述擴展策略中使用"最新 VM"時，也適用相同的過程。

## <a name="modifying-scale-in-policies"></a>修改放大縮小字體功能 放大縮小字體功能

修改縮減策略遵循與應用縮減策略相同的過程。 例如，如果在上面的示例中，您希望將策略從"最舊 VM"更改為"最新 VM"，則可以通過以下方式執行此操作：

### <a name="azure-portal"></a>Azure 入口網站

可以通過 Azure 門戶修改現有規模集的放大縮小字體功能 放大縮小字體功能 
 
1. 在現有虛擬機器縮放集中，從左側的功能表中選擇 **"縮放**"。
1. 選擇"**放大策略**"選項卡。
1. 從下拉清單中選擇"放大"策略。
1. 完成之後，請選取 [儲存]****。 

### <a name="using-api"></a>使用 API

使用 API 2019-03-01 在虛擬機器規模集中執行 PUT：

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

更新現有規模集的縮減策略：

```azurepowershell-interactive
Update-AzVmss `
 -ResourceGroupName "myResourceGroup" `
 -VMScaleSetName "myScaleSet" `
 -ScaleInPolicy “OldestVM”
```

### <a name="azure-cli-20"></a>Azure CLI 2.0

以下是更新現有規模集的縮減策略的示例： 

```azurecli-interactive
az vmss update \  
  --resource-group <myResourceGroup> \
  --name <myVMScaleSet> \
  --scale-in-policy OldestVM
```

### <a name="using-template"></a>使用範本

在範本中，在"屬性"下，修改範本如下並重新部署： 

```json
"scaleInPolicy": {  
      "rules": ["NewestVM"]  
} 
```

如果您決定將"最新 VM"更改為"預設"或"最舊VM"，則適用相同的流程

## <a name="instance-protection-and-scale-in-policy"></a>實例保護和擴展策略

虛擬機器規模集提供兩種類型的[實例保護](./virtual-machine-scale-sets-instance-protection.md#types-of-instance-protection)：

1. 防止放大
2. 防止縮放設置操作

無論應用了哪種擴展策略，都不會通過擴展操作刪除受保護的虛擬機器。 例如，如果VM_0（比例集中中最早的 VM）受到保護，不受擴展，並且規模集啟用了"最舊 VM"擴展策略，則VM_0將不會被視為在中擴展，即使它是比例集中中最早的 VM。 

無論在規模集中啟用的擴展策略如何，使用者都可以隨時手動刪除受保護的虛擬機器。 

## <a name="usage-examples"></a>使用範例 

以下示例演示如何在觸發規模放大事件時選擇要刪除的 VM。 具有最高實例指示的虛擬機器假定是比例集中的最新 VM，具有最小實例指示的 VM 假定是比例集中最舊的 VM。 

### <a name="oldestvm-scale-in-policy"></a>最老的 VM 擴展策略

| 事件                 | 區域 1 中的實例指示  | 區域 2 中的實例指示  | 區域 3 中的實例指示  | 放大縮小字體功能 放大縮小字體功能                                                                                                               |
|-----------------------|------------------------|------------------------|------------------------|----------------------------------------------------------------------------------------------------------------------------------|
| Initial               | 3, 4, 5, 10            | 2, 6, 9, 11            | 1, 7, 8                |                                                                                                                                  |
| 放大縮小字體功能 放大縮小字體功能              | 3, 4, 5, 10            | ***2***， 6， 9， 11      | 1, 7, 8                | 在區域 1 和 2 之間進行選擇，即使區域 3 具有最舊的 VM。 從區域 2 中刪除 VM2，因為它是該區域中最早的 VM。   |
| 放大縮小字體功能 放大縮小字體功能              | ***3***， 4， 5， 10      | 6, 9, 11               | 1, 7, 8                | 選擇區域 1，即使區域 3 具有最舊的 VM。 從區域 1 中刪除 VM3，因為它是該區域中最早的 VM。                  |
| 放大縮小字體功能 放大縮小字體功能              | 4, 5, 10               | 6, 9, 11               | ***1***， 7， 8          | 區域是平衡的。 刪除區域 3 中的 VM1，因為它是規模集中最早的 VM。                                               |
| 放大縮小字體功能 放大縮小字體功能              | ***4***， 5， 10         | 6, 9, 11               | 7、8                   | 在區域 1 和區域 2 之間進行選擇。 刪除區域 1 中的 VM4，因為它是跨兩個區域的最舊的 VM。                              |
| 放大縮小字體功能 放大縮小字體功能              | 5, 10                  | ***6***， 9， 11         | 7、8                   | 選擇區域 2，即使區域 1 具有最舊的 VM。 刪除區域 1 中的 VM6，因為它是該區域中最早的 VM。                    |
| 放大縮小字體功能 放大縮小字體功能              | ***5***， 10            | 9, 11                  | 7、8                   | 區域是平衡的。 刪除區域 1 中的 VM5，因為它是規模集中最早的 VM。                                                |

對於非區域虛擬機器縮放集，策略選擇規模集中最舊的 VM 進行刪除。 將跳過任何"受保護的"VM 進行刪除。

### <a name="newestvm-scale-in-policy"></a>最新的 VM 擴展策略

| 事件                 | 區域 1 中的實例指示  | 區域 2 中的實例指示  | 區域 3 中的實例指示  | 放大縮小字體功能 放大縮小字體功能                                                                                                               |
|-----------------------|------------------------|------------------------|------------------------|----------------------------------------------------------------------------------------------------------------------------------|
| Initial               | 3, 4, 5, 10            | 2, 6, 9, 11            | 1, 7, 8                |                                                                                                                                  |
| 放大縮小字體功能 放大縮小字體功能              | 3, 4, 5, 10            | 2， 6， 9， ***11***      | 1, 7, 8                | 在區域 1 和 2 之間進行選擇。 從區域 2 中刪除 VM11，因為它是跨兩個區域的最新 VM。                                |
| 放大縮小字體功能 放大縮小字體功能              | 3， 4， 5， ***10***      | 2, 6, 9                | 1, 7, 8                | 選擇區域 1，因為它比其他兩個區域有更多的 VM。 從區域 1 中刪除 VM10，因為它是該區域中最新的 VM。          |
| 放大縮小字體功能 放大縮小字體功能              | 3, 4, 5                | 2， 6， ***9***          | 1, 7, 8                | 區域是平衡的。 刪除區域 2 中的 VM9，因為它是規模集中的最新 VM。                                                |
| 放大縮小字體功能 放大縮小字體功能              | 3, 4, 5                | 2, 6                   | 1， 7， ***8***          | 在區域 1 和區域 3 之間進行選擇。 刪除區域 3 中的 VM8，因為它是該區域中最新的 VM。                                      |
| 放大縮小字體功能 放大縮小字體功能              | 3， 4， ***5***          | 2, 6                   | 1, 7                   | 選擇區域 1，即使區域 3 具有最新的 VM。 刪除區域 1 中的 VM5，因為它是該區域中最新的 VM。                    |
| 放大縮小字體功能 放大縮小字體功能              | 3, 4                   | 2, 6                   | 1， ***7***             | 區域是平衡的。 刪除區域 3 中的 VM7，因為它是規模集中的最新 VM。                                                |

對於非區域虛擬機器規模集，策略將在整個規模集中選擇最新的 VM 進行刪除。 將跳過任何"受保護的"VM 進行刪除。 

## <a name="troubleshoot"></a>疑難排解

1. 如果收到"BadRequest"錯誤，錯誤訊息顯示"在類型'屬性'物件上找不到成員'scaleInPolicy'"，則無法啟用 scale策略，則檢查用於虛擬機器規模集的 API 版本。 此功能需要 API 版本 2019-03-01 或更高版本。

2. 用於擴展的 VM 選擇錯誤 請參閱上面的示例。 如果您的虛擬機器規模集是區域部署，則將首先將擴展策略應用於不平衡的區域，然後在區域平衡後跨比例集應用。 如果擴展的順序與上述示例不一致，請向虛擬機器規模集組引發查詢以進行故障排除。

## <a name="next-steps"></a>後續步驟

了解如何在虛擬機器擴展集上[部署您的應用程式](virtual-machine-scale-sets-deploy-app.md)。