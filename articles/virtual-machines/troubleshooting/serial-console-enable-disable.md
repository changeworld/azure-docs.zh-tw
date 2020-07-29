---
title: 啟用和停用 Azure 序列主控台 |Microsoft Docs
description: 如何啟用和停用 Azure 序列主控台服務
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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "75451293"
---
# <a name="enable-and-disable-the-azure-serial-console"></a>啟用和停用 Azure 序列主控台

就像任何其他資源一樣，可以啟用和停用 Azure 序列主控台。 預設會針對全域 Azure 中的所有訂用帳戶啟用序列主控台。 目前，停用序列主控台將會停用整個訂用帳戶的服務。 停用或重新啟用訂用帳戶的序列主控台需要訂用帳戶上的參與者層級存取權或更新版本。

您也可以藉由停用 [開機診斷]，停用個別 VM 或虛擬機器擴展集實例的序列主控台。 在 VM/虛擬機器擴展集和您的開機診斷儲存體帳戶上，您將需要有「參與者」層級存取或以上版本。

## <a name="vm-level-disable"></a>VM 層級的停用
您可以停用 [開機診斷] 設定，針對特定的 VM 或虛擬機器擴展集停用序列主控台。 關閉 Azure 入口網站的開機診斷，以停用 VM 或虛擬機器擴展集的序列主控台。 如果您在虛擬機器擴展集上使用序列主控台，請務必將您的虛擬機器擴展集實例升級為最新的模型。


## <a name="subscription-level-enabledisable"></a>訂用帳戶層級啟用/停用

> [!NOTE]
> 在執行此命令之前，請確定您是在正確的雲端（Azure 公用雲端、Azure 美國政府雲端）。 您可以使用來檢查 `az cloud list` 並設定雲端 `az cloud set -n <Name of cloud>` 。

### <a name="azure-cli"></a>Azure CLI

您可以使用 Azure CLI 中的下列命令，針對整個訂用帳戶停用和重新啟用序列主控台（您可以使用 [試試看] 按鈕來啟動可執行命令的 Azure Cloud Shell 實例）：

若要停用訂用帳戶的序列主控台，請使用下列命令：
```azurecli-interactive
subscriptionId=$(az account show --output=json | jq -r .id)

az resource invoke-action --action disableConsole --ids "/subscriptions/$subscriptionId/providers/Microsoft.SerialConsole/consoleServices/default" --api-version="2018-05-01"
```

若要啟用訂用帳戶的序列主控台，請使用下列命令：
```azurecli-interactive
subscriptionId=$(az account show --output=json | jq -r .id)

az resource invoke-action --action enableConsole --ids "/subscriptions/$subscriptionId/providers/Microsoft.SerialConsole/consoleServices/default" --api-version="2018-05-01"
```

若要取得訂用帳戶的序列主控台目前已啟用/已停用狀態，請使用下列命令：
```azurecli-interactive
subscriptionId=$(az account show --output=json | jq -r .id)

az resource show --ids "/subscriptions/$subscriptionId/providers/Microsoft.SerialConsole/consoleServices/default" --output=json --api-version="2018-05-01" | jq .properties
```

### <a name="powershell"></a>PowerShell

您也可以使用 PowerShell 來啟用和停用序列主控台。

若要停用訂用帳戶的序列主控台，請使用下列命令：
```azurepowershell-interactive
$subscription=(Get-AzContext).Subscription.Id

Invoke-AzResourceAction -Action disableConsole -ResourceId /subscriptions/$subscription/providers/Microsoft.SerialConsole/consoleServices/default -ApiVersion 2018-05-01
```

若要啟用訂用帳戶的序列主控台，請使用下列命令：
```azurepowershell-interactive
$subscription=(Get-AzContext).Subscription.Id

Invoke-AzResourceAction -Action enableConsole -ResourceId /subscriptions/$subscription/providers/Microsoft.SerialConsole/consoleServices/default -ApiVersion 2018-05-01
```

## <a name="next-steps"></a>後續步驟
* 深入瞭解[適用于 Linux vm 的 Azure 序列主控台](./serial-console-linux.md)
* 深入瞭解[適用于 Windows vm 的 Azure 序列主控台](./serial-console-windows.md)
* 瞭解[Azure 序列主控台內的電源管理選項](./serial-console-power-options.md)