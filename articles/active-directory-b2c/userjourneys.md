---
title: UserJourneys | Microsoft Docs
description: 指定 Azure Active Directory B2C 中自訂原則的 UserJourneys 元素。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 12/14/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: ce41edd2c0048a20368dd02c2dd6101248e26c14
ms.sourcegitcommit: cc13f3fc9b8d309986409276b48ffb77953f4458
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/14/2020
ms.locfileid: "97400008"
---
# <a name="userjourneys"></a>UserJourneys

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

使用者旅程圖會指定明確的路徑，原則可透過這類路徑來讓信賴憑證者應用程式取得使用者所需的宣告。 使用者會透過這些路徑來擷取要呈現給信賴憑證者的宣告。 換句話說，使用者旅程圖會定義當 Azure AD B2C 識別體驗架構處理要求時，使用者要經歷的商務邏輯。

這些使用者旅程可視為範本，可滿足所需的各群信賴憑證者的核心需求。 使用者旅程有助於定義原則的信賴憑證者部分。 原則可以定義多個使用者旅程圖。 每個使用者旅程圖都是一連串的協調流程步驟。

為了定義原則所支援的使用者旅程圖，會將 **UserJourneys** 元素新增到原則檔的最上層元素下方。

**UserJourneys** 元素包含下列元素：

| 元素 | 發生次數 | 描述 |
| ------- | ----------- | ----------- |
| UserJourney | 1:n | 使用者旅程圖，可定義完整使用者流程所需的所有建構。 |

**UserJourney** 元素包含下列屬性：

| 屬性 | 必要 | 描述 |
| --------- | -------- | ----------- |
| 識別碼 | 是 | 使用者旅程圖的識別碼，可用來從原則中的其他元素參考它。 [信賴憑證者原則](relyingparty.md)的 **DefaultUserJourney** 元素會指向這個屬性。 |

**UserJourney** 元素包含下列元素：

| 元素 | 發生次數 | 描述 |
| ------- | ----------- | ----------- |
| Authorizationtechnicalprofile | 0:1 | 授權技術設定檔的清單。 | 
| OrchestrationSteps | 1:n | 必須遵循才能獲得成功交易的協調流程序列。 每個使用者旅程圖都由依序執行的已排序協調流程步驟清單所組成。 如果有任何步驟失敗，交易就會失敗。 |

## <a name="authorizationtechnicalprofiles"></a>Authorizationtechnicalprofile

假設使用者已完成 UserJourney 並取得存取權或識別碼權杖。 若要管理其他資源（例如使用者 [資訊端點](userinfo-endpoint.md)），必須識別使用者。 若要開始此程式，使用者必須出示先前發行的存取權杖，證明它們最初是由有效的 Azure AD B2C 原則驗證。 使用者的有效 token 必須一律存在於此程式中，以確保允許使用者提出此要求。 授權技術設定檔會驗證連入權杖，並從權杖中將宣告解壓縮。

**Authorizationtechnicalprofile** 元素包含下列元素：

| 元素 | 發生次數 | 描述 |
| ------- | ----------- | ----------- |
| AuthorizationTechnicalProfile | 0:1 | 授權技術設定檔的清單。 | 

**AuthorizationTechnicalProfile** 元素包含下列屬性：

| 屬性 | 必要 | 描述 |
| --------- | -------- | ----------- |
| TechnicalProfileReferenceId | 是 | 要執行的技術設定檔識別碼。 |

下列範例顯示具有授權技術設定檔的使用者旅程圖元素：

```xml
<UserJourney Id="UserInfoJourney" DefaultCpimIssuerTechnicalProfileReferenceId="UserInfoIssuer">
  <Authorization>
    <AuthorizationTechnicalProfiles>
      <AuthorizationTechnicalProfile ReferenceId="UserInfoAuthorization" />
    </AuthorizationTechnicalProfiles>
  </Authorization>
  <OrchestrationSteps>
    <OrchestrationStep Order="1" Type="ClaimsExchange">
     ...
```

## <a name="orchestrationsteps"></a>OrchestrationSteps

使用者旅程圖會表示為必須遵循才能獲得成功交易的協調流程序列。 如果有任何步驟失敗，交易就會失敗。 這些協調流程步驟會參考這兩個建置組塊以及原則檔中所允許的宣告提供者。 任何負責顯示或呈現使用者體驗的協調流程步驟，也會參考對應的內容定義識別碼。

協調流程步驟可以根據在協調流程步驟元素中定義的前置條件，有條件地執行。 例如，您可以在特定宣告存在時，或如果宣告等於或不是指定的值時，檢查以執行協調流程步驟。

為了指定已排序的協調流程步驟清單，會新增 **OrchestrationSteps** 元素作為原則的一部分。 這個元素是必要的。

**OrchestrationSteps** 元素包含下列元素：

| 元素 | 發生次數 | 描述 |
| ------- | ----------- | ----------- |
| OrchestrationStep | 1:n | 已排序的協調流程步驟。 |

**OrchestrationStep** 元素包含下列屬性：

| 屬性 | 必要 | 描述 |
| --------- | -------- | ----------- |
| `Order` | 是 | 協調流程步驟的順序。 |
| `Type` | 是 | 協調流程步驟的類型。 可能的值： <ul><li>**ClaimsProviderSelection**：指出協調流程步驟會向使用者呈現各種宣告提供者，使其可選取其中一個。</li><li>**CombinedSignInAndSignUp**：指出協調流程步驟會呈現社交提供者登入和本機帳戶註冊的組合頁面。</li><li>**ClaimsExchange**：指出協調流程步驟會與宣告提供者交換宣告。</li><li>**GetClaims** -指定協調流程步驟應透過其設定，處理從信賴憑證者傳送至 Azure AD B2C 的宣告資料 `InputClaims` 。</li><li>**InvokeSubJourney** -指出協調流程步驟會與公開預覽)  (的 [子旅程](subjourneys.md) 交換宣告。</li><li>**SendClaims**：指出協調流程步驟會使用宣告簽發者所發出的權杖，將宣告傳送到信賴憑證者。</li></ul> |
| ContentDefinitionReferenceId | 否 | 與此協調流程步驟相關聯的[內容定義](contentdefinitions.md)識別碼。 內容定義參考識別碼通常定義於自我判斷技術設定檔中。 但是，在某些案例中，Azure AD B2C 需要在沒有技術設定檔的情況下顯示某些內容。 有兩個範例-如果協調流程步驟的類型是下列其中一項： `ClaimsProviderSelection` 或  `CombinedSignInAndSignUp` ，Azure AD B2C 必須在沒有技術設定檔的情況下顯示身分識別提供者選取專案。 |
| CpimIssuerTechnicalProfileReferenceId | 否 | 協調流程步驟的類型為 `SendClaims`。 這個屬性會定義宣告提供者的技術設定檔識別碼，此宣告提供者會發出適用於信賴憑證者的權杖。  如果不存在，就不會建立任何信賴憑證者權杖。 |

**OrchestrationStep** 元素可以包含下列元素：

| 元素 | 發生次數 | 描述 |
| ------- | ----------- | ----------- |
| 先決條件 | 0:n | 必須基於要執行的協調流程步驟滿足的先決條件清單。 |
| ClaimsProviderSelections | 0:n | 適用於協調流程步驟的宣告提供者選取項目清單。 |
| ClaimsExchanges | 0:n | 適用於協調流程步驟的宣告交換清單。 |
| JourneyList | 0:1 | 協調流程步驟的子旅程候選清單。 |

### <a name="preconditions"></a>先決條件

**Preconditions** 元素包含下列元素：

| 元素 | 發生次數 | 描述 |
| ------- | ----------- | ----------- |
| 先決條件 | 1:n | 根據所使用的技術設定檔，系統會依據宣告提供者選取項目來將用戶端重新導向，或進行伺服器呼叫來交換宣告。 |


#### <a name="precondition"></a>先決條件

**先決條件** 元素包含下列屬性：

| 屬性 | 必要 | 描述 |
| --------- | -------- | ----------- |
| `Type` | 是 | 要針對此先決條件執行的檢查或查詢類型。 值可以是 **ClaimsExist** (指定如果指定的宣告存在於使用者目前的宣告組中，就應執行動作) 或 **ClaimEquals** (指定如果指定宣告存在且其值等於指定的值，就應執行動作)。 |
| `ExecuteActionsIf` | 是 | 使用 True 或 False 測試，來決定是否應執行先決條件中的動作。 |

**Precondition** 元素包含下列元素：

| 元素 | 發生次數 | 描述 |
| ------- | ----------- | ----------- |
| 值 | 1:n | 要查詢的 ClaimTypeReferenceId。 另一個值元素包含要檢查的值。</li></ul>|
| 動作 | 1:1 | 當協調流程步驟內的先決條件檢查為 True 時應執行的動作。 如果將 `Action` 的值設定為 `SkipThisOrchestrationStep`，就不應執行相關聯的 `OrchestrationStep`。 |

#### <a name="preconditions-examples"></a>先決條件範例

下列先決條件會檢查使用者的 objectId 是否存在。 在使用者旅程圖中，使用者已選取來使用本機帳戶登入。 如果 objectId 存在，請略過此協調流程步驟。

```xml
<OrchestrationStep Order="2" Type="ClaimsExchange">
  <Preconditions>
    <Precondition Type="ClaimsExist" ExecuteActionsIf="true">
      <Value>objectId</Value>
      <Action>SkipThisOrchestrationStep</Action>
    </Precondition>
  </Preconditions>
  <ClaimsExchanges>
    <ClaimsExchange Id="FacebookExchange" TechnicalProfileReferenceId="Facebook-OAUTH" />
    <ClaimsExchange Id="SignUpWithLogonEmailExchange" TechnicalProfileReferenceId="LocalAccountSignUpWithLogonEmail" />
  </ClaimsExchanges>
</OrchestrationStep>
```

下列先決條件會檢查使用者是否已使用社交帳戶登入。 嘗試在目錄中尋找使用者帳戶。 如果使用者登入或註冊本機帳戶，請略過此協調流程步驟。

```xml
<OrchestrationStep Order="3" Type="ClaimsExchange">
  <Preconditions>
    <Precondition Type="ClaimEquals" ExecuteActionsIf="true">
      <Value>authenticationSource</Value>
      <Value>localAccountAuthentication</Value>
      <Action>SkipThisOrchestrationStep</Action>
    </Precondition>
  </Preconditions>
  <ClaimsExchanges>
    <ClaimsExchange Id="AADUserReadUsingAlternativeSecurityId" TechnicalProfileReferenceId="AAD-UserReadUsingAlternativeSecurityId-NoError" />
  </ClaimsExchanges>
</OrchestrationStep>
```

先決條件可以檢查多個先決條件。 下列範例會檢查 'objectId' 或 'email' 是否存在。 如果第一個條件為 true，則旅程會跳到下一個協調流程步驟。

```xml
<OrchestrationStep Order="4" Type="ClaimsExchange">
  <Preconditions>
    <Precondition Type="ClaimsExist" ExecuteActionsIf="true">
      <Value>objectId</Value>
      <Action>SkipThisOrchestrationStep</Action>
    </Precondition>
    <Precondition Type="ClaimsExist" ExecuteActionsIf="true">
      <Value>email</Value>
      <Action>SkipThisOrchestrationStep</Action>
    </Precondition>
  </Preconditions>
  <ClaimsExchanges>
    <ClaimsExchange Id="SelfAsserted-SocialEmail" TechnicalProfileReferenceId="SelfAsserted-SocialEmail" />
  </ClaimsExchanges>
</OrchestrationStep>
```

## <a name="claimsproviderselection"></a>ClaimsProviderSelection

類型 `ClaimsProviderSelection` 或 `CombinedSignInAndSignUp` 的協調流程步驟可能包含使用者可用以登入的宣告提供者清單。 `ClaimsProviderSelections` 元素內部的元素順序會控制呈現給使用者的識別提供者順序。

**>claimsproviderselections >claimsproviderselection** 元素包含下列元素：

| 元素 | 發生次數 | 描述 |
| ------- | ----------- | ----------- |
| ClaimsProviderSelection | 1:n | 提供可選取的宣告提供者清單。|

**>claimsproviderselections >claimsproviderselection** 元素包含下列屬性：

| 屬性 | 必要 | 描述 |
| --------- | -------- | ----------- |
| DisplayOption| 否 | 控制案例的行為，其中提供單一宣告提供者選取專案。 可能的值： `DoNotShowSingleProvider` (預設) ，使用者會立即重新導向至同盟身分識別提供者。 或 `ShowSingleProvider` Azure AD B2C 使用單一身分識別提供者選取專案來顯示登入頁面。 若要使用這個屬性， [內容定義版本](page-layout.md) 必須是 `urn:com:microsoft:aad:b2c:elements:contract:providerselection:1.0.0` 和更新版本。|

**ClaimsProviderSelection** 元素包含下列屬性：

| 屬性 | 必要 | 描述 |
| --------- | -------- | ----------- |
| TargetClaimsExchangeId | 否 | 宣告交換的識別碼，這會在宣告提供者選取項目的下一個協調流程步驟中執行。 您必須指定這個屬性或 ValidationClaimsExchangeId 屬性，但不需同時指定。 |
| ValidationClaimsExchangeId | 否 | 宣告交換的識別碼，這會在目前協調流程步驟中執行以驗證宣告提供者選取項目。 您必須指定這個屬性或 TargetClaimsExchangeId 屬性，但不需同時指定。 |

### <a name="claimsproviderselection-example"></a>ClaimsProviderSelection 範例

在下列協調流程步驟中，使用者可以選擇使用 Facebook、LinkedIn、Twitter、Google 或本機帳戶進行登入。 如果使用者選取其中一個社交識別提供者，第二個協調流程步驟就會使用 `TargetClaimsExchangeId` 屬性中指定的所選取宣告交換來執行。 第二個協調流程步驟會將使用者重新導向到該社交識別提供者，以完成登入程序。 如果使用者選擇使用本機帳戶登入，Azure AD B2C 就會停留在同一個協調流程步驟 (相同的註冊頁面或登入頁面)，並略過第二個協調流程步驟。

```xml
<OrchestrationStep Order="1" Type="CombinedSignInAndSignUp" ContentDefinitionReferenceId="api.signuporsignin">
  <ClaimsProviderSelections>
    <ClaimsProviderSelection TargetClaimsExchangeId="FacebookExchange" />
    <ClaimsProviderSelection TargetClaimsExchangeId="LinkedInExchange" />
    <ClaimsProviderSelection TargetClaimsExchangeId="TwitterExchange" />
    <ClaimsProviderSelection TargetClaimsExchangeId="GoogleExchange" />
    <ClaimsProviderSelection ValidationClaimsExchangeId="LocalAccountSigninEmailExchange" />
  </ClaimsProviderSelections>
  <ClaimsExchanges>
  <ClaimsExchange Id="LocalAccountSigninEmailExchange"
        TechnicalProfileReferenceId="SelfAsserted-LocalAccountSignin-Email" />
  </ClaimsExchanges>
</OrchestrationStep>


<OrchestrationStep Order="2" Type="ClaimsExchange">
  <Preconditions>
    <Precondition Type="ClaimsExist" ExecuteActionsIf="true">
      <Value>objectId</Value>
      <Action>SkipThisOrchestrationStep</Action>
    </Precondition>
  </Preconditions>
  <ClaimsExchanges>
    <ClaimsExchange Id="FacebookExchange" TechnicalProfileReferenceId="Facebook-OAUTH" />
    <ClaimsExchange Id="SignUpWithLogonEmailExchange" TechnicalProfileReferenceId="LocalAccountSignUpWithLogonEmail" />
  <ClaimsExchange Id="GoogleExchange" TechnicalProfileReferenceId="Google-OAUTH" />
    <ClaimsExchange Id="LinkedInExchange" TechnicalProfileReferenceId="LinkedIn-OAUTH" />
    <ClaimsExchange Id="TwitterExchange" TechnicalProfileReferenceId="Twitter-OAUTH1" />
  </ClaimsExchanges>
</OrchestrationStep>
```

## <a name="claimsexchanges"></a>ClaimsExchanges

**ClaimsExchanges** 元素包含下列元素：

| 元素 | 發生次數 | 描述 |
| ------- | ----------- | ----------- |
| ClaimsExchange | 1:n | 根據所使用的技術設定檔，系統會依據所選取的 ClaimsProviderSelection 來將用戶端重新導向，或進行伺服器呼叫來交換宣告。 |

**ClaimsExchange** 元素包含下列屬性：

| 屬性 | 必要 | 描述 |
| --------- | -------- | ----------- |
| 識別碼 | 是 | 宣告交換步驟的識別碼。 識別碼會用於參考原則中來自宣告提供者選取步驟的宣告交換。 |
| TechnicalProfileReferenceId | 是 | 要執行的技術設定檔識別碼。 |

## <a name="journeylist"></a>JourneyList

**JourneyList** 元素包含下列元素：

| 元素 | 發生次數 | 描述 |
| ------- | ----------- | ----------- |
| 候選項目 | 1:1 | 要呼叫之 sub 旅程的參考。 |

### <a name="candidate"></a>候選項目

**候選** 元素包含下列屬性：

| 屬性 | 必要 | 描述 |
| --------- | -------- | ----------- |
| SubJourneyReferenceId | 是 | 要執行之 [sub 旅程](subjourneys.md) 的識別碼。 |
