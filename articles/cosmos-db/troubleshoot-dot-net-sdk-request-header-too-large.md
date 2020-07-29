---
title: 在 Azure Cosmos DB 中疑難排解要求標頭太大或400不正確的要求
description: 如何診斷和修正要求標頭太大例外狀況
author: j82w
ms.service: cosmos-db
ms.date: 07/13/2020
ms.author: jawilley
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: 942af52c43a1d4526a42f20df40abf3f510e0ee6
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87294176"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-request-header-too-large-message"></a>針對 Azure Cosmos DB 要求標頭太大的訊息進行診斷和疑難排解
要求標頭過大的訊息會擲回，並出現 HTTP 錯誤碼400。 如果要求標頭的大小成長過大，且超過允許的大小上限，就會發生此錯誤。 我們建議您使用最新版的 SDK。 請務必使用至少版本3.x 或2.x，因為這些版本會在例外狀況訊息中加入標頭大小追蹤。

## <a name="troubleshooting-steps"></a>疑難排解步驟
如果會話或接續 token 太大，就會發生要求標頭太大的訊息。 下列各節說明每個類別中問題的原因和解決方案。

### <a name="1-session-token-too-large"></a>1. 會話權杖太大

#### <a name="cause"></a>原因：
400不正確的要求很可能是因為會話權杖太大所造成。 如果下列語句為 true，則會確認會話 token 太大。

* 此錯誤發生在點作業，例如建立、讀取、更新等等，其中沒有接續 token。
* 例外狀況已啟動，但未對應用程式進行任何變更。 會話權杖會隨著容器中增加的分割區數目成長。 分割區數目會隨著資料量增加或輸送量增加而增加。

#### <a name="temporary-mitigation"></a>暫時緩和： 
重新開機您的用戶端應用程式，以重設所有會話權杖。 會話權杖最後會變回造成此問題的先前大小。 因此，請使用下一節中的解決方案，完全避免這個問題。

#### <a name="solution"></a>解決方案：
1. 遵循[.Net V3](performance-tips-dotnet-sdk-v3-sql.md)或[.net V2](performance-tips.md)效能秘訣一文中的指導方針，並將應用程式轉換成使用具有 TCP 通訊協定的直接連接模式。 具有 TCP 通訊協定的直接模式沒有像 HTTP 通訊協定的標頭大小限制，因此可避免這個問題。 請務必使用最新版本的 SDK，當服務 interop 無法使用時，它會修正查詢作業。
2. 如果您的工作負載無法選擇具有 TCP 通訊協定的直接連線模式，請變更[用戶端一致性層級](how-to-manage-consistency.md)來減輕此問題。 會話權杖僅用於會話一致性，這是 Azure Cosmos DB 的預設一致性層級。 其他一致性層級不會使用會話 token。

### <a name="2-continuation-token-too-large"></a>2. 接續 token 太大

#### <a name="cause"></a>原因：
使用接續 token 的查詢作業會發生400錯誤的要求。 如果接續 token 成長過大，或不同的查詢有不同的接續 token 大小，則為。
    
#### <a name="solution"></a>解決方案：
1. 遵循[.Net V3](performance-tips-dotnet-sdk-v3-sql.md)或[.net V2](performance-tips.md)效能秘訣一文中的指導方針，並將應用程式轉換成使用具有 TCP 通訊協定的直接連接模式。 具有 TCP 通訊協定的直接模式沒有像 HTTP 通訊協定的標頭大小限制，因此可避免這個問題。 
3. 如果您的工作負載無法選擇具有 TCP 通訊協定的直接連線模式，請嘗試設定 `ResponseContinuationTokenLimitInKb` 選項。 您可以在 `FeedOptions` for v2 或 v3 中找到此選項 `QueryRequestOptions` 。

## <a name="next-steps"></a>後續步驟
* 針對使用 Azure Cosmos DB .NET SDK 時[的問題進行診斷和疑難排解](troubleshoot-dot-net-sdk.md)
* 瞭解[.Net V3](performance-tips-dotnet-sdk-v3-sql.md)和[.net V2](performance-tips.md)的效能指導方針
