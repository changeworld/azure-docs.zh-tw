---
title: 使用 Studio 3T 連接到 Azure Cosmos DB 的 MongoDB API
description: 瞭解如何使用 Studio 3T 連接到 Azure Cosmos DB 的 MongoDB API。
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: conceptual
ms.date: 03/20/2020
author: timsander1
ms.author: tisande
ms.custom: seodec18
ms.openlocfilehash: 84b703cceeb130b177b8ab32281ef616b1ec632b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80548829"
---
# <a name="connect-to-an-azure-cosmos-account-using-studio-3t"></a>使用 Studio 3T 連線到 Azure Cosmos 帳戶

若要使用 Studio 3T 連接到適用于 MongoDB 的 Azure Cosmos DB API，您必須：

* 下載並安裝[Studio 3T](https://studio3t.com/)。
* 請提供您的 Azure Cosmos 帳戶的[連接字串](connect-mongodb-account.md)資訊。

## <a name="create-the-connection-in-studio-3t"></a>在 Studio 3T 中建立連線

若要將您的 Azure Cosmos 帳戶新增至 Studio 3T 連線管理員，請使用下列步驟：

1. 使用將[MongoDB 應用程式連線至 Azure Cosmos DB](connect-mongodb-account.md)文章中的指示，抓取 Azure Cosmos DB 的 mongodb API 帳戶的連接資訊。

    ![[連接字串] 頁面的螢幕擷取畫面](./media/mongodb-mongochef/ConnectionStringBlade.png)

2. 按一下 [連接]**** 以開啟 [連接管理員]，然後按一下 [新增連線]****

    ![Studio 3T 連線管理員的螢幕擷取畫面](./media/mongodb-mongochef/ConnectionManager.png)
3. 在 [**新增**連線] 視窗的 [**伺服器**] 索引標籤上，輸入 AZURE Cosmos 帳戶的主機（FQDN）和埠。

    ![[Studio 3T 連接管理員伺服器] 索引標籤的螢幕擷取畫面](./media/mongodb-mongochef/ConnectionManagerServerTab.png)
4. 在 [新增連線]**** 視窗中，請在 [驗證]**** 索引標籤上選擇驗證模式 [基本 (MONGODB-CR 或 SCARM-SHA-1)]****，並輸入使用者名稱和密碼。  接受預設的驗證資料庫 (管理員)，或提供您自己的值。

    ![[Studio 3T 連接管理員驗證] 索引標籤的螢幕擷取畫面](./media/mongodb-mongochef/ConnectionManagerAuthenticationTab.png)
5. 在 [新增連接]**** 視窗的 [SSL]**** 索引標籤上，勾選 [使用 SSL 通訊協定連接]**** 核取方塊和 [接受自我簽署的 SSL 憑證]**** 選項按鈕。

    ![[Studio 3T 連接管理員 SSL] 索引標籤的螢幕擷取畫面](./media/mongodb-mongochef/ConnectionManagerSSLTab.png)
6. 按一下 [測試連接]**** 按鈕以驗證連接資訊，按一下 [確定]**** 以返回 [新增連線] 視窗，然後按一下 [儲存]****。

    ![[Studio 3T 測試連接] 視窗的螢幕擷取畫面](./media/mongodb-mongochef/TestConnectionResults.png)

## <a name="use-studio-3t-to-create-a-database-collection-and-documents"></a>使用 Studio 3T 建立資料庫、集合和文件
若要使用 Studio 3T 建立資料庫、集合和文件，請執行下列步驟：

1. 在 [連接管理員]**** 中，反白顯示連接，然後按一下 [連接]****。

    ![Studio 3T 連線管理員的螢幕擷取畫面](./media/mongodb-mongochef/ConnectToAccount.png)
2. 以滑鼠右鍵按一下主機，然後選擇 [新增資料庫]****。  提供資料庫名稱，然後按一下 [確定] ****。

    ![[Studio 3T 新增資料庫] 選項的螢幕擷取畫面](./media/mongodb-mongochef/AddDatabase1.png)
3. 以滑鼠右鍵按一下資料庫，然後選擇 [新增集合]****。  提供集合名稱，然後按一下 [建立] ****。

    ![[Studio 3T 新增集合] 選項的螢幕擷取畫面](./media/mongodb-mongochef/AddCollection.png)
4. 按一下 [集合]**** 功能表項目，然後按一下 [新增文件]****。

    ![[Studio 3T 新增檔] 功能表項目的螢幕擷取畫面](./media/mongodb-mongochef/AddDocument1.png)
5. 在 [新增文件] 對話方塊中，貼上下列項目，然後按一下 [新增文件] ****。

        {
        "_id": "AndersenFamily",
        "lastName": "Andersen",
        "parents": [
               { "firstName": "Thomas" },
               { "firstName": "Mary Kay"}
        ],
        "children": [
           {
               "firstName": "Henriette Thaulow", "gender": "female", "grade": 5,
               "pets": [{ "givenName": "Fluffy" }]
           }
        ],
        "address": { "state": "WA", "county": "King", "city": "seattle" },
        "isRegistered": true
        }
6. 新增其他文件，這次使用下列內容：

        {
        "_id": "WakefieldFamily",
        "parents": [
            { "familyName": "Wakefield", "givenName": "Robin" },
            { "familyName": "Miller", "givenName": "Ben" }
        ],
        "children": [
            {
                "familyName": "Merriam",
                 "givenName": "Jesse",
                "gender": "female", "grade": 1,
                "pets": [
                    { "givenName": "Goofy" },
                    { "givenName": "Shadow" }
                ]
            },
            {
                "familyName": "Miller",
                 "givenName": "Lisa",
                 "gender": "female",
                 "grade": 8 }
        ],
        "address": { "state": "NY", "county": "Manhattan", "city": "NY" },
        "isRegistered": false
        }
7. 執行範例查詢。 例如，搜尋姓氏 'Andersen' 的家族，並傳回父母和州欄位。

    ![Mongo Chef 查詢結果的螢幕擷取畫面](./media/mongodb-mongochef/QueryDocument1.png)

## <a name="next-steps"></a>後續步驟

- 了解如何[使用 Robo 3T](mongodb-robomongo.md) 搭配 Azure Cosmos DB 適用於 MongoDB 的 API。
- 使用 Azure Cosmos DB 適用於 MongoDB 的 API 瀏覽 Cosmos DB [範例](mongodb-samples.md)。
