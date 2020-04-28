---
title: 評估新 Azure 原則定義的影響
description: 瞭解在 Azure 環境中引進新的原則定義時，所要遵循的流程。
ms.date: 09/23/2019
ms.topic: conceptual
ms.openlocfilehash: 394f87c0328bce46d4c7177a336a7861991ad0e8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "82187744"
---
# <a name="evaluate-the-impact-of-a-new-azure-policy-definition"></a>評估新 Azure 原則定義的影響

Azure 原則是一種功能強大的工具，可讓您管理 Azure 資源以符合商業標準，並滿足合規性需求。 當人員、進程或管線建立或更新資源時，Azure 原則審查要求。 當原則定義效果是[Append](./effects.md#deny)或[DeployIfNotExists](./effects.md#deployifnotexists)時，原則會改變要求或將其加入其中。 當原則定義效果為[Audit](./effects.md#audit)或[AuditIfNotExists](./effects.md#auditifnotexists)時，原則會導致建立活動記錄專案。 當原則定義的效果為 [[拒絕](./effects.md#deny)] 時，原則就會停止建立或修改要求。

當您知道已正確定義原則時，這些結果就會完全符合預期。 不過，請務必驗證新的原則是否如預期運作，再允許它變更或封鎖工作。 驗證必須確保只會將預期的資源判定為不符合規範，而且不會在結果中不正確地包含符合規範的資源（也稱為「_假肯定_」）。

驗證新原則定義的建議方法是遵循下列步驟：

- 緊密定義原則
- 審核您現有的資源
- Audit 新的或已更新的資源要求
- 將原則部署至資源
- 連續監視

## <a name="tightly-define-your-policy"></a>緊密定義原則

請務必瞭解如何將商務原則實作為原則定義，以及 Azure 資源與其他 Azure 服務的關聯性。 此步驟是藉由[識別需求](../tutorials/create-custom-policy-definition.md#identify-requirements)和[判斷資源屬性](../tutorials/create-custom-policy-definition.md#determine-resource-properties)來完成。
但請務必查看您的商務原則的狹窄定義以外的範圍。 您的原則狀態例如「所有虛擬機器必須 ...」嗎？ 使用 Vm 的其他 Azure 服務（例如 HDInsight 或 AKS）呢？ 定義原則時，我們必須考慮此原則如何影響其他服務所使用的資源。

基於這個理由，您的原則定義應該嚴格地定義，並著重于資源和您需要評估相容性的屬性。

## <a name="audit-existing-resources"></a>審核現有的資源

在想要使用新的原則定義來管理新的或更新的資源之前，最好先查看它如何評估有限的現有資源子集，例如測試資源群組。 在您的原則指派上使用[強制模式](./assignment-structure.md#enforcement-mode)
_停_用（DoNotEnforce），以防止觸發或建立活動記錄專案的[效果](./effects.md)。

此步驟讓您有機會評估現有資源上新原則的相容性結果，而不會影響工作流程。 檢查是否沒有符合規範的資源標示為不符合規範（_誤報_），而且所有您預期不相容的資源都會標示為正確。
資源的初始子集如預期般驗證之後，就會慢慢地將評估擴展到所有現有的資源。

以這種方式評估現有的資源也可讓您在完整執行新原則之前，先補救不符合規範的資源。 這項清除作業可以手動完成，或在原則定義效果為_DeployIfNotExists_時透過[補救](../how-to/remediate-resources.md)工作進行。

## <a name="audit-new-or-updated-resources"></a>審核新的或更新的資源

當您驗證新的原則定義在現有資源上正確報告後，就可以在建立或更新資源時查看原則的影響。 如果原則定義支援效果參數化，請使用[Audit](./effects.md#audit)。 此設定可讓您監視資源的建立和更新，以瞭解新的原則定義是否會針對不符合規範的資源觸發 Azure 活動記錄中的專案，而不會影響現有的工作或要求。

建議您更新並建立符合原則定義的新資源，以查看是否正確地在預期的情況之下觸發_審核_效果。 在不應受觸發_Audit_效果的新原則定義影響的資源要求的 lookout 上。
這些受影響的資源是另一個_誤報_範例，必須先在原則定義中修正，才能進行完整的執行。

在此測試階段變更原則定義的情況下，建議您先使用現有資源的審核來開始驗證程式。 針對新的或更新的資源， _false 肯定_的原則定義變更可能也會影響現有的資源。

## <a name="deploy-your-policy-to-resources"></a>將原則部署至資源

當您使用現有的資源和新的或已更新的資源要求來完成新原則定義的驗證之後，就會開始執行原則的程式。 建議您先建立新原則定義的原則指派給所有資源的子集，例如資源群組。 驗證初始部署之後，將原則的範圍擴充為更廣泛且更廣泛的層級，例如訂用帳戶和管理群組。 這項擴充的達成方式是移除指派，並在目標範圍建立新的，直到它指派給新的原則定義所涵蓋的資源的完整範圍為止。

在推出期間，如果資源位於應該豁免新原則定義的位置，請以下列其中一種方式解決這些問題：

- 將原則定義更新為更明確，以減少非預期的影響
- 變更原則指派的範圍（藉由移除並建立新的指派）
- 將資源群組新增至原則指派的排除清單

對範圍（層級或排除）所做的任何變更都應該完整驗證，並與您的安全性和合規性組織溝通，以確保涵蓋範圍沒有任何缺口。

## <a name="monitor-your-policy-and-compliance"></a>監視您的原則與合規性

執行和指派原則定義並不是最後一個步驟。 持續監視您新原則定義的資源[相容性](../how-to/get-compliance-data.md)層級，並設定適當的[Azure 監視器警示和通知](../../../azure-monitor/platform/alerts-overview.md)，以瞭解何時識別不符合規範的裝置。 此外，也建議您依照排程評估原則定義和相關指派，以驗證原則定義是否符合商務原則和合規性需求。 如果不再需要原則，則應該將其移除。 當基礎 Azure 資源演變並新增屬性和功能時，原則也需要及時更新。

## <a name="next-steps"></a>後續步驟

- 瞭解[原則定義結構](./definition-structure.md)。
- 瞭解[原則指派結構](./assignment-structure.md)。
- 瞭解如何以程式設計[方式建立原則](../how-to/programmatically-create.md)。
- 瞭解如何[取得合規性資料](../how-to/get-compliance-data.md)。
- 瞭解如何[補救不符合規範的資源](../how-to/remediate-resources.md)。
- 請參閱使用[Azure 管理群組來組織資源](../../management-groups/overview.md)的管理群組。