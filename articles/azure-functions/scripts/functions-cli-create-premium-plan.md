---
title: 在進階方案中建立函式應用程式 - Azure CLI
description: 使用 Azure CLI 在可調整的進階方案中建立函式應用程式
ms.service: azure-functions
ms.topic: sample
ms.date: 11/23/2019
ms.openlocfilehash: 3587e5b5e46867ff3abe97b3593c9732be3afa22
ms.sourcegitcommit: 23604d54077318f34062099ed1128d447989eea8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/20/2020
ms.locfileid: "85117213"
---
# <a name="create-a-function-app-in-a-premium-plan---azure-cli"></a>在進階方案中建立函式應用程式 - Azure CLI

這個 Azure Functions 範例指令碼會建立函式應用程式，這是您的函式容器。 所建立的函式應用程式會使用[可調整的進階方案](../functions-premium-plan.md)。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

如果您選擇在本機安裝和使用 CLI，本文需要 Azure CLI 2.0 版或更新版本。 執行 `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI]( /cli/azure/install-azure-cli)。 

## <a name="sample-script"></a>範例指令碼

此指令碼會使用[進階方案](../functions-premium-plan.md)建立函式應用程式。

[!code-azurecli-interactive[main](../../../cli_scripts/azure-functions/create-function-app-premium-plan/create-function-app-premium-plan.sh "Create an Azure Function on an App Service plan")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>指令碼說明

下表中的每個命令都會連結至命令特定的文件。 此指令碼會使用下列命令：

| Command | 注意 |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | 建立用來存放所有資源的資源群組。 |
| [az storage account create](/cli/azure/storage/account#az-storage-account-create) | 建立 Azure 儲存體帳戶。 |
| [az functionapp plan create](/cli/azure/functionapp/plan#az-functionapp-plan-create) | 在[特定 SKU](../functions-premium-plan.md#available-instance-skus) 中建立進階方案。 |
| [az functionapp create](/cli/azure/functionapp#az-functionapp-create) | 在 App Service 方案中建立函式應用程式。 |

## <a name="next-steps"></a>後續步驟

如需 Azure CLI 的詳細資訊，請參閱 [Azure CLI 文件](/cli/azure)。

您可以在 [Azure Functions 文件](../functions-cli-samples.md)中找到其他 Azure Functions CLI 指令碼範例。
