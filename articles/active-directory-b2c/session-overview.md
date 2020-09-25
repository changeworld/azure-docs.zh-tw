---
title: Azure Active Directory B2C 中的 SSO 會話 |Microsoft Docs
description: 在 Azure Active Directory B2C 中設定會話行為的總覽。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 05/07/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 0004c874a2011a78bb5cfe67ff0a840224d47bbb
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91258960"
---
# <a name="azure-ad-b2c-session"></a>Azure AD B2C 會話

單一登入 (SSO) 在使用者于 Azure Active Directory B2C (Azure AD B2C) 的應用程式之間登入時，增加安全性和便利性。 本文說明 Azure AD B2C 中使用的單一登入方法，並協助您在設定原則時選擇最適當的 SSO 方法。

使用單一登入時，使用者只要使用單一帳戶登入一次，就可以存取多個應用程式。 無論平臺或功能變數名稱為何，應用程式都可以是 web、行動或單頁應用程式。

當使用者一開始登入應用程式時，Azure AD B2C 保存以 cookie 為基礎的會話。 在後續的驗證要求中，Azure AD B2C 會讀取並驗證以 cookie 為基礎的會話，並在不提示使用者再次登入的情況下發出存取權杖。 如果以 cookie 為基礎的會話過期或變成無效，系統會提示使用者重新登入。  

## <a name="sso-session-types"></a>SSO 會話類型

與 Azure AD B2C 的整合牽涉到三種類型的 SSO 會話：

- **Azure AD B2C** -由 Azure AD B2C 管理的會話
- 同盟**識別提供者**-由身分識別提供者管理的會話，例如 Facebook、Salesforce 或 Microsoft 帳戶
- Web、行動或單一頁面應用程式所管理的**應用程式**會話

![SSO 工作階段](media/session-overview/sso-session-types.png)

### <a name="azure-ad-b2c-sso"></a>Azure AD B2C SSO 

當使用者使用本機或社交帳戶成功驗證時，Azure AD B2C 會在使用者的瀏覽器上儲存以 cookie 為基礎的會話。 Cookie 會儲存在 Azure AD B2C 租使用者功能變數名稱下，例如 `https://contoso.b2clogin.com` 。

如果使用者一開始以同盟帳戶登入，然後在會話時間範圍內 (存留時間或 TTL) 登入相同的應用程式或不同的應用程式，Azure AD B2C 會嘗試從同盟身分識別提供者取得新的存取權杖。 如果同盟識別提供者會話已過期或無效，同盟身分識別提供者會提示使用者提供其認證。 如果會話仍在使用中 (或使用者已使用本機帳戶（而非同盟帳戶) ）登入，Azure AD B2C 授權使用者並消除進一步的提示。

您可以設定會話行為，包括會話 TTL，以及 Azure AD B2C 如何在原則和應用程式之間共用會話。

### <a name="federated-identity-provider-sso"></a>同盟識別提供者 SSO

社交或企業身分識別提供者會管理自己的會話。 Cookie 會儲存在身分識別提供者的功能變數名稱下，例如 `https://login.salesforce.com` 。 Azure AD B2C 不會控制同盟身分識別提供者會話。 相反地，會話行為是由同盟身分識別提供者所決定。 

請考慮下列案例：

1. 使用者登入 Facebook 以檢查其摘要。
2. 之後，使用者會開啟您的應用程式，並開始登入程式。 應用程式會將使用者重新導向至 Azure AD B2C，以完成登入程式。
3. 在 [Azure AD B2C 註冊或登入] 頁面上，使用者會選擇使用其 Facebook 帳戶登入。 使用者會被重新導向至 Facebook。 如果 Facebook 上有使用中的會話，系統就不會提示使用者提供其認證，並會立即以 Facebook 權杖將其重新導向至 Azure AD B2C。

### <a name="application-sso"></a>應用程式 SSO

您可以透過 OAuth 存取、識別碼權杖或 SAML 權杖來保護 web、行動或單一頁面應用程式。 當使用者嘗試存取應用程式上的受保護資源時，應用程式會檢查應用程式端是否有作用中的會話。 如果沒有任何應用程式會話或會話已過期，應用程式將會讓使用者 Azure AD B2C 登入頁面。

應用程式會話可以是儲存在應用程式功能變數名稱下的以 cookie 為基礎的會話，例如 `https://contoso.com` 。 行動應用程式可能會以不同的方式儲存會話，但使用類似的方法。

## <a name="azure-ad-b2c-session-configuration"></a>Azure AD B2C 會話設定

### <a name="session-scope"></a>工作階段範圍

您可以使用下列範圍設定 Azure AD B2C 會話：

- **Tenant** - 這項設定是預設值。 此設定可允許 B2C 租用戶中的多個應用程式和使用者流程共用同一個使用者工作階段。 例如，一旦使用者登入應用程式，使用者也可以在存取應用程式時順暢地登入另一個應用程式。
- **應用程式** - 這項設定可讓您保留應用程式專用的使用者工作階段，不受其他應用程式所限制。 例如，如果您想要讓使用者登入 Contoso 藥房，不論使用者是否已登入 Contoso Groceries，您都可以使用此設定。
- **原則** - 這項設定可讓您保留使用者流程專用的使用者工作階段，不論使用該使用者工作階段的應用程式為何。 例如，如果使用者已登入並完成多重要素驗證 (MFA) 步驟，則只要系結至使用者流程的會話未過期，使用者就可以存取多個應用程式的更高安全性部分。
- **停用** -此設定會強制使用者在每次原則執行時，執行整個使用者流程。

### <a name="session-life-time"></a>會話存留時間

**會話的存留時間**是在成功驗證之後，Azure AD B2C 會話 cookie 儲存在使用者瀏覽器上的時間量。 您可以將會話存留時間設定為15到720分鐘的值。

### <a name="keep-me-signed-in"></a>讓我保持登入

「 [讓我保持登入](custom-policy-keep-me-signed-in.md) 」功能可透過使用持續性 cookie 來延長會話生命週期。 在使用者關閉並重新開啟瀏覽器之後，會話會保持作用中狀態。 只有當使用者登出時，才會撤銷此會話。[讓我保持登入] 功能僅適用于以本機帳戶登入。

「讓我保持登入」功能的優先順序高於會話的存留時間。 如果已啟用「讓我保持登入」功能，且使用者選取該功能，則此功能會規定會話將到期的時間。 

### <a name="session-expiry-type"></a>會話到期類型

**會話到期類型**表示會話如何透過會話生命時間設定或 [讓我保持登入] 設定來延長。

- 輪流 **-指出**每次使用者執行以 cookie 為基礎的驗證 (預設) 時，會將會話延伸。
- [**絕對**]-表示在指定的時間週期之後，會強制使用者重新驗證。

## <a name="sign-out"></a>登出

當您想要將使用者登出應用程式時，並沒有足夠的方式可清除應用程式的 cookie 或結束使用者的會話。 您必須將使用者重新導向至 Azure AD B2C 才能登出。否則，使用者可能可以重新驗證您的應用程式，而不需要再次輸入認證。

登出要求時 Azure AD B2C：

1. 使 Azure AD B2C cookie 型會話失效。
1. 嘗試從同盟身分識別提供者登出：
   - OpenId Connect-如果識別提供者的知名設定端點指定一個 `end_session_endpoint` 位置。
   - SAML-如果識別提供者中繼資料包含 `SingleLogoutService` 位置。
1. （選擇性）登出其他應用程式。 如需詳細資訊，請參閱 [單一登出](#single-sign-out) 一節。

登出會使用 Azure AD B2C 來清除使用者的單一登入狀態，但可能不會將使用者登出其社交身分識別提供者會話。 如果使用者在後續登入時選取相同的身分識別提供者，他們可能會在未輸入認證的情況下重新驗證。 如果使用者想要登出應用程式，則不一定表示他們想要登出其 Facebook 帳戶。 但是，如果使用本機帳戶，使用者的會話就會正確結束。

### <a name="single-sign-out"></a>單一登出 


> [!NOTE]
> 這項功能僅限於 [自訂原則](custom-policy-overview.md)。

當您將使用者重新導向至 OAuth2 和 SAML 通訊協定) 的 Azure AD B2C 登出端點 (時，Azure AD B2C 會從瀏覽器清除使用者的會話。 不過，使用者仍可能登入使用 Azure AD B2C 進行驗證的其他應用程式。 若要讓這些應用程式同時將使用者登出，Azure AD B2C 將 HTTP GET 要求傳送至 `LogoutUrl` 使用者目前登入之所有應用程式的註冊。


應用程式必須藉由清除任何可識別使用者的工作階段並傳回 `200` 回應，以回應此要求。 如果您想要在應用程式中支援單一登出，您必須 `LogoutUrl` 在應用程式的程式碼中執行。 

## <a name="next-steps"></a>後續步驟

- 瞭解如何 [設定使用者流程中的會話行為](session-behavior.md)。
- 瞭解如何 [設定自訂原則中的會話行為](session-behavior-custom-policy.md)。