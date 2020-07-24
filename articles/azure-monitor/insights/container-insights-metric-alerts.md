---
title: 來自容器 Azure 監視器的計量警示 |Microsoft Docs
description: 本文會從適用于容器的 Azure 監視器（公開預覽）中，檢查建議的計量警示。
ms.topic: conceptual
ms.date: 07/21/2020
ms.openlocfilehash: c8b75b0d9b22658253c4637bd6507144575934de
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87096158"
---
# <a name="recommended-metric-alerts-preview-from-azure-monitor-for-containers"></a>來自容器 Azure 監視器的建議計量警示（預覽）

若要在發生尖峰需求並執行接近容量的系統資源問題時發出警示，您可以使用容器的 Azure 監視器，根據儲存在 Azure 監視器記錄中的效能資料來建立記錄警示。 Azure 監視器容器現在包含預先設定的計量警示規則，適用于您的 AKS 叢集（目前處於公開預覽狀態）。

本文將探討其體驗，並提供設定和管理這些警示規則的指引。

如果您不熟悉 Azure 監視器警示，請先參閱[Microsoft Azure 中的警示總覽](../platform/alerts-overview.md)，再開始進行。 若要深入瞭解計量警示，請參閱[Azure 監視器中的計量警示](../platform/alerts-metric-overview.md)。

## <a name="prerequisites"></a>必要條件

開始之前，請確認下列事項：

* 自訂計量僅適用于 Azure 區域的子集中。 支援的區域清單記載于[此處](../platform/metrics-custom-overview.md#supported-regions)。

* 若要支援計量警示及引進額外的度量，所需的最低代理程式版本為**microsoft/oms： ciprod05262020**。

    若要確認您的叢集正在執行較新版本的代理程式，您可以：

    * 執行命令： `kubectl describe <omsagent-pod-name> --namespace=kube-system` 。 在傳回的狀態中，請注意輸出之 [*容器*] 區段中 [omsagent] 下的 [**影像**] 值。 
    * 在 [**節點**] 索引標籤上，選取叢集節點，然後在右側的 [**屬性**] 窗格中，記下 [**代理程式映射標記**] 底下的值。

    所顯示的值應該是晚于**ciprod05262020**的版本。 如果您的叢集有較舊的版本，請遵循[AKS 叢集上的升級代理程式](container-insights-manage-agent.md#upgrade-agent-on-aks-cluster)步驟來取得最新版本。
    
    如需代理程式版本的相關詳細資訊，請參閱[代理程式發行歷程記錄](https://github.com/microsoft/docker-provider/tree/ci_feature_prod)。 若要確認收集計量，您可以使用 Azure 監視器計量瀏覽器，並從列出**深入**解析的**度量命名空間**進行驗證。 如果是，您可以繼續並開始設定警示。

## <a name="alert-rules-overview"></a>警示規則總覽

若要對重要事項發出警示，Azure 監視器容器包含下列 AKS 叢集的計量警示：

|名稱| 說明 |預設閾值 |
|----|-------------|------------------|
|平均容器 CPU% |計算每個容器所使用的平均 CPU。|當每個容器的平均 CPU 使用量大於95% 時。| 
|平均容器工作集記憶體% |計算每個容器所使用的平均工作集記憶體。|當每個容器的平均工作集記憶體使用量大於95% 時。 |
|平均 CPU 百分比 |計算每個節點所使用的平均 CPU。 |當平均節點 CPU 使用率大於80% 時 |
|平均磁片使用量% |計算節點的平均磁片使用量。|當節點的磁片使用量大於80% 時。 |
|平均工作集記憶體% |計算節點的平均工作集記憶體。 |當節點的平均工作集記憶體大於80% 時。 |
|正在重新開機容器計數 |計算重新開機容器的數目。 | 當容器重新開機大於0時。 |
|失敗的 Pod 計數 |計算任何處于失敗狀態的 pod。|當處於 [失敗] 狀態的 pod 數目大於0時。 |
|節點 NotReady 狀態 |計算是否有任何節點處於 NotReady 狀態。|當 NotReady 狀態的節點數目大於0時。 |
|OOM 已終止的容器 |計算 OOM 已終止容器的數目。 |當 OOM 終止的容器數目大於0時。 |
|Pod 就緒% |計算 pod 的平均就緒狀態。 |當 pod 的就緒狀態小於80% 時。|
|完成的工作計數 |計算超過六小時前完成的作業數。 |當超過6小時的過時作業數目大於0時。|

所有警示規則都有共同的屬性：

* 所有警示規則都是以度量為基礎。

* 預設會停用所有警示規則。

* 所有警示規則都會每分鐘評估一次，並在最後5分鐘的時間內回顧。

* 根據預設，警示規則並不會指派動作群組。 您可以在編輯警示規則時選取現有的動作群組或建立新的動作群組，藉此將[動作群組](../platform/action-groups.md)新增至警示。

* 您可以直接編輯警示規則來修改其閾值。 不過，在修改其閾值之前，請參閱每個警示規則中提供的指導方針。

相較于其他計量，下列以警示為基礎的計量具有獨特的行為特性：

* 只有在已完成超過六小時的作業時，才會傳送*completedJobsCount*度量。

* 只有在有容器重新開機時，才會傳送*containerRestartCount*度量。

* 只有在有 OOM 的已終止容器時，才會傳送*oomKilledContainerCount*度量。

* 當 CPU、記憶體 Rss 和記憶體工作集值超過設定的閾值（預設閾值為95%）時，就會傳送*cpuExceededPercentage*、 *memoryRssExceededPercentage*和*memoryWorkingSetExceededPercentage*計量。 這些閾值是針對對應的警示規則所指定的警示條件閾值所排除的。 也就是說，如果您想要收集這些計量並從[計量瀏覽器](../platform/metrics-getting-started.md)進行分析，建議您將閾值設定為低於警示閾值的值。 在 ConfigMaps 檔案的區段下，可以覆寫與容器資源使用量閾值的集合設定相關的設定 `[alertable_metrics_configuration_settings.container_resource_utilization_thresholds]` 。 如需設定 ConfigMap 設定檔的相關詳細資料，請參閱[設定打斷計量 ConfigMaps](#configure-alertable-metrics-in-configmaps)一節。

## <a name="metrics-collected"></a>收集的計量

除非另有指定，否則會啟用並收集下列計量：

|計量命名空間 |計量 |描述 |
|---------|----|------------|
|深入解析。容器/節點 |cpuUsageMillicores |依主機 millicore 的 CPU 使用率。|
|深入解析。容器/節點 |cpuUsagePercentage |依節點的 CPU 使用量百分比。|
|深入解析。容器/節點 |memoryRssBytes |依主機的記憶體 RSS 使用率（以位元組為單位）。|
|深入解析。容器/節點 |memoryRssPercentage |依主機的記憶體 RSS 使用量百分比。|
|深入解析。容器/節點 |memoryWorkingSetBytes |依主機的記憶體工作集使用率（以位元組為單位）。|
|深入解析。容器/節點 |memoryWorkingSetPercentage |記憶體工作集使用百分比（依主機）。|
|深入解析。容器/節點 |nodesCount |節點計數（依狀態）。|
|深入解析。容器/節點 |diskUsedPercentage |節點上由裝置使用的磁片百分比。|
|深入解析。容器/pod |podCount |依控制器、命名空間、節點和階段的 pod 計數。|
|深入解析。容器/pod |completedJobsCount |已完成的工作會計算舊版的使用者可設定閾值（預設為六小時），由 controller，Kubernetes 命名空間。 |
|深入解析。容器/pod |restartingContainerCount |依控制器、Kubernetes 命名空間的容器重新開機計數。|
|深入解析。容器/pod |oomKilledContainerCount |OOMkilled 容器的計數（依控制器、Kubernetes 命名空間）。|
|深入解析。容器/pod |podReadyPercentage |由控制器、Kubernetes 命名空間之就緒狀態的 pod 百分比。|
|深入解析。容器/容器 |cpuExceededPercentage |容器名稱、控制器名稱、Kubernetes 命名空間、pod 名稱，超過使用者可設定閾值（預設值為95.0）之容器的 CPU 使用率百分比。<br> 徵收  |
|深入解析。容器/容器 |memoryRssExceededPercentage |容器名稱、控制器名稱、Kubernetes 命名空間、pod 名稱，超過使用者可設定閾值（預設值為95.0）之容器的記憶體 RSS 百分比。|
|深入解析。容器/容器 |memoryWorkingSetExceededPercentage |容器名稱、控制器名稱、Kubernetes 命名空間、pod 名稱，超過使用者可設定閾值（預設值為95.0）之容器的記憶體工作集百分比。|

## <a name="enable-alert-rules"></a>啟用警示規則

請遵循下列步驟，從 Azure 入口網站啟用 Azure 監視器中的計量警示。 若要使用 Resource Manager 範本來啟用，請參閱[使用 Resource Manager 範本啟用](#enable-with-a-resource-manager-template)。

### <a name="from-the-azure-portal"></a>從 Azure 入口網站

本節將逐步解說如何從 Azure 入口網站啟用容器計量警示（預覽）的 Azure 監視器。

1. 登入 [Azure 入口網站](https://portal.azure.com/)。

2. 從 Azure 入口網站的左窗格中選取 [**見解**]，即可直接從 AKS 叢集存取容器的 Azure 監視器計量警示（預覽）功能。

3. 從命令列中，選取 [**建議的警示**]。

    ![容器 Azure 監視器中的建議警示選項](./media/container-insights-metric-alerts/command-bar-recommended-alerts.png)

4. [**建議的警示**] 屬性窗格會自動顯示在頁面的右側。 根據預設，清單中的所有警示規則都會停用。 選取 [**啟用**] 之後，就會建立警示規則，並更新規則名稱以包含警示資源的連結。

    ![建議的警示屬性窗格](./media/container-insights-metric-alerts/recommended-alerts-pane.png)

    選取 [**啟用/停**用] 開關以啟用警示之後，會建立警示規則，而規則名稱會更新以包含實際警示資源的連結。

    ![啟用警示規則](./media/container-insights-metric-alerts/recommended-alerts-pane-enable.png)

5. 警示規則不會與[動作群組](../platform/action-groups.md)相關聯，以通知使用者已觸發警示。 選取 [**未指派動作群組**]，然後在 [**動作群組**] 頁面上選取 [**新增**] 或 [**建立**]，以指定現有或建立動作群組。

    ![選取動作群組](./media/container-insights-metric-alerts/select-action-group.png)

### <a name="enable-with-a-resource-manager-template"></a>使用 Resource Manager 範本啟用

您可以使用 Azure Resource Manager 範本和參數檔案，在 Azure 監視器中建立包含的計量警示。

基本步驟如下：

1. 下載一或所有可用的範本，其中說明如何從[GitHub](https://github.com/microsoft/Docker-Provider/tree/ci_dev/alerts/recommended_alerts_ARM)建立警示。

2. 建立並使用[參數](../../azure-resource-manager/templates/parameter-files.md)檔案做為 JSON，以設定建立警示規則所需的值。

3. 從 Azure 入口網站、PowerShell 或 Azure CLI 部署範本。

#### <a name="deploy-through-azure-portal"></a>透過 Azure 入口網站部署

1. 下載並儲存到本機資料夾（Azure Resource Manager 範本和參數檔案），以使用下列命令建立警示規則：

2. 若要透過入口網站部署自訂範本，請從 [ [Azure 入口網站](https://portal.azure.com)中選取 [**建立資源**]。

3. 搜尋 [**範本**]，然後選取 [**範本**]。 部署.

4. 選取 [建立]。

5. 您會看到數個建立範本的選項，請選取 [**在編輯器中建立您自己的範本**]。

6. 在 [**編輯範本] 頁面**上，選取 [**載入**檔案]，然後選取範本檔案。

7. 在 [**編輯範本**] 頁面上，選取 [**儲存**]。

8. 在 [**自訂部署**] 頁面上，指定下列各項，然後在完成時選取 [**購買**] 以部署範本並建立警示規則。

    * 資源群組
    * Location
    * 警示名稱
    * 叢集資源識別碼

#### <a name="deploy-with-azure-powershell-or-cli"></a>使用 Azure PowerShell 或 CLI 部署

1. 下載並儲存到本機資料夾（Azure Resource Manager 範本和參數檔案），以使用下列命令建立警示規則：

2. 您可以透過 PowerShell 或 Azure CLI 使用範本和參數檔案來建立計量警示。

    使用 Azure PowerShell

    ```powershell
    Connect-AzAccount

    Select-AzSubscription -SubscriptionName <yourSubscriptionName>
    New-AzResourceGroupDeployment -Name CIMetricAlertDeployment -ResourceGroupName ResourceGroupofTargetResource `
    -TemplateFile templateFilename.json -TemplateParameterFile templateParameterFilename.parameters.json
    ```

    使用 Azure CLI

    ```azurecli
    az login

    az group deployment create \
    --name AlertDeployment \
    --resource-group ResourceGroupofTargetResource \
    --template-file templateFileName.json \
    --parameters @templateParameterFilename.parameters.json
    ```

    >[!NOTE]
    >雖然計量警示能夠建立在與目標資源不同的資源群組中，但仍建議使用與目標資源相同的資源群組。

## <a name="edit-alert-rules"></a>編輯警示規則

您可以查看和管理容器的 Azure 監視器警示規則，以編輯其閾值或為 AKS 叢集設定[動作群組](../platform/action-groups.md)。 雖然您可以從 Azure 入口網站和 Azure CLI 執行這些動作，但也可以直接從容器 Azure 監視器中的 AKS 叢集進行。

1. 從命令列中，選取 [**建議的警示**]。

2. 若要修改閾值，請在 [**建議的警示**] 窗格中，選取 [已啟用] 警示。 在 [**編輯規則**] 中，選取您想要編輯的**警示準則**。

    * 若要修改警示規則閾值，請選取**條件**。
    * 若要指定現有或建立動作群組，請選取 [**動作群組**] 底下的 [**新增**] 或 [**建立**]

若要查看為啟用的規則所建立的警示，請在 [**建議的警示**] 窗格中選取 [**警示**]。 系統會將您重新導向至 AKS 叢集的 [警示] 功能表，您可以在其中查看目前為叢集建立的所有警示。

## <a name="configure-alertable-metrics-in-configmaps"></a>在 ConfigMaps 中設定打斷計量

請執行下列步驟來設定您的 ConfigMap 設定檔，以覆寫預設的容器資源使用量閾值。 這些步驟僅適用于下列打斷計量。

* *cpuExceededPercentage*
* *memoryRssExceededPercentage*
* *memoryWorkingSetExceededPercentage*

1. 編輯區段下的 ConfigMap yaml 檔案 `[alertable_metrics_configuration_settings.container_resource_utilization_thresholds]` 。

2. 若要將*cpuExceededPercentage*臨界值修改為90%，並在達到或超過該臨界值時開始收集此計量，請使用下列範例來設定 ConfigMap 檔案。

    ```
    container_cpu_threshold_percentage = 90.0
    # Threshold for container memoryRss, metric will be sent only when memory rss exceeds or becomes equal to the following percentage
    container_memory_rss_threshold_percentage = 95.0
    # Threshold for container memoryWorkingSet, metric will be sent only when memory working set exceeds or becomes equal to the following percentage
    container_memory_working_set_threshold_percentage = 95.0
    ```

3. 執行下列 kubectl 命令： `kubectl apply -f <configmap_yaml_file.yaml>` 。

    範例： `kubectl apply -f container-azm-ms-agentconfig.yaml`.

設定變更可能需要幾分鐘的時間才會生效，且叢集中的所有 omsagent pod 都會重新開機。 重新開機是所有 omsagent pod 的輪流重新開機，不會同時全部重新開機。 當重新開機完成時，會顯示與下列類似的訊息，並包含結果： `configmap "container-azm-ms-agentconfig" created` 。

## <a name="next-steps"></a>後續步驟

- 查看[記錄查詢範例](container-insights-log-search.md#search-logs-to-analyze-data)，以查看預先定義的查詢和範例，以評估或自訂警示、視覺化或分析您的叢集。

- 若要深入瞭解 Azure 監視器以及如何監視 Kubernetes 叢集的其他層面，請參閱[View Kubernetes cluster performance](container-insights-analyze.md)。
