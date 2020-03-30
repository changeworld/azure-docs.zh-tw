---
title: Azure Active Directory 中的 Web API 應用程式
description: 說明何謂 Web API 應用程式，以及此應用程式類型的通訊協定流程、註冊與權杖到期等基本概念。
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.workload: identity
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: ryanwi
ms.reviewer: saeeda, jmprieur
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: 9cf5a9c81ca1d7a42a5a8e342dee55f335656c3e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80154418"
---
# <a name="web-api"></a>Web API

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

Web API 應用程式是必須從 Web API 取得資源的 Web 應用程式。 在此案例中，有兩個識別類型可供 Web 應用程式用來驗證和呼叫 Web API：

- **應用程式識別** - 此案例使用 OAuth 2.0 用戶端認證授與來驗證應用程式和存取 Web API。 使用應用程式識別時，Web API 只能偵測到 Web 應用程式正在呼叫它，因為 Web API 沒有收到關於使用者的任何資訊。 如果應用程式收到使用者的相關資訊，此資訊會透過應用程式通訊協定傳送，但未經過 Azure AD 簽署。 Web API 信任 Web 應用程式已驗證使用者。 基於這個理由，這種模式稱為受信任子系統。
- **委派的使用者識別** - 有兩種方式可實現此案例：OpenID Connect 和搭配機密用戶端的 OAuth 2.0 授權碼授與。 Web 應用程式會為使用者取得存取權杖，向 Web API 證明使用者已順利通過 Web 應用程式的驗證，而且 Web 應用程式能夠取得委派的使用者識別來呼叫 Web API。 此存取權杖隨著要求傳送至 Web API，Web API 再授權使用者並傳回所需的資源。

下列流程中討論應用程式識別和委派的使用者識別類型。 它們之間的主要差異是委派的使用者識別必須先取得授權碼，使用者才能登入並存取 Web API。

## <a name="diagram"></a>圖表

![Web 應用程式到 Web API 圖表](./media/authentication-scenarios/web-app-to-web-api.png)

## <a name="protocol-flow"></a>通訊協定流程

### <a name="application-identity-with-oauth-20-client-credentials-grant"></a>採用 OAuth 2.0 用戶端認證授與的應用程式識別碼

1. 使用者在 Web 應用程式中登入 Azure AD (如需詳細資訊，請參閱 **Web 應用程式**一節)。
1. Web 應用程式需要取得存取權杖，才能向 Web API 驗證和擷取所需的資源。 它向 Azure AD 的權杖終結點發出請求，提供憑據、應用程式 ID 和 Web API 的應用程式 ID URI。
1. Azure AD 驗證應用程式，並傳回用來呼叫 Web API 的 JWT 存取權杖。
1. 通過 HTTPS，Web 應用程式使用返回的 JWT 訪問權杖將 JWT 字串添加具有"承載"名稱的請求到 Web API 的授權標頭中的"承載"名稱。 接著，Web API 驗證 JWT 權杖，如果驗證成功，則傳回所需的資源。

### <a name="delegated-user-identity-with-openid-connect"></a>採用 OpenID Connect 的委派使用者識別

1. 使用者使用 Azure AD 登入 Web 應用程式 (請參閱上方的＜Web 瀏覽器到 Web 應用程式＞一節)。 如果 Web 應用程式的使用者尚未同意允許 Web 應用程式代表他來呼叫 Web API，使用者必須同意。 應用程式會顯示它需要的權限，如果其中任何一項是系統管理員層級權限，則目錄中的一般使用者將無法同意。 此同意程序僅適用於多租用戶應用程式，而非單一租用戶應用程式，因為應用程式已具有必要的權限。 使用者登入後，Web 應用程式會收到識別碼權杖和使用者相關資訊，以及授權碼。
1. 使用 Azure AD 頒發的授權代碼，Web 應用程式向 Azure AD 的權杖終結點發送請求，其中包括授權代碼、有關用戶端應用程式的詳細資訊（應用程式 ID 和重定向 URI）以及所需的資源（應用程式 ID）用於 Web API 的 URI）。
1. Azure AD 驗證授權碼及 Web 應用程式和 Web API 的相關資訊。 成功驗證後，Azure AD 會傳回兩個權杖：JWT 存取權杖和 JWT 重新整理權杖。
1. 通過 HTTPS，Web 應用程式使用返回的 JWT 訪問權杖將 JWT 字串添加具有"承載"名稱的請求到 Web API 的授權標頭中的"承載"名稱。 接著，Web API 驗證 JWT 權杖，如果驗證成功，則傳回所需的資源。

### <a name="delegated-user-identity-with-oauth-20-authorization-code-grant"></a>採用 OAuth 2.0 授權碼授與的委派使用者識別

1. 使用者已經登入 Web 應用程式，其驗證機制與 Azure AD 無關。
1. Web 應用程式需要授權代碼才能獲取訪問權杖，因此它通過瀏覽器向 Azure AD 的授權終結點發出請求，在成功身份驗證後為 Web 應用程式提供應用程式 ID 並重定向 URI。 使用者登入 Azure AD。
1. 如果 Web 應用程式的使用者尚未同意允許 Web 應用程式代表他來呼叫 Web API，使用者必須同意。 應用程式會顯示它需要的權限，如果其中任何一項是系統管理員層級權限，則目錄中的一般使用者將無法同意。 此同意適用於單一和多租用戶應用程式。 在單一租用戶案例中，管理員可以代表其使用者執行要同意的管理員同意。 這可以使用[Azure](https://portal.azure.com)門戶`Grant Permissions`中的按鈕完成。 
1. 使用者同意之後，Web 應用程式會收到它取得存取權杖所需的授權碼。
1. 使用 Azure AD 頒發的授權代碼，Web 應用程式向 Azure AD 的權杖終結點發送請求，其中包括授權代碼、有關用戶端應用程式的詳細資訊（應用程式 ID 和重定向 URI）以及所需的資源（應用程式 ID）用於 Web API 的 URI）。
1. Azure AD 驗證授權碼及 Web 應用程式和 Web API 的相關資訊。 成功驗證後，Azure AD 會傳回兩個權杖：JWT 存取權杖和 JWT 重新整理權杖。
1. 通過 HTTPS，Web 應用程式使用返回的 JWT 訪問權杖將 JWT 字串添加具有"承載"名稱的請求到 Web API 的授權標頭中的"承載"名稱。 接著，Web API 驗證 JWT 權杖，如果驗證成功，則傳回所需的資源。

## <a name="code-samples"></a>程式碼範例

請參閱「Web 應用程式到 Web API」案例的程式碼範例。 歡迎您經常回來看看，我們會隨時新增新的範例。 Web [應用程式到 Web API](sample-v1-code.md#web-applications-signing-in-users-calling-microsoft-graph-or-a-web-api-with-the-users-identity)。

## <a name="app-registration"></a>應用程式註冊

要將應用程式註冊到 Azure AD v1.0 終結點，請參閱[註冊應用](../develop/quickstart-register-app.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)。

* 單一租用戶 - 無論是應用程式識別或委派的使用者識別的情況，Web 應用程式和 Web API 都必須註冊在 Azure AD 的相同目錄中。 可以將 Web API 配置為公開一組許可權，這些許可權用於限制 Web 應用程式對其資源的訪問。 如果使用委派的使用者標識類型，則 Web 應用程式需要從 Azure 門戶中**的許可權中選擇所需的許可權到其他應用程式**下拉式功能表。 如果使用應用程式識別類型，則不需要此步驟。
* 多租用戶 - 首先，設定 Web 應用程式來指出它運作所需的權限。 當目的地目錄中的使用者或系統管理員同意應用程式時 (使得應用程式可供組織使用)，這份必要權限清單會顯示在對話方塊中。 有些應用程式只需要使用者層級權限，亦即組織中的任何使用者都可以同意應用程式。 其他應用程式需要系統管理員層級權限，亦即組織中的使用者無法同意應用程式。 只有目錄管理員才能對需要此權限層級的應用程式表示同意。 當使用者或系統管理員同意時，Web 應用程式和 Web API 都會註冊在他們的目錄中。

## <a name="token-expiration"></a>權杖到期

當 Web 應用程式使用其授權碼來取得 JWT 存取權杖時，它也會收到 JWT 重新整理權杖。 當存取權杖到期時，重新整理權杖可用來重新驗證使用者，而不需要他們再次登入。 然後，此重新整理權杖用來驗證使用者，結果會產生新的存取權杖和重新整理權杖。

## <a name="next-steps"></a>後續步驟

- 深入了解其他[應用程式類型和案例](app-types.md)
- 瞭解 Azure AD[身份驗證基礎知識](v1-authentication-scenarios.md)
