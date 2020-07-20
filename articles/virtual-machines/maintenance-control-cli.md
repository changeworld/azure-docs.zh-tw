---
title: 使用 CLI 進行 Azure 虛擬機器的維護控制
description: 瞭解如何使用維護控制和 CLI，控制何時將維護套用至您的 Azure Vm。
author: cynthn
ms.service: virtual-machines
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 04/20/2020
ms.author: cynthn
ms.openlocfilehash: 56f9873828e2f93008498beed986827a01872bf1
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84675854"
---
# <a name="control-updates-with-maintenance-control-and-the-azure-cli"></a>控制維護控制和 Azure CLI 的更新

維護控制可讓您決定何時要將更新套用到隔離的 Vm 和 Azure 專用主機。 本主題涵蓋維護控制的 Azure CLI 選項。 如需使用維護控制、其限制和其他管理選項之優點的詳細資訊，請參閱[管理具有維護控制的平臺更新](maintenance-control.md)。

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
   --location eastus
```

複製輸出中的設定識別碼，以供稍後使用。

使用 `--maintenanceScope host` 可確保維護設定用於控制主機的更新。

如果您嘗試使用相同的名稱建立設定，但在不同的位置，您會收到錯誤。 設定名稱在您的訂用帳戶中必須是唯一的。

您可以使用來查詢可用的維護設定 `az maintenance configuration list` 。

```azurecli-interactive
az maintenance configuration list --query "[].{Name:name, ID:id}" -o table 
```

## <a name="assign-the-configuration"></a>指派設定

使用將設定 `az maintenance assignment create` 指派給隔離的 VM 或 Azure 專用主機。

### <a name="isolated-vm"></a>隔離的 VM

使用設定的識別碼，將設定套用至 VM。 針對指定 `--resource-type virtualMachines` 虛擬機器的名稱，並為中的 vm 提供的資源群組，以及 vm 的 `--resource-name` `--resource-group` 位置 `--location` 。 

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

若要將設定套用至專用主機，您需要包含 `--resource-type hosts` ， `--resource-parent-name` 並以主機群組的名稱和 `--resource-parent-type hostGroups` 。 

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

您可以確認設定已正確套用，或檢查以查看目前使用套用的設定 `az maintenance assignment list` 。

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

如果沒有任何更新，此命令會傳回錯誤訊息，其中將包含下列文字： `Resource not found...StatusCode: 404` 。

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

您可以使用來檢查更新進度 `az maintenance applyupdate get` 。 

您可以使用 `default` 做為更新名稱來查看上次更新的結果，或將取代 `myUpdateName` 為您執行時所傳回的更新名稱 `az maintenance applyupdate create` 。

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
LastUpdateTime 會是更新完成的時間，可能是由您或平臺在未使用自我維護視窗時所起始。 如果從未透過維護控制套用更新，它將會顯示預設值。

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

用 `az maintenance configuration delete` 來刪除維護設定。 刪除設定會將維護控制從相關聯的資源中移除。

```azurecli-interactive
az maintenance configuration delete \
   --subscription 1111abcd-1a11-1a2b-1a12-123456789abc \
   -g myResourceGroup \
   --name myConfig
```

## <a name="next-steps"></a>後續步驟
若要深入瞭解，請參閱[維護和更新](maintenance-and-updates.md)。
