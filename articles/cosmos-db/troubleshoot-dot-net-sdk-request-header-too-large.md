---
title: 針對 Azure Cosmos DB 中的「要求標頭太大」訊息或400不正確的要求進行疑難排解
description: 瞭解如何診斷並修正要求標頭太大的例外狀況。
author: j82w
ms.service: cosmos-db
ms.date: 07/13/2020
ms.author: jawilley
ms.topic: troubleshooting
ms.reviewer: sngun
ms.custom: devx-track-dotnet
ms.openlocfilehash: fff9bc55b324875601a2c4e68835a17c61c3769b
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93079252"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-request-header-too-large-message"></a>診斷和疑難排解 Azure Cosmos DB 「要求標頭太大」訊息
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

「要求標頭太大」訊息擲回，並出現 HTTP 錯誤碼400。 如果要求標頭的大小成長得很大，而超過允許的最大大小，就會發生此錯誤。 建議您使用最新版本的 SDK。 請使用至少3.x 版或2.x 版，因為這些版本會將標頭大小追蹤新增至例外狀況訊息。

## <a name="troubleshooting-steps"></a>疑難排解步驟
如果會話或接續權杖太大，則會發生「要求標頭太大」訊息。 下列各節說明問題的原因，以及其在每個類別中的解決方案。

### <a name="session-token-is-too-large"></a>會話權杖太大

#### <a name="cause"></a>原因：
因為會話權杖太大，所以最有可能發生400錯誤的要求。 如果下列語句為 true，表示會話權杖太大：

* 此錯誤發生在沒有接續 token 的點作業（例如建立、讀取和更新）。
* 例外狀況會在未對應用程式進行任何變更的情況下啟動。 會話權杖會隨著容器中的分割區數目增加而增加。 當資料量增加或輸送量增加時，資料分割數目會增加。

#### <a name="temporary-mitigation"></a>暫時緩和： 
重新開機您的用戶端應用程式，以重設所有會話權杖。 最後，會話權杖會成長回先前造成問題的大小。 若要完全避免這個問題，請使用下一節中的解決方案。

#### <a name="solution"></a>解決方案：
1. 遵循 [.net v3](performance-tips-dotnet-sdk-v3-sql.md) 或 [.net v2](performance-tips.md) 效能秘訣文章中的指導方針。 將應用程式轉換為使用具有傳輸控制通訊協定的直接連接模式 (TCP) 。 使用 TCP 通訊協定的直接連接模式沒有標頭大小限制，例如 HTTP 通訊協定，因此可避免此問題。 請務必使用最新版本的 SDK，此 SDK 在服務 interop 無法使用時，會修正查詢作業。
1. 如果使用 TCP 通訊協定的直接連線模式不適用於您的工作負載，請變更 [用戶端一致性層級](how-to-manage-consistency.md)加以減輕。 會話權杖只會用於會話一致性，這是 Azure Cosmos DB 的預設一致性層級。 其他一致性層級不會使用會話權杖。

### <a name="continuation-token-is-too-large"></a>接續權杖太大

#### <a name="cause"></a>原因：
如果接續 token 的成長太大或不同的查詢具有不同的接續 token 大小，則會在查詢作業中使用接續 token，以產生400錯誤的要求。
    
#### <a name="solution"></a>解決方案：
1. 遵循 [.net v3](performance-tips-dotnet-sdk-v3-sql.md) 或 [.net v2](performance-tips.md) 效能秘訣文章中的指導方針。 將應用程式轉換為使用直接連接模式與 TCP 通訊協定。 使用 TCP 通訊協定的直接連接模式沒有標頭大小限制，例如 HTTP 通訊協定，因此可避免此問題。 
1. 如果使用 TCP 通訊協定的直接連接模式不是您工作負載的選項，請設定 `ResponseContinuationTokenLimitInKb` 選項。 您可以在 `FeedOptions` 中的 v2 或 v3 中找到這個選項 `QueryRequestOptions` 。

## <a name="next-steps"></a>下一步
* 當您使用 Azure Cosmos DB .NET SDK 時[，診斷和疑難排解](troubleshoot-dot-net-sdk.md)問題。
* 瞭解 [.net v3](performance-tips-dotnet-sdk-v3-sql.md) 和 [.net v2](performance-tips.md)的效能指導方針。
