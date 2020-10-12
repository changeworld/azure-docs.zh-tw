---
title: 如何管理適用於容器的 Azure 監視器代理程式 | Microsoft Docs
description: 本文說明如何利用適用於容器的 Azure 監視器所使用的容器化 Log Analytics 代理程式來管理最常見的維護工作。
ms.topic: conceptual
ms.date: 07/21/2020
ms.openlocfilehash: b656b0cc89e40dd732def4ebf56dceae69a033b0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91618432"
---
# <a name="how-to-manage-the-azure-monitor-for-containers-agent"></a>如何管理適用於容器的 Azure 監視器代理程式

適用於容器的 Azure 監視器會使用適用於 Linux 的 Log Analytics 代理程式容器化版本。 初始部署之後，您可能需要在其生命週期期間執行一些例行性或選擇性工作。 本文將詳細說明如何手動升級代理程式，並停用從特定容器收集環境變數。 

## <a name="how-to-upgrade-the-azure-monitor-for-containers-agent"></a>如何升級適用於容器的 Azure 監視器代理程式

適用於容器的 Azure 監視器會使用適用於 Linux 的 Log Analytics 代理程式容器化版本。 發行新版的代理程式後，代理程式會在您裝載於 Azure Kubernetes Service (AKS) 和 Azure Red Hat OpenShift 3.x 版的受控 Kubernetes 叢集上自動升級。 針對[混合式 Kubernetes 叢集](container-insights-hybrid-setup.md)和 Azure Red Hat OpenShift 4.x 版，代理程式不受管理，您必須手動升級代理程式。

如果代理程式針對 AKS 或 Azure Red Hat OpenShift 3.x 版上裝載的叢集升級失敗，本文也會說明手動升級代理程式的程序。 若要遵循所發行的版本，請參閱[代理程式發行公告](https://github.com/microsoft/docker-provider/tree/ci_feature_prod) (英文)。

### <a name="upgrade-agent-on-aks-cluster"></a>在 AKS 叢集上升級代理程式

在 AKS 叢集上升級代理程式的程序是由兩個直接簡單的步驟所組成。 第一個步驟是使用 Azure CLI 停用適用於容器的 Azure 監視器所進行的監視。 請依照[停用監視](container-insights-optout.md?#azure-cli)一文中說明的步驟操作。 我們可以使用 Azure CLI 從叢集中的節點移除代理程式，而不會影響解決方案和儲存在工作區中的對應資料。 

>[!NOTE]
>當您執行這項維護活動時，叢集中的節點將不會轉送收集的資料，且在您移除代理程式到安裝新版本的這段時間內，效能檢視不會顯示資料。 
>

若要安裝新版的代理程式，請依照[使用 Azure CLI 啟用監視](container-insights-enable-new-cluster.md#enable-using-azure-cli)一文中說明的步驟，使用 Azure CLI 完成此程序。  

啟用監視之後，可能需要約 15 分鐘的時間才能檢視叢集的更新健康情況計量。 若要確認代理程式已順利升級，您可以執行下列其中一項：

* 執行命令： `kubectl get pod <omsagent-pod-name> -n kube-system -o=jsonpath='{.spec.containers[0].image}'` 。 在傳回的狀態中，記下輸出的*容器*區段中 omsagent 的 [**影像**] 底下的值。
* 在 [ **節點** ] 索引標籤上，選取叢集節點，並在右邊的 [ **屬性** ] 窗格中，記下 [ **代理程式映射**標籤] 下的值。

所顯示的代理程式版本應該符合 [發行記錄](https://github.com/microsoft/docker-provider/tree/ci_feature_prod) 頁面上所列的最新版本。

### <a name="upgrade-agent-on-hybrid-kubernetes-cluster"></a>在混合式 Kubernetes 叢集上升級代理程式

請執行下列步驟，在 Kubernetes 叢集上升級代理程式，而該叢集執行於：

* 使用 AKS 引擎裝載於 Azure 的自我管理 Kubernetes 叢集。
* 使用 AKS 引擎裝載於 Azure Stack 或內部部署環境的自我管理 Kubernetes 叢集。
* Red Hat OpenShift 4.x 版。

如果 Log Analytics 工作區位於商用 Azure，請執行下列命令：

```console
$ helm upgrade --name myrelease-1 \
--set omsagent.secret.wsid=<your_workspace_id>,omsagent.secret.key=<your_workspace_key>,omsagent.env.clusterName=<my_prod_cluster> incubator/azuremonitor-containers
```

如果 Log Analytics 工作區位於 Azure 中國的世紀，請執行下列命令：

```console
$ helm upgrade --name myrelease-1 \
--set omsagent.domain=opinsights.azure.cn,omsagent.secret.wsid=<your_workspace_id>,omsagent.secret.key=<your_workspace_key>,omsagent.env.clusterName=<your_cluster_name> incubator/azuremonitor-containers
```

如果 Log Analytics 工作區位於 Azure 美國政府，請執行下列命令：

```console
$ helm upgrade --name myrelease-1 \
--set omsagent.domain=opinsights.azure.us,omsagent.secret.wsid=<your_workspace_id>,omsagent.secret.key=<your_workspace_key>,omsagent.env.clusterName=<your_cluster_name> incubator/azuremonitor-containers
```

### <a name="upgrade-agent-on-azure-red-hat-openshift-v4"></a>在 Azure Red Hat OpenShift v4 上升級代理程式

請執行下列步驟，在 Azure Red Hat OpenShift 4.x 版上執行的 Kubernetes 叢集上升級代理程式。 

>[!NOTE]
>Azure Red Hat OpenShift 4.x 版僅支援在 Azure 商業雲端執行。
>

```console
curl -o upgrade-monitoring.sh -L https://aka.ms/upgrade-monitoring-bash-script
export azureAroV4ClusterResourceId="/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.RedHatOpenShift/OpenShiftClusters/<clusterName>"
bash upgrade-monitoring.sh --resource-id $ azureAroV4ClusterResourceId
```

如需使用服務主體搭配此命令的詳細資訊，請參閱 **使用服務主體** 在 [啟用 Azure Arc 啟用 Kubernetes](container-insights-enable-arc-enabled-clusters.md#enable-using-bash-script) 叢集的監視。

### <a name="upgrade-agent-on-azure-arc-enabled-kubernetes"></a>Azure Arc 啟用 Kubernetes 上的升級代理程式

執行下列命令，在已啟用 Azure Arc 的 Kubernetes 叢集上升級代理程式。

```console
curl -o upgrade-monitoring.sh -L https://aka.ms/upgrade-monitoring-bash-script
export azureArcClusterResourceId="/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Kubernetes/connectedClusters/<clusterName>"
bash upgrade-monitoring.sh --resource-id $azureArcClusterResourceId
```

如需使用服務主體搭配此命令的詳細資訊，請參閱 **使用服務主體** 在 [啟用 Azure Arc 啟用 Kubernetes](container-insights-enable-arc-enabled-clusters.md#enable-using-bash-script) 叢集的監視。


## <a name="how-to-disable-environment-variable-collection-on-a-container"></a>如何在容器上停用收集環境變數

適用於容器的 Azure 監視器會從在 Pod 中執行的容器收集環境變數，並在 [容器] 檢視內所選取容器的屬性窗格中顯示它們。 您可以藉由在 Kubernetes 叢集部署期間，或在設定環境變數 *AZMON_COLLECT_ENV* 之後，停用對特定容器進行收集。 此功能可從代理程式版本 (ciprod11292018 和更新版本) 中取得。  

若要在新的或現有容器上停用收集環境變數，請在您的 Kubernetes 部署 yaml 設定檔中，將變數 **AZMON_COLLECT_ENV** 的值設定為 **False**。 

```yaml
- name: AZMON_COLLECT_ENV  
  value: "False"  
```

執行下列命令，將變更套用至 Azure Red Hat OpenShift 以外的 Kubernetes 叢集：`kubectl apply -f  <path to yaml file>`。 若要編輯 ConfigMap 並將此變更套用到 Azure Red Hat OpenShift 叢集，請執行以下命令：

```bash
oc edit configmaps container-azm-ms-agentconfig -n openshift-azure-logging
```

這會開啟您的預設文字編輯器。 設定變數之後，請在編輯器中儲存檔案。

若要確認設定變更已生效，在適用於容器的 Azure 監視器中選取 [容器] 檢視中的容器，並在 [屬性] 面板中展開 [環境變數]。  此區段應該只會顯示稍早建立的變數 **AZMON_COLLECT_ENV=FALSE**。 對於所有其他容器，[環境變數] 區段應該會列出探索到的所有環境變數。

若要重新啟用環境變數的探索，請套用稍早的相同程序，並將值從 **False** 變更為 **True**，然後重新執行 `kubectl` 命令來更新容器。  

```yaml
- name: AZMON_COLLECT_ENV  
  value: "True"  
```  

## <a name="next-steps"></a>後續步驟

如果您在升級代理程式時遇到問題，請檢閱[疑難排解指南](container-insights-troubleshoot.md)以取得支援。
