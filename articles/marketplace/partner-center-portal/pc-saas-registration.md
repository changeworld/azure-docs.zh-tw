---
title: 註冊 SaaS 應用程式 |Azure 應用商店
description: 說明如何使用 Azure 門戶註冊 SaaS 應用程式。
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: dsindona
ms.openlocfilehash: 9c20fe34e108de95a34aabea56390e8a6f0d858f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80275708"
---
# <a name="register-a-saas-application"></a>註冊 SaaS 應用程式

本文介紹如何使用 Microsoft [Azure 門戶](https://portal.azure.com/)註冊 SaaS 應用程式。  註冊成功後，您將收到一個 Azure 活動目錄 （Azure AD） 安全權杖，可用於訪問 SaaS 履行 API。  有關 Azure AD 的詳細資訊，請參閱[什麼是身份驗證？](https://docs.microsoft.com/azure/active-directory/develop/authentication-scenarios)


## <a name="service-to-service-authentication-flow"></a>服務到服務身份驗證流

下圖顯示新客戶的訂閱流程和使用這些 API 的時間：

![SaaS 供應項目的 API 流程](./media/saas-offer-publish-api-flow-v1.png)

Azure 不會對 SaaS 服務向終端使用者顯示的驗證施加任何限制式。 但是，使用 SaaS 履行 API 進行身份驗證時使用 Azure AD 安全權杖執行，通常通過 Azure 門戶註冊 SaaS 應用來獲取。 


## <a name="register-an-azure-ad-secured-app"></a>註冊 Azure AD 保護應用

任何想要使用 Azure AD 功能的應用程式都必須先在 Azure AD 租用戶中註冊。 此註冊程序包含提供應用程式的 Azure AD 相關詳細資料，例如其所在的 URL、要在使用者經驗證後傳送回應的 URL，以及可識別應用程式的 URI 等。  若要使用 Azure 入口網站註冊新的應用程式，請執行下列步驟：

1.  登錄到 Azure[門戶](https://portal.azure.com/)。
2.  如果您的帳戶允許您訪問多個帳戶，請按一下右上角的帳戶，並將門戶會話設置為所需的 Azure AD 租戶。
3.  在左側導覽窗格中，依序按一下 [Azure Active Directory]**** 服務、[應用程式註冊]**** 和 [新增應用程式註冊]****。

    ![SaaS AD 應用程式註冊](./media/saas-offer-app-registration-v1.png)

4.  在 [建立] 頁面中，輸入您的應用程式\'註冊資訊：
    -   **名稱**：請輸入有意義的應用程式名稱
    -   **應用程式類型**： 
        - 為本地安裝在設備上的[用戶端應用程式](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#client-application)選擇**本機**。 此設定適用於 OAuth 公用[原生用戶端](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#native-client)。
        - 為安裝在安全伺服器上的[用戶端應用程式](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#client-application)[和資源/API 應用程式](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#resource-server)選擇**Web 應用/API。** 此設置用於 OAuth 機密[Web 用戶端](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#web-client)和[基於公共使用者代理的用戶端](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#user-agent-based-client)。
        相同的應用程式也可以公開用戶端和資源/API。
    -   **登入 URL：** 在 [Web 應用程式/API] 應用程式中，提供您應用程式的基底 URL。 例如，**http://localhost:31544**可能是在本地電腦上運行的 Web 應用的 URL。 接著，使用者會使用此 URL 來登入 Web 用戶端應用程式。
    -   **重新導向 URI：** 在 [原生] 應用程式中，提供 Azure AD 用來傳回權杖回應的 URI。 輸入特定于應用程式的值，例如**http://MyFirstAADApp**。

        ![SaaS AD 應用程式註冊](./media/saas-offer-app-registration-v1-2.png)

        有關 Web 應用程式或本機應用程式的特定示例，請查看[Azure AD 開發人員指南](https://docs.microsoft.com/azure/active-directory/develop/active-directory-developers-guide)的"*入門"* 部分中提供的快速入門引導設置。

5.  完成後，按一下 [建立]****。 Azure AD 向應用程式分配唯一*的應用程式 ID，* 然後\'重新訪問應用程式\'的主註冊頁。 根據您的應用程式是 Web 還是原生應用程式，系統會提供您不同選項以供您在應用程式中新增其他功能。

>[!Note]
>根據預設，新註冊的應用程式會設定為只允許相同租用戶的使用者登入您的應用程式。


## <a name="using-the-azure-ad-security-token"></a>使用 Azure AD 安全權杖

註冊應用程式後，可以以程式設計方式請求 Azure AD 安全權杖。  發行者應使用此權杖，並請求解析它。  使用各種履行 API 時，當使用者從 Azure 重定向到 SaaS 網站時，權杖查詢參數位於 URL 中。  此權杖僅有效一小時。  此外，在使用權杖值之前，應從瀏覽器中解碼權杖值。

有關這些權杖的詳細資訊，請參閱 Azure[活動目錄訪問權杖](https://docs.microsoft.com/azure/active-directory/develop/access-tokens)。


### <a name="get-a-token-based-on-the-azure-ad-app"></a>取得以 Azure AD 應用程式為基礎的權杖

HTTP 方法

`POST`

*要求 URL*

**https://login.microsoftonline.com/*[租戶 Id]*/auth2/權杖**

*URI 參數*

|  **參數名稱**  | **必要**  | **描述**                               |
|  ------------------  | ------------- | --------------------------------------------- |
| tenantId             | True          | 已註冊的 AAD 應用程式租用戶識別碼   |
|  |  |  |


*要求標頭*

|  **標頭名稱**  | **必要** |  **描述**                                   |
|  --------------   | ------------ |  ------------------------------------------------- |
|  Content-Type     | True         | 與要求相關聯的內容類型。 預設值是 `application/x-www-form-urlencoded`。  |
|  |  |  |


*請求正文*

| **屬性名稱**   | **必要** |  **描述**                                                          |
| -----------------   | -----------  | ------------------------------------------------------------------------- |
|  Grant_type         | True         | 授與類型。 預設值是 `client_credentials`。                    |
|  Client_id          | True         |  與 Azure AD 應用程式相關聯的用戶端/應用程式識別碼。                  |
|  client_secret      | True         |  與 Azure AD 應用程式相關聯的密碼。                               |
|  資源           | True         |  要求權杖的目標資源。 預設值是 `62d94f6c-d599-489b-a797-3e10e42fbe22`。 |
|  |  |  |


*回應*

|  **名稱**  | **類型**       |  **描述**    |
| ---------- | -------------  | ------------------- |
| 200 確定    | TokenResponse  | 要求成功   |
|  |  |  |

*TokenResponse*

權杖回應範例：

``` json
  {
      "token_type": "Bearer",
      "expires_in": "3600",
      "ext_expires_in": "0",
      "expires_on": "15251…",
      "not_before": "15251…",
      "resource": "62d94f6c-d599-489b-a797-3e10e42fbe22",
      "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6ImlCakwxUmNxemhpeTRmcHhJeGRacW9oTTJZayIsImtpZCI6ImlCakwxUmNxemhpeTRmcHhJeGRacW9oTTJZayJ9…"
  }               
```


## <a name="next-steps"></a>後續步驟

Azure AD 保護應用現在可以使用[SaaS 履行 API 版本 2](./pc-saas-fulfillment-api-v2.md)。
