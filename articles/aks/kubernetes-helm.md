---
title: 在 AKS 中使用 Helm 安裝現有的應用程式
description: 瞭解如何使用 Helm 封裝工具，在 Azure Kubernetes Service （AKS）叢集中部署容器
services: container-service
author: zr-msft
ms.topic: article
ms.date: 06/24/2020
ms.author: zarhoads
ms.openlocfilehash: 6ee99eee02e874208106d39c6442f54f59f95dad
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85361603"
---
# <a name="install-existing-applications-with-helm-in-azure-kubernetes-service-aks"></a>在 Azure Kubernetes Service 中使用 Helm 安裝現有的應用程式（AKS）

[Helm][helm]是開放原始碼的封裝工具，可協助您安裝和管理 Kubernetes 應用程式的生命週期。 類似于 Linux 套件管理員（例如*APT*和*Yum*），Helm 是用來管理 Kubernetes 圖表，這是預先設定之 Kubernetes 資源的套件。

本文示範如何在 AKS 上的 Kubernetes 叢集中設定及使用 Helm。

## <a name="before-you-begin"></a>開始之前

此文章假設您目前具有 AKS 叢集。 如果您需要 AKS 叢集，請參閱[使用 Azure CLI][aks-quickstart-cli] 或[使用 Azure 入口網站][aks-quickstart-portal]的 AKS 快速入門。

您也需要安裝 Helm CLI，這是在您的開發系統上執行的用戶端。 它可讓您使用 Helm 來啟動、停止和管理應用程式。 如果您使用 Azure Cloud Shell，則已安裝 Helm CLI。 如需本機平臺上的安裝指示，請參閱[安裝 Helm][helm-install]。

> [!IMPORTANT]
> Helm 的目的是要在 Linux 節點上執行。 如果您的叢集中有 Windows Server 節點，您必須確定 Helm pod 只排程在 Linux 節點上執行。 您也必須確定您安裝的任何 Helm 圖表也會排程在正確的節點上執行。 本文中的命令會使用 [節點-選取器] [k8s] 來確定 pod 已排程至正確的節點，但並非所有的 Helm 圖表都可以公開節點選取器。 您也可以考慮在叢集上使用其他選項，例如[污點][taints]。

## <a name="verify-your-version-of-helm"></a>驗證您的 Helm 版本

使用 `helm version` 命令來確認您已安裝 Helm 3：

```console
helm version
```

下列範例會顯示已安裝的 Helm 版本3.0.0：

```console
$ helm version

version.BuildInfo{Version:"v3.0.0", GitCommit:"e29ce2a54e96cd02ccfce88bee4f58bb6e2a28b6", GitTreeState:"clean", GoVersion:"go1.13.4"}
```

## <a name="install-an-application-with-helm-v3"></a>使用 Helm v3 安裝應用程式

### <a name="add-the-official-helm-stable-charts-repository"></a>新增官方 Helm 穩定圖表存放庫

使用[helm][helm-repo-add]存放庫命令來新增官方 helm 穩定圖表儲存機制。

```console
helm repo add stable https://kubernetes-charts.storage.googleapis.com/
```

### <a name="find-helm-charts"></a>尋找 Helm 圖表

使用 Helm 圖表將應用程式部署到 Kubernetes 叢集中。 若要搜尋預先建立的 Helm 圖表，請使用[Helm search][helm-search]命令：

```console
helm search repo stable
```

下列扼要範例輸出顯示一些可供使用的 Helm 圖表：

```console
$ helm search repo stable

NAME                                    CHART VERSION   APP VERSION                     DESCRIPTION                                       
stable/acs-engine-autoscaler            2.2.2           2.1.1                           DEPRECATED Scales worker nodes within agent pools 
stable/aerospike                        0.3.1           v4.5.0.5                        A Helm chart for Aerospike in Kubernetes          
stable/airflow                          4.10.0          1.10.4                          Airflow is a platform to programmatically autho...
stable/ambassador                       4.4.7           0.85.0                          A Helm chart for Datawire Ambassador              
stable/anchore-engine                   1.3.7           0.5.2                           Anchore container analysis and policy evaluatio...
stable/apm-server                       2.1.5           7.0.0                           The server receives data from the Elastic APM a...
stable/ark                              4.2.2           0.10.2                          DEPRECATED A Helm chart for ark                   
stable/artifactory                      7.3.1           6.1.0                           DEPRECATED Universal Repository Manager support...
stable/artifactory-ha                   0.4.1           6.2.0                           DEPRECATED Universal Repository Manager support...
stable/atlantis                         3.8.4           v0.8.2                          A Helm chart for Atlantis https://www.runatlant...
stable/auditbeat                        1.1.0           6.7.0                           A lightweight shipper to audit the activities o...
stable/aws-cluster-autoscaler           0.3.3                                           Scales worker nodes within autoscaling groups.    
stable/aws-iam-authenticator            0.1.1           1.0                             A Helm chart for aws-iam-authenticator            
stable/bitcoind                         0.2.2           0.17.1                          Bitcoin is an innovative payment network and a ...
stable/bookstack                        1.1.2           0.27.4-1                        BookStack is a simple, self-hosted, easy-to-use...
stable/buildkite                        0.2.4           3                               DEPRECATED Agent for Buildkite                    
stable/burrow                           1.5.2           0.29.0                          Burrow is a permissionable smart contract machine 
stable/centrifugo                       3.1.0           2.1.0                           Centrifugo is a real-time messaging server.       
stable/cerebro                          1.3.1           0.8.5                           A Helm chart for Cerebro - a web admin tool tha...
stable/cert-manager                     v0.6.7          v0.6.2                          A Helm chart for cert-manager                     
stable/chaoskube                        3.1.2           0.14.0                          Chaoskube periodically kills random pods in you...
stable/chartmuseum                      2.4.0           0.8.2                           Host your own Helm Chart Repository               
stable/chronograf                       1.1.0           1.7.12                          Open-source web application written in Go and R...
stable/clamav                           1.0.4           1.6                             An Open-Source antivirus engine for detecting t...
stable/cloudserver                      1.0.3           8.1.5                           An open-source Node.js implementation of the Am...
stable/cluster-autoscaler               6.2.0           1.14.6                          Scales worker nodes within autoscaling groups.    
stable/cluster-overprovisioner          0.2.6           1.0                             Installs the a deployment that overprovisions t...
stable/cockroachdb                      2.1.16          19.1.5                          CockroachDB is a scalable, survivable, strongly...
stable/collabora-code                   1.0.5           4.0.3.1                         A Helm chart for Collabora Office - CODE-Edition  
stable/concourse                        8.2.7           5.6.0                           Concourse is a simple and scalable CI system.     
stable/consul                           3.9.2           1.5.3                           Highly available and distributed service discov...
stable/contour                          0.1.0           v0.15.0                         Contour Ingress controller for Kubernetes         
stable/coredns                          1.7.4           1.6.4                           CoreDNS is a DNS server that chains plugins and...
stable/cosbench                         1.0.1           0.0.6                           A benchmark tool for cloud object storage services
stable/coscale                          1.0.0           3.16.0                          CoScale Agent                                     
stable/couchbase-operator               1.0.1           1.2.1                           A Helm chart to deploy the Couchbase Autonomous...
stable/couchdb                          2.3.0           2.3.1                           DEPRECATED A database featuring seamless multi-...
stable/dask                             3.1.0           1.1.5                           Distributed computation in Python with task sch...
stable/dask-distributed                 2.0.2                                           DEPRECATED: Distributed computation in Python     
stable/datadog                          1.38.3          6.14.0                          DataDog Agent 
...
```

若要更新圖表清單，請使用 [helm repo update][helm-repo-update] 命令。

```console
helm repo update
```

以下範例顯示成功存放庫更新的情況：

```console
$ helm repo update

Hang tight while we grab the latest from your chart repositories...
...Successfully got an update from the "stable" chart repository
Update Complete. ⎈ Happy Helming!⎈
```

### <a name="run-helm-charts"></a>執行 Helm 圖表

若要使用 Helm 安裝圖表，請使用[Helm install][helm-install-command]命令，並指定要安裝之圖表的發行名稱和名稱。 若要查看如何安裝 Helm 圖，讓我們使用 Helm 圖表來安裝基本的 nginx 部署。

```console
helm install my-nginx-ingress stable/nginx-ingress \
    --set controller.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set defaultBackend.nodeSelector."beta\.kubernetes\.io/os"=linux
```

下列扼要範例輸出顯示 Helm 圖表所建立的 Kubernetes 資源部署狀態：

```console
$ helm install my-nginx-ingress stable/nginx-ingress \
>     --set controller.nodeSelector."beta\.kubernetes\.io/os"=linux \
>     --set defaultBackend.nodeSelector."beta\.kubernetes\.io/os"=linux

NAME: my-nginx-ingress
LAST DEPLOYED: Fri Nov 22 10:08:06 2019
NAMESPACE: default
STATUS: deployed
REVISION: 1
TEST SUITE: None
NOTES:
The nginx-ingress controller has been installed.
It may take a few minutes for the LoadBalancer IP to be available.
You can watch the status by running 'kubectl --namespace default get services -o wide -w my-nginx-ingress-controller'
...
```

使用 `kubectl get services` 命令來取得服務的*外部 IP* 。

```console
kubectl --namespace default get services -o wide -w my-nginx-ingress-controller
```

例如，下列命令會顯示*nginx 輸入控制器*服務的*外部 IP* ：

```console
$ kubectl --namespace default get services -o wide -w my-nginx-ingress-controller

NAME                          TYPE           CLUSTER-IP     EXTERNAL-IP     PORT(S)                      AGE   SELECTOR
my-nginx-ingress-controller   LoadBalancer   10.0.123.1     <EXTERNAL-IP>   80:31301/TCP,443:31623/TCP   96s   app=nginx-ingress,component=controller,release=my-nginx-ingress
```

### <a name="list-releases"></a>清單版本

若要查看叢集上所安裝的版本清單，請使用 `helm list` 命令。

```console
helm list
```

下列範例顯示在上一個步驟中部署的*nginx*輸入版本：

```console
$ helm list

NAME                NAMESPACE   REVISION    UPDATED                                 STATUS      CHART                   APP VERSION
my-nginx-ingress    default     1           2019-11-22 10:08:06.048477 -0600 CST    deployed    nginx-ingress-1.25.0    0.26.1 
```

### <a name="clean-up-resources"></a>清除資源

部署 Helm 圖表時會建立一些 Kubernetes 資源。 這些資源包含 Pod、部署和服務。 若要清除這些資源，請使用[helm 卸載][helm-cleanup]命令，並指定您的發行名稱，如先前的命令中所找到 `helm list` 。

```console
helm uninstall my-nginx-ingress
```

下列範例顯示已卸載名為*nginx*的版本：

```console
$ helm uninstall my-nginx-ingress

release "my-nginx-ingress" uninstalled
```

## <a name="next-steps"></a>後續步驟

如需使用 Helm 管理 Kubernetes 應用程式部署的詳細資訊，請參閱 Helm 文件。

> [!div class="nextstepaction"]
> [Helm 文件][helm-documentation]

<!-- LINKS - external -->
[helm]: https://github.com/kubernetes/helm/
[helm-cleanup]: https://helm.sh/docs/intro/using_helm/#helm-uninstall-uninstalling-a-release
[helm-documentation]: https://helm.sh/docs/
[helm-install]: https://helm.sh/docs/intro/install/
[helm-install-command]: https://helm.sh/docs/intro/using_helm/#helm-install-installing-a-package
[helm-repo-add]: https://helm.sh/docs/intro/quickstart/#initialize-a-helm-chart-repository
[helm-search]: https://helm.sh/docs/intro/using_helm/#helm-search-finding-charts
[helm-repo-update]: https://helm.sh/docs/intro/using_helm/#helm-repo-working-with-repositories
            
<!-- LINKS - internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[taints]: operator-best-practices-advanced-scheduler.md
