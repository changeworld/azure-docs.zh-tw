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
ms.custom: devx-track-dotnet
ms.openlocfilehash: eee59d906da46e315ede52796bd89c7827833ede
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93095725"
---
# <a name="azure-cosmos-db-table-net-standard-api-download-and-release-notes"></a>Azure Cosmos DB 資料表 .NET Standard API：下載和版本資訊
[!INCLUDE[appliesto-table-api](includes/appliesto-table-api.md)]
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

## <a name="release-notes-for-200-series"></a>2.0.0 系列的版本資訊
2.0.0 系列採用 [Cosmos](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/)的相依性，並將效能改進和命名空間合併到 Cosmos DB 端點。

### <a name="200-preview"></a><a name="2.0.0-preview"></a>2.0.0-preview
* 2.0.0 資料表 SDK 的初始預覽，其相依于 [Cosmos](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/)，其效能改進和命名空間匯總可 Cosmos DB 端點。 公用 API 維持不變。

## <a name="release-notes-for-100-series"></a>1.0.0 系列的版本資訊
1.0.0 系列會相依于[Microsoft.Azure.DocumentDB。](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.Core/)

### <a name="108"></a><a name="1.0.8"></a>1.0.8
* 新增支援以設定 TTL 屬性（如果它是 cosmosdb 端點） 
* 在超時和工作取消的例外狀況時接受重試原則
* 修正 asp .net 應用程式中出現的間歇性工作已取消例外狀況
* 從次要端點僅限位置模式修正 azure 資料表儲存體取出
* 將相依性 `Microsoft.Azure.DocumentDB.Core` 版本更新為2.11.2，以修正間歇的 null 參考例外狀況
* `Odata.Core`將相依性版本更新為7.6.4，以修正與 azure shell 的相容性衝突

### <a name="107"></a><a name="1.0.7"></a>1.0.7
* 將資料表 SDK 預設追蹤層級設定為 System.diagnostics.sourcelevels>，以改善效能，您可以選擇透過 app.config

### <a name="105"></a><a name="1.0.5"></a>1.0.5
* 在 TableClientConfiguration 下引進新的設定，以使用 Rest 執行程式來與 Cosmos DB 通訊資料表 API

### <a name="105-preview"></a><a name="1.0.5-preview"></a>1.0.5-預覽
* 錯誤修正

### <a name="104"></a><a name="1.0.4"></a>1.0.4
* 錯誤修正
* 提供 RestExecutorConfiguration 的 HttpClientTimeout 選項。

### <a name="104-preview"></a><a name="1.0.4-preview"></a>1.0.4-預覽
* 錯誤修正
* 提供 RestExecutorConfiguration 的 HttpClientTimeout 選項。

### <a name="101"></a><a name="1.0.1"></a>1.0.1
* 錯誤修正

### <a name="100"></a><a name="1.0.0"></a>1.0.0
* 公開上市版本

### <a name="0110-preview"></a><a name="0.11.0-preview"></a>0.11.0-預覽
* 變更 CloudTableClient 的設定方式。 在結構中，它現在會採用 TableClientConfiguration 物件。 TableClientConfiguration 提供不同的屬性來設定用戶端行為，視目標端點是否 Cosmos DB 資料表 API 或 Azure 儲存體資料表 API 而定。
* 新增對 >tablequery 的支援，以便在自訂資料行上以排序的順序傳回結果。 只有 Cosmos DB 資料表端點支援這項功能。
* 已新增在各種結果類型上公開 RequestCharges 的支援。 只有 Cosmos DB 資料表端點支援這項功能。

### <a name="0101-preview"></a><a name="0.10.1-preview"></a>0.10.1 - 預覽
* 針對 Azure 儲存體資料表端點新增 SAS 權杖、TablePermissions、ServiceProperties 和 ServiceStats 作業支援。 
   > [!NOTE]
   > 尚未支援之前 Azure 儲存體資料表 SDK 中的某些功能，例如用戶端加密。

### <a name="0100-preview"></a><a name="0.10.0-preview"></a>0.10.0-預覽
* 新支援對 Azure 儲存體資料表端點的核心 CRUD、批次及查詢作業。 
   > [!NOTE]
   > 尚未支援之前 Azure 儲存體資料表 SDK 中的某些功能，例如用戶端加密。

### <a name="091-preview"></a><a name="0.9.1-preview"></a>0.9.1-預覽
* Azure Cosmos DB 資料表 .NET Standard SDK 是一個跨平台 .NET 程式庫，可提供有效率地存取 Cosmos DB 上資料表資料模型的權限。 這個初始版本支援一組完整的資料表和實體 CRUD + 查詢功能，其中具有類似[適用於 .NET Framework 的 Cosmos DB 資料表 SDK](table-sdk-dotnet.md) 的 API。 
   > [!NOTE]
   >  0.9.1-預覽版本中尚未支援 Azure 儲存體資料表端點。

## <a name="release-and-retirement-dates"></a>發行和停用日期
Microsoft 至少會在停用 SDK 的 **12 個月** 之前提供通知，以供順利轉換至較新/支援的版本。

此跨平臺 .NET Standard 程式庫 [Cosmos](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table) 將會取代 .NET Framework 程式庫 [CosmosDB. 資料表](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.Table)。

### <a name="200-series"></a>2.0.0 系列
| 版本 | 發行日期 | 停用日期 |
| --- | --- | --- |
| [2.0.0-preview](#2.0.0-preview) |Auguest 22，2019 |--- |

### <a name="100-series"></a>1.0.0 系列
| 版本 | 發行日期 | 停用日期 |
| --- | --- | --- |
| [1.0.5](#1.0.5) |2019 年 9 月 13 日 |--- |
| [1.0.5-預覽](#1.0.5-preview) |Auguest 20，2019 |--- |
| [1.0.4](#1.0.4) |Auguest 12，2019 |--- |
| [1.0.4-預覽](#1.0.4-preview) |2019 年 7 月 26 日 |--- |
| 1.0.2-preview |5月2日，2019 |--- |
| [1.0.1](#1.0.1) |2019年4月19日 |--- |
| [1.0.0](#1.0.0) |2019 年 3 月 13 日 |--- |
| [0.11.0-預覽](#0.11.0-preview) |2019 年 3 月 5 日 |--- |
| [0.10.1 - 預覽](#0.10.1-preview) |2019 年 1 月 22 日 |--- |
| [0.10.0-預覽](#0.10.0-preview) |2018 年 12 月 18 日 |--- |
| [0.9.1-預覽](#0.9.1-preview) |2018 年 10 月 18 日 |--- |


## <a name="faq"></a>常見問題集

[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>另請參閱
若要深入了解 Azure Cosmos DB 資料表 API，請參閱 [Azure Cosmos DB 資料表 API 的簡介](table-introduction.md)。
