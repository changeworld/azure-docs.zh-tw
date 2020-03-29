---
title: 啟用和禁用 Azure 串列主控台 |微軟文檔
description: 如何啟用和禁用 Azure 串列主控台服務
services: virtual-machines
documentationcenter: ''
author: asinn826
manager: borisb
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm
ms.workload: infrastructure-services
ms.date: 8/20/2019
ms.author: alsin
ms.openlocfilehash: e09e08f8ba36cf576bc27551254225adee3bb0fd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75451293"
---
# <a name="enable-and-disable-the-azure-serial-console"></a>啟用和禁用 Azure 串列主控台

與任何其他資源一樣，可以啟用和禁用 Azure 串列主控台。 預設情況下，全域 Azure 中的所有訂閱都啟用串列主控台。 目前，禁用串列主控台將禁用整個訂閱的服務。 禁用或重新啟用訂閱的串列主控台需要訂閱上的參與者級別或以上存取權限。

還可以通過禁用引導診斷來禁用單個 VM 或虛擬機器規模集實例的串列主控台。 在 VM/虛擬機器規模集和引導診斷存儲帳戶上，您將需要參與者級別存取權限或以上存取權限。

## <a name="vm-level-disable"></a>VM 層級的停用
可以通過禁用引導診斷設置，為特定的 VM 或虛擬機器規模設置禁用串列主控台。 關閉 Azure 門戶的啟動診斷，以禁用 VM 或虛擬機器規模集的串列主控台。 如果在虛擬機器規模集中使用串列主控台，請確保將虛擬機器縮放集實例升級到最新型號。


## <a name="subscription-level-enabledisable"></a>訂閱級啟用/禁用

> [!NOTE]
> 在運行此命令之前，請確保您處於正確的雲中（Azure 公共雲、Azure 美國政府雲）。 您可以使用 簽入`az cloud list`並設置雲`az cloud set -n <Name of cloud>`。

### <a name="azure-cli"></a>Azure CLI

通過使用 Azure CLI 中的以下命令（您可以使用"試用"按鈕啟動 Azure 雲外殼的實例，您可以在其中運行命令），從而禁用並重新啟用整個訂閱的串列主控台：

要禁用訂閱的串列主控台，請使用以下命令：
```azurecli-interactive
subscriptionId=$(az account show --output=json | jq -r .id)

az resource invoke-action --action disableConsole --ids "/subscriptions/$subscriptionId/providers/Microsoft.SerialConsole/consoleServices/default" --api-version="2018-05-01"
```

要為訂閱啟用串列主控台，請使用以下命令：
```azurecli-interactive
subscriptionId=$(az account show --output=json | jq -r .id)

az resource invoke-action --action enableConsole --ids "/subscriptions/$subscriptionId/providers/Microsoft.SerialConsole/consoleServices/default" --api-version="2018-05-01"
```

要獲取訂閱串列主控台的當前啟用/禁用狀態，請使用以下命令：
```azurecli-interactive
subscriptionId=$(az account show --output=json | jq -r .id)

az resource show --ids "/subscriptions/$subscriptionId/providers/Microsoft.SerialConsole/consoleServices/default" --output=json --api-version="2018-05-01" | jq .properties
```

### <a name="powershell"></a>PowerShell

也可以使用 PowerShell 啟用和禁用串列主控台。

要禁用訂閱的串列主控台，請使用以下命令：
```azurepowershell-interactive
$subscription=(Get-AzContext).Subscription.Id

Invoke-AzResourceAction -Action disableConsole -ResourceId /subscriptions/$subscription/providers/Microsoft.SerialConsole/consoleServices/default -ApiVersion 2018-05-01
```

要為訂閱啟用串列主控台，請使用以下命令：
```azurepowershell-interactive
$subscription=(Get-AzContext).Subscription.Id

Invoke-AzResourceAction -Action enableConsole -ResourceId /subscriptions/$subscription/providers/Microsoft.SerialConsole/consoleServices/default -ApiVersion 2018-05-01
```

## <a name="next-steps"></a>後續步驟
* 瞭解有關 Linux VM 的[Azure 串列主控台](./serial-console-linux.md)的資訊
* 瞭解有關 Windows VM 的[Azure 串列主控台](./serial-console-windows.md)的資訊
* 瞭解[Azure 串列主控台中的電源管理選項](./serial-console-power-options.md)