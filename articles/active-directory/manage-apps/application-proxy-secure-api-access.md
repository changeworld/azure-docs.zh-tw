---
title: 使用 Azure AD 應用程式代理訪問本地 API
description: Azure 活動目錄的應用程式代理允許本機應用安全地訪問您在本地或雲 VM 上託管的 API 和業務邏輯。
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77165995"
---
# <a name="secure-access-to-on-premises-apis-with-azure-ad-application-proxy"></a>使用 Azure AD 應用程式代理安全地訪問本地 API

您可能有業務邏輯 API 在本地運行，或託管在雲中的虛擬機器上。 您的本機 Android、iOS、Mac 或 Windows 應用需要與 API 終結點進行交互才能使用資料或提供使用者交互。 Azure AD 應用程式代理和[Azure 活動目錄身份驗證庫 （ADAL）](/azure/active-directory/develop/active-directory-authentication-libraries)允許本機應用安全地訪問本地 API。 與在應用層打開防火牆埠和控制身份驗證和授權相比，Azure 活動目錄應用程式代理是一個更快、更安全的解決方案。 

本文將引導您設置 Azure AD 應用程式代理解決方案，以便託管本機應用可以訪問的 Web API 服務。 

## <a name="overview"></a>總覽

下圖顯示了發佈本地 API 的傳統方法。 此方法需要打開傳入埠 80 和 443。

![傳統 API 訪問](./media/application-proxy-secure-api-access/overview-publish-api-open-ports.png)

下圖顯示了如何使用 Azure AD 應用程式代理在不打開任何傳入埠的情況下安全地發佈 API：

![Azure AD 應用程式代理 API 訪問](./media/application-proxy-secure-api-access/overview-publish-api-app-proxy.png)

Azure AD 應用程式代理構成解決方案的主幹，用作 API 訪問的公共終結點，並提供身份驗證和授權。 您可以使用[ADAL](/azure/active-directory/develop/active-directory-authentication-libraries)庫從大量平臺訪問 API。 

由於 Azure AD 應用程式代理身份驗證和授權構建在 Azure AD 之上，因此可以使用 Azure AD 條件訪問來確保只有受信任的設備才能訪問通過應用程式代理髮布的 API。 對桌面使用 Azure AD 聯接或 Azure AD 混合聯接，對設備使用 Intune 管理。 您還可以利用 Azure 活動目錄高級功能（如 Azure 多重要素驗證）和機器學習支援的[Azure 標識保護](/azure/active-directory/active-directory-identityprotection)安全性。

## <a name="prerequisites"></a>Prerequisites

要遵循本演練，您需要：

- 管理員訪問 Azure 目錄，具有可創建和註冊應用的帳戶
- 示例 Web API 和本機用戶端應用來自[https://github.com/jeevanbisht/API-NativeApp-ADAL-SampleApp](https://github.com/jeevanbisht/API-NativeApp-ADAL-SampleApp) 

## <a name="publish-the-api-through-application-proxy"></a>通過應用程式代理髮布 API

要通過應用程式代理在 Intranet 外部發佈 API，請遵循與發佈 Web 應用相同的模式。 有關詳細資訊，請參閱[教程：在 Azure 活動目錄中添加用於遠端存取的應用程式](application-proxy-add-on-premises-application.md)。

要通過應用程式代理髮布秘密 API：

1. 將示例 SecretAPI 專案生成併發布為本地電腦或 Intranet 上的ASP.NET Web 應用。 確保可以在本地訪問 Web 應用。 
   
1. 在 [[Azure 入口網站]](https://portal.azure.com) 中，選取 **[Azure Active Directory]**。 然後選擇**企業應用程式**。
   
1. 在**企業應用程式 - 所有應用程式**頁面的頂部，選擇 **"新應用程式**"。
   
1. 在"**添加應用程式**"頁上，選擇**本地應用程式**。 將顯示 **"添加您自己的本地應用程式**"頁。
   
1. 如果未安裝應用程式代理連接器，系統將提示您安裝它。 選擇 **"下載應用程式代理連接器**"以下載並安裝連接器。 
   
1. 在 **"添加您自己的本地應用程式**"頁上安裝應用程式代理連接器後：
   
   1. 在**名稱**旁邊，輸入*秘密 API*。
      
   1. 在**內部 URL**旁邊，輸入用於從 Intranet 內訪問 API 的 URL。
      
   1. 確保**預身份驗證**已設置為 Azure**活動目錄**。 
      
   1. 選擇 **"在**頁面頂部添加"，然後等待創建應用。
   
   ![添加 API 應用](./media/application-proxy-secure-api-access/3-add-api-app.png)
   
1. 在**企業應用程式 - 所有應用程式**頁面上，選擇**SecretAPI**應用。 
   
1. 在 **"機密 API - 概述"** 頁上，選擇左側導航中**的屬性**。
   
1. 您不希望 API 可供 **"MyApps"** 面板中的最終使用者使用，因此在 **"屬性"** 頁的底部將 **"使用者可見"** 設置為 **"否**"，然後選擇"**保存**"。
   
   ![使用者看不到](./media/application-proxy-secure-api-access/5-not-visible-to-users.png)
   
您已經通過 Azure AD 應用程式代理髮布了 Web API。 現在，添加可以訪問應用的使用者。 

1. 在 **"機密 API - 概述"** 頁上，選擇左側導航中的 **"使用者"和"組**"。
   
1. 在 **"使用者和組**"頁上，選擇 **"添加使用者**"。  
   
1. 在"**添加分配"** 頁上，選擇 **"使用者"和"組**"。 
   
1. 在 **"使用者和組**"頁上，搜索並選擇可以訪問應用的使用者，至少包括您自己。 選擇所有使用者後，選擇 **"選擇**"。 
   
   ![選擇和分配使用者](./media/application-proxy-secure-api-access/7-select-admin-user.png)
   
1. 返回"**添加分配"** 頁，選擇 **"分配**"。 

> [!NOTE]
> 使用集成 Windows 身份驗證的 API 可能需要[其他步驟](/azure/active-directory/manage-apps/application-proxy-configure-single-sign-on-with-kcd)。

## <a name="register-the-native-app-and-grant-access-to-the-api"></a>註冊本機應用並授予對 API 的存取權限

本機應用是開發用於特定平臺或設備上的程式。 在本機應用可以連接和訪問 API 之前，必須在 Azure AD 中註冊它。 以下步驟演示如何註冊本機應用，並授予它對通過應用程式代理髮布的 Web API 的存取權限。

要註冊 AppProxyNativeAppSample 本機應用：

1. 在 Azure 活動目錄**概述**頁上，選擇**應用註冊**，並在**應用註冊**窗格的頂部選擇 **"新建註冊**"。
   
1. 在 **"註冊應用程式**"頁上：
   
   1. 在**名稱**下，輸入*應用程式代理本機Appsample*。 
      
   1. 在 [支援的帳戶類型]**** 底下，選取 [任何組織目錄中的帳戶及個人的 Microsoft 帳戶]****。 
      
   1. 在**重定向 URL**下，下拉並選擇**公共用戶端（移動&桌面），** 然後輸入*HTTPs：/\/應用程式本機應用程式*。 
      
   1. 選擇 **"註冊**"，然後等待應用成功註冊。 
      
      ![新增應用程式註冊](./media/application-proxy-secure-api-access/8-create-reg-ga.png)
   
您現在已註冊了 Azure 活動目錄中的應用代理應用。 要授予本機應用對秘密 API Web API 的存取權限，請進行以下訪問：

1. 在 Azure 活動目錄**概述** > **應用註冊頁上**，選擇**AppProxyNativeAppSample**應用。 
   
1. 在**AppProxyNativeAppSample**頁上，在左側導航中選擇**API 許可權**。 
   
1. 在**API 許可權**頁上，選擇 **"添加許可權**"。
   
1. 在第一個**請求 API 許可權**頁上，選擇**組織使用的 API**選項卡，然後搜索並選擇**SecretAPI**。 
   
1. 在下一個**請求 API 許可權**頁上，選擇**user_impersonation**旁邊的核取方塊，然後選擇 **"添加許可權**"。 
   
    ![選取 API](./media/application-proxy-secure-api-access/10-secretapi-added.png)
   
1. 回到**API 許可權**頁，您可以選擇**Contoso 授予管理員同意，** 以防止其他使用者單獨同意應用。 

## <a name="configure-the-native-app-code"></a>配置本機應用代碼

最後一步是配置本機應用。 NativeClient 示例應用中*Form1.cs*檔中的以下片段導致 ADAL 庫獲取請求 API 呼叫的權杖，並將其作為承載器附加到應用標頭。 
   
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
   
要將本機應用配置為連接到 Azure 活動目錄並調用 API 應用代理，請使用 Azure AD 中的值更新本機用戶端示例應用的*App.config*檔中的預留位置值： 

- 將**目錄（租戶）ID**粘貼到`<add key="ida:Tenant" value="" />`欄位中。 您可以在任一應用的 **"概述"** 頁中查找和複製此值（GUID）。 
  
- 將 AppProxyNativeApp 示例**應用程式（用戶端）ID** `<add key="ida:ClientId" value="" />`粘貼到欄位中。 您可以在 AppProxyNativeApp 示例**概述**頁面查找和複製此值（GUID）。
  
- 將應用代理原點應用示例**重定向 URI**粘貼`<add key="ida:RedirectUri" value="" />`到欄位中。 您可以在 AppProxyNativeApp 示例**身份驗證**頁面查找和複製此值（URI）。 
  
- 將機密 API**應用程式 ID** `<add key="todo:TodoListResourceId" value="" />` URI 粘貼到欄位中。 可以從 SecretAPI**公開 API**頁中查找和複製此值（URI）。
  
- 將 SecretAPI**主頁 URL** `<add key="todo:TodoListBaseAddress" value="" />`粘貼到欄位中。 您可以在 SecretAPI**品牌**頁面查找和複製此值（URL）。

配置參數後，生成並運行本機應用。 當您選擇"**登錄**"按鈕時，應用允許您登錄，然後顯示一個成功螢幕，以確認它已成功連接到 SecretAPI。

![Success](./media/application-proxy-secure-api-access/success.png)

## <a name="next-steps"></a>後續步驟

- [教程：在 Azure 活動目錄中添加本地應用程式，以便通過應用程式代理進行遠端存取](application-proxy-add-on-premises-application.md)
- [快速入門：配置用戶端應用程式以訪問 Web API](../develop/quickstart-configure-app-access-web-apis.md)
- [如何啟用本機用戶端應用程式與代理應用程式交互](application-proxy-configure-native-client-application.md)
