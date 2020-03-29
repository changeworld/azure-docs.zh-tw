---
title: 使用 CLI 獲取維護通知
description: 查看在 Azure 中運行的虛擬機器的維護通知，並使用 Azure CLI 啟動自助服務維護。
author: shants123
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 11/19/2019
ms.author: shants
ms.openlocfilehash: 4ad57c1c71a51f948bd405a5487a1e27e36bfff7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77920887"
---
# <a name="handling-planned-maintenance-notifications-using-the-azure-cli"></a>使用 Azure CLI 處理計畫維護通知

**本文適用于同時運行 Linux 和 Windows 的虛擬機器。**

您可以使用 CLI 查看 VM 何時計畫[進行維護](maintenance-notifications.md)。 計畫維護資訊可從[az vm 獲取實例視圖](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az-vm-get-instance-view)獲得。
 
只在有預定進行的維修作業時，才會傳回維修資訊。 

```azurecli-interactive
az vm get-instance-view -n myVM -g myResourceGroup --query instanceView.maintenanceRedeployStatus
```

## <a name="start-maintenance"></a>開始維護

如果`IsCustomerInitiatedMaintenanceAllowed`設置為 true，以下調用將在 VM 上開始維護。

```azurecli-interactive
az vm perform-maintenance -g myResourceGroup -n myVM 
```

## <a name="classic-deployments"></a>傳統部署

[!INCLUDE [classic-vm-deprecation](../../includes/classic-vm-deprecation.md)]

如果您仍有使用傳統部署模型部署的舊版 VM，可以使用 Azure 傳統 CLI 查詢 VM 並起始維護。

請鍵入下列項目，以確認您使用傳統 VM 的模式正確：

```
azure config mode asm
```

若要取得名為 *myVM* 的 VM 維護狀態，請鍵入：

```
azure vm show myVM 
``` 

若要開始維護 *myService* 服務和 *myDeployment* 部署中名為 *myVM* 的傳統虛擬機器，請鍵入：

```
azure compute virtual-machine initiate-maintenance --service-name myService --name myDeployment --virtual-machine-name myVM
```

## <a name="next-steps"></a>後續步驟

您還可以使用[Azure PowerShell](maintenance-notifications-powershell.md)或[門戶](maintenance-notifications-portal.md)來處理計畫維護。
