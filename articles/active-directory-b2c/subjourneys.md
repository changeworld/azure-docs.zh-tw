---
title: Azure Active Directory B2C 中的 SubJourneys |Microsoft Docs
description: 在 Azure Active Directory B2C 中指定自訂原則的 SubJourneys 元素。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/15/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 6609dabe9bd507751bd131a4effe24295e2aac04
ms.sourcegitcommit: a2d8acc1b0bf4fba90bfed9241b299dc35753ee6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/12/2020
ms.locfileid: "91952438"
---
# <a name="subjourneys"></a>SubJourneys

Subjourneys 可以用來組織和簡化使用者旅程圖中的協調流程步驟流程。 [使用者旅程圖](userjourneys.md)會指定明確的路徑，原則可透過這類路徑來讓信賴憑證者應用程式取得使用者所需的宣告。 使用者會透過這些路徑來擷取要呈現給信賴憑證者的宣告。 換句話說，使用者旅程圖會定義當 Azure AD B2C 識別體驗架構處理要求時，使用者要經歷的商務邏輯。 使用者旅程圖會表示為必須遵循才能獲得成功交易的協調流程序列。 協調流程步驟的 [ClaimsExchange](userjourneys.md#claimsexchanges) 元素會系結至執行的單一 [技術設定檔](technical-profiles-overview.md) 。

Subjourney 是可在使用者旅程圖中的任何時間點叫用的協調流程步驟群組。 您可以使用 subjourneys 來建立可重複使用的步驟序列，或執行分支來更妥善地表示商務邏輯。

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

## <a name="user-journey-branching"></a>使用者旅程圖分支

Subjourneys 的行為就像 [使用者旅程](userjourneys.md)一樣，因為兩者都是以協調流程順序表示，必須遵循才能成功進行交易。 使用者旅程可以自行叫用，且需要 SendClaims 步驟來執行。 Subjourneys 是使用者旅程的元件，無法單獨叫用，且一律會從使用者旅程圖中呼叫。

分支的主要元件是在使用者旅程圖中允許更佳的商務邏輯處理。 一般協調流程步驟會分組為個別要個別叫用的部分。 Subjourney 可以簡化將多個協調流程步驟結合在一起 (具有相同前置條件) 的旅程。 Subjourney 只會從使用者旅程中呼叫，不應呼叫另一個 subjourney。

Subjourneys 有兩種類型：

- **Call** -將控制權傳回給呼叫端。 SubJourney 會執行，然後控制權會傳回給目前在使用者旅程圖中執行的協調流程步驟。
- **傳輸** -將控制權傳輸至 subjourney (無法復原的分支) 。 Subjourney 必須有 SendClaims 步驟，才能將宣告傳回給信賴憑證者應用程式。

## <a name="example-scenarios"></a>範例案例

### <a name="call-subjourney"></a>呼叫 SubJourney

呼叫 SubJourney 在下列案例中很有用：

- 年齡管制：對於年齡管制，使用者旅程之間有許多共用的元件。 分支可讓您將通用元素編譯成可共用的元件。  
- 家長同意：分支可讓我們從使用者旅程圖中存取未執行的宣告，並可在尋找使用者需要同意之後，分支到家長同意使用者旅程圖中，方便您使用「家長同意」設計。 
- 註冊以登入：假設某個使用者已經存在於目錄中，但可能忘了他們事實上已建立帳戶。 在這種情況下，您可能會想要在這種情況下，而不是告知使用者他們所輸入的認證已經存在，並強制使用者重新開機原則能夠從註冊流程切換至該使用者的登入流程的旅程。  

### <a name="transfer-subjourney"></a>傳送 SubJourney

在下列案例中，傳輸 SubJourney 很有用：

- 顯示區塊頁面。
- A/B 測試，方法是將要求路由傳送至 SubJourney 來執行和發行權杖。

## <a name="adding-a-subjourney-element"></a>新增 SubJourney 元素

以下是 `SubJourney` 型別的元素範例，其會將 `Call` 控制權傳回給使用者旅程圖。

```xml
<SubJourneys>
  <SubJourney Id="ConditionalAccess_Evaluation" Type="Call">
    <OrchestrationSteps>
      <OrchestrationStep Order="1" Type="ClaimsExchange">
       <ClaimsExchanges>
        <ClaimsExchange Id="ConditionalAccessEvaluation" TechnicalProfileReferenceId="ConditionalAccessEvaluation" />
       </ClaimsExchanges>
      </OrchestrationStep>
      <OrchestrationStep Order="2" Type="ClaimsExchange">
        <Preconditions>
          <Precondition Type="ClaimsExist" ExecuteActionsIf="false">
            <Value>conditionalAccessClaimCollection</Value>
            <Action>SkipThisOrchestrationStep</Action>
          </Precondition>
        </Preconditions>
        <ClaimsExchanges>
          <ClaimsExchange Id="GenerateCAClaimFlags" TechnicalProfileReferenceId="GenerateCAClaimFlags" />
        </ClaimsExchanges>
      </OrchestrationStep>
    </OrchestrationSteps>
  </SubJourney>
</SubJourneys>
```

以下是型別的元素範例，會將 `SubJourney` `Transfer` 權杖傳回給信賴憑證者應用程式。

```xml
<SubJourneys>
  <SubJourney Id="B" Type="Transfer">
    <OrchestrationSteps>
      ...
      <OrchestrationStep Order="5" Type="SendClaims">
    </OrchestrationSteps>
  </SubJourney>
</SubJourneys>
```

### <a name="invoke-a-subjourney-step"></a>叫用 subjourney 步驟

型別的新協調流程步驟 `InvokeSubJourney` 是用來執行 subjourney。 下列範例顯示此協調流程步驟的所有執行元素。

```xml
<OrchestrationStep Order="5" Type="InvokeSubJourney">
  <JourneyList>
    <Candidate SubJourneyReferenceId="ConditionalAccess_Evaluation" />
  </JourneyList>
</OrchestrationStep>
```

> [!NOTE]
> Subjourney 不應該呼叫另一個 subjourney。

## <a name="components"></a>元件

若要定義原則所支援的 subjourneys，請在原則檔的最上層元素底下加入 **subjourneys** 專案。

**SubJourneys**元素包含下列元素：

| 元素 | 發生次數 | 描述 |
| ------- | ----------- | ----------- |
| SubJourney | 1:n | 定義完整使用者流程所需之所有結構的 subjourney。 |

**SubJourneys**元素包含下列屬性：

| 屬性 | 必要 | 描述 |
| --------- | -------- | ----------- |
| Id | 是 | SubJourney 識別碼，可供使用者旅程用來參考原則中的 SubJourney。 [候選](userjourneys.md#journeylist)專案的**SubJourneyReferenceId**元素指向這個屬性。 |
| 類型 | 是 | 可能的值： `Call` 、或 `Transfer` 。 如需詳細資訊，請參閱 [使用者旅程圖分支](#user-journey-branching)|

**SubJourney**元素包含下列元素：

| 元素 | 發生次數 | 描述 |
| ------- | ----------- | ----------- |
| OrchestrationSteps | 1:n | 必須遵循才能獲得成功交易的協調流程序列。 每個使用者旅程圖都由依序執行的已排序協調流程步驟清單所組成。 如果有任何步驟失敗，交易就會失敗。 |

## <a name="orchestrationsteps"></a>OrchestrationSteps

如需協調流程步驟元素的完整清單，請參閱 [>userjourneys](userjourneys.md)。

## <a name="next-steps"></a>後續步驟

[UserJourneys](userjourneys.md)