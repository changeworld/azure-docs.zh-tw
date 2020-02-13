---
title: 使用 Azure AD 應用程式 Proxy 存取內部部署 Api
description: Azure Active Directory 的應用程式 Proxy 可讓原生應用程式安全地存取您裝載于內部部署或雲端 Vm 上的 Api 和商務邏輯。
services: active-directory
author: jeevanbisht
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: mimart
ms.reviewer: japere
ms.openlocfilehash: ecd5d8bae22d67f8d9f5b99d5c94eecf54a4a1f3
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/12/2020
ms.locfileid: "77165995"
---
# <a name="secure-access-to-on-premises-apis-with-azure-ad-application-proxy"></a>使用 Azure AD 應用程式 Proxy 來保護內部部署 Api 的存取

您可能有在內部部署執行的商務邏輯 Api，或裝載在雲端的虛擬機器上。 您的原生 Android、iOS、Mac 或 Windows 應用程式必須與 API 端點互動，才能使用資料或提供使用者互動。 Azure AD 應用程式 Proxy 和[Azure Active Directory 驗證程式庫（ADAL）](/azure/active-directory/develop/active-directory-authentication-libraries)可讓您的原生應用程式安全地存取您的內部部署 api。 Azure Active Directory 應用程式 Proxy 是更快速且更安全的解決方案，而不是在應用層開啟防火牆埠和控制驗證和授權。 

本文會逐步引導您設定 Azure AD 應用程式 Proxy 解決方案，以裝載原生應用程式可以存取的 Web API 服務。 

## <a name="overview"></a>概觀

下圖顯示發行內部部署 Api 的傳統方式。 這種方法需要開啟連入埠80和443。

![傳統 API 存取](./media/application-proxy-secure-api-access/overview-publish-api-open-ports.png)

下圖顯示如何使用 Azure AD 應用程式 Proxy 來安全地發佈 Api，而不需要開啟任何連入的埠：

![Azure AD 應用程式 Proxy API 存取](./media/application-proxy-secure-api-access/overview-publish-api-app-proxy.png)

Azure AD 應用程式 Proxy 會形成解決方案的骨幹、做為 API 存取的公用端點，以及提供驗證和授權。 您可以使用[ADAL](/azure/active-directory/develop/active-directory-authentication-libraries)程式庫，從大量的平臺存取您的 api。 

由於 Azure AD 的應用程式 Proxy 驗證和授權是建置於 Azure AD 之上，因此您可以使用 Azure AD 條件式存取，以確保只有受信任的裝置可以存取透過應用程式 Proxy 發佈的 Api。 針對桌上型電腦使用 Azure AD 加入或 Azure AD 混合式聯結，以及針對裝置管理 Intune。 您也可以利用 azure 多因素驗證等 Azure Active Directory Premium 功能，以及[Azure Identity Protection](/azure/active-directory/active-directory-identityprotection)的機器學習支援安全性。

## <a name="prerequisites"></a>Prerequisites

若要遵循此逐步解說，您需要：

- 使用可建立及註冊應用程式的帳戶，來管理 Azure 目錄的存取權
- 範例 Web API 和原生用戶端應用程式， [https://github.com/jeevanbisht/API-NativeApp-ADAL-SampleApp](https://github.com/jeevanbisht/API-NativeApp-ADAL-SampleApp) 

## <a name="publish-the-api-through-application-proxy"></a>透過應用程式 Proxy 發佈 API

若要透過應用程式 Proxy 在內部網路外部發行 API，您可以遵循與發佈 web 應用程式相同的模式。 如需詳細資訊，請參閱[教學課程：在 Azure Active Directory 中新增透過應用程式 Proxy 進行遠端存取的內部部署應用程式](application-proxy-add-on-premises-application.md)。

若要透過應用程式 Proxy 發佈 SecretAPI Web API：

1. 在您的本機電腦或內部網路上建立範例 SecretAPI 專案，並將其發佈為 ASP.NET web 應用程式。 請確定您可以在本機存取 web 應用程式。 
   
1. 在 [[Azure 入口網站]](https://portal.azure.com) 中，選取 **[Azure Active Directory]** 。 然後選取 [**企業應用程式**]。
   
1. 在 [**企業應用程式-所有應用程式**] 頁面的頂端，選取 [**新增應用程式**]。
   
1. 在 [**新增應用程式**] 頁面上，選取 [**內部部署應用程式**]。 [**新增您自己的內部部署應用程式**] 頁面隨即出現。
   
1. 如果您沒有安裝應用程式 Proxy 連接器，系統會提示您安裝它。 選取 [**下載應用程式 Proxy 連接器**] 以下載並安裝連接器。 
   
1. 安裝應用程式 Proxy 連接器之後，請在 [**新增您自己的內部部署應用程式**] 頁面上：
   
   1. 在 [**名稱**] 旁，輸入*SecretAPI*。
      
   1. 在 [**內部 Url**] 旁，輸入您用來從內部網路存取 API 的 Url。
      
   1. 請確定 [**預先驗證**] 設定為 [ **Azure Active Directory**]。 
      
   1. 選取頁面頂端的 [**新增**]，並等候應用程式建立。
   
   ![新增 API 應用程式](./media/application-proxy-secure-api-access/3-add-api-app.png)
   
1. 在 [**企業應用程式-所有應用程式**] 頁面上，選取 [ **SecretAPI** ] 應用程式。 
   
1. 在 [ **SecretAPI-總覽**] 頁面上，選取左側導覽中的 [**屬性**]。
   
1. 您不想讓 Api 可供使用者在 [ **MyApps** ] 面板中使用，因此**請在 [內容] 頁面底部**將 [**使用者可見**] 設定為 [**否**]，然後選取 [**儲存**]。
   
   ![使用者看不到](./media/application-proxy-secure-api-access/5-not-visible-to-users.png)
   
您已透過 Azure AD 應用程式 Proxy 發佈您的 Web API。 現在，新增可存取應用程式的使用者。 

1. 在 [ **SecretAPI-總覽**] 頁面上，選取左側導覽中的 [**使用者和群組**]。
   
1. 在 [**使用者和群組**] 頁面上，選取 [**新增使用者**]。  
   
1. 在 [**新增指派**] 頁面上，選取 [**使用者和群組**]。 
   
1. 在 [**使用者和群組**] 頁面上，搜尋並選取可存取應用程式的使用者，至少包括您自己。 選取 [所有使用者] 之後，選取 [**選取**]。 
   
   ![選取並指派使用者](./media/application-proxy-secure-api-access/7-select-admin-user.png)
   
1. 回到 [**新增指派**] 頁面上，選取 [**指派**]。 

> [!NOTE]
> 使用整合式 Windows 驗證的 Api 可能需要[額外的步驟](/azure/active-directory/manage-apps/application-proxy-configure-single-sign-on-with-kcd)。

## <a name="register-the-native-app-and-grant-access-to-the-api"></a>註冊原生應用程式，並授與 API 的存取權

原生應用程式是開發用來在特定平臺或裝置上使用的程式。 在您的原生應用程式可以連線並存取 API 之前，您必須先在 Azure AD 中註冊它。 下列步驟示範如何註冊原生應用程式，並讓它能夠存取您透過應用程式 Proxy 發佈的 Web API。

若要註冊 AppProxyNativeAppSample 原生應用程式：

1. 在 [Azure Active Directory**總覽**] 頁面上，選取 [**應用程式註冊**]，然後在 [**應用程式註冊**] 窗格的頂端選取 [**新增註冊**]。
   
1. 在 [**註冊應用程式**] 頁面上：
   
   1. 在 [**名稱**] 下，輸入*AppProxyNativeAppSample*。 
      
   1. 在 [支援的帳戶類型] 底下，選取 [任何組織目錄中的帳戶及個人的 Microsoft 帳戶]。 
      
   1. 在 [重新**導向 URL**] 底下，下拉並選取 **[公用用戶端（行動 & 桌面）** ]，然後輸入*HTTPs：\//appproxynativeapp*。 
      
   1. 選取 [**註冊**]，然後等候應用程式成功註冊。 
      
      ![新增應用程式註冊](./media/application-proxy-secure-api-access/8-create-reg-ga.png)
   
您現在已在 Azure Active Directory 中註冊 AppProxyNativeAppSample 應用程式。 若要將您的原生應用程式存取權授與 SecretAPI Web API：

1. 在 Azure Active Directory**總覽** > **應用程式註冊** 頁面上，選取  **AppProxyNativeAppSample**  應用程式。 
   
1. 在 [ **AppProxyNativeAppSample** ] 頁面上，選取左側導覽中的 [ **API 許可權**]。 
   
1. 在 [ **API 許可權**] 頁面上，選取 [**新增許可權**]。
   
1. 在第一個 [**要求 API 許可權**] 頁面上，選取 [**我的組織使用的 api** ] 索引標籤，然後搜尋並選取 [ **SecretAPI**]。 
   
1. 在 [下一個**要求 API 許可權**] 頁面上，選取 [ **user_impersonation**] 旁的核取方塊，然後選取 [**新增許可權**]。 
   
    ![選取 API](./media/application-proxy-secure-api-access/10-secretapi-added.png)
   
1. 回到 [ **API 許可權**] 頁面，您可以選取 **[授與 Contoso 的系統管理員同意**]，以防止其他使用者個別同意應用程式。 

## <a name="configure-the-native-app-code"></a>設定原生應用程式代碼

最後一個步驟是設定原生應用程式。 下列來自 Nativeclient-android 範例應用程式中*Form1.cs*檔案的程式碼片段會導致 ADAL 程式庫取得要求 API 呼叫的權杖，並將其附加為應用程式標頭的持有人。 
   
   ```csharp
       AuthenticationResult result = null;
       HttpClient httpClient = new HttpClient();
       authContext = new AuthenticationContext(authority);
       result = await authContext.AcquireTokenAsync(todoListResourceId, clientId, redirectUri, new PlatformParameters(PromptBehavior.Auto));
       
       // Append the token as bearer in the request header.
       httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);
       
       // Call the API.
       HttpResponseMessage response = await httpClient.GetAsync(todoListBaseAddress + "/api/values/4");
   
       // MessageBox.Show(response.RequestMessage.ToString());
       string s = await response.Content.ReadAsStringAsync();
       MessageBox.Show(s);
   ```
   
若要將原生應用程式設定為連接到 Azure Active Directory 並呼叫 API 應用程式 Proxy，請使用來自 Azure AD 的值，更新 Nativeclient-android 範例應用*程式 app.config 檔案*中的預留位置值： 

- 在 [`<add key="ida:Tenant" value="" />`] 欄位中貼上**目錄（租使用者）識別碼**。 您可以從任何一個應用程式的 [**總覽**] 頁面，尋找並複製此值（GUID）。 
  
- 在 [`<add key="ida:ClientId" value="" />`] 欄位中貼上 AppProxyNativeAppSample**應用程式（用戶端）識別碼**。 您可以從 [AppProxyNativeAppSample] [**總覽**] 頁面尋找並複製此值（GUID）。
  
- 在 [`<add key="ida:RedirectUri" value="" />`] 欄位中貼上 AppProxyNativeAppSample 重新**導向 URI** 。 您可以從 [AppProxyNativeAppSample **Authentication** ] 頁面尋找並複製此值（URI）。 
  
- 在 [`<add key="todo:TodoListResourceId" value="" />`] 欄位中貼上 SecretAPI**應用程式識別碼 URI** 。 您可以從 [SecretAPI] [**公開 API** ] 頁面中找到並複製此值（URI）。
  
- 在 [`<add key="todo:TodoListBaseAddress" value="" />`] 欄位中貼上 SecretAPI**首頁 URL** 。 您可以從 [SecretAPI**商標**] 頁面尋找並複製此值（URL）。

設定參數之後，請建立並執行原生應用程式。 當您選取 [登**入**] 按鈕時，應用程式可讓您登入，然後顯示成功畫面，確認它已成功連線到 SecretAPI。

![Success](./media/application-proxy-secure-api-access/success.png)

## <a name="next-steps"></a>後續步驟

- [教學課程：在 Azure Active Directory 中新增內部部署應用程式，以透過應用程式 Proxy 進行遠端存取](application-proxy-add-on-premises-application.md)
- [快速入門：設定用戶端應用程式以存取 web Api](../develop/quickstart-configure-app-access-web-apis.md)
- [如何讓原生用戶端應用程式與 proxy 應用程式互動](application-proxy-configure-native-client-application.md)
