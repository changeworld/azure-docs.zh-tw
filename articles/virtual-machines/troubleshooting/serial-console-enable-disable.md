---
title: 啟用及停用 Azure 序列主控台 |Microsoft Docs
description: 如何啟用及停用 Azure 序列主控台服務
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
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "75451293"
---
# <a name="enable-and-disable-the-azure-serial-console"></a>啟用及停用 Azure 序列主控台

就像任何其他資源一樣，可以啟用和停用 Azure 序列主控台。 依預設，全域 Azure 中的所有訂用帳戶都會啟用序列主控台。 目前，停用序列主控台將會停用整個訂用帳戶的服務。 停用或重新啟用訂用帳戶的序列主控台，需要訂用帳戶的參與者層級存取權或更高的許可權。

您也可以停用開機診斷，以停用個別 VM 或虛擬機器擴展集實例的序列主控台。 您將需要在 VM/虛擬機器擴展集和您的開機診斷儲存體帳戶上，擁有者層級存取權或更高的許可權。

## <a name="vm-level-disable"></a>VM 層級的停用
您可以停用開機診斷設定，以停用特定 VM 或虛擬機器擴展集的序列主控台。 關閉 Azure 入口網站的開機診斷，以停用 VM 或虛擬機器擴展集的序列主控台。 如果您在虛擬機器擴展集上使用序列主控台，請確定您已將虛擬機器擴展集實例升級為最新的模型。


## <a name="subscription-level-enabledisable"></a>訂用帳戶層級啟用/停用

> [!NOTE]
> 執行此命令之前，請確定您是在正確的雲端 (Azure 公用雲端、Azure 美國政府雲端) 。 您可以使用來檢查 `az cloud list` 和設定您的 `az cloud set -n <Name of cloud>` 雲端。

### <a name="azure-cli"></a>Azure CLI

您可以使用 Azure CLI 中的下列命令，為整個訂用帳戶停用並重新啟用序列主控台 (您可以使用 [試試看] 按鈕來啟動 Azure Cloud Shell 的實例，您可以在其中執行命令) ：

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

若要取得訂用帳戶目前已啟用/停用的序列主控台狀態，請使用下列命令：
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
* 深入瞭解 [適用于 Linux vm 的 Azure 序列主控台](./serial-console-linux.md)
* 深入瞭解 [適用于 Windows vm 的 Azure 序列主控台](./serial-console-windows.md)
* 瞭解 [Azure 序列主控台內的電源管理選項](./serial-console-power-options.md)