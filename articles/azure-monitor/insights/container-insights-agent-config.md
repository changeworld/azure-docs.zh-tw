---
title: 為容器代理資料收集配置 Azure 監視器 |微軟文檔
description: 本文介紹如何為容器代理配置 Azure 監視器，以控制堆/斯特和環境變數日誌收集。
ms.topic: conceptual
ms.date: 01/13/2020
ms.openlocfilehash: 28b93190298ae61732ff7d2e297899af4ba0e5f2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75933012"
---
# <a name="configure-agent-data-collection-for-azure-monitor-for-containers"></a>為容器配置 Azure 監視器的代理資料收集

容器的 Azure 監視器從部署到託管 Kubernetes 群集的容器工作負載中收集容器工作負載中的粘結、穩重和環境變數。 您可以通過創建自訂 Kubernets ConfigMaps 來配置代理資料收集設置，以控制此體驗。 

本文演示如何創建 ConfigMap 並根據您的要求配置資料收集。

>[!NOTE]
>對於 Azure 紅帽 OpenShift，在*開放移位-azure 日誌記錄*命名空間中創建範本 ConfigMap 檔。 
>

## <a name="configmap-file-settings-overview"></a>配置映射檔設置概述

提供了一個範本 ConfigMap 檔，允許您輕鬆地使用自訂項對其進行編輯，而無需從頭開始創建該檔。 在開始之前，您應該查看有關[ConfigMaps](https://kubernetes.io/docs/tasks/configure-pod-container/configure-pod-configmap/)的 Kubernetes 文檔，並熟悉如何創建、配置和部署 ConfigMaps。 這將允許您篩選每個命名空間或整個群集的穩穩和穩點，以及跨群集中所有 Pod/節點運行的任何容器的環境變數。

>[!IMPORTANT]
>支援從容器工作負載收集抖杆、穩重和環境變數的最小代理版本是 ciprod06142019 或更高版本。 要驗證代理版本，請從 **"節點"** 選項卡中選擇一個節點，並在 **"代理圖像標記**"屬性的屬性窗格注釋值中選擇。 有關代理版本和每個版本中包含的內容的其他資訊，請參閱[代理版本資訊](https://github.com/microsoft/Docker-Provider/tree/ci_feature_prod)。

### <a name="data-collection-settings"></a>資料收集設置

以下是可配置為控制資料收集的設置。

|Key |資料類型 |值 |描述 |
|----|----------|------|------------|
|`schema-version` |字串（區分大小寫） |v1 |這是代理在分析此 ConfigMap 時使用的架構版本。 當前支援的架構版本是 v1。 不支援修改此值，在計算 ConfigMap 時將被拒絕。|
|`config-version` |String | | 支援在原始程式碼管理系統/存儲庫中跟蹤此設定檔版本的能力。 允許的最大字元為 10，所有其他字元將被截斷。 |
|`[log_collection_settings.stdout] enabled =` |Boolean | true 或 false | 如果啟用了穩大容器日誌集合，則此控制項。 當設置為`true`和 不排除用於堆定日誌收集 （`log_collection_settings.stdout.exclude_namespaces`設置在下面）， 停滯日誌將從群集中所有 Pod/節點的所有容器收集。 如果未在 ConfigMap 中指定，則預設值`enabled = true`為 。 |
|`[log_collection_settings.stdout] exclude_namespaces =`|String | 逗號分隔陣列 |不會為其收集停滯日誌的 Kubernets 命名空間的陣列。 僅當設置為 時`log_collection_settings.stdout.enabled`，此設置才有效。 `true` 如果未在 ConfigMap 中指定，則預設值`exclude_namespaces = ["kube-system"]`為 。|
|`[log_collection_settings.stderr] enabled =` |Boolean | true 或 false |如果啟用了更穩的容器日誌集合，則此控制項。 當設置為`true`和 不排除用於堆定日誌收集 （`log_collection_settings.stderr.exclude_namespaces`設置） 的命名空間時，將從群集中所有 Pod/節點的所有容器收集更穩的日誌。 如果未在 ConfigMap 中指定，則預設值`enabled = true`為 。 |
|`[log_collection_settings.stderr] exclude_namespaces =` |String |逗號分隔陣列 |不會為其收集更穩積日誌的 Kubernets 命名空間的陣列。 僅當設置為 時`log_collection_settings.stdout.enabled`，此設置才有效。 `true` 如果未在 ConfigMap 中指定，則預設值`exclude_namespaces = ["kube-system"]`為 。 |
| `[log_collection_settings.env_var] enabled =` |Boolean | true 或 false | 此設置控制群集中所有 Pod/節點的環境變數集合，`enabled = true`並在 ConfigMaps 中未指定時預設為。 如果環境變數集合是全域啟用的，則可以`AZMON_COLLECT_ENV`通過將環境變數設置為**False**與 Dockerfile 設置或**env：** 節下的 Pod[設定檔](https://kubernetes.io/docs/tasks/inject-data-application/define-environment-variable-container/)中來禁用它。 如果環境變數集合全域禁用，則無法為特定容器啟用集合（也就是說，可在容器級別應用的唯一重寫是在集合已全域啟用時禁用集合）。 |
| `[log_collection_settings.enrich_container_logs] enabled =` |Boolean | true 或 false | 此設置控制容器日誌擴充，以填充群集中所有容器日誌寫入容器日誌的每個日誌記錄的名稱和圖像屬性值。 它預設為`enabled = false`在 ConfigMap 中未指定時。 |

ConfigMap 是一個全域清單，只能向代理應用一個 ConfigMap。 您不能讓另一個 ConfigMap 覆蓋集合。

## <a name="configure-and-deploy-configmaps"></a>配置和部署配置映射

執行以下步驟以配置 ConfigMap 設定檔並將其部署到群集。

1. [下載](https://github.com/microsoft/OMS-docker/blob/ci_feature_prod/Kubernetes/container-azm-ms-agentconfig.yaml)範本 ConfigMap yaml 檔並將其另存為容器-azm-ms-代理 config.yaml。 

   >[!NOTE]
   >使用 Azure 紅帽 OpenShift 時不需要此步驟，因為群集上已存在 ConfigMap 範本。

2. 使用自訂項編輯 ConfigMap yaml 檔，以收集抖定、更粘、和/或環境變數。 如果要為 Azure 紅帽 OpenShift 編輯 ConfigMap yaml 檔，`oc edit configmaps container-azm-ms-agentconfig -n openshift-azure-logging`則首先運行命令以在文字編輯器中打開該檔。

    - 要排除用於 stdout 日誌集合的特定命名空間，請使用以下示例配置鍵/值： `[log_collection_settings.stdout] enabled = true exclude_namespaces = ["my-namespace-1", "my-namespace-2"]`。
    
    - 要禁用特定容器的環境變數集合，請設置鍵/值`[log_collection_settings.env_var] enabled = true`以全域啟用變數集合，然後按照[此處](container-insights-manage-agent.md#how-to-disable-environment-variable-collection-on-a-container)的步驟完成特定容器的配置。
    
    - 要禁用群集範圍的跟蹤日誌集合，請使用以下示例配置鍵/值： `[log_collection_settings.stderr] enabled = false`。

3. 對於 Azure 紅帽 OpenShift 以外的群集，通過運行以下 kubectl 命令創建`kubectl apply -f <configmap_yaml_file.yaml>`ConfigMap：在 Azure 紅帽 OpenShift 以外的群集上。 
    
    範例： `kubectl apply -f container-azm-ms-agentconfig.yaml`. 

    對於 Azure 紅帽 OpenShift，請將更改保存在編輯器中。

配置更改可能需要幾分鐘才能完成才能生效，群集中的所有 omsagent pod 都將重新開機。 重新開機是所有 omsagent pod 的滾動重新開機，並非所有重新開機都同時重新開機。 重新開機完成後，將顯示類似于以下內容的消息，並包含結果： `configmap "container-azm-ms-agentconfig" created`。

## <a name="verify-configuration"></a>驗證組態

要驗證配置已成功應用於 Azure 紅帽 OpenShift 以外的群集，請使用以下命令查看代理窗格中的日誌： `kubectl logs omsagent-fdf58 -n=kube-system`。 如果 omsagent pod 存在配置錯誤，則輸出將顯示類似于以下內容的錯誤：

``` 
***************Start Config Processing******************** 
config::unsupported/missing config schema version - 'v21' , using defaults
```

與應用配置更改相關的錯誤也可供查看。 以下選項可用於對配置更改執行其他故障排除：

- 使用同`kubectl logs`一命令從代理 pod 日誌。 

    >[!NOTE]
    >此命令不適用於 Azure 紅帽 OpenShift 群集。
    > 

- 從即時日誌。 即時日誌顯示的錯誤類似于以下內容：

    ```
    config::error::Exception while parsing config map for log collection/env variable settings: \nparse error on value \"$\" ($end), using defaults, please check config map for errors
    ```

- 從日誌分析工作區中的**KubeMonAgent 事件**表。 每小時發送一次資料，配置錯誤時具有*錯誤*嚴重性。 如果沒有錯誤，表中的條目將具有嚴重性*資訊*的資料，該資料不報告任何錯誤。 **"標記"** 屬性包含有關發生錯誤的 Pod 和容器 ID 的詳細資訊，以及發生錯誤的第一次發生、最後一次發生和計數的資訊。

- 使用 Azure 紅帽 OpenShift，通過搜索**容器日誌**表來檢查 omsagent 日誌，以驗證是否啟用了打開 Shift-azure 日誌記錄的日誌集合。

在 Azure 紅帽 OpenShift 以外的群集上更正 ConfigMap 中的錯誤後，請保存 yaml 檔，並通過運行命令應用於更新的 ConfigMap。 `kubectl apply -f <configmap_yaml_file.yaml` 對於 Azure 紅帽 OpenShift，通過運行命令編輯並保存更新的 ConfigMaps：

``` bash
oc edit configmaps container-azm-ms-agentconfig -n openshift-azure-logging
```

## <a name="applying-updated-configmap"></a>應用更新的 ConfigMap

如果已在 Azure 紅帽 OpenShift 以外的群集上部署了 ConfigMap，並且希望使用較新的配置更新它，則可以編輯以前使用過的 ConfigMap 檔，`kubectl apply -f <configmap_yaml_file.yaml`然後使用與以前相同的命令應用。 對於 Azure 紅帽 OpenShift，通過運行命令編輯並保存更新的 ConfigMaps：

``` bash
oc edit configmaps container-azm-ms-agentconfig -n openshift-azure-logging
```

配置更改可能需要幾分鐘才能完成才能生效，群集中的所有 omsagent pod 都將重新開機。 重新開機是所有 omsagent pod 的滾動重新開機，並非所有重新開機都同時重新開機。 重新開機完成後，將顯示類似于以下內容的消息，並包含結果： `configmap "container-azm-ms-agentconfig" updated`。

## <a name="verifying-schema-version"></a>驗證架構版本

支援的配置架構版本在 omsagent 窗格中作為窗格注釋（架構版本）提供。 您可以使用以下 kubectl 命令查看它們：`kubectl describe pod omsagent-fdf58 -n=kube-system`

輸出將顯示類似于注釋架構版本的以下內容：

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

- 容器的 Azure 監視器不包括預定義的警報集。 查看[使用 Azure 監視器為容器創建性能警報](container-insights-alerts.md)，瞭解如何為高 CPU 和記憶體利用率創建建議警報以支援 DevOps 或操作過程。

- 啟用監視以收集 AKS 或混合群集的運行狀況和資源利用率以及運行在其中的工作負載，瞭解如何將 Azure 監視器[用於](container-insights-analyze.md)容器。

- 查看[日誌查詢示例](container-insights-log-search.md#search-logs-to-analyze-data)以查看預定義的查詢和示例，以評估或自訂以用於警報、視覺化或分析群集。
