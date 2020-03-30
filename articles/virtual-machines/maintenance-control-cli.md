---
title: 維修控制
description: 瞭解如何使用維護控制控制何時將維護應用於 Azure VM。
author: cynthn
ms.service: virtual-machines
ms.topic: article
ms.workload: infrastructure-services
ms.date: 11/21/2019
ms.author: cynthn
ms.openlocfilehash: 58c0964d170f49066802b955f09dab01eaf998a7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79250175"
---
# <a name="preview-control-updates-with-maintenance-control-and-the-azure-cli"></a>預覽：使用維護控制和 Azure CLI 進行控制更新

使用維護控制管理不需要重新開機的平臺更新。 Azure 經常更新其基礎結構以提高可靠性、性能、安全性或啟動新功能。 大多數更新對使用者都是透明的。 某些敏感工作負載（如遊戲、媒體流和金融交易）甚至無法容忍幾秒鐘的 VM 凍結或斷開連接以進行維護。 維護控制允許您選擇等待平臺更新並在 35 天滾動視窗中應用它們。 

通過維護控制，您可以決定何時將更新應用於隔離的 VM 和 Azure 專用主機。

通過維護控制，您可以：
- 批量更新到一個更新包中。
- 最多等待 35 天才能應用更新。 
- 使用 Azure 函數自動為維護視窗執行平臺更新。
- 維護配置跨訂閱和資源組工作。 

> [!IMPORTANT]
> 維護控制當前處於公共預覽版中。
> 此預覽版本是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。
>

## <a name="limitations"></a>限制

- VM 必須位於[專用主機](./linux/dedicated-hosts.md)上，或者使用[隔離的 VM 大小](./linux/isolation.md)創建。
- 35 天后，將自動應用更新。
- 使用者必須具有**資源參與者**存取權限。


## <a name="install-the-maintenance-extension"></a>安裝維護擴展

如果選擇在本地安裝[Azure CLI，](https://docs.microsoft.com/cli/azure/install-azure-cli)則需要版本 2.0.76 或更高版本。

在`maintenance`本地或雲殼中安裝預覽 CLI 擴展。 

```azurecli-interactive
az extension add -n maintenance
```


## <a name="create-a-maintenance-configuration"></a>創建維護配置

用於`az maintenance configuration create`創建維護配置。 本示例創建名為*myConfig*的維護配置，該配置範圍為主機。 

```azurecli-interactive
az group create \
   --location eastus \
   --name myMaintenanceRG
az maintenance configuration create \
   -g myMaintenanceRG \
   --name myConfig \
   --maintenanceScope host\
   --location  eastus
```

從輸出複製配置 ID 以以後使用。

使用`--maintenanceScope host`可確保維護配置用於控制對主機的更新。

如果嘗試創建具有相同名稱的配置，但在不同的位置，將出現錯誤。 配置名稱必須對訂閱是唯一的。

您可以使用`az maintenance configuration list`查詢可用的維護配置。

```azurecli-interactive
az maintenance configuration list --query "[].{Name:name, ID:id}" -o table 
```

## <a name="assign-the-configuration"></a>分配配置

用於`az maintenance assignment create`將配置分配給隔離的 VM 或 Azure 專用主機。

### <a name="isolated-vm"></a>隔離的 VM

使用配置的 ID 將配置應用於 VM。 指定`--resource-type virtualMachines``--resource-name`的 VM 的名稱和 中的 VM 以及`--resource-group``--location`中的 VM 的位置。 

```azurecli-interactive
az maintenance assignment create \
   --resource-group myMaintenanceRG \
   --location eastus \
   --resource-name myVM \
   --resource-type virtualMachines \
   --provider-name Microsoft.Compute \
   --configuration-assignment-name myConfig \
   --maintenance-configuration-id "/subscriptions/1111abcd-1a11-1a2b-1a12-123456789abc/resourcegroups/myMaintenanceRG/providers/Microsoft.Maintenance/maintenanceConfigurations/myConfig"
```

### <a name="dedicated-host"></a>專用主機

要將配置應用於專用主機，您需要包含`--resource-type hosts``--resource-parent-name`具有主機組的名稱 和`--resource-parent-type hostGroups`。 

參數`--resource-id`是主機的 ID。 您可以使用[az vm 主機獲取實例視圖](/cli/azure/vm/host#az-vm-host-get-instance-view)來獲取專用主機的 ID。

```azurecli-interactive
az maintenance assignment create \
   -g myDHResourceGroup \
   --resource-name myHost \
   --resource-type hosts \
   --provider-name Microsoft.Compute \
   --configuration-assignment-name myConfig \
   --maintenance-configuration-id "/subscriptions/1111abcd-1a11-1a2b-1a12-123456789abc/resourcegroups/myDhResourceGroup/providers/Microsoft.Maintenance/maintenanceConfigurations/myConfig" \
   -l eastus \
   --resource-parent-name myHostGroup \
   --resource-parent-type hostGroups 
```

## <a name="check-configuration"></a>檢查配置

您可以驗證配置是否正確應用，或者檢查當前使用`az maintenance assignment list`應用的配置。

### <a name="isolated-vm"></a>隔離的 VM

```azurecli-interactive
az maintenance assignment list \
   --provider-name Microsoft.Compute \
   --resource-group myMaintenanceRG \
   --resource-name myVM \
   --resource-type virtualMachines \
   --query "[].{resource:resourceGroup, configName:name}" \
   --output table
```

### <a name="dedicated-host"></a>專用主機 

```azurecli-interactive
az maintenance assignment list \
   --resource-group myDHResourceGroup \
   --resource-name myHost \
   --resource-type hosts \
   --provider-name Microsoft.Compute \
   --resource-parent-name myHostGroup \
   --resource-parent-type hostGroups 
   --query "[].{ResourceGroup:resourceGroup,configName:name}" \
   -o table
```


## <a name="check-for-pending-updates"></a>檢查掛起的更新

用於`az maintenance update list`查看是否有掛起的更新。 更新 -- 訂閱是包含 VM 的訂閱的 ID。

如果沒有更新，該命令將返回一條錯誤訊息，其中包含文本： `Resource not found...StatusCode: 404`。

如果有更新，則僅返回一個更新，即使有多個更新掛起。 此更新的資料將在物件中返回：

```text
[
  {
    "impactDurationInSec": 9,
    "impactType": "Freeze",
    "maintenanceScope": "Host",
    "notBefore": "2020-03-03T07:23:04.905538+00:00",
    "resourceId": "/subscriptions/9120c5ff-e78e-4bd0-b29f-75c19cadd078/resourcegroups/DemoRG/providers/Microsoft.Compute/hostGroups/demoHostGroup/hosts/myHost",
    "status": "Pending"
  }
]
  ```

### <a name="isolated-vm"></a>隔離的 VM

檢查隔離 VM 的掛起更新。 在此示例中，輸出被格式化為可讀性表。

```azurecli-interactive
az maintenance update list \
   -g myMaintenanceRg \
   --resource-name myVM \
   --resource-type virtualMachines \
   --provider-name Microsoft.Compute \
   -o table
```

### <a name="dedicated-host"></a>專用主機

檢查專用主機的掛起更新。 在此示例中，輸出被格式化為可讀性表。 將資源的值替換為您自己的值。

```azurecli-interactive
az maintenance update list \
   --subscription 1111abcd-1a11-1a2b-1a12-123456789abc \
   -g myHostResourceGroup \
   --resource-name myHost \
   --resource-type hosts \
   --provider-name Microsoft.Compute \
   --resource-parentname myHostGroup \
   --resource-parent-type hostGroups \
   -o table
```

## <a name="apply-updates"></a>套用更新

用於`az maintenance apply update`應用掛起的更新。 成功後，此命令將返回 JSON，其中包含更新的詳細資訊。

### <a name="isolated-vm"></a>隔離的 VM

創建將更新應用於隔離 VM 的請求。

```azurecli-interactive
az maintenance applyupdate create \
   --subscription 1111abcd-1a11-1a2b-1a12-123456789abc \
   --resource-group myMaintenanceRG \
   --resource-name myVM \
   --resource-type virtualMachines \
   --provider-name Microsoft.Compute
```


### <a name="dedicated-host"></a>專用主機

將更新應用於專用主機。

```azurecli-interactive
az maintenance applyupdate create \
   --subscription 1111abcd-1a11-1a2b-1a12-123456789abc \
   --resource-group myHostResourceGroup \
   --resource-name myHost \
   --resource-type hosts \
   --provider-name Microsoft.Compute \
   --resource-parent-name myHostGroup \
   --resource-parent-type hostGroups
```

## <a name="check-the-status-of-applying-updates"></a>檢查應用更新的狀態 

您可以使用 檢查更新的進度`az maintenance applyupdate get`。 

可以使用`default`作為更新名稱查看上次更新的結果，或替換為`myUpdateName`運行`az maintenance applyupdate create`時返回的更新的名稱。

```text
Status         : Completed
ResourceId     : /subscriptions/12ae7457-4a34-465c-94c1-17c058c2bd25/resourcegroups/TestShantS/providers/Microsoft.Comp
ute/virtualMachines/DXT-test-04-iso
LastUpdateTime : 1/1/2020 12:00:00 AM
Id             : /subscriptions/12ae7457-4a34-465c-94c1-17c058c2bd25/resourcegroups/TestShantS/providers/Microsoft.Comp
ute/virtualMachines/DXT-test-04-iso/providers/Microsoft.Maintenance/applyUpdates/default
Name           : default
Type           : Microsoft.Maintenance/applyUpdates
```
上次更新時間將是更新完成的時間，由您啟動，或者由平臺啟動，以防未使用自我維護視窗。 如果從未通過維護控制應用更新，則將顯示預設值。

### <a name="isolated-vm"></a>隔離的 VM

```azurecli-interactive
az maintenance applyupdate get \
   --resource-group myMaintenanceRG \
   --resource-name myVM \
   --resource-type virtualMachines \
   --provider-name Microsoft.Compute \
   --apply-update-name default 
```

### <a name="dedicated-host"></a>專用主機

```azurecli-interactive
az maintenance applyupdate get \
   --subscription 1111abcd-1a11-1a2b-1a12-123456789abc \ 
   --resource-group myMaintenanceRG \
   --resource-name myHost \
   --resource-type hosts \
   --provider-name Microsoft.Compute \
   --resource-parent-name myHostGroup \ 
   --resource-parent-type hostGroups \
   --apply-update-name myUpdateName \
   --query "{LastUpdate:lastUpdateTime, Name:name, ResourceGroup:resourceGroup, Status:status}" \
   --output table
```


## <a name="delete-a-maintenance-configuration"></a>刪除維護配置

用於`az maintenance configuration delete`刪除維護配置。 刪除配置會從關聯的資源中刪除維護控制。

```azurecli-interactive
az maintenance configuration delete \
   --subscription 1111abcd-1a11-1a2b-1a12-123456789abc \
   -g myResourceGroup \
   --name myConfig
```

## <a name="next-steps"></a>後續步驟
要瞭解更多資訊，請參閱[維護和更新](maintenance-and-updates.md)。
