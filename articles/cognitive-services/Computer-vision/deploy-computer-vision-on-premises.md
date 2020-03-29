---
title: 將電腦視覺容器與庫伯奈斯和赫爾姆一起使用
titleSuffix: Azure Cognitive Services
description: 將電腦視覺容器部署到 Azure 容器實例，並在 Web 瀏覽器中測試它。
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 03/16/2020
ms.author: dapine
ms.openlocfilehash: 126060875c09d70b8680447d78b7cf6ccdd782af
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79458013"
---
# <a name="use-computer-vision-container-with-kubernetes-and-helm"></a>將電腦視覺容器與庫伯奈斯和赫爾姆一起使用

本地管理電腦視覺容器的一個選項是使用庫伯內特斯和赫爾姆。 使用 Kubernetes 和 Helm 定義電腦視覺容器映射，我們將創建一個庫伯奈斯包。 此包將部署到本地的 Kubernetes 群集。 最後，我們將探討如何測試已部署的服務。 有關在沒有 Kubernetes 業務流程的情況下運行 Docker 容器的詳細資訊，請參閱[安裝和運行電腦視覺容器](computer-vision-how-to-install-containers.md)。

## <a name="prerequisites"></a>Prerequisites

在本地使用電腦視覺容器之前，以下先決條件：

| 必要 | 目的 |
|----------|---------|
| Azure 帳戶 | 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶][free-azure-account]。 |
| 庫伯內斯 CLI | [庫伯內斯 CLI][kubernetes-cli]是管理容器註冊表中的共用憑據所必需的。 在赫爾姆之前也需要庫伯內特斯，這是庫伯內斯的包經理。 |
| Helm CLI | 安裝[Helm CLI][helm-install]，用於安裝掌舵圖（容器包定義）。 |
| 電腦視覺資源 |若要使用此容器，您必須具備：<br><br>Azure**電腦視覺**資源和關聯的 API 鍵端點 URI。 這兩個值都可以在資源的"概述"和"鍵"頁上使用，並且需要啟動容器。<br><br>**[API_KEY]**：**金鑰**頁上的兩個可用資源鍵之一<br><br>**[ENDPOINT_URI]**：**概述**頁上提供的終結點|

[!INCLUDE [Gathering required parameters](../containers/includes/container-gathering-required-parameters.md)]

### <a name="the-host-computer"></a>主機電腦

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="container-requirements-and-recommendations"></a>容器的需求和建議

[!INCLUDE [Container requirements and recommendations](includes/container-requirements-and-recommendations.md)]

## <a name="connect-to-the-kubernetes-cluster"></a>連接到庫伯內特斯群集

主機應具有可用的庫伯內斯群集。 有關[部署 Kubernetes 群集](../../aks/tutorial-kubernetes-deploy-cluster.md)的本教程，瞭解如何將 Kubernetes 群集部署到主機的概念性理解。

### <a name="sharing-docker-credentials-with-the-kubernetes-cluster"></a>與庫伯內斯群集共用 Docker 憑據

要允許 Kubernetes 群集`docker pull`從`containerpreview.azurecr.io`容器註冊表到配置的映射，您需要將 Docker 憑據傳輸到群集中。 執行下面的[`kubectl create`][kubectl-create]命令，根據從容器註冊表訪問先決條件提供的憑據創建*Docker 註冊表金鑰*。

從您選擇的命令列介面中，運行以下命令。 請確保將 和`<username>``<password>``<email-address>`替換為容器註冊表憑據。

```console
kubectl create secret docker-registry containerpreview \
    --docker-server=containerpreview.azurecr.io \
    --docker-username=<username> \
    --docker-password=<password> \
    --docker-email=<email-address>
```

> [!NOTE]
> 如果您已經有權訪問容器註冊表，`containerpreview.azurecr.io`則可以使用泛型標誌創建 Kubernetes 機密。 請考慮以下針對 Docker 配置 JSON 執行的命令。
> ```console
>  kubectl create secret generic containerpreview \
>      --from-file=.dockerconfigjson=~/.docker/config.json \
>      --type=kubernetes.io/dockerconfigjson
> ```

成功創建機密後，將以下輸出列印到主控台。

```console
secret "containerpreview" created
```

要驗證已創建機密，請使用[`kubectl get`][kubectl-get]`secrets`標誌執行 。

```console
kubectl get secrets
```

執行列印`kubectl get secrets`列印的所有已配置的機密。

```console
NAME                  TYPE                                  DATA      AGE
containerpreview      kubernetes.io/dockerconfigjson        1         30s
```

## <a name="configure-helm-chart-values-for-deployment"></a>配置用於部署的 Helm 圖表值

首先創建名為*read*的資料夾，然後將以下 YAML 內容粘貼到名為*Chart.yml*的新檔中。

```yaml
apiVersion: v1
name: read
version: 1.0.0
description: A Helm chart to deploy the microsoft/cognitive-services-read to a Kubernetes cluster
```

要配置 Helm 圖表預設值，請將以下 YAML 複製並粘貼到名為`values.yaml`的檔中。 將`# {ENDPOINT_URI}`和`# {API_KEY}`注釋替換為您自己的值。

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
> 如果未提供`billing``apikey`和 值，則服務將在 15 分鐘後過期。 同樣，驗證將失敗，因為服務將不可用。

在*讀取*目錄下創建*範本*資料夾。 複製以下 YAML 並將其粘貼到名為`deployment.yaml`的檔中。 該檔`deployment.yaml`將用作 Helm 範本。

> 範本生成清單檔，這是 Kubernetes 可以理解的 YAML 格式的資源描述。 [- 頭盔圖範本指南][chart-template-guide]

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

該範本指定負載等化器服務以及用於讀取的容器/映射的部署。

### <a name="the-kubernetes-package-helm-chart"></a>庫伯內斯包（赫爾姆圖）

*Helm 圖表*包含從容器註冊表中提取的 Docker 映射的`containerpreview.azurecr.io`配置。

> [Helm 圖表][helm-charts]是描述一組相關的庫伯奈斯資源的檔的集合。 單個圖表可用於部署簡單內容，如 memcached pod 或複雜內容，例如包含 HTTP 伺服器、資料庫、緩存等的完整 Web 應用堆疊。

提供的*Helm 圖表*從`containerpreview.azurecr.io`容器註冊表中提取電腦視覺服務的 Docker 映射和相應的服務。

## <a name="install-the-helm-chart-on-the-kubernetes-cluster"></a>在庫伯內斯群集上安裝赫爾姆圖

要安裝*掌舵圖*，我們需要執行該[`helm install`][helm-install-cmd]命令。 確保從`read`資料夾上方的目錄中執行安裝命令。

```console
helm install read ./read
```

下面是一個示例輸出，您可能期望從成功的安裝執行中看到：

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

庫伯內斯的部署可能需要幾分鐘才能完成。 要確認 pod 和服務都已正確部署且可用，請執行以下命令：

```console
kubectl get all
```

您應該會看到類似于以下輸出的內容：

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

有關在 Azure 庫伯奈斯服務 （AKS） 中使用 Helm 安裝應用程式的更多詳細資訊，[請訪問此處][installing-helm-apps-in-aks]。

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
