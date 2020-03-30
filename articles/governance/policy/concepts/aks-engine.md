---
title: 瞭解 AKS 引擎的 Azure 策略
description: 瞭解 Azure 策略如何使用網守 v3 中的自訂資源定義和打開策略代理使用 AKS 引擎管理群集。
ms.date: 11/04/2019
ms.topic: conceptual
ms.openlocfilehash: c41a9d84dfe43e356e9a4a17af523a37209c2933
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75436432"
---
# <a name="understand-azure-policy-for-aks-engine"></a>瞭解 AKS 引擎的 Azure 策略

Azure 策略與[AKS 引擎](https://github.com/Azure/aks-engine/blob/master/docs/README.md)集成，該系統提供了方便的工具，可在 Azure 上快速引導自管理的 Kubernetes 群集。 這種集成支援以集中、一致的方式對 AKS 引擎自管理群集進行大規模實施和防護。 通過擴展[使用開放策略代理](https://www.openpolicyagent.org/)（OPA）[門衛](https://github.com/open-policy-agent/gatekeeper)v3（Beta），這是 Kubernetes 的_准入控制器 Webhook，Azure_策略可以管理和報告 Azure 資源和 AKS 引擎自管理群集從一個位置的合規性狀態。

> [!NOTE]
> AKS 引擎的 Azure 策略處於公共預覽狀態，沒有 SLA。 網守 v3 位於 Beta 中，由開源社區提供支援。 該服務僅支援內置策略定義和每個佈建服務主體的資源組的單個 AKS 引擎群集。

> [!IMPORTANT]
> 要獲得對 AKS 引擎、AKS 引擎或閘衛 v3 的 Azure 策略的支援，請在 AKS 引擎 GitHub 存儲庫中創建新[問題](https://github.com/Azure/aks-engine/issues/new/choose)。

## <a name="overview"></a>總覽

要在 Azure 上啟用 AKS 引擎的 Azure 策略並將其與自管理的 Kubernetes 群集一起使用，請執行以下操作：

- [必要條件](#prerequisites)
- [安裝 Azure 策略載入項](#installing-the-add-on)
- [為 AKS 引擎分配策略定義](#built-in-policies)
- [等待驗證](#validation-and-reporting-frequency)

## <a name="prerequisites"></a>Prerequisites

在安裝 Azure 策略載入項或啟用任何服務功能之前，訂閱必須啟用**Microsoft.PolicyInsights**資來源提供者，並為叢集服務主體創建角色指派。 

1. 要啟用資來源提供者，請按照[資來源提供者和類型](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-portal)中的步驟操作或運行 Azure CLI 或 Azure PowerShell 命令：

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

1. 為叢集服務主體創建角色指派

   - 如果您不知道叢集服務主體應用 ID，則使用以下命令查找它。

     ```bash
     # Get the kube-apiserver pod name
     kubectl get pods -n kube-system
   
     # Find the aadClientID value
     kubectl exec <kube-apiserver pod name> -n kube-system cat /etc/kubernetes/azure.json
     ```

   - 使用 Azure CLI 將"策略見解資料寫入器外掛程式（預覽）"角色指派分配給叢集服務主體應用 ID（上一步中的值_aadClientID）。_ 替換為`<subscriptionId>`訂閱 ID，並將`<aks engine cluster resource group>`AKS 引擎自管理的庫伯奈斯群集替換為資源組。

     ```azurecli-interactive
     az role assignment create --assignee <cluster service principal app ID> --scope "/subscriptions/<subscriptionId>/resourceGroups/<aks engine cluster resource group>" --role "Policy Insights Data Writer (Preview)"
     ```

## <a name="azure-policy-add-on"></a>Azure 策略載入項

庫伯內斯的_Azure 策略載入項_將 Azure 策略服務連接到門衛准入控制器。 載入項安裝在_kube 系統_命名空間中，將實現以下功能：

- 使用 Azure 策略檢查分配給 AKS 引擎群集
- 下載並安裝策略詳細資訊、約束範本和約束
- 在 AKS 引擎群集上運行完全掃描合規性檢查
- 將審核和合規性詳細資訊報告回 Azure 策略

### <a name="installing-the-add-on"></a>安裝載入項

完成先決條件後，可以安裝 Azure 策略載入項。 安裝可以是在 AKS 引擎的創建或更新週期中，也可以作為對現有群集的獨立操作。

- 在創建或更新週期期間安裝

  要在創建新的自託管群集期間啟用 Azure 策略載入項或作為對現有群集的更新，請包括 AKS 引擎的**載入**項屬性群集定義。

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

  有關詳細資訊，請參閱外部指南[AKS 引擎群集定義](https://github.com/Azure/aks-engine/blob/master/docs/topics/clusterdefinitions.md)。

- 使用 Helm 圖表在現有群集中安裝

  使用以下步驟準備群集並安裝載入項：

  1. 將網守安裝到_網守系統_命名空間。

     ```bash
     kubectl apply -f https://raw.githubusercontent.com/open-policy-agent/gatekeeper/master/deploy/gatekeeper.yaml
     ```
  1. 將_控制平面_標籤添加到_kube 系統_。 此標籤不包括網守和 Azure 策略載入項對_kube 系統_窗格和服務的審核。

     ```bash
     kubectl label namespaces kube-system control-plane=controller-manager
     ```

  1. 將庫伯內斯資料（命名空間、Pod、入口、服務）與 OPA 同步。

     ```bash
     kubectl apply -f https://raw.githubusercontent.com/Azure/azure-policy/master/built-in-references/Kubernetes/gatekeeper-opa-sync.yaml
     ```

     有關詳細資訊，請參閱[OPA - 複製資料](https://github.com/open-policy-agent/gatekeeper#replicating-data)。

  1. 將 Azure 策略回購添加到 Helm。

     ```bash
     helm repo add azure-policy https://raw.githubusercontent.com/Azure/azure-policy/master/extensions/policy-addon-kubernetes/helm-charts
     ```

     有關詳細資訊，請參閱[頭盔圖 - 快速入門手冊](https://helm.sh/docs/using_helm/#quickstart-guide)。

  1. 使用 Helm 圖表安裝載入項。 替換為`<subscriptionId>`訂閱 ID，並將`<aks engine cluster resource group>`AKS 引擎自管理的庫伯奈斯群集替換為資源組。

     ```bash
     helm install azure-policy/azure-policy-addon-aks-engine --name azure-policy-addon --set azurepolicy.env.resourceid="/subscriptions/<subscriptionId>/resourceGroups/<aks engine cluster resource group>"
     ```

     有關附加的 Helm 圖表安裝內容的詳細資訊，請參閱 GitHub 上的[Azure 策略附加顯示頭盔圖定義](https://github.com/Azure/azure-policy/tree/master/extensions/policy-addon-kubernetes/helm-charts)。

     > [!NOTE]
     > 由於 Azure 策略載入項和資源組 ID 之間的關係，Azure 策略僅支援每個資源組的一個 AKS 引擎群集。

要驗證載入項安裝是否成功以及_azure 策略_窗格是否正在運行，請運行以下命令：

```bash
kubectl get pods -n kube-system
```

### <a name="validation-and-reporting-frequency"></a>驗證和報告頻率

載入項使用 Azure 策略簽入每 5 分鐘檢查策略分配的更改。 在此刷新週期中，載入項將檢查更改。 這些更改觸發器創建、更新或刪除約束範本和約束。

> [!NOTE]
> 雖然_群集管理員_可能有權對約束範本和約束進行更改，但不建議或支援更改 Azure 策略創建的約束範本或約束。 在刷新週期中，所做的任何手動更改都丟失。

每 5 分鐘，載入項將調用群集的完整掃描。 在收集完全掃描的詳細資訊以及 Gatekeeper 對群集嘗試的更改的任何即時評估後，載入項將結果報告回 Azure 策略，以便像任何 Azure 策略分配一樣包含在[合規性詳細資訊](../how-to/get-compliance-data.md)中。 在審核週期中僅返回活動策略分配的結果。 審核結果也可以被視為失敗約束的狀態欄位中列出的違規行為。

## <a name="policy-language"></a>政策語言

用於管理 AKS 引擎的 Azure 策略語言結構遵循現有策略。 效果_強制OPA約束_用於管理 AKS 引擎群集，並獲取特定于使用[OPA 約束框架](https://github.com/open-policy-agent/frameworks/tree/master/constraint)和門衛 v3_的詳細資訊_屬性。 有關詳細資訊和示例，請參閱[強制OPA約束](effects.md#enforceopaconstraint)效果。

作為策略定義中_的詳細資訊.約束範本_和_詳細資訊.約束_屬性的一部分，Azure 策略將這些[自訂資源定義](https://github.com/open-policy-agent/gatekeeper#constraint-templates)（CRD） 的 URI 傳遞給載入項。 Rego 是 OPA 和門衛支援的語言，用於驗證對 Kubernetes 群集的請求。 通過支援 Kubernetes 管理的現有標準，Azure 策略可以重用現有規則並將其與 Azure 策略配對，從而獲得統一的雲合規性報告體驗。 有關詳細資訊，請參閱什麼是[Rego？。](https://www.openpolicyagent.org/docs/how-do-i-write-policies.html#what-is-rego)

## <a name="built-in-policies"></a>內建原則

要查找使用 Azure 門戶管理 AKS 引擎群集的內置策略，請按照以下步驟操作：

1. 在 Azure 門戶中啟動 Azure 策略服務。 選擇左側窗格中的所有**服務**，然後搜索並選擇 **"策略**"。

1. 在 Azure 策略頁的左側窗格中，選擇 **"定義**"。

1. 在"類別"下拉式清單方塊中，使用 **"全部選擇"** 清除篩選器，然後選擇**Kubernetes**。

1. 選擇策略定義，然後選擇"**分配"** 按鈕。

> [!NOTE]
> 為 AKS 引擎定義分配 Azure 策略時，**範圍**必須是 AKS 引擎群集的資源組。

或者，使用[分配策略 - 門戶](../assign-policy-portal.md)快速啟動來查找和分配 AKS 引擎策略。 搜索 AKS 引擎策略定義，而不是示例"審核 vms"。

> [!IMPORTANT]
> **Kubernetes**類別中的內置策略僅適用于 AKS 引擎。

## <a name="logging"></a>記錄

### <a name="azure-policy-add-on-logs"></a>Azure 策略載入項日誌

作為 Kubernetes 控制器/容器，Azure 策略載入項將日誌保留在 AKS 引擎群集中。

要查看 Azure 策略載入項日誌，請使用`kubectl`：

```bash
# Get the Azure Policy Add-on pod name
kubectl -n kube-system get pods -l app=azure-policy --output=name

# Get the logs for the add-on
kubectl logs <Azure Policy Add-on pod name> -n kube-system
```

### <a name="gatekeeper-logs"></a>門衛日誌

網守窗格，_網守-控制器-管理器-0，_ 通常位於`gatekeeper-system`或`kube-system`命名空間中，但可以位於不同的命名空間中，具體取決於其部署方式。

要查看網守日誌，請使用 ： `kubectl`

```bash
NAMESPACE=<namespace of gatekeeper>
kubectl logs gatekeeper-controller-manager-0 -n $NAMESPACE
```

有關詳細資訊，請參閱 OPA 文檔中的[調試網守](https://github.com/open-policy-agent/gatekeeper#debugging)。

## <a name="remove-the-add-on"></a>刪除載入項

要從 AKS 引擎群集中刪除 Azure 策略載入項和網守，請使用與載入項安裝方式一致的方法：

- 如果通過在 AKS 引擎的群集定義中設置**載入項**屬性進行安裝：

  將_azure 策略_**的載入項**屬性更改為 false 後，將群集定義重新部署到 AKS 引擎：


  ```json
  "addons": [{
      "name": "azure-policy",
      "enabled": false
  }]
  ```

- 如果隨頭盔圖一起安裝：

  1. 刪除舊約束

     目前卸載機制僅刪除網守系統，它不刪除使用者創建的任何_約束範本_、_約束_或_配置_資源，也不會刪除其附帶_的 CRD。_

     當網守運行時，可以通過：

     - 刪除約束資源的所有實例
     - 刪除_約束範本_資源，該資源應自動清理_CRD_
     - 刪除_Config_資源將刪除同步資源上的終端子

  1. 卸載 Azure 策略載入項
  
     ```bash
     helm del --purge azure-policy-addon
     ```

  1. 卸載門衛
  
     ```bash
     kubectl delete -f https://raw.githubusercontent.com/open-policy-agent/gatekeeper/master/deploy/gatekeeper.yaml
     ```

## <a name="diagnostic-data-collected-by-azure-policy-add-on"></a>Azure 策略載入項收集的診斷資料

庫伯內斯的 Azure 策略載入項收集有限的群集診斷資料。 此診斷資料是與軟體和性能相關的重要技術資料。 資料會用於下列用途：

- 使 Azure 策略載入項保持最新
- 確保 Azure 策略載入項的安全、可靠、性能
- 改進 Azure 策略載入項 - 通過對載入項的使用進行聚合分析

載入項收集的資訊不是個人資料。 當前收集以下詳細資訊：

- Azure 策略載入項代理版本
- 叢集類型
- 群集區域
- 群集資源組
- 群集資源識別碼
- 群集訂閱 ID
- 群集作業系統（示例：Linux）
- 集群城市（示例：西雅圖）
- 群集州或省（示例：華盛頓）
- 群集國家或地區（示例：美國）
- Azure 策略載入項在策略評估上的代理安裝期間遇到的異常/錯誤
- Azure 策略載入項未安裝的網守策略數

## <a name="next-steps"></a>後續步驟

- 查看[Azure 策略示例](../samples/index.md)中的示例。
- 查看[策略定義結構](definition-structure.md)。
- 回顧[瞭解政策效果](effects.md)。
- 瞭解如何[以程式設計方式創建策略](../how-to/programmatically-create.md)。
- 瞭解如何[獲取合規性資料](../how-to/get-compliance-data.md)。
- 瞭解如何[修復不合規資源](../how-to/remediate-resources.md)。
- 使用[Azure 管理組查看](../../management-groups/overview.md)管理組的內容。請組織資源。