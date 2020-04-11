---
title: 使用 Robo 3T 連線至 Azure Cosmos DB
description: 了解如何使用 Robo 3T 和 Azure Cosmos DB 適用於 MongoDB 的 API 連線到 Azure Cosmos DB
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: conceptual
ms.date: 03/23/2020
author: timsander1
ms.author: tisande
ms.openlocfilehash: 627849acb902a721c80b98adbd16103b4729591b
ms.sourcegitcommit: fb23286d4769442631079c7ed5da1ed14afdd5fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/10/2020
ms.locfileid: "81114199"
---
# <a name="use-robo-3t-with-azure-cosmos-dbs-api-for-mongodb"></a>使用 Robo 3T 搭配 Azure Cosmos DB 適用於 MongoDB 的 API

若要使用 Robo 3T 連線到 Cosmos 帳戶，您必須：

* 下載並安裝 [Robo 3T](https://robomongo.org/)
* 取得您的 Cosmos DB [連接字串](connect-mongodb-account.md)資訊

> [!NOTE]
> 目前,Robo 3T v1.2 和較低版本支援 Cosmos DB 的蒙戈DB API。

## <a name="connect-using-robo-3t"></a>使用 Robo 3T 連線

若要將 Cosmos 帳戶新增至 Robo 3T 連線管理員，請執行下列步驟：

1. 使用[這裡](connect-mongodb-account.md)的指示，擷取由 Azure Cosmos DB API MongoDB 為您的 Cosmos 帳戶設定的連線資訊。

    ![[連接字串] 刀鋒視窗的螢幕擷取畫面](./media/mongodb-robomongo/connectionstringblade.png)
2. 運行*Robomongo*應用程式。

3. 按一下 [檔案]**** 下的 [連接] 按鈕來管理您的連接。 然後，按一下 [MongoDB 連接]**** 視窗中的 [建立]****，將會開啟 [連接設定]**** 視窗。

4. 在 [連接設定]**** 視窗中，選擇名稱。 然後，從步驟 1 的連接資訊中，找出 [主機]**** 和 [連接埠]****，分別輸入至 [位址]**** 和 [連接埠]****。

    ![Robomongo 管理連線的螢幕擷取](./media/mongodb-robomongo/manageconnections.png)
5. 在 [驗證]**** 索引標籤上，按一下 [執行驗證]****。 然後，輸入您的 [資料庫] \(預設值是 *Admin*)、[使用者名稱]**** 和 [密碼]****。
在步驟 1 的連接資訊中，可以找到 [使用者名稱]**** 和 [密碼]****。

    ![Robomongo 認證選項卡的螢幕擷取](./media/mongodb-robomongo/authentication.png)
6. 在 [SSL]**** 索引標籤上，勾選 [使用 SSL 通訊協定]****，然後將 [驗證方法]**** 變更為 [自我簽署憑證]****。

    ![Robomongo SSL 選項卡的螢幕擷取](./media/mongodb-robomongo/SSL.png)
7. 最後，按一下 [測試]**** 以確認您能夠連接，然後按一下 [儲存]****。

## <a name="next-steps"></a>後續步驟

- 了解如何[使用 Studio 3T](mongodb-mongochef.md) 搭配 Azure Cosmos DB 適用於 MongoDB 的 API。
- 使用 Azure Cosmos DB 適用於 MongoDB 的 API 瀏覽 Cosmos DB [範例](mongodb-samples.md)。
