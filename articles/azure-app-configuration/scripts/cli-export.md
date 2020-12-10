---
title: Azure CLI 指令碼範例 - 從 Azure 應用程式組態存放區匯出
titleSuffix: Azure App Configuration
description: 使用 Azure CLI 指令碼從 Azure 應用程式組態匯出組態
services: azure-app-configuration
author: AlexandraKemperMS
ms.service: azure-app-configuration
ms.devlang: azurecli
ms.topic: sample
ms.date: 02/19/2020
ms.author: alkemper
ms.custom: devx-track-azurecli
ms.openlocfilehash: be3fac17ba99194a76b27c78040a76cce43405a0
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/09/2020
ms.locfileid: "96931107"
---
# <a name="export-from-an-azure-app-configuration-store"></a>從 Azure 應用程式設定存放區匯出

此範例指令碼會從 Azure 應用程式設定存放區匯出索引鍵/值。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - 本教學課程需要 2.0 版或更新版本的 Azure CLI。 如果您是使用 Azure Cloud Shell，就已安裝最新版本。

## <a name="sample-script"></a>範例指令碼

```azurecli-interactive
#!/bin/bash

# Export all key-values
az appconfig kv export --name myTestAppConfigStore --file ~/Export.json
```

[!INCLUDE [cli-script-cleanup](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>指令碼說明

此指令碼會使用下列命令從應用程式組態存放區匯出。 下表中的每個命令都會連結至命令特定的文件。

| Command | 注意 |
|---|---|
| [az appconfig kv export](/cli/azure/appconfig/kv#az-appconfig-kv-export) | 從應用程式組態存放區資源匯出。 |

## <a name="next-steps"></a>後續步驟

如需 Azure CLI 的詳細資訊，請參閱 [Azure CLI 文件](/cli/azure)。

您可以在 [Azure 應用程式組態 CLI 範例](../cli-samples.md)中找到其他的應用程式組態 CLI 指令碼範例。
