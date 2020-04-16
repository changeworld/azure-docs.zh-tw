---
title: Azure CLI 範例 - 啟用主機型自動調整
description: 此指令碼會建立執行 Ubuntu 的虛擬機器擴展集，並在 CPU 負載變更時，使用主機型計量來進行自動調整。
author: ju-shim
tags: azure-resource-manager
ms.service: virtual-machine-scale-sets
ms.devlang: azurecli
ms.topic: sample
ms.date: 03/27/2018
ms.author: jushiman
ms.custom: mvc
ms.openlocfilehash: 0b934e327a4c1ed18c682f909b2d82e85d61125f
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/10/2020
ms.locfileid: "81008381"
---
# <a name="automatically-scale-a-virtual-machine-scale-set-with-the-azure-cli"></a>如何使用 Azure CLI 自動調整虛擬機器擴展集
此指令碼會建立執行 Ubuntu 的虛擬機器擴展集，並在 CPU 負載變更時，使用主機型計量來進行自動調整。

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>範例指令碼
[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine-scale-sets/auto-scale-host-metrics/auto-scale-host-metrics.sh "Automatically scale a virtual machine scale set")]

## <a name="clean-up-deployment"></a>清除部署
執行下列命令來移除資源群組、擴展集和所有相關資源。

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>指令碼說明
此指令碼使用下列命令來建立資源群組、虛擬機器擴展集和所有相關資源。 下表中的每個命令都會連結至命令特定的文件。

| Command | 注意 |
|---|---|
| [az group create](/cli/azure/ad/group) | 建立用來存放所有資源的資源群組。 |
| [az vmss create](/cli/azure/vmss) | 建立虛擬機器擴展集，並將它連線到虛擬網路、子網路及網路安全性群組。 若要將流量散發到多個虛擬機器執行個體，也會建立負載平衡器。 此命令也會指定要使用的 VM 映像和管理認證。  |
| [az monitor autoscale-settings create](/cli/azure/monitor/autoscale-settings) | 建立自動調整規則，並將其套用到虛擬機器擴展集。 |
| [az group delete](/cli/azure/ad/group) | 刪除資源群組，包括所有的巢狀資源。 |

## <a name="next-steps"></a>後續步驟
如需 Azure CLI 的詳細資訊，請參閱 [Azure CLI 文件](https://docs.microsoft.com/cli/azure/overview)。

您可以在 [Azure 虛擬機器擴展集文件](../cli-samples.md)中找到其他虛擬機器擴展集的 Azure CLI 指令碼範例。
