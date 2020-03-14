---
title: 檢視 Azure Kubernetes Service (AKS) 控制器記錄
description: 了解如何在 Azure Kubernetes Service (AKS) 中啟用並檢視 Kubernetes 主要節點的記錄
services: container-service
ms.topic: article
ms.date: 01/03/2019
ms.openlocfilehash: f759f15cf98546cb95ba0adb5890885f85ca6aa1
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/13/2020
ms.locfileid: "79259379"
---
# <a name="enable-and-review-kubernetes-master-node-logs-in-azure-kubernetes-service-aks"></a>在 Azure Kubernetes Service (AKS) 中啟用並檢閱 Kubernetes 主要節點記錄

使用 Azure Kubernetes Service (AKS) 時，*kube-apiserver* 和 *kube-controller-manager* 等主要元件是以受控服務的形式提供。 您會建立並管理執行 *kubelet* 和容器執行階段的節點，並透過受控 Kubernetes API 伺服器部署應用程式。 為了協助對應用程式和服務進行疑難排解，您可能需要檢視由這些主要元件所產生的記錄。 本文會示範如何使用 Azure 監視器記錄來啟用和查詢來自 Kubernetes 主要元件的記錄。

## <a name="before-you-begin"></a>開始之前

本文需要您的 Azure 帳戶中有正在執行的現有 AKS 叢集。 如果您還沒有 AKS 叢集，請使用[Azure CLI][cli-quickstart]或[Azure 入口網站][portal-quickstart]建立一個叢集。 Azure 監視器記錄可同時搭配已啟用 RBAC 和未啟用 RBAC 的 AKS 叢集運作。

## <a name="enable-diagnostics-logs"></a>啟用診斷記錄

為了協助從多個來源收集並檢閱資料，Azure 監視器記錄提供能針對您的環境提供見解的查詢語言和分析引擎。 工作區可用來收集並分析資料，並可與其他 Azure 服務 (例如 Application Insights 和資訊安全中心) 整合。 若要使用不同的平台來分析記錄，您可以選擇將診斷記錄傳送至 Azure 儲存體帳戶或事件中樞。 如需詳細資訊，請參閱[什麼是 Azure 監視器記錄？][log-analytics-overview]。

Azure 監視器記錄會在 Azure 入口網站中啟用和管理。 若要在 AKS 叢集中啟用 Kubernetes 主要元件的記錄收集，請在網頁瀏覽器中開啟 Azure 入口網站並完成下列步驟：

1. 選取適用於您 AKS 叢集的資源群組，例如 *myResourceGroup*。 請勿選取包含個別 AKS 叢集資源的資源群組，例如 *MC_myResourceGroup_myAKSCluster_eastus*。
1. 選擇左邊的 [診斷設定]。
1. 選取您的 AKS 叢集（例如*myAKSCluster*），然後選擇 [**新增診斷設定**]。
1. 輸入名稱 (例如 myAKSClusterLogs)，然後選取 [傳送至 Log Analytics] 選項。
1. 選取現有的工作區，或建立一個新的。 如果您建立工作區，請提供工作區名稱、資源群組和位置。
1. 在可用的記錄清單中，選取想要啟用的記錄。 一般記錄包含*kube-kube-apiserver*、kube 和*kube-* 排程*器*。 您可以啟用其他記錄，例如 kube-audit 和 cluster-autoscaler。 您可以在啟用 Log Analytics 工作區之後返回這裡並變更收集的記錄。
1. 準備好後，請選取 [儲存] 以啟用所選取記錄的收集。

下列範例入口網站螢幕擷取畫面顯示 [*診斷設定*] 視窗：

![針對 AKS 叢集的 Azure 監視器記錄啟用 Log Analytics 工作區](media/view-master-logs/enable-oms-log-analytics.png)

## <a name="schedule-a-test-pod-on-the-aks-cluster"></a>在 AKS 叢集上對測試 Pod 進行排程

若要產生一些記錄，請在 AKS 叢集中建立新的 Pod。 下列範例 YAML 資訊清單可用來建立基本的 NGINX 執行個體。 在您偏好的編輯器中建立名為 `nginx.yaml` 的檔案，並貼上下列內容：

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx
spec:
  containers:
  - name: mypod
    image: nginx:1.15.5
    resources:
      requests:
        cpu: 100m
        memory: 128Mi
      limits:
        cpu: 250m
        memory: 256Mi
    ports:
    - containerPort: 80
```

使用[kubectl create][kubectl-create]命令建立 pod，並指定您的 YAML 檔案，如下列範例所示：

```
$ kubectl create -f nginx.yaml

pod/nginx created
```

## <a name="view-collected-logs"></a>檢視收集的記錄

診斷記錄可能需要幾分鐘的時間才會啟用並出現在 Log Analytics 工作區中。 在 Azure 入口網站中，選取 Log Analytics 工作區的資源群組（例如*myResourceGroup*），然後選擇您的 log analytics 資源，例如*myakslogs)* 。

![選擇適用於 AKS 叢集的 Log Analytics 工作區](media/view-master-logs/select-log-analytics-workspace.png)

選擇左邊的 [記錄]。 若要檢視 *kube-apiserver*，請在文字方塊中輸入下列查詢：

```
AzureDiagnostics
| where Category == "kube-apiserver"
| project log_s
```

系統應該會針對 API 伺服器傳回許多記錄。 若要縮小查詢範圍以檢視在上一個步驟中所建立之 NGINX Pod 的相關記錄，請加入額外的 *where* 陳述式以搜尋 *pods/nginx*，如下列範例查詢所示：

```
AzureDiagnostics
| where Category == "kube-apiserver"
| where log_s contains "pods/nginx"
| project log_s
```

系統會顯示 NGINX Pod 的特定記錄，如下列範例螢幕擷取畫面所示：

![範例 NGINX Pod 的 Log Analytics 查詢結果](media/view-master-logs/log-analytics-query-results.png)

若要檢視其他記錄，您可以更新查詢以將 *Category* 名稱變更為 *kube-controller-manager* 或 *kube-scheduler* (視您所啟用的其他記錄而定)。 您可以接著使用額外的 *where* 陳述式來精簡您想要查詢的事件。

如需如何查詢和篩選記錄資料的詳細資訊，請參閱[查看或分析以 log analytics 記錄搜尋所收集的資料][analyze-log-analytics]。

## <a name="log-event-schema"></a>記錄事件結構描述

為了協助分析記錄資料，下表會詳細說明適用於每個事件的結構描述：

| 欄位名稱               | 描述 |
|--------------------------|-------------|
| *resourceId*             | 產生記錄的 Azure 資源 |
| *time*                   | 上傳記錄的時間戳記 |
| *類別*               | 產生記錄之容器/元件的名稱 |
| *operationName*          | Always *Microsoft.ContainerService/managedClusters/diagnosticLogs/Read* |
| *properties.log*         | 來自元件之記錄的全文 |
| *properties.stream*      | *stderr* 或 *stdout* |
| *properties.pod*         | 作為記錄來源的 Pod 名稱 |
| *properties.containerID* | 此記錄檔來源之 docker 容器的識別碼 |

## <a name="log-roles"></a>記錄角色

| 角色                     | 描述 |
|--------------------------|-------------|
| *aksService*             | 控制平面作業在 audit 記錄檔中的顯示名稱（從 hcpService） |
| *masterclient*           | MasterClientCertificate 的 audit 記錄檔中的顯示名稱，是您從 az aks 取得認證取得的憑證 |
| *nodeclient*             | 代理程式節點所使用之 ClientCertificate 的顯示名稱 |

## <a name="next-steps"></a>後續步驟

在本文中，您已了解如何在 AKS 叢集中啟用並檢閱 Kubernetes 主要元件的記錄。 若要進一步進行監視和疑難排解，您也可以[查看 Kubelet 記錄][kubelet-logs]，並[啟用 SSH 節點存取][aks-ssh]。

<!-- LINKS - external -->
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create

<!-- LINKS - internal -->
[cli-quickstart]: kubernetes-walkthrough.md
[portal-quickstart]: kubernetes-walkthrough-portal.md
[log-analytics-overview]: ../log-analytics/log-analytics-overview.md
[analyze-log-analytics]: ../azure-monitor/learn/tutorial-viewdata.md
[kubelet-logs]: kubelet-logs.md
[aks-ssh]: ssh.md
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
