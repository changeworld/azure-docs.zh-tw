---
title: 使用自訂策略配置密碼複雜性
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79138429"
---
# <a name="configure-password-complexity-using-custom-policies-in-azure-active-directory-b2c"></a>在 Azure Active Directory B2C 中使用自訂原則來設定密碼複雜度

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

在 Azure 活動目錄 B2C（Azure AD B2C）中，您可以配置使用者在創建帳戶時提供的密碼的複雜性要求。 根據預設，Azure AD B2C 是使用**強式**密碼。 本文說明如何在[自訂原則](custom-policy-overview.md)中設定密碼複雜度。 此外，您也可以在[使用者流程](user-flow-password-complexity.md)中設定密碼複雜度。

## <a name="prerequisites"></a>Prerequisites

完成[開始使用自訂原則](custom-policy-get-started.md)中的步驟。 您應該有一個使用本機帳戶來註冊和登入的有效自訂原則。


## <a name="add-the-elements"></a>新增元素

要配置密碼複雜性，請用引用[謂詞驗證](predicates.md#predicatevalidations)來覆蓋`newPassword`和`reenterPassword`[聲明類型](claimsschema.md)。 謂詞驗證元素對一組謂詞進行分組，以形成可應用於聲明類型的使用者輸入驗證。 打開策略的擴展檔。 例如， <em> `SocialAndLocalAccounts/` </em>.

1. 搜尋 [BuildingBlocks](buildingblocks.md) 元素。 如果此元素不存在，請加以新增。
1. 找到[聲明架構](claimsschema.md)元素。 如果此元素不存在，請加以新增。
1. 將`newPassword`和`reenterPassword`聲明添加到**聲明架構**元素。

    ```XML
    <ClaimType Id="newPassword">
      <PredicateValidationReference Id="CustomPassword" />
    </ClaimType>
    <ClaimType Id="reenterPassword">
      <PredicateValidationReference Id="CustomPassword" />
    </ClaimType>
    ```

1. [謂詞](predicates.md)定義基本驗證以檢查聲明類型的值並返回 true 或 false。 驗證通過使用指定的方法元素和一組與方法相關的參數來完成。 在`</ClaimsSchema>`元素關閉後立即將以下謂詞添加到**構建塊**元素：

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

1. 在`</Predicates>`元素關閉後立即將以下謂詞驗證添加到**構建塊**元素：

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

1. 以下技術設定檔是[活動目錄技術設定檔](active-directory-technical-profile.md)，用於讀取資料並將其寫入 Azure 活動目錄。 覆蓋擴展檔中的這些技術設定檔。 用於`PersistedClaims`禁用強密碼原則。 尋找 **ClaimsProviders** 元素。  添加以下聲明提供程式，如下所示：

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

1. 登錄到 Azure[門戶](https://portal.azure.com/)。
2. 選取頂端功能表中的 [目錄 + 訂用帳戶]**** 篩選，然後選擇包含您租用戶的目錄，以確定您使用的是包含 Azure AD B2C 租用戶的目錄。
3. 選擇 Azure 入口網站左上角的 [所有服務]****，然後搜尋並選取 [Azure AD B2C]****。
4. 選取 [識別體驗架構]****。
5. 在 [自訂原則] 頁面上，按一下 [上傳原則]****。
6. 選擇 **"覆蓋策略（如果存在），** 然後搜索並選擇*TrustFramework 擴展.xml*檔。
7. 按一下 [上傳]****。

### <a name="run-the-policy"></a>執行原則

1. 打開註冊或登錄策略。 例如，*B2C_1A_signup_signin*。
2. 針對**應用程式**，請選取您先前註冊的應用程式。 若要查看權杖，[回覆 URL]**** 應該顯示 `https://jwt.ms`。
3. 按一下 [立即執行] ****。
4. 選取 [立即註冊]****，輸入電子郵件地址，然後輸入新密碼。 系統會顯示有關密碼限制的指引。 完成使用者資訊輸入，然後按一下 [建立]****。 您應該會看到傳回的權杖內容。

## <a name="next-steps"></a>後續步驟

- 了解如何[在 Azure Active Directory B2C 中使用自訂原則來設定密碼變更](custom-policy-password-change.md)。
- 詳細瞭解 IEF 引用中的[謂詞](predicates.md)和[謂詞驗證](predicates.md#predicatevalidations)元素。
