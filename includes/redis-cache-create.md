---
title: 包含檔案
description: 包含檔案
services: redis-cache
author: wesmc7777
ms.service: cache
ms.topic: include
ms.date: 11/05/2019
ms.author: wesmc
ms.custom: include file
ms.openlocfilehash: 3c064aa8e57a77b96161da06847f543816be1217
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/26/2020
ms.locfileid: "73719106"
---
1. 若要建立快取，請登入 [Azure 入口網站](https://portal.azure.com)，然後選取 [建立資源]  。 
   
   ![選取 [建立資源]](media/redis-cache-create/create-a-resource.png)
   
1. 在 [新增]  頁面上選取 [資料庫]  ，然後選取 [Azure Cache for Redis]  。
   
   ![選取 Azure Cache for Redis](media/redis-cache-create/redis-cache-new-cache-menu.png)
   
1. 在 [新的 Redis 快取]  頁面上，設定新快取的設定。
   
   | 設定      | 建議的值  | 描述 |
   | ------------ |  ------- | -------------------------------------------------- |
   | **DNS 名稱** | 輸入全域唯一名稱。 | 快取名稱必須是 1 到 63 個字元的字串，且只能包含數字、字母或連字號。 名稱的開頭和結尾必須是數字或字母，且不可包含連續的連字號。 您的快取執行個體的*主機名稱*將是 *\<DNS name>.redis.cache.windows.net*。 | 
   | **訂用帳戶** | 下拉並選取您的訂用帳戶。 | 這個新的 Azure Cache for Redis 執行個體建立所在的訂用帳戶。 | 
   | **資源群組** | 下拉並選取資源群組，或選取 [新建]  並輸入新的資源群組名稱。 | 用來建立快取和其他資源的資源群組名稱。 將所有的應用程式資源放在一個資源群組中，您將可輕鬆地一併管理或刪除這些資源。 | 
   | **位置** | 下拉並選取位置。 | 選取其他將使用快取的服務附近的[區域](https://azure.microsoft.com/regions/)。 |
   | **定價層** | 下拉並選取 [定價層](https://azure.microsoft.com/pricing/details/cache/)。 |  快取的可用大小、效能和功能取決於定價層。 如需詳細資訊，請參閱 [Azure Cache for Redis 概觀](../articles/azure-cache-for-redis/cache-overview.md)。 |
   
1. 選取 [建立]  。 
   
   ![建立 Azure Cache for Redis](media/redis-cache-create/redis-cache-cache-create.png) 
   
   建立快取需要一些時間。 您可以在 Azure Cache for Redis 的 [概觀]  頁面上監視進度。 當 [狀態]  顯示為 [執行中]  時，表示快取已可供使用。
   
   ![Azure Cache for Redis 已建立](media/redis-cache-create/redis-cache-cache-created.png)

