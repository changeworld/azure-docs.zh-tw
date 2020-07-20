---
title: Azure Cosmos DB 資料表 API .NET SDK 和資源
description: 全面了解 Azure Cosmos DB 資料表 API，包括發行日期、停用日期，以及每個版本之間的變更。
author: sakash279
ms.author: akshanka
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: dotnet
ms.topic: reference
ms.date: 08/17/2018
ms.openlocfilehash: 419c3f21c8606bb16112fd18cfe141655fd7b868
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84168143"
---
# <a name="azure-cosmos-db-table-net-api-download-and-release-notes"></a>Azure Cosmos DB 資料表 .NET API：下載和版本資訊

> [!div class="op_single_selector"]
> * [.NET](table-sdk-dotnet.md)
> * [.NET Standard](table-sdk-dotnet-standard.md)
> * [Java](table-sdk-java.md)
> * [Node.js](table-sdk-nodejs.md)
> * [Python](table-sdk-python.md)

|   |   |
|---|---|
|**SDK 下載**|[NuGet](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.Table)|
|**快速入門**|[Azure Cosmos DB：使用 .NET 和資料表 API 建置應用程式 (英文)](create-table-dotnet.md)|
|**教學課程**|[Azure Cosmos DB：使用 .NET 搭配資料表 API 進行開發](tutorial-develop-table-dotnet.md)|
|**目前支援的架構**|[Microsoft .NET Framework 4.5.1](https://www.microsoft.com/en-us/download/details.aspx?id=40779)|

> [!IMPORTANT]
> .NET Framework SDK [Microsoft.Azure.CosmosDB.Table](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.Table) 現為維護模式，且即將被取代。 請升級至新的.NET Standard 程式庫[Microsoft.Azure.Cosmos.Table](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table)，才能繼續取得資料表 API 支援的最新功能。

> 如果您在預覽期間建立了資料表 API 帳戶，請建立[新表格 API 帳戶](create-table-dotnet.md#create-a-database-account)以使用正式推出的資料表 API SDK。
>

## <a name="release-notes"></a>版本資訊

### <a name="212"></a><a name="2.1.2"></a>2.1.2

* Bug 修正

### <a name="210"></a><a name="2.1.0"></a>2.1.0

* Bug 修正

### <a name="200"></a><a name="2.0.0"></a>2.0.0

* 已新增多區域寫入支援
* 已修正 Microsoft.Azure.DocumentDB、Microsoft.OData.Core、Microsoft.OData.Edm、Microsoft.Spatial 上的 NuGet 套件相依性

### <a name="113"></a><a name="1.1.3"></a>1.1.3

* 已修正 Microsoft.Azure.Storage.Common 和 Microsoft.Azure.DocumentDB 上的 NuGet 套件相依性。
* 設定 JsonConvert.DefaultSettings 時將資料表序列化的錯誤修正。

### <a name="111"></a><a name="1.1.1"></a>1.1.1

* 新增格式錯誤的 ETAG 在「直接模式」中的驗證。
* 修正閘道模式中 LINQ 的查詢錯誤 (bug)。
* 同步 API 現在使用 SynchronizationContext 在執行緒集區上執行。

### <a name="110"></a><a name="1.1.0"></a>1.1.0

* 將 TableQueryMaxItemCount、TableQueryEnableScan、TableQueryMaxDegreeOfParallelism 及 TableQueryContinuationTokenLimitInKb 新增至 TableRequestOptions
* Bug 修正

### <a name="100"></a><a name="1.0.0"></a>1.0.0

* 公開上市版本

### <a name="090-preview"></a><a name="0.1.0-preview"></a>0.9.0-preview

* 初始預覽版本

## <a name="release-and-retirement-dates"></a>發行和停用日期

Microsoft 至少會在停用 SDK 的 **12 個月** 之前提供通知，以供順利轉換至較新/支援的版本。

此連結 `Microsoft.Azure.CosmosDB.Table` 庫目前僅適用于 .NET Framework，而且處於維護模式，即將淘汰。 新的特性和功能和優化只會新增至 .NET Standard 程式庫[Cosmos](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table)，因此建議您升級至[Cosmos. table. 資料表](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table)。

Windowsazure.storage 已淘汰[windowsazure.storage-premiumtable](https://www.nuget.org/packages/WindowsAzure.Storage-PremiumTable/0.1.0-preview)預覽套件。 WindowsAzure.Storage-PremiumTable SDK 將於 2018 年 11 月 15 日淘汰，屆時不允許使用已停用 SDK 的要求。 

服務會拒絕使用已停用 SDK 的任何 Azure Cosmos DB 要求。
<br/>

| 版本 | 發行日期 | 停用日期 |
| --- | --- | --- |
| [2.1.2](#2.1.2) |2019 年 9 月 16 日| |
| [2.1.0](#2.1.0) |2019 年 1 月 22 日|2020 年 4 月 1 日 |
| [2.0.0](#2.0.0) |2018 年 9 月 26 日|2020年3月01日 |
| [1.1.3](#1.1.3) |2018 年 7 月 17 日|2019年12月01日 |
| [1.1.1](#1.1.1) |2018 年 3 月 26 日|2019年12月01日 |
| [1.1.0](#1.1.0) |2018 年 2 月 21 日|2019年12月01日 |
| [1.0.0](#1.0.0) |2017 年 11 月 15 日|2019 年 11 月 15 日 |
| 0.9.0-preview |2017 年 11 月 11 日 |2019年11月11日 |

## <a name="troubleshooting"></a>疑難排解

如果發生錯誤 

```
Unable to resolve dependency 'Microsoft.Azure.Storage.Common'. Source(s) used: 'nuget.org', 
'CliFallbackFolder', 'Microsoft Visual Studio Offline Packages', 'Microsoft Azure Service Fabric SDK'`
```

(在嘗試使用 Microsoft.Azure.CosmosDB.Table NuGet 套件時)，有兩個選項可以修正這個問題：

* 使用套件管理器主控台安裝 Microsoft.Azure.CosmosDB.Table 套件和其相依項目。 若要這樣做，請在方案的套件管理器主控台中輸入下列項目。 

    ```powershell
    Install-Package Microsoft.Azure.CosmosDB.Table -IncludePrerelease
    ```

    
* 使用慣用的 NuGet 套件管理工具，先安裝 Microsoft.Azure.Storage.Common NuGet 套件，再安裝 Microsoft.Azure.CosmosDB.Table。

## <a name="faq"></a>常見問題集

[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>另請參閱

若要深入了解 Azure Cosmos DB 資料表 API，請參閱 [Azure Cosmos DB 資料表 API 的簡介](table-introduction.md)。 
