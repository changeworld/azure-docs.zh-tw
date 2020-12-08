---
title: 在 AKS 中使用 Helm 安裝現有的應用程式
description: 瞭解如何使用 Helm 封裝工具，在 Azure Kubernetes Service (AKS) 叢集中部署容器
services: container-service
author: zr-msft
ms.topic: article
ms.date: 12/07/2020
ms.author: zarhoads
ms.openlocfilehash: f12dffe0b538738a8f6dd00cd3d87d44da828f21
ms.sourcegitcommit: 8b4b4e060c109a97d58e8f8df6f5d759f1ef12cf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/07/2020
ms.locfileid: "96779162"
---
# <a name="install-existing-applications-with-helm-in-azure-kubernetes-service-aks"></a>在 Azure Kubernetes Service (AKS) 中使用 Helm 安裝現有的應用程式

[Helm][helm] 是開放原始碼的封裝工具，可協助您安裝和管理 Kubernetes 應用程式的生命週期。 如同 Linux 套件管理員（例如 *APT* 和 *Yum*），Helm 可用來管理 Kubernetes 圖表，也就是預先設定的 Kubernetes 資源套件。

本文示範如何在 AKS 上的 Kubernetes 叢集中設定及使用 Helm。

## <a name="before-you-begin"></a>開始之前

此文章假設您目前具有 AKS 叢集。 如果您需要 AKS 叢集，請參閱[使用 Azure CLI][aks-quickstart-cli] 或[使用 Azure 入口網站][aks-quickstart-portal]的 AKS 快速入門。

您也需要安裝 Helm CLI，也就是在您的開發系統上執行的用戶端。 它可讓您使用 Helm 來啟動、停止和管理應用程式。 如果您使用 Azure Cloud Shell，則已安裝 Helm CLI。 如需本機平臺的安裝指示，請參閱 [安裝 Helm][helm-install]。

> [!IMPORTANT]
> Helm 的目的是要在 Linux 節點上執行。 如果您的叢集中有 Windows Server 節點，您必須確定 Helm pod 只排程在 Linux 節點上執行。 您也必須確定您安裝的任何 Helm 圖表也都已排程在正確的節點上執行。 本文中的命令會使用 [節點選取器] [k8s] 來確定 pod 已排程至正確的節點，但並非所有的 Helm 圖表都可能會公開節點選取器。 您也可以考慮使用叢集中的其他選項，例如 [污點][taints]。

## <a name="verify-your-version-of-helm"></a>驗證您的 Helm 版本

使用 `helm version` 命令確認您已安裝 Helm 3：

```console
helm version
```

下列範例會顯示已安裝的 Helm 版本3.0.0：

```console
$ helm version

version.BuildInfo{Version:"v3.0.0", GitCommit:"e29ce2a54e96cd02ccfce88bee4f58bb6e2a28b6", GitTreeState:"clean", GoVersion:"go1.13.4"}
```

## <a name="install-an-application-with-helm-v3"></a>使用 Helm v3 安裝應用程式

### <a name="add-helm-repositories"></a>新增 Helm 儲存機制

使用 [helm][helm-repo-add] 存放庫命令來新增 *nginx* 儲存機制。

```console
helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx
```

### <a name="find-helm-charts"></a>尋找 Helm 圖表

使用 Helm 圖表將應用程式部署到 Kubernetes 叢集中。 若要搜尋預先建立的 Helm 圖表，請使用 [Helm 搜尋][helm-search] 命令：

```console
helm search repo ingress-nginx
```

下列扼要範例輸出顯示一些可供使用的 Helm 圖表：

```console
$ helm search repo ingress-nginx

NAME                            CHART VERSION   APP VERSION     DESCRIPTION                                       
ingress-nginx/ingress-nginx     2.12.0          0.34.1          Ingress controller for Kubernetes using NGINX a...
```

若要更新圖表清單，請使用 [helm repo update][helm-repo-update] 命令。

```console
helm repo update
```

以下範例顯示成功存放庫更新的情況：

```console
$ helm repo update

Hang tight while we grab the latest from your chart repositories...
...Successfully got an update from the "ingress-nginx" chart repository
Update Complete. ⎈ Happy Helming!⎈
```

### <a name="run-helm-charts"></a>執行 Helm 圖表

若要使用 Helm 安裝圖表，請使用 [Helm install][helm-install-command] 命令，並指定發行名稱和要安裝的圖表名稱。 若要查看如何安裝 Helm 圖，讓我們使用 Helm 圖表來安裝基本的 nginx 部署。

```console
helm install my-nginx-ingress ingress-nginx/ingress-nginx \
    --set controller.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set defaultBackend.nodeSelector."beta\.kubernetes\.io/os"=linux
```

下列扼要範例輸出顯示 Helm 圖表所建立的 Kubernetes 資源部署狀態：

```console
$ helm install my-nginx-ingress ingress-nginx/ingress-nginx \
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
You can watch the status by running 'kubectl --namespace default get services -o wide -w my-nginx-ingress-ingress-nginx-controller'
...
```

使用 `kubectl get services` 命令來取得服務的 *外部 IP* 。

```console
kubectl --namespace default get services -o wide -w my-nginx-ingress-ingress-nginx-controller
```

例如，下列命令會顯示 nginx-輸入-輸入- *nginx 控制器* 服務的 *外部 IP* ：

```console
$ kubectl --namespace default get services -o wide -w my-nginx-ingress-ingress-nginx-controller

NAME                                        TYPE           CLUSTER-IP   EXTERNAL-IP      PORT(S)                      AGE   SELECTOR
my-nginx-ingress-ingress-nginx-controller   LoadBalancer   10.0.2.237   <EXTERNAL-IP>    80:31380/TCP,443:32239/TCP   72s   app.kubernetes.io/component=controller,app.kubernetes.io/instance=my-nginx-ingress,app.kubernetes.io/name=ingress-nginx
```

### <a name="list-releases"></a>列出版本

若要查看叢集上安裝的版本清單，請使用 `helm list` 命令。

```console
helm list
```

下列範例顯示在上一個步驟中部署的 *nginx* 輸入版本：

```console
$ helm list

NAME                NAMESPACE   REVISION    UPDATED                                 STATUS      CHART                   APP VERSION
my-nginx-ingress    default     1           2019-11-22 10:08:06.048477 -0600 CST    deployed    nginx-ingress-1.25.0    0.26.1 
```

### <a name="clean-up-resources"></a>清除資源

部署 Helm 圖表時會建立一些 Kubernetes 資源。 這些資源包含 Pod、部署和服務。 若要清除這些資源，請使用 [helm uninstall][helm-cleanup] 命令，並指定您的發行名稱，如先前命令中所找到 `helm list` 。

```console
helm uninstall my-nginx-ingress
```

下列範例顯示已卸載名為 *nginx* 的發行：

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
