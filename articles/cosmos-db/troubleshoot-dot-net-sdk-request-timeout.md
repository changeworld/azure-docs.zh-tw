---
title: 針對 Azure Cosmos DB HTTP 408 或 .NET SDK 的要求超時問題進行疑難排解
description: 如何診斷和修正 .NET SDK 要求超時例外狀況
author: j82w
ms.service: cosmos-db
ms.date: 07/13/2020
ms.author: jawilley
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: 29b0c6237ae04ea5da9ec496498fc7c20890b173
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87294160"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-net-sdk-request-timeout"></a>診斷和疑難排解 Azure Cosmos DB .NET SDK 要求超時
如果 SDK 在發生超時限制之前無法完成要求，就會發生 HTTP 408 錯誤。

## <a name="troubleshooting-steps"></a>疑難排解步驟
下列清單包含要求超時例外狀況的已知原因和解決方案。

### <a name="1-high-cpu-utilization-most-common-case"></a>1. 高 CPU 使用率（最常見的案例）
為了達到最佳延遲，建議 CPU 使用量大約為40%。 建議使用10秒作為間隔，以監視最大（非平均） CPU 使用率。 跨分割區查詢，CPU 尖峰較常見，它可能會對單一查詢執行多個連接。

#### <a name="solution"></a>解決方案：
使用 SDK 的用戶端應用程式應該相應增加/相應放大。

### <a name="2-socket--port-availability-might-be-low"></a>2. 通訊端/埠可用性可能很低
在 Azure 中執行時，使用 .NET SDK 的用戶端可以達到 Azure SNAT （PAT）埠耗盡的狀態。

#### <a name="solution-1"></a>解決方案 1：
請遵循[SNAT 埠耗盡指南](troubleshoot-dot-net-sdk.md#snat)。

#### <a name="solution-2"></a>解決方案 2：
如果您使用 HTTP Proxy，請確定它可以支援在 SDK `ConnectionPolicy` 中設定的連線數目。
否則就會遇到連線問題。

### <a name="3-creating-multiple-client-instances"></a>3. 建立多個用戶端實例
建立多個用戶端實例可能會導致連線爭用和超時問題。

#### <a name="solution"></a>解決方案：
請遵循[效能秘訣](performance-tips-dotnet-sdk-v3-sql.md#sdk-usage)，並在整個進程中使用單一 CosmosClient 實例。

### <a name="4-hot-partition-key"></a>4. 熱分割區索引鍵
Azure Cosmos DB 會將整體布建的輸送量平均分散到實體分割區。 當有熱資料分割時，實體分割區上的一或多個邏輯分割區索引鍵會耗用所有實體分割區的 RU/秒，而其他實體磁碟分割上的 RU/秒則不會使用。 就徵兆而言，取用的 RU/秒總數會小於資料庫或容器上的整體布建 RU/秒，但您仍會在對熱邏輯分割區索引鍵的要求上看到節流（429s）。 使用 [[正規化 RU 耗用量](monitor-normalized-request-units.md)] 計量，查看工作負載是否遇到熱資料分割。 

#### <a name="solution"></a>解決方案：
選擇一個良好的資料分割索引鍵，以平均分配要求磁片區和儲存體。 瞭解如何[變更您的分割](https://devblogs.microsoft.com/cosmosdb/how-to-change-your-partition-key/)區索引鍵。

### <a name="5-high-degree-of-concurrency"></a>5. 高程度的平行存取
應用程式正在進行高階並行處理，這可能會導致通道上的爭用

#### <a name="solution"></a>解決方案：
使用 SDK 的用戶端應用程式應該相應增加/相應放大。

### <a name="6-large-requests-andor-responses"></a>6. 大型要求和/或回應
大型要求或回應可能會導致通道上的標頭封鎖，以及惡化爭用，即使是相對較低的並行程度也一樣。

#### <a name="solution"></a>解決方案：
使用 SDK 的用戶端應用程式應該相應增加/相應放大。

### <a name="7-failure-rate-is-within-cosmos-db-sla"></a>7. 失敗率在 Cosmos DB SLA 內
應用程式應該能夠處理暫時性失敗，並在必要時重試。 408例外狀況不會重試，因為在建立路徑上，無法得知服務是否已建立專案，或者如果沒有的話。 針對 create 再次傳送相同的專案，將會造成衝突例外狀況。 使用者應用程式商務邏輯可能會有處理衝突的自訂邏輯，這會中斷現有專案與建立重試衝突的不明確。

### <a name="8-failure-rate-is-violating-the-cosmos-db-sla"></a>8. 失敗率違反 Cosmos DB SLA
請洽詢 Azure 支援。

## <a name="next-steps"></a>後續步驟
* 針對使用 Azure Cosmos DB .NET SDK 時[的問題進行診斷和疑難排解](troubleshoot-dot-net-sdk.md)
* 瞭解[.Net V3](performance-tips-dotnet-sdk-v3-sql.md)和[.net V2](performance-tips.md)的效能指導方針