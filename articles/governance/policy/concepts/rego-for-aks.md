---
title: 瞭解 Azure Kubernetes Service 的 Azure 原則
description: 瞭解 Azure 原則如何使用 Rego 和開啟原則代理程式來管理 Azure Kubernetes Service 上的叢集。
ms.date: 03/18/2020
ms.topic: conceptual
ms.openlocfilehash: f6c70d676914cf861ecc378efc4ec23a78879f6e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "82187710"
---
# <a name="understand-azure-policy-for-azure-kubernetes-service"></a>了解 Azure Kubernetes Service 的 Azure 原則

Azure 原則與[Azure Kubernetes Service](../../../aks/intro-kubernetes.md) （AKS）整合，以集中、一致的方式，在您的叢集上套用大規模實行原則和保護措施。
藉由擴充[閘道管理員](https://github.com/open-policy-agent/gatekeeper/tree/master/deprecated)v2 的使用（[開放原則代理程式](https://www.openpolicyagent.org/)（OPA）的_許可控制站 webhook_ ），Azure 原則可讓您從一個位置管理和報告 Azure 資源和 AKS 叢集的合規性狀態。

> [!NOTE]
> 適用于 AKS 的 Azure 原則處於有限預覽狀態，而且只支援內建原則定義。

## <a name="overview"></a>概觀

若要啟用和使用 AKS 搭配 AKS 叢集的 Azure 原則，請採取下列動作：

- [加入宣告預覽功能](#opt-in-for-preview)
- [安裝 Azure 原則附加元件](#installation-steps)
- [指派 AKS 的原則定義](#built-in-policies)
- [等待驗證](#validation-and-reporting-frequency)

## <a name="opt-in-for-preview"></a>加入宣告預覽版

在安裝 Azure 原則附加元件或啟用任何服務功能之前，您的訂用帳戶必須啟用**microsoft.containerservice**資源提供者和**microsoft.policyinsights**資源提供者，然後核准加入預覽版。 若要加入預覽，請遵循 Azure 入口網站或 Azure CLI 中的下列步驟：

- Azure 入口網站：

  1. 註冊**microsoft.containerservice**和**microsoft.policyinsights**資源提供者。 如需相關步驟，請參閱[資源提供者和類型](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-portal)。

  1. 藉由按一下 [所有服務]**** 然後搜尋並選取 [原則]****，在 Azure 入口網站中啟動 Azure 原則服務。

     :::image type="content" source="../media/rego-for-aks/search-policy.png" alt-text="搜尋所有服務中的原則" border="false":::

  1. 選取 [Azure 原則] 頁面左側的 [**聯結預覽**]。

     :::image type="content" source="../media/rego-for-aks/join-aks-preview.png" alt-text="加入 AKS preview 的原則" border="false":::

  1. 選取您想要新增至預覽的訂用帳戶資料列。

  1. 選取訂**用**帳戶清單頂端的 [加入宣告] 按鈕。

- Azure CLI：

  ```azurecli-interactive
  # Log in first with az login if you're not using Cloud Shell

  # Provider register: Register the Azure Kubernetes Services provider
  az provider register --namespace Microsoft.ContainerService

  # Provider register: Register the Azure Policy provider
  az provider register --namespace Microsoft.PolicyInsights

  # Feature register: enables installing the add-on
  az feature register --namespace Microsoft.ContainerService --name AKS-AzurePolicyAutoApprove
  
  # Use the following to confirm the feature has registered
  az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AKS-AzurePolicyAutoApprove')].{Name:name,State:properties.state}"
  
  # Once the above shows 'Registered' run the following to propagate the update
  az provider register -n Microsoft.ContainerService
  
  # Feature register: enables the add-on to call the Azure Policy resource provider
  az feature register --namespace Microsoft.PolicyInsights --name AKS-DataPlaneAutoApprove
  
  # Use the following to confirm the feature has registered
  az feature list -o table --query "[?contains(name, 'Microsoft.PolicyInsights/AKS-DataPlaneAutoApprove')].{Name:name,State:properties.state}"
  
  # Once the above shows 'Registered' run the following to propagate the update
  az provider register -n Microsoft.PolicyInsights
  
  ```

## <a name="azure-policy-add-on"></a>Azure 原則附加元件

Kubernetes 的_Azure 原則附加_元件會將 Azure 原則服務連接到閘道管理員的許可控制站。 安裝在_azure 原則_命名空間中的附加元件會制定下列功能：

- 檢查 AKS 叢集指派的 Azure 原則
- 下載和快取原則詳細資料（包括_rego_原則定義）做為**configmaps**
- 在 AKS 叢集上執行完整掃描合規性檢查
- 將審核和合規性詳細資料包告回到 Azure 原則

### <a name="installing-the-add-on"></a>安裝附加元件

#### <a name="prerequisites"></a>Prerequisites

在您的 AKS 叢集中安裝附加元件之前，必須先安裝預覽延伸模組。 此步驟是使用 Azure CLI 完成：

1. 您需要安裝並設定 Azure CLI 版本2.0.62 或更新版本。 執行 `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI](/cli/azure/install-azure-cli)。

1. AKS 叢集必須是_1.10_或更高版本。 使用下列腳本來驗證您的 AKS 叢集版本：

   ```azurecli-interactive
   # Log in first with az login if you're not using Cloud Shell

   # Look for the value in kubernetesVersion
   az aks list
   ```

1. 安裝適用_0.4.0_于 AKS 的 Azure CLI preview 延伸模組版本 0.4.0 `aks-preview`：

   ```azurecli-interactive
   # Log in first with az login if you're not using Cloud Shell

   # Install/update the preview extension
   az extension add --name aks-preview

   # Validate the version of the preview extension
   az extension show --name aks-preview --query [version]
   ```

   > [!NOTE]
   > 如果您先前已安裝_aks-preview_延伸模組，請使用`az extension update --name aks-preview`命令安裝任何更新。

#### <a name="installation-steps"></a>安裝步驟

完成必要條件之後，請在您想要管理的 AKS 叢集中安裝 Azure 原則附加元件。

- Azure 入口網站

  1. 按一下 [**所有服務**]，然後搜尋並選取 [ **Kubernetes 服務**]，以在 Azure 入口網站中啟動 AKS 服務。

  1. 選取其中一個 AKS 叢集。

  1. 選取 [Kubernetes 服務] 頁面左側的 [**原則（預覽）** ]。

     :::image type="content" source="../media/rego-for-aks/policies-preview-from-aks-cluster.png" alt-text="來自 AKS 叢集的原則" border="false":::

  1. 在主頁面中，選取 [**啟用附加**元件] 按鈕。

     :::image type="content" source="../media/rego-for-aks/enable-policy-add-on.png" alt-text="啟用 AKS 附加元件的 Azure 原則" border="false":::

     > [!NOTE]
     > 如果 [**啟用附加**元件] 按鈕呈現灰色，表示訂用帳戶尚未新增至預覽。 如需必要的步驟，請參閱[加入宣告預覽版](#opt-in-for-preview)。

- Azure CLI

  ```azurecli-interactive
  # Log in first with az login if you're not using Cloud Shell

  az aks enable-addons --addons azure-policy --name MyAKSCluster --resource-group MyResourceGroup
  ```

### <a name="validation-and-reporting-frequency"></a>驗證和報告頻率

此附加元件會每隔5分鐘檢查一次原則指派中的變更 Azure 原則。 在這個重新整理週期期間，附加元件會移除_azure 原則_命名空間中的所有_configmaps_ ，然後重新建立閘道管理員使用的_configmaps_ 。

> [!NOTE]
> 雖然叢集系統_管理員_可能具有_azure 原則_命名空間的許可權，但不建議或不支援對命名空間進行變更。 在重新整理週期期間，所做的任何手動變更都會遺失。

每隔5分鐘，附加元件會呼叫叢集的完整掃描。 在收集完整掃描的詳細資料以及嘗試變更叢集的任何即時評估之後，附加元件會將結果回報給 Azure 原則，以納入任何 Azure 原則指派的[相容性詳細資料](../how-to/get-compliance-data.md)。 在 audit 迴圈期間，只會傳回作用中原則指派的結果。

## <a name="policy-language"></a>原則語言

用於管理 AKS 的 Azure 原則語言結構會遵循現有原則的指示。 效果_EnforceRegoPolicy_是用來管理您的 AKS 叢集，並使用特定的_詳細資料_屬性來使用 OPA 和閘道管理員 v2。 如需詳細資訊和範例，請參閱[EnforceRegoPolicy](effects.md#enforceregopolicy)效果。

Azure 原則將 rego 原則的 URI 傳遞至附加元件，這是原則定義中的_policy 屬性。_ Rego 是 OPA 和閘道管理員支援的語言，可驗證或改變 Kubernetes 叢集的要求。 藉由支援現有的 Kubernetes 管理標準，Azure 原則可讓您重複使用現有的規則，並將其與 Azure 原則結合，以取得統一的雲端合規性報告體驗。 如需詳細資訊，請參閱[什麼是 Rego？](https://www.openpolicyagent.org/docs/latest/policy-language/#what-is-rego)。

## <a name="built-in-policies"></a>內建原則

若要使用 Azure 入口網站來尋找用來管理 AKS 的內建原則，請遵循下列步驟：

1. 啟動 Azure 入口網站中的 Azure 原則服務。 選取左窗格中的 [**所有服務**]，然後搜尋並選取 [**原則**]。

1. 在 [Azure 原則] 頁面的左窗格中，選取 [**定義**]。

1. 從 [類別] 下拉式清單方塊中，使用 [**全選**] 清除篩選準則，然後選取 [ **Kubernetes 服務**]。

1. 選取原則定義，然後選取 [**指派**] 按鈕。

> [!NOTE]
> 指派 AKS 定義的 Azure 原則時，**範圍**必須包含 AKS 叢集資源。

或者，使用 [[指派原則-入口網站](../assign-policy-portal.md)] 快速入門來尋找並指派 AKS 原則。 搜尋 Kubernetes 原則定義，而不是範例「audit vm」。

> [!IMPORTANT]
> 類別**Kubernetes 服務**中的內建原則僅供搭配 AKS 使用。

## <a name="logging"></a>記錄

### <a name="azure-policy-add-on-logs"></a>Azure 原則附加元件記錄檔

Azure 原則附加元件是 Kubernetes 控制器/容器，會將記錄保留在 AKS 叢集中。 記錄會在 AKS 叢集的 [**深入**解析] 頁面中公開。 如需詳細資訊，請參閱[使用適用于容器的 Azure 監視器來瞭解 AKS 叢集效能](../../../azure-monitor/insights/container-insights-analyze.md)。

### <a name="gatekeeper-logs"></a>閘道管理員記錄

若要啟用新資源要求的閘道管理員記錄，請遵循[啟用和查看 AKS 中的 Kubernetes 主要節點記錄](../../../aks/view-master-logs.md)中的步驟。
以下是針對新資源要求查看拒絕事件的範例查詢：

```kusto
| where Category == "kube-audit"
| where log_s contains "admission webhook"
| limit 100
```

若要從閘道管理員容器中查看記錄，請遵循[啟用和查看 AKS 中的 Kubernetes 主要節點記錄](../../../aks/view-master-logs.md)中的步驟，並核取 [**診斷設定**] 窗格中的 [ _kube-kube-apiserver_ ] 選項。

## <a name="remove-the-add-on"></a>移除附加元件

若要從您的 AKS 叢集中移除 Azure 原則附加元件，請使用 Azure 入口網站或 Azure CLI：

- Azure 入口網站

  1. 按一下 [**所有服務**]，然後搜尋並選取 [ **Kubernetes 服務**]，以在 Azure 入口網站中啟動 AKS 服務。

  1. 選取您想要停用 Azure 原則附加元件的 AKS 叢集。

  1. 選取 [Kubernetes 服務] 頁面左側的 [**原則（預覽）** ]。

     :::image type="content" source="../media/rego-for-aks/policies-preview-from-aks-cluster.png" alt-text="來自 AKS 叢集的原則" border="false":::

  1. 在主頁面中，選取 [**停用附加**元件] 按鈕。

     :::image type="content" source="../media/rego-for-aks/disable-policy-add-on.png" alt-text="停用 AKS 附加元件的 Azure 原則" border="false":::

- Azure CLI

  ```azurecli-interactive
  # Log in first with az login if you're not using Cloud Shell

  az aks disable-addons --addons azure-policy --name MyAKSCluster --resource-group MyResourceGroup
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