---
title: 連線至適用於 Azure Cosmos DB 的 Synapse Link
description: 如何使用 Synapse Link 將 Azure Cosmos DB 連線至 Synapse 工作區
services: synapse-analytics
author: ArnoMicrosoft
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: ''
ms.date: 04/21/2020
ms.author: acomet
ms.reviewer: jrasnick
ms.openlocfilehash: dbacb70cd2166b601a47200b81f31a8eb7bb79ec
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/19/2020
ms.locfileid: "83599095"
---
# <a name="connect-to-synapse-link-for-azure-cosmos-db"></a>連線至適用於 Azure Cosmos DB 的 Synapse Link

本文說明如何使用 Synapse Link 從 Azure Synapse Analytics Studio 存取 Azure Cosmos DB 資料庫。 

## <a name="prerequisites"></a>Prerequisites

要將 Azure Cosmos DB 帳戶連線至您的工作區，必須符合幾項先決條件。

* 現有的 Azure Cosmos DB 帳戶，或依照此[快速入門](https://docs.microsoft.com/azure/cosmos-db/how-to-manage-database-account)的指示建立新帳戶
* 現有的 Synapse 工作區，或依照此[快速入門](https://docs.microsoft.com/azure/synapse-analytics/quickstart-create-workspace)的指示建立新的工作區 

## <a name="enable-azure-cosmos-db-analytical-store"></a>啟用 Azure Cosmos DB 分析存放區

若要對 Azure Cosmos DB 執行大規模的分析，而不影響您的作業效能，建議您啟用適用於 Azure Cosmos DB 的 Synapse Link。 此函式可將 HTAP 功能導入 Azure Synapse 的容器和內建支援中。 依照本快速入門的指示，啟用「適用於 Cosmos DB 的 Synapse Link」容器。

## <a name="navigate-to-synapse-studio"></a>瀏覽至 Synapse Studio

從您的 Synapse 工作區中，選取 [啟動 Synapse Studio]。 在 Synapse Studio 首頁上選取 [**資料]，這會使您進入**資料物件總管**。

## <a name="connect-an-azure-cosmos-db-database-to-a-synapse-workspace"></a>將 Azure Cosmos DB 資料庫連線至 Synapse 工作區

Azure Cosmos DB 資料庫連線會以連結服務的形式完成。 Cosmos DB 連結服務可讓使用者瀏覽及探索資料，以及從適用於 Azure Synapse Analytics 的 Apache Spark 或 SQL 讀取和或寫入至 Azure Cosmos DB。

在資料物件總管中，您可以執行下列步驟以直接連線至 Azure Cosmos DB 資料庫：

1. 選取 [資料] 附近的 ***+*** 圖示
2. 選取 [連線至外部資料]
3. 選取要連線到的 API：SQL 或 MongoDB
4. 選取 [繼續]
5. 為連結服務命名。 此名稱會顯示在物件總管中，且供 Synapse 執行階段用來連線至資料庫和容器。 建議使用易記名稱。
6. 選取 [Cosmos DB 帳戶名稱] 和 [資料庫名稱]
7. (選擇性) 若未指定區域，則 Synapse 執行階段作業將會路由至已啟用分析存放區的最近區域。 不過，您可以手動設定您想要讓使用者存取 Cosmos DB 分析存放區的區域。 選取 [其他連線屬性]，然後選取 [新增]。 在 [屬性名稱] 底下，寫入 ***PreferredRegions***，並將 [值] 設定為您要的區域 (例如：WestUS2，文字與數字之間沒有空格)
8. 選取 [建立] 

Azure Cosmos DB 資料庫會顯示在 [Azure Cosmos DB] 區段的 [已連結] 索引標籤底下。 您可以使用下列圖示，區分已啟用 HTAP 的 Azure Cosmos DB 容器與僅限 OLTP 的容器：

**Synapse 容器**：

![HTAP 容器](./media/quickstart-connect-synapse-link-cosmosdb/htap-container.png)

**僅限 OLTP 的容器**：

![OLTP 容器](./media/quickstart-connect-synapse-link-cosmosdb/oltp-container.png)

## <a name="quickly-interact-with-code-generated-actions"></a>與程式碼產生的動作快速互動

當您以滑鼠右鍵按一下容器時，您會看到將觸發 Spark 或 SQL 執行階段的手勢清單。 寫入容器的作業會透過 Azure Cosmos DB 的交易存放區執行，且將會使用要求單位。  

## <a name="next-steps"></a>後續步驟

* [了解 Synapse 與 Azure Cosmos DB 之間支援的項目](./synapse-link/concept-synapse-link-cosmos-db-support.md)
* [了解如何使用適用於 Azure Synapse Analytics 的 Apache Spark 查詢分析存放區](synapse-link/how-to-query-analytical-store-spark.md)