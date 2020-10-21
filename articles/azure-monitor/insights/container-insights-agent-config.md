---
title: 設定容器代理程式資料集合的 Azure 監視器 |Microsoft Docs
description: 本文說明如何設定容器代理程式的 Azure 監視器，以控制 stdout/stderr 和環境變數記錄收集。
ms.topic: conceptual
ms.date: 10/09/2020
ms.openlocfilehash: 1644e541ee873a5bb058dd9bde2b82a907a400ff
ms.sourcegitcommit: ce8eecb3e966c08ae368fafb69eaeb00e76da57e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/21/2020
ms.locfileid: "92320402"
---
# <a name="configure-agent-data-collection-for-azure-monitor-for-containers"></a>為容器的 Azure 監視器設定代理程式資料收集

適用于容器的 Azure 監視器會從容器化代理程式部署到受控 Kubernetes 叢集的容器工作負載，收集 stdout、stderr 和環境變數。 您可以藉由建立自訂 Kubernetes ConfigMaps 來控制此體驗，來設定代理程式資料收集設定。 

本文將示範如何根據您的需求建立 ConfigMap 和設定資料收集。

>[!NOTE]
>針對 Azure Red Hat OpenShift，會在 *OpenShift-Azure 記錄* 命名空間中建立範本 ConfigMap 檔案。 
>

## <a name="configmap-file-settings-overview"></a>ConfigMap 檔案設定總覽

提供範本 ConfigMap 檔，可讓您輕鬆地使用自訂進行編輯，而不需要從頭建立。 開始之前，您應該先複習有關 [ConfigMaps](https://kubernetes.io/docs/tasks/configure-pod-container/configure-pod-configmap/) 的 Kubernetes 檔，並熟悉如何建立、設定和部署 ConfigMaps。 這可讓您篩選每個命名空間或整個叢集的 stderr 和 stdout，以及在叢集中所有 pod/節點上執行之任何容器的環境變數。

>[!IMPORTANT]
>從容器工作負載收集 stdout、stderr 和環境變數所支援的最低代理程式版本是 ciprod06142019 或更新版本。 若要驗證您的代理程式版本，請在 [ **節點** ] 索引標籤中選取節點，然後在 [ **代理程式映射** 標籤] 屬性的 [屬性] 窗格附注值。 如需代理程式版本及每個版本中所含內容的詳細資訊，請參閱 [代理程式](https://github.com/microsoft/Docker-Provider/tree/ci_feature_prod)版本資訊。

### <a name="data-collection-settings"></a>資料收集設定

下表描述您可以設定來控制資料收集的設定：

| Key | 資料類型 | 值 | 描述 |
|--|--|--|--|
| `schema-version` | 字串 (區分大小寫)  | v1 | 這是代理程式所使用的架構版本<br> 剖析這個 ConfigMap 時。<br> 目前支援的架構版本為 v1。<br> 不支援修改此值，而且將會<br> 在評估 ConfigMap 時拒絕。 |
| `config-version` | String |  | 支援在您的原始檔控制系統/存放庫中追蹤此設定檔版本的功能。<br> 允許的字元數上限為10，而所有其他字元則會被截斷。 |
| `[log_collection_settings.stdout] enabled =` | Boolean | true 或 false | 這會控制是否啟用 stdout 容器記錄收集。 當設定為時 `true` ，不會針對 stdout 記錄檔收集排除任何命名空間<br>  (`log_collection_settings.stdout.exclude_namespaces` 設定如下) ，將會從叢集中所有 pod/節點的所有容器中收集 stdout 記錄檔。 如果未在 ConfigMaps 中指定，<br> 預設值為 `enabled = true` 。 |
| `[log_collection_settings.stdout] exclude_namespaces =` | String | 逗點分隔陣列 | 將不會收集 stdout 記錄檔的 Kubernetes 命名空間陣列。 只有在下列情況下，此設定才有效<br> `log_collection_settings.stdout.enabled`<br> 設定為 `true`。<br> 如果未在 ConfigMap 中指定，則預設值為<br> `exclude_namespaces = ["kube-system"]`. |
| `[log_collection_settings.stderr] enabled =` | Boolean | true 或 false | 這會控制是否啟用 stderr 容器記錄收集。<br> 當設定為時 `true` ，不會針對 stdout 記錄檔收集排除任何命名空間<br>  (`log_collection_settings.stderr.exclude_namespaces` 設定) ，會從叢集中所有 pod/節點的所有容器收集 stderr 記錄檔。<br> 如果未在 ConfigMaps 中指定，則預設值為<br> `enabled = true`. |
| `[log_collection_settings.stderr] exclude_namespaces =` | String | 逗點分隔陣列 | 將不會收集 stderr 記錄的 Kubernetes 命名空間陣列。<br> 只有在下列情況下，此設定才有效<br> `log_collection_settings.stdout.enabled` 設定為 `true`。<br> 如果未在 ConfigMap 中指定，則預設值為<br> `exclude_namespaces = ["kube-system"]`. |
| `[log_collection_settings.env_var] enabled =` | Boolean | true 或 false | 此設定會控制環境變數集合<br> 在叢集中的所有 pod/節點<br> `enabled = true`如果未指定，則預設為<br> 在 ConfigMaps 中。<br> 如果環境變數的集合是全域啟用的，您可以針對特定容器停用它<br> 藉由設定環境變數<br> `AZMON_COLLECT_ENV`若為 False，則會使用 Dockerfile 設定，或在**env：** 區段下[的 Pod 設定檔](https://kubernetes.io/docs/tasks/inject-data-application/define-environment-variable-container/)中設定為**False** 。<br> 如果環境變數的集合已全域停用，則您無法啟用特定容器的集合 (也就是說，可在容器層級套用的唯一覆寫是在全域啟用時停用集合。 ) 。 |
| `[log_collection_settings.enrich_container_logs] enabled =` | Boolean | true 或 false | 此設定會控制容器記錄擴充，以填入 Name 和 Image 屬性值<br> 針對針對叢集中的所有容器記錄，寫入 ContainerLog 資料表的每個記錄檔記錄。<br> `enabled = false`如果未在 ConfigMap 中指定，它會預設為。 |
| `[log_collection_settings.collect_all_kube_events]` | Boolean | true 或 false | 這項設定允許收集所有類型的 Kube 事件。<br> 依預設，不會收集類型為 *Normal* 的 Kube 事件。 當這項設定設定為時 `true` ，就不會再篩選 *一般* 事件，並且會收集所有事件。<br> 根據預設，這項設定為 `false`。 |

### <a name="metric-collection-settings"></a>度量收集設定

下表說明您可以設定以控制計量集合的設定：

| Key | 資料類型 | 值 | 描述 |
|--|--|--|--|
| `[metric_collection_settings.collect_kube_system_pv_metrics] enabled =` | Boolean | true 或 false | 這項設定可讓您在 kube 系統命名空間中收集永久性磁片區 (PV) 使用計量。 根據預設，不會收集 kube 系統命名空間中持續性磁片區宣告的持續性磁片區的使用計量。 當這項設定設定為時 `true` ，就會收集所有命名空間的 PV 使用計量。 根據預設，這項設定為 `false`。 |

ConfigMaps 是全域清單，而且只能有一個 ConfigMap 套用至代理程式。 您不能有其他 ConfigMaps overruling 集合。

## <a name="configure-and-deploy-configmaps"></a>設定和部署 ConfigMaps

請執行下列步驟來設定您的 ConfigMap 設定檔，並將其部署至您的叢集。

1. 下載 [範本 CONFIGMAP YAML](https://github.com/microsoft/Docker-Provider/blob/ci_prod/kubernetes/container-azm-ms-agentconfig.yaml) 檔案，並將它儲存為容器-container.azm.ms-ms-agentconfig. YAML。 

   > [!NOTE]
   > 使用 Azure Red Hat OpenShift 時，不需要執行此步驟，因為 ConfigMap 範本已經存在於叢集上。

2. 使用您的自訂編輯 ConfigMap yaml 檔案，以收集 stdout、stderr 及/或環境變數。 如果您正在編輯 Azure Red Hat OpenShift 的 ConfigMap yaml 檔案，請先執行命令 `oc edit configmaps container-azm-ms-agentconfig -n openshift-azure-logging` 以在文字編輯器中開啟檔案。

    - 若要排除 stdout 記錄檔收集的特定命名空間，請使用下列範例來設定索引鍵/值： `[log_collection_settings.stdout] enabled = true exclude_namespaces = ["my-namespace-1", "my-namespace-2"]` 。
    
    - 若要停用特定容器的環境變數集合，請將索引鍵/值設定 `[log_collection_settings.env_var] enabled = true` 為全域啟用變數集合，然後遵循 [此處](container-insights-manage-agent.md#how-to-disable-environment-variable-collection-on-a-container) 的步驟來完成特定容器的設定。
    
    - 若要停用 stderr 記錄檔收集整個叢集，請使用下列範例來設定索引鍵/值： `[log_collection_settings.stderr] enabled = false` 。

3. 針對 Azure Red Hat OpenShift 以外的叢集，執行下列 kubectl 命令以建立 ConfigMap： `kubectl apply -f <configmap_yaml_file.yaml>` 在 Azure Red Hat OpenShift 以外的叢集上執行。 
    
    範例： `kubectl apply -f container-azm-ms-agentconfig.yaml`. 

    針對 Azure Red Hat OpenShift，將您的變更儲存在編輯器中。

設定變更可能需要幾分鐘的時間才會生效，且叢集中的所有 omsagent pod 都會重新開機。 重新開機是所有 omsagent pod 的輪流重新開機，不會同時重新開機。 當重新開機完成時，會顯示類似下列的訊息，並包含結果： `configmap "container-azm-ms-agentconfig" created` 。

## <a name="verify-configuration"></a>驗證組態

若要確認已成功將設定套用至 Azure Red Hat OpenShift 以外的叢集，請使用下列命令來檢查代理程式 pod 的記錄： `kubectl logs omsagent-fdf58 -n kube-system` 。 如果有來自 omsagent pod 的設定錯誤，輸出會顯示類似下列的錯誤：

``` 
***************Start Config Processing******************** 
config::unsupported/missing config schema version - 'v21' , using defaults
```

套用設定變更的相關錯誤也可供審查。 下列選項可用於執行設定變更的額外疑難排解：

- 從代理程式 pod 記錄檔使用相同的 `kubectl logs` 命令。 

    >[!NOTE]
    >此命令不適用於 Azure Red Hat OpenShift 叢集。
    > 

- 從即時記錄。 即時記錄顯示類似下列的錯誤：

    ```
    config::error::Exception while parsing config map for log collection/env variable settings: \nparse error on value \"$\" ($end), using defaults, please check config map for errors
    ```

- 從 Log Analytics 工作區中的 **KubeMonAgentEvents** 資料表。 資料會每小時傳送一次，併發生設定錯誤的 *錯誤* 嚴重性。 如果沒有任何錯誤，資料表中的專案將會有嚴重性 *資訊*的資料，而不會報告任何錯誤。 [標籤] 屬性包含發生錯誤之 pod 和容器識別碼的詳細資訊，以及最後一次發生的第一次出現、最後一次發生和 **計數的詳細** 資訊。

- 使用 Azure Red Hat OpenShift，藉由搜尋 **ContainerLog** 資料表來檢查 omsagent 記錄，以確認 OpenShift 的記錄收集是否已啟用。

當您在 Azure Red Hat OpenShift 以外的叢集上更正 ConfigMap 中的錯誤 () s 之後，請儲存 yaml 檔案，並執行下列命令以套用更新的 ConfigMaps： `kubectl apply -f <configmap_yaml_file.yaml` 。 針對 Azure Red Hat OpenShift，執行下列命令來編輯並儲存更新的 ConfigMaps：

``` bash
oc edit configmaps container-azm-ms-agentconfig -n openshift-azure-logging
```

## <a name="applying-updated-configmap"></a>套用更新的 ConfigMap

如果您已在 Azure Red Hat OpenShift 以外的叢集上部署 ConfigMap，而您想要使用較新的設定來更新它，您可以編輯先前使用的 ConfigMap 檔案，然後使用與之前相同的命令進行套用 `kubectl apply -f <configmap_yaml_file.yaml` 。 針對 Azure Red Hat OpenShift，執行下列命令來編輯並儲存更新的 ConfigMaps：

``` bash
oc edit configmaps container-azm-ms-agentconfig -n openshift-azure-logging
```

設定變更可能需要幾分鐘的時間才會生效，且叢集中的所有 omsagent pod 都會重新開機。 重新開機是所有 omsagent pod 的輪流重新開機，不會同時重新開機。 當重新開機完成時，會顯示類似下列的訊息，並包含結果： `configmap "container-azm-ms-agentconfig" updated` 。

## <a name="verifying-schema-version"></a>驗證架構版本

支援的設定架構版本可作為 pod 批註， (omsagent pod 上) 的架構版本。 您可以使用下列 kubectl 命令來查看它們： `kubectl describe pod omsagent-fdf58 -n=kube-system`

輸出會顯示類似下列的批註架構版本：

```
    Name:           omsagent-fdf58
    Namespace:      kube-system
    Node:           aks-agentpool-95673144-0/10.240.0.4
    Start Time:     Mon, 10 Jun 2019 15:01:03 -0700
    Labels:         controller-revision-hash=589cc7785d
                    dsName=omsagent-ds
                    pod-template-generation=1
    Annotations:    agentVersion=1.10.0.1
                  dockerProviderVersion=5.0.0-0
                    schema-versions=v1 
```

## <a name="next-steps"></a>後續步驟

- 容器的 Azure 監視器不包含一組預先定義的警示。 請參閱 [容器的 Azure 監視器建立效能警示](./container-insights-log-alerts.md) ，以瞭解如何建立高 CPU 和記憶體使用量的建議警示，以支援您的 DevOps 或操作程式和程式。

- 啟用監視以收集 AKS 或混合式叢集的健康情況和資源使用量，以及在其上執行的工作負載，瞭解 [如何使用](container-insights-analyze.md) 容器 Azure 監視器。

- 查看 [記錄查詢範例](container-insights-log-search.md#search-logs-to-analyze-data) 以查看預先定義的查詢和範例，以評估或自訂警示、視覺化或分析您的叢集。