---
title: 針對 Azure Cosmos DB 服務要求超時例外狀況進行疑難排解
description: 瞭解如何診斷和修正 Azure Cosmos DB 服務要求超時例外狀況。
author: j82w
ms.service: cosmos-db
ms.date: 07/13/2020
ms.author: jawilley
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: 76a1558534728613dcdedc78b64a0366f2bd643d
ms.sourcegitcommit: 927dd0e3d44d48b413b446384214f4661f33db04
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/26/2020
ms.locfileid: "88871066"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-request-timeout-exceptions"></a>診斷和疑難排解 Azure Cosmos DB 要求超時例外狀況
Azure Cosmos DB 傳回 HTTP 408 要求超時。

## <a name="troubleshooting-steps"></a>疑難排解步驟
下列清單包含要求超時例外狀況的已知原因和解決方案。

### <a name="check-the-sla"></a>檢查 SLA
檢查 [Azure Cosmos DB 監視](monitor-cosmos-db.md) ，以查看408例外狀況的數目是否違反 Azure Cosmos DB SLA。

#### <a name="solution-1-it-didnt-violate-the-azure-cosmos-db-sla"></a>解決方案1：不違反 Azure Cosmos DB SLA
應用程式應該會處理此案例，並在這些暫時性失敗時重試。

#### <a name="solution-2-it-did-violate-the-azure-cosmos-db-sla"></a>解決方案2：它違反了 Azure Cosmos DB SLA
請聯絡 [Azure 支援](https://aka.ms/azure-support)。
 
### <a name="hot-partition-key"></a>熱分割區索引鍵
Azure Cosmos DB 會將整體布建的輸送量平均分散到實體分割區。 當有熱分割區時，實體分割區上的一或多個邏輯分割區索引鍵會使用每秒的所有實體資料分割要求單位 (RU/s) 。 同時，其他實體資料分割上的 RU/秒也不會使用。 作為徵兆，取用的 RU/秒總數將小於資料庫或容器上的整體布建 RU/秒。 您仍會看到節流 (429 對經常性邏輯分割區索引鍵的要求) 。 使用 [正規化的 RU 耗用量](monitor-normalized-request-units.md) 計量來查看工作負載是否遇到熱資料分割。 

#### <a name="solution"></a>解決方案：
選擇可平均分配要求磁片區和儲存體的良好分割區索引鍵。 瞭解如何 [變更分割](https://devblogs.microsoft.com/cosmosdb/how-to-change-your-partition-key/)區索引鍵。

## <a name="next-steps"></a>後續步驟
* 當您使用 Azure Cosmos DB .NET SDK 時[，診斷和疑難排解](troubleshoot-dot-net-sdk.md)問題。
* 瞭解 [.net v3](performance-tips-dotnet-sdk-v3-sql.md) 和 [.net v2](performance-tips.md)的效能指導方針。