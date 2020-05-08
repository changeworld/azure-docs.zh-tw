---
title: 註冊 SaaS 應用程式-Azure Marketplace
description: 瞭解如何使用 Azure 入口網站來註冊 SaaS 應用程式，並接收 Azure Active Directory 的安全性權杖。
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: dsindona
ms.openlocfilehash: b3c20d25917d66cba8ae3d811eddaa6455b87722
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/05/2020
ms.locfileid: "82792950"
---
# <a name="register-a-saas-application"></a>註冊 SaaS 應用程式

本文說明如何使用 Microsoft [Azure 入口網站](https://portal.azure.com/)註冊 SaaS 應用程式。  成功註冊之後，您會收到可用來存取 SaaS 履行 Api 的 Azure Active Directory （Azure AD）安全性權杖。  如需 Azure AD 的詳細資訊，請參閱[什麼是驗證？](https://docs.microsoft.com/azure/active-directory/develop/authentication-scenarios)

## <a name="service-to-service-authentication-flow"></a>服務對服務驗證流程

下圖顯示新客戶的訂閱流程和使用這些 API 的時間：

![SaaS 供應項目的 API 流程](./media/saas-offer-publish-api-flow-v1.png)

Azure 不會對 SaaS 服務向終端使用者顯示的驗證施加任何限制式。 不過，使用 SaaS 履行 Api 進行驗證時，會使用 Azure AD 的安全性權杖來執行，通常是透過 Azure 入口網站註冊 SaaS 應用程式來取得。 

## <a name="register-an-azure-ad-secured-app"></a>註冊 Azure AD 保護的應用程式

任何想要使用 Azure AD 功能的應用程式都必須先在 Azure AD 租用戶中註冊。 此註冊程序包含提供應用程式的 Azure AD 相關詳細資料，例如其所在的 URL、要在使用者經驗證後傳送回應的 URL，以及可識別應用程式的 URI 等。  若要使用 Azure 入口網站註冊新的應用程式，請執行下列步驟：

1.  登入 [Azure 入口網站](https://portal.azure.com/)。
2.  如果您的帳戶可讓您存取多個使用者，請在右上角按一下您的帳戶，並將入口網站會話設定為所需的 Azure AD 租使用者。
3.  在左側導覽窗格中，依序按一下 [Azure Active Directory]**** 服務、[應用程式註冊]**** 和 [新增應用程式註冊]****。

    ![SaaS AD 應用程式註冊](./media/saas-offer-app-registration-v1.png)

4.  在 [建立] 頁面中，輸入您的應用程式\'註冊資訊：
    -   **名稱**：請輸入有意義的應用程式名稱
    -   **應用程式類型**： 
        - 針對在裝置本機上安裝的[用戶端應用程式](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#client-application)選取 [**原生**]。 此設定適用於 OAuth 公用[原生用戶端](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#native-client)。
        - 針對安裝在安全伺服器上的[用戶端應用程式](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#client-application)和[資源/api 應用程式](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#resource-server)選取 [ **Web 應用程式/api** ]。 此設定適用于 OAuth 機密[web 用戶端](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#web-client)和公用[使用者代理程式型用戶端](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#user-agent-based-client)。
        相同的應用程式也可以公開用戶端和資源/API。
    -   **登入 URL：** 在 [Web 應用程式/API] 應用程式中，提供您應用程式的基底 URL。 例如， **http://localhost:31544**可能是在本機電腦上執行之 web 應用程式的 URL。 接著，使用者會使用此 URL 來登入 Web 用戶端應用程式。
    -   **重新導向 URI：** 在 [原生] 應用程式中，提供 Azure AD 用來傳回權杖回應的 URI。 輸入應用程式特定的值，例如**http://MyFirstAADApp**。

        ![SaaS AD 應用程式註冊](./media/saas-offer-app-registration-v1-2.png)

        如需 web 應用程式或原生應用程式的特定範例，請參閱《 [Azure AD 開發人員指南》](https://docs.microsoft.com/azure/active-directory/develop/active-directory-developers-guide)中的*開始*使用一節所提供的快速入門引導式設置。

5.  完成後，按一下 [建立]  。 Azure AD 會\'將唯一的*應用程式識別碼*指派給您的應用程式，並重新\'進入應用程式的主要註冊頁面。 根據您的應用程式是 Web 還是原生應用程式，系統會提供您不同選項以供您在應用程式中新增其他功能。

>[!Note]
>根據預設，新註冊的應用程式會設定為只允許相同租用戶的使用者登入您的應用程式。

## <a name="using-the-azure-ad-security-token"></a>使用 Azure AD 安全性權杖

註冊應用程式之後，您可以透過程式設計方式要求 Azure AD 的安全性權杖。  發行者應使用此權杖，並提出要求來解決此問題。  使用各種履行 Api 時，權杖查詢參數會在使用者從 Azure 重新導向至 SaaS 網站時的 URL 中。  此權杖只適用于一小時。  此外，您應該在使用瀏覽器的權杖值之前，先對其進行 URL 解碼。

如需這些權杖的詳細資訊，請參閱[Azure Active Directory 存取權杖](https://docs.microsoft.com/azure/active-directory/develop/access-tokens)。


### <a name="get-a-token-based-on-the-azure-ad-app"></a>取得以 Azure AD 應用程式為基礎的權杖

HTTP 方法

`POST`

*要求 URL*

**https://login.microsoftonline.com/*{tenantId}*/oauth2/token**

*URI 參數*

|  **參數名稱**  | **必要**  | **說明**                               |
|  ------------------  | ------------- | --------------------------------------------- |
| tenantId             | True          | 已註冊的 AAD 應用程式租用戶識別碼   |
|  |  |  |


*要求標頭*

|  **標頭名稱**  | **必要** |  **說明**                                   |
|  --------------   | ------------ |  ------------------------------------------------- |
|  Content-Type     | True         | 與要求相關聯的內容類型。 預設值為 `application/x-www-form-urlencoded`。  |
|  |  |  |


*要求本文*

| **屬性名稱**   | **必要** |  **說明**                                                          |
| -----------------   | -----------  | ------------------------------------------------------------------------- |
|  Grant_type         | True         | 授與類型。 預設值為 `client_credentials`。                    |
|  Client_id          | True         |  與 Azure AD 應用程式相關聯的用戶端/應用程式識別碼。                  |
|  client_secret      | True         |  與 Azure AD 應用程式相關聯的密碼。                               |
|  資源           | True         |  要求權杖的目標資源。 預設值為 `62d94f6c-d599-489b-a797-3e10e42fbe22`。 |
|  |  |  |


*回應*

|  **名稱**  | **型別**       |  **說明**    |
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

Azure AD 保護的應用程式現在可以使用[SaaS 履行 API 第2版](./pc-saas-fulfillment-api-v2.md)。
