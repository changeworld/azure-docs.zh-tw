---
title: 連線至適用於 Azure Cosmos DB 的 Azure Synapse Link (預覽)
description: 了解如何使用 Azure Synapse Link 將 Azure Cosmos DB 資料庫連線至 Azure Synapse 工作區。
services: synapse-analytics
author: ArnoMicrosoft
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: synapse-link
ms.date: 04/21/2020
ms.author: acomet
ms.reviewer: jrasnick
ms.openlocfilehash: 5316f74ee38f597592ae4582aef31837f0f05fda
ms.sourcegitcommit: aacbf77e4e40266e497b6073679642d97d110cda
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/12/2021
ms.locfileid: "98119827"
---
# <a name="connect-to-azure-synapse-link-for-azure-cosmos-db"></a>連線至適用於 Azure Cosmos DB 的 Azure Synapse Link

本文說明如何使用 Azure Synapse Link 從 Azure Synapse Analytics Studio 存取 Azure Cosmos DB 資料庫。

## <a name="prerequisites"></a>必要條件

要將 Azure Cosmos DB 資料庫連線至您的工作區，您將需要：

* 現有的 Azure Cosmos DB 資料庫，或依照下列指示建立新帳戶：[快速入門：管理 Azure Cosmos DB 帳戶](../../cosmos-db/how-to-manage-database-account.md)。
* 現有的 Azure Synapse Analytics 工作區，或遵循下列步驟建立新的工作區：[快速入門：建立 Synapse 工作區](../quickstart-create-workspace.md)。

> [!IMPORTANT]
> 適用於 Azure Cosmos DB 的 Azure Synapse Link 現在支援未啟用受控虛擬網路的工作區。

## <a name="enable-synapse-link-on-an-azure-cosmos-db-database-account"></a>在 Azure Cosmos DB 資料庫帳戶上啟用 Synapse 連結

若要對 Azure Cosmos DB 執行大規模的分析，而不影響您的作業效能，建議您啟用適用於 Azure Cosmos DB 的 Synapse Link。 Synapse Link 可將 HTAP 功能導入 Azure Synapse 的容器和內建支援中。

## <a name="go-to-synapse-studio"></a>移至 Synapse Studio

從 Azure Synapse 工作區，選取 [啟動 Synapse Studio]。 在 Synapse Studio 首頁上選取 [資料]，這會使您進入資料物件總管。

## <a name="connect-an-azure-cosmos-db-database-to-an-azure-synapse-workspace"></a>將 Azure Cosmos DB 資料庫連線至 Azure Synapse 工作區

Azure Cosmos DB 資料庫連線會以連結服務的形式完成。 Azure Cosmos DB 連結服務可讓您瀏覽及探索資料，以及從適用於 Azure Synapse Analytics 的 Apache Spark 或 SQL 讀取和或寫入至 Azure Cosmos DB。

在資料物件總管中，您可以遵循這些步驟以直接連線至 Azure Cosmos DB 資料庫：

1. 選取 [資料] 附近的 [+] 圖示。
1. 選取 [連線至外部資料]。
1. 選取您想要連線的 API，例如 **SQL API** 或 **適用於 MongoDB 的 API**。
1. 選取 [繼續]。
1. 使用自訂名稱來命名連結服務。 此名稱會出現在「資料物件總管」中，可供 Azure Synapse 執行階段用來連線到資料庫和容器。
1. 選取 [Azure Cosmos DB 帳戶名稱] 和 [資料庫名稱]。
1. (選擇性) 若未指定區域，則 Azure Synapse 執行階段作業將會路由至已啟用分析存放區的最近區域。 您也可以手動設定要讓使用者存取 Azure Cosmos DB 分析存放區的區域。 選取 [其他連線屬性]，然後選取 [新增]。 在 **屬性名稱** 下，輸入 **PreferredRegions**。 **值** 設定為您想要的區域，例如 **WestUS2**。 (字母和數字之間沒有空格。)
1. 選取 [建立]。

Azure Cosmos DB 資料庫會顯示在 **Azure Cosmos DB** 區段下的 **已連結** 索引標籤中。 透過 Azure Cosmos DB，您可以使用下列圖示來區分已啟用 HTAP 的容器與僅限 OLTP 的容器：

**僅限 OLTP 的容器**：

![顯示 OLTP 容器圖示的視覺效果。](../media/quickstart-connect-synapse-link-cosmosdb/oltp-container.png)

**已啟用 HTAP 的容器**：

![顯示 HTAP 容器圖示的視覺效果。](../media/quickstart-connect-synapse-link-cosmosdb/htap-container.png)

## <a name="quickly-interact-with-code-generated-actions"></a>與程式碼產生的動作快速互動

以滑鼠右鍵按一下容器時，您會看到將觸發 Spark 或 SQL 執行階段的手勢清單。 寫入容器的作業會透過 Azure Cosmos DB 的交易存放區執行，且將會使用要求單位。  

## <a name="next-steps"></a>後續步驟

* [了解 Azure Synapse 與 Azure Cosmos DB 之間支援的項目](./concept-synapse-link-cosmos-db-support.md)
* [了解如何使用 Spark 查詢分析存放區](./how-to-query-analytical-store-spark.md)