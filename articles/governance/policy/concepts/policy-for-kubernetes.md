---
title: 預覽 - 了解適用於 Kubernetes 的 Azure 原則
description: 了解 Azure 原則如何使用 Rego 和 Open Policy Agent 來管理在 Azure 或內部部署中執行 Kubernetes 的叢集。 這是預覽功能。
ms.date: 06/12/2020
ms.topic: conceptual
ms.openlocfilehash: a044ea33f1a7710c4bb97d30cf8f11d4de2838b1
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85373619"
---
# <a name="understand-azure-policy-for-kubernetes-clusters-preview"></a>了解適用於 Kubernetes 叢集的 Azure 原則 (預覽)

Azure 原則會延伸 [Gatekeeper](https://github.com/open-policy-agent/gatekeeper) v3 ([Open Policy Agent](https://www.openpolicyagent.org/) (OPA) 的_許可控制站 Webhook_)，以集中且一致的方式，對叢集進行大規模的政策實施和保護。 Azure 原則可以從單一位置管理和報告 Kubernetes 叢集的合規性狀態。 附加元件會制定下列功能：

- 向 Azure 原則服務確認以了解叢集的原則指派。
- 將原則定義部署至叢集中，作為[條件約束範本](https://github.com/open-policy-agent/gatekeeper#constraint-templates)和[條件約束](https://github.com/open-policy-agent/gatekeeper#constraints)自訂資源。
- 將稽核和合規性詳細資料回報給 Azure 原則服務。

適用於 Kubernetes 的 Azure 原則支援下列叢集環境：

- [Azure Kubernetes Service (AKS)](../../../aks/intro-kubernetes.md)
- [已啟用 Azure Arc 的 Kubernetes](../../../azure-arc/kubernetes/overview.md)
- [AKS 引擎](https://github.com/Azure/aks-engine/blob/master/docs/README.md)

> [!IMPORTANT]
> 適用於 Kubernetes 的 Azure 原則處於 [預覽] 狀態，且僅支援 Linux 節點集區與內建的原則定義。 內建的原則定義位在 **Kubernetes** 類別中。 具有**EnforceOPAConstraint**和**EnforceRegoPolicy**效果的有限預覽原則定義，以及相關的**Kubernetes 服務**類別已被_取代_。 相反地，請使用_audit_和_Deny_與資源提供者模式的效果 `Microsoft.Kubernetes.Data` 。

## <a name="overview"></a>概觀

若要啟用和使用 Azure 原則搭配 Kubernetes 叢集，請採取下列動作：

1. 設定 Kubernetes 叢集並安裝附加元件：
   - [Azure Kubernetes Service (AKS)](#install-azure-policy-add-on-for-aks)
   - [已啟用 Azure Arc 的 Kubernetes](#install-azure-policy-add-on-for-azure-arc-enabled-kubernetes)
   - [AKS 引擎](#install-azure-policy-add-on-for-aks-engine)

   > [!NOTE]
   > 如需安裝的常見問題，請參閱[疑難排解-Azure 原則附加](../troubleshoot/general.md#add-on-installation-errors)元件。

1. [了解適用於 Kubernetes 的 Azure 原則語言](#policy-language)

1. [將內建定義指派給您的 Kubernetes 叢集](#assign-a-built-in-policy-definition)

1. [等待驗證](#policy-evaluation)

## <a name="install-azure-policy-add-on-for-aks"></a>安裝 AKS 的 Azure 原則附加元件

在安裝 Azure 原則附加元件或啟用任何服務功能之前，您的訂用帳戶必須啟用 **Microsoft.ContainerService** 和 **Microsoft.PolicyInsights** 資源提供者。

1. 您必須安裝並設定 Azure CLI 2.0.62 版或更新版本。 執行 `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI](/cli/azure/install-azure-cli)。

1. 註冊資源提供者和預覽功能。

   - Azure 入口網站：

     1. 註冊 **Microsoft.ContainerService** 和 **Microsoft.PolicyInsights** 資源提供者。 如需相關步驟，請參閱[資源提供者和類型](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-portal)。

     1. 藉由按一下 [所有服務] 然後搜尋並選取 [原則]，在 Azure 入口網站中啟動 Azure 原則服務。

        :::image type="content" source="../media/policy-for-kubernetes/search-policy.png" alt-text="搜尋所有服務中的原則" border="false":::

     1. 選取「Azure 原則」頁面左側的 [加入預覽]。

        :::image type="content" source="../media/policy-for-kubernetes/join-aks-preview.png" alt-text="加入 AKS 預覽的原則" border="false":::

     1. 選取您要新增至預覽的訂用帳戶資料列。

     1. 選取訂用帳戶清單頂端的 [加入] 按鈕。

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
     az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AKS-AzurePolicyAutoApprove')].   {Name:name,State:properties.state}"
     
     # Once the above shows 'Registered' run the following to propagate the update
     az provider register -n Microsoft.ContainerService
     ```

1. 如果已安裝有限的預覽原則定義，請在 [原則 (預覽)] 頁面下，使用 AKS 叢集上的 [停用] 按鈕移除附加元件。

1. AKS 叢集必須是 _1.14_ 版或更新版本。 使用下列指令碼來驗證您的 AKS 叢集版本：

   ```azurecli-interactive
   # Log in first with az login if you're not using Cloud Shell

   # Look for the value in kubernetesVersion
   az aks list
   ```

1. 安裝 AKS 適用的 Azure CLI 預覽延伸模組 0.4.0 版 (`aks-preview`)：

   ```azurecli-interactive
   # Log in first with az login if you're not using Cloud Shell

   # Install/update the preview extension
   az extension add --name aks-preview

   # Validate the version of the preview extension
   az extension show --name aks-preview --query [version]
   ```

   > [!NOTE]
   > 如果您先前已安裝 _aks-preview_ 延伸模組，請使用 `az extension update --name aks-preview` 命令安裝任何更新。

完成上述先決條件步驟後，請在您想要管理的 AKS 叢集中安裝 Azure 原則附加元件。

- Azure 入口網站

  1. 按一下 [所有服務]，然後搜尋並選取 [Kubernetes 服務]，以在 Azure 入口網站中啟動 AKS 服務。

  1. 選取您的其中一個 AKS 叢集。

  1. 選取 [Kubernetes 服務] 頁面左側的 [原則 (預覽)]。

     :::image type="content" source="../media/policy-for-kubernetes/policies-preview-from-aks-cluster.png" alt-text="來自 AKS 叢集的原則定義" border="false":::

  1. 在主頁面中，選取 [啟用附加元件] 按鈕。

     :::image type="content" source="../media/policy-for-kubernetes/enable-policy-add-on.png" alt-text="啟用 AKS 適用的 Azure 原則附加元件" border="false":::

     > [!NOTE]
     > 如果 [啟用附加元件] 按鈕呈現灰色，則表示訂用帳戶尚未新增至預覽。 如果已啟用 [停用附加元件] 按鈕，並顯示 v2 訊息的移轉警告，則仍然會安裝 Gatekeepver v2，因此必須移除。

- Azure CLI

  ```azurecli-interactive
  # Log in first with az login if you're not using Cloud Shell

  az aks enable-addons --addons azure-policy --name MyAKSCluster --resource-group MyResourceGroup
  ```

若要驗證附加元件安裝是否成功，以及 _azure-policy_ 和 _gatekeeper_ Pod 是否正在執行，請執行下列命令：

```bash
# azure-policy pod is installed in kube-system namespace
kubectl get pods -n kube-system

# gatekeeper pod is installed in gatekeeper-system namespace
kubectl get pods -n gatekeeper-system
```

最後，請執行此 Azure CLI 命令，並將 `<rg>` 取代為您的資源群組名稱，然後將 `<cluster-name>` 取代為您的 AKS 叢集名稱：`az aks show -g <rg> -n <cluster-name>`，以確認是否已安裝最新的附加元件。 結果看起來應該會類似下列輸出，且 **config.version** 應為 `v2`：

```output
"addonProfiles": {
    "azurepolicy": {
        "config": {
            "version": "v2"
        },
        "enabled": true,
        "identity": null
    },
}
```

## <a name="install-azure-policy-add-on-for-azure-arc-enabled-kubernetes"></a>為已啟用 Azure Arc 的 Kubernetes 安裝 Azure 原則附加元件

在安裝 Azure 原則附加元件或啟用任何服務功能之前，您的訂用帳戶必須啟用 **Microsoft.PolicyInsights** 資源提供者，並建立叢集服務主體的角色指派。

1. 您必須安裝並設定 Azure CLI 2.0.62 版或更新版本。 執行 `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI](/cli/azure/install-azure-cli)。

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

1. Kubernetes 叢集必須是 _1.14_ 版或更新版本。

1. 安裝 [Helm 3](https://v3.helm.sh/docs/intro/install/)。

1. 您的 Kubernetes 叢集已針對 Azure Arc 啟用。如需詳細資訊，請參閱[將 Kubernetes 叢集上架至 Azure Arc](../../../azure-arc/kubernetes/connect-cluster.md)。

1. 具備已啟用 Azure Arc 的 Kubernetes 叢集之完整 Azure 資源識別碼。 

1. 開啟附加元件的連接埠。 Azure 原則附加元件會使用這些網域和連接埠擷取原則定義和指派，並將叢集的合規性回報給 Azure 原則。

   |網域 |連接埠 |
   |---|---|
   |`gov-prod-policy-data.trafficmanager.net` |`443` |
   |`raw.githubusercontent.com` |`443` |
   |`login.windows.net` |`443` |
   |`dc.services.visualstudio.com` |`443` |

1. 將「原則見解資料寫入者 (預覽)」角色指派指派給已啟用 Azure Arc 的 Kubernetes 叢集。 將 `<subscriptionId>` 取代為您的訂用帳戶識別碼、`<rg>` 取代為已啟用 Azure Arc 的 Kubernetes 叢集的資源群組，並將 `<clusterName>` 取代為啟用 Azure Arc 的 Kubernetes 叢集名稱。 針對安裝步驟，追蹤 _appId_、_password_ 和 _tenant_ 的傳回值。

   - Azure CLI

     ```azurecli-interactive
     az ad sp create-for-rbac --role "Policy Insights Data Writer (Preview)" --scopes "/subscriptions/<subscriptionId>/resourceGroups/<rg>/providers/Microsoft.Kubernetes/connectedClusters/<clusterName>"
     ```

   - Azure PowerShell

     ```azure powershell-interactive
     $sp = New-AzADServicePrincipal -Role "Policy Insights Data Writer (Preview)" -Scope "/subscriptions/<subscriptionId>/resourceGroups/<rg>/providers/Microsoft.Kubernetes/connectedClusters/<clusterName>"

     @{ appId=$sp.ApplicationId;password=[System.Runtime.InteropServices.Marshal]::PtrToStringAuto([System.Runtime.InteropServices.Marshal]::SecureStringToBSTR($sp.Secret));tenant=(Get-AzContext).Tenant.Id } | ConvertTo-Json
     ```

   上述命令的範例輸出：

   ```json
   {
       "appId": "aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa",
       "password": "bbbbbbbb-bbbb-bbbb-bbbb-bbbbbbbbbbbb",
       "tenant": "cccccccc-cccc-cccc-cccc-cccccccccccc"
   }
   ```

完成上述先決條件步驟後，請在已啟用 Azure Arc 的 Kubernetes 叢集中安裝 Azure 原則附加元件：

1. 將 Azure 原則附加元件存放庫新增至 Helm：

   ```bash
   helm repo add azure-policy https://raw.githubusercontent.com/Azure/azure-policy/master/extensions/policy-addon-kubernetes/helm-charts
   ```

1. 使用 Helm Chart 安裝 Azure 原則附加元件：

   ```bash
   # In below command, replace the following values with those gathered above.
   #    <AzureArcClusterResourceId> with your Azure Arc enabled Kubernetes cluster resource Id. For example: /subscriptions/<subscriptionId>/resourceGroups/<rg>/providers/Microsoft.Kubernetes/connectedClusters/<clusterName>
   #    <ServicePrincipalAppId> with app Id of the service principal created during prerequisites.
   #    <ServicePrincipalPassword> with password of the service principal created during prerequisites.
   #    <ServicePrincipalTenantId> with tenant of the service principal created during prerequisites.
   helm install azure-policy-addon azure-policy/azure-policy-addon-arc-clusters \
       --set azurepolicy.env.resourceid=<AzureArcClusterResourceId> \
       --set azurepolicy.env.clientid=<ServicePrincipalAppId> \
       --set azurepolicy.env.clientsecret=<ServicePrincipalPassword> \
       --set azurepolicy.env.tenantid=<ServicePrincipalTenantId>
   ```

   如需有關 Helm Chart 會安裝哪些附加元件的詳細資訊，請參閱 GitHub 上的 [Azure 原則附加元件 Helm Chart 定義](https://github.com/Azure/azure-policy/tree/master/extensions/policy-addon-kubernetes/helm-charts/azure-policy-addon-arc-clusters)。

若要驗證附加元件安裝是否成功，以及 _azure-policy_ 和 _gatekeeper_ Pod 是否正在執行，請執行下列命令：

```bash
# azure-policy pod is installed in kube-system namespace
kubectl get pods -n kube-system

# gatekeeper pod is installed in gatekeeper-system namespace
kubectl get pods -n gatekeeper-system
```

## <a name="install-azure-policy-add-on-for-aks-engine"></a>安裝 AKS 引擎的 Azure 原則附加元件

在安裝 Azure 原則附加元件或啟用任何服務功能之前，您的訂用帳戶必須啟用 **Microsoft.PolicyInsights** 資源提供者，並建立叢集服務主體的角色指派。

1. 您必須安裝並設定 Azure CLI 2.0.62 版或更新版本。 執行 `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI](/cli/azure/install-azure-cli)。

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

1. 建立叢集服務主體的角色指派。

   - 如果您不知道叢集服務主體的應用程式識別碼，請使用下列命令來查閱。

     ```bash
     # Get the kube-apiserver pod name
     kubectl get pods -n kube-system
   
     # Find the aadClientID value
     kubectl exec <kube-apiserver pod name> -n kube-system cat /etc/kubernetes/azure.json
     ```

   - 使用 Azure CLI，對叢集服務主體的應用程式識別碼 (上一個步驟中的 aadClientID) 指派「原則見解資料寫入者 (預覽)」角色指派。 將 `<subscriptionId>` 取代為您的訂用帳戶識別碼，並將 `<aks engine cluster resource group>` 取代為 AKS 引擎自我管理 Kubernetes 叢集所在的資源群組。

     ```azurecli-interactive
     az role assignment create --assignee <cluster service principal app ID> --scope "/subscriptions/<subscriptionId>/resourceGroups/<aks engine cluster resource group>" --role "Policy Insights Data Writer (Preview)"
     ```

完成上述先決條件步驟後，安裝 Azure 原則附加元件。 安裝可以在 AKS 引擎建立或更新週期期間進行，或是在現有叢集上作為獨立動作來進行。

- 在建立或更新循環期間安裝

  若要在建立新的自我管理叢集或更新現有叢集時啟用 Azure 原則附加元件，請包含 AKS 引擎的[附加元件](https://github.com/Azure/aks-engine/tree/master/examples/addons/azure-policy)屬性叢集定義。

  ```json
  "addons": [{
      "name": "azure-policy",
      "enabled": true
  }]
  ```

  如需相關詳細資訊，請參閱 [AKS 引擎叢集定義](https://github.com/Azure/aks-engine/blob/master/docs/topics/clusterdefinitions.md)的外部指南。

- 使用 Helm Chart 在現有叢集中進行安裝

  使用下列步驟來準備叢集並安裝附加元件：

  1. 安裝 [Helm 3](https://v3.helm.sh/docs/intro/install/)。

  1. 將 Azure 原則存放庫新增至 Helm。

     ```bash
     helm repo add azure-policy https://raw.githubusercontent.com/Azure/azure-policy/master/extensions/policy-addon-kubernetes/helm-charts
     ```

     如需詳細資訊，請參閱 [Helm Chart - 快速入門指南](https://helm.sh/docs/using_helm/#quickstart-guide)。

  1. 以 Helm Chart 安裝附加元件。 將 `<subscriptionId>` 取代為您的訂用帳戶識別碼，並將 `<aks engine cluster resource group>` 取代為 AKS 引擎自我管理 Kubernetes 叢集所在的資源群組。

     ```bash
     helm install azure-policy-addon azure-policy/azure-policy-addon-aks-engine --set azurepolicy.env.resourceid="/subscriptions/<subscriptionId>/resourceGroups/<aks engine cluster resource group>"
     ```

     如需有關 Helm Chart 會安裝哪些項目的詳細資訊，請參閱 GitHub 上的 [Azure 原則附加元件 Helm Chart 定義](https://github.com/Azure/azure-policy/tree/master/extensions/policy-addon-kubernetes/helm-charts/azure-policy-addon-aks-engine)。

     > [!NOTE]
     > 由於 Azure 原則附加元件與資源群組識別碼之間的關聯性，Azure 原則僅針對每個資源群組支援一個 AKS 引擎叢集。

若要驗證附加元件安裝是否成功，以及 _azure-policy_ 和 _gatekeeper_ Pod 是否正在執行，請執行下列命令：

```bash
# azure-policy pod is installed in kube-system namespace
kubectl get pods -n kube-system

# gatekeeper pod is installed in gatekeeper-system namespace
kubectl get pods -n gatekeeper-system
```

## <a name="policy-language"></a>原則語言

用於管理 Kubernetes 的 Azure 原則語言結構會遵循現有原則定義的結構。 使用的[資源提供者模式](./definition-structure.md#resource-provider-modes)時 `Microsoft.Kubernetes.Data` ，會使用[audit](./effects.md#audit)和[deny](./effects.md#deny)的效果來管理 Kubernetes 叢集。 _Audit_和_deny_必須提供特定的**詳細資料**屬性，以使用[OPA 條件約束架構](https://github.com/open-policy-agent/frameworks/tree/master/constraint)和閘道管理員 v3。

在原則定義中的 _details.constraintTemplate_ 和 _details.constraint_ 屬性中，Azure 原則會將這些 [CustomResourceDefinitions](https://github.com/open-policy-agent/gatekeeper#constraint-templates) (CRD) 的 URI 傳遞至附加元件。 Rego 是 OPA 和 Gatekeeper 向 Kubernetes 叢集驗證要求時的支援語言。 藉由支援現有的 Kubernetes 管理標準，Azure 原則可讓您重複使用現有的規則，並可將其與 Azure 原則結合，以取得統一的雲端合規性報告體驗。 如需詳細資訊，請參閱[什麼是 Rego？](https://www.openpolicyagent.org/docs/latest/policy-language/#what-is-rego)。

## <a name="assign-a-built-in-policy-definition"></a>指派內建原則定義

若要將原則定義指派給您的 Kubernetes 叢集，必須獲指派適當的角色型存取控制 (RBAC) 原則指派作業。 內建的 RBAC 角色 [資源原則參與者] 和 [擁有者] 具有這些作業。 若要深入了解，請參閱 [Azure 原則中的 RBAC 權限](../overview.md#rbac-permissions-in-azure-policy)。

使用 Azure 入口網站搭配下列步驟，尋找用於管理叢集的內建原則定義：

1. 在 Azure 入口網站中啟動 Azure 原則服務。 在左側窗格中選取 [所有服務]，然後搜尋並選取 [原則]。

1. 在 [Azure 原則] 頁面的左窗格中，選取 [定義]。

1. 從 [類別] 下拉式清單方塊中，使用 [全選] 來清除篩選，然後選取 [Kubernetes]。

1. 選取原則定義，然後選取 [指派] 按鈕。

1. 將 [範圍] 設定為將套用原則指派所在 Kubernetes 叢集的管理群組、訂用帳戶或資源群組。

   > [!NOTE]    
   > 指派 Kubernetes 適用的 Azure 原則定義時，**範圍**必須包含叢集資源。 若為 AKS 引擎叢集，**範圍**必須是叢集的資源群組。

1. 為原則指派賦予**名稱**和**描述**，讓您能夠輕鬆識別該原則。    

1. 將[原則強制執行](./assignment-structure.md#enforcement-mode)設定為其中一個值    
   。   

   - **已啟用** - 在叢集上強制執行原則。 具有違規的 Kubernetes 許可要求會遭到拒絕。    

   - **已停用** - 在叢集上不強制執行原則。 具有違規的 Kubernetes 許可要求不會遭到拒絕。 合規性評估結果仍然可供使用。 將新的原則定義推出到執行中的叢集時，[已停用] 選項有助於測試原則定義，因為具有違規的許可要求不會遭到拒絕。

1. 選取 [下一步] 。 

1. 設定**參數值** 

   - 若要從原則評估中排除 Kubernetes 命名空間，請在 [命名空間排除] 參數中指定命名空間的清單。 建議您排除：_kube-system_、_gatekeeper-system_ 和 _azure-arc_。

1. 選取 [檢閱 + 建立]。

或者，使用[指派原則 - 入口網站](../assign-policy-portal.md)快速入門來尋找並指派 Kubernetes 原則。 搜尋 Kubernetes 原則定義，而不是 'audit vms' 範例。

> [!IMPORTANT]
> 內建原則定義適用於 **Kubernetes** 類別中的 Kubernetes 叢集。 如需內建原則定義的清單，請參閱 [Kubernetes 範例](../samples/built-in-policies.md#kubernetes)。

## <a name="policy-evaluation"></a>原則評估

此附加元件會每隔 15 分鐘向 Azure 原則服務檢查原則指派中的變更。
在此重新整理週期期間，附加元件會檢查是否有變更。 這些變更會觸發條件約束範本和條件約束的建立、更新或刪除。

在 Kubernetes 叢集中，如果命名空間有下列其中一個標籤，則不會拒絕具有違規的許可要求。 合規性評估結果仍然可供使用。

- `control-plane`
- `admission.policy.azure.com/ignore`

> [!NOTE]
> 雖然叢集管理員可能具備建立和更新 Azure 原則附加元件所安裝之條件約束範本和條件約束資源的權限，但這些不是支援的案例，因為手動更新會遭到覆寫。 Gatekeeper 會繼續評估安裝附加元件並指派 Azure 原則的原則定義之前已存在的原則。

每隔 15 分鐘，附加元件就會呼叫叢集的完整掃描。 在收集完整掃描的詳細資料以及嘗試變更叢集的任何即時評估 (由 Gatekeeper 執行) 之後，此附加元件會將結果回報給 Azure 原則以納入[合規性詳細資料](../how-to/get-compliance-data.md)，例如任何 Azure 原則指派。 在稽核週期期間，只會傳回作用中原則指派的結果。 稽核結果也可以視為失敗條件約束 [狀態] 欄位中所列的[違規](https://github.com/open-policy-agent/gatekeeper#audit)。

> [!NOTE]
> 在 Kubernetes 叢集 Azure 原則中，每個合規性報告都包含過去 45 分鐘內的所有違規。 時間戳記會指出違規發生的時間。

## <a name="logging"></a>記錄

作為 Kubernetes 控制器/容器，_azure-policy_ 和 _gatekeeper_ Pod 都會將記錄保留在 Kubernetes 叢集中。 記錄可以在 Kubernetes 叢集的 [見解] 頁面中公開。
如需詳細資訊，請參閱[使用適用於容器的 Azure 監視器來監視您的 Kubernetes 叢集效能](../../../azure-monitor/insights/container-insights-analyze.md)。

若要檢視附加元件記錄，請使用 `kubectl`：

```bash
# Get the azure-policy pod name installed in kube-system namespace
kubectl logs <azure-policy pod name> -n kube-system

# Get the gatekeeper pod name installed in gatekeeper-system namespace
kubectl logs <gatekeeper pod name> -n gatekeeper-system
```

如需詳細資訊，請參閱 Gatekeeper 文件中的 [Gatekeeper 偵錯](https://github.com/open-policy-agent/gatekeeper#debugging)。

## <a name="remove-the-add-on"></a>移除附加元件

### <a name="remove-the-add-on-from-aks"></a>從 AKS 移除附加元件

若要從 AKS 叢集中移除 Azure 原則附加元件，請使用 Azure 入口網站或 Azure CLI：

- Azure 入口網站

  1. 按一下 [所有服務]，然後搜尋並選取 [Kubernetes 服務]，以在 Azure 入口網站中啟動 AKS 服務。

  1. 選取您想要停用 Azure 原則附加元件的 AKS 叢集。

  1. 選取 [Kubernetes 服務] 頁面左側的 [原則 (預覽)]。

     :::image type="content" source="../media/policy-for-kubernetes/policies-preview-from-aks-cluster.png" alt-text="來自 AKS 叢集的原則定義" border="false":::

  1. 在主頁面中，選取 [停用附加元件] 按鈕。

     :::image type="content" source="../media/policy-for-kubernetes/disable-policy-add-on.png" alt-text="停用 AKS 適用的 Azure 原則附加元件" border="false":::

- Azure CLI

  ```azurecli-interactive
  # Log in first with az login if you're not using Cloud Shell

  az aks disable-addons --addons azure-policy --name MyAKSCluster --resource-group MyResourceGroup
  ```

### <a name="remove-the-add-on-from-azure-arc-enabled-kubernetes"></a>從已啟用 Azure Arc 的 Kubernetes 中移除附加元件

若要從已啟用 Azure Arc 的 Kubernetes 叢集中移除 Azure 原則附加元件和 Gatekeeper，請執行下列 Helm 命令：

```bash
helm uninstall azure-policy-addon
```

### <a name="remove-the-add-on-from-aks-engine"></a>從 AKS 引擎移除附加元件

若要從 AKS 引擎叢集中移除 Azure 原則附加元件和 Gatekeeper，請使用與安裝附加元件時一致的方法：

- 如果您藉由在 AKS 引擎的叢集定義中設定 **addons** 屬性來進行安裝：

  將 _azure-policy_ 的 **addons** 屬性變更為 false 之後，將叢集定義重新部署至 AKS 引擎：


  ```json
  "addons": [{
      "name": "azure-policy",
      "enabled": false
  }]
  ```

  如需詳細資訊，請參閱 [AKS 引擎 - 停用 Azure 原則附加元件](https://github.com/Azure/aks-engine/blob/master/examples/addons/azure-policy/README.md#disable-azure-policy-add-on) \(英文\)。

- 如果使用 Helm Charts 安裝，請執行下列 Helm 命令：

  ```bash
  helm uninstall azure-policy-addon
  ```

## <a name="diagnostic-data-collected-by-azure-policy-add-on"></a>Azure 原則附加元件所收集的診斷資料

適用於 Kubernetes 的 Azure 原則附加元件會收集有限的叢集診斷資料。 此診斷資料是與軟體和效能相關的重要技術資料。 其使用方式如下：

- 讓 Azure 原則附加元件保持在最新狀態
- 確保 Azure 原則附加元件安全、可靠、高效能
- 改善 Azure 原則附加元件 - 透過使用附加元件時的彙總分析

附加元件所收集的資訊不是個人資料。 目前會收集下列詳細資料：

- Azure 原則附加元件代理程式版本
- 叢集類型
- 叢集區域
- 叢集資源群組
- 叢集資源識別碼
- 叢集訂用帳戶識別碼
- 叢集作業系統 (範例：LINUX)
- 叢集城市 (例如：西雅圖)
- 叢集的州或省 (例如：華盛頓州)
- 叢集的國家或地區 (例如：美國)
- 在原則評估上安裝代理程式期間，Azure 原則附加元件所遇到的例外狀況/錯誤
- Azure 原則附加元件未安裝的 Gatekeeper 原則定義數目

## <a name="next-steps"></a>後續步驟

- 在 [Azure 原則範例](../samples/index.md)檢閱範例。
- 檢閱[原則定義結構](definition-structure.md)。
- 檢閱[了解原則效果](effects.md)。
- 了解如何[以程式設計方式建立原則](../how-to/programmatically-create.md)。
- 了解如何[取得合規性資料](../how-to/get-compliance-data.md)。
- 了解如何[補救不符合規範的資源](../how-to/remediate-resources.md)。
- 透過[使用 Azure 管理群組來組織資源](../../management-groups/overview.md)來檢閱何謂管理群組。