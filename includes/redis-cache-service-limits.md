---
author: wesmc7777
ms.service: redis-cache
ms.topic: include
ms.date: 11/09/2018
ms.author: wesmc
ms.openlocfilehash: 1ab6243be39bf30bc060ed5745fbf600924743a9
ms.sourcegitcommit: 6a4fbc5ccf7cca9486fe881c069c321017628f20
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/27/2020
ms.locfileid: "71839232"
---
| 資源 | 限制 |
| --- | --- |
| 快取大小 |1.2 TB |
| 資料庫 |64 |
| 連線的用戶端數目上限 |40,000 |
| Azure Cache for Redis 複本，適用于高可用性 |1 |
| 高級快取中包含叢集的分區 |10 |

針對各個定價層，Azure Cache for Redis 限制與大小有所不同。 若要查看定價層和其相關聯的大小，請參閱[Azure Cache For Redis 定價](https://azure.microsoft.com/pricing/details/cache/)。

如需 Azure Cache for Redis 組態限制的相關資訊，請參閱[預設 Redis 伺服器組態](../articles/azure-cache-for-redis/cache-configure.md#default-redis-server-configuration)。

因為 Azure Cache for Redis 執行個體是由 Microsoft 設定與管理，所以 Azure Cache for Redis 並未支援所有的 Redis 命令。 如需詳細資訊，請參閱 [Azure Cache for Redis 中不支援的 Redis 命令](../articles/azure-cache-for-redis/cache-configure.md#redis-commands-not-supported-in-azure-cache-for-redis)。

