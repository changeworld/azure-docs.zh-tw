---
title: Azure Cosmos DB 中的分頁
description: 瞭解分頁概念和接續權杖
author: timsander1
ms.author: tisande
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/29/2020
ms.openlocfilehash: 4de3ec79b94969e45553857f1179a1104e090347
ms.sourcegitcommit: b6f3ccaadf2f7eba4254a402e954adf430a90003
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92276105"
---
# <a name="pagination-in-azure-cosmos-db"></a>Azure Cosmos DB 中的分頁

在 Azure Cosmos DB 中，查詢可能會有多個頁面的結果。 本檔說明 Azure Cosmos DB 的查詢引擎用來決定是否要將查詢結果分割成多個頁面的準則。 您可以選擇性地使用接續權杖來管理跨越多個頁面的查詢結果。

## <a name="understanding-query-executions"></a>瞭解查詢執行

有時查詢結果將會分割成多個頁面。 每個頁面的結果都是由個別的查詢執行所產生。 如果在單一執行時無法傳回查詢結果，Azure Cosmos DB 會自動將結果分割成多個頁面。

您可以藉由設定，指定查詢所傳回的最大專案數 `MaxItemCount` 。 `MaxItemCount`是針對每個要求所指定，並告知查詢引擎會傳回該數量的專案或較少的專案。 `MaxItemCount` `-1` 如果您不想限制每個查詢執行的結果數目，您可以將設定為。

此外，還有其他原因是查詢引擎可能需要將查詢結果分割成多個頁面。 它們包括：

- 容器已進行節流處理，而且沒有可用的 ru 可傳回更多查詢結果
- 查詢執行的回應太大
- 查詢執行的時間太長
- 更有效率的方式是讓查詢引擎在其他執行中傳回結果。

每個查詢執行所傳回的專案數一律會小於或等於 `MaxItemCount` 。 不過，其他條件可能會限制查詢可能傳回的結果數目。 如果您多次執行相同的查詢，則頁面數目可能不會是常數。 例如，如果查詢受到節流，每頁的可用結果可能較少，這表示查詢將會有額外的頁面。 在某些情況下，您的查詢也可能會傳回空白頁面的結果。

## <a name="handling-multiple-pages-of-results"></a>處理多個結果頁面

為了確保正確的查詢結果，您應該進行所有頁面的進度。 您應該繼續執行查詢，直到沒有其他頁面為止。

以下是使用多個頁面處理查詢結果的一些範例：

- [.NET SDK](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/Queries/Program.cs#L280)
- [Java SDK](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/documentcrud/sync/DocumentCRUDQuickstart.java#L162-L176)
- [Node.js SDK](https://github.com/Azure/azure-sdk-for-js/blob/83fcc44a23ad771128d6e0f49043656b3d1df990/sdk/cosmosdb/cosmos/samples/IndexManagement.ts#L128-L140)
- [Python SDK](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/examples.py#L89)

## <a name="continuation-tokens"></a>接續權杖

在 .NET SDK 和 JAVA SDK 中，您可以選擇性地使用接續權杖作為查詢進度的書簽。 Azure Cosmos DB 查詢執行在伺服器端為無狀態，而且可以使用接續 token 隨時繼續執行。 Node.js SDK 或 Python SDK 不支援接續權杖。

以下是使用接續權杖的一些範例：

- [.NET SDK](https://github.com/Azure/azure-cosmos-dotnet-v2/blob/master/samples/code-samples/Queries/Program.cs#L699-L734)
- [Java SDK](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L216)

如果查詢傳回接續 token，則會有其他查詢結果。

在 Azure Cosmos DB 的 REST API 中，您可以使用標頭來管理接續權杖 `x-ms-continuation` 。 如同使用 .NET 或 JAVA SDK 進行查詢，如果 `x-ms-continuation` 回應標頭不是空的，則表示查詢具有其他結果。

只要您使用相同的 SDK 版本，接續權杖永遠不會過期。 您可以選擇性地 [限制接續 token 的大小](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.feedoptions.responsecontinuationtokenlimitinkb?view=azure-dotnet&preserve-view=true#Microsoft_Azure_Documents_Client_FeedOptions_ResponseContinuationTokenLimitInKb)。 無論您容器中的資料量或實體分割區數目為何，查詢都會傳回單一的接續 token。

您無法針對具有 [GROUP BY](sql-query-group-by.md) 或 [DISTINCT](sql-query-keywords.md#distinct) 的查詢使用接續 token，因為這些查詢需要儲存相當大量的狀態。 針對使用的查詢 `DISTINCT` ，您可以在加入至查詢時使用接續標記 `ORDER BY` 。

以下是使用接續 token 的查詢範例 `DISTINCT` ：

```sql
SELECT DISTINCT VALUE c.name
FROM c
ORDER BY c.name
```

## <a name="next-steps"></a>後續步驟

- [Azure Cosmos DB 簡介](introduction.md)
- [Azure Cosmos DB .NET 範例](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [ORDER BY 子句](sql-query-order-by.md)