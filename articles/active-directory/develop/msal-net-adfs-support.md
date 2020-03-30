---
title: MSAL.NET中的 AD FS 支援 |蔚藍
titleSuffix: Microsoft identity platform
description: 瞭解 .NET （MSAL.NET） 的 Microsoft 身份驗證庫中的活動目錄聯合服務 （AD FS） 支援。
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
ms.custom: aaddev
ms.openlocfilehash: 15af18177cea217612a4d5276d130abe02d339f4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77160754"
---
# <a name="active-directory-federation-services-support-in-msalnet"></a>活動目錄聯合服務支援MSAL.NET
Windows 伺服器中的活動目錄聯合服務 （AD FS） 使您能夠向正在開發的應用程式添加 OpenID 連接和 OAuth 2.0 身份驗證和授權。 因此，這些應用程式可以直接根據 AD FS 對使用者進行身份驗證。 有關詳細資訊，請閱讀[開發人員的 AD FS 方案](/windows-server/identity/ad-fs/overview/ad-fs-openid-connect-oauth-flows-scenarios)。

.NET （MSAL.NET） 的 Microsoft 身份驗證庫支援兩種針對 AD FS 進行身份驗證的方案：

- MSAL.NET與 Azure 活動目錄進行對話，該目錄本身與 AD FS*聯合*。
- MSAL.NET**直接**與ADFS當局對話。 這僅支援 AD FS 2019 及以上。 此亮點的方案之一是 Azure[堆疊](https://azure.microsoft.com/overview/azure-stack/)支援


## <a name="msal-connects-to-azure-ad-which-is-federated-with-ad-fs"></a>MSAL 連接到 Azure AD，該 AD 與 AD FS 聯合
MSAL.NET支援連接到 Azure AD，該 AD 在託管使用者（Azure AD 中管理的使用者）或聯合使用者（由 AD FS 等其他標識提供程式管理的使用者）中簽名。 MSAL.NET不知道使用者是聯合的。 就它而言，它與 Azure AD 對話。

在這種情況下，您使用[的許可權](msal-client-application-configuration.md#authority)是通常的許可權（許可權主機名稱 + 租戶、公用或組織）。

### <a name="acquiring-a-token-interactively"></a>以對話模式獲取權杖
調用`AcquireTokenInteractive`方法時，使用者體驗通常是：

1. 使用者輸入其帳戶 ID。
2. Azure AD 會簡要顯示"將您帶到組織的頁面"的消息。
3. 使用者被重定向到標識提供程式的登錄頁。 登錄頁通常使用組織的徽標進行自訂。

在此聯合方案中支援的 AD FS 版本有 AD FS v2、AD FS v3（Windows 伺服器 2012 R2）和 AD FS v4（AD FS 2016）。

### <a name="acquiring-a-token-using-acquiretokenbyintegratedauthentication-or-acquiretokenbyusernamepassword"></a>使用獲取權杖通過集成身份驗證或獲取權杖密碼獲取權杖
使用`AcquireTokenByIntegratedAuthentication`或`AcquireTokenByUsernamePassword`方法獲取權杖時，MSAL.NET讓標識提供程式根據使用者名進行聯繫。  MSAL.NET聯繫標識提供程式後收到[SAML 1.1 權杖](reference-saml-tokens.md)。  然後MSAL.NET將 SAML 權杖作為使用者斷言（類似于[代理流](msal-authentication-flows.md#on-behalf-of)）作為使用者斷言向 Azure AD 提供，以返回 JWT。

## <a name="msal-connects-directly-to-ad-fs"></a>MSAL 直接連接到 AD FS
MSAL.NET支援連接到 AD FS 2019，後者符合開放 ID 連接，並且瞭解 PKCE 和作用域。 此支援要求將服務包[KB 4490481](https://support.microsoft.com/en-us/help/4490481/windows-10-update-kb4490481)應用於 Windows 伺服器。 當直接連接到 AD FS 時，要用於構建應用程式的許可權類似于`https://mysite.contoso.com/adfs/`。

目前，沒有計劃支援與：

- AD FS 16，因為它不支援 PKCE，並且仍然使用資源，而不是範圍
- AD FS v2，不符合 OIDC 標準。

 如果需要支援需要直接連接到 AD FS 2016 的方案，請使用最新版本的[Azure 活動目錄身份驗證庫](../azuread-dev/active-directory-authentication-libraries.md#microsoft-supported-client-libraries)。 將本地系統升級到 AD FS 2019 後，您將能夠使用MSAL.NET。

## <a name="next-steps"></a>後續步驟

對於聯合情況，請參閱[使用主域發現策略為應用程式佈建 Azure 活動目錄登錄行為](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-authentication-for-federated-users-portal)
