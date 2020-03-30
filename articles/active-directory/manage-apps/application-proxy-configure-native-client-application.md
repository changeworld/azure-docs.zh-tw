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
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3d2a2bb9dd543da7455a276075a829ef06032edb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77159279"
---
# <a name="how-to-enable-native-client-applications-to-interact-with-proxy-applications"></a>如何啟用本機用戶端應用程式與代理應用程式交互

可以使用 Azure 活動目錄 （Azure AD） 應用程式代理髮布 Web 應用，但它也可用於發佈使用 Azure AD 身份驗證庫 （ADAL） 配置的本機用戶端應用程式。 原生用戶端應用程式與 Web 應用程式不同，因為這種應用程式會安裝在裝置上，而 Web 應用程式則是透過瀏覽器存取。

為了支援本機用戶端應用程式，應用程式代理接受在標頭中發送的 Azure AD 頒發的權杖。 應用程式代理服務對使用者執行身份驗證。 此解決方案不使用應用程式權杖進行身份驗證。

![最終使用者、Azure AD 和已發佈應用程式之間的關係](./media/application-proxy-configure-native-client-application/richclientflow.png)

要發佈本機應用程式，請使用 Azure AD 身份驗證庫，該庫負責身份驗證並支援許多用戶端環境。 應用程式 Proxy 融入 [原生應用程式到 Web API 案例](../azuread-dev/native-app.md)。

本文引導您完成使用應用程式 Proxy 和 Azure AD 驗證程式庫發佈原生應用程式的四個步驟。

## <a name="step-1-publish-your-proxy-application"></a>第 1 步：發佈代理應用程式

如同任何其他應用程式一般，發佈您的 Proxy 應用程式，並指派使用者以存取您的應用程式。 有關詳細資訊，請參閱[使用應用程式代理髮布應用程式](application-proxy-add-on-premises-application.md)。

## <a name="step-2-register-your-native-application"></a>第 2 步：註冊本機應用程式

現在需要在 Azure AD 中註冊應用程式，如下所示：

1. 登錄到 Azure[活動目錄門戶](https://aad.portal.azure.com/)。 將顯示 Azure**活動目錄管理中心**的**儀表板**。
1. 在邊欄中，選擇**Azure 活動目錄**。 將顯示**Azure 活動目錄**概覽頁。
1. 在 Azure AD 概述邊欄中，選擇**應用註冊**。 將顯示所有應用註冊的清單。
1. 選取 [新增註冊]****。 將顯示 **"註冊應用程式**"頁。

   ![在 Azure 門戶中創建新的應用註冊](./media/application-proxy-configure-native-client-application/create.png)

1. 在 **"名稱"** 標題中，為應用程式指定面向使用者的顯示名稱。
1. 在 **"支援帳戶類型"** 標題下，使用以下準則選擇存取層級：

   - 要僅針對組織內部的帳戶，請選擇 **"僅在此組織目錄中的帳戶**"。
   - 要僅針對業務或教育客戶，請選擇**任何組織目錄中的帳戶**。
   - 要定位最廣泛的 Microsoft 標識集，請選擇**任何組織目錄中的帳戶和個人 Microsoft 帳戶**。

1. 在**重定向 URI**標題中，選擇**公共用戶端（移動&桌面），** 然後鍵入應用程式的重定向 URI。
1. 選擇並閱讀**Microsoft 平臺策略**，然後選擇 **"註冊**"。 創建並顯示新應用程式註冊的概覽頁。

有關創建新應用程式註冊的詳細資訊，請參閱[將應用程式與 Azure 活動目錄集成](../develop/quickstart-register-app.md)。

## <a name="step-3-grant-access-to-your-proxy-application"></a>第 3 步：授予對代理應用程式的存取權限

現在，您已經註冊了本機應用程式，您可以授予它訪問目錄中的其他應用程式（本例中為訪問代理應用程式） 要使本機應用程式能夠公開給代理應用程式，請執行以下情況：

1. 在新應用程式註冊頁的側邊欄中，選擇**API 許可權**。 將顯示新應用程式註冊的**API 許可權**頁。
1. 選取 [新增權限]****。 將顯示 **"請求 API 許可權**"頁。
1. 在 **"選擇 API"** 設置下，選擇**組織使用的 API。** 將顯示一個清單，其中包含目錄中公開 API 的應用程式。
1. 在搜索框中鍵入或滾動以查找在步驟 1 中發佈的代理應用程式[：發佈代理應用程式](#step-1-publish-your-proxy-application)，然後選擇代理應用程式。
1. 在"**應用程式需要哪些類型的許可權"** 標題中，選擇許可權類型。 如果本機應用程式需要作為登錄使用者訪問代理應用程式 API，請選擇**委派許可權**。
1. 在 **"選擇許可權"** 標題中，選擇所需的許可權，然後選擇 **"添加許可權**"。 本機應用程式的**API 許可權**頁現在顯示您添加的代理應用程式和許可權 API。

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

示例代碼中所需的資訊可在 Azure AD 門戶中找到，如下所示：

| 所需資訊 | 如何在 Azure AD 門戶中找到它 |
| --- | --- |
| \<租戶 ID> | **Azure 活動目錄** > **屬性** > **目錄 ID** |
| \<代理應用程式>的外部 Url | **企業應用程式** > *您的代理* > **應用程式應用程式代理** > **外部 Url** |
| \<本機應用的應用 id> | **企業應用程式** > *您的本機應用程式* > **屬性** > **應用程式 ID** |
| \<重定向本機應用>的 URI | **Azure 活動目錄** > **應用註冊** > *本機應用程式* > **重定向 URI** |
| \<代理應用 API Url> | **Azure 活動目錄** > **應用註冊** > *本機應用程式* > **API 許可權** > **API /許可權名稱** |

使用這些參數編輯 ADAL 後，即使本地用戶端應用程式處於公司網路之外，也可以對本機用戶端應用程式進行身份驗證。

## <a name="next-steps"></a>後續步驟

有關本機應用程式流的詳細資訊，請參閱 Azure[活動目錄中的本機應用](../azuread-dev/native-app.md)。

瞭解如何在[Azure 活動目錄中設置應用程式的單一登入](what-is-single-sign-on.md#choosing-a-single-sign-on-method)。
