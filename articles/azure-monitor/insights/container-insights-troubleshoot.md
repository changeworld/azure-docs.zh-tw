---
title: 如何對適用於容器的 Azure 監視器進行疑難排解 | Microsoft Docs
description: 本文說明如何對適用於容器的 Azure 監視器問題進行疑難排解並解決問題。
ms.topic: conceptual
ms.date: 10/15/2019
ms.openlocfilehash: 17a2817b320599b2aa2c331c354d316b9d864a32
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75403371"
---
# <a name="troubleshooting-azure-monitor-for-containers"></a>對適用於容器的 Azure 監視器進行疑難排解

當您設定使用適用於容器的 Azure 監視器來監視 Azure Kubernetes Service (AKS) 叢集時，您可能會遇到阻止資料收集或報告狀態的問題。 本文將詳細說明一些常見問題與疑難排解步驟。

## <a name="authorization-error-during-onboarding-or-update-operation"></a>載入或更新操作期間的授權錯誤
在為容器啟用 Azure 監視器或更新群集以支援收集指標時，您可能會收到類似于以下內容的錯誤 -*用戶端<使用者的身份>"，物件 id"<使用者的物件 Id id>"無權在範圍範圍內執行操作"Microsoft.授權/角色指派/寫入"*

在入職或更新過程中，嘗試在群集資源上授予**監視指標發行者**角色指派。 啟動支援容器的 Azure 監視器或支援指標集合的更新的使用者必須有權訪問**Microsoft。** 只有**擁有者**和**使用者訪問管理員**內置角色的成員才能獲得對此許可權的存取權限。 如果您的安全性原則需要分配細微性級別許可權，我們建議您查看[自訂角色](../../role-based-access-control/custom-roles.md)並將其分配給需要該角色的使用者。 

還可以通過執行以下步驟，從 Azure 門戶手動授予此角色：

1. 登錄到 Azure[門戶](https://portal.azure.com)。 
2. 在 Azure 入口網站中，按一下左上角的 [所有服務]****。 在資源清單中，鍵入**庫伯內斯**。 當您開始輸入時，清單會根據您輸入的文字進行篩選。 選擇**Azure 庫伯奈斯**。
3. 在 Kubernetes 群集清單中，從清單中選擇一個。
2. 在左側功能表中，按一下**存取控制 （IAM）。**
3. 選擇 **"添加 "** 以添加角色指派並選擇 **"監視指標發行者"** 角色，並在 **"選擇**"框類型**AKS**下篩選訂閱中定義的叢集服務主體上的結果。 從清單中從特定于該群集的清單中選擇該群集。
4. 選取 [儲存]**** 以完成角色指派。 

## <a name="azure-monitor-for-containers-is-enabled-but-not-reporting-any-information"></a>適用於容器的 Azure 監視器已啟用但未報告任何資訊
如果成功啟用並配置了容器的 Azure 監視器，但無法查看狀態資訊或日誌查詢未返回任何結果，請按照以下步驟診斷問題： 

1. 執行下列命令來檢查代理程式的狀態： 

    `kubectl get ds omsagent --namespace=kube-system`

    輸出應該像下面這樣，這表示它已正確部署：

    ```
    User@aksuser:~$ kubectl get ds omsagent --namespace=kube-system 
    NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR                 AGE
    omsagent   2         2         2         2            2           beta.kubernetes.io/os=linux   1d
    ```  
2. 使用下列命令，利用代理程式版本 *06072018* 或更新版本來檢查部署狀態：

    `kubectl get deployment omsagent-rs -n=kube-system`

    輸出應該會像下列範例，這表示它已正確部署：

    ```
    User@aksuser:~$ kubectl get deployment omsagent-rs -n=kube-system 
    NAME       DESIRED   CURRENT   UP-TO-DATE   AVAILABLE    AGE
    omsagent   1         1         1            1            3h
    ```

3. 執行下列命令來檢查 Pod 的狀態，以確認其正在執行：`kubectl get pods --namespace=kube-system`

    輸出應該會像下列範例，且 omsagent 的狀態為「執行中」**：

    ```
    User@aksuser:~$ kubectl get pods --namespace=kube-system 
    NAME                                READY     STATUS    RESTARTS   AGE 
    aks-ssh-139866255-5n7k5             1/1       Running   0          8d 
    azure-vote-back-4149398501-7skz0    1/1       Running   0          22d 
    azure-vote-front-3826909965-30n62   1/1       Running   0          22d 
    omsagent-484hw                      1/1       Running   0          1d 
    omsagent-fkq7g                      1/1       Running   0          1d 
    ```

4. 檢查代理程式記錄。 容器化的代理程式完成部署時，它會執行 OMI 命令並顯示代理程式與提供者版本，以執行快速檢查。 

5. 要驗證代理是否已成功部署，請運行以下命令：`kubectl logs omsagent-484hw --namespace=kube-system`

    狀態應該會像下列範例：

    ```
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
    omi 1.4.2.2
    omsagent 1.6.0.23
    docker-cimprov 1.0.0.31
    ```

## <a name="error-messages"></a>錯誤訊息

下表簡要說明使用適用於容器的 Azure 監視器時，可能遇到的已知錯誤。

| 錯誤訊息  | 動作 |  
| ---- | --- |  
| 錯誤訊息 `No data for selected filters`  | 為新建立的叢集打造監視資料流程可能需要點時間。 至少 10 到 15 分鐘才能為群集顯示資料。 |   
| 錯誤訊息 `Error retrieving data` | 當 Azure Kubernetes 服務群集設置為運行狀況和效能監控時，群集和 Azure 日誌分析工作區之間將建立連接。 Log Analytics 工作區是用來儲存叢集的所有監視資料。 刪除日誌分析工作區時，可能會發生此錯誤。 檢查工作區是否已被刪除，如果是，則需要使用 Azure 監視器重新啟用容器的群集監視，並指定現有工作區或創建新工作區。 要重新啟用，您需要[禁用](container-insights-optout.md)群集的監視，並再次[為容器啟用](container-insights-enable-new-cluster.md)Azure 監視器。 |  
| 透過 az aks cli 新增適用於容器的 Azure 監視器後，會出現 `Error retrieving data` | 使用 啟用監視`az aks cli`時，可能無法正確部署容器的 Azure 監視器。 檢查解決方案是否已部署。 若要檢查，請前往 Log Analytics 工作區，選取左側窗格的 [Solutions (解決方案)]****，查看解決方案是否可使用。 若要解決此問題，您必須按照[如何部署適用於容器的 Azure 監視器](container-insights-onboard.md)的指示，重新部署這個解決方案 |  

為協助診斷問題，[在此](https://github.com/Microsoft/OMS-docker/tree/ci_feature_prod/Troubleshoot#troubleshooting-script)提供疑難排解指令碼。

## <a name="azure-monitor-for-containers-agent-replicaset-pods-are-not-scheduled-on-non-azure-kubernetes-cluster"></a>容器代理複本集 Pod 的 Azure 監視器未安排在非 Azure 庫伯奈斯群集上

容器代理 ReplicaSet Pods 的 Azure 監視器依賴于計畫工作（或代理）節點上的以下節點選擇器：

```
nodeSelector:
  beta.kubernetes.io/os: Linux
  kubernetes.io/role: agent
```

如果輔助節點未附加節點標籤，則代理複本集 Pod 將不會計畫。 有關如何附加標籤的說明，請參閱[Kubernets 分配標籤選擇器](https://kubernetes.io/docs/concepts/configuration/assign-pod-node/)。

## <a name="performance-charts-dont-show-cpu-or-memory-of-nodes-and-containers-on-a-non-azure-cluster"></a>性能圖表不顯示非 Azure 群集上的 CPU 或節點和容器的記憶體

容器代理 Pods 的 Azure 監視器使用節點代理上的 cAdvisor 終結點來收集性能指標。 驗證節點上的容器代理配置為允許在`cAdvisor port: 10255`群集中的所有節點上打開以收集性能指標。

## <a name="non-azure-kubernetes-cluster-are-not-showing-in-azure-monitor-for-containers"></a>容器的 Azure 監視器中未顯示非 Azure 庫伯奈斯群集

要查看容器 Azure 監視器中的非 Azure Kubernetes 群集，需要在支援此 Insight 的日誌分析工作區和容器**見解解決方案資源容器見解 （*工作區*）** 上訪問。

## <a name="next-steps"></a>後續步驟

藉由啟用監視來擷取 AKS 叢集節點和 Pod 的健康情況計量，這些健康情況計量都可在 Azure 入口網站中取得。 若要了解如何使用適用於容器的 Azure 監視器，請參閱[檢視 Azure Kubernetes Service 健康情況](container-insights-analyze.md)。
