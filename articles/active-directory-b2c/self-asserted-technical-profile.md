---
title: 在自訂策略中定義自我斷言的技術設定檔
titleSuffix: Azure AD B2C
description: 在 Azure Active Directory B2C 的自訂原則中定義自我判斷技術設定檔。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/26/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 2b29b8b0975639e5c5315a55e1382794d7662665
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80332498"
---
# <a name="define-a-self-asserted-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>在 Azure Active Directory B2C 自訂原則中定義自我判斷技術設定檔

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure 活動目錄 B2C（Azure AD B2C）中預期使用者提供輸入的所有交互都是自斷言的技術設定檔。 例如，註冊頁面、登入頁面或密碼重設頁面。

## <a name="protocol"></a>通訊協定

**Protocol** 元素的 **Name** 屬性必須設定為 `Proprietary`。 **handler** 屬性必須包含 Azure AD B2C 所使用之通訊協定處理常式組件的完整名稱，以進行自我判斷：`Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null`

下列範例顯示適用於電子郵件註冊的自我判斷技術設定檔：

```XML
<TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
  <DisplayName>Email signup</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
```

## <a name="input-claims"></a>輸入宣告

在自斷言技術設定檔中，可以使用 **"輸入索賠"** 和 **"輸入索賠轉換"** 元素預填充顯示在自斷言頁上的聲明的值（顯示聲明）。 例如，在編輯設定檔原則中，使用者旅程圖會先從 Azure AD B2C 目錄服務中讀取使用者設定檔，接著，自我判斷技術設定檔會使用儲存於使用者設定檔的使用者資料來設定輸入宣告。 這些宣告均收集自使用者設定檔，然後呈現給使用者，讓使用者接著可以編輯現有的資料。

```XML
<TechnicalProfile Id="SelfAsserted-ProfileUpdate">
...
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="alternativeSecurityId" />
    <InputClaim ClaimTypeReferenceId="userPrincipalName" />
    <InputClaim ClaimTypeReferenceId="givenName" />
    <InputClaim ClaimTypeReferenceId="surname" />
  </InputClaims>
```

## <a name="display-claims"></a>顯示聲明

顯示聲明功能當前處於**預覽狀態**。

**DisplayClaims**元素包含要在螢幕上顯示用於從使用者收集資料的聲明清單。 要預填充顯示聲明的值，請使用前面描述的輸入聲明。 元素可能也包含預設值。

**DisplayClaims**中聲明的順序指定 Azure AD B2C 在螢幕上呈現聲明的順序。 要強制使用者為特定聲明提供值，請將**DisplayClaim**元素的`true`**"必需"** 屬性設置為 。

**DisplayClaim**集合中的**ClaimType**元素需要將**使用者輸入類型**元素設置為 Azure AD B2C 支援的任何使用者輸入類型。 例如，`TextBox` 或 `DropdownSingleSelect`。

### <a name="add-a-reference-to-a-displaycontrol"></a>添加對顯示控制項的引用

在顯示聲明集合中，可以包含對已創建的[DisplayControl](display-controls.md)的引用。 顯示控制項是具有特殊功能並與 Azure AD B2C 後端服務交互的使用者介面元素。 它允許使用者在後端調用驗證技術設定檔的頁面上執行操作。 例如，驗證電子郵件地址、電話號碼或客戶忠誠度號碼。

下面的示例`TechnicalProfile`說明了使用顯示聲明與顯示控制項。

* 第一個顯示聲明引用`emailVerificationControl`顯示控制項，該控制項收集和驗證電子郵件地址。
* 第五個顯示聲明引用`phoneVerificationControl`顯示控制項，該控制項收集和驗證電話號碼。
* 另一個顯示聲明是從使用者收集的聲明類型。

```XML
<TechnicalProfile Id="Id">
  <DisplayClaims>
    <DisplayClaim DisplayControlReferenceId="emailVerificationControl" />
    <DisplayClaim ClaimTypeReferenceId="displayName" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="givenName" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="surName" Required="true" />
    <DisplayClaim DisplayControlReferenceId="phoneVerificationControl" />
    <DisplayClaim ClaimTypeReferenceId="newPassword" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="reenterPassword" Required="true" />
  </DisplayClaims>
</TechnicalProfile>
```

如前所述，引用顯示控制項的顯示聲明可能會運行其自己的驗證，例如驗證電子郵件地址。 此外，自斷言頁支援使用驗證技術設定檔來驗證整個頁面，包括任何使用者輸入（聲明類型或顯示控制項），然後再繼續執行下一個業務流程步驟。

### <a name="combine-usage-of-display-claims-and-output-claims-carefully"></a>仔細結合顯示聲明和輸出聲明的使用

如果在自斷言技術設定檔中指定一個或多個**DisplayClaim**元素，則必須對要在螢幕上顯示並從使用者那裡收集的*每個*索賠使用 DisplayClaim。 包含至少一個顯示聲明的自斷言技術設定檔不會顯示任何輸出聲明。

請考慮以下示例，其中聲明`age`在基策略中定義為**輸出**聲明。 在將任何顯示聲明添加到自斷言技術設定檔之前，該`age`聲明將顯示在螢幕上，以便從使用者收集資料：

```XML
<TechnicalProfile Id="id">
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="age" />
  </OutputClaims>
</TechnicalProfile>
```

如果繼承該基的葉策略隨後指定`officeNumber`為**顯示**聲明：

```XML
<TechnicalProfile Id="id">
  <DisplayClaims>
    <DisplayClaim ClaimTypeReferenceId="officeNumber" />
  </DisplayClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="officeNumber" />
  </OutputClaims>
</TechnicalProfile>
```

基本`age`策略中的聲明不再在螢幕上呈現給使用者 - 它實際上是"隱藏"的。 要顯示`age`聲明並從使用者那裡收集年齡值，必須添加`age` **DisplayClaim**。

## <a name="output-claims"></a>輸出宣告

**OutputClaims**元素包含要返回到下一個業務流程步驟的聲明清單。 僅當從未設置聲明時 **，DefaultValue**屬性才會生效。 如果該值在上一個業務流程步驟中設置，則即使使用者將該值留空，預設值也不會生效。 若要強制使用預設值，請將 **AlwaysUseDefaultValue** 屬性設定為 `true`。

出於安全原因，密碼聲明值 （`UserInputType`設置為`Password`） 僅適用于自斷言的技術設定檔的驗證技術設定檔。 您不能在下一個業務流程步驟中使用密碼聲明。 

> [!NOTE]
> 在以前的標識體驗框架 （IEF） 版本中，輸出聲明用於從使用者收集資料。 要從使用者收集資料，請使用**DisplayClaims**集合。

**OutputClaimsTransformations** 元素可能含有 **OutputClaimsTransformation** 的集合，用於修改輸出宣告或產生新的輸出宣告。

### <a name="when-you-should-use-output-claims"></a>何時應使用輸出聲明

在自斷言的技術設定檔中，輸出聲明集合將聲明返回到下一個業務流程步驟。

在：

- **聲明按輸出聲明轉換輸出**。
- **在輸出聲明中設置預設值，** 而無需從使用者收集資料或從驗證技術設定檔返回資料。 `LocalAccountSignUpWithLogonEmail` 自我判斷提示技術設定檔會將 **executed-SelfAsserted-Input** 宣告設定為 `true`。
- **驗證技術設定檔會傳回輸出宣告**：您的技術設定檔可能會呼叫驗證技術設定檔來傳回某些宣告。 您可能想要提升宣告，並將其傳回到使用者旅程圖中的下一個協調流程步驟。 例如，使用本機帳戶登入時，名為 `SelfAsserted-LocalAccountSignin-Email` 的自我判斷技術設定檔會呼叫名為 `login-NonInteractive` 的驗證技術設定檔。 此技術設定檔會驗證使用者認證，也會傳回使用者設定檔。 例如，'userPrincipalName'、'displayName'、'givenName' 與 'surName'。
- **顯示控制項返回輸出聲明**- 您的技術設定檔可能具有對[顯示控制項](display-controls.md)的引用。 顯示控制項返回一些聲明，如已驗證的電子郵件地址。 您可能想要提升宣告，並將其傳回到使用者旅程圖中的下一個協調流程步驟。 顯示控制功能當前處於**預覽狀態**。

下面的示例演示了使用同時使用顯示聲明和輸出聲明的自我斷言技術設定檔。

```XML
<TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
  <DisplayName>Email signup</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="IpAddressClaimReferenceId">IpAddress</Item>
    <Item Key="ContentDefinitionReferenceId">api.localaccountsignup</Item>
    <Item Key="language.button_continue">Create</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="email" />
  </InputClaims>
  <DisplayClaims>
    <DisplayClaim DisplayControlReferenceId="emailVerificationControl" />
    <DisplayClaim DisplayControlReferenceId="SecondaryEmailVerificationControl" />
    <DisplayClaim ClaimTypeReferenceId="displayName" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="givenName" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="surName" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="newPassword" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="reenterPassword" Required="true" />
  </DisplayClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="email" Required="true" />
    <OutputClaim ClaimTypeReferenceId="objectId" />
    <OutputClaim ClaimTypeReferenceId="executed-SelfAsserted-Input" DefaultValue="true" />
    <OutputClaim ClaimTypeReferenceId="authenticationSource" />
    <OutputClaim ClaimTypeReferenceId="newUser" />
  </OutputClaims>
  <ValidationTechnicalProfiles>
    <ValidationTechnicalProfile ReferenceId="AAD-UserWriteUsingLogonEmail" />
  </ValidationTechnicalProfiles>
  <UseTechnicalProfileForSessionManagement ReferenceId="SM-AAD" />
</TechnicalProfile>
```

## <a name="persist-claims"></a>保存宣告

不使用持久聲明元素。 自斷言的技術設定檔不會將資料保存到 Azure AD B2C。 而是改為呼叫負責保存資料的驗證技術設定檔。 例如，註冊原則會使用 `LocalAccountSignUpWithLogonEmail` 自我判斷技術設定檔來收集新的使用者設定檔。 `LocalAccountSignUpWithLogonEmail` 技術設定檔會呼叫驗證技術設定檔來建立 Azure AD B2C 中的帳戶。

## <a name="validation-technical-profiles"></a>驗證技術設定檔

驗證技術設定檔可用於驗證參考技術設定檔的部分或所有輸出宣告。 驗證技術設定檔的輸入宣告必須出現在自我判斷技術設定檔的輸出宣告中。 驗證技術設定檔會驗證使用者輸入，並且可將錯誤傳回給使用者。

驗證技術設定檔可以是原則中的任何技術設定檔，例如 [Azure Active Directory](active-directory-technical-profile.md) 或 [REST API](restful-technical-profile.md) 技術設定檔。 在上述範例中，`LocalAccountSignUpWithLogonEmail` 技術設定檔會驗證 signinName 不存在目錄中。 如果沒有，驗證技術設定檔就會建立本機帳戶，並傳回 objectId、authenticationSource、newUser。 `SelfAsserted-LocalAccountSignin-Email` 技術設定檔會呼叫 `login-NonInteractive` 驗證技術設定檔來驗證使用者認證。

您也可以使用您的商務邏輯來呼叫 REST API 技術設定檔、覆寫輸入宣告，或透過進一步整合企業營運應用程式來擴充使用者資料。 如需詳細資訊，請參閱[驗證技術設定檔](validation-technical-profile.md)

## <a name="metadata"></a>中繼資料

| 屬性 | 必要 | 描述 |
| --------- | -------- | ----------- |
| 設置.操作模式<sup>1</sup>| 否 | 對於登入頁面，此屬性會控制使用者名稱欄位的行為，例如輸入驗證和錯誤訊息。 預期的值：`Username` 或 `Email`。  |
| 允許生成具有空值的 claims| 否| 允許生成具有 null 值的聲明。 例如，在這種情況下，使用者不選擇核取方塊。|
| ContentDefinitionReferenceId | 是 | 與此技術設定檔相關聯的[內容定義](contentdefinitions.md)識別碼。 |
| EnforceEmailVerification | 否 | 針對註冊或設定檔編輯，強制執行電子郵件驗證。 可能的值：`true` (預設) 或 `false`。 |
| setting.retryLimit | 否 | 控制使用者可以嘗試提供針對驗證技術設定檔檢查的資料的次數。 例如，使用者嘗試使用已經存在的帳戶進行註冊，並持續嘗試，直到達到限制為止。
| 註冊目標<sup>1</sup>| 否 | 註冊目標交換識別碼。 當使用者按一下註冊按鈕時，Azure AD B2C 就會執行指定的交換識別碼。 |
| setting.showCancelButton | 否 | 顯示取消按鈕。 可能的值：`true` (預設) 或 `false` |
| setting.showContinueButton | 否 | 顯示繼續按鈕。 可能的值：`true` (預設) 或 `false` |
| 設置.showSignupLink <sup>2</sup>| 否 | 顯示註冊按鈕。 可能的值：`true` (預設) 或 `false` |
| 設置.忘記密碼連結位置<sup>2</sup>| 否| 顯示忘記的密碼連結。 可能的值：（`AfterInput`預設）連結顯示在頁面底部，或刪除`None`忘記的密碼連結。|
| 設置.啟用記住Me <sup>2</sup>| 否| 顯示"[讓我登錄](custom-policy-keep-me-signed-in.md)"核取方塊。 可能的值：`true`或`false`（預設值）。 |
| 包括索賠解決索賠處理  | 否 | 對於輸入和輸出聲明，指定[索賠解析](claim-resolver-overview.md)是否包含在技術設定檔中。 可能的值：`true`或`false` （預設值）。 如果要在技術設定檔中使用聲明解析器，則將此解決方案設置為`true`。 |

注意：
1. 可用於 內容定義[DataUri](contentdefinitions.md#datauri) `unifiedssp`類型`unifiedssd`。
1. 可用於 內容定義[DataUri](contentdefinitions.md#datauri) `unifiedssp`類型`unifiedssd`。 [頁面配置版本](page-layout.md)1.1.0 及以上。

## <a name="cryptographic-keys"></a>密碼編譯金鑰

不使用 **CryptographicKeys** 元素。
