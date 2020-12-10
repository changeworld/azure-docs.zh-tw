---
title: Azure AD B2C 的新應用程式註冊體驗
description: Azure AD B2C 中新的應用程式註冊體驗簡介。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 05/25/2020
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 7bb38b382add2a0ea3506c31c5e6e76a985df6a1
ms.sourcegitcommit: dea56e0dd919ad4250dde03c11d5406530c21c28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/09/2020
ms.locfileid: "96936466"
---
# <a name="the-new-app-registrations-experience-for-azure-active-directory-b2c"></a>Azure Active Directory B2C 的新應用程式註冊體驗

Azure Active Directory B2C (Azure AD B2C) 的新 **[應用程式註冊](https://aka.ms/b2cappregistrations)** 體驗現已正式推出。 如果您更熟悉用來註冊應用程式的 **應用程式** 體驗 Azure AD B2C，在此稱為「舊版體驗」，本指南將協助您開始使用新的體驗。

## <a name="overview"></a>總覽
先前，您必須使用舊版體驗，與應用程式的其餘部分分開管理 Azure AD B2C 取用者面向應用程式。 這表示不同的應用程式在 Azure 中的不同位置之間的建立體驗。

新體驗會顯示所有 Azure AD B2C 應用程式註冊，並在單一位置 Azure AD 應用程式註冊，並提供一致的方式來管理它們。 從建立客戶面向的應用程式，到以 Microsoft Graph 的資源管理許可權來管理應用程式，您只需要學習一種方法來進行工作。

您可以從 Azure 入口網站的 **Azure AD B2C** 或 **Azure Active Directory** 服務流覽至 Azure AD B2C 租使用者中的 **應用程式註冊**，以達到新的體驗。

Azure AD B2C 的應用程式註冊體驗是以任何 Azure AD 租使用者的一般 [應用程式註冊經驗](https://developer.microsoft.com/identity/blogs/new-app-registrations-experience-is-now-generally-available/) 為基礎，但專為 Azure AD B2C 租使用者量身打造。

## <a name="whats-not-changing"></a>什麼不會變更？
- 在新的體驗中，您可以找到您的應用程式和相關設定。 您不需要再次註冊應用程式，應用程式的使用者就不需要再次登入。

> [!NOTE]
> 若要查看所有先前建立的應用程式，請流覽至 **應用程式註冊** 分頁，然後選取 [ **所有應用程式** ] 索引標籤。這會顯示在舊版體驗中建立的應用程式、新的體驗，以及在 Azure AD 服務中建立的應用程式。

## <a name="key-new-features"></a>重要的新功能

-   **整合應用程式清單** 會顯示您所有的應用程式，這些應用程式會使用 Azure AD B2C 進行驗證，並在一個方便的位置 Azure AD 此外，您可以利用已可用於 Azure AD 應用程式的功能，包括 **建立** 日期、 **憑證 & 秘密** 狀態、搜尋列等等。

-   **結合的應用程式註冊** 可讓您快速註冊應用程式，無論它是客戶面向的應用程式，還是可存取 Microsoft Graph 的應用程式。

- [ **端點** ] 窗格可讓您快速識別案例的相關端點，包括 OpenID connect 設定、SAML 中繼資料、Microsoft Graph API 和 [OAuth 2.0 使用者流程端點](tokens-overview.md#endpoints)。

- **Api 許可權** 和 **公開 api** 提供更廣泛的範圍、許可權和同意管理。 您現在也可以將 MS Graph 和 Azure AD Graph 許可權指派給應用程式。

-   **擁有** 者和 **資訊清單** 現在可供使用 Azure AD B2C 進行驗證的應用程式使用。 您可以新增註冊的擁有者，並 [使用資訊清單編輯器](../active-directory/develop/reference-app-manifest.md)直接編輯應用程式屬性。


## <a name="new-supported-account-types"></a>新支援的帳戶類型

在新的體驗中，您可以從下列選項中選取支援帳戶類型：
- 僅此組織目錄中的帳戶
- 任何組織目錄中的帳戶 (任何 Azure AD 目錄–多租使用者) 
- 任何身分識別提供者或組織目錄 (中的帳戶，用來驗證使用者流程的使用者) 

若要瞭解不同的帳戶類型，請選取 [ **協助我** 在建立體驗中選擇]。

在舊版體驗中，應用程式一律會建立為面向客戶的應用程式。 針對這些應用程式，帳戶類型會設定為 **任何身分識別提供者或組織目錄中的帳戶， (用於驗證具有使用者流程) 的使用者**。
> [!NOTE]
> 需要此選項才能執行 Azure AD B2C 使用者流程，以驗證此應用程式的使用者。 瞭解 [如何註冊應用程式以用於使用者流程。](tutorial-register-applications.md)

您也可以使用此選項，將 Azure AD B2C 作為 SAML 服務提供者。 [深入了解](identity-provider-adfs.md)。

## <a name="applications-for-devops-scenarios"></a>適用于 DevOps 案例的應用程式
您可以使用其他帳戶類型來建立應用程式來管理您的 DevOps 案例，例如使用 Microsoft Graph 上傳 Identity Experience Framework 原則或布建使用者。 瞭解 [如何註冊 Microsoft Graph 的應用程式，以管理 Azure AD B2C 的資源](microsoft-graph-get-started.md)。

您可能不會看到所有的 Microsoft Graph 許可權，因為這些許可權中有許多無法套用至 Azure B2C 取用者使用者。 [閱讀有關使用 Microsoft Graph 管理使用者的詳細資訊](manage-user-accounts-graph-api.md)。

## <a name="admin-consent-and-offline_accessopenid-scopes"></a>系統管理員同意和 offline_access + openid 範圍
<!-- Azure AD B2C doesn't support user consent. That is, when a user signs into an application, the user doesn't see a screen requesting consent for the application permissions. All permissions have to be granted through admin consent.  -->

需要 **openid** 範圍，才能讓 Azure AD B2C 將使用者登入應用程式。 需要 **offline_access** 範圍，才能為使用者發出重新整理權杖。 先前已新增這些範圍，並根據預設獲得系統管理員同意。 現在，您可以在建立程式期間，藉由確定已選取 [授與系統 **管理員同意 openid 和 offline_access 許可權** ] 選項，輕鬆地新增這些範圍的許可權。 或者，您可以使用現有應用程式的 **API 許可權** 設定中的系統管理員同意來新增 Microsoft Graph 許可權。

深入瞭解 [許可權和同意](../active-directory/develop/v2-permissions-and-consent.md)。

## <a name="platformsauthentication-reply-urlsredirect-uris"></a>平臺/驗證：回復 Url/重新導向 Uri
在舊版體驗中，會在 [ **屬性** ] 下管理各種平臺類型，以做為 web Apps/api 的回復 Url 和原生用戶端的重新導向 URI。 「原生用戶端」也稱為「公用用戶端」，並包含適用于 iOS、macOS、Android 和其他行動裝置和桌面應用程式類型的應用程式。

在新的體驗中，回復 Url 和重新導向 Uri 都稱為重新導向 Uri，而且可以在應用程式的 [ **驗證** ] 區段中找到。 應用程式註冊不會限制為 web 應用程式或原生應用程式。 您可以註冊個別的重新導向 Uri，以針對所有這些平臺類型使用相同的應用程式註冊。

重新導向 Uri 必須與應用程式類型（web 或公用 (行動裝置和桌面) ）相關聯。 [深入瞭解重新導向 Uri](../active-directory/develop/quickstart-register-app.md#add-a-redirect-uri)

<!-- Whether an application should be treated as a public client is inferred at run-time from the Redirect URI platform type, if possible. The **Treat application as a public client** setting should be set to **Yes** for flows that might not use a redirect URI, such as ROPC flows. -->

**IOS/macOS** 和 **Android** 平臺是一種公開用戶端。 它們提供簡單的方式，讓您使用對應的重新導向 Uri 來設定 iOS/macOS 或 Android 應用程式，以搭配 MSAL 使用。 深入瞭解 [應用程式設定選項](../active-directory/develop/msal-client-applications.md)。


## <a name="application-certificates--secrets"></a>應用程式憑證 & 秘密

在新的體驗中，您可以使用 **憑證 & 秘密** 分頁來管理憑證和密碼，而不是使用 **金鑰**。 憑證 & 秘密可讓應用程式在 web 可定址位置接收權杖時，向驗證服務識別其本身， (使用 HTTPS 配置) 。 針對 Azure AD 進行驗證時，建議使用憑證，而不是用戶端認證案例的用戶端密碼。 憑證不能用來對 Azure AD B2C 進行驗證。


## <a name="features-not-applicable-in-azure-ad-b2c-tenants"></a>Azure AD B2C 租使用者中不適用的功能
下列 Azure AD 應用程式註冊功能不適用於 Azure AD B2C 租使用者或可供使用：
- **角色和系統管理員** -目前不適用於 Azure AD B2C。
- **商標** -UI/UX 自訂是在 **公司商標** 體驗中設定，或做為使用者流程的一部分。 瞭解如何 [在 Azure Active Directory B2C 中自訂使用者介面](customize-ui-overview.md)。
- **發行者網域驗證** -您的應用程式已在 *onmicrosoft.com* 上註冊，這不是經過驗證的網域。 此外，「發行者」網域主要是用來授與使用者同意，這不適用於使用者驗證 Azure AD B2C 應用程式。 [深入瞭解發行者網域](../active-directory/develop/howto-configure-publisher-domain.md)。
- **權杖** 設定-權杖會設定為使用者流程的一部分，而不是應用程式的一部分。
- Azure AD B2C 的租使用者目前無法使用 **快速入門** 體驗。
<!-- - The **Integration assistant** blade is currently not available for Azure AD B2C tenants. -->

## <a name="limitations"></a>限制
新的體驗具有下列限制：
- 目前，Azure AD B2C 不會區分是否能夠針對隱含流程發出存取或識別碼權杖;如果在 **驗證** 分頁中選取了 [**識別碼權杖**] 選項，則這兩種類型的 token 都適用于隱含授與流程。
<!-- - Azure AD B2C doesn't currently support the single-page application "SPA" app type.  -->
- 在 UI 中不支援變更支援帳戶的值。 除非您要在 Azure AD 單一租使用者和多租使用者之間切換，否則您必須使用應用程式資訊清單。

## <a name="next-steps"></a>後續步驟

若要開始使用新的應用程式註冊體驗：
* 瞭解 [如何註冊 web 應用程式](tutorial-register-applications.md)。
* 瞭解 [如何註冊 WEB API](add-web-api-application.md)。
* 瞭解 [如何註冊原生用戶端應用程式](add-native-application.md)。
* 瞭解 [如何註冊 Microsoft Graph 的應用程式，以管理 Azure AD B2C 的資源](microsoft-graph-get-started.md)。
* 瞭解 [如何使用 Azure AD B2C 作為 SAML 服務提供者。](identity-provider-adfs.md)
* 深入瞭解 [應用程式類型](application-types.md)。