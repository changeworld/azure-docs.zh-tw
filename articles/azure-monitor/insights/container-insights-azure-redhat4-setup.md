---
title: 使用容器 Azure 監視器設定 Azure Red Hat OpenShift v4. x |Microsoft Docs
description: 本文說明如何使用 Azure Red Hat OpenShift 第4版或更新版本上裝載的 Azure 監視器來設定 Kubernetes 叢集的監視。
ms.topic: conceptual
ms.date: 06/30/2020
ms.openlocfilehash: 63db888419ee691e83ea456fcd7fc28a4d9909fe
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91620319"
---
# <a name="configure-azure-red-hat-openshift-v4x-with-azure-monitor-for-containers"></a>使用容器 Azure 監視器設定 Azure Red Hat OpenShift v4. x

適用于容器的 Azure 監視器可針對 Azure Kubernetes Service (AKS) 和 AKS 引擎叢集提供豐富的監視體驗。 本文說明如何針對裝載于 [Azure Red Hat OpenShift](../../openshift/intro-openshift.md) 4.x 版的 Kubernetes 叢集啟用監視，以達成類似的監視體驗。

>[!NOTE]
>Azure Red Hat OpenShift 的支援目前為公開預覽功能。
>

您可以使用本文中所述的支援方法，為 Azure Red Hat OpenShift v4. x 的一或多個現有部署啟用容器的 Azure 監視器。

針對現有的叢集，請 [在 Azure CLI 中執行此 Bash 腳本](/cli/azure/openshift?view=azure-cli-latest#az-openshift-create&preserve-view=true)。

## <a name="supported-and-unsupported-features"></a>支援和不支援的功能

容器的 Azure 監視器支援監視 Azure Red Hat OpenShift v4. x （如 [容器的 Azure 監視器](container-insights-overview.md)所述），但有下列功能：

- 即時資料 (預覽) 
- 從叢集節點和 pod[收集計量](container-insights-update-metrics.md)，並將它們儲存在 Azure 監視器計量資料庫中

## <a name="prerequisites"></a>必要條件

- Azure CLI 2.0.72 版或更新版本  

- [Helm 3](https://helm.sh/docs/intro/install/) CLI 工具

- [Bash 第4版](https://www.gnu.org/software/bash/)

- [Kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/)命令列工具

- [Log Analytics 工作區](../platform/design-logs-deployment.md)。

    適用于容器的 Azure 監視器支援 [依區域](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=monitor)列示在 Azure 產品的區域中的 Log Analytics 工作區。 若要建立您自己的工作區，您可以透過 [Azure Resource Manager](../platform/template-workspace-configuration.md)、 [PowerShell](../scripts/powershell-sample-create-workspace.md?toc=%2fpowershell%2fmodule%2ftoc.json)或 [Azure 入口網站](../learn/quick-create-workspace.md)來建立它。

- 若要啟用和存取容器 Azure 監視器的功能，您必須至少擁有 Azure 訂用帳戶中的 Azure *參與者* 角色，以及 log analytics 工作區中的 [*log analytics 參與者*](../platform/manage-access.md#manage-access-using-azure-permissions) 角色（以容器的 Azure 監視器設定）。

- 若要查看監視資料，您必須在 Log Analytics 工作區中擁有 [*Log analytics 讀者*](../platform/manage-access.md#manage-access-using-azure-permissions) 角色，並使用容器的 Azure 監視器設定。

## <a name="enable-monitoring-for-an-existing-cluster"></a>針對現有的叢集啟用監視

若要使用所提供的 Bash 腳本來監視在 Azure 中部署的 Azure Red Hat OpenShift 第4版或更新版本的叢集，請執行下列動作：

1. 執行下列命令以登入 Azure：

    ```azurecli
    az login
    ```

1. 藉由執行下列命令，下載並儲存至本機資料夾的腳本，以使用監視增益集設定您的叢集：

    `curl -o enable-monitoring.sh -L https://aka.ms/enable-monitoring-bash-script`

1. 若要識別叢集的 *kubeCoNtext* ，請執行下列命令

    ```
    adminUserName=$(az aro list-credentials -g $clusterResourceGroup -n $clusterName --query 'kubeadminUsername' -o tsv)
    adminPassword=$(az aro list-credentials -g $clusterResourceGroup -n $clusterName --query 'kubeadminPassword' -o tsv)
    apiServer=$(az aro show -g $clusterResourceGroup -n $clusterName --query apiserverProfile.url -o tsv)
    oc login $apiServer -u $adminUserName -p $adminPassword
    # openshift project name for azure monitor for containers
    openshiftProjectName="azure-monitor-for-containers"
    oc new-project $openshiftProjectName
    # get the kube config context
    kubeContext=$(oc config current-context)
    ```

1. 複製值以供稍後使用。

### <a name="integrate-with-an-existing-workspace"></a>與現有的工作區整合

在本節中，您會使用稍早下載的 Bash 腳本來啟用對叢集的監視。 若要整合現有的 Log Analytics 工作區，請先識別參數所需之 Log Analytics 工作區的完整資源識別碼， `logAnalyticsWorkspaceResourceId` 然後執行命令以針對指定的工作區啟用監視增益集。

如果您沒有可指定的工作區，您可以跳到 [ [與預設工作區整合](#integrate-with-the-default-workspace) ] 區段，並讓腳本為您建立新的工作區。

1. 藉由執行下列命令，列出您有權存取的所有訂用帳戶：

    ```azurecli
    az account list --all -o table
    ```

    輸出顯示如下：

    ```azurecli
    Name                                  CloudName    SubscriptionId                        State    IsDefault
    ------------------------------------  -----------  ------------------------------------  -------  -----------
    Microsoft Azure                       AzureCloud   0fb60ef2-03cc-4290-b595-e71108e8f4ce  Enabled  True
    ```

1. 複製 **SubscriptionId**的值。

1. 藉由執行下列命令，切換至裝載 Log Analytics 工作區的訂用帳戶：

    ```azurecli
    az account set -s <subscriptionId of the workspace>
    ```

1. 藉由執行下列命令，以預設的 JSON 格式顯示訂用帳戶中的工作區清單：

    ```
    az resource list --resource-type Microsoft.OperationalInsights/workspaces -o json
    ```

1. 在輸出中，尋找工作區名稱，然後在欄位 **識別碼**下複製該 Log Analytics 工作區的完整資源識別碼。

1. 若要啟用監視，請執行下列命令。 取代 `azureAroV4ClusterResourceId` 、和參數的值 `logAnalyticsWorkspaceResourceId` `kubeContext` 。

    ```bash
    export azureAroV4ClusterResourceId=“/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.RedHatOpenShift/OpenShiftClusters/<clusterName>”
    export logAnalyticsWorkspaceResourceId=“/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/microsoft.operationalinsights/workspaces/<workspaceName>”
    export kubeContext="<kubeContext name of your ARO v4 cluster>"  
    ```

    範例：

    `bash enable-monitoring.sh --resource-id $azureAroV4ClusterResourceId --kube-context $kubeContext --workspace-id $logAnalyticsWorkspaceResourceId`

啟用監視之後，可能需要大約15分鐘的時間，您才能查看叢集的健康情況計量。

### <a name="integrate-with-the-default-workspace"></a>與預設工作區整合

在本節中，您會使用您下載的 Bash 腳本來啟用 Azure Red Hat OpenShift v4. x 叢集的監視。

在此範例中，您不需要預先建立或指定現有的工作區。 此命令會在叢集訂用帳戶的預設資源群組中建立預設工作區，以簡化您的程式，如果區域中還沒有該工作區存在的話。

所建立的預設工作區格式為*DefaultWorkspace- \<GUID> - \<Region> *。  

取代和參數的值 `azureAroV4ClusterResourceId` `kubeContext` 。

```bash
export azureAroV4ClusterResourceId="/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.RedHatOpenShift/OpenShiftClusters/<clusterName>"
export kubeContext="<kubeContext name of your ARO v4 cluster>"
```

例如：

`bash enable-monitoring.sh --resource-id $azureAroV4ClusterResourceId --kube-context $kubeContext`

啟用監視之後，可能需要約 15 分鐘的時間才能檢視叢集的健康情況計量。

### <a name="enable-monitoring-from-the-azure-portal"></a>從 Azure 入口網站啟用監視

適用于容器的 Azure 監視器中的多重叢集視圖會 **在 [未受監視的** 叢集] 索引標籤下，反白顯示未啟用監視的 Azure Red Hat OpenShift 叢集。叢集旁的 [ **啟用** ] 選項不會從入口網站起始監視的上架。 您會被重新導向至本文，以依照本文稍早所述的步驟來手動啟用監視。

1. 登入 [Azure 入口網站](https://portal.azure.com)。

1. 在左窗格上，或從 [首頁] 中，選取 [ **Azure 監視器**]。

1. 在 [ **見解** ] 區段中，選取 [ **容器**]。

1. 在 [ **監視-容器** ] 頁面上，選取 [未受 **監視**的叢集]。

1. 在未受監視叢集的清單中，選取叢集，然後選取 [ **啟用**]。

    您可以在 [叢集**類型**] 欄中尋找**ARO**值，以識別清單中的結果。 當您選取 [ **啟用**] 之後，系統會將您重新導向到這篇文章。

## <a name="next-steps"></a>後續步驟

- 既然您已啟用監視來收集 RedHat OpenShift 4.x 叢集的健康情況和資源使用量，以及它們上執行的工作負載，請瞭解 [如何使用](container-insights-analyze.md) 容器 Azure 監視器。

- 根據預設，容器化代理程式會收集在所有命名空間中執行之所有容器的 *stdout* 和 *stderr* 容器記錄，但 kube 系統除外。 若要設定特定命名空間或命名空間專屬的容器記錄檔集合，請參閱 [Container Insights 代理程式](container-insights-agent-config.md) 設定，以針對您的 *ConfigMap* 設定檔設定您想要的資料收集設定。

- 若要從您的叢集抓取並分析 Prometheus 計量，請參閱 [設定 Prometheus 計量抓取](container-insights-prometheus-integration.md)。

- 若要瞭解如何使用容器的 Azure 監視器停止監視您的叢集，請參閱 [如何停止監視您的 Azure Red Hat OpenShift](./container-insights-optout-openshift-v3.md)叢集。
