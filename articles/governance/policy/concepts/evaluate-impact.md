---
title: 評估新 Azure 原則定義的影響
description: 了解在 Azure 環境中導入新的原則定義時所須依循的程序。
ms.date: 05/20/2020
ms.topic: conceptual
ms.openlocfilehash: d9844e8435452b388c934c5969898fe01d23fb47
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/20/2020
ms.locfileid: "83684297"
---
# <a name="evaluate-the-impact-of-a-new-azure-policy-definition"></a>評估新 Azure 原則定義的影響

Azure 原則是一項功能強大的工具，可讓您依據商務標準管理 Azure 資源，並符合合規需求。 當人員、程序或管線建立或更新資源時，Azure 原則會檢閱要求。 當原則定義效果為 [Append](./effects.md#deny) 或 [DeployIfNotExists](./effects.md#deployifnotexists) 時，原則會改變要求或對其進行新增。 當原則定義效果為 [Audit](./effects.md#audit) 或 [AuditIfNotExists](./effects.md#auditifnotexists) 時，原則會促使系統建立活動記錄項目。 當原則定義效果為 [Deny](./effects.md#deny) 時，原則會停止建立或改變要求。

若您確知原則已正確定義，這些結果就會完全符合預期。 不過，請務必先驗證新的原則可如預期運作，再允許該原則變更或封鎖工作。 此驗證必須確定只有預期的資源會被認定為不符合規範，而不會誤將符合規範的資源包含在結果中 (也稱為_誤判為真_)。

驗證新原則定義的建議方法，是執行下列步驟：

- 嚴格定義您的原則
- 稽核您現有的資源
- 稽核新的或更新的資源要求
- 將原則部署至資源
- 連續監視

## <a name="tightly-define-your-policy"></a>嚴格定義您的原則

請務必了解將商務原則實作為原則定義的方式，以及 Azure 資源與其他 Azure 服務之間的關聯性。 此步驟可藉由[識別需求](../tutorials/create-custom-policy-definition.md#identify-requirements)和[確認資源屬性](../tutorials/create-custom-policy-definition.md#determine-resource-properties)來完成。
不過，您也必須留意商務原則較廣義的定義。 您的原則是否提及「所有虛擬機器都必須...」的事項？ 其他使用 VM 的 Azure 服務 (例如 HDInsight 或 AKS) 呢？ 定義原則時，我們必須考量此原則對其他服務所使用的資源將有何影響。

因此，您的原則定義應盡可能嚴格定義，並著重於需要評估合規性的資源和屬性。

## <a name="audit-existing-resources"></a>稽核現有的資源

在著手使用新的原則定義來管理新的或更新的資源之前，最好先確認其對於有限的現有資源子集 (例如測試資源群組) 的評估結果。 請對您的原則指派使用 [停用] (DoNotEnforce) 的[強制模式](./assignment-structure.md#enforcement-mode)，以避免觸發[效果](./effects.md)或建立活動記錄項目。

此步驟可讓您在不影響工作流程的情況下，評估新原則用於現有資源時的合規性結果。 請確認沒有符合規範的資源標示為不符合規範 (_誤判為真_)，且所有預期不符合規範的資源均正確標示。
初始資源子集經驗證符合預期後，請漸次對所有現有的資源都進行評估。

以這種方式評估現有的資源，也可讓您在完整實作新原則前先補救不符合規範的資源。 這項清除作業可以手動完成，或者，如果原則定義效果為 _DeployIfNotExists_，則透過[補救工作](../how-to/remediate-resources.md)來完成。

## <a name="audit-new-or-updated-resources"></a>稽核新的或更新的資源

當您驗證新的原則定義在現有資源上正確報告後，就可以在資源建立或更新後查看原則的影響。 如果原則定義支援效果參數化，請使用[稽核](./effects.md#audit)。 此設定可讓您監視資源的建立和更新，以了解新的原則定義是否會針對不符合規範的資源觸發 Azure 活動記錄中的項目，而不會影響現有的工作或要求。

建議您更新和建立符合原則定義的新資源，以查看是否如預期正確地觸發_稽核_效果。 請留意觸發_稽核_效果的新原則定義是否對不應受影響的資源要求造成影響。
這些受影響的資源也屬於_誤判為真_，必須先在原則定義中修正，才能進行完整實作。

如果在此測試階段變更了原則定義，建議您先從現有資源的稽核開始進行驗證程序。 因新的或更新的資源_誤判為真_而變更原則定義後，現有的資源也可能會受到影響。

## <a name="deploy-your-policy-to-resources"></a>將原則部署至資源

對現有資源和新的或更新的資源要求完成新原則定義的驗證之後，即可進行實作原則的程序。 建議您先建立將新的原則定義指派給資源子集的原則指派，例如資源群組。 驗證初始部署之後，請漸次將原則的範圍擴充至更廣泛的層級，例如訂用帳戶和管理群組。 進行此擴充的方式是，先移除指派，然後在目標範圍建立新的指派，直到指派給新的原則定義預定要涵蓋的所有資源為止。

在推出期間，如果發現不應受新原則定義影響的資源，請以下列其中一種方式處理：

- 將原則定義更新得更為明確，以減少非預期的影響
- 變更原則指派的範圍 (藉由移除指派並建立新的指派)
- 將資源群組新增至原則指派的排除清單

對範圍 (層級或排除) 所做的任何變更均應完整驗證，並與您的安全性和合規性組織充分溝通，以確保涵蓋範圍沒有差異。

## <a name="monitor-your-policy-and-compliance"></a>監視您的原則和合規性

實作和指派原則定義並不是最後一個步驟。 持續監視資源在新原則定義下的[合規性](../how-to/get-compliance-data.md)層級，並設定適當的 [Azure 監視器警示和通知](../../../azure-monitor/platform/alerts-overview.md)，在識別出不符合規範的裝置時使用。 此外，也建議您按排程評估原則定義和相關指派，以驗證原則定義是否符合商務原則和合規性需求。 如果不再需要原則，則應將其移除。 隨著基礎 Azure 資源的演變以及新增屬性和功能，原則也必須適時更新。

## <a name="next-steps"></a>後續步驟

- 了解[原則定義結構](./definition-structure.md)。
- 了解[原則指派結構](./assignment-structure.md)。
- 了解如何[以程式設計方式建立原則](../how-to/programmatically-create.md)。
- 了解如何[取得合規性資料](../how-to/get-compliance-data.md)。
- 了解如何[補救不符合規範的資源](../how-to/remediate-resources.md)。
- 透過[使用 Azure 管理群組來組織資源](../../management-groups/overview.md)來檢閱何謂管理群組。