---
title: 針對 Azure Cosmos DB 未授權例外狀況進行疑難排解
description: 瞭解如何診斷及修正未授權的例外狀況。
author: j82w
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.date: 07/13/2020
ms.author: jawilley
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: dce956b932274a44e98a28b4c6567a5e98771613
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/04/2020
ms.locfileid: "93340000"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-unauthorized-exceptions"></a>診斷和疑難排解 Azure Cosmos DB 未授權的例外狀況
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

HTTP 401：在 HTTP 要求中找到的 MAC 簽名碼與計算的簽章不同。
如果您收到401錯誤訊息「在 HTTP 要求中找到的 MAC 簽章與計算的簽章不同」，則可能是下列案例所造成。

針對較舊的 Sdk，例外狀況可能會顯示為不正確 JSON 例外狀況，而不是正確的401未授權例外狀況。 較新的 Sdk 會適當地處理此案例，並提供有效的錯誤訊息。

## <a name="troubleshooting-steps"></a>疑難排解步驟
下列清單包含未經授權例外狀況的已知原因和解決方案。

### <a name="the-key-wasnt-properly-rotated-is-the-most-common-scenario"></a>金鑰未正確輪替是最常見的案例
在金鑰輪替之後不久就會看到 401 MAC 簽章，而且最終會在沒有任何變更的情況下停止。 

#### <a name="solution"></a>解決方案：
金鑰已輪替，且未遵循 [最佳作法](secure-access-to-data.md#key-rotation)。 視 Azure Cosmos DB 帳戶大小而定，Azure Cosmos DB 帳戶金鑰輪替可能需要幾秒鐘的時間才能完成。

### <a name="the-key-is-misconfigured"></a>機碼設定不正確 
401 MAC 簽章問題將會一致，並會針對所有使用該金鑰的呼叫進行。

#### <a name="solution"></a>解決方案：
金鑰在應用程式上的設定不正確，而且使用了錯誤的帳戶金鑰，或未複製整個金鑰。

### <a name="the-application-is-using-the-read-only-keys-for-write-operations"></a>應用程式使用寫入作業的唯讀金鑰
只有在建立或取代等寫入作業時，才會發生 401 MAC 簽章問題，但是讀取要求成功。

#### <a name="solution"></a>解決方案：
切換應用程式以使用讀取/寫入金鑰，以允許作業順利完成。

### <a name="race-condition-with-create-container"></a>具有 create container 的競爭條件
建立容器之後不久就會出現 401 MAC 簽章問題。 只有在容器建立完成後，才會發生此問題。

#### <a name="solution"></a>解決方案：
建立容器時有競爭情形。 應用程式實例正在嘗試在容器建立完成之前存取容器。 此競爭情形最常見的案例是，如果應用程式正在執行，且容器已刪除，並以相同的名稱重新建立。 SDK 會嘗試使用新的容器，但建立的容器仍在進行中，因此沒有金鑰。

## <a name="next-steps"></a>後續步驟
* 當您使用 Azure Cosmos DB .NET SDK 時[，診斷和疑難排解](troubleshoot-dot-net-sdk.md)問題。
* 瞭解 [.net v3](performance-tips-dotnet-sdk-v3-sql.md) 和 [.net v2](performance-tips.md)的效能指導方針。