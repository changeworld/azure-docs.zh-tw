---
title: 如何針對 Azure Data Lake Analytics 的 SQL 作業失敗進行疑難排解，因為 .NET Framework 的4.7.2 升級
description: 針對因為升級到 .NET Framework 4.7.2 而發生的失敗，進行疑難排解。
ms.reviewer: jasonh
ms.service: data-lake-analytics
ms.topic: troubleshooting
ms.date: 10/11/2019
ms.openlocfilehash: ab03ea8a88187289f5dce55f8a396a9d51346a3f
ms.sourcegitcommit: 8d8deb9a406165de5050522681b782fb2917762d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92217672"
---
# <a name="azure-data-lake-analytics-is-upgrading-to-the-net-framework-v472"></a>Azure Data Lake Analytics 正在升級為 .NET Framework v 4.7。2

Azure Data Lake Analytics 預設執行時間是從 .NET Framework v 4.5.2 升級到 .NET Framework v 4.7.2。 如果您的內部 SQL 程式碼使用自訂群組件，而這些自訂群組件使用 .NET 程式庫，這項變更會造成重大變更的小風險。

這會從 .NET Framework 4.5.2 升級為版本4.7.2，表示在 U-SQL (執行時間中部署的 .NET Framework 現在一律會 4.7.2) 。 .NET Framework 版本沒有並存的選項。

升級至 .NET Framework 4.7.2 完成後，系統的 managed 程式碼將會以版本4.7.2 的形式執行，而使用者提供的程式庫（例如，雙 SQL 自訂群組件）將會以回溯相容的模式執行，此模式適合用來產生元件的版本。

- 如果您的元件 Dll 是針對版本4.5.2 而產生的，則已部署的架構會將它們視為4.5.2 程式庫，並提供 (，但) 4.5.2 的語法中有一些例外狀況。
- 如果您將目標設為 .NET Framework 4.7.2，您現在可以使用使用版本4.7.2 功能的雙 SQL 自訂群組件。

因為這項升級到 .NET Framework 4.7.2，所以有可能會對使用 .NET 自訂群組件的 U SQL 作業引進重大變更。 建議您使用下列程式來檢查回溯相容性問題。

## <a name="how-to-check-for-backwards-compatibility-issues"></a>如何檢查回溯相容性問題

在您的 .NET 程式碼中，對您的 .NET 程式碼執行 .NET 相容性檢查，以檢查回溯相容性中斷問題的可能性。

> [!Note]
> 此工具不會偵測到實際的重大變更。 它只會識別針對某些輸入可能 (的呼叫 .NET Api，) 造成問題。 如果您收到問題的通知，您的程式碼可能仍沒問題，不過您應該查看更多詳細資料。

1. 在 .NET Dll 上執行回溯相容性檢查程式，方法是
   1. 使用[.net 可攜性分析器](https://marketplace.visualstudio.com/items?itemName=ConnieYau.NETPortabilityAnalyzer)的 Visual Studio 延伸模組 Visual Studio 擴充功能
   1. 從 [GitHub dotnetapiport](https://github.com/microsoft/dotnet-apiport)下載並使用獨立工具。 執行獨立工具的指示位於 [GitHub dotnetapiport 重大變更](https://github.com/microsoft/dotnet-apiport/blob/dev/docs/HowTo/BreakingChanges.md)
   1. 適用于4.7.2。 相容性， `read isRetargeting == True` 可識別可能的問題。
2. 如果工具指出您的程式碼可能會受到任何可能的回溯不相容 (的影響，以下列出一些常見的不相容範例) ，您可以進一步檢查
   1. 分析您的程式碼，並識別您的程式碼是否要將值傳遞給受影響的 Api
   1. 執行執行時間檢查。 執行時間部署不會在 ADLA 中並存進行。 您可以使用 VisualStudio 的 local run 搭配具有代表性資料集的本機 .NET Framework 4.7.2，在升級之前執行執行時間檢查。
3. 如果您確實受到回溯不相容的影響，請採取必要步驟來修正此問題 (例如修正您的資料或程式碼邏輯) 。

在大部分情況下，您應該不會受到回溯不相容的影響。

## <a name="timeline"></a>時間表

您可以在這裡檢查是否有新執行時間的部署 [執行時間疑難排解](runtime-troubleshoot.md)，以及查看任何先前成功的作業。

### <a name="what-if-i-cant-get-my-code-reviewed-in-time"></a>如果我無法在時間內檢查我的程式碼，該怎麼辦？

您可以針對以) 4.5.2 為目標的舊版執行階段版本 (提交作業，不過因為缺乏 .NET Framework 並存的功能，所以它仍只會在4.5.2 相容性模式下執行。 由於這種行為，您可能還是會遇到一些回溯相容性問題。

### <a name="what-are-the-most-common-backwards-compatibility-issues-you-may-encounter"></a>您可能會遇到的最常見回溯相容性問題

檢查程式可能會識別的最常見回溯不相容性，是我們在自己的內部 ADLA 作業中執行檢查程式， (我們產生此清單) 、哪些程式庫受到影響 (注意：您只能間接呼叫程式庫，因此必須採取必要的 #1 動作，才能檢查您的作業是否受影響) 以及可能要補救的動作。 注意：在幾乎所有情況下，在我們自己的工作中，因為最重大的變更性質，所以警告會變成誤報。

- IAsyncResult.CompletedSynchronously 屬性必須正確，產生的工作才能完成
  - 呼叫 TaskFactory.FromAsync 時，IAsyncResult.CompletedSynchronously 屬性的實作必須正確，產生的工作才能完成。 也就是說，只有在實作同步完成時，屬性才必須傳回 true。 以往不會檢查屬性。
  - 受影響的程式庫： mscorlib、System.object。工作
  - 建議的動作：請確認 TaskFactory。 System.threading.tasks.taskfactory.fromasync 正確傳回 true

- DataObject.GetData 現在會以 UTF-8 形式來擷取資料
  - 若為以 NET Framework 4 為目標的應用程式，或者在 .NET Framework 4.5.1 或舊版上執行的應用程式，DataObject.GetData 會以 ASCII 字串形式來擷取 HTML 格式的資料。 如此一來，非 ASCII 字元 (ASCII 代碼大於 0x7F) 的字元會以兩個隨機字元表示。 #N # #N # 適用于以 .NET Framework 4.5 或更新版本為目標，並在 .NET Framework 4.5.2 上執行的應用程式，則會將 `DataObject.GetData` HTML 格式的資料當作 utf-8 來取得，表示正確的字元數大於0x7f。
  - 受影響的程式庫： Glo
  - 建議的動作：確定抓取的資料是您想要的格式

- 無效的代理字組會擲回 XmlWriter
  - 針對以 .NET Framework 4.5.2 或之前版本為目標的應用程式，使用例外狀況後援處理寫入無效的 Surrogate 字組時不一定每次都會擲回例外狀況。 針對以 .NET Framework 4.6 為目標的應用程式，嘗試寫入無效的代理字組會擲回 `ArgumentException` 例外狀況。
  - 受影響的程式庫： System.Xml、System.Xml。ReaderWriter
  - 建議的動作：請確定您未撰寫會導致引數例外狀況的無效代理對

- HtmlTextWriter 無法正確轉譯 `<br/>` 項目
  - 從 .NET Framework 4.6 開始，使用 `<BR />` 項目呼叫 `HtmlTextWriter.RenderBeginTag()` 和 `HtmlTextWriter.RenderEndTag()` 將會正確地只插入一個 `<BR />` (而不是兩個)
  - 受影響的程式庫： System. Web
  - 建議的動作：請確認您正在插入預期會看到的數量， `<BR />` 因此不會在生產作業中看到隨機行為

- 使用 Null 引數呼叫 CreateDefaultAuthorizationContext 已變更
  - 使用 Null authorizationPolicies 引數呼叫 `CreateDefaultAuthorizationContext(IList<IAuthorizationPolicy>)` 所傳回的 AuthorizationContext 實作，已變更其在 .NET Framework 4.6 中的實作。
  - 受影響的程式庫： System. Microsoft.identitymodel
  - 建議的動作：確定您在有 null 授權原則時，會處理新的預期行為
  
- RSACng 現在會正確地載入非標準金鑰大小的 RSA 金鑰
  - 在 .NET Framework 4.6.2 之前，使用非標準的 RSA 憑證金鑰大小客戶，無法透過 `GetRSAPublicKey()` 和 `GetRSAPrivateKey()` 擴充方法存取這些金鑰。 擲回 `CryptographicException` 具有訊息「不支援要求的金鑰大小」的。 在 .NET Framework 4.6.2 中，已修正此問題。 同樣地 `RSA.ImportParameters()` ， `RSACng.ImportParameters()` 現在會使用非標準的金鑰大小，而不會擲回 `CryptographicException` 。
  - 受影響的程式庫： mscorlib、System.object
  - 建議的動作：確定 RSA 金鑰如預期般運作

- 路徑冒號檢查更嚴格
  - 在 .NET Framework 4.6.2 中，為了支援先前所不支援的路徑 (就長度和格式兩方面) 而有數項變更。 對於適當磁碟機分隔符號 (冒號) 語法的檢查更為正確，副作用則是會封鎖一些選取路徑 API 中的某些 URI 路徑，而在過去都會容許它們。
  - 受影響的程式庫： mscorlib、System.object 副檔名
  - 建議的動作：

- 呼叫 ClaimsIdentity 建構函式
  - 從 .NET Framework 4.6.2 開始，具有 `T:System.Security.Principal.IIdentity` 參數的 `T:System.Security.Claims.ClaimsIdentity` 建構函式如何設定 `P:System.Security.Claims.ClaimsIdentify.Actor` 屬性的方法有變更。 如果 `T:System.Security.Principal.IIdentity` 引數是 `T:System.Security.Claims.ClaimsIdentity` 物件，而且該 `T:System.Security.Claims.ClaimsIdentity` 物件的 `P:System.Security.Claims.ClaimsIdentify.Actor` 屬性不是 `null`，則會使用 `M:System.Security.Claims.ClaimsIdentity.Clone` 方法來附加 `P:System.Security.Claims.ClaimsIdentify.Actor` 屬性。 在 Framework 4.6.1 和較早的版本中， `P:System.Security.Claims.ClaimsIdentify.Actor` 屬性會附加為現有的參考。 由於這項變更（從 .NET Framework 4.6.2 開始）， `P:System.Security.Claims.ClaimsIdentify.Actor` 新物件的屬性 `T:System.Security.Claims.ClaimsIdentity` 不等於函式 `P:System.Security.Claims.ClaimsIdentify.Actor` `T:System.Security.Principal.IIdentity` 引數的屬性。 在 .NET Framework 4.6.1 和更早版本中，它是相等的。
  - 受影響的程式庫： mscorlib
  - 建議的動作：確定 ClaimsIdentity 在新執行時間中如預期般運作

- DataContractJsonSerializer 的控制字元序列化現在與 ECMAScript V6 和 V8 相容
  - 在 .NET framework 4.6.2 和舊版中，DataContractJsonSerializer 未序列化某些特殊控制字元，例如 \b、\f 和 \t，其方式與 ECMAScript V6 和 V8 標準相容。 從 .NET Framework 4.7 開始，序列化這些控制字元的方式已相容於 ECMAScript V6 和 V8。
  - 受影響的程式庫： System.Runtime.Serialization.Js開啟
  - 建議的動作：確定與 DataContractJsonSerializer 相同的行為
