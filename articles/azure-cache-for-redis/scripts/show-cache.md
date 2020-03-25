---
title: 取得 Azure Cache for Redis 的詳細資料 - Azure CLI
description: 此 Azure CLI 程式碼範例示範如何擷取 Azure Cache for Redis 執行個體的詳細資料 (包括其佈建狀態)。
author: yegu-ms
ms.author: yegu
tags: azure-service-management
ms.service: cache
ms.devlang: azurecli
ms.topic: sample
ms.date: 08/30/2017
ms.openlocfilehash: 54920017f4a0598a74a54114e3d1e0a5392d7d2a
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/24/2020
ms.locfileid: "75411064"
---
# <a name="get-details-of-an-azure-cache-for-redis"></a>取得 Azure Cache for Redis 的詳細資料

在此案例中，您將了解關於如何擷取 Azure Cache for Redis 執行個體 (包括其佈建狀態) 的詳細資料。

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>範例指令碼

[!code-azurecli[main](../../../cli_scripts/redis-cache/show-cache/show-cache.sh "Azure Cache for Redis")]

## <a name="script-explanation"></a>指令碼說明

此指令碼會使用下列命令，擷取 Azure Cache for Redis 執行個體的詳細資料。 下表中的每個命令都會連結至命令特定的文件。

| Command | 注意 |
|---|---|
| [az redis show](https://docs.microsoft.com/cli/azure/redis) | 取得 Azure Cache for Redis 執行個體的詳細資料。 |


## <a name="next-steps"></a>後續步驟

如需 Azure CLI 的詳細資訊，請參閱 [Azure CLI 文件](https://docs.microsoft.com/cli/azure)。

您可以在 [Azure Cache for Redis 文件](../cli-samples.md)中找到其他 Azure Cache for Redis CLI 指令碼範例。
