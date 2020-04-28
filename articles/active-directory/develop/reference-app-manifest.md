---
title: 瞭解 Azure Active Directory 應用程式資訊清單
description: 詳細說明 Azure Active Directory 應用程式資訊清單；此資訊清單代表應用程式在 Azure AD 租用戶中的身分識別組態，可用來協助進行 OAuth 授權、同意體驗等等。
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/15/2020
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: sureshja
ms.openlocfilehash: 9f2ed6ea8cc75e2ee72f15c14f3de7bb8bf8cef6
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81450871"
---
# <a name="azure-active-directory-app-manifest"></a>Azure Active Directory 應用程式資訊清單

應用程式資訊清單包含 Microsoft 身分識別平台中應用程式物件的所有屬性定義。 它也可作為更新應用程式物件的機制。 如需應用程式實體及其架構的詳細資訊，請參閱[圖形 API 應用程式實體檔](https://docs.microsoft.com/previous-versions/azure/ad/graph/api/entity-and-complex-type-reference#application-entity)。

您可以透過 Azure 入口網站或以程式設計方式使用[REST API](https://docs.microsoft.com/previous-versions/azure/ad/graph/api/entity-and-complex-type-reference#application-entity)或[PowerShell](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0#applications)，來設定應用程式的屬性。 不過，在某些情況下，您需要編輯應用程式資訊清單，以設定應用程式的屬性。 這些案例包括：

* 如果您將應用程式註冊為 Azure AD 多租使用者和個人 Microsoft 帳戶，則無法在 UI 中變更支援的 Microsoft 帳戶。 相反地，您必須使用應用程式資訊清單編輯器來變更支援的帳戶類型。
* 如果您需要定義應用程式支援的權限和角色，則必須修改應用程式資訊清單。

## <a name="configure-the-app-manifest"></a>設定應用程式資訊清單

若要設定應用程式資訊清單：

1. 移至 [Azure 入口網站](https://portal.azure.com)。 搜尋並選取 [ **Azure Active Directory** ] 服務。
1. 選取 [**應用程式註冊**]。
1. 選取您要設定的應用程式。
1. 從應用程式的 [概觀]**** 頁面，選取 [資訊清單]**** 區段。 Web 式的資訊清單編輯器隨即開啟，以供您在入口網站內編輯資訊清單。 或者，您也可以選取 [下載]**** 在本機編輯資訊清單，然後使用 [上傳]**** 將其重新套用到您的應用程式。

## <a name="manifest-reference"></a>資訊清單參考

本節說明在應用程式資訊清單中找到的屬性。

### <a name="id-attribute"></a>ID 屬性

| Key | 值類型 |
| :--- | :--- |
| id | String |

目錄中應用程式的唯一識別碼。 此識別碼不是用來在任何通訊協定交易中識別應用程式的識別碼。 它是用來參考目錄查詢中的物件。

範例：

```json
    "id": "f7f9acfc-ae0c-4d6c-b489-0a81dc1652dd",
```

### <a name="accesstokenacceptedversion-attribute"></a>accessTokenAcceptedVersion 屬性

| Key | 值類型 |
| :--- | :--- |
| accessTokenAcceptedVersion | 可為 null 的 Int32 |

指定資源所需的存取權杖版本。 此參數會變更產生的 JWT 版本和格式，與用來要求存取權杖的端點或用戶端無關。

由客戶端選擇的使用端點 v1.0 或 v2.0 僅影響 id_tokens 的版本。 資源必須明確設定 `accesstokenAcceptedVersion`，以表示支援的存取權杖格式。

`accesstokenAcceptedVersion` 的可能值為 1、2 或 Null。 如果值為 null，則此參數預設為1，其對應于 v1.0 端點。

如果`signInAudience`為`AzureADandPersonalMicrosoftAccount`，則此值必須`2`是。

範例：

```json
    "accessTokenAcceptedVersion": 2,
```

### <a name="addins-attribute"></a>addIns 屬性

| Key | 值類型 |
| :--- | :--- |
| addIns | 集合 |

定義取用服務可用來在特定內容中呼叫應用程式的自訂行為。 例如，可以轉譯檔案資料流程的應用程式可能會設定`addIns`其 "FileHandler" 功能的屬性。 此參數可讓 Office 365 之類的服務在使用者正在處理的檔內容中呼叫應用程式。

範例：

```json
    "addIns": [
       {
        "id": "968A844F-7A47-430C-9163-07AE7C31D407",
        "type":" FileHandler",
        "properties": [
           {
              "key": "version",
              "value": "2"
           }
        ]
       }
    ],
```

### <a name="allowpublicclient-attribute"></a>allowPublicClient 屬性

| Key | 值類型 |
| :--- | :--- |
| allowPublicClient | Boolean |

指定後援應用程式類型。 根據預設，Azure AD 會從 replyUrlsWithType 推斷應用程式類型。 在某些情況下，Azure AD 無法判斷用戶端應用程式類型。 例如，其中一個案例是[ROPC](https://tools.ietf.org/html/rfc6749#section-4.3)流程，其中發生 HTTP 要求，而不需要 URL 重新導向）。 在這些情況下，Azure AD 會根據這個屬性的值來解讀應用程式類型。 如果此值設為 true，後援應用程式類型就會設定為公用用戶端，例如在行動裝置上執行的已安裝應用程式。 預設值為 false，這表示後援應用程式類型是機密用戶端，例如 Web 應用程式。

範例：

```json
    "allowPublicClient": false,
```

### <a name="availabletoothertenants-attribute"></a>availableToOtherTenants 屬性

| Key | 值類型 |
| :--- | :--- |
| availableToOtherTenants | Boolean |

如果應用程式與其他租使用者共用，則設定為 true;否則為 false。

> [!NOTE]
> 此屬性僅適用于**應用程式註冊（舊版）** 體驗。 `signInAudience`在[應用程式註冊](https://go.microsoft.com/fwlink/?linkid=2083908)體驗中取代為。

### <a name="appid-attribute"></a>appId 屬性

| Key | 值類型 |
| :--- | :--- |
| appId | String |

針對由 Azure AD 指派給應用程式的應用程式，指定唯一識別碼。

範例：

```json
    "appId": "601790de-b632-4f57-9523-ee7cb6ceba95",
```

### <a name="approles-attribute"></a>appRoles 屬性

| Key | 值類型 |
| :--- | :--- |
| appRoles | 集合 |

指定應用程式可以宣告的角色集合。 這些角色可以指派給使用者、群組或服務主體。 如需更多範例和資訊，請參閱在[您的應用程式中新增應用程式角色，並在權杖中接收它們](howto-add-app-roles-in-azure-ad-apps.md)。

範例：

```json
    "appRoles": [
        {
           "allowedMemberTypes": [
               "User"
           ],
           "description": "Read-only access to device information",
           "displayName": "Read Only",
           "id": "601790de-b632-4f57-9523-ee7cb6ceba95",
           "isEnabled": true,
           "value": "ReadOnly"
        }
    ],
```

### <a name="displayname-attribute"></a>displayName 屬性

| Key | 值類型 |
| :--- | :--- |
| displayName | String |

應用程式的顯示名稱。

> [!NOTE]
> 此屬性僅適用于**應用程式註冊（舊版）** 體驗。 `name`在[應用程式註冊](https://go.microsoft.com/fwlink/?linkid=2083908)體驗中取代為。

### <a name="errorurl-attribute"></a>errorUrl 屬性

| Key | 值類型 |
| :--- | :--- |
| errorUrl | String |

不支援。

### <a name="groupmembershipclaims-attribute"></a>groupMembershipClaims 屬性

| Key | 值類型 |
| :--- | :--- |
|groupMembershipClaims | String |

可設定應用程式所需使用者或 OAuth 2.0 存取權杖中所發出的 `groups` 宣告。 若要設定此屬性，請使用下列其中一個有效字串值：

- `"None"`
- `"SecurityGroup"` (適用於安全性群組和 Azure AD 角色)
- `"All"` (這會取得登入使用者所屬的所有安全性群組、通訊群組及 Azure AD 目錄角色)。

範例：

```json
    "groupMembershipClaims": "SecurityGroup",
```

### <a name="homepage-attribute"></a>首頁屬性

| Key | 值類型 |
| :--- | :--- |
| homepage |String |

應用程式首頁的 URL。

> [!NOTE]
> 此屬性僅適用于**應用程式註冊（舊版）** 體驗。 `signInUrl`在[應用程式註冊](https://go.microsoft.com/fwlink/?linkid=2083908)體驗中取代為。

### <a name="objectid-attribute"></a>objectId 屬性

| Key | 值類型 |
| :--- | :--- |
|objectId | String |

目錄中應用程式的唯一識別碼。

這僅適用于**應用程式註冊（舊版）** 體驗。 `id`在[應用程式註冊](https://go.microsoft.com/fwlink/?linkid=2083908)體驗中取代為。

範例：

```json
    "objectId": "f7f9acfc-ae0c-4d6c-b489-0a81dc1652dd",
```

### <a name="optionalclaims-attribute"></a>optionalClaims 屬性

| Key | 值類型 |
| :--- | :--- |
| optionalClaims | String |

由此特定應用程式的安全性權杖服務在權杖中傳回的選擇性宣告。

此時，同時支援個人帳戶和 Azure AD (透過應用程式註冊入口網站註冊) 的應用程式無法使用選擇性宣告。 不過，使用 v2.0 端點只註冊 Azure AD 的應用程式，可在資訊清單中取得所要求的選擇性宣告。 如需詳細資訊，請參閱[選擇性宣告](active-directory-optional-claims.md)。

範例：

```json
    "optionalClaims": null,
```



### <a name="identifieruris-attribute"></a>identifierUris 屬性

| Key | 值類型 |
| :--- | :--- |
| identifierUris | 字串陣列 |

當應用程式為多租用戶時，專門在 Web 應用程式的 Azure AD 租用戶或經過驗證的自訂網域中，用於識別 Web 應用程式的使用者定義 URI。

範例：

```json
    "identifierUris": "https://MyRegisteredApp",
```

### <a name="informationalurls-attribute"></a>informationalUrls 屬性

| Key | 值類型 |
| :--- | :--- |
| informationalUrls | String |

指定應用程式服務條款和隱私權聲明的連結。 使用者會透過使用者同意體驗看到服務條款和隱私權聲明。 如需詳細資訊，請參閱[如何：新增 Azure AD 註冊應用程式的服務條款和隱私權聲明](howto-add-terms-of-service-privacy-statement.md)。

範例：

```json
    "informationalUrls": {
        "termsOfService": "https://MyRegisteredApp/termsofservice",
        "support": "https://MyRegisteredApp/support",
        "privacy": "https://MyRegisteredApp/privacystatement",
        "marketing": "https://MyRegisteredApp/marketing"
    },
```

### <a name="keycredentials-attribute"></a>keyCredentials 屬性

| Key | 值類型 |
| :--- | :--- |
| keyCredentials | 集合 |

保留對應用程式指定認證、字串型的共用密碼以及 X.509 憑證的參考。 要求存取權杖時，會使用這些認證（當應用程式作為用戶端，而不是做為資源時）。

範例：

```json
    "keyCredentials": [
        {
           "customKeyIdentifier":null,
           "endDate":"2018-09-13T00:00:00Z",
           "keyId":"<guid>",
           "startDate":"2017-09-12T00:00:00Z",
           "type":"AsymmetricX509Cert",
           "usage":"Verify",
           "value":null
        }
    ],
```

### <a name="knownclientapplications-attribute"></a>knownClientApplications 屬性

| Key | 值類型 |
| :--- | :--- |
| knownClientApplications | 字串陣列 |

如果您有包含兩個組件 (用戶端應用程式及自訂 Web API 應用程式) 的解決方案，則用來統合同意。 如果您將此值輸入為用戶端應用程式的 appID ，則使用者只需同意用戶端應用程式一次。 Azure AD 會知道同意用戶端表示隱含地同意 Web API。 它會同時自動布建用戶端和 Web API 的服務主體。 用戶端與 Web API 應用程式都必須在相同的租用戶中註冊。

範例：

```json
    "knownClientApplications": ["f7f9acfc-ae0c-4d6c-b489-0a81dc1652dd"],
```

### <a name="logourl-attribute"></a>logoUrl 屬性

| Key | 值類型 |
| :--- | :--- |
| logoUrl | String |

此為唯讀值，會指向入口網站中已上傳標誌的 CDN URL。

範例：

```json
    "logoUrl": "https://MyRegisteredAppLogo",
```

### <a name="logouturl-attribute"></a>logoutUrl 屬性

| Key | 值類型 |
| :--- | :--- |
| logoutUrl | String |

用於登出應用程式的 URL。

範例：

```json
    "logoutUrl": "https://MyRegisteredAppLogout",
```

### <a name="name-attribute"></a>名稱屬性

| Key | 值類型 |
| :--- | :--- |
| NAME | String |

應用程式的顯示名稱。

範例：

```json
    "name": "MyRegisteredApp",
```

### <a name="oauth2allowimplicitflow-attribute"></a>oauth2AllowImplicitFlow 屬性

| Key | 值類型 |
| :--- | :--- |
| oauth2AllowImplicitFlow | Boolean |

指定此 Web 應用程式可否要求 OAuth2.0 隱含流程存取權杖。 預設值為 false。 此旗標用於瀏覽器型應用程式，例如 JavaScript 單頁應用程式。 若要進一步了解，請在目錄中輸入 `OAuth 2.0 implicit grant flow` 並查看隱含流程相關主題。

範例：

```json
    "oauth2AllowImplicitFlow": false,
```

### <a name="oauth2allowidtokenimplicitflow-attribute"></a>oauth2AllowIdTokenImplicitFlow 屬性

| Key | 值類型 |
| :--- | :--- |
| oauth2AllowIdTokenImplicitFlow | Boolean |

指定此 Web 應用程式可否要求 OAuth2.0 隱含流程識別碼權杖。 預設值為 false。 此旗標用於瀏覽器型應用程式，例如 JavaScript 單頁應用程式。

範例：

```json
    "oauth2AllowIdTokenImplicitFlow": false,
```

### <a name="oauth2permissions-attribute"></a>oauth2Permissions 屬性

| Key | 值類型 |
| :--- | :--- |
| oauth2Permissions | 集合 |

指定 Web API (資源) 應用程式公開給用戶端應用程式的 OAuth 2.0 權限範圍集合。 這些權限範圍可能會在同意過程中授與用戶端應用程式。

範例：

```json
    "oauth2Permissions": [
       {
          "adminConsentDescription": "Allow the app to access resources on behalf of the signed-in user.",
          "adminConsentDisplayName": "Access resource1",
          "id": "<guid>",
          "isEnabled": true,
          "type": "User",
          "userConsentDescription": "Allow the app to access resource1 on your behalf.",
          "userConsentDisplayName": "Access resources",
          "value": "user_impersonation"
        }
    ],
```

### <a name="oauth2requiredpostresponse-attribute"></a>oauth2RequiredPostResponse 屬性

| Key | 值類型 |
| :--- | :--- |
| oauth2RequiredPostResponse | Boolean |

指定 Azure AD 在 OAuth 2.0 權杖要求期間是否允許 POST 要求 (相對於 GET 要求)。 預設值為 false，亦即指定只允許 GET 要求。

範例：

```json
    "oauth2RequirePostResponse": false,
```

### <a name="parentalcontrolsettings-attribute"></a>parentalControlSettings 屬性

| Key | 值類型 |
| :--- | :--- |
| parentalControlSettings | String |

- `countriesBlockedForMinors` 指定會對未成年人封鎖此應用程式的國家/地區。
- `legalAgeGroupRule` 指定會套用到應用程式使用者的法定年齡群組規則。 可以設定為 `Allow`、`RequireConsentForPrivacyServices`、`RequireConsentForMinors`、`RequireConsentForKids` 或 `BlockMinors`。  

範例：

```json
    "parentalControlSettings": {
        "countriesBlockedForMinors": [],
        "legalAgeGroupRule": "Allow"
    },
```

### <a name="passwordcredentials-attribute"></a>passwordCredentials 屬性

| Key | 值類型 |
| :--- | :--- |
| passwordCredentials | 集合 |

請參閱 `keyCredentials` 屬性的描述。

範例：

```json
    "passwordCredentials": [
      {
        "customKeyIdentifier": null,
        "endDate": "2018-10-19T17:59:59.6521653Z",
        "keyId": "<guid>",
        "startDate":"2016-10-19T17:59:59.6521653Z",
        "value":null
      }
    ],
```

### <a name="preauthorizedapplications-attribute"></a>preAuthorizedApplications 屬性

| Key | 值類型 |
| :--- | :--- |
| preAuthorizedApplications | 集合 |

列出需要隱含同意的應用程式和要求的權限。 需要管理員已向應用程式提供同意。 preAuthorizedApplications 不需要使用者同意要求的權限。 PreAuthorizedApplications 中所列的權限不需要使用者同意。 不過，preAuthorizedApplications 中未列出的任何其他要求權限則需要使用者同意。

範例：

```json
    "preAuthorizedApplications": [
       {
          "appId": "abcdefg2-000a-1111-a0e5-812ed8dd72e8",
          "permissionIds": [
             "8748f7db-21fe-4c83-8ab5-53033933c8f1"
            ]
        }
    ],
```

### <a name="publicclient-attribute"></a>publicClient 屬性

| Key | 值類型 |
| :--- | :--- |
| publicClient | Boolean|

指定此應用程式是否為公用的用戶端 (例如行動裝置上執行的已安裝應用程式)。 

此屬性僅適用于**應用程式註冊（舊版）** 體驗。 `allowPublicClient`在[應用程式註冊](https://go.microsoft.com/fwlink/?linkid=2083908)體驗中取代為。

### <a name="publisherdomain-attribute"></a>publisherDomain 屬性

| Key | 值類型 |
| :--- | :--- |
| publisherDomain | String |

應用程式的已驗證發行者網域。 唯讀。

範例：

```json
    "publisherDomain": "https://www.contoso.com",
````

### <a name="replyurls-attribute"></a>回復 url 路徑屬性

| Key | 值類型 |
| :--- | :--- |
| replyUrls | 字串陣列 |

傳回權杖時，此多值屬性會保留 Azure AD 接受作為目的地的已註冊 redirect_uri 值清單。

此屬性僅適用于**應用程式註冊（舊版）** 體驗。 `replyUrlsWithType`在[應用程式註冊](https://go.microsoft.com/fwlink/?linkid=2083908)體驗中取代為。

### <a name="replyurlswithtype-attribute"></a>replyUrlsWithType 屬性

| Key | 值類型 |
| :--- | :--- |
| replyUrlsWithType | 集合 |

傳回權杖時，此多值屬性會保留 Azure AD 接受作為目的地的已註冊 redirect_uri 值清單。 每個 URI 值都應該包含相關聯的應用程式類型值。 支援的類型值為：

- `Web`
- `InstalledClient`

若要深入瞭解，請參閱[replyUrl 限制和限制](https://docs.microsoft.com/azure/active-directory/develop/reply-url)。

範例：

```json
    "replyUrlsWithType": [
       {
          "url": "https://localhost:4400/services/office365/redirectTarget.html",
          "type": "InstalledClient"
       }
    ],
```

### <a name="requiredresourceaccess-attribute"></a>requiredResourceAccess 屬性

| Key | 值類型 |
| :--- | :--- |
| requiredResourceAccess | 集合 |

在動態同意的情況下，`requiredResourceAccess` 可對使用靜態同意的使用者，推動系統管理員同意體驗和使用者同意體驗。 不過，此參數不會驅動一般案例的使用者同意體驗。

- `resourceAppId` 是應用程式所需存取資源的唯一識別碼。 此值應該等於目標資源應用程式上宣告的 appId。
- `resourceAccess` 是一個陣列，其中列出應用程式向指定的資源要求的 OAuth2.0 權限範圍和應用程式角色。 包含所指定資源的 `id` 和 `type` 值。

範例：

```json
    "requiredResourceAccess": [
        {
            "resourceAppId": "00000002-0000-0000-c000-000000000000",
            "resourceAccess": [
                {
                    "id": "311a71cc-e848-46a1-bdf8-97ff7156d8e6",
                    "type": "Scope"
                }
            ]
        }
    ],
```

### <a name="samlmetadataurl-attribute"></a>samlMetadataUrl 屬性

| Key | 值類型 |
| :--- | :--- |
| samlMetadataUrl | String |

應用程式 SAML 中繼資料的 URL。

範例：

```json
    "samlMetadataUrl": "https://MyRegisteredAppSAMLMetadata",
```

### <a name="signinurl-attribute"></a>signInUrl 屬性

| Key | 值類型 |
| :--- | :--- |
| signInUrl | String |

指定應用程式首頁的 URL。

範例：

```json
    "signInUrl": "https://MyRegisteredApp",
```

### <a name="signinaudience-attribute"></a>signInAudience 屬性

| Key | 值類型 |
| :--- | :--- |
| signInAudience | String |

指定目前的應用程式支援哪些 Microsoft 帳戶。 支援的值為：
- `AzureADMyOrg`-在組織的 Azure AD 租使用者中使用 Microsoft 公司或學校帳戶（例如，單一租使用者）
- `AzureADMultipleOrgs`-在任何組織的 Azure AD 租使用者中具有 Microsoft 公司或學校帳戶的使用者（例如，多租使用者）
- `AzureADandPersonalMicrosoftAccount`-個人 Microsoft 帳戶或任何組織 Azure AD 租使用者中的公司或學校帳戶
- `PersonalMicrosoftAccount`-用來登入 Xbox 和 Skype 等服務的個人帳戶。

範例：

```json
    "signInAudience": "AzureADandPersonalMicrosoftAccount",
```

### <a name="tags-attribute"></a>tags 屬性

| Key | 值類型 |
| :--- | :--- |
| tags | 字串陣列  |

可用來分類及識別應用程式的自訂字串。

範例：

```json
    "tags": [
       "ProductionApp"
    ],
```

## <a name="common-issues"></a>常見問題

### <a name="manifest-limits"></a>資訊清單限制

應用程式資訊清單有多個屬性，稱為集合;例如，appRoles、keyCredentials、knownClientApplications、identifierUris、redirectUris、requiredResourceAccess 和 oauth2Permissions。 在任何應用程式的完整應用程式資訊清單中，結合所有集合中的專案總數已限制于1200。 如果您先前在應用程式資訊清單中指定100重新導向 Uri，則您只會留下1100個剩餘的專案，以便在構成資訊清單的所有其他集合中使用。

> [!NOTE]
> 如果您嘗試在應用程式資訊清單中新增超過1200個專案，您可能會看到「**無法更新應用程式 xxxxxx」錯誤。錯誤詳細資料：資訊清單的大小已超過其限制。請減少值的數目，然後重試您的要求。** 」

### <a name="unsupported-attributes"></a>不支援的屬性

應用程式資訊清單代表 Azure AD 中基礎應用程式模型的架構。 隨著基礎架構的發展，資訊清單編輯器將會更新，以反映時間的新架構。 因此，您可能會注意到應用程式資訊清單中顯示的新屬性。 在罕見的情況下，您可能會注意到現有的屬性有語法或語義上的變更，或者您可能會發現先前已有的屬性已不再受到支援。 例如，您會在[應用程式註冊](https://go.microsoft.com/fwlink/?linkid=2083908)中看到新的屬性，在應用程式註冊（舊版）體驗中，這是已知的不同名稱。

| 應用程式註冊（舊版）| 應用程式註冊           |
|---------------------------|-----------------------------|
| `availableToOtherTenants` | `signInAudience`            |
| `displayName`             | `name`                      |
| `errorUrl`                | -                           |
| `homepage`                | `signInUrl`                 |
| `objectId`                | `Id`                        |
| `publicClient`            | `allowPublicClient`         |
| `replyUrls`               | `replyUrlsWithType`         |

如需這些屬性的說明，請參閱[資訊清單參考](#manifest-reference)一節。

當您嘗試上傳先前下載的資訊清單時，您可能會看到下列其中一個錯誤。 此錯誤很可能是因為資訊清單編輯器現在支援較新版本的架構，這與您嘗試上傳的不相符。

* 「無法更新 xxxxxx 應用程式。 錯誤詳細資料：不正確物件識別碼「未定義」。 []."
* 「無法更新 xxxxxx 應用程式。 錯誤詳細資料：指定的一或多個屬性值無效。 []."
* 「無法更新 xxxxxx 應用程式。 錯誤詳細資料：不允許在此 api 版本中設定 availableToOtherTenants 以進行更新。 []."
* 「無法更新 xxxxxx 應用程式。 錯誤詳細資料：此應用程式不允許更新 ' 回復 url 路徑 ' 屬性。 請改用 ' replyUrlsWithType ' 屬性。 []."
* 「無法更新 xxxxxx 應用程式。 錯誤詳細資料：找到不具類型名稱的值，而且沒有任何預期的類型可用。 指定模型時，裝載中的每個值都必須具有可在裝載中指定的型別，由呼叫端明確或從父值隱含推斷。 []"

當您看到其中一個錯誤時，我們建議您採取下列動作：

1. 在資訊清單編輯器中個別編輯屬性，而不是上傳先前下載的資訊清單。 使用[資訊清單參考](#manifest-reference)資料表來瞭解新舊屬性的語法和語義，讓您可以成功編輯您感興趣的屬性。 
1. 如果您的工作流程需要您儲存來源存放庫中的資訊清單以供稍後使用，建議您重定基底存放庫中的已儲存資訊清單，以及您在**應用程式註冊**體驗中所看到的資訊清單。

## <a name="next-steps"></a>後續步驟

* 如需應用程式的應用程式與服務主體物件之間關聯性的詳細資訊，請參閱[Azure AD 中的應用程式和服務主體物件](app-objects-and-service-principals.md)。
* 如需一些核心 Microsoft 身分識別平臺開發人員概念的定義，請參閱[microsoft 身分識別平臺開發人員詞彙](developer-glossary.md)。

使用下列意見區段來提供意見反應，以協助改善及塑造我們的內容。

<!--article references -->
[AAD-APP-OBJECTS]:app-objects-and-service-principals.md
[AAD-DEVELOPER-GLOSSARY]:developer-glossary.md
[AAD-GROUPS-FOR-AUTHORIZATION]: http://www.dushyantgill.com/blog/2014/12/10/authorization-cloud-applications-using-ad-groups/
[ADD-UPD-RMV-APP]:quickstart-v1-integrate-apps-with-azure-ad.md
[APPLICATION-ENTITY]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#application-entity
[APPLICATION-ENTITY-APP-ROLE]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#approle-type
[APPLICATION-ENTITY-OAUTH2-PERMISSION]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#oauth2permission-type
[AZURE-PORTAL]: https://portal.azure.com
[DEV-GUIDE-TO-AUTH-WITH-ARM]: http://www.dushyantgill.com/blog/2015/05/23/developers-guide-to-auth-with-azure-resource-manager-api/
[GRAPH-API]: active-directory-graph-api.md
[IMPLICIT-GRANT]:v1-oauth2-implicit-grant-flow.md
[INTEGRATING-APPLICATIONS-AAD]: https://azure.microsoft.com/documentation/articles/active-directory-integrating-applications/
[O365-PERM-DETAILS]: https://msdn.microsoft.com/office/office365/HowTo/application-manifest
[O365-SERVICE-DAEMON-APPS]: https://msdn.microsoft.com/office/office365/howto/building-service-apps-in-office-365
[RBAC-CLOUD-APPS-AZUREAD]: http://www.dushyantgill.com/blog/2014/12/10/roles-based-access-control-in-cloud-applications-using-azure-ad/
