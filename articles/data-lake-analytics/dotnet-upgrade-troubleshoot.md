---
title: 如何排除由於 .NET 框架 4.7.2 升級而導致的 Azure 資料湖分析 U-SQL 作業故障
description: 由於升級到 .NET 框架 4.7.2，因此排除 U-SQL 作業故障。
services: data-lake-analytics
author: guyhay
ms.author: guyhay
ms.reviewer: jasonwhowell
ms.service: data-lake-analytics
ms.topic: troubleshooting
ms.workload: big-data
ms.date: 10/11/2019
ms.openlocfilehash: f909419810cbd837e57b19a13b2df6ae9ad2ee97
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79213590"
---
# <a name="azure-data-lake-analytics-is-upgrading-to-the-net-framework-v472"></a>Azure 資料湖分析正在升級到 .NET 框架 v4.7.2

Azure 資料湖分析預設運行時正在從 .NET 框架 v4.5.2 升級到 .NET 框架 v4.7.2。 如果 U-SQL 代碼使用自訂程式集，並且這些自訂程式集使用 .NET 庫，則此更改會帶來中斷更改的小風險。

從 .NET Framework 4.5.2 升級到版本 4.7.2 意味著部署在 U-SQL 運行時（預設運行時）中的 .NET 框架現在始終為 4.7.2。 .NET 框架版本沒有並行選項。

此升級到 .NET Framework 4.7.2 完成後，系統的託管代碼將運行為版本 4.7.2，使用者提供的庫（如 U-SQL 自訂程式集）將在向後相容模式下運行，該模式適用于程式集已過的版本生成。

- 如果為版本 4.5.2 生成程式集 DLL，則部署的框架將將它們視為 4.5.2 庫，提供（少數例外）4.5.2語義。
- 現在，如果您的目標是 .NET Framework 4.7.2，則可以使用使用版本 4.7.2 功能的 U-SQL 自訂程式集。

由於此升級到 .NET Framework 4.7.2，因此可能會對使用 .NET 自訂程式集的 U-SQL 作業引入重大更改。 我們建議您使用以下步驟檢查向後相容性問題。

## <a name="how-to-check-for-backwards-compatibility-issues"></a>如何檢查向後相容性問題

通過在 U-SQL 自訂程式集中運行 .NET 相容性檢查，檢查存在向後相容性中斷問題的可能性。

> [!Note]
> 該工具不會檢測到實際的中斷更改。 它僅標識可能（對於某些輸入）可能導致問題的稱為 .NET API。 如果您收到問題通知，您的代碼可能仍然正常，但應簽入更多詳細資訊。

1. 在 .NET DLL 上運行向後相容性檢查器，要麼通過
   1. 在[.NET 可攜性分析器視覺化工作室擴展](https://marketplace.visualstudio.com/items?itemName=ConnieYau.NETPortabilityAnalyzer)處使用視覺化工作室擴展
   1. 下載並使用獨立工具從[GitHub點網港](https://github.com/microsoft/dotnet-apiport)。 運行獨立工具的說明位於[GitHub 點網埠突發更改](https://github.com/microsoft/dotnet-apiport/blob/dev/docs/HowTo/BreakingChanges.md)
   1. 對於 4.7.2。 相容性，`read isRetargeting == True`識別可能的問題。
2. 如果該工具指示代碼是否可能受到任何可能的向後不相容的影響（下面列出了一些常見的不相容示例），則可以通過
   1. 分析代碼並確定代碼是否將值傳遞給受影響的 API
   1. 執行運行時檢查。 運行時部署在 ADLA 中不是並行進行的。 您可以在升級前執行運行時檢查，使用 VisualStudio 的本地運行與本地 .NET 框架 4.7.2 針對具有代表性的資料集。
3. 如果確實受到向後不相容的影響，請採取必要的步驟來修復它（例如修復資料或代碼邏輯）。

在大多數情況下，不應受到向後不相容的影響。

## <a name="timeline"></a>時間軸

您可以在此處檢查新運行時的部署[運行時故障排除](runtime-troubleshoot.md)，並查看任何以前成功的工作。

### <a name="what-if-i-cant-get-my-code-reviewed-in-time"></a>如果無法及時審核代碼，該怎麼辦？

您可以針對舊的執行階段版本（針對 4.5.2 構建）提交作業，但由於缺少 .NET Framework 並行功能，它仍將僅在 4.5.2 相容模式下運行。 由於此行為，您仍可能遇到一些向後相容性問題。

### <a name="what-are-the-most-common-backwards-compatibility-issues-you-may-encounter"></a>您可能會遇到的最常見向後相容性問題

檢查器可能識別的最常見向後不相容是（我們通過在我們自己的內部 ADLA 作業上運行檢查程式生成此清單），哪些庫受到影響（注意：您可以僅間接調用庫，因此執行所需的操作#1檢查作業是否受到影響）以及可能的操作來補救非常重要。 注意：在幾乎所有情況下，對於我們自己的工作，由於大多數中斷更改的狹隘性質，警告被證明是誤報。

- IAsyncResult.CompletedSynchronously 屬性必須正確，產生的工作才能完成
  - 呼叫 TaskFactory.FromAsync 時，IAsyncResult.CompletedSynchronously 屬性的實作必須正確，產生的工作才能完成。 也就是說，只有在實作同步完成時，屬性才必須傳回 true。 以往不會檢查屬性。
  - 受影響的庫：mscorlib，系統.執行緒.任務
  - 建議的操作：確保任務工廠。從Async返回正確

- DataObject.GetData 現在會以 UTF-8 形式來擷取資料
  - 若為以 NET Framework 4 為目標的應用程式，或者在 .NET Framework 4.5.1 或舊版上執行的應用程式，DataObject.GetData 會以 ASCII 字串形式來擷取 HTML 格式的資料。 因此，非 ASCII 字元（ASCII 代碼大於 0x7F 的字元）由兩個隨機字元表示。 #N_#N_對於針對 .NET 框架 4.5 或更高版本並在 .NET 框架 4.5.2 上運行並在 .NET 框架 4.5.2 上運行的應用，`DataObject.GetData`檢索 HTML 格式的資料為 UTF-8，表示正確大於 0x7F 的字元。
  - 受影響的庫：Glo
  - 建議的操作：確保檢索的資料是您想要的格式

- 無效的代理字組會擲回 XmlWriter
  - 針對以 .NET Framework 4.5.2 或之前版本為目標的應用程式，使用例外狀況後援處理寫入無效的 Surrogate 字組時不一定每次都會擲回例外狀況。 針對以 .NET Framework 4.6 為目標的應用程式，嘗試寫入無效的代理字組會擲回 `ArgumentException` 例外狀況。
  - 受影響的庫：系統.Xml，系統.Xml.ReaderWriter
  - 建議的操作：確保您沒有編寫將導致參數異常的無效代理項對

- HtmlTextWriter 無法正確轉譯 `<br/>` 項目
  - 從 .NET Framework 4.6 開始，使用 `<BR />` 項目呼叫 `HtmlTextWriter.RenderBeginTag()` 和 `HtmlTextWriter.RenderEndTag()` 將會正確地只插入一個 `<BR />` (而不是兩個)
  - 受影響的庫：系統.Web
  - 建議的操作：確保插入預期看到的數量，`<BR />`以便在生產作業中看不到隨機行為

- 使用 Null 引數呼叫 CreateDefaultAuthorizationContext 已變更
  - 使用 Null authorizationPolicies 引數呼叫 `CreateDefaultAuthorizationContext(IList<IAuthorizationPolicy>)` 所傳回的 AuthorizationContext 實作，已變更其在 .NET Framework 4.6 中的實作。
  - 受影響的庫：系統.身份模型
  - 建議的操作：確保在存在空授權策略時處理新的預期行為
  
- RSACng 現在會正確地載入非標準金鑰大小的 RSA 金鑰
  - 在 .NET Framework 4.6.2 之前，使用非標準的 RSA 憑證金鑰大小客戶，無法透過 `GetRSAPublicKey()` 和 `GetRSAPrivateKey()` 擴充方法存取這些金鑰。 將`CryptographicException`引發一條消息"不支援請求的金鑰大小" 使用 .NET 框架 4.6.2 此問題已修復。 同樣，`RSA.ImportParameters()``RSACng.ImportParameters()`現在使用非標準金鑰大小，而不引發`CryptographicException`。
  - 受影響的庫：mscorlib，系統。
  - 建議的操作：確保 RSA 金鑰按預期工作

- 路徑冒號檢查更嚴格
  - 在 .NET Framework 4.6.2 中，為了支援先前所不支援的路徑 (就長度和格式兩方面) 而有數項變更。 對於適當磁碟機分隔符號 (冒號) 語法的檢查更為正確，副作用則是會封鎖一些選取路徑 API 中的某些 URI 路徑，而在過去都會容許它們。
  - 受影響的庫：mscorlib，系統.運行時.擴展
  - 建議的操作：

- 呼叫 ClaimsIdentity 建構函式
  - 從 .NET Framework 4.6.2 開始，具有 `T:System.Security.Principal.IIdentity` 參數的 `T:System.Security.Claims.ClaimsIdentity` 建構函式如何設定 `P:System.Security.Claims.ClaimsIdentify.Actor` 屬性的方法有變更。 如果 `T:System.Security.Principal.IIdentity` 引數是 `T:System.Security.Claims.ClaimsIdentity` 物件，而且該 `T:System.Security.Claims.ClaimsIdentity` 物件的 `P:System.Security.Claims.ClaimsIdentify.Actor` 屬性不是 `null`，則會使用 `M:System.Security.Claims.ClaimsIdentity.Clone` 方法來附加 `P:System.Security.Claims.ClaimsIdentify.Actor` 屬性。 在框架 4.6.1 和早期版本中，`P:System.Security.Claims.ClaimsIdentify.Actor`該屬性將作為現有引用附加。 由於此更改，從 .NET 框架 4.6.2 開始`P:System.Security.Claims.ClaimsIdentify.Actor`，新`T:System.Security.Claims.ClaimsIdentity`物件的屬性不等於`P:System.Security.Claims.ClaimsIdentify.Actor`建構函式參數的屬性。 `T:System.Security.Principal.IIdentity` 在 .NET Framework 4.6.1 和更早版本中，它是相等的。
  - 受影響的庫：mscorlib
  - 建議的操作：確保聲明身份在新運行時按預期工作

- DataContractJsonSerializer 的控制字元序列化現在與 ECMAScript V6 和 V8 相容
  - 在 .NET 框架 4.6.2 和早期版本中，DataContractJson 序列化器沒有以與 ECMAScript V6 和 V8 標準相容的方式序列化某些特殊控制字元，如 \b、\f 和 \t。 從 .NET Framework 4.7 開始，序列化這些控制字元的方式已相容於 ECMAScript V6 和 V8。
  - 受影響的庫：系統.運行時.序列化。Json
  - 建議的操作：使用資料合同Jon序列化器確保相同的行為
