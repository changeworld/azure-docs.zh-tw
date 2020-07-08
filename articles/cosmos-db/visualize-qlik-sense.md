---
title: 將 Qlik Sense 連線到 Azure Cosmos DB 並將資料視覺化
description: 本文說明將 Azure Cosmos DB 連線到 Qlik Sense 並將資料視覺化所需的步驟。
ms.service: cosmos-db
author: SnehaGunda
ms.author: sngun
ms.topic: how-to
ms.date: 05/23/2019
ms.reviewer: sngun
ms.openlocfilehash: 986db85ddbf741dc459625dbd763b9cbbba1c429
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85263342"
---
# <a name="connect-qlik-sense-to-azure-cosmos-db-and-visualize-your-data"></a>將 Qlik Sense 連線到 Azure Cosmos DB 並將資料視覺化

Qlik Sense 是一種資料視覺效果工具，可將不同來源的資料組合為單一檢視。 Qlik Sense 會在您的資料中為每個可能的關聯性編製索引，如此一來，您就能立即深入解析該資料。 您可以使用 Qlik Sense 來將 Azure Cosmos DB 資料視覺化。 本文說明將 Azure Cosmos DB 連線到 Qlik Sense 並將資料視覺化所需的步驟。 

> [!NOTE]
> 目前只有 SQL API 和適用於 MongoDB 帳戶的 Azure Cosmos DB API 支援將 Qlik Sense 連線至 Azure Cosmos DB。

您可以使用下列項目來將 Qlik Sense 連線到 Azure Cosmos DB：

* 使用 ODBC 連接器的 Cosmos DB SQL API。

* 適用於 MongoDB 的 Azure Cosmos DB API，使用 Qlik Sense MongoDB 連接器 (目前處於預覽狀態)。

* 適用於 MongoDB 的 Azure Cosmos DB API 和 SQL API，使用 Qlik Sense 中的 REST API 連接器。

* 使用 Qlik Core 之 gRPC 連接器的 Cosmos DB Mongo DB API。
本文將說明使用 ODBC 連接器連線到 Cosmos DB SQL API 的詳細資料。

本文將說明使用 ODBC 連接器連線到 Cosmos DB SQL API 的詳細資料。

## <a name="prerequisites"></a>必要條件

依照本文的指示進行之前，請確定已備妥下列資源：

* 下載 [Qlik Sense Desktop](https://www.qlik.com/us/try-or-buy/download-qlik-sense) \(英文\)，或藉由[安裝 Qlik Sense Marketplace 項目](https://azuremarketplace.microsoft.com/marketplace/apps/qlik.qlik-sense) \(英文\)，在 Azure 中安裝 Qlik Sense。

* 下載[視訊遊戲資料](https://www.kaggle.com/gregorut/videogamesales) \(英文\)，此範例資料是 CSV 格式。 您會將此資料儲存於 Cosmos DB 帳戶，並在 Qlik Sense 中將其視覺化。

* 使用快速入門文章中[建立帳戶](create-sql-api-dotnet.md#create-account)一節所述的步驟，來建立 Azure Cosmos DB SQL API 帳戶。

* [建立資料庫和集合](create-sql-api-java.md#add-a-container)：您可以將集合輸送量值設定為 1000 RU/秒。 

* 將範例視訊遊戲銷售資料載入到您的 Cosmos DB 帳戶。 您可以使用 Azure Cosmos DB 資料移轉工具來匯入資料，您可以[循序](import-data.md#SQLSeqTarget)或[大量匯入](import-data.md#SQLBulkTarget)資料。 大約需要 3-5 分鐘的時間，才能將資料匯入到 Cosmos DB 帳戶。

* 藉由執行[使用 ODBC 驅動程式連線到 Cosmos DB](odbc-driver.md) 一文中的步驟，來下載、安裝及設定 ODBC 驅動程式。 視訊遊戲資料是一個簡單的資料集，而您不需要編輯結構描述，只需使用預設的集合對應結構描述。

## <a name="connect-qlik-sense-to-cosmos-db"></a>將 Qlik Sense 連線到 Cosmos DB

1. 開啟 Qlik Sense，然後選取 [建立新的應用程式]****。 提供應用程式的名稱，然後選取 [建立]****。

   :::image type="content" source="./media/visualize-qlik-sense/create-new-qlik-sense-app.png" alt-text="建立新的 Qlik Sense 應用程式":::

2. 成功建立新的應用程式之後，選取 [開啟應用程式]****，然後選擇 [從檔案和其他來源新增資料]****。 

3. 從資料來源中，選取 [ODBC]**** 以開啟新的連線設定視窗。 

4. 切換至 [使用者 DSN]****，然後選擇您稍早建立的 ODBC 連線。 提供連線的名稱，然後選取 [建立]****。 

   :::image type="content" source="./media/visualize-qlik-sense/create-new-connection.png" alt-text="建立新的連線":::

5. 當您建立連線之後，您可以選擇資料庫、視訊遊戲資料所在的集合，然後進行預覽。

   :::image type="content" source="./media/visualize-qlik-sense/choose-database-and-collection.png" alt-text="選擇資料庫與集合"::: 

6. 接下來，選取 [新增資料]****，將資料載入至 Qlik Sense。 當您將資料載入至 Qlik Sense 之後，您可以產生資料的深入解析並執行分析。 您可以使用深入解析或建置自己的應用程式，來探索視訊遊戲銷售。 下圖說明 

   :::image type="content" source="./media/visualize-qlik-sense/visualize-data.png" alt-text="顯現資料":::

### <a name="limitations-when-connecting-with-odbc"></a>使用 ODBC 連線時的限制 

Cosmos DB 是一個無結構描述的分散式資料庫，其具備根據開發人員需求建立模型的驅動程式。 ODBC 驅動程式需要含有結構描述的資料庫，可推斷資料行、其資料類型及其他屬性。 具備關聯式功能的一般 SQL 查詢或 DML 語法不適用 Cosmos DB SQL API，因為 SQL API 不是 ANSI SQL。 基於此因素，透過 ODBC 驅動程式發出的 SQL 陳述式會轉譯為 Cosmos DB 特有的 SQL 語法，此語法沒有適用於所有建構的對等項目。 若要避免這些轉譯問題，您必須在設定 ODBC 連線時套用結構描述。 [使用 ODBC 驅動程式連線](odbc-driver.md)一文可提供建議和方法以協助您設定結構描述。 請務必在 Cosmos DB 帳戶內，針對每個資料庫/集合建立此對應。

## <a name="next-steps"></a>後續步驟

如果您使用的是 Power BI 之類的不同視覺效果工具，您可以使用下列文件的指示來與它連線：

* [使用 Power BI 連接器將 Cosmos DB 資料視覺化](powerbi-visualize.md)
