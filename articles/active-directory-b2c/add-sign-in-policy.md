---
title: 設定登入流程
titleSuffix: Azure Active Directory B2C
description: 瞭解如何在 Azure Active Directory B2C 中設定登入流程。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 01/12/2021
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 2956f0ffb562214477249da3198ebbe42ef9bb45
ms.sourcegitcommit: 52e3d220565c4059176742fcacc17e857c9cdd02
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/21/2021
ms.locfileid: "98660364"
---
# <a name="set-up-a-sign-in-flow-in-azure-active-directory-b2c"></a>在 Azure Active Directory B2C 中設定登入流程

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

## <a name="sign-in-flow-overview"></a>登入流程總覽

登入原則可讓使用者： 

* 使用者可以使用 Azure AD B2C 本機帳戶登入
* 使用社交帳戶註冊或登入
* 密碼重設
* 使用者無法註冊 Azure AD B2C 本機帳戶-若要建立帳戶，系統管理員可以使用 [MS 圖形 API](microsoft-graph-operations.md)。

![設定檔編輯流程](./media/add-sign-in-policy/sign-in-user-flow.png)

## <a name="prerequisites"></a>必要條件

如果您尚未這麼做，請 [在 Azure Active Directory B2C 中註冊 web 應用程式](tutorial-register-applications.md)。

::: zone pivot="b2c-user-flow"

## <a name="create-a-sign-in-user-flow"></a>建立登入使用者流程

若要新增登入原則：

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 在入口網站工具列中選取 **目錄 + 訂用帳戶** 圖示，然後選取包含 Azure AD B2C 租用戶的目錄。
1. 在 Azure 入口網站中，搜尋並選取 [Azure AD B2C]。
1. 在 [原則] 底下，選取 [使用者流程]，然後選取 [新使用者流程]。
1. 在 [ **建立使用者流程** ] 頁面上，選取 [登 **入** 使用者流程]。
1. 在 [選取版本] 底下，選取 [建議]，然後選取 [建立]。 ([深入了解](user-flow-versions.md)使用者流程版本。)
1. 輸入使用者流程的 [名稱]。 例如，*signupsignin1*。
1. 針對身分 **識別提供者**，選取 [ **電子郵件登入**]。
1. 針對 [ **應用程式宣告**]，選擇您想要傳送至應用程式的宣告和屬性。 例如，選取 [**顯示更多**]，然後選擇 [**顯示名稱** **]、[****姓氏**] 和 [**使用者的物件識別碼**] 的 [屬性] 和 [宣告]。 按一下 [確定]。
1. 按一下 [建立]  新增使用者流程。 名稱前面會自動加上前置詞 *B2C_1*。

### <a name="test-the-user-flow"></a>測試使用者流程

1. 選取您建立的使用者流程來開啟其 [概觀] 頁面，然後選取 [執行使用者流程]。
1. 針對 [應用程式]，選取您先前註冊名為 *webapp1* 的 Web 應用程式。 **Reply URL** 應顯示 `https://jwt.ms`。
1. 按一下 [執行使用者流程]。
1. 您應該能夠使用您 (使用 MS 圖形 API) 建立的帳戶登入，而不需要註冊連結。 傳回的權杖包含您所選取的宣告。

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="remove-the-sign-up-link"></a>移除註冊連結

**SelfAsserted-LocalAccountSignin-電子郵件** 技術設定檔是 [自我](self-asserted-technical-profile.md)判斷提示，可在註冊或登入流程期間叫用。 若要移除註冊連結，請將 `setting.showSignupLink` 中繼資料設定為 `false` 。 覆寫延伸模組檔案中的 SelfAsserted-LocalAccountSignin-電子郵件技術設定檔。 

1. 開啟您原則的擴充檔。 例如，_`SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`**_。
1. 尋找 `ClaimsProviders` 元素。 如果此元素不存在，請加以新增。
1. 將下列宣告提供者新增至 `ClaimsProviders` 元素：

    ```xml
    <ClaimsProvider>
      <DisplayName>Local Account</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="SelfAsserted-LocalAccountSignin-Email">
          <Metadata>
            <Item Key="setting.showSignupLink">false</Item>
          </Metadata>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

1. 在 `<BuildingBlocks>` 元素中，新增下列 [ContentDefinition](contentdefinitions.md) 以參考版本1.2.0 或較新的資料 URI：

    ```XML
    <ContentDefinitions>
     <ContentDefinition Id="api.localaccountsignup">
        <DataUri>urn:com:microsoft:aad:b2c:elements:contract:unifiedssp:1.2.0</DataUri>
      </ContentDefinition>
    </ContentDefinitions>
    ```

## <a name="update-and-test-your-policy"></a>更新和測試您的原則

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 選取頂端功能表中的 [目錄 + 訂用帳戶] 篩選，然後選擇包含您 Azure AD 租用戶的目錄，以確定您使用的是包含 Azure AD 租用戶的目錄。
1. 選擇 Azure 入口網站左上角的 [所有服務]，然後搜尋並選取 [應用程式註冊]。
1. 選取 [識別體驗架構]。
1. 選取 [上傳自訂原則]，然後上傳您所變更的兩個原則檔案。
1. 選取您上傳的登入原則，然後按一下 [ **立即執行** ] 按鈕。
1. 您應該能夠使用您 (使用 MS 圖形 API) 建立的帳戶登入，而不需要註冊連結。

::: zone-end

## <a name="next-steps"></a>下一步

* 新增 [具有社交識別提供者](add-identity-provider.md)的登入。
* 設定 [密碼重設流程](add-password-reset-policy.md)。
