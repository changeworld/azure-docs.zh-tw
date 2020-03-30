---
title: 了解 Azure Active Directory 應用程式資訊清單 | Microsoft Docs
description: 詳細說明 Azure Active Directory 應用程式資訊清單；此資訊清單代表應用程式在 Azure AD 租用戶中的身分識別組態，可用來協助進行 OAuth 授權、同意體驗等等。
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 03/23/2020
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: sureshja
ms.openlocfilehash: 6d9a4af5ee814282589959fcf840c1061358ca18
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/29/2020
ms.locfileid: "80383934"
---
# <a name="azure-active-directory-app-manifest"></a>Azure Active Directory 應用程式資訊清單

應用程式資訊清單包含 Microsoft 身分識別平台中應用程式物件的所有屬性定義。 它也可作為更新應用程式物件的機制。 有關應用程式實體及其架構的詳細資訊，請參閱[圖形 API 應用程式實體文檔](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#application-entity)。

您可以通過 Azure 門戶配置應用的屬性，也可以使用[REST API](https://docs.microsoft.com/previous-versions/azure/ad/graph/api/entity-and-complex-type-reference#application-entity)或[PowerShell](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0#applications)以程式設計方式配置應用的屬性。 不過，在某些情況下，您需要編輯應用程式資訊清單，以設定應用程式的屬性。 這些案例包括：

* 如果將應用註冊為 Azure AD 多租戶和個人 Microsoft 帳戶，則無法更改 UI 中支援的 Microsoft 帳戶。 相反地，您必須使用應用程式資訊清單編輯器來變更支援的帳戶類型。
* 如果您需要定義應用程式支援的權限和角色，則必須修改應用程式資訊清單。

## <a name="configure-the-app-manifest"></a>設定應用程式資訊清單

若要設定應用程式資訊清單：

1. 轉到[Azure 門戶](https://portal.azure.com)。 搜索並選擇**Azure 活動目錄**服務。
1. 選擇**應用註冊**。
1. 選取您要設定的應用程式。
1. 從應用程式的 [概觀]**** 頁面，選取 [資訊清單]**** 區段。 Web 式的資訊清單編輯器隨即開啟，以供您在入口網站內編輯資訊清單。 或者，您也可以選取 [下載]**** 在本機編輯資訊清單，然後使用 [上傳]**** 將其重新套用到您的應用程式。

## <a name="manifest-reference"></a>資訊清單參考

本節介紹應用程式清單中的屬性。

### <a name="accesstokenacceptedversion-attribute"></a>訪問權杖接受版本屬性

| Key | 值類型 |
| :--- | :--- |
| 訪問權杖接受版本 | 可為 null 的 Int32 |

指定資源所需的存取權杖版本。 此參數更改生成的 JWT 的版本和格式，而獨立于用於請求訪問權杖的終結點或用戶端。

由客戶端選擇的使用端點 v1.0 或 v2.0 僅影響 id_tokens 的版本。 資源必須明確設定 `accesstokenAcceptedVersion`，以表示支援的存取權杖格式。

`accesstokenAcceptedVersion` 的可能值為 1、2 或 Null。 如果值為 null，則此參數預設為 1，對應于 v1.0 終結點。

如果`signInAudience``AzureADandPersonalMicrosoftAccount`為 ，則值`2`必須為 。

範例：

```json
    "accessTokenAcceptedVersion": 2,
```

### <a name="addins-attribute"></a>添加項屬性

| Key | 值類型 |
| :--- | :--- |
| addIns | 集合 |

定義取用服務可用來在特定內容中呼叫應用程式的自訂行為。 例如，可以呈現檔流的應用程式可能會為其"FileHandler"功能設置`addIns`屬性。 此參數將允許 Office 365 等服務在使用者正在處理的文檔的上下文中調用應用程式。

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

### <a name="allowpublicclient-attribute"></a>允許公共用戶端屬性

| Key | 值類型 |
| :--- | :--- |
| 允許公共用戶端 | Boolean |

指定後援應用程式類型。 根據預設，Azure AD 會從 replyUrlsWithType 推斷應用程式類型。 在某些情況下，Azure AD 無法確定用戶端應用類型。 例如，其中一種情況是[ROPC](https://tools.ietf.org/html/rfc6749#section-4.3)流，其中 HTTP 要求在沒有 URL 重定向的情況下發生。 在這些情況下，Azure AD 將根據此屬性的值解釋應用程式類型。 如果此值設為 true，後援應用程式類型就會設定為公用用戶端，例如在行動裝置上執行的已安裝應用程式。 預設值為 false，這表示後援應用程式類型是機密用戶端，例如 Web 應用程式。

範例：

```json
    "allowPublicClient": false,
```

### <a name="availabletoothertenants-attribute"></a>可供其他租戶屬性

| Key | 值類型 |
| :--- | :--- |
| availableToOtherTenants | Boolean |

如果應用程式與其他租戶共用，則設置為 true;否則，假。

> [!NOTE]
> 此屬性僅在**應用註冊（舊版）** 體驗中可用。 替換為`signInAudience`[應用註冊](https://go.microsoft.com/fwlink/?linkid=2083908)體驗。

### <a name="appid-attribute"></a>appId 屬性

| Key | 值類型 |
| :--- | :--- |
| appId | String |

針對由 Azure AD 指派給應用程式的應用程式，指定唯一識別碼。

範例：

```json
    "appId": "601790de-b632-4f57-9523-ee7cb6ceba95",
```

### <a name="approles-attribute"></a>應用角色屬性

| Key | 值類型 |
| :--- | :--- |
| appRoles | 集合 |

指定應用程式可以宣告的角色集合。 這些角色可以指派給使用者、群組或服務主體。 有關詳細資訊示例和資訊，請參閱[在應用程式中添加應用角色，並在權杖中接收它們](howto-add-app-roles-in-azure-ad-apps.md)。

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

### <a name="displayname-attribute"></a>顯示名稱屬性

| Key | 值類型 |
| :--- | :--- |
| displayName | String |

應用程式的顯示名稱。

> [!NOTE]
> 此屬性僅在**應用註冊（舊版）** 體驗中可用。 替換為`name`[應用註冊](https://go.microsoft.com/fwlink/?linkid=2083908)體驗。

### <a name="errorurl-attribute"></a>錯誤Url屬性

| Key | 值類型 |
| :--- | :--- |
| errorUrl | String |

不支援。

### <a name="groupmembershipclaims-attribute"></a>組成員要求屬性

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

### <a name="homepage-attribute"></a>主頁屬性

| Key | 值類型 |
| :--- | :--- |
| homepage |String |

應用程式首頁的 URL。

> [!NOTE]
> 此屬性僅在**應用註冊（舊版）** 體驗中可用。 替換為`signInUrl`[應用註冊](https://go.microsoft.com/fwlink/?linkid=2083908)體驗。

### <a name="objectid-attribute"></a>物件 Id 屬性

| Key | 值類型 |
| :--- | :--- |
|objectId | String |

目錄中應用程式的唯一識別碼。

這僅在**應用註冊（舊版）** 體驗中可用。 替換為`id`[應用註冊](https://go.microsoft.com/fwlink/?linkid=2083908)體驗。

範例：

```json
    "objectId": "f7f9acfc-ae0c-4d6c-b489-0a81dc1652dd",
```

### <a name="optionalclaims-attribute"></a>可選索賠屬性

| Key | 值類型 |
| :--- | :--- |
| optionalClaims | String |

由此特定應用程式的安全性權杖服務在權杖中傳回的選擇性宣告。

此時，同時支援個人帳戶和 Azure AD (透過應用程式註冊入口網站註冊) 的應用程式無法使用選擇性宣告。 不過，使用 v2.0 端點只註冊 Azure AD 的應用程式，可在資訊清單中取得所要求的選擇性宣告。 有關詳細資訊，請參閱[可選聲明](active-directory-optional-claims.md)。

範例：

```json
    "optionalClaims": null,
```

### <a name="id-attribute"></a>ID 屬性

| Key | 值類型 |
| :--- | :--- |
| id | String |

目錄中應用程式的唯一識別碼。 此識別碼不是用來在任何通訊協定交易中識別應用程式的識別碼。 它是用來參考目錄查詢中的物件。

範例：

```json
    "id": "f7f9acfc-ae0c-4d6c-b489-0a81dc1652dd",
```

### <a name="identifieruris-attribute"></a>識別碼 Uris 屬性

| Key | 值類型 |
| :--- | :--- |
| identifierUris | 字串陣列 |

當應用程式為多租用戶時，專門在 Web 應用程式的 Azure AD 租用戶或經過驗證的自訂網域中，用於識別 Web 應用程式的使用者定義 URI。

範例：

```json
    "identifierUris": "https://MyRegisteredApp",
```

### <a name="informationalurls-attribute"></a>資訊Urls屬性

| Key | 值類型 |
| :--- | :--- |
| 資訊 Url | String |

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

### <a name="keycredentials-attribute"></a>鍵憑據屬性

| Key | 值類型 |
| :--- | :--- |
| keyCredentials | 集合 |

保留對應用程式指定認證、字串型的共用密碼以及 X.509 憑證的參考。 這些憑據在請求訪問權杖時（當應用充當用戶端而不是作為資源時）使用。

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

### <a name="knownclientapplications-attribute"></a>已知用戶端應用程式屬性

| Key | 值類型 |
| :--- | :--- |
| knownClientApplications | 字串陣列 |

如果您有包含兩個組件 (用戶端應用程式及自訂 Web API 應用程式) 的解決方案，則用來統合同意。 如果您將此值輸入為用戶端應用程式的 appID ，則使用者只需同意用戶端應用程式一次。 Azure AD 將知道同意用戶端意味著隱式同意 Web API。 它將自動同時為用戶端和 Web API 預配服務主體。 用戶端與 Web API 應用程式都必須在相同的租用戶中註冊。

範例：

```json
    "knownClientApplications": ["f7f9acfc-ae0c-4d6c-b489-0a81dc1652dd"],
```

### <a name="logourl-attribute"></a>logoUrl 屬性

| Key | 值類型 |
| :--- | :--- |
| 標誌Url | String |

此為唯讀值，會指向入口網站中已上傳標誌的 CDN URL。

範例：

```json
    "logoUrl": "https://MyRegisteredAppLogo",
```

### <a name="logouturl-attribute"></a>登出Url屬性

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

### <a name="oauth2allowimplicitflow-attribute"></a>oauth2Allow隱式流屬性

| Key | 值類型 |
| :--- | :--- |
| oauth2AllowImplicitFlow | Boolean |

指定此 Web 應用程式可否要求 OAuth2.0 隱含流程存取權杖。 預設值為 false。 此標誌用於基於瀏覽器的應用，如 JavaScript 單頁應用。 若要進一步了解，請在目錄中輸入 `OAuth 2.0 implicit grant flow` 並查看隱含流程相關主題。

範例：

```json
    "oauth2AllowImplicitFlow": false,
```

### <a name="oauth2allowidtokenimplicitflow-attribute"></a>oauth2AllowIdToken隱式流屬性

| Key | 值類型 |
| :--- | :--- |
| oauth2AllowIdToken隱式流 | Boolean |

指定此 Web 應用程式可否要求 OAuth2.0 隱含流程識別碼權杖。 預設值為 false。 此標誌用於基於瀏覽器的應用，如 JavaScript 單頁應用。

範例：

```json
    "oauth2AllowIdTokenImplicitFlow": false,
```

### <a name="oauth2permissions-attribute"></a>oauth2許可權屬性

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

### <a name="oauth2requiredpostresponse-attribute"></a>oauth2必需的後回應屬性

| Key | 值類型 |
| :--- | :--- |
| oauth2RequiredPostResponse | Boolean |

指定 Azure AD 在 OAuth 2.0 權杖要求期間是否允許 POST 要求 (相對於 GET 要求)。 預設值為 false，亦即指定只允許 GET 要求。

範例：

```json
    "oauth2RequirePostResponse": false,
```

### <a name="parentalcontrolsettings-attribute"></a>家長監護設置屬性

| Key | 值類型 |
| :--- | :--- |
| 家長監護設置 | String |

- `countriesBlockedForMinors` 指定會對未成年人封鎖此應用程式的國家/地區。
- `legalAgeGroupRule` 指定會套用到應用程式使用者的法定年齡群組規則。 可以設定為 `Allow`、`RequireConsentForPrivacyServices`、`RequireConsentForMinors`、`RequireConsentForKids` 或 `BlockMinors`。  

範例：

```json
    "parentalControlSettings": {
        "countriesBlockedForMinors": [],
        "legalAgeGroupRule": "Allow"
    },
```

### <a name="passwordcredentials-attribute"></a>密碼憑據屬性

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

### <a name="preauthorizedapplications-attribute"></a>預授權應用程式屬性

| Key | 值類型 |
| :--- | :--- |
| 預授權應用程式 | 集合 |

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

### <a name="publicclient-attribute"></a>公共用戶端屬性

| Key | 值類型 |
| :--- | :--- |
| publicClient | Boolean|

指定此應用程式是否為公用的用戶端 (例如行動裝置上執行的已安裝應用程式)。 

此屬性僅在**應用註冊（舊版）** 體驗中可用。 替換為`allowPublicClient`[應用註冊](https://go.microsoft.com/fwlink/?linkid=2083908)體驗。

### <a name="publisherdomain-attribute"></a>發行者域屬性

| Key | 值類型 |
| :--- | :--- |
| 發行者域 | String |

應用程式的已驗證發佈域。 唯讀。

範例：

```json
    "publisherDomain": "https://www.contoso.com",
````

### <a name="replyurls-attribute"></a>回復 Urls 屬性

| Key | 值類型 |
| :--- | :--- |
| replyUrls | 字串陣列 |

傳回權杖時，此多值屬性會保留 Azure AD 接受作為目的地的已註冊 redirect_uri 值清單。

此屬性僅在**應用註冊（舊版）** 體驗中可用。 替換為`replyUrlsWithType`[應用註冊](https://go.microsoft.com/fwlink/?linkid=2083908)體驗。

### <a name="replyurlswithtype-attribute"></a>回復Urls與類型屬性

| Key | 值類型 |
| :--- | :--- |
| 回復 Urls 與類型 | 集合 |

傳回權杖時，此多值屬性會保留 Azure AD 接受作為目的地的已註冊 redirect_uri 值清單。 每個 URI 值都應包含關聯的應用類型值。 支援的類型值包括：

- `Web`
- `InstalledClient`

要瞭解更多資訊，請參閱[回復 Url 限制和限制](https://docs.microsoft.com/azure/active-directory/develop/reply-url)。

範例：

```json
    "replyUrlsWithType": [
       {
          "url": "https://localhost:4400/services/office365/redirectTarget.html",
          "type": "InstalledClient"
       }
    ],
```

### <a name="requiredresourceaccess-attribute"></a>所需的資源訪問屬性

| Key | 值類型 |
| :--- | :--- |
| requiredResourceAccess | 集合 |

在動態同意的情況下，`requiredResourceAccess` 可對使用靜態同意的使用者，推動系統管理員同意體驗和使用者同意體驗。 但是，此參數不會驅動一般情況的使用者同意體驗。

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

### <a name="signinurl-attribute"></a>符號 InUrl 屬性

| Key | 值類型 |
| :--- | :--- |
| 符號 InUrl | String |

指定應用程式首頁的 URL。

範例：

```json
    "signInUrl": "https://MyRegisteredApp",
```

### <a name="signinaudience-attribute"></a>符號屬性

| Key | 值類型 |
| :--- | :--- |
| 符號在觀眾 | String |

指定目前的應用程式支援哪些 Microsoft 帳戶。 支援的值為：
- `AzureADMyOrg`- 在組織的 Azure AD 租戶中具有 Microsoft 工作或學校帳戶的使用者（例如，單個租戶）
- `AzureADMultipleOrgs`- 在任何組織的 Azure AD 租戶中具有 Microsoft 工作或學校帳戶的使用者（例如，多租戶）
- `AzureADandPersonalMicrosoftAccount`- 具有個人 Microsoft 帳戶或任何組織的 Azure AD 租戶中的工作或學校帳戶的使用者
- `PersonalMicrosoftAccount`- 用於登錄到 Xbox 和 Skype 等服務的個人帳戶。

範例：

```json
    "signInAudience": "AzureADandPersonalMicrosoftAccount",
```

### <a name="tags-attribute"></a>標記屬性

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

### <a name="manifest-limits"></a>清單限制

應用程式清單具有多個稱為集合的屬性;例如，應用角色、金鑰憑據、已知用戶端應用程式、識別碼 Uris、重定向 Uris、所需的資源訪問和 oauth2 許可權。 在任何應用程式的完整應用程式清單中，所有集合中合併的條目總數上限為 1200。 如果以前在應用程式清單中指定 100 個重定向 URI，則僅剩 1100 個條目可用於構成清單的所有其他集合。

> [!NOTE]
> 如果您嘗試在應用程式清單中添加超過 1200 個條目，您可能會看到一個錯誤 **"無法更新應用程式 xxxxxx"。錯誤詳細資訊：清單的大小已超過其限制。請減少值數並重試您的請求。**

### <a name="unsupported-attributes"></a>不支援的屬性

應用程式清單表示 Azure AD 中基礎應用程式模型的架構。 隨著基礎架構的發展，清單編輯器將不時更新以反映新架構。 因此，您可能會注意到應用程式清單中顯示的新屬性。 在極少數情況下，您可能會注意到現有屬性中的語法或語義更改，或者您可能會發現以前存在的屬性不再受支援。 例如，您將在[應用註冊中](https://go.microsoft.com/fwlink/?linkid=2083908)看到新屬性，這些屬性在應用註冊（舊版）體驗中使用不同的名稱已知。

| 應用註冊（舊版）| 應用程式註冊           |
|---------------------------|-----------------------------|
| `availableToOtherTenants` | `signInAudience`            |
| `displayName`             | `name`                      |
| `errorUrl`                | -                           |
| `homepage`                | `signInUrl`                 |
| `objectId`                | `Id`                        |
| `publicClient`            | `allowPublicClient`         |
| `replyUrls`               | `replyUrlsWithType`         |

有關這些屬性的說明，請參閱[清單參考](#manifest-reference)部分。

當您嘗試上傳以前下載的清單時，您可能會看到以下錯誤之一。 此錯誤可能是因為清單編輯器現在支援較新版本的架構，該版本與您嘗試上載的架構不匹配。

* "無法更新 xxxxxx 應用程式。 錯誤詳細資訊：不正確物件識別碼"未定義"。 []."
* "無法更新 xxxxxx 應用程式。 錯誤詳細資訊：指定的一個或多個屬性值無效。 []."
* "無法更新 xxxxxx 應用程式。 錯誤詳細資訊：不允許在此 api 版本中為其他租戶設置可用以進行更新。 []."
* "無法更新 xxxxxx 應用程式。 錯誤詳細資訊：此應用程式不允許更新"回復 Urls"屬性。 改用"回復UrlsWithType"屬性。 []."
* "無法更新 xxxxxx 應用程式。 錯誤詳細資訊：找到沒有類型名稱的值，並且沒有可用的預期類型。 指定模型時，有效負載中的每個值都必須具有可在有效負載中指定的類型、由調用方顯式或從父值隱式推斷的類型。 []"

當您看到以下錯誤之一時，我們建議執行以下操作：

1. 在清單編輯器中單獨編輯屬性，而不是上載以前下載的清單。 使用[清單引用](#manifest-reference)表瞭解新舊屬性的語法和語義，以便成功編輯感興趣的屬性。 
1. 如果您的工作流要求您將清單保存在源存儲庫中以供以後使用，我們建議您使用**應用註冊**體驗中看到的清單重新設置存儲庫中保存的清單。

## <a name="next-steps"></a>後續步驟

* 有關應用的應用程式和服務主體物件之間的關係的詳細資訊，請參閱[Azure AD 中的應用程式和服務主體物件](app-objects-and-service-principals.md)。
* 有關一些核心 Microsoft 標識平臺開發人員概念的定義，請參閱[Microsoft 標識平臺開發人員術語表](developer-glossary.md)。

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
