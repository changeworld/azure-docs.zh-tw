---
title: Azure Active Directory B2C 中的 SSO 會話 |Microsoft Docs
description: 在 Azure Active Directory B2C 中設定會話行為。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 05/07/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: ea8c40faad4ee709ae98f868e36fd42e46501bea
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "82927032"
---
# <a name="azure-ad-b2c-session"></a>Azure AD B2C 會話

當使用者在 Azure Active Directory B2C （Azure AD B2C）中跨應用程式登入時，單一登入（SSO）會增加安全性和便利性。 本文說明在 Azure AD B2C 中使用的單一登入方法，並協助您在設定原則時選擇最適當的 SSO 方法。

使用單一登入時，使用者可以使用單一帳戶登入一次，並取得多個應用程式的存取權。 應用程式可以是 web、行動或單一頁面應用程式，而不論平臺或功能變數名稱。

當使用者一開始登入應用程式時，Azure AD B2C 會保存以 cookie 為基礎的會話。 在後續的驗證要求中，Azure AD B2C 會讀取和驗證以 cookie 為基礎的會話，併發出存取權杖，而不會提示使用者重新登入。 如果以 cookie 為基礎的會話過期或變得無效，則會提示使用者重新登入。  

## <a name="sso-session-types"></a>SSO 會話類型

與 Azure AD B2C 的整合牽涉到三種 SSO 會話類型：

- 由 Azure AD B2C 管理的**Azure AD B2C**會話
- 同盟**識別提供者**-由身分識別提供者（例如 Facebook、Salesforce 或 Microsoft 帳戶）管理的會話
- Web、行動或單一頁面應用程式所管理的**應用程式**會話

![SSO 工作階段](media/session-overview/sso-session-types.png)

### <a name="azure-ad-b2c-sso"></a>Azure AD B2C SSO 

當使用者使用本機或社交帳戶成功進行驗證時，Azure AD B2C 會在使用者的瀏覽器上儲存以 cookie 為基礎的會話。 Cookie 會儲存在 Azure AD B2C 的租使用者功能變數名稱底下，例如 `https://contoso.b2clogin.com` 。

如果使用者一開始以同盟帳戶登入，然後在會話時間範圍（存留時間或 TTL）登入相同的應用程式或不同的應用程式，Azure AD B2C 會嘗試從同盟識別提供者取得新的存取權杖。 如果同盟身分識別提供者會話已過期或無效，同盟身分識別提供者會提示使用者提供其認證。 如果會話仍在作用中（或使用者已使用本機帳戶登入，而不是同盟帳戶），Azure AD B2C 會授權使用者並排除進一步的提示。

您可以設定會話行為，包括會話 TTL，以及 Azure AD B2C 如何跨原則和應用程式共用會話。

### <a name="federated-identity-provider-sso"></a>同盟身分識別提供者 SSO

社交或企業身分識別提供者會管理自己的會話。 Cookie 會儲存在識別提供者的功能變數名稱底下，例如 `https://login.salesforce.com` 。 Azure AD B2C 不會控制同盟身分識別提供者會話。 相反地，會話行為是由同盟身分識別提供者所決定。 

請考慮下列案例：

1. 使用者登入 Facebook 以檢查其摘要。
2. 之後，使用者會開啟您的應用程式，並啟動登入處理。 應用程式會將使用者重新導向至 Azure AD B2C 來完成登入程式。
3. 在 Azure AD B2C 註冊或登入頁面上，使用者選擇使用其 Facebook 帳戶來登入。 使用者會重新導向至 Facebook。 如果 Facebook 上有作用中的會話，則不會提示使用者提供其認證，而且會立即重新導向至使用 Facebook 權杖 Azure AD B2C。

### <a name="application-sso"></a>應用程式 SSO

Web、行動或單一頁面應用程式可以透過 OAuth 存取、識別碼權杖或 SAML 權杖來保護。 當使用者嘗試存取應用程式上受保護的資源時，應用程式會檢查應用程式端是否有使用中的會話。 如果沒有應用程式會話，或會話已過期，應用程式將會讓使用者 Azure AD B2C 登入頁面。

應用程式會話可以是儲存在應用程式功能變數名稱下的以 cookie 為基礎的會話，例如 `https://contoso.com` 。 行動應用程式可能會以不同的方式儲存會話，但使用類似的方法。

## <a name="azure-ad-b2c-session-configuration"></a>Azure AD B2C 會話設定

### <a name="session-scope"></a>工作階段範圍

您可以使用下列範圍來設定 Azure AD B2C 會話：

- **Tenant** - 這項設定是預設值。 此設定可允許 B2C 租用戶中的多個應用程式和使用者流程共用同一個使用者工作階段。 例如，一旦使用者登入應用程式，使用者也可以在存取它時順暢地登入另一個。
- **應用程式** - 這項設定可讓您保留應用程式專用的使用者工作階段，不受其他應用程式所限制。 例如，如果您想要讓使用者登入 Contoso 藥房，無論使用者是否已登入 Contoso 雜貨商店，您都可以使用此設定。
- **原則** - 這項設定可讓您保留使用者流程專用的使用者工作階段，不論使用該使用者工作階段的應用程式為何。 例如，如果使用者已登入並完成多重要素驗證（MFA）步驟，只要系結至使用者流程的會話未過期，使用者就可以存取多個應用程式的更高安全性元件。
- **已停用**-這項設定會在每次原則執行時，強制使用者執行整個使用者流程。

### <a name="session-life-time"></a>會話存留時間

**會話的存留時間**是在驗證成功之後，將 Azure AD B2C 會話 cookie 儲存在使用者的瀏覽器上的時間量。 您可以將會話存留時間設定為15到720分鐘的值。

### <a name="keep-me-signed-in"></a>讓我保持登入

「[讓我保持登入](custom-policy-keep-me-signed-in.md)」功能會透過使用持續性的 cookie 來延伸會話的存留時間。 使用者關閉並重新開啟瀏覽器之後，會話仍會保持作用中狀態。 只有在使用者登出時，才會撤銷會話。[讓我保持登入] 功能僅適用于使用本機帳戶登入。

[讓我保持登入] 功能優先于會話的存留時間。 如果已啟用 [讓我保持登入] 功能，而且使用者選取它，則此功能會指示會話到期的時間。 

### <a name="session-expiry-type"></a>會話到期類型

**會話到期類型**指出會話如何由會話存留時間設定或 [讓我保持登入] 設定擴充。

- 輪流 **-指出**每次使用者執行以 cookie 為基礎的驗證（預設）時，都會擴充會話。
- **絕對**-表示在指定的時間週期之後，強制使用者重新驗證。

## <a name="sign-out"></a>登出

當您想要將使用者登出應用程式時，並不足以清除應用程式的 cookie 或結束使用者的會話。 您必須將使用者重新導向至 Azure AD B2C 來登出。否則，使用者可能可以重新驗證您的應用程式，而不需要再次輸入其認證。

登出要求時，Azure AD B2C：

1. 使 Azure AD B2C 以 cookie 為基礎的會話失效。
1. 嘗試登出同盟身分識別提供者：
   - OpenId Connect-如果識別提供者的知名設定端點指定了 `end_session_endpoint` 位置。
   - SAML-如果身分識別提供者中繼資料包含 `SingleLogoutService` 位置。
1. （選擇性）從其他應用程式登出。 如需詳細資訊，請參閱[單一登出](#single-sign-out)一節。

登出會使用 Azure AD B2C 來清除使用者的單一登入狀態，但它可能不會將使用者登出其社交身分識別提供者會話。 如果使用者在後續登入時選取相同的身分識別提供者，他們可能會重新驗證，而不需要輸入其認證。 如果使用者想要登出應用程式，則不一定表示他們想要登出其 Facebook 帳戶。 不過，如果使用本機帳戶，使用者的會話就會正確結束。

### <a name="single-sign-out"></a>單一登出 


> [!NOTE]
> 這項功能僅限於[自訂原則](custom-policy-overview.md)。

當您將使用者重新導向至 Azure AD B2C 登出端點（適用于 OAuth2 和 SAML 通訊協定）時，Azure AD B2C 會從瀏覽器清除使用者的會話。 不過，使用者可能仍然登入其他使用 Azure AD B2C 進行驗證的應用程式。 若要讓這些應用程式同時登出使用者，Azure AD B2C 將 HTTP GET 要求傳送至 `LogoutUrl` 使用者目前登入之所有應用程式的註冊。


應用程式必須藉由清除任何可識別使用者的工作階段並傳回 `200` 回應，以回應此要求。 如果您想要在應用程式中支援單一登出，您必須 `LogoutUrl` 在應用程式的程式碼中執行。 

## <a name="next-steps"></a>後續步驟

- 瞭解如何[在使用者流程中設定會話行為](session-behavior.md)。
- 瞭解如何[在自訂原則中設定會話行為](session-behavior-custom-policy.md)。