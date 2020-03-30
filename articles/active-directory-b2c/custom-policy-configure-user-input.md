---
title: 在自訂策略中添加聲明並自訂使用者輸入
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79473671"
---
#  <a name="add-claims-and-customize-user-input-using-custom-policies-in-azure-active-directory-b2c"></a>在 Azure Active Directory B2C 中使用自訂原則來新增宣告並自訂使用者輸入

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

在本文中，您可以在 Azure 活動目錄 B2C （Azure AD B2C） 中的註冊過程中收集新屬性。 您將獲取使用者的城市，將其配置為下拉清單，並定義是否需要提供它。

> [!NOTE]
> 此示例使用內置聲明"城市"。 相反，您可以選擇受支援的[Azure AD B2C 內置屬性](user-profile-attributes.md)之一或自訂屬性。 要使用自訂屬性，請[在策略中啟用自訂屬性](custom-policy-custom-attributes.md)。 要使用其他內置屬性或自訂屬性，請將"城市"替換為您選擇的屬性，例如內置屬性*作業Title*或自訂屬性（如*extension_loyaltyId*）。  

您可以使用註冊或登錄使用者旅程從使用者那裡收集初始資料。 其他宣告則可在稍後透過使用設定檔編輯使用者旅程圖來收集。 每當 Azure AD B2C 以對話模式直接從使用者那裡收集資訊時，身份體驗框架就使用其[自斷言的技術設定檔](self-asserted-technical-profile.md)。 在此示例中，您可以：

1. 定義"城市"聲明。 
1. 向使用者詢問他們的城市。
1. 將城市保存到 Azure AD B2C 目錄中的使用者設定檔。
1. 從每個登錄的 Azure AD B2C 目錄中讀取城市聲明。
1. 登錄或註冊後，將城市返回到您的依賴方應用程式。  

## <a name="prerequisites"></a>Prerequisites

完成[開始使用自訂原則](custom-policy-get-started.md)中的步驟。 您應該有一個工作自訂策略，用於使用社交帳戶和本地帳戶進行註冊和登錄。

## <a name="define-a-claim"></a>定義聲明

聲明在 Azure AD B2C 策略執行期間提供臨時資料存儲。 [聲明架構](claimsschema.md)是聲明聲明的位置。 下列元素用來定義宣告：

- **DisplayName** - 一個字串，會定義使用者端的「標籤」。
- [資料類型](claimsschema.md#datatype)- 聲明的類型。
- **UserHelpText** - 可協助使用者了解所需項目。
- [使用者輸入類型](claimsschema.md#userinputtype)- 輸入控制項的類型，如文字方塊、無線電選擇、下拉清單或多個選擇。

打開策略的擴展檔。 例如， <em> `SocialAndLocalAccounts/` </em>.

1. 搜尋 [BuildingBlocks](buildingblocks.md) 元素。 如果此元素不存在，請加以新增。
1. 找到[聲明架構](claimsschema.md)元素。 如果此元素不存在，請加以新增。
1. 將城市聲明添加到**聲明架構**元素。  

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

## <a name="add-a-claim-to-the-user-interface"></a>向使用者介面添加聲明

以下技術設定檔是自[斷言的](self-asserted-technical-profile.md)，在使用者應提供輸入時調用：

- **本地帳戶簽名與登錄電子郵件**- 本地帳戶註冊流。
- **自我斷言 - 社交**- 聯合帳戶首次使用者登錄。
- **自我斷言-設定檔更新**- 編輯設定檔流。

要在註冊期間收集城市聲明，必須將其添加為技術設定檔中的`LocalAccountSignUpWithLogonEmail`輸出聲明。 覆蓋擴展檔中的此技術設定檔。 指定整個輸出聲明清單，以控制聲明在螢幕上顯示的順序。 尋找 **ClaimsProviders** 元素。 添加新的聲明提供程式，如下所示：

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

要在使用聯合帳戶進行初始登錄後收集城市聲明，必須將其添加為技術設定檔中的`SelfAsserted-Social`輸出聲明。 對於本地帳戶和聯合帳戶使用者以後能夠編輯其設定檔資料，請將輸出聲明添加到`SelfAsserted-ProfileUpdate`技術設定檔。 覆蓋擴展檔中的這些技術設定檔。 指定輸出聲明的整個清單，以控制聲明在螢幕上顯示的順序。 尋找 **ClaimsProviders** 元素。 添加新的聲明提供程式，如下所示：

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

## <a name="read-and-write-a-claim"></a>閱讀和寫入索賠

以下技術設定檔是[活動目錄技術設定檔](active-directory-technical-profile.md)，用於讀取資料並將其寫入 Azure 活動目錄。  
用於`PersistedClaims`將資料寫入使用者設定檔，並從`OutputClaims`相應的 Active Directory 技術設定檔中的使用者設定檔中讀取資料。

覆蓋擴展檔中的這些技術設定檔。 尋找 **ClaimsProviders** 元素。  添加新的聲明提供程式，如下所示：

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

## <a name="include-a-claim-in-the-token"></a>在權杖中包含聲明 

要將城市聲明返回到依賴方應用程式，請向<em>`SocialAndLocalAccounts/`</em>檔添加輸出聲明。 輸出聲明將在使用者成功訪問後添加到權杖中，並將發送到應用程式。 修改依賴方部分中的技術設定檔元素，將城市添加為輸出聲明。
 
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

1. 登錄到 Azure[門戶](https://portal.azure.com)。
2. 通過在頂部功能表中選擇**目錄 + 訂閱**篩選器並選擇包含 Azure AD 租戶的目錄，請確保使用的目錄包含 Azure AD 租戶。
3. 選擇 Azure 入口網站左上角的 [所有服務]****，然後搜尋並選取 [應用程式註冊]****。
4. 選取 [識別體驗架構]****。
5. 選取 [上傳自訂原則]****，然後上傳您所變更的兩個原則檔案。
2. 選取您上傳的註冊或登入原則，按一下 [立即執行]**** 按鈕。
3. 您應該可以使用電子郵件地址註冊。

註冊螢幕應類似于以下螢幕截圖：

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

- 瞭解有關 IEF 引用中[聲明架構](claimsschema.md)元素的更多內容。
- 瞭解如何[在自訂設定檔編輯策略中使用自訂屬性](custom-policy-custom-attributes.md)。
