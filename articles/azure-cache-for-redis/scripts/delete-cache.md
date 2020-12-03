---
title: 刪除 Azure Cache for Redis - Azure CLI
description: 此 Azure CLI 程式碼範例示範如何使用 az redis delete 命令來刪除 Azure Cache for Redis 執行個體。
author: yegu-ms
ms.author: yegu
tags: azure-service-management
ms.service: cache
ms.devlang: azurecli
ms.topic: sample
ms.date: 08/30/2017
ms.custom: devx-track-azurecli
ms.openlocfilehash: 9ce6e20e5e4866bc4daa3f331fa2a8612ac6c260
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/26/2020
ms.locfileid: "96184156"
---
# <a name="delete-an-azure-cache-for-redis"></a>刪除 Azure Cache for Redis

在此案例中，您會了解如何刪除 Azure Cache for Redis。

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>範例指令碼

[!code-azurecli[main](../../../cli_scripts/redis-cache/delete-cache/delete-cache.sh "Azure Cache for Redis")]

[!INCLUDE [cli-script-clean-up](../../../includes/redis-cli-script-clean-up.md)]

## <a name="script-explanation"></a>指令碼說明

此指令碼會使用下列命令，刪除 Azure Cache for Redis 執行個體。 下表中的每個命令都會連結至命令特定的文件。

| Command | 注意 |
|---|---|
| [az redis 刪除](/cli/azure/redis) | 刪除 Azure Cache for Redis 執行個體。 |


## <a name="next-steps"></a>後續步驟

如需 Azure CLI 的詳細資訊，請參閱 [Azure CLI 文件](/cli/azure)。

您可以在 [Azure Cache for Redis 文件](../cli-samples.md)中找到其他 Azure Cache for Redis CLI 指令碼範例。