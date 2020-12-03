---
title: 建立具有叢集的進階 Azure Cache for Redis - Azure CLI
description: 此 Azure CLI 程式碼範例示範如何建立啟用叢集且具有兩分區的 6 GB 進階層 Azure Cache for Redis。
author: yegu-ms
ms.author: yegu
tags: azure-service-management
ms.service: cache
ms.devlang: azurecli
ms.topic: sample
ms.date: 08/30/2017
ms.custom: devx-track-azurecli
ms.openlocfilehash: bb5a6ac082ebaf978023321f15341ec7f35779a6
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/26/2020
ms.locfileid: "96184209"
---
# <a name="create-a-premium-azure-cache-for-redis-with-clustering"></a>建立具有叢集的進階 Azure Cache for Redis

在此案例中，您將學習如何建立啟用叢集和兩分區的 6 GB 進階層 Azure Cache for Redis。

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>範例指令碼

[!code-azurecli[main](../../../cli_scripts/redis-cache/create-premium-cache-cluster/create-premium-cache-cluster.sh "Azure Cache for Redis")]

[!INCLUDE [cli-script-clean-up](../../../includes/redis-cli-script-clean-up.md)]

## <a name="script-explanation"></a>指令碼說明

此指令碼使用下列命令來建立資源群組和啟用叢集的進階層 Azure Cache for Redis。 下表中的每個命令都會連結至命令特定的文件。

| Command | 注意 |
|---|---|
| [az group create](/cli/azure/group) | 建立用來存放所有資源的資源群組。 |
| [az redis create](/cli/azure/redis) | 建立 Azure Cache for Redis 執行個體。 |


## <a name="next-steps"></a>後續步驟

如需 Azure CLI 的詳細資訊，請參閱 [Azure CLI 文件](/cli/azure)。

您可以在 [Azure Cache for Redis 文件](../cli-samples.md)中找到其他 Azure Cache for Redis CLI 指令碼範例。