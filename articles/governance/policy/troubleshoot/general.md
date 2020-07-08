---
title: 常見問題疑難排解
description: 瞭解如何針對建立原則定義、各種 SDK 和 Kubernetes 附加元件的問題進行疑難排解。
ms.date: 05/22/2020
ms.topic: troubleshooting
ms.openlocfilehash: 868b8c53a120ebdb6a35806538e02af39e25c338
ms.sourcegitcommit: f684589322633f1a0fafb627a03498b148b0d521
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/06/2020
ms.locfileid: "85970836"
---
# <a name="troubleshoot-errors-using-azure-policy"></a>使用 Azure 原則疑難排解錯誤

當您建立原則定義、使用 SDK 或設定 Kubernetes 附加元件的[Azure 原則](../concepts/policy-for-kubernetes.md)時，可能會遇到錯誤。 此文章說明可能發生的各種錯誤與解決方式。

## <a name="finding-error-details"></a>尋找錯誤詳細資料

錯誤詳細資料的位置取決於導致錯誤的動作。

- 使用自訂原則時，請在 Azure 入口網站中嘗試，以取得有關架構的 linting 意見反應，或查看產生的[合規性資料](../how-to/get-compliance-data.md)，以查看資源的評估方式。
- 使用各種 SDK 時，SDK 會提供有關函數失敗原因的詳細資料。
- 使用 Kubernetes 的附加元件時，請從叢集中的[記錄](../concepts/policy-for-kubernetes.md#logging)開始。

## <a name="general-errors"></a>一般錯誤

### <a name="scenario-alias-not-found"></a>案例：找不到別名

#### <a name="issue"></a>問題

Azure 原則會使用[別名](../concepts/definition-structure.md#aliases)來對應 Azure Resource Manager 屬性。

#### <a name="cause"></a>原因

原則定義中使用了不正確或不存在的別名。

#### <a name="resolution"></a>解決方案

首先，驗證 Resource Manager 屬性是否有別名。 使用 Visual Studio Code、 [Azure Resource Graph](../../resource-graph/samples/starter.md#distinct-alias-values)或 SDK [Azure 原則延伸](../how-to/extension-for-vscode.md)模組來查閱可用的別名。 如果 Resource Manager 屬性的別名不存在，請建立支援票證。

### <a name="scenario-evaluation-details-not-up-to-date"></a>案例：評估詳細資料不是最新狀態

#### <a name="issue"></a>問題

資源處於「未啟動」狀態，或不是目前的合規性詳細資料。

#### <a name="cause"></a>原因

套用新的原則或方案指派大約需要30分鐘的時間。 在現有指派的範圍內，新的或更新的資源會在15分鐘後變為可用。 標準合規性掃描會每24小時發生一次。 如需詳細資訊，請參閱[評估觸發](../how-to/get-compliance-data.md#evaluation-triggers)程式。

#### <a name="resolution"></a>解決方案

首先，請等候適當的時間量完成評估，並在 Azure 入口網站或 SDK 中提供合規性結果。 若要使用 Azure PowerShell 或 REST API 開始新的評估掃描，請參閱隨[選評估掃描](../how-to/get-compliance-data.md#on-demand-evaluation-scan)。

### <a name="scenario-evaluation-not-as-expected"></a>案例：不符合預期的評估

#### <a name="issue"></a>問題

資源不在評估狀態（_符合規範_或_不符合規範_），這是預期的資源。

#### <a name="cause"></a>原因

資源不在原則指派的正確範圍內，或原則定義未如預期運作。

#### <a name="resolution"></a>解決方案

- 對於預期符合規範的不相容資源，請從[判斷不符合規範的原因](../how-to/determine-non-compliance.md)開始。 定義與評估屬性值的比較，表示資源不符合規範的原因。
- 對於預期不相容的相容資源，請依條件讀取原則定義條件，並針對 resources 屬性進行評估。 驗證邏輯運算子會將正確的條件群組在一起，而且您的條件不會反轉。

如果原則指派的相容性顯示 `0/0` 資源，則不會在指派範圍內判斷是否有任何資源適用。 檢查原則定義和指派範圍。

### <a name="scenario-enforcement-not-as-expected"></a>案例：強制執行不符合預期

#### <a name="issue"></a>問題

預期會由 Azure 原則處理的資源不在[Azure 活動記錄](../../../azure-monitor/platform/platform-logs-overview.md)中，因此不會有任何專案。

#### <a name="cause"></a>原因

已針對_停用_的[enforcementMode](../concepts/assignment-structure.md#enforcement-mode)設定原則指派。 強制模式停用時，不會強制執行原則效果，而且活動記錄中不會有任何專案。

#### <a name="resolution"></a>解決方案

將**enforcementMode**更新為 [_已啟用_]。 這項變更可讓 Azure 原則對此原則指派中的資源採取動作，並將專案傳送至活動記錄。 如果已啟用**enforcementMode** ，請參閱適用于動作課程的[評估不符合預期](#scenario-evaluation-not-as-expected)。

### <a name="scenario-denied-by-azure-policy"></a>案例：被 Azure 原則拒絕

#### <a name="issue"></a>問題

拒絕建立或更新資源。

#### <a name="cause"></a>原因

原則指派至新的或已更新的資源所在的範圍，符合原則定義的條件並具有[拒絕](../concepts/effects.md#deny)效果。 資源會議：無法建立或更新這些定義。

#### <a name="resolution"></a>解決方案

拒絕原則指派中的錯誤訊息包含原則定義和原則指派識別碼。 如果遺漏訊息中的錯誤資訊，它也會出現在[活動記錄](../../../azure-monitor/platform/activity-log-view.md)中。 使用此資訊可取得更多詳細資料，以瞭解資源限制，並在您的要求中調整資源屬性，以符合允許的值。

## <a name="template-errors"></a>範本錯誤

### <a name="scenario-policy-supported-functions-processed-by-template"></a>案例：範本所處理的原則支援函式

#### <a name="issue"></a>問題

Azure 原則支援一些只能在原則定義中使用的 Azure Resource Manager 範本（ARM 範本）功能和函式。 Resource Manager 會將這些函式當做部署的一部分來處理，而不是做為原則定義的一部分。

#### <a name="cause"></a>原因

使用支援的函式（例如 `parameter()` 或），會在部署時產生函式的已 `resourceGroup()` 處理結果，而不是離開原則定義和 Azure 原則引擎的函式。

#### <a name="resolution"></a>解決方案

若要將函式傳遞至，使其成為原則定義的一部分，請使用來將整個字串與此屬性（property）類似 `[` `[[resourceGroup().tags.myTag]` 。 在處理範本時，escape 字元會使 Resource Manager 將值視為字串。 Azure 原則接著會將函式放入原則定義中，讓它能夠如預期般動態。 如需詳細資訊，請參閱[Azure Resource Manager 範本中的語法和運算式](../../../azure-resource-manager/templates/template-expressions.md)。

## <a name="add-on-installation-errors"></a>附加元件安裝錯誤

### <a name="scenario-install-using-helm-chart-fails-on-password"></a>案例：使用 Helm 圖表進行安裝時，密碼會失敗

#### <a name="issue"></a>問題

此 `helm install azure-policy-addon` 命令失敗，並出現下列其中一則訊息：

- `!: event not found`
- `Error: failed parsing --set data: key "<key>" has no value (cannot end with ,)`

#### <a name="cause"></a>原因

產生的密碼包含以逗號（ `,` ）作為 Helm 圖表的分割依據。

#### <a name="resolution"></a>解決方案

`,` `helm install azure-policy-addon` 使用反斜線（）執行時，請在 password 值中將逗號（）換用 `\` 。

### <a name="scenario-install-using-helm-chart-fails-as-name-already-exists"></a>案例：使用 Helm 圖安裝失敗，因為名稱已經存在

#### <a name="issue"></a>問題

此 `helm install azure-policy-addon` 命令失敗，並出現下列訊息：

- `Error: cannot re-use a name that is still in use`

#### <a name="cause"></a>原因

已安裝或部分安裝名稱為 Helm 的圖表 `azure-policy-addon` 。

#### <a name="resolution"></a>解決方案

遵循指示來[移除 Kubernetes 附加元件的 Azure 原則](../concepts/policy-for-kubernetes.md#remove-the-add-on)，然後重新執行 `helm install azure-policy-addon` 命令。

## <a name="next-steps"></a>後續步驟

如果您沒有看到您的問題，或無法解決您的問題，請瀏覽下列其中一個管道以取得更多支援：

- 透過[Microsoft Q&A](/answers/topics/azure-policy.html)取得專家提供的解答。
- 與 [@AzureSupport](https://twitter.com/azuresupport) 連繫－專為改善客戶體驗而設的官方 Microsoft Azure 帳戶，協助 Azure 社群連接至適當的資源，像是解答、支援及專家等。
- 如果需要更多協助，您可以提出 Azure 支援事件。 請移至 [Azure 支援網站](https://azure.microsoft.com/support/options/)，然後選取 [取得支援]。
