---
title: 發佈原生用戶端應用程式 - Azure AD | Microsoft Docs
description: 涵蓋如何讓原生用戶端應用程式與 Azure AD 應用程式 Proxy 連接器通訊，為內部部署的應用程式提供安全的遠端存取。
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/15/2019
ms.author: mimart
ms.reviewer: japere
ms.custom: it-pro, has-adal-ref
ms.collection: M365-identity-device-management
ms.openlocfilehash: a649a6fab1fe85efc4edcfd2d3151ab85302101b
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/30/2020
ms.locfileid: "82610270"
---
# <a name="how-to-enable-native-client-applications-to-interact-with-proxy-applications"></a>如何讓原生用戶端應用程式與 proxy 應用程式互動

您可以使用 Azure Active Directory （Azure AD）應用程式 Proxy 來發佈 web 應用程式，但也可以用來發佈以 Azure AD 驗證程式庫（ADAL）設定的原生用戶端應用程式。 原生用戶端應用程式與 Web 應用程式不同，因為這種應用程式會安裝在裝置上，而 Web 應用程式則是透過瀏覽器存取。

為了支援原生用戶端應用程式，應用程式 Proxy 會接受在標頭中傳送 Azure AD 發行的權杖。 應用程式 Proxy 服務會對使用者進行驗證。 此解決方案不會使用應用程式權杖進行驗證。

![終端使用者、Azure AD 和已發佈應用程式之間的關聯性](./media/application-proxy-configure-native-client-application/richclientflow.png)

若要發佈原生應用程式，請使用 Azure AD 驗證程式庫，它會負責驗證並支援許多用戶端環境。 應用程式 Proxy 融入 [原生應用程式到 Web API 案例](../azuread-dev/native-app.md)。

本文引導您完成使用應用程式 Proxy 和 Azure AD 驗證程式庫發佈原生應用程式的四個步驟。

## <a name="step-1-publish-your-proxy-application"></a>步驟1：發佈您的 proxy 應用程式

如同任何其他應用程式一般，發佈您的 Proxy 應用程式，並指派使用者以存取您的應用程式。 如需詳細資訊，請參閱[使用應用程式 Proxy 發佈應用程式](application-proxy-add-on-premises-application.md)。

## <a name="step-2-register-your-native-application"></a>步驟2：註冊您的原生應用程式

您現在必須在 Azure AD 中註冊您的應用程式，如下所示：

1. 登入[Azure Active Directory 入口網站](https://aad.portal.azure.com/)。 **Azure Active Directory 系統管理中心**的 [**儀表板**] 隨即出現。
1. 在提要欄位中，選取 [ **Azure Active Directory**]。 [ **Azure Active Directory**總覽] 頁面隨即出現。
1. 在 Azure AD 總覽] 提要欄位中，選取 [**應用程式註冊**]。 所有應用程式註冊的清單隨即出現。
1. 選取 [新增註冊]  。 [**註冊應用程式**] 頁面隨即出現。

   ![在 Azure 入口網站中建立新的應用程式註冊](./media/application-proxy-configure-native-client-application/create.png)

1. 在 [**名稱**] 標題中，為您的應用程式指定使用者面向的顯示名稱。
1. 在 [**支援的帳戶類型**] 標題下，使用下列指導方針選取存取層級：

   - 若只要將組織內部的帳戶設為目標，請選取 [**僅此組織目錄中的帳戶**]。
   - 若只要以商業或教育客戶為目標，請選取**任何組織目錄中的 [帳戶**]。
   - 若要以最廣泛的 Microsoft 身分識別為目標，請選取 [**任何組織目錄中的帳戶] 和 [個人 Microsoft 帳戶**]。

1. 在 [重新**導向 uri** ] 標題中，選取 [**公用用戶端（行動 & 桌面）**]，然後輸入應用程式的重新導向 URI。
1. 選取並閱讀**Microsoft 平臺原則**，然後選取 [**註冊**]。 隨即會建立並顯示新應用程式註冊的 [總覽] 頁面。

如需建立新應用程式註冊的詳細資訊，請參閱[整合應用程式與 Azure Active Directory](../develop/quickstart-register-app.md)。

## <a name="step-3-grant-access-to-your-proxy-application"></a>步驟3：授與 proxy 應用程式的存取權

既然您已註冊原生應用程式，就可以讓它存取您目錄中的其他應用程式，在此案例中，是用來存取 proxy 應用程式。 若要讓原生應用程式公開至 proxy 應用程式：

1. 在 [新增應用程式註冊] 頁面的提要欄位中，選取 [ **API 許可權**]。 [新增應用程式註冊] 的 [ **API 許可權**] 頁面隨即出現。
1. 選取 [新增權限]****。 [**要求 API 許可權**] 頁面隨即出現。
1. 在 [**選取 api** ] 設定下，選取 [**我的組織使用的 api**]。 隨即會出現清單，其中包含您目錄中公開 Api 的應用程式。
1. 在 [搜尋] 方塊中鍵入，或按一下 [流覽] 以尋找您在[步驟1：發佈 proxy 應用程式](#step-1-publish-your-proxy-application)中發佈的 proxy 應用程式，然後選取 [proxy 應用程式]。
1. 在 [**您的應用程式需要何種許可權？** ] 標題中，選取許可權類型。 如果您的原生應用程式需要以登入的使用者身分存取 proxy 應用程式 API，請選擇 [**委派的許可權**]。
1. 在 [**選取許可權**] 標題中，選取所需的許可權，然後選取 [**新增許可權**]。 原生應用程式的 [ **API 許可權**] 頁面現在會顯示您所新增的 proxy 應用程式和許可權 API。

## <a name="step-4-edit-the-active-directory-authentication-library"></a>步驟 4：編輯 Active Directory 驗證程式庫

在 Active Directory 驗證程式庫 (ADAL) 的驗證內容中編輯原生應用程式程式碼，以包含下列文字：

```
// Acquire Access Token from AAD for Proxy Application
AuthenticationContext authContext = new AuthenticationContext("https://login.microsoftonline.com/<Tenant ID>");
AuthenticationResult result = await authContext.AcquireTokenAsync("< External Url of Proxy App >",
        "<App ID of the Native app>",
        new Uri("<Redirect Uri of the Native App>"),
        PromptBehavior.Never);

//Use the Access Token to access the Proxy Application
HttpClient httpClient = new HttpClient();
httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);
HttpResponseMessage response = await httpClient.GetAsync("< Proxy App API Url >");
```

您可以在 Azure AD 入口網站中找到範例程式碼中的必要資訊，如下所示：

| 需要的資訊 | 如何在 Azure AD 入口網站中尋找 |
| --- | --- |
| \<租使用者識別碼> | **Azure Active Directory** > **Properties**屬性 > **目錄識別碼** |
| \<Proxy 應用程式> 的外部 Url | **企業應用** > 程式*proxy 應用* > **程式 proxy** > **外部 Url** |
| \<原生應用程式的應用程式識別碼> | **企業應用程式** > *您的原生應用* > 程式**屬性** > **應用程式識別碼** |
| \<原生應用程式的重新導向 URI> | **Azure Active Directory** > **App registrations**應用程式註冊 > *您的原生應用程式* > 重新**導向 uri** |
| \<Proxy 應用程式 API Url> | **Azure Active Directory** > **App registrations**應用程式註冊 > *您的原生應用程式* > **api 許可權** > **api/許可權名稱** |

使用這些參數編輯 ADAL 之後，您的使用者就可以驗證原生用戶端應用程式，即使它們在公司網路外部也一樣。

## <a name="next-steps"></a>後續步驟

如需原生應用程式流程的詳細資訊，請參閱[Azure Active Directory 中的原生應用](../azuread-dev/native-app.md)程式。

瞭解如何[在 Azure Active Directory 中設定應用程式的單一登入](what-is-single-sign-on.md#choosing-a-single-sign-on-method)。
