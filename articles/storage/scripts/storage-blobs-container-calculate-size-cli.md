---
title: Azure CLI 指令碼範例 - 計算 Blob 容器大小 | Microsoft Docs
description: 您可以加總容器中 Blob 的大小，計算出 Azure Blob 儲存體的容器大小。
services: storage
author: tamram
ms.service: storage
ms.subservice: blobs
ms.devlang: cli
ms.topic: sample
ms.date: 06/28/2017
ms.author: tamram
ms.custom: devx-track-azurecli
ms.openlocfilehash: 45712632ebfb2da4b713038503965ce908c1dfc6
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/31/2020
ms.locfileid: "87498883"
---
# <a name="calculate-the-size-of-a-blob-storage-container"></a>計算 Blob 儲存體容器的大小

此指令碼會加總容器中 Blob 的大小，計算出 Azure Blob 儲存體的容器大小。

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

> [!IMPORTANT]
> 此 CLI 指令碼提供的是容器的估計大小，且不應用於計費計算。

## <a name="sample-script"></a>範例指令碼

[!code-azurecli[main](../../../cli_scripts/storage/calculate-container-size/calculate-container-size.sh?highlight=2-3 "Calculate container size")]

## <a name="clean-up-deployment"></a>清除部署

執行下列命令來移除資源群組、容器和所有相關資源。

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>指令碼說明

此指令碼會使用下列命令來計算 Blob 儲存體容器的大小。 下表中的每個項目都會連結至特定命令的文件。

| Command | 注意 |
|---|---|
| [az group create](/cli/azure/group) | 建立用來存放所有資源的資源群組。 |
| [az storage blob upload](/cli/azure/storage/account) | 將本機檔案上傳至 Azure Blob 儲存體容器。 |
| [az storage blob list](/cli/azure/storage/account/keys) | 列出 Azure Blob 儲存體容器中的 Blob。 |

## <a name="next-steps"></a>後續步驟

如需 Azure CLI 的詳細資訊，請參閱 [Azure CLI 文件](/cli/azure)。

您可以在[適用於 Azure Blob 儲存體的 Azure CLI 範例](../blobs/storage-samples-blobs-cli.md)中，找到其他儲存體 CLI 指令碼範例。
