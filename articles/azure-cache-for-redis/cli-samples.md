---
title: Azure CLI Azure Cache for Redis 範例
description: 適用于 Azure Cache for Redis 的 Azure CLI 範例：建立快取、刪除快取、取得快取記憶體詳細資料、主機名稱、埠和金鑰，以及連線 web 應用程式。
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.devlang: azurecli
ms.topic: conceptual
ms.date: 04/14/2017
ms.openlocfilehash: 43a986f884d621f257de2e9c305a0bcc59092d3d
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87033660"
---
# <a name="azure-cli-samples-for-azure-cache-for-redis"></a>Azure Redis 快取的 Azure CLI 範例

下表包含使用 Azure CLI 所建置之 Bash 指令碼的連結。

| Create cache | 描述 |
| ------------ | ----------- |
| [建立快取](./scripts/create-cache.md) | 建立資源群組和基本層「Azure Redis 快取」。 |
| [使用叢集建立高級快取](./scripts/create-premium-cache-cluster.md) | 透過啟用的叢集建立資源群組與高級層快取。|
| [取得快取詳細資料](./scripts/show-cache.md) | 取得「Azure Redis 快取」執行個體的詳細資料，包括佈建狀態。 |
| [取得主機名稱、連接埠及金鑰](./scripts/cache-keys-ports.md) | 取得「Azure Redis 快取」執行個體的主機名稱、連接埠及金鑰。 |
|**Web 應用程式加上快取**| **描述**|
| [將 Web 應用程式連線至 Azure Redis 快取](./../app-service/scripts/cli-connect-to-redis.md) | 建立 Azure Web 應用程式和「Azure Redis 快取」，然後將 Redis 連線詳細資料新增至應用程式設定。 |
|**刪除快取**| **描述** |
| [刪除快取](./scripts/delete-cache.md) | 刪除 Azure Redis 快取執行個體  |

如需有關 Azure CLI 的詳細資訊，請參閱[安裝 Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) 與[開始使用 Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli)。
