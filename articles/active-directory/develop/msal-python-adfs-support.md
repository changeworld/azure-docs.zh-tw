---
title: Azure AD FS 支援（MSAL Python）
titleSuffix: Microsoft identity platform
description: 瞭解適用于 Python 的 Microsoft 驗證程式庫中的 Active Directory 同盟服務（AD FS）支援
services: active-directory
author: abhidnya13
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/23/2019
ms.author: abpati
ms.reviewer: nacanuma
ms.custom: aaddev
ms.openlocfilehash: 01d4cb626aabc83117e864b75b49eec63a6c0af0
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/23/2020
ms.locfileid: "76699541"
---
# <a name="active-directory-federation-services-support-in-msal-for-python"></a>MSAL for Python 中的 Active Directory 同盟服務支援

Windows Server 中的 Active Directory 同盟服務（AD FS）可讓您使用適用于 Python 的 Microsoft 驗證程式庫（MSAL），將 OpenID Connect 和 OAuth 2.0 型驗證和授權新增至您的應用程式。 使用 Python 程式庫的 MSAL，您的應用程式可以直接針對 AD FS 驗證使用者。 如需案例的詳細資訊，請參閱[開發人員的 AD FS 案例](/windows-server/identity/ad-fs/ad-fs-development)。

有兩種方式可針對 AD FS 進行驗證：

- MSAL Python 會與 Azure Active Directory 進行交談，其本身會與其他身分識別提供者同盟。 同盟會透過 AD FS 進行。 MSAL Python 會連線到 Azure AD，其會登入 Azure AD （受管理的使用者）中管理的使用者，或由其他身分識別提供者（例如 AD FS （同盟使用者）所管理的使用者。 MSAL Python 不知道使用者是否已同盟。 它只會與 Azure AD 交談。 您在此案例中使用的[授權](msal-client-application-configuration.md#authority)單位是一般授權（授權主機名稱 + 租使用者、通用或組織）。
- MSAL Python 會直接與 AD FS 授權單位交談。 只有 AD FS 2019 和更新版本才支援此項。

## <a name="connect-to-active-directory-federated-with-ad-fs"></a>連接到 Active Directory 與 AD FS 同盟

### <a name="acquire-a-token-interactively-for-a-federated-user"></a>以互動方式取得同盟使用者的權杖

無論您是直接連接到 Active Directory 同盟服務（AD FS）還是透過 Active Directory，都適用下列情況。

當您呼叫 `acquire_token_by_authorization_code` 或 `acquire_token_by_device_flow`時，使用者體驗通常如下所示：

1. 使用者輸入其帳戶識別碼。
2. Azure AD 會短暫顯示「將您帶到您的組織頁面」的訊息，並將使用者重新導向至身分識別提供者的登入頁面。 登入頁面通常會以組織的標誌進行自訂。

此同盟案例中支援的 AD FS 版本如下：
- Active Directory 同盟服務 FS v2
- Active Directory 同盟服務 v3 （Windows Server 2012 R2）
- Active Directory 同盟服務 v4 （AD FS 2016）

### <a name="acquire-a-token-via-username-and-password"></a>透過使用者名稱和密碼取得權杖

無論您是直接連接到 Active Directory 同盟服務（AD FS）還是透過 Active Directory，都適用下列情況。

當您使用 `acquire_token_by_username_password`取得權杖時，MSAL Python 會根據使用者名稱，讓身分識別提供者聯繫。 MSAL Python 會從識別提供者取得[SAML 1.1 權杖](reference-saml-tokens.md)，然後提供給傳回 JSON Web 權杖（JWT）的 Azure AD。

## <a name="connecting-directly-to-ad-fs"></a>直接連接到 AD FS

當您將目錄連線到 AD FS 時，您要用來建立應用程式的授權單位將會像這樣 `https://somesite.contoso.com/adfs/`

MSAL Python 支援 ADFS 2019。

它不支援與 ADFS 2016 或 ADFS v2 的直接連接。 如果您需要支援需要直接連線至 ADFS 2016 的案例，請使用最新版的 ADAL Python。 將內部部署系統升級至 ADFS 2019 之後，您就可以使用 MSAL Python。

## <a name="next-steps"></a>後續步驟

- 針對同盟案例，請參閱[使用主領域探索原則來設定應用程式的 Azure Active Directory 登入行為](../manage-apps/configure-authentication-for-federated-users-portal.md)