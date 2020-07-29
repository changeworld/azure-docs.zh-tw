---
title: 針對 Azure Cosmos DB 服務要求超時例外狀況進行疑難排解
description: 如何診斷和修正 Cosmos DB 服務要求超時例外狀況
author: j82w
ms.service: cosmos-db
ms.date: 07/13/2020
ms.author: jawilley
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: bd8102ee73721e2a56185709d1076e489ecc7607
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87294109"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-request-timeout"></a>診斷 Azure Cosmos DB 要求超時並進行疑難排解
Azure Cosmos DB 傳回 HTTP 408 要求超時

## <a name="troubleshooting-steps"></a>疑難排解步驟
下列清單包含要求超時例外狀況的已知原因和解決方案。

### <a name="1-check-the-sla"></a>1. 檢查 SLA
客戶應檢查[Azure Cosmos DB 監視](monitor-cosmos-db.md)，以檢查數位408的例外狀況是否違反 Cosmos DB SLA。

#### <a name="solution-1-it-did-not-violate-the-cosmos-db-sla"></a>解決方案1：不違反 Cosmos DB SLA
應用程式應處理這種情況，並在這些暫時性失敗時重試。

#### <a name="solution-2-it-did-violate-the-cosmos-db-sla"></a>解決方案2：它違反了 Cosmos DB SLA
聯絡 Azure 支援：https://aka.ms/azure-support
 
### <a name="2-hot-partition-key"></a>2. 熱分割區索引鍵
Azure Cosmos DB 會將整體布建的輸送量平均分散到實體分割區。 當有熱資料分割時，實體分割區上的一或多個邏輯分割區索引鍵會耗用所有實體分割區的 RU/秒，而其他實體磁碟分割上的 RU/秒則不會使用。 就徵兆而言，取用的 RU/秒總數會小於資料庫或容器上的整體布建 RU/秒，但您仍會在對熱邏輯分割區索引鍵的要求上看到節流（429s）。 使用 [[正規化 RU 耗用量](monitor-normalized-request-units.md)] 計量，查看工作負載是否遇到熱資料分割。 

#### <a name="solution"></a>解決方案：
選擇一個良好的資料分割索引鍵，以平均分配要求磁片區和儲存體。 瞭解如何[變更您的分割](https://devblogs.microsoft.com/cosmosdb/how-to-change-your-partition-key/)區索引鍵。

## <a name="next-steps"></a>後續步驟
* 針對使用 Azure Cosmos DB .NET SDK 時[的問題進行診斷和疑難排解](troubleshoot-dot-net-sdk.md)
* 瞭解[.Net V3](performance-tips-dotnet-sdk-v3-sql.md)和[.net V2](performance-tips.md)的效能指導方針