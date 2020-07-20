---
title: Azure AD B2C 的新應用程式註冊體驗
description: Azure AD B2C 中的新應用程式註冊體驗簡介。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 05/25/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: c89ed98d8100df270f09f1d2d1b621e71e326fe3
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85386295"
---
# <a name="the-new-app-registrations-experience-for-azure-active-directory-b2c"></a>Azure Active Directory B2C 的新應用程式註冊體驗

Azure Active Directory B2C （Azure AD B2C）的新**[應用程式註冊](https://aka.ms/b2cappregistrations)** 體驗現已正式推出。 如果您更熟悉用來註冊應用程式以進行 Azure AD B2C 的**應用程式**體驗（在此稱為「舊版體驗」），本指南將協助您開始使用新的體驗。

## <a name="overview"></a>總覽
以往，您必須使用舊版體驗，從您的應用程式的其餘部分分別管理您 Azure AD B2C 取用者面向的應用程式。 這表示不同的應用程式建立體驗會跨 Azure 中的不同位置。

新體驗會顯示所有 Azure AD B2C 應用程式註冊，並在一個位置 Azure AD 應用程式註冊，並提供一致的方式來進行管理。 從建立客戶面向的應用程式，到使用 Microsoft Graph 的資源管理許可權來管理應用程式，您只需要學習一種方法來執行工作。

您可以從**Azure AD B2C**或 Azure 入口網站中的**Azure Active Directory**服務，流覽至 Azure AD B2C 租使用者中的**應用程式註冊**，以達到新的體驗。

Azure AD B2C 應用程式註冊體驗是以適用于任何 Azure AD 租使用者的一般[應用程式註冊體驗](https://developer.microsoft.com/identity/blogs/new-app-registrations-experience-is-now-generally-available/)為基礎，但已針對 Azure AD B2C 租使用者量身打造。

## <a name="whats-not-changing"></a>什麼不會改變？
- 您的應用程式和相關設定可以在新的體驗中找到。 您不需要再次註冊應用程式，應用程式的使用者也不需要再次登入。 

> [!NOTE]
> 若要查看所有先前建立的應用程式，請流覽至 [**應用程式註冊**] 分頁，然後選取 [**所有應用程式**] 索引標籤。這會顯示在舊版體驗中建立的應用程式、新的體驗，以及在 Azure AD 服務中建立的應用程式。

## <a name="key-new-features"></a>主要的新功能

-   **統一的應用程式清單**會顯示您的所有應用程式，以 Azure AD B2C 進行驗證，並在一個方便的位置 Azure AD。 此外，您還可以利用已可用於 Azure AD 應用程式的功能，包括**建立**日期、**憑證 & 秘密**狀態、搜尋列等等。

-   **結合的應用程式註冊**可讓您快速註冊應用程式，不論是客戶面向的應用程式或應用程式，都可以存取 Microsoft Graph。

- [**端點**] 窗格可讓您快速識別案例的相關端點，包括 OpenID connect 設定、SAML 中繼資料、Microsoft Graph API 和[OAuth 2.0 使用者流程端點](tokens-overview.md#endpoints)。 

- **Api 許可權**和**公開 api 可**提供更廣泛的範圍、許可權和同意管理。 您現在也可以將 MS Graph 和 Azure AD Graph 許可權指派給應用程式。

-   **擁有**者和**資訊清單**現在可供使用 Azure AD B2C 進行驗證的應用程式使用。 您可以為您的註冊新增擁有者，並[使用資訊清單編輯器](../active-directory/develop/reference-app-manifest.md)直接編輯應用程式屬性。


## <a name="new-supported-account-types"></a>新支援的帳戶類型

在新的體驗中，您可以從下列選項中選取支援帳戶類型：
- 僅限此組織目錄中的帳戶。
- 任何組織目錄中的帳戶（任何 Azure AD 目錄-多租使用者）。
- 任何組織目錄或任何身分識別提供者中的帳戶。 用於驗證 Azure AD B2C 的使用者。

若要瞭解不同的帳戶類型，請選取 [**協助我選擇**建立體驗]。 

在舊版的體驗中，應用程式一律會建立為客戶面向的應用程式。 針對這些應用程式，帳戶類型會設定為**任何組織目錄中的帳戶或任何識別提供者。用於驗證 Azure AD B2C 的使用者**。
> [!NOTE]
> 必須要有此選項，才能執行 Azure AD B2C 使用者流程來驗證此應用程式的使用者。 瞭解[如何註冊應用程式以與使用者流程搭配使用。](tutorial-register-applications.md)

您也可以使用此選項，將 Azure AD B2C 做為 SAML 服務提供者。 [深入了解](identity-provider-adfs2016-custom.md)。

## <a name="applications-for-devops-scenarios"></a>適用于 DevOps 案例的應用程式
您可以使用其他帳戶類型來建立應用程式來管理您的 DevOps 案例，例如使用 Microsoft Graph 上傳 Identity Experience Framework 原則或布建使用者。 瞭解[如何註冊 Microsoft Graph 應用程式來管理 Azure AD B2C 資源](microsoft-graph-get-started.md)。

您可能不會看到所有的 Microsoft Graph 許可權，因為其中有許多許可權不適用於 Azure B2C 取用者使用者。 [閱讀更多有關使用 Microsoft Graph 管理使用者的資訊](manage-user-accounts-graph-api.md)。  

## <a name="admin-consent-and-offline_accessopenid-scopes"></a>管理員同意和 offline_access + openid 範圍  
<!-- Azure AD B2C doesn't support user consent. That is, when a user signs into an application, the user doesn't see a screen requesting consent for the application permissions. All permissions have to be granted through admin consent.  -->

需要**openid**範圍，Azure AD B2C 可以將使用者登入應用程式。 需要**offline_access**範圍，才能為使用者發出重新整理權杖。 這些範圍先前已新增，並在預設的情況下獲得系統管理員同意。 現在，您可以在建立程式期間，藉由確定已選取 [授與系統**管理員同意 openid 和 offline_access 許可權**] 選項，輕鬆地新增這些範圍的許可權。 否則，您可以在現有應用程式的 [ **API 許可權**] 設定中，以系統管理員同意來新增 Microsoft Graph 許可權。

深入瞭解[許可權和同意](../active-directory/develop/v2-permissions-and-consent.md)。

## <a name="platformsauthentication-reply-urlsredirect-uris"></a>平臺/驗證：回復 Url/重新導向 Uri
在舊版體驗中，會在 [**屬性**] 下管理各種平臺類型，作為 web Apps/api 的回復 Url 和原生用戶端的重新導向 URI。 「原生用戶端」也稱為「公用用戶端」，並包含適用于 iOS、macOS、Android 及其他行動和桌面應用程式類型的應用程式。 

在新體驗中，回復 Url 和重新導向 Uri 兩者都稱為「重新導向 Uri」，而且可以在應用程式的**驗證**一節中找到。 應用程式註冊不限於 web 應用程式或原生應用程式。 您可以藉由註冊個別的重新導向 Uri，針對所有平臺類型使用相同的應用程式註冊。 

重新導向 Uri 必須與應用程式類型（web 或公用（行動裝置和桌面））相關聯。 [深入瞭解重新導向 Uri](../active-directory/develop/quickstart-configure-app-access-web-apis.md#add-redirect-uris-to-your-application)

<!-- Whether an application should be treated as a public client is inferred at run-time from the Redirect URI platform type, if possible. The **Treat application as a public client** setting should be set to **Yes** for flows that might not use a redirect URI, such as ROPC flows. -->

**IOS/macOS**和**Android**平臺是一種公用用戶端。 它們提供一個簡單的方式，讓您使用對應的重新導向 Uri 來設定 iOS/macOS 或 Android 應用程式，以搭配 MSAL 使用。 深入瞭解[應用程式設定選項](../active-directory/develop/msal-client-applications.md)。


## <a name="application-certificates--secrets"></a>& 秘密的應用程式憑證

在新的體驗中，您可以使用 [**憑證 & 密碼**] 分頁來管理憑證和密碼，而不是**金鑰**。 憑證 & 秘密可讓應用程式在 web 可定址位置（使用 HTTPS 配置）接收權杖時，向驗證服務識別自己的身分。 針對 Azure AD 進行驗證時，建議您針對用戶端認證案例使用憑證，而不是用戶端密碼。 憑證無法用來對 Azure AD B2C 進行驗證。


## <a name="features-not-applicable-in-azure-ad-b2c-tenants"></a>Azure AD B2C 租使用者中不適用的功能
下列 Azure AD 應用程式註冊功能不適用於 Azure AD B2C 租使用者中或可供使用：
- **角色和系統管理員**-這需要目前無法供 Azure AD B2C 使用的 Azure AD Premium P1 或 P2 授權。
- **商標**-UI/UX 自訂會在**公司商標**體驗中設定，或做為使用者流程的一部分。 瞭解如何[在 Azure Active Directory B2C 中自訂使用者介面](customize-ui-overview.md)。
- **發行者網域驗證**-您的應用程式已在*onmicrosoft.com*上註冊，這不是已驗證的網域。 此外，發行者網域主要是用來授與使用者同意，這不適用於使用者驗證 Azure AD B2C 應用程式。 [深入瞭解發行者網域](https://docs.microsoft.com/azure/active-directory/develop/howto-configure-publisher-domain)。
- **權杖**設定-權杖會設定為使用者流程的一部分，而不是應用程式。
- Azure AD B2C 的租使用者目前無法使用**快速入門**體驗。
- [**整合助理**] 分頁目前無法供 Azure AD B2C 租使用者使用。


## <a name="limitations"></a>限制
新的體驗有下列限制：
- 此時，Azure AD B2C 不會區分能夠對隱含流程發出存取或識別碼權杖;如果在 [**驗證**] 分頁中選取 [**識別碼權杖**] 選項，則這兩種類型的 token 都適用于隱含授與流程。
<!-- - Azure AD B2C doesn't currently support the single-page application "SPA" app type.  -->
- 使用者介面不支援變更支援帳戶的值。 您將需要使用應用程式資訊清單，除非您要在 Azure AD 單一租使用者與多租使用者之間切換。

## <a name="next-steps"></a>後續步驟

若要開始使用新的應用程式註冊體驗：
* 瞭解[如何註冊 web 應用程式](tutorial-register-applications.md)。
* 瞭解[如何註冊 Web API](add-web-api-application.md)。
* 瞭解[如何註冊原生用戶端應用程式](add-native-application.md)。
* 瞭解[如何註冊 Microsoft Graph 應用程式來管理 Azure AD B2C 資源](microsoft-graph-get-started.md)。
* 瞭解[如何使用 Azure AD B2C 做為 SAML 服務提供者。](identity-provider-adfs2016-custom.md)
* 瞭解[應用程式類型](application-types.md)。
