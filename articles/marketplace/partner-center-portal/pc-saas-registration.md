---
title: 註冊 SaaS 應用程式-Azure Marketplace
description: 瞭解如何使用 Azure 入口網站來註冊 SaaS 應用程式，並接收 Azure Active Directory 的安全性權杖。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 06/10/2020
ms.openlocfilehash: 85bd6f4192f5c1f47856851ab53521a101340007
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/08/2020
ms.locfileid: "86109258"
---
# <a name="register-a-saas-application"></a>註冊 SaaS 應用程式

本文說明如何使用 Microsoft [Azure 入口網站](https://portal.azure.com/)註冊 SaaS 應用程式，以及如何取得發行者的存取權杖（Azure Active Directory 存取權杖）。 發行者會藉由呼叫 SaaS 履行 Api，使用此權杖來驗證 SaaS 應用程式。  履行 Api 會使用 OAuth 2.0 用戶端認證來授與 Azure Active Directory （v1.0）端點的流程，以進行服務對服務存取權杖要求。

Azure Marketplace 不會對您的 SaaS 服務針對使用者所使用的驗證方法強加任何條件約束。 只有在 Azure Marketplace 中驗證 SaaS 服務時，才需要下列流程。

如需 Azure AD （Active Directory）的詳細資訊，請參閱[什麼是驗證](../../active-directory/develop/authentication-scenarios.md)？

## <a name="register-an-azure-ad-secured-app"></a>註冊 Azure AD 保護的應用程式

任何想要使用 Azure AD 功能的應用程式都必須先在 Azure AD 租用戶中註冊。 此註冊套裝程式含提供 Azure AD 您的應用程式的一些詳細資料。 若要使用 Azure 入口網站註冊新的應用程式，請執行下列步驟：

1. 登入 [Azure 入口網站](https://portal.azure.com/)。
2. 如果您的帳戶可讓您存取多個使用者，請在右上角按一下您的帳戶，並將入口網站會話設定為所需的 Azure AD 租使用者。
3. 在左側導覽窗格中，依序按一下 [Azure Active Directory]**** 服務、[應用程式註冊]**** 和 [新增應用程式註冊]****。

    ![SaaS AD 應用程式註冊](./media/saas-offer-app-registration-v1.png)

4. 在 [建立] 頁面中，輸入您的應用程式\'註冊資訊：
    -   **名稱**：請輸入有意義的應用程式名稱
    -   **應用程式類型**：  
        
        選取安裝在安全伺服器上的 [用於[用戶端應用程式](../../active-directory/develop/active-directory-dev-glossary.md#client-application)的**Web 應用程式/api** ）] 和 [[資源/api 應用程式](../../active-directory/develop/active-directory-dev-glossary.md#resource-server)]。 此設定適用于 OAuth 機密[web 客戶](../../active-directory/develop/active-directory-dev-glossary.md#web-client)端）和公用[使用者代理程式型用戶端](../../active-directory/develop/active-directory-dev-glossary.md#user-agent-based-client)）。
        相同的應用程式也可以公開用戶端和資源/API。

        如需 web 應用程式的特定範例，請參閱《 [Azure AD 開發人員指南》](../../active-directory/develop/index.yml)中的[開始](../../active-directory/develop/quickstart-create-new-tenant.md)使用一節所提供的快速入門引導式設置。

5. 完成後，按一下 [**註冊**]。  Azure AD 會將唯一的*應用程式識別碼*指派給新的應用程式。 我們建議您註冊一個只存取 API 的應用程式，以及做為單一租使用者。

6. 若要建立用戶端密碼，請流覽至 [**憑證 & 秘密] 頁面**，然後按一下 [ **+ 新增用戶端密碼**]。  請務必複製秘密值，以便在您的程式碼中使用它。

**Azure AD 應用程式識別碼**會與您的發行者識別碼相關聯，因此請確定您的所有供應專案都使用相同的*應用程式識別碼*。

>[!Note]
>如果發行者在合作夥伴中心有兩個不同的帳戶，則應該使用兩個不同的 Azure AD 應用程式識別碼。  合作夥伴中心內的每個合作夥伴帳戶都應該針對透過此帳戶發佈的所有 SaaS 供應專案，使用唯一的 Azure AD 應用程式識別碼。

## <a name="how-to-get-the-publishers-authorization-token"></a>如何取得發行者的授權權杖

當您註冊應用程式之後，您可以使用 Azure AD V1 端點，以程式設計方式要求發行者的授權權杖（Azure AD 存取權杖）。 發行者在呼叫各種 SaaS 履行 Api 時，必須使用此權杖。 此權杖只適用于一小時。 

如需這類權杖的詳細資訊，請參閱 [Azure Active Directory 存取權杖](../../active-directory/develop/access-tokens.md)。  請注意，在此流程中，會使用 V1 結束點權杖。

### <a name="get-the-token-with-an-http-post"></a>使用 HTTP POST 取得權杖

#### <a name="http-method"></a>HTTP 方法

郵寄<br>

##### <a name="request-url"></a>*Request URL (要求 URL)* 

`https://login.microsoftonline.com/*{tenantId}*/oauth2/token`

##### <a name="uri-parameter"></a>*URI 參數*

|  參數名稱    |  必要         |  描述 |
|  ---------------   |  ---------------  | ------------ |
|  `tenantId`        |  True      |  已註冊 AAD 應用程式的租使用者識別碼。 |

##### <a name="request-header"></a>*要求標頭*

|  標頭名稱       |  必要         |  描述 |
|  ---------------   |  ---------------  | ------------ |
|  `content-type`    |  True      |  與要求相關聯的內容類型。 預設值是 `application/x-www-form-urlencoded`。 |

##### <a name="request-body"></a>*要求本文*

|  屬性名稱     |  必要         |  描述 |
|  ---------------   |  ---------------  | ------------ |
|  `grant-type`      |  True      |  授與類型。 使用 `"client_credentials"`。 |
|  `client_id`       |  True      |  與 Azure AD 應用程式相關聯的用戶端/應用程式識別碼。 |
|  `client_secret`   |  True      |  與 Azure AD 應用程式相關聯的密碼。 |
|  `resource`        |  True      |  要求權杖的目標資源。 請使用， `20e940b3-4c77-4b0b-9a53-9e16a1b010a7` 因為在此情況下，Marketplace SAAS API 一律是目標資源。 |

##### <a name="response"></a>*回應*

|  名稱     |  類型         |  Description |
|  ------   |  ---------------  | ------------ |
|  200 確定   |  TokenResponse    |  要求成功。 |

##### <a name="tokenresponse"></a>*TokenResponse*

範例回應：

```json
{
      "token_type": "Bearer",
      "expires_in": "3600",
      "ext_expires_in": "0",
      "expires_on": "15251…",
      "not_before": "15251…",
      "resource": "20e940b3-4c77-4b0b-9a53-9e16a1b010a7",
      "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6ImlCakwxUmNxemhpeTRmcHhJeGRacW9oTTJZayIsImtpZCI6ImlCakwxUmNxemhpeTRmcHhJeGRacW9oTTJZayJ9…"
  }
```

`"access_token"`回應中的域值是， `<access_token>` 您在呼叫所有 SaaS 履行和 Marketplace 計量 api 時，將會以授權參數的形式傳遞。

## <a name="next-steps"></a>後續步驟

Azure AD 保護的應用程式現在可以使用[SaaS 履行 API 第2版](./pc-saas-fulfillment-api-v2.md)。
