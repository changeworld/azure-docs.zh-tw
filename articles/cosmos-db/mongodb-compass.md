---
title: 使用 Compass 連線至 Azure Cosmos DB
description: 了解如何使用 MongoDB Compass 來儲存和管理 Azure Cosmos DB 中的資料。
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: how-to
ms.date: 06/05/2020
author: LuisBosquez
ms.author: lbosq
ms.openlocfilehash: 2f461ebbd84a66470eba012660db3aefebc8c5e7
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85260775"
---
# <a name="use-mongodb-compass-to-connect-to-azure-cosmos-dbs-api-for-mongodb"></a>使用 MongoDB Compass 連線至 Azure Cosmos DB 的 MongoDB 版 API

本教學課程示範在 Cosmos DB 中儲存和 (或) 管理資料時如何使用 [MongoDB Compass](https://www.mongodb.com/products/compass)。 我們使用 Azure Cosmos DB 的 MongoDB 版 API 進行此逐步解說。 簡單介紹一下，Compass 是 MongoDB 的 GUI。 它通常用來將資料視覺化、執行特定查詢，以及管理您的資料。

Cosmos DB 是 Microsoft 的全域分散式多模型資料庫服務。 您可以快速建立及查詢文件、索引鍵/值及圖形資料庫，所有這些都受惠於位於 Cosmos DB 核心的全域散發和水平調整功能。

## <a name="pre-requisites"></a>必要條件

若要使用 MongoDB 羅盤連接到您的 Cosmos DB 帳戶，您必須：

* 下載並安裝 [Compass](https://www.mongodb.com/download-center/compass?jmp=hero)
* 取得您的 Cosmos DB [連接字串](connect-mongodb-account.md)資訊

> [!NOTE]
> 目前，最新版的 MongoDB 羅盤支援 Azure Cosmos DB 的 MongoDB 伺服器3.2 版的 API。 3.6 伺服器版本尚不支援 MongoDB 羅盤。 

## <a name="connect-to-cosmos-dbs-api-for-mongodb"></a>連線至 Cosmos DB 的 MongoDB 版 API

若要將 Cosmos DB 帳戶連線至 Compass，您可以依照下列步驟操作：

1. 使用[這裡](connect-mongodb-account.md)的指示，擷取由 Azure Cosmos DB API MongoDB 為您的 Cosmos 帳戶設定的連線資訊。

    :::image type="content" source="./media/mongodb-compass/mongodb-compass-connection.png" alt-text="[連接字串] 刀鋒視窗的螢幕擷取畫面":::

2. 在 Cosmos DB 中的 [主要/次要連接字串]**** 旁，按一下顯示為 [複製到剪貼簿]**** 的按鈕。 按一下此按鈕，會將您的整個連接字串複製到剪貼簿。

    :::image type="content" source="./media/mongodb-compass/mongodb-connection-copy.png" alt-text="[複製到剪貼簿] 按鈕的螢幕擷取畫面":::

3. 在您的桌面/電腦上開啟 Compass，並按一下 [連線]****，然後按一下 [連線至...]****。

4. Compass 會自動偵測剪貼簿中的連接字串，並出現提示詢問您是否要使用該連接字串來連線。 按一下 [是]****，如下列螢幕擷取畫面所示。

    :::image type="content" source="./media/mongodb-compass/mongodb-compass-detect.png" alt-text="Compass 連線提示的螢幕擷取畫面":::

5. 在上述步驟中按一下 [是]**** 後，將會自動填入連接字串中的詳細資料。 請移除在 [複本集名稱]**** 欄位中自動填入的值，以確定該欄位保留為空白。

    :::image type="content" source="./media/mongodb-compass/mongodb-compass-replica.png" alt-text="Compass 連線提示的螢幕擷取畫面":::

6. 按一下頁面底部的 [連線]****。 您的 Cosmos DB 帳戶和資料庫現在應該會顯示在 MongoDB Compass 內。

## <a name="next-steps"></a>後續步驟

- 了解如何[使用 Studio 3T](mongodb-mongochef.md) 搭配 Azure Cosmos DB 適用於 MongoDB 的 API。
- 使用 Azure Cosmos DB 適用於 MongoDB 的 API 瀏覽 Cosmos DB [範例](mongodb-samples.md)。
