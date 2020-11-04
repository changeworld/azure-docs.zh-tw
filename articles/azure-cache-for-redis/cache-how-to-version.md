---
title: '針對 Azure Cache for Redis (Preview 設定 Redis 版本) '
description: 瞭解如何設定 Redis 版本
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 09/30/2020
ms.openlocfilehash: d9f48de7ef5d9525a995af4ebbd12c5f14f40189
ms.sourcegitcommit: 99955130348f9d2db7d4fb5032fad89dad3185e7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/04/2020
ms.locfileid: "93349132"
---
# <a name="set-redis-version-for-azure-cache-for-redis-preview"></a>針對 Azure Cache for Redis (Preview 設定 Redis 版本) 
在本文中，您將瞭解如何設定要與快取實例搭配使用的 Redis 軟體版本。 Azure Cache for Redis 提供最新的 Redis 主要版本，以及至少一個舊版。 它會在發行較新的 Redis 軟體時定期更新這些版本。 您可以選擇兩個可用版本。 請記住，如果不再支援目前使用的版本，您的快取將會自動升級為下一個版本。

## <a name="prerequisites"></a>Prerequisites
* Azure 訂用帳戶 - [建立免費帳戶](https://azure.microsoft.com/free/)

## <a name="create-a-cache"></a>建立快取
若要建立快取，請遵循下列步驟：

1. 登入 [Azure 入口網站](https://portal.azure.com)，然後選取 [建立資源]。
  
1. 在 [新增]  頁面上選取 [資料庫]  ，然後選取 [Azure Cache for Redis]  。

    :::image type="content" source="media/cache-create/new-cache-menu.png" alt-text="選取 Azure Cache for Redis。":::
   
1. 在 [ **基本** ] 頁面上，設定新快取的設定。
   
    | 設定      | 建議的值  | 描述 |
    | ------------ |  ------- | -------------------------------------------------- |
    | **訂用帳戶** | 選取您的訂用帳戶。 | 這個新的 Azure Cache for Redis 執行個體建立所在的訂用帳戶。 | 
    | **資源群組** | 選取資源群組，或選取 [ **建立新** 的]，並輸入新的資源組名。 | 用來建立快取和其他資源的資源群組名稱。 將所有的應用程式資源放在一個資源群組中，您將可輕鬆地一併管理或刪除這些資源。 | 
    | **DNS 名稱** | 輸入全域唯一名稱。 | 快取名稱必須是 1 到 63 個字元的字串，且只能包含數字、字母或連字號。 名稱的開頭和結尾必須是數字或字母，且不可包含連續的連字號。 您的快取執行個體 *主機名稱* 將是 *\<DNS name>.redis.cache.windows.net* 。 | 
    | **位置** | 選取位置。 | 選取其他將使用快取的服務附近的[區域](https://azure.microsoft.com/regions/)。 |
    | **快取類型** | 選取快取 [層和大小](https://azure.microsoft.com/pricing/details/cache/)。 |  快取的可用大小、效能和功能取決於定價層。 如需詳細資訊，請參閱 [Azure Cache for Redis 概觀](cache-overview.md)。 |
   
1. 在 [ **Advanced （Advanced** ）] 頁面上，選擇要使用的 Redis 版本。
   
    :::image type="content" source="media/cache-how-to-version/select-redis-version.png" alt-text="Redis 版本。":::

1. 按一下頁面底部的 [新增]  。 
   
    建立快取需要一些時間。 您可以在 Azure Cache for Redis 的 [概觀] 頁面上監視進度。 當 [狀態] 顯示為 [執行中] 時，表示快取已可供使用。

    > [!NOTE]
    > 目前，在建立快取之後，就無法變更 Redis 版本。
    >

## <a name="next-steps"></a>後續步驟
深入瞭解 Azure Cache for Redis 功能。

> [!div class="nextstepaction"]
> [Azure Cache for Redis Premium 服務層級](cache-overview.md#service-tiers)
