---
title: 使用 Azure Active Directory B2C 保護 Azure API 管理 API
description: 了解如何使用 Azure Active Directory B2C 所簽發的存取權杖來保護 Azure API 管理 API 端點。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 07/30/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 0e8802d612f2497cc58c90856e9a5a5572a142f1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "87482833"
---
# <a name="secure-an-azure-api-management-api-with-azure-ad-b2c"></a>使用 Azure AD B2C 保護 Azure API 管理 API

了解如何將對 Azure API 管理 (APIM) API 的存取限制為已使用 Azure Active Directory B2C (Azure AD B2C) 進行驗證的用戶端。 遵循此文章的步驟，在 APIM 中建立及測試連入原則，以限制只能存取包含 Azure AD B2C 所簽發之有效存取權杖的要求。

## <a name="prerequisites"></a>Prerequisites

您需要先備妥下列資源，才能繼續此文章中的步驟：

* [Azure AD B2C 租用戶](tutorial-create-tenant.md)
* 您租用戶中[註冊的應用程式](tutorial-register-applications.md)
* 您租用戶中[建立的使用者流程](tutorial-create-user-flows.md)
* 在 Azure API 管理中[發佈的 API](../api-management/import-and-publish.md)
* [Postman](https://www.getpostman.com/) \(英文\) 以測試安全存取 (選擇性)

## <a name="get-azure-ad-b2c-application-id"></a>取得 Azure AD B2C 應用程式識別碼

當您在 Azure API 管理中使用 Azure AD B2C 保護 API 時，您需要數個適用於在 APIM 中建立之[輸入原則](../api-management/api-management-howto-policies.md)的值。 首先，記錄您先前在 Azure AD B2C 租用戶中建立之應用程式的應用程式識別碼。 如果您使用的是您在先決條件中所建立的應用程式，請使用 *webapp1* 的應用程式識別碼。

若要在您的 Azure AD B2C 租用戶中註冊應用程式，您可以使用我們新的整合**應用程式註冊**體驗，或使用舊版**應用程式 (舊版)** 體驗。 [深入了解新的體驗](https://aka.ms/b2cappregtraining)。

#### <a name="app-registrations"></a>[應用程式註冊](#tab/app-reg-ga/)

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 在頂端功能表中選取 [目錄 + 訂用帳戶] 篩選，然後選取包含您 Azure AD B2C 租用戶的目錄。
1. 在左側功能表中，選取 [Azure AD B2C]。 或者，選取 [所有服務]，然後搜尋並選取 [Azure AD B2C]。
1. 選取 **應用程式註冊**，然後選取 [ **擁有的應用程式** ] 索引標籤。
1. 針對 *webapp1* 或您先前建立的另一個應用程式，記錄 [應用程式 (用戶端) 識別碼] 欄的值。

#### <a name="applications-legacy"></a>[應用程式 (舊版)](#tab/applications-legacy/)

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 在頂端功能表中選取 [目錄 + 訂用帳戶] 篩選，然後選取包含您 Azure AD B2C 租用戶的目錄。
1. 在左側功能表中，選取 [Azure AD B2C]。 或者，選取 [所有服務]，然後搜尋並選取 [Azure AD B2C]。
1. 在 [ **管理**] 底下，選取 [ **應用程式] (舊版) **。
1. 針對 *webapp1* 或您先前建立的另一個應用程式，記錄 [應用程式識別碼] 欄的值。

* * *

## <a name="get-token-issuer-endpoint"></a>取得權杖簽發者端點

接下來，針對您的一個 Azure AD B2C 使用者流程取得已知的設定 URL。 您還需要您想在 Azure API 管理中支援的權杖簽發者端點 URI。

1. 在 [Azure 入口網站](https://portal.azure.com)中，瀏覽至您的 Azure AD B2C 租用戶。
1. 在 [原則] 底下，選取 [使用者流程]。
1. 選取現有的原則 (例如 *B2C_1_signupsignin1*)，然後選取 [執行使用者流程]。
1. 記錄顯示於頁面頂端附近 [執行使用者流程] 標題底下之超連結中的 URL。 此 URL 是使用者流程的 OpenID Connect 已知探索端點，您會在下一節，於 Azure API 管理中設定輸入原則時使用此端點。

    ![Azure 入口網站上 [立即執行] 頁面中已知的 URI 超連結](media/secure-apim-with-b2c-token/portal-01-policy-link.png)

1. 選取超連結，以瀏覽至 OpenID Connect 已知的設定頁面。
1. 在瀏覽器中開啟的頁面上，記錄 `issuer` 值，例如：

    `https://<tenant-name>.b2clogin.com/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/v2.0/`

    當您在下一節，於 Azure API 管理中設定 API 時會使用此值。

您現在應該已記錄兩個可在下一節使用的 URL：OpenID Connect 已知的設定端點 URL 和簽發者 URI。 例如：

```
https://<tenant-name>.b2clogin.com/<tenant-name>.onmicrosoft.com/B2C_1_signupsignin1/v2.0/.well-known/openid-configuration
https://<tenant-name>.b2clogin.com/99999999-0000-0000-0000-999999999999/v2.0/
```

## <a name="configure-inbound-policy-in-azure-api-management"></a>在 Azure API 管理中設定輸入原則

您現在已準備好在 Azure API 管理中新增輸入原則，以驗證 API 呼叫。 藉由新增 [JWT 驗證](../api-management/api-management-access-restriction-policies.md#ValidateJWT)原則來驗證存取權杖中的對象和簽發者，您可以確保只接受具備有效權杖的 API 呼叫。

1. 在 [Azure 入口網站](https://portal.azure.com)中瀏覽至您的 Azure API 管理執行個體。
1. 選取 [API]。
1. 選取您想要使用 Azure AD B2C 保護的 API。
1. 選取 [設計] 索引標籤。
1. 在 [ **輸入處理**] 底下，選取 **\</\>** 以開啟 [原則程式碼編輯器]。
1. 將下列 `<validate-jwt>` 標記放置於 `<inbound>` 原則內。

    1. 使用原則已知的設定 URL 來更新 `<openid-config>` 元素中的 `url` 值。
    1. 使用您先前在 B2C 租用戶中建立之應用程式 (例如 *webapp1*) 的應用程式識別碼來更新 `<audience>` 元素。
    1. 使用您稍早記錄的權杖簽發者端點來更新 `<issuer>` 元素。

    ```xml
    <policies>
        <inbound>
            <validate-jwt header-name="Authorization" failed-validation-httpcode="401" failed-validation-error-message="Unauthorized. Access token is missing or invalid.">
                <openid-config url="https://<tenant-name>.b2clogin.com/<tenant-name>.onmicrosoft.com/B2C_1_signupsignin1/v2.0/.well-known/openid-configuration" />
                <audiences>
                    <audience>44444444-0000-0000-0000-444444444444</audience>
                </audiences>
                <issuers>
                    <issuer>https://<tenant-name>.b2clogin.com/99999999-0000-0000-0000-999999999999/v2.0/</issuer>
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

若要確保只有經過驗證的呼叫者可存取您的 API，您可以使用 [Postman](https://www.getpostman.com/) \(英文\) 來呼叫 API，以驗證您的 Azure API 管理設定。

若要呼叫 API，您需要 Azure AD B2C 所簽發的存取權杖及 APIM 訂用帳戶金鑰。

### <a name="get-an-access-token"></a>取得存取權杖

您首先需要 Azure AD B2C 所簽發的權杖，以在 Postman 的 `Authorization` 標頭中使用。 您可以在您應該已建立來作為先決條件之一的註冊/登入使用者流程中，使用 [立即執行] 功能來取得一個。

1. 在 [Azure 入口網站](https://portal.azure.com)中，瀏覽至您的 Azure AD B2C 租用戶。
1. 在 [原則] 底下，選取 [使用者流程]。
1. 選取現有的註冊/登入使用者流程，例如 *B2C_1_signupsignin1*。
1. 針對 [應用程式] 選取 [webapp1]。
1. 針對 [回覆 URL] 選擇 `https://jwt.ms`。
1. 選取 [執行使用者流程]。

    ![Azure 入口網站中註冊/登入使用者流程的執行使用者流程頁面](media/secure-apim-with-b2c-token/portal-03-user-flow.png)

1. 完成登入程序。 系統應該會將您重新導向到 `https://jwt.ms`。
1. 記錄您瀏覽器中所顯示的已編碼權杖值。 您會在 Postman 中使用此權杖值作為授權標頭。

    ![jwt.ms 中所顯示的已編碼權杖值](media/secure-apim-with-b2c-token/jwt-ms-01-token.png)

### <a name="get-api-subscription-key"></a>取得 API 訂用帳戶金鑰

呼叫已發佈 API 的用戶端應用程式 (在此案例中為 Postman) 必須在其對 API 的 HTTP 要求中包含有效的 API 管理訂用帳戶金鑰。 取得要包含於 Postman HTTP 要求中的訂用帳戶金鑰：

1. 在 [Azure 入口網站](https://portal.azure.com)中，瀏覽至您的 Azure API 管理服務執行個體。
1. 選取 **訂用帳戶** 。
1. 選取 [產品:無限制] 的省略符號，然後選取 [顯示/隱藏金鑰]。
1. 記錄產品的**主要金鑰**。 您會在 Postman 的 HTTP 要求中，使用此金鑰作為 `Ocp-Apim-Subscription-Key` 標頭。

![已在 Azure 入口網站中選取 [顯示/隱藏金鑰] 的 [訂用帳戶金鑰] 頁面](media/secure-apim-with-b2c-token/portal-04-api-subscription-key.png)

### <a name="test-a-secure-api-call"></a>測試安全的 API 呼叫

記錄存取權杖和 APIM 訂用帳戶金鑰之後，您現在可以開始測試是否已正確設定對 API 的安全存取。

1. 在 `GET`Postman[ \(英文\) 中，建立新的 ](https://www.getpostman.com/) 要求。 針對要求 URL，指定您發佈來作為先決條件之一的 API 的喇叭清單端點。 例如：

    `https://contosoapim.azure-api.net/conference/speakers`

1. 接下來，新增下列標頭：

    | Key | 值 |
    | --- | ----- |
    | `Authorization` | 您先前記錄的已編碼權杖值，前面加上 `Bearer ` (在 "Bearer" 後面加上空格) |
    | `Ocp-Apim-Subscription-Key` | 您稍早記錄的 APIM 訂用帳戶金鑰 |

    您的 **GET** 要求 URL 和**標頭**應如下所示：

    ![顯示 GET 要求 URL 和標頭的 Postman UI](media/secure-apim-with-b2c-token/postman-01-headers.png)

1. 選取 Postman 中的 [傳送] 按鈕，以執行要求。 如果您已正確設定所有內容，您應該會看到具有一組會議喇叭的 JSON 回應 (此處顯示的內容已截斷)：

    ```json
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

既然您已成功完成要求，請測試失敗案例，以確保使用「無效」權杖的 API 呼叫會如預期般遭到拒絕。 執行測試的方式之一是，在權杖值中新增或變更幾個字元，然後執行與之前相同的 `GET` 要求。

1. 在權杖值中新增數個字元，以模擬無效的權杖。 例如，在權杖值中新增 "INVALID"：

    ![Postman UI 的標頭區段，顯示已在權杖中新增 INVALID](media/secure-apim-with-b2c-token/postman-02-invalid-token.png)

1. 選取 [傳送] 按鈕，以執行要求。 使用無效的權杖時，預期的結果是 `401` 未經授權的狀態碼：

    ```json
    {
        "statusCode": 401,
        "message": "Unauthorized. Access token is missing or invalid."
    }
    ```

如果您看到 `401` 狀態碼，表示您已驗證只有具備 Azure AD B2C 所簽發之有效存取權杖的呼叫者，才能夠對您的 Azure API 管理 API 提出成功的要求。

## <a name="support-multiple-applications-and-issuers"></a>支援多個應用程式和簽發者

數個應用程式通常會與單一 REST API 互動。 若要讓您的 API 接受適用於多個應用程式的權杖，請將其應用程式識別碼新增至 APIM 輸入原則中的 `<audiences>` 元素。

```xml
<!-- Accept tokens intended for these recipient applications -->
<audiences>
    <audience>44444444-0000-0000-0000-444444444444</audience>
    <audience>66666666-0000-0000-0000-666666666666</audience>
</audiences>
```

同樣地，若要支援多個權杖簽發者，將其端點 URI 新增至 APIM 輸入原則中的 `<issuers>` 元素。

```xml
<!-- Accept tokens from multiple issuers -->
<issuers>
    <issuer>https://<tenant-name>.b2clogin.com/99999999-0000-0000-0000-999999999999/v2.0/</issuer>
    <issuer>https://login.microsoftonline.com/99999999-0000-0000-0000-999999999999/v2.0/</issuer>
</issuers>
```

## <a name="migrate-to-b2clogincom"></a>移轉至 b2clogin.com

如果您的 APIM API 會驗證舊版 `login.microsoftonline.com` 端點所簽發的權杖，您應該移轉此 API 及呼叫此 API 的應用程式，以使用 [b2clogin.com](b2clogin.md) 所簽發的權杖。

您可以遵循這個一般程序來執行分段移轉：

1. 針對 b2clogin.com 和 login.microsoftonline.com 所簽發的權杖，在您的 APIM 輸入原則中新增支援。
1. 一次更新一個應用程式，以從 b2clogin.com 端點取得權杖。
1. 一旦您的所有應用程式都從 b2clogin.com 正確取得權杖之後，請從 API 中移除對 login.microsoftonline.com 所簽發之權杖的支援。

下列範例 APIM 輸入原則說明如何接受 b2clogin.com 和 login.microsoftonline.com 所簽發的權杖。 此外，其也支援來自兩個應用程式的 API 要求。

```xml
<policies>
    <inbound>
        <validate-jwt header-name="Authorization" failed-validation-httpcode="401" failed-validation-error-message="Unauthorized. Access token is missing or invalid.">
            <openid-config url="https://<tenant-name>.b2clogin.com/<tenant-name>.onmicrosoft.com/B2C_1_signupsignin1/v2.0/.well-known/openid-configuration" />
            <audiences>
                <audience>44444444-0000-0000-0000-444444444444</audience>
                <audience>66666666-0000-0000-0000-666666666666</audience>
            </audiences>
            <issuers>
                <issuer>https://login.microsoftonline.com/99999999-0000-0000-0000-999999999999/v2.0/</issuer>
                <issuer>https://<tenant-name>.b2clogin.com/99999999-0000-0000-0000-999999999999/v2.0/</issuer>
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

如需 Azure API 管理原則的其他詳細資料，請參閱 [APIM 原則參考索引](../api-management/api-management-policies.md)。

您可以在[將以 OWIN 為基礎的 Web API 移轉至 b2clogin.com](multiple-token-endpoints.md) 中，找到將以 OWIN 為基礎的 Web API 及其應用程式移轉至 b2clogin.com 的相關資訊。
