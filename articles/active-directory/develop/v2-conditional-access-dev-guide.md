---
title: Azure Active Directory 條件式存取的開發人員指引
titleSuffix: Microsoft identity platform
description: Azure AD 條件式存取及 Microsoft 身分識別平台的開發人員指引和案例。
services: active-directory
keywords: ''
author: rwike77
manager: CelesteDG
ms.author: ryanwi
ms.reviewer: jmprieur, saeeda
ms.date: 05/18/2020
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev
ms.topic: conceptual
ms.workload: identity
ms.openlocfilehash: 6b31a03a6367c9c6f2025c1544b59c95b3f69175
ms.sourcegitcommit: 318d1bafa70510ea6cdcfa1c3d698b843385c0f6
ms.contentlocale: zh-TW
ms.lasthandoff: 05/21/2020
ms.locfileid: "83771072"
---
# <a name="developer-guidance-for-azure-active-directory-conditional-access"></a>Azure Active Directory 條件式存取的開發人員指引

您可以使用 Azure Active Directory (Azure AD) 中條件式存取功能所提供的其中一種方式，來保護您的應用程式及保護服務。 條件式存取可讓開發人員和企業客戶以多種方式保護服務，包括：

* [多重要素驗證](../authentication/concept-mfa-howitworks.md)
* 只允許已註冊 Intune 的裝置存取特定服務
* 限制使用者位置及 IP 範圍

如需有關條件式存取的完整功能詳細資訊，請參閱[什麼是條件式存取](../conditional-access/overview.md)一文。

針對建置 Azure AD 適用應用程式的開發人員，本文會說明如何使用條件式存取，而您也會了解存取已套用條件式存取原則的資源有何影響。 本文也會探討在代理者流程、Web 應用程式、存取 Microsoft Graph 以及呼叫 API 中進行條件式存取的含意。

它假設對於[單一](quickstart-register-app.md)和[多租用戶](howto-convert-app-to-be-multi-tenant.md)應用程式以及[常見驗證模式](authentication-scenarios.md)的認知。

> [!NOTE]
> 使用此方法需要 Azure AD Premium P1 授權。 若要尋找適用於您需求的正確授權，請參閱[比較 Free、Basic 及 Premium 版本的正式運作功能](https://azure.microsoft.com/pricing/details/active-directory/)。
> 擁有 [Microsoft 365 商務版授權](/office365/servicedescriptions/microsoft-365-service-descriptions/microsoft-365-business-service-description)的客戶也有條件式存取功能的存取權。

## <a name="how-does-conditional-access-impact-an-app"></a>條件式存取如何影響應用程式？

### <a name="app-types-impacted"></a>受影響的應用程式類型

在最常見的案例中，條件式存取不會變更應用程式的行為，或是需要從開發人員進行任何變更。 只有當應用程式以間接或無訊息方式要求權杖提供服務的特定案例中，應用程式才會要求程式碼變更，以處理條件式存取「挑戰」。 這有如執行互動式登入要求一樣簡單。

具體而言，下列情節需要程式碼來處理條件式存取「挑戰」：

* 執行代理者流程的應用程式
* 存取多個服務/資源的應用程式
* 使用 MSAL.js 的單頁應用程式
* 呼叫資源的 Web Apps

條件式存取原則可以套用至應用程式，也可套用至您應用程式存取的 Web API。 若要深入了解如何設定條件式存取原則的相關詳細資訊，請參閱[快速入門：透過 Azure Active Directory 條件式存取來要求特定應用程式必須使用 MFA](../conditional-access/app-based-mfa.md)。

企業客戶可以根據這種情節，隨時套用及移除條件式存取原則。 若要在套用新原則時讓您的應用程式繼續運作，您必須實作「挑戰」處理。 下列範例說明挑戰處理。

### <a name="conditional-access-examples"></a>條件式存取範例

某些情節需要程式碼變更才能處理條件式存取，而其他情節則是以原狀運作。 以下幾個情節使用條件式存取來執行多重要素驗證，能深入解析差異。

* 您要建置單一租用戶 iOS 應用程式，並套用條件式存取原則。 應用程式會將使用者登入，且不會要求存取 API。 當使用者登入時，會自動叫用原則，而使用者必須執行多重要素驗證 (MFA)。
* 您正在建置一個會使用中介層服務來存取下游 API 的原生應用程式。 公司中使用此應用程式的企業客戶會將原則套用至下游 API。 當使用者登入時，原生應用程式會要求存取中介層並傳送權杖。 中介層會執行代理者流程來要求存取下游 API。 此時，宣告「挑戰」會呈現至中介層。 中介層會將挑戰傳送回原生應用程式，其必須遵守條件式存取原則。

#### <a name="microsoft-graph"></a>Microsoft Graph

Microsoft Graph 在條件式存取環境中建置應用程式時有特殊考慮。 一般而言，條件式存取的機制會有相同的行為，但您使用者所看到的原則會以您應用程式從圖表要求的基礎資料為基礎。

具體而言，所有 Microsoft Graph 範圍都代表可個別套用原則的部分資料集。 由於條件式存取原則是指派給特定的資料集，Azure AD 會根據圖表背後的資料 (而不是圖表本身) 來強制執行條件式存取原則。

例如，如果應用程式要求下列 Microsoft Graph 範圍，

```
scopes="Bookings.Read.All Mail.Read"
```

應用程式可以預期其使用者滿足在 Bookings 和 Exchange 上設定的所有原則。 某些範圍如果授與存取權，可能會對應到多個資料集。

### <a name="complying-with-a-conditional-access-policy"></a>遵守條件式存取原則

針對幾個不同的應用程式拓樸，當工作階段建立時，會評估條件式存取原則。 由於條件式存取原則是運作於應用程式和服務的細微性，其叫用的點會大幅取決於您嘗試完成的情節。

當您的應用程式嘗試存取具有條件式存取原則的服務時，其可能會遇到條件式存取的挑戰。 這項挑戰會以來自 Azure AD 之回應的 `claims` 參數進行編碼。 以下是這項挑戰參數的範例：

```
claims={"access_token":{"polids":{"essential":true,"Values":["<GUID>"]}}}
```

開發人員可以使用這項挑戰，並將其附加到 Azure AD 的新要求。 傳遞此狀態會提示使用者執行遵守條件式存取原則所需的任何動作。 在下列情節中，會說明錯誤的詳細規格和擷取參數的方式。

## <a name="scenarios"></a>案例

### <a name="prerequisites"></a>Prerequisites

Azure AD 條件式存取是 [Azure AD Premium](https://docs.microsoft.com/azure/active-directory/active-directory-whatis) 中所包含的一項功能。 擁有 [Microsoft 365 商務版授權](/office365/servicedescriptions/microsoft-365-service-descriptions/microsoft-365-business-service-description)的客戶也有條件式存取功能的存取權。

### <a name="considerations-for-specific-scenarios"></a>特定情節的考量

下列資訊僅適用於這些條件式存取情節：

* 執行代理者流程的應用程式
* 存取多個服務/資源的應用程式
* 使用 MSAL.js 的單頁應用程式

下列各節會討論更複雜的常見案例。 核心作業的原則是在要求權杖提供已套用條件式存取原則的服務時評估條件式存取原則。

## <a name="scenario-app-performing-the-on-behalf-of-flow"></a>案例：執行代理者流程的應用程式

在此情節中，我們會逐步解說原生應用程式呼叫 web 服務/API 的案例。 接著，此服務會執行「代理者」流程來呼叫下游服務。 在我們的案例中，已將我們的條件式存取原則套用至下游服務 (Web API 2)，且是使用原生應用程式，而不是伺服器/精靈應用程式。

![執行代理者流程圖的應用程式](./media/v2-conditional-access-dev-guide/app-performing-on-behalf-of-scenario.png)

Web API 1 的初始權杖要求不會提示使用者進行多重要素驗證，因為 Web API 1 不一定會叫用下游 API。 一旦 Web API 1 嘗試要求權杖代理者使用者提供 Web API 2 後，要求就會失敗，因為使用者尚未使用多重要素驗證登入。

Azure AD 會傳回 HTTP 回應，包含一些有趣的資料：

> [!NOTE]
> 在這個執行個體中，有多重要素驗證錯誤的描述，但有各種不同的 `interaction_required` 可能屬於條件式存取。

```
HTTP 400; Bad Request
error=interaction_required
error_description=AADSTS50076: Due to a configuration change made by your administrator, or because you moved to a new location, you must use multi-factor authentication to access '<Web API 2 App/Client ID>'.
claims={"access_token":{"polids":{"essential":true,"Values":["<GUID>"]}}}
```

在 Web API 1 中，攔截到 `error=interaction_required` 錯誤，並將 `claims` 挑戰傳回給桌面應用程式。 此時，桌面應用程式可以進行新的 `acquireToken()` 呼叫，並附加 `claims` 挑戰作為額外的查詢字串參數。 這個新的要求需要使用者執行多重要素驗證，然後將這個新的權杖傳回 Web API 1，才能完成代理者流程。

若要試用此情節，請參閱我們的 [.NET 程式碼範例](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/tree/master/2.%20Web%20API%20now%20calls%20Microsoft%20Graph#handling-required-interactions-with-the-user-dynamic-consent-mfa-etc-)。 它會示範如何從 Web API 1 將宣告挑戰傳回到原生應用程式，並在用戶端應用程式內建構新的要求。

## <a name="scenario-app-accessing-multiple-services"></a>案例：存取多個服務的應用程式

在此情節中，我們會逐步解說 Web 應用程式存取兩個服務的案例，其中之一已指派條件式存取原則。 根據您的應用程式邏輯而定，可能有一個路徑是您的應用程式在其中不需要存取這兩個 web 服務。 在此情節中，您要求權杖的順序在使用者體驗中扮演重要的角色。

假設我們有 A 和 B 的 Web 服務，而 Web 服務 B 已套用我們的條件式存取原則。 初始互動式驗證要求需要兩個服務的同意，但在所有情況下都不需要條件式存取原則。 如果應用程式要求 web 服務 B 的權杖，就會叫用原則，且 web 服務 A 的後續要求也會成功，如下所示。

![存取多個服務的應用程式流程圖](./media/v2-conditional-access-dev-guide/app-accessing-multiple-services-scenario.png)

或者，如果應用程式一開始要求 Web 服務 A 的權杖，使用者就不會叫用條件式存取原則。 這可讓應用程式開發人員控制使用者體驗，且在所有情況下皆不會強制叫用條件式存取原則。 如果應用程式後續要求 Web 服務 B 的權杖，則會是棘手的案例。此時，使用者就必須遵守條件式存取原則。 當應用程式嘗試 `acquireToken` 時，可能會產生下列錯誤 (如下列圖表所示)：

```
HTTP 400; Bad Request
error=interaction_required
error_description=AADSTS50076: Due to a configuration change made by your administrator, or because you moved to a new location, you must use multi-factor authentication to access '<Web API App/Client ID>'.
claims={"access_token":{"polids":{"essential":true,"Values":["<GUID>"]}}}
```

![存取多個要求新權杖之服務的應用程式](./media/v2-conditional-access-dev-guide/app-accessing-multiple-services-new-token.png)

如果應用程式是使用 MSAL 程式庫，則一律會以互動方式重試取得權杖失敗。 當此互動式要求出現時，使用者就有機會遵守條件式存取。 這個是通用情況，除非要求為 `AcquireTokenSilentAsync` 或 `PromptBehavior.Never`，在此情況下，應用程式必須執行互動式 ```AcquireToken``` 要求，讓使用者有機會遵守原則。

## <a name="scenario-single-page-app-spa-using-msaljs"></a>案例：使用 MSAL.js 的單頁應用程式 (SPA)

在此情節中，我們會使用 MSAL.js 來呼叫受條件式存取保護的 Web API，逐步解說具有單頁應用程式 (SPA) 時的案例。 這是簡單的架構，但在對條件式存取進行開發時，有一些需要加以考量的細微差別。

在 MSAL.js 中，有幾個函式可取得權杖：`loginPopup()`、`acquireTokenSilent(...)`、`acquireTokenPopup(…)` 和 `acquireTokenRedirect(…)`。

* `loginPopup()` 會透過互動式登入要求取得 ID 權杖，但不會取得任何服務的存取權杖 (包括受條件式存取保護的 Web API)。
* 接著可以使用 `acquireTokenSilent(…)`，以無訊息方式取得存取權杖，這表示它在任何情況下都不會顯示 UI。
* `acquireTokenPopup(…)` 和 `acquireTokenRedirect(…)` 都是用來以互動方式要求資源的權杖，這表示它們一律會顯示登入 UI。

當應用程式需要存取權杖來呼叫 Web API 時，其會嘗試 `acquireTokenSilent(…)`。 如果權杖工作階段已過期，或是我們必須遵守條件式存取原則，acquireToken 函式就會失敗，且應用程式會使用 `acquireTokenPopup()` 或 `acquireTokenRedirect()`。

![使用 MSAL 的單頁應用程式流程圖](./media/v2-conditional-access-dev-guide/spa-using-msal-scenario.png)

我們會使用條件式存取情節來步解說範例。 使用者只需登陸網站，且沒有工作階段。 我們執行 `loginPopup()` 呼叫時，無需進行多重要素驗證即可取得 ID 權杖。 然後使用者會叫用按鈕，要求應用程式向 web API 要求資料。 應用程式嘗試執行 `acquireTokenSilent()` 呼叫但失敗，因為使用者尚未執行多重要素驗證，且必須遵守條件式存取原則。

Azure AD 會傳回下列 HTTP 回應：

```
HTTP 400; Bad Request
error=interaction_required
error_description=AADSTS50076: Due to a configuration change made by your administrator, or because you moved to a new location, you must use multi-factor authentication to access '<Web API App/Client ID>'.
```

我們的應用程式必須攔截 `error=interaction_required`。 應用程式就可以在相同的資源上使用 `acquireTokenPopup()` 或 `acquireTokenRedirect()`。 系統會強制使用者執行多重要素驗證。 使用者完成多重要素驗證之後，就會發出新的存取權杖給應用程式，以取得要求的資源。

若要試用此情節，請參閱我們的 [JS SPA 代理者程式碼範例](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/blob/a2b257381b410c765ee01ecb611aa6f98c099eb1/2.%20Web%20API%20now%20calls%20Microsoft%20Graph/README.md)。 此程式碼範例會使用條件式存取原則，以及您稍早向 JS SPA 註冊以示範此情節的 Web API。 其會示範如何正確處理宣告挑戰，並取得可用於您 Web API 的存取權杖。 或者，查看一般 [Angular.js 程式碼範例](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2)，以取得 Angular SPA 的指引

## <a name="see-also"></a>另請參閱

* 若要深入了解功能，請參閱 [Azure Active Directory 中的條件式存取](/azure/active-directory/conditional-access/overview)。
* 如需更多的 Azure AD 程式碼範例，請參閱[範例](sample-v2-code.md)。
* 如需 MSAL SDK 的詳細資訊及存取參考文件，請參閱 [Microsoft 驗證程式庫概觀](msal-overview.md)。
* 若要深入了解多租用戶情節，請參閱[如何使用多租用戶模式將使用者登入](howto-convert-app-to-be-multi-tenant.md)。
* 深入了解[條件式存取及保護 IoT 應用程式的存取權](/azure/architecture/example-scenario/iot-aad/iot-aad)。
