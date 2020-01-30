---
title: Azure Cosmos DB 資料表 API .NET Standard SDK 和資源
description: 全面了解 Azure Cosmos DB 資料表 API 和 .NET Standard SDK，包括發行日期、停用日期及每個版本之間所做的變更。
author: sakash279
ms.author: akshanka
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: dotnet
ms.topic: reference
ms.date: 03/18/2019
ms.openlocfilehash: 48fd85e27feb3d0c7f7f722dbbd502be55684385
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/28/2020
ms.locfileid: "76771581"
---
# <a name="azure-cosmos-db-table-net-standard-api-download-and-release-notes"></a>Azure Cosmos DB 資料表 .NET Standard API：下載和版本資訊
> [!div class="op_single_selector"]
> 
> * [.NET](table-sdk-dotnet.md)
> * [.NET Standard](table-sdk-dotnet-standard.md)
> * [Java](table-sdk-java.md)
> * [Node.js](table-sdk-nodejs.md)
> * [Python](table-sdk-python.md)

|   |   |
|---|---|
|**SDK 下載**|[NuGet](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table)|
|**範例**|[Cosmos DB 資料表 API .NET 範例](https://github.com/Azure-Samples/azure-cosmos-table-dotnet-core-getting-started)|
|**快速入門**|[快速入門](create-table-dotnet.md)|
|**教學課程**|[教學課程](tutorial-develop-table-dotnet.md)|
|**目前支援的架構**|[Microsoft .NET Standard 2.0](https://www.nuget.org/packages/NETStandard.Library)|
|**報告問題**|[報告問題](https://github.com/Azure/azure-cosmos-table-dotnet/issues)|

## <a name="release-notes-for-200-series"></a>2\.0.0 系列的版本資訊
2.0.0 系列會依賴[Cosmos](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/)，並改善效能，並將命名空間匯總到 Cosmos DB 端點。

### <a name="a-name200-preview200-preview"></a><a name="2.0.0-preview"/>2.0.0-preview
* 2\.0.0 Table SDK 的初始預覽，它會相依于[Cosmos](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/)，並改善效能，並將命名空間匯總到 Cosmos DB 端點。 公用 API 保持不變。

## <a name="release-notes-for-100-series"></a>1\.0.0 系列的版本資訊
1.0.0 系列會依賴[Microsoft. Azure DocumentDB](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.Core/)。

### <a name="a-name105105"></a><a name="1.0.5"/>1.0。5
* 在 TableClientConfiguration 下引進新的設定，以使用 Rest 執行程式來與 Cosmos DB 進行通訊資料表 API

### <a name="a-name105-preview105-preview"></a><a name="1.0.5-preview"/>1.0.5-預覽
* 錯誤修正

### <a name="a-name104104"></a><a name="1.0.4"/>1.0。4
* 錯誤修正
* 提供 RestExecutorConfiguration 的 HttpClientTimeout 選項。

### <a name="a-name104-preview104-preview"></a><a name="1.0.4-preview"/>1.0.4-預覽
* 錯誤修正
* 提供 RestExecutorConfiguration 的 HttpClientTimeout 選項。

### <a name="a-name101101"></a><a name="1.0.1"/>1.0.1
* 錯誤修正

### <a name="a-name100100"></a><a name="1.0.0"/>1.0.0
* 公開上市版本

### <a name="a-name0110-preview0110-preview"></a><a name="0.11.0-preview"/>0.11.0-預覽
* 已變更 CloudTableClient 的設定方式。 它現在會在結構中取得 TableClientConfiguration 物件。 TableClientConfiguration 提供不同的屬性來設定用戶端行為，取決於目標端點是 Cosmos DB 資料表 API 還是 Azure 儲存體資料表 API。
* 已將支援新增至 TableQuery，以便在自訂資料行上以排序次序傳回結果。 只有 Cosmos DB 資料表端點上才支援這項功能。
* 已新增在各種結果類型上公開 RequestCharges 的支援。 只有 Cosmos DB 資料表端點上才支援這項功能。

### <a name="a-name0101-preview0101-preview"></a><a name="0.10.1-preview"/>0.10.1 - 預覽
* 針對 Azure 儲存體資料表端點新增 SAS 權杖、TablePermissions、ServiceProperties 和 ServiceStats 作業支援。 
   > [!NOTE]
   > 尚未支援之前 Azure 儲存體資料表 SDK 中的某些功能，例如用戶端加密。

### <a name="a-name0100-preview0100-preview"></a><a name="0.10.0-preview"/>0.10.0-預覽
* 新支援對 Azure 儲存體資料表端點的核心 CRUD、批次及查詢作業。 
   > [!NOTE]
   > 尚未支援之前 Azure 儲存體資料表 SDK 中的某些功能，例如用戶端加密。

### <a name="a-name091-preview091-preview"></a><a name="0.9.1-preview"/>0.9.1-預覽
* Azure Cosmos DB 資料表 .NET Standard SDK 是一個跨平台 .NET 程式庫，可提供有效率地存取 Cosmos DB 上資料表資料模型的權限。 這個初始版本支援一組完整的資料表和實體 CRUD + 查詢功能，其中具有類似[適用於 .NET Framework 的 Cosmos DB 資料表 SDK](table-sdk-dotnet.md) 的 API。 
   > [!NOTE]
   >  0\.9.1-預覽版本中尚未支援 Azure 儲存體資料表端點。

## <a name="release-and-retirement-dates"></a>發行和停用日期
Microsoft 至少會在停用 SDK 的 **12 個月** 之前提供通知，以供順利轉換至較新/支援的版本。

這種跨平臺 .NET Standard 程式庫[Cosmos](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table)會取代 .NET Framework 程式庫[CosmosDB. table](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.Table)。

### <a name="200-series"></a>2.0.0 系列
| 版本 | 發行日期 | 停用日期 |
| --- | --- | --- |
| [2.0.0-preview](#2.0.0-preview) |Auguest 22，2019 |--- |

### <a name="100-series"></a>1.0.0 系列
| 版本 | 發行日期 | 停用日期 |
| --- | --- | --- |
| [1.0.5](#1.0.5) |2019 年 9 月 13 日 |--- |
| [1.0.5-預覽](#1.0.5-preview) |Auguest 20，2019 |--- |
| [1.0.4](#1.0.4) |Auguest 12、2019 |--- |
| [1.0.4-預覽](#1.0.4-preview) |2019年7月26日 |--- |
| 1.0.2-預覽 |2019 5 月2日 |--- |
| [1.0.1](#1.0.1) |2019年4月19日 |--- |
| [1.0.0](#1.0.0) |2019 年 3 月 13 日 |--- |
| [0.11.0-預覽](#0.11.0-preview) |2019年3月5日 |--- |
| [0.10.1 - 預覽](#0.10.1-preview) |2019 年 1 月 22 日 |--- |
| [0.10.0-預覽](#0.10.0-preview) |2018 年 12 月 18 日 |--- |
| [0.9.1-預覽](#0.9.1-preview) |2018 年 10 月 18 日 |--- |


## <a name="faq"></a>常見問題集

[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>請參閱
若要深入了解 Azure Cosmos DB 資料表 API，請參閱 [Azure Cosmos DB 資料表 API 的簡介](table-introduction.md)。
