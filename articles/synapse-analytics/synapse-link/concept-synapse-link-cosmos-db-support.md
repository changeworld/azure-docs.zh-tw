---
title: 適用於 Azure Cosmos DB 的 Azure Synapse Link (預覽) 支援的功能
description: 了解適用於 Azure Cosmos DB 的 Azure Synapse Link 目前支援的動作清單
services: synapse-analytics
author: ArnoMicrosoft
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: synapse-link
ms.date: 09/15/2020
ms.author: acomet
ms.reviewer: jrasnick
ms.openlocfilehash: fa05f8a8b05a0acddae906a93c90c42424466969
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/04/2020
ms.locfileid: "93322591"
---
# <a name="azure-synapse-link-preview-for-azure-cosmos-db-supported-features"></a>適用於 Azure Cosmos DB 的 Azure Synapse Link (預覽) 支援的功能

本文說明適用於 Azure Cosmos DB 的 Azure Synapse Link 中目前支援的功能。

## <a name="azure-synapse-support"></a>Azure Synapse 支援

Azure Cosmos DB 中有兩種類型的容器：
* HTAP 容器 - 已啟用 Synapse Link 的容器。 此容器同時具有交易存放區和分析存放區。 
* OLTP 容器-未啟用 Synaspe 連結的容器。 此容器只有交易式存放區，沒有分析存放區。

> [!IMPORTANT]
> 在未啟用受控虛擬網路的 Synapse 工作區中，目前支援 Azure Cosmos DB 的 Azure Synapse 連結。 

您可以連接到 Azure Cosmos DB 容器，而不需要啟用 Synapse 連結。 在此案例中，您只能讀取/寫入交易式存放區。 接下來是 Azure Cosmos DB Synapse 連結內目前支援的功能清單。 

| 類別              | 描述 |[Spark](https://docs.microsoft.com/azure/synapse-analytics/sql/on-demand-workspace-overview) | [SQL 無伺服器](https://docs.microsoft.com/azure/synapse-analytics/sql/on-demand-workspace-overview) |
| -------------------- | ----------------------------------------------------------- |----------------------------------------------------------- | ----------------------------------------------------------- |
| **執行階段支援** |支援存取 Azure Cosmos DB 的 Azure Synapse 執行時間| ✓ | ✓ |
| **Azure Cosmos DB API 支援** | 支援的 Azure Cosmos DB API 種類 | SQL/MongoDB | SQL/MongoDB |
| **Object**  |可建立的物件 (如資料表)，直接指向 Azure Cosmos DB 容器| 資料框架、View、Table | 檢視 |
| **讀取**    | 可讀取的 Azure Cosmos DB 容器類型 | OLTP/HTAP | HTAP  |
| **寫入**   | Azure Synapse 執行時間可以用來將資料寫入 Azure Cosmos DB 容器 | 是 | 否 |

* 如果您將資料從 Spark 寫入 Azure Cosmos DB 容器中，此程式會透過 Azure Cosmos DB 的交易式存放區進行。 它會藉由取用要求單位來影響 Azure Cosmos DB 的交易效能。
* 目前不支援透過外部資料表進行專用的 SQL 集區整合。
 
## <a name="supported-code-generated-actions-for-spark"></a>Spark 支援的程式碼產生動作

| 手勢              | 描述 |OLTP |HTAP  |
| -------------------- | ----------------------------------------------------------- |----------------------------------------------------------- |----------------------------------------------------------- |
| **載入至 DataFrame** |將資料載入和讀取至 Spark DataFrame |✓| ✓ |
| **建立 Spark 資料表** |建立指向 Azure Cosmos DB 容器的資料表|✓| ✓ |
| **將 DataFrame 寫入容器** |將資料寫入容器中|✓| ✓ |
| **從容器載入串流 DataFrame** |使用 Azure Cosmos DB 變更摘要進行資料串流|✓| ✓ |
| **將串流 DataFrame 寫入容器** |使用 Azure Cosmos DB 變更摘要進行資料串流|✓| ✓ |


## <a name="supported-code-generated-actions-for-sql-serverless"></a>SQL 無伺服器支援的程式碼產生動作

| 手勢              | 描述 |OLTP |HTAP |
| -------------------- | ----------------------------------------------------------- |----------------------------------------------------------- |----------------------------------------------------------- |
| **探索資料** |使用熟悉的 T-sql 語法和自動架構推斷，探索容器中的資料|X| ✓ |
| **建立 views 和組建 BI 報表** |建立 SQL view，以透過 Synapse SQL 無伺服器直接存取 BI 的容器 |X| ✓ |
| **聯結不同的資料來源以及 Cosmos DB 資料** | 使用 CETAS 將查詢的結果儲存在 Cosmos DB 容器中，以及使用 Azure Blob 儲存體或 Azure Data Lake Storage 中的資料來讀取資料 |X| ✓ |

## <a name="next-steps"></a>後續步驟

* 了解如何[連線至適用於 Azure Cosmos DB 的 Synapse Link](../quickstart-connect-synapse-link-cosmos-db.md)
* [瞭解如何使用 Spark 查詢 Cosmos DB 分析存放區](how-to-query-analytical-store-spark.md)
