---
title: 針對 Azure Cosmos DB 對大型例外狀況的要求速率進行疑難排解
description: 如何診斷和修正對大型例外狀況的要求率
author: j82w
ms.service: cosmos-db
ms.date: 07/13/2020
ms.author: jawilley
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: b20ee2e10648d2d8eb248fc3f98c4fb33805f914
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87294108"
---
# <a name="diagnose-and-troubleshoot-cosmos-db-too-many-requests"></a>針對 Cosmos DB 太多要求進行診斷和疑難排解
「要求速率太大」或錯誤碼429表示您的要求正在進行節流。

## <a name="troubleshooting-steps"></a>疑難排解步驟
下列清單包含太多要求的已知原因和解決方案。

### <a name="1-check-the-metrics"></a>1. 檢查計量
客戶應檢查[Azure Cosmos DB 監視](monitor-cosmos-db.md)，以檢查數位429的例外狀況。

## <a name="cause"></a>原因：
已耗用的輸送量（RU/秒）已超過布[建的輸送量](set-throughput.md)。 SDK 會根據指定的重試原則自動重試要求。 如果您經常遇到此失敗，請考慮增加集合的輸送量。 檢查入口網站的計量，以查看您是否收到429錯誤。 請檢查您的資料分割索引鍵，以確保它會產生[儲存體和要求磁片區的平均分佈](partition-data.md)。

## <a name="solution"></a>解決方案：
1. 使用[入口網站或 SDK](set-throughput.md)來增加布建的輸送量。
2. 將資料庫或容器切換為[自動](provision-throughput-autoscale.md)調整。

## <a name="next-steps"></a>後續步驟
* 針對使用 Azure Cosmos DB .NET SDK 時[的問題進行診斷和疑難排解](troubleshoot-dot-net-sdk.md)
* 瞭解[.Net V3](performance-tips-dotnet-sdk-v3-sql.md)和[.net V2](performance-tips.md)的效能指導方針