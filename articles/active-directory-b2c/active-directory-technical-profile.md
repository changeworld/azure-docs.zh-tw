---
title: 在自訂原則中定義 Azure AD 技術設定檔
titleSuffix: Azure AD B2C
description: 定義 Azure Active Directory B2C 自訂原則中的 Azure Active Directory 技術設定檔。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/26/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 49d8e83c158cd14357a74b9dde4af1daba26dc36
ms.sourcegitcommit: 6172a6ae13d7062a0a5e00ff411fd363b5c38597
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/11/2020
ms.locfileid: "97109108"
---
# <a name="define-an-azure-active-directory-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>定義 Azure Active Directory B2C 自訂原則中的 Azure Active Directory 技術設定檔

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) 提供 Azure Active Directory 使用者管理的支援。 本文會說明技術設定檔的詳細規格，其可和支援此標準化通訊協定的宣告提供者互動。

## <a name="protocol"></a>通訊協定

**Protocol** 元素的 **Name** 屬性必須設定為 `Proprietary`。 **handler** 屬性必須包含通訊協定處理常式組件的完整名稱 `Web.TPEngine.Providers.AzureActiveDirectoryProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null`。

下列 [自訂原則入門套件](custom-policy-get-started.md#custom-policy-starter-pack) Azure AD 技術設定檔，包括 **AAD 通用** 技術設定檔。 Azure AD 技術設定檔未指定通訊協定，因為已在 **AAD 一般** 技術設定檔中設定通訊協定：
 
- **AAD-UserReadUsingAlternativeSecurityId** 和 **AAD-UserReadUsingAlternativeSecurityId-NoError** - 在目錄中尋找社交帳戶。
- **AAD-UserWriteUsingAlternativeSecurityId** - 建立新的社交帳戶。
- **AAD-UserReadUsingEmailAddress** - 在目錄中尋找本機帳戶。
- **AAD-UserWriteUsingLogonEmail** - 建立新的本機帳戶。
- **AAD-UserWritePasswordUsingObjectId** - 更新本機帳戶的密碼。
- **AAD-UserWriteProfileUsingObjectId** - 更新本機或社交帳戶的使用者設定檔。
- **AAD-UserReadUsingObjectId** - 讀取本機或社交帳戶的使用者設定檔。
- **AAD-UserWritePhoneNumberUsingObjectId** - 寫入本機或社交帳戶的 MFA 電話號碼

下列範例顯示 **AAD-Common** 技術設定檔：

```xml
<TechnicalProfile Id="AAD-Common">
  <DisplayName>Azure Active Directory</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.AzureActiveDirectoryProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />

  <CryptographicKeys>
    <Key Id="issuer_secret" StorageReferenceId="B2C_1A_TokenSigningKeyContainer" />
  </CryptographicKeys>

  <!-- We need this here to suppress the SelfAsserted provider from invoking SSO on validation profiles. -->
  <IncludeInSso>false</IncludeInSso>
  <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
</TechnicalProfile>
```

## <a name="inputclaims"></a>InputClaims

InputClaims 元素包含宣告，可用來查閱目錄中的帳戶，或建立一個新的帳戶。 所有 Azure AD 技術設定檔的輸入宣告集合中必須只有一個 InputClaim 元素。 您可能需要將原則中定義的宣告名稱對應至 Azure Active Directory 中定義的名稱。

若要讀取、更新或刪除現有的使用者帳戶，輸入宣告是可唯一識別 Azure AD 目錄中帳戶的金鑰。 例如， **objectId**、 **userPrincipalName**、 **>signinnames.emailaddress. emailAddress**、>signinnames.emailaddress、 **userName** 或 **>alternativesecurityid**。 

若要建立新的使用者帳戶，輸入宣告是可唯一識別本機或同盟帳戶的金鑰。 例如，本機帳戶： **>signinnames.emailaddress. emailAddress** 或 **>signinnames.emailaddress。 userName**。 針對同盟帳戶： **>alternativesecurityid**。

[InputClaimsTransformations](technicalprofiles.md#inputclaimstransformations)元素可以包含輸入宣告轉換元素的集合，這些專案是用來修改輸入宣告或產生新的宣告。

## <a name="outputclaims"></a>OutputClaims

**OutputClaims** 元素包含 Azure AD 技術設定檔傳回的宣告清單。 您可能需要將原則中定義的宣告名稱對應至 Azure Active Directory 中定義的名稱。 只要設定了 `DefaultValue` 屬性，也可以加入 Azure Active Directory 未傳回的宣告。

[OutputClaimsTransformations](technicalprofiles.md#outputclaimstransformations) 元素可能含有 **OutputClaimsTransformation** 的集合，用於修改輸出宣告或產生新的輸出宣告。

例如，**AAD-UserWriteUsingLogonEmail** 技術設定檔會建立本機帳戶，並傳回下列宣告：

- **objectId**，這是新帳戶的識別碼
- **newUser**，可顯示使用者是否為新使用者
- **authenticationSource**，可將驗證設為 `localAccountAuthentication`
- **userPrincipalName**，是新帳戶的使用者主體名稱
- **signInNames.emailAddress**，是帳戶登入名稱，類似於 **email** 輸入宣告

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="objectId" />
  <OutputClaim ClaimTypeReferenceId="newUser" PartnerClaimType="newClaimsPrincipalCreated" />
  <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="localAccountAuthentication" />
  <OutputClaim ClaimTypeReferenceId="userPrincipalName" />
  <OutputClaim ClaimTypeReferenceId="signInNames.emailAddress" />
</OutputClaims>
```

## <a name="persistedclaims"></a>PersistedClaims

**PersistedClaims** 元素包含應由 Azure AD 保存的所有值，以及在原則的 [ClaimsSchema](claimsschema.md)區段中已定義的宣告類型和 Azure AD 屬性名稱之間可能的對應資訊。

**AAD-UserWriteUsingLogonEmail** 技術設定檔，可建立新的本機帳戶，持續使用下列宣告：

```xml
  <PersistedClaims>
    <!-- Required claims -->
    <PersistedClaim ClaimTypeReferenceId="email" PartnerClaimType="signInNames.emailAddress" />
    <PersistedClaim ClaimTypeReferenceId="newPassword" PartnerClaimType="password"/>
    <PersistedClaim ClaimTypeReferenceId="displayName" DefaultValue="unknown" />
    <PersistedClaim ClaimTypeReferenceId="passwordPolicies" DefaultValue="DisablePasswordExpiration" />

    <!-- Optional claims. -->
    <PersistedClaim ClaimTypeReferenceId="givenName" />
    <PersistedClaim ClaimTypeReferenceId="surname" />
  </PersistedClaims>
```

宣告的名稱是 Azure AD 屬性的名稱，除非已指定了含有 Azure AD 屬性名稱 **PartnerClaimType** 屬性。

## <a name="requirements-of-an-operation"></a>作業需求

- 所有 Azure AD 技術設定檔的宣告包中，只能有一個 **InputClaim** 元素。
- [使用者設定檔屬性一文](user-profile-attributes.md)說明您可以在輸入宣告、輸出宣告和保存宣告中使用的支援 Azure AD B2C 使用者設定檔屬性。 
- 如果作業是 `Write` 或 `DeleteClaims`，則該作業也必須出現在 **PersistedClaims** 元素中。
- **userPrincipalName** 宣告值的格式必須是 `user@tenant.onmicrosoft.com`。
- **displayName** 宣告是必要的，而且不可為空白字串。

## <a name="azure-ad-technical-provider-operations"></a>Azure AD 技術提供者作業

### <a name="read"></a>讀取

**讀取** 作業可讀取單一使用者帳戶的相關資料。 以下技術設定檔會利用使用者的 objectId 讀取使用者帳戶的相關資料：

```xml
<TechnicalProfile Id="AAD-UserReadUsingObjectId">
  <Metadata>
    <Item Key="Operation">Read</Item>
    <Item Key="RaiseErrorIfClaimsPrincipalDoesNotExist">true</Item>
  </Metadata>
  <IncludeInSso>false</IncludeInSso>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="objectId" Required="true" />
  </InputClaims>
  <OutputClaims>

    <!-- Required claims -->
    <OutputClaim ClaimTypeReferenceId="strongAuthenticationPhoneNumber" />

    <!-- Optional claims -->
    <OutputClaim ClaimTypeReferenceId="signInNames.emailAddress" />
    <OutputClaim ClaimTypeReferenceId="displayName" />
    <OutputClaim ClaimTypeReferenceId="otherMails" />
    <OutputClaim ClaimTypeReferenceId="givenName" />
    <OutputClaim ClaimTypeReferenceId="surname" />
  </OutputClaims>
  <IncludeTechnicalProfile ReferenceId="AAD-Common" />
</TechnicalProfile>
```

### <a name="write"></a>寫入

**寫入** 作業可建立或更新單一使用者帳戶。 下列技術設定檔會建立新的社交帳戶：

```xml
<TechnicalProfile Id="AAD-UserWriteUsingAlternativeSecurityId">
  <Metadata>
    <Item Key="Operation">Write</Item>
    <Item Key="RaiseErrorIfClaimsPrincipalAlreadyExists">true</Item>
    <Item Key="UserMessageIfClaimsPrincipalAlreadyExists">You are already registered, please press the back button and sign in instead.</Item>
  </Metadata>
  <IncludeInSso>false</IncludeInSso>
  <InputClaimsTransformations>
    <InputClaimsTransformation ReferenceId="CreateOtherMailsFromEmail" />
  </InputClaimsTransformations>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="AlternativeSecurityId" PartnerClaimType="alternativeSecurityId" Required="true" />
  </InputClaims>
  <PersistedClaims>
    <!-- Required claims -->
    <PersistedClaim ClaimTypeReferenceId="alternativeSecurityId" />
    <PersistedClaim ClaimTypeReferenceId="userPrincipalName" />
    <PersistedClaim ClaimTypeReferenceId="mailNickName" DefaultValue="unknown" />
    <PersistedClaim ClaimTypeReferenceId="displayName" DefaultValue="unknown" />

    <!-- Optional claims -->
    <PersistedClaim ClaimTypeReferenceId="otherMails" />
    <PersistedClaim ClaimTypeReferenceId="givenName" />
    <PersistedClaim ClaimTypeReferenceId="surname" />
  </PersistedClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="objectId" />
    <OutputClaim ClaimTypeReferenceId="newUser" PartnerClaimType="newClaimsPrincipalCreated" />
    <OutputClaim ClaimTypeReferenceId="otherMails" />
  </OutputClaims>
  <IncludeTechnicalProfile ReferenceId="AAD-Common" />
  <UseTechnicalProfileForSessionManagement ReferenceId="SM-AAD" />
</TechnicalProfile>
```

### <a name="deleteclaims"></a>DeleteClaims

**DeleteClaims** 作業會清除提供之宣告清單中的資訊。 下列技術設定檔會刪除宣告：

```xml
<TechnicalProfile Id="AAD-DeleteClaimsUsingObjectId">
  <Metadata>
    <Item Key="Operation">DeleteClaims</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="objectId" Required="true" />
  </InputClaims>
  <PersistedClaims>
    <PersistedClaim ClaimTypeReferenceId="objectId" />
    <PersistedClaim ClaimTypeReferenceId="Verified.strongAuthenticationPhoneNumber" PartnerClaimType="strongAuthenticationPhoneNumber" />
  </PersistedClaims>
  <OutputClaims />
  <IncludeTechnicalProfile ReferenceId="AAD-Common" />
</TechnicalProfile>
```

### <a name="deleteclaimsprincipal"></a>DeleteClaimsPrincipal

**DeleteClaimsPrincipal** 作業會將單一使用者帳戶從目錄中刪除。 下列技術設定檔會使用使用者主體名稱，將使用者帳戶從目錄中刪除：

```xml
<TechnicalProfile Id="AAD-DeleteUserUsingObjectId">
  <Metadata>
    <Item Key="Operation">DeleteClaimsPrincipal</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="objectId" Required="true" />
  </InputClaims>
  <OutputClaims/>
  <IncludeTechnicalProfile ReferenceId="AAD-Common" />
</TechnicalProfile>
```

下列技術設定檔使用 **alternativeSecurityId** 刪除社交使用者帳戶：

```xml
<TechnicalProfile Id="AAD-DeleteUserUsingAlternativeSecurityId">
  <Metadata>
    <Item Key="Operation">DeleteClaimsPrincipal</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="alternativeSecurityId" Required="true" />
  </InputClaims>
  <OutputClaims/>
  <IncludeTechnicalProfile ReferenceId="AAD-Common" />
</TechnicalProfile>
```
## <a name="metadata"></a>中繼資料

| 屬性 | 必要 | 說明 |
| --------- | -------- | ----------- |
| 作業 | 是 | 要執行的作業。 可能的值：`Read`、`Write`、`DeleteClaims` 或 `DeleteClaimsPrincipal`。 |
| RaiseErrorIfClaimsPrincipalDoesNotExist | 否 | 如果使用者物件不存在目錄中，會發生錯誤。 可能的值：`true` 或 `false`。 |
| RaiseErrorIfClaimsPrincipalAlreadyExists | 否 | 如果使用者物件已存在，則會引發錯誤。 可能的值：`true` 或 `false`。|
| ApplicationObjectId | 否 | 擴充屬性的應用程式物件識別碼。 值：應用程式的 ObjectId。 如需詳細資訊，請參閱 [在自訂設定檔編輯原則中使用自訂屬性](custom-policy-custom-attributes.md)。 |
| ClientId | 否 | 以協力廠商身分存取租用戶的用戶端識別碼。 如需詳細資訊，請參閱[在自訂設定檔編輯原則中使用自訂屬性](custom-policy-custom-attributes.md) |
| IncludeClaimResolvingInClaimsHandling  | 否 | 針對輸入和輸出宣告，指定技術設定檔中是否包含 [宣告解析](claim-resolver-overview.md) 。 可能的值為：`true` 或 `false` (預設)。 如果您想要在技術設定檔中使用宣告解析程式，請將此設定為 `true` 。 |

### <a name="ui-elements"></a>UI 元素
 
您可以使用下列設定來設定失敗時所顯示的錯誤訊息。 中繼資料應該在 [自我](self-asserted-technical-profile.md) 判斷技術設定檔中設定。 錯誤訊息可以[當地語系化](localization.md)。

| 屬性 | 必要 | 說明 |
| --------- | -------- | ----------- |
| UserMessageIfClaimsPrincipalAlreadyExists | 否 | 如果會引發錯誤 (請參閱 RaiseErrorIfClaimsPrincipalAlreadyExists 屬性說明)，請指定當使用者物件存在時，要向使用者顯示的訊息。 |
| UserMessageIfClaimsPrincipalDoesNotExist | 否 | 如果會引發錯誤 (請參閱 RaiseErrorIfClaimsPrincipalDoesNotExist 屬性的說明)，請指定當使用者物件不存在時，要向使用者顯示的訊息。 |


## <a name="next-steps"></a>後續步驟

請參閱下列文章，例如使用 Azure AD 技術設定檔：

- [在 Azure Active Directory B2C 中使用自訂原則來新增宣告並自訂使用者輸入](configure-user-input.md)














