---
title: 使用適用于容器的 Azure 監視器來設定啟用 Azure Arc 的 Kubernetes 叢集 |Microsoft Docs
description: 本文說明如何使用已啟用 Azure Arc Kubernetes 叢集上的容器 Azure 監視器來設定監視。
ms.topic: conceptual
ms.date: 06/23/2020
ms.openlocfilehash: f8002b20f37ca5149c58ca3e29402916ebbc1333
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87092876"
---
# <a name="enable-monitoring-of-azure-arc-enabled-kubernetes-cluster"></a>啟用已啟用 Azure Arc 的 Kubernetes 叢集監視

適用于容器的 Azure 監視器可為 Azure Kubernetes Service （AKS）和 AKS 引擎叢集提供豐富的監視體驗。 本文說明如何針對 Azure 上使用 Azure Arc 啟用的 Kubernetes 叢集啟用監視，以達到類似的監視體驗。

您可以使用 PowerShell 或 Bash 腳本，針對 Kubernetes 的一或多個現有部署啟用容器的 Azure 監視器。

## <a name="supported-configurations"></a>支援的設定

適用于[容器的 Azure 監視器](container-insights-overview.md)支援監視已啟用 Azure Arc 的 Kubernetes （預覽），如下列功能所述：

- 即時資料（預覽）

- 從叢集節點和 pod[收集計量](container-insights-update-metrics.md)，並將其儲存在 Azure 監視器計量資料庫中

以下已正式支援容器的 Azure 監視器：

- Kubernetes 和支援原則的版本與[支援的 AKS](../../aks/supported-kubernetes-versions.md)版本相同。

- 支援下列容器執行時間： Docker、Moby 和 CRI 相容的執行時間，例如 CRI-O 和 ContainerD。

- 支援的主要和背景工作節點的 Linux OS 版本為： Ubuntu （18.04 LTS 和 16.04 LTS）。

## <a name="prerequisites"></a>必要條件

開始之前，請確定您有下列項目：

- Log Analytics 工作區。

    容器的 Azure 監視器支援[依區域](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=monitor)在 Azure 產品中列出的區域中的 Log Analytics 工作區。 若要建立您自己的工作區，可以透過[Azure Resource Manager](../platform/template-workspace-configuration.md)、透過[PowerShell](../scripts/powershell-sample-create-workspace.md?toc=%2fpowershell%2fmodule%2ftoc.json)或在[Azure 入口網站](../learn/quick-create-workspace.md)中建立。

- 若要啟用及存取容器 Azure 監視器中的功能，您至少必須是 Azure 訂用帳戶中 Azure*參與者*角色的成員，以及 log analytics 工作區的[*log analytics 參與者*](../platform/manage-access.md#manage-access-using-azure-permissions)角色的成員，其已設定為使用 Azure 監視器的容器。

- 您是 Azure Arc 叢集資源上「[參與者](../../role-based-access-control/built-in-roles.md#contributor)」角色的成員。

- 若要查看監視資料，您是[*log analytics 讀取*](../platform/manage-access.md#manage-access-using-azure-permissions)者角色許可權的成員，其已設定適用于容器的 Azure 監視器的 log analytics 工作區。

- [HELM 用戶端](https://helm.sh/docs/using_helm/)，以針對指定的 Kubernetes 叢集，將容器的 Azure 監視器上架圖表。

- 適用于 Linux 的 Log Analytics 代理程式容器化版本需要下列 proxy 和防火牆設定資訊，才能與 Azure 監視器進行通訊：

    |代理程式資源|連接埠 |
    |------|---------|
    |`*.ods.opinsights.azure.com` |連接埠 443 |
    |`*.oms.opinsights.azure.com` |連接埠 443 |
    |`*.dc.services.visualstudio.com` |連接埠 443 |

- 容器化代理程式需要在 `cAdvisor secure port: 10250` 叢 `unsecure port :10255` 集中的所有節點上開啟 Kubelet 的或，才能收集效能計量。 我們建議您 `secure port: 10250` 在 Kubelet 的 cAdvisor 上進行設定（如果尚未設定）。

- 容器化代理程式需要在容器上指定下列環境變數，才能與叢集中的 Kubernetes API 服務進行通訊，以收集清查資料- `KUBERNETES_SERVICE_HOST` 和 `KUBERNETES_PORT_443_TCP_PORT` 。

    >[!IMPORTANT]
    >監視啟用 Arc 的 Kubernetes 叢集所支援的最低代理程式版本是 ciprod04162020 或更新版本。

- 如果您使用 PowerShell 腳本方法來啟用監視，則需要[Powershell Core](/powershell/scripting/install/installing-powershell?view=powershell-6) 。

- 如果您使用 Bash 腳本方法來啟用監視，則需要[Bash 第4版](https://www.gnu.org/software/bash/)。

## <a name="identify-workspace-resource-id"></a>識別工作區資源識別碼

若要使用您稍早下載的 PowerShell 或 bash 腳本來啟用叢集的監視，並與現有的 Log Analytics 工作區整合，請執行下列步驟，先找出 Log Analytics 工作區的完整資源識別碼。 `workspaceResourceId`當您執行命令以針對指定的工作區啟用監視附加元件時，參數就需要這種情況。 如果您沒有要指定的工作區，您可以略過包含 `workspaceResourceId` 參數的，然後讓腳本為您建立新的工作區。

1. 使用下列命令，列出您有權存取的所有訂用帳戶：

    ```azurecli
    az account list --all -o table
    ```

    輸出看起來會像下面這樣：

    ```azurecli
    Name                                  CloudName    SubscriptionId                        State    IsDefault
    ------------------------------------  -----------  ------------------------------------  -------  -----------
    Microsoft Azure                       AzureCloud   0fb60ef2-03cc-4290-b595-e71108e8f4ce  Enabled  True
    ```

    複製**SubscriptionId**的值。

2. 使用下列命令，切換至裝載 Log Analytics 工作區的訂用帳戶：

    ```azurecli
    az account set -s <subscriptionId of the workspace>
    ```

3. 下列範例會以預設的 JSON 格式顯示訂用帳戶中的工作區清單。

    ```
    az resource list --resource-type Microsoft.OperationalInsights/workspaces -o json
    ```

    在輸出中，尋找工作區名稱，然後將該 Log Analytics 工作區的完整資源識別碼複製到欄位**識別碼**之下。

## <a name="enable-monitoring-using-powershell"></a>使用 PowerShell 啟用監視

1. 使用下列命令，將腳本下載並儲存到本機資料夾，以使用監視附加元件來設定您的叢集：

    ```powershell
    wget https://aka.ms/enable-monitoring-powershell-script -outfile enable-monitoring.ps1
    ```

2. 設定的 `$azureArcClusterResourceId` 對應值 `subscriptionId` ， `resourceGroupName` 並 `clusterName` 代表已啟用 Azure Arc 之 Kubernetes 叢集資源的資源識別碼，來設定變數。

    ```powershell
    $azureArcClusterResourceId = "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Kubernetes/connectedClusters/<clusterName>"
    ```

3. 藉 `$kubeContext` 由執行命令，使用您叢集的**kube 內容**來設定變數 `kubectl config get-contexts` 。 如果您想要使用目前的內容，請將值設定為 `""` 。

    ```powershell
    $kubeContext = "<kubeContext name of your k8s cluster>"
    ```

4. 如果您想要使用現有的 Azure 監視器 Log Analytics 工作區，請 `$logAnalyticsWorkspaceResourceId` 使用代表工作區資源識別碼的對應值來設定變數。 否則，請將變數設定為 `""` ，腳本會在叢集訂用帳戶的預設資源群組中建立預設工作區（如果該區域中尚未存在）。 建立的預設工作區類似*DefaultWorkspace- \<SubscriptionID> - \<Region> *的格式。

    ```powershell
    $logAnalyticsWorkspaceResourceId = “/subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/microsoft.operationalinsights/workspaces/<workspaceName>”
    ```

5. 如果已啟用 Arc 的 Kubernetes 叢集透過 proxy 伺服器進行通訊，請 `$proxyEndpoint` 使用 proxy 伺服器的 URL 來設定變數。 如果叢集未透過 proxy 伺服器進行通訊，則您可以將值設定為 `""` 。  如需詳細資訊，請參閱本文稍後的[設定 proxy 端點](#configure-proxy-endpoint)。

6. 執行下列命令以啟用監視。

    ```powershell
    .\enable-monitoring.ps1 -clusterResourceId $azureArcClusterResourceId -kubeContext $kubeContext -workspaceResourceId $logAnalyticsWorkspaceResourceId -proxyEndpoint $proxyEndpoint
    ```

啟用監視之後，可能需要約 15 分鐘的時間才能檢視叢集的健康情況計量。

## <a name="enable-using-bash-script"></a>使用 bash 腳本來啟用

執行下列步驟，使用提供的 bash 腳本來啟用監視。

1. 使用下列命令，將腳本下載並儲存到本機資料夾，以使用監視附加元件來設定您的叢集：

    ```bash
    curl -o enable-monitoring.sh -L https://aka.ms/enable-monitoring-bash-script
    ```

2. 設定的 `azureArcClusterResourceId` 對應值 `subscriptionId` ， `resourceGroupName` 並 `clusterName` 代表已啟用 Azure Arc 之 Kubernetes 叢集資源的資源識別碼，來設定變數。

    ```bash
    export azureArcClusterResourceId="/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Kubernetes/connectedClusters/<clusterName>"
    ```

3. 藉 `kubeContext` 由執行命令，使用您叢集的**kube 內容**來設定變數 `kubectl config get-contexts` 。 如果您想要使用目前的內容，請將值設定為 `""` 。

    ```bash
    export kubeContext="<kubeContext name of your k8s cluster>"
    ```

4. 如果您想要使用現有的 Azure 監視器 Log Analytics 工作區，請 `logAnalyticsWorkspaceResourceId` 使用代表工作區資源識別碼的對應值來設定變數。 否則，請將變數設定為 `""` ，腳本會在叢集訂用帳戶的預設資源群組中建立預設工作區（如果該區域中尚未存在）。 建立的預設工作區類似*DefaultWorkspace- \<SubscriptionID> - \<Region> *的格式。

    ```bash
    export logAnalyticsWorkspaceResourceId=“/subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/microsoft.operationalinsights/workspaces/<workspaceName>”
    ```

5. 如果已啟用 Arc 的 Kubernetes 叢集透過 proxy 伺服器進行通訊，請 `proxyEndpoint` 使用 proxy 伺服器的 URL 來設定變數。 如果叢集未透過 proxy 伺服器進行通訊，則您可以將值設定為 `""` 。 如需詳細資訊，請參閱本文稍後的[設定 proxy 端點](#configure-proxy-endpoint)。

6. 若要在您的叢集上啟用監視，根據您的部署案例，會提供不同的命令。

    執行下列命令來啟用使用預設選項的監視，例如使用目前的 kube 內容、建立預設的 Log Analytics 工作區，而不指定 proxy 伺服器：

    ```bash
    bash enable-monitoring.sh --resource-id $azureArcClusterResourceId
    ```

    執行下列命令來建立預設的 Log Analytics 工作區，而不指定 proxy 伺服器：

    ```bash
   bash enable-monitoring.sh --resource-id $azureArcClusterResourceId --kube-context $kubeContext
    ```

    執行下列命令以使用現有的 Log Analytics 工作區，而不指定 proxy 伺服器：

    ```bash
    bash enable-monitoring.sh --resource-id $azureArcClusterResourceId --kube-context $kubeContext  --workspace-id $logAnalyticsWorkspaceResourceId
    ```

    執行下列命令以使用現有的 Log Analytics 工作區，並指定 proxy 伺服器：

    ```bash
    bash enable-monitoring.sh --resource-id $azureArcClusterResourceId --kube-context $kubeContext  --workspace-id $logAnalyticsWorkspaceResourceId --proxy $proxyEndpoint
    ```

啟用監視之後，可能需要約 15 分鐘的時間才能檢視叢集的健康情況計量。

## <a name="configure-proxy-endpoint"></a>設定 proxy 端點

有了容器的容器化代理 Azure 監視器程式，您可以設定 proxy 端點，讓它能夠透過您的 proxy 伺服器進行通訊。 容器化代理程式和 Azure 監視器之間的通訊可以是 HTTP 或 HTTPS proxy 伺服器，同時支援匿名和基本驗證（使用者名稱/密碼）。

Proxy 設定值具有下列語法：`[protocol://][user:password@]proxyhost[:port]`

> [!NOTE]
>如果您的 proxy 伺服器不需要驗證，您仍然需要指定虛擬使用者名稱/密碼。 這可以是任何使用者名稱或密碼。

|屬性| 描述 |
|--------|-------------|
|通訊協定 | http 或 https |
|user | 用於驗證 Proxy 的選擇性使用者名稱 |
|密碼 | 用於驗證 Proxy 的選擇性密碼 |
|proxyhost | Proxy 伺服器的位址或 FQDN |
|連接埠 | Proxy 伺服器的選擇性埠號碼 |

例如：`http://user01:password@proxy01.contoso.com:3128`

如果您將通訊協定指定為**HTTP**，則會使用 SSL/TLS 安全連線來建立 HTTP 要求。 您的 proxy 伺服器必須支援 SSL/TLS 通訊協定。

### <a name="configure-using-powershell"></a>使用 PowerShell 進行設定

指定 [使用者名稱] 和 [密碼]、[IP 位址] 或 [FQDN]，以及 proxy 伺服器的 [埠號碼]。 例如:

```powershell
$proxyEndpoint = https://<user>:<password>@<proxyhost>:<port>
```

### <a name="configure-using-bash"></a>使用 bash 進行設定

指定 [使用者名稱] 和 [密碼]、[IP 位址] 或 [FQDN]，以及 proxy 伺服器的 [埠號碼]。 例如：

```bash
export proxyEndpoint=https://<user>:<password>@<proxyhost>:<port>
```

## <a name="next-steps"></a>後續步驟

- 啟用監視以收集已啟用 Arc 之 Kubernetes 叢集的健康情況與資源使用率，以及在其上執行的工作負載，瞭解[如何將](container-insights-analyze.md)Azure 監視器用於容器。

- 根據預設，容器化代理程式會收集所有命名空間中執行之所有容器的 stdout/stderr 容器記錄（kube 除外）。 若要設定特定命名空間或命名空間專屬的容器記錄檔集合，請參閱[Container Insights 代理程式](container-insights-agent-config.md)設定，以設定您的 ConfigMap 設定檔案所需的資料收集設定。

- 若要從您的叢集中抓取和分析 Prometheus 計量，請參閱[設定 Prometheus 計量抓取](container-insights-prometheus-integration.md)

- 若要瞭解如何使用適用于容器的 Azure 監視器來停止監視已啟用 Arc 的 Kubernetes 叢集，請參閱[如何停止監視混合](container-insights-optout-hybrid.md#how-to-stop-monitoring-on-arc-enabled-kubernetes)式叢集。
