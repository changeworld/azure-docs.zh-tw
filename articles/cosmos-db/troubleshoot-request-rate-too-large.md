---
title: 針對 Azure Cosmos DB 要求率太大的例外狀況進行疑難排解
description: 瞭解如何診斷和修正要求率太大的例外狀況。
author: j82w
ms.service: cosmos-db
ms.date: 07/13/2020
ms.author: jawilley
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: f8b1129c94ecf80efb60a13a0b80b1cc1817ff3f
ms.sourcegitcommit: 927dd0e3d44d48b413b446384214f4661f33db04
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/26/2020
ms.locfileid: "88871083"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-request-rate-too-large-exceptions"></a>針對 Azure Cosmos DB 要求率太大的例外狀況進行診斷和疑難排解
「要求速率太大」訊息或錯誤碼429表示要求正在進行節流。

## <a name="troubleshooting-steps"></a>疑難排解步驟
下一節包含了太多要求的已知原因和解決方案。

### <a name="check-the-metrics"></a>檢查計量
檢查 [Azure Cosmos DB 監視](monitor-cosmos-db.md) ] 以查看429例外狀況的數目。

#### <a name="cause"></a>原因：
耗用的輸送量 (每秒要求單位數) 超過布 [建的輸送量](set-throughput.md)。 SDK 會根據指定的重試原則自動重試要求。 如果您經常遇到此失敗，請考慮增加集合的輸送量。 檢查入口網站的計量，以查看您是否遇到429錯誤。 檢查您的分割區索引鍵，以確保它會產生 [儲存體和要求磁片區的平均分配](partition-data.md)。

#### <a name="solution"></a>解決方案：
1. 使用 [入口網站或 SDK](set-throughput.md) 來增加布建的輸送量。
1. 將資料庫或容器切換為 [自動](provision-throughput-autoscale.md)調整。

## <a name="next-steps"></a>後續步驟
* 當您使用 Azure Cosmos DB .NET SDK 時[，診斷和疑難排解](troubleshoot-dot-net-sdk.md)問題。
* 瞭解 [.net v3](performance-tips-dotnet-sdk-v3-sql.md) 和 [.net v2](performance-tips.md)的效能指導方針。