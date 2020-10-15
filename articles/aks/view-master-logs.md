---
title: 檢視 Azure Kubernetes Service (AKS) 控制器記錄
description: 了解如何在 Azure Kubernetes Service (AKS) 中啟用並檢視 Kubernetes 主要節點的記錄
services: container-service
ms.topic: article
ms.date: 10/14/2020
ms.openlocfilehash: 79ed9308488725d9be0c839bbd04b6783bbbd85a
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/15/2020
ms.locfileid: "92076380"
---
# <a name="enable-and-review-kubernetes-master-node-logs-in-azure-kubernetes-service-aks"></a>在 Azure Kubernetes Service (AKS) 中啟用並檢閱 Kubernetes 主要節點記錄

使用 Azure Kubernetes Service (AKS) 時，*kube-apiserver* 和 *kube-controller-manager* 等主要元件是以受控服務的形式提供。 您會建立並管理執行 *kubelet* 和容器執行階段的節點，並透過受控 Kubernetes API 伺服器部署應用程式。 為了協助對應用程式和服務進行疑難排解，您可能需要檢視由這些主要元件所產生的記錄。 本文會示範如何使用 Azure 監視器記錄來啟用和查詢來自 Kubernetes 主要元件的記錄。

## <a name="before-you-begin"></a>開始之前

本文需要您的 Azure 帳戶中有正在執行的現有 AKS 叢集。 若您沒有 AKS 叢集，請使用 [Azure CLI][cli-quickstart] 或 [Azure 入口網站][portal-quickstart]建立一個。 Azure 監視器記錄可同時搭配已啟用 RBAC 和未啟用 RBAC 的 AKS 叢集運作。

## <a name="enable-resource-logs"></a>啟用資源記錄

為了協助從多個來源收集並檢閱資料，Azure 監視器記錄提供能針對您的環境提供見解的查詢語言和分析引擎。 工作區可用來收集並分析資料，並可與其他 Azure 服務 (例如 Application Insights 和資訊安全中心) 整合。 若要使用不同的平臺來分析記錄，您可以改為選擇將資源記錄傳送到 Azure 儲存體帳戶或事件中樞。 如需詳細資訊，請參閱[何謂 Azure 監視器記錄][log-analytics-overview]。

Azure 監視器記錄會在 Azure 入口網站中啟用和管理。 若要在 AKS 叢集中啟用 Kubernetes 主要元件的記錄收集，請在網頁瀏覽器中開啟 Azure 入口網站並完成下列步驟：

1. 選取適用於您 AKS 叢集的資源群組，例如 *myResourceGroup*。 請勿選取包含個別 AKS 叢集資源的資源群組，例如 *MC_myResourceGroup_myAKSCluster_eastus*。
1. 選擇左邊的 [診斷設定]****。
1. 選取您的 AKS 叢集（例如 *myAKSCluster*），然後選擇 **新增診斷設定**。
1. 輸入名稱 (例如 myAKSClusterLogs**)，然後選取 [傳送至 Log Analytics]**** 選項。
1. 選取現有的工作區，或建立一個新的工作區。 如果您建立工作區，請提供工作區名稱、資源群組和位置。
1. 在可用的記錄清單中，選取想要啟用的記錄。 在此範例中，請啟用 *kube-audit* and *kube-audit-admin* 記錄。 常見的記錄包括 *kube-apiserver*、 *kube-controller*和 *kube*排程器。 您可以在啟用 Log Analytics 工作區之後返回這裡並變更收集的記錄。
1. 準備好後，請選取 [儲存]**** 以啟用所選取記錄的收集。

## <a name="log-categories"></a>記錄類別

除了 Kubernetes 撰寫的專案之外，您專案的 audit 記錄也有來自 AKS 的專案。

Audit 記錄會記錄到兩個類別中： *kube-audit-admin* 和 *kube-audit*。 *Kube-audit*類別包含每個 audit 事件的所有審核記錄資料，包括*get*、 *list*、 *create*、 *update*、 *delete*、 *patch*和*post*。

*Kube-audit-admin*類別是*kube-audit*記錄類別的子集。 *kube-audit-系統管理員* 會從記錄檔中排除 *get* 和 *list* audit 事件，以大幅減少記錄檔的數目。

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

使用 [kubectl create][kubectl-create] 命令建立 Pod 並指定您的 YAML 檔案，如下列範例所示：

```
$ kubectl create -f nginx.yaml

pod/nginx created
```

## <a name="view-collected-logs"></a>檢視收集的記錄

可能需要幾分鐘的時間，才會啟用並顯示診斷記錄。

> [!NOTE]
> 如果您需要所有的 audit 記錄資料以符合規範或其他用途，請將其收集並儲存在低成本的儲存體中，例如 blob 儲存體。 使用 *kube-audit-admin* 記錄類別來收集和儲存一組有意義的 audit 記錄資料，以供監視和警示之用。

在 Azure 入口網站中，流覽至您的 AKS 叢集，然後選取左側的 [ **記錄** 檔]。 如果出現 [ *查詢範例* ] 視窗，請加以關閉。

選擇左邊的 [記錄]****。 若要查看 *kube-audit* 記錄檔，請在文字方塊中輸入下列查詢：

```
AzureDiagnostics
| where Category == "kube-audit"
| project log_s
```

可能會傳回許多記錄。 若要將查詢範圍縮小以查看上一個步驟中所建立之 NGINX pod 的相關記錄，請新增其他 *where* 語句來搜尋 *NGINX* ，如下列範例查詢所示：

```
AzureDiagnostics
| where Category == "kube-audit"
| where log_s contains "nginx"
| project log_s
```

若要查看 *kube-audit-admin* 記錄，請在文字方塊中輸入下列查詢：

```
AzureDiagnostics
| where Category == "kube-audit-admin"
| project log_s
```

在此範例中，查詢會顯示 *kube-audit-admin*中的所有建立作業。傳回的結果可能有很多，若要將查詢範圍縮小以查看在上一個步驟中建立的 NGINX pod 記錄，請新增其他 *where* 語句來搜尋 *NGINX* ，如下列範例查詢所示。

```
AzureDiagnostics
| where Category == "kube-audit-admin"
| where log_s contains "nginx"
| project log_s
```


如需有關如何查詢及篩選記錄資料的詳細資訊，請參閱 [查看或分析使用 log analytics 記錄搜尋所收集的資料][analyze-log-analytics]。

## <a name="log-event-schema"></a>記錄事件結構描述

AKS 會記錄下列事件：

* [AzureActivity][log-schema-azureactivity]
* [AzureDiagnostics][log-schema-azurediagnostics]
* [AzureMetrics][log-schema-azuremetrics]
* [ContainerImageInventory][log-schema-containerimageinventory]
* [ContainerInventory][log-schema-containerinventory]
* [ContainerLog][log-schema-containerlog]
* [ContainerNodeInventory][log-schema-containernodeinventory]
* [ContainerServiceLog][log-schema-containerservicelog]
* [活動訊號][log-schema-heartbeat]
* [InsightsMetrics][log-schema-insightsmetrics]
* [KubeEvents][log-schema-kubeevents]
* [KubeHealth][log-schema-kubehealth]
* [KubeMonAgentEvents][log-schema-kubemonagentevents]
* [KubeNodeInventory][log-schema-kubenodeinventory]
* [KubePodInventory][log-schema-kubepodinventory]
* [KubeServices][log-schema-kubeservices]
* [Perf][log-schema-perf]

## <a name="log-roles"></a>記錄角色

| 角色                     | 描述 |
|--------------------------|-------------|
| *aksService*             | HcpService 中的控制平面作業的顯示名稱 ()  |
| *masterclient*           | MasterClientCertificate 的 audit 記錄檔中的顯示名稱，您從 az aks get 認證取得的憑證 |
| *nodeclient*             | 代理程式節點所使用之 ClientCertificate 的顯示名稱 |

## <a name="next-steps"></a>後續步驟

在本文中，您已了解如何在 AKS 叢集中啟用並檢閱 Kubernetes 主要元件的記錄。 若要進行進一步的監視及疑難排解，您也可以[檢視 Kubelet 記錄][kubelet-logs]及[啟用 SSH 節點存取][aks-ssh]。

<!-- LINKS - external -->
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create

<!-- LINKS - internal -->
[cli-quickstart]: kubernetes-walkthrough.md
[portal-quickstart]: kubernetes-walkthrough-portal.md
[log-analytics-overview]: ../azure-monitor/log-query/log-query-overview.md
[analyze-log-analytics]: ../azure-monitor/log-query/get-started-portal.md
[kubelet-logs]: kubelet-logs.md
[aks-ssh]: ssh.md
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
[log-schema-azureactivity]: /azure/azure-monitor/reference/tables/azureactivity
[log-schema-azurediagnostics]: /azure/azure-monitor/reference/tables/azurediagnostics
[log-schema-azuremetrics]: /azure/azure-monitor/reference/tables/azuremetrics
[log-schema-containerimageinventory]: /azure/azure-monitor/reference/tables/containerimageinventory
[log-schema-containerinventory]: /azure/azure-monitor/reference/tables/containerinventory
[log-schema-containerlog]: /azure/azure-monitor/reference/tables/containerlog
[log-schema-containernodeinventory]: /azure/azure-monitor/reference/tables/containernodeinventory
[log-schema-containerservicelog]: /azure/azure-monitor/reference/tables/containerservicelog
[log-schema-heartbeat]: /azure/azure-monitor/reference/tables/heartbeat
[log-schema-insightsmetrics]: /azure/azure-monitor/reference/tables/insightsmetrics
[log-schema-kubeevents]: /azure/azure-monitor/reference/tables/kubeevents
[log-schema-kubehealth]: /azure/azure-monitor/reference/tables/kubehealth
[log-schema-kubemonagentevents]: /azure/azure-monitor/reference/tables/kubemonagentevents
[log-schema-kubenodeinventory]: /azure/azure-monitor/reference/tables/kubenodeinventory
[log-schema-kubepodinventory]: /azure/azure-monitor/reference/tables/kubepodinventory
[log-schema-kubeservices]: /azure/azure-monitor/reference/tables/kubeservices
[log-schema-perf]: /azure/azure-monitor/reference/tables/perf
