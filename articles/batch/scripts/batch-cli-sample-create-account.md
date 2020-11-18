---
title: Azure CLI 指令碼範例 - 建立 Batch 帳戶 - Batch 服務
description: 此指令碼會在 Batch 服務模式中建立 Azure Batch 帳戶，並顯示如何查詢或更新帳戶的各種屬性。
ms.topic: sample
ms.date: 01/29/2018
ms.custom: devx-track-azurecli
ms.openlocfilehash: 2349b6b373f271a5aa0f169e5a9ebc9f58f6f608
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93076805"
---
# <a name="cli-example-create-a-batch-account-in-batch-service-mode"></a>CLI 範例 - 在 Batch 服務模式中建立 Batch 帳戶

此指令碼會在 Batch 服務模式中建立 Azure Batch 帳戶，並顯示如何查詢或更新帳戶的各種屬性。 當您在預設 Batch 服務模式中建立 Batch 帳戶時，其計算節點是由 Batch 服務在內部指派。 配置的計算節點受個別的 vCPU (核心) 配額限制，帳戶可透過共用金鑰認證或 Azure Active Dirctory 權杖進行驗證。

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- 本教學課程需要 2.0.20 版或更新版本的 Azure CLI。 如果您是使用 Azure Cloud Shell，就已安裝最新版本。 

## <a name="example-script"></a>範例指令碼

[!code-azurecli-interactive[main](../../../cli_scripts/batch/create-account/create-account.sh "Create Account")]

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
| [az storage account create](/cli/azure/storage/account#az-storage-account-create) | 建立儲存體帳戶。 |
| [az batch account set](/cli/azure/batch/account#az-batch-account-set) | 更新 Batch 帳戶的屬性。  |
| [az batch account show](/cli/azure/batch/account#az-batch-account-show) | 擷取指定的 Batch 帳戶的詳細資料。  |
| [az batch account keys list](/cli/azure/batch/account/keys#az-batch-account-keys-list) | 擷取指定的 Batch 帳戶的存取金鑰。  |
| [az batch account login](/cli/azure/batch/account#az-batch-account-login) | 對指定的 Batch 帳戶驗證以進行進一步的 CLI 互動。  |
| [az group delete](/cli/azure/group#az-group-delete) | 刪除資源群組，包括所有的巢狀資源。 |

## <a name="next-steps"></a>後續步驟

如需 Azure CLI 的詳細資訊，請參閱 [Azure CLI 文件](/cli/azure)。
