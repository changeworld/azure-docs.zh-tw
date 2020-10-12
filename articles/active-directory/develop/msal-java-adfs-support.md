---
title: AD FS 支援 JAVA) 的 (MSAL
titleSuffix: Microsoft identity platform
description: 瞭解 Active Directory 同盟服務 (AD FS 適用于 JAVA 的 Microsoft 驗證程式庫中的) 支援 (MSAL4j) 。
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
ms.openlocfilehash: 5d01d1143563637e21aaa06a3f997c1507e4d8f1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "88114773"
---
# <a name="active-directory-federation-services-support-in-msal-for-java"></a>MSAL for JAVA 的 Active Directory 同盟服務支援

Windows Server 中的 Active Directory 同盟服務 (AD FS) 可讓您將 OpenID Connect 和 OAuth 2.0 型驗證和授權新增至適用于 java (MSAL for JAVA) 應用程式的 Microsoft 驗證程式庫。 整合之後，您的應用程式可以透過 Azure AD 在 AD FS 中驗證使用者。 如需案例的詳細資訊，請參閱 [開發人員 AD FS 案例](/windows-server/identity/ad-fs/ad-fs-development)。

使用 MSAL for JAVA 的應用程式將會與 Azure Active Directory (Azure AD) ，然後再則聯盟至 AD FS。

適用于 JAVA 的 MSAL 會連線到 Azure AD，其會將 Azure AD (受管理的使用者) 或由其他身分識別提供者（例如 AD FS (同盟使用者) ）管理的使用者登入。 MSAL for JAVA 不知道使用者是否已同盟。 它只會與 Azure AD 交談。

在此情況下，您使用的 [授權](msal-client-application-configuration.md#authority) 單位是一般授權 (授權單位主機名稱 + 租使用者、一般或組織) 。

## <a name="acquire-a-token-interactively-for-a-federated-user"></a>以互動方式取得同盟使用者的權杖

當您 `ConfidentialClientApplication.AcquireToken()` 使用或來呼叫或時 `PublicClientApplication.AcquireToken()` `AuthorizationCodeParameters` `DeviceCodeParameters` ，使用者體驗通常是：

1. 使用者輸入他們的帳戶識別碼。
2. Azure AD 短暫地顯示「將您帶到您的組織頁面」，系統會將使用者重新導向至身分識別提供者的登入頁面。 登入頁面通常會使用組織的標誌進行自訂。

此同盟案例中支援的 AD FS 版本如下：
- Active Directory 同盟服務 FS v2
- Active Directory 同盟服務 v3 (Windows Server 2012 R2) 
- Active Directory 同盟服務 v4 (AD FS 2016) 

## <a name="acquire-a-token-via-username-and-password"></a>透過使用者名稱和密碼取得權杖

當您使用或搭配或取得權杖時 `ConfidentialClientApplication.AcquireToken()` `PublicClientApplication.AcquireToken()` `IntegratedWindowsAuthenticationParameters` `UsernamePasswordParameters` ，MSAL for JAVA 會根據使用者名稱取得要聯絡的身分識別提供者。 MSAL for JAVA 會從身分識別提供者取得 [SAML 1.1 權杖](reference-saml-tokens.md) 權杖，然後提供給 Azure AD，以傳回 (JWT) 的 JSON Web 權杖。

## <a name="next-steps"></a>後續步驟

針對同盟案例，請參閱 [使用主領域探索原則設定應用程式的 Azure Active Directory 登入行為](../manage-apps/configure-authentication-for-federated-users-portal.md)