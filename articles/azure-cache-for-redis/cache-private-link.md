---
title: '使用 Azure Private Link (Preview 的 Azure Cache for Redis) '
description: Azure 私人端點是一種網路介面，可讓您以私人且安全的方式連接到 Azure Cache for Redis，Azure Private Link。 在本文中，您將瞭解如何使用 Azure 入口網站建立 Azure 快取、Azure 虛擬網路和私人端點。
author: curib
ms.author: cauribeg
ms.service: cache
ms.topic: conceptual
ms.date: 10/14/2020
ms.openlocfilehash: efba69372f46c9b8a7f2857e37b34ec8c88654a0
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/26/2020
ms.locfileid: "92546274"
---
# <a name="azure-cache-for-redis-with-azure-private-link-public-preview"></a>Azure Cache for Redis 與 Azure Private Link (公開預覽) 
在本文中，您將瞭解如何使用 Azure 入口網站來建立虛擬網路和具有私人端點的 Azure Cache for Redis 實例。 您也將瞭解如何將私人端點新增至現有的 Azure Cache for Redis 實例。

Azure 私人端點是一種網路介面，可讓您以私人且安全的方式連接到 Azure Cache for Redis，Azure Private Link。 

## <a name="prerequisites"></a>必要條件
* Azure 訂用帳戶 - [建立免費帳戶](https://azure.microsoft.com/free/)

> [!IMPORTANT]
> 若要使用私人端點，您的 Azure Cache for Redis 實例必須在2020年7月28日之後建立。
> 目前，異地複寫、防火牆規則、入口網站主控台支援、每個叢集快取有多個端點、不支援持續性的防火牆和 VNet 插入快取。 
>
>

## <a name="create-a-private-endpoint-with-a-new-azure-cache-for-redis-instance"></a>使用新的 Azure Cache for Redis 實例來建立私人端點 

在本節中，您會使用私人端點建立新的 Azure Cache for Redis 實例。

### <a name="create-a-virtual-network"></a>建立虛擬網路 

1. 登入  。

    :::image type="content" source="media/cache-private-link/1-create-resource.png" alt-text="選取 [建立資源]。":::

2. 在 [ **新增** ] 頁面上，選取 [ **網路** ]，然後選取 [ **虛擬網路** ]。

3. 選取 [ **新增** ] 以建立虛擬網路。

4. 在 [建立虛擬網路] 中，在 [基本] 索引標籤中輸入或選取這項資訊：

   | 設定      | 建議的值  | 描述 |
   | ------------ |  ------- | -------------------------------------------------- |
   | **訂用帳戶** | 下拉並選取您的訂用帳戶。 | 用來建立此虛擬網路的訂用帳戶。 | 
   | **資源群組** | 下拉並選取資源群組，或選取 [新建]  並輸入新的資源群組名稱。 | 要在其中建立虛擬網路和其他資源的資源組名。 將所有的應用程式資源放在一個資源群組中，您將可輕鬆地一併管理或刪除這些資源。 | 
   | **名稱** | 輸入虛擬網路名稱。 | 名稱必須以字母或數位開頭，以字母、數位或底線結尾，且只能包含字母、數位、底線、句點或連字號。 | 
   | **區域** | 下拉並選取區域。 | 選取將使用虛擬網路的其他服務附近的 [區域](https://azure.microsoft.com/regions/) 。 |

5. 選取 [ **IP 位址** ] 索引標籤，或按一下頁面底部的 [ **下一步： IP 位址** ] 按鈕。

6. 在 [ **IP 位址** ] 索引標籤中，將 **IPv4 位址空間** 指定為 CIDR 標記法中的一或多個位址首碼 (例如 192.168.1.0/24) 。

7. 在 [ **子網名稱** ] 底下，按一下 [ **預設** ] 以編輯子網的屬性。

8. 在 [ **編輯子網** ] 窗格中，指定 **子網名稱** 和 **子網位址範圍** 。 子網的位址範圍應為 CIDR 標記法 (例如 192.168.1.0/24) 。 其必須包含在虛擬網路的位址空間中。

9. 選取 [儲存]。

10. 選取 [ **審核 + 建立** ] 索引標籤，或按一下 [ **審核 + 建立** ] 按鈕。

11. 確認所有資訊都正確無誤，然後按一下 [建立] 以布 **建** 虛擬網路。

### <a name="create-an-azure-cache-for-redis-instance-with-a-private-endpoint"></a>使用私人端點建立 Azure Cache for Redis 實例
若要建立快取實例，請遵循下列步驟。

1. 返回至 Azure 入口網站首頁，或開啟側邊欄功能表，然後選取 [ **建立資源** ]。 
   
1. 在 [新增]  頁面上選取 [資料庫]  ，然後選取 [Azure Cache for Redis]  。

    :::image type="content" source="media/cache-private-link/2-select-cache.png" alt-text="選取 [建立資源]。":::
   
1. 在 [新的 Redis 快取]  頁面上，設定新快取的設定。
   
   | 設定      | 建議的值  | 描述 |
   | ------------ |  ------- | -------------------------------------------------- |
   | **DNS 名稱** | 輸入全域唯一名稱。 | 快取名稱必須是 1 到 63 個字元的字串，且只能包含數字、字母或連字號。 名稱的開頭和結尾必須是數字或字母，且不可包含連續的連字號。 您的快取執行個體 *主機名稱* 將是 *\<DNS name>.redis.cache.windows.net* 。 | 
   | **訂用帳戶** | 下拉並選取您的訂用帳戶。 | 這個新的 Azure Cache for Redis 執行個體建立所在的訂用帳戶。 | 
   | **資源群組** | 下拉並選取資源群組，或選取 [新建]  並輸入新的資源群組名稱。 | 用來建立快取和其他資源的資源群組名稱。 將所有的應用程式資源放在一個資源群組中，您將可輕鬆地一併管理或刪除這些資源。 | 
   | **位置** | 下拉並選取位置。 | 選取其他將使用快取的服務附近的[區域](https://azure.microsoft.com/regions/)。 |
   | **定價層** | 下拉並選取 [定價層](https://azure.microsoft.com/pricing/details/cache/)。 |  快取的可用大小、效能和功能取決於定價層。 如需詳細資訊，請參閱 [Azure Cache for Redis 概觀](cache-overview.md)。 |

1. 選取 [網路] 索引標籤，或按一下頁面底部的 [網路] 按鈕。

1. 在 [ **網路** 功能] 索引標籤中，選取連線方法的 **私人端點** 。

1. 按一下 [ **新增** ] 按鈕以建立私人端點。

    :::image type="content" source="media/cache-private-link/3-add-private-endpoint.png" alt-text="選取 [建立資源]。":::

1. 在 [ **建立私人端點** ] 頁面上，使用您在上一節中建立的虛擬網路和子網來設定私人端點的設定，然後選取 **[確定]** 。 

1. 選取頁面底部的 [下一步:進階] 索引標籤，或按一下頁面底部的 [下一步:進階] 按鈕。

1. 在基本或標準快取執行個體的 [進階] 索引標籤中，如果您想要啟用非 TLS 連接埠，請選取啟用切換。

1. 在高階快取執行個體的 [進階] 索引標籤中，設定非 TLS 連接埠、叢集和資料持續性的設定。


1. 選取頁面底部的 [下一步:標記] 索引標籤，或按一下頁面底部的 [下一步:標記] 按鈕。

1. 在 [標記] 索引標籤中，如果您想要分類資源，可以選擇性地輸入名稱和值。 

1. 選取 [檢閱 + 建立]。 您會移至 [檢閱 + 建立] 索引標籤，其中 Azure 會驗證您的組態。

1. 出現綠色的「通過驗證」訊息之後，請選取 [建立]。

建立快取需要一些時間。 您可以在 Azure Cache for Redis 的 [概觀] 頁面上監視進度。 當 [狀態] 顯示為 [執行中] 時，表示快取已可供使用。 
    
> [!IMPORTANT]
> 
> 預設會有 `publicNetworkAccess` 旗標 `Enabled` 。 
> 此旗標旨在讓您選擇性地允許公用和私人端點存取快取（若其設定為） `Enabled` 。 如果設定為 `Disabled` ，則只會允許私人端點存取。 您可以 `Disabled` 使用下列 PATCH 要求將值設定為。
> ```http
> PATCH  https://management.azure.com/subscriptions/{subscription}/resourceGroups/{resourcegroup}/providers/Microsoft.Cache/Redis/{cache}?api-version=2020-06-01
> {    "properties": {
>        "publicNetworkAccess":"Disabled"
>    }
> }
> ```
>

> [!IMPORTANT]
> 
> 若要連接到叢集快取，必須將 `publicNetworkAccess` 設定為， `Disabled` 而且只能有一個私人端點連接。 
>

## <a name="create-a-private-endpoint-with-an-existing-azure-cache-for-redis-instance"></a>使用現有的 Azure Cache for Redis 實例來建立私人端點 

在本節中，您會將私人端點新增至現有的 Azure Cache for Redis 實例。 

### <a name="create-a-virtual-network"></a>建立虛擬網路 
若要建立虛擬網路，請遵循下列步驟。

1. 登入  。

2. 在 [ **新增** ] 頁面上，選取 [ **網路** ]，然後選取 [ **虛擬網路** ]。

3. 選取 [ **新增** ] 以建立虛擬網路。

4. 在 [建立虛擬網路] 中，在 [基本] 索引標籤中輸入或選取這項資訊：

   | 設定      | 建議的值  | 描述 |
   | ------------ |  ------- | -------------------------------------------------- |
   | **訂用帳戶** | 下拉並選取您的訂用帳戶。 | 用來建立此虛擬網路的訂用帳戶。 | 
   | **資源群組** | 下拉並選取資源群組，或選取 [新建]  並輸入新的資源群組名稱。 | 要在其中建立虛擬網路和其他資源的資源組名。 將所有的應用程式資源放在一個資源群組中，您將可輕鬆地一併管理或刪除這些資源。 | 
   | **名稱** | 輸入虛擬網路名稱。 | 名稱必須以字母或數位開頭，以字母、數位或底線結尾，且只能包含字母、數位、底線、句點或連字號。 | 
   | **區域** | 下拉並選取區域。 | 選取將使用虛擬網路的其他服務附近的 [區域](https://azure.microsoft.com/regions/) 。 |

5. 選取 [ **IP 位址** ] 索引標籤，或按一下頁面底部的 [ **下一步： IP 位址** ] 按鈕。

6. 在 [ **IP 位址** ] 索引標籤中，將 **IPv4 位址空間** 指定為 CIDR 標記法中的一或多個位址首碼 (例如 192.168.1.0/24) 。

7. 在 [ **子網名稱** ] 底下，按一下 [ **預設** ] 以編輯子網的屬性。

8. 在 [ **編輯子網** ] 窗格中，指定 **子網名稱** 和 **子網位址範圍** 。 子網的位址範圍應為 CIDR 標記法 (例如 192.168.1.0/24) 。 其必須包含在虛擬網路的位址空間中。

9. 選取 [儲存]。

10. 選取 [ **審核 + 建立** ] 索引標籤，或按一下 [ **審核 + 建立** ] 按鈕。

11. 確認所有資訊都正確無誤，然後按一下 [建立] 以布 **建** 虛擬網路。

### <a name="create-a-private-endpoint"></a>建立私人端點 

若要建立私人端點，請遵循下列步驟。

1. 在 Azure 入口網站中，搜尋 **Azure Cache for Redis** ，然後按 enter 鍵或從搜尋建議中選取。

    :::image type="content" source="media/cache-private-link/4-search-for-cache.png" alt-text="選取 [建立資源]。":::

2. 選取您要新增私人端點的快取實例。

3. 在畫面的左側，選取 [ **(預覽]) 私人端點** 。

4. 按一下 [ **私人端點** ] 按鈕以建立私人端點。

    :::image type="content" source="media/cache-private-link/5-add-private-endpoint.png" alt-text="選取 [建立資源]。":::

5. 在 [ **建立私人端點] 頁面** 上，設定私人端點的設定。

   | 設定      | 建議的值  | 描述 |
   | ------------ |  ------- | -------------------------------------------------- |
   | **訂用帳戶** | 下拉並選取您的訂用帳戶。 | 用來建立此私人端點的訂用帳戶。 | 
   | **資源群組** | 下拉並選取資源群組，或選取 [新建]  並輸入新的資源群組名稱。 | 要在其中建立私人端點和其他資源的資源組名。 將所有的應用程式資源放在一個資源群組中，您將可輕鬆地一併管理或刪除這些資源。 | 
   | **名稱** | 輸入私人端點名稱。 | 名稱必須以字母或數位開頭，以字母、數位或底線結尾，且只能包含字母、數位、底線、句點或連字號。 | 
   | **區域** | 下拉並選取區域。 | 選取將使用您私人端點的其他服務附近的 [區域](https://azure.microsoft.com/regions/) 。 |

6. 按一下頁面底部的 [ **下一步：資源]** 按鈕。

7. 在 [ **資源** ] 索引標籤中，選取您的訂用帳戶，選擇 [資源類型 `Microsoft.Cache/Redis` ]，然後選取您想要用來連接私人端點的快取。

8. 按一下頁面底部的 [ **下一步：設定]** 按鈕。

9. **在 [設定] 索引** 標籤中，選取您在上一節中建立的虛擬網路和子網。

10. 按一下頁面底部的 [ **下一步：標記]** 按鈕。

11. 在 [標記] 索引標籤中，如果您想要分類資源，可以選擇性地輸入名稱和值。

12. 選取 [檢閱 + 建立]。 您會進入 [ **審核] + [建立** ] 索引標籤，其中 Azure 會驗證您的設定。

13. 出現綠色 **驗證通過** 訊息之後，請選取 [ **建立** ]。


## <a name="next-steps"></a>下一步

若要深入瞭解 Azure Private Link，請參閱 [Azure Private Link 檔](../private-link/private-link-overview.md)。