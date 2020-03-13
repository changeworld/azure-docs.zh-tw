---
title: 如何針對 Azure Data Lake Analytics .NET Framework 4.7.2 升級而進行的雙 SQL 作業失敗疑難排解
description: 針對因升級至 .NET Framework 4.7.2 而進行的雙 SQL 作業失敗進行疑難排解。
services: data-lake-analytics
author: guyhay
ms.author: guyhay
ms.reviewer: jasonwhowell
ms.service: data-lake-analytics
ms.topic: troubleshooting
ms.workload: big-data
ms.date: 10/11/2019
ms.openlocfilehash: f909419810cbd837e57b19a13b2df6ae9ad2ee97
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/13/2020
ms.locfileid: "79213590"
---
# <a name="azure-data-lake-analytics-is-upgrading-to-the-net-framework-v472"></a>Azure Data Lake Analytics 正在升級至 .NET Framework v 4.7。2

Azure Data Lake Analytics 預設執行時間是從 .NET Framework v 4.5.2 升級至 .NET Framework v 4.7.2。 如果您的 U-SQL 程式碼使用自訂群組件，而這些自訂群組件使用 .NET 程式庫，這項變更會帶來重大變更的小型風險。

從 .NET Framework 4.5.2 到版本4.7.2 的升級，表示部署在 U SQL 執行時間（預設執行時間）中的 .NET Framework 現在一律會是4.7.2。 .NET Framework 版本沒有並存的選項。

當此升級至 .NET Framework 4.7.2 完成之後，系統的 managed 程式碼就會以版本4.7.2 的形式執行，使用者提供的程式庫（例如，U-SQL 自訂群組件）將會以與該元件所用版本適用的回溯相容模式執行為產生。

- 如果您的元件 Dll 是針對版本4.5.2 所產生，則部署的架構會將它們視為4.5.2 程式庫，並提供（但有一些例外狀況）4.5.2 的語義。
- 如果您將目標設為 .NET Framework 4.7.2，您現在可以使用會使用版本4.7.2 功能的 U-SQL 自訂群組件。

由於此升級為 .NET Framework 4.7.2，因此可能會對使用 .NET 自訂群組件的 U-SQL 作業引入重大變更。 建議您使用下列程式來檢查回溯相容性問題。

## <a name="how-to-check-for-backwards-compatibility-issues"></a>如何檢查回溯相容性問題

在您的 .NET 程式碼中，針對您的 U-SQL 自訂群組件執行 .NET 相容性檢查，以檢查回溯相容性中斷問題的可能性。

> [!Note]
> 此工具不會偵測實際的重大變更。 它只會識別可能（針對特定輸入）導致問題的呼叫 .NET Api。 如果您收到問題的通知，您的程式碼可能仍然正常，但您應該查看更多詳細資料。

1. 在您的 .NET Dll 上執行回溯相容性檢查程式，方法是：
   1. 使用[.net 可攜性分析器](https://marketplace.visualstudio.com/items?itemName=ConnieYau.NETPortabilityAnalyzer)的 Visual Studio 延伸模組 Visual Studio 延伸模組
   1. 從[GitHub dotnetapiport](https://github.com/microsoft/dotnet-apiport)下載並使用獨立工具。 執行獨立工具的指示位於[GitHub dotnetapiport 重大變更](https://github.com/microsoft/dotnet-apiport/blob/dev/docs/HowTo/BreakingChanges.md)
   1. 適用于4.7.2。 相容性，`read isRetargeting == True` 識別可能的問題。
2. 如果工具指出您的程式碼是否可能受到任何可能的回溯不相容影響（以下列出一些常見的不相容範例），您可以進一步檢查
   1. 分析您的程式碼，並識別您的程式碼是否會將值傳遞至受影響的 Api
   1. 執行執行時間檢查。 執行時間部署不會在 ADLA 中並存完成。 您可以在升級之前執行執行時間檢查，其方式是使用 VisualStudio 的本機執行，並針對具代表性的資料集4.7.2 本機 .NET Framework。
3. 如果您真的受到回溯不相容的影響，請採取必要的步驟來修正此問題（例如，修正您的資料或程式碼邏輯）。

在大部分情況下，您不應該受到回溯相容性的影響。

## <a name="timeline"></a>時間軸

您可以在這裡檢查是否有新執行時間的部署，在此[執行時間進行疑難排解](runtime-troubleshoot.md)，以及查看任何先前成功的作業。

### <a name="what-if-i-cant-get-my-code-reviewed-in-time"></a>如果我無法及時檢查程式碼，該怎麼辦？

您可以針對舊的執行階段版本（以4.5.2 為目標）提交您的作業，不過由於缺少並存 .NET Framework 的功能，因此它仍然只能在4.5.2 相容性模式下執行。 由於這種行為，您可能還是會遇到一些回溯相容性問題。

### <a name="what-are-the-most-common-backwards-compatibility-issues-you-may-encounter"></a>您可能會遇到的最常見回溯相容性問題

檢查工具可能識別的最常見回溯-不相容性（我們會在我們自己的內部 ADLA 作業上執行檢查程式來產生此清單），哪些程式庫會受到影響（注意：您只能間接呼叫程式庫，因此，請務必採取必要的動作 #1 檢查您的作業是否受到影響），以及要補救的可能動作。 注意：在大部分的情況下，在我們自己的作業中，由於最大的重大變更 natures，因此警告已關閉為誤報。

- CompletedSynchronously 屬性必須正確，產生的工作才能完成
  - 呼叫 TaskFactory System.threading.tasks.taskfactory.fromasync 時，CompletedSynchronously 屬性的執行必須是正確的，才能完成產生的工作。 也就是說，只有在執行同步完成時，屬性才必須傳回 true。 先前未檢查屬性。
  - 受影響的程式庫： mscorlib、System.web. Tasks
  - 建議的動作：請確定 TaskFactory 正確地傳回 true

- DataObject 現在會以 UTF-8 形式抓取資料
  - 針對以 .NET Framework 4 為目標或在 .NET Framework 4.5.1 或更早版本上執行的應用程式，DataObject 會將 HTML 格式的資料視為 ASCII 字串。 因此，非 ASCII 字元（ASCII 碼大於0x7F 的字元）會以兩個隨機字元來表示。 #N # #N # 適用于以 .NET Framework 4.5 或更新版本為目標的應用程式，並在 .NET Framework 4.5.2 上執行，`DataObject.GetData` 會將 HTML 格式的資料視為 UTF-8，這表示正確地代表大於0x7F 的字元。
  - 受影響的程式庫： Glo
  - 建議的動作：請確定抓取的資料是您想要的格式

- XmlWriter 在不正確代理項配對上擲回
  - 針對以 .NET Framework 4.5.2 或舊版為目標的應用程式，使用例外狀況回退處理來撰寫不正確代理配對並不一定會擲回例外狀況。 對於以 .NET Framework 4.6 為目標的應用程式，嘗試寫入不正確代理配對會擲回 `ArgumentException`。
  - 受影響的程式庫： system.string、ReaderWriter
  - 建議的動作：請確定您未寫入會導致引數例外狀況的無效代理項配對

- HtmlTextWriter 不會正確呈現 `<br/>` 元素
  - 從 .NET Framework 4.6 開始，使用 `<BR />` 專案呼叫 `HtmlTextWriter.RenderBeginTag()` 和 `HtmlTextWriter.RenderEndTag()`，將只會正確地插入一個 `<BR />` （而不是兩個）
  - 受影響的程式庫： System.web
  - 建議的動作：請確定您要插入預期會看到的 `<BR />` 數量，以便在生產作業中看不到隨機行為

- 使用 null 引數呼叫 CreateDefaultAuthorizationCoNtext 已變更
  - 使用 null authorizationPolicies 引數呼叫 `CreateDefaultAuthorizationContext(IList<IAuthorizationPolicy>)` 所傳回的 AuthorizationCoNtext，已變更其在 .NET Framework 4.6 中的實值。
  - 受影響的程式庫： System. Microsoft.identitymodel
  - 建議的動作：當有 null 授權原則時，請確定您正在處理新的預期行為
  
- RSACng 現在會正確載入非標準金鑰大小的 RSA 金鑰
  - 在4.6.2 之前的 .NET Framework 版本中，針對 RSA 憑證使用非標準金鑰大小的客戶無法透過 `GetRSAPublicKey()` 和 `GetRSAPrivateKey()` 擴充方法來存取這些金鑰。 會擲回具有「不支援要求的金鑰大小」訊息的 `CryptographicException`。 在 .NET Framework 4.6.2 中，已修正此問題。 同樣地，`RSA.ImportParameters()` 和 `RSACng.ImportParameters()` 現在會使用非標準的金鑰大小，而不會擲回 `CryptographicException`的。
  - 受影響的程式庫： mscorlib、Core
  - 建議的動作：確定 RSA 金鑰如預期般運作

- 路徑冒號檢查較嚴格
  - 在 .NET Framework 4.6.2 中，為了支援先前不支援的路徑（長度和格式）所做的一些變更。 檢查正確的磁片磁碟機分隔符號（冒號）語法是否正確，其副作用是在少數幾個選取路徑 Api 中封鎖某些 URI 路徑，而在這些路徑中，它們曾被容許。
  - 受影響的程式庫： mscorlib、System.webserver。擴充功能
  - 建議的動作：

- 呼叫 ClaimsIdentity 的函式
  - 從 .NET Framework 4.6.2 開始，`T:System.Security.Claims.ClaimsIdentity` 具有 `T:System.Security.Principal.IIdentity` 參數的處理常式如何設定 `P:System.Security.Claims.ClaimsIdentify.Actor` 屬性會有變更。 如果 `T:System.Security.Principal.IIdentity` 引數是 `T:System.Security.Claims.ClaimsIdentity` 物件，而且該 `T:System.Security.Claims.ClaimsIdentity` 物件的 `P:System.Security.Claims.ClaimsIdentify.Actor` 屬性不 `null`，則會使用 `P:System.Security.Claims.ClaimsIdentify.Actor` 方法附加 `M:System.Security.Claims.ClaimsIdentity.Clone` 屬性。 在架構4.6.1 和較早的版本中，`P:System.Security.Claims.ClaimsIdentify.Actor` 屬性會附加為現有的參考。 由於這項變更，從 .NET Framework 4.6.2 開始，新 `T:System.Security.Claims.ClaimsIdentity` 物件的 `P:System.Security.Claims.ClaimsIdentify.Actor` 屬性不等於此函式之 `T:System.Security.Principal.IIdentity` 引數的 `P:System.Security.Claims.ClaimsIdentify.Actor` 屬性。 在 .NET Framework 4.6.1 和較舊版本中，它是相等的。
  - 受影響的程式庫： mscorlib
  - 建議的動作：確定 ClaimsIdentity 在新的執行時間上如預期般運作

- 使用 DataContractJsonSerializer 將控制字元序列化現在與 ECMAScript V6 和 V8 相容
  - 在 .NET framework 4.6.2 和更早版本中，DataContractJsonSerializer 不會將一些特殊控制字元（例如 \b、\f 和 \t）序列化為與 ECMAScript V6 和 V8 標準相容的方式。 從 .NET Framework 4.7 開始，這些控制字元的序列化會與 ECMAScript V6 和 V8 相容。
  - 受影響的程式庫： System.object。
  - 建議的動作：使用 DataContractJsonSerializer 確保相同的行為
