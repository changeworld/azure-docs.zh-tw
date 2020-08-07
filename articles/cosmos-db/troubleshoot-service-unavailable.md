---
title: 疑難排解 Azure Cosmos DB 服務無法使用的例外狀況
description: 如何診斷和修正 Cosmos DB 服務無法使用的例外狀況
author: j82w
ms.service: cosmos-db
ms.date: 08/06/2020
ms.author: jawilley
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: b9c24ee3b94be86ccf9d27b928c42fc194800a3b
ms.sourcegitcommit: 25bb515efe62bfb8a8377293b56c3163f46122bf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/07/2020
ms.locfileid: "87987370"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-service-unavailable"></a>診斷和疑難排解 Azure Cosmos DB 服務無法使用
SDK 無法連接到 Azure Cosmos DB 服務。

## <a name="troubleshooting-steps"></a>疑難排解步驟
下列清單包含「服務無法使用」例外狀況的已知原因和解決方案。

### <a name="1-the-required-ports-are-being-blocked"></a>1. 正在封鎖所需的埠
確認所有必要的[埠](performance-tips-dotnet-sdk-v3-sql.md#networking)皆已啟用。

### <a name="2-client-side-transient-connectivity-issues"></a>2. 用戶端暫時性連線問題
當發生暫時性連線問題而造成超時時，服務無法使用的例外狀況介面可能會呈現。 此案例的相關堆疊追蹤通常會包含，例如 `TransportException` ：

```C#
TransportException: A client transport error occurred: The request timed out while waiting for a server response. 
(Time: xxx, activity ID: xxx, error code: ReceiveTimeout [0x0010], base error: HRESULT 0x80131500
```

請遵循[要求超時疑難排解](troubleshoot-dot-net-sdk-request-timeout.md#troubleshooting-steps)來解決此問題。

### <a name="3-service-outage"></a>3. 服務中斷
檢查[Azure 狀態](https://status.azure.com/status)，以查看是否有持續發生的問題。


## <a name="next-steps"></a>後續步驟
* 針對使用 Azure Cosmos DB .NET SDK 時[的問題進行診斷和疑難排解](troubleshoot-dot-net-sdk.md)
* 瞭解[.Net V3](performance-tips-dotnet-sdk-v3-sql.md)和[.net V2](performance-tips.md)的效能指導方針