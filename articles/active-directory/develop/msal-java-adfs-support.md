---
title: AD FS 支援（適用于 JAVA 的 MSAL）
titleSuffix: Microsoft identity platform
description: 瞭解適用于 JAVA 的 Microsoft 驗證程式庫（MSAL4j）中的 Active Directory 同盟服務（AD FS）支援。
services: active-directory
author: sangonzal
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/21/2019
ms.author: sagonzal
ms.reviewer: nacanuma
ms.custom: aaddev, devx-track-java
ms.openlocfilehash: f1ad755e444968cbbbe9e048be53fb72f65c3b37
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87312634"
---
# <a name="active-directory-federation-services-support-in-msal-for-java"></a>MSAL for JAVA 中的 Active Directory 同盟服務支援

Windows Server 中的 Active Directory 同盟服務（AD FS）可讓您將 OpenID Connect 和 OAuth 2.0 型驗證和授權新增至您的 Microsoft Authentication Library for JAVA （MSAL for JAVA）應用程式。 整合之後，您的應用程式就可以在 AD FS 中驗證使用者，並透過 Azure AD 進行同盟。 如需案例的詳細資訊，請參閱[開發人員的 AD FS 案例](/windows-server/identity/ad-fs/ad-fs-development)。

使用 MSAL for JAVA 的應用程式將會與 Azure Active Directory （Azure AD）交談，然後則聯盟至 AD FS。

MSAL for JAVA 會連線到 Azure AD，其會登入 Azure AD （受管理的使用者）中管理的使用者，或由其他身分識別提供者（例如 AD FS （同盟使用者）所管理的使用者。 MSAL for JAVA 不知道使用者是同盟的。 它只會與 Azure AD 交談。

您在此案例中使用的[授權](msal-client-application-configuration.md#authority)單位是一般授權（授權主機名稱 + 租使用者、通用或組織）。

## <a name="acquire-a-token-interactively-for-a-federated-user"></a>以互動方式取得同盟使用者的權杖

當您 `ConfidentialClientApplication.AcquireToken()` 使用或呼叫或時 `PublicClientApplication.AcquireToken()` `AuthorizationCodeParameters` `DeviceCodeParameters` ，使用者體驗通常是：

1. 使用者輸入其帳戶識別碼。
2. Azure AD 短暫顯示「帶您前往貴組織的頁面」，然後將使用者重新導向至身分識別提供者的登入頁面。 登入頁面通常會以組織的標誌進行自訂。

此同盟案例中支援的 AD FS 版本如下：
- Active Directory 同盟服務 FS v2
- Active Directory 同盟服務 v3 （Windows Server 2012 R2）
- Active Directory 同盟服務 v4 （AD FS 2016）

## <a name="acquire-a-token-via-username-and-password"></a>透過使用者名稱和密碼取得權杖

當您使用或搭配或取得權杖時 `ConfidentialClientApplication.AcquireToken()` `PublicClientApplication.AcquireToken()` `IntegratedWindowsAuthenticationParameters` `UsernamePasswordParameters` ，MSAL for JAVA 會根據使用者名稱，讓身分識別提供者聯繫。 MSAL for JAVA 會從識別提供者取得[SAML 1.1 權杖](reference-saml-tokens.md)權杖，然後提供給傳回 JSON Web 權杖（JWT）的 Azure AD。

## <a name="next-steps"></a>後續步驟

針對同盟案例，請參閱[使用主領域探索原則來設定應用程式的 Azure Active Directory 登入行為](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-authentication-for-federated-users-portal)