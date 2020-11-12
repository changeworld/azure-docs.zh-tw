---
title: 顯示控制項參考
titleSuffix: Azure AD B2C
description: Azure AD B2C 顯示控制項的參考。 使用顯示控制項自訂自訂原則中定義的使用者旅程。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 10/12/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 950c159ed4d2c57796f33b9505e6931dbec983ba
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/12/2020
ms.locfileid: "94532370"
---
# <a name="display-controls"></a>顯示控制項

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

**顯示控制項** 是具有特殊功能的使用者介面元素，並與 Azure Active Directory B2C (Azure AD B2C) 後端服務互動。 它可讓使用者在頁面上執行動作，該頁面會在後端叫用 [驗證技術設定檔](validation-technical-profile.md) 。 顯示控制項會顯示在頁面上，並由自我判斷 [技術設定檔](self-asserted-technical-profile.md)參考。

下圖說明自我判斷的註冊頁面，其中包含兩個可驗證主要和次要電子郵件地址的顯示控制項。

![範例轉譯的顯示控制項](media/display-controls/display-control-email.png)

## <a name="prerequisites"></a>必要條件

 在[自我判斷技術設定檔](self-asserted-technical-profile.md)的[中繼資料](self-asserted-technical-profile.md#metadata)區段中，參考的[ContentDefinition](contentdefinitions.md)必須 `DataUri` 設定為頁面合約版本2.0.0 或更高版本。 例如：

```xml
<ContentDefinition Id="api.selfasserted">
  <LoadUri>~/tenant/default/selfAsserted.cshtml</LoadUri>
  <RecoveryUri>~/common/default_page_error.html</RecoveryUri>
  <DataUri>urn:com:microsoft:aad:b2c:elements:selfasserted:2.0.0</DataUri>
  ...
```

## <a name="defining-display-controls"></a>定義顯示控制項

[ **控制項** ] 元素包含下列屬性：

| 屬性 | 必要 | 描述 |
| --------- | -------- | ----------- |
| Id | 是 | 用於顯示控制項的識別碼。 您可以 [參考](#referencing-display-controls)它。 |
| UserInterfaceControlType | 是 | 顯示控制項的類型。 目前支援 [VerificationControl](display-control-verification.md) |

[ **控制項** ] 元素包含下列元素：

| 元素 | 發生次數 | 描述 |
| ------- | ----------- | ----------- |
| InputClaims | 0:1 | **InputClaims** 用來預先填入要從使用者收集的宣告值。 如需詳細資訊，請參閱 [InputClaims](technicalprofiles.md#inputclaims) 元素。 |
| DisplayClaims | 0:1 | **DisplayClaims** 可用來代表要從使用者收集的宣告。 如需詳細資訊，請參閱 [DisplayClaim](technicalprofiles.md#displayclaim) 元素。|
| OutputClaims | 0:1 | **OutputClaims** 可用來代表要暫時 **儲存此顯示的宣告** 。 如需詳細資訊，請參閱 [OutputClaims](technicalprofiles.md#outputclaims) 元素。|
| 動作 | 0:1 | **動作** 可用來列出驗證技術設定檔，以針對前端發生的使用者動作叫用。 |

### <a name="input-claims"></a>輸入宣告

在顯示控制項中，您可以使用 **InputClaims** 專案預先填入要在頁面上從使用者收集的宣告值。 任何 **InputClaimsTransformations** 都可以在參考此顯示控制項的自我判斷提示技術設定檔中定義。

下列範例會會預先填入電子郵件地址，以使用已存在的位址進行驗證。

```xml
<DisplayControl Id="emailControl" UserInterfaceControlType="VerificationControl">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="emailAddress" />
  </InputClaims>
  ...
```

### <a name="display-claims"></a>顯示宣告

每種類型的顯示控制項都需要一組不同的顯示宣告、 [輸出宣告](#output-claims)，以及要執行的 [動作](#display-control-actions) 。

類似于 [自我判斷技術設定檔](self-asserted-technical-profile.md#display-claims)中定義的 **顯示宣告** ，顯示宣告代表要從使用者在顯示控制項中收集的宣告。 參考的 **ClaimType** 元素必須針對 Azure AD B2C 所支援的使用者輸入類型指定 **>userinputtype** 元素，例如 `TextBox` 或 `DropdownSingleSelect` 。 如果 **動作** 需要顯示宣告值，請將 **必要** 的屬性設定為， `true` 以強制使用者提供該特定顯示宣告的值。

特定的顯示控制項類型需要特定的顯示宣告。 例如， **VerificationControl** 類型的顯示控制項需要 **VerificationCode** 。 您可以使用屬性 **ControlClaimType** 來指定所需的宣告所指定的 DisplayClaim。 例如：

```xml
<DisplayClaim ClaimTypeReferenceId="otpCode" ControlClaimType="VerificationCode" Required="true" />
```

### <a name="output-claims"></a>輸出宣告

顯示控制項的 **輸出宣告** 不會傳送到下一個協調流程步驟。 它們只會暫時儲存在目前的顯示控制項會話中。 您可以在相同顯示控制項的不同動作之間共用這些暫存宣告。

若要將輸出宣告升階到下一個協調流程步驟，請使用參考此顯示控制項之實際自我判斷技術設定檔的 **OutputClaims** 。

### <a name="display-control-actions"></a>顯示控制項動作

顯示控制項的 **動作** 就是當使用者在用戶端上執行特定動作時，會在 Azure AD B2C 後端執行的程式 (瀏覽器) 。 例如，當使用者選取頁面上的按鈕時所要執行的驗證。

動作會定義 **驗證技術設定檔** 的清單。 它們是用來驗證顯示控制項的部分或全部顯示宣告。 驗證技術設定檔會驗證使用者輸入，並可能會將錯誤傳回給使用者。 您可以在顯示控制項動作中使用 **ContinueOnError** 、 **ContinueOnSuccess** 和 **前置條件** ，類似于在自我判斷技術設定檔中的 [驗證技術配置](validation-technical-profile.md) 檔中使用它們的方式。

#### <a name="actions"></a>動作

**Actions** 元素包含下列元素：

| 元素 | 發生次數 | 描述 |
| ------- | ----------- | ----------- |
| 動作 | 1:n | 要執行的動作清單。 |

#### <a name="action"></a>動作

**Action** 元素包含下列屬性：

| 屬性 | 必要 | 描述 |
| --------- | -------- | ----------- |
| Id | 是 | 作業的類型。 可能的值：`SendCode` 或 `VerifyCode`。 此 `SendCode` 值會將程式碼傳送給使用者。 此動作可能包含兩個驗證技術設定檔：一個用來產生程式碼，另一個用於傳送程式碼。 `VerifyCode`值會驗證使用者在 [輸入] 文字方塊中輸入的程式碼。 |

**Action** 元素包含下列元素：

| 元素 | 發生次數 | 描述 |
| ------- | ----------- | ----------- |
| ValidationClaimsExchange | 1:1 | 技術設定檔的識別碼，可用來驗證參考技術設定檔的部分或所有顯示宣告。 參考技術設定檔的所有輸入宣告必須出現在參考技術設定檔的顯示宣告中。 |

#### <a name="validationclaimsexchange"></a>ValidationClaimsExchange

**ValidationClaimsExchange** 元素包含下列元素：

| 元素 | 發生次數 | 描述 |
| ------- | ----------- | ----------- |
| ValidationTechnicalProfile | 1:n | 一種技術設定檔，用來驗證參考技術設定檔的部分或所有顯示宣告。 |

**ValidationTechnicalProfile** 元素包含下列屬性：

| 屬性 | 必要 | 描述 |
| --------- | -------- | ----------- |
| ReferenceId | 是 | 已在原則或父原則中定義之技術設定檔的識別碼。 |
|ContinueOnError|否| 指出當此驗證技術設定檔引發錯誤時，是否應該繼續進行任何後續驗證技術設定檔的驗證。 可能的值： `true` 或 `false` (預設值，進一步驗證設定檔的處理將會停止，且) 會傳回錯誤。 |
|ContinueOnSuccess | 否 | 指出當此驗證技術設定檔成功時，是否要繼續驗證任何後續的驗證設定檔。 可能的值：`true` 或 `false`。 預設值是 `true`，表示進一步驗證設定檔的處理會繼續。 |

**ValidationTechnicalProfile** 元素包含下列元素：

| 元素 | 發生次數 | 描述 |
| ------- | ----------- | ----------- |
| 先決條件 | 0:1 | 必須滿足才能使驗證技術設定檔執行的先決條件清單。 |

**先決條件** 元素包含下列屬性：

| 屬性 | 必要 | 描述 |
| --------- | -------- | ----------- |
| `Type` | 是 | 要針對此先決條件執行的檢查或查詢類型。 可能的值：`ClaimsExist` 或 `ClaimEquals`。 `ClaimsExist` 指定如果指定的宣告存在於使用者目前的宣告集中，則應該執行動作。 `ClaimEquals` 指定如果指定的宣告存在且其值等於指定的值時，應執行動作。 |
| `ExecuteActionsIf` | 是 | 指出如果測試為 True 或 False，是否應執行先決條件中的動作。 |

**Precondition** 元素包含下列元素：

| 元素 | 發生次數 | 描述 |
| ------- | ----------- | ----------- |
| 值 | 1:n | 檢查所用的資料。 如果這項檢查的型別是 `ClaimsExist`，此欄位會指定要查詢的 ClaimTypeReferenceId。 如果檢查的型別是 `ClaimEquals`，此欄位會指定要查詢的 ClaimTypeReferenceId。 指定要在另一個值元素中檢查的值。|
| 動作 | 1:1 | 當協調流程步驟內的先決條件檢查為 True 時應採取的動作。 **動作** 的值設定為 `SkipThisValidationTechnicalProfile` ，指定不應該執行相關聯的驗證技術設定檔。 |

下列範例會使用 [AZURE AD SSPR 技術設定檔](aad-sspr-technical-profile.md)來傳送和驗證電子郵件地址。

```xml
<DisplayControl Id="emailVerificationControl" UserInterfaceControlType="VerificationControl">
  <InputClaims></InputClaims>
  <DisplayClaims>
    <DisplayClaim ClaimTypeReferenceId="email" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="verificationCode" ControlClaimType="VerificationCode" Required="true" />
  </DisplayClaims>
  <OutputClaims></OutputClaims>
  <Actions>
    <Action Id="SendCode">
      <ValidationClaimsExchange>
        <ValidationClaimsExchangeTechnicalProfile TechnicalProfileReferenceId="AadSspr-SendCode" />
      </ValidationClaimsExchange>
    </Action>
    <Action Id="VerifyCode">
      <ValidationClaimsExchange>
        <ValidationClaimsExchangeTechnicalProfile TechnicalProfileReferenceId="AadSspr-VerifyCode" />
      </ValidationClaimsExchange>
    </Action>
  </Actions>
</DisplayControl>
```

下列範例會根據使用者在 **mfaType** 宣告中選取的前置條件，在電子郵件或 SMS 中傳送程式碼。

```xml
<Action Id="SendCode">
  <ValidationClaimsExchange>
    <ValidationClaimsExchangeTechnicalProfile TechnicalProfileReferenceId="AzureMfa-SendSms">
      <Preconditions>
        <Precondition Type="ClaimEquals" ExecuteActionsIf="true">
          <Value>mfaType</Value>
          <Value>email</Value>
          <Action>SkipThisValidationTechnicalProfile</Action>
        </Precondition>
      </Preconditions>
    </ValidationClaimsExchangeTechnicalProfile>
    <ValidationClaimsExchangeTechnicalProfile TechnicalProfileReferenceId="AadSspr-SendEmail">
      <Preconditions>
        <Precondition Type="ClaimEquals" ExecuteActionsIf="true">
          <Value>mfaType</Value>
          <Value>phone</Value>
          <Action>SkipThisValidationTechnicalProfile</Action>
        </Precondition>
      </Preconditions>
    </ValidationClaimsExchangeTechnicalProfile>
  </ValidationClaimsExchange>
</Action>
```

## <a name="referencing-display-controls"></a>參考顯示控制項

顯示控制項是在[自我判斷技術設定檔](self-asserted-technical-profile.md)的[顯示宣告](self-asserted-technical-profile.md#display-claims)中參考。

例如：

```xml
<TechnicalProfile Id="SelfAsserted-ProfileUpdate">
  ...
  <DisplayClaims>
    <DisplayClaim DisplayControlReferenceId="emailVerificationControl" />
    <DisplayClaim DisplayControlReferenceId="PhoneVerificationControl" />
    <DisplayClaim ClaimTypeReferenceId="displayName" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="givenName" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="surName" Required="true" />
```

## <a name="next-steps"></a>後續步驟

如需使用顯示控制項的範例，請參閱： 

- [使用 Mailjet 進行自訂電子郵件驗證](custom-email-mailjet.md)
- [使用 SendGrid 進行自訂電子郵件驗證](custom-email-sendgrid.md)
