---
title: 在 Azure Kubernetes Service (AKS) 中安裝 Istio
description: 了解如何在 Azure Kubernetes Service (AKS) 叢集中安裝和使用 Istio 以建立服務網格
author: paulbouwer
ms.topic: article
ms.date: 10/02/2020
ms.author: pabouwer
zone_pivot_groups: client-operating-system
ms.openlocfilehash: 285fa34db3886cf405a3682438a27a17c75d81ed
ms.sourcegitcommit: 67e8e1caa8427c1d78f6426c70bf8339a8b4e01d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/02/2020
ms.locfileid: "91666689"
---
# <a name="install-and-use-istio-in-azure-kubernetes-service-aks"></a>在 Azure Kubernetes Service (AKS) 中安裝和使用 Istio

[Istio][istio-github] 是一種開放原始碼服務網格，可在 Kubernetes 叢集中提供跨微服務的主要功能集。 這些功能包括流量管理、服務識別和安全性、原則強制執行和可檢視性。 如需 Istio 的詳細資訊，請參閱官方文件[什麼是 Istio？][istio-docs-concepts]。

本文說明如何安裝 Istio。 Istio `istioctl` 用戶端二進位檔會安裝到您的用戶端電腦上，而 Istio 元件則會安裝到 AKS 上的 Kubernetes 叢集中。

> [!NOTE]
> 下列指示參考 Istio 版本 `1.7.3` 。
>
> Istio 版本已 `1.7.x` 由 Istio 團隊針對 Kubernetes 版本進行測試 `1.16+` 。 您可以在 [GitHub Istio 版本][istio-github-releases]中找到其他 Istio 版本、 [Istio 新聞][istio-release-notes] 的每個版本的相關資訊，以及 [Istio 一般常見問題][istio-faq]的支援 Kubernetes 版本。

在本文中，您將學會如何：

> [!div class="checklist"]
> * 下載並安裝 Istio istioctl 用戶端二進位檔
> * 在 AKS 上安裝 Istio
> * 驗證 Istio 安裝
> * 存取附加元件
> * 從 AKS 卸載 Istio

## <a name="before-you-begin"></a>開始之前

本文中詳述的步驟假設您已建立 AKS 叢集 (Kubernetes 和更新版本 `1.16` ，並已啟用 RBAC) 並已建立與叢集的連線 `kubectl` 。 如果您需要前述任何方面的協助，請參閱 [AKS 快速入門][aks-quickstart]。

請確定您已閱讀 [Istio 效能和擴充性](https://istio.io/docs/concepts/performance-and-scalability/) 檔，以瞭解在 AKS 叢集中執行 Istio 的其他資源需求。 核心和記憶體需求會根據您的特定工作負載而有所不同。 選擇適當的節點數目和 VM 大小，以滿足您的設定。

本文將 Istio 安裝指引分成數個獨立的步驟。 最終結果的結構與官方的 Istio 安裝[指引][istio-install-istioctl]相同。

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Linux - download and install client binary](includes/servicemesh/istio/install-client-binary-linux.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [macOS - download and install client binary](includes/servicemesh/istio/install-client-binary-macos.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [Windows - download and install client binary](includes/servicemesh/istio/install-client-binary-windows.md)]

::: zone-end

## <a name="install-the-istio-operator-on-aks"></a>在 AKS 上安裝 Istio 操作員

Istio 提供 [操作員][istio-install-operator] 來管理 AKS 叢集中 Istio 元件的安裝和更新。 我們會使用用戶端二進位檔來安裝 Istio 操作員 `istioctl` 。

```bash
istioctl operator init
```

您應該會看到類似下列的輸出，以確認已安裝 Istio 操作員。

```console
Using operator Deployment image: docker.io/istio/operator:1.7.3
✔ Istio operator installed
✔ Installation complete
```

Istio 運算子會安裝到 `istio-operator` 命名空間中。 查詢命名空間。

```bash
kubectl get all -n istio-operator
```

您應該會看到下列元件已部署。

```console
NAME                                  READY   STATUS    RESTARTS   AGE
pod/istio-operator-6d7958b7bf-wxgdc   1/1     Running   0          2m43s

NAME                     TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)    AGE
service/istio-operator   ClusterIP   10.0.8.57    <none>        8383/TCP   2m43s

NAME                             READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/istio-operator   1/1     1            1           2m43s

NAME                                        DESIRED   CURRENT   READY   AGE
replicaset.apps/istio-operator-6d7958b7bf   1         1         1       2m43s
```

您可以深入瞭解運算子模式，以及它如何透過 [kubernetes.io][kubernetes-operator]來協助將複雜的工作自動化。


### <a name="install-istio-components"></a>安裝 Istio 元件

既然我們已成功在 AKS 叢集中安裝 Istio 操作員，現在就可以開始安裝 Istio 元件了。 

我們將利用 `default` [Istio 設定檔][istio-configuration-profiles] 來建立 [Istio 操作員規格][istio-control-plane]。

您可以執行下列 `istioctl` 命令來查看 `default` Istio 設定檔的設定。

```bash
istioctl profile dump default
```

> [!NOTE]
> Istio 目前必須排程在 Linux 節點上執行。 如果您的叢集中有 Windows Server 節點，您必須確定 Istio pod 只排程在 Linux 節點上執行。 我們將使用 [節點選取器][kubernetes-node-selectors] 來確定 pod 已排程至正確的節點。

> [!CAUTION]
> [ISTIO CNI][istio-feature-cni] Istio 功能目前是以[Alpha][istio-feature-stages]為，因此應該先提供考慮才能啟用這些功能。 

使用下列內容建立名為的檔案 `istio.aks.yaml` 。 此檔案將保留 [Istio 運算子規格][istio-control-plane] 來設定 Istio。

```yaml
apiVersion: install.istio.io/v1alpha1
kind: IstioOperator
metadata:
  namespace: istio-system
  name: istio-control-plane
spec:
  # Use the default profile as the base
  # More details at: https://istio.io/docs/setup/additional-setup/config-profiles/
  profile: default
  # Enable the addons that we will want to use
  addonComponents:
    grafana:
      enabled: true
    prometheus:
      enabled: true
    tracing:
      enabled: true
    kiali:
      enabled: true
  values:
    global:
      # Ensure that the Istio pods are only scheduled to run on Linux nodes
      defaultNodeSelector:
        beta.kubernetes.io/os: linux
    kiali:
      dashboard:
        auth:
          strategy: anonymous 
```

建立 `istio-system` 命名空間，並將 Istio Operator 規格部署到該命名空間。 Istio 運算子將會監看 Istio Operator 規格，並將其用來在您的 AKS 叢集中安裝和設定 Istio。

```bash
kubectl create ns istio-system

kubectl apply -f istio.aks.yaml 
```

至此，您已將 Istio 部署到您的 AKS 叢集。 為了確保能成功部署 Istio，讓我們繼續進行下一節以 [驗證 Istio 安裝](#validate-the-istio-installation)。

## <a name="validate-the-istio-installation"></a>驗證 Istio 安裝

查詢 `istio-system` 命名空間，此命名空間是由 Istio 運算子安裝的 Istio 和附加元件：

```bash
kubectl get all -n istio-system
```

您應該會看到下列元件：

- `istio*` -Istio 元件
- `jaeger-*`、 `tracing` 和 `zipkin` -追蹤附加元件
- `prometheus` -計量附加元件
- `grafana` -分析和監視儀表板附加元件
- `kiali` -服務網格儀表板附加元件

```console
NAME                                        READY   STATUS    RESTARTS   AGE
pod/grafana-7cf9794c74-mpfbp                1/1     Running   0          5m53s
pod/istio-ingressgateway-86b5dbdcb9-ndrp5   1/1     Running   0          5m57s
pod/istio-tracing-c98f4b8fc-zqklg           1/1     Running   0          82s
pod/istiod-6965c56995-4ph9h                 1/1     Running   0          6m15s
pod/kiali-7b44985d68-p87zh                  1/1     Running   0          81s
pod/prometheus-6868989549-5ghzz             1/1     Running   0          81s

NAME                                TYPE           CLUSTER-IP     EXTERNAL-IP    PORT(S)                                                      AGE
service/grafana                     ClusterIP      10.0.226.39    <none>         3000/TCP                                                     5m54s
service/istio-ingressgateway        LoadBalancer   10.0.143.56    20.53.72.254   15021:32166/TCP,80:31684/TCP,443:31302/TCP,15443:30863/TCP   5m57s
service/istiod                      ClusterIP      10.0.211.228   <none>         15010/TCP,15012/TCP,443/TCP,15014/TCP,853/TCP                6m16s
service/jaeger-agent                ClusterIP      None           <none>         5775/UDP,6831/UDP,6832/UDP                                   82s
service/jaeger-collector            ClusterIP      10.0.7.62      <none>         14267/TCP,14268/TCP,14250/TCP                                82s
service/jaeger-collector-headless   ClusterIP      None           <none>         14250/TCP                                                    82s
service/jaeger-query                ClusterIP      10.0.52.172    <none>         16686/TCP                                                    82s
service/kiali                       ClusterIP      10.0.71.179    <none>         20001/TCP                                                    82s
service/prometheus                  ClusterIP      10.0.171.151   <none>         9090/TCP                                                     82s
service/tracing                     ClusterIP      10.0.195.137   <none>         80/TCP                                                       82s
service/zipkin                      ClusterIP      10.0.136.111   <none>         9411/TCP                                                     82s

NAME                                   READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/grafana                1/1     1            1           5m54s
deployment.apps/istio-ingressgateway   1/1     1            1           5m58s
deployment.apps/istio-tracing          1/1     1            1           83s
deployment.apps/istiod                 1/1     1            1           6m16s
deployment.apps/kiali                  1/1     1            1           83s
deployment.apps/prometheus             1/1     1            1           82s

NAME                                              DESIRED   CURRENT   READY   AGE
replicaset.apps/grafana-7cf9794c74                1         1         1       5m54s
replicaset.apps/istio-ingressgateway-86b5dbdcb9   1         1         1       5m58s
replicaset.apps/istio-tracing-c98f4b8fc           1         1         1       83s
replicaset.apps/istiod-6965c56995                 1         1         1       6m16s
replicaset.apps/kiali-7b44985d68                  1         1         1       82s
replicaset.apps/prometheus-6868989549             1         1         1       82s

NAME                                                       REFERENCE                         TARGETS   MINPODS   MAXPODS   REPLICAS   AGE
horizontalpodautoscaler.autoscaling/istio-ingressgateway   Deployment/istio-ingressgateway   7%/80%    1         5         1          5m57s
horizontalpodautoscaler.autoscaling/istiod                 Deployment/istiod                 1%/80%    1         5         1          6m16s
```

您也可以藉由監看 Istio 操作員的記錄，來取得安裝的額外見解。

```bash
kubectl logs -n istio-operator -l name=istio-operator -f
```

如果 `istio-ingressgateway` 顯示外部 IP 是 `<pending>`，請等候幾分鐘，直到 Azure 網路指派了 IP 位址。

所有 pod 都應該會顯示狀態 `Running` 。 如果您的 Pod 沒有這些狀態，請等候一兩分鐘直到其成為該狀態。 如果有任何 Pod 回報發生問題，請使用 [kubectl describe pod][kubectl-describe] 命令檢閱其輸出和狀態。

## <a name="accessing-the-add-ons"></a>存取附加元件

Istio 運算子已安裝許多附加元件，可提供額外的功能。 附加元件的 web 應用程式 **不** 會經由外部 ip 位址公開。 

若要存取附加元件使用者介面，請使用 `istioctl dashboard` 命令。 此命令會使用 [kubectl 埠轉送][kubectl-port-forward] 和隨機埠，在您的用戶端電腦與 AKS 叢集中相關的 pod 之間建立安全連線。 然後，它會在您的預設瀏覽器中自動開啟附加元件 web 應用程式。

### <a name="grafana"></a>Grafana

Istio 的分析和監視儀表板是由 [Grafana][grafana]提供。 請記得在出現提示時，使用您稍早透過 Grafana 秘密建立的認證。 安全地開啟 Grafana 儀表板，如下所示：

```console
istioctl dashboard grafana
```

### <a name="prometheus"></a>Prometheus

Istio 的計量是由 [Prometheus][prometheus]提供。 安全地開啟 Prometheus 儀表板，如下所示：

```console
istioctl dashboard prometheus
```

### <a name="jaeger"></a>Jaeger

Istio 內的追蹤是由 [Jaeger][jaeger]提供。 安全地開啟 Jaeger 儀表板，如下所示：

```console
istioctl dashboard jaeger
```

### <a name="kiali"></a>Kiali

服務網格可檢視性儀表板會由 [Kiali][kiali] 來提供。 請記得在出現提示時，使用您稍早透過 Kiali 秘密建立的認證。 安全地開啟 Kiali 儀表板，如下所示：

```console
istioctl dashboard kiali
```

### <a name="envoy"></a>Envoy

提供 [Envoy][envoy] proxy 的簡單介面。 它提供在指定的 pod 中執行之 Envoy proxy 的設定資訊和計量。 安全地開啟 Envoy 介面，如下所示：

```console
istioctl dashboard envoy <pod-name>.<namespace>
```

## <a name="uninstall-istio-from-aks"></a>從 AKS 卸載 Istio

> [!WARNING]
> 從執行中系統刪除 Istio 可能會導致您的服務之間發生流量相關問題。 請確定您為系統提供的布建在沒有 Istio 的情況下仍可正常運作，然後再繼續進行。

### <a name="remove-istio"></a>移除 Istio

若要從您的 AKS 叢集中移除 Istio，請刪除 `IstioOperator` 先前新增的資源（名為） `istio-control-plane` 。 Istio 運算子會辨識 Istio 運算子規格已移除，然後刪除所有相關聯的 Istio 元件。

```bash
kubectl delete istiooperator istio-control-plane -n istio-system
```

您可以執行下列動作來檢查所有 Istio 元件是否已刪除。

```bash
kubectl get all -n istio-system
```

### <a name="remove-istio-operator"></a>移除 Istio 運算子

成功卸載 Istio 之後，您也可以移除 Istio 運算子。

```bash
istioctl operator remove
```

最後，移除 `istio-` 命名空間。

```bash
kubectl delete ns istio-system
kubectl delete ns istio-operator
```

## <a name="next-steps"></a>後續步驟

若要探索更多 Istio 的安裝和設定選項，請參閱下列官方 Istio 指導方針：

- [Istio-安裝指南][istio-installation-guides]

您也可以使用下列內容來執行其他案例：

- [Istio Bookinfo 應用程式範例][istio-bookinfo-example]

<!-- LINKS - external -->
[istio]: https://istio.io
[helm]: https://helm.sh

[istio-faq]: https://istio.io/faq/general/
[istio-docs-concepts]: https://istio.io/docs/concepts/what-is-istio/
[istio-github]: https://github.com/istio/istio
[istio-github-releases]: https://github.com/istio/istio/releases
[istio-release-notes]: https://istio.io/news/
[istio-installation-guides]: https://istio.io/docs/setup/install/
[istio-install-download]: https://istio.io/docs/setup/kubernetes/download-release/
[istio-install-istioctl]: https://istio.io/docs/setup/install/istioctl/
[istio-install-operator]: https://istio.io/latest/docs/setup/install/operator/
[istio-configuration-profiles]: https://istio.io/docs/setup/additional-setup/config-profiles/
[istio-control-plane]: https://istio.io/docs/reference/config/istio.operator.v1alpha1/
[istio-bookinfo-example]: https://istio.io/docs/examples/bookinfo/

[istio-feature-stages]: https://istio.io/about/feature-stages/
[istio-feature-sds]: https://istio.io/docs/tasks/traffic-management/ingress/secure-ingress-sds/
[istio-feature-cni]: https://istio.io/docs/setup/additional-setup/cni/

[kubernetes-operator]: https://kubernetes.io/docs/concepts/extend-kubernetes/operator/
[kubernetes-feature-sa-projected-volume]: https://kubernetes.io/docs/tasks/configure-pod-container/configure-service-account/#service-account-token-volume-projection
[kubernetes-crd]: https://kubernetes.io/docs/concepts/extend-kubernetes/api-extension/custom-resources/#customresourcedefinitions
[kubernetes-secrets]: https://kubernetes.io/docs/concepts/configuration/secret/
[kubernetes-node-selectors]: ./concepts-clusters-workloads.md#node-selectors
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-port-forward]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#port-forward

[grafana]: https://grafana.com/
[prometheus]: https://prometheus.io/
[jaeger]: https://www.jaegertracing.io/
[kiali]: https://www.kiali.io/
[envoy]: https://www.envoyproxy.io/

<!-- LINKS - internal -->
[aks-quickstart]: ./kubernetes-walkthrough.md
