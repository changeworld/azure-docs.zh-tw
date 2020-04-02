---
title: Azure 宇宙資料庫:SQL Python API、SDK &资源
description: 了解所有 SQL Python API 和 SDK 相關資訊，包括發行日期、停用日期及 Azure Cosmos DB Python SDK 每個版本之間所做的變更。
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: python
ms.topic: reference
ms.date: 11/29/2018
ms.author: sngun
ms.openlocfilehash: 090446d286b38a0bfcbde4ed9e77235050b36417
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/02/2020
ms.locfileid: "80547708"
---
# <a name="azure-cosmos-db-python-sdk-for-sql-api-release-notes-and-resources"></a>Azure Cosmos DB Python SDK for SQL API：版本資訊與資源
> [!div class="op_single_selector"]
> * [.NET](sql-api-sdk-dotnet.md)
> * [.NET 更改來源](sql-api-sdk-dotnet-changefeed.md)
> * [.NET Core](sql-api-sdk-dotnet-core.md)
> * [Node.js](sql-api-sdk-node.md)
> * [非同步 Java](sql-api-sdk-async-java.md)
> * [Java](sql-api-sdk-java.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](https://docs.microsoft.com/rest/api/cosmos-db/)
> * [REST 資源提供者](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/)
> * [SQL](sql-api-query-reference.md)
> * [批次執行器 -.NET](sql-api-sdk-bulk-executor-dot-net.md)
> * [批次執行器 -Java](sql-api-sdk-bulk-executor-java.md)

| |  |
|---|---|
|**下載 SDK**|[PyPI](https://pypi.org/project/azure-cosmos)|
|**API 文件**|[Python API 參考文件](https://docs.microsoft.com/python/api/azure-cosmos/?view=azure-python)|
|**SDK 安裝指示**|[SDK 安裝指示](https://github.com/Azure/azure-cosmos-python)|
|**參與 SDK**|[GitHub](https://github.com/Azure/azure-cosmos-python)|
|**開始使用**|[開始使用 Python SDK](sql-api-python-application.md)|
|**目前支援的平台**|[Python 2.7](https://www.python.org/downloads/) 和 [Python 3.5](https://www.python.org/downloads/)|

## <a name="release-notes"></a>版本資訊

### <a name="302"></a><a name="3.0.2"/>3.0.2
* 新增對 MultiPolygon 資料類型的支援
* 修正工作階段讀取重試原則中的錯誤 (bug)
* 修正在 Base 64 字串解碼期間造成錯誤填補問題的錯誤 (bug)

### <a name="301"></a><a name="3.0.1"/>3.0.1
* 修正 LocationCache 中的錯誤 (bug)
* 修正端點重試邏輯的錯誤 (bug)
* 修正文件

### <a name="300"></a><a name="3.0.0"/>3.0.0
* 多重區域寫入支援。
* 命名空間已變更為 azure.cosmos。
* 集合及文件概念已重新命名為容器及項目，document_client 已重新命名為 cosmos_client。 

### <a name="233"></a><a name="2.3.3"/>2.3.3
* 新增對 Proxy 的支援
* 新增對讀取變更摘要的支援
* 新增對集合配額標頭的支援
* 修正造成大型工作階段權杖問題的錯誤 (bug)
* 修正 ReadMedia API 的錯誤 (bug)
* 修正分割區索引鍵範圍快取中的錯誤 (bug)

### <a name="232"></a><a name="2.3.2"/>2.3.2
* 已新增對連線問題預設重試次數的支援。

### <a name="231"></a><a name="2.3.1"/>2.3.1
* 更新參考 Azure Cosmos DB 而非 Azure DocumentDB 的文件。

### <a name="230"></a><a name="2.3.0"/>2.3.0
* 此 SDK 版本需要使用從 https://aka.ms/cosmosdb-emulator 下載之最新版本的 Azure Cosmos DB 模擬器。

### <a name="221"></a><a name="2.2.1"/>2.2.1
* 彙總字典的修正錯誤。
* 資源連結中的修剪斜線修正錯誤。
* 已新增 Unicode 編碼的測試。

### <a name="220"></a><a name="2.2.0"/>2.2.0
* 已新增對名為 ConsistentPrefix 的新一致性層級的支援。


### <a name="210"></a><a name="2.1.0"/>2.1.0
* 新增彙總查詢的支援 (COUNT、MIN、MAX、SUM 和 AVG)。
* 添加了一個選項,用於在針對Cosmos DB 模擬器運行時禁用TLS驗證。
* 移除相依要求模組必須為 2.10.0 的限制。
* 已將分割區集合的最小輸送量從 10,100 RU/s 降低為 2500 RU/s。
* 在預存程序執行期間，加入支援指令碼記錄功能。
* REST API 版本在此版本中提升至 '2017-01-19'。

### <a name="201"></a><a name="2.0.1"/>2.0.1
* 對文件註解進行編輯性的變更。

### <a name="200"></a><a name="2.0.0"/>2.0.0
* 新增 Python 3.5 的支援。
* 新增使用要求模組的連接集區支援。
* 新增工作階段一致性的支援。
* 新增對已分割集合的 TOP/ORDERBY 查詢支援。

### <a name="190"></a><a name="1.9.0"/>1.9.0
* 新加入已節流處理要求的重試原則支援。 (已限制的請求收到請求速率過大的異常,錯誤代碼 429。預設情況下,當遇到錯誤代碼 429 時,Azure Cosmos DB 會為每個請求重試 9 次,從而在響應標頭中遵守重試時間。 如果您想要忽略伺服器在多次重試之間傳回的 retryAfter 時間，現在可以在 ConnectionPolicy 物件上的 RetryOptions 屬性中設定固定的重試間隔時間。 Azure Cosmos DB 現在會針對每個要進行節流處理的要求等候最多 30 秒 (不論重試計數為何)，並傳回包含錯誤碼 429 的回應。 您也可以在 ConnectionPolicy 物件上的 RetryOptions 屬性中覆寫該時間。
* Cosmos DB 現在會傳回 x-ms-throttle-retry-count 和 x-ms-throttle-retry-wait-time-ms 做為每個要求的回應標頭，其代表節流重試計數和要求歷經多次重試的累積時間。
* 移除 RetryPolicy 類別和 document_client 類別上公開的對應屬性 (retry_policy)，改為引進公開 ConnectionPolicy 類別上的 RetryOptions 屬性，它可以用來覆寫某些預設的重試選項。

### <a name="180"></a><a name="1.8.0"/>1.8.0
* 新增對多重區域資料庫帳戶的支援。

### <a name="170"></a><a name="1.7.0"/>1.7.0
* 新加入文件的存留時間 (TTL) 功能支援。

### <a name="161"></a><a name="1.6.1"/>1.6.1
* 伺服器端資料分割相關錯誤修正，允許在分割區索引鍵路徑中使用特殊字元。

### <a name="160"></a><a name="1.6.0"/>1.6.0
* 實作[已分割的集合](partition-data.md)和[使用者定義的效能等級](performance-levels.md)。 

### <a name="150"></a><a name="1.5.0"/>1.5.0
* 新增「雜湊和範圍」分割區解析程式來協助將應用程式跨多個分割區分區。

### <a name="142"></a><a name="1.4.2"/>1.4.2
* 實作 Upsert。 已新增新的 UpsertXXX 方法以支援 Upsert 功能。
* 實作以識別碼為基礎的路由。 不需變更公用 API，所有變更皆為內部變更。

### <a name="120"></a><a name="1.2.0"/>1.2.0
* 支援地理空間索引。
* 驗證所有資源的識別碼屬性。 資源的識別碼不能包含 ?、/、#、\, 字元，或以空格作為結尾。
* 將新標頭「索引轉換進度」加至 ResourceResponse。

### <a name="110"></a><a name="1.1.0"/>1.1.0
* 實作 V2 索引原則。

### <a name="101"></a><a name="1.0.1"/>1.0.1
* 支援 Proxy 連線。

### <a name="100"></a><a name="1.0.0"/>1.0.0
* GA SDK。

## <a name="release--retirement-dates"></a>發行和停用日期
Microsoft 至少會在停用 SDK 的 **12 個月** 之前提供通知，以供順利轉換至較新/支援的版本。

新的功能與最佳化項目只會新增至目前的 SDK，因此建議您一律盡早升級至最新的 SDK 版本。 

服務會拒絕使用已停用 SDK 的任何 Cosmos DB 要求。

> [!WARNING]
> 在版本**1.0.0**之前,用於 SQL API 的所有版本的 Python SDK 均於**2016 年 2 月 29**日停用。 
> 
> 

> [!WARNING]
> 用於 SQL API 的 Python SDK 的所有版本 1.x 和 2.x 將於**2020 年 8 月 30**日停用。 
> 
> 

<br/>

| 版本 | 發行日期 | 停用日期 |
| --- | --- | --- |
| [3.0.2](#3.0.2) |2018 年 11 月 15 日 |--- |
| [3.0.1](#3.0.1) |2018 年 10 月 4 日 |--- |
| [2.3.3](#2.3.3) |2018 年 9 月 8 日 |2020年8月30日 |
| [2.3.2](#2.3.2) |2018 年 5 月 8 日 |2020年8月30日 |
| [2.3.1](#2.3.1) |2017 年 12 月 21 日 |2020年8月30日 |
| [2.3.0](#2.3.0) |2017 年 11 月 10 日 |2020年8月30日 |
| [2.2.1](#2.2.1) |2017 年 9 月 29 日 |2020年8月30日 |
| [2.2.0](#2.2.0) |2017 年 5 月 10 日 |2020年8月30日 |
| [2.1.0](#2.1.0) |2017 年 5 月 1 日 |2020年8月30日 |
| [2.0.1](#2.0.1) |2016 年 10 月 30 日 |2020年8月30日 |
| [2.0.0](#2.0.0) |2016 年 9 月 29 日 |2020年8月30日 |
| [1.9.0](#1.9.0) |2016 年 7 月 7 日 |2020年8月30日 |
| [1.8.0](#1.8.0) |2016 年 6 月 14 日 |2020年8月30日 |
| [1.7.0](#1.7.0) |2016 年 4 月 26 日 |2020年8月30日 |
| [1.6.1](#1.6.1) |2016 年 4 月 8 日 |2020年8月30日 |
| [1.6.0](#1.6.0) |2016 年 3 月 29 日 |2020年8月30日 |
| [1.5.0](#1.5.0) |2016 年 1 月 3 日 |2020年8月30日 |
| [1.4.2](#1.4.2) |2015 年 10 月 6 日 |2020年8月30日 |
| 1.4.1 |2015 年 10 月 6 日 |2020年8月30日 |
| [1.2.0](#1.2.0) |2015 年 8 月 6 日 |2020年8月30日 |
| [1.1.0](#1.1.0) |2015 年 7 月 9 日 |2020年8月30日 |
| [1.0.1](#1.0.1) |2015 年 5 月 25 日 |2020年8月30日 |
| [1.0.0](#1.0.0) |2015 年 4 月 7 日 |2020年8月30日 |
| 0.9.4-prelease |2015 年 1 月 14 日 |2016 年 2 月 29 日 |
| 0.9.3-prelease |2014 年 12 月 9 日 |2016 年 2 月 29 日 |
| 0.9.2-prelease |2014 年 11 月 25 日 |2016 年 2 月 29 日 |
| 0.9.1-prelease |2014 年 9 月 23 日 |2016 年 2 月 29 日 |
| 0.9.0-prelease |2014 年 8 月 21 日 |2016 年 2 月 29 日 |

## <a name="faq"></a>常見問題集
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>另請參閱
若要深入了解 Cosmos DB，請參閱 [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) 服務頁面。 

