---
title: 使用語音服務容器搭配 Kubernetes 和 Helm
titleSuffix: Azure Cognitive Services
description: 使用 Kubernetes 和 Helm 來定義語音轉換文字和文字轉換語音的容器映射，我們將建立 Kubernetes 套件。 此套件將會部署到內部部署的 Kubernetes 叢集。
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/09/2020
ms.author: dapine
ms.openlocfilehash: cd4ff97902b1ce3d1d5a0ea066608fd33e6bf697
ms.sourcegitcommit: b8d0d72dfe8e26eecc42e0f2dbff9a7dd69d3116
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/10/2020
ms.locfileid: "79037090"
---
# <a name="use-speech-service-containers-with-kubernetes-and-helm"></a>使用語音服務容器搭配 Kubernetes 和 Helm

管理內部部署語音容器的一個選項是使用 Kubernetes 和 Helm。 使用 Kubernetes 和 Helm 來定義語音轉換文字和文字轉換語音的容器映射，我們將建立 Kubernetes 套件。 此套件將會部署到內部部署的 Kubernetes 叢集。 最後，我們將探討如何測試已部署的服務和各種設定選項。 如需在沒有 Kubernetes 協調流程的情況下執行 Docker 容器的詳細資訊，請參閱[安裝和執行語音服務容器](speech-container-howto.md)。

## <a name="prerequisites"></a>Prerequisites

在內部部署使用語音容器之前，請先遵循下列必要條件：

|必要|目的|
|--|--|
| Azure 帳戶 | 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶][free-azure-account]。 |
| 容器登錄存取 | 為了讓 Kubernetes 將 docker 映射提取到叢集，它需要容器登錄的存取權。 |
| Kubernetes CLI | 需要[KUBERNETES CLI][kubernetes-cli] ，才能從容器登錄中管理共用認證。 Helm 之前也需要 Kubernetes，這是 Kubernetes 套件管理員。 |
| Helm CLI | 在[HELM CLI][helm-install]安裝過程中，您也需要初始化 Helm，這將會安裝[Tiller][tiller-install]。 |
|語音資源 |若要使用這些容器，您必須具備：<br><br>用來取得相關聯計費金鑰和計費端點 URI 的_語音_Azure 資源。 這兩個值都可在 Azure 入口網站的 [**語音**總覽] 和 [金鑰] 頁面上取得，而且必須要有才能啟動容器。<br><br>**{API_KEY}** ：資源金鑰<br><br>**{ENDPOINT_URI}** ：端點 URI 範例為： `https://westus.api.cognitive.microsoft.com/sts/v1.0`|

## <a name="the-recommended-host-computer-configuration"></a>建議的主機電腦設定

如需參考，請參閱[語音服務容器主機電腦][speech-container-host-computer]詳細資料。 此*helm 圖*會根據使用者指定的解碼（並行要求數目），自動計算 CPU 和記憶體需求。 此外，它也會根據音訊/文字輸入的優化是否設定為 `enabled`進行調整。 Helm 圖預設為、兩個並行要求和停用優化。

| 服務 | CPU/容器 | 記憶體/容器 |
|--|--|--|
| **語音轉文字** | 一個解碼器至少需要 1150 millicore。 如果已啟用 `optimizedForAudioFile`，則需要 1950 millicore。 （預設值：兩個解碼器） | 必要： 2 GB<br>限制： 4 GB |
| **文字轉換語音** | 一個並行要求至少需要 500 millicore。 如果已啟用 `optimizeForTurboMode`，則需要 1000 millicore。 （預設值：兩個並行要求） | 必要： 1 GB<br> 限制： 2 GB |

## <a name="connect-to-the-kubernetes-cluster"></a>連接到 Kubernetes 叢集

主機電腦預期會有可用的 Kubernetes 叢集。 請參閱本教學課程以瞭解如何[部署 Kubernetes](../../aks/tutorial-kubernetes-deploy-cluster.md)叢集，以瞭解如何將 Kubernetes 叢集部署到主機電腦的概念。

### <a name="sharing-docker-credentials-with-the-kubernetes-cluster"></a>與 Kubernetes 叢集共用 Docker 認證

若要允許 Kubernetes 叢集從 `containerpreview.azurecr.io` 容器登錄中 `docker pull` 設定的映射，您需要將 docker 認證傳輸到叢集。 執行下列[`kubectl create`][kubectl-create]命令，根據容器登錄存取必要條件中提供的認證建立*docker 登錄密碼*。

從您選擇的命令列介面，執行下列命令。 請務必將 `<username>`、`<password>`和 `<email-address>` 取代為 container registry 認證。

```console
kubectl create secret docker-registry mcr \
    --docker-server=containerpreview.azurecr.io \
    --docker-username=<username> \
    --docker-password=<password> \
    --docker-email=<email-address>
```

> [!NOTE]
> 如果您已經有 `containerpreview.azurecr.io` 容器登錄的存取權，您可以改為使用一般旗標來建立 Kubernetes 秘密。 請考慮下列會針對您的 Docker 設定 JSON 執行的命令。
> ```console
>  kubectl create secret generic mcr \
>      --from-file=.dockerconfigjson=~/.docker/config.json \
>      --type=kubernetes.io/dockerconfigjson
> ```

成功建立秘密之後，會將下列輸出列印到主控台。

```console
secret "mcr" created
```

若要確認是否已建立密碼，請使用 `secrets` 旗標來執行[`kubectl get`][kubectl-get] 。

```console
kubectl get secrets
```

執行 `kubectl get secrets` 會列印所有設定的秘密。

```console
NAME    TYPE                              DATA    AGE
mcr     kubernetes.io/dockerconfigjson    1       30s
```

## <a name="configure-helm-chart-values-for-deployment"></a>設定部署的 Helm 圖表值

請造訪[Microsoft Helm Hub][ms-helm-hub] ，取得 microsoft 所提供的所有公開上市 Helm 圖表。 您可以從 Microsoft Helm 中樞找到**認知服務的語音內部部署圖表**。 **認知服務 Speech 內部部署**是我們將安裝的圖表，但我們必須先建立具有明確設定的 `config-values.yaml` 檔案。 讓我們從將 Microsoft 存放庫新增至我們的 Helm 實例開始。

```console
helm repo add microsoft https://microsoft.github.io/charts/repo
```

接下來，我們將設定我們的 Helm 圖表值。 將下列 YAML 複製並貼到名為 `config-values.yaml`的檔案中。 如需自訂**認知服務 Speech 內部部署 Helm 圖表**的詳細資訊，請參閱[自訂 Helm 圖表](#customize-helm-charts)。 以您自己的值取代 `# {ENDPOINT_URI}` 和 `# {API_KEY}` 批註。

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
> 如果未提供 `billing` 和 `apikey` 值，服務會在15分鐘後到期。 同樣地，驗證將會失敗，因為服務將無法使用。

### <a name="the-kubernetes-package-helm-chart"></a>Kubernetes 套件（Helm 圖）

*Helm 圖表*包含要從 `containerpreview.azurecr.io` 容器登錄中提取之 docker 映射的設定。

> [Helm 圖][helm-charts]是描述一組相關 Kubernetes 資源的檔案集合。 單一圖表可能用來部署一些簡單的東西，像是 memcached pod 或複雜的東西，像是具有 HTTP 伺服器、資料庫、快取等的完整 web 應用程式堆疊。

提供的*Helm 圖表*會從 `containerpreview.azurecr.io` 容器登錄中，提取語音服務的 docker 映射，包括文字轉換語音和語音轉換文字服務。

## <a name="install-the-helm-chart-on-the-kubernetes-cluster"></a>在 Kubernetes 叢集上安裝 Helm 圖表

若要安裝*helm 圖*，我們必須執行[`helm install`][helm-install-cmd]命令，以適當的路徑和檔案名引數取代 `<config-values.yaml>`。 以下所參考的 `microsoft/cognitive-services-speech-onpremise` Helm 圖可在[這裡的 Microsoft Helm Hub][ms-helm-hub-speech-chart]取得。

```console
helm install onprem-speech microsoft/cognitive-services-speech-onpremise \
    --version 0.1.1 \
    --values <config-values.yaml> 
```

以下是您可能預期會從成功的安裝執行中看到的範例輸出：

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

Kubernetes 部署可能需要數分鐘的時間才能完成。 若要確認 pod 和服務都已正確部署並可供使用，請執行下列命令：

```console
kubectl get all
```

您應該會看到類似下列輸出的內容：

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

### <a name="verify-helm-deployment-with-helm-tests"></a>使用 Helm 測試來驗證 Helm 部署

已安裝的 Helm 圖表會定義*Helm 測試*，做為驗證的便利性。 這些測試會驗證服務的就緒程度。 若要驗證**語音轉換文字**和**文字轉換語音**服務，我們會執行[Helm 測試][helm-test]命令。

```console
helm test onprem-speech
```

> [!IMPORTANT]
> 如果 POD 狀態不是 `Running` 或部署未列在 [`AVAILABLE`] 資料行之下，這些測試將會失敗。 請耐心等候，這可能需要十分鐘的時間才能完成。

這些測試會輸出各種狀態結果：

```console
RUNNING: speech-to-text-readiness-test
PASSED: speech-to-text-readiness-test
RUNNING: text-to-speech-readiness-test
PASSED: text-to-speech-readiness-test
```

除了執行*helm 測試*以外，您還可以從 `kubectl get all` 命令收集*外部 IP*位址和對應的埠。 使用 IP 和埠，開啟網頁瀏覽器並流覽至 `http://<external-ip>:<port>:/swagger/index.html` 以查看 API swagger 頁面。

## <a name="customize-helm-charts"></a>自訂 Helm 圖表

Helm 圖表是階層式的。 階層式允許圖表繼承，它也會已經考慮到明確的概念，其中較特定的設定會覆寫繼承的規則。

[!INCLUDE [Speech umbrella-helm-chart-config](includes/speech-umbrella-helm-chart-config.md)]

[!INCLUDE [Speech-to-Text Helm Chart Config](includes/speech-to-text-chart-config.md)]

[!INCLUDE [Text-to-Speech Helm Chart Config](includes/text-to-speech-chart-config.md)]

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
