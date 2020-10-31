---
title: 針對 JAVA v4 SDK Azure Cosmos DB HTTP 408 或要求超時問題進行疑難排解
description: 瞭解如何使用 JAVA v4 SDK 診斷和修正 JAVA SDK 要求超時例外狀況。
author: kushagrathapar
ms.service: cosmos-db
ms.date: 10/28/2020
ms.author: kuthapar
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: 45452109582be40f007ae57a00c2a151f216bdb8
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93102889"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-java-v4-sdk-request-timeout-exceptions"></a>Azure Cosmos DB JAVA v4 SDK 要求超時例外狀況進行診斷和疑難排解
如果 SDK 無法在發生超時限制之前完成要求，就會發生 HTTP 408 錯誤。

## <a name="troubleshooting-steps"></a>疑難排解步驟
下列清單包含要求超時例外狀況的已知原因和解決方案。

### <a name="high-cpu-utilization"></a>高 CPU 使用率
高 CPU 使用率是最常見的情況。 為了達到最佳延遲，CPU 使用量大約是40%。 使用10秒作為間隔，以監視 (不是平均) CPU 使用率。 CPU 尖峰對於跨資料分割查詢較為常見，因為它可能會針對單一查詢進行多個連接。

#### <a name="solution"></a>解決方案：
使用 SDK 的用戶端應用程式應該相應增加或相應放大。

### <a name="connection-throttling"></a>連線節流
連線節流的發生原因可能是因為主機電腦上的連線限制或 Azure SNAT (PAT) 連接埠耗盡。

### <a name="connection-limit-on-a-host-machine"></a>主機電腦上的連線限制
某些 Linux 系統 (例如 Red Hat) 具有開啟檔案的總數上限。 Linux 中的通訊端會實作為檔案，因此，這個數字也會限制連線總數。 執行下列命令。

```bash
ulimit -a
```

#### <a name="solution"></a>解決方案：
最大允許的開啟檔案數目（識別為「nofile」）必須至少為10000或以上。 如需詳細資訊，請參閱 Azure Cosmos DB Java SDK v4 [效能秘訣](performance-tips-java-sdk-v4-sql.md)。

### <a name="socket-or-port-availability-might-be-low"></a>通訊端或埠可用性可能很低
在 Azure 中執行時，使用 JAVA SDK 的用戶端可以達到 Azure SNAT (PAT) 埠耗盡。

#### <a name="solution-1"></a>解決方案 1：
如果您正在 Azure Vm 上執行，請遵循 [SNAT 埠耗盡指南](troubleshoot-java-sdk-v4-sql.md#snat)。

#### <a name="solution-2"></a>解決方案 2：
如果您是在 Azure App Service 上執行，請遵循 [連接錯誤疑難排解指南](../app-service/troubleshoot-intermittent-outbound-connection-errors.md#cause) 並 [使用 App Service 診斷](https://azure.github.io/AppService/2018/03/01/Deep-Dive-into-TCP-Connections-in-App-Service-Diagnostics.html)。

#### <a name="solution-3"></a>解決方案3：
如果您是在 Azure Functions 上執行，請確認您已遵循維護所有相關服務的單一或靜態用戶端的 [Azure Functions 建議](../azure-functions/manage-connections.md#static-clients) (包括 Azure Cosmos DB) 。 根據您的函數應用程式裝載的類型和大小，檢查 [服務限制](../azure-functions/functions-scale.md#service-limits) 。

#### <a name="solution-4"></a>解決方案4：
如果您使用 HTTP Proxy，請確定它可以支援在 SDK `GatewayConnectionConfig` 中設定的連線數目。 否則，您會面對連接問題。

### <a name="create-multiple-client-instances"></a>建立多個用戶端實例
建立多個用戶端實例可能會導致連接爭用和超時問題。

#### <a name="solution-1"></a>解決方案 1：
遵循 [效能提示](performance-tips-java-sdk-v4-sql.md#sdk-usage)，並在整個應用程式中使用單一 CosmosClient 實例。

#### <a name="solution-2"></a>解決方案 2：
如果應用程式中不能有 singleton CosmosClient，建議您在 CosmosClient 中透過此 API，使用跨多個 Cosmos 用戶端的連線共用 `connectionSharingAcrossClientsEnabled(true)` 。 當您在與多個 Cosmos 帳戶互動的相同 JVM 中有多個 Cosmos 用戶端實例時，啟用這項功能可讓您在 Cosmos 用戶端的實例之間有可能在直接模式中共用。 請注意，設定此選項時，將會針對所有其他用戶端實例使用第一個具現化用戶端的連線設定 (例如，socket timeout config、idle timeout config) 。

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
* 當您使用 Azure Cosmos DB JAVA v4 SDK 時[，診斷和疑難排解](troubleshoot-java-sdk-v4-sql.md)問題。
* 瞭解 [JAVA v4](performance-tips-java-sdk-v4-sql.md)的效能指導方針。
