---
title: 將檔案共用掛接至 Python 函式應用程式 - Azure CLI
description: 建立無伺服器 Python 函式應用程式，並使用 Azure CLI 掛接現有的檔案共用。
ms.topic: sample
ms.date: 03/01/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 63ab9ba3219dc600187e73bbf124d62d3f51317a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "87498203"
---
# <a name="mount-a-file-share-to-a-python-function-app-using-azure-cli"></a>使用 Azure CLI 將檔案共用掛接至 Python 函式應用程式

本 Azure Functions 範例指令碼會建立函式應用程式，並在 Azure 檔案儲存體中建立共用； 接著會掛接共用，讓您的函式可以存取資料。  

>[!NOTE]
>所建立的函式應用程式會在 Python 3.7 版本上執行。 Azure Functions 也[支援 Python 版本 3.6 和 3.8](../functions-reference-python.md#python-version)。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

如果您選擇在本機安裝和使用 CLI，本文會要求您執行 Azure CLI 2.0 版或更新版本。 執行 `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI]( /cli/azure/install-azure-cli)。 範例是針對 Bash 殼層所撰寫，而且必須修改成在 Windows 命令提示字元中執行。 

## <a name="sample-script"></a>範例指令碼

此指令碼會使用[取用方案](../functions-scale.md#consumption-plan)建立 Azure 函式應用程式。

[!code-azurecli-interactive[main](../../../cli_scripts/azure-functions/functions-cli-mount-files-storage-linux/functions-cli-mount-files-storage-linux.sh "Create an Azure Function on a Consumption plan")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>指令碼說明

下表中的每個命令都會連結至命令特定的文件。 此指令碼會使用下列命令：

| Command | 注意 |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | 建立用來存放所有資源的資源群組。 |
| [az storage account create](/cli/azure/storage/account#az-storage-account-create) | 建立 Azure 儲存體帳戶。 |
| [az functionapp create](/cli/azure/functionapp#az-functionapp-create) | 建立函式應用程式。 |
| [az storage share create](/cli/azure/storage/share#az-storage-share-create) | 在儲存體帳戶中建立 Azure 檔案儲存體共用。 | 
| [az storage directory create](/cli/azure/storage/directory#az-storage-directory-create) | 在共用中建立目錄。 |
| [az webapp config storage-account add](/cli/azure/webapp/config/storage-account#az-webapp-config-storage-account-add) | 將共用掛接至函式應用程式。 |
| [az webapp config storage-account list](/cli/azure/webapp/config/storage-account#az-webapp-config-storage-account-list) | 顯示掛接至函式應用程式的檔案共用。 | 

## <a name="next-steps"></a>後續步驟

如需 Azure CLI 的詳細資訊，請參閱 [Azure CLI 文件](/cli/azure)。

您可以在 [Azure Functions 文件](../functions-cli-samples.md)中找到其他 Azure Functions CLI 指令碼範例。
