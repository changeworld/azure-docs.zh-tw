---
title: '遷移您的應用程式以使用 Azure Cosmos DB .NET SDK 2.0 (Cosmos) '
description: 瞭解如何將您現有的 .NET 應用程式從 v1 SDK 升級為適用于 Core (SQL) API 的 .NET SDK v2。
author: stefArroyo
ms.author: esarroyo
ms.service: cosmos-db
ms.topic: how-to
ms.date: 10/15/2020
ms.openlocfilehash: 88c40452da72ed4ab43d2d7613636136a5b78cfe
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/12/2020
ms.locfileid: "94550061"
---
# <a name="migrate-your-application-to-use-the-azure-cosmos-db-net-sdk-v2"></a>遷移您的應用程式以使用 Azure Cosmos DB .NET SDK v2

> [!IMPORTANT]
> 請務必注意，.NET SDK 的 v3 目前已可供使用，而且您可以在 [這裡](migrate-dotnet-v3.md)找到 v2 到 v3 的遷移計畫。 若要瞭解 Azure Cosmos DB .NET SDK v2，請參閱 [版本](sql-api-sdk-dotnet.md)資訊、 [.net GitHub 存放庫](https://github.com/Azure/azure-cosmos-dotnet-v2)、.Net sdk V2 [效能秘訣](performance-tips.md)和 [疑難排解指南](troubleshoot-dot-net-sdk.md)。
>

本文將重點說明如何將您現有的 v1 .NET 應用程式升級為適用于 Core (SQL) API 的 Azure Cosmos DB .NET SDK v2。 Azure Cosmos DB .NET SDK v2 對應至 `Microsoft.Azure.DocumentDB` 命名空間。 如果您要從下列任何一個 Azure Cosmos DB .NET 平臺遷移應用程式，以使用 v2 SDK，您可以使用本檔所提供的資訊 `Microsoft.Azure.Cosmos` ：

* 適用于 SQL API 的 Azure Cosmos DB .NET Framework v1 SDK
* 適用于 SQL API 的 Azure Cosmos DB .NET Core SDK v1

## <a name="whats-available-in-the-net-v2-sdk"></a>.NET v2 SDK 中提供的功能

V2 SDK 包含許多可用性和效能改進，包括：

* 支援非 Windows 用戶端的 TCP 直接模式
* 多區域寫入支援
* 改善查詢效能
* 支援地理空間/幾何集合和編制索引
* 提高直接/TCP 傳輸診斷的增強功能
* 直接 TCP 傳輸堆疊的更新，以減少已建立的連接數目
* RequestTimeout 減少延遲的改進

大部分的重試邏輯和較低層級的大部分仍維持不變。

## <a name="why-migrate-to-the-net-v2-sdk"></a>為何要遷移至 .NET v2 SDK

除了許多效能改進之外，在最新的 SDK 中所做的新功能投資也不會再移植到較舊的版本。

此外，較舊的 Sdk 將會取代為較新的版本，而 v1 SDK 將會進入 [維護模式](sql-api-sdk-dotnet.md)。 為了獲得最佳的開發經驗，建議您將應用程式遷移至較新版本的 SDK。

## <a name="major-changes-from-v1-sdk-to-v2-sdk"></a>從 v1 SDK 到 v2 SDK 的重大變更

### <a name="direct-mode--tcp"></a>Direct 模式 + TCP

.NET v2 SDK 現在支援直接與閘道模式。 直接模式支援透過 TCP 通訊協定連線，並提供更佳的效能，因為它會直接連接到較少網路躍點的後端複本。

如需詳細資訊，請參閱 [AZURE COSMOS DB SQL SDK 連線模式指南](sql-sdk-connection-modes.md)。

### <a name="session-token-formatting"></a>會話權杖格式化

V2 SDK 不再使用 v1 中使用的 *簡單* 會話權杖格式，而是使用 *向量* 格式化。 使用不同的版本傳遞至用戶端應用程式時，應該轉換格式，因為這些格式不是可互換的。

如需詳細資訊，請參閱 [在 .NET SDK 中轉換會話權杖格式](how-to-convert-session-token.md)。

### <a name="using-the-net-change-feed-processor-sdk"></a>使用 .NET 變更摘要處理器 SDK

.NET 變更摘要處理器程式庫 2.1. x 需要 `Microsoft.Azure.DocumentDB` 2.0 或更新版本。

2.1. x 程式庫具有下列金鑰變更：

* 穩定性和診斷改進
* 改善錯誤和例外狀況的處理
* 分割區租用集合的額外支援
* 用來執行 `ChangeFeedDocument` 介面和類別以進行其他錯誤處理和追蹤的 Advanced 擴充功能
* 已新增使用自訂存放區以保存每個分割區之接續權杖的支援

如需詳細資訊，請參閱變更摘要處理器程式庫 [版本](sql-api-sdk-dotnet-changefeed.md)資訊。

### <a name="using-the-bulk-executor-library"></a>使用大量執行程式程式庫

V2 大量執行程式程式庫目前相依于 Azure Cosmos DB .NET SDK 2.5.1 或更新版本。

如需詳細資訊，請參閱 [Azure Cosmos DB 大量執行程式程式庫總覽](bulk-executor-overview.md) 和 .net 大量執行程式程式庫 [版本](sql-api-sdk-bulk-executor-dot-net.md)資訊。

## <a name="next-steps"></a>後續步驟

* 閱讀有關使用 SQL API v2 Azure Cosmos DB 的 [額外效能秘訣](sql-api-get-started.md) ，以優化您的應用程式以達到最大效能
* 深入瞭解[您可以使用 V2 SDK 進行的](sql-api-dotnet-samples.md)作業