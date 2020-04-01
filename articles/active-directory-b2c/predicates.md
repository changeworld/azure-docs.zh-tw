---
title: Predicates 與 PredicateValidations
titleSuffix: Azure AD B2C
description: 通過使用 Azure 活動目錄 B2C 中的自訂策略,防止將格式不正確的數據添加到 Azure AD B2C 租戶。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/30/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 887c9432f04cce775e045bb6da83f0af4a4a4bce
ms.sourcegitcommit: 632e7ed5449f85ca502ad216be8ec5dd7cd093cb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/30/2020
ms.locfileid: "80396895"
---
# <a name="predicates-and-predicatevalidations"></a>Predicates 與 PredicateValidations

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

**謂詞**和**謂詞驗證**元素使您能夠執行驗證過程,以確保僅將格式正確的數據輸入 Azure 活動目錄 B2C (Azure AD B2C) 租戶。

下圖顯示元素之間的關聯性：

![顯示謂詞與謂詞驗證關係的圖表](./media/predicates/predicates.png)

## <a name="predicates"></a>述詞

**Predicate** 元素會定義基本驗證來檢查宣告類型的值，並傳回 `true` 或 `false`。 您可以使用指定的 **Method** 元素及與該方法相關聯的一組 **Parameter** 元素來完成驗證。 例如，述詞可以檢查字串宣告值的長度是否介於所指定 Minimum 和 Maximum 參數的範圍內，或者字串宣告值是否包含字元集。 **UserHelpText** 元素會在檢查失敗時，為使用者提供錯誤訊息。 **UserHelpText** 元素的值可以使用[語言自訂](localization.md)進行當地語系化。

**謂詞**元素必須直接出現在[構建塊](buildingblocks.md)元素中的 **「聲明架構**」元素之後。

**Predicates** 元素包含下列元素：

| 元素 | 發生次數 | 描述 |
| ------- | ----------- | ----------- |
| Predicate | 1:n | 述詞清單。 |

**Predicate** 元素包含下列屬性：

| 屬性 | 必要 | 描述 |
| --------- | -------- | ----------- |
| Id | 是 | 要用於述詞的識別碼。 其他元素可以在原則中使用這個識別碼。 |
| 方法 | 是 | 要用於驗證的方法類型。 可能的值：[IsLengthRange](#islengthrange)、[MatchesRegex](#matchesregex)、[IncludesCharacters](#includescharacters) 或 [IsDateRange](#isdaterange)。  |
| HelpText | 否 | 檢查失敗時提供給使用者的錯誤訊息。 此字串可以使用[語言自訂](localization.md)進行當地語系化。 |

**Predicate** 元素包含下列元素：

| 元素 | 發生次數 | 描述 |
| ------- | ----------- | ----------- |
| UserHelpText | 0:1 | (已棄用)如果檢查失敗,則給使用者發送錯誤消息。 |
| 參數 | 1:1 | 適用於字串驗證方法類型的參數。 |

**Parameters** 元素包含下列元素：

| 元素 | 發生次數 | 描述 |
| ------- | ----------- | ----------- |
| 參數 | 1:n | 適用於字串驗證方法類型的參數。 |

**Parameter** 元素包含下列屬性：

| 元素 | 發生次數 | 描述 |
| ------- | ----------- | ----------- |
| Id | 1:1 | 參數的識別碼。 |

### <a name="predicate-methods"></a>謂詞方法

#### <a name="islengthrange"></a>是長度範圍

IsLengthRange 方法檢查字串聲明值的長度是否在指定的最小參數和最大參數範圍內。 謂字元素支援以下參數:

| 參數 | 必要 | 描述 |
| ------- | ----------- | ----------- |
| 最大值 | 是 | 可輸入的最大字元數。 |
| 最小值 | 是 | 必須輸入的最小字元數。 |


下面的範例顯示了具有參數`Minimum``Maximum`並指定字串長度範圍的 IsLengthRange 方法:

```XML
<Predicate Id="IsLengthBetween8And64" Method="IsLengthRange" HelpText="The password must be between 8 and 64 characters.">
  <Parameters>
    <Parameter Id="Minimum">8</Parameter>
    <Parameter Id="Maximum">64</Parameter>
  </Parameters>
</Predicate>
```

#### <a name="matchesregex"></a>符合雷格ex

MatchesRegex 方法檢查字串聲明值是否與正則表達式匹配。 謂字元素支援以下參數:

| 參數 | 必要 | 描述 |
| ------- | ----------- | ----------- |
| RegularExpression | 是 | 要比對的規則運算式模式。 |

下列範例顯示 `MatchesRegex` 方法，以及可指定規則運算式的 `RegularExpression` 參數：

```XML
<Predicate Id="PIN" Method="MatchesRegex" HelpText="The password must be numbers only.">
  <Parameters>
    <Parameter Id="RegularExpression">^[0-9]+$</Parameter>
  </Parameters>
</Predicate>
```

#### <a name="includescharacters"></a>包括字元

"包括字元"方法檢查字串聲明值是否包含字元集。 謂字元素支援以下參數:

| 參數 | 必要 | 描述 |
| ------- | ----------- | ----------- |
| CharacterSet | 是 | 可以輸入的字元集。 例如,小寫`a-z`字元、大寫字元`A-Z`、`0-9`數位或符號清單(`@#$%^&amp;*\-_+=[]{}|\\:',?/~"();!`如 )。 |

下列範例顯示 `IncludesCharacters` 方法，以及可指定字元集的 `CharacterSet` 參數：

```XML
<Predicate Id="Lowercase" Method="IncludesCharacters" HelpText="a lowercase letter">
  <Parameters>
    <Parameter Id="CharacterSet">a-z</Parameter>
  </Parameters>
</Predicate>
```

#### <a name="isdaterange"></a>是日期

IsDateRange 方法檢查日期聲明值是否介於指定的最小參數和最大參數範圍之間。 謂字元素支援以下參數:

| 參數 | 必要 | 描述 |
| ------- | ----------- | ----------- |
| 最大值 | 是 | 可以輸入的最大可能日期。 日期的格式遵循`yyyy-mm-dd`慣例`Today`或 。 |
| 最小值 | 是 | 可以輸入的最小日期。 日期的格式遵循`yyyy-mm-dd`慣例`Today`或 。|

下列範例顯示 `IsDateRange` 方法，以及可使用 `yyyy-mm-dd` 和 `Today` 格式來指定日期範圍的 `Minimum` 和 `Maximum` 參數。

```XML
<Predicate Id="DateRange" Method="IsDateRange" HelpText="The date must be between 1970-01-01 and today.">
  <Parameters>
    <Parameter Id="Minimum">1970-01-01</Parameter>
    <Parameter Id="Maximum">Today</Parameter>
  </Parameters>
</Predicate>
```

## <a name="predicatevalidations"></a>PredicateValidations

當述詞定義驗證以針對宣告類型進行檢查時，**PredicateValidations** 會群組一組述詞，以形成可套用至宣告類型的使用者輸入驗證。 每個 **PredicateValidation** 元素均包含一組 **PredicateGroup** 元素，其中包含一組指向 **Predicate** 的 **PredicateReference** 元素。 若要通過驗證，宣告的值應該在所有的 **PredicateGroup** 下方，使用它們的 **PredicateReference** 元素組來傳遞任何述詞的所有測試。

**謂詞驗證**元素必須直接出現在[構建塊](buildingblocks.md)元素中的**謂詞**元素之後。

```XML
<PredicateValidations>
  <PredicateValidation Id="">
    <PredicateGroups>
      <PredicateGroup Id="">
        <UserHelpText></UserHelpText>
        <PredicateReferences MatchAtLeast="">
          <PredicateReference Id="" />
          ...
        </PredicateReferences>
      </PredicateGroup>
      ...
    </PredicateGroups>
  </PredicateValidation>
...
</PredicateValidations>
```

**PredicateValidations** 元素包含下列元素：

| 元素 | 發生次數 | 描述 |
| ------- | ----------- | ----------- |
| PredicateValidation | 1:n | 述詞驗證清單。 |

**PredicateValidation** 元素包含下列屬性：

| 屬性 | 必要 | 描述 |
| --------- | -------- | ----------- |
| Id | 是 | 要用於述詞驗證的識別碼。 **ClaimType** 元素可以在原則中使用這個識別碼。 |

**PredicateValidation** 元素包含下列元素：

| 元素 | 發生次數 | 描述 |
| ------- | ----------- | ----------- |
| PredicateGroups | 1:n | 述詞群組清單。 |

**PredicateGroups** 元素包含下列元素：

| 元素 | 發生次數 | 描述 |
| ------- | ----------- | ----------- |
| PredicateGroup | 1:n | 述詞清單。 |

**PredicateGroup** 元素包含下列屬性：

| 屬性 | 必要 | 描述 |
| --------- | -------- | ----------- |
| Id | 是 | 要用於述詞群組的識別碼。  |

**PredicateGroup** 元素包含下列元素：

| 元素 | 發生次數 | 描述 |
| ------- | ----------- | ----------- |
| UserHelpText | 0:1 |  述詞的說明，有助於使用者了解他們應輸入的值。 |
| PredicateReferences | 1:n | 述詞參考清單。 |

**PredicateReferences** 元素包含下列屬性：

| 屬性 | 必要 | 描述 |
| --------- | -------- | ----------- |
| MatchAtLeast | 否 | 指定值至少必須符合許多述詞定義，以用於要接受的輸入。 如果未指定,該值必須匹配所有謂詞定義。 |

**PredicateReferences** 元素包含下列元素：

| 元素 | 發生次數 | 描述 |
| ------- | ----------- | ----------- |
| PredicateReference | 1:n | 對述詞的參考。 |

**PredicateReference** 元素包含下列屬性：

| 屬性 | 必要 | 描述 |
| --------- | -------- | ----------- |
| Id | 是 | 要用於述詞驗證的識別碼。  |


## <a name="configure-password-complexity"></a>設定密碼複雜度

利用 **Predicates** 和 **PredicateValidationsInput**，您可以控制使用者在建立帳戶時所提供密碼的複雜度需求。 根據預設，Azure AD B2C 會使用強式密碼。 Azure AD B2C 也支援組態選項，可控制客戶可以使用的密碼複雜度。 您可以使用這些述詞元素來定義密碼複雜度：

- 使用 `IsLengthRange` 方法的 **IsLengthBetween8And64**，驗證密碼長度必須介於 8 到 64 個字元之間。
- 使用 `IncludesCharacters` 方法的 **Lowercase**，驗證密碼包含小寫字母。
- 使用 `IncludesCharacters` 方法的 **Uppercase**，驗證密碼包含大寫字母。
- 使用 `IncludesCharacters` 方法的 **Number**，驗證密碼包含數字。
- **Symbol**使用方法`IncludesCharacters`的符號驗證密碼是否包含多個符號字元之一。
- 使用 `MatchesRegex` 方法的 **PIN**，驗證密碼只包含數字。
- 使用 `MatchesRegex` 方法的 **AllowedAADCharacters**，驗證提供了只對密碼無效的字元。
- 使用 `MatchesRegex` 方法的 **DisallowedWhitespace**，驗證密碼不是以空白字元開始或結尾。

```XML
<Predicates>
  <Predicate Id="IsLengthBetween8And64" Method="IsLengthRange" HelpText="The password must be between 8 and 64 characters.">
    <Parameters>
      <Parameter Id="Minimum">8</Parameter>
      <Parameter Id="Maximum">64</Parameter>
    </Parameters>
  </Predicate>

  <Predicate Id="Lowercase" Method="IncludesCharacters" HelpText="a lowercase letter">
    <Parameters>
      <Parameter Id="CharacterSet">a-z</Parameter>
    </Parameters>
  </Predicate>

  <Predicate Id="Uppercase" Method="IncludesCharacters" HelpText="an uppercase letter">
    <Parameters>
      <Parameter Id="CharacterSet">A-Z</Parameter>
    </Parameters>
  </Predicate>

  <Predicate Id="Number" Method="IncludesCharacters" HelpText="a digit">
    <Parameters>
      <Parameter Id="CharacterSet">0-9</Parameter>
    </Parameters>
  </Predicate>

  <Predicate Id="Symbol" Method="IncludesCharacters" HelpText="a symbol">
    <Parameters>
      <Parameter Id="CharacterSet">@#$%^&amp;*\-_+=[]{}|\\:',.?/`~"();!</Parameter>
    </Parameters>
  </Predicate>

  <Predicate Id="PIN" Method="MatchesRegex" HelpText="The password must be numbers only.">
    <Parameters>
      <Parameter Id="RegularExpression">^[0-9]+$</Parameter>
    </Parameters>
  </Predicate>

  <Predicate Id="AllowedAADCharacters" Method="MatchesRegex" HelpText="An invalid character was provided.">
    <Parameters>
      <Parameter Id="RegularExpression">(^([0-9A-Za-z\d@#$%^&amp;*\-_+=[\]{}|\\:',?/`~"();! ]|(\.(?!@)))+$)|(^$)</Parameter>
    </Parameters>
  </Predicate>

  <Predicate Id="DisallowedWhitespace" Method="MatchesRegex" HelpText="The password must not begin or end with a whitespace character.">
    <Parameters>
      <Parameter Id="RegularExpression">(^\S.*\S$)|(^\S+$)|(^$)</Parameter>
    </Parameters>
  </Predicate>
```

當您定義基本驗證之後，可將它們組合在一起，然後建立一組您可在原則中使用的密碼原則：

- **SimplePassword** 會驗證 DisallowedWhitespace、AllowedAADCharacters 及 IsLengthBetween8And64
- **StrongPassword** 會驗證 DisallowedWhitespace、AllowedAADCharacters、IsLengthBetween8And64。 最後一個群組 `CharacterClasses` 會搭配設定為 3 的 `MatchAtLeast` 來執行一組額外的述詞。 使用者密碼長度必須介於 8 到 16 個字元之間，並具備下列其中三個字元：小寫、大寫、數字或符號。
- **CustomPassword** 只會驗證 DisallowedWhitespace、AllowedAADCharacters。 因此，只要字元有效，使用者就能提供任意長度的任何密碼。

```XML
<PredicateValidations>
  <PredicateValidation Id="SimplePassword">
    <PredicateGroups>
      <PredicateGroup Id="DisallowedWhitespaceGroup">
        <PredicateReferences>
          <PredicateReference Id="DisallowedWhitespace" />
        </PredicateReferences>
      </PredicateGroup>
      <PredicateGroup Id="AllowedAADCharactersGroup">
        <PredicateReferences>
          <PredicateReference Id="AllowedAADCharacters" />
        </PredicateReferences>
      </PredicateGroup>
      <PredicateGroup Id="LengthGroup">
        <PredicateReferences>
          <PredicateReference Id="IsLengthBetween8And64" />
        </PredicateReferences>
      </PredicateGroup>
    </PredicateGroups>
  </PredicateValidation>

  <PredicateValidation Id="StrongPassword">
    <PredicateGroups>
      <PredicateGroup Id="DisallowedWhitespaceGroup">
        <PredicateReferences>
          <PredicateReference Id="DisallowedWhitespace" />
       </PredicateReferences>
      </PredicateGroup>
      <PredicateGroup Id="AllowedAADCharactersGroup">
        <PredicateReferences>
          <PredicateReference Id="AllowedAADCharacters" />
        </PredicateReferences>
      </PredicateGroup>
      <PredicateGroup Id="LengthGroup">
        <PredicateReferences>
          <PredicateReference Id="IsLengthBetween8And64" />
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

  <PredicateValidation Id="CustomPassword">
    <PredicateGroups>
      <PredicateGroup Id="DisallowedWhitespaceGroup">
        <PredicateReferences>
          <PredicateReference Id="DisallowedWhitespace" />
        </PredicateReferences>
      </PredicateGroup>
      <PredicateGroup Id="AllowedAADCharactersGroup">
        <PredicateReferences>
          <PredicateReference Id="AllowedAADCharacters" />
        </PredicateReferences>
      </PredicateGroup>
    </PredicateGroups>
  </PredicateValidation>
</PredicateValidations>
```

在您的宣告類型中，新增 **PredicateValidationReference** 元素，並指定識別碼作為其中一個述詞驗證，例如 SimplePassword、StrongPassword 或 CustomPassword。

```XML
<ClaimType Id="password">
  <DisplayName>Password</DisplayName>
  <DataType>string</DataType>
  <AdminHelpText>Enter password</AdminHelpText>
  <UserHelpText>Enter password</UserHelpText>
  <UserInputType>Password</UserInputType>
  <PredicateValidationReference Id="StrongPassword" />
</ClaimType>
```

以下顯示當 Azure AD B2C 顯示錯誤訊息時組織元素的方式：

![謂詞和謂片語密碼複雜性圖示例](./media/predicates/predicates-pass.png)

## <a name="configure-a-date-range"></a>設定日期範圍

利用 **Predicates** 和 **PredicateValidations** 元素，您可以使用 `DateTimeDropdown` 來控制 **UserInputType** 的最小和最大日期值。 若要這樣做，請使用 `IsDateRange` 方法來建立 **Predicate**，並提供 Minimum 和 Maximum 參數。

```XML
<Predicates>
  <Predicate Id="DateRange" Method="IsDateRange" HelpText="The date must be between 01-01-1980 and today.">
    <Parameters>
      <Parameter Id="Minimum">1980-01-01</Parameter>
      <Parameter Id="Maximum">Today</Parameter>
    </Parameters>
  </Predicate>
</Predicates>
```

使用對 `DateRange` 述詞的參考來新增 **PredicateValidation**。

```XML
<PredicateValidations>
  <PredicateValidation Id="CustomDateRange">
    <PredicateGroups>
      <PredicateGroup Id="DateRangeGroup">
        <PredicateReferences>
          <PredicateReference Id="DateRange" />
        </PredicateReferences>
      </PredicateGroup>
    </PredicateGroups>
  </PredicateValidation>
</PredicateValidations>
```

在您的宣告類型中，新增 **PredicateValidationReference** 元素，並將識別碼指定為 `CustomDateRange`。

```XML
<ClaimType Id="dateOfBirth">
  <DisplayName>Date of Birth</DisplayName>
  <DataType>date</DataType>
  <AdminHelpText>The user's date of birth.</AdminHelpText>
  <UserHelpText>Your date of birth.</UserHelpText>
  <UserInputType>DateTimeDropdown</UserInputType>
  <PredicateValidationReference Id="CustomDateRange" />
</ClaimType>
 ```

## <a name="next-steps"></a>後續步驟

- 瞭解如何使用[Azure 活動目錄 B2C 中的自訂策略](custom-policy-password-complexity.md)使用謂詞驗證配置密碼複雜性。