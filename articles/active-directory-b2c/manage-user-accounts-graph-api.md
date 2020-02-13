---
title: 使用中的圖形 API Azure Active Directory B2C
description: 如何藉由呼叫 Azure AD 圖形 API 並使用應用程式身分識別來自動化進程，來管理 Azure AD B2C 租使用者中的使用者。
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/24/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 71b437f57f9d9e6e18af88d6413269cac6f66c47
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/12/2020
ms.locfileid: "77161659"
---
# <a name="azure-ad-b2c-use-the-azure-ad-graph-api"></a>Azure AD B2C：使用 Azure AD 圖形 API

Azure Active Directory B2C （Azure AD B2C）租使用者可以有數千或數百萬個使用者。 這表示許多常見的租用戶管理工作需要以程式設計方式執行。 使用者管理是主要範例。

您可能需要將現有的使用者存放區移轉到 B2C 租用戶。 您希望在自己的頁面上裝載使用者註冊，並在幕後的 Azure AD B2C 目錄中建立使用者帳戶。 這類工作需要建立、讀取、更新和刪除使用者帳戶的能力。 您可以使用 Azure AD 圖形 API 來執行這類工作。

對於 B2C 租使用者，有兩種主要模式與圖形 API 通訊：

* 若為**互動式**的執行一次工作，當您執行這些工作時，您應該以 B2C 租使用者中的系統管理員帳戶的身分進行作業。 此模式需要系統管理員先使用認證登入，系統管理員才能對圖形 API進行任何呼叫。
* 針對**自動**、連續的工作，您應該使用您以必要許可權提供的某種服務帳戶來執行管理工作。 在 Azure AD 中，作法上您可以註冊應用程式並向 Azure AD 驗證。 使用採用 *OAuth 2.0 用戶端認證授與* 的 [應用程式識別碼](../active-directory/develop/v2-oauth2-client-creds-grant-flow.md)即可完成。 在此情況下，應用程式會以本身 (而非使用者的身分) 呼叫圖形 API。

在本文中，您將瞭解如何執行自動化使用案例。 您將建置 .NET 4.5 `B2CGraphClient` 來執行使用者建立、讀取、更新和刪除 (CRUD) 作業。 用戶端會有 Windows 命令列介面讓您叫用各種方法。 不過，程式碼是以非互動式、自動化的方式來撰寫的行為。

## <a name="prerequisites"></a>Prerequisites

您需要有 Azure AD B2C 租用戶，才能建立應用程式或使用者。 如果您還沒有帳戶，請[建立一個 Azure Active Directory B2C 的租](tutorial-create-tenant.md)使用者。

## <a name="register-an-application"></a>註冊應用程式

一旦有了 Azure AD B2C 租使用者，您就必須使用[Azure 入口網站](https://portal.azure.com)來註冊管理應用程式。 您也需要授與它代表您的自動化腳本或管理應用程式執行管理工作所需的許可權。

### <a name="register-application-in-azure-active-directory"></a>在 Azure Active Directory 中註冊應用程式

若要將 Azure AD 圖形 API 與 B2C 租使用者搭配使用，您需要使用 Azure Active Directory 應用程式註冊工作流程來註冊應用程式。

[!INCLUDE [active-directory-b2c-appreg-mgmt](../../includes/active-directory-b2c-appreg-mgmt.md)]

### <a name="assign-api-access-permissions"></a>指派 API 存取權限

[!INCLUDE [active-directory-b2c-permissions-directory](../../includes/active-directory-b2c-permissions-directory.md)]

### <a name="create-client-secret"></a>建立用戶端密碼

[!INCLUDE [active-directory-b2c-client-secret](../../includes/active-directory-b2c-client-secret.md)]

您現在有一個應用程式，具有在 Azure AD B2C 租使用者中*建立*、*讀取*和*更新*使用者的許可權。 繼續進行下一節，以新增使用者*刪除*和*密碼更新*許可權。

## <a name="add-user-delete-and-password-update-permissions"></a>新增使用者刪除和密碼更新許可權

您先前授與的*讀取和寫入目錄資料*許可權**不**包含刪除使用者或更新其密碼的能力。

如果您想要讓應用程式能夠刪除使用者或更新密碼，您必須授與*使用者系統管理員*角色。

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 在入口網站工具列中選取 [**目錄 + 訂**用帳戶] 圖示，然後選取包含您 Azure AD B2C 租使用者的目錄。
1. 在 Azure 入口網站中，搜尋並選取  **Azure AD B2C**。
1. 在 [**管理**] 底下，選取 [**角色和系統管理員**]。
1. 選取 [**使用者系統管理員**] 角色。
1. 選取 [**新增指派**]。
1. 在 [**選取**] 文字方塊中，輸入您先前註冊之應用程式的名稱，例如*managementapp1*。 當您的應用程式出現在搜尋結果中時，請加以選取。
1. 選取 [新增]。 可能需要幾分鐘的時間才能完全傳播許可權。

您的 Azure AD B2C 應用程式現在具有在 B2C 租使用者中刪除使用者或更新其密碼所需的其他許可權。

## <a name="get-the-sample-code"></a>取得範例程式碼

此程式碼範例是一個 .NET 主控台應用程式，它會使用[Active Directory 驗證程式庫（ADAL）](../active-directory/azuread-dev/active-directory-authentication-libraries.md)與 Azure AD 圖形 API 進行互動。 其程式碼示範如何呼叫 API，以程式設計方式管理 Azure AD B2C 租使用者中的使用者。

您可以[下載範例](https://github.com/AzureADQuickStarts/B2C-GraphAPI-DotNet/archive/master.zip)封存（\*.zip）或複製 GitHub 存放庫：

```cmd
git clone https://github.com/AzureADQuickStarts/B2C-GraphAPI-DotNet.git
```

取得程式碼範例之後，請針對您的環境進行設定，然後建立專案：

1. 在 Visual Studio 中開啟 `B2CGraphClient\B2CGraphClient.sln` 解決方案。
1. 在**B2CGraphClient**專案中，開啟*app.config*檔案。
1. 以下列 XML 取代 `<appSettings>` 區段。 然後，將 `{your-b2c-tenant}` 取代為您的租使用者名稱，並以您先前記錄的值 `{Application ID}` 和 `{Client secret}`。

    ```xml
    <appSettings>
        <add key="b2c:Tenant" value="{your-b2c-tenant}.onmicrosoft.com" />
        <add key="b2c:ClientId" value="{Application ID}" />
        <add key="b2c:ClientSecret" value="{Client secret}" />
    </appSettings>
    ```

1. 建置方案。 以滑鼠右鍵按一下 方案總管中的  **B2CGraphClient**  方案，然後選取 **重建方案**。

如果組建成功，可以在 `B2CGraphClient\bin\Debug`中找到 `B2C.exe` 主控台應用程式。

## <a name="review-the-sample-code"></a>檢閱範例程式碼

若要使用 B2CGraphClient，請開啟命令提示字元（`cmd.exe`），然後變更為專案的 `Debug` 目錄。 然後，執行 `B2C Help` 命令。

```cmd
cd B2CGraphClient\bin\Debug
B2C Help
```

`B2C Help` 命令會顯示可用子命令的簡短描述。 每當您叫用其中一個子命令時，`B2CGraphClient` 會將要求傳送至 Azure AD 圖形 API。

下列各節將討論應用程式的程式碼對 Azure AD 圖形 API 的呼叫。

### <a name="get-an-access-token"></a>取得存取權杖

Azure AD 圖形 API 的任何要求都需要存取權杖以進行驗證。 `B2CGraphClient` 會使用開放原始碼 Active Directory 驗證程式庫（ADAL）來協助取得存取權杖。 ADAL 藉由提供 helper API 並處理一些重要的細節，例如快取存取權杖，讓權杖取得變得更容易。 不過，您不需要使用 ADAL 來取得權杖。 您可以改為手動製作 HTTP 要求來取得權杖。

> [!NOTE]
> 您必須使用 ADAL v2 或更新版本，取得可與 Azure AD 圖形 API 搭配使用的存取權杖。 您無法使用 ADAL v1。

當 `B2CGraphClient` 執行時，它會建立 `B2CGraphClient` 類別的實例。 此類別的函式會設定 ADAL 驗證樣板：

```csharp
public B2CGraphClient(string clientId, string clientSecret, string tenant)
{
    // The client_id, client_secret, and tenant are provided in Program.cs, which pulls the values from App.config
    this.clientId = clientId;
    this.clientSecret = clientSecret;
    this.tenant = tenant;

    // The AuthenticationContext is ADAL's primary class, in which you indicate the tenant to use.
    this.authContext = new AuthenticationContext("https://login.microsoftonline.com/" + tenant);

    // The ClientCredential is where you pass in your client_id and client_secret, which are
    // provided to Azure AD in order to receive an access_token by using the app's identity.
    this.credential = new ClientCredential(clientId, clientSecret);
}
```

讓我們使用 `B2C Get-User` 命令做為範例。

叫用不含其他引數的 `B2C Get-User` 時，應用程式會呼叫 `B2CGraphClient.GetAllUsers()` 方法。 `GetAllUsers()` 接著會呼叫 `B2CGraphClient.SendGraphGetRequest()`，這會將 HTTP GET 要求提交至 Azure AD 圖形 API。 在 `B2CGraphClient.SendGraphGetRequest()` 傳送 GET 要求之前，它會先使用 ADAL 取得存取權杖：

```csharp
public async Task<string> SendGraphGetRequest(string api, string query)
{
    // First, use ADAL to acquire a token by using the app's identity (the credential)
    // The first parameter is the resource we want an access_token for; in this case, the Graph API.
    AuthenticationResult result = authContext.AcquireToken("https://graph.windows.net", credential);
    ...
```

您可以呼叫 ADAL `AuthenticationContext.AcquireToken()` 方法，以取得圖形 API 的存取權杖。 然後 ADAL 會傳回 `access_token` 表示應用程式的身分識別。

### <a name="read-users"></a>讀取使用者

當您想要取得使用者清單，或從 Azure AD 圖形 API 取得特定使用者時，您可以將 HTTP `GET` 要求傳送至 `/users` 端點。 要求取得租用戶中所有使用者時，情況如下：

```HTTP
GET https://graph.windows.net/contosob2c.onmicrosoft.com/users?api-version=1.6
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
```

若要查看此要求，請執行：

 ```cmd
 B2C Get-User
 ```

有兩個重點值得注意：

* 使用 ADAL 取得的存取權杖會使用 `Bearer` 配置新增至 `Authorization` 標頭。
* 對於 B2C 租用戶，您必須使用查詢參數 `api-version=1.6`。

這兩個細節都在 `B2CGraphClient.SendGraphGetRequest()` 方法中處理：

```csharp
public async Task<string> SendGraphGetRequest(string api, string query)
{
    ...

    // For B2C user management, be sure to use the 1.6 Graph API version.
    HttpClient http = new HttpClient();
    string url = "https://graph.windows.net/" + tenant + api + "?" + "api-version=1.6";
    if (!string.IsNullOrEmpty(query))
    {
        url += "&" + query;
    }

    // Append the access token for the Graph API to the Authorization header of the request by using the Bearer scheme.
    HttpRequestMessage request = new HttpRequestMessage(HttpMethod.Get, url);
    request.Headers.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);
    HttpResponseMessage response = await http.SendAsync(request);

    ...
```

### <a name="create-consumer-user-accounts"></a>建立取用者的使用者帳戶

當您在 B2C 租使用者中建立使用者帳戶時，您可以將 HTTP `POST` 要求傳送至 `/users` 端點。 下列 HTTP `POST` 要求會顯示要在租使用者中建立的範例使用者。

建立取用者使用者時，需要下列要求中的大部分屬性。 `//` 批註已包含在說明中，請勿將它們包含在實際的要求中。

```HTTP
POST https://graph.windows.net/contosob2c.onmicrosoft.com/users?api-version=1.6
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
Content-Type: application/json
Content-Length: 338

{
    // All of these properties are required to create consumer users.

    "accountEnabled": true,
    "signInNames": [                           // controls which identifier the user uses to sign in to the account
        {
            "type": "emailAddress",            // can be 'emailAddress' or 'userName'
            "value": "consumer@fabrikam.com"
        }
    ],
    "creationType": "LocalAccount",            // always set to 'LocalAccount'
    "displayName": "Consumer User",            // a value that can be used for displaying to the end user
    "mailNickname": "cuser",                   // an email alias for the user
    "passwordProfile": {
        "password": "P@ssword!",
        "forceChangePasswordNextLogin": false  // always set to false
    },
    "passwordPolicies": "DisablePasswordExpiration"
}
```

若要查看此要求，請執行下列其中一個命令：

```cmd
B2C Create-User ..\..\..\usertemplate-email.json
B2C Create-User ..\..\..\usertemplate-username.json
```

`Create-User` 命令會採用 JSON 檔案作為輸入參數，其中包含使用者物件的 JSON 標記法。 程式碼範例中有兩個範例 JSON 檔案： `usertemplate-email.json` 和 `usertemplate-username.json`。 您可以修改這些檔案以符合您的需求。 除了上述必要欄位以外，檔案中還包含數個選擇性欄位。

如需必要和選擇性欄位的詳細資訊，請參閱[實體和複雜型別參考 |圖形 API 參考](/previous-versions/azure/ad/graph/api/entity-and-complex-type-reference)。

您可以在 `B2CGraphClient.SendGraphPostRequest()`中查看 POST 要求的結構：

* 它會將存取權杖附加至要求的 `Authorization` 標頭。
* 它會設定 `api-version=1.6`。
* 它會將 JSON 使用者物件加入要求主體中。

> [!NOTE]
> 如果您想要從現有使用者存放區遷移的帳戶，其密碼強度比[Azure AD B2C 強制執行的強式密碼強度](user-flow-password-complexity.md)還低，您可以使用 `passwordPolicies` 屬性中的 `DisableStrongPassword` 值來停用強式密碼需求。 例如，您可以修改先前的建立使用者要求，如下所示： `"passwordPolicies": "DisablePasswordExpiration, DisableStrongPassword"`。

### <a name="update-consumer-user-accounts"></a>更新取用者的使用者帳戶

當您更新使用者物件時，此程式類似于您用來建立使用者物件的進程，但會使用 HTTP `PATCH` 方法：

```HTTP
PATCH https://graph.windows.net/contosob2c.onmicrosoft.com/users/<user-object-id>?api-version=1.6
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
Content-Type: application/json
Content-Length: 37

{
    "displayName": "Joe Consumer"    // this request updates only the user's displayName
}
```

嘗試藉由修改 JSON 檔案中的某些值來更新使用者，然後使用 `B2CGraphClient` 執行下列其中一個命令：

```cmd
B2C Update-User <user-object-id> ..\..\..\usertemplate-email.json
B2C Update-User <user-object-id> ..\..\..\usertemplate-username.json
```

如需有關如何傳送此要求的詳細資訊，請檢閱 `B2CGraphClient.SendGraphPatchRequest()` 方法。

### <a name="search-users"></a>搜尋使用者

您可以透過下列方式在 B2C 租使用者中搜尋使用者：

* 參考使用者的**物件識別碼**。
* 參考其登入識別碼，也就是 `signInNames` 屬性。
* 參考任何有效的 OData 參數。 例如，「givenName」、「姓氏」、「displayName」等等。

執行下列其中一個命令來搜尋使用者：

```cmd
B2C Get-User <user-object-id>
B2C Get-User <filter-query-expression>
```

例如，

```cmd
B2C Get-User 2bcf1067-90b6-4253-9991-7f16449c2d91
B2C Get-User $filter=signInNames/any(x:x/value%20eq%20%27consumer@fabrikam.com%27)
B2C get-user $filter=givenName%20eq%20%27John%27
B2C get-user $filter=surname%20eq%20%27Doe%27
B2C get-user $filter=displayName%20eq%20%27John%20Doe%27
```

### <a name="delete-users"></a>刪除使用者

若要刪除使用者，請使用 HTTP `DELETE` 方法，並以使用者的物件識別碼來建立 URL：

```HTTP
DELETE https://graph.windows.net/contosob2c.onmicrosoft.com/users/<user-object-id>?api-version=1.6
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
```

若要查看範例，請輸入此命令並檢視主控台印出的 Delete 要求：

```cmd
B2C Delete-User <object-id-of-user>
```

如需有關如何傳送此要求的詳細資訊，請檢閱 `B2CGraphClient.SendGraphDeleteRequest()` 方法。

除了使用者管理，您還可以使用 Azure AD 圖形 API 執行其他許多動作。 [Azure AD 圖形 API 參考](/previous-versions/azure/ad/graph/api/api-catalog) 會提供每個動作的詳細資訊以及範例要求。

## <a name="use-custom-attributes"></a>使用自訂屬性

大部分消費者應用程式都需要儲存某種自訂使用者設定檔資訊。 其中一種方式是在 B2C 租使用者中定義自訂屬性。 接著，您可以使用處理使用者物件上任何其他屬性的相同方式來處理該屬性。 該屬性就像登入權杖中的宣告，您可以更新、刪除、查詢、傳送該屬性。

若要在 B2C 租用戶中定義自訂屬性，請參閱 [B2C 自訂屬性參考](user-flow-custom-attributes.md)。

您可以使用下列 `B2CGraphClient` 命令來查看 B2C 租使用者中定義的自訂屬性：

```cmd
B2C Get-B2C-Application
B2C Get-Extension-Attribute <object-id-in-the-output-of-the-above-command>
```

輸出會顯示每個自訂屬性的詳細資料。 例如，

```json
{
      "odata.type": "Microsoft.DirectoryServices.ExtensionProperty",
      "objectType": "ExtensionProperty",
      "objectId": "cec6391b-204d-42fe-8f7c-89c2b1964fca",
      "deletionTimestamp": null,
      "appDisplayName": "",
      "name": "extension_55dc0861f9a44eb999e0a8a872204adb_Jersey_Number",
      "dataType": "Integer",
      "isSyncedFromOnPremises": false,
      "targetObjects": [
        "User"
      ]
}
```

您可以使用完整名稱做為使用者物件的屬性，例如 `extension_55dc0861f9a44eb999e0a8a872204adb_Jersey_Number`。 使用新的屬性和屬性的值來更新您的 JSON 檔案，然後執行：

```cmd
B2C Update-User <object-id-of-user> <path-to-json-file>
```

## <a name="next-steps"></a>後續步驟

使用 `B2CGraphClient`，您會有一個服務應用程式可利用程式設計方式來管理 B2C 租用戶使用者。 `B2CGraphClient` 會使用自己的應用程式身分識別，向 Azure AD 圖形 API 進行驗證。 它也會使用用戶端密碼來取得權杖。

當您將此功能納入您自己的應用程式中時，請記住 B2C 應用程式的幾個要點：

* 在租使用者中授與應用程式必要的許可權。
* 當您呼叫圖形 API 時，請使用 `api-version=1.6`。
* 當建立和更新取用者使用者，有幾個必要的屬性，如上所述。
