---
title: Azure 活動目錄條件訪問的開發人員指南
description: Azure AD 條件訪問和 Microsoft 標識平臺的開發人員指南和方案。
services: active-directory
keywords: ''
author: rwike77
manager: CelesteDG
ms.author: ryanwi
ms.reviewer: jmprieur, saeeda
ms.date: 03/16/2020
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.collection: M365-identity-device-management
ms.openlocfilehash: a9d82840681450ec855cb35c8700da2a53b9dd6b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79481444"
---
# <a name="developer-guidance-for-azure-active-directory-conditional-access"></a>Azure 活動目錄條件訪問的開發人員指南

Azure 活動目錄 （Azure AD） 中的條件訪問功能提供了可用於保護應用和保護服務的幾種方法之一。 條件訪問使開發人員和企業客戶能夠以多種方式保護服務，包括：

* Multi-Factor Authentication
* 只允許已註冊 Intune 的裝置存取特定服務
* 限制使用者位置及 IP 範圍

有關條件訪問的全部功能的詳細資訊，請參閱[Azure 活動目錄中的條件訪問](../active-directory-conditional-access-azure-portal.md)。

對於為 Azure AD 構建應用的開發人員，本文演示如何使用條件訪問，還將瞭解訪問可能應用條件訪問策略的資源的影響。 本文還探討了條件訪問在代理流、Web 應用、訪問 Microsoft 圖形和調用 API 中的含義。

它假設對於[單一](quickstart-register-app.md)和[多租用戶](howto-convert-app-to-be-multi-tenant.md)應用程式以及[常見驗證模式](authentication-scenarios.md)的認知。

> [!NOTE]
> 使用此方法需要 Azure AD Premium P1 授權。 若要尋找適用於您需求的正確授權，請參閱[比較 Free、Basic 及 Premium 版本的正式運作功能](https://azure.microsoft.com/pricing/details/active-directory/)。
> 擁有 [Microsoft 365 商務版授權](/office365/servicedescriptions/microsoft-365-service-descriptions/microsoft-365-business-service-description)的客戶也有條件式存取功能的存取權。

## <a name="how-does-conditional-access-impact-an-app"></a>條件訪問如何影回應用？

### <a name="app-types-impacted"></a>受影響的應用程式類型

在大多數情況下，條件訪問不會更改應用的行為或需要開發人員進行任何更改。僅當應用直接或間接或默默請求服務權杖時，應用才需要更改代碼來處理條件訪問"挑戰"。這有如執行互動式登入要求一樣簡單。

具體而言，以下方案需要代碼來處理條件訪問"挑戰"：

* 執行代理者流程的應用程式
* 存取多個服務/資源的應用程式
* 使用 MSAL.js 的單頁應用
* 呼叫資源的 Web Apps

條件訪問策略可以應用於應用，也可以應用於應用訪問的 Web API。 要瞭解有關如何配置條件訪問策略的更多資訊，請參閱[快速入門：對於具有 Azure 活動目錄條件訪問的特定應用需要 MFA。](../conditional-access/app-based-mfa.md)

根據方案，企業客戶可以隨時應用和刪除條件訪問策略。 若要在套用新原則時讓您的應用程式繼續運作，您必須實作「挑戰」處理。 下列範例說明挑戰處理。

### <a name="conditional-access-examples"></a>條件訪問示例

某些方案需要代碼更改來處理條件訪問，而其他方案則以現在的工作方式工作。 下面是一些使用條件訪問進行多重要素驗證的方案，這些身份驗證提供了對差異的一些瞭解。

* 您正在構建單租戶 iOS 應用並應用條件訪問策略。 應用程式會將使用者登入，且不會要求存取 API。 當使用者登入時，會自動叫用原則，而使用者必須執行多重要素驗證 (MFA)。 
* 您正在建置一個會使用中介層服務來存取下游 API 的原生應用程式。 公司中使用此應用程式的企業客戶會將原則套用至下游 API。 當使用者登入時，原生應用程式會要求存取中介層並傳送權杖。 中介層會執行代理者流程來要求存取下游 API。 此時，宣告「挑戰」會呈現至中介層。 中介層將質詢發送回本機應用，該應用需要遵守條件訪問策略。

#### <a name="microsoft-graph"></a>Microsoft Graph

在條件訪問環境中構建應用時，Microsoft 圖形具有特殊注意事項。 通常，條件存取機制執行相同的行為，但使用者看到的策略將基於應用從圖形請求的基礎資料。 

具體而言，所有 Microsoft 圖形作用域表示可以單獨應用策略的某些資料集。 由於為條件訪問策略分配了特定的資料集，Azure AD 將基於圖形背後的資料強制實施條件訪問策略，而不是圖形本身。

例如，如果應用請求以下 Microsoft 圖形作用域，

```
scopes="Bookings.Read.All Mail.Read"
```

應用可以期望其使用者完成在預訂和交換上設置的所有策略。 如果某些作用域授予存取權限，則可能會映射到多個資料集。 

### <a name="complying-with-a-conditional-access-policy"></a>遵守條件訪問策略

對於多個不同的應用拓撲，在建立會話時評估條件訪問策略。 當條件訪問策略根據應用和服務的細微性進行操作時，調用它時，很大程度上取決於您嘗試完成的方案。

當應用嘗試使用條件訪問策略訪問服務時，它可能會遇到條件訪問挑戰。 此質詢在 Azure AD`claims`回應中的參數中編碼。 以下是這項挑戰參數的範例： 

```
claims={"access_token":{"polids":{"essential":true,"Values":["<GUID>"]}}}
```

開發人員可以使用這項挑戰，並將其附加到 Azure AD 的新要求。 傳遞此狀態會提示最終使用者執行符合條件訪問策略所需的任何操作。 在下列情節中，會說明錯誤的詳細規格和擷取參數的方式。

## <a name="scenarios"></a>案例

### <a name="prerequisites"></a>Prerequisites

Azure AD 條件訪問是[Azure AD 高級版](https://docs.microsoft.com/azure/active-directory/active-directory-whatis)中包含的功能。 擁有 [Microsoft 365 商務版授權](/office365/servicedescriptions/microsoft-365-service-descriptions/microsoft-365-business-service-description)的客戶也有條件式存取功能的存取權。

### <a name="considerations-for-specific-scenarios"></a>特定情節的考量

以下資訊僅適用于這些條件訪問方案：

* 執行代理者流程的應用程式
* 存取多個服務/資源的應用程式
* 使用 MSAL.js 的單頁應用

下列各節會討論更複雜的常見案例。 核心操作原則是條件訪問策略在請求應用條件訪問策略的服務時權杖時進行評估。

## <a name="scenario-app-performing-the-on-behalf-of-flow"></a>情節：執行代理者流程的應用程式

在此情節中，我們會逐步解說原生應用程式呼叫 web 服務/API 的案例。 接著，此服務會執行「代理者」流程來呼叫下游服務。 在我們的案例中，我們已經將條件訪問策略應用於下游服務（Web API 2），並使用本機應用而不是伺服器/守護進程應用。 

![執行代理者流程圖的應用程式](./media/v2-conditional-access-dev-guide/app-performing-on-behalf-of-scenario.png)

Web API 1 的初始權杖要求不會提示使用者進行多重要素驗證，因為 Web API 1 不一定會叫用下游 API。 一旦 Web API 1 嘗試要求權杖代理者使用者提供 Web API 2 後，要求就會失敗，因為使用者尚未使用多重要素驗證登入。

Azure AD 會傳回 HTTP 回應，包含一些有趣的資料：

> [!NOTE]
> 在這種情況下，它是一個多重要素驗證錯誤描述，但與條件訪問相關的`interaction_required`範圍很廣。

```
HTTP 400; Bad Request
error=interaction_required
error_description=AADSTS50076: Due to a configuration change made by your administrator, or because you moved to a new location, you must use multi-factor authentication to access '<Web API 2 App/Client ID>'.
claims={"access_token":{"polids":{"essential":true,"Values":["<GUID>"]}}}
```

在 Web API 1 中，攔截到 `error=interaction_required` 錯誤，並將 `claims` 挑戰傳回給桌面應用程式。 此時，桌面應用程式可以進行新的 `acquireToken()` 呼叫，並附加 `claims` 挑戰作為額外的查詢字串參數。 這個新的要求需要使用者執行多重要素驗證，然後將這個新的權杖傳回 Web API 1，才能完成代理者流程。

若要試用此情節，請參閱我們的 [.NET 程式碼範例](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/blob/master/Microsoft.Identity.Web/README.md#handle-conditional-access)。 它會示範如何從 Web API 1 將宣告挑戰傳回到原生應用程式，並在用戶端應用程式內建構新的要求。

## <a name="scenario-app-accessing-multiple-services"></a>情節：存取多個服務的應用程式

在這種情況下，我們將演練 Web 應用訪問兩個服務的情況，其中一個服務分配了條件訪問策略。 根據您的應用程式邏輯而定，可能有一個路徑是您的應用程式在其中不需要存取這兩個 web 服務。 在此案例中，您要求權杖的順序在使用者體驗中扮演重要的角色。

假設我們應用了 Web 服務 A 和 B，Web 服務 B 應用了條件訪問策略。 雖然初始互動式 auth 請求需要兩個服務的同意，但條件訪問策略並非在所有情況下都是必需的。 如果應用程式要求 web 服務 B 的權杖，就會叫用原則，且 web 服務 A 的後續要求也會成功，如下所示。

![存取多個服務的應用程式流程圖](./media/v2-conditional-access-dev-guide/app-accessing-multiple-services-scenario.png)

或者，如果應用最初請求 Web 服務 A 的權杖，最終使用者不會調用條件訪問策略。 這允許應用開發人員控制最終使用者體驗，而不是強制在所有情況下調用條件訪問策略。 棘手的情況是，如果應用隨後請求 Web 服務 B 的權杖。此時，最終使用者需要遵守條件訪問策略。 當應用程式嘗試 `acquireToken` 時，可能會產生下列錯誤 (如下列圖表所示)：

```
HTTP 400; Bad Request
error=interaction_required
error_description=AADSTS50076: Due to a configuration change made by your administrator, or because you moved to a new location, you must use multi-factor authentication to access '<Web API App/Client ID>'.
claims={"access_token":{"polids":{"essential":true,"Values":["<GUID>"]}}}
```

![存取多個要求新權杖之服務的應用程式](./media/v2-conditional-access-dev-guide/app-accessing-multiple-services-new-token.png)

如果應用正在使用 MSAL 庫，則始終以對話模式重試獲取權杖失敗。 發生此互動式請求時，最終使用者有機會遵守條件訪問。 這是事實，除非請求為`AcquireTokenSilentAsync`或`PromptBehavior.Never`在這種情況下，應用需要執行互動式```AcquireToken```請求，以便最終使用者有機會遵守策略。

## <a name="scenario-single-page-app-spa-using-msaljs"></a>方案：使用 MSAL.js 的單頁應用 （SPA）

在這種情況下，我們將演練使用 MSAL.js 調用條件訪問受保護的 Web API 的單頁應用 （SPA） 的情況。 這是一個簡單的體系結構，但在圍繞條件訪問進行開發時需要考慮一些細微差別。

在`loginPopup()`MSAL.js 中，有幾個函數獲取權杖： 、`acquireTokenSilent(...)`和`acquireTokenPopup(…)``acquireTokenRedirect(…)`。

* `loginPopup()`通過互動式登入請求獲取 ID 權杖，但不獲取任何服務（包括條件訪問受保護的 Web API）的訪問權杖。
* 接著可以使用 `acquireTokenSilent(…)`，以無訊息方式取得存取權杖，這表示它在任何情況下都不會顯示 UI。
* `acquireTokenPopup(…)` 和 `acquireTokenRedirect(…)` 都是用來以互動方式要求資源的權杖，這表示它們一律會顯示登入 UI。

當應用程式需要存取權杖來呼叫 Web API 時，它會嘗試 `acquireTokenSilent(…)`。 如果權杖會話已過期，或者我們需要遵守條件訪問策略，則*accessToken*函數將失敗，應用使用`acquireTokenPopup()`或`acquireTokenRedirect()`。

![使用 MSAL 流程圖的單頁應用](./media/v2-conditional-access-dev-guide/spa-using-msal-scenario.png)

讓我們通過條件訪問方案來演練一個示例。 使用者只需登陸網站，且沒有工作階段。 我們執行 `loginPopup()` 呼叫時，無需進行多重要素驗證即可取得 ID 權杖。 然後使用者會叫用按鈕，要求應用程式向 web API 要求資料。 應用嘗試執行調用，`acquireTokenSilent()`但失敗，因為使用者尚未執行多重要素驗證，並且需要遵守條件訪問策略。

Azure AD 會傳回下列 HTTP 回應：

```
HTTP 400; Bad Request
error=interaction_required
error_description=AADSTS50076: Due to a configuration change made by your administrator, or because you moved to a new location, you must use multi-factor authentication to access '<Web API App/Client ID>'.
```

我們的應用程式必須攔截 `error=interaction_required`。 應用程式就可以在相同的資源上使用 `acquireTokenPopup()` 或 `acquireTokenRedirect()`。 系統會強制使用者執行多重要素驗證。 使用者完成多重要素驗證之後，就會發出新的存取權杖給應用程式，以取得要求的資源。

若要試用此情節，請參閱我們的 [JS SPA 代理者程式碼範例](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/blob/master/Microsoft.Identity.Web/README.md#handle-conditional-access)。 此代碼示例使用之前在 JS SPA 中註冊的條件訪問策略和 Web API 來演示此方案。 它會示範如何正確處理宣告挑戰，並取得可用於您 Web API 的存取權杖。 或者，查看一般 [Angular.js 程式碼範例](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2)，以取得 Angular SPA 的指引

## <a name="see-also"></a>另請參閱

* 若要深入了解功能，請參閱 [Azure Active Directory 中的條件式存取](/azure/active-directory/conditional-access/overview)。
* 有關更多 Azure AD 代碼示例，請參閱[示例](sample-v2-code.md)。
* 有關 MSAL SDK 和訪問參考文檔的詳細資訊，請參閱 Microsoft[身份驗證庫概述](msal-overview.md)。
* 若要深入了解多租用戶情節，請參閱[如何使用多租用戶模式將使用者登入](howto-convert-app-to-be-multi-tenant.md)。
* 瞭解有關[條件訪問和保護對 IoT 應用的訪問](/azure/architecture/example-scenario/iot-aad/iot-aad)的更多資訊。
