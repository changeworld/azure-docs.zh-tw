---
title: 搭配 Kubernetes 和 Helm 使用電腦視覺容器
titleSuffix: Azure Cognitive Services
description: 將電腦視覺容器部署至 Azure 容器實例，並在網頁瀏覽器中進行測試。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 9aac374de5af748eafbe4c22e5fc89f64e483c2a
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/29/2020
ms.locfileid: "80877963"
---
# <a name="use-computer-vision-container-with-kubernetes-and-helm"></a>搭配 Kubernetes 和 Helm 使用電腦視覺容器

管理內部部署電腦視覺容器的其中一個選項是使用 Kubernetes 和 Helm。 使用 Kubernetes 和 Helm 定義電腦視覺的容器映射，我們將建立 Kubernetes 套件。 此套件將會部署到內部部署的 Kubernetes 叢集。 最後，我們將探討如何測試已部署的服務。 如需執行 Docker 容器而不 Kubernetes 協調流程的詳細資訊，請參閱[安裝並執行電腦視覺容器](computer-vision-how-to-install-containers.md)。

## <a name="prerequisites"></a>必要條件

在內部部署使用電腦視覺容器之前，請先遵循下列必要條件：

| 必要 | 目的 |
|----------|---------|
| Azure 帳戶 | 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶][free-azure-account]。 |
| Kubernetes CLI | 需要[KUBERNETES CLI][kubernetes-cli] ，才能從容器登錄中管理共用認證。 Helm 之前也需要 Kubernetes，這是 Kubernetes 套件管理員。 |
| Helm CLI | 安裝[HELM CLI][helm-install]，用來安裝 Helm 圖表（容器封裝定義）。 |
| 電腦視覺資源 |若要使用此容器，您必須具備：<br><br>Azure**電腦視覺**資源和相關聯的 API 金鑰端點 URI。 這兩個值都可在資源的 [總覽] 和 [金鑰] 頁面上取得，而且必須要有才能啟動容器。<br><br>**{API_KEY}**： [**金鑰**] 頁面上有兩個可用的資源金鑰之一<br><br>**{ENDPOINT_URI}**： [**總覽**] 頁面上所提供的端點|

[!INCLUDE [Gathering required parameters](../containers/includes/container-gathering-required-parameters.md)]

### <a name="the-host-computer"></a>主機電腦

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="container-requirements-and-recommendations"></a>容器的需求和建議

[!INCLUDE [Container requirements and recommendations](includes/container-requirements-and-recommendations.md)]

## <a name="connect-to-the-kubernetes-cluster"></a>連接到 Kubernetes 叢集

主機電腦預期會有可用的 Kubernetes 叢集。 請參閱本教學課程以瞭解如何[部署 Kubernetes](../../aks/tutorial-kubernetes-deploy-cluster.md)叢集，以瞭解如何將 Kubernetes 叢集部署到主機電腦的概念。

### <a name="sharing-docker-credentials-with-the-kubernetes-cluster"></a>與 Kubernetes 叢集共用 Docker 認證

若要允許 Kubernetes 叢集 `docker pull` 從容器登錄中設定的映射 `containerpreview.azurecr.io` ，您需要將 docker 認證傳輸到叢集。 執行 [`kubectl create`][kubectl-create] 下列命令，根據容器登錄存取必要條件中提供的認證建立*docker 登錄密碼*。

從您選擇的命令列介面，執行下列命令。 請務必將 `<username>` 、和取代為 `<password>` `<email-address>` 容器登錄認證。

```console
kubectl create secret docker-registry containerpreview \
    --docker-server=containerpreview.azurecr.io \
    --docker-username=<username> \
    --docker-password=<password> \
    --docker-email=<email-address>
```

> [!NOTE]
> 如果您已經有容器登錄的存取權 `containerpreview.azurecr.io` ，您可以改為使用一般旗標來建立 Kubernetes 秘密。 請考慮下列會針對您的 Docker 設定 JSON 執行的命令。
> ```console
>  kubectl create secret generic containerpreview \
>      --from-file=.dockerconfigjson=~/.docker/config.json \
>      --type=kubernetes.io/dockerconfigjson
> ```

成功建立秘密之後，會將下列輸出列印到主控台。

```console
secret "containerpreview" created
```

若要確認是否已建立密碼，請使用旗標來執行 [`kubectl get`][kubectl-get] `secrets` 。

```console
kubectl get secrets
```

執行會 `kubectl get secrets` 列印所有設定的秘密。

```console
NAME                  TYPE                                  DATA      AGE
containerpreview      kubernetes.io/dockerconfigjson        1         30s
```

## <a name="configure-helm-chart-values-for-deployment"></a>設定部署的 Helm 圖表值

首先，建立名為*read*的資料夾，然後將下列 YAML 內容貼入名為*Chart. yml*的新檔案中。

```yaml
apiVersion: v1
name: read
version: 1.0.0
description: A Helm chart to deploy the microsoft/cognitive-services-read to a Kubernetes cluster
```

若要設定 Helm 圖表的預設值，請將下列 YAML 複製並貼到名為的檔案中 `values.yaml` 。 `# {ENDPOINT_URI}` `# {API_KEY}` 以您自己的值取代和批註。

```yaml
# These settings are deployment specific and users can provide customizations

read:
  enabled: true
  image:
    name: cognitive-services-read
    registry: containerpreview.azurecr.io/
    repository: microsoft/cognitive-services-read
    tag: latest
    pullSecret: containerpreview # Or an existing secret
    args:
      eula: accept
      billing: # {ENDPOINT_URI}
      apikey: # {API_KEY}
```

> [!IMPORTANT]
> 如果 `billing` `apikey` 未提供和值，服務會在15分鐘後到期。 同樣地，驗證將會失敗，因為服務將無法使用。

在*讀取*目錄下建立*templates*資料夾。 將下列 YAML 複製並貼到名為的檔案中 `deployment.yaml` 。 檔案 `deployment.yaml` 將作為 Helm 範本。

> 範本會產生資訊清單檔案，這些檔案是 Kubernetes 可瞭解的 YAML 格式資源描述。 [-Helm 圖表範本指南][chart-template-guide]

```yaml
apiVersion: apps/v1beta1
kind: Deployment
metadata:
  name: read
spec:
  template:
    metadata:
      labels:
        app: read-app
    spec:
      containers:
      - name: {{.Values.read.image.name}}
        image: {{.Values.read.image.registry}}{{.Values.read.image.repository}}
        ports:
        - containerPort: 5000
        env:
        - name: EULA
          value: {{.Values.read.image.args.eula}}
        - name: billing
          value: {{.Values.read.image.args.billing}}
        - name: apikey
          value: {{.Values.read.image.args.apikey}}
      imagePullSecrets:
      - name: {{.Values.read.image.pullSecret}}

--- 
apiVersion: v1
kind: Service
metadata:
  name: read
spec:
  type: LoadBalancer
  ports:
  - port: 5000
  selector:
    app: read-app
```

範本會指定負載平衡器服務，以及要讀取的容器/映射部署。

### <a name="the-kubernetes-package-helm-chart"></a>Kubernetes 套件（Helm 圖）

*Helm 圖表*包含要從容器登錄中提取之 docker 映射的設定 `containerpreview.azurecr.io` 。

> [Helm 圖][helm-charts]是描述一組相關 Kubernetes 資源的檔案集合。 單一圖表可能用來部署一些簡單的東西，像是 memcached pod 或複雜的東西，像是具有 HTTP 伺服器、資料庫、快取等的完整 web 應用程式堆疊。

提供的*Helm 圖表*會提取電腦視覺服務的 docker 映射，以及容器登錄中的對應服務 `containerpreview.azurecr.io` 。

## <a name="install-the-helm-chart-on-the-kubernetes-cluster"></a>在 Kubernetes 叢集上安裝 Helm 圖表

若要安裝*helm 圖*，我們必須執行 [`helm install`][helm-install-cmd] 命令。 請務必從資料夾上方的目錄執行 install 命令 `read` 。

```console
helm install read ./read
```

以下是您可能預期會從成功的安裝執行中看到的範例輸出：

```console
NAME: read
LAST DEPLOYED: Thu Sep 04 13:24:06 2019
NAMESPACE: default
STATUS: DEPLOYED

RESOURCES:
==> v1/Pod(related)
NAME                    READY  STATUS             RESTARTS  AGE
read-57cb76bcf7-45sdh   0/1    ContainerCreating  0         0s

==> v1/Service
NAME     TYPE          CLUSTER-IP    EXTERNAL-IP  PORT(S)         AGE
read     LoadBalancer  10.110.44.86  localhost    5000:31301/TCP  0s

==> v1beta1/Deployment
NAME    READY  UP-TO-DATE  AVAILABLE  AGE
read    0/1    1           0          0s
```

Kubernetes 部署可能需要數分鐘的時間才能完成。 若要確認 pod 和服務都已正確部署並可供使用，請執行下列命令：

```console
kubectl get all
```

您應該會看到類似下列輸出的內容：

```console
kubectl get all
NAME                        READY   STATUS    RESTARTS   AGE
pod/read-57cb76bcf7-45sdh   1/1     Running   0          17s

NAME                   TYPE           CLUSTER-IP     EXTERNAL-IP   PORT(S)          AGE
service/kubernetes     ClusterIP      10.96.0.1      <none>        443/TCP          45h
service/read           LoadBalancer   10.110.44.86   localhost     5000:31301/TCP   17s

NAME                   READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/read   1/1     1            1           17s

NAME                              DESIRED   CURRENT   READY   AGE
replicaset.apps/read-57cb76bcf7   1         1         1       17s
```
<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="next-steps"></a>後續步驟

如需在 Azure Kubernetes Service （AKS）中使用 Helm 安裝應用程式的詳細資訊，請[造訪這裡][installing-helm-apps-in-aks]。

> [!div class="nextstepaction"]
> [認知服務容器][cog-svcs-containers]

<!-- LINKS - external -->
[free-azure-account]: https://azure.microsoft.com/free
[git-download]: https://git-scm.com/downloads
[azure-cli]: https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest
[docker-engine]: https://www.docker.com/products/docker-engine
[kubernetes-cli]: https://kubernetes.io/docs/tasks/tools/install-kubectl
[helm-install]: https://helm.sh/docs/using_helm/#installing-helm
[helm-install-cmd]: https://helm.sh/docs/intro/using_helm/#helm-install-installing-a-package
[helm-charts]: https://helm.sh/docs/topics/charts/
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[helm-test]: https://helm.sh/docs/helm/#helm-test
[chart-template-guide]: https://helm.sh/docs/chart_template_guide

<!-- LINKS - internal -->
[vision-container-host-computer]: computer-vision-how-to-install-containers.md#the-host-computer
[installing-helm-apps-in-aks]: ../../aks/kubernetes-helm.md
[cog-svcs-containers]: ../cognitive-services-container-support.md
