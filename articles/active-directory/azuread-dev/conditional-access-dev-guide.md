---
title: Azure AD 條件式存取開發人員指導方針
description: Azure AD 條件式存取的開發人員指引和案例
services: active-directory
author: rwike77
manager: CelesteDG
ms.author: ryanwi
ms.reviewer: jmprieur, saeeda
ms.date: 02/28/2019
ms.service: active-directory
ms.subservice: azuread-dev
ms.custom: aaddev
ms.topic: conceptual
ms.workload: identity
ms.openlocfilehash: 5b7434af51cca2677b6b3d678ca8031cc6dbffab
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/12/2020
ms.locfileid: "77165003"
---
# <a name="developer-guidance-for-azure-active-directory-conditional-access"></a>Azure Active Directory 條件式存取的開發人員指引

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

Azure Active Directory （Azure AD）中的條件式存取功能提供了數種方式的其中一種，可讓您用來保護應用程式及保護服務。 條件式存取可讓開發人員和企業客戶以多種方式來保護服務，包括：

* Multi-Factor Authentication
* 只允許已註冊 Intune 的裝置存取特定服務
* 限制使用者位置及 IP 範圍

如需條件式存取完整功能的詳細資訊，請參閱[Azure Active Directory 中的條件式存取](../active-directory-conditional-access-azure-portal.md)。

針對建立 Azure AD 應用程式的開發人員，本文說明如何使用條件式存取，同時也會瞭解存取無法控制的資源所產生的影響，這可能會套用條件式存取原則。 本文也會探討代理者流程、web 應用程式、存取 Microsoft Graph 和呼叫 Api 的條件式存取的含意。

假設有[單一和多租](../develop/howto-convert-app-to-be-multi-tenant.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)使用者應用程式的知識，以及[常見的驗證模式](v1-authentication-scenarios.md)。

## <a name="how-does-conditional-access-impact-an-app"></a>條件式存取對應用程式的影響為何？

### <a name="app-types-impacted"></a>受影響的應用程式類型

在最常見的情況下，條件式存取不會變更應用程式的行為，或需要開發人員所做的任何變更。 只有在某些情況下，當應用程式間接或以無訊息方式要求服務的權杖時，應用程式才需要變更程式碼來處理條件式存取「挑戰」。 這可能會像執行互動式登入要求一樣簡單。

具體而言，下列案例需要程式碼來處理條件式存取「挑戰」：

* 執行代理者流程的應用程式
* 存取多個服務/資源的應用程式
* 使用 ADAL.js 的單頁應用程式
* 呼叫資源的 Web Apps

條件式存取原則可以套用至應用程式，但也可以套用至應用程式存取 Web API。 若要深入瞭解如何設定條件式存取原則，請參閱[快速入門：使用 Azure Active Directory 條件式存取的特定應用程式需要 MFA](../conditional-access/app-based-mfa.md)。

根據案例而定，企業客戶隨時都可以套用和移除條件式存取原則。 若要在套用新原則時讓您的應用程式繼續運作，您必須實作「挑戰」處理。 下列範例說明挑戰處理。

### <a name="conditional-access-examples"></a>條件式存取範例

某些案例需要變更程式碼來處理條件式存取，而其他情況則以相同的方式工作。 以下是一些使用條件式存取進行多重要素驗證的案例，可讓您深入瞭解差異。

* 您正在建立單一租使用者 iOS 應用程式，並套用條件式存取原則。 應用程式會將使用者登入，且不會要求存取 API。 當使用者登入時，會自動叫用原則，而使用者必須執行多重要素驗證 (MFA)。 
* 您正在建置一個會使用中介層服務來存取下游 API 的原生應用程式。 公司中使用此應用程式的企業客戶會將原則套用至下游 API。 當使用者登入時，原生應用程式會要求存取中介層並傳送權杖。 中介層會執行代理者流程來要求存取下游 API。 此時，宣告「挑戰」會呈現至中介層。 中介層會將挑戰傳回原生應用程式，這必須符合條件式存取原則。

#### <a name="microsoft-graph"></a>Microsoft Graph

Microsoft Graph 在條件式存取環境中建立應用程式時有特殊考慮。 一般而言，條件式存取的機制會有相同的行為，但您的使用者所看到的原則會以您的應用程式從圖形要求的基礎資料為基礎。 

具體而言，所有 Microsoft Graph 範圍都代表可個別套用原則的部分資料集。 由於條件式存取原則是指派給特定的資料集，Azure AD 會根據圖形背後的資料（而不是圖形本身）來強制執行條件式存取原則。

例如，如果應用程式要求下列 Microsoft Graph 範圍，

```
scopes="Bookings.Read.All Mail.Read"
```

應用程式可以預期他們的使用者能夠滿足所有在預約和 Exchange 上設定的原則。 某些範圍如果授與存取權，可能會對應到多個資料集。 

### <a name="complying-with-a-conditional-access-policy"></a>遵守條件式存取原則

針對數個不同的應用程式拓撲，會在建立會話時評估條件式存取原則。 當條件式存取原則會在應用程式和服務的資料細微性上運作時，叫用它的點主要取決於您嘗試完成的案例。

當您的應用程式嘗試存取具有條件式存取原則的服務時，可能會遇到條件式存取挑戰。 這項挑戰會以來自 Azure AD 回應的 `claims` 參數編碼。 以下是這項挑戰參數的範例： 

```
claims={"access_token":{"polids":{"essential":true,"Values":["<GUID>"]}}}
```

開發人員可以使用這項挑戰，並將其附加到 Azure AD 的新要求。 傳遞此狀態會提示使用者執行任何必要的動作，以符合條件式存取原則。 在下列情節中，會說明錯誤的詳細規格和擷取參數的方式。

## <a name="scenarios"></a>案例

### <a name="prerequisites"></a>Prerequisites

Azure AD 條件式存取是[Azure AD Premium](https://docs.microsoft.com/azure/active-directory/active-directory-whatis)中包含的功能。 您可以在[未經授權的使用報告](../active-directory-conditional-access-unlicensed-usage-report.md)中深入了解授權需求。 開發人員可以加入 [Microsoft Developer Network](https://msdn.microsoft.com/dn308572.aspx)，其中包含的 Enterprise Mobility Suite 免費訂用帳戶會包含 Azure AD Premium。

### <a name="considerations-for-specific-scenarios"></a>特定情節的考量

下列資訊僅適用于下列條件式存取案例：

* 執行代理者流程的應用程式
* 存取多個服務/資源的應用程式
* 使用 ADAL.js 的單頁應用程式

下列各節會討論更複雜的常見案例。 核心作業原則是針對已套用條件式存取原則的服務要求權杖時，評估條件式存取原則。

## <a name="scenario-app-performing-the-on-behalf-of-flow"></a>情節：執行代理者流程的應用程式

在此情節中，我們會逐步解說原生應用程式呼叫 web 服務/API 的案例。 接著，此服務會執行「代理者」流程來呼叫下游服務。 在我們的案例中，我們已將條件式存取原則套用至下游服務（Web API 2），並使用原生應用程式，而不是伺服器/daemon 應用程式。 

![執行代理者流程圖的應用程式](./media/conditional-access-dev-guide/app-performing-on-behalf-of-scenario.png)

Web API 1 的初始權杖要求不會提示使用者進行多重要素驗證，因為 Web API 1 不一定會叫用下游 API。 一旦 Web API 1 嘗試要求權杖代理者使用者提供 Web API 2 後，要求就會失敗，因為使用者尚未使用多重要素驗證登入。

Azure AD 會傳回 HTTP 回應，包含一些有趣的資料：

> [!NOTE]
> 在此例中，這是多重要素驗證錯誤的描述，但有各種 `interaction_required` 可能與條件式存取有關。

```
HTTP 400; Bad Request
error=interaction_required
error_description=AADSTS50076: Due to a configuration change made by your administrator, or because you moved to a new location, you must use multi-factor authentication to access '<Web API 2 App/Client ID>'.
claims={"access_token":{"polids":{"essential":true,"Values":["<GUID>"]}}}
```

在 Web API 1 中，攔截到 `error=interaction_required` 錯誤，並將 `claims` 挑戰傳回給桌面應用程式。 此時，桌面應用程式可以進行新的 `acquireToken()` 呼叫，並附加 `claims` 挑戰作為額外的查詢字串參數。 這個新的要求需要使用者執行多重要素驗證，然後將這個新的權杖傳回 Web API 1，才能完成代理者流程。

若要試用此情節，請參閱我們的 [.NET 程式碼範例](https://github.com/Azure-Samples/active-directory-dotnet-webapi-onbehalfof-ca)。 它會示範如何從 Web API 1 將宣告挑戰傳回到原生應用程式，並在用戶端應用程式內建構新的要求。

## <a name="scenario-app-accessing-multiple-services"></a>情節：存取多個服務的應用程式

在此案例中，我們會逐步解說 web 應用程式存取兩個服務的情況，其中一個已指派條件式存取原則。 根據您的應用程式邏輯而定，可能有一個路徑是您的應用程式在其中不需要存取這兩個 web 服務。 在此案例中，您要求權杖的順序在使用者體驗中扮演重要的角色。

假設我們有 web 服務 A 和 B，而 web 服務 B 已套用我們的條件式存取原則。 雖然初始互動式驗證要求必須同意這兩項服務，但在所有情況下都不需要條件式存取原則。 如果應用程式要求 web 服務 B 的權杖，就會叫用原則，且 web 服務 A 的後續要求也會成功，如下所示。

![存取多個服務的應用程式流程圖](./media/conditional-access-dev-guide/app-accessing-multiple-services-scenario.png)

或者，如果應用程式一開始要求 web 服務 A 的權杖，終端使用者就不會叫用條件式存取原則。 這可讓應用程式開發人員控制使用者體驗，而不會強制在所有情況下叫用條件式存取原則。 如果應用程式後續要求 web 服務 B 的權杖，則會是棘手的情況。此時，終端使用者必須遵守條件式存取原則。 當應用程式嘗試 `acquireToken` 時，可能會產生下列錯誤 (如下列圖表所示)：

```
HTTP 400; Bad Request
error=interaction_required
error_description=AADSTS50076: Due to a configuration change made by your administrator, or because you moved to a new location, you must use multi-factor authentication to access '<Web API App/Client ID>'.
claims={"access_token":{"polids":{"essential":true,"Values":["<GUID>"]}}}
```

![存取多個要求新權杖之服務的應用程式](./media/conditional-access-dev-guide/app-accessing-multiple-services-new-token.png)

如果應用程式是使用 ADAL 程式庫，就一律會以互動方式重試取得權杖失敗。 當此互動式要求發生時，使用者就有機會遵守條件式存取。 除非要求是 `AcquireTokenSilentAsync` 或 `PromptBehavior.Never` 在此情況下，應用程式需要執行互動式 ```AcquireToken``` 要求，讓使用者有機會符合原則。

## <a name="scenario-single-page-app-spa-using-adaljs"></a>案例：使用 ADAL.js 的單頁應用程式 (SPA)

在此案例中，我們會逐步解說當我們有單一頁面應用程式（SPA）時，使用 ADAL 呼叫受保護 Web API 的條件式存取。 這是一個簡單的架構，但在開發條件式存取時，需要考慮一些細微之處。

在 ADAL.js 中，有幾個函式可取得權杖：`login()`、`acquireToken(...)`、`acquireTokenPopup(…)` 和 `acquireTokenRedirect(…)`。

* `login()` 會透過互動式登入要求取得識別碼權杖，但不會取得任何服務的存取權杖（包括受條件式存取保護的 Web API）。
* 接著可以使用 `acquireToken(…)`，以無訊息方式取得存取權杖，這表示它在任何情況下都不會顯示 UI。
* `acquireTokenPopup(…)` 和 `acquireTokenRedirect(…)` 都是用來以互動方式要求資源的權杖，這表示它們一律會顯示登入 UI。

當應用程式需要存取權杖來呼叫 Web API 時，它會嘗試 `acquireToken(…)`。 如果權杖會話已過期，或我們需要遵守條件式存取原則，則*acquireToken*函式會失敗，且應用程式會使用 `acquireTokenPopup()` 或 `acquireTokenRedirect()`。

![使用 ADAL 的單頁應用程式流程圖](./media/conditional-access-dev-guide/spa-using-adal-scenario.png)

讓我們逐步解說使用條件式存取案例的範例。 使用者只需登陸網站，且沒有工作階段。 我們執行 `login()` 呼叫時，無需進行多重要素驗證即可取得 ID 權杖。 然後使用者會叫用按鈕，要求應用程式向 web API 要求資料。 應用程式嘗試執行 `acquireToken()` 呼叫但失敗，因為使用者尚未執行多重要素驗證，而且必須符合條件式存取原則。

Azure AD 會傳回下列 HTTP 回應：

```
HTTP 400; Bad Request
error=interaction_required
error_description=AADSTS50076: Due to a configuration change made by your administrator, or because you moved to a new location, you must use multi-factor authentication to access '<Web API App/Client ID>'.
```

我們的應用程式必須攔截 `error=interaction_required`。 應用程式就可以在相同的資源上使用 `acquireTokenPopup()` 或 `acquireTokenRedirect()`。 系統會強制使用者執行多重要素驗證。 使用者完成多重要素驗證之後，就會發出新的存取權杖給應用程式，以取得要求的資源。

若要試用此情節，請參閱我們的 [JS SPA 代理者程式碼範例](https://github.com/Azure-Samples/active-directory-dotnet-webapi-onbehalfof-ca)。 此程式碼範例會使用條件式存取原則，以及您稍早使用 JS SPA 註冊的 Web API 來示範這種情況。 它會示範如何正確處理宣告挑戰，並取得可用於您 Web API 的存取權杖。 或者，查看一般 [Angular.js 程式碼範例](https://github.com/Azure-Samples/active-directory-angularjs-singlepageapp)，以取得 Angular SPA 的指引

## <a name="see-also"></a>另請參閱

* 若要深入了解功能，請參閱 [Azure Active Directory 中的條件式存取](../active-directory-conditional-access-azure-portal.md)。
* 如需更多的 Azure AD 程式碼範例，請參閱[程式碼範例的 Github 存放庫](https://github.com/azure-samples?utf8=%E2%9C%93&q=active-directory)。
* 如需 ADAL SDK 的詳細資訊及存取參考文件，請參閱[程式庫指南](active-directory-authentication-libraries.md)。
* 若要深入了解多租用戶情節，請參閱[如何使用多租用戶模式將使用者登入](../develop/howto-convert-app-to-be-multi-tenant.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)。
