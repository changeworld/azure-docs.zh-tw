---
title: 用於搜尋索引的 Azure SQL 受控執行個體連接
titleSuffix: Azure Cognitive Search
description: 啟用公用端點，以允許從 Azure 認知搜尋上的索引子連接到 SQL 受控實例。
manager: nitinme
author: vl8163264128
ms.author: victliu
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: a1304ad05e05285aa75167ec21519ec90d157662
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/26/2020
ms.locfileid: "88922883"
---
# <a name="configure-a-connection-from-an-azure-cognitive-search-indexer-to-sql-managed-instance"></a>設定從 Azure 認知搜尋索引子到 SQL 受控執行個體的連接

如同 [使用索引子將 Azure SQL Database 連接到 Azure 認知搜尋](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md#faq)所述，Azure 認知搜尋透過公用端點來為 **SQL 受控實例** 建立索引子。

## <a name="create-azure-sql-managed-instance-with-public-endpoint"></a>使用公用端點建立 Azure SQL 受控執行個體
建立 SQL 受控執行個體，並選取 [ **啟用公用端點** ] 選項。

   ![啟用公用端點](media/search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers/enable-public-endpoint.png "啟用公用端點")

## <a name="enable-azure-sql-managed-instance-public-endpoint"></a>啟用 Azure SQL 受控執行個體公用端點
您也可以在**安全性**  >  **虛擬網路**  >  **公用端點**  >  **啟用**的現有 SQL 受控執行個體上啟用公用端點。

   ![啟用公用端點](media/search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers/mi-vnet.png "啟用公用端點")

## <a name="verify-nsg-rules"></a>確認 NSG 規則
檢查網路安全性群組的 **輸入安全性規則** 是否正確，可允許來自 Azure 服務的連線。

   ![NSG 輸入安全性規則](media/search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers/nsg-rule.png "NSG 輸入安全性規則")

> [!NOTE]
> 索引子仍需要以公用端點設定 SQL 受控執行個體，才能讀取資料。
> 不過，您可以選擇 `public_endpoint_inbound` 使用下列2個規則來取代目前的規則 () ，以限制對該公用端點的輸入存取：
>
> * 允許來自 `AzureCognitiveSearch` [服務標記](../virtual-network/service-tags-overview.md#available-service-tags) 的輸入存取 ( "SOURCE" = `AzureCognitiveSearch` ，"NAME" = `cognitive_search_inbound`) 
>
> * 允許來自搜尋服務 IP 位址的輸入存取權，您可以藉由 ping 其完整功能變數名稱來取得該位址 (例如 `<your-search-service-name>.search.windows.net`) 。  ( "SOURCE" = `IP address` ，"NAME" = `search_service_inbound`) 
>
> 針對這兩個規則，設定 "PORT" = `3342` ，"PROTOCOL" = `TCP` ，"DESTINATION" = `Any` ，"ACTION" = `Allow`

## <a name="get-public-endpoint-connection-string"></a>取得公用端點連接字串
請確定您使用 **公用端點** 的連接字串 (埠3342，而不是埠 1433) 。

   ![公用端點連接字串](media/search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers/mi-connection-string.png "公用端點連接字串")

## <a name="next-steps"></a>後續步驟
在設定的過程中，您現在可以使用入口網站或 REST API，將 SQL 受控執行個體指定為 Azure 認知搜尋索引子的資料來源。 如需詳細資訊，請參閱 [使用索引子將 Azure SQL Database 連接到 Azure 認知搜尋](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md) 。