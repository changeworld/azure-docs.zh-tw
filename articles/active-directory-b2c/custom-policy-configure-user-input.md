---
title: 在自訂原則中新增宣告並自訂使用者輸入
titleSuffix: Azure AD B2C
description: 了解如何自訂使用者輸入並向 Azure Active Directory B2C 中的註冊或登錄旅程中新增宣告。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/17/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 85f2ab6f8c3e5edda027e44eeda13a3279a88321
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "79473671"
---
#  <a name="add-claims-and-customize-user-input-using-custom-policies-in-azure-active-directory-b2c"></a>在 Azure Active Directory B2C 中使用自訂原則來新增宣告並自訂使用者輸入

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

在本文中，您會在 Azure Active Directory B2C （Azure AD B2C）中的註冊旅程期間收集新的屬性。 您將取得使用者的城市、將其設定為下拉式記錄，以及定義是否必須提供。

> [!NOTE]
> 這個範例會使用內建的宣告「city」。 相反地，您可以選擇其中一個支援的[Azure AD B2C 內建屬性](user-profile-attributes.md)或自訂屬性。 若要使用自訂屬性，請[在您的原則中啟用自訂屬性](custom-policy-custom-attributes.md)。 若要使用不同的內建或自訂屬性，請以您選擇的屬性取代 ' city '，例如內建屬性*jobTitle*或如*extension_loyaltyId*的自訂屬性。  

您可以使用註冊或登入使用者旅程圖，收集使用者的初始資料。 其他宣告則可在稍後透過使用設定檔編輯使用者旅程圖來收集。 每當 Azure AD B2C 以互動方式直接從使用者收集資訊時，Identity Experience Framework 就會使用其[自我判斷技術設定檔](self-asserted-technical-profile.md)。 在此範例中，您會：

1. 定義「城市」宣告。 
1. 詢問使用者的城市。
1. 將城市保存在 Azure AD B2C 目錄中的使用者設定檔。
1. 從每次登入時的 Azure AD B2C 目錄中讀取 city 宣告。
1. 在登入或註冊之後，將 city 傳回給您的信賴憑證者應用程式。  

## <a name="prerequisites"></a>先決條件

完成[開始使用自訂原則](custom-policy-get-started.md)中的步驟。 您應該擁有可運作的自訂原則，以便使用社交和本機帳戶註冊和登入。

## <a name="define-a-claim"></a>定義宣告

宣告會在 Azure AD B2C 原則執行期間，提供資料的暫時儲存。 [宣告架構](claimsschema.md)是您宣告宣告的位置。 下列元素用來定義宣告：

- **DisplayName** - 一個字串，會定義使用者端的「標籤」。
- [DataType](claimsschema.md#datatype) -宣告的類型。
- **UserHelpText** - 可協助使用者了解所需項目。
- [UserInputType](claimsschema.md#userinputtype) -輸入控制項的類型，例如文字方塊、選項按鈕、下拉式清單或多個選取專案。

開啟原則的擴充檔案。 例如， <em> `SocialAndLocalAccounts/` </em>。

1. 搜尋 [BuildingBlocks](buildingblocks.md) 元素。 如果此元素不存在，請加以新增。
1. 找出[ClaimsSchema](claimsschema.md)元素。 如果此元素不存在，請加以新增。
1. 將 city 宣告新增至**ClaimsSchema**元素。  

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

下列技術設定檔是[自我](self-asserted-technical-profile.md)判斷的，會在使用者預期提供輸入時叫用：

- **LocalAccountSignUpWithLogonEmail** -本機帳戶註冊流程。
- **SelfAsserted-** 聯盟帳戶第一次使用者登入。
- **SelfAsserted-selfasserted-profileupdate** -編輯設定檔流程。

若要在註冊期間收集 city 宣告，必須將其新增為`LocalAccountSignUpWithLogonEmail`技術設定檔的輸出宣告。 覆寫擴充檔中的此技術設定檔。 指定輸出宣告的完整清單，以控制宣告呈現在螢幕上的順序。 尋找 **ClaimsProviders** 元素。 新增新的 ClaimsProviders，如下所示：

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
<ClaimsProvider>
```

若要在使用同盟帳戶初始登入後收集 city 宣告，必須將其新增為技術設定檔的`SelfAsserted-Social`輸出宣告。 若要讓本機和同盟帳戶使用者稍後能夠編輯其設定檔資料，請將輸出宣告新增至`SelfAsserted-ProfileUpdate`技術設定檔。 覆寫延伸模組檔案中的這些技術設定檔。 指定輸出宣告的完整清單，以控制宣告呈現在螢幕上的順序。 尋找 **ClaimsProviders** 元素。 新增新的 ClaimsProviders，如下所示：

```xml
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

下列技術設定檔是[Active Directory 的技術設定檔](active-directory-technical-profile.md)，可將資料讀取和寫入至 Azure Active Directory。  
使用`PersistedClaims`將資料寫入使用者設定檔，並`OutputClaims`從個別 Active Directory 技術設定檔中的使用者設定檔讀取資料。

覆寫延伸模組檔案中的這些技術設定檔。 尋找 **ClaimsProviders** 元素。  新增新的 ClaimsProviders，如下所示：

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

若要將 city 宣告傳回給信賴憑證者應用程式，請將輸出宣告新增<em> `SocialAndLocalAccounts/` </em>至檔案。 在使用者旅程成功之後，輸出宣告將會新增至權杖，並將傳送至應用程式。 修改 [信賴憑證者] 區段內的 [技術設定檔] 元素，將城市新增為輸出宣告。
 
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
2. 請選取頂端功能表中的 [**目錄 + 訂**用帳戶] 篩選，然後選擇包含您 Azure AD 租使用者的目錄，以確定您使用的是包含 Azure AD 租使用者的目錄。
3. 選擇 Azure 入口網站左上角的 [所有服務]****，然後搜尋並選取 [應用程式註冊]****。
4. 選取 [識別體驗架構]****。
5. 選取 [上傳自訂原則]****，然後上傳您所變更的兩個原則檔案。
2. 選取您上傳的註冊或登入原則，按一下 [立即執行]**** 按鈕。
3. 您應該可以使用電子郵件地址註冊。

註冊畫面看起來應該類似下列螢幕擷取畫面：

![修改過的註冊選項螢幕擷取畫面](./media/custom-policy-configure-user-input/signup-with-city-claim-dropdown-example.png)

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

## <a name="next-steps"></a>後續步驟

- 深入瞭解 IEF 參考中的[ClaimsSchema](claimsschema.md)元素。
- 瞭解如何[在自訂設定檔編輯原則中使用自訂屬性](custom-policy-custom-attributes.md)。
