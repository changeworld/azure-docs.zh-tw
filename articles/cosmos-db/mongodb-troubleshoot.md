---
title: 針對 Azure Cosmos DB 的 Mongo DB API 中常見的錯誤進行疑難排解
description: 本檔將討論在 Azure Cosmos DB 的 MongoDB API 中遇到的常見問題進行疑難排解的方法。
author: christopheranderson
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: troubleshooting
ms.date: 07/15/2020
ms.author: chrande
ms.openlocfilehash: 26097408d0b83b043f4a25183146c892fc4b48ad
ms.sourcegitcommit: fc23b4c625f0b26d14a5a6433e8b7b6fb42d868b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/17/2021
ms.locfileid: "98538547"
---
# <a name="troubleshoot-common-issues-in-azure-cosmos-dbs-api-for-mongodb"></a>針對 Azure Cosmos DB 的 MongoDB API 常見問題進行疑難排解
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

下列文章說明使用適用于 MongoDB 的 Azure Cosmos DB API 進行部署的常見錯誤和解決方案。

>[!Note]
> Azure Cosmos DB 不會裝載 MongoDB 引擎。 它會提供 MongoDB 網路通訊協定的執行。 因此，其中有些錯誤只會在 Azure Cosmos DB 的 MongoDB API 中找到。 

## <a name="common-errors-and-solutions"></a>常見錯誤和解決方案

| 程式碼       | 錯誤                | 描述  | 解決方法  |
|------------|----------------------|--------------|-----------|
| 2 | 已排除對應于指定之 order by 專案的索引路徑，或 order by 查詢沒有可從中提供服務的對應複合索引。 | 查詢針對未編制索引的欄位要求排序。 | 針對嘗試的排序查詢，建立相符的索引 (或複合索引) 。 |
| 13 | 未經授權 | 要求缺少完成的許可權。 | 確定您已為資料庫和集合設定適當的許可權。  |
| 16 | InvalidLength | 指定的要求有不正確長度。 | 如果您使用說明 ( # A1 函式，請確定您只提供一個作業。 |
| 26 | NamespaceNotFound | 找不到查詢中所參考的資料庫或集合。 | 確定您的資料庫/集合名稱與查詢中的名稱完全相符。|
| 50 | ExceededTimeLimit | 要求已超過執行的逾時值 (60 秒)。 |  此錯誤的原因可能很多。 其中一個原因是目前配置的要求單位容量不足以完成要求。 這可以藉由增加該集合或資料庫的要求單位來解決。 在其他情況下，您可以藉由將大型要求分割成較小的要求，來解決此錯誤。 重試已收到此錯誤的寫入作業，可能會導致寫入重複。|
| 61 | ShardKeyNotFound | 您要求中的檔未包含集合的分區索引鍵 (Azure Cosmos DB 分割區索引鍵) 。 | 確定已在要求中使用集合的分區金鑰。|
| 66 | ImmutableField | 要求正在嘗試變更不可變的欄位 | 「識別碼」欄位是不可變的。 確定您的要求不會嘗試更新該欄位。 |
| 67 | CannotCreateIndex | 無法完成建立索引的要求。 | 最多可在容器中建立500個單一欄位索引。 多達八個欄位可以包含在複合索引中， (3.6 +) 支援複合索引。 |
| 115 | CommandNotSupported | 不支援所嘗試的要求。 | 錯誤中應提供其他詳細資料。 如果這項功能對您的部署很重要，請在 [Azure 入口網站](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)中建立支援票證，讓我們知道。 |
| 11000 | DuplicateKey | 分區索引鍵 (您要插入之檔的 Azure Cosmos DB 資料分割索引鍵) 已經存在於集合中，或違反了唯一的索引欄位條件約束。 | 使用 update ( # A1 函數來更新現有的檔。 如果違反了唯一索引欄位條件約束，請使用尚未存在於分區/分割區中的域值來插入或更新檔。 |
| 16500 | TooManyRequests  | 取用的要求單位總數已超過針對集合佈建的要求單位率並已進行節流。 | 請考慮從 Azure 入口網站調整指派給容器或容器集的輸送量，或重試作業。 如果您 [啟用 SSR](prevent-rate-limiting-errors.md) (伺服器端重試) ，Azure Cosmos DB 會自動重試因為此錯誤而失敗的要求。 |
| 16501 | ExceededMemoryLimit | 做為多租用戶服務，作業已超出用戶端的記憶體配額。 | 透過更嚴格的查詢準則來縮小作業的範圍，或經由 [Azure 入口網站](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)連絡支援人員。 範例： `db.getCollection('users').aggregate([{$match: {name: "Andy"}}, {$sort: {age: -1}}]))` |
| 40324 | 無法辨識的管線階段名稱。 | 無法辨識匯總管線要求中的階段名稱。 | 確定所有匯總管線名稱在您的要求中都是有效的。 |
| - | MongoDB 線路版本問題 | 舊版 MongoDB 驅動程式無法在連接字串中偵測 Azure Cosmos 帳戶的名稱。 | 在 MongoDB 連接字串的 Cosmos DB API 結尾附加 *appName = @**accountName** @* ，其中 ***accountName*** 是您的 Cosmos DB 帳戶名稱。 |
| - | MongoDB 用戶端網路問題 (例如通訊端或 endOfStream 例外狀況) | 網路要求失敗。 這通常是因為 MongoDB 用戶端嘗試使用的非使用中 TCP 連線所致。 MongoDB 驅動程式通常會利用連接共用，這會導致從用於要求的集區中選擇隨機連接。 非作用中的連接通常會在四分鐘後 Azure Cosmos DB 結束時超時。 | 您可以在應用程式程式碼中重試這些失敗的要求、變更您的 MongoDB 用戶端 (驅動程式) 設定，以在四分鐘的超時時間範圍之前清除非使用中的 TCP 連線，或設定您的作業系統存留期設定，以維護處於作用中狀態的 TCP 連線。 |

## <a name="next-steps"></a>後續步驟

- 了解如何[使用 Studio 3T](mongodb-mongochef.md) 搭配 Azure Cosmos DB 適用於 MongoDB 的 API。
- 了解如何[使用 Robo 3T](mongodb-robomongo.md) 搭配 Azure Cosmos DB 適用於 MongoDB 的 API。
- 使用 Azure Cosmos DB 適用於 MongoDB 的 API 瀏覽 Cosmos DB [範例](mongodb-samples.md)。
