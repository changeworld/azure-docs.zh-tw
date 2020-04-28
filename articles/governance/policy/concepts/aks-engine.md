---
title: 瞭解適用于 AKS 引擎的 Azure 原則
description: 瞭解 Azure 原則如何使用 CustomResourceDefinitions，並從閘道管理員 v3 開啟原則代理程式，以使用 AKS 引擎來管理叢集。
ms.date: 11/04/2019
ms.topic: conceptual
ms.openlocfilehash: c41a9d84dfe43e356e9a4a17af523a37209c2933
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "75436432"
---
# <a name="understand-azure-policy-for-aks-engine"></a>瞭解 AKS 引擎 Azure 原則

Azure 原則與[AKS Engine](https://github.com/Azure/aks-engine/blob/master/docs/README.md)整合，這是一種系統，可提供便利的工具，在 Azure 上快速啟動自我管理的 Kubernetes 叢集。 這項整合可讓您以集中、一致的方式，在 AKS 引擎自我管理的叢集上進行大規模的實行原則和保護。 藉由擴充[開放式原則代理程式](https://www.openpolicyagent.org/)（OPA）[閘道管理員](https://github.com/open-policy-agent/gatekeeper)（搶鮮版（Beta））（適用于 Kubernetes 的_許可控制站 webhook_ ），Azure 原則可讓您從一個位置管理和報告 Azure 資源和 AKS 引擎自我管理叢集的合規性狀態。

> [!NOTE]
> 適用于 AKS 引擎的 Azure 原則處於公開預覽狀態，而且沒有 SLA。 閘道管理員 v3 處於搶鮮版（Beta），並受到開放原始碼社區的支援。 服務僅支援內建原則定義，以及使用服務主體設定之每個資源群組的單一 AKS 引擎叢集。

> [!IMPORTANT]
> 若要取得 AKS 引擎、AKS 引擎或閘道管理員 v3 Azure 原則的支援，請在 AKS 引擎 GitHub 存放庫中建立[新的問題](https://github.com/Azure/aks-engine/issues/new/choose)。

## <a name="overview"></a>概觀

若要在 Azure 上以自我管理的 Kubernetes 叢集啟用和使用 Azure 原則 for AKS Engine，請採取下列動作：

- [先決條件](#prerequisites)
- [安裝 Azure 原則附加元件](#installing-the-add-on)
- [指派 AKS 引擎的原則定義](#built-in-policies)
- [等待驗證](#validation-and-reporting-frequency)

## <a name="prerequisites"></a>先決條件

在安裝 Azure 原則附加元件或啟用任何服務功能之前，您的訂用帳戶必須啟用**microsoft.policyinsights**資源提供者，並建立叢集服務主體的角色指派。 

1. 若要啟用資源提供者，請遵循[資源提供者和類型](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-portal)中的步驟，或執行 Azure CLI 或 Azure PowerShell 命令：

   - Azure CLI

     ```azurecli-interactive
     # Log in first with az login if you're not using Cloud Shell
     
     # Provider register: Register the Azure Policy provider
     az provider register --namespace 'Microsoft.PolicyInsights'
     ```

   - Azure PowerShell
   
     ```azurepowershell-interactive
     # Log in first with Connect-AzAccount if you're not using Cloud Shell
   
     # Provider register: Register the Azure Policy provider
     Register-AzResourceProvider -ProviderNamespace 'Microsoft.PolicyInsights'
     ```

1. 建立叢集服務主體的角色指派

   - 如果您不知道叢集服務主體應用程式識別碼，請使用下列命令來查閱它。

     ```bash
     # Get the kube-apiserver pod name
     kubectl get pods -n kube-system
   
     # Find the aadClientID value
     kubectl exec <kube-apiserver pod name> -n kube-system cat /etc/kubernetes/azure.json
     ```

   - 將「原則深入解析資料寫入器外掛程式（預覽）」角色指派指派給叢集服務主體應用程式識別碼（從上一個步驟_aadClientID_的值）與 Azure CLI。 請`<subscriptionId>`以您的訂用`<aks engine cluster resource group>`帳戶 ID 取代，並以 AKS 引擎自我管理 Kubernetes 叢集所在的資源群組取代。

     ```azurecli-interactive
     az role assignment create --assignee <cluster service principal app ID> --scope "/subscriptions/<subscriptionId>/resourceGroups/<aks engine cluster resource group>" --role "Policy Insights Data Writer (Preview)"
     ```

## <a name="azure-policy-add-on"></a>Azure 原則附加元件

Kubernetes 的_Azure 原則附加_元件會將 Azure 原則服務連接到閘道管理員的許可控制站。 安裝在_kube 系統_命名空間中的附加元件會制定下列功能：

- 檢查是否有 Azure 原則指派給 AKS 引擎叢集
- 下載並安裝原則詳細資料、條件約束範本和條件約束
- 在 AKS 引擎叢集上執行完整掃描合規性檢查
- 將審核和合規性詳細資料包告回到 Azure 原則

### <a name="installing-the-add-on"></a>安裝附加元件

完成必要條件之後，即可安裝 Azure 原則附加元件。 安裝可能是在 AKS 引擎建立或更新週期期間，或是在現有叢集上做為獨立動作。

- 在建立或更新週期期間安裝

  若要在建立新的自我管理叢集或現有叢集的更新期間啟用 Azure 原則附加元件，請包含 AKS 引擎的**附加元件**屬性叢集定義。

  ```json
  "addons": [{
      "name": "azure-policy",
      "enabled": true,
      "config": {
          "auditInterval": "30",
          "constraintViolationsLimit": "20"
      }
  }]
  ```

  如需的詳細資訊，請參閱 external guide [AKS Engine cluster definition](https://github.com/Azure/aks-engine/blob/master/docs/topics/clusterdefinitions.md)。

- 在具有 Helm 圖表的現有叢集中安裝

  使用下列步驟來準備叢集並安裝附加元件：

  1. 將閘道管理員安裝到_閘道管理員系統_命名空間。

     ```bash
     kubectl apply -f https://raw.githubusercontent.com/open-policy-agent/gatekeeper/master/deploy/gatekeeper.yaml
     ```
  1. 將 [_控制平面_] 標籤新增至 [ _kube-系統_]。 此標籤會排除閘道管理員和 Azure 原則附加元件的_kube 系統_pod 和服務的審核。

     ```bash
     kubectl label namespaces kube-system control-plane=controller-manager
     ```

  1. 使用 OPA 同步 Kubernetes 資料（命名空間、Pod、輸入、服務）。

     ```bash
     kubectl apply -f https://raw.githubusercontent.com/Azure/azure-policy/master/built-in-references/Kubernetes/gatekeeper-opa-sync.yaml
     ```

     如需詳細資訊，請參閱[OPA-複寫資料](https://github.com/open-policy-agent/gatekeeper#replicating-data)。

  1. 將 Azure 原則存放庫新增至 Helm。

     ```bash
     helm repo add azure-policy https://raw.githubusercontent.com/Azure/azure-policy/master/extensions/policy-addon-kubernetes/helm-charts
     ```

     如需詳細資訊，請參閱[Helm 圖表-快速入門手冊](https://helm.sh/docs/using_helm/#quickstart-guide)。

  1. 以 Helm 圖表安裝附加元件。 請`<subscriptionId>`以您的訂用`<aks engine cluster resource group>`帳戶 ID 取代，並以 AKS 引擎自我管理 Kubernetes 叢集所在的資源群組取代。

     ```bash
     helm install azure-policy/azure-policy-addon-aks-engine --name azure-policy-addon --set azurepolicy.env.resourceid="/subscriptions/<subscriptionId>/resourceGroups/<aks engine cluster resource group>"
     ```

     如需附加元件 Helm 圖表安裝內容的詳細資訊，請參閱 GitHub 上的[Azure 原則附加元件 Helm 圖表定義](https://github.com/Azure/azure-policy/tree/master/extensions/policy-addon-kubernetes/helm-charts)。

     > [!NOTE]
     > 由於 Azure 原則附加元件與資源群組識別碼之間的關聯性，Azure 原則僅針對每個資源群組支援一個 AKS 引擎叢集。

若要驗證附加元件安裝是否成功，以及_azure 原則_pod 是否正在執行，請執行下列命令：

```bash
kubectl get pods -n kube-system
```

### <a name="validation-and-reporting-frequency"></a>驗證和報告頻率

此附加元件會每隔5分鐘檢查一次原則指派中的變更 Azure 原則。 在此重新整理週期期間，附加元件會檢查是否有變更。 這些變更會觸發條件約束範本和條件約束的建立、更新或刪除。

> [!NOTE]
> 雖然叢集系統_管理員_可能具有變更條件約束範本和條件約束的許可權，但不建議或不支援對 Azure 原則所建立的條件約束範本或條件約束進行變更。 在重新整理週期期間，所做的任何手動變更都會遺失。

每隔5分鐘，附加元件會呼叫叢集的完整掃描。 在收集完整掃描的詳細資料以及嘗試變更叢集的任何即時評估之後，附加元件會將結果回報給 Azure 原則，以納入任何 Azure 原則指派的[相容性詳細資料](../how-to/get-compliance-data.md)。 在 audit 迴圈期間，只會傳回作用中原則指派的結果。 Audit 結果也可以視為失敗條件約束的 [狀態] 欄位中所列的違規。

## <a name="policy-language"></a>原則語言

管理 AKS 引擎的 Azure 原則語言結構會遵循現有原則的指示。 效果_EnforceOPAConstraint_是用來管理您的 AKS 引擎叢集，並使用特定的_詳細資料_屬性來使用[OPA 條件約束架構](https://github.com/open-policy-agent/frameworks/tree/master/constraint)和閘道管理員 v3。 如需詳細資訊和範例，請參閱[EnforceOPAConstraint](effects.md#enforceopaconstraint)效果。

做為_詳細資料 constraintTemplate_和_詳細資料。_ 原則定義中的條件約束屬性，Azure 原則將這些[CustomResourceDefinitions](https://github.com/open-policy-agent/gatekeeper#constraint-templates) （.crd）的 uri 傳遞至附加元件。 Rego 是 OPA 和閘道管理員支援向 Kubernetes 叢集驗證要求的語言。 藉由支援現有的 Kubernetes 管理標準，Azure 原則可讓您重複使用現有的規則，並將其與 Azure 原則結合，以取得統一的雲端合規性報告體驗。 如需詳細資訊，請參閱[什麼是 Rego？](https://www.openpolicyagent.org/docs/how-do-i-write-policies.html#what-is-rego)。

## <a name="built-in-policies"></a>內建原則

若要尋找使用 Azure 入口網站來管理 AKS 引擎叢集的內建原則，請遵循下列步驟：

1. 啟動 Azure 入口網站中的 Azure 原則服務。 選取左窗格中的 [**所有服務**]，然後搜尋並選取 [**原則**]。

1. 在 [Azure 原則] 頁面的左窗格中，選取 [**定義**]。

1. 從 [類別] 下拉式清單方塊中，使用 [**全選**] 清除篩選，然後選取 [ **Kubernetes**]。

1. 選取原則定義，然後選取 [**指派**] 按鈕。

> [!NOTE]
> 指派 AKS 引擎定義的 Azure 原則時，**範圍**必須是 AKS 引擎叢集的資源群組。

或者，使用 [[指派原則-入口網站](../assign-policy-portal.md)] 快速入門來尋找並指派 AKS 引擎原則。 搜尋 AKS 引擎原則定義，而不是範例「audit vm」。

> [!IMPORTANT]
> 類別**Kubernetes**中的內建原則僅適用于 AKS 引擎。

## <a name="logging"></a>記錄

### <a name="azure-policy-add-on-logs"></a>Azure 原則附加元件記錄檔

Azure 原則附加元件是 Kubernetes 控制器/容器，會將記錄保留在 AKS 引擎叢集中。

若要查看 Azure 原則附加元件記錄檔，請`kubectl`使用：

```bash
# Get the Azure Policy Add-on pod name
kubectl -n kube-system get pods -l app=azure-policy --output=name

# Get the logs for the add-on
kubectl logs <Azure Policy Add-on pod name> -n kube-system
```

### <a name="gatekeeper-logs"></a>閘道管理員記錄

閘道管理員 pod （_閘道管理員-控制器-0_）通常是在`gatekeeper-system`或`kube-system`命名空間中，但可以在不同的命名空間中，視部署的方式而定。

若要查看閘道管理員記錄`kubectl`，請使用：

```bash
NAMESPACE=<namespace of gatekeeper>
kubectl logs gatekeeper-controller-manager-0 -n $NAMESPACE
```

如需詳細資訊，請參閱 OPA 檔中的「[偵錯工具閘道管理員](https://github.com/open-policy-agent/gatekeeper#debugging)」。

## <a name="remove-the-add-on"></a>移除附加元件

若要從 AKS Engine 叢集移除 Azure 原則附加元件和閘道管理員，請使用與附加元件安裝方式一致的方法：

- 若已安裝，請在 AKS 引擎的叢集定義中設定**附加元件**屬性：

  將_azure 原則_的**附加元件**屬性變更為 false 之後，將叢集定義重新部署至 AKS 引擎：


  ```json
  "addons": [{
      "name": "azure-policy",
      "enabled": false
  }]
  ```

- 如果與 Helm 圖表一起安裝：

  1. 移除舊的條件約束

     目前，卸載機制只會移除閘道管理員系統，它不會移除使用者所建立的任何_ConstraintTemplate_、_條件約束__或設定_資源，也不會移除其隨附的_CRDs_。

     當閘道管理員正在執行時，可以藉由下列方式移除不必要的條件約束：

     - 刪除條件約束資源的所有實例
     - 刪除_ConstraintTemplate_資源，這應該會自動清除 _.crd_
     - 刪除設定_資源會_移除已同步處理資源上的完成項

  1. 卸載 Azure 原則附加元件
  
     ```bash
     helm del --purge azure-policy-addon
     ```

  1. 卸載閘道管理員
  
     ```bash
     kubectl delete -f https://raw.githubusercontent.com/open-policy-agent/gatekeeper/master/deploy/gatekeeper.yaml
     ```

## <a name="diagnostic-data-collected-by-azure-policy-add-on"></a>Azure 原則附加元件所收集的診斷資料

適用于 Kubernetes 的 Azure 原則附加元件會收集有限的叢集診斷資料。 此診斷資料是與軟體和效能相關的重要技術資料。 資料會用於下列用途：

- 保持 Azure 原則最新的附加元件
- 保持 Azure 原則附加元件安全、可靠、高效能
- 改善 Azure 原則附加元件-透過使用附加元件的匯總分析

附加元件所收集的資訊不是個人資料。 目前會收集下列詳細資料：

- Azure 原則附加元件代理程式版本
- 叢集類型
- 叢集區域
- 叢集資源群組
- 叢集資源識別碼
- 叢集訂用帳戶識別碼
- 叢集作業系統（範例： Linux）
- 叢集城市（範例：西雅圖）
- 叢集州或省（範例：華盛頓州）
- 叢集國家或地區（例如：美國）
- 在原則評估上安裝代理程式期間，Azure 原則附加元件所遇到的例外狀況/錯誤
- Azure 原則附加元件未安裝的閘道管理員原則數目

## <a name="next-steps"></a>後續步驟

- 如[Azure 原則範例](../samples/index.md)，請參閱範例。
- 檢查[原則定義結構](definition-structure.md)。
- 檢閱[了解原則效果](effects.md)。
- 瞭解如何以程式設計[方式建立原則](../how-to/programmatically-create.md)。
- 瞭解如何[取得合規性資料](../how-to/get-compliance-data.md)。
- 瞭解如何[補救不符合規範的資源](../how-to/remediate-resources.md)。
- 請參閱使用[Azure 管理群組來組織資源](../../management-groups/overview.md)的管理群組。