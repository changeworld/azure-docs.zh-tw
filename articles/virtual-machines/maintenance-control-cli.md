---
title: Azure 虛擬機器的維護控制
description: 瞭解如何使用維護控制來控制何時將維護套用至您的 Azure Vm。
services: virtual-machines-linux
author: cynthn
ms.service: virtual-machines
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 11/21/2019
ms.author: cynthn
ms.openlocfilehash: 13e4923bc5d49843710c9df4523992f541f1d343
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/04/2020
ms.locfileid: "76988017"
---
# <a name="preview-control-updates-with-maintenance-control-and-the-azure-cli"></a>預覽：使用維護控制和 Azure CLI 控制更新

使用維護控制來管理不需要重新開機的平臺更新。 Azure 會經常更新其基礎結構，以改善可靠性、效能、安全性或啟動新功能。 大部分的更新對使用者而言是透明的。 某些敏感的工作負載（例如遊戲、媒體串流處理和財務交易）無法容忍虛擬機器甚至幾秒的時間，就會凍結或中斷連接以進行維護。 維護控制可讓您選擇是否要等候平臺更新，並將它們套用到35天的輪流時段內。 

維護控制可讓您決定何時要將更新套用到隔離的 Vm 和 Azure 專用主機。

有了維護控制，您可以：
- 批次更新為一個更新套件。
- 請等候35天以套用更新。 
- 使用 Azure Functions 自動進行維護視窗的平臺更新。
- 維護設定適用于訂用帳戶和資源群組。 

> [!IMPORTANT]
> 維護控制目前為公開預覽狀態。
> 此預覽版本是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。
>

## <a name="limitations"></a>限制

- Vm 必須位於[專用主機](./linux/dedicated-hosts.md)上，或使用[隔離的 VM 大小](./linux/isolation.md)建立。
- 35天后，將會自動套用更新。
- 使用者必須擁有**資源參與者**存取權。


## <a name="install-the-maintenance-extension"></a>安裝維護延伸模組

如果您選擇在本機安裝[Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) ，則需要2.0.76 或更新版本。

在本機或 Cloud Shell 中安裝 `maintenance` preview CLI 擴充功能。 

```azurecli-interactive
az extension add -n maintenance
```


## <a name="create-a-maintenance-configuration"></a>建立維護設定

使用 `az maintenance configuration create` 來建立維護設定。 這個範例會建立名為*myconfig.xml*的維護設定，範圍限定于主機。 

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

複製輸出中的設定識別碼，以供稍後使用。

使用 `--maintenanceScope host` 可確保維護設定用於控制主機的更新。

如果您嘗試使用相同的名稱建立設定，但在不同的位置，您會收到錯誤。 設定名稱在您的訂用帳戶中必須是唯一的。

您可以使用 `az maintenance configuration list`來查詢可用的維護設定。

```azurecli-interactive
az maintenance configuration list --query "[].{Name:name, ID:id}" -o table 
```

## <a name="assign-the-configuration"></a>指派設定

使用 `az maintenance assignment create` 將設定指派給隔離的 VM 或 Azure 專用主機。

### <a name="isolated-vm"></a>隔離的 VM

使用設定的識別碼，將設定套用至 VM。 指定 `--resource-type virtualMachines` 並提供 `--resource-name`的 VM 名稱，以及 `--resource-group`中 vm 的資源群組，以及 `--location`的 VM 位置。 

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

若要將設定套用至專用主機，您必須包含 `--resource-type hosts`、`--resource-parent-name` 主機群組的名稱，以及 `--resource-parent-type hostGroups`。 

參數 `--resource-id` 是主機的識別碼。 您可以使用[az vm host get 實例-view](/cli/azure/vm/host#az-vm-host-get-instance-view)來取得專用主機的識別碼。

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

## <a name="check-configuration"></a>檢查設定

您可以確認設定已正確套用，或檢查以查看目前使用 `az maintenance assignment list`套用的設定。

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


## <a name="check-for-pending-updates"></a>檢查暫止的更新

使用 `az maintenance update list` 查看是否有擱置中的更新。 更新--訂用帳戶為包含 VM 之訂用帳戶的識別碼。

如果沒有任何更新，此命令會傳回錯誤訊息，其中將包含文字： `Resource not found...StatusCode: 404`。

如果有更新，即使有多個擱置中的更新，也只會傳回一個。 此更新的資料會在物件中傳回：

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

檢查隔離 VM 的暫止更新。 在此範例中，會將輸出格式化為資料表以方便閱讀。

```azurecli-interactive
az maintenance update list \
   -g myMaintenanceRg \
   --resource-name myVM \
   --resource-type virtualMachines \
   --provider-name Microsoft.Compute \
   -o table
```

### <a name="dedicated-host"></a>專用主機

以檢查專用主機的暫止更新。 在此範例中，會將輸出格式化為資料表以方便閱讀。 將資源的值取代為您自己的值。

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

使用 `az maintenance apply update` 來套用擱置中的更新。 成功時，此命令會傳回 JSON，其中包含更新的詳細資料。

### <a name="isolated-vm"></a>隔離的 VM

建立將更新套用至隔離 VM 的要求。

```azurecli-interactive
az maintenance applyupdate create \
   --subscription 1111abcd-1a11-1a2b-1a12-123456789abc \
   --resource-group myMaintenanceRG \
   --resource-name myVM \
   --resource-type virtualMachines \
   --provider-name Microsoft.Compute
```


### <a name="dedicated-host"></a>專用主機

將更新套用至專用主機。

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

## <a name="check-the-status-of-applying-updates"></a>檢查套用更新的狀態 

您可以使用 `az maintenance applyupdate get`來檢查更新進度。 

您可以使用 `default` 做為更新名稱，以查看上次更新的結果，或以執行 `az maintenance applyupdate create`時傳回的更新名稱取代 `myUpdateName`。

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


## <a name="delete-a-maintenance-configuration"></a>刪除維護設定

使用 `az maintenance configuration delete` 來刪除維護設定。 刪除設定會將維護控制從相關聯的資源中移除。

```azurecli-interactive
az maintenance configuration delete \
   --subscription 1111abcd-1a11-1a2b-1a12-123456789abc \
   -g myResourceGroup \
   --name myConfig
```

## <a name="next-steps"></a>後續步驟
若要深入瞭解，請參閱[維護和更新](maintenance-and-updates.md)。
