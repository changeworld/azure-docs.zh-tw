---
title: 使用適用于容器的 Azure 監視器來設定 Azure Red Hat OpenShift v4. x |Microsoft Docs
description: 本文說明如何使用裝載于 Azure Red Hat OpenShift 第4版和更新版本上的 Azure 監視器來設定監視 Kubernetes 叢集。
ms.topic: conceptual
ms.date: 04/22/2020
ms.openlocfilehash: 4b827524845874dabaabe535163d99c408f77a60
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "82196292"
---
# <a name="configure-azure-red-hat-openshift-v4x-with-azure-monitor-for-containers"></a>使用適用于容器的 Azure 監視器來設定 Azure Red Hat OpenShift v4. x

適用于容器的 Azure 監視器可為 Azure Kubernetes Service （AKS）和 AKS 引擎叢集提供豐富的監視體驗。 本文說明如何啟用[Azure Red Hat OpenShift](../../openshift/intro-openshift.md) 1.x 版所裝載的 Kubernetes 叢集監視，以達到類似的監視體驗。

>[!NOTE]
>Azure Red Hat OpenShift 的支援目前是公開預覽中的一項功能。
>

您可以使用下列支援的方法，針對 Azure Red Hat OpenShift v4 的一或多個現有部署啟用容器的 Azure 監視器：

- 若為現有的叢集，請使用提供的 bash 腳本，並在[Azure CLI](https://docs.microsoft.com/cli/azure/openshift?view=azure-cli-latest#az-openshift-create)中執行。

## <a name="supported-and-unsupported-features"></a>支援和不支援的功能

容器[的 Azure 監視器](container-insights-overview.md)支援監視 Azure Red Hat OpenShift v4，如下列功能所述：

- 即時資料（預覽）
- 從叢集節點和 pod[收集計量](container-insights-update-metrics.md)，並將其儲存在 Azure 監視器計量資料庫中

## <a name="prerequisites"></a>Prerequisites

- Azure CLI 2.0.72 或更高版本

- [Helm 3](https://helm.sh/docs/intro/install/)CLI 工具

- [Bash 第4版](https://www.gnu.org/software/bash/)

- [Kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/)命令列工具

- 若要啟用及存取容器 Azure 監視器中的功能，您至少必須是 Azure 訂用帳戶中 Azure*參與者*角色的成員，以及 log analytics 工作區的[*log analytics 參與者*](../platform/manage-access.md#manage-access-using-azure-permissions)角色的成員，其已設定為使用 Azure 監視器的容器。

- 若要查看監視資料，您是[*log analytics 讀取*](../platform/manage-access.md#manage-access-using-azure-permissions)者角色許可權的成員，其已設定適用于容器的 Azure 監視器的 log analytics 工作區。

## <a name="enable-for-an-existing-cluster"></a>針對現有的叢集啟用

執行下列步驟，以使用所提供的 bash 腳本，來監視 azure 中部署的 Azure Red Hat OpenShift 第4版和更新版本的叢集。

1. 登入 Azure

    ```azurecli
    az login
    ```

2. 使用下列命令，將腳本下載並儲存到本機資料夾，以使用監視附加元件來設定您的叢集：

    `curl -LO https://raw.githubusercontent.com/microsoft/OMS-docker/ci_feature/docs/aroV4/onboarding_azuremonitor_for_containers.sh.`

3. 若要識別叢集的**kube 內容**，請在叢集成功`oc login`之後，執行命令`kubectl config current-context`並複製值。

### <a name="integrate-with-an-existing-workspace"></a>與現有的工作區整合

下列步驟會使用您稍早下載的 bash 腳本來啟用叢集的監視。 若要與現有的 Log Analytics 工作區整合，請執行下列步驟，先找出`workspaceResourceId`參數所需之 Log Analytics 工作區的完整資源識別碼，然後執行命令，針對指定的工作區啟用監視附加元件。 如果您沒有要指定的工作區，您可以跳到步驟5，然後讓腳本為您建立新的工作區。

1. 使用下列命令，列出您有權存取的所有訂用帳戶：

    ```azurecli
    az account list --all -o table
    ```

    輸出看起來會像下面這樣：

    ```azurecli
    Name                                  CloudName    SubscriptionId                        State    IsDefault
    ------------------------------------  -----------  ------------------------------------  -------  -----------
    Microsoft Azure                       AzureCloud   68627f8c-91fO-4905-z48q-b032a81f8vy0  Enabled  True
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

4. 執行下列命令來啟用監視，並取代`workspaceResourceId`參數的值： 

    `bash onboarding_azuremonitor_for_containers.sh <kube-context> <azureAroV4ResourceId> <LogAnayticsWorkspaceResourceId>`

    範例：

    `bash onboarding_azuremonitor_for_containers.sh MyK8sTestCluster /subscriptions/57ac26cf-a9f0-4908-b300-9a4e9a0fb205/resourceGroups/test-aro-v4-rg/providers/Microsoft.RedHatOpenShift/OpenShiftClusters/test-aro-v4  /subscriptions/57ac26cf-a9f0-4908-b300-9a4e9a0fb205/resourcegroups/test-la-workspace-rg/providers/microsoft.operationalinsights/workspaces/test-la-workspace`

啟用監視之後，可能需要約 15 分鐘的時間才能檢視叢集的健康情況計量。

### <a name="integrate-with-default-workspace"></a>與預設工作區整合

下列步驟會使用您下載的 bash 腳本，來監視您的 Azure Red Hat OpenShift v4. x 叢集。 在此範例中，您不需要預先建立或指定現有的工作區。 此命令會在叢集訂用帳戶的預設資源群組中建立預設工作區，以簡化您的程式（如果區域中尚未存在）。 所建立的預設工作區格式類似 *DefaultWorkspace-\<GUID>-\<Region>*。  

    `bash onboarding_azuremonitor_for_containers.sh <kube-context> <azureAroV4ResourceId>`

    For example:

    `bash onboarding_azuremonitor_for_containers.sh MyK8sTestCluster /subscriptions/57ac26cf-a9f0-4908-b300-9a4e9a0fb205/resourceGroups/test-aro-v4-rg/providers/Microsoft.RedHatOpenShift/OpenShiftClusters/test-aro-v4`

啟用監視之後，可能需要約 15 分鐘的時間才能檢視叢集的健康情況計量。

### <a name="from-the-azure-portal"></a>從 Azure 入口網站

容器 Azure 監視器中的多叢集視圖會反白顯示未在 [**受監視**的叢集] 索引標籤下啟用監視的 Azure Red Hat OpenShift 叢集。叢集旁的 [**啟用**] 選項不會從入口網站起始監視的上架。 您會被重新導向至本文，以手動啟用遵循本文稍早步驟的監視。

1. 登入 [Azure 入口網站](https://portal.azure.com)。

2. 在 [Azure 入口網站] 功能表上或從首頁選取 [ **Azure 監視器**]。 在 [Insights]**** 區段下方，選取 [容器]****。

3. 在 [監視器 - 容器]**** 頁面上，選取 [不受監視的叢集]****。

4. 從未受監視的叢集清單中，尋找清單中的叢集，然後按一下 [**啟用**]。 您可以在 [資料行叢集**類型**] 底下尋找值**ARO** ，以識別清單中的結果。 按一下 [**啟用**] 之後，系統會將您重新導向至這篇文章。

## <a name="next-steps"></a>後續步驟

- 啟用監視以收集 RedHat OpenShift 1.x 版叢集的健全狀況和資源使用率，以及在其上執行的工作負載，瞭解[如何將](container-insights-analyze.md)Azure 監視器用於容器。

- 根據預設，容器化代理程式會收集所有命名空間中執行之所有容器的 stdout/stderr 容器記錄（kube 除外）。 若要設定特定命名空間或命名空間專屬的容器記錄檔集合，請參閱[Container Insights 代理程式](container-insights-agent-config.md)設定，以設定您的 ConfigMap 設定檔案所需的資料收集設定。

- 若要從您的叢集中抓取和分析 Prometheus 計量，請參閱[設定 Prometheus 計量抓取](container-insights-prometheus-integration.md)

- 若要瞭解如何使用容器的 Azure 監視器來停止監視您的叢集，請參閱[如何停止監視您的 Azure Red Hat OpenShift](container-insights-optout-openshift.md)叢集。
