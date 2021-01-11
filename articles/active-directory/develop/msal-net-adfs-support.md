---
title: MSAL.NET 中的 AD FS 支援 |蔚藍
titleSuffix: Microsoft identity platform
description: 在適用于 .NET 的 Microsoft 驗證程式庫 (MSAL.NET) 中，瞭解 Active Directory 同盟服務 (AD FS) 支援。
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/16/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: devx-track-csharp, aaddev
ms.openlocfilehash: a146b310e6056954ac2655ff2fd99e1e3d7c694f
ms.sourcegitcommit: 2488894b8ece49d493399d2ed7c98d29b53a5599
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98063632"
---
# <a name="active-directory-federation-services-support-in-msalnet"></a>MSAL.NET 中的 Active Directory 同盟服務支援
Windows Server 中的 Active Directory 同盟服務 (AD FS) 可讓您將 OpenID Connect 和 OAuth 2.0 型驗證和授權新增到您正在開發的應用程式。 然後，這些應用程式可以直接對 AD FS 驗證使用者。 如需詳細資訊，請參閱 [開發人員 AD FS 案例](/windows-server/identity/ad-fs/overview/ad-fs-openid-connect-oauth-flows-scenarios)。

適用于 .NET 的 Microsoft 驗證程式庫 (MSAL.NET) 支援兩種驗證 AD FS 的案例：

- MSAL.NET 會與 Azure Active Directory 交談，其本身 *會與 AD FS* 同盟。
- MSAL.NET 會 **直接** 與 ADFS 授權單位交談。 只有 AD FS 2019 和更新版本才支援此功能。 其中一個重點是 [Azure Stack](https://azure.microsoft.com/overview/azure-stack/) 支援的案例


## <a name="msal-connects-to-azure-ad-which-is-federated-with-ad-fs"></a>MSAL 會連接到與 AD FS 同盟的 Azure AD
MSAL.NET 支援連線到 Azure AD，其會將 Azure AD) 或同盟使用者所管理 (使用者登入或同盟使用者， (由其他身分識別提供者（例如 AD FS) ）管理的使用者。 MSAL.NET 並不知道使用者的同盟事實。 至於這一點，它會與 Azure AD 交談。

在此情況下，您使用的 [授權](msal-client-application-configuration.md#authority) 單位是一般授權 (授權單位主機名稱 + 租使用者、一般或組織) 。

### <a name="acquiring-a-token-interactively"></a>以互動方式取得權杖
當您呼叫 `AcquireTokenInteractive` 方法時，使用者體驗通常是：

1. 使用者輸入他們的帳戶識別碼。
2. Azure AD 會短暫顯示「將您帶到您的組織頁面」訊息。
3. 使用者會重新導向至身分識別提供者的登入頁面。 登入頁面通常會使用組織的標誌進行自訂。

此同盟案例中支援的 AD FS 版本為 AD FS v2、AD FS v3 (Windows Server 2012 R2) 和 AD FS v4 (AD FS 2016) 。

### <a name="acquiring-a-token-using-acquiretokenbyintegratedauthentication-or-acquiretokenbyusernamepassword"></a>使用 AcquireTokenByIntegratedAuthentication 或 AcquireTokenByUsernamePassword 取得權杖
使用 `AcquireTokenByIntegratedAuthentication` 或 `AcquireTokenByUsernamePassword` 方法取得權杖時，MSAL.NET 會根據使用者名稱取得要聯絡的身分識別提供者。  MSAL.NET 在聯繫身分識別提供者之後，會收到 [SAML 1.1 權杖](reference-saml-tokens.md) 。  接著，MSAL.NET 會提供 SAML 權杖，Azure AD 為使用者判斷提示 (類似于代理者 [流程](msal-authentication-flows.md#on-behalf-of)) 以取得 JWT。

## <a name="msal-connects-directly-to-ad-fs"></a>MSAL 會直接連接到 AD FS
MSAL.NET 支援連線至 AD FS 2019，這是符合 Open ID Connect 規範且瞭解 PKCE 和範圍。 這項支援要求 service pack [KB 4490481](https://support.microsoft.com/en-us/help/4490481/windows-10-update-kb4490481) 必須套用到 Windows Server。 直接連接到 AD FS 時，您要用來建立應用程式的授權單位類似于 `https://mysite.contoso.com/adfs/` 。

目前沒有任何計畫可支援直接連接至：

- AD FS 16，因為它不支援 PKCE，但仍使用資源，而非範圍
- AD FS v2，不符合 OIDC 規範。

 如果您需要支援需要直接連線至 AD FS 2016 的案例，請使用最新版的 [Azure Active Directory Authentication Library](../azuread-dev/active-directory-authentication-libraries.md#microsoft-supported-client-libraries)。 當您將內部部署系統升級至 AD FS 2019 時，您將能夠使用 MSAL.NET。

## <a name="next-steps"></a>後續步驟

針對同盟案例，請參閱 [使用主領域探索原則設定應用程式的 Azure Active Directory 登入行為](../manage-apps/configure-authentication-for-federated-users-portal.md)