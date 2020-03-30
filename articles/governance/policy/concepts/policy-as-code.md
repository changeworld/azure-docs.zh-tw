---
title: 設計原則即程式碼工作流程
description: 瞭解如何設計工作流以將 Azure 策略定義部署為代碼並自動驗證資源。
ms.date: 11/04/2019
ms.topic: conceptual
ms.openlocfilehash: 0914ba6510c9d2ef87d3f83417f97340d42c8bce
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74267270"
---
# <a name="design-policy-as-code-workflows"></a>設計原則即程式碼工作流程

隨著雲治理之旅的不斷進步，您需要從手動管理 Azure 門戶中的每個策略定義或通過各種 SDK 轉變為在企業規模上更易於管理和重複的內容。 在雲中大規模管理系統的兩種主要方法是：

- 基礎結構作為代碼：將定義環境的內容（從資源管理器範本到 Azure 策略定義到 Azure 藍圖）的所有內容視為原始程式碼的做法。
- DevOps：人員、流程和產品的結合，以實現持續向最終使用者交付價值。

政策即代碼是這些想法的組合。 從本質上講，將策略定義保留在原始程式碼管理中，每當進行更改時，都會測試和驗證該更改。 但是，這不應該是策略與基礎結構作為代碼或 DevOps 的參與程度。

驗證步驟還應是其他持續集成或連續部署工作流的組成部分。 示例包括部署應用程式環境或虛擬基礎結構。 通過將 Azure 策略驗證作為生成和部署過程的早期元件，應用程式和操作團隊會發現其更改是否不受投訴，這遠在為時已晚且它們嘗試在生產中部署之前。

## <a name="workflow-overview"></a>工作流程概觀

策略作為代碼的建議常規工作流如下所示：

![策略作為代碼工作流概述](../media/policy-as-code/policy-as-code-workflow.png)

### <a name="create-and-update-policy-definitions"></a>創建和更新策略定義

策略定義使用 JSON 創建，並存儲在原始程式碼管理中。 每個策略都有自己的檔集，如應存儲在同一資料夾中的參數、規則和環境參數。 以下結構是將策略定義保留在原始程式碼管理中的推薦方法。

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

添加新策略或更新現有策略時，工作流應自動更新 Azure 中的策略定義。 測試新的或更新的策略定義在後面的步驟中。

### <a name="create-and-update-initiative-definitions"></a>創建和更新計畫定義

同樣，計畫有自己的 JSON 檔和相關檔，這些檔應存儲在同一資料夾中。 計畫定義要求策略定義已經存在，因此在源控制項中更新策略的源然後在 Azure 中更新之前，無法創建或更新策略。 以下結構是將主動性定義保留在原始程式碼管理中的推薦方法：

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

與策略定義一樣，在添加或更新現有計劃時，工作流應自動更新 Azure 中的計畫定義。 測試新的或更新的計畫定義在後面的步驟。

### <a name="test-and-validate-the-updated-definition"></a>測試和驗證更新的定義

一旦自動化獲取了新創建或更新的策略或計畫定義，並在 Azure 中對物件進行了更新，就該測試所做的更改了。 然後，無論是策略還是其部分的主動性，都應分配給離生產最遠的環境中的資源。 此環境通常是_Dev_。

分配應使用_禁用_的[強制模式](./assignment-structure.md#enforcement-mode)，以便不會阻止資源創建和更新，但仍會審核現有資源是否符合更新的策略定義。 即使使用強制模式，建議分配範圍是資源組或專門用於驗證策略的訂閱。

> [!NOTE]
> 雖然強制模式很有用，但它不能替代在各種條件下徹底測試策略定義。 策略定義應使用`PUT`和`PATCH`REST API 呼叫、相容和不合規的資源以及資源中缺少的屬性等邊緣情況進行測試。

部署分配後，使用策略 SDK 獲取新分配的[合規性資料](../how-to/get-compliance-data.md)。 用於測試策略和分配的環境應同時具有符合和不符合的資源。 與代碼良好的單元測試一樣，您希望測試資源是否按預期進行，並且您也沒有誤報或假陰性。 如果僅針對預期內容進行測試和驗證，則策略可能會產生意外和未識別的影響。 有關詳細資訊，請參閱[評估新的 Azure 策略的影響](./evaluate-impact.md)。

### <a name="enable-remediation-tasks"></a>啟用補救任務

如果分配的驗證符合預期，下一步是驗證修正。
使用[DeployNot存在](./effects.md#deployifnotexists)或[修改](./effects.md#modify)的策略可能會轉換為修正任務，並從不合規狀態更正資源。

執行此操作的第一步是授予策略分配策略定義中定義的角色指派。 此角色指派為策略分配託管標識提供了足夠的許可權，以便進行必要的更改以使資源符合要求。

策略分配具有適當許可權後，使用策略 SDK 針對已知不符合的一組資源觸發修正任務。 在繼續之前，應針對這些修正後的任務完成三個測試：

- 驗證補救任務是否成功完成
- 運行策略評估以查看策略合規性結果按預期更新
- 直接針對資源運行環境單元測試，以驗證其屬性已更改

測試更新的策略評估結果和環境直接可以確認修正任務改變了預期，並且策略定義按預期方式看到合規性更改。

### <a name="update-to-enforced-assignments"></a>更新到強制分配

完成所有驗證門後，更新分配以使用_啟用_的**強制模式**。 此更改最初應在遠離生產的同一環境中進行。 驗證該環境為按預期工作後，應將更改的範圍限定為包括下一個環境等，直到策略部署到生產資源。

## <a name="process-integrated-evaluations"></a>過程綜合評價

策略為代碼的一般工作流用於大規模開發和將策略和計畫部署到環境中。 但是，策略評估應該是在 Azure 中部署或創建資源的任何工作流的部署過程的一部分，例如部署應用程式或運行資源管理器範本以創建基礎結構。

在這些情況下，在將應用程式或基礎結構部署到測試訂閱或資源組後，應針對該範圍執行策略評估，檢查所有現有策略和計畫驗證。 雖然它們可能配置為在這樣的環境中_禁用_**的強制模式**，但儘早瞭解應用程式或基礎結構部署是否違反了策略定義非常有用。 因此，此策略評估應該是這些工作流中的一個步驟，以及創建不合規資源的失敗部署。

## <a name="review"></a>檢閱

本文介紹了策略作為代碼的一般工作流，以及策略評估應成為其他部署工作流的一部分的位置。 此工作流可用於支援基於觸發器的腳本化步驟和自動化的任何環境。

## <a name="next-steps"></a>後續步驟

- 瞭解[策略定義結構](./definition-structure.md)。
- 瞭解[策略分配結構](./assignment-structure.md)。
- 瞭解如何[以程式設計方式創建策略](../how-to/programmatically-create.md)。
- 瞭解如何[獲取合規性資料](../how-to/get-compliance-data.md)。
- 瞭解如何[修復不合規資源](../how-to/remediate-resources.md)。
- 使用[Azure 管理組查看](../../management-groups/overview.md)管理組的內容。請組織資源。