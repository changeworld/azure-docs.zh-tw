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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76771581"
---
# <a name="azure-cosmos-db-table-net-standard-api-download-and-release-notes"></a>Azure Cosmos DB 資料表 .NET Standard API：下載和版本資訊
> [!div class="op_single_selector"]
> 
> * [.NET](table-sdk-dotnet.md)
> * [.NET Standard](table-sdk-dotnet-standard.md)
> * [JAVA](table-sdk-java.md)
> * [Node.js](table-sdk-nodejs.md)
> * [Python](table-sdk-python.md)

|   |   |
|---|---|
|**SDK 下載**|[NuGet](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table)|
|**樣品**|[宇宙資料庫表 API .NET 示例](https://github.com/Azure-Samples/azure-cosmos-table-dotnet-core-getting-started)|
|**快速入門**|[快速入門](create-table-dotnet.md)|
|**教學課程**|[教學課程](tutorial-develop-table-dotnet.md)|
|**目前支援的架構**|[Microsoft .NET Standard 2.0](https://www.nuget.org/packages/NETStandard.Library)|
|**報告問題**|[報告問題](https://github.com/Azure/azure-cosmos-table-dotnet/issues)|

## <a name="release-notes-for-200-series"></a>2.0.0 系列的版本資訊
2.0.0 系列依賴于[Microsoft.Azure.Cosmos，](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/)性能改進和命名空間合併到 Cosmos DB 終結點。

### <a name="200-preview"></a><a name="2.0.0-preview"/>2.0.0-preview
* 2.0.0 表 SDK 的初始預覽，它依賴于[Microsoft.Azure.Cosmos，](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/)性能改進和命名空間合併到 Cosmos DB 終結點。 公共 API 保持不變。

## <a name="release-notes-for-100-series"></a>1.0.0 系列的版本資訊
1.0.0 系列依賴于[Microsoft.Azure.DocumentDB.Core](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.Core/)。

### <a name="105"></a><a name="1.0.5"/>1.0.5
* 在表用戶端配置下引入新的配置，以使用休息執行器與 Cosmos DB 表 API 進行通信

### <a name="105-preview"></a><a name="1.0.5-preview"/>1.0.5 預覽版
* 錯誤修正

### <a name="104"></a><a name="1.0.4"/>1.0.4
* 錯誤修正
* 為休息執行器配置提供 HttpClient 超時選項。

### <a name="104-preview"></a><a name="1.0.4-preview"/>1.0.4 預覽
* 錯誤修正
* 為休息執行器配置提供 HttpClient 超時選項。

### <a name="101"></a><a name="1.0.1"/>1.0.1
* 錯誤修正

### <a name="100"></a><a name="1.0.0"/>1.0.0
* 公開上市版本

### <a name="0110-preview"></a><a name="0.11.0-preview"/>0.11.0 預覽
* 更改了如何配置雲表用戶端。 它現在在構造期間採用表用戶端設定物件。 表用戶端配置提供不同的屬性來配置用戶端行為，具體取決於目標終結點是 Cosmos DB 表 API 還是 Azure 存儲表 API。
* 添加了對表查詢的支援，以按自訂列的排序次序返回結果。 此功能僅在 Cosmos DB 表終結點上受支援。
* 添加了用於公開不同結果類型的"請求費用"的支援。 此功能僅在 Cosmos DB 表終結點上受支援。

### <a name="0101-preview"></a><a name="0.10.1-preview"/>0.10.1 - 預覽
* 針對 Azure 儲存體資料表端點新增 SAS 權杖、TablePermissions、ServiceProperties 和 ServiceStats 作業支援。 
   > [!NOTE]
   > 尚未支援之前 Azure 儲存體資料表 SDK 中的某些功能，例如用戶端加密。

### <a name="0100-preview"></a><a name="0.10.0-preview"/>0.10.0-預覽
* 新支援對 Azure 儲存體資料表端點的核心 CRUD、批次及查詢作業。 
   > [!NOTE]
   > 尚未支援之前 Azure 儲存體資料表 SDK 中的某些功能，例如用戶端加密。

### <a name="091-preview"></a><a name="0.9.1-preview"/>0.9.1-預覽
* Azure Cosmos DB 資料表 .NET Standard SDK 是一個跨平台 .NET 程式庫，可提供有效率地存取 Cosmos DB 上資料表資料模型的權限。 這個初始版本支援一組完整的資料表和實體 CRUD + 查詢功能，其中具有類似[適用於 .NET Framework 的 Cosmos DB 資料表 SDK](table-sdk-dotnet.md) 的 API。 
   > [!NOTE]
   >  0.9.1-預覽版本中尚未支援 Azure 儲存體資料表端點。

## <a name="release-and-retirement-dates"></a>發行和停用日期
Microsoft 至少會在停用 SDK 的 **12 個月** 之前提供通知，以供順利轉換至較新/支援的版本。

這個跨平臺.NET標準庫[微軟.Azure.Cosmos.表](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table)將取代.NET框架庫[微軟.Azure.CosmosDB.表](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.Table)。

### <a name="200-series"></a>2.0.0 系列
| 版本 | 發行日期 | 停用日期 |
| --- | --- | --- |
| [2.0.0-preview](#2.0.0-preview) |奧客 22， 2019 |--- |

### <a name="100-series"></a>1.0.0 系列
| 版本 | 發行日期 | 停用日期 |
| --- | --- | --- |
| [1.0.5](#1.0.5) |2019 年 9 月 13 日 |--- |
| [1.0.5 預覽版](#1.0.5-preview) |奧客 20， 2019 |--- |
| [1.0.4](#1.0.4) |奧Guest 12， 2019 |--- |
| [1.0.4 預覽](#1.0.4-preview) |2019 年 7 月 26 日 |--- |
| 1.0.2 預覽 |2019年5月2日 |--- |
| [1.0.1](#1.0.1) |2019年4月19日 |--- |
| [1.0.0](#1.0.0) |2019 年 3 月 13 日 |--- |
| [0.11.0 預覽](#0.11.0-preview) |2019 年 3 月 5 日 |--- |
| [0.10.1 - 預覽](#0.10.1-preview) |2019 年 1 月 22 日 |--- |
| [0.10.0-預覽](#0.10.0-preview) |2018 年 12 月 18 日 |--- |
| [0.9.1 預覽](#0.9.1-preview) |2018 年 10 月 18 日 |--- |


## <a name="faq"></a>常見問題集

[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>另請參閱
若要深入了解 Azure Cosmos DB 資料表 API，請參閱 [Azure Cosmos DB 資料表 API 的簡介](table-introduction.md)。
