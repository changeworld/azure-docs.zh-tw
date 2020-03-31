---
title: 向 Azure AD 應用提供可選聲明 |蔚藍
titleSuffix: Microsoft identity platform
description: 如何向 Azure 活動目錄頒發的 SAML 2.0 和 JSON Web 權杖 （JWT） 權杖添加自訂或其他聲明。
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 3/11/2020
ms.author: ryanwi
ms.reviewer: paulgarn, hirsin, keyam
ms.custom: aaddev
ms.openlocfilehash: 23d83b59c510f2565b2f66f78dad56c9c9592dd0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79136512"
---
# <a name="how-to-provide-optional-claims-to-your-azure-ad-app"></a>如何：向 Azure AD 應用提供可選聲明

應用程式開發人員可以在 Azure AD 應用程式中使用可選聲明來指定在發送到其應用程式的權杖中需要哪些聲明。 

您可以使用選擇性宣告來：

- 選取要包含在應用程式之權杖中的額外宣告。
- 變更 Azure AD 在權杖中傳回之特定宣告的行為。
- 新增和存取應用程式的自訂宣告。

有關標準聲明的清單，請參閱[訪問權杖](access-tokens.md)和[id_token](id-tokens.md)聲明文檔。 

雖然 v1.0 和 v2.0 格式權杖以及 SAML 權杖都支援可選聲明，但它們在從 v1.0 移動到 v2.0 時提供了大部分值。 [v2.0 Microsoft 標識平臺終結點](active-directory-appmodel-v2-overview.md)的目標之一是減小權杖大小，以確保用戶端獲得最佳性能。 因此，數個先前包含在存取和識別碼權杖中的宣告在 v2.0 權杖中已不再提供，而必須依據個別應用程式明確提出要求才會提供。

**表 1：適用性**

| 帳戶類型 | v1.0 權杖 | v2.0 權杖  |
|--------------|---------------|----------------|
| 個人 Microsoft 帳戶  | N/A  | 支援 |
| Azure AD 帳戶      | 支援 | 支援 |

## <a name="v10-and-v20-optional-claims-set"></a>v1.0 和 v2.0 可選聲明集

以下列出預設可供應用程式使用的一組選擇性宣告。 若要為您的應用程式新增自訂選擇性宣告，請參閱下方的[目錄延伸模組](#configuring-directory-extension-optional-claims)。 向**訪問權杖**添加聲明時，這些聲明適用于*為*應用程式（Web API）請求的訪問權杖，而不是應用程式*請求*的聲明。 無論用戶端如何訪問 API，用於針對 API 進行身份驗證的訪問權杖中都存在正確的資料。

> [!NOTE]
> 這些宣告中大多數都可包含在 v1.0 和 v2.0 權杖的 JWT 中，但不可包含在 SAML 權杖中 (「權杖類型」欄中已註明者除外)。 消費者帳戶支援這些聲明的子集，在"使用者類型"列中標記。  列出的許多聲明不適用於消費者使用者（它們沒有租戶，因此`tenant_ctry`沒有價值）。  

**表 2：v1.0 和 v2.0 可選聲明集**

| 名稱                       |  描述   | 權杖類型 | 使用者類型 | 注意  |
|----------------------------|----------------|------------|-----------|--------|
| `auth_time`                | 上次驗證使用者的時間。 請參閱 OpenID Connect 規格。| JWT        |           |  |
| `tenant_region_scope`      | 資源租用戶的區域 | JWT        |           | |
| `home_oid`                 | 就來賓使用者而言，是使用者主租用戶中的使用者物件識別碼。| JWT        |           | |
| `sid`                      | 會話 ID，用於每個會話使用者登出。 | JWT        |  個人帳戶和 Azure AD 帳戶。   |         |
| `platf`                    | 裝置平台    | JWT        |           | 限制為可驗證裝置類型的受控裝置。|
| `verified_primary_email`   | 以使用者的 PrimaryAuthoritativeEmail 為來源      | JWT        |           |         |
| `verified_secondary_email` | 以使用者的 SecondaryAuthoritativeEmail 為來源   | JWT        |           |        |
| `enfpolids`                | 強制執行的原則識別碼。 已針對目前使用者評估的原則識別碼清單。 | JWT |  |  |
| `vnet`                     | VNET 規範資訊。 | JWT        |           |      |
| `fwd`                      | IP 位址。| JWT    |   | 新增發出要求之用戶端的原始 IPv4 位址 (位於 VNET 內部時) |
| `ctry`                     | 使用者的國家/地區 | JWT |  | 如果出現且宣告值是兩個字母的國家/地區碼 (例如 FR、JP、SZ 等等)，則 Azure AD 會傳回 `ctry` 選擇性宣告。 |
| `tenant_ctry`              | 資源租用戶的國家/地區 | JWT | | |
| `xms_pdl`          | 慣用資料位置   | JWT | | 對於多地理位置租戶，首選的資料位置是顯示使用者所在地理區域的三個字母代碼。 有關詳細資訊，請參閱[有關首選資料位置的 Azure AD 連接文檔](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-feature-preferreddatalocation)。<br/>例如：`APC` 是指亞太地區。 |
| `xms_pl`                   | 使用者慣用語言  | JWT ||如果設定，則為使用者的慣用語言。 在來賓存取案例中，來源是其主租用戶。 格式化 LL-CC (“en-us”)。 |
| `xms_tpl`                  | 租用戶慣用語言| JWT | | 如果設定，則為資源租用戶的慣用語言。 格式化 LL (“en”)。 |
| `ztdid`                    | 全自動部署識別碼 | JWT | | 裝置身分識別，用於 [Windows AutoPilot](https://docs.microsoft.com/windows/deployment/windows-autopilot/windows-10-autopilot) |
| `email`                    | 此使用者可定址的電子郵件 (如果使用者有的話)。  | JWT、SAML | MSA， Azure AD | 如果使用者是租用戶中的來賓，則預設會包含此值。  對於託管使用者（租戶內的使用者），必須通過此可選聲明或僅在 v2.0 上使用 OpenID 作用域請求它。  若為受管理的使用者，電子郵件地址必須設定於 [Office 管理入口網站](https://portal.office.com/adminportal/home#/users)。| 
| `groups`| 組聲明的可選格式 |JWT、SAML| |與[應用程式清單](reference-app-manifest.md)中的組成員聲明設置結合使用，該設置也必須設置。 有關詳細資訊，請參閱下面的[組聲明](#configuring-groups-optional-claims)。 有關組聲明的詳細資訊，請參閱[如何配置組聲明](../hybrid/how-to-connect-fed-group-claims.md)
| `acct`             | 租用戶中的使用者帳戶狀態。 | JWT、SAML | | 如果使用者是租用戶的成員，則值為 `0`。 如果是來賓使用者，則值為 `1`。 |
| `upn`                      | UserPrincipalName 宣告。 | JWT、SAML  |           | 雖然會自動包含此宣告，但在來賓使用者案例中，您可以將它指定為選擇性宣告來附加額外屬性，以修改其行為。  |

## <a name="v20-specific-optional-claims-set"></a>v2.0 特定的可選聲明集

這些聲明始終包含在 v1.0 Azure AD 權杖中，但不包括在 v2.0 權杖中，除非請求。 這些聲明僅適用于 JWT（ID 權杖和訪問權杖）。 

**表 3：僅限 v2.0 的可選聲明**

| JWT 宣告     | 名稱                            | 描述                                | 注意 |
|---------------|---------------------------------|-------------|-------|
| `ipaddr`      | IP 位址                      | 用戶端的登入來源 IP 位址。   |       |
| `onprem_sid`  | 內部部署安全性識別碼 |                                             |       |
| `pwd_exp`     | 密碼到期時間        | 密碼到時的日期時間。 |       |
| `pwd_url`     | 變更密碼 URL             | 使用者可以瀏覽來變更其密碼的 URL。   |   |
| `in_corp`     | 公司網路內部        | 指出用戶端是否是從公司網路登入的。 如果不是，則不包括索賠。   |  根據 MFA 中的[可信任 IP](../authentication/howto-mfa-mfasettings.md#trusted-ips) 設定。    |
| `nickname`    | 暱稱                        | 使用者的其他名稱。 昵稱與名字或姓氏分開。 需要`profile`作用域。| 
| `family_name` | 姓氏                       | 提供使用者物件中定義的使用者的姓氏、姓氏或姓氏。 <br>"family_name":"Miller" | MSA 和 Azure AD 中支援。 需要`profile`作用域。   |
| `given_name`  | 名字                      | 提供使用者物件上設置的使用者的第一個或"給定"名稱。<br>"given_name": "Frank"                   | MSA 和 Azure AD 中受支援。  需要`profile`作用域。 |
| `upn`         | 使用者主體名稱 | 可與 username_hint 參數搭配使用的使用者識別碼。  不是使用者的持久識別碼，且不應該用於金鑰資料。 | 如需了解宣告的設定，請參閱下方的[額外屬性](#additional-properties-of-optional-claims)。 需要`profile`作用域。|

### <a name="additional-properties-of-optional-claims"></a>選擇性宣告的額外屬性

有些選擇性宣告可經由設定來變更傳回宣告的方式。 這些額外的屬性大多數用來協助移轉對資料有不同要求的內部部署應用程式 (例如 `include_externally_authenticated_upn_without_hash` 可協助無法處理 UPN 中井號 (`#`) 的用戶端)

**表 4：用於配置可選聲明的值**

| 屬性名稱  | 額外屬性名稱 | 描述 |
|----------------|--------------------------|-------------|
| `upn`          |                          | 可同時用於 SAML 和 JWT 回應，以及用於 v1.0 和 v2.0 權杖。 |
|                | `include_externally_authenticated_upn`  | 包含儲存在資源租用戶中的來賓 UPN。 例如， `foo_hometenant.com#EXT#@resourcetenant.com` |             
|                | `include_externally_authenticated_upn_without_hash` | 與上述相同，但雜湊標記 （`#`） 替換為底線 （`_`） 除外。`foo_hometenant.com_EXT_@resourcetenant.com` |

#### <a name="additional-properties-example"></a>額外屬性範例

    ```json
        "optionalClaims": 
         {
             "idToken": [ 
            { 
                      "name": "upn", 
                      "essential": false,
                  "additionalProperties": [ "include_externally_authenticated_upn"]  
                    }
                 ]
        }
    ```

此可選聲明物件會導致返回到用戶端的 ID 權杖包含包含具有附加主租戶和資源租戶資訊的 upn 聲明。 僅當`upn`使用者是租戶中的來賓（使用其他 IDP 進行身份驗證）時，才會在權杖中更改聲明。 

## <a name="configuring-optional-claims"></a>設定選擇性宣告

> [!IMPORTANT]
> 訪問權杖**始終**使用資源清單而不是用戶端生成。  因此，在請求`...scope=https://graph.microsoft.com/user.read...`中，資源是 Microsoft 圖形 API。  因此，訪問權杖是使用 Microsoft 圖形 API 清單創建的，而不是用戶端的清單。  更改應用程式的清單絕不會導致 Microsoft 圖形 API 的權杖看起來不同。  為了驗證所做的`accessToken`更改是否有效，請為應用程式請求權杖，而不是為其他應用請求權杖。  


您可以通過 UI 或應用程式清單為應用程式佈建可選聲明。

1. 轉到[Azure 門戶](https://portal.azure.com)。 搜尋並選取 [Azure Active Directory]****。
1. 在 **"管理"** 部分中，選擇**應用註冊**。
1. 選擇要在清單中為其配置可選聲明的應用程式。

**通過 UI 配置可選聲明：**

[![演示如何使用 UI 配置可選聲明](./media/active-directory-optional-claims/token-configuration.png)](./media/active-directory-optional-claims/token-configuration.png)

1. 在 **"管理**"部分中，選擇**權杖配置（預覽）。**
2. 選擇 **"添加可選聲明**"。
3. 選擇要配置的權杖類型。
4. 選擇要添加的可選聲明。
5. 按一下 **[新增]**。

**通過應用程式清單配置可選聲明：**

[![演示如何使用應用清單配置可選聲明](./media/active-directory-optional-claims/app-manifest.png)](./media/active-directory-optional-claims/app-manifest.png)

1. 在 **"管理**"部分中，選擇 **"清單**"。 將打開基於 Web 的清單編輯器，允許您編輯清單。 或者，您也可以選取 [下載]**** 並在本機編輯資訊清單，然後使用 [上傳]**** 以將其重新套用到您的應用程式。 有關應用程式清單的詳細資訊，請參閱瞭解 Azure [AD 應用程式清單一文](reference-app-manifest.md)。

    以下應用程式清單條目將auth_time、ipaddr 和 upn 可選聲明添加到 ID、訪問和 SAML 權杖。

    ```json
        "optionalClaims":  
           {
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

2. 完成時，按一下 [儲存]****。 現在，指定的可選聲明將包含在應用程式的權杖中。    


### <a name="optionalclaims-type"></a>可選索賠類型

宣告應用程式所要求的選擇性宣告。 應用程式可以針對它可從安全性權杖服務接收的三種權杖 (識別碼權杖、存取權杖、SAML 2 權杖)，設定要在這每一種權杖中傳回的選擇性宣告。 應用程式可以設定一組要在每個權杖類型中傳回的不同選擇性宣告。 「應用程式」實體的 OptionalClaims 屬性是一個 OptionalClaims 物件。

**表 5：可選索賠類型屬性**

| 名稱        | 類型                       | 描述                                           |
|-------------|----------------------------|-------------------------------------------------------|
| `idToken`     | 集合 (OptionalClaim) | 在 JWT 識別碼權杖中傳回的選擇性宣告。 |
| `accessToken` | 集合 (OptionalClaim) | 在 JWT 存取權杖中傳回的選擇性宣告。 |
| `saml2Token`  | 集合 (OptionalClaim) | 在 SAML 權杖中傳回的選擇性宣告。   |

### <a name="optionalclaim-type"></a>可選索賠類型

包含與應用程式或服務主體關聯的選擇性宣告。 [OptionalClaims](https://docs.microsoft.com/graph/api/resources/optionalclaims?view=graph-rest-1.0) 類型的 idToken、accessToken 及 saml2Token 屬性是 OptionalClaim 的集合。
如果特定宣告可支援，您也可以使用 AdditionalProperties 欄位來修改 OptionalClaim 的行為。

**表 6：可選索賠類型屬性**

| 名稱                 | 類型                    | 描述                                                                                                                                                                                                                                                                                                   |
|----------------------|-------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `name`                 | Edm.String              | 選擇性宣告的名稱。                                                                                                                                                                                                                                                                           |
| `source`               | Edm.String              | 宣告的來源 (目錄物件)。 有來自延伸模組屬性的預先定義宣告和使用者定義宣告。 如果來源值為 null，宣告便是預先定義的選擇性宣告。 如果來源值為 user，名稱屬性中的值即為來自使用者物件的延伸模組屬性。 |
| `essential`            | Edm.Boolean             | 如果值為 true，就必須要有用戶端指定的宣告，才能確保使用者所要求之特定工作的授權體驗順暢。 預設值為 false。                                                                                                             |
| `additionalProperties` | 集合 (Edm.String) | 宣告的額外屬性。 如果屬性存在於此集合中，它就會修改名稱屬性中所指定之選擇性宣告的行為。                                                                                                                                               |
## <a name="configuring-directory-extension-optional-claims"></a>配置目錄擴展可選聲明

除了標準可選聲明集外，您還可以將權杖配置為包含擴展。 有關詳細資訊，請參閱[Microsoft Graph 擴充屬性文檔](https://docs.microsoft.com/graph/api/resources/extensionproperty?view=graph-rest-1.0)- 請注意，可選聲明不支援架構和打開擴展，僅支援 AAD-Graph 樣式目錄擴展。 此功能可用來附加應用程式可使用的額外使用者資訊 – 例如，使用者已設定的額外識別碼或重要設定選項。 有關示例，請參閱此頁面底部。

> [!NOTE]
> - 目錄架構擴展是僅 Azure AD 的功能，因此，如果應用程式清單請求自訂擴展，並且 MSA 使用者登錄到應用，則不會返回這些擴展。

### <a name="directory-extension-formatting"></a>目錄擴展格式

使用應用程式清單配置目錄擴展可選聲明時，請使用擴展的全名（格式： `extension_<appid>_<attributename>`）。 必須`<appid>`與請求聲明的應用程式的 ID 匹配。 

在 JWT 內，這些宣告將會以下列名稱格式發出：`extn.<attributename>`。

在 SAML 權杖內，這些宣告則會以下列 URI 格式發出：`http://schemas.microsoft.com/identity/claims/extn.<attributename>`

## <a name="configuring-groups-optional-claims"></a>配置組可選聲明

   > [!NOTE]
   > 為從本地同步的使用者和組發出組名稱的能力是"公共預覽"。

本節介紹從預設組物件識別碼 更改為從本地 Windows 活動目錄同步的屬性的可選聲明下的配置選項，用於將組聲明中使用的組屬性從預設組物件識別碼 更改為屬性。 您可以通過 UI 或應用程式清單為應用程式佈建組可選聲明。

> [!IMPORTANT]
> 有關更多詳細資訊，包括從本地屬性進行組聲明的公共預覽的重要注意事項，請參閱[為具有 Azure AD 的應用程式佈建組聲明](../hybrid/how-to-connect-fed-group-claims.md)。

**通過 UI 配置組可選聲明：**
1. 登錄到 Azure[門戶](https://portal.azure.com)
1. 經過身份驗證後，請從頁面右上角選擇 Azure AD 租戶
1. 從左側功能表中選擇**Azure 活動目錄**
1. 在 **"管理"** 部分下，選擇**應用註冊**
1. 選擇要在清單中配置可選聲明的應用程式
1. 在 **"管理**"部分下，選擇**權杖配置（預覽）**
2. 選擇 **"添加組"索賠**
3. 選擇要返回的組類型（**所有組**、**安全性群組**或**目錄角色**）。 "**所有組**"選項包括**安全性群組**、**目錄角色**和**通訊群組清單**
4. 可選：按一下特定的權杖類型屬性以修改組聲明值以包含本機群組屬性或將聲明類型更改為角色
5. 按一下 [儲存]****。

**通過應用程式清單配置組可選聲明：**
1. 登錄到 Azure[門戶](https://portal.azure.com)
1. 經過身份驗證後，請從頁面右上角選擇 Azure AD 租戶
1. 從左側功能表中選擇**Azure 活動目錄**
1. 選擇要在清單中配置可選聲明的應用程式
1. 在 **"管理**"部分下，選擇 **"清單"**
3. 使用清單編輯器添加以下條目：

   有效值為：

   - "全部"（此選項包括安全性群組、目錄角色和通訊群組清單）
   - "安全性群組"
   - "目錄角色"

   例如：

    ```json
        "groupMembershipClaims": "SecurityGroup"
    ```

   預設情況下，組物件 I 將在組聲明值中發出。  要修改聲明值以包含本機群組屬性，或將聲明類型更改為角色，請使用"可選索賠"配置，如下所示：

3. 設置組名稱配置可選聲明。

   如果要在權杖中分組以在可選宣告區段中包含本地 AD 組屬性，請指定應應用於哪個權杖類型可選聲明、請求的可選聲明的名稱和所需的任何其他屬性。  可以列出多種權杖類型：

   - OIDC ID 權杖的 idToken
   - OAuth 訪問權杖的訪問權杖
   - SAML 權杖的 Saml2 權杖。

   > [!NOTE]
   > Saml2Token 類型適用于 SAML1.1 和 SAML2.0 格式權杖

   對於每個相關的權杖類型，修改組聲明以在清單中使用"可選聲明"部分。 可選聲明架構如下所示：

    ```json
       {
       "name": "groups",
       "source": null,
       "essential": false,
       "additionalProperties": []
       }
    ```

   | 可選聲明架構 | 值 |
   |----------|-------------|
   | **名字：** | 必須是"組" |
   | **源：** | 未使用。 省略或指定 null |
   | **基本：** | 未使用。 省略或指定 false |
   | **其他屬性：** | 其他屬性的清單。  有效選項為"sam_account_name"、"dns_domain_and_sam_account_name"、"netbios_domain_and_sam_account_name"、"emit_as_roles" |

   在附加屬性中，只需要"sam_account_name"、"dns_domain_and_sam_account_name"、"netbios_domain_and_sam_account_name"之一。  如果存在多個，則使用第一個，忽略任何其他。

   某些應用程式需要有關角色聲明中的使用者的組資訊。  要將聲明類型從組聲明更改為角色聲明，向其他屬性添加"emit_as_roles"。  將在角色聲明中發出組值。

   > [!NOTE]
   > 如果使用"emit_as_roles"，則配置分配給使用者的任何應用程式角色將不會出現在角色聲明中

**例子：**

1) 以 dnsDomainName_sAMAccountName 格式在 OAuth 訪問權杖中將組名稱作為組名稱發出

    
    **UI 配置：**

    [![演示如何使用 UI 配置可選聲明](./media/active-directory-optional-claims/groups-example-1.png)](./media/active-directory-optional-claims/groups-example-1.png)


    **應用程式清單條目：**
    ```json
        "optionalClaims": {
            "accessToken": [{
            "name": "groups",
            "additionalProperties": ["dns_domain_and_sam_account_name"]
            }]
        }
    ```

 
    
2) 以 netbiosDomain_sAMAccountName 格式返回的組名稱，作為 SAML 和 OIDC ID 權杖中的角色聲明

    **UI 配置：**

    [![演示如何使用 UI 配置可選聲明](./media/active-directory-optional-claims/groups-example-2.png)](./media/active-directory-optional-claims/groups-example-2.png)

    **應用程式清單條目：**
    
    ```json
        "optionalClaims": {
        "saml2Token": [{
            "name": "groups",
            "additionalProperties": ["netbios_name_and_sam_account_name", "emit_as_roles"]
        }],
        "idToken": [{
            "name": "groups",
            "additionalProperties": ["netbios_name_and_sam_account_name", "emit_as_roles"]
        }]
    ``` 
     

## <a name="optional-claims-example"></a>選擇性宣告範例

在本節中，您可以逐步進行案例，以了解如何針對應用程式使用選擇性宣告功能。
有多個選項可用來更新應用程式身分識別設定上的屬性，以啟用和設定選擇性宣告：
-    您可以使用**權杖配置（預覽）UI（** 請參閱下面的示例）
-    您可以使用**清單**（請參閱下面的示例）。 請先閱讀介紹資訊清單的[了解 Azure AD 應用程式資訊清單](https://docs.microsoft.com/azure/active-directory/develop/active-directory-application-manifest)文件。
-   也可以編寫使用[Microsoft 圖形 API](https://docs.microsoft.com/graph/use-the-api?context=graph%2Fapi%2F1.0&view=graph-rest-1.0)更新應用程式的應用程式。 Microsoft 圖形 API 參考指南中的[可選聲明](https://docs.microsoft.com/graph/api/resources/optionalclaims?view=graph-rest-1.0)類型可以説明您配置可選聲明。

**示例：** 在下面的示例中，您將使用**權杖配置（預覽）UI**和**清單**向用於應用程式的訪問、ID 和 SAML 權杖添加可選聲明。 不同的可選聲明將添加到應用程式可以接收的每種權杖類型：
-    識別碼權杖現在將會在完整格式 (`<upn>_<homedomain>#EXT#@<resourcedomain>`) 中包含同盟使用者的 UPN。
-    其他用戶端請求此應用程式的訪問權杖現在包括auth_time聲明
-    SAML 權杖現在將會包含 skypeId 目錄結構描述延伸模組 (在此範例中，此應用程式的應用程式識別碼是 ab603c56068041afb2f6832e2a17e237)。 SAML 權杖會將 Skype 識別碼公開為 `extension_skypeId`。

**UI 配置：**

1. 登錄到 Azure[門戶](https://portal.azure.com)

1. 進行身份驗證後，通過從頁面右上角選擇 Azure AD 租戶來選擇它。

1. 從左側功能表中選擇**Azure 活動目錄**。

1. 在 **"管理"** 部分下，選擇**應用註冊**。

1. 從清單中找出您要為其設定選擇性宣告的應用程式，然後按一下該應用程式。

1. 在 **"管理"** 部分下，按一下 **"權杖配置"（預覽）。**

1. 選擇 **"添加可選聲明**"，選擇**ID**權杖類型，從聲明清單中選擇**upn，** 然後按一下"**添加**"。

1. 選擇 **"添加可選聲明**"，選擇**Access**權杖類型，從聲明清單中選擇**auth_time，** 然後按一下"**添加**"。

1. 在"權杖配置"概述螢幕中，按一下**upn**旁邊的鉛筆圖示，按一下**外部身份驗證的**切換，然後按一下"**保存**"。

1. 選擇 **"添加可選聲明**"，選擇**SAML**權杖類型，從聲明清單中選擇**extn.skypeID（** 僅在創建名為 skypeID 的 Azure AD 使用者物件時才適用），然後按一下"**添加**"。

    [![演示如何使用 UI 配置可選聲明](./media/active-directory-optional-claims/token-config-example.png)](./media/active-directory-optional-claims/token-config-example.png)

**清單配置：**
1. 登錄到 Azure[門戶](https://portal.azure.com)。
1. 進行身份驗證後，通過從頁面右上角選擇 Azure AD 租戶來選擇它。
1. 從左側功能表中選擇**Azure 活動目錄**。
1. 從清單中找出您要為其設定選擇性宣告的應用程式，然後按一下該應用程式。
1. 在 **"管理**"部分下，按一下 **"清單"** 以打開內聯清單編輯器。
1. 您可以使用此編輯器直接編輯資訊清單。 清單遵循[應用程式實體](https://docs.microsoft.com/azure/active-directory/develop/reference-app-manifest)的架構，並在保存後自動格式化清單。 新元素會新增至 `OptionalClaims` 屬性。

    ```json
            "optionalClaims": {
                "idToken": [ 
                     { 
                        "name": "upn", 
                        "essential": false, 
                        "additionalProperties": [ "include_externally_authenticated_upn"]  
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
        ``` 


1. When you're finished updating the manifest, click **Save** to save the manifest.

## Next steps

Learn more about the standard claims provided by Azure AD.

- [ID tokens](id-tokens.md)
- [Access tokens](access-tokens.md)
