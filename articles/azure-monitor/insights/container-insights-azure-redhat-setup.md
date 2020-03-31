---
title: 使用 Azure 監視器配置 Azure 紅帽 OpenShift 群集，用於容器 |微軟文檔
description: 本文介紹如何使用 Azure 紅帽 OpenShift 上託管的 Azure 監視器配置庫伯奈斯群集的監視。
ms.topic: conceptual
ms.date: 02/12/2020
ms.openlocfilehash: c2fd3568be2c51296bb1377e91031ebfb7ca6ee3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79275512"
---
# <a name="configure-azure-red-hat-openshift-clusters-with-azure-monitor-for-containers"></a>使用容器的 Azure 監視器配置 Azure 紅帽打開 Shift 群集

容器的 Azure 監視器為 Azure 庫伯奈服務 （AKS） 和 AKS 引擎群集提供了豐富的監視體驗。 本文介紹如何啟用對[Azure 紅帽 OpenShift](../../openshift/intro-openshift.md)上託管的 Kubernetes 群集的監視，以實現類似的監視體驗。

>[!NOTE]
>支援 Azure 紅帽 OpenShift 是此時公共預覽中的一項功能。
>

可以使用以下支援的方法為容器啟用 Azure 紅帽 OpenShift 的一個或多個現有部署的 Azure 紅帽 OpenShift 的 Azure 監視器監視器：

- 對於 Azure 門戶或使用 Azure 資源管理器範本的現有群集。
- 對於使用 Azure 資源管理器範本的新群集，或使用[Azure CLI](https://docs.microsoft.com/cli/azure/openshift?view=azure-cli-latest#az-openshift-create)創建新群集時。

## <a name="supported-and-unsupported-features"></a>支援且不受支援的功能

容器的 Azure 監視器支援監視 Azure 紅帽 OpenShift，[如概述](container-insights-overview.md)文章中所述，以下功能除外：

- 即時資料（預覽）
- 從叢集節點和窗格[收集指標](container-insights-update-metrics.md)並將其存儲在 Azure 監視器指標資料庫中

## <a name="prerequisites"></a>Prerequisites

- 要啟用和訪問容器 Azure 監視器中的功能，至少需要成為 Azure 訂閱中的 Azure*參與者*角色的成員，以及配置了用於容器的 Azure 監視器的[*日誌分析工作區的日誌分析參與者*](../platform/manage-access.md#manage-access-using-azure-permissions)角色的成員。

- 要查看監視資料，您是[*日誌分析讀取器*](../platform/manage-access.md#manage-access-using-azure-permissions)角色許可權的成員，日誌分析工作區配置了用於容器的 Azure 監視器。

## <a name="enable-for-a-new-cluster-using-an-azure-resource-manager-template"></a>使用 Azure 資源管理器範本為新群集啟用

執行以下步驟以部署啟用監視的 Azure 紅帽 OpenShift 群集。 在繼續之前，請查看教程["創建 Azure 紅帽 OpenShift"群集](../../openshift/tutorial-create-cluster.md#prerequisites)，以瞭解需要配置的依賴項，以便正確設置環境。

此方法包含兩個 JSON 範本。 一個範本指定部署啟用監視的群集的配置，另一個範本包含您配置為指定以下內容的參數值：

- Azure 紅帽 OpenShift 群集資源識別碼。

- 群集部署在其中的資源組。

- [Azure 活動目錄租戶 ID](../../openshift/howto-create-tenant.md#create-a-new-azure-ad-tenant)在執行創建一個或一個已創建的步驟後記錄。

- [Azure 活動目錄用戶端應用程式 ID](../../openshift/howto-aad-app-configuration.md#create-an-azure-ad-app-registration)在執行創建一個或一個已創建的步驟後記錄。

- [Azure 活動目錄用戶端金鑰](../../openshift/howto-aad-app-configuration.md#create-a-client-secret)在執行創建已創建一個或一個的步驟後記錄。

- [Azure AD 安全性群組](../../openshift/howto-aad-app-configuration.md#create-an-azure-ad-security-group)在執行創建一個或一個已創建的步驟後指出。

- 現有日誌分析工作區的資源識別碼。

- 要在群集中創建的主節點數。

- 代理池設定檔中的計算節點數。

- 代理池設定檔中的基礎結構節點數。

若您不熟悉使用範本來部署資源的概念，請參閱：

- [使用 Resource Manager 範本與 Azure PowerShell 來部署資源](../../azure-resource-manager/templates/deploy-powershell.md)

- [使用 Resource Manager 範本與 Azure CLI 部署資源](../../azure-resource-manager/templates/deploy-cli.md)

如果您選擇使用 Azure CLI，必須先在本機安裝並使用 CLI。 您必須運行 Azure CLI 版本 2.0.65 或更高版本。 若要知道您使用的版本，請執行 `az --version`。 如果您需要安裝或升級 Azure CLI，請參閱[安裝 Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)。

在使用 Azure PowerShell 或 CLI 啟用監視之前，必須創建日誌分析工作區。 若要建立工作區，您可以透過 [Azure Resource Manager](../../azure-monitor/platform/template-workspace-configuration.md)、透過 [PowerShell](../scripts/powershell-sample-create-workspace.md?toc=%2fpowershell%2fmodule%2ftoc.json)，或是在 [Azure 入口網站](../../azure-monitor/learn/quick-create-workspace.md)中設定它。

1. 下載並保存到本地資料夾 Azure 資源管理器範本和參數檔，以便使用以下命令創建具有監視載入項的群集：

    `curl -LO https://raw.githubusercontent.com/microsoft/OMS-docker/ci_feature/docs/aro/enable_monitoring_to_new_cluster/newClusterWithMonitoring.json`

    `curl -LO https://raw.githubusercontent.com/microsoft/OMS-docker/ci_feature/docs/aro/enable_monitoring_to_new_cluster/newClusterWithMonitoringParam.json`

2. 登入 Azure

    ```azurecli
    az login    
    ```

    如果您可存取多個訂用帳戶，請執行 `az account set -s {subscription ID}` 並以您要使用的訂用帳戶取代 `{subscription ID}`。

3. 如果尚未創建資源組，請為群集創建資源組。 有關在 Azure 上支援 OpenShift 的 Azure 區域的清單，請參閱[支援區域](../../openshift/supported-resources.md#azure-regions)。

    ```azurecli
    az group create -g <clusterResourceGroup> -l <location>
    ```

4. 編輯 JSON 參數檔**newCluster 與監視Param.json**並更新以下值：

    - *位置*
    - *群集名稱*
    - *阿德特裡德*
    - *aadClientId*
    - *阿德客戶秘密*
    - *aadCustomerAdminGroupId*
    - *workspaceResourceId*
    - *主節點計數*
    - *計算節點計數*
    - *紅外線計數*

5. 以下步驟部署使用 Azure CLI 啟用監視的群集。

    ```azurecli
    az group deployment create --resource-group <ClusterResourceGroupName> --template-file ./newClusterWithMonitoring.json --parameters @./newClusterWithMonitoringParam.json
    ```

    輸出結果類似下面：

    ```output
    provisioningState       : Succeeded
    ```

## <a name="enable-for-an-existing-cluster"></a>為現有群集啟用

執行以下步驟，以啟用監視部署在 Azure 中的 Azure 紅帽 OpenShift 群集。 可以從 Azure 門戶或使用提供的範本完成此目的。

### <a name="from-the-azure-portal"></a>從 Azure 入口網站

1. 登錄到 Azure[門戶](https://portal.azure.com)。

2. 在 Azure 門戶功能表或主頁上，選擇**Azure 監視器**。 在 [Insights]**** 區段下方，選取 [容器]****。

3. 在 [監視器 - 容器]**** 頁面上，選取 [不受監視的叢集]****。

4. 從非受監視群集清單中，查找清單中的群集 **，然後按一下啟用**。 您可以通過在列**CLUSTER TYPE**下查找值**ARO**來識別欄位表中的結果。

5. 在 [適用於容器的 Azure 監視器上線]**** 頁面上，如果相同訂用帳戶中有現有 Log Analytics 工作區可作為叢集，請從下拉式清單中加以選取。  
    該清單預先選擇群集在訂閱中部署到的預設工作區和位置。

    ![啟用對未監控的群集的監視](./media/container-insights-onboard/kubernetes-onboard-brownfield-01.png)

    >[!NOTE]
    >如果您想要建立新的 Log Analytics 工作區以儲存來自叢集的監視資料，請遵循[建立 Log Analytics 工作區](../../azure-monitor/learn/quick-create-workspace.md)中的指示。 請確保在部署 RedHat OpenShift 群集的同一訂閱中創建工作區。

啟用監視之後，可能需要約 15 分鐘的時間才能檢視叢集的健康情況計量。

### <a name="enable-using-an-azure-resource-manager-template"></a>使用 Azure 資源管理器範本啟用

此方法包含兩個 JSON 範本。 一個範本會指定啟用監視的設定，另一個範本則包含可設定以指定下列各項的參數值：

- Azure 紅帽開放移位群集資源識別碼。

- 群集部署在其中的資源組。

- Log Analytics 工作區。

若您不熟悉使用範本來部署資源的概念，請參閱：

- [使用 Resource Manager 範本與 Azure PowerShell 來部署資源](../../azure-resource-manager/templates/deploy-powershell.md)

- [使用 Resource Manager 範本與 Azure CLI 部署資源](../../azure-resource-manager/templates/deploy-cli.md)

如果您選擇使用 Azure CLI，必須先在本機安裝並使用 CLI。 您必須運行 Azure CLI 版本 2.0.65 或更高版本。 若要知道您使用的版本，請執行 `az --version`。 如果您需要安裝或升級 Azure CLI，請參閱[安裝 Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)。

在使用 Azure PowerShell 或 CLI 啟用監視之前，必須創建日誌分析工作區。 若要建立工作區，您可以透過 [Azure Resource Manager](../../azure-monitor/platform/template-workspace-configuration.md)、透過 [PowerShell](../scripts/powershell-sample-create-workspace.md?toc=%2fpowershell%2fmodule%2ftoc.json)，或是在 [Azure 入口網站](../../azure-monitor/learn/quick-create-workspace.md)中設定它。

1. 下載範本和參數檔，使用以下命令使用監視載入項更新群集：

    `curl -LO https://raw.githubusercontent.com/microsoft/OMS-docker/ci_feature/docs/aro/enable_monitoring_to_existing_cluster/existingClusterOnboarding.json`

    `curl -LO https://raw.githubusercontent.com/microsoft/OMS-docker/ci_feature/docs/aro/enable_monitoring_to_existing_cluster/existingClusterParam.json`

2. 登入 Azure

    ```azurecli
    az login    
    ```

    如果您可存取多個訂用帳戶，請執行 `az account set -s {subscription ID}` 並以您要使用的訂用帳戶取代 `{subscription ID}`。

3. 指定 Azure RedHat OpenShift 群集的訂閱。

    ```azurecli
    az account set --subscription "Subscription Name"  
    ```

4. 運行以下命令以標識群集位置和資源識別碼：

    ```azurecli
    az openshift show -g <clusterResourceGroup> -n <clusterName>
    ```

5. 編輯JSON參數檔**現有的ClusterParam.json**並更新值*阿拉資源Id*和*阿拉雷索魯塞定位*。 **workspaceResourceId** 值是您 Log Analytics 工作區的完整資源識別碼，其中包含工作區名稱。

6. 要使用 Azure CLI 進行部署，運行以下命令：

    ```azurecli
    az group deployment create --resource-group <ClusterResourceGroupName> --template-file ./ExistingClusterOnboarding.json --parameters @./existingClusterParam.json
    ```

    輸出結果類似下面：

    ```output
    provisioningState       : Succeeded
    ```

## <a name="next-steps"></a>後續步驟

- 啟用監視以收集 RedHat OpenShift 群集的運行狀況和資源利用率以及運行在它們的工作負載後，[瞭解如何將](container-insights-analyze.md)Azure 監視器用於容器。

- 要瞭解如何停止使用 Azure 監視器監視群集的容器，請參閱[如何停止監視 Azure 紅帽 OpenShift 群集](container-insights-optout-openshift.md)。
