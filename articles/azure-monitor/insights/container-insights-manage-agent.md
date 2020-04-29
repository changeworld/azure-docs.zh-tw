---
title: 如何管理適用於容器的 Azure 監視器代理程式 | Microsoft Docs
description: 本文說明如何利用適用於容器的 Azure 監視器所使用的容器化 Log Analytics 代理程式來管理最常見的維護工作。
ms.topic: conceptual
ms.date: 01/24/2020
ms.openlocfilehash: 1a1f8d690979a846dbf5041999180221752acc0b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "79275317"
---
# <a name="how-to-manage-the-azure-monitor-for-containers-agent"></a>如何管理適用於容器的 Azure 監視器代理程式

適用於容器的 Azure 監視器會使用適用於 Linux 的 Log Analytics 代理程式容器化版本。 初始部署之後，您可能需要在其生命週期期間執行一些例行性或選擇性工作。 本文將詳細說明如何手動升級代理程式，並停用從特定容器收集環境變數。 

## <a name="how-to-upgrade-the-azure-monitor-for-containers-agent"></a>如何升級適用於容器的 Azure 監視器代理程式

適用於容器的 Azure 監視器會使用適用於 Linux 的 Log Analytics 代理程式容器化版本。 發行代理程式的新版本時，代理程式會在您裝載于 Azure Kubernetes Service （AKS）和 Azure Red Hat OpenShift 上的受控 Kubernetes 叢集上自動升級。 針對[混合式 Kubernetes](container-insights-hybrid-setup.md)叢集，代理程式不受管理，您必須手動升級代理程式。

如果在 AKS 上裝載的叢集代理程式升級失敗，本文也會說明手動升級代理程式的流程。 若要遵循所發行的版本，請參閱[代理程式發行公告](https://github.com/microsoft/docker-provider/tree/ci_feature_prod) (英文)。

### <a name="upgrade-agent-on-monitored-kubernetes-cluster"></a>在受監視的 Kubernetes 叢集上升級代理程式

在叢集（而非 Azure Red Hat OpenShift）上升級代理程式的套裝程式含兩個直接的正向步驟。 第一個步驟是使用 Azure CLI 停用適用於容器的 Azure 監視器所進行的監視。 請依照[停用監視](container-insights-optout.md?#azure-cli)一文中說明的步驟操作。 我們可以使用 Azure CLI 從叢集中的節點移除代理程式，而不會影響解決方案和儲存在工作區中的對應資料。 

>[!NOTE]
>當您執行這項維護活動時，叢集中的節點將不會轉送收集的資料，且在您移除代理程式到安裝新版本的這段時間內，效能檢視不會顯示資料。 
>

若要安裝新版的代理程式，請遵循[使用 Azure CLI 啟用監視](container-insights-enable-new-cluster.md#enable-using-azure-cli)中所述的步驟來完成此流程。  

重新啟用監視之後，可能需要約15分鐘的時間，才能查看叢集的更新健康情況計量。 若要確認代理程式已順利升級，請執行下列命令：`kubectl logs omsagent-484hw --namespace=kube-system`

狀態應該類似下列範例，其中 *omi* 和 *omsagent* 的值應符合[代理程式發行歷程記錄](https://github.com/microsoft/docker-provider/tree/ci_feature_prod) \(英文\) 中所指定的最新版本。  

    User@aksuser:~$ kubectl logs omsagent-484hw --namespace=kube-system
    :
    :
    instance of Container_HostInventory
    {
        [Key] InstanceID=3a4407a5-d840-4c59-b2f0-8d42e07298c2
        Computer=aks-nodepool1-39773055-0
        DockerVersion=1.13.1
        OperatingSystem=Ubuntu 16.04.3 LTS
        Volume=local
        Network=bridge host macvlan null overlay
        NodeRole=Not Orchestrated
        OrchestratorType=Kubernetes
    }
    Primary Workspace: b438b4f6-912a-46d5-9cb1-b44069212abc    Status: Onboarded(OMSAgent Running)
    omi 1.4.2.5
    omsagent 1.6.0-163
    docker-cimprov 1.0.0.31

## <a name="upgrade-agent-on-hybrid-kubernetes-cluster"></a>在混合式 Kubernetes 叢集上升級代理程式

若要在裝載內部部署的 Kubernetes 叢集上升級代理程式，請執行下列命令來完成在 Azure 上裝載 AKS 引擎和 Azure Stack 的流程：

```
$ helm upgrade --name myrelease-1 \
--set omsagent.secret.wsid=<your_workspace_id>,omsagent.secret.key=<your_workspace_key>,omsagent.env.clusterName=<my_prod_cluster> incubator/azuremonitor-containers
```

如果 Log Analytics 工作區位於 Azure 中國，請執行下列命令：

```
$ helm upgrade --name myrelease-1 \
--set omsagent.domain=opinsights.azure.cn,omsagent.secret.wsid=<your_workspace_id>,omsagent.secret.key=<your_workspace_key>,omsagent.env.clusterName=<your_cluster_name> incubator/azuremonitor-containers
```

如果 Log Analytics 工作區位於 Azure 美國政府中，請執行下列命令：

```
$ helm upgrade --name myrelease-1 \
--set omsagent.domain=opinsights.azure.us,omsagent.secret.wsid=<your_workspace_id>,omsagent.secret.key=<your_workspace_key>,omsagent.env.clusterName=<your_cluster_name> incubator/azuremonitor-containers
```

## <a name="how-to-disable-environment-variable-collection-on-a-container"></a>如何在容器上停用收集環境變數

適用於容器的 Azure 監視器會從在 Pod 中執行的容器收集環境變數，並在 [容器]**** 檢視內所選取容器的屬性窗格中顯示它們。 您可以在部署 Kubernetes 叢集期間，或在設定環境變數*AZMON_COLLECT_ENV*之後，停用特定容器的集合來控制此行為。 此功能可從代理程式版本 (ciprod11292018 和更新版本) 中取得。  

若要在新的或現有容器上停用收集環境變數，請在您的 Kubernetes 部署 yaml 設定檔中，將變數 **AZMON_COLLECT_ENV** 的值設定為 **False**。 

```  
- name: AZMON_COLLECT_ENV  
  value: "False"  
```  

執行下列命令，將變更套用至 Azure Red Hat OpenShift 以外的 Kubernetes 叢集）： `kubectl apply -f  <path to yaml file>`。 若要編輯 ConfigMap，並將此變更套用到 Azure Red Hat OpenShift 叢集，請執行下列命令：

``` bash
oc edit configmaps container-azm-ms-agentconfig -n openshift-azure-logging
```

這會開啟您的預設文字編輯器。 設定變數之後，請將檔案儲存在編輯器中。

若要確認設定變更已生效，在適用於容器的 Azure 監視器中選取 [容器]**** 檢視中的容器，並在 [屬性] 面板中展開 [環境變數]****。  此區段應該只會顯示稍早建立的變數 **AZMON_COLLECT_ENV=FALSE**。 對於所有其他容器，[環境變數] 區段應該會列出探索到的所有環境變數。

若要重新啟用環境變數的探索，請套用稍早的相同程序，並將值從 **False** 變更為 **True**，然後重新執行 `kubectl` 命令來更新容器。  

```  
- name: AZMON_COLLECT_ENV  
  value: "True"  
```  

## <a name="next-steps"></a>後續步驟

如果您在升級代理程式時遇到問題，請檢閱[疑難排解指南](container-insights-troubleshoot.md)以取得支援。
