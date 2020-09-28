---
title: 針對 Azure Cosmos DB 的 Mongo DB API 中常見的錯誤進行疑難排解
description: 本檔將討論在 Azure Cosmos DB 的 MongoDB API 中遇到的常見問題進行疑難排解的方法。
author: jasonwhowell
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: troubleshooting
ms.date: 07/15/2020
ms.author: jasonh
ms.openlocfilehash: 27a9c7eb48c4a0148401c0d146a50a5197593806
ms.sourcegitcommit: b48e8a62a63a6ea99812e0a2279b83102e082b61
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/28/2020
ms.locfileid: "91409624"
---
# <a name="troubleshoot-common-issues-in-azure-cosmos-dbs-api-for-mongodb"></a>針對 Azure Cosmos DB 的 MongoDB API 常見問題進行疑難排解

下列文章說明使用適用于 MongoDB 的 Azure Cosmos DB API 之資料庫的常見錯誤和解決方案。

>[!Note]
> Azure Cosmos DB 不會裝載 MongoDB 引擎。 它提供 MongoDB [網路通訊協定3.6 版](mongodb-feature-support-36.md) 和舊版支援的連線 [通訊協定版本 3.2](mongodb-feature-support.md)，因此某些錯誤只會在 Azure Cosmos DB 適用于 MongoDB 的 API 中找到。 

## <a name="common-errors-and-solutions"></a>常見錯誤和解決方案

| 錯誤               | 程式碼  | 描述  | 解決方法  |
|---------------------|-------|--------------|-----------|
| ExceededTimeLimit   | 50 | 要求已超過執行的逾時值 (60 秒)。 | 此錯誤的原因可能很多。 其中一個原因是目前配置的要求單位容量不足，而無法完成要求。 這可以藉由增加該集合或資料庫的要求單位來解決。 在其他情況下，您可以藉由將大型要求分割成較小的要求，來解決此錯誤。 |
| TooManyRequests     | 16500 | 取用的要求單位總數已超過針對集合佈建的要求單位率並已進行節流。 | 請考慮從 Azure 入口網站調整指派給容器或容器集的輸送量，或重試作業。 |
| ExceededMemoryLimit | 16501 | 做為多租用戶服務，作業已超出用戶端的記憶體配額。 | 透過更嚴格的查詢準則來縮小作業的範圍，或經由 [Azure 入口網站](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)連絡支援人員。 範例： `db.getCollection('users').aggregate([{$match: {name: "Andy"}}, {$sort: {age: -1}}]))` |
| 與指定的排序依據項目對應的索引路徑已遭到排除/排序依據查詢沒有可從中提供服務的對應複合式索引。 | 2 | 查詢針對未編制索引的欄位要求排序。 | 針對嘗試的排序查詢，建立相符的索引 (或複合索引) 。 |
| MongoDB 線路版本問題 | - | 舊版 MongoDB 驅動程式無法在連接字串中偵測 Azure Cosmos 帳戶的名稱。 | 在 MongoDB 連接字串的 Cosmos DB API 結尾附加*appName = @**accountName** @ * ，其中***accountName***是您的 Cosmos DB 帳戶名稱。 |

## <a name="next-steps"></a>後續步驟

- 了解如何[使用 Studio 3T](mongodb-mongochef.md) 搭配 Azure Cosmos DB 適用於 MongoDB 的 API。
- 了解如何[使用 Robo 3T](mongodb-robomongo.md) 搭配 Azure Cosmos DB 適用於 MongoDB 的 API。
- 使用 Azure Cosmos DB 適用於 MongoDB 的 API 瀏覽 Cosmos DB [範例](mongodb-samples.md)。

