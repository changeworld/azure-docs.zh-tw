---
title: 使用顯示控制項驗證聲明
titleSuffix: Azure AD B2C
description: 瞭解如何使用 Azure AD B2C 顯示控制項來驗證自訂策略提供的使用者旅程中的聲明。
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78188778"
---
# <a name="verification-display-control"></a>驗證顯示控制

使用驗證[顯示控制項](display-controls.md)驗證索賠，例如電子郵件地址或電話號碼，並將驗證碼發送給使用者。

## <a name="verificationcontrol-actions"></a>驗證控制操作

驗證顯示控制項由兩個步驟（操作）組成：

1. 向使用者請求目標，如應向其發送驗證碼的電子郵件地址或電話號碼。 當使用者選擇"**發送代碼"** 按鈕時，將執行驗證顯示控制項的 **"發送代碼操作**"。 **SendCode 操作**生成代碼，構造要發送的內容，並將其發送給使用者。 可以預填充位址的值，並作為第二因素身份驗證。

    ![發送代碼操作的示例頁](media/display-control-verification/display-control-verification-email-action-01.png)

1. 發送代碼後，使用者讀取消息，將驗證碼輸入顯示控制項提供的控制項，然後選擇**驗證碼**。 通過選擇 **"驗證代碼**"，將執行**驗證代碼操作**以驗證與位址關聯的代碼。 如果使用者選擇 **"發送新代碼**"，則再次執行第一個操作。

    ![驗證代碼操作的示例頁](media/display-control-verification/display-control-verification-email-action-02.png)

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

## <a name="verificationcontrol-required-elements"></a>驗證控制所需元素

**驗證控制**必須包含以下元素：

- 的類型`DisplayControl`是`VerificationControl`。
- `DisplayClaims`
  - **發送到**- 一個或多個聲明，指定將驗證碼發送到何處。 例如，*電子郵件*或*國家/地區代碼*和*電話號碼*。
  - **驗證碼**- 使用者在發送代碼後提供的驗證碼聲明。 此聲明必須根據需要設置，`ControlClaimType`並且必須設置為`VerificationCode`。
- 輸出聲明（可選）在使用者完成驗證過程後返回到自斷言頁。 例如，*電子郵件*或*國家/地區代碼*和*電話號碼*。 自斷言的技術設定檔使用聲明來保留資料或將輸出聲明冒泡到下一個業務流程步驟。
- 兩`Action`個 s，名稱如下：
  - **發送代碼**- 向使用者發送代碼。 此操作通常包含兩個驗證技術設定檔，用於生成代碼併發送代碼。
  - **驗證代碼**- 驗證代碼。 此操作通常包含單個驗證技術設定檔。

在下面的示例中，頁面上將顯示一**個電子郵件**文字方塊。 當使用者輸入其電子郵件地址並選擇**SendCode**時，**發送代碼**操作在 Azure AD B2C 後端觸發。

然後，使用者輸入**驗證碼**並選擇**驗證碼**以在後端觸發**驗證碼**操作。 如果所有驗證都通過，**則驗證控制**將被視為已完成，使用者可以繼續執行下一步。

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
