---
title: Azure CLI 指令碼範例 - 匯入至應用程式組態存放區
titleSuffix: Azure App Configuration
description: 使用 Azure CLI 指令碼 - 將組態匯入至 Azure 應用程式組態
services: azure-app-configuration
author: lisaguthrie
ms.service: azure-app-configuration
ms.devlang: azurecli
ms.topic: sample
ms.date: 02/19/2020
ms.author: lcozzens
ms.custom: devx-track-azurecli
ms.openlocfilehash: b7a42e3592eee0bc6f905a83e9b1845746dbfd91
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/12/2020
ms.locfileid: "94566858"
---
# <a name="import-to-an-azure-app-configuration-store"></a>匯入到 Azure 應用程式設定存放區

此範例指令碼會將索引鍵/值設定匯入至 Azure 應用程式組態存放區。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - 本教學課程需要 2.0 版或更新版本的 Azure CLI。 如果您是使用 Azure Cloud Shell，就已安裝最新版本。

## <a name="sample-script"></a>範例指令碼

```azurecli-interactive
#!/bin/bash

# Import key-values from a file
az appconfig kv import --name myTestAppConfigStore --source file --path ~/Import.json
```

[!INCLUDE [cli-script-cleanup](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>指令碼說明

此指令碼會使用下列命令來匯入至應用程式組態存放區。 下表中的每個命令都會連結至命令特定的文件。

| Command | 注意 |
|---|---|
| [az appconfig kv import](/cli/azure/appconfig/kv#az-appconfig-kv-import) | 匯入至應用程式組態存放區資源。 |

## <a name="next-steps"></a>後續步驟

如需 Azure CLI 的詳細資訊，請參閱 [Azure CLI 文件](/cli/azure)。

您可以在 [Azure 應用程式組態 CLI 範例](../cli-samples.md)中找到其他的應用程式組態 CLI 指令碼範例。
