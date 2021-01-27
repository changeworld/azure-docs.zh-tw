---
title: 常見問題疑難排解
description: 瞭解如何針對建立原則定義、各種 Sdk 和 Kubernetes 的附加元件的問題進行疑難排解。
ms.date: 12/01/2020
ms.topic: troubleshooting
ms.openlocfilehash: 6f31f6e6f8d24f83f44dc14112f1bdc90c8af859
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/27/2021
ms.locfileid: "98897066"
---
# <a name="troubleshoot-errors-with-using-azure-policy"></a>使用 Azure 原則針對錯誤進行疑難排解

當您建立原則定義、使用 Sdk，或設定 Kubernetes 附加元件的 [Azure 原則](../concepts/policy-for-kubernetes.md) 時，您可能會遇到錯誤。 本文描述可能發生的各種一般錯誤，並建議解決這些錯誤的方法。

## <a name="find-error-details"></a>尋找錯誤詳細資料

錯誤詳細資料的位置取決於您正在使用 Azure 原則的層面。

- 如果您正在使用自訂原則，請移至 Azure 入口網站以取得有關架構的 linting 意見反應，或查看所產生的 [合規性資料](../how-to/get-compliance-data.md) ，以查看資源的評估方式。
- 如果您使用的是各種 Sdk，SDK 會提供函式失敗原因的詳細資料。
- 如果您使用 Kubernetes 的附加元件，請從叢集中的 [記錄](../concepts/policy-for-kubernetes.md#logging) 開始。

## <a name="general-errors"></a>一般錯誤

### <a name="scenario-alias-not-found"></a>案例：找不到別名

#### <a name="issue"></a>問題

原則定義中使用了不正確或不存在的別名。 Azure 原則會使用 [別名](../concepts/definition-structure.md#aliases) 來對應 Azure Resource Manager 屬性。

#### <a name="cause"></a>原因

原則定義中使用了不正確或不存在的別名。

#### <a name="resolution"></a>解決方案

首先，請驗證 Resource Manager 屬性具有別名。 若要查詢可用的別名，請移至 Visual Studio Code 或 SDK 的 [Azure 原則延伸](../how-to/extension-for-vscode.md) 模組。 如果 Resource Manager 屬性的別名不存在，請建立支援票證。

### <a name="scenario-evaluation-details-arent-up-to-date"></a>案例：評估詳細資料不是最新的

#### <a name="issue"></a>問題

資源處於 [ *未啟動* ] 狀態，或 [合規性詳細資料] 不是最新狀態。

#### <a name="cause"></a>原因

套用新的原則或計畫指派大約需要30分鐘。 現有指派範圍內新的或更新的資源會在大約15分鐘內變成可用。 標準合規性掃描每24小時進行一次。 如需詳細資訊，請參閱 [評估觸發](../how-to/get-compliance-data.md#evaluation-triggers)程式。

#### <a name="resolution"></a>解決方案

首先，請等候一段適當的時間讓評估完成，並將相容性結果提供給 Azure 入口網站或 SDK。 若要使用 Azure PowerShell 或 REST API 開始新的評估掃描，請參閱隨 [選評估掃描](../how-to/get-compliance-data.md#on-demand-evaluation-scan)。

### <a name="scenario-compliance-isnt-as-expected"></a>案例：合規性不符合預期

#### <a name="issue"></a>問題

資源不是預期的資源 _符合規範_ 或 _不符合規範_ 的評估狀態。

#### <a name="cause"></a>原因

資源不在原則指派的正確範圍內，或原則定義未如預期運作。

#### <a name="resolution"></a>解決方案

若要對原則定義進行疑難排解，請執行下列動作：

1. 首先，請等候一段適當的時間讓評估完成，並將相容性結果提供給 Azure 入口網站或 SDK。 

1. 若要使用 Azure PowerShell 或 REST API 開始新的評估掃描，請參閱隨 [選評估掃描](../how-to/get-compliance-data.md#on-demand-evaluation-scan)。
1. 確定已正確設定指派參數和指派範圍。
1. 檢查[原則定義模式](../concepts/definition-structure.md#mode)：
   - `all`適用于所有資源類型的模式。
   - 如果原則定義檢查標籤或位置，則模式應該是 `indexed` 。
1. 確定資源範圍未被 [排除](../concepts/assignment-structure.md#excluded-scopes) 或 [豁免](../concepts/exemption-structure.md)。
1. 如果原則指派的合規性顯示 `0/0` 資源，則不會判斷任何資源是否適用于指派範圍內。 檢查原則定義和指派範圍。
1. 若為預期符合規範的資源，請參閱判斷不符合 [規範的原因](../how-to/determine-non-compliance.md)。 定義與評估屬性值的比較會指出資源為何不符合規範。
   - 如果 **目標值** 錯誤，請修改原則定義。
   - 如果 **目前的值** 錯誤，請透過驗證資源承載 `resources.azure.com` 。
1. 如需其他常見的問題和解決方案，請參閱 [疑難排解：強制執行非預期](#scenario-enforcement-not-as-expected)的。

如果您仍有重複和自訂內建原則定義或自訂定義的問題，請在 **撰寫原則** 下建立支援票證，以正確地路由問題。

### <a name="scenario-enforcement-not-as-expected"></a>案例：不符合預期的強制

#### <a name="issue"></a>問題

您預期 Azure 原則處理的資源未進行處理，且 [Azure 活動記錄](../../../azure-monitor/platform/platform-logs-overview.md)中沒有任何專案。

#### <a name="cause"></a>原因

原則指派已設定為 [_已停用_] 的 [**enforcementMode**](../concepts/assignment-structure.md#enforcement-mode)設定。 停用 **enforcementMode** 時，不會強制執行原則效果，而且活動記錄中不會有任何專案。

#### <a name="resolution"></a>解決方案

藉由執行下列動作來針對原則指派的強制進行疑難排解：

1. 首先，請等候一段適當的時間讓評估完成，並將相容性結果提供給 Azure 入口網站或 SDK。 

1. 若要使用 Azure PowerShell 或 REST API 開始新的評估掃描，請參閱隨 [選評估掃描](../how-to/get-compliance-data.md#on-demand-evaluation-scan)。
1. 確定已正確設定指派參數和指派範圍，且已啟用 **enforcementMode** 。 
1. 檢查[原則定義模式](../concepts/definition-structure.md#mode)：
   - `all`適用于所有資源類型的模式。
   - 如果原則定義檢查標籤或位置，則模式應該是 `indexed` 。
1. 確定資源範圍未被 [排除](../concepts/assignment-structure.md#excluded-scopes) 或 [豁免](../concepts/exemption-structure.md)。
1. 確認資源承載符合原則邏輯。 這可以藉由將 [HTTP 封存 (HAR) 追蹤](../../../azure-portal/capture-browser-trace.md) 或查看 Azure Resource Manager 範本 (ARM 範本) 屬性來完成。
1. 如需其他常見的問題和解決方案，請參閱 [疑難排解：不符合預期的合規性](#scenario-compliance-isnt-as-expected)。

如果您仍有重複和自訂內建原則定義或自訂定義的問題，請在 **撰寫原則** 下建立支援票證，以正確地路由問題。

### <a name="scenario-denied-by-azure-policy"></a>案例： Azure 原則拒絕

#### <a name="issue"></a>問題

資源的建立或更新遭到拒絕。

#### <a name="cause"></a>原因

新資源或已更新資源範圍的原則指派，符合具有 [拒絕](../concepts/effects.md#deny) 效果的原則定義準則。 無法建立或更新符合這些定義的資源。

#### <a name="resolution"></a>解決方案

拒絕原則指派中的錯誤訊息包含原則定義和原則指派識別碼。 如果訊息中的錯誤資訊遺失，也可以在 [活動記錄](../../../azure-monitor/platform/activity-log.md#view-the-activity-log)中取得。 您可以使用這項資訊來取得更多詳細資料，以瞭解資源限制並調整要求中的資源屬性，以符合允許的值。

## <a name="template-errors"></a>範本錯誤

### <a name="scenario-policy-supported-functions-processed-by-template"></a>案例：範本所處理的原則支援函式

#### <a name="issue"></a>問題

Azure 原則支援一些僅適用于原則定義的 ARM 範本函數和函式。 Resource Manager 會將這些函式處理為部署的一部分，而不是原則定義的一部分。

#### <a name="cause"></a>原因

使用支援的函式（例如 `parameter()` 或 `resourceGroup()` ），會在部署期間產生函數的已處理結果，而不是允許原則定義和 Azure 原則引擎的函式進行處理。

#### <a name="resolution"></a>解決方案

若要透過作為原則定義的一部分來傳遞函式，請使用屬性的形式來將整個字串全部 escape `[` `[[resourceGroup().tags.myTag]` 。 Escape 字元會在處理範本時，讓 Resource Manager 將值視為字串。 Azure 原則接著將函式放入原則定義中，讓它能夠如預期般動態。 如需詳細資訊，請參閱 [Azure Resource Manager 範本中的語法和運算式](../../../azure-resource-manager/templates/template-expressions.md)。

## <a name="add-on-for-kubernetes-installation-errors"></a>Kubernetes 安裝錯誤的附加元件

### <a name="scenario-installation-by-using-a-helm-chart-fails-because-of-a-password-error"></a>案例：使用 Helm 圖表進行安裝失敗，因為發生密碼錯誤

#### <a name="issue"></a>問題

此 `helm install azure-policy-addon` 命令會失敗，並傳回下列其中一個錯誤：

- `!: event not found`
- `Error: failed parsing --set data: key "<key>" has no value (cannot end with ,)`

#### <a name="cause"></a>原因

產生的密碼包含 Helm 圖所分割的逗號 (`,`) 。

#### <a name="resolution"></a>解決方案

當您執行時 `helm install azure-policy-addon` ，請 `,` 使用反斜線 () ，以反斜線 () 的密碼值 `\` 。

### <a name="scenario-installation-by-using-a-helm-chart-fails-because-the-name-already-exists"></a>案例：使用 Helm 圖進行安裝失敗，因為名稱已存在

#### <a name="issue"></a>問題

此 `helm install azure-policy-addon` 命令會失敗，並傳回下列錯誤：

- `Error: cannot re-use a name that is still in use`

#### <a name="cause"></a>原因

已 `azure-policy-addon` 安裝或部分安裝名稱的 Helm 圖表。

#### <a name="resolution"></a>解決方案

依照指示 [移除 Kubernetes 附加元件的 Azure 原則](../concepts/policy-for-kubernetes.md#remove-the-add-on)，然後重新執行 `helm install azure-policy-addon` 命令。

### <a name="scenario-azure-virtual-machine-user-assigned-identities-are-replaced-by-system-assigned-managed-identities"></a>案例： Azure 虛擬機器使用者指派的身分識別會取代為系統指派的受控識別

#### <a name="issue"></a>問題

當您將「來賓設定原則」方案指派給機器內的設定之後，就不會再指派指派給該電腦的使用者指派受控識別。 只會指派系統指派的受控識別。

#### <a name="cause"></a>原因

先前用於來賓設定 DeployIfNotExists 定義中的原則定義可確保系統指派的身分識別會指派給電腦，但也會移除使用者指派的身分識別指派。

#### <a name="resolution"></a>解決方案

先前造成此問題的定義會顯示為 *[已淘汰]*，而不會移除使用者指派的受控識別，則會以管理必要條件的原則定義取代這些定義。 需要手動步驟。 刪除任何標記為 *[已淘汰]* 的現有原則指派，並將其取代為與原始的相同名稱的更新必要條件原則計畫和原則定義。

如需詳細敘述的詳細資訊，請參閱 blog 張貼 [針對來賓設定稽核原則所發行的重要變更](https://techcommunity.microsoft.com/t5/azure-governance-and-management/important-change-released-for-guest-configuration-audit-policies/ba-p/1655316)。

## <a name="add-on-for-kubernetes-general-errors"></a>Kubernetes 一般錯誤的附加元件

### <a name="scenario-the-add-on-is-unable-to-reach-the-azure-policy-service-endpoint-because-of-egress-restrictions"></a>案例：附加元件因為輸出限制而無法連接 Azure 原則服務端點

#### <a name="issue"></a>問題

附加元件無法連線到 Azure 原則服務端點，而且會傳回下列其中一個錯誤：

- `failed to fetch token, service not reachable`
- `Error getting file "Get https://raw.githubusercontent.com/Azure/azure-policy/master/built-in-references/Kubernetes/container-allowed-images/template.yaml: dial tcp 151.101.228.133.443: connect: connection refused`

#### <a name="cause"></a>原因

當叢集輸出鎖定時，就會發生此問題。

#### <a name="resolution"></a>解決方案

確定已開啟下列文章中所述的網域和埠：

- [AKS 叢集 (Fqdn) 所需的輸出網路規則和完整功能變數名稱](../../../aks/limit-egress-traffic.md#required-outbound-network-rules-and-fqdns-for-aks-clusters)
- [針對已啟用 Azure Arc 的 Kubernetes 安裝 Azure 原則附加元件 (preview) ](../concepts/policy-for-kubernetes.md#install-azure-policy-add-on-for-azure-arc-enabled-kubernetes)

### <a name="scenario-the-add-on-is-unable-to-reach-the-azure-policy-service-endpoint-because-of-the-aad-pod-identity-configuration"></a>案例：附加元件因為 aad-pod-身分識別設定而無法連線到 Azure 原則服務端點

#### <a name="issue"></a>問題

附加元件無法連線到 Azure 原則服務端點，而且會傳回下列其中一個錯誤：

- `azure.BearerAuthorizer#WithAuthorization: Failed to refresh the Token for request to https://gov-prod-policy-data.trafficmanager.net/checkDataPolicyCompliance?api-version=2019-01-01-preview: StatusCode=404`
- `adal: Refresh request failed. Status Code = '404'. Response body: getting assigned identities for pod kube-system/azure-policy-8c785548f-r882p in CREATED state failed after 16 attempts, retry duration [5]s, error: <nil>`

#### <a name="cause"></a>原因

當安裝在叢集上，且 _kube 系統_ _pod 未在_ aad-身分 _識別_ 中排除時，就會發生此錯誤。

_Aad-pod-身分識別_ 元件節點受控身分識別 (NMI) pod 會修改節點的 iptables 來攔截對 Azure 實例中繼資料端點的呼叫。 這項設定表示，對中繼資料端點提出的任何要求都會由 NMI 攔截，即使 pod 未使用 aad （ _aad_）身分識別。
您可以設定 *AzurePodIdentityException* CUSTOMRESOURCEDEFINITION (.crd) ，以通知 _aad-pod-_ 對於源自于 .crd 中所定義之標籤的 pod 所發出的中繼資料端點的任何要求，都應該是 proxy，而不需要在 NMI 中進行任何處理。

#### <a name="resolution"></a>解決方案

藉 `kubernetes.azure.com/managedby: aks` 由設定 *AzurePodIdentityException* .crd，將具有 _kube 系統_ 命名空間中標籤的系統 pod 排除在 _aad-pod 身分識別_ 中。

如需詳細資訊，請參閱 [停用特定 pod/應用程式的 Azure Active Directory (Azure AD) pod 身分識別](https://azure.github.io/aad-pod-identity/docs/configure/application_exception)。

若要設定例外狀況，請遵循此範例：

```yaml
apiVersion: "aadpodidentity.k8s.io/v1"
kind: AzurePodIdentityException
metadata:
  name: mic-exception
  namespace: default
spec:
  podLabels:
    app: mic
    component: mic
---
apiVersion: "aadpodidentity.k8s.io/v1"
kind: AzurePodIdentityException
metadata:
  name: aks-addon-exception
  namespace: kube-system
spec:
  podLabels:
    kubernetes.azure.com/managedby: aks
```

### <a name="scenario-the-resource-provider-isnt-registered"></a>案例：資源提供者未註冊

#### <a name="issue"></a>問題

附加元件可以觸達 Azure 原則服務端點，但附加元件記錄會顯示下列其中一個錯誤：

- `The resource provider 'Microsoft.PolicyInsights' is not registered in subscription '{subId}'. See
https://aka.ms/policy-register-subscription for how to register subscriptions.`

- `policyinsightsdataplane.BaseClient#CheckDataPolicyCompliance: Failure responding to request:
StatusCode=500 -- Original Error: autorest/azure: Service returned an error. Status=500
Code="InternalServerError" Message="Encountered an internal server error.`

#### <a name="cause"></a>原因

未註冊 ' Microsoft.policyinsights ' 資源提供者。 它必須針對附加元件註冊，才能取得原則定義並傳回合規性資料。

#### <a name="resolution"></a>解決方案

在叢集訂用帳戶中註冊 ' Microsoft.policyinsights ' 資源提供者。 如需相關指示，請參閱 [註冊資源提供者](../../../azure-resource-manager/management/resource-providers-and-types.md#register-resource-provider)。

### <a name="scenario-the-subscription-is-disabled"></a>案例：訂用帳戶已停用

#### <a name="issue"></a>問題

附加元件可以連接 Azure 原則服務端點，但會顯示下列錯誤：

`The subscription '{subId}' has been disabled for azure data-plane policy. Please contact support.`

#### <a name="cause"></a>原因

此錯誤表示訂用帳戶已判定為有問題，而且已新增功能旗標 `Microsoft.PolicyInsights/DataPlaneBlocked` 來封鎖訂用帳戶。

#### <a name="resolution"></a>解決方案

若要調查並解決此問題，請 [洽詢功能小組](mailto:azuredg@microsoft.com)。

## <a name="next-steps"></a>後續步驟

如果您的問題未列于本文中，或您無法解決問題，請造訪下列其中一個通道來取得支援：

- 透過 [Microsoft Q&A](/answers/topics/azure-policy.html)取得專家的解答。
- 聯繫 [@AzureSupport](https://twitter.com/azuresupport)。 這項官方 Microsoft Azure 在 Twitter 上的資源可協助您將 Azure 社區連接至正確的解答、支援及專家，以改善客戶體驗。
- 如果您仍然需要協助，請移至 [Azure 支援網站](https://azure.microsoft.com/support/options/) ，然後選取 [ **提交支援要求**]。
