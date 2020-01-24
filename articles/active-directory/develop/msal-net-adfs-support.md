---
title: MSAL.NET 中的 AD FS 支援 |Azure
titleSuffix: Microsoft identity platform
description: 瞭解適用于 .NET 的 Microsoft 驗證程式庫（MSAL.NET）中的 Active Directory 同盟服務（AD FS）支援。
services: active-directory
author: TylerMSFT
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/16/2019
ms.author: twhitney
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 6e40a03e70fa04855037165462761c56f13acc58
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/23/2020
ms.locfileid: "76695614"
---
# <a name="active-directory-federation-services-support-in-msalnet"></a>MSAL.NET 中的 Active Directory 同盟服務支援
Windows Server 中的 Active Directory 同盟服務（AD FS）可讓您將 OpenID Connect 和 OAuth 2.0 型驗證和授權新增至您正在開發的應用程式。 然後，這些應用程式就可以直接針對 AD FS 驗證使用者。 如需詳細資訊，請參閱[開發人員 AD FS 案例](/windows-server/identity/ad-fs/overview/ad-fs-openid-connect-oauth-flows-scenarios)。

適用于 .NET 的 Microsoft 驗證程式庫（MSAL.NET）支援兩種針對 AD FS 進行驗證的案例：

- MSAL.NET 會與 Azure Active Directory 交談，其本身*會與 AD FS*同盟。
- MSAL.NET 會**直接**與 ADFS 授權單位交談。 只有 AD FS 2019 和更新版本才支援這種情況。 其中一個重點是[Azure Stack](https://azure.microsoft.com/overview/azure-stack/)支援的案例


## <a name="msal-connects-to-azure-ad-which-is-federated-with-ad-fs"></a>MSAL 會連接到與同盟的 Azure AD AD FS
MSAL.NET 支援連接到 Azure AD，以登入受管理使用者（在 Azure AD 中管理的使用者）或同盟使用者（由其他身分識別提供者所管理的使用者，例如 AD FS）。 MSAL.NET 並不知道使用者是聯盟的事實。 就這一點而言，它會與 Azure AD 交談。

您在此案例中使用的[授權](msal-client-application-configuration.md#authority)單位是一般授權（授權主機名稱 + 租使用者、通用或組織）。

### <a name="acquiring-a-token-interactively"></a>以互動方式取得權杖
當您呼叫 `AcquireTokenInteractive` 方法時，使用者體驗通常是：

1. 使用者輸入其帳戶識別碼。
2. Azure AD 會短暫顯示「帶您前往貴組織的頁面」的訊息。
3. 使用者會重新導向至身分識別提供者的登入頁面。 登入頁面通常會以組織的標誌進行自訂。

此同盟案例中支援的 AD FS 版本包括 AD FS v2、AD FS v3 （Windows Server 2012 R2）和 AD FS v4 （AD FS 2016）。

### <a name="acquiring-a-token-using-acquiretokenbyintegratedauthentication-or-acquiretokenbyusernamepassword"></a>使用 AcquireTokenByIntegratedAuthentication 或 AcquireTokenByUsernamePassword 取得權杖
使用 `AcquireTokenByIntegratedAuthentication` 或 `AcquireTokenByUsernamePassword` 方法取得權杖時，MSAL.NET 會根據使用者名稱取得身分識別提供者。  MSAL.NET 會在聯繫識別提供者之後收到[SAML 1.1 token](reference-saml-tokens.md) 。  然後，MSAL.NET 會將 SAML 權杖提供給 Azure AD 做為使用者判斷提示（類似于代理者[流程](msal-authentication-flows.md#on-behalf-of)）以取回 JWT。

## <a name="msal-connects-directly-to-ad-fs"></a>MSAL 直接連接到 AD FS
MSAL.NET 支援連接到 AD FS 2019，這是與 Open ID Connect 相容，並瞭解 PKCE 和範圍。 這種支援需要將 Service Pack [KB 4490481](https://support.microsoft.com/en-us/help/4490481/windows-10-update-kb4490481)套用到 Windows Server。 當直接連接到 AD FS 時，您要用來建立應用程式的授權單位與 `https://mysite.contoso.com/adfs/`類似。

目前，沒有任何計畫可支援直接連接至：

- AD FS 16，因為它不支援 PKCE，但仍使用資源，而非範圍
- AD FS v2，這與 OIDC 不相容。

 如果您需要支援需要直接連線至 AD FS 2016 的案例，請使用最新版的[Azure Active Directory Authentication Library](active-directory-authentication-libraries.md#microsoft-supported-client-libraries)。 當您將內部部署系統升級至 AD FS 2019 時，您將能夠使用 MSAL.NET。

## <a name="next-steps"></a>後續步驟

針對同盟案例，請參閱[使用主領域探索原則來設定應用程式的 Azure Active Directory 登入行為](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-authentication-for-federated-users-portal)
