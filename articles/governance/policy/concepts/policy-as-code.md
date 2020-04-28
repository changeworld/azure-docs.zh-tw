---
title: 設計原則即程式碼工作流程
description: 瞭解如何設計工作流程，將您的 Azure 原則定義部署為程式碼，並自動驗證資源。
ms.date: 11/04/2019
ms.topic: conceptual
ms.openlocfilehash: fd77fdd4011c3e1e83f8dfa9f30045bb72881c25
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "82187727"
---
# <a name="design-policy-as-code-workflows"></a>設計原則即程式碼工作流程

當您透過雲端治理進行旅程時，您會想要從手動管理 Azure 入口網站中的每個原則定義，或透過各種 Sdk 來進行，以在企業規模更容易管理且可重複使用。 在雲端大規模管理系統的主要方法有兩種：

- 基礎結構即程式碼：將定義您環境的內容視為一種作法，從 Resource Manager 範本到 Azure 原則定義，到 Azure 藍圖，都是原始程式碼。
- DevOps：人員、程式和產品的聯集，以持續傳遞價值給我們的終端使用者。

原則即程式碼是這些想法的組合。 基本上，在原始檔控制中保留您的原則定義，並在每次進行變更時，測試並驗證該變更。 不過，這不應該是使用基礎結構即程式碼或 DevOps 的原則範圍。

驗證步驟也應該是其他持續整合或持續部署工作流程的元件。 範例包括部署應用程式環境或虛擬基礎結構。 藉由將 Azure 原則驗證做為組建和部署程式的初期元件，應用程式和作業小組會探索其變更是否為非投訴，而在過了一段時間之後，他們會嘗試在生產環境中進行部署。

## <a name="workflow-overview"></a>工作流程概觀

原則 as 程式碼的建議一般工作流程如下圖所示：

:::image type="content" source="../media/policy-as-code/policy-as-code-workflow.png" alt-text="原則即程式碼工作流程總覽" border="false":::

### <a name="create-and-update-policy-definitions"></a>建立和更新原則定義

原則定義是使用 JSON 建立，並儲存在原始檔控制中。 每個原則都有自己的一組檔案，例如參數、規則和環境參數，這些檔案應該儲存在相同的資料夾中。 下列結構是將原則定義保留在原始檔控制中的建議方式。

```text
.
|
|- policies/  ________________________ # Root folder for policies
|  |- policy1/  ______________________ # Subfolder for a policy
|     |- policy.json _________________ # Policy definition
|     |- policy.parameters.json ______ # Policy definition of parameters
|     |- policy.rules.json ___________ # Policy rule
|     |- params.dev.json _____________ # Parameters for a Dev environment
|     |- params.prd.json _____________ # Parameters for a Prod environment
|     |- params.tst.json _____________ # Parameters for a Test environment
|
|  |- policy2/  ______________________ # Subfolder for a policy
|     |- policy.json _________________ # Policy definition
|     |- policy.parameters.json ______ # Policy definition of parameters
|     |- policy.rules.json ___________ # Policy rule
|     |- params.dev.json _____________ # Parameters for a Dev environment
|     |- params.prd.json _____________ # Parameters for a Prod environment
|     |- params.tst.json _____________ # Parameters for a Test environment
|
```

新增或更新現有的原則時，工作流程應該會自動更新 Azure 中的原則定義。 新的或更新原則定義的測試會在稍後的步驟中提供。

### <a name="create-and-update-initiative-definitions"></a>建立和更新方案定義

同樣地，計畫會有自己的 JSON 檔案，以及應該儲存在相同資料夾中的相關檔案。 計畫定義要求原則定義必須已經存在，因此必須等到原則的來源已在原始檔控制中更新，然後在 Azure 中更新之後，才能建立或更新。 下列結構是將您的計畫定義保留在原始檔控制中的建議方式：

```text
.
|
|- initiatives/ ______________________ # Root folder for initiatives
|  |- init1/ _________________________ # Subfolder for an initiative
|     |- policyset.json ______________ # Initiative definition
|     |- policyset.definitions.json __ # Initiative list of policies
|     |- policyset.parameters.json ___ # Initiative definition of parameters
|     |- params.dev.json _____________ # Parameters for a Dev environment
|     |- params.prd.json _____________ # Parameters for a Prod environment
|     |- params.tst.json _____________ # Parameters for a Test environment
|
|  |- init2/ _________________________ # Subfolder for an initiative
|     |- policyset.json ______________ # Initiative definition
|     |- policyset.definitions.json __ # Initiative list of policies
|     |- policyset.parameters.json ___ # Initiative definition of parameters
|     |- params.dev.json _____________ # Parameters for a Dev environment
|     |- params.prd.json _____________ # Parameters for a Prod environment
|     |- params.tst.json _____________ # Parameters for a Test environment
|
```

如同原則定義，新增或更新現有的計畫時，工作流程應該會自動更新 Azure 中的計畫定義。 測試新的或更新的計畫定義會在稍後的步驟中進行。

### <a name="test-and-validate-the-updated-definition"></a>測試及驗證更新的定義

一旦自動化已採取新建立或更新的原則或計畫定義，並對 Azure 中的物件進行更新，就可以測試所做的變更。 該原則或其所屬的計畫應指派給環境中的資源，而不是生產環境的最遠。 此環境通常是_Dev_。

指派應使用_已停_用的[enforcementMode](./assignment-structure.md#enforcement-mode) ，如此一來，就不會封鎖資源的建立和更新，但仍會針對已更新的原則定義，將現有的資源進行審核。 即使使用 enforcementMode，建議指派範圍可以是資源群組或特別用於驗證原則的訂用帳戶。

> [!NOTE]
> 雖然強制模式很有説明，但它不是在各種情況下徹底測試原則定義的取代。 原則定義應使用`PUT`和`PATCH` REST API 呼叫、符合規範和不符合規範的資源，以及邊緣案例（例如資源中遺漏的屬性）進行測試。

部署指派之後，請使用原則 SDK 來取得新指派的[相容性資料](../how-to/get-compliance-data.md)。 用來測試原則和指派的環境應該同時具備符合規範和不符合規範的資源。 就像程式碼的良好單元測試一樣，您想要測試資源是否如預期般，而且也不會有誤報或誤否定。 如果您只針對預期的內容進行測試和驗證，原則可能會有未預期和無法辨識的影響。 如需詳細資訊，請參閱[評估新 Azure 原則定義的影響](./evaluate-impact.md)。

### <a name="enable-remediation-tasks"></a>啟用補救工作

如果指派的驗證符合預期，下一步就是驗證補救。
使用[deployIfNotExists](./effects.md#deployifnotexists)或[modify](./effects.md#modify)的原則可能會變成補救工作，並從不相容的狀態更正資源。

執行此動作的第一個步驟是將原則定義中定義的角色指派授與原則指派。 此角色指派可讓原則指派受控識別有足夠的許可權，以進行所需的變更，使資源符合規範。

一旦原則指派具有適當的許可權，請使用原則 SDK，針對已知不相容的一組資源觸發補救工作。 在繼續之前，應該先針對這些補救的工作完成三項測試：

- 驗證補救工作已順利完成
- 執行原則評估，以查看原則合規性結果已如預期般更新
- 直接對資源執行環境單元測試，以驗證其屬性已變更

同時測試已更新的原則評估結果和環境，可直接提供補救工作已變更預期的確認，且原則定義看到合規性變更如預期。

### <a name="update-to-enforced-assignments"></a>更新為強制指派

完成所有驗證閘道之後，請將指派更新為使用_已啟用_的**enforcementMode** 。 這項變更一開始應該在與生產環境相同的環境中進行。 一旦該環境驗證為如預期般運作，則應該將變更的範圍設定為包含下一個環境，依此類推，直到將原則部署到生產資源為止。

## <a name="process-integrated-evaluations"></a>進程整合的評估

原則即程式碼的一般工作流程是在大規模環境中開發和部署原則和計畫。 不過，在 Azure 中部署或建立資源的任何工作流程（例如部署應用程式或執行 Resource Manager 範本來建立基礎結構），原則評估都應該是部署流程的一部分。

在這些情況下，將應用程式或基礎結構部署至測試訂用帳戶或資源群組之後，應該針對該範圍檢查所有現有原則和方案的驗證來進行原則評估。 雖然在這類環境中可能會將它們設定為_停用_ **enforcementMode** ，但如果應用程式或基礎結構部署提早違反原則定義，就會很有説明。 因此，此原則評估應該是這些工作流程中的步驟，而且會導致建立不相容資源的部署失敗。

## <a name="review"></a>檢閱

本文涵蓋原則即程式碼的一般工作流程，以及原則評估應為其他部署工作流程的一部分。 此工作流程可以用於任何支援腳本式步驟的環境，以及以觸發程式為基礎的自動化。

## <a name="next-steps"></a>後續步驟

- 瞭解[原則定義結構](./definition-structure.md)。
- 瞭解[原則指派結構](./assignment-structure.md)。
- 瞭解如何以程式設計[方式建立原則](../how-to/programmatically-create.md)。
- 瞭解如何[取得合規性資料](../how-to/get-compliance-data.md)。
- 瞭解如何[補救不符合規範的資源](../how-to/remediate-resources.md)。
- 請參閱使用[Azure 管理群組來組織資源](../../management-groups/overview.md)的管理群組。