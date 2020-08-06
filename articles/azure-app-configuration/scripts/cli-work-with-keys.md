---
title: Azure CLI 指令碼範例 - 在應用程式組態存放區中使用索引鍵/值
titleSuffix: Azure App Configuration
description: 使用 Azure CLI 指令碼從應用程式組態存放區建立、檢視、更新和刪除索引鍵/值
services: azure-app-configuration
author: lisaguthrie
ms.service: azure-app-configuration
ms.devlang: azurecli
ms.topic: sample
ms.date: 02/19/2020
ms.author: lcozzens
ms.custom: devx-track-azurecli
ms.openlocfilehash: e3407a1da469bf4f31e5d54d83ee837bddbafffc
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/31/2020
ms.locfileid: "87494752"
---
# <a name="work-with-key-values-in-an-azure-app-configuration-store"></a>在 Azure 應用程式設定存放區中搭配使用索引鍵/值

此範例指令碼說明如何：
* 建立新的索引鍵/值組
* 列出所有現有的索引鍵/值組
* 更新新建立索引鍵的值
* 刪除新的索引鍵/值組

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

本文需要使用 Azure CLI 2.0 版或更新版本。 執行 `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI](/cli/azure/install-azure-cli)。 

## <a name="sample-script"></a>範例指令碼

```azurecli-interactive
#!/bin/bash

appConfigName=myTestAppConfigStore
newKey="TestKey"
refKey="KeyVaultReferenceTestKey"
uri="[URL to value stored in Key Vault]"
uri2="[URL to another value stored in Key Vault]"

# Create a new key-value 
az appconfig kv set --name $appConfigName --key $newKey --value "Value 1"

# List current key-values
az appconfig kv list --name $appConfigName

# Update new key's value
az appconfig kv set --name $appConfigName --key $newKey --value "Value 2"

# List current key-values
az appconfig kv list --name $appConfigName

# Create a new key-value referencing a value stored in Azure Key Vault
az appconfig kv set --name $appConfigName --key $refKey --content-type "application/vnd.microsoft.appconfig.keyvaultref+json;charset=utf-8" --value "{\"uri\":\"$uri\"}"

# List current key-values
az appconfig kv list --name $appConfigName

# Update Key Vault reference
az appconfig kv set --name $appConfigName --key $refKey --value "{\"uri\":\"$uri2\"}"

# List current key-values
az appconfig kv list --name $appConfigName

# Delete new key
az appconfig kv delete  --name $appConfigName --key $newKey

# Delete Key Vault reference
az appconfig kv delete --name $appConfigName --key $refKey

# List current key-values
az appconfig kv list --name $appConfigName
```

[!INCLUDE [cli-script-cleanup](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>指令碼說明

下表列出範例指令碼所使用的命令。 

| Command | 注意 |
|---|---|
| [az appconfig kv set](/cli/azure/appconfig/kv#az-appconfig-kv-set) | 建立或更新索引鍵/值組。 |
| [az appconfig kv list](/cli/azure/appconfig/kv#az-appconfig-kv-list) | 列出應用程式組態存放區中的索引鍵/值組。 |
| [az appconfig kv delete](/cli/azure/appconfig/kv#az-appconfig-kv-delete) | 刪除索引鍵/值組。 |

## <a name="next-steps"></a>後續步驟

如需 Azure CLI 的詳細資訊，請參閱 [Azure CLI 文件](/cli/azure)。

您可以在 [Azure 應用程式組態 CLI 範例](../cli-samples.md)中找到其他的應用程式組態 CLI 指令碼範例。
