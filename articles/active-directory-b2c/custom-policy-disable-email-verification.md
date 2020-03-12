---
title: 使用自訂原則在客戶註冊期間停用電子郵件驗證
titleSuffix: Azure AD B2C
description: 瞭解如何在 Azure Active Directory B2C 的客戶註冊期間停用電子郵件驗證。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 8ec60f694000985f51db25db621e5814df62cdb3
ms.sourcegitcommit: f97d3d1faf56fb80e5f901cd82c02189f95b3486
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/11/2020
ms.locfileid: "79126806"
---
# <a name="disable-email-verification-during-customer-sign-up-using-a-custom-policy-in-azure-active-directory-b2c"></a>使用 Azure Active Directory B2C 中的自訂原則在客戶註冊期間停用電子郵件驗證

[!INCLUDE [disable email verification intro](../../includes/active-directory-b2c-disable-email-verification.md)]

## <a name="prerequisites"></a>Prerequisites

完成[開始使用自訂原則](custom-policy-get-started.md)中的步驟。 您應該擁有可運作的自訂原則，以便使用社交和本機帳戶註冊和登入。

## <a name="add-the-metadata-to-the-self-asserted-technical-profile"></a>將中繼資料新增至自我判斷技術設定檔

**LocalAccountSignUpWithLogonEmail**技術設定檔是[自我](self-asserted-technical-profile.md)判斷的，在註冊流程期間會叫用。 若要停用電子郵件驗證，請將 `EnforceEmailVerification` 中繼資料設定為 false。 覆寫擴充檔中的 LocalAccountSignUpWithLogonEmail 技術設定檔。 尋找 `ClaimsProviders` 元素。 將下列宣告提供者新增至 `ClaimsProviders` 元素：


```XML
<ClaimsProvider>
  <DisplayName>Local Account</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="SelfAsserted-LocalAccountSignin-Email">
      <Metadata>
        <Item Key="EnforceEmailVerification">false</Item>
      </Metadata>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="test-the-custom-policy"></a>測試自訂原則

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 請選取頂端功能表中的 [**目錄 + 訂**用帳戶] 篩選，然後選擇包含您 Azure AD 租使用者的目錄，以確定您使用的是包含 Azure AD 租使用者的目錄。
3. 選擇 Azure 入口網站左上角的 [所有服務]，然後搜尋並選取 [應用程式註冊]。
4. 選取 [識別體驗架構]。
5. 選取 [上傳自訂原則]，然後上傳您所變更的兩個原則檔案。
2. 選取您上傳的註冊或登入原則，按一下 [立即執行] 按鈕。
3. 您應該可以使用電子郵件地址進行註冊，而不需要驗證。


## <a name="next-steps"></a>後續步驟

- 若要深入瞭解[自我判斷技術設定檔](self-asserted-technical-profile.md)，請參閱 IEF 參考。
