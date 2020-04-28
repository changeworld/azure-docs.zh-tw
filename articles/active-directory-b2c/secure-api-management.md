---
title: 使用 Azure Active Directory B2C 保護 Azure API 管理 API
description: 瞭解如何使用 Azure Active Directory B2C 所簽發的存取權杖來保護 Azure API 管理 API 端點。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/31/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 00938d831e70289b24acb599b81016aa6e564d78
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "78186925"
---
# <a name="secure-an-azure-api-management-api-with-azure-ad-b2c"></a>使用 Azure AD B2C 保護 Azure API 管理 API

瞭解如何將 Azure API 管理（APIM） API 的存取權，限制為已使用 Azure Active Directory B2C （Azure AD B2C）進行驗證的用戶端。 請遵循本文中的步驟，在 APIM 中建立及測試輸入原則，以限制只有包含有效 Azure AD B2C 發行存取權杖的要求才能存取。

## <a name="prerequisites"></a>Prerequisites

在繼續進行本文中的步驟之前，您必須先準備好下列資源：

* [Azure AD B2C 租用戶](tutorial-create-tenant.md)
* 您租用戶中[註冊的應用程式](tutorial-register-applications.md)
* 您租用戶中[建立的使用者流程](tutorial-create-user-flows.md)
* Azure API 管理中[已發佈的 API](../api-management/import-and-publish.md)
* [Postman](https://www.getpostman.com/)以測試安全存取（選擇性）

## <a name="get-azure-ad-b2c-application-id"></a>取得 Azure AD B2C 應用程式識別碼

當您使用 Azure AD B2C 保護 Azure API 管理中的 API 時，您需要在 APIM 中建立的[輸入原則](../api-management/api-management-howto-policies.md)有數個值。 首先，記錄您先前在 Azure AD B2C 租使用者中建立之應用程式的應用程式識別碼。 如果您使用的是您在必要條件中所建立的應用程式，請使用*webbapp1*的應用程式識別碼。

您可以使用目前的**應用程式**體驗或我們新的統一**應用程式註冊（預覽）** 體驗來取得應用程式識別碼。 [深入了解新的體驗](https://aka.ms/b2cappregintro)。

#### <a name="applications"></a>[應用程式](#tab/applications/)

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 在頂端功能表中選取 [目錄 + 訂用帳戶]  篩選，然後選取包含您 Azure AD B2C 租用戶的目錄。
1. 在左側功能表中，選取 [Azure AD B2C]  。 或者，選取 [所有服務]  ，然後搜尋並選取 [Azure AD B2C]  。
1. 在 [**管理**] 底下，選取 [**應用程式**]。
1. 記錄*webapp1*的 [**應用程式識別碼**] 資料行中的值，或您先前建立的另一個應用程式。

#### <a name="app-registrations-preview"></a>[應用程式註冊 (預覽)](#tab/app-reg-preview/)

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 在頂端功能表中選取 [目錄 + 訂用帳戶]  篩選，然後選取包含您 Azure AD B2C 租用戶的目錄。
1. 在左側功能表中，選取 [Azure AD B2C]  。 或者，選取 [所有服務]  ，然後搜尋並選取 [Azure AD B2C]  。
1. 選取 [**應用程式註冊（預覽）**]，然後選取 [**擁有的應用程式**] 索引標籤。
1. 記錄*webapp1*的 [**應用程式（用戶端）識別碼**] 資料行中的值，或您先前建立的另一個應用程式。

* * *

## <a name="get-token-issuer-endpoint"></a>取得權杖簽發者端點

接下來，取得您其中一個 Azure AD B2C 使用者流程的知名設定 URL。 您也需要您想要在 Azure API 管理中支援的權杖簽發者端點 URI。

1. 流覽至您在[Azure 入口網站](https://portal.azure.com)中的 Azure AD B2C 租使用者。
1. 在 [**原則**] 底下，選取 **[使用者流程（原則）**]。
1. 選取現有的原則，例如*B2C_1_signupsignin1*，然後選取 [**執行使用者流程**]。
1. 將 URL 記錄在頁面頂端附近 [**執行使用者流程**] 標題底下的超連結中。 此 URL 是使用者流程的 OpenID Connect 知名探索端點，當您在 Azure API 管理中設定輸入原則時，您會在下一節中使用它。

    ![Azure 入口網站的 [立即執行] 頁面中的知名 URI 超連結](media/secure-apim-with-b2c-token/portal-01-policy-link.png)

1. 選取超連結以流覽至 OpenID Connect 的知名設定頁面。
1. 在瀏覽器中開啟的頁面上，記錄`issuer`值，例如：

    `https://your-b2c-tenant.b2clogin.com/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/v2.0/`

    當您在 Azure API 管理中設定 API 時，會在下一節中使用此值。

您現在應該已記錄兩個 Url，以便在下一節中使用： OpenID Connect 知名設定端點 URL 和簽發者 URI。 例如：

```
https://yourb2ctenant.b2clogin.com/yourb2ctenant.onmicrosoft.com/v2.0/.well-known/openid-configuration?p=B2C_1_signupsignin1
https://yourb2ctenant.b2clogin.com/99999999-0000-0000-0000-999999999999/v2.0/
```

## <a name="configure-inbound-policy-in-azure-api-management"></a>在 Azure API 管理中設定輸入原則

您現在已準備好在 Azure API 管理中新增輸入原則，以驗證 API 呼叫。 藉由新增[JWT 驗證](../api-management/api-management-access-restriction-policies.md#ValidateJWT)原則來驗證存取權杖中的物件和簽發者，您可以確保只接受具有有效權杖的 API 呼叫。

1. 在 [Azure 入口網站](https://portal.azure.com)中瀏覽至您的 Azure API 管理執行個體。
1. 選取 [API]****。
1. 選取您想要使用 Azure AD B2C 保護的 API。
1. 選取 [設計]**** 索引標籤。
1. 在 [**輸入處理**] ** \< / **底下，選取以開啟 [原則程式碼編輯器]。
1. 將下列`<validate-jwt>`標記放在`<inbound>`原則內。

    1. 將`<openid-config>`元素`url`中的值更新為您的原則知名設定 URL。
    1. 使用您`<audience>`先前在 B2C 租使用者中建立之應用程式的應用程式識別碼（例如， *webapp1*）來更新元素。
    1. 使用您`<issuer>`稍早記錄的權杖簽發者端點來更新元素。

    ```xml
    <policies>
        <inbound>
            <validate-jwt header-name="Authorization" failed-validation-httpcode="401" failed-validation-error-message="Unauthorized. Access token is missing or invalid.">
                <openid-config url="https://yourb2ctenant.b2clogin.com/yourb2ctenant.onmicrosoft.com/v2.0/.well-known/openid-configuration?p=B2C_1_signupsignin1" />
                <audiences>
                    <audience>44444444-0000-0000-0000-444444444444</audience>
                </audiences>
                <issuers>
                    <issuer>https://yourb2ctenant.b2clogin.com/99999999-0000-0000-0000-999999999999/v2.0/</issuer>
                </issuers>
            </validate-jwt>
            <base />
        </inbound>
        <backend> <base /> </backend>
        <outbound> <base /> </outbound>
        <on-error> <base /> </on-error>
    </policies>
    ```

## <a name="validate-secure-api-access"></a>驗證安全 API 存取

若要確保只有經過驗證的呼叫端可以存取您的 API，您可以使用[Postman](https://www.getpostman.com/)呼叫 API 來驗證您的 Azure API 管理設定。

若要呼叫 API，您需要 Azure AD B2C 所簽發的存取權杖，以及 APIM 的訂用帳戶金鑰。

### <a name="get-an-access-token"></a>取得存取權杖

您首先需要 Azure AD B2C 所簽發的權杖，以在 Postman `Authorization`的標頭中使用。 您可以使用您的註冊/登入使用者流程的 [**立即執行**] 功能來取得一個，您應該已建立為其中一個必要條件。

1. 流覽至您在[Azure 入口網站](https://portal.azure.com)中的 Azure AD B2C 租使用者。
1. 在 [**原則**] 底下，選取 **[使用者流程（原則）**]。
1. 選取現有的註冊/登入使用者流程，例如*B2C_1_signupsignin1*。
1. 針對 [**應用程式**]，選取 [ *webapp1*]。
1. 針對 [**回復 URL**] `https://jwt.ms`，選擇。
1. 選取 [**執行使用者流程**]。

    ![[執行使用者流程] 頁面，以在 Azure 入口網站中註冊登入使用者流程](media/secure-apim-with-b2c-token/portal-03-user-flow.png)

1. 完成登入程序。 您應該會被重新`https://jwt.ms`導向至。
1. 記錄您的瀏覽器中顯示的編碼權杖值。 您會在 Postman 中使用此權杖值作為 Authorization 標頭。

    ![Jwt.ms 上顯示的編碼權杖值](media/secure-apim-with-b2c-token/jwt-ms-01-token.png)

### <a name="get-api-subscription-key"></a>取得 API 訂用帳戶金鑰

呼叫已發佈 API 的用戶端應用程式（在此案例中為 Postman）必須在其對 API 的 HTTP 要求中包含有效的 API 管理訂用帳戶金鑰。 若要取得要包含在 Postman HTTP 要求中的訂用帳戶金鑰：

1. 在[Azure 入口網站](https://portal.azure.com)中，流覽至您的 Azure API 管理服務實例。
1. 選取 **[** 訂用帳戶]。
1. 選取 [**產品：無限制**] 的省略號，然後選取 [**顯示/隱藏金鑰**]。
1. 記錄產品的**主要金鑰**。 您會在 Postman 的`Ocp-Apim-Subscription-Key` HTTP 要求中使用此金鑰做為標頭。

![已在 Azure 入口網站中選取 [顯示/隱藏金鑰] 的 [訂用帳戶金鑰] 頁面](media/secure-apim-with-b2c-token/portal-04-api-subscription-key.png)

### <a name="test-a-secure-api-call"></a>測試安全的 API 呼叫

記錄存取權杖和 APIM 訂用帳戶金鑰後，您就可以開始測試是否已正確設定對 API 的安全存取。

1. 在 Postman 中`GET`建立新[Postman](https://www.getpostman.com/)的要求。 針對 [要求 URL]，指定您發佈為其中一個必要條件之 API 的喇叭清單端點。 例如：

    `https://contosoapim.azure-api.net/conference/speakers`

1. 接下來，新增下列標頭：

    | Key | 值 |
    | --- | ----- |
    | `Authorization` | 您先前記錄的編碼權杖值，前面`Bearer `加上（在「持有人」後面加上空格） |
    | `Ocp-Apim-Subscription-Key` | 您稍早記錄的 APIM 訂用帳戶金鑰 |

    您的**GET**要求 URL 和**標頭**應如下所示：

    ![顯示 GET 要求 URL 和標頭的 Postman UI](media/secure-apim-with-b2c-token/postman-01-headers.png)

1. 選取 Postman 中的 [**傳送**] 按鈕以執行要求。 如果您已正確設定所有專案，您應該會看到具有一組會議喇叭的 JSON 回應（此處顯示已截斷）：

    ```JSON
    {
      "collection": {
        "version": "1.0",
        "href": "https://conferenceapi.azurewebsites.net:443/speakers",
        "links": [],
        "items": [
          {
            "href": "https://conferenceapi.azurewebsites.net/speaker/1",
            "data": [
              {
                "name": "Name",
                "value": "Scott Guthrie"
              }
            ],
            "links": [
              {
                "rel": "http://tavis.net/rels/sessions",
                "href": "https://conferenceapi.azurewebsites.net/speaker/1/sessions"
              }
            ]
          },
    [...]
    ```

### <a name="test-an-insecure-api-call"></a>測試不安全的 API 呼叫

既然您已成功完成要求，請測試失敗案例，以確保以*無效*的權杖呼叫 API 時，會如預期般遭到拒絕。 執行測試的其中一種方式是在權杖值中新增或變更幾個字元，然後執行與之前`GET`相同的要求。

1. 將數個字元新增至 token 值，以模擬不正確 token。 例如，將「無效」新增至權杖值：

    ![Postman UI 的標頭區段，其中顯示已新增至權杖的無效](media/secure-apim-with-b2c-token/postman-02-invalid-token.png)

1. 選取 [**傳送**] 按鈕以執行要求。 使用不正確權杖時，預期的結果會`401`是未授權的狀態碼：

    ```JSON
    {
        "statusCode": 401,
        "message": "Unauthorized. Access token is missing or invalid."
    }
    ```

如果您看到`401`狀態碼，表示您已確認只有具備 Azure AD B2C 所發行之有效存取權杖的呼叫者，才能夠對您的 Azure API 管理 API 提出成功的要求。

## <a name="support-multiple-applications-and-issuers"></a>支援多個應用程式和簽發者

數個應用程式通常會與單一 REST API 互動。 若要讓您的 API 接受適用于多個應用程式的權杖，請將`<audiences>`其應用程式識別碼新增至 APIM 輸入原則中的元素。

```XML
<!-- Accept tokens intended for these recipient applications -->
<audiences>
    <audience>44444444-0000-0000-0000-444444444444</audience>
    <audience>66666666-0000-0000-0000-666666666666</audience>
</audiences>
```

同樣地，若要支援多個權杖簽發者，請將`<issuers>`其端點 uri 新增至 APIM 輸入原則中的元素。

```XML
<!-- Accept tokens from multiple issuers -->
<issuers>
    <issuer>https://yourb2ctenant.b2clogin.com/99999999-0000-0000-0000-999999999999/v2.0/</issuer>
    <issuer>https://login.microsoftonline.com/99999999-0000-0000-0000-999999999999/v2.0/</issuer>
</issuers>
```

## <a name="migrate-to-b2clogincom"></a>遷移至 b2clogin.com

如果您的 APIM API 會驗證舊版`login.microsoftonline.com`端點所發行的權杖，您應該遷移 API 和呼叫它的應用程式，以使用[b2clogin.com](b2clogin.md)所簽發的權杖。

您可以遵循此一般程式來執行分段遷移：

1. 針對 b2clogin.com 和 login.microsoftonline.com 所發行的權杖，在您的 APIM 輸入原則中新增支援。
1. 一次更新一個應用程式，以從 b2clogin.com 端點取得權杖。
1. 一旦您的所有應用程式都正確地從 b2clogin.com 取得權杖，請從 API 移除對 login.microsoftonline.com 發行之權杖的支援。

下列範例 APIM 輸入原則說明如何接受 b2clogin.com 和 login.microsoftonline.com 所簽發的權杖。 此外，它也支援來自兩個應用程式的 API 要求。

```XML
<policies>
    <inbound>
        <validate-jwt header-name="Authorization" failed-validation-httpcode="401" failed-validation-error-message="Unauthorized. Access token is missing or invalid.">
            <openid-config url="https://yourb2ctenant.b2clogin.com/yourb2ctenant.onmicrosoft.com/v2.0/.well-known/openid-configuration?p=B2C_1_signupsignin1" />
            <audiences>
                <audience>44444444-0000-0000-0000-444444444444</audience>
                <audience>66666666-0000-0000-0000-666666666666</audience>
            </audiences>
            <issuers>
                <issuer>https://login.microsoftonline.com/99999999-0000-0000-0000-999999999999/v2.0/</issuer>
                <issuer>https://yourb2ctenant.b2clogin.com/99999999-0000-0000-0000-999999999999/v2.0/</issuer>
            </issuers>
        </validate-jwt>
        <base />
    </inbound>
    <backend> <base /> </backend>
    <outbound> <base /> </outbound>
    <on-error> <base /> </on-error>
</policies>
```

## <a name="next-steps"></a>後續步驟

如需 Azure API 管理原則的其他詳細資料，請參閱[APIM 原則參考索引](../api-management/api-management-policies.md)。

您可以在將[OWIN 為基礎的 Web API 遷移至 b2clogin.com](multiple-token-endpoints.md)中，找到將 OWIN 型 web api 及其應用程式遷移至 b2clogin.com 的相關資訊。
