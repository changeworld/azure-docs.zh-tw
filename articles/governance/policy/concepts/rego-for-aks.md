---
title: 瞭解 Azure 庫伯內斯服務的 Azure 策略
description: 瞭解 Azure 策略如何使用 Rego 和打開策略代理來管理 Azure 庫伯奈斯服務上的群集。
ms.date: 03/27/2020
ms.topic: conceptual
ms.openlocfilehash: d77c5cf94a8239f4617e563961cbe1cc40e48fe0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "80372651"
---
# <a name="understand-azure-policy-for-azure-kubernetes-service"></a>了解 Azure Kubernetes Service 的 Azure 原則

Azure 策略與[Azure Kubernetes 服務](../../../aks/intro-kubernetes.md)（AKS） 集成，以便以集中、一致的方式對群集應用大規模實施和安全措施。
通過擴展使用[Gatekeeper](https://github.com/open-policy-agent/gatekeeper) v3（[開放策略代理](https://www.openpolicyagent.org/)（OPA） 的_准入控制器 Webhook，Azure_策略可以從一個位置管理和報告 Azure 資源和 AKS 群集的合規性狀態。

> [!IMPORTANT]
> AKS 的 Azure 策略處於預覽狀態，僅支援內置策略定義。 內置策略屬於**庫伯奈斯**類別。 **"執行再策略"** 效果和相關**庫伯內斯服務**類別策略正在被_棄用_。 相反，請使用更新的[強制OPA約束](./effects.md#enforceopaconstraint)效果。

> [!WARNING]
> 此功能尚未在所有區域都可用。 有關推出狀態，請參閱[AKS 問題 - 策略載入項的中斷更改](https://github.com/Azure/AKS/issues/1529)。

## <a name="overview"></a>總覽

要啟用 AKS 的 Azure 策略並將其與 AKS 群集配合使用，請執行以下操作：

- [加入宣告預覽功能](#opt-in-for-preview)
- [安裝 Azure 策略載入項](#installation-steps)
- [為 AKS 分配策略定義](#built-in-policies)
- [等待驗證](#validation-and-reporting-frequency)

## <a name="opt-in-for-preview"></a>加入宣告預覽

在安裝 Azure 策略載入項或啟用任何服務功能之前，訂閱必須啟用**Microsoft.ContainerService 資源**提供程式和**Microsoft.PolicyInsights**資來源提供者，然後獲得批准才能加入預覽。 要加入預覽，請按照 Azure 門戶或 Azure CLI 中的以下步驟操作：

- Azure 入口網站：

  1. 註冊**Microsoft.ContainerService 服務和** **Microsoft.政策見解**資源供應商。 有關步驟，請參閱[資來源提供者和類型](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-portal)。

  1. 藉由按一下 [所有服務]**** 然後搜尋並選取 [原則]****，在 Azure 入口網站中啟動 Azure 原則服務。

     ![搜尋所有服務中的原則](../media/rego-for-aks/search-policy.png)

  1. 選擇 Azure 策略頁面左側的 **"加入預覽**"。

     ![加入 AKS 預覽策略](../media/rego-for-aks/join-aks-preview.png)

  1. 選擇要添加到預覽的訂閱行。

  1. 選擇訂閱清單頂部的 **"加入宣告**"按鈕。

- Azure CLI：

  ```azurecli-interactive
  # Log in first with az login if you're not using Cloud Shell

  # Provider register: Register the Azure Kubernetes Service provider
  az provider register --namespace Microsoft.ContainerService

  # Provider register: Register the Azure Policy provider
  az provider register --namespace Microsoft.PolicyInsights

  # Feature register: enables installing the add-on
  az feature register --namespace Microsoft.ContainerService --name AKS-AzurePolicyAutoApprove
  
  # Use the following to confirm the feature has registered
  az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AKS-AzurePolicyAutoApprove')].{Name:name,State:properties.state}"
  
  # Once the above shows 'Registered' run the following to propagate the update
  az provider register -n Microsoft.ContainerService
  
  ```

## <a name="azure-policy-add-on"></a>Azure 策略載入項

庫伯內斯的_Azure 策略載入項_將 Azure 策略服務連接到門衛准入控制器。 載入項安裝在_kube 系統_命名空間中，將實現以下功能：

- 使用 Azure 策略服務檢查群集的分配。
- 將群集中的策略部署為[約束範本](https://github.com/open-policy-agent/gatekeeper#constraint-templates)和[約束](https://github.com/open-policy-agent/gatekeeper#constraints)自訂資源。
- 將審核和合規性詳細資訊報告回 Azure 策略服務。

### <a name="installing-the-add-on"></a>安裝載入項

#### <a name="prerequisites"></a>Prerequisites

在 AKS 群集中安裝載入項之前，必須安裝預覽擴展。 此步驟使用 Azure CLI 完成：

1. 如果安裝了 Gatekeeper v2 策略，請刪除 **"策略（預覽）"** 頁上 AKS 群集上的 **"禁用**"按鈕的載入項。

1. 您需要 Azure CLI 版本 2.0.62 或更高版本安裝和配置。 執行 `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI](/cli/azure/install-azure-cli)。

1. AKS 群集必須是版本_1.14_或更高版本。 使用以下腳本驗證 AKS 群集版本：

   ```azurecli-interactive
   # Log in first with az login if you're not using Cloud Shell

   # Look for the value in kubernetesVersion
   az aks list
   ```

1. 安裝 AKS 的 Azure CLI 預覽擴展版_0.4.0，_ `aks-preview`：

   ```azurecli-interactive
   # Log in first with az login if you're not using Cloud Shell

   # Install/update the preview extension
   az extension add --name aks-preview

   # Validate the version of the preview extension
   az extension show --name aks-preview --query [version]
   ```

   > [!NOTE]
   > 如果您以前安裝了_aks 預覽_擴展，請使用 命令`az extension update --name aks-preview`安裝任何更新。

#### <a name="installation-steps"></a>安裝步驟

完成先決條件後，在要管理的 AKS 群集中安裝 Azure 策略載入項。

- Azure 入口網站

  1. 按一下 **"所有服務**"，然後搜索並選擇**庫伯奈斯服務**，在 Azure 門戶中啟動 AKS 服務。

  1. 選擇其中一個 AKS 群集。

  1. 選擇庫伯內斯服務頁面左側**的策略（預覽）。**

     ![來自 AKS 群集的策略](../media/rego-for-aks/policies-preview-from-aks-cluster.png)

  1. 在主頁中，選擇"**啟用載入項**"按鈕。

     ![啟用 AKS 載入項的 Azure 策略](../media/rego-for-aks/enable-policy-add-on.png)

     > [!NOTE]
     > 如果**啟用載入項**按鈕已灰顯，則訂閱尚未添加到預覽版。 有關所需步驟，請參閱[加入宣告](#opt-in-for-preview)預覽。 如果**禁用**按鈕可用，則仍安裝網守 v2，必須刪除。

- Azure CLI

  ```azurecli-interactive
  # Log in first with az login if you're not using Cloud Shell

  az aks enable-addons --addons azure-policy --name MyAKSCluster --resource-group MyResourceGroup
  ```

### <a name="validation-and-reporting-frequency"></a>驗證和報告頻率

載入項使用 Azure 策略服務簽入每 15 分鐘檢查策略分配中的更改。
在此刷新週期中，載入項將檢查更改。 這些更改觸發器創建、更新或刪除約束範本和約束。

> [!NOTE]
> 雖然群集管理員可能有權創建和更新約束範本和約束資源，但這些方案不受支援，因為手動更新將被覆蓋。

每 15 分鐘，載入項將調用群集的完整掃描。 在收集完全掃描的詳細資訊以及 Gatekeeper 對群集嘗試的更改的任何即時評估後，載入項將結果報告回 Azure 策略服務，以便像任何 Azure 策略分配一樣包含在[合規性詳細資訊](../how-to/get-compliance-data.md#portal)中。 在審核週期中僅返回活動策略分配的結果。 審核結果也可以被視為失敗約束的狀態欄位中列出的[違規行為](https://github.com/open-policy-agent/gatekeeper#audit)。

## <a name="policy-language"></a>政策語言

用於管理庫伯奈斯的 Azure 策略語言結構遵循現有策略。 效果_強制OPA約束_用於管理 Kubernetes 群集，並獲取特定于使用[OPA 約束框架](https://github.com/open-policy-agent/frameworks/tree/master/constraint)和門衛 v3 的詳細資訊屬性。 有關詳細資訊和示例，請參閱[強制OPA約束](./effects.md#enforceopaconstraint)效果。
  
作為策略定義中_的詳細資訊.約束範本_和_詳細資訊.約束_屬性的一部分，Azure 策略將這些[自訂資源定義](https://github.com/open-policy-agent/gatekeeper#constraint-templates)（CRD） 的 URI 傳遞給載入項。 Rego 是 OPA 和門衛支援的語言，用於驗證對 Kubernetes 群集的請求。 通過支援 Kubernetes 管理的現有標準，Azure 策略可以重用現有規則並將其與 Azure 策略配對，從而獲得統一的雲合規性報告體驗。 有關詳細資訊，請參閱什麼是[Rego？。](https://www.openpolicyagent.org/docs/latest/policy-language/#what-is-rego)

## <a name="built-in-policies"></a>內建原則

要查找使用 Azure 門戶管理群集的內置策略，請按照以下步驟操作：

1. 在 Azure 門戶中啟動 Azure 策略服務。 選擇左側窗格中的所有服務，然後搜索並選擇 **"策略**"。

1. 在 Azure 策略頁的左側窗格中，選擇 **"定義**"。

1. 在"類別"下拉式清單方塊中，使用"全部選擇"清除篩選器，然後選擇**Kubernetes**。

1. 選擇策略定義，然後選擇"**分配"** 按鈕。

1. 將 **"範圍**"設置為將應用策略分配的 Kubernetes 群集的管理組、訂閱或資源組。

   > [!NOTE]
   > 為 AKS 定義分配 Azure 策略時，**範圍**必須包括 AKS 群集資源。

1. 為策略分配指定一**個名稱**和說明，您可以使用這些名稱和**說明**輕鬆識別它。

1. 將[策略強制設置為](./assignment-structure.md#enforcement-mode)以下值之一。

   - **已啟用**- 在群集上強制實施策略。 違反的庫伯內斯錄取申請被拒絕。
   
   - **已禁用**- 不要在群集上強制實施策略。 庫伯內斯的入學申請與違反不被拒絕。 合規性評估結果仍然可用。 在將新策略推出到運行群集時，"_已禁用"_ 選項有助於測試策略，因為不會拒絕具有違規的准入請求。

1. 選取 [下一步]****。

1. 設置**參數值**
   
   - 要從策略評估中排除 Kubernetes 命名空間，請在參數**Namespace 排除中**指定命名空間的清單。 建議排除 _：kube 系統_

1. 選擇 **"審閱" = 創建**。

或者，使用[分配策略 - 門戶](../assign-policy-portal.md)快速入門查找和分配 AKS 策略。 搜索 Kubernetes 策略定義，而不是示例"審核 vms"。

> [!IMPORTANT]
> **Kubernetes**類別中的內置策略僅用於 AKS。 有關內置策略的清單，請參閱[庫伯內斯示例](../samples/built-in-policies.md#kubernetes)。

## <a name="logging"></a>記錄

### <a name="azure-policy-add-on-logs"></a>Azure 策略載入項日誌

作為 Kubernetes 控制器/容器，Azure 策略載入項和網守在 AKS 群集中保留日誌。 日誌在 AKS 群集的 **"見解"** 頁中公開。 有關詳細資訊，請參閱[瞭解容器使用 Azure 監視器的 AKS 群集性能](../../../azure-monitor/insights/container-insights-analyze.md)。

## <a name="remove-the-add-on"></a>刪除載入項

要從 AKS 群集中刪除 Azure 策略載入項，請使用 Azure 門戶或 Azure CLI：

- Azure 入口網站

  1. 按一下 **"所有服務**"，然後搜索並選擇**庫伯奈斯服務**，在 Azure 門戶中啟動 AKS 服務。

  1. 選擇要禁用 Azure 策略載入項的 AKS 群集。

  1. 選擇庫伯內斯服務頁面左側**的策略（預覽）。**

     ![來自 AKS 群集的策略](../media/rego-for-aks/policies-preview-from-aks-cluster.png)

  1. 在主頁中，選擇 **"禁用載入項**"按鈕。

     ![禁用 AKS 載入項的 Azure 策略](../media/rego-for-aks/disable-policy-add-on.png)

- Azure CLI

  ```azurecli-interactive
  # Log in first with az login if you're not using Cloud Shell

  az aks disable-addons --addons azure-policy --name MyAKSCluster --resource-group MyResourceGroup
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
