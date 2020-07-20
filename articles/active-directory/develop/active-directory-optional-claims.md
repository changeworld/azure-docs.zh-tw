---
title: 提供選擇性宣告給 Azure AD 應用程式
titleSuffix: Microsoft identity platform
description: 將自訂或額外宣告新增至 Azure Active Directory 所簽發的 SAML 2.0 和 JSON Web Tokens (JWT) 權杖。
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 06/11/2020
ms.author: ryanwi
ms.reviewer: paulgarn, hirsin, keyam
ms.custom: aaddev
ms.openlocfilehash: f751c45b12ec2c8f6f09080b01b24f59af1fc0d0
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85478326"
---
# <a name="how-to-provide-optional-claims-to-your-azure-ad-app"></a>如何：提供選擇性宣告給 Azure AD 應用程式

應用程式開發人員可以在其 Azure AD 應用程式中使用選擇性宣告，以指定將哪些宣告放入傳送至應用程式的權杖中。

您可以使用選擇性宣告來：

- 選取要包含在應用程式之權杖中的額外宣告。
- 變更 Azure AD 在權杖中傳回之特定宣告的行為。
- 新增和存取應用程式的自訂宣告。

如需標準宣告的清單，請參閱[存取權杖](access-tokens.md)和 [id_token](id-tokens.md) 宣告文件。

雖然 v1.0 和 v2.0 格式權杖及 SAML 權杖都支援選擇性宣告，但只有在從 v1.0 移至 v2.0 時，這種宣告才最有價值。 [v2.0 Microsoft 身分識別平台端點](active-directory-appmodel-v2-overview.md)的目標之一是維持較小的權杖，以確保用戶端的最佳效能。 因此，數個先前包含在存取和識別碼權杖中的宣告在 v2.0 權杖中已不再提供，而必須依據個別應用程式明確提出要求才會提供。

**表 1：適用性**

| 帳戶類型               | v1.0 權杖 | v2.0 權杖 |
|----------------------------|-------------|-------------|
| 個人 Microsoft 帳戶 | N/A         | 支援   |
| Azure AD 帳戶           | 支援   | 支援   |

## <a name="v10-and-v20-optional-claims-set"></a>v1.0 和 V2.0 選擇性宣告集

以下列出預設可供應用程式使用的一組選擇性宣告。 若要為您的應用程式新增自訂選擇性宣告，請參閱下方的[目錄延伸模組](#configuring-directory-extension-optional-claims)。 請注意，將宣告新增至**存取權杖**時，宣告會套用至「為」應用程式 (Web API) 要求的存取權杖，而不是「由」應用程式要求的存取權杖。 無論用戶端如何存取您的 API，存取權杖中都有正確的資料，可用來向您的 API 驗證。

> [!NOTE]
> 這些宣告中大多數都可包含在 v1.0 和 v2.0 權杖的 JWT 中，但不可包含在 SAML 權杖中 (「權杖類型」欄中已註明者除外)。 取用者帳戶支援這些宣告的子集 (在「使用者類型」資料行中標示)。  列出的許多宣告不會套用至取用者使用者 (沒有租用戶，因此 `tenant_ctry` 沒有值)。

**表 2：v1.0 和 V2.0 選擇性宣告集**

| 名稱                       |  描述   | 權杖類型 | 使用者類型 | 注意  |
|----------------------------|----------------|------------|-----------|--------|
| `auth_time`                | 上次驗證使用者的時間。 請參閱 OpenID Connect 規格。| JWT        |           |  |
| `tenant_region_scope`      | 資源租用戶的區域 | JWT        |           | |
| `home_oid`                 | 若為來賓使用者，這是指使用者主租用戶中，使用者的物件識別碼。| JWT        |           | |
| `sid`                      | 工作階段識別碼，用於每個工作階段的使用者登出。 | JWT        |  個人和 Azure AD 帳戶。   |         |
| `platf`                    | 裝置平台    | JWT        |           | 限制為可驗證裝置類型的受控裝置。|
| `verified_primary_email`   | 源自使用者的 PrimaryAuthoritativeEmail      | JWT        |           |         |
| `verified_secondary_email` | 源自使用者的 SecondaryAuthoritativeEmail   | JWT        |           |        |
| `enfpolids`                | 強制執行的原則識別碼。 已針對目前使用者評估的原則識別碼清單。 | JWT |  |  |
| `vnet`                     | VNET 規範資訊。 | JWT        |           |      |
| `fwd`                      | IP 位址。| JWT    |   | 新增發出要求之用戶端的原始 IPv4 位址 (位於 VNET 內部時) |
| `ctry`                     | 使用者的國家/地區 | JWT |  | Azure AD 會傳回 `ctry` 選擇性宣告 (如果有的話)，宣告的值是標準雙字母的國家/地區代碼，例如 FR、JP、SZ 等等。 |
| `tenant_ctry`              | 資源租用戶的國家/地區 | JWT | | |
| `xms_pdl`             | 慣用資料位置   | JWT | | 若為多地理位置租用戶，慣用的資料位置是三個字母的代碼，顯示使用者所在的地理區域。 如需詳細資訊，請參閱 [Azure AD Connect 慣用資料位置的相關文件](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-feature-preferreddatalocation)。<br/>例如：`APC` 是指亞太地區。 |
| `xms_pl`                   | 使用者慣用語言  | JWT ||使用者的慣用語言 (如果已設定)。 在來賓存取案例中，來源是其主租用戶。 格式化 LL-CC ("en-us")。 |
| `xms_tpl`                  | 租用戶慣用語言| JWT | | 資源租用戶的慣用語言 (如果已設定)。 格式化 LL ("en")。 |
| `ztdid`                    | 全自動部署識別碼 | JWT | | 裝置身分識別，用於 [Windows AutoPilot](https://docs.microsoft.com/windows/deployment/windows-autopilot/windows-10-autopilot) |
| `email`                    | 此使用者可定址的電子郵件 (如果使用者有的話)。  | JWT、SAML | MSA、Azure AD | 如果使用者是租用戶中的來賓，則預設會包含此值。  若為受管理的使用者 (租用戶內的使用者)，則必須透過此選擇性宣告，或使用 OpenID 範圍 (僅限 v2.0) 來要求此值。  若為受管理的使用者，電子郵件地址必須設定於 [Office 管理入口網站](https://portal.office.com/adminportal/home#/users)。|
| `groups`| 群組宣告的選擇性格式化 |JWT、SAML| |與[應用程式資訊清單](reference-app-manifest.md)中的 GroupMembershipClaims 設定 (也必須設定) 搭配使用。 如需詳細資訊，請參閱下面的[群組宣告](#configuring-groups-optional-claims)。 如需群組宣告的詳細資訊，請參閱[如何設定群組宣告](../hybrid/how-to-connect-fed-group-claims.md)
| `acct`                | 租使用者中的使用者帳戶狀態 | JWT、SAML | | 如果使用者是租用戶的成員，則值為 `0`。 如果是來賓使用者，則值為 `1`。 |
| `upn`                      | UserPrincipalName | JWT、SAML  |           | 雖然會自動包含此宣告，但在來賓使用者案例中，您可以將它指定為選擇性宣告來附加額外屬性，以修改其行為。  |
| `idtyp`                    | Token 類型   | JWT 存取權杖 | 特殊：僅限僅限應用程式存取權杖 |  `app`當令牌為僅限應用程式權杖時，值為。 若要讓 API 判斷權杖是應用程式權杖或應用程式 + 使用者權杖，這是最精確的方式。|

## <a name="v20-specific-optional-claims-set"></a>v2.0 特有的選擇性宣告集

v1.0 Azure AD 權杖中一律包含這些宣告，但在 v2.0 權杖中，除非提出要求，否則不會包含。 這些宣告僅適用於 JWT (識別碼權杖和存取權杖)。

**表 3：僅限 v2.0 的選擇性宣告**

| JWT 宣告     | Name                            | 說明                                | 注意 |
|---------------|---------------------------------|-------------|-------|
| `ipaddr`      | IP 位址                      | 用戶端的登入來源 IP 位址。   |       |
| `onprem_sid`  | 內部部署安全性識別碼 |                                             |       |
| `pwd_exp`     | 密碼到期時間        | 密碼到時的日期時間。 |       |
| `pwd_url`     | 變更密碼 URL             | 使用者可以瀏覽來變更其密碼的 URL。   |   |
| `in_corp`     | 公司網路內部        | 指出用戶端是否是從公司網路登入的。 如果不是，則不包含此宣告。   |  根據 MFA 中的[可信任 IP](../authentication/howto-mfa-mfasettings.md#trusted-ips) 設定。    |
| `family_name` | 姓氏                       | 提供使用者物件中定義的使用者姓氏。 <br>"family_name":"Miller" | MSA 和 Azure AD 支援。 需要 `profile` 範圍。   |
| `given_name`  | 名字                      | 提供使用者物件上設定的使用者名字。<br>"given_name"："Frank"                   | MSA 和 Azure AD 支援。  需要 `profile` 範圍。 |
| `upn`         | 使用者主體名稱 | 可與 username_hint 參數搭配使用的使用者識別碼。  不是使用者的持久識別碼，且不應該用於金鑰資料。 | 如需了解宣告的設定，請參閱下方的[額外屬性](#additional-properties-of-optional-claims)。 需要 `profile` 範圍。|

### <a name="additional-properties-of-optional-claims"></a>選擇性宣告的額外屬性

有些選擇性宣告可經由設定來變更傳回宣告的方式。 這些額外的屬性大多數用來協助移轉對資料有不同要求的內部部署應用程式 (例如 `include_externally_authenticated_upn_without_hash` 可協助無法處理 UPN 中井號 (`#`) 的用戶端)

**表 4：用來設定選擇性宣告的值**

| 屬性名稱  | 額外屬性名稱 | 描述 |
|----------------|--------------------------|-------------|
| `upn`          |                          | 可同時用於 SAML 和 JWT 回應，以及用於 v1.0 和 v2.0 權杖。 |
|                | `include_externally_authenticated_upn`  | 包含儲存在資源租用戶中的來賓 UPN。 例如， `foo_hometenant.com#EXT#@resourcetenant.com` |
|                | `include_externally_authenticated_upn_without_hash` | 同上，只不過井字號 (`#`) 換成底線 (`_`)，例如 `foo_hometenant.com_EXT_@resourcetenant.com` |

#### <a name="additional-properties-example"></a>額外屬性範例

```json
"optionalClaims": {
    "idToken": [
        {
            "name": "upn",
            "essential": false,
            "additionalProperties": [
                "include_externally_authenticated_upn"
            ]
        }
    ]
}
```

這個 OptionalClaims 物件會導致傳回給用戶端的識別碼權杖包含 upn 宣告，還附上額外的主租用戶和資源租用戶資訊。 只有當使用者是租用戶中的來賓時 (使用不同的 IDP 進行驗證)，才會變更權杖中的 `upn` 宣告。

## <a name="configuring-optional-claims"></a>設定選擇性宣告

> [!IMPORTANT]
> 系統**一律**使用資源 (而非用戶端) 的資訊清單來產生存取權杖。  所以，在 `...scope=https://graph.microsoft.com/user.read...` 要求中，資源是 Microsoft Graph API。  因此，系統會使用 Microsoft Graph API 資訊清單 (而不是用戶端的資訊清單) 來建立存取權杖。  即使變更應用程式的資訊清單，也絕不會導致 Microsoft Graph API 的權杖有所變化。  若要驗證 `accessToken` 變更是否生效，請為您的應用程式 (而不是另一個應用程式) 要求權杖。

針對您的應用程式，您可以透過 UI 或應用程式資訊清單來設定選擇性宣告。

1. 移至 [Azure 入口網站](https://portal.azure.com)。 搜尋並選取 [Azure Active Directory]。
1. 從 [管理] 區段中，選取 [應用程式註冊]。
1. 從清單中，選取您要設定選擇性宣告的應用程式。

**透過 UI 設定選擇性宣告：**

[![示範如何使用 UI 設定選擇性宣告](./media/active-directory-optional-claims/token-configuration.png)](./media/active-directory-optional-claims/token-configuration.png)

1. 從 [管理] 區段中，選取 [權杖設定]。
1. 選取 [新增選擇性宣告]。
1. 選取您要設定的權杖類型。
1. 選取要新增的選擇性宣告。
1. 選取 [新增]。

**透過應用程式資訊清單設定選擇性宣告：**

[![示範如何使用應用程式資訊清單設定選擇性宣告](./media/active-directory-optional-claims/app-manifest.png)](./media/active-directory-optional-claims/app-manifest.png)

1. 從 [管理] 區段中，選取 [資訊清單]。 網頁式資訊清單編輯器隨即開啟，讓您編輯資訊清單。 或者，您也可以選取 [下載] 並在本機編輯資訊清單，然後使用 [上傳] 以將其重新套用到您的應用程式。 如需應用程式資訊清單的詳細資訊，請參閱[了解 Azure AD 應用程式資訊清單](reference-app-manifest.md)一文。

    下列應用程式資訊清單項目將 auth_time、ipaddr 和 upn 選擇性宣告，新增至識別碼權杖、存取權杖和 SAML 權杖。

    ```json
    "optionalClaims": {
        "idToken": [
            {
                "name": "auth_time",
                "essential": false
            }
        ],
        "accessToken": [
            {
                "name": "ipaddr",
                "essential": false
            }
        ],
        "saml2Token": [
            {
                "name": "upn",
                "essential": false
            },
            {
                "name": "extension_ab603c56068041afb2f6832e2a17e237_skypeId",
                "source": "user",
                "essential": false
            }
        ]
    }
    ```

2. 完成後，選取 [儲存]。 現在，應用程式的權杖已包含指定的選擇性宣告。

### <a name="optionalclaims-type"></a>OptionalClaims 類型

宣告應用程式所要求的選擇性宣告。 應用程式可以針對它可從安全性權杖服務接收的三種權杖 (識別碼權杖、存取權杖、SAML 2 權杖)，設定要在這每一種權杖中傳回的選擇性宣告。 應用程式可以設定一組要在每個權杖類型中傳回的不同選擇性宣告。 「應用程式」實體的 OptionalClaims 屬性是一個 OptionalClaims 物件。

**表 5：OptionalClaims 類型屬性**

| 名稱          | 類型                       | Description                                           |
|---------------|----------------------------|-------------------------------------------------------|
| `idToken`     | 集合 (OptionalClaim) | 在 JWT 識別碼權杖中傳回的選擇性宣告。     |
| `accessToken` | 集合 (OptionalClaim) | 在 JWT 存取權杖中傳回的選擇性宣告。 |
| `saml2Token`  | 集合 (OptionalClaim) | 在 SAML 權杖中傳回的選擇性宣告。       |

### <a name="optionalclaim-type"></a>OptionalClaim 類型

包含與應用程式或服務主體關聯的選擇性宣告。 [OptionalClaims](https://docs.microsoft.com/graph/api/resources/optionalclaims?view=graph-rest-1.0) 類型的 idToken、accessToken 及 saml2Token 屬性是 OptionalClaim 的集合。
如果特定宣告可支援，您也可以使用 AdditionalProperties 欄位來修改 OptionalClaim 的行為。

**表 6：OptionalClaim 類型屬性**

| 名稱                   | 類型                    | 描述                                                                                                                                                                                                                                                                                                   |
|------------------------|-------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `name`                 | Edm.String              | 選擇性宣告的名稱。                                                                                                                                                                                                                                                                               |
| `source`               | Edm.String              | 宣告的來源 (目錄物件)。 有來自延伸模組屬性的預先定義宣告和使用者定義宣告。 如果來源值為 null，宣告便是預先定義的選擇性宣告。 如果來源值為 user，名稱屬性中的值即為來自使用者物件的延伸模組屬性。 |
| `essential`            | Edm.Boolean             | 如果值為 true，就必須要有用戶端指定的宣告，才能確保使用者所要求之特定工作的授權體驗順暢。 預設值為 false。                                                                                                                 |
| `additionalProperties` | 集合 (Edm.String) | 宣告的額外屬性。 如果屬性存在於此集合中，它就會修改名稱屬性中所指定之選擇性宣告的行為。                                                                                                                                                   |

## <a name="configuring-directory-extension-optional-claims"></a>設定目錄擴充的選擇性宣告

除了標準的選擇性宣告集之外，您也可以設定權杖來包含擴充。 如需詳細資訊，請參閱 [Microsoft Graph extensionProperty 文件](https://docs.microsoft.com/graph/api/resources/extensionproperty?view=graph-rest-1.0)。

選擇性宣告不支援結構描述和開啟式擴充，僅支援 AAD-Graph 樣式的目錄擴充。 此功能可用來附加應用程式可使用的額外使用者資訊 – 例如，使用者已設定的額外識別碼或重要設定選項。 如需範例，請參閱本頁底部。

> [!NOTE]
> 目錄結構描述擴充是僅限 Azure AD 的功能。 如果您的應用程式資訊清單要求自訂擴充，但有 MSA 使用者登入您的應用程式，則不會傳回這些擴充。

### <a name="directory-extension-formatting"></a>目錄擴充格式

使用應用程式資訊清單來設定目錄擴充的選擇性宣告時，請使用擴充的完整名稱 (格式為：`extension_<appid>_<attributename>`)。 `<appid>` 必須符合宣告要求端應用程式的識別碼。

在 JWT 內，這些宣告將會以下列名稱格式發出：`extn.<attributename>`。

在 SAML 權杖內，這些宣告則會以下列 URI 格式發出：`http://schemas.microsoft.com/identity/claims/extn.<attributename>`

## <a name="configuring-groups-optional-claims"></a>設定群組選擇性宣告

   > [!NOTE]
   > 針對從內部部署同步的使用者和群組，發出群組名稱的功能已公開預覽。

本節涵蓋選擇性宣告下的設定選項，可將來自預設群組 objectID 的群組宣告所使用的群組屬性，變更為從內部部署 Windows Active Directory 同步的屬性。 針對您的應用程式，您可以透過 UI 或應用程式資訊清單來設定群組選擇性宣告。

> [!IMPORTANT]
> 如需詳細資料，包括內部部署屬性中公開預覽群組宣告的重要注意事項，請參閱[使用 Azure AD 設定應用程式的群組宣告](../hybrid/how-to-connect-fed-group-claims.md)。

**透過 UI 設定群組選擇性宣告：**

1. 登入 [Azure 入口網站](https://portal.azure.com)
1. 通過驗證後，請從頁面右上角選取您的 Azure AD 租用戶
1. 從左側功能表中，選取 [Azure Active Directory]
1. 在 [管理] 區段下，選取 [應用程式註冊]
1. 從清單中，選取您要設定選擇性宣告的應用程式
1. 在 [管理] 區段下，選取 [權杖設定]
1. 選取 [新增群組宣告]
1. 選取要傳回的群組類型 (**所有群組**、**SecurityGroup** 或 **DirectoryRole**)。 [所有群組] 選項包含 **SecurityGroup**、**DirectoryRole** 和 **DistributionList**
1. 選擇性：選取特定的權杖類型屬性，以將群組宣告值修改為包含內部部署群組屬性，或將宣告類型變更為角色
1. 選取 [儲存]。

**透過應用程式資訊清單設定群組選擇性宣告：**

1. 登入 [Azure 入口網站](https://portal.azure.com)
1. 通過驗證後，請從頁面右上角選取您的 Azure AD 租用戶
1. 從左側功能表中，選取 [Azure Active Directory]
1. 從清單中，選取您要設定選擇性宣告的應用程式
1. 在 [管理] 區段下，選取 [資訊清單]
1. 使用資訊清單編輯器新增下列項目：

   有效值為：

   - "All" (此選項包含 SecurityGroup、DirectoryRole 和 DistributionList)
   - "SecurityGroup"
   - "DirectoryRole"

   例如：

    ```json
    "groupMembershipClaims": "SecurityGroup"
    ```

   預設會在群組宣告值中發出群組 ObjectID。  若要將宣告值修改為包含內部部署群組屬性，或將宣告類型變更為角色，請使用 OptionalClaims 設定，如下所示：

1. 設定群組名稱設定的選擇性宣告。

   如果希望權杖中的群組在選擇性宣告區段中包含內部部署 AD 群組屬性，請指定應該套用選擇性宣告的權杖類型、所要求選擇性宣告的名稱，以及想要的任何額外屬性。  可以列出多個權杖類型：

   - idToken，代表 OIDC 識別碼權杖
   - accessToken，代表 OAuth 存取權杖
   - Saml2Token，代表 SAML 權杖。

   > [!NOTE]
   > Saml2Token 類型適用於 SAML1.1 和 SAML2.0 格式的權杖

   針對每個相關的權杖類型，將群組宣告修改為使用資訊清單中的 OptionalClaims 區段。 OptionalClaims 結構描述如下所示：

    ```json
    {
        "name": "groups",
        "source": null,
        "essential": false,
        "additionalProperties": []
    }
    ```

   | 選擇性宣告範例 | 值 |
   |----------|-------------|
   | **name:** | 必須是 "groups" |
   | **source:** | 未使用。 省略或指定 null |
   | **essential:** | 未使用。 省略或指定 false |
   | **additionalProperties:** | 額外屬性的清單。  有效的選項包括 "sam_account_name"、"dns_domain_and_sam_account_name"、"netbios_domain_and_sam_account_name"、"emit_as_roles" |

   在 additionalProperties 中，只需要 "sam_account_name"、"dns_domain_and_sam_account_name"、"netbios_domain_and_sam_account_name" 其中一個。  如果出現多個，則會使用第一個，其他都忽略。

   某些應用程式在角色宣告中需要使用者的群組資訊。  若要將宣告類型從群組宣告變更為角色宣告，請將 "emit_as_roles" 新增至額外屬性。  角色宣告中會發出群組值。

   > [!NOTE]
   > 如果使用 "emit_as_roles"，則任何已設定指派給使用者的應用程式角色，將不會出現在角色宣告中。

**範例：**

1) 在 OAuth 存取權杖中以 dnsDomainName\sAMAccountName 格式發出群組作為群組名稱

    **UI 設定：**

    [![示範如何使用 UI 設定選擇性宣告](./media/active-directory-optional-claims/groups-example-1.png)](./media/active-directory-optional-claims/groups-example-1.png)

    **應用程式資訊清單項目：**

    ```json
    "optionalClaims": {
        "accessToken": [
            {
                "name": "groups",
                "additionalProperties": [
                    "dns_domain_and_sam_account_name"
                ]
            }
        ]
    }
    ```

2) 在 SAML 和 OIDC 識別碼權杖中發出以 netbiosDomain\sAMAccountName 格式傳回的群組名稱作為角色宣告

    **UI 設定：**

    [![示範如何使用 UI 設定選擇性宣告](./media/active-directory-optional-claims/groups-example-2.png)](./media/active-directory-optional-claims/groups-example-2.png)

    **應用程式資訊清單項目：**

    ```json
    "optionalClaims": {
        "saml2Token": [
            {
                "name": "groups",
                "additionalProperties": [
                    "netbios_name_and_sam_account_name",
                    "emit_as_roles"
                ]
            }
        ],
        "idToken": [
            {
                "name": "groups",
                "additionalProperties": [
                    "netbios_name_and_sam_account_name",
                    "emit_as_roles"
                ]
            }
        ]
    }
    ```

## <a name="optional-claims-example"></a>選擇性宣告範例

在本節中，您可以逐步進行案例，以了解如何針對應用程式使用選擇性宣告功能。
有多個選項可讓您更新應用程式身分識別設定上的屬性，以啟用和設定選擇性宣告：

- 您可以使用 [權杖設定] UI (請參閱下列範例)
- 您可以使用 [資訊清單] (請參閱下列範例)。 請先閱讀介紹資訊清單的[了解 Azure AD 應用程式資訊清單](https://docs.microsoft.com/azure/active-directory/develop/active-directory-application-manifest)文件。
- 您也可以撰寫應用程式，以使用 [Microsoft Graph API](https://docs.microsoft.com/graph/use-the-api?context=graph%2Fapi%2F1.0&view=graph-rest-1.0) 來更新您的應用程式。 Microsoft Graph API 參考指南中的 [OptionalClaims](https://docs.microsoft.com/graph/api/resources/optionalclaims?view=graph-rest-1.0) 類型，可協助您設定選擇性宣告。

**範例︰**

在下列範例中，您將使用 [權杖設定] UI 和 [資訊清單]，將選擇性宣告新增至應用程式的存取權杖、識別碼權杖和 SAML 權杖。 不同的選擇性宣告會新增至應用程式可接收的每一種權杖：

- 識別碼權杖現在將會在完整格式 (`<upn>_<homedomain>#EXT#@<resourcedomain>`) 中包含同盟使用者的 UPN。
- 其他用戶端為這個應用程式要求的存取權杖，現在將包含 auth_time 宣告
- SAML 權杖現在將會包含 skypeId 目錄結構描述延伸模組 (在此範例中，此應用程式的應用程式識別碼是 ab603c56068041afb2f6832e2a17e237)。 SAML 權杖會將 Skype 識別碼公開為 `extension_skypeId`。

**UI 設定：**

1. 登入 [Azure 入口網站](https://portal.azure.com)

1. 通過驗證後，請從頁面右上角選取您的 Azure AD 租用戶。

1. 從左側功能表中，選取 [Azure Active Directory]。

1. 在 [管理] 區段下，選取 [應用程式註冊]。

1. 從清單中，找出並選取您要設定選擇性宣告的應用程式。

1. 在 [管理] 區段下，選取 [權杖設定]。

1. 選取 [新增選擇性宣告]、選取 [識別碼] 權杖類型、從宣告清單中選取 [upn]，然後選取 [新增]。

1. 選取 [新增選擇性宣告]、選取 [存取] 權杖類型、從宣告清單中選取 [auth_time]，然後選取 [新增]。

1. 從 [權杖設定] 概觀畫面中，選取 [upn] 旁的鉛筆圖示，選取 [外部驗證] 切換，然後選取 [儲存]。

1. 選取 [新增選擇性宣告]、選取 [SAML] 權杖類型、從宣告清單中選取 [extn.skypeID] (只在已建立名為 skypeID 的 Azure AD 使用者物件時才適用)，然後選取 [新增]。

    [![示範如何使用 UI 設定選擇性宣告](./media/active-directory-optional-claims/token-config-example.png)](./media/active-directory-optional-claims/token-config-example.png)

**資訊清單設定：**

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 通過驗證後，請從頁面右上角選取您的 Azure AD 租用戶。
1. 從左側功能表中，選取 [Azure Active Directory]。
1. 從清單中，找出並選取您要設定選擇性宣告的應用程式。
1. 在 [管理] 區段下，選取 [資訊清單]，以開啟內嵌資訊清單編輯器。
1. 您可以使用此編輯器直接編輯資訊清單。 資訊清單遵循 [Application 實體](https://docs.microsoft.com/azure/active-directory/develop/reference-app-manifest)的結構描述，而且資訊清單儲存時會自動格式化。 新元素會新增至 `OptionalClaims` 屬性。

    ```json
    "optionalClaims": {
        "idToken": [
            {
                "name": "upn",
                "essential": false,
                "additionalProperties": [
                    "include_externally_authenticated_upn"
                ]
            }
        ],
        "accessToken": [
            {
                "name": "auth_time",
                "essential": false
            }
        ],
        "saml2Token": [
            {
                "name": "extension_ab603c56068041afb2f6832e2a17e237_skypeId",
                "source": "user",
                "essential": true
            }
        ]
    }
    ```

1. 資訊清單更新完成後，請選取 [儲存] 以儲存資訊清單。

## <a name="next-steps"></a>後續步驟

深入了解 Azure AD 所提供的標準宣告。

- [識別碼權杖](id-tokens.md)
- [存取權杖](access-tokens.md)
