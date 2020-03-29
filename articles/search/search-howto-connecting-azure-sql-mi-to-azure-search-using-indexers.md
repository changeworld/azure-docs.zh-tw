---
title: 用於搜索索引的 Azure SQL 託管實例連接
titleSuffix: Azure Cognitive Search
description: 啟用公共終結點以允許從 Azure 認知搜索上的索引子連接到 SQL 託管實例。
manager: nitinme
author: vl8163264128
ms.author: victliu
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 65e483fd772e20daa73b465ea17dfa6ecde42233
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76964884"
---
# <a name="configure-a-connection-from-an-azure-cognitive-search-indexer-to-sql-managed-instance"></a>配置從 Azure 認知搜索索引子到 SQL 託管實例的連接

如[使用索引子將 Azure SQL 資料庫連接到 Azure 認知搜索](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md#faq)中所述，Azure 認知搜索通過公共終結點支援針對**SQL 託管實例**創建索引子。

## <a name="create-azure-sql-managed-instance-with-public-endpoint"></a>使用公共終結點創建 Azure SQL 託管實例
使用選中的 **"啟用公共終結點**"選項創建 SQL 託管實例。

   ![啟用公用端點](media/search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers/enable-public-endpoint.png "啟用公用端點")

## <a name="enable-azure-sql-managed-instance-public-endpoint"></a>啟用 Azure SQL 託管實例公共終結點
您還可以在**安全** > **虛擬網路** > **公共終結點** > 下在現有 SQL 託管實例上**啟用**公共終結點。

   ![啟用公用端點](media/search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers/mi-vnet.png "啟用公用端點")

## <a name="verify-nsg-rules"></a>驗證 NSG 規則
檢查網路安全性群組具有允許從 Azure 服務連接的正確**入站安全規則**。

   ![NSG 入站安全規則](media/search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers/nsg-rule.png "NSG 入站安全規則")

> [!NOTE]
> 索引子仍要求使用公共終結點配置 SQL 託管實例才能讀取資料。
> 但是，您可以選擇通過用以下 2 個規則替換當前規則 （`public_endpoint_inbound`） 來限制對該公共終結點的入站訪問：
>
> * 允許從`AzureCognitiveSearch`[服務標記](https://docs.microsoft.com/azure/virtual-network/service-tags-overview#available-service-tags)（"SOURCE" ="NAME" `AzureCognitiveSearch`= `cognitive_search_inbound`） 進行入站訪問
>
> * 允許從搜索服務的 IP 位址進行入站訪問，可以通過 ping 其完全限定的功能變數名稱（例如 ， `<your-search-service-name>.search.windows.net`） 獲得訪問。 （"源" `IP address`= "名稱" `search_service_inbound`= ）
>
> 對於這兩個規則中的每一個，設置"PORT" = `3342`"PROTOCOL" = `TCP` `Any`"目標" = "操作" |`Allow`

## <a name="get-public-endpoint-connection-string"></a>獲取公共終結點連接字串
請確保將連接字串用於**公共終結點**（埠 3342，而不是埠 1433）。

   ![公共終結點連接字串](media/search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers/mi-connection-string.png "公共終結點連接字串")

## <a name="next-steps"></a>後續步驟
通過配置，現在可以使用門戶或 REST API 指定 SQL 託管實例作為 Azure 認知搜索索引子的資料來源。 有關詳細資訊，請參閱[使用索引子將 Azure SQL 資料庫連接到 Azure 認知搜索](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)。
