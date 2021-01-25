---
title: MSAL.js 和 ADAL.js 之間的差異 | Azure
titleSuffix: Microsoft identity platform
description: 了解適用於 JavaScript 的 Microsoft 驗證程式庫 (MSAL.js) 與適用於 JavaScript 的 Azure AD 驗證程式庫 (ADAL.js) 之間的差異，以及如何選擇要使用哪一個。
services: active-directory
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/10/2019
ms.author: nacanuma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: a1ec8c31681ab6c0ac40ba33e94f33057ee948e7
ms.sourcegitcommit: 5cdd0b378d6377b98af71ec8e886098a504f7c33
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/25/2021
ms.locfileid: "98754792"
---
# <a name="differences-between-msaljs-and-adaljs"></a>MSAL.js 與 ADAL.js 之間的差異

適用于 JavaScript 的 Microsoft 驗證程式庫 ( # A0) 和適用于 JavaScript 的 Azure AD 驗證程式庫 ( # A1) 可用來從 Azure AD 驗證 Azure AD 實體和要求權杖。 到目前為止，大多數開發人員都已經使用 ADAL 要求權杖，進而使用開發人員適用的 Azure AD (v1.0) 驗證 Azure AD 身分識別 (公司和學校帳戶)。 現在，您可以使用 MSAL.js，透過 Microsoft 身分識別平臺，透過 Azure AD B2C) 驗證一組更廣泛的 Microsoft 身分識別 (Azure AD 身分識別和 Microsoft 帳戶，以及社交和本機帳戶。

本文描述如何在適用於 JavaScript 的 Microsoft 驗證程式庫 (MSAL.js) 和適用於 JavaScript 的 Azure AD 驗證程式庫 (ADAL.js) 之間做選擇，並比較這兩個程式庫。

## <a name="choosing-between-adaljs-and-msaljs"></a>在 ADAL.js 與 MSAL.js 之間選擇

在大部分情況下，您想使用 Microsoft 身分識別平台和 MSAL.js，也就是最新一代的 Microsoft 驗證程式庫。 您可以使用 MSAL.js 取得權杖，以供使用者利用 Azure AD (公司和學校帳戶)、Microsoft (個人) 帳戶 (MSA) 或 Azure AD B2C 登入您的應用程式。

如果您已熟悉 v1.0 端點 (和 ADAL.js)，您可以閱讀 [V2.0 端點有哪些差異？](../azuread-dev/azure-ad-endpoint-comparison.md)。

不過，如果您的應用程式需要使用舊版的 [Active Directory 同盟服務 (ADFS)](/windows-server/identity/active-directory-federation-services) 登入使用者，您仍然需要使用 ADAL.js。

## <a name="key-differences-in-authentication-with-msaljs"></a>使用 MSAL.js 進行驗證的主要差異

### <a name="core-api"></a>Core API

* ADAL.js 會使用 [AuthenticationContext](https://github.com/AzureAD/azure-activedirectory-library-for-js/wiki/Config-authentication-context#authenticationcontext) 作為應用程式連線至授權伺服器，或透過授權單位 URL 連線至身分識別提供者的執行個體表示法。 相反地，MSAL.js API 專為使用者代理程式用戶端應用程式 (公用用戶端應用程式的一種形式，可在網頁瀏覽器之類的使用者代理程式中執行用戶端程式碼) 而設計。 它會提供 `UserAgentApplication` 類別來表示應用程式向授權伺服器驗證之內容的執行個體。 如需詳細資料，請參閱[使用 MSAL.js 初始化](msal-js-initializing-client-applications.md)。

* 在 ADAL.js 中，取得權杖的方法會與 `AuthenticationContext` 中設定的單一授權單位相關聯。 在 0MSAL.js 中，取得權杖要求可能會採用不同於 `UserAgentApplication` 中設定的授權值。 這可讓 MSAL.js 分別針對相同應用程式中的多個租用戶和使用者帳戶，取得並快取權杖。

* 在不提示使用者的情況下，於 ADAL.js 中，以無訊息方式取得並更新權杖的方法稱為 `acquireToken`。 在 MSAL.js 中，此方法稱為 `acquireTokenSilent`，更能描述這項功能。

### <a name="authority-value-common"></a>授權值 `common`

在 v1.0 中使用 `https://login.microsoftonline.com/common` 授權單位可讓使用者使用任何 Azure AD 帳戶 (適用於任何組織) 登入。

在 v2.0 中使用 `https://login.microsoftonline.com/common` 授權單位可讓使用者使用任何 Azure AD 組織帳戶或 Microsoft 個人帳戶 (MSA) 登入。 若要限制僅使用 Azure AD 帳戶 (與使用 ADAL.js 的行為相同) 登入，您需要使用 `https://login.microsoftonline.com/organizations`。 如需詳細資料，請參閱[使用 MSAL.js 初始化](msal-js-initializing-client-applications.md)中的 `authority` 組態選項。

### <a name="scopes-for-acquiring-tokens"></a>取得權杖的範圍
* 而不是在取得權杖的驗證要求中資源參數的範圍

    v2.0 通訊協定會使用範圍而非要求中的資源。 換句話說，當您的應用程式需要使用資源 (例如 MS Graph) 的權限要求權杖時，傳遞至程式庫方法的值差異如下所示：

    v1.0: resource = https\://graph.microsoft.com

    v2.0: scope = https\://graph.microsoft.com/User.Read

    您可以使用以下格式之 API 的 URI，要求任何資源 API 的範圍：appidURI/scope，例如：https:\//mytenant.onmicrosoft.com/myapi/api.read

    只有在 MS 圖形 API，範圍值 `user.read` 會對應至 HTTPs： \/ /graph.microsoft.com/User.Read，而且可以交換使用。

    ```javascript
    var request = {
        scopes = ["User.Read"];
    };

    acquireTokenPopup(request);   
    ```

* 累加式同意的動態範圍。

    使用 v1.0 建置應用程式時，您必須註冊應用程式所需的一組完整權限 (靜態範圍)，讓使用者在登入時同意。 在 v2.0 中，您可以使用範圍參數，在您需要時要求權限。 這些稱為動態範圍。 這可讓使用者對範圍提供累加式同意。 因此，如果一開始您只是希望使用者登入您的應用程式，而且您不需要任何一種存取，就可以這麼做。 之後，如果您需要能夠讀取使用者的行事曆，您可以在取得權杖方法中要求行事曆範圍，並取得使用者的同意。 例如：

    ```javascript
    var request = {
        scopes = ["https://graph.microsoft.com/User.Read", "https://graph.microsoft.com/Calendar.Read"];
    };

    acquireTokenPopup(request);   
    ```

* V1.0 API 的範圍

    使用 MSAL.js 取得 V1.0 API 的權杖時，您可以將 `.default` 附加到 API 的應用程式識別碼 URI 作為範圍，以要求在 API 上註冊的所有靜態範圍。 例如：

    ```javascript
    var request = {
        scopes = [ appidURI + "/.default"];
    };

    acquireTokenPopup(request);
    ```

## <a name="next-steps"></a>後續步驟
如需詳細資訊，請參閱 [v1.0 和 v2.0 的比較](../azuread-dev/azure-ad-endpoint-comparison.md)。
