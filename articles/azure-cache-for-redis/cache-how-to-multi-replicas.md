---
title: '將複本新增至 Azure Cache for Redis (預覽) '
description: 瞭解如何將更多複本新增至進階層 Azure Cache for Redis 實例
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 08/11/2020
ms.openlocfilehash: a747cf8e1713eb905aee02af95c568a448b47f05
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91342586"
---
# <a name="add-replicas-to-azure-cache-for-redis-preview"></a>將複本新增至 Azure Cache for Redis (預覽) 
在本文中，您將瞭解如何使用 Azure 入口網站來設定具有額外複本的 Azure 快取實例。

Azure Cache for Redis Standard 和 Premium 層提供冗余，方法是將每個快取裝載在兩部專用的虛擬機器上 (Vm) 。 這些 Vm 會設定為主要和複本。 當主要 VM 無法使用時，複本會偵測到它，並自動接管為新的主 VM。 您現在可以將高階快取中的複本數目增加到三個，讓您總共有四部 Vm 支援快取。 擁有多個複本會比單一複本所能提供的彈性更高。

> [!IMPORTANT]
> 此預覽版是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 如需詳細資訊，請參閱 [Microsoft Azure 預覽的補充使用條款。](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) 
> 

## <a name="prerequisites"></a>Prerequisites
* Azure 訂用帳戶- [免費建立一個](https://azure.microsoft.com/free/)訂用帳戶

> [!NOTE]
> 這項功能目前為預覽狀態，如果您有興趣， [請洽詢我們](mailto:azurecache@microsoft.com) 。
>

## <a name="create-a-cache"></a>建立快取
若要建立快取，請遵循下列步驟：

1. 登入 [Azure 入口網站](https://portal.azure.com)，然後選取 [建立資源]****。
  
1. 在 [新增]  頁面上選取 [資料庫]  ，然後選取 [Azure Cache for Redis]  。

    :::image type="content" source="media/cache-create/new-cache-menu.png" alt-text="選取 Azure Cache for Redis。":::
   
1. 在 [新的 Redis 快取]  頁面上，設定新快取的設定。
   
    | 設定      | 建議的值  | 描述 |
    | ------------ |  ------- | -------------------------------------------------- |
    | **DNS 名稱** | 輸入全域唯一名稱。 | 快取名稱必須是 1 到 63 個字元的字串，且只能包含數字、字母或連字號。 名稱的開頭和結尾必須是數字或字母，且不可包含連續的連字號。 您的快取執行個體*主機名稱*將是 *\<DNS name>.redis.cache.windows.net*。 | 
    | **訂用帳戶** | 下拉並選取您的訂用帳戶。 | 這個新的 Azure Cache for Redis 執行個體建立所在的訂用帳戶。 | 
    | **資源群組** | 下拉並選取資源群組，或選取 [新建]  並輸入新的資源群組名稱。 | 用來建立快取和其他資源的資源群組名稱。 將所有的應用程式資源放在一個資源群組中，您將可輕鬆地一併管理或刪除這些資源。 | 
    | **位置** | 下拉並選取位置。 | 選取其他將使用快取的服務附近的[區域](https://azure.microsoft.com/regions/)。 |
    | **定價層** | 下拉並選取進階層[Premium tier](https://azure.microsoft.com/pricing/details/cache/)快取。 |  快取的可用大小、效能和功能取決於定價層。 如需詳細資訊，請參閱 [Azure Cache for Redis 概觀](cache-overview.md)。 |
    | **複本計數** | 滑動以選擇複本數目。 | 預設值為 1。 |
   
1. 選取進階層快取後，系統會詢問您是否要啟用 Redis 叢集。 將 **叢集** 保持為 *停用*。 
   
    :::image type="content" source="media/cache-how-to-premium-clustering/redis-clustering-disabled.png" alt-text="設定 Redis 叢集。":::

    > [!NOTE]
    > 多重複本支援目前僅適用于非叢集快取。
    >

1. 按一下頁面底部的 [新增] 。 
   
    :::image type="content" source="media/cache-how-to-multi-replicas/create-multi-replicas.png" alt-text="建立 Azure Cache for Redis。":::
   
    建立快取需要一些時間。 您可以在 Azure Cache for Redis 的 [概觀]  頁面上監視進度。 當 [狀態]  顯示為 [執行中]  時，表示快取已可供使用。

    > [!NOTE]
    > 快取中的複本數目在建立後即無法變更。
    >

## <a name="next-steps"></a>後續步驟
深入瞭解 Azure Cache for Redis 功能。

> [!div class="nextstepaction"]
> [Azure Cache for Redis Premium 服務層級](cache-overview.md#service-tiers)
