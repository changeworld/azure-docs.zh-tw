---
title: 評估新的 Azure 策略的影響
description: 瞭解在 Azure 環境中引入新策略定義時應遵循的過程。
ms.date: 09/23/2019
ms.topic: conceptual
ms.openlocfilehash: 562fa2378356ddc1eac48b6ea5c160ebf655d525
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74463518"
---
# <a name="evaluate-the-impact-of-a-new-azure-policy"></a>評估新的 Azure 策略的影響

Azure 策略是管理 Azure 資源以符合業務標準和滿足合規性需求的強大工具。 當人員、流程或管道創建或更新資源時，Azure 策略會審核請求。 當策略定義效果為["追加"](./effects.md#deny)或["部署不存在"時](./effects.md#deployifnotexists)，策略將變更要求或添加到請求中。 當策略定義效果為["審核](./effects.md#audit)["或"審核不存在"時](./effects.md#auditifnotexists)，策略會導致創建活動日誌條目。 當策略定義效果為["拒絕"](./effects.md#deny)時，策略將停止請求的創建或更改。

當您知道策略定義正確時，這些結果完全如預期的那樣。 但是，在允許新策略更改或阻止工作之前，驗證新策略的工作方式非常重要。 驗證必須確保僅確定預期資源不符合要求，並且結果中未錯誤地包含任何符合要求的資源（稱為_誤報_）。

驗證新策略定義的推薦方法是按照以下步驟操作：

- 嚴格定義策略
- 審核現有資源
- 審核新的或更新的資源請求
- 將策略部署到資源
- 連續監視

## <a name="tightly-define-your-policy"></a>嚴格定義策略

瞭解業務策略如何作為策略定義實現以及 Azure 資源與其他 Azure 服務的關係非常重要。 此步驟是通過[確定需求](../tutorials/create-custom-policy-definition.md#identify-requirements)並確定[資源屬性來實現的](../tutorials/create-custom-policy-definition.md#determine-resource-properties)。
但是，除了業務策略的狹義定義之外，還要看到這一點也很重要。 例如，您的策略狀態是否"所有虛擬機器都必須..."？ 使用 VM 的其他 Azure 服務（如 HDInsight 或 AKS）如何？ 定義策略時，必須考慮此策略如何影響其他服務使用的資源。

因此，策略定義應同樣嚴格定義，並側重于需要評估的資源和屬性，以便盡可能符合性。

## <a name="audit-existing-resources"></a>審核現有資源

在使用新的策略定義管理新資源或更新資源之前，最好瞭解它如何評估有限的現有資源子集，如測試資源組。 在策略分配上使用_禁用_（DoNot強制）[強制模式](./assignment-structure.md#enforcement-mode)
，以防止創建觸發或創建活動日誌條目[。](./effects.md)

此步驟為您提供了評估新有關現有資源策略的合規性結果的機會，而不會影響工作流。 檢查未將任何合規資源標記為不合規 （_誤報_）， 以及預期不合規的所有資源是否都已正確標記。
資源的初始子集按預期驗證後，將評估緩慢擴展到所有現有資源。

以這種方式評估現有資源也提供了在全面實施新政策之前修復不合規資源的機會。 如果策略定義效果為 _"DeployIfNot 存在"，_ 則可以手動或通過[修正任務](../how-to/remediate-resources.md)進行此清理。

## <a name="audit-new-or-updated-resources"></a>審核新資源或更新資源

驗證新策略定義正確報告現有資源後，是時候查看創建或更新資源時策略的影響了。 如果策略定義支援效果參數化，請使用[審核](./effects.md#audit)。 此配置允許您監視資源的創建和更新，以查看新策略定義是否觸發 Azure 活動日誌中不符合的資源的條目，而不會影響現有工作或請求。

建議更新和創建與您的策略定義匹配的新資源，以查看在預期時正確觸發_審核_效果。 留意不應受觸發_審核_效果的新策略定義影響的資源請求。
這些受影響的資源是_誤報_的另一個示例，必須在完全實現之前在策略定義中修復。

如果策略定義在此測試階段發生更改，建議從審核現有資源開始驗證過程。 對新資源或更新資源進行_誤報_的策略定義的更改也可能對現有資源產生影響。

## <a name="deploy-your-policy-to-resources"></a>將策略部署到資源

使用現有資源以及新的或更新的資源請求完成新策略定義的驗證後，將開始實施該策略的過程。 建議首先為所有資源（如資源組）的子集創建新策略定義的策略分配。 驗證初始部署後，將策略的範圍擴展到更廣泛和更廣泛的級別，如訂閱和管理組。 此擴展是通過刪除分配並在目標作用域創建新分配來實現的，直到分配給新策略定義要涵蓋的全部資源範圍。

在推出期間，如果資源位於應不受新策略定義的限制，請通過以下方式之一解決這些問題：

- 更新策略定義以更明確，以減少意外影響
- 更改策略分配的範圍（通過刪除和創建新分配）
- 將資源組添加到策略分配的排除清單中

對範圍的任何更改（級別或排除）都應經過充分驗證，並與您的安全和合規性組織溝通，以確保在覆蓋範圍上沒有差距。

## <a name="monitor-your-policy-and-compliance"></a>監控您的政策和合規性

實施和分配策略定義不是最後一步。 持續監視資源符合新策略定義的[合規性](../how-to/get-compliance-data.md)級別，並設置相應的[Azure 監視器警報和通知](../../../azure-monitor/platform/alerts-overview.md)，以便識別不符合的設備。 還建議按計劃評估策略定義和相關分配，以驗證策略定義是否滿足業務策略和合規性需求。 如果不再需要，應刪除策略。 策略還需要隨著基礎 Azure 資源的發展以及添加新屬性和功能而不時更新。

## <a name="next-steps"></a>後續步驟

- 瞭解[策略定義結構](./definition-structure.md)。
- 瞭解[策略分配結構](./assignment-structure.md)。
- 瞭解如何[以程式設計方式創建策略](../how-to/programmatically-create.md)。
- 瞭解如何[獲取合規性資料](../how-to/get-compliance-data.md)。
- 瞭解如何[修復不合規資源](../how-to/remediate-resources.md)。
- 使用[Azure 管理組查看](../../management-groups/overview.md)管理組的內容。請組織資源。