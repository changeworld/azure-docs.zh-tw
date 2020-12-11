---
title: 設定密碼複雜度需求
titleSuffix: Azure AD B2C
description: 如何設定 Azure Active Directory B2C 中取用者所提供的密碼複雜度需求。
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
ms.openlocfilehash: 0cd9f0d6cf9529439f7b5ce46b1a5807d0a68a7c
ms.sourcegitcommit: 6172a6ae13d7062a0a5e00ff411fd363b5c38597
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/11/2020
ms.locfileid: "97111165"
---
# <a name="configure-complexity-requirements-for-passwords-in-azure-active-directory-b2c"></a>設定 Azure Active Directory B2C 中的密碼複雜度需求

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

Azure Active Directory B2C (Azure AD B2C) 支援的變更密碼複雜度需求，是在建立帳戶時由使用者所提供的。 根據預設，Azure AD B2C 是使用 **強式** 密碼。 Azure AD B2C 也支援組態選項，可控制客戶可以使用的密碼複雜度。

## <a name="prerequisites"></a>Prerequisites

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

::: zone pivot="b2c-user-flow"

## <a name="password-rule-enforcement"></a>密碼規則強制執行

在註冊期間或密碼重設期間，使用者必須提供符合複雜度規則的密碼。 依每個使用者流程強制執行密碼複雜度規則。 在註冊期間，有一個使用者流程需要四位數的 pin，而另一個使用者流程在註冊期間需要八個字元的字串。 例如，您可能會針對成人的使用者流程與針對兒童的原則使用不同的密碼複雜度。

登入期間一律不會強制要求密碼複雜度。 系統一律不會在登入時提示使用者變更其密碼，因為它不符合目前的複雜度需求。

可以在以下類型的使用者流程類型中設定密碼複雜度：

- 註冊或登入使用者流程
- 密碼重設使用者流程

如果您使用自訂原則，您可以 ([在自訂原則中設定密碼複雜度](password-complexity.md))。

## <a name="configure-password-complexity"></a>設定密碼複雜度

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 在入口網站工具列中選取 **目錄 + 訂用帳戶** 圖示，然後選取包含 Azure AD B2C 租用戶的目錄。
3. 在 Azure 入口網站中，搜尋並選取 [Azure AD B2C]。
4. 選取 [使用者流程]。
2. 選取一個使用者流程，然後按一下 [屬性]。
3. 在 [密碼複雜度] 之下，將這個使用者流程的密碼複雜度變更為 [簡單]、[強式] 或 [自訂]。

### <a name="comparison-chart"></a>比較圖表

| 複雜度 | 說明 |
| --- | --- |
| 簡單 | 密碼至少為 8 到 64 個字元。 |
| 強式 | 密碼至少為 8 到 64 個字元。 它需要小寫字母、大寫字母、數字或符號 4 個之中的 3 個。 |
| 自訂 | 此選項會對密碼複雜度規則提供最多的控制權。  它允許設定自訂的長度。  它也可以接受僅限數字的密碼 (PIN)。 |

## <a name="custom-options"></a>自訂選項

### <a name="character-set"></a>字元集

可讓您只接受數字 (PIN) 或完整的字元集。

- **僅限數字** 在輸入密碼時只允許數字 (0-9)。
- **所有** 允許任何字母、數字或符號。

### <a name="length"></a>長度

可讓您控制密碼的長度要求。

- **最小長度** 必須至少為 4。
- **最大長度** 必須大於或等於最小長度，且最多可以有 64 個字元。

### <a name="character-classes"></a>字元類別

可讓您控制密碼中使用的不同字元類型。

- **4 個其中 2 個：小寫字元、大寫字元、數字 (0-9)、符號** 可確保密碼包含至少兩個字元類型。 例如，數字和小寫字元。
- **3 的4：小寫字元、大寫字元、數位 (0-9) ，符號** 可確保密碼包含至少三個字元類型。 例如，數字、小寫字元和大寫字元。
- **4 個其中 4 個：小寫字元、大寫字元、數字 (0-9)、符號** 可確保密碼包含所有的字元類型。

    > [!NOTE]
    > 要求 **4 個其中 4 個** 可能會導致使用者感到挫折。 一些根據研究顯示，這項需求並未改善密碼熵。 請參閱 [NIST 密碼指導方針](https://pages.nist.gov/800-63-3/sp800-63b.html#appA)

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="password-predicate-validation"></a>密碼述詞驗證

若要設定密碼複雜度，請以述詞驗證的參考覆寫 `newPassword` 和宣告 `reenterPassword` [類型](claimsschema.md)。 [](predicates.md#predicatevalidations) PredicateValidations 元素會將一組述詞分組，以形成可套用至宣告類型的使用者輸入驗證。 開啟您原則的擴充檔。 例如，<em>`SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`**</em>。

1. 搜尋 [BuildingBlocks](buildingblocks.md) 元素。 如果此元素不存在，請加以新增。
1. 尋找 [ClaimsSchema](claimsschema.md) (機器翻譯) 元素。 如果此元素不存在，請加以新增。
1. 將 `newPassword` 和 `reenterPassword` 宣告加入至 **ClaimsSchema** 元素。

    ```xml
    <ClaimType Id="newPassword">
      <PredicateValidationReference Id="CustomPassword" />
    </ClaimType>
    <ClaimType Id="reenterPassword">
      <PredicateValidationReference Id="CustomPassword" />
    </ClaimType>
    ```

1. 述詞[會定義基本](predicates.md)身份驗證來檢查宣告類型的值，並傳回 true 或 false。 您可以使用指定的方法專案，以及與此方法相關的一組參數來完成驗證。 將下列述詞加入至 **BuildingBlocks** 元素，緊接在元素的結尾之後 `</ClaimsSchema>` ：

    ```xml
    <Predicates>
      <Predicate Id="LengthRange" Method="IsLengthRange">
        <UserHelpText>The password must be between 6 and 64 characters.</UserHelpText>
        <Parameters>
          <Parameter Id="Minimum">6</Parameter>
          <Parameter Id="Maximum">64</Parameter>
        </Parameters>
      </Predicate>
      <Predicate Id="Lowercase" Method="IncludesCharacters">
        <UserHelpText>a lowercase letter</UserHelpText>
        <Parameters>
          <Parameter Id="CharacterSet">a-z</Parameter>
        </Parameters>
      </Predicate>
      <Predicate Id="Uppercase" Method="IncludesCharacters">
        <UserHelpText>an uppercase letter</UserHelpText>
        <Parameters>
          <Parameter Id="CharacterSet">A-Z</Parameter>
        </Parameters>
      </Predicate>
      <Predicate Id="Number" Method="IncludesCharacters">
        <UserHelpText>a digit</UserHelpText>
        <Parameters>
          <Parameter Id="CharacterSet">0-9</Parameter>
        </Parameters>
      </Predicate>
      <Predicate Id="Symbol" Method="IncludesCharacters">
        <UserHelpText>a symbol</UserHelpText>
        <Parameters>
          <Parameter Id="CharacterSet">@#$%^&amp;*\-_+=[]{}|\\:',.?/`~"();!</Parameter>
        </Parameters>
      </Predicate>
    </Predicates>
    ```

1. 將下列述詞驗證新增至 **BuildingBlocks** 元素，緊接在元素的結尾之後 `</Predicates>` ：

    ```xml
    <PredicateValidations>
      <PredicateValidation Id="CustomPassword">
        <PredicateGroups>
          <PredicateGroup Id="LengthGroup">
            <PredicateReferences MatchAtLeast="1">
              <PredicateReference Id="LengthRange" />
            </PredicateReferences>
          </PredicateGroup>
          <PredicateGroup Id="CharacterClasses">
            <UserHelpText>The password must have at least 3 of the following:</UserHelpText>
            <PredicateReferences MatchAtLeast="3">
              <PredicateReference Id="Lowercase" />
              <PredicateReference Id="Uppercase" />
              <PredicateReference Id="Number" />
              <PredicateReference Id="Symbol" />
            </PredicateReferences>
          </PredicateGroup>
        </PredicateGroups>
      </PredicateValidation>
    </PredicateValidations>
    ```

## <a name="disable-strong-password"></a>停用強式密碼 

下列技術設定檔是 [Active Directory 的技術設定檔](active-directory-technical-profile.md)，可讀取和寫入 Azure Active Directory 的資料。 覆寫延伸模組檔案中的這些技術設定檔。 使用 `PersistedClaims` 停用增強式密碼原則。 尋找 **ClaimsProviders** 元素。  新增下列宣告提供者，如下所示：

```xml
<ClaimsProvider>
  <DisplayName>Azure Active Directory</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="AAD-UserWriteUsingLogonEmail">
      <PersistedClaims>
        <PersistedClaim ClaimTypeReferenceId="passwordPolicies" DefaultValue="DisablePasswordExpiration, DisableStrongPassword"/>
      </PersistedClaims>
    </TechnicalProfile>
    <TechnicalProfile Id="AAD-UserWritePasswordUsingObjectId">
      <PersistedClaims>
        <PersistedClaim ClaimTypeReferenceId="passwordPolicies" DefaultValue="DisablePasswordExpiration, DisableStrongPassword"/>
      </PersistedClaims>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

儲存原則檔案。

## <a name="test-your-policy"></a>測試您的原則

### <a name="upload-the-files"></a>上傳檔案

1. 登入 [Azure 入口網站](https://portal.azure.com/)。
2. 選取頂端功能表中的 [目錄 + 訂用帳戶] 篩選，然後選擇包含您租用戶的目錄，以確定您使用的是包含 Azure AD B2C 租用戶的目錄。
3. 選擇 Azure 入口網站左上角的 [所有服務]，然後搜尋並選取 [Azure AD B2C]。
4. 選取 [識別體驗架構]。
5. 在 [自訂原則] 頁面上，按一下 [上傳原則]。
6. 選取 [ **覆寫原則（如果有** 的話）]，然後搜尋並選取 *TrustFrameworkExtensions.xml* 的檔案。
7. 按一下 [上傳] 。

### <a name="run-the-policy"></a>執行原則

1. 開啟註冊或登入原則。 例如，*B2C_1A_signup_signin*。
2. 針對 **應用程式**，請選取您先前註冊的應用程式。 若要查看權杖，[回覆 URL] 應該顯示 `https://jwt.ms`。
3. 按一下 [立即執行] 。
4. 選取 [立即註冊]，輸入電子郵件地址，然後輸入新密碼。 系統會顯示有關密碼限制的指引。 完成使用者資訊輸入，然後按一下 [建立]。 您應該會看到傳回的權杖內容。

## <a name="next-steps"></a>後續步驟

- 了解如何[在 Azure Active Directory B2C 中使用自訂原則來設定密碼變更](custom-policy-password-change.md)。
- 深入瞭解 IEF 參考中的述 [詞和](predicates.md) [PredicateValidations](predicates.md#predicatevalidations) 元素。


::: zone-end