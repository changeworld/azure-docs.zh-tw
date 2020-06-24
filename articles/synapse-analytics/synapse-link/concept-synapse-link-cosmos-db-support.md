---
title: 適用於 Azure Cosmos DB 的 Azure Synapse Link (預覽) 支援的功能
description: 了解適用於 Azure Cosmos DB 的 Azure Synapse Link 目前支援的動作清單
services: synapse-analytics
author: ArnoMicrosoft
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: synapse-link
ms.date: 04/21/2020
ms.author: acomet
ms.reviewer: jrasnick
ms.openlocfilehash: c9f6a61dfa688980fa6400a2fa93ab8862798750
ms.sourcegitcommit: 3988965cc52a30fc5fed0794a89db15212ab23d7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/22/2020
ms.locfileid: "85194768"
---
# <a name="azure-synapse-link-preview-for-azure-cosmos-db-supported-features"></a>適用於 Azure Cosmos DB 的 Azure Synapse Link (預覽) 支援的功能

本文說明適用於 Azure Cosmos DB 的 Azure Synapse Link 中目前支援的功能。

## <a name="azure-synapse-support"></a>Azure Synapse 支援

Azure Cosmos DB 中有兩種類型的容器：
* HTAP 容器 - 已啟用 Synapse Link 的容器。 此容器同時具有交易存放區和分析存放區。 
* OLTP 容器 - 僅具有交易存放區的容器；未啟用 Synapse Link。 

> [!IMPORTANT]
> 適用於 Azure Cosmos DB 的 Azure Synapse Link 現在支援未啟用受控虛擬網路的工作區。 

您可以在未啟用 Synapse Link 的情況下連線至 Azure Cosmos DB 的容器，此時，您只能讀取/寫入交易存放區。 接下來是適用於 Azure Cosmos DB 的 Synapse Link 中目前支援的功能清單。 

| 類別              | 描述 |[Spark](https://docs.microsoft.com/azure/synapse-analytics/sql/on-demand-workspace-overview) | [SQL 無伺服器](https://docs.microsoft.com/azure/synapse-analytics/sql/on-demand-workspace-overview) |
| -------------------- | ----------------------------------------------------------- |----------------------------------------------------------- | ----------------------------------------------------------- | ----------------------------------------------------------- |
| **執行階段支援** |支援 Azure Synapse 執行階段的讀取或寫入| ✓ | [與我們連絡](mailto:AskSynapse@microsoft.com?subject=[Enable%20Preview%20Feature]%20SQL%20serverless%20for%20Cosmos%20DB)|
| **Azure Cosmos DB API 支援** |Synapse Link 形式的 API 支援| SQL/MongoDB | SQL/MongoDB |
| **Object**  |可建立的物件 (如資料表)，直接指向 Azure Cosmos DB 容器| 檢視、資料表 | 檢視 |
| **讀取**    |從 Azure Cosmos DB 容器讀取資料| OLTP/HTAP | HTAP  |
| **寫入**   |將資料從執行階段寫入 Azure Cosmos DB 容器中| OLTP | n/a |

* 如果您將資料從 Spark 寫入 Azure Cosmos DB 容器中，此程序將會透過 Azure Cosmos DB 的交易存放區來進行，且會因為使用要求單位而影響到 Azure Cosmos DB 的交易效能。
* 目前不支援透過外部資料表的 SQL 集區整合。

## <a name="supported-code-generated-actions-for-spark"></a>Spark 支援的程式碼產生動作

| 手勢              | 描述 |OLTP |HTAP  |
| -------------------- | ----------------------------------------------------------- |----------------------------------------------------------- |----------------------------------------------------------- |
| **載入至 DataFrame** |將資料載入和讀取至 Spark DataFrame |X| ✓ |
| **建立 Spark 資料表** |建立指向 Azure Cosmos DB 容器的資料表|X| ✓ |
| **將 DataFrame 寫入容器** |將資料寫入容器中|✓| ✓ |
| **從容器載入串流 DataFrame** |使用 Azure Cosmos DB 變更摘要進行資料串流|✓| ✓ |
| **將串流 DataFrame 寫入容器** |使用 Azure Cosmos DB 變更摘要進行資料串流|✓| ✓ |



## <a name="supported-code-generated-actions-for-sql-serverless"></a>SQL 無伺服器支援的程式碼產生動作

| 手勢              | 描述 |OLTP |HTAP |
| -------------------- | ----------------------------------------------------------- |----------------------------------------------------------- |----------------------------------------------------------- |
| **選取前 100 個** |預覽容器中的前 100 個項目|X| ✓ |
| **建立檢視** |建立可透過 Synapse SQL 在容器中直接擁有 BI 存取權的檢視|X| ✓ |

## <a name="next-steps"></a>後續步驟

* 了解如何[連線至適用於 Azure Cosmos DB 的 Synapse Link](../quickstart-connect-synapse-link-cosmos-db.md)
* [了解如何使用 Spark 查詢分析存放區](how-to-query-analytical-store-spark.md)