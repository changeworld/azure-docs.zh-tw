---
title: 針對 Azure Cosmos DB 服務無法使用的例外狀況進行疑難排解
description: 瞭解如何診斷及修正 Azure Cosmos DB 服務無法使用的例外狀況。
author: j82w
ms.service: cosmos-db
ms.date: 08/06/2020
ms.author: jawilley
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: 749592b778612c6903c9c15e336de3fb00978199
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "88870862"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-service-unavailable-exceptions"></a>診斷和疑難排解 Azure Cosmos DB 服務無法使用的例外狀況
SDK 無法連接至 Azure Cosmos DB。

## <a name="troubleshooting-steps"></a>疑難排解步驟
下列清單包含服務無法使用例外狀況的已知原因和解決方案。

### <a name="the-required-ports-are-being-blocked"></a>需要的埠遭到封鎖
確認已啟用所有 [必要的埠](performance-tips-dotnet-sdk-v3-sql.md#networking) 。

### <a name="client-side-transient-connectivity-issues"></a>用戶端暫時性連線問題
服務無法使用例外狀況可能會在發生暫時性連線問題而造成超時時出現。 一般而言，與此案例相關的堆疊追蹤將包含 `TransportException` 錯誤。 例如：

```C#
TransportException: A client transport error occurred: The request timed out while waiting for a server response. 
(Time: xxx, activity ID: xxx, error code: ReceiveTimeout [0x0010], base error: HRESULT 0x80131500
```

遵循 [要求超時疑難排解步驟](troubleshoot-dot-net-sdk-request-timeout.md#troubleshooting-steps) 來解決此問題。

### <a name="service-outage"></a>服務中斷
請檢查 [Azure 狀態](https://status.azure.com/status) 以查看是否有進行中的問題。


## <a name="next-steps"></a>後續步驟
* 當您使用 Azure Cosmos DB .NET SDK 時[，診斷和疑難排解](troubleshoot-dot-net-sdk.md)問題。
* 瞭解 [.net v3](performance-tips-dotnet-sdk-v3-sql.md) 和 [.net v2](performance-tips.md)的效能指導方針。