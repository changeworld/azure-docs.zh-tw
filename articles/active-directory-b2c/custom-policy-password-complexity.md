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
ms.date: 12/13/2018
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 2de1130e28b5071913e4cf3632c3fe4407597a98
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/29/2020
ms.locfileid: "78189135"
---
# <a name="configure-password-complexity-using-custom-policies-in-azure-active-directory-b2c"></a>在 Azure Active Directory B2C 中使用自訂原則來設定密碼複雜度

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

在 Azure Active Directory B2C （Azure AD B2C）中，您可以設定使用者建立帳戶時所提供之密碼的複雜性需求。 根據預設，Azure AD B2C 是使用**強式**密碼。 本文說明如何在[自訂原則](custom-policy-overview.md)中設定密碼複雜度。 此外，您也可以在[使用者流程](user-flow-password-complexity.md)中設定密碼複雜度。

## <a name="prerequisites"></a>必要條件

完成[在 Active Directory B2C 中開始使用自訂原則](custom-policy-get-started.md)中的步驟。

## <a name="add-the-elements"></a>新增元素

1. 複製與入門套件一起下載的 *SignUpOrSignIn.xml* 檔案，並將其命名為 *SingUpOrSignInPasswordComplexity.xml*。
2. 開啟 *SingUpOrSignInPasswordComplexity.xml* 檔案，然後將 **PolicyId** 和 **PublicPolicyUri** 變更成新的原則名稱。 例如 *B2C_1A_signup_signin_password_complexity*。
3. 新增下列識別碼為 **和** 的 `newPassword`ClaimType`reenterPassword` 元素：

    ```XML
    <ClaimsSchema>
      <ClaimType Id="newPassword">
        <InputValidationReference Id="PasswordValidation" />
      </ClaimType>
      <ClaimType Id="reenterPassword">
        <InputValidationReference Id="PasswordValidation" />
      </ClaimType>
    </ClaimsSchema>
    ```

4. [Predicates](predicates.md) 具有 `IsLengthRange` 或 `MatchesRegex` 方法類型。 `MatchesRegex` 類型可用來比對規則運算式。 `IsLengthRange` 類型可接受最小和最大字串長度。 將含有下列 **Predicate** 元素的 **Predicates** 元素 (如果尚未存在) 新增至 **BuildingBlocks** 元素：

    ```XML
    <Predicates>
      <Predicate Id="PIN" Method="MatchesRegex" HelpText="The password must be a pin.">
        <Parameters>
          <Parameter Id="RegularExpression">^[0-9]+$</Parameter>
        </Parameters>
      </Predicate>
      <Predicate Id="Length" Method="IsLengthRange" HelpText="The password must be between 8 and 16 characters.">
        <Parameters>
          <Parameter Id="Minimum">8</Parameter>
          <Parameter Id="Maximum">16</Parameter>
        </Parameters>
      </Predicate>
    </Predicates>
    ```

5. 每個 **InputValidation** 元素都是使用已定義的 **Predicate** 元素來建構的。 此元素可讓您執行類似於 `and` 和 `or`的布林值彙總。 將含有下列 **InputValidation** 元素的 **InputValidations** 元素 (如果尚未存在) 新增至 **BuildingBlocks** 元素：

    ```XML
    <InputValidations>
      <InputValidation Id="PasswordValidation">
        <PredicateReferences Id="LengthGroup" MatchAtLeast="1">
          <PredicateReference Id="Length" />
        </PredicateReferences>
        <PredicateReferences Id="3of4" MatchAtLeast="3" HelpText="You must have at least 3 of the following character classes:">
          <PredicateReference Id="Lowercase" />
          <PredicateReference Id="Uppercase" />
          <PredicateReference Id="Number" />
          <PredicateReference Id="Symbol" />
        </PredicateReferences>
      </InputValidation>
    </InputValidations>
    ```

6. 確定 **PolicyProfile** 技術設定檔包含下列元素：

    ```XML
    <RelyingParty>
      <DefaultUserJourney ReferenceId="SignUpOrSignIn"/>
      <TechnicalProfile Id="PolicyProfile">
        <DisplayName>PolicyProfile</DisplayName>
        <Protocol Name="OpenIdConnect"/>
        <InputClaims>
          <InputClaim ClaimTypeReferenceId="passwordPolicies" DefaultValue="DisablePasswordExpiration, DisableStrongPassword"/>
        </InputClaims>
        <OutputClaims>
          <OutputClaim ClaimTypeReferenceId="displayName"/>
          <OutputClaim ClaimTypeReferenceId="givenName"/>
          <OutputClaim ClaimTypeReferenceId="surname"/>
          <OutputClaim ClaimTypeReferenceId="email"/>
          <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
        </OutputClaims>
        <SubjectNamingInfo ClaimType="sub"/>
      </TechnicalProfile>
    </RelyingParty>
    ```

7. 儲存原則檔案。

## <a name="test-your-policy"></a>測試您的原則

在 Azure AD B2C 中測試應用程式時，將 Azure AD B2C 權杖傳回到 `https://jwt.ms` 以檢閱其中的宣告很有用。

### <a name="upload-the-files"></a>上傳檔案

1. 登入 [Azure 入口網站](https://portal.azure.com/)。
2. 選取頂端功能表中的 [目錄 + 訂用帳戶] 篩選，然後選擇包含您租用戶的目錄，以確定您使用的是包含 Azure AD B2C 租用戶的目錄。
3. 選擇 Azure 入口網站左上角的 [所有服務]，然後搜尋並選取 [Azure AD B2C]。
4. 選取 [識別體驗架構]。
5. 在 [自訂原則] 頁面上，按一下 [上傳原則]。
6. 選取 [覆寫現有的原則]，然後搜尋並選取 *SingUpOrSignInPasswordComplexity.xml*檔案。
7. 按一下 [上傳]。

### <a name="run-the-policy"></a>執行原則

1. 開啟您所變更的原則。 例如 *B2C_1A_signup_signin_password_complexity*。
2. 針對**應用程式**，請選取您先前註冊的應用程式。 若要查看權杖，[回覆 URL] 應該顯示 `https://jwt.ms`。
3. 按一下 [立即執行]。
4. 選取 [立即註冊]，輸入電子郵件地址，然後輸入新密碼。 系統會顯示有關密碼限制的指引。 完成使用者資訊輸入，然後按一下 [建立]。 您應該會看到傳回的權杖內容。

## <a name="next-steps"></a>後續步驟

- 了解如何[在 Azure Active Directory B2C 中使用自訂原則來設定密碼變更](custom-policy-password-change.md)。


