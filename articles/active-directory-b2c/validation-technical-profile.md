---
title: 定義自訂原則中的驗證技術設定檔
titleSuffix: Azure AD B2C
description: 在 Azure Active Directory B2C 的自訂原則中使用驗證技術設定檔來驗證宣告。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 65a2eab05e7c475431602d9c2d3fc44b59bbc8f7
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/29/2020
ms.locfileid: "78185721"
---
# <a name="define-a-validation-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>定義 Azure Active Directory B2C 自訂原則中的驗證技術設定檔

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

驗證技術設定檔是任何通訊協定的一般技術設定檔，例如 [Azure Active Directory](active-directory-technical-profile.md) 或 [REST API](restful-technical-profile.md)。 驗證技術設定檔會使用下列資料傳回輸出宣告，或傳回 HTTP 409 錯誤訊息 (衝突回應狀態碼)：

```JSON
{
    "version": "1.0.0",
    "status": 409,
    "userMessage": "Your error message"
}
```

從驗證技術設定檔傳回的宣告會新增至宣告包。 在下一個驗證技術設定檔中，您可以使用這些宣告。

驗證技術設定檔會在出現於 **ValidationTechnicalProfiles** 元素的序列中執行。 您可以在驗證技術設定檔中設定，如果驗證技術設定檔引發錯誤或成功，任何後續驗證技術設定檔的執行是否應該繼續。

驗證技術設定檔可以有條件地按照 **ValidationTechnicalProfile** 元素中定義的先決條件執行。 例如，您可以檢查特定宣告是否存在，或宣告是否等於指定的值時才要執行協調流程步驟。

自我判斷的驗證技術設定檔可定義驗證技術設定檔用於驗證部分或全部輸出宣告。 參考驗證技術設定檔的所有輸入宣告必須出現在參考技術設定檔的輸出宣告中。

> [!NOTE]
> 只有自我判斷技術設定檔可以使用驗證技術設定檔。 如果您需要驗證來自非自我判斷技術設定檔的輸出宣告，請考慮在使用者旅程圖中使用額外的協調流程步驟，以配合驗證所需的技術設定檔。

## <a name="validationtechnicalprofiles"></a>ValidationTechnicalProfiles

**ValidationTechnicalProfiles** 元素包含下列元素：

| 元素 | 發生次數 | 描述 |
| ------- | ----------- | ----------- |
| ValidationTechnicalProfile | 1:n | 技術設定檔可用於驗證參考技術設定檔的部分或所有輸出宣告。 |

**ValidationTechnicalProfile** 元素包含下列屬性：

| 屬性 | 必要項 | 描述 |
| --------- | -------- | ----------- |
| ReferenceId | 是 | 已在原則或父原則中定義之技術設定檔的識別碼。 |
|ContinueOnError|否| 指出當此驗證技術設定檔引發錯誤時，是否應該繼續進行任何後續驗證技術設定檔的驗證。 可能的值：`true` 或 `false` (預設值，進一步驗證設定檔的處理將停止，並傳回錯誤)。 |
|ContinueOnSuccess | 否 | 表示如果此驗證設定檔成功，任何後續驗證技術設定檔的驗證是否應該繼續。 可能的值：`true` 或 `false`。 預設值是 `true`，表示進一步驗證設定檔的處理會繼續。 |

**ValidationTechnicalProfile** 元素包含下列元素：

| 元素 | 發生次數 | 描述 |
| ------- | ----------- | ----------- |
| 先決條件 | 0:1 | 必須滿足才能使驗證技術設定檔執行的先決條件清單。 |

**Precondition** 元素包含下列屬性：

| 屬性 | 必要項 | 描述 |
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
