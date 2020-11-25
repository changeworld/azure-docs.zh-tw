---
title: 透過 GitHub 部署建立函式應用程式 - Azure CLI
description: 建立函數應用程式，並使用 Azure Functions 從 GitHub 存放庫部署函式程式碼。
ms.date: 07/03/2018
ms.topic: sample
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 752d8a729c56fc2e0e110fcedc263eab4ba7a96b
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/12/2020
ms.locfileid: "94565302"
---
# <a name="create-a-function-app-in-azure-that-is-deployed-from-github"></a>在 Azure 中建立從 GitHub 部署的函式應用程式

此 Azure Functions 範例指令碼會使用[取用方案](../functions-scale.md#consumption-plan)建立函式應用程式及其相關資源。 此指令碼也會您的函式程式碼，以便從 GitHub 存放庫持續進行部署。 

在此範例中，您需要：

* 一個含有函式程式碼的 GitHub 存放庫 (您必須有此存放庫的系統管理權限)。
* 您 GitHub 帳戶的[個人存取權杖 (PAT)](https://help.github.com/articles/creating-an-access-token-for-command-line-use)。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - 本教學課程需要 2.0 版或更新版本的 Azure CLI。 如果您是使用 Azure Cloud Shell，就已安裝最新版本。 

## <a name="sample-script"></a>範例指令碼

此指令碼範例會建立 Azure 函式應用程式，並從 GitHub 部署函式程式碼。

[!code-azurecli-interactive[main](../../../cli_scripts/azure-functions/deploy-function-app-with-function-github-continuous/deploy-function-app-with-function-github-continuous.sh?highlight=3-4 "Azure Service")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>指令碼說明

下表中的每個命令都會連結至命令特定的文件。 此指令碼會使用下列命令：

| Command | 注意 |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | 建立用來存放所有資源的資源群組。 |
| [az storage account create](/cli/azure/storage/account#az-storage-account-create) | 建立函式應用程式所需的儲存體帳戶。 |
| [az functionapp create](/cli/azure/functionapp#az-functionapp-create) | 在無伺服器[取用方案](../functions-scale.md#consumption-plan)中建立函式應用程式，並與 Git 或 Mercurial 存放庫產生關聯。 |

## <a name="next-steps"></a>後續步驟

如需 Azure CLI 的詳細資訊，請參閱 [Azure CLI 文件](/cli/azure)。

您可以在 [Azure Functions 文件](../functions-cli-samples.md)中找到其他 Azure Functions CLI 指令碼範例。
