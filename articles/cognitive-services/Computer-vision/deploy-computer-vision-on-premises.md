---
title: 搭配 Kubernetes 和 Helm 使用電腦視覺容器
titleSuffix: Azure Cognitive Services
description: 瞭解如何使用 Kubernetes 和 Helm 部署電腦視覺容器。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 01/27/2020
ms.author: aahi
ms.openlocfilehash: 8f795a7c0d8a2bac09c588924b0fd87e62258da3
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/28/2021
ms.locfileid: "98946183"
---
# <a name="use-computer-vision-container-with-kubernetes-and-helm"></a>搭配 Kubernetes 和 Helm 使用電腦視覺容器

管理內部部署電腦視覺容器的選項之一，是使用 Kubernetes 和 Helm。 使用 Kubernetes 和 Helm 來定義電腦視覺的容器映射，我們將建立 Kubernetes 套件。 此套件將會部署到內部部署的 Kubernetes 叢集。 最後，我們將探討如何測試已部署的服務。 如需在沒有 Kubernetes 協調流程的情況下執行 Docker 容器的詳細資訊，請參閱 [安裝和執行電腦視覺容器](computer-vision-how-to-install-containers.md)。

## <a name="prerequisites"></a>先決條件

使用內部部署電腦視覺容器之前的必要條件如下：

| 必要 | 目的 |
|----------|---------|
| Azure 帳戶 | 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶][free-azure-account]。 |
| Kubernetes CLI | 需要 [KUBERNETES CLI][kubernetes-cli] 才能管理容器登錄中的共用認證。 Helm 之前也需要 Kubernetes，也就是 Kubernetes 套件管理員。 |
| Helm CLI | 安裝 [HELM CLI][helm-install]，這是用來安裝 Helm 圖表 (容器套件定義) 。 |
| 電腦視覺資源 |若要使用此容器，您必須具備：<br><br>Azure **電腦視覺** 資源和相關聯的 API 金鑰（端點 URI）。 這兩個值都可在資源的 [總覽] 和 [金鑰] 頁面上取得，而且必須要有這些值才能啟動容器。<br><br>**{API_KEY}**： [ **金鑰** ] 頁面上兩個可用資源金鑰的其中一個<br><br>**{ENDPOINT_URI}**： **總覽** 頁面上提供的端點|

[!INCLUDE [Gathering required parameters](../containers/includes/container-gathering-required-parameters.md)]

### <a name="the-host-computer"></a>主機電腦

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="container-requirements-and-recommendations"></a>容器的需求和建議

[!INCLUDE [Container requirements and recommendations](includes/container-requirements-and-recommendations.md)]

## <a name="connect-to-the-kubernetes-cluster"></a>連線至 Kubernetes 叢集

主機電腦預期會有可用的 Kubernetes 叢集。 請參閱本教學課程以瞭解如何部署 [Kubernetes](../../aks/tutorial-kubernetes-deploy-cluster.md) 叢集，以瞭解如何將 Kubernetes 叢集部署到主機電腦。 您可以在 [Kubernetes 檔](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/)中找到有關部署的詳細資訊。

## <a name="configure-helm-chart-values-for-deployment"></a>設定部署的 Helm 圖表值

首先，建立名為 *read* 的資料夾。 然後，在名為的新檔案中貼上下列 YAML 內容 `chart.yaml` ：

```yaml
apiVersion: v2
name: read
version: 1.0.0
description: A Helm chart to deploy the Read OCR container to a Kubernetes cluster
dependencies:
- name: rabbitmq
  condition: read.image.args.rabbitmq.enabled
  version: ^6.12.0
  repository: https://kubernetes-charts.storage.googleapis.com/
- name: redis
  condition: read.image.args.redis.enabled
  version: ^6.0.0
  repository: https://kubernetes-charts.storage.googleapis.com/
```

若要設定 Helm 圖表的預設值，請將下列 YAML 複製並貼到名為的檔案中 `values.yaml` 。 `# {ENDPOINT_URI}` `# {API_KEY}` 以您自己的值取代和批註。 如有需要，請設定 resultExpirationPeriod、Redis 和 RabbitMQ。

```yaml
# These settings are deployment specific and users can provide customizations
read:
  enabled: true
  image:
    name: cognitive-services-read
    registry:  mcr.microsoft.com/
    repository: azure-cognitive-services/vision/read
    tag: 3.2-preview.1
    args:
      eula: accept
      billing: # {ENDPOINT_URI}
      apikey: # {API_KEY}
      
      # Result expiration period setting. Specify when the system should clean up recognition results.
      # For example, resultExpirationPeriod=1, the system will clear the recognition result 1hr after the process.
      # resultExpirationPeriod=0, the system will clear the recognition result after result retrieval.
      resultExpirationPeriod: 1
      
      # Redis storage, if configured, will be used by read container to store result records.
      # A cache is required if multiple read containers are placed behind load balancer.
      redis:
        enabled: false # {true/false}
        password: password

      # RabbitMQ is used for dispatching tasks. This can be useful when multiple read containers are
      # placed behind load balancer.
      rabbitmq:
        enabled: false # {true/false}
        rabbitmq:
          username: user
          password: password
```

> [!IMPORTANT]
> - 如果 `billing` `apikey` 未提供和值，則服務會在15分鐘後到期。 同樣地，驗證也會失敗，因為服務無法使用。
> 
> - 如果您在負載平衡器後方部署多個讀取容器，例如 Docker Compose 或 Kubernetes 下，您必須有外部快取。 因為處理容器和 GET 要求容器可能不相同，所以外部快取會儲存結果，並在容器之間共用這些結果。 如需快取設定的詳細資訊，請參閱 [設定電腦視覺 Docker 容器](./computer-vision-resource-container-config.md)。
>

在 [*讀取*] 目錄下建立 [*範本*] 資料夾。 將下列 YAML 複製並貼到名為的檔案中 `deployment.yaml` 。 檔案 `deployment.yaml` 將作為 Helm 範本。

> 範本會產生資訊清單檔案，這些是 Kubernetes 可理解的 YAML 格式資源描述。 [-Helm 圖表範本指南][chart-template-guide]

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: read
  labels:
    app: read-deployment
spec:
  selector:
    matchLabels:
      app: read-app
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
        args:        
        - ReadEngineConfig:ResultExpirationPeriod={{ .Values.read.image.args.resultExpirationPeriod }}
        {{- if .Values.read.image.args.rabbitmq.enabled }}
        - Queue:RabbitMQ:HostName={{ include "rabbitmq.hostname" . }}
        - Queue:RabbitMQ:Username={{ .Values.read.image.args.rabbitmq.rabbitmq.username }}
        - Queue:RabbitMQ:Password={{ .Values.read.image.args.rabbitmq.rabbitmq.password }}
        {{- end }}      
        {{- if .Values.read.image.args.redis.enabled }}
        - Cache:Redis:Configuration={{ include "redis.connStr" . }}
        {{- end }}
      imagePullSecrets:
      - name: {{.Values.read.image.pullSecret}}      
--- 
apiVersion: v1
kind: Service
metadata:
  name: read-service
spec:
  type: LoadBalancer
  ports:
  - port: 5000
  selector:
    app: read-app
```

在相同的 *範本* 資料夾中，複製下列 helper 函式並貼到中 `helpers.tpl` 。 `helpers.tpl` 定義有助於產生 Helm 範本的實用函數。

> [!NOTE]
> 本文包含「從屬」一詞的參考，Microsoft 已不再使用該字詞。 從軟體中移除該字詞時，我們也會將其從本文中移除。

```yaml
{{- define "rabbitmq.hostname" -}}
{{- printf "%s-rabbitmq" .Release.Name -}}
{{- end -}}

{{- define "redis.connStr" -}}
{{- $hostMain := printf "%s-redis-master:6379" .Release.Name }}
{{- $hostReplica := printf "%s-redis-slave:6379" .Release.Name -}}
{{- $passWord := printf "password=%s" .Values.read.image.args.redis.password -}}
{{- $connTail := "ssl=False,abortConnect=False" -}}
{{- printf "%s,%s,%s,%s" $hostMain $hostReplica $passWord $connTail -}}
{{- end -}}
```
範本會指定負載平衡器服務，以及要讀取的容器/映射的部署。

### <a name="the-kubernetes-package-helm-chart"></a>Kubernetes 封裝 (Helm 圖表) 

*Helm 圖* 包含要從容器登錄中提取哪些 docker 映射 () 的設定 `mcr.microsoft.com` 。

> [Helm 圖][helm-charts]是描述一組相關 Kubernetes 資源的檔案集合。 您可以使用單一圖表來部署簡單的程式（例如 memcached pod）或複雜的內容，例如包含 HTTP 伺服器、資料庫、快取等的完整 web 應用程式堆疊。

提供的 *Helm 圖表* 會從容器登錄提取電腦視覺服務的 docker 映射，以及對應的服務 `mcr.microsoft.com` 。

## <a name="install-the-helm-chart-on-the-kubernetes-cluster"></a>在 Kubernetes 叢集上安裝 Helm 圖表

若要安裝 *helm 圖*，我們必須執行 [`helm install`][helm-install-cmd] 命令。 請務必從資料夾上方的目錄執行安裝命令 `read` 。

```console
helm install read ./read
```

以下是您預期會在成功安裝執行時看到的範例輸出：

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

Kubernetes 部署可能需要幾分鐘的時間才能完成。 若要確認 pod 和服務都已正確部署且可供使用，請執行下列命令：

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

## <a name="deploy-multiple-v3-containers-on-the-kubernetes-cluster"></a>在 Kubernetes 叢集上部署多個 v3 容器

從容器的 v3 開始，您可以在工作和頁面層級平行使用容器。

根據設計，每個 v3 容器都有發送器和辨識背景工作。 發送器負責將多頁工作分割成多個單一頁面子工作。 辨識背景工作已針對辨識單一分頁檔進行優化。 若要達到頁面層級的平行處理原則，請在負載平衡器後方部署多個 v3 容器，並讓容器共用通用儲存體和佇列。 

> [!NOTE] 
> 目前僅支援 Azure 儲存體和 Azure 佇列。 

接收要求的容器可以將工作分割成單一頁面子工作，並將它們新增至通用佇列。 任何來自較不忙碌容器的辨識背景工作，都可以從佇列取用單一頁面子工作、執行辨識，然後將結果上傳至儲存體。 視部署的容器數目而定，輸送量可以提升至最多 `n` 時間。

V3 容器會公開路徑底下的活動探查 API `/ContainerLiveness` 。 使用下列部署範例來設定 Kubernetes 的活動探查。 

將下列 YAML 複製並貼到名為的檔案中 `deployment.yaml` 。 `# {ENDPOINT_URI}` `# {API_KEY}` 以您自己的值取代和批註。 將 `# {AZURE_STORAGE_CONNECTION_STRING}` 批註取代為您 Azure 儲存體的連接字串。 將 `replicas` 設定為您想要的數位，其會 `3` 在下列範例中設定為。

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: read
  labels:
    app: read-deployment
spec:
  selector:
    matchLabels:
      app: read-app
  replicas: # {NUMBER_OF_READ_CONTAINERS}
  template:
    metadata:
      labels:
        app: read-app
    spec:
      containers:
      - name: cognitive-services-read
        image: mcr.microsoft.com/azure-cognitive-services/vision/read
        ports:
        - containerPort: 5000
        env:
        - name: EULA
          value: accept
        - name: billing
          value: # {ENDPOINT_URI}
        - name: apikey
          value: # {API_KEY}
        - name: Storage__ObjectStore__AzureBlob__ConnectionString
          value: # {AZURE_STORAGE_CONNECTION_STRING}
        - name: Queue__Azure__ConnectionString
          value: # {AZURE_STORAGE_CONNECTION_STRING}
        livenessProbe:
          httpGet:
            path: /ContainerLiveness
            port: 5000
          initialDelaySeconds: 60
          periodSeconds: 60
          timeoutSeconds: 20
--- 
apiVersion: v1
kind: Service
metadata:
  name: azure-cognitive-service-read
spec:
  type: LoadBalancer
  ports:
  - port: 5000
    targetPort: 5000
  selector:
    app: read-app
```

執行下列命令。 

```console
kubectl apply -f deployment.yaml
```

以下是您可能會在成功部署執行時看到的範例輸出：

```console
deployment.apps/read created
service/azure-cognitive-service-read created
```

Kubernetes 部署可能需要幾分鐘的時間才能完成。 若要確認 pod 和服務都已正確部署且可供使用，請執行下列命令：

```console
kubectl get all
```

您應該會看到類似下列的主控台輸出：

```console
kubectl get all
NAME                       READY   STATUS    RESTARTS   AGE
pod/read-6cbbb6678-58s9t   1/1     Running   0          3s
pod/read-6cbbb6678-kz7v4   1/1     Running   0          3s
pod/read-6cbbb6678-s2pct   1/1     Running   0          3s

NAME                                   TYPE           CLUSTER-IP   EXTERNAL-IP    PORT(S)          AGE
service/azure-cognitive-service-read   LoadBalancer   10.0.134.0   <none>         5000:30846/TCP   17h
service/kubernetes                     ClusterIP      10.0.0.1     <none>         443/TCP          78d

NAME                   READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/read   3/3     3            3           3s

NAME                             DESIRED   CURRENT   READY   AGE
replicaset.apps/read-6cbbb6678   3         3         3       3s
```

<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="next-steps"></a>後續步驟

如需在 Azure Kubernetes Service (AKS) 中使用 Helm 安裝應用程式的詳細資訊，請 [造訪這裡][installing-helm-apps-in-aks]。

> [!div class="nextstepaction"]
> [認知服務容器][cog-svcs-containers]

<!-- LINKS - external -->
[free-azure-account]: https://azure.microsoft.com/free
[git-download]: https://git-scm.com/downloads
[azure-cli]: /cli/azure/install-azure-cli?view=azure-cli-latest
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
