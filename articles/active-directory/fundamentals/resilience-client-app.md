---
title: 提升您開發的用戶端應用程式中驗證和授權的復原能力
titleSuffix: Microsoft identity platform
description: 使用 Microsoft 身分識別平臺提高用戶端應用程式中的驗證和授權復原的指引
services: active-directory
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: how-to
author: knicholasa
ms.author: nichola
manager: martinco
ms.date: 11/23/2020
ms.openlocfilehash: 69967035f98d7ec2fcedff173dcf481455014ac2
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/25/2020
ms.locfileid: "95919461"
---
# <a name="increase-the-resilience-of-authentication-and-authorization-in-client-applications-you-develop"></a>提升您開發的用戶端應用程式中驗證和授權的復原能力

本節提供在用戶端應用程式中建立恢復功能的指導方針，這些用戶端應用程式使用 Microsoft 身分識別平臺和 Azure Active Directory 來代表這些使用者登入使用者並執行動作。

## <a name="use-the-microsoft-authentication-library-msal"></a>使用 Microsoft 驗證程式庫 (MSAL) 

[Microsoft 驗證程式庫 (MSAL) ](https://docs.microsoft.com/azure/active-directory/develop/msal-overview)是[microsoft 身分識別平臺](https://docs.microsoft.com/azure/active-directory/develop)的重要部分。 它可簡化和管理取得、管理、快取和重新整理權杖，並使用最佳作法來進行復原。 MSAL 的設計目的是為了啟用安全的解決方案，讓開發人員不必擔心執行的詳細資料。

MSAL 會快取權杖，並使用無訊息的權杖取得模式。 它也會自動序列化平臺上的權杖快取，原生提供 Windows UWP、iOS 和 Android 等安全存儲裝置。 開發人員可以在使用 [Microsoft](https://github.com/AzureAD/microsoft-identity-web/wiki/token-cache-serialization)、 [MSAL.NET](https://docs.microsoft.com/azure/active-directory/develop/msal-net-token-cache-serialization)、 [MSAL For JAVA](https://docs.microsoft.com/azure/active-directory/develop/msal-java-token-cache-serialization)和 [MSAL for Python](https://docs.microsoft.com/azure/active-directory/develop/msal-python-token-cache-serialization)時，自訂序列化行為。

![使用 MSAL 呼叫 Microsoft 身分識別之裝置與應用程式的影像](media/resilience-client-app/resilience-with-microsoft-authentication-library.png)

使用 MSAL 時，您可以使用下列模式來取得權杖快取、重新整理和無訊息權杖取得。

```csharp
try
{
    result = await app.AcquireTokenSilent(scopes, account).ExecuteAsync();
}
catch(MsalUiRequiredException ex)
{
    result = await app.AcquireToken(scopes).WithClaims(ex.Claims).ExecuteAsync()
}
```

在某些情況下，MSAL 可能會主動重新整理權杖。 當 Microsoft 身分識別發出長期的權杖時，它可以將資訊傳送至用戶端，以取得重新整理權杖 ( 「重新整理」 ) 的最佳時間 \_ 。 MSAL 會根據此資訊主動重新整理權杖。 應用程式將會繼續執行，但舊的權杖有效，但會有較長的時間範圍，讓取得其他成功的權杖。

### <a name="stay-up-to-date"></a>隨時掌握最新消息

開發人員應該要有更新至最新 MSAL 版本的程式。 驗證是應用程式安全性的一部分，而您的應用程式必須保持最新的 MSAL 版本所包含的安全性增強功能。 這通常適用于持續開發的程式庫，因此可確保您擁有最新的程式碼，以確保應用程式的復原能力。 隨著 Microsoft 身分識別不斷創新應用程式更有彈性的方式，使用最新 MSAL 的應用程式將會是最適合建立這些創新的應用程式。

[查看最新的 MSAL.js 版本和版本資訊](https://github.com/AzureAD/microsoft-authentication-library-for-js/releases)

[查看最新的 MSAL .NET 版本和版本資訊](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/releases)

[查看最新的 MSAL Python 版本和版本資訊](https://github.com/AzureAD/microsoft-authentication-library-for-python/releases)

[查看最新的 MSAL JAVA 版本和版本資訊](https://github.com/AzureAD/microsoft-authentication-library-for-java/releases)

[查看最新的 MSAL iOS 和 macOS 版本和版本資訊](https://github.com/AzureAD/microsoft-authentication-library-for-objc/releases)

[查看最新的 MSAL Android 版本和版本資訊](https://github.com/AzureAD/microsoft-authentication-library-for-android/releases)

[查看最新的 MSAL 角度版本和版本資訊](https://github.com/AzureAD/microsoft-authentication-library-for-js/releases)

[查看最新的 Microsoft 版本和版本資訊](https://github.com/AzureAD/microsoft-identity-web/releases)

## <a name="if-not-using-msal-use-these-resilient-patterns-for-token-handling"></a>如果未使用 MSAL，請使用這些復原模式來處理權杖

一般情況下，使用新式驗證的應用程式將會呼叫端點來取得驗證使用者的權杖，或授權應用程式呼叫受保護的 Api。 MSAL 的目的是要處理驗證的詳細資料，並實施數種模式來改善此程式的復原能力。 如果您選擇使用 MSAL 以外的程式庫，請使用本節中的指導方針來執行最佳作法。 如果您使用 MSAL，您可以免費取得所有這些最佳做法，因為 MSAL 會自動加以執行。

### <a name="cache-tokens"></a>快取權杖

應用程式應該正確地快取從 Microsoft 身分識別收到的權杖。 當您的應用程式收到權杖時，包含權杖的 HTTP 回應也會包含 "expires_in" 屬性，告訴應用程式快取和重複使用權杖的時間長度。 重要的是，應用程式必須使用 "expires_in" 屬性來判斷權杖的存留期。 應用程式絕對不能嘗試解碼 API 存取權杖。

![呼叫 Microsoft 身分識別的應用程式，但呼叫會透過執行應用程式的裝置上的權杖快取進行](media/resilience-client-app/token-cache.png)

使用快取的權杖可防止您的應用程式與 Microsoft 身分識別之間不必要的流量，並藉由減少權杖取得呼叫次數，讓您的應用程式更不容易遭受權杖取得失敗的影響。 快取的權杖也可改善應用程式的效能，因為應用程式需要封鎖以減少取得權杖的速度。 您的使用者可以在權杖存留期的長度內保持登入應用程式。

### <a name="serialize-and-persist-tokens"></a>序列化和保存權杖

應用程式應該安全地序列化其權杖快取，以保存應用程式實例之間的權杖。 只要權杖的有效存留期內，就可以重複使用權杖。 重新整理[權杖](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-auth-code-flow#refresh-the-access-token)，以及越來越多的[存取權杖](https://docs.microsoft.com/azure/active-directory/develop/access-tokens)都會發出許多小時的時間。 此有效時間可以跨越使用者多次啟動您的應用程式。 當您的應用程式啟動時，它應該會檢查是否有可使用的有效存取或重新整理權杖。 這會增加應用程式的復原能力和效能，因為它可避免任何不必要的 Microsoft 身分識別呼叫。

![呼叫 Microsoft 身分識別的應用程式，但呼叫會透過權杖快取，以及執行應用程式的裝置上的權杖存放區來進行](media/resilience-client-app/token-store.png)

持續性權杖儲存區應可進行存取控制，並加密為擁有使用者或進程身分識別。 在 mobile、Windows 和 Mac 等平臺上，開發人員應該利用內建功能來儲存認證。

### <a name="acquire-tokens-silently"></a>以無訊息方式取得權杖

驗證使用者或抓取授權以呼叫 API 的程式，可能需要在 Microsoft 身分識別中執行多個步驟。 例如，使用者第一次登入時，可能需要輸入認證，並透過文字訊息執行多重要素驗證。 每個步驟會對提供該服務的資源新增相依性。 使用者的最佳體驗，以及相依性最少的使用者，是嘗試以無訊息方式取得權杖，以在要求使用者互動之前避免這些額外的步驟。

![此圖顯示 Microsoft 身分識別內可能需要執行才能完成驗證或授權使用者之程式的各種服務](media/resilience-client-app/external-dependencies.png)

從應用程式的權杖快取以無訊息方式從使用有效的權杖開始取得權杖。 如果沒有有效的權杖可用，應用程式應該嘗試使用重新整理權杖（如果有的話）和權杖端點來取得權杖。 如果這兩個選項都無法使用，則應用程式應該使用 "prompt = none" 參數來取得權杖。 這將會使用授權端點，但不會向使用者顯示任何 UI。 如果 Microsoft 身分識別可以提供權杖給應用程式，而不需要與使用者互動，則會。 如果這些方法都不會產生權杖，則使用者將需要以互動方式重新驗證。

> [!NOTE]
> 一般情況下，應用程式應該避免使用「登入」和「同意」之類的提示，因為即使不需要任何互動，也會強制使用者互動。

## <a name="handle-service-responses-properly"></a>適當地處理服務回應

雖然應用程式應該會處理所有錯誤回應，但是有一些回應可能會影響復原。 如果您的應用程式收到 HTTP 429 回應碼、太多要求，Microsoft 身分識別就會對您的要求進行節流。 如果您的應用程式繼續進行太多要求，它會繼續進行節流處理，以防止您的應用程式接收權杖。 您的應用程式不應該嘗試再次取得權杖，直到達到 [Retry-After 回應] 欄位中的時間（以秒為單位）。 接收429回應通常表示應用程式不會正確地快取和重複使用權杖。 開發人員應該複習權杖如何快取，並在應用程式中重複使用。

當應用程式收到 HTTP 5xx 回應碼時，應用程式不能進入快速重試迴圈。 如果存在，應用程式應該遵守與429回應相同的 Retry-After 處理。 如果回應未提供任何 Retry-After 標頭，我們建議您在回應之後的第一次重試時，執行指數反向重試。

當要求超時的應用程式不應該立即重試。 在回應之後的第一次重試時，執行指數退重試。

## <a name="evaluate-options-for-retrieving-authorization-related-information"></a>評估用來抓取授權相關資訊的選項

許多應用程式和 Api 都需要使用者的特定資訊，才能進行授權決策。 有幾種方式可讓應用程式取得此資訊。 每個方法都有其優點和缺點。 開發人員應該權衡這些，以判斷哪一種策略最適合其應用程式的復原能力。

### <a name="tokens"></a>權杖

身分識別 (識別碼) 權杖和存取權杖包含提供主體相關資訊的標準宣告。 這些記錄在 [microsoft 身分識別平臺識別碼權杖](https://docs.microsoft.com/azure/active-directory/develop/id-tokens) 和 [microsoft 身分識別平臺存取權杖](https://docs.microsoft.com/azure/active-directory/develop/access-tokens)中。 如果您的應用程式所需的資訊已經存在於權杖中，則最有效率的方法就是使用權杖宣告，因為這會儲存額外的網路呼叫段無意，以個別取得資訊。 較少的網路呼叫表示應用程式的整體恢復能力較高。

> [!NOTE]
> 有些應用程式會呼叫「使用者類型」端點，以取得已驗證使用者的相關宣告。 您的應用程式可以接收的識別碼權杖中所提供的資訊，是它可以從使用者資訊端點取得的資訊超集合。 您的應用程式應該使用識別碼權杖來取得使用者的相關資訊，而不是呼叫使用者資訊端點。

應用程式開發人員可以使用 [選擇性宣告](https://docs.microsoft.com/azure/active-directory/develop/active-directory-optional-claims)來增強標準權杖宣告。 其中一個常見的選擇性宣告是 [群組](https://docs.microsoft.com/azure/active-directory/develop/active-directory-optional-claims#configuring-groups-optional-claims)。 有幾種方式可以新增群組宣告。 [應用程式群組] 選項只包含指派給應用程式的群組。 「所有」或「安全性群組」選項包括相同租使用者中所有應用程式的群組，可將許多群組新增至權杖。 在您的案例中評估效果很重要，因為它可能會導致權杖膨脹，甚至需要額外的呼叫來取得群組的完整清單，藉以在權杖中要求群組來消除效率。

您可以改為使用和包含應用程式角色，而不是在您的權杖中使用群組。 開發人員可以使用入口網站或 Api 來定義其應用程式和 Api 的 [應用程式角色](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) ，讓客戶可以從其目錄進行管理。 然後 IT 專業人員可以將角色指派給不同的使用者和群組，以控制誰可以存取哪些內容和功能。 針對應用程式或 API 發出權杖時，指派給使用者的角色將可在權杖的 role 宣告中使用。 直接在權杖中取得這項資訊可以節省額外的 Api 呼叫。

最後，IT 系統管理員也可以根據租使用者中的特定資訊來新增宣告。 例如，企業可擁有具有企業特定使用者識別碼的擴充功能。

在所有情況下，直接將目錄中的資訊新增至權杖可能很有效率，並藉由減少應用程式所擁有的相依性數目來提高應用程式的復原能力。 另一方面，它無法解決任何無法取得權杖的復原性問題。 您只應在應用程式的主要案例中新增選擇性的宣告。 如果應用程式只需要系統管理員功能的資訊，則應用程式最好只在需要時才取得該資訊。

### <a name="microsoft-graph"></a>Microsoft Graph

Microsoft Graph 提供統一的 API 端點，以存取描述組織中生產力、身分識別和安全性模式的 Microsoft 365 資料。 使用 Microsoft Graph 的應用程式可能會在 Microsoft 365 的授權決策中使用任何資訊。

應用程式只需要單一權杖即可存取所有的 Microsoft 365。 這比使用舊版 Api （例如 Microsoft Exchange 或 Microsoft SharePoint） Microsoft 365 專用的 Api 更具彈性，需要多個權杖。

使用 Microsoft Graph Api 時，我們建議您使用 [MICROSOFT GRAPH SDK](https://docs.microsoft.com/graph/sdks/sdks-overview)。 Microsoft Graph Sdk 的設計目的是要簡化可存取 Microsoft Graph 的高品質、有效率且彈性的應用程式。

針對授權決策，開發人員應該考慮何時使用權杖中可用的宣告做為某些 Microsoft Graph 呼叫的替代方案。 如先前所述，開發人員可以在其權杖中要求群組、應用程式角色和選擇性宣告。 在恢復功能方面，使用 Microsoft Graph 進行授權需要額外的網路呼叫，依賴 Microsoft 身分識別 (來取得權杖，以存取 Microsoft Graph) 和 Microsoft Graph 本身。 不過，如果您的應用程式已經依賴 Microsoft Graph 做為其資料層，則依賴圖形進行授權並不需要額外的風險。

## <a name="use-broker-authentication-on-mobile-devices"></a>在行動裝置上使用 broker 驗證

在行動裝置上，使用驗證代理程式（例如 Microsoft Authenticator）將會改善復原能力。 Broker 增加了與其他選項（例如系統瀏覽器或內嵌的瀏覽器）一起提供的優點。 驗證代理程式可以利用主要的重新整理 [權杖](https://docs.microsoft.com/azure/active-directory/devices/concept-primary-refresh-token) (PRT) ，其中包含有關使用者和裝置的宣告，而且可用來取得驗證權杖以從裝置存取其他應用程式。 當 PRT 用來要求存取應用程式時，它的裝置和 MFA 宣告會受到 Azure AD 的信任。 這可避免額外的步驟再次驗證裝置，從而提高復原能力。 使用者不會在同一部裝置上遇到多個 MFA 提示，因此藉由減少對外部服務的相依性，並提升使用者體驗來提高復原能力。

![呼叫 Microsoft 身分識別的應用程式，但呼叫會透過權杖快取，以及執行應用程式的裝置上的權杖存放區和驗證代理程式進行](media/resilience-client-app/authentication-broker.png)

MSAL 會自動支援 Broker 驗證。 您可以在下列頁面找到有關使用代理驗證的詳細資訊：

- [在 macOS 和 iOS 上設定 SSO](https://docs.microsoft.com/azure/active-directory/develop/single-sign-on-macos-ios#sso-through-authentication-broker-on-ios)
- [如何使用 MSAL 在 Android 上啟用跨應用程式的 SSO](https://docs.microsoft.com/azure/active-directory/develop/msal-android-single-sign-on)

## <a name="adopt-continuous-access-evaluation"></a>採用連續存取評估

[持續存取評估 (CAE) ](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-continuous-access-evaluation) 是最新的開發，可利用長期的權杖來提高應用程式的安全性和彈性。 CAE 是一種新興的產業標準，是在 OpenID Foundation 的共用信號和事件工作群組中開發。 使用 CAE 時，您可以根據 [重大事件](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-continuous-access-evaluation#critical-event-evaluation) 和 [原則評估](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-continuous-access-evaluation#conditional-access-policy-evaluation-preview)來撤銷存取權杖，而不需依賴簡短的權杖存留期。 針對某些資源 Api，因為風險和原則會即時評估，CAE 可能會大幅增加最多28小時的權杖存留期。 由於資源 Api 和應用程式採用 CAE，因此 Microsoft 身分識別將能夠發出可撤銷的存取權杖，並可在一段長時間內有效。 這些長期的權杖將由 MSAL 主動重新整理。

雖然 CAE 是在早期階段，但 [現在可以開發用戶端應用程式](../develop/app-resilience-continuous-access-evaluation.md) ，以在應用程式) 應用程式使用採用 CAE 的資源 (api 時受益于 CAE。 當更多資源採用 CAE 時，您的應用程式也可以為這些資源取得啟用 CAE 的權杖。 Microsoft Graph API 和 [Microsoft Graph sdk](https://docs.microsoft.com/graph/sdks/sdks-overview)將于2021年初預覽 CAE 功能。 如果您想要透過 CAE 參與 Microsoft Graph 的公開預覽，您可以讓我們知道您有興趣： [https://aka.ms/GraphCAEPreview](https://aka.ms/GraphCAEPreview) 。

如果您開發資源 Api，我們鼓勵您參與 [共用信號和事件 WG](https://openid.net/wg/sse/)。 我們會使用此群組來啟用 Microsoft 身分識別與資源提供者之間的安全性事件共用。

## <a name="next-steps"></a>後續步驟

- [如何在您的應用程式中使用持續存取評估啟用的 Api](../develop/app-resilience-continuous-access-evaluation.md)
- [為 daemon 應用程式建立恢復功能](resilience-daemon-app.md)
- [在您的身分識別與存取管理基礎結構中建立恢復功能](resilience-in-infrastructure.md)
- [使用 Azure Active Directory B2C 在您的客戶身分識別和存取管理中打造復原能力](resilience-b2c.md)