---
title: 使用顯示控制項驗證宣告
titleSuffix: Azure AD B2C
description: 瞭解如何在您的自訂原則所提供的使用者旅程中，使用 Azure AD B2C 顯示控制項來驗證宣告。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 12/10/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: ff2a8ad05e26ea31fc8100d4000171313881f4e1
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "78188778"
---
# <a name="verification-display-control"></a>驗證顯示控制項

使用驗證[顯示控制項](display-controls.md)來驗證宣告，例如電子郵件地址或電話號碼，以及傳送給使用者的驗證碼。

## <a name="verificationcontrol-actions"></a>VerificationControl 動作

驗證顯示控制項包含兩個步驟（動作）：

1. 向使用者要求應傳送驗證碼的目的地，例如電子郵件地址或電話號碼。 當使用者選取 [**傳送程式碼**] 按鈕時，會執行驗證顯示控制項的 [ **SendCode] 動作**。 **SendCode 動作**會產生程式碼、建立要傳送的內容，並將它傳送給使用者。 位址的值可以預先填入，並做為第二因素驗證。

    ![傳送程式碼動作的範例頁面](media/display-control-verification/display-control-verification-email-action-01.png)

1. 在傳送程式碼之後，使用者會讀取訊息、將驗證碼輸入顯示控制項所提供的控制項中，然後選取 [**驗證代碼**]。 藉由選取 [**驗證程式代碼**]，會執行**VerifyCode 動作**以驗證與該位址相關聯的程式碼。 如果使用者選取 [**傳送新程式碼**]，則會再次執行第一個動作。

    ![驗證程式代碼動作的範例頁面](media/display-control-verification/display-control-verification-email-action-02.png)

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

## <a name="verificationcontrol-required-elements"></a>VerificationControl 必要元素

**VerificationControl**必須包含下列元素：

- 的類型`DisplayControl`為`VerificationControl`。
- `DisplayClaims`
  - **傳送到**-一或多個宣告，指定要將驗證碼傳送至何處。 例如，*電子郵件*或*國家/地區代碼*和*電話號碼*。
  - **驗證碼**-在傳送程式碼之後，使用者所提供的驗證碼宣告。 此宣告必須設定為必要，且`ControlClaimType`必須設定為。 `VerificationCode`
- 在使用者完成驗證程式之後，要傳回自我判斷頁的輸出宣告（選擇性）。 例如，*電子郵件*或*國家/地區代碼*和*電話號碼*。 自我判斷技術設定檔會使用宣告來保存資料，或將輸出宣告向上升到下一個協調流程步驟。
- 具有`Action`下列名稱的兩個：
  - **SendCode** -將程式碼傳送給使用者。 此動作通常包含兩個驗證技術設定檔，以產生程式碼並傳送它。
  - **VerifyCode** -驗證程式代碼。 此動作通常包含單一驗證技術設定檔。

在下列範例中，頁面上會顯示 [**電子郵件**] 文字方塊。 當使用者輸入其電子郵件地址並選取**SendCode**時，會在 Azure AD B2C 後端觸發**SendCode**動作。

然後，使用者輸入**verificationCode**並選取 [ **VerifyCode** ]，以在後端觸發**VerifyCode**動作。 如果所有驗證都通過，則會將**VerificationControl**視為完成，使用者可以繼續進行下一個步驟。

```XML
<DisplayControl Id="emailVerificationControl" UserInterfaceControlType="VerificationControl">
  <DisplayClaims>
    <DisplayClaim ClaimTypeReferenceId="email"  Required="true" />
    <DisplayClaim ClaimTypeReferenceId="verificationCode" ControlClaimType="VerificationCode" Required="true" />
  </DisplayClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="email" />
  </OutputClaims>
  <Actions>
    <Action Id="SendCode">
      <ValidationClaimsExchange>
        <ValidationClaimsExchangeTechnicalProfile TechnicalProfileReferenceId="GenerateOtp" />
        <ValidationClaimsExchangeTechnicalProfile TechnicalProfileReferenceId="SendGrid" />
      </ValidationClaimsExchange>
    </Action>
    <Action Id="VerifyCode">
      <ValidationClaimsExchange>
        <ValidationClaimsExchangeTechnicalProfile TechnicalProfileReferenceId="VerifyOtp" />
      </ValidationClaimsExchange>
    </Action>
  </Actions>
</DisplayControl>
```
