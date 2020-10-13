---
title: 常見問題疑難排解
description: 瞭解如何針對建立原則定義、各種 SDK 和 Kubernetes 的附加元件的問題進行疑難排解。
ms.date: 10/05/2020
ms.topic: troubleshooting
ms.openlocfilehash: 6026dc75187c8a70203a2484380eed70d519599d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91743432"
---
# <a name="troubleshoot-errors-using-azure-policy"></a>使用 Azure 原則針對錯誤進行疑難排解

當您建立原則定義、使用 SDK 或設定 Kubernetes 附加元件的 [Azure 原則](../concepts/policy-for-kubernetes.md) 時，可能會遇到錯誤。 此文章說明可能發生的各種錯誤與解決方式。

## <a name="finding-error-details"></a>尋找錯誤詳細資料

錯誤詳細資料的位置取決於造成錯誤的動作。

- 使用自訂原則時，請在 Azure 入口網站中嘗試，以取得有關架構的 linting 意見反應，或查看產生的 [合規性資料](../how-to/get-compliance-data.md) ，以查看資源的評估方式。
- 使用各種 SDK 時，SDK 會提供函數失敗原因的詳細資料。
- 使用 Kubernetes 的附加元件時，請從叢集中的 [記錄](../concepts/policy-for-kubernetes.md#logging) 開始。

## <a name="general-errors"></a>一般錯誤

### <a name="scenario-alias-not-found"></a>案例：找不到別名

#### <a name="issue"></a>問題

Azure 原則會使用 [別名](../concepts/definition-structure.md#aliases) 來對應 Azure Resource Manager 屬性。

#### <a name="cause"></a>原因

原則定義中使用了不正確或不存在的別名。

#### <a name="resolution"></a>解決方案

首先，請驗證 Resource Manager 屬性具有別名。 使用 Visual Studio Code、 [Azure Resource Graph](../../resource-graph/samples/starter.md#distinct-alias-values)或 SDK [Azure 原則擴充](../how-to/extension-for-vscode.md)功能來查閱可用的別名。 如果 Resource Manager 屬性的別名不存在，請建立支援票證。

### <a name="scenario-evaluation-details-not-up-to-date"></a>案例：評估詳細資料不是最新狀態

#### <a name="issue"></a>問題

資源處於「未啟動」狀態，或合規性詳細資料不是最新狀態。

#### <a name="cause"></a>原因

套用新的原則或計畫指派大約需要30分鐘。 現有指派範圍內新的或更新的資源將于稍後15分鐘內變成可用。 標準合規性掃描每24小時會進行一次。 如需詳細資訊，請參閱 [評估觸發](../how-to/get-compliance-data.md#evaluation-triggers)程式。

#### <a name="resolution"></a>解決方案

首先，請等候一段適當的時間讓評估完成，並將相容性結果提供給 Azure 入口網站或 SDK。 若要使用 Azure PowerShell 或 REST API 開始新的評估掃描，請參閱隨 [選評估掃描](../how-to/get-compliance-data.md#on-demand-evaluation-scan)。

### <a name="scenario-compliance-not-as-expected"></a>案例：不符合預期的合規性

#### <a name="issue"></a>問題

資源不在評估狀態（ _符合規範_ 或 _不符合規範_）。

#### <a name="cause"></a>原因

資源不在原則指派的正確範圍內，或原則定義未如預期運作。

#### <a name="resolution"></a>解決方案

遵循下列步驟來針對您的原則定義進行疑難排解：

1. 首先，請等候一段適當的時間讓評估完成，並將相容性結果提供給 Azure 入口網站或 SDK。 若要使用 Azure PowerShell 或 REST API 開始新的評估掃描，請參閱隨 [選評估掃描](../how-to/get-compliance-data.md#on-demand-evaluation-scan)。
1. 請檢查指派參數和指派範圍是否已正確設定。
1. 檢查 [原則定義模式](../concepts/definition-structure.md#mode)：
   - 所有資源類型的「全部」模式。
   - 如果原則定義檢查標記或位置，則為「已編制索引」模式。
1. 檢查資源範圍是否未被 [排除](../concepts/assignment-structure.md#excluded-scopes) 或 [豁免](../concepts/exemption-structure.md)。
1. 如果原則指派的合規性顯示 `0/0` 資源，則不會判斷任何資源是否適用于指派範圍內。 檢查原則定義和指派範圍。
1. 針對預期符合規範的不符合規範資源，請檢查 [判斷不符合規範的原因](../how-to/determine-non-compliance.md)。 定義與評估屬性值的比較會指出資源不符合規範的原因。
   - 如果 **目標值** 錯誤，請修改原則定義。
   - 如果 **目前的值** 錯誤，請透過驗證資源承載 `resources.azure.com` 。
1. 檢查 [疑難排解：](#scenario-enforcement-not-as-expected) 其他常見問題和解決方案的強制性不如預期。

如果您仍有重複和自訂內建原則定義或自訂定義的問題，請在 **撰寫原則** 下建立支援票證，以正確地路由問題。

### <a name="scenario-enforcement-not-as-expected"></a>案例：不符合預期的強制

#### <a name="issue"></a>問題

預期由 Azure 原則處理的資源不是，且 [Azure 活動記錄](../../../azure-monitor/platform/platform-logs-overview.md)中沒有任何專案。

#### <a name="cause"></a>原因

原則指派已設定為_停用_ [enforcementMode](../concepts/assignment-structure.md#enforcement-mode) 。 強制模式停用時，不會強制執行原則效果，而且活動記錄中不會有任何專案。

#### <a name="resolution"></a>解決方案

遵循下列步驟來針對您的原則指派強制進行疑難排解：

1. 首先，請等候一段適當的時間讓評估完成，並將相容性結果提供給 Azure 入口網站或 SDK。 若要使用 Azure PowerShell 或 REST API 開始新的評估掃描，請參閱隨 [選評估掃描](../how-to/get-compliance-data.md#on-demand-evaluation-scan)。
1. 請檢查指派參數和指派範圍是否已正確設定，而且已_啟用_ **enforcementMode** 。 
1. 檢查 [原則定義模式](../concepts/definition-structure.md#mode)：
   - 所有資源類型的「全部」模式。
   - 如果原則定義檢查標記或位置，則為「已編制索引」模式。
1. 檢查資源範圍是否未被 [排除](../concepts/assignment-structure.md#excluded-scopes) 或 [豁免](../concepts/exemption-structure.md)。
1. 確認資源承載符合原則邏輯。 這可以藉由 [捕捉 HAR 追蹤](../../../azure-portal/capture-browser-trace.md) 或檢查 ARM 範本屬性來完成。
1. 檢查疑難排解：其他常見問題和解決方案的 [合規性不如預期](#scenario-compliance-not-as-expected) 。

如果您仍有重複和自訂內建原則定義或自訂定義的問題，請在 **撰寫原則** 下建立支援票證，以正確地路由問題。

### <a name="scenario-denied-by-azure-policy"></a>案例： Azure 原則拒絕

#### <a name="issue"></a>問題

資源的建立或更新遭到拒絕。

#### <a name="cause"></a>原因

新資源或已更新資源所在範圍的原則指派，符合具有 [拒絕](../concepts/effects.md#deny) 效果的原則定義準則。 資源會議無法建立或更新這些定義。

#### <a name="resolution"></a>解決方案

拒絕原則指派中的錯誤訊息包含原則定義和原則指派識別碼。 如果訊息中的錯誤資訊遺失，也可以在 [活動記錄](../../../azure-monitor/platform/activity-log.md#view-the-activity-log)中取得。 您可以使用這項資訊來取得更多詳細資料，以瞭解資源限制並調整要求中的資源屬性，以符合允許的值。

## <a name="template-errors"></a>範本錯誤

### <a name="scenario-policy-supported-functions-processed-by-template"></a>案例：範本所處理的原則支援函式

#### <a name="issue"></a>問題

Azure 原則支援多個 Azure Resource Manager 範本 (ARM 範本) 只能在原則定義中使用的函式和函式。 Resource Manager 會將這些函式處理為部署的一部分，而不是原則定義的一部分。

#### <a name="cause"></a>原因

使用支援的函式（例如 `parameter()` 或 `resourceGroup()` ），會在部署期間產生函數的已處理結果，而不是離開原則定義和 Azure 原則引擎的函式來處理。

#### <a name="resolution"></a>解決方案

若要將函式傳遞至作為原則定義的一部分，請將整個字串與屬性類似的屬性（property）進行換用 `[` `[[resourceGroup().tags.myTag]` 。 在處理範本時，escape 字元會使 Resource Manager 將值視為字串。 Azure 原則接著將函式放入原則定義中，讓它能夠如預期般動態。 如需詳細資訊，請參閱 [Azure Resource Manager 範本中的語法和運算式](../../../azure-resource-manager/templates/template-expressions.md)。

## <a name="add-on-installation-errors"></a>附加元件安裝錯誤

### <a name="scenario-install-using-helm-chart-fails-on-password"></a>案例：使用 Helm 圖表的安裝在密碼上失敗

#### <a name="issue"></a>問題

`helm install azure-policy-addon`命令失敗，並出現下列其中一則訊息：

- `!: event not found`
- `Error: failed parsing --set data: key "<key>" has no value (cannot end with ,)`

#### <a name="cause"></a>原因

產生的密碼包含 `,` Helm 圖表正在進行分割的逗號 () 。

#### <a name="resolution"></a>解決方案

在以 `,` `helm install azure-policy-addon` 反斜線 () 執行時， () 密碼值中的逗號 `\` 。

### <a name="scenario-install-using-helm-chart-fails-as-name-already-exists"></a>案例：使用 Helm 圖表的安裝失敗，因為名稱已存在

#### <a name="issue"></a>問題

`helm install azure-policy-addon`命令失敗，並出現下列訊息：

- `Error: cannot re-use a name that is still in use`

#### <a name="cause"></a>原因

已 `azure-policy-addon` 安裝或部分安裝名稱的 Helm 圖表。

#### <a name="resolution"></a>解決方案

遵循指示來 [移除 Kubernetes 附加元件的 Azure 原則](../concepts/policy-for-kubernetes.md#remove-the-add-on)，然後重新執行 `helm install azure-policy-addon` 命令。

## <a name="next-steps"></a>後續步驟

如果您沒有看到您的問題，或無法解決您的問題，請瀏覽下列其中一個管道以取得更多支援：

- 透過 [Microsoft Q&A](/answers/topics/azure-policy.html)取得專家的解答。
- 與 [@AzureSupport](https://twitter.com/azuresupport) 連繫－專為改善客戶體驗而設的官方 Microsoft Azure 帳戶，協助 Azure 社群連接至適當的資源，像是解答、支援及專家等。
- 如果需要更多協助，您可以提出 Azure 支援事件。 請移至 [Azure 支援網站](https://azure.microsoft.com/support/options/)，然後選取 [取得支援]。
