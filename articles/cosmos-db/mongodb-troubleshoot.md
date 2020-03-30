---
title: 解決 Azure Cosmos DB Mongo DB API 中的常見錯誤
description: 本文檔討論瞭解決 Azure Cosmos DB 為 MongoDB 的 API 中遇到的常見問題的方法。
author: LuisBosquez
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: troubleshooting
ms.date: 06/05/2019
ms.author: lbosq
ms.openlocfilehash: d9a4e336f582e866fd057f6c281f892ce07b34fc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75941837"
---
# <a name="troubleshoot-common-issues-in-azure-cosmos-dbs-api-for-mongodb"></a>解決 Azure 宇宙 DB MongoDB API 中的常見問題

Azure Cosmos DB 實現常見 NoSQL 資料庫（包括 MongoDB）的線協定。 由於有線協定實現，您可以使用使用 NoSQL 資料庫的現有用戶端 SDK、驅動程式和工具，透明地與 Azure Cosmos DB 進行交互。 Azure Cosmos DB 不使用資料庫的任何原始程式碼為任何 NoSQL 資料庫提供與線相容的 API。 任何瞭解有線協定版本的 MongoDB 用戶端驅動程式都可以連接到 Azure Cosmos DB。

雖然 Azure Cosmos DB 的 MongoDB API 與 MongoDB 的有線協定的 3.2 版本相容（3.4 版中添加的查詢運算子和功能當前可作為預覽版本提供），但有一些自訂錯誤代碼對應于 Azure Cosmos DB特定錯誤。 本文介紹了不同的錯誤、錯誤代碼和解決這些錯誤的步驟。

## <a name="common-errors-and-solutions"></a>常見錯誤和解決方案

| 錯誤               | 程式碼  | 描述  | 解決方法  |
|---------------------|-------|--------------|-----------|
| TooManyRequests     | 16500 | 取用的要求單位總數已超過針對集合佈建的要求單位率並已進行節流。 | 請考慮從 Azure 門戶縮放分配給容器或一組容器的輸送量，也可以重試該操作。 |
| ExceededMemoryLimit | 16501 | 做為多租用戶服務，作業已超出用戶端的記憶體配額。 | 透過更嚴格的查詢準則來縮小作業的範圍，或經由 [Azure 入口網站](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)連絡支援人員。 範例： `db.getCollection('users').aggregate([{$match: {name: "Andy"}}, {$sort: {age: -1}}]))` |
| 排除與指定訂單項對應的索引路徑/按查詢排序沒有可以從中提供的相應複合索引。 | 2 | 查詢請求未編制索引的欄位上的排序。 | 為正在嘗試的排序查詢創建匹配索引（或複合索引）。 |
| MongoDB 線路版本問題 | - | 較舊版本的 MongoDB 驅動程式無法檢測連接字串中的 Azure Cosmos 帳戶的名稱。 | 在 Cosmos DB 的 MongoDB 連接字串的 API 末尾追加*appName_**帳戶名稱**@*，其中***帳戶名稱***是 Cosmos DB 帳戶名稱。 |


## <a name="next-steps"></a>後續步驟

- 了解如何[使用 Studio 3T](mongodb-mongochef.md) 搭配 Azure Cosmos DB 適用於 MongoDB 的 API。
- 了解如何[使用 Robo 3T](mongodb-robomongo.md) 搭配 Azure Cosmos DB 適用於 MongoDB 的 API。
- 使用 Azure Cosmos DB 適用於 MongoDB 的 API 瀏覽 Cosmos DB [範例](mongodb-samples.md)。

