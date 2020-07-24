---
title: 針對 Mongo DB Azure Cosmos DB API 中的常見錯誤進行疑難排解
description: 本檔討論針對 Azure Cosmos DB 的 MongoDB API 中遇到的常見問題進行疑難排解的方式。
author: LuisBosquez
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: troubleshooting
ms.date: 07/15/2020
ms.author: lbosq
ms.openlocfilehash: f75374fc88923a0f131d513bebf0ffe1feeca359
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87076758"
---
# <a name="troubleshoot-common-issues-in-azure-cosmos-dbs-api-for-mongodb"></a>針對 Azure Cosmos DB 的 MongoDB API 中常見的問題進行疑難排解

下列文章說明使用適用于 MongoDB 的 Azure Cosmos DB API 之資料庫的常見錯誤和解決方案。

>[!Note]
> Azure Cosmos DB 不裝載 MongoDB 引擎。 它提供 MongoDB[線路通訊協定3.6 版](mongodb-feature-support-36.md)的執行，以及連線[通訊協定版本 3.2](mongodb-feature-support.md)的舊版支援，因此某些錯誤只會在 Azure Cosmos DB 的 MongoDB API 中找到。 

## <a name="common-errors-and-solutions"></a>常見的錯誤和解決方案

| 錯誤               | 程式碼  | 描述  | 解決方法  |
|---------------------|-------|--------------|-----------|
| ExceededTimeLimit   | 50 | 要求已超過執行60秒的時間。 | 造成此錯誤的原因可能有許多種。 其中一個原因是目前配置的要求單位容量不足，無法完成要求。 這可以藉由增加該集合或資料庫的要求單位來解決。 在其他情況下，這個錯誤可以藉由將大型要求分割成較小的要求來解決。 |
| TooManyRequests     | 16500 | 取用的要求單位總數已超過針對集合佈建的要求單位率並已進行節流。 | 請考慮從 Azure 入口網站調整指派給容器或一組容器的輸送量，或重試作業。 |
| ExceededMemoryLimit | 16501 | 做為多租用戶服務，作業已超出用戶端的記憶體配額。 | 透過更嚴格的查詢準則來縮小作業的範圍，或經由 [Azure 入口網站](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)連絡支援人員。 範例： `db.getCollection('users').aggregate([{$match: {name: "Andy"}}, {$sort: {age: -1}}]))` |
| 已排除對應于指定之 order by 專案的索引路徑/order by 查詢沒有可從中提供服務的對應複合索引。 | 2 | 查詢會針對未編制索引的欄位要求排序。 | 為嘗試的排序查詢建立相符的索引（或複合索引）。 |
| MongoDB 線路版本問題 | - | 較舊版本的 MongoDB 驅動程式無法在連接字串中偵測 Azure Cosmos 帳戶的名稱。 | 在 MongoDB 的 Cosmos DB API 連接字串結尾附加*appName = @**accountName** @ * ，其中***accountName***是您 Cosmos DB 的帳戶名稱。 |

## <a name="next-steps"></a>後續步驟

- 了解如何[使用 Studio 3T](mongodb-mongochef.md) 搭配 Azure Cosmos DB 適用於 MongoDB 的 API。
- 了解如何[使用 Robo 3T](mongodb-robomongo.md) 搭配 Azure Cosmos DB 適用於 MongoDB 的 API。
- 使用 Azure Cosmos DB 適用於 MongoDB 的 API 瀏覽 Cosmos DB [範例](mongodb-samples.md)。

