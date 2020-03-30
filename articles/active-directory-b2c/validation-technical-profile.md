---
title: 在自訂策略中定義驗證技術設定檔
titleSuffix: Azure AD B2C
description: 在 Azure 活動目錄 B2C 中的自訂策略中使用驗證技術設定檔驗證聲明。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/16/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 1eaf159149bb353b1cf0474aad5bc233decddc5c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79481563"
---
# <a name="define-a-validation-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>定義 Azure Active Directory B2C 自訂原則中的驗證技術設定檔

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

驗證技術設定檔是任何通訊協定的一般技術設定檔，例如 [Azure Active Directory](active-directory-technical-profile.md) 或 [REST API](restful-technical-profile.md)。 驗證技術設定檔返回輸出聲明，或返回 4xx HTTP 狀態碼，並帶有以下資料。 有關詳細資訊，請參閱[返回錯誤訊息](restful-technical-profile.md#returning-error-message)

```JSON
{
    "version": "1.0.0",
    "status": 409,
    "userMessage": "Your error message"
}
```

驗證技術設定檔的輸出聲明的範圍僅限於調用驗證技術[設定檔的自斷言技術設定檔](self-asserted-technical-profile.md)及其驗證技術設定檔。 如果要在下一個業務流程步驟中使用輸出聲明，請將輸出聲明添加到調用驗證技術設定檔的自斷言技術設定檔中。

驗證技術設定檔會在出現於 **ValidationTechnicalProfiles** 元素的序列中執行。 您可以在驗證技術設定檔中設定，如果驗證技術設定檔引發錯誤或成功，任何後續驗證技術設定檔的執行是否應該繼續。

驗證技術設定檔可以有條件地按照 **ValidationTechnicalProfile** 元素中定義的先決條件執行。 例如，可以檢查是否存在特定聲明，或者聲明是否與指定值相等。

自我判斷的驗證技術設定檔可定義驗證技術設定檔用於驗證部分或全部輸出宣告。 參考驗證技術設定檔的所有輸入宣告必須出現在參考技術設定檔的輸出宣告中。

> [!NOTE]
> 只有自斷言的技術設定檔才能使用驗證技術設定檔。 如果需要驗證來自非自斷言技術設定檔的輸出聲明，請考慮在使用者旅程中使用額外的業務流程步驟來適應負責驗證的技術設定檔。

## <a name="validationtechnicalprofiles"></a>ValidationTechnicalProfiles

**ValidationTechnicalProfiles** 元素包含下列元素：

| 元素 | 發生次數 | 描述 |
| ------- | ----------- | ----------- |
| ValidationTechnicalProfile | 1:n | 技術設定檔可用於驗證參考技術設定檔的部分或所有輸出宣告。 |

**ValidationTechnicalProfile** 元素包含下列屬性：

| 屬性 | 必要 | 描述 |
| --------- | -------- | ----------- |
| ReferenceId | 是 | 已在原則或父原則中定義之技術設定檔的識別碼。 |
|ContinueOnError|否| 指示如果此驗證技術設定檔引發錯誤，是否應繼續驗證任何後續驗證技術設定檔。 可能的值：`true` 或 `false` (預設值，進一步驗證設定檔的處理將停止，並傳回錯誤)。 |
|ContinueOnSuccess | 否 | 表示如果此驗證設定檔成功，任何後續驗證技術設定檔的驗證是否應該繼續。 可能的值：`true` 或 `false`。 預設值是 `true`，表示進一步驗證設定檔的處理會繼續。 |

**ValidationTechnicalProfile** 元素包含下列元素：

| 元素 | 發生次數 | 描述 |
| ------- | ----------- | ----------- |
| 先決條件 | 0:1 | 必須滿足才能使驗證技術設定檔執行的先決條件清單。 |

**Precondition** 元素包含下列屬性：

| 屬性 | 必要 | 描述 |
| --------- | -------- | ----------- |
| `Type` | 是 | 要針對此先決條件執行的檢查或查詢類型。 `ClaimsExist` 指定如果指定的宣告存在於使用者目前的宣告組中，就應執行動作，或 `ClaimEquals` 指定如果指定宣告存在且其值等於指定的值，就應執行動作。 |
| `ExecuteActionsIf` | 是 | 指出如果測試為 True 或 False，是否應執行先決條件中的動作。 |

**Precondition** 元素包含下列元素：

| 元素 | 發生次數 | 描述 |
| ------- | ----------- | ----------- |
| 值 | 1:n | 檢查所用的資料。 如果這項檢查的型別是 `ClaimsExist`，此欄位會指定要查詢的 ClaimTypeReferenceId。 如果檢查的型別是 `ClaimEquals`，此欄位會指定要查詢的 ClaimTypeReferenceId。 而另一個值元素包含要檢查的值。|
| 動作 | 1:1 | 當協調流程步驟內的先決條件檢查為 True 時應採取的動作。 **動作**的值是設定為 `SkipThisValidationTechnicalProfile`。 指定應該不執行相關聯的驗證技術設定檔。 |

### <a name="example"></a>範例

下列範例會使用這些驗證技術設定檔：

1. 第一個驗證技術設定檔會檢查使用者認證，如果發生錯誤，例如使用者名稱無效或密碼不正確，則不會繼續。
2. 如果 userType 宣告不存在，或 userType 值是 `Partner`，則下一個驗證技術設定檔不會執行。 驗證技術設定檔是嘗試讀取內部客戶資料庫中的使用者設定檔，而且，如果發生錯誤，例如無法使用 REST API 服務或任何內部錯誤，仍會繼續讀取。
3. 如果 userType 宣告不存在，或 userType 值是 `Customer`，則最後一個驗證技術設定檔不會執行。 驗證技術設定檔是嘗試讀取內部夥伴資料庫中的使用者設定檔，而且，如果發生錯誤，例如無法使用 REST API 服務或任何內部錯誤，仍會繼續讀取。

```XML
<ValidationTechnicalProfiles>
  <ValidationTechnicalProfile ReferenceId="login-NonInteractive" ContinueOnError="false" />
  <ValidationTechnicalProfile ReferenceId="REST-ReadProfileFromCustomertsDatabase" ContinueOnError="true" >
    <Preconditions>
      <Precondition Type="ClaimsExist" ExecuteActionsIf="false">
        <Value>userType</Value>
        <Action>SkipThisValidationTechnicalProfile</Action>
      </Precondition>
      <Precondition Type="ClaimEquals" ExecuteActionsIf="true">
        <Value>userType</Value>
        <Value>Partner</Value>
        <Action>SkipThisValidationTechnicalProfile</Action>
      </Precondition>
    </Preconditions>
  </ValidationTechnicalProfile>
  <ValidationTechnicalProfile ReferenceId="REST-ReadProfileFromPartnersDatabase" ContinueOnError="true" >
    <Preconditions>
      <Precondition Type="ClaimsExist" ExecuteActionsIf="false">
        <Value>userType</Value>
        <Action>SkipThisValidationTechnicalProfile</Action>
      </Precondition>
      <Precondition Type="ClaimEquals" ExecuteActionsIf="true">
        <Value>userType</Value>
        <Value>Customer</Value>
        <Action>SkipThisValidationTechnicalProfile</Action>
      </Precondition>
    </Preconditions>
  </ValidationTechnicalProfile>
</ValidationTechnicalProfiles>
```
