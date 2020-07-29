---
title: 疑難排解 Azure Cosmos DB 服務無法使用的例外狀況
description: 如何診斷和修正 Cosmos DB 服務無法使用的例外狀況
author: j82w
ms.service: cosmos-db
ms.date: 07/13/2020
ms.author: jawilley
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: 12ecec7cf8f406ed53fb5e054fc304bf672cbbb0
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87294105"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-service-unavailable"></a>診斷和疑難排解 Azure Cosmos DB 服務無法使用
SDK 無法連接到 Azure Cosmos DB 服務。

## <a name="troubleshooting-steps"></a>疑難排解步驟
下列清單包含「服務無法使用」例外狀況的已知原因和解決方案。

### <a name="1-the-required-ports-are-not-enabled"></a>1. 未啟用所需的埠。
確認所有必要的[埠](performance-tips-dotnet-sdk-v3-sql.md#networking)皆已啟用。

## <a name="if-an-existing-application-or-service-started-getting-503"></a>如果現有的應用程式或服務已開始取得503

### <a name="1-there-is-an-outage"></a>1. 發生中斷
檢查[Azure 狀態](https://status.azure.com/status)，以查看是否有持續發生的問題。

### <a name="2-snat-port-exhaustion"></a>2. SNAT 埠耗盡
請遵循[SNAT 埠耗盡指南](troubleshoot-dot-net-sdk.md#snat)。

### <a name="3-the-required-ports-are-being-blocked"></a>3. 已封鎖所需的埠
確認所有必要的[埠](performance-tips-dotnet-sdk-v3-sql.md#networking)皆已啟用。

## <a name="next-steps"></a>後續步驟
* 針對使用 Azure Cosmos DB .NET SDK 時[的問題進行診斷和疑難排解](troubleshoot-dot-net-sdk.md)
* 瞭解[.Net V3](performance-tips-dotnet-sdk-v3-sql.md)和[.net V2](performance-tips.md)的效能指導方針