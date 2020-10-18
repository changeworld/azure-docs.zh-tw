---
title: 使用 Studio 3T 連接到 Azure Cosmos DB 適用于 MongoDB 的 API
description: 瞭解如何使用 Studio 3T 連接到 Azure Cosmos DB 的 MongoDB API。
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: how-to
ms.date: 03/20/2020
author: timsander1
ms.author: tisande
ms.custom: seodec18
ms.openlocfilehash: 6c9ea401a4a62f617b5acf6f96c60532d929ff1c
ms.sourcegitcommit: 419c8c8061c0ff6dc12c66ad6eda1b266d2f40bd
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/18/2020
ms.locfileid: "92167962"
---
# <a name="connect-to-an-azure-cosmos-account-using-studio-3t"></a>使用 Studio 3T 連接到 Azure Cosmos 帳戶

若要使用 Studio 3T 連接到 Azure Cosmos DB 的 MongoDB API，您必須：

* 下載並安裝 [Studio 3T](https://studio3t.com/)。
* 擁有您 Azure Cosmos 帳戶的 [連接字串](connect-mongodb-account.md) 資訊。

## <a name="create-the-connection-in-studio-3t"></a>在 Studio 3T 中建立連線

若要將您的 Azure Cosmos 帳戶新增至 Studio 3T 連線管理員，請使用下列步驟：

1. 使用將 [mongodb 應用程式連線至 Azure Cosmos DB](connect-mongodb-account.md) 文章中的指示，來取得 Azure Cosmos DB 的 mongodb API 帳戶的連線資訊。

    :::image type="content" source="./media/mongodb-mongochef/ConnectionStringBlade.png" alt-text="[連接字串] 頁面的螢幕擷取畫面":::

2. 按一下 [連接]**** 以開啟 [連接管理員]，然後按一下 [新增連線]****

    :::image type="content" source="./media/mongodb-mongochef/ConnectionManager.png" alt-text="[連接字串] 頁面的螢幕擷取畫面":::
3. 在 [ **新增** 連線] 視窗的 [ **伺服器** ] 索引標籤上，輸入 AZURE Cosmos 帳戶和埠的主機 (FQDN) 。

    :::image type="content" source="./media/mongodb-mongochef/ConnectionManagerServerTab.png" alt-text="[連接字串] 頁面的螢幕擷取畫面":::
4. 在 [新增連線]**** 視窗中，請在 [驗證]**** 索引標籤上選擇驗證模式 [基本 (MONGODB-CR 或 SCARM-SHA-1)]****，並輸入使用者名稱和密碼。  接受預設的驗證資料庫 (管理員)，或提供您自己的值。

    :::image type="content" source="./media/mongodb-mongochef/ConnectionManagerAuthenticationTab.png" alt-text="[連接字串] 頁面的螢幕擷取畫面":::
5. 在 [新增連接]**** 視窗的 [SSL]**** 索引標籤上，勾選 [使用 SSL 通訊協定連接]**** 核取方塊和 [接受自我簽署的 SSL 憑證]**** 選項按鈕。

    :::image type="content" source="./media/mongodb-mongochef/ConnectionManagerSSLTab.png" alt-text="[連接字串] 頁面的螢幕擷取畫面":::
6. 按一下 [測試連接]**** 按鈕以驗證連接資訊，按一下 [確定]**** 以返回 [新增連線] 視窗，然後按一下 [儲存]****。

    :::image type="content" source="./media/mongodb-mongochef/TestConnectionResults.png" alt-text="[連接字串] 頁面的螢幕擷取畫面":::

## <a name="use-studio-3t-to-create-a-database-collection-and-documents"></a>使用 Studio 3T 建立資料庫、集合和文件
若要使用 Studio 3T 建立資料庫、集合和文件，請執行下列步驟：

1. 在 [連接管理員]**** 中，反白顯示連接，然後按一下 [連接]****。

    :::image type="content" source="./media/mongodb-mongochef/ConnectToAccount.png" alt-text="[連接字串] 頁面的螢幕擷取畫面":::
2. 以滑鼠右鍵按一下主機，然後選擇 [新增資料庫]****。  提供資料庫名稱，然後按一下 [確定] ****。

    :::image type="content" source="./media/mongodb-mongochef/AddDatabase1.png" alt-text="[連接字串] 頁面的螢幕擷取畫面":::
3. 以滑鼠右鍵按一下資料庫，然後選擇 [新增集合]****。  提供集合名稱，然後按一下 [建立] ****。

    :::image type="content" source="./media/mongodb-mongochef/AddCollection.png" alt-text="[連接字串] 頁面的螢幕擷取畫面":::
4. 按一下 [集合]**** 功能表項目，然後按一下 [新增文件]****。

    :::image type="content" source="./media/mongodb-mongochef/AddDocument1.png" alt-text="[連接字串] 頁面的螢幕擷取畫面" },
        "isRegistered": false
    }
    ```

7. 執行範例查詢。 例如，搜尋姓氏 'Andersen' 的家族，並傳回父母和州欄位。

    :::image type="content" source="./media/mongodb-mongochef/QueryDocument1.png" alt-text="[連接字串] 頁面的螢幕擷取畫面":::

## <a name="next-steps"></a>後續步驟

- 了解如何[使用 Robo 3T](mongodb-robomongo.md) 搭配 Azure Cosmos DB 適用於 MongoDB 的 API。
- 使用 Azure Cosmos DB 適用於 MongoDB 的 API 瀏覽 Cosmos DB [範例](mongodb-samples.md)。
