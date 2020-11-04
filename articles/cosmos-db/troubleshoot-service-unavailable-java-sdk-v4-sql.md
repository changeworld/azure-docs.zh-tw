---
title: 使用 JAVA v4 SDK 進行 Azure Cosmos DB 服務無法使用例外狀況的疑難排解
description: 瞭解如何使用 JAVA v4 SDK 診斷和修正 Azure Cosmos DB 服務無法使用的例外狀況。
author: kushagrathapar
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.date: 10/28/2020
ms.author: kuthapar
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: 03c899307b00010ca87b279ed720b92946d3673c
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/04/2020
ms.locfileid: "93340053"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-java-v4-sdk-service-unavailable-exceptions"></a>Azure Cosmos DB JAVA v4 SDK 服務無法使用例外狀況進行診斷和疑難排解
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]
JAVA v4 SDK 無法連接至 Azure Cosmos DB。

## <a name="troubleshooting-steps"></a>疑難排解步驟
下列清單包含服務無法使用例外狀況的已知原因和解決方案。

### <a name="the-required-ports-are-being-blocked"></a>需要的埠遭到封鎖
確認已啟用所有 [必要的埠](sql-sdk-connection-modes.md#service-port-ranges) 。

### <a name="client-side-transient-connectivity-issues"></a>用戶端暫時性連線問題
服務無法使用例外狀況可能會在發生暫時性連線問題而造成超時時出現。 一般而言，與此案例相關的堆疊追蹤將包含包含 `ServiceUnavailableException` 診斷詳細資料的錯誤。 例如：

```java
Exception in thread "main" ServiceUnavailableException{userAgent=azsdk-java-cosmos/4.6.0 Linux/4.15.0-1096-azure JRE/11.0.8, error=null, resourceAddress='null', requestUri='null', statusCode=503, message=Service is currently unavailable, please retry after a while. If this problem persists please contact support.: Message: "" {"diagnostics"}
```

遵循 [要求超時疑難排解步驟](troubleshoot-request-timeout-java-sdk-v4-sql.md#troubleshooting-steps) 來解決此問題。

### <a name="service-outage"></a>服務中斷
請檢查 [Azure 狀態](https://status.azure.com/status) 以查看是否有進行中的問題。


## <a name="next-steps"></a>後續步驟
* 當您使用 Azure Cosmos DB JAVA v4 SDK 時[，診斷和疑難排解](troubleshoot-java-sdk-v4-sql.md)問題。
* 瞭解 [JAVA V4 SDK](performance-tips-java-sdk-v4-sql.md)的效能指導方針。