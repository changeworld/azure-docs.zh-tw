---
title: 使用 Azure 活動目錄 B2C 保護 Azure API 管理 API
description: 瞭解如何使用 Azure 活動目錄 B2C 頒發的訪問權杖來保護 Azure API 管理 API 終結點。
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78186925"
---
# <a name="secure-an-azure-api-management-api-with-azure-ad-b2c"></a>使用 Azure AD B2C 保護 Azure API 管理 API

瞭解如何將 Azure API 管理 （APIM） API 的訪問限制為已使用 Azure 活動目錄 B2C （Azure AD B2C） 進行身份驗證的用戶端。 按照本文中的步驟在 APIM 中創建和測試入站策略，該策略僅限制對包含有效 Azure AD B2C 頒發的訪問權杖的請求的訪問。

## <a name="prerequisites"></a>Prerequisites

在繼續執行本文中的步驟之前，您需要提供以下資源：

* [Azure AD B2C 租用戶](tutorial-create-tenant.md)
* 您租用戶中[註冊的應用程式](tutorial-register-applications.md)
* 您租用戶中[建立的使用者流程](tutorial-create-user-flows.md)
* Azure API 管理中[已發佈的 API](../api-management/import-and-publish.md)
* [郵遞員](https://www.getpostman.com/)測試安全訪問（可選）

## <a name="get-azure-ad-b2c-application-id"></a>獲取 Azure AD B2C 應用程式 ID

使用 Azure API 管理中保護 API 時，需要在 APIM 中創建的[入站策略](../api-management/api-management-howto-policies.md)中具有多個值。 首先，記錄以前在 Azure AD B2C 租戶中創建的應用程式的應用程式 ID。 如果您正在使用在先決條件中創建的應用程式，請使用*webbapp1*的應用程式 ID。

您可以使用當前**的應用程式**體驗或我們新的統一**應用註冊（預覽）** 體驗來獲取應用程式 ID。 [深入了解新的體驗](https://aka.ms/b2cappregintro)。

#### <a name="applications"></a>[應用程式](#tab/applications/)

1. 登錄到 Azure[門戶](https://portal.azure.com)。
1. 在頂端功能表中選取 [目錄 + 訂用帳戶]**** 篩選，然後選取包含您 Azure AD B2C 租用戶的目錄。
1. 在左側功能表中，選取 [Azure AD B2C]****。 或者，選取 [所有服務]****，然後搜尋並選取 [Azure AD B2C]****。
1. 在 **"管理**"下，選擇 **"應用程式**"。
1. 在*Webapp1*或其他以前創建的應用程式的 **"應用 ID"** 列中記錄該值。

#### <a name="app-registrations-preview"></a>[應用程式註冊 (預覽)](#tab/app-reg-preview/)

1. 登錄到 Azure[門戶](https://portal.azure.com)。
1. 在頂端功能表中選取 [目錄 + 訂用帳戶]**** 篩選，然後選取包含您 Azure AD B2C 租用戶的目錄。
1. 在左側功能表中，選取 [Azure AD B2C]****。 或者，選取 [所有服務]****，然後搜尋並選取 [Azure AD B2C]****。
1. 選擇**應用註冊（預覽），** 然後選擇 **"擁有的應用程式**"選項卡。
1. 在*Webapp1*或其他以前創建的應用程式 **（用戶端）ID**列中記錄該值。

* * *

## <a name="get-token-issuer-endpoint"></a>獲取權杖頒發者終結點

接下來，獲取 Azure AD B2C 使用者流的已知配置 URL。 您還需要要在 Azure API 管理中支援的權杖頒發者終結點 URI。

1. 流覽到[Azure 門戶](https://portal.azure.com)中的 Azure AD B2C 租戶。
1. 在**策略**下，選擇**使用者流（策略）。**
1. 選擇現有策略（例如*B2C_1_signupsignin1，* 然後選擇 **"運行使用者流**"。
1. 在頁面頂部附近的 **"運行使用者流**"標題下顯示的超連結中記錄 URL。 此 URL 是使用者流的 OpenID Connect 已知發現終結點，在 Azure API 管理中配置入站策略時，在下一節中使用它。

    ![Azure 門戶的"立即運行"頁中的已知 URI 超連結](media/secure-apim-with-b2c-token/portal-01-policy-link.png)

1. 選擇要流覽到 OpenID 連接已知配置頁的超連結。
1. 在瀏覽器中打開的頁面中，記錄`issuer`該值，例如：

    `https://your-b2c-tenant.b2clogin.com/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/v2.0/`

    在 Azure API 管理中配置 API 時，在下一節中使用此值。

現在，您應該記錄兩個 URL，以便在下一節中使用：OpenID 連接眾所周知的配置終結點 URL 和頒發者 URI。 例如：

```
https://yourb2ctenant.b2clogin.com/yourb2ctenant.onmicrosoft.com/v2.0/.well-known/openid-configuration?p=B2C_1_signupsignin1
https://yourb2ctenant.b2clogin.com/99999999-0000-0000-0000-999999999999/v2.0/
```

## <a name="configure-inbound-policy-in-azure-api-management"></a>在 Azure API 管理中配置入站策略

現在，您已準備好在 Azure API 管理中添加用於驗證 API 呼叫的入站策略。 通過添加[JWT 驗證](../api-management/api-management-access-restriction-policies.md#ValidateJWT)策略來驗證訪問權杖中的訪問群體和頒發者，可以確保僅接受具有有效權杖的 API 呼叫。

1. 在 [Azure 入口網站](https://portal.azure.com)中瀏覽至您的 Azure API 管理執行個體。
1. 選取 [API]****。
1. 選擇要使用 Azure AD B2C 保護的 API。
1. 選取 [設計]**** 索引標籤。
1. 在 **"入站處理**"**\</** 下，選擇以打開策略代碼編輯器。
1. 將以下`<validate-jwt>`標記放在策略中`<inbound>`。

    1. 使用策略`url`的已知配置`<openid-config>`URL 更新元素中的值。
    1. 使用以前`<audience>`在 B2C 租戶中創建的應用程式的應用程式 ID 更新元素（例如 *，Webapp1*）。
    1. 使用前面`<issuer>`記錄的權杖頒發終結點更新元素。

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

## <a name="validate-secure-api-access"></a>驗證安全 API 訪問

為了確保只有經過身份驗證的調用方才能訪問 API，可以通過使用[Postman](https://www.getpostman.com/)調用 API 來驗證 Azure API 管理配置。

要調用 API，需要 Azure AD B2C 頒發的訪問權杖和 APIM 訂閱金鑰。

### <a name="get-an-access-token"></a>取得存取權杖

首先需要由 Azure AD B2C 頒發的權杖才能在`Authorization`Postman 中的標頭中使用。 您可以使用您應該創建的註冊/登錄使用者流的 **"立即運行"** 功能作為先決條件之一獲得。

1. 流覽到[Azure 門戶](https://portal.azure.com)中的 Azure AD B2C 租戶。
1. 在**策略**下，選擇**使用者流（策略）。**
1. 選擇現有的註冊/登錄使用者流，例如*B2C_1_signupsignin1*。
1. 對於**應用程式**，選擇*Webapp1*。
1. 對於**回復 URL，** 請選擇`https://jwt.ms`。
1. 選擇 **"運行使用者流**"。

    ![運行使用者流頁以在 Azure 門戶中註冊登錄使用者流](media/secure-apim-with-b2c-token/portal-03-user-flow.png)

1. 完成登入程序。 您應該重定向到`https://jwt.ms`。
1. 記錄瀏覽器中顯示的編碼權杖值。 對於 Postman 中的"授權"標頭，使用此權杖值。

    ![jwt.ms上顯示的編碼權杖值](media/secure-apim-with-b2c-token/jwt-ms-01-token.png)

### <a name="get-api-subscription-key"></a>獲取 API 訂閱金鑰

調用已發佈 API 的用戶端應用程式（本例中為 Postman）必須在其對 API 的 HTTP 要求中包含有效的 API 管理訂閱金鑰。 要獲取要包含在郵遞員 HTTP 要求中的訂閱金鑰，請進行以下操作：

1. 流覽到[Azure 門戶](https://portal.azure.com)中的 Azure API 管理服務實例。
1. 選擇**訂閱**。
1. 選擇 **"產品"** 的省略號：無限制，然後選擇 **"顯示/隱藏鍵**"。
1. 記錄產品的**初級金鑰**。 在 Postman 中的`Ocp-Apim-Subscription-Key`HTTP 要求中，對於標頭使用此金鑰。

![在 Azure 門戶中選擇顯示/隱藏鍵的訂閱金鑰頁](media/secure-apim-with-b2c-token/portal-04-api-subscription-key.png)

### <a name="test-a-secure-api-call"></a>測試安全 API 呼叫

通過記錄訪問權杖和 APIM 訂閱金鑰，現在可以測試您是否正確配置了對 API 的安全訪問。

1. 在`GET`[郵遞員](https://www.getpostman.com/)中創建新請求。 對於請求 URL，指定您發佈的 API 的揚聲器清單終結點作為先決條件之一。 例如：

    `https://contosoapim.azure-api.net/conference/speakers`

1. 接下來，添加以下標頭：

    | Key | 值 |
    | --- | ----- |
    | `Authorization` | 您之前錄製的編碼權杖值（`Bearer `包括"承載"後的空間） |
    | `Ocp-Apim-Subscription-Key` | 您之前錄製的 APIM 訂閱金鑰 |

    您的**GET**請求 URL 和**標頭**應類似于：

    ![顯示 GET 請求 URL 和標頭的郵遞員 UI](media/secure-apim-with-b2c-token/postman-01-headers.png)

1. 選擇"郵遞員 **"中的"發送**"按鈕以執行請求。 如果配置了所有內容正確，則應顯示 JSON 回應，其中有會議演講者的集合（此處顯示截斷）：

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

現在您已成功發出請求，請測試失敗情況，以確保按預期拒絕對具有*無效*權杖的 API 的調用。 執行測試的一種方法是在權杖值中添加或更改幾個字元，然後執行與以前相同的`GET`請求。

1. 向權杖值添加多個字元以類比不正確權杖。 例如，向權杖值添加"INVALID"：

    ![郵遞員 UI 的標題部分，顯示添加到權杖的 INVALID](media/secure-apim-with-b2c-token/postman-02-invalid-token.png)

1. 選擇 **"發送**"按鈕以執行請求。 使用無效權杖時，預期結果是`401`未經授權的狀態碼：

    ```JSON
    {
        "statusCode": 401,
        "message": "Unauthorized. Access token is missing or invalid."
    }
    ```

如果看到`401`狀態碼，則已驗證只有具有 Azure AD B2C 頒發的有效訪問權杖的調用方才能向 Azure API 管理 API 發出成功請求。

## <a name="support-multiple-applications-and-issuers"></a>支援多個應用程式和頒發者

多個應用程式通常與單個 REST API 進行交互。 要使 API 能夠接受用於多個應用程式的權杖，請將其應用程式權杖添加到 APIM 入`<audiences>`站策略中的元素。

```XML
<!-- Accept tokens intended for these recipient applications -->
<audiences>
    <audience>44444444-0000-0000-0000-444444444444</audience>
    <audience>66666666-0000-0000-0000-666666666666</audience>
</audiences>
```

同樣，要支援多個權杖頒發者，將其終結點 URI 添加到`<issuers>`APIM 入站策略中的元素。

```XML
<!-- Accept tokens from multiple issuers -->
<issuers>
    <issuer>https://yourb2ctenant.b2clogin.com/99999999-0000-0000-0000-999999999999/v2.0/</issuer>
    <issuer>https://login.microsoftonline.com/99999999-0000-0000-0000-999999999999/v2.0/</issuer>
</issuers>
```

## <a name="migrate-to-b2clogincom"></a>遷移到b2clogin.com

如果您有一個 APIM API 來驗證由舊終結點`login.microsoftonline.com`頒發的權杖，則應遷移 API 和調用它的應用程式，以使用[b2clogin.com](b2clogin.md)頒發的權杖。

您可以按照此常規過程執行暫存遷移：

1. 在 APIM 入站策略中添加對 b2clogin.com和login.microsoftonline.com頒發的權杖的支援。
1. 一次更新一個應用程式，從b2clogin.com終結點獲取權杖。
1. 一旦所有應用程式都正確從b2clogin.com獲取權杖，請從 API 中刪除對login.microsoftonline.com頒發的權杖的支援。

下面的示例 APIM 入站策略說明了如何接受b2clogin.com和login.microsoftonline.com頒發的權杖。 此外，它還支援來自兩個應用程式的 API 請求。

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

有關 Azure API 管理原則的其他詳細資訊，請參閱[APIM 策略參考索引](../api-management/api-management-policies.md)。

您可以找到有關將基於 OWIN 的 Web API 及其應用程式遷移到b2clogin.com遷移[基於 OWIN 的 Web API 的資訊b2clogin.com。](multiple-token-endpoints.md)
