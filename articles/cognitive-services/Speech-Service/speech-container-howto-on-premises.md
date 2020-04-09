---
title: 使用具有庫伯奈斯和赫爾姆的語音服務容器
titleSuffix: Azure Cognitive Services
description: 使用 Kubernetes 和 Helm 定義語音到文本和文字到語音的容器映射,我們將創建一個 Kubernetes 包。 此包將部署到本地的 Kubernetes 群集。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 3c183f6d0e2d80ed497654448a726a1562bd046c
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/08/2020
ms.locfileid: "80874331"
---
# <a name="use-speech-service-containers-with-kubernetes-and-helm"></a>使用具有庫伯奈斯和赫爾姆的語音服務容器

本地管理語音容器的一個選項是使用庫伯內特斯和赫爾姆。 使用 Kubernetes 和 Helm 定義語音到文本和文字到語音的容器映射,我們將創建一個 Kubernetes 包。 此包將部署到本地的 Kubernetes 群集。 最後,我們將探討如何測試已部署的服務和各種配置選項。 有關在沒有 Kubernetes 業務流程的情況下執行 Docker 容器的詳細資訊,請參閱[安裝和執行語音服務容器](speech-container-howto.md)。

## <a name="prerequisites"></a>Prerequisites

在本地使用語音容器之前,以下先決條件:

| 必要 | 目的 |
|----------|---------|
| Azure 帳戶 | 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶][free-azure-account]。 |
| 容器登錄 | 為了使 Kubernetes 將 Docker 映射拉入群集,它將需要存取容器註冊表。 |
| 庫伯內斯 CLI | [庫伯內斯 CLI][kubernetes-cli]是管理容器註冊表中的共用憑據所必需的。 在赫爾姆之前也需要庫伯內特斯,這是庫伯內斯的包經理。 |
| Helm CLI | 安裝[Helm CLI][helm-install],用於安裝掌舵圖(容器包定義)。 |
|語音資源 |若要使用這些容器，您必須具備：<br><br>用於獲取關聯的計費密鑰和計費終結點URI的_語音_Azure 資源。 這兩個值在 Azure 門戶的**語音**概述和密鑰頁上都可用,並且需要啟動容器。<br><br>**[API_KEY]**: 資源鍵<br><br>**[ENDPOINT_URI]**: 終結點 URI 範例是:`https://westus.api.cognitive.microsoft.com/sts/v1.0`|

## <a name="the-recommended-host-computer-configuration"></a>建議的主機設定

請參閱[語音服務容器主機計算機][speech-container-host-computer]詳細資訊作為參考。 此*掌舵圖*根據使用者指定的解碼(併發請求)數自動計算 CPU 和記憶體要求。 此外,它將根據音訊/文本輸入的優化是否配置為`enabled`進行調整。 掌舵圖預設為兩個併發請求和禁用優化。

| 服務 | CPU / 容器 | 記憶體/容器 |
|--|--|--|
| **語音轉文字** | 一個解碼器至少需要 1,150 毫芯。 如果啟用`optimizedForAudioFile`,則需要 1,950 毫芯。 (預設值:兩個解碼器) | 需要: 2 GB<br>受限: 4 GB |
| **文字轉換語音** | 一個併發請求至少需要 500 毫芯。 如果啟用`optimizeForTurboMode`,則需要 1,000 毫芯。 (預設值:兩個併發請求) | 需要: 1 GB<br> 受限: 2 GB |

## <a name="connect-to-the-kubernetes-cluster"></a>連接到庫伯內特斯群集

主機應具有可用的庫伯內斯群集。 有關[部署 Kubernetes 群集](../../aks/tutorial-kubernetes-deploy-cluster.md)的本教程,瞭解如何將 Kubernetes 群集部署到主機的概念性理解。

### <a name="sharing-docker-credentials-with-the-kubernetes-cluster"></a>與庫伯內斯群集共用 Docker 認證

要允許 Kubernetes`docker pull`群`containerpreview.azurecr.io`集 從 容器註冊表到配置的映射,您需要將 Docker 認證到群集中。 執行下面的[`kubectl create`][kubectl-create]指令,根據從容器註冊表存取先決條件提供的認證為*Docker 註冊表金鑰*。

從您選擇的命令列介面中,執行以下命令。 請確保將和`<username>``<password>``<email-address>`替換為容器註冊表憑據。

```console
kubectl create secret docker-registry mcr \
    --docker-server=containerpreview.azurecr.io \
    --docker-username=<username> \
    --docker-password=<password> \
    --docker-email=<email-address>
```

> [!NOTE]
> 如果您已經有權存取容器註冊表`containerpreview.azurecr.io`, 則可以使用泛型標誌創建 Kubernetes 機密。 請考慮以下針對 Docker 配置 JSON 執行的命令。
> ```console
>  kubectl create secret generic mcr \
>      --from-file=.dockerconfigjson=~/.docker/config.json \
>      --type=kubernetes.io/dockerconfigjson
> ```

成功創建機密後,將以下輸出列印到主控台。

```console
secret "mcr" created
```

要驗證已建立機密,請使用[`kubectl get`][kubectl-get]`secrets`旗標執行 。

```console
kubectl get secrets
```

執行列印`kubectl get secrets`列印的所有已配置的機密。

```console
NAME    TYPE                              DATA    AGE
mcr     kubernetes.io/dockerconfigjson    1       30s
```

## <a name="configure-helm-chart-values-for-deployment"></a>設定用於部署的 Helm 圖表值

訪問[微軟頭盔中心][ms-helm-hub],獲取微軟提供的所有公開提供的掌舵圖。 在 Microsoft Helm Hub 中,您會找到**認知服務語音本機圖表**。 **本地認知服務語音**是我們將安裝的圖表,但我們必須首先創建`config-values.yaml`具有 顯式配置的檔。 讓我們首先將Microsoft儲存庫添加到我們的Helm實例中。

```console
helm repo add microsoft https://microsoft.github.io/charts/repo
```

接下來,我們將配置 Helm 圖表值。 複製以下 YAML 並將其貼上`config-values.yaml`到名為的檔案中。 有關自定義**認知服務語音本地頭盔圖**的詳細資訊,請參閱[自定義掌舵圖](#customize-helm-charts)。 將`# {ENDPOINT_URI}`和`# {API_KEY}`註釋替換為您自己的值。

```yaml
# These settings are deployment specific and users can provide customizations

# speech-to-text configurations
speechToText:
  enabled: true
  numberOfConcurrentRequest: 3
  optimizeForAudioFile: true
  image:
    registry: containerpreview.azurecr.io
    repository: microsoft/cognitive-services-speech-to-text
    tag: latest
    pullSecrets:
      - mcr # Or an existing secret
    args:
      eula: accept
      billing: # {ENDPOINT_URI}
      apikey: # {API_KEY}

# text-to-speech configurations
textToSpeech:
  enabled: true
  numberOfConcurrentRequest: 3
  optimizeForTurboMode: true
  image:
    registry: containerpreview.azurecr.io
    repository: microsoft/cognitive-services-text-to-speech
    tag: latest
    pullSecrets:
      - mcr # Or an existing secret
    args:
      eula: accept
      billing: # {ENDPOINT_URI}
      apikey: # {API_KEY}
```

> [!IMPORTANT]
> 如果未提供`billing``apikey`和值,則服務將在 15 分鐘後過期。 同樣,驗證將失敗,因為服務將不可用。

### <a name="the-kubernetes-package-helm-chart"></a>庫伯內斯包(赫爾姆圖)

*Helm 圖表*包含從容器註冊錶中提取的 Docker`containerpreview.azurecr.io`映射的 配置。

> [Helm 圖表][helm-charts]是描述一組相關的庫伯奈斯資源的檔的集合。 單個圖表可用於部署簡單內容,如 memcached pod 或複雜內容,例如包含 HTTP 伺服器、資料庫、緩存等的完整 Web 應用堆疊。

提供的*Helm 圖表*從`containerpreview.azurecr.io`容器註冊表中提取語音服務的 Docker 圖像,包括文本到語音和語音到文本服務。

## <a name="install-the-helm-chart-on-the-kubernetes-cluster"></a>在庫伯內斯群集上安裝赫爾姆圖

要安裝*掌舵圖*,我們[`helm install`][helm-install-cmd]需要執行 此指令,用相應的`<config-values.yaml>`路徑和檔案 名參數取代 。 下面`microsoft/cognitive-services-speech-onpremise`引用的赫爾姆圖表[可在此處的 Microsoft 頭盔中心上][ms-helm-hub-speech-chart]查閱。

```console
helm install onprem-speech microsoft/cognitive-services-speech-onpremise \
    --version 0.1.1 \
    --values <config-values.yaml> 
```

下面是一個範例輸出,您可能期望從成功的安裝執行中看到:

```console
NAME:   onprem-speech
LAST DEPLOYED: Tue Jul  2 12:51:42 2019
NAMESPACE: default
STATUS: DEPLOYED

RESOURCES:
==> v1/Pod(related)
NAME                             READY  STATUS             RESTARTS  AGE
speech-to-text-7664f5f465-87w2d  0/1    Pending            0         0s
speech-to-text-7664f5f465-klbr8  0/1    ContainerCreating  0         0s
text-to-speech-56f8fb685b-4jtzh  0/1    ContainerCreating  0         0s
text-to-speech-56f8fb685b-frwxf  0/1    Pending            0         0s

==> v1/Service
NAME            TYPE          CLUSTER-IP    EXTERNAL-IP  PORT(S)       AGE
speech-to-text  LoadBalancer  10.0.252.106  <pending>    80:31811/TCP  1s
text-to-speech  LoadBalancer  10.0.125.187  <pending>    80:31247/TCP  0s

==> v1beta1/PodDisruptionBudget
NAME                                MIN AVAILABLE  MAX UNAVAILABLE  ALLOWED DISRUPTIONS  AGE
speech-to-text-poddisruptionbudget  N/A            20%              0                    1s
text-to-speech-poddisruptionbudget  N/A            20%              0                    1s

==> v1beta2/Deployment
NAME            READY  UP-TO-DATE  AVAILABLE  AGE
speech-to-text  0/2    2           0          0s
text-to-speech  0/2    2           0          0s

==> v2beta2/HorizontalPodAutoscaler
NAME                       REFERENCE                  TARGETS        MINPODS  MAXPODS  REPLICAS  AGE
speech-to-text-autoscaler  Deployment/speech-to-text  <unknown>/50%  2        10       0         0s
text-to-speech-autoscaler  Deployment/text-to-speech  <unknown>/50%  2        10       0         0s


NOTES:
cognitive-services-speech-onpremise has been installed!
Release is named onprem-speech
```

庫伯內斯的部署可能需要幾分鐘才能完成。 要確認 pod 和服務都已正確部署且可用,請執行以下命令:

```console
kubectl get all
```

您應該會看到類似於以下輸出的內容:

```console
NAME                                  READY     STATUS    RESTARTS   AGE
pod/speech-to-text-7664f5f465-87w2d   1/1       Running   0          34m
pod/speech-to-text-7664f5f465-klbr8   1/1       Running   0          34m
pod/text-to-speech-56f8fb685b-4jtzh   1/1       Running   0          34m
pod/text-to-speech-56f8fb685b-frwxf   1/1       Running   0          34m

NAME                     TYPE           CLUSTER-IP     EXTERNAL-IP      PORT(S)        AGE
service/kubernetes       ClusterIP      10.0.0.1       <none>           443/TCP        3h
service/speech-to-text   LoadBalancer   10.0.252.106   52.162.123.151   80:31811/TCP   34m
service/text-to-speech   LoadBalancer   10.0.125.187   65.52.233.162    80:31247/TCP   34m

NAME                             DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/speech-to-text   2         2         2            2           34m
deployment.apps/text-to-speech   2         2         2            2           34m

NAME                                        DESIRED   CURRENT   READY     AGE
replicaset.apps/speech-to-text-7664f5f465   2         2         2         34m
replicaset.apps/text-to-speech-56f8fb685b   2         2         2         34m

NAME                                                            REFERENCE                   TARGETS   MINPODS   MAXPODS   REPLICAS   AGE
horizontalpodautoscaler.autoscaling/speech-to-text-autoscaler   Deployment/speech-to-text   1%/50%    2         10        2          34m
horizontalpodautoscaler.autoscaling/text-to-speech-autoscaler   Deployment/text-to-speech   0%/50%    2         10        2          34m
```

### <a name="verify-helm-deployment-with-helm-tests"></a>使用 Helm 測試驗證 Helm 部署

安裝的Helm圖表定義了*Helm測試*,這為驗證提供了方便。 這些測試驗證服務就緒性。 要驗證**語音到文本**和**文本到語音服務**,我們將執行[Helm 測試][helm-test]命令。

```console
helm test onprem-speech
```

> [!IMPORTANT]
> 如果 POD 狀態未處於或`Running`部署未`AVAILABLE`列在 列下,則這些測試將失敗。 要有耐心,因為這可能需要十多分鐘才能完成。

這些測試將輸出各種狀態結果:

```console
RUNNING: speech-to-text-readiness-test
PASSED: speech-to-text-readiness-test
RUNNING: text-to-speech-readiness-test
PASSED: text-to-speech-readiness-test
```

作為執行*helm 測試*的替代方法,`kubectl get all`可以從命令收集外部*IP*位址和相應的埠。 使用 IP 和埠,打開 Web`http://<external-ip>:<port>:/swagger/index.html`瀏覽器並瀏覽到以檢視 API 搖擺頁。

## <a name="customize-helm-charts"></a>自訂頭盔圖表

赫爾姆圖是分層的。 分層允許圖表繼承,它還迎合了特定性的概念,其中更具體的設置重寫繼承了規則。

[!INCLUDE [Speech umbrella-helm-chart-config](includes/speech-umbrella-helm-chart-config.md)]

[!INCLUDE [Speech-to-Text Helm Chart Config](includes/speech-to-text-chart-config.md)]

[!INCLUDE [Text-to-Speech Helm Chart Config](includes/text-to-speech-chart-config.md)]

## <a name="next-steps"></a>後續步驟

有關在 Azure 函式庫伯奈斯服務 (AKS) 中使用 Helm 安裝應用程式的更多詳細資訊,[請存取此處][installing-helm-apps-in-aks]。

> [!div class="nextstepaction"]
> [認知服務容器][cog-svcs-containers]

<!-- LINKS - external -->
[free-azure-account]: https://azure.microsoft.com/free
[git-download]: https://git-scm.com/downloads
[azure-cli]: https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest
[docker-engine]: https://www.docker.com/products/docker-engine
[kubernetes-cli]: https://kubernetes.io/docs/tasks/tools/install-kubectl
[helm-install]: https://helm.sh/docs/intro/install/
[helm-install-cmd]: https://helm.sh/docs/intro/using_helm/#helm-install-installing-a-package
[tiller-install]: https://helm.sh/docs/install/#installing-tiller
[helm-charts]: https://helm.sh/docs/topics/charts/
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[helm-test]: https://v2.helm.sh/docs/helm/#helm-test
[ms-helm-hub]: https://hub.helm.sh/charts/microsoft
[ms-helm-hub-speech-chart]: https://hub.helm.sh/charts/microsoft/cognitive-services-speech-onpremise

<!-- LINKS - internal -->
[speech-container-host-computer]: speech-container-howto.md#the-host-computer
[installing-helm-apps-in-aks]: ../../aks/kubernetes-helm.md
[cog-svcs-containers]: ../cognitive-services-container-support.md
