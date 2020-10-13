---
title: 設計原則即程式碼工作流程
description: 了解如何設計將您的 Azure 原則定義部署為程式碼並自動驗證資源的工作流程。
ms.date: 09/22/2020
ms.topic: conceptual
ms.openlocfilehash: 7fa8eb36283821527e16c1d97e326aa9dcde9dba
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91598215"
---
# <a name="design-policy-as-code-workflows"></a>設計原則即程式碼工作流程

透過雲端治理進行旅程時，您會想要從使用 Azure 入口網站或各種 SDK 手動管理每個原則定義，轉換為在企業規模更容易管理且可重複使用的程序。 要在雲端大規模管理系統，主要有兩種方法：

- 基礎結構即程式碼：將定義您的環境內容的做法，從 Azure Resource Manager 範本 (ARM 範本的所有專案，) Azure 原則定義為 Azure 藍圖原始程式碼。
- DevOps：為我們的使用者持續傳遞價值的人員、程序和產品組合。

原則即程式碼是這些想法的組合。 基本上，請將原則定義保存在原始檔控制中，並在每次進行變更後測試並驗證該變更。 不過，基礎結構即程式碼或 DevOps 的相關原則並不在此範圍內。

驗證步驟也應該是其他持續整合或持續部署工作流程的元件。 其範例包括部署應用程式環境或虛擬基礎結構。 藉由讓 Azure 原則驗證成為組建和部署程式的早期元件，應用程式和作業小組會探索其變更是否不符合規範、是否晚于太晚，並嘗試在生產環境中部署。

## <a name="definitions-and-foundational-information"></a>定義和基本資訊

在取得原則的詳細資料作為程式碼工作流程之前，請先參閱下列定義和範例：

- [原則定義](./definition-structure.md)
- [計畫定義](./initiative-definition-structure.md)

檔案名會與原則或方案定義的部分相符：
- `policy(set).json` -整個定義
- `policy(set).parameters.json` - `properties.parameters` 定義的部分
- `policy.rules.json` - `properties.policyRule` 定義的部分
- `policyset.definitions.json` - `properties.policyDefinitions` 定義的部分

[Azure 原則 GitHub](https://github.com/Azure/azure-policy/)存放庫提供這些檔案格式的範例：

- 原則定義： [將標記新增至資源](https://github.com/Azure/azure-policy/tree/master/samples/Tags/add-tag)
- 方案定義： [計費標記](https://github.com/Azure/azure-policy/tree/master/samples/PolicyInitiatives/multiple-billing-tags)

此外，請參閱 [匯出 Azure 原則資源](../how-to/export-resources.md) ，以將現有的定義和指派指派至原始程式碼管理環境 [GitHub](https://www.github.com)。

## <a name="workflow-overview"></a>工作流程概觀

針對原則即程式碼而建議的一般工作流程如下圖所示：

:::image type="complex" source="../media/policy-as-code/policy-as-code-workflow.png" alt-text="顯示原則即程式碼工作流程方塊的圖表，從建立到測試要部署。" border="false":::
   顯示原則即程式碼工作流程方塊的圖表。 建立涵蓋原則和計畫定義的建立。 測試涵蓋已停用強制模式的指派。 相容性狀態的閘道檢查會接著授與指派 M S 許可權，並修復資源。  部署涵蓋更新已啟用強制模式的指派。
:::image-end:::

### <a name="create-and-update-policy-definitions"></a>建立及更新原則定義

原則定義會使用 JSON 建立，並儲存在原始檔控制中。 每個原則都有其本身的一組檔案 (例如參數、規則和環境參數)，而這些檔案應儲存在相同的資料夾中。 下列結構是將原則定義保存在原始檔控制中的建議方式。

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

新增原則或更新現有的原則時，工作流程應該會自動更新 Azure 中的原則定義。 新的或更新的原則定義會在後續步驟中進行測試。

### <a name="create-and-update-initiative-definitions"></a>建立及更新方案定義

同樣地，方案也有本身的 JSON 檔案和相關檔案，應儲存在相同資料夾中。 方案定義需要已存在的原則定義，因此必須等到原則的來源已在原始檔控制中更新，繼而在 Azure 中更新之後，才能建立或更新。 下列結構是將方案定義保存在原始檔控制中的建議方式：

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

如同原則定義，在新增方案或更新現有的方案時，工作流程應該會自動更新 Azure 中的方案定義。 新的或更新的方案定義會在後續步驟中進行測試。

### <a name="test-and-validate-the-updated-definition"></a>測試及驗證更新的定義

在自動流程已取得您新建立或更新的原則或方案定義，並對 Azure 中的物件進行更新後，就可以測試所做的變更。 原則或其所屬的方案應指派給生產之前最初步的環境所含的資源。 此環境通常為 _Dev_。

指派的 [enforcementMode](./assignment-structure.md#enforcement-mode) 應使用 [停用]，如此就不會封鎖資源的建立和更新，但仍會對現有的資源進行審核以符合更新的原則定義。 即便使用 enforcementMode，仍建議將指派範圍設為專門用來驗證原則的資源群組或訂用帳戶。

> [!NOTE]
> 雖然強制模式很有幫助，但仍應在各種情況下徹底測試原則定義。 原則定義應使用 `PUT` 和 `PATCH` REST API 呼叫、符合規範和不符合規範的資源以及邊緣案例 (例如資源中遺漏的屬性) 進行測試。

部署指派之後，請使用原則 SDK 或 [Azure 原則合規性掃描 GitHub 動作](https://github.com/marketplace/actions/azure-policy-compliance-scan) 來取得新指派的 [合規性資料](../how-to/get-compliance-data.md) 。 用來測試原則和指派的環境應同時具備符合規範和不符合規範的資源。
如同程式碼的適當單元測試，您應測試資源是否符合預期，且沒有誤判為真或誤否定。 如果您只針對預期的部分進行測試和驗證，原則可能會有非預期和未辨識的影響。 如需詳細資訊，請參閱[評估新 Azure 原則定義的影響](./evaluate-impact.md)。

### <a name="enable-remediation-tasks"></a>啟用補救工作

如果指派的驗證符合預期，下一步就是驗證補救。
使用 [deployIfNotExists](./effects.md#deployifnotexists) 或[修改](./effects.md#modify)的原則可以轉換為補救工作，並更正不符合規範的資源。

補救資源的第一個步驟，是為原則指派授與原則定義中定義的角色指派。 此角色指派可讓原則指派受控識別有足夠的權限可進行所需的變更，使資源符合規範。

原則指派具有適當的權限後，請使用原則 SDK，對已知不符合規範的一組資源觸發補救工作。 繼續操作之前，應先對這些補救工作完成三項測試：

- 驗證補救工作已順利完成
- 執行原則評估，以確認原則合規性結果已如預期更新
- 直接對資源執行環境單元測試，以驗證其屬性已變更

直接測試更新的原則評估結果和環境，可確認補救工作已變更預期的部分，且原則定義所顯示的合規性變更符合預期。

### <a name="update-to-enforced-assignments"></a>更新為強制指派

所有驗證閘道皆已完成後，請更新指派以使用 [啟用] 的 **enforcementMode**。 建議您應先在生產之前的同一個最初期的環境中進行這項變更。 該環境經驗證可如預期運作之後，即應該將變更的範圍設定為包含下一個環境，依此類推，直到原則部署至生產資源為止。

## <a name="process-integrated-evaluations"></a>程序整合評估

原則即程式碼的一般工作流程可在環境中大規模開發及部署原則和方案。 不過，在 Azure 中部署或建立資源的任何工作流程（例如部署應用程式或執行 ARM 範本以建立基礎結構）的部署程式中，原則評估應該是部署程式的一部分。

在這些情況下，將應用程式或基礎結構部署至測試訂用帳戶或資源群組之後，即應對該範圍執行原則評估，以檢查所有現有原則和方案的有效性。 雖然其 **enforcementMode** 在這類環境中可能設定為 [停用]，但及早了解應用程式或基礎結構部署是否違反原則定義，仍很有幫助。 因此，此原則評估應納入為這些工作流程中的步驟之一，使建立的資源不符合規範的部署失敗。

## <a name="review"></a>檢閱

本文說明原則即程式碼的一般工作流程，以及應將原則評估納入其他部署工作流程中的階段。 任何環境只要支援指令碼式步驟和以觸發程序為基礎的自動化，都可以使用此工作流程。

## <a name="next-steps"></a>後續步驟

- 了解[原則定義結構](./definition-structure.md)。
- 了解[原則指派結構](./assignment-structure.md)。
- 了解如何[以程式設計方式建立原則](../how-to/programmatically-create.md)。
- 了解如何[取得合規性資料](../how-to/get-compliance-data.md)。
- 了解如何[補救不符合規範的資源](../how-to/remediate-resources.md)。
- 透過[使用 Azure 管理群組來組織資源](../../management-groups/overview.md)來檢閱何謂管理群組。
