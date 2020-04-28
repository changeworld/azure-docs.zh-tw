---
title: 針對 Mongo DB Azure Cosmos DB API 中的常見錯誤進行疑難排解
description: 本檔討論針對 Azure Cosmos DB 的 MongoDB API 中遇到的常見問題進行疑難排解的方式。
author: LuisBosquez
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: troubleshooting
ms.date: 06/05/2019
ms.author: lbosq
ms.openlocfilehash: d9a4e336f582e866fd057f6c281f892ce07b34fc
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "75941837"
---
# <a name="troubleshoot-common-issues-in-azure-cosmos-dbs-api-for-mongodb"></a>針對 Azure Cosmos DB 的 MongoDB API 中常見的問題進行疑難排解

Azure Cosmos DB 會實作為通用 NoSQL 資料庫（包括 MongoDB）的有線通訊協定。 由於有線通訊協定的實行，您可以使用現有的用戶端 Sdk、驅動程式和工具來與 NoSQL 資料庫互動，以透明的方式與 Azure Cosmos DB 互動。 Azure Cosmos DB 不會使用資料庫的任何原始程式碼，為任何 NoSQL 資料庫提供與有線相容的 Api。 任何瞭解有線通訊協定版本的 MongoDB 用戶端驅動程式都可以連接到 Azure Cosmos DB。

雖然 Azure Cosmos DB 的 MongoDB 版 API 相容于3.2 版的 MongoDB 連線通訊協定（在3.4 版中新增的查詢運算子和功能目前可供預覽），但有一些自訂錯誤碼會對應到 Azure Cosmos DB 的特定錯誤。 本文說明不同的錯誤、錯誤代碼，以及解決這些錯誤的步驟。

## <a name="common-errors-and-solutions"></a>常見的錯誤和解決方案

| 錯誤               | 程式碼  | 描述  | 解決方法  |
|---------------------|-------|--------------|-----------|
| TooManyRequests     | 16500 | 取用的要求單位總數已超過針對集合佈建的要求單位率並已進行節流。 | 請考慮從 Azure 入口網站調整指派給容器或一組容器的輸送量，或重試作業。 |
| ExceededMemoryLimit | 16501 | 做為多租用戶服務，作業已超出用戶端的記憶體配額。 | 透過更嚴格的查詢準則來縮小作業的範圍，或經由 [Azure 入口網站](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)連絡支援人員。 範例： `db.getCollection('users').aggregate([{$match: {name: "Andy"}}, {$sort: {age: -1}}]))` |
| 已排除對應于指定之 order by 專案的索引路徑/order by 查詢沒有可從中提供服務的對應複合索引。 | 2 | 查詢會針對未編制索引的欄位要求排序。 | 為嘗試的排序查詢建立相符的索引（或複合索引）。 |
| MongoDB 線路版本問題 | - | 較舊版本的 MongoDB 驅動程式無法在連接字串中偵測 Azure Cosmos 帳戶的名稱。 | 在 MongoDB 的 Cosmos DB API 連接字串結尾附加*appName = @**accountName** @ * ，其中***accountName***是您 Cosmos DB 的帳戶名稱。 |


## <a name="next-steps"></a>後續步驟

- 了解如何[使用 Studio 3T](mongodb-mongochef.md) 搭配 Azure Cosmos DB 適用於 MongoDB 的 API。
- 了解如何[使用 Robo 3T](mongodb-robomongo.md) 搭配 Azure Cosmos DB 適用於 MongoDB 的 API。
- 使用 Azure Cosmos DB 適用於 MongoDB 的 API 瀏覽 Cosmos DB [範例](mongodb-samples.md)。

