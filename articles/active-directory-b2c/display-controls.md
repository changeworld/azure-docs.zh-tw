---
title: 顯示控制參考
titleSuffix: Azure AD B2C
description: Azure AD B2C 顯示控制項的參考。 使用顯示控制項自訂自訂策略中定義的使用者旅程。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 12/10/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 4998fb19e42e123edd57bfcf10931d594ac4cb44
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78188727"
---
# <a name="display-controls"></a>顯示控制項

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

**顯示控制項**是具有特殊功能並與 Azure 活動目錄 B2C （Azure AD B2C） 後端服務交互的使用者介面元素。 它允許使用者在後端調用[驗證技術設定檔](validation-technical-profile.md)的頁面上執行操作。 顯示控制項顯示在頁面上，並由[自斷言的技術設定檔](self-asserted-technical-profile.md)引用。

下圖演示了一個帶有兩個顯示控制項的自斷言註冊頁，該控制項驗證主電子郵件地址和輔助電子郵件地址。

![渲染顯示控制項示例](media/display-controls/display-control-email.png)

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

## <a name="prerequisites"></a>Prerequisites

 在[自斷言技術設定檔](self-asserted-technical-profile.md)的["中繼資料"](self-asserted-technical-profile.md#metadata)部分中，引用[的內容定義](contentdefinitions.md)需要設置為`DataUri`頁面協定版本 2.0.0 或更高版本。 例如：

```XML
<ContentDefinition Id="api.selfasserted">
  <LoadUri>~/tenant/default/selfAsserted.cshtml</LoadUri>
  <RecoveryUri>~/common/default_page_error.html</RecoveryUri>
  <DataUri>urn:com:microsoft:aad:b2c:elements:selfasserted:2.0.0</DataUri>
  ...
```

## <a name="defining-display-controls"></a>定義顯示控制項

**DisplayControl**元素包含以下屬性：

| 屬性 | 必要 | 描述 |
| --------- | -------- | ----------- |
| Id | 是 | 用於顯示控制項的識別碼。 可以[引用](#referencing-display-controls)它。 |
| 使用者介面控制類型 | 是 | 顯示控制項的類型。 當前支援的是[驗證控制](display-control-verification.md) |

**DisplayControl**元素包含以下元素：

| 元素 | 發生次數 | 描述 |
| ------- | ----------- | ----------- |
| InputClaims | 0:1 | **輸入聲明**用於預填充要從使用者收集的聲明的值。 |
| 顯示索賠 | 0:1 | **顯示聲明**用於表示要從使用者收集的聲明。 |
| OutputClaims | 0:1 | **輸出聲明**用於表示將暫時保存為此**顯示控制**的聲明。 |
| 動作 | 0:1 | **操作**用於列出用於調用前端發生的使用者操作的驗證技術設定檔。 |

### <a name="input-claims"></a>輸入宣告

在顯示控制項中，可以使用**InputClaims**元素預填充要從頁面上的使用者收集的聲明的值。 任何**輸入要求轉換**都可以在引用此顯示控制項的自斷言技術設定檔中定義。

以下示例預填充要驗證的電子郵件地址與已存在的位址。

```XML
<DisplayControl Id="emailControl" UserInterfaceControlType="VerificationControl">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="emailAddress" />
  </InputClaims>
  ...
```

### <a name="display-claims"></a>顯示聲明

每種類型的顯示控制項都需要一組不同的顯示聲明、[輸出聲明](#output-claims)[和要](#display-control-actions)執行的操作。

與[自斷言技術設定檔](self-asserted-technical-profile.md#display-claims)中定義的**顯示聲明**類似，顯示聲明表示要從顯示控制項內的使用者收集的聲明。 引用**的 ClaimType**元素需要為 Azure AD B2C 支援的使用者輸入類型指定**使用者輸入類型**，如`TextBox``DropdownSingleSelect`或 。 如果**Action**需要顯示聲明值，則將 **"必需"** 屬性`true`設置為 以強制使用者為該特定顯示聲明提供值。

某些類型的顯示控制需要某些顯示聲明。 例如，**驗證碼**是**驗證控制**類型的顯示控制所必需的。 使用屬性**ControlClaimType**指定為所需索賠指定的顯示索賠。 例如：

```XML
<DisplayClaim ClaimTypeReferenceId="otpCode" ControlClaimType="VerificationCode" Required="true" />
```

### <a name="output-claims"></a>輸出宣告

顯示控制項的**輸出聲明**不會發送到下一個業務流程步驟。 它們僅暫時保存為當前顯示控制會話。 這些臨時聲明可以在同一顯示控制項的不同操作之間共用。

要將輸出聲明冒泡到下一個業務流程步驟，請使用引用此顯示控制項的實際自斷言技術設定檔的 **"輸出聲明**"。

### <a name="display-control-actions"></a>顯示控制操作

顯示**控制項的操作是**當使用者在用戶端（瀏覽器）上執行特定操作時，Azure AD B2C 後端中發生的過程。 例如，當使用者選擇頁面上的按鈕時要執行的驗證。

操作定義**驗證技術設定檔**的清單。 它們用於驗證顯示控制項的部分或全部顯示聲明。 驗證技術設定檔驗證使用者輸入，並可能將錯誤返回給使用者。 您可以在顯示控制操作中使用 **"繼續OnError"、"****繼續操作成功****"和"先決條件"，** 類似于在自斷言技術設定檔中[驗證技術設定檔](validation-technical-profile.md)中所使用的方式。

下面的示例根據使用者選擇**mfaType**聲明在電子郵件或 SMS 中發送代碼。

```XML
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

## <a name="referencing-display-controls"></a>引用顯示控制項

顯示控制項在[自斷言技術設定檔](self-asserted-technical-profile.md)的[顯示聲明](self-asserted-technical-profile.md#display-claims)中引用。

例如：

```XML
<TechnicalProfile Id="SelfAsserted-ProfileUpdate">
  ...
  <DisplayClaims>
    <DisplayClaim DisplayControlReferenceId="emailVerificationControl" />
    <DisplayClaim DisplayControlReferenceId="PhoneVerificationControl" />
    <DisplayClaim ClaimTypeReferenceId="displayName" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="givenName" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="surName" Required="true" />
```
