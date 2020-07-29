---
title: 針對 Azure Cosmos DB 未經授權的例外狀況進行疑難排解
description: 如何診斷和修正未經授權的例外狀況
author: j82w
ms.service: cosmos-db
ms.date: 07/13/2020
ms.author: jawilley
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: 872780ff65dabe5a931ae712c0f99b6e4f3e726d
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87294102"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-unauthorized-exception"></a>診斷 Azure Cosmos DB 未經授權的例外狀況並進行疑難排解

HTTP 401：在 HTTP 要求中找到的 MAC 簽章與計算的簽章不同。
如果您收到下列 401 錯誤訊息：「在 HTTP 要求中找到的 MAC 簽章與計算的簽章不同。」 這可能是由下列案例所造成。

較舊的 Sdk，例外狀況可能會顯示為不正確 json 例外狀況，而不是正確的401未授權例外狀況。 較新的 Sdk 會適當地處理這種情況，並提供有效的錯誤訊息。

## <a name="troubleshooting-steps"></a>疑難排解步驟
下列清單包含未授權例外狀況的已知原因和解決方案。

### <a name="1-key-was-not-properly-rotated-is-the-most-common-scenario"></a>1. 未正確輪替金鑰是最常見的案例。
在金鑰輪替後不久就會看到 401 MAC 簽章，而且最終會在沒有任何變更的情況下停止。 

#### <a name="solution"></a>解決方案：
金鑰已輪替，且未遵循[最佳做法](secure-access-to-data.md#key-rotation)。 視 Cosmos DB 帳戶大小而定，Cosmos DB 帳戶金鑰輪替可能需要幾秒鐘到數秒的時間。

### <a name="2-the-key-is-misconfigured"></a>2. 金鑰設定錯誤 
401 MAC 簽章問題會一致，且所有使用該金鑰的呼叫都會發生

#### <a name="solution"></a>解決方案：
應用程式上的金鑰設定錯誤，且帳戶使用了錯誤的金鑰，或未複製整個金鑰。

### <a name="3-the-application-is-using-the-read-only-keys-for-write-operations"></a>3. 應用程式正在使用寫入作業的唯讀金鑰
401只有如 create 或 replace 之類的寫入作業才會發生 MAC 簽章問題，但讀取要求成功。

#### <a name="solution"></a>解決方案：
將應用程式切換為使用讀取/寫入金鑰，以允許作業順利完成。

### <a name="4-race-condition-with-create-container"></a>4. 建立容器的競爭條件
401建立容器之後，很快就會出現 MAC 簽章問題。 這只會在容器建立完成後才會發生。

#### <a name="solution"></a>解決方案：
建立容器時有競爭條件。 應用程式執行個體在完成容器建立之前，會嘗試存取容器。 此競爭條件最常見的案例是應用程式是否正在執行，且容器會以相同的名稱刪除並重新建立。 SDK 會嘗試使用新的容器，但容器仍在建立中，因此沒有金鑰。

## <a name="next-steps"></a>後續步驟
* 針對使用 Azure Cosmos DB .NET SDK 時[的問題進行診斷和疑難排解](troubleshoot-dot-net-sdk.md)
* 瞭解[.Net V3](performance-tips-dotnet-sdk-v3-sql.md)和[.net V2](performance-tips.md)的效能指導方針