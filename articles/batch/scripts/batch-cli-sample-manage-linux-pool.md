---
title: Azure CLI 指令碼範例 - Batch 中的 Linux 集區
description: 此指令碼示範 Azure CLI 中一些可用的命令，用於建立和管理 Azure Batch 中的 Linux 計算節點集區。
ms.topic: sample
ms.date: 01/29/2018
ms.openlocfilehash: 21236ef2f314cb1306adc7346e620b80db540034
ms.sourcegitcommit: 845a55e6c391c79d2c1585ac1625ea7dc953ea89
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/05/2020
ms.locfileid: "85964034"
---
# <a name="cli-example-create-and-manage-a-linux-pool-in-azure-batch"></a>CLI 範例：在 Azure Batch 中建立和管理 Linux 集區

此指令碼示範 Azure CLI 中一些可用的命令，用於建立和管理 Azure Batch 中的 Linux 計算節點集區。

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

如果您選擇在本機安裝和使用 CLI，本快速入門會要求您執行 Azure CLI 2.0.20 版或更新版本。 執行 `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI](/cli/azure/install-azure-cli)。 

## <a name="example-script"></a>範例指令碼

[!code-azurecli-interactive[main](../../../cli_scripts/batch/manage-pool/manage-pool-linux.sh "Manage Linux Virtual Machine Pool")]

## <a name="clean-up-deployment"></a>清除部署

執行下列命令來移除資源群組及其所有相關聯的資源。

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>指令碼說明

此指令碼會使用下列命令。 下表中的每個命令都會連結至命令特定的文件。

| Command | 注意 |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | 建立用來存放所有資源的資源群組。 |
| [az batch account create](/cli/azure/batch/account#az-batch-account-create) | 建立 Batch 帳戶。 |
| [az batch account login](/cli/azure/batch/account#az-batch-account-login) | 對指定的 Batch 帳戶驗證以進行進一步的 CLI 互動。  |
| [az batch pool node-agent-skus list](../batch-linux-nodes.md#list-of-virtual-machine-images) | 列出可用的節點代理程式 SKU 和映像資訊。  |
| [az batch pool create](/cli/azure/batch/pool#az-batch-pool-create) | 建立計算節點的集區。  |
| [az batch pool resize](/cli/azure/batch/pool#az-batch-pool-resize) | 調整指定集區的執行中 VM 數大小。  |
| [az batch pool show](/cli/azure/batch/pool#az-batch-pool-show) | 顯示集區的屬性。  |
| [az batch node list](/cli/azure/batch/node#az-batch-node-list) | 列出指定集區中的所有計算節點。  |
| [az batch node reboot](/cli/azure/batch/node#az-batch-node-reboot) | 重新啟動指定的計算節點。  |
| [az batch node delete](/cli/azure/batch/node#az-batch-node-delete) | 從指定的集區中刪除列出的節點。  |
| [az group delete](/cli/azure/group#az-group-delete) | 刪除資源群組，包括所有的巢狀資源。 |

## <a name="next-steps"></a>後續步驟

如需 Azure CLI 的詳細資訊，請參閱 [Azure CLI 文件](/cli/azure)。
