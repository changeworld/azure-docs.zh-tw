---
title: Azure CLI 指令碼範例 - 刪除 Azure 應用程式組態存放區
titleSuffix: Azure App Configuration
description: 使用 Azure CLI 指令碼來刪除 Azure 應用程式組態存放區
services: azure-app-configuration
author: lisaguthrie
ms.service: azure-app-configuration
ms.devlang: azurecli
ms.topic: sample
ms.date: 02/19/2020
ms.author: lcozzens
ms.openlocfilehash: 7f73de459d8ce9f74e3925789af630b7c804d605
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/24/2020
ms.locfileid: "77523567"
---
# <a name="delete-an-azure-app-configuration-store"></a>刪除 Azure 應用程式設定存放區

此範例指令碼會刪除 Azure 應用程式設定的執行個體。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

如果您選擇在本機安裝和使用 CLI，本文會要求您執行 Azure CLI 2.0 版或更新版本。 執行 `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI](/cli/azure/install-azure-cli)。

## <a name="sample-script"></a>範例指令碼

```azurecli-interactive
#/bin/bash

# Delete an App Configuration store named myTestAppConfigStore from the Resource Group myResourceGroup
az appconfig delete --name myTestAppConfigStore --resource-group myResourceGroup
```

[!INCLUDE [cli-script-cleanup](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>指令碼說明

此指令碼會使用下列命令來刪除應用程式組態存放區。 下表中的每個命令都會連結至命令特定的文件。

| Command | 注意 |
|---|---|
| [az appconfig delete](/cli/azure/appconfig#az-appconfig-delete) | 刪除應用程式組態存放區資源。 |

## <a name="next-steps"></a>後續步驟

如需 Azure CLI 的詳細資訊，請參閱 [Azure CLI 文件](/cli/azure)。

您可以在 [Azure 應用程式組態 CLI 範例](../cli-samples.md)中找到其他的應用程式組態 CLI 指令碼範例。
