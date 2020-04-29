---
title: 使用 CLI 取得維護通知
description: 查看在 Azure 中執行之虛擬機器的維護通知，並使用 Azure CLI 來啟動自助維護。
author: shants123
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 11/19/2019
ms.author: shants
ms.openlocfilehash: 4ad57c1c71a51f948bd405a5487a1e27e36bfff7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "77920887"
---
# <a name="handling-planned-maintenance-notifications-using-the-azure-cli"></a>使用 Azure CLI 處理預定的維護通知

**本文適用于同時執行 Linux 和 Windows 的虛擬機器。**

您可以使用 CLI 來查看 Vm 何時排程進行[維護](maintenance-notifications.md)。 您可以從[az vm get-view 取得](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az-vm-get-instance-view)預定的維護資訊。
 
只在有預定進行的維修作業時，才會傳回維修資訊。 

```azurecli-interactive
az vm get-instance-view -n myVM -g myResourceGroup --query instanceView.maintenanceRedeployStatus
```

## <a name="start-maintenance"></a>開始維護

如果`IsCustomerInitiatedMaintenanceAllowed`設定為 true，則下列呼叫會在 VM 上開始維護。

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

您也可以使用[Azure PowerShell](maintenance-notifications-powershell.md)或[入口網站](maintenance-notifications-portal.md)來處理預定的維護。
