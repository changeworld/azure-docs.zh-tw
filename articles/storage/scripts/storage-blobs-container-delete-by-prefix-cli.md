---
title: Azure CLI 指令碼範例 - 根據前置詞刪除容器 | Microsoft Docs
description: 根據容器名稱前置詞來刪除 Azure 儲存體 Blob 容器，然後清除部署。 請參閱指令碼範例中所用命令的說明連結。
services: storage
author: tamram
ms.service: storage
ms.subservice: blobs
ms.devlang: cli
ms.topic: sample
ms.date: 06/22/2017
ms.author: tamram
ms.custom: devx-track-azurecli
ms.openlocfilehash: aeccf255004cd4512fbc591942324341504b20f7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "87901873"
---
# <a name="use-an-azure-cli-script-to-delete-containers-based-on-container-name-prefix"></a>使用 Azure CLI 指令碼，根據容器名稱前置詞來刪除容器

此指令碼會先在 Azure Blob 儲存體中建立數個範例容器，然後根據容器名稱中的前置詞來刪除部分容器。

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>範例指令碼

[!code-azurecli-interactive[main](../../../cli_scripts/storage/delete-containers-by-prefix/delete-containers-by-prefix.sh?highlight=2-3 "Delete containers by prefix")]

## <a name="clean-up-deployment"></a>清除部署

執行下列命令來移除資源群組、剩餘的容器，以及所有相關資源。

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>指令碼說明

此指令碼使用下列命令，根據容器名稱前置詞來刪除容器。 下表中的每個項目都會連結至特定命令的文件。

| Command | 注意 |
|---|---|
| [az group create](/cli/azure/group) | 建立用來存放所有資源的資源群組。 |
| [az storage account create](/cli/azure/storage/account) | 在指定的資源群組中建立 Azure 儲存體帳戶。 |
| [az storage container create](/cli/azure/storage/container) | 在 Azure Blob 儲存體中建立容器。 |
| [az storage container list](/cli/azure/storage/container) | 列出 Azure 儲存體帳戶中的容器。 |
| [az storage container delete](/cli/azure/storage/container) | 刪除 Azure 儲存體帳戶中的容器。 |

## <a name="next-steps"></a>後續步驟

如需 Azure CLI 的詳細資訊，請參閱 [Azure CLI 文件](/cli/azure)。

在[適用於 Azure 儲存體的 Azure CLI 範例](../blobs/storage-samples-blobs-cli.md)中，提供了其他儲存體 CLI 指令碼範例。
