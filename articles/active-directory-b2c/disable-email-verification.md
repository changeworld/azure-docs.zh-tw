---
title: 在客戶註冊期間停用電子郵件驗證
titleSuffix: Azure AD B2C
description: 瞭解如何在 Azure Active Directory B2C 中的客戶註冊期間停用電子郵件驗證。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 12/10/2020
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 0f70c8d501a7d56f4bc29e0f2b065760cad625e5
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/16/2020
ms.locfileid: "97585015"
---
# <a name="disable-email-verification-during-customer-sign-up-in-azure-active-directory-b2c"></a>在 Azure Active Directory B2C 中的客戶註冊期間停用電子郵件驗證

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

根據預設，Azure Active Directory B2C (Azure AD B2C) 會針對使用電子郵件地址或使用者名稱 (註冊的使用者，驗證客戶的本機帳戶電子郵件地址) 帳戶。 Azure AD B2C 在註冊程式期間要求客戶驗證電子郵件地址，以確保有效的電子郵件地址。 它也可防止惡意執行者在您的應用程式中使用自動化進程產生詐騙帳戶。

有些應用程式開發人員偏好在註冊程式期間略過電子郵件驗證，而改為讓客戶在稍後驗證其電子郵件地址。 為支援此方式，您可以將 Azure AD B2C 設定為停用電子郵件驗證。 這樣做會建立更流暢的註冊程式，並讓開發人員有彈性地區分已驗證其電子郵件地址的客戶，而不是來自不是的客戶。

> [!WARNING]
> 在註冊程序期間停用電子郵件驗證可能會導致收到垃圾郵件。 如果您停用預設的 Azure AD B2C 提供的電子郵件驗證，建議您執行取代驗證系統。

## <a name="prerequisites"></a>必要條件

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]
## <a name="disable-email-verification"></a>停用電子郵件驗證

::: zone pivot="b2c-user-flow"

請遵循下列步驟來停用電子郵件驗證：

1. 登入 [Azure 入口網站](https://portal.azure.com)
1. 使用上方功能表中的 [ **目錄 + 訂** 用帳戶] 篩選，來選取包含您 Azure AD B2C 租使用者的目錄。
1. 在左側功能表中，選取 [Azure AD B2C]。 或者，選取 [所有服務]，然後搜尋並選取 [Azure AD B2C]。
1. 選取 [使用者流程]。
1. 選取您要停用電子郵件驗證的使用者流程。 例如， *B2C_1_signinsignup*。
1. 選取 [ **頁面配置**]。
1. 選取 **本機帳戶註冊頁面**。
1. 在 [ **使用者屬性**] 下，選取 [ **電子郵件地址**]。
1. 在 [ **需要驗證** ] 下拉式清單中，選取 [ **否**]。
1. 選取 [儲存]。 此使用者流程的電子郵件驗證現在已停用。

::: zone-end

::: zone pivot="b2c-custom-policy"
**LocalAccountSignUpWithLogonEmail** 技術設定檔是 [自我](self-asserted-technical-profile.md)判斷提示，可在註冊流程期間叫用。 若要停用電子郵件驗證，請將 `EnforceEmailVerification` 中繼資料設定為 false。 覆寫延伸模組檔案中的 LocalAccountSignUpWithLogonEmail 技術設定檔。 

1. 開啟您原則的擴充檔。 例如，<em>`SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`**</em>。
1. 尋找 `ClaimsProviders` 元素。 如果此元素不存在，請加以新增。
1. 將下列宣告提供者新增至 `ClaimsProviders` 元素：

```xml
<ClaimsProvider>
  <DisplayName>Local Account</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
      <Metadata>
        <Item Key="EnforceEmailVerification">false</Item>
      </Metadata>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```
::: zone-end

::: zone pivot="b2c-user-flow"

## <a name="test-your-policy"></a>測試您的原則 

1. 登入 [Azure 入口網站](https://portal.azure.com)
1. 使用上方功能表中的 [ **目錄 + 訂** 用帳戶] 篩選，來選取包含您 Azure AD B2C 租使用者的目錄。
1. 在左側功能表中，選取 [Azure AD B2C]。 或者，選取 [所有服務]，然後搜尋並選取 [Azure AD B2C]。
1. 選取 [使用者流程]。
1. 選取您要停用電子郵件驗證的使用者流程。 例如， *B2C_1_signinsignup*。
1. 若要測試您的原則，請選取 [ **執行使用者流程**]。
1. 針對 [ **應用程式**]，選取您先前註冊的 web 應用程式（名為 *testapp1-pre-production* ）。 **Reply URL** 應顯示 `https://jwt.ms`。
1. 按一下 [**執行使用者流程**]
1. 您應該能夠使用電子郵件地址進行註冊，而不需要進行驗證。

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="update-and-test-the-relying-party-file"></a>更新並測試信賴憑證者檔案

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 選取頂端功能表中的 [目錄 + 訂用帳戶] 篩選，然後選擇包含您 Azure AD 租用戶的目錄，以確定您使用的是包含 Azure AD 租用戶的目錄。
1. 選擇 Azure 入口網站左上角的 [所有服務]，然後搜尋並選取 [應用程式註冊]。
1. 選取 [識別體驗架構]。
1. 選取 [上傳自訂原則]，然後上傳您所變更的兩個原則檔案。
1. 選取您上傳的註冊或登入原則，按一下 [立即執行] 按鈕。
1. 您應該能夠使用電子郵件地址進行註冊，而不需要進行驗證。

::: zone-end


## <a name="next-steps"></a>後續步驟

- 瞭解如何 [在 Azure Active Directory B2C 中自訂使用者介面](customize-ui-with-html.md)

