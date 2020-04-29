---
title: 在 AKS 中使用 Helm 安裝現有的應用程式
description: 瞭解如何使用 Helm 封裝工具，在 Azure Kubernetes Service （AKS）叢集中部署容器
services: container-service
author: zr-msft
ms.topic: article
ms.date: 11/22/2019
ms.author: zarhoads
ms.openlocfilehash: e46bed5fc9fd83a907f8c9e716317a54548c58cc
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81870243"
---
# <a name="install-existing-applications-with-helm-in-azure-kubernetes-service-aks"></a>在 Azure Kubernetes Service 中使用 Helm 安裝現有的應用程式（AKS）

[Helm][helm] 是開放原始碼的封裝工具，可協助您安裝和管理 Kubernetes 應用程式的生命週期。 Helm 類似於 *APT* 和 *Yum* 等 Linux 套件管理工具，可用於管理 Kubernetes 圖表 (即預先設定的 Kubernetes 資源套件)。

本文示範如何在 AKS 上的 Kubernetes 叢集中設定及使用 Helm。

## <a name="before-you-begin"></a>開始之前

此文章假設您目前具有 AKS 叢集。 如果您需要 AKS 叢集，請參閱[使用 Azure CLI][aks-quickstart-cli] 或[使用 Azure 入口網站][aks-quickstart-portal]的 AKS 快速入門。

您也需要安裝 Helm CLI，這是在您的開發系統上執行的用戶端。 它可讓您使用 Helm 來啟動、停止和管理應用程式。 如果您使用 Azure Cloud Shell，則已安裝 Helm CLI。 如需本機平臺上的安裝指示，請參閱[安裝 Helm][helm-install]。

> [!IMPORTANT]
> Helm 的目的是要在 Linux 節點上執行。 如果您的叢集中有 Windows Server 節點，您必須確定 Helm pod 只排程在 Linux 節點上執行。 您也必須確定您安裝的任何 Helm 圖表也會排程在正確的節點上執行。 本文中的命令會使用[節點選取器][k8s-node-selector]來確定 pod 已排程至正確的節點，但並非所有的 Helm 圖表都可以公開節點選取器。 您也可以考慮在叢集上使用其他選項，例如[污點][taints]。

## <a name="verify-your-version-of-helm"></a>驗證您的 Helm 版本

使用`helm version`命令來確認您已安裝的 Helm 版本：

```console
helm version
```

下列範例會顯示已安裝的 Helm 版本3.0.0：

```console
$ helm version

version.BuildInfo{Version:"v3.0.0", GitCommit:"e29ce2a54e96cd02ccfce88bee4f58bb6e2a28b6", GitTreeState:"clean", GoVersion:"go1.13.4"}
```

針對 Helm v3，請遵循[Helm v3 一節](#install-an-application-with-helm-v3)中的步驟。 針對 Helm v2，請遵循[Helm v2 一節](#install-an-application-with-helm-v2)中的步驟

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

若要更新圖表清單，請使用 [helm repo update][helm-repo-update] 命令。 以下範例顯示成功存放庫更新的情況：

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

使用`kubectl get services`命令來取得服務的*外部 IP* 。 例如，下列命令會顯示*nginx 輸入控制器*服務的*外部 IP* ：

```console
$ kubectl --namespace default get services -o wide -w my-nginx-ingress-controller

NAME                          TYPE           CLUSTER-IP     EXTERNAL-IP     PORT(S)                      AGE   SELECTOR
my-nginx-ingress-controller   LoadBalancer   10.0.123.1     <EXTERNAL-IP>   80:31301/TCP,443:31623/TCP   96s   app=nginx-ingress,component=controller,release=my-nginx-ingress
```

### <a name="list-releases"></a>清單版本

若要查看叢集上所安裝的版本清單，請使用`helm list`命令。

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

部署 Helm 圖表時會建立一些 Kubernetes 資源。 這些資源包含 Pod、部署和服務。 若要清除這些資源，請使用[helm 卸載][helm-cleanup]命令，並指定您的發行名稱，如先前`helm list`的命令中所找到。

```console
helm uninstall my-nginx-ingress
```

下列範例顯示已卸載名為*nginx*的版本：

```console
$ helm uninstall my-nginx-ingress

release "my-nginx-ingress" uninstalled
```

## <a name="install-an-application-with-helm-v2"></a>使用 Helm v2 安裝應用程式

### <a name="create-a-service-account"></a>建立服務帳戶

在已啟用 RBAC 的 AKS 叢集中部署 Helm 之前，您需要適用於 Tiller 服務的服務帳戶與角色繫結。 如需在已啟用 RBAC 的叢集中保護 Helm / Tiller 的詳細資訊，請參閱 [Tiller、命名空間和 RBAC][tiller-rbac]。 如果 AKS 叢集未啟用 RBAC，請略過此步驟。

建立名為 `helm-rbac.yaml` 的檔案，然後將下列 YAML 複製進來：

```yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  name: tiller
  namespace: kube-system
---
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: tiller
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
  - kind: ServiceAccount
    name: tiller
    namespace: kube-system
```

使用 `kubectl apply` 命令來建立服務帳戶和角色繫結：

```console
kubectl apply -f helm-rbac.yaml
```

### <a name="secure-tiller-and-helm"></a>保護 Tiller 和 Helm

Helm 用戶端和 Tiller 服務會使用 TLS/SSL 互相驗證及通訊。 這個驗證方法有助於保護 Kubernetes 叢集，以及驗證可以部署哪些服務。 為了提高安全性，您可以產生自己的簽署憑證。 每個 Helm 使用者會收到自己的用戶端憑證，而 Tiller 會在 Kubernetes 叢集中初始化，並且套用憑證。 如需詳細資訊，請參閱[在 Helm 與 Tiller 之間使用 TLS/SSL][helm2-ssl]。

使用了已啟用 RBAC 的 Kubernetes 叢集，您就可以控制 Tiller 對於叢集的存取權層級。 您可以定義 Kubernetes 命名空間 (Tiller 部署於其中)，並且限制 Tiller 後續可以在其中部署資源的命名空間。 這個方法可讓您在不同的命名空間中建立 Tiller 執行個體並且限制部署界限，以及將 Helm 用戶端使用者的範圍限制為特定命名空間。 如需詳細資訊，請參閱 [Helm 角色型存取控制][helm2-rbac]。

### <a name="configure-helm"></a>設定 Helm

若要將基本 Tiller 部署到 AKS 叢集，請使用 [helm init][helm2-init] 命令。 如果您的叢集並未啟用 RBAC，請移除 `--service-account` 引數和值。 下列範例也會將歷程[記錄設定為最大值][helm2-history-max]200。

如果您已為 Tiller 和 Helm 設定 TLS/SSL，請略過這個基本初始化步驟，改為提供必要的 `--tiller-tls-`，如接下來的範例所示。

```console
helm init --history-max 200 --service-account tiller --node-selectors "beta.kubernetes.io/os=linux"
```

如果您已在 Helm 與 Tiller 之間設定 TLS/SSL，請提供 `--tiller-tls-*` 參數和您自己的憑證名稱，如下列範例所示：

```console
helm init \
    --tiller-tls \
    --tiller-tls-cert tiller.cert.pem \
    --tiller-tls-key tiller.key.pem \
    --tiller-tls-verify \
    --tls-ca-cert ca.cert.pem \
    --history-max 200 \
    --service-account tiller \
    --node-selectors "beta.kubernetes.io/os=linux"
```

### <a name="find-helm-charts"></a>尋找 Helm 圖表

使用 Helm 圖表將應用程式部署到 Kubernetes 叢集中。 若要搜尋預先建立的 Helm 圖表，請使用[Helm search][helm2-search]命令：

```console
helm search
```

下列扼要範例輸出顯示一些可供使用的 Helm 圖表：

```
$ helm search

NAME                           CHART VERSION    APP VERSION  DESCRIPTION
stable/aerospike               0.1.7            v3.14.1.2    A Helm chart for Aerospike in Kubernetes
stable/anchore-engine          0.1.7            0.1.10       Anchore container analysis and policy evaluatio...
stable/apm-server              0.1.0            6.2.4        The server receives data from the Elastic APM a...
stable/ark                     1.0.1            0.8.2        A Helm chart for ark
stable/artifactory             7.2.1            6.0.0        Universal Repository Manager supporting all maj...
stable/artifactory-ha          0.2.1            6.0.0        Universal Repository Manager supporting all maj...
stable/auditbeat               0.1.0            6.2.4        A lightweight shipper to audit the activities o...
stable/aws-cluster-autoscaler  0.3.3                         Scales worker nodes within autoscaling groups.
stable/bitcoind                0.1.3            0.15.1       Bitcoin is an innovative payment network and a ...
stable/buildkite               0.2.3            3            Agent for Buildkite
stable/burrow                  0.4.4            0.17.1       Burrow is a permissionable smart contract machine
stable/centrifugo              2.0.1            1.7.3        Centrifugo is a real-time messaging server.
stable/cerebro                 0.1.0            0.7.3        A Helm chart for Cerebro - a web admin tool tha...
stable/cert-manager            v0.3.3           v0.3.1       A Helm chart for cert-manager
stable/chaoskube               0.7.0            0.8.0        Chaoskube periodically kills random pods in you...
stable/chartmuseum             1.5.0            0.7.0        Helm Chart Repository with support for Amazon S...
stable/chronograf              0.4.5            1.3          Open-source web application written in Go and R...
stable/cluster-autoscaler      0.6.4            1.2.2        Scales worker nodes within autoscaling groups.
stable/cockroachdb             1.1.1            2.0.0        CockroachDB is a scalable, survivable, strongly...
stable/concourse               1.10.1           3.14.1       Concourse is a simple and scalable CI system.
stable/consul                  3.2.0            1.0.0        Highly available and distributed service discov...
stable/coredns                 0.9.0            1.0.6        CoreDNS is a DNS server that chains plugins and...
stable/coscale                 0.2.1            3.9.1        CoScale Agent
stable/dask                    1.0.4            0.17.4       Distributed computation in Python with task sch...
stable/dask-distributed        2.0.2                         DEPRECATED: Distributed computation in Python
stable/datadog                 0.18.0           6.3.0        DataDog Agent
...
```

若要更新圖表清單，請使用 [helm repo update][helm2-repo-update] 命令。 以下範例顯示成功存放庫更新的情況：

```console
$ helm repo update

Hold tight while we grab the latest from your chart repositories...
...Skip local chart repository
...Successfully got an update from the "stable" chart repository
Update Complete.
```

### <a name="run-helm-charts"></a>執行 Helm 圖表

若要使用 Helm 來安裝圖表，請使用 [helm install][helm2-install-command] 命令並且指定要安裝的圖表名稱。 若要查看如何安裝 Helm 圖，讓我們使用 Helm 圖表來安裝基本的 nginx 部署。 如果您已設定 TLS/SSL，請新增 `--tls` 參數以使用 Helm 用戶端憑證。

```console
helm install stable/nginx-ingress \
    --set controller.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set defaultBackend.nodeSelector."beta\.kubernetes\.io/os"=linux
```

下列扼要範例輸出顯示 Helm 圖表所建立的 Kubernetes 資源部署狀態：

```
$ helm install stable/nginx-ingress --set controller.nodeSelector."beta\.kubernetes\.io/os"=linux --set defaultBackend.nodeSelector."beta\.kubernetes\.io/os"=linux

NAME:   flailing-alpaca
LAST DEPLOYED: Thu May 23 12:55:21 2019
NAMESPACE: default
STATUS: DEPLOYED

RESOURCES:
==> v1/ConfigMap
NAME                                      DATA  AGE
flailing-alpaca-nginx-ingress-controller  1     0s

==> v1/Pod(related)
NAME                                                            READY  STATUS             RESTARTS  AGE
flailing-alpaca-nginx-ingress-controller-56666dfd9f-bq4cl       0/1    ContainerCreating  0         0s
flailing-alpaca-nginx-ingress-default-backend-66bc89dc44-m87bp  0/1    ContainerCreating  0         0s

==> v1/Service
NAME                                           TYPE          CLUSTER-IP  EXTERNAL-IP  PORT(S)                     AGE
flailing-alpaca-nginx-ingress-controller       LoadBalancer  10.0.109.7  <pending>    80:31219/TCP,443:32421/TCP  0s
flailing-alpaca-nginx-ingress-default-backend  ClusterIP     10.0.44.97  <none>       80/TCP                      0s
...
```

需要一或兩分鐘的時間，nginx 輸入控制器服務的*外部 IP*位址才會填入，並可讓您使用網頁瀏覽器來存取它。

### <a name="list-helm-releases"></a>列出 Helm 版本

若要查看安裝於叢集上的版本清單，請使用 [helm list][helm2-list] 命令。 下列範例顯示在上一個步驟中部署的 nginx 輸入版本。 如果您已設定 TLS/SSL，請新增 `--tls` 參數以使用 Helm 用戶端憑證。

```console
$ helm list

NAME                REVISION    UPDATED                     STATUS      CHART                 APP VERSION   NAMESPACE
flailing-alpaca   1         Thu May 23 12:55:21 2019    DEPLOYED    nginx-ingress-1.6.13    0.24.1      default
```

### <a name="clean-up-resources"></a>清除資源

部署 Helm 圖表時會建立一些 Kubernetes 資源。 這些資源包含 Pod、部署和服務。 若要清除這些資源，請使用 `helm delete` 命令，並指定在先前 `helm list` 命令中找到的版本名稱。 下列範例會刪除名為*flailing-alpaca*的發行：

```console
$ helm delete flailing-alpaca

release "flailing-alpaca" deleted
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
[helm2-init]: https://v2.helm.sh/docs/helm/#helm-init
[helm2-install-command]: https://v2.helm.sh/docs/helm/#helm-install
[helm2-install-options]: https://github.com/kubernetes/helm/blob/master/docs/install.md
[helm2-list]: https://v2.helm.sh/docs/helm/#helm-list
[helm2-history-max]: https://v2.helm.sh/docs/using_helm/#initialize-helm-and-install-tiller
[helm2-rbac]: https://v2.helm.sh/docs/using_helm/#role-based-access-control
[helm2-repo-update]: https://v2.helm.sh/docs/helm/#helm-repo-update
[helm2-search]: https://v2.helm.sh/docs/helm/#helm-search
[tiller-rbac]: https://v2.helm.sh/docs/using_helm/#tiller-namespaces-and-rbac
[helm2-ssl]: https://v2.helm.sh/docs/using_helm/#using-ssl-between-helm-and-tiller
            
<!-- LINKS - internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[k8s-node-selector]: concepts-clusters-workloads.md#node-selectors
[taints]: operator-best-practices-advanced-scheduler.md
