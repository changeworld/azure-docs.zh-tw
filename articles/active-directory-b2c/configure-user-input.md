---
title: 新增使用者屬性和自訂使用者輸入
titleSuffix: Azure AD B2C
description: 瞭解如何自訂使用者輸入，並在 Azure Active Directory B2C 的註冊或登入旅程圖中新增使用者屬性。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 12/10/2020
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 698864a4dc1081cb8cad9036ff1cfc737a17473c
ms.sourcegitcommit: 6172a6ae13d7062a0a5e00ff411fd363b5c38597
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/11/2020
ms.locfileid: "97111190"
---
#  <a name="add-user-attributes-and-customize-user-input-in-azure-active-directory-b2c"></a>在 Azure Active Directory B2C 中新增使用者屬性和自訂使用者輸入

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-custom-policy"

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

::: zone-end

在本文中，您會在 Azure Active Directory B2C (Azure AD B2C) 的註冊旅程期間收集新的屬性。 您將取得使用者的城市、將其設定為下拉式清單，並定義是否需要提供。

## <a name="prerequisites"></a>Prerequisites

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

::: zone pivot="b2c-user-flow"

## <a name="add-user-attributes-your-user-flow"></a>新增使用者流程的使用者屬性

1. 在 Azure AD B2C 租用戶中，選取 [使用者流程]。
1. 選取您的原則 (例如「B2C_1_SignupSignin」) 以開啟之。
1. 選取 [ **使用者屬性** ]，然後選取 [使用者屬性] (例如 [城市] ) 。 
1. 選取 [儲存]。

## <a name="provide-optional-claims-to-your-app"></a>為您的應用程式提供選擇性宣告

應用程式宣告是傳回給應用程式的值。 更新您的使用者流程以包含所需的宣告。

1. 選取您的原則 (例如「B2C_1_SignupSignin」) 以開啟之。
1. 選取 [應用程式宣告]。
1. 選取您要傳回給應用程式的屬性 (例如「City」 ) 。
1. 選取 [儲存]。
 
## <a name="configure-user-attributes-input-type"></a>設定使用者屬性輸入類型

1. 選取您的原則 (例如「B2C_1_SignupSignin」) 以開啟之。
1. 選取 [ **頁面配置**]。
1. 選取 **本機帳戶註冊頁面**。
1. 在 [ **使用者屬性**] 下，選取 [ **城市**]。
    1. 在 [ **使用者輸入類型** ] 下拉式清單中，選取 [ **DropdownSingleSelect**]。
    1. 在 **選用** 的下拉式清單中，選取 [ **否**]。
1. 選取 [儲存]。 

### <a name="provide-a-list-of-values-by-using-localized-collections"></a>使用當地語系化的集合提供值清單

若要提供 city 屬性的設定值清單： 

1. [在使用者流程上啟用語言自訂](language-customization.md#support-requested-languages-for-ui_locales)
1. 選取您的原則 (例如「B2C_1_SignupSignin」) 以開啟之。
1. 在使用者流程的 [語言] 頁面上，選取您想要自訂的語言。
1. 在 [ **頁面層級資源**] 檔案底下，選取 [ **本機帳戶註冊頁面**]。
1. 選取 [下載預設值] (如果您先前已編輯這個語言，請按一下 [下載覆寫])。
1. 建立 `LocalizedCollections` 屬性。

`LocalizedCollections`是和配對的陣列 `Name` `Value` 。 項目的順序即為顯示它們的順序。 

* `ElementId` 是使用者屬性，而其回應則是這個 `LocalizedCollections` 屬性。
* `Name` 是向使用者顯示的值。
* `Value` 是選取此選項時在宣告中所傳回的內容。

```json
{
  "LocalizedStrings": [...],
  "LocalizedCollections": [
    {
      "ElementType": "ClaimType",
      "ElementId": "city",
      "TargetCollection": "Restriction",
      "Override": false,
      "Items": [
        {
          "Name": "Berlin",
          "Value": "Berlin"
        },
        {
          "Name": "London",
          "Value": "London"
        },
        {
          "Name": "Seattle",
          "Value": "Seattle"
        }
      ]
    }
  ]
}
```

#### <a name="upload-your-changes"></a>上傳您的變更

1. 對 JSON 檔案完成變更後，請瀏覽回到 B2C 租用戶。
1. 選取 [ **使用者流程** ]，然後選取您的原則 (例如，[B2C_1_SignupSignin] ) 開啟。
1. 選取 [語言]。
1. 選取您想要轉譯成什麼語言。
1. 選取 **本機帳戶註冊頁面**。
1. 選取資料夾圖示，然後選取要上傳的 JSON 檔案。 這些變更會自動儲存到您的使用者流程。

## <a name="test-your-user-flow"></a>測試您的使用者流程

1. 選取您的原則 (例如「B2C_1_SignupSignin」) 以開啟之。
1. 若要測試您的原則，請選取 [ **執行使用者流程**]。
1. 針對 [ **應用程式**]，選取您先前註冊的 web 應用程式（名為 *testapp1-pre-production* ）。 **Reply URL** 應顯示 `https://jwt.ms`。
1. 按一下 [**執行使用者流程**]

::: zone-end

::: zone pivot="b2c-custom-policy"

> [!NOTE]
> 此範例會使用內建的宣告「city」。 相反地，您可以選擇其中一個支援的 [Azure AD B2C 內建屬性](user-profile-attributes.md) 或自訂屬性。 若要使用自訂屬性，請 [在您的原則中啟用自訂屬性](custom-policy-custom-attributes.md)。 若要使用不同的內建或自訂屬性，請將 ' city ' 取代為您選擇的屬性，例如內建屬性 *jobTitle* 或自訂屬性（例如 *extension_loyaltyId*）。  

您可以使用註冊或登入使用者旅程圖，從使用者收集初始資料。 其他宣告則可在稍後透過使用設定檔編輯使用者旅程圖來收集。 每當 Azure AD B2C 以互動方式直接從使用者收集資訊時，Identity Experience Framework 就會使用自我判斷的 [技術設定檔](self-asserted-technical-profile.md)。 在此範例中，您會：

1. 定義「城市」宣告。 
1. 要求使用者提供其城市。
1. 將城市保存到 Azure AD B2C 目錄中的使用者設定檔。
1. 在每次登入時讀取 Azure AD B2C 目錄中的 city 宣告。
1. 登入或註冊之後，請將城市返回您的信賴憑證者應用程式。  

## <a name="define-a-claim"></a>定義宣告

宣告會在 Azure AD B2C 原則執行期間，提供資料的暫時儲存。 [宣告結構描述](claimsschema.md)是您宣告自有宣告的位置。 下列元素用來定義宣告：

- **DisplayName** - 一個字串，會定義使用者端的「標籤」。
- [DataType](claimsschema.md#datatype) -宣告的型別。
- **UserHelpText** - 可協助使用者了解所需項目。
- [>userinputtype](claimsschema.md#userinputtype) -輸入控制項的類型，例如文字方塊、選項按鈕、下拉式清單或多重選取專案。

開啟您原則的擴充檔。 例如，<em>`SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`**</em>。

1. 搜尋 [BuildingBlocks](buildingblocks.md) 元素。 如果此元素不存在，請加以新增。
1. 尋找 [ClaimsSchema](claimsschema.md) (機器翻譯) 元素。 如果此元素不存在，請加以新增。
1. 將 city 宣告加入至 **ClaimsSchema** 元素。  

```xml
<ClaimType Id="city">
  <DisplayName>City where you work</DisplayName>
  <DataType>string</DataType>
  <UserInputType>DropdownSingleSelect</UserInputType>
  <Restriction>
    <Enumeration Text="Bellevue" Value="bellevue" SelectByDefault="false" />
    <Enumeration Text="Redmond" Value="redmond" SelectByDefault="false" />
    <Enumeration Text="Kirkland" Value="kirkland" SelectByDefault="false" />
  </Restriction>
</ClaimType>
```

## <a name="add-a-claim-to-the-user-interface"></a>將宣告新增至使用者介面

下列技術設定檔是 [自我](self-asserted-technical-profile.md)判斷的，當使用者預期會提供輸入時，就會叫用這些設定檔：

- **LocalAccountSignUpWithLogonEmail** -本機帳戶註冊流程。
- **SelfAsserted-社交** 同盟帳戶首次使用者登入。
- **SelfAsserted-selfasserted-profileupdate** -編輯設定檔流程。

若要在註冊期間收集城市宣告，必須將其新增為技術設定檔的輸出宣告 `LocalAccountSignUpWithLogonEmail` 。 覆寫延伸模組檔案中的這個技術設定檔。 指定輸出宣告的完整清單，以控制宣告在螢幕上的顯示順序。 尋找 **ClaimsProviders** 元素。 新增 Claimsprovider，如下所示：

```xml
<ClaimsProvider>
  <DisplayName>Local Account</DisplayName>
  <TechnicalProfiles>
    <!--Local account sign-up page-->
    <TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
      <OutputClaims>
       <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="Verified.Email" Required="true" />
       <OutputClaim ClaimTypeReferenceId="newPassword" Required="true" />
       <OutputClaim ClaimTypeReferenceId="reenterPassword" Required="true" />
       <OutputClaim ClaimTypeReferenceId="displayName" />
       <OutputClaim ClaimTypeReferenceId="givenName" />
       <OutputClaim ClaimTypeReferenceId="surName" />
       <OutputClaim ClaimTypeReferenceId="city"/>
     </OutputClaims>
   </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

若要在初始以同盟帳戶登入之後收集 city 宣告，必須將其新增為技術設定檔的輸出宣告 `SelfAsserted-Social` 。 若要讓本機和同盟帳戶使用者稍後能夠編輯其設定檔資料，請將輸出宣告新增至 `SelfAsserted-ProfileUpdate` 技術設定檔。 覆寫延伸模組檔案中的這些技術設定檔。 指定輸出宣告的完整清單，以控制宣告在螢幕上的顯示順序。 尋找 **ClaimsProviders** 元素。 新增 Claimsprovider，如下所示：

```xml
<ClaimsProvider>
  <DisplayName>Self Asserted</DisplayName>
  <TechnicalProfiles>
    <!--Federated account first-time sign-in page-->
    <TechnicalProfile Id="SelfAsserted-Social">
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="displayName"/>
        <OutputClaim ClaimTypeReferenceId="givenName"/>
        <OutputClaim ClaimTypeReferenceId="surname"/>
        <OutputClaim ClaimTypeReferenceId="city"/>
      </OutputClaims>
    </TechnicalProfile>
    <!--Edit profile page-->
    <TechnicalProfile Id="SelfAsserted-ProfileUpdate">
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="displayName"/>
        <OutputClaim ClaimTypeReferenceId="givenName" />
        <OutputClaim ClaimTypeReferenceId="surname" />
        <OutputClaim ClaimTypeReferenceId="city"/>
      </OutputClaims>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="read-and-write-a-claim"></a>讀取和寫入宣告

下列技術設定檔是 [Active Directory 的技術設定檔](active-directory-technical-profile.md)，可讀取和寫入 Azure Active Directory 的資料。  
用 `PersistedClaims` 來將資料寫入使用者設定檔，以及 `OutputClaims` 從個別 Active Directory 技術設定檔內的使用者設定檔讀取資料。

覆寫延伸模組檔案中的這些技術設定檔。 尋找 **ClaimsProviders** 元素。  新增 Claimsprovider，如下所示：

```xml
<ClaimsProvider>
  <DisplayName>Azure Active Directory</DisplayName>
  <TechnicalProfiles>
    <!-- Write data during a local account sign-up flow. -->
    <TechnicalProfile Id="AAD-UserWriteUsingLogonEmail">
      <PersistedClaims>
        <PersistedClaim ClaimTypeReferenceId="city"/>
      </PersistedClaims>
    </TechnicalProfile>
    <!-- Write data during a federated account first-time sign-in flow. -->
    <TechnicalProfile Id="AAD-UserWriteUsingAlternativeSecurityId">
      <PersistedClaims>
        <PersistedClaim ClaimTypeReferenceId="city"/>
      </PersistedClaims>
    </TechnicalProfile>
    <!-- Write data during edit profile flow. -->
    <TechnicalProfile Id="AAD-UserWriteProfileUsingObjectId">
      <PersistedClaims>
        <PersistedClaim ClaimTypeReferenceId="city"/>
      </PersistedClaims>
    </TechnicalProfile>
    <!-- Read data after user authenticates with a local account. -->
    <TechnicalProfile Id="AAD-UserReadUsingEmailAddress">
      <OutputClaims>  
        <OutputClaim ClaimTypeReferenceId="city" />
      </OutputClaims>
    </TechnicalProfile>
    <!-- Read data after user authenticates with a federated account. -->
    <TechnicalProfile Id="AAD-UserReadUsingObjectId">
      <OutputClaims>  
        <OutputClaim ClaimTypeReferenceId="city" />
      </OutputClaims>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="include-a-claim-in-the-token"></a>在權杖中包含宣告 

若要將 city 索取傳回給信賴憑證者應用程式，請將輸出宣告新增至檔案 <em>`SocialAndLocalAccounts/`**`SignUpOrSignIn.xml`**</em> 。 在使用者旅程圖成功後，將會將輸出宣告新增至權杖，並且將會傳送至應用程式。 修改 [信賴憑證者] 區段內的技術設定檔元素，以新增城市作為輸出宣告。
 
```xml
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
  <TechnicalProfile Id="PolicyProfile">
    <DisplayName>PolicyProfile</DisplayName>
    <Protocol Name="OpenIdConnect" />
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="displayName" />
      <OutputClaim ClaimTypeReferenceId="givenName" />
      <OutputClaim ClaimTypeReferenceId="surname" />
      <OutputClaim ClaimTypeReferenceId="email" />
      <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
      <OutputClaim ClaimTypeReferenceId="identityProvider" />
      <OutputClaim ClaimTypeReferenceId="tenantId" AlwaysUseDefaultValue="true" DefaultValue="{Policy:TenantObjectId}" />
      <OutputClaim ClaimTypeReferenceId="city" DefaultValue="" />
    </OutputClaims>
    <SubjectNamingInfo ClaimType="sub" />
  </TechnicalProfile>
</RelyingParty>
```

## <a name="test-the-custom-policy"></a>測試自訂原則

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 選取頂端功能表中的 [目錄 + 訂用帳戶] 篩選，然後選擇包含您 Azure AD 租用戶的目錄，以確定您使用的是包含 Azure AD 租用戶的目錄。
3. 選擇 Azure 入口網站左上角的 [所有服務]，然後搜尋並選取 [應用程式註冊]。
4. 選取 [識別體驗架構]。
5. 選取 [上傳自訂原則]，然後上傳您所變更的兩個原則檔案。
2. 選取您上傳的註冊或登入原則，按一下 [立即執行] 按鈕。
3. 您應該可以使用電子郵件地址註冊。

::: zone-end

註冊畫面看起來應該類似下列螢幕擷取畫面：

![修改過的註冊選項螢幕擷取畫面](./media/configure-user-input/signup-with-city-claim-drop-down-example.png)

傳送回您的應用程式的權杖包含 `city` 宣告。

```json
{
  "typ": "JWT",
  "alg": "RS256",
  "kid": "X5eXk4xyojNFum1kl2Ytv8dlNP4-c57dO6QGTVBwaNk"
}.{
  "exp": 1583500140,
  "nbf": 1583496540,
  "ver": "1.0",
  "iss": "https://contoso.b2clogin.com/f06c2fe8-709f-4030-85dc-38a4bfd9e82d/v2.0/",
  "aud": "e1d2612f-c2bc-4599-8e7b-d874eaca1ee1",
  "acr": "b2c_1a_signup_signin",
  "nonce": "defaultNonce",
  "iat": 1583496540,
  "auth_time": 1583496540,
  "name": "Emily Smith",
  "email": "joe@outlook.com",
  "given_name": "Emily",
  "family_name": "Smith",
  "city": "Bellevue"
  ...
}
```

::: zone pivot="b2c-custom-policy"

## <a name="next-steps"></a>後續步驟

- 深入瞭解 IEF 參考中的 [ClaimsSchema](claimsschema.md) 元素。
- 瞭解如何 [在自訂設定檔編輯原則中使用自訂屬性](custom-policy-custom-attributes.md)。

::: zone-end