---
title: 針對 .NET SDK 的 Azure Cosmos DB HTTP 408 或要求超時問題進行疑難排解
description: 瞭解如何診斷及修正 .NET SDK 要求超時例外狀況。
author: j82w
ms.service: cosmos-db
ms.date: 08/06/2020
ms.author: jawilley
ms.topic: troubleshooting
ms.reviewer: sngun
ms.custom: devx-track-dotnet
ms.openlocfilehash: 735c098fcf6fed76019850a1cb58d9eb6c485b7a
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93101012"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-net-sdk-request-timeout-exceptions"></a>診斷和疑難排解 Azure Cosmos DB .NET SDK 要求超時例外狀況
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

如果 SDK 無法在發生超時限制之前完成要求，就會發生 HTTP 408 錯誤。

## <a name="customize-the-timeout-on-the-azure-cosmos-db-net-sdk"></a>自訂 Azure Cosmos DB .NET SDK 上的超時

SDK 有兩種不同的替代方式來控制超時，每個都有不同的範圍。

### <a name="requesttimeout"></a>RequestTimeout

`CosmosClientOptions.RequestTimeout` (或 `ConnectionPolicy.RequestTimeout` for SDK v2) 設定可讓您設定影響每個個別網路要求的超時時間。 使用者啟動的作業可以跨越多個網路要求 (例如，可能會有節流) 。 這項設定會在重試時針對每個網路要求套用。 此超時不是端對端作業要求超時。

### <a name="cancellationtoken"></a>CancellationToken

SDK 中的所有非同步作業都有選擇性的 CancellationToken 參數。 整個作業都會使用這個 [CancellationToken](/dotnet/standard/threading/how-to-listen-for-cancellation-requests-by-polling) 參數，跨所有網路要求。 在網路要求之間，如果相關的權杖已過期，則可能會檢查取消權杖，並取消作業。 解除標記應該用來定義作業範圍的大約預期超時。

> [!NOTE]
> `CancellationToken`參數是一種機制，當程式庫[不會造成無效狀態](https://devblogs.microsoft.com/premier-developer/recommended-patterns-for-cancellationtoken/)時，它會檢查取消。 當取消中定義的時間已啟動時，作業可能不會完全取消。 相反地，在這段時間之後，它會在安全時取消。

## <a name="troubleshooting-steps"></a>疑難排解步驟
下列清單包含要求超時例外狀況的已知原因和解決方案。

### <a name="high-cpu-utilization"></a>高 CPU 使用率
高 CPU 使用率是最常見的情況。 為了達到最佳延遲，CPU 使用量大約是40%。 使用10秒作為間隔，以監視 (不是平均) CPU 使用率。 CPU 尖峰對於跨資料分割查詢較為常見，因為它可能會針對單一查詢進行多個連接。

#### <a name="solution"></a>解決方案：
使用 SDK 的用戶端應用程式應該相應增加或相應放大。

### <a name="socket-or-port-availability-might-be-low"></a>通訊端或埠可用性可能很低
在 Azure 中執行時，使用 .NET SDK 的用戶端可以叫用 Azure SNAT (PAT) 埠耗盡。

#### <a name="solution-1"></a>解決方案 1：
如果您正在 Azure Vm 上執行，請遵循 [SNAT 埠耗盡指南](troubleshoot-dot-net-sdk.md#snat)。

#### <a name="solution-2"></a>解決方案 2：
如果您是在 Azure App Service 上執行，請遵循 [連接錯誤疑難排解指南](../app-service/troubleshoot-intermittent-outbound-connection-errors.md#cause) 並 [使用 App Service 診斷](https://azure.github.io/AppService/2018/03/01/Deep-Dive-into-TCP-Connections-in-App-Service-Diagnostics.html)。

#### <a name="solution-3"></a>解決方案3：
如果您是在 Azure Functions 上執行，請確認您已遵循維護所有相關服務的單一或靜態用戶端的 [Azure Functions 建議](../azure-functions/manage-connections.md#static-clients) (包括 Azure Cosmos DB) 。 根據您的函數應用程式裝載的類型和大小，檢查 [服務限制](../azure-functions/functions-scale.md#service-limits) 。

#### <a name="solution-4"></a>解決方案4：
如果您使用 HTTP Proxy，請確定它可以支援在 SDK `ConnectionPolicy` 中設定的連線數目。 否則，您會面對連接問題。

### <a name="create-multiple-client-instances"></a>建立多個用戶端實例
建立多個用戶端實例可能會導致連接爭用和超時問題。

#### <a name="solution"></a>解決方案：
遵循 [效能提示](performance-tips-dotnet-sdk-v3-sql.md#sdk-usage)，並在整個進程中使用單一 CosmosClient 實例。

### <a name="hot-partition-key"></a>熱分割區索引鍵
Azure Cosmos DB 會將整體布建的輸送量平均分散到實體分割區。 當有熱分割區時，實體分割區上的一或多個邏輯分割區索引鍵會使用每秒的所有實體資料分割要求單位 (RU/s) 。 同時，其他實體資料分割上的 RU/秒也不會使用。 作為徵兆，取用的 RU/秒總數將小於資料庫或容器上的整體布建 RU/秒，但您仍會看到節流 (429 對經常性邏輯分割區索引鍵的要求) 。 使用 [正規化的 RU 耗用量](monitor-normalized-request-units.md) 計量來查看工作負載是否遇到熱資料分割。 

#### <a name="solution"></a>解決方案：
選擇可平均分配要求磁片區和儲存體的良好分割區索引鍵。 瞭解如何 [變更分割](https://devblogs.microsoft.com/cosmosdb/how-to-change-your-partition-key/)區索引鍵。

### <a name="high-degree-of-concurrency"></a>高程度的平行存取
應用程式正在執行高層級的平行存取，這可能會導致通道上的競爭情形。

#### <a name="solution"></a>解決方案：
使用 SDK 的用戶端應用程式應該相應增加或相應放大。

### <a name="large-requests-or-responses"></a>大型要求或回應
大型要求或回應可能會導致通道和加劇競爭的標頭封鎖，即使是相對較低的並行程度也是一樣。

#### <a name="solution"></a>解決方案：
使用 SDK 的用戶端應用程式應該相應增加或相應放大。

### <a name="failure-rate-is-within-the-azure-cosmos-db-sla"></a>失敗率在 Azure Cosmos DB SLA 中
應用程式應該能夠處理暫時性失敗，並在必要時重試。 任何408例外狀況都不會重試，因為在建立路徑上，無法得知服務是否已建立該專案。 再次傳送相同的專案以供建立時，將會造成衝突例外狀況。 使用者應用程式商務邏輯可能會有自訂邏輯來處理衝突，這會中斷現有專案與建立重試衝突的混淆。

### <a name="failure-rate-violates-the-azure-cosmos-db-sla"></a>失敗率違反 Azure Cosmos DB SLA
請聯絡 [Azure 支援](https://aka.ms/azure-support)。

## <a name="next-steps"></a>下一步
* 當您使用 Azure Cosmos DB .NET SDK 時[，診斷和疑難排解](troubleshoot-dot-net-sdk.md)問題。
* 瞭解 [.net v3](performance-tips-dotnet-sdk-v3-sql.md) 和 [.net v2](performance-tips.md)的效能指導方針。