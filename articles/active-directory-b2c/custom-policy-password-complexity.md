---
title: 使用自訂原則設定密碼複雜度
titleSuffix: Azure AD B2C
description: 如何在 Azure Active Directory B2C 中使用自訂原則來設定密碼複雜度需求。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/10/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: b16790e288f6569f08ce14e5a7c751bbd8083faf
ms.sourcegitcommit: d322d0a9d9479dbd473eae239c43707ac2c77a77
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/12/2020
ms.locfileid: "79138429"
---
# <a name="configure-password-complexity-using-custom-policies-in-azure-active-directory-b2c"></a>在 Azure Active Directory B2C 中使用自訂原則來設定密碼複雜度

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

在 Azure Active Directory B2C （Azure AD B2C）中，您可以設定使用者建立帳戶時所提供之密碼的複雜性需求。 根據預設，Azure AD B2C 是使用**強式**密碼。 本文說明如何在[自訂原則](custom-policy-overview.md)中設定密碼複雜度。 此外，您也可以在[使用者流程](user-flow-password-complexity.md)中設定密碼複雜度。

## <a name="prerequisites"></a>必要條件

完成[開始使用自訂原則](custom-policy-get-started.md)中的步驟。 您應該有一個使用本機帳戶來註冊和登入的有效自訂原則。


## <a name="add-the-elements"></a>新增元素

若要設定密碼複雜性，請覆寫 `newPassword`，並使用述詞[驗證](predicates.md#predicatevalidations)的參考來 `reenterPassword` 宣告[類型](claimsschema.md)。 PredicateValidations 元素會將一組述詞分組，以構成可套用至宣告類型的使用者輸入驗證。 開啟原則的擴充檔案。 例如， <em>`SocialAndLocalAccounts/` **`TrustFrameworkExtensions.xml`** </em>。

1. 搜尋 [BuildingBlocks](buildingblocks.md) 元素。 如果此元素不存在，請加以新增。
1. 找出[ClaimsSchema](claimsschema.md)元素。 如果此元素不存在，請加以新增。
1. 將 `newPassword` 和 `reenterPassword` 宣告加入至**ClaimsSchema**元素。

    ```XML
    <ClaimType Id="newPassword">
      <PredicateValidationReference Id="CustomPassword" />
    </ClaimType>
    <ClaimType Id="reenterPassword">
      <PredicateValidationReference Id="CustomPassword" />
    </ClaimType>
    ```

1. 述詞[會定義基本](predicates.md)身份驗證來檢查宣告類型的值，並傳回 true 或 false。 驗證是透過使用指定的 method 元素，以及與方法相關的一組參數來完成。 將下列述詞新增至**BuildingBlocks**元素，緊接在 `</ClaimsSchema>` 元素的結尾之後：

    ```XML
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

1. 將下列述詞驗證新增至**BuildingBlocks**元素，緊接在 `</Predicates>` 元素的結尾之後：

    ```XML
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

1. 下列技術設定檔是[Active Directory 的技術設定檔](active-directory-technical-profile.md)，可將資料讀取和寫入至 Azure Active Directory。 覆寫延伸模組檔案中的這些技術設定檔。 使用 `PersistedClaims` 來停用強式密碼原則。 尋找 **ClaimsProviders** 元素。  新增下列宣告提供者，如下所示：

    ```XML
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

1. 儲存原則檔案。

## <a name="test-your-policy"></a>測試您的原則

### <a name="upload-the-files"></a>上傳檔案

1. 登入 [Azure 入口網站](https://portal.azure.com/)。
2. 選取頂端功能表中的 [目錄 + 訂用帳戶] 篩選，然後選擇包含您租用戶的目錄，以確定您使用的是包含 Azure AD B2C 租用戶的目錄。
3. 選擇 Azure 入口網站左上角的 [所有服務]，然後搜尋並選取 [Azure AD B2C]。
4. 選取 [識別體驗架構]。
5. 在 [自訂原則] 頁面上，按一下 [上傳原則]。
6. **如果原則存在**，請選取 [覆寫]，然後搜尋並選取*TrustFrameworkExtensions*檔案。
7. 按一下 [上傳]。

### <a name="run-the-policy"></a>執行原則

1. 開啟註冊或登入原則。 例如，*B2C_1A_signup_signin*。
2. 針對**應用程式**，請選取您先前註冊的應用程式。 若要查看權杖，[回覆 URL] 應該顯示 `https://jwt.ms`。
3. 按一下 [立即執行]。
4. 選取 [立即註冊]，輸入電子郵件地址，然後輸入新密碼。 系統會顯示有關密碼限制的指引。 完成使用者資訊輸入，然後按一下 [建立]。 您應該會看到傳回的權杖內容。

## <a name="next-steps"></a>後續步驟

- 了解如何[在 Azure Active Directory B2C 中使用自訂原則來設定密碼變更](custom-policy-password-change.md)。
- 深入瞭解 IEF 參考中的述[詞和](predicates.md) [PredicateValidations](predicates.md#predicatevalidations)元素。
