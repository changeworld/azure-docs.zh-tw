---
title: 適用于搜尋索引的 Azure SQL 受控執行個體連線
titleSuffix: Azure Cognitive Search
description: 啟用公用端點，允許從 Azure 認知搜尋上的索引子連接到 SQL 受控實例。
manager: nitinme
author: vl8163264128
ms.author: victliu
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 65e483fd772e20daa73b465ea17dfa6ecde42233
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "76964884"
---
# <a name="configure-a-connection-from-an-azure-cognitive-search-indexer-to-sql-managed-instance"></a>設定從 Azure 認知搜尋索引子到 SQL 受控執行個體的連線

如[使用索引子將 Azure SQL Database 連線至 Azure 認知搜尋](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md#faq)中所述，Azure 認知搜尋會透過公用端點來支援針對**SQL 受控實例**建立索引子。

## <a name="create-azure-sql-managed-instance-with-public-endpoint"></a>建立具有公用端點的 Azure SQL 受控執行個體
建立 SQL 受控執行個體並選取 [**啟用公用端點**] 選項。

   ![啟用公用端點](media/search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers/enable-public-endpoint.png "啟用公用端點")

## <a name="enable-azure-sql-managed-instance-public-endpoint"></a>啟用 Azure SQL 受控執行個體公用端點
您也可以在 [**安全性**] [  >  **虛擬網路**] [  >  **公用端點**  >  **啟用**] 底下的現有 SQL 受控執行個體上啟用公用端點。

   ![啟用公用端點](media/search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers/mi-vnet.png "啟用公用端點")

## <a name="verify-nsg-rules"></a>驗證 NSG 規則
檢查網路安全性群組是否具有正確的**輸入安全性規則**，以允許來自 Azure 服務的連線。

   ![NSG 輸入安全性規則](media/search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers/nsg-rule.png "NSG 輸入安全性規則")

> [!NOTE]
> 索引子仍需要使用公用端點來設定 SQL 受控執行個體，才能讀取資料。
> 不過，您可以選擇 `public_endpoint_inbound` 使用下列2個規則來取代目前的規則（），以限制該公用端點的輸入存取：
>
> * 允許來自 `AzureCognitiveSearch` [服務標記](https://docs.microsoft.com/azure/virtual-network/service-tags-overview#available-service-tags)的輸入存取（"SOURCE" = `AzureCognitiveSearch` ，"NAME" = `cognitive_search_inbound` ）
>
> * 允許從搜尋服務的 IP 位址進行輸入存取，其可透過 ping 其完整功能變數名稱（例如）來取得 `<your-search-service-name>.search.windows.net` 。 （"SOURCE" = `IP address` ，"NAME" = `search_service_inbound` ）
>
> 針對這兩個規則的每個，設定 "PORT" = `3342` 、"PROTOCOL" = `TCP` 、"DESTINATION" = `Any` 、"ACTION" =`Allow`

## <a name="get-public-endpoint-connection-string"></a>取得公用端點連接字串
請確定您使用的是**公用端點**的連接字串（埠3342，而不是埠1433）。

   ![公用端點連接字串](media/search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers/mi-connection-string.png "公用端點連接字串")

## <a name="next-steps"></a>後續步驟
使用設定時，您現在可以使用入口網站或 REST API，將 SQL 受控執行個體指定為 Azure 認知搜尋索引子的資料來源。 如需詳細資訊，請參閱[使用索引子將 Azure SQL Database 連接到 Azure 認知搜尋](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)。
