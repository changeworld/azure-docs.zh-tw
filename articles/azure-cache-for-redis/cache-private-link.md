---
title: Azure Cache for Redis 與 Azure 私人連結（預覽）
description: Azure 私用端點是一種網路介面，可讓您私下且安全地連線到 azure Cache for Redis （由 Azure 私用連結提供技術支援）。 在本文中，您將瞭解如何使用 Azure 入口網站來建立 Azure 快取、Azure 虛擬網路和私人端點。
author: curib
ms.author: cauribeg
ms.service: cache
ms.topic: conceptual
ms.date: 07/21/2020
ms.openlocfilehash: 5db756b60330cdac4e43e13bfe29d9397f87af50
ms.sourcegitcommit: e71da24cc108efc2c194007f976f74dd596ab013
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/29/2020
ms.locfileid: "87421649"
---
# <a name="azure-cache-for-redis-with-azure-private-link-preview"></a>Azure Cache for Redis 與 Azure 私人連結（預覽）
Azure 私用端點是一種網路介面，可讓您私下且安全地連線到 azure Cache for Redis （由 Azure 私用連結提供技術支援）。 

在本文中，您將瞭解如何使用 Azure 入口網站來建立 Azure 快取、Azure 虛擬網路和私人端點。  

> [!IMPORTANT]
> 此預覽版是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 如需詳細資訊，請參閱[Microsoft Azure 預覽的補充使用規定。](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) 
> 

## <a name="prerequisites"></a>必要條件
* Azure 訂用帳戶- [免費建立一個](https://azure.microsoft.com/free/)

> [!NOTE]
> 這項功能目前為預覽狀態，如果您有興趣，[請洽詢我們](mailto:azurecache@microsoft.com)。
>

## <a name="create-a-cache"></a>建立快取
1. 若要建立快取，請登入 [Azure 入口網站](https://portal.azure.com)，然後選取 [建立資源]****。 

    :::image type="content" source="media/cache-private-link/1-create-resource.png" alt-text="選取 [建立資源]。":::
   
1. 在 [新增] 頁面上選取 [資料庫]，然後選取 [Azure Cache for Redis]。

    :::image type="content" source="media/cache-private-link/2-select-cache.png" alt-text="選取 [Azure Cache for Redis]。":::
   
1. 在 [新的 Redis 快取] 頁面上，設定新快取的設定。
   
   | 設定      | 建議的值  | 描述 |
   | ------------ |  ------- | -------------------------------------------------- |
   | **DNS 名稱** | 輸入全域唯一名稱。 | 快取名稱必須是 1 到 63 個字元的字串，且只能包含數字、字母或連字號。 名稱的開頭和結尾必須是數字或字母，且不可包含連續的連字號。 您的快取實例的*主機名稱*會是* \<DNS name> . redis.cache.windows.net*。 | 
   | **訂用帳戶** | 下拉並選取您的訂用帳戶。 | 這個新的 Azure Cache for Redis 執行個體建立所在的訂用帳戶。 | 
   | **資源群組** | 下拉並選取資源群組，或選取 [新建] 並輸入新的資源群組名稱。 | 用來建立快取和其他資源的資源群組名稱。 將所有的應用程式資源放在一個資源群組中，您將可輕鬆地一併管理或刪除這些資源。 | 
   | **位置** | 下拉並選取位置。 | 選取其他將使用快取的服務附近的[區域](https://azure.microsoft.com/regions/)。 |
   | **定價層** | 下拉並選取 [定價層](https://azure.microsoft.com/pricing/details/cache/)。 |  快取的可用大小、效能和功能取決於定價層。 如需詳細資訊，請參閱 [Azure Cache for Redis 概觀](cache-overview.md)。 |
   
1. 選取 [建立]。 
   
    :::image type="content" source="media/cache-private-link/3-new-cache.png" alt-text="建立 Azure Cache for Redis。":::
   
   建立快取需要一些時間。 您可以在 Azure Cache for Redis 的 [概觀] 頁面上監視進度。 當 [狀態] 顯示為 [執行中] 時，表示快取已可供使用。
    
    :::image type="content" source="media/cache-private-link/4-status.png" alt-text="已建立 Azure Cache for Redis。":::

## <a name="create-a-virtual-network"></a>建立虛擬網路

在本節中，您將建立虛擬網路和子網。

1. 選取 [建立資源]。

    :::image type="content" source="media/cache-private-link/1-create-resource.png" alt-text="選取 [建立資源]。":::

2. 在 [**新增**] 頁面上，選取 [**網路**]，然後選取 [**虛擬網路**]。

    :::image type="content" source="media/cache-private-link/5-select-vnet.png" alt-text="建立虛擬網路。":::

3. 在 [**建立虛擬網路**] 中，于 [**基本**] 索引標籤中輸入或選取這項資訊：

    | **設定**          | **值**                                                           |
    |------------------|-----------------------------------------------------------------|
    | **專案詳細資料**  |                                                                 |
    | 訂用帳戶     | 下拉並選取您的訂用帳戶。                                  |
    | 資源群組   | 下拉並選取資源群組。 |
    | **執行個體詳細資料** |                                                                 |
    | 名稱             | 輸入 **\<virtual-network-name>**                                    |
    | 區域           | 請**\<region-name>** |

4. 選取 [ **IP 位址**] 索引標籤，或選取頁面底部的 [**下一步： IP 位址**] 按鈕。

5. 在 [ **IP 位址**] 索引標籤中，輸入下列資訊：

    | 設定            | 值                      |
    |--------------------|----------------------------|
    | IPv4 位址空間 | 輸入 **\<IPv4-address-space>** |

6. 在 [**子網名稱**] 底下，選取 [**預設**] 這個字。

7. 在 [**編輯子網**] 中，輸入下列資訊：

    | 設定            | 值                      |
    |--------------------|----------------------------|
    | 子網路名稱 | 輸入 **\<subnet-name>** |
    | 子網路位址範圍 | 輸入 **\<subnet-address-range>**

8. 選取 [儲存]。

9. 選取 [**審核**] 和 [建立] 索引標籤，或選取 [**審核 + 建立**] 按鈕。

10. 選取 [建立]。


## <a name="create-a-private-endpoint"></a>建立私人端點 

在本節中，您將建立私人端點，並將它連接到您稍早建立的快取。

1. 搜尋**私人連結**，然後按 enter 鍵或從搜尋建議中選取。

    :::image type="content" source="media/cache-private-link/7-create-private-link.png" alt-text="搜尋私人連結。":::

2. 在畫面的左側，選取 [**私人端點**]。

    :::image type="content" source="media/cache-private-link/8-select-private-endpoint.png" alt-text="選取 [私人連結]。":::

3. 選取 [ **+ 新增**] 按鈕以建立您的私用端點。 

    :::image type="content" source="media/cache-private-link/9-add-private-endpoint.png" alt-text="新增私用連結。":::

4. 在 [**建立私人端點] 頁面**上，設定私人端點的設定。

    | 設定 | 值 |
    | ------- | ----- |
    | **專案詳細資料** | |
    | 訂用帳戶 | 下拉並選取您的訂用帳戶。 |
    | 資源群組 | 下拉並選取資源群組。 |
    | **執行個體詳細資料** |  |
    | 名稱 |輸入私人端點的 [名稱]。  |
    | 區域 |下拉並選取位置。 |
    |||

5. 選取頁面底部的 [**下一步：資源]** 按鈕。

6. 在 [**資源**] 索引標籤中，選取您的訂用帳戶，選擇 [資源類型] 為 [Redis]，然後選取您在上一節中所做的快取。

    :::image type="content" source="media/cache-private-link/10-resource-private-endpoint.png" alt-text="私人連結的資源。":::

7. 選取頁面底部的 [**下一步：設定]** 按鈕。

8. **在 [設定] 索引**標籤中，選取您在上一節中建立的虛擬網路和子網。

    :::image type="content" source="media/cache-private-link/11-configuration-private-endpoint.png" alt-text="私人連結的設定。":::

9. 選取頁面底部的 [下一步: 標記]**** 按鈕。

10. 在 [**標記**] 索引標籤中，如果您想要將資源分類，請輸入名稱和值。 此為選用步驟。

    :::image type="content" source="media/cache-private-link/12-tags-private-endpoint.png" alt-text="私人連結的標記。":::

11. 選取 [檢閱 + 建立] ****。 您會進入 [ **審核] + [建立**] 索引標籤，   其中 Azure 會驗證您的設定。

12. 一旦出現綠色**驗證**的訊息，請選取 [**建立**]。


## <a name="next-steps"></a>後續步驟

若要深入瞭解私人連結，請參閱[Azure 私人連結檔](https://docs.microsoft.com/azure/private-link/private-link-overview)。 

