---
title: 在 Azure Kubernetes Service (AKS) 中安裝 Istio
description: 了解如何在 Azure Kubernetes Service (AKS) 叢集中安裝和使用 Istio 以建立服務網格
author: paulbouwer
ms.service: container-service
ms.topic: article
ms.date: 02/19/2020
ms.author: pabouwer
zone_pivot_groups: client-operating-system
ms.openlocfilehash: 15b73380269c568977b524a63ca709e352485433
ms.sourcegitcommit: 98a5a6765da081e7f294d3cb19c1357d10ca333f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/20/2020
ms.locfileid: "77485211"
---
# <a name="install-and-use-istio-in-azure-kubernetes-service-aks"></a>在 Azure Kubernetes Service (AKS) 中安裝和使用 Istio

[Istio][istio-github]是一種開放原始碼服務網格，可在 Kubernetes 叢集中跨微服務提供一組重要的功能。 這些功能包括流量管理、服務識別和安全性、原則強制執行和可檢視性。 如需有關 Istio 的詳細資訊，請參閱官方[什麼是 Istio？][istio-docs-concepts]檔。

本文說明如何安裝 Istio。 Istio `istioctl` 用戶端二進位檔會安裝到您的用戶端電腦上，而 Istio 元件則會安裝到 AKS 上的 Kubernetes 叢集中。

> [!NOTE]
> 下列指示會參考 Istio 版本 `1.4.0`。
>
> Istio 團隊已針對 Kubernetes 版本 `1.13`、`1.14``1.15`，測試了 Istio `1.4.x` 版本。 您可以在[GitHub Istio 版本][istio-github-releases]（英文）中找到其他 Istio 版本、 [Istio 新聞][istio-release-notes]中每個版本的相關資訊，以及[Istio 一般常見問題][istio-faq]中的支援 Kubernetes 版本。

在本文中，您將學會如何：

> [!div class="checklist"]
> * 下載並安裝 Istio istioctl 用戶端二進位檔
> * 在 AKS 上安裝 Istio
> * 驗證 Istio 安裝
> * 存取附加元件
> * 從 AKS 卸載 Istio

## <a name="before-you-begin"></a>開始之前

本文中詳述的步驟假設您已建立 AKS 叢集（已啟用 RBAC 的 Kubernetes `1.13` 和更新版本），並已建立與叢集的 `kubectl` 連接。 如果您需要這些專案的協助，請參閱[AKS 快速入門][aks-quickstart]。

請確定您已閱讀[Istio 效能和擴充性](https://istio.io/docs/concepts/performance-and-scalability/)檔，以瞭解在 AKS 叢集中執行 Istio 的其他資源需求。 核心和記憶體需求會根據您的特定工作負載而有所不同。 選擇適當數目的節點和 VM 大小，以滿足您的設定。

本文將 Istio 安裝指引分成數個獨立的步驟。 最終結果在結構上與官方 Istio 安裝[指導][istio-install-istioctl]方針相同。

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Linux - download and install client binary](includes/servicemesh/istio/install-client-binary-linux.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [MacOS - download and install client binary](includes/servicemesh/istio/install-client-binary-macos.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [Windows - download and install client binary](includes/servicemesh/istio/install-client-binary-windows.md)]

::: zone-end

## <a name="install-the-istio-components-on-aks"></a>在 AKS 上安裝 Istio 元件

我們將在 Istio 安裝過程中安裝[Grafana][grafana]和[Kiali][kiali] 。 Grafana 提供分析和監視儀表板，而 Kiali 則提供服務網格可檢視性儀表板。 在我們的設定中，每個元件都需要認證，必須以[秘密][kubernetes-secrets]的形式提供。

在我們可以安裝 Istio 元件之前，必須先為 Grafana 和 Kiali 建立秘密。 這些密碼必須安裝在 Istio 所使用的 `istio-system` 命名空間中，因此我們也需要建立命名空間。 當您透過 `kubectl create` 建立命名空間時，必須使用 `--save-config` 選項，讓 Istio 安裝程式日後可以在此物件上執行 `kubectl apply`。

```console
kubectl create namespace istio-system --save-config
```

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Bash - create secrets for Grafana and Kiali](includes/servicemesh/istio/install-create-secrets-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [Bash check for CRDs](includes/servicemesh/istio/install-create-secrets-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [PowerShell check for CRDs](includes/servicemesh/istio/install-create-secrets-powershell.md)]

::: zone-end

### <a name="install-istio-components"></a>安裝 Istio 元件

既然我們已成功建立 AKS 叢集中的 Grafana 和 Kiali 秘密，就可以安裝 Istio 元件了。 

Istio 的[Helm][helm]安裝方法將于未來淘汰。 適用于 Istio 的新安裝方法會利用 `istioctl` 的用戶端二進位檔、Istio 設定配置[檔][istio-configuration-profiles]，以及新的[Istio 控制平面規格和 api][istio-control-plane]。 這是我們將用來安裝 Istio 的新方法。

> [!NOTE]
> Istio 目前必須排程在 Linux 節點上執行。 如果您的叢集中有 Windows Server 節點，您必須確定 Istio pod 只排程在 Linux 節點上執行。 我們將使用[節點選取器][kubernetes-node-selectors]來確定 pod 已排程至正確的節點。

> [!CAUTION]
> [SDS （秘密探索服務）][istio-feature-sds]和[Istio CNI][istio-feature-cni] Istio 功能目前是以[Alpha][istio-feature-stages]提供，因此請先考慮一下，再啟用這些功能。 此外，目前的 AKS 版本不會啟用[服務帳戶權杖磁片區投射][kubernetes-feature-sa-projected-volume]Kubernetes 功能（SDS 的需求）。
使用下列內容建立名為 `istio.aks.yaml` 的檔案。 這個檔案會保留[Istio 控制平面的規格][istio-control-plane]詳細資料，以供設定 Istio。

```yaml
apiVersion: install.istio.io/v1alpha2
kind: IstioControlPlane
spec:
  # Use the default profile as the base
  # More details at: https://istio.io/docs/setup/additional-setup/config-profiles/
  profile: default
  values:
    global:
      # Ensure that the Istio pods are only scheduled to run on Linux nodes
      defaultNodeSelector:
        beta.kubernetes.io/os: linux
      # Enable mutual TLS for the control plane
      controlPlaneSecurityEnabled: true
      mtls:
        # Require all service to service communication to have mtls
        enabled: false
    grafana:
      # Enable Grafana deployment for analytics and monitoring dashboards
      enabled: true
      security:
        # Enable authentication for Grafana
        enabled: true
    kiali:
      # Enable the Kiali deployment for a service mesh observability dashboard
      enabled: true
    tracing:
      # Enable the Jaeger deployment for tracing
      enabled: true
```

使用 `istioctl apply` 命令和上述 `istio.aks.yaml` Istio 控制平面規格檔案安裝 istio，如下所示：

```console
istioctl manifest apply -f istio.aks.yaml --logtostderr --set installPackagePath=./install/kubernetes/operator/charts
```

安裝程式將會部署一些[CRDs][kubernetes-crd] ，然後管理相依性，以安裝針對此 Istio 設定所定義的所有相關物件。 您應該會看到類似下列的輸出程式碼片段。

```console
Applying manifests for these components:
- Tracing
- EgressGateway
- NodeAgent
- Grafana
- Policy
- Citadel
- CertManager
- IngressGateway
- Injector
- Prometheus
- PrometheusOperator
- Kiali
- Telemetry
- Galley
- Cni
- Pilot
- Base
- CoreDNS
NodeAgent is waiting on a prerequisite...
Telemetry is waiting on a prerequisite...
Galley is waiting on a prerequisite...
Cni is waiting on a prerequisite...
Grafana is waiting on a prerequisite...
Policy is waiting on a prerequisite...
Citadel is waiting on a prerequisite...
EgressGateway is waiting on a prerequisite...
Tracing is waiting on a prerequisite...
Kiali is waiting on a prerequisite...
PrometheusOperator is waiting on a prerequisite...
IngressGateway is waiting on a prerequisite...
Prometheus is waiting on a prerequisite...
CertManager is waiting on a prerequisite...
Injector is waiting on a prerequisite...
Pilot is waiting on a prerequisite...
Applying manifest for component Base
Waiting for CRDs to be applied.
CRDs applied.
Finished applying manifest for component Base
Prerequisite for Tracing has completed, proceeding with install.
Prerequisite for Injector has completed, proceeding with install.
Prerequisite for Telemetry has completed, proceeding with install.
Prerequisite for Policy has completed, proceeding with install.
Prerequisite for PrometheusOperator has completed, proceeding with install.
Prerequisite for NodeAgent has completed, proceeding with install.
Prerequisite for IngressGateway has completed, proceeding with install.
Prerequisite for Kiali has completed, proceeding with install.
Prerequisite for EgressGateway has completed, proceeding with install.
Prerequisite for Galley has completed, proceeding with install.
Prerequisite for Grafana has completed, proceeding with install.
Prerequisite for Cni has completed, proceeding with install.
Prerequisite for Citadel has completed, proceeding with install.
Applying manifest for component Tracing
Prerequisite for Prometheus has completed, proceeding with install.
Prerequisite for Pilot has completed, proceeding with install.
Prerequisite for CertManager has completed, proceeding with install.
Applying manifest for component Kiali
Applying manifest for component Prometheus
Applying manifest for component IngressGateway
Applying manifest for component Policy
Applying manifest for component Telemetry
Applying manifest for component Citadel
Applying manifest for component Galley
Applying manifest for component Pilot
Applying manifest for component Injector
Applying manifest for component Grafana
Finished applying manifest for component Kiali
Finished applying manifest for component Tracing
Finished applying manifest for component Prometheus
Finished applying manifest for component Citadel
Finished applying manifest for component Policy
Finished applying manifest for component IngressGateway
Finished applying manifest for component Injector
Finished applying manifest for component Galley
Finished applying manifest for component Pilot
Finished applying manifest for component Grafana
Finished applying manifest for component Telemetry

Component IngressGateway installed successfully:
================================================

serviceaccount/istio-ingressgateway-service-account created
deployment.apps/istio-ingressgateway created
gateway.networking.istio.io/ingressgateway created
sidecar.networking.istio.io/default created
poddisruptionbudget.policy/ingressgateway created
horizontalpodautoscaler.autoscaling/istio-ingressgateway created
service/istio-ingressgateway created

...
```

此時，您已將 Istio 部署至 AKS 叢集。 為了確保我們已成功部署 Istio，讓我們繼續進行下一節，以[驗證 Istio 安裝](#validate-the-istio-installation)。

## <a name="validate-the-istio-installation"></a>驗證 Istio 安裝

先確認是否已建立預期的服務。 使用[kubectl get svc][kubectl-get]命令來查看執行中的服務。 查詢 `istio-system` 命名空間，其中 Istio 和附加元件是由 `istio` Helm 圖表所安裝：

```console
kubectl get svc --namespace istio-system --output wide
```

下列範例輸出會顯示現在應該正在執行的服務：

- `istio-*` 服務
- `jaeger-*`、`tracing`和 `zipkin` 附加元件追蹤服務
- `prometheus` 附加元件計量服務
- `grafana` 附加元件分析和監視儀表板服務
- `kiali` 附加元件服務網格儀表板服務

如果 `istio-ingressgateway` 顯示外部 IP 是 `<pending>`，請等候幾分鐘，直到 Azure 網路指派了 IP 位址。

```console
NAME                     TYPE           CLUSTER-IP     EXTERNAL-IP      PORT(S)                                                                                                                      AGE   SELECTOR
grafana                  ClusterIP      10.0.116.147   <none>           3000/TCP                                                                                                                     92s   app=grafana
istio-citadel            ClusterIP      10.0.248.152   <none>           8060/TCP,15014/TCP                                                                                                           94s   app=citadel
istio-galley             ClusterIP      10.0.50.100    <none>           443/TCP,15014/TCP,9901/TCP,15019/TCP                                                                                         93s   istio=galley
istio-ingressgateway     LoadBalancer   10.0.36.213    20.188.221.111   15020:30369/TCP,80:31368/TCP,443:30045/TCP,15029:32011/TCP,15030:31212/TCP,15031:32411/TCP,15032:30009/TCP,15443:30010/TCP   93s   app=istio-ingressgateway
istio-pilot              ClusterIP      10.0.23.222    <none>           15010/TCP,15011/TCP,8080/TCP,15014/TCP                                                                                       93s   istio=pilot
istio-policy             ClusterIP      10.0.59.250    <none>           9091/TCP,15004/TCP,15014/TCP                                                                                                 93s   istio-mixer-type=policy,istio=mixer
istio-sidecar-injector   ClusterIP      10.0.123.219   <none>           443/TCP                                                                                                                      93s   istio=sidecar-injector
istio-telemetry          ClusterIP      10.0.216.9     <none>           9091/TCP,15004/TCP,15014/TCP,42422/TCP                                                                                       89s   istio-mixer-type=telemetry,istio=mixer
jaeger-agent             ClusterIP      None           <none>           5775/UDP,6831/UDP,6832/UDP                                                                                                   96s   app=jaeger
jaeger-collector         ClusterIP      10.0.221.24    <none>           14267/TCP,14268/TCP,14250/TCP                                                                                                95s   app=jaeger
jaeger-query             ClusterIP      10.0.46.154    <none>           16686/TCP                                                                                                                    95s   app=jaeger
kiali                    ClusterIP      10.0.174.97    <none>           20001/TCP                                                                                                                    94s   app=kiali
prometheus               ClusterIP      10.0.245.226   <none>           9090/TCP                                                                                                                     94s   app=prometheus
tracing                  ClusterIP      10.0.249.95    <none>           9411/TCP                                                                                                                     95s   app=jaeger
zipkin                   ClusterIP      10.0.154.89    <none>           9411/TCP                                                                                                                     94s   app=jaeger
```

接著，確認是否已建立所需的 Pod。 使用[kubectl get][kubectl-get] pod 命令，然後重新查詢 `istio-system` 命名空間：

```console
kubectl get pods --namespace istio-system
```

下列範例輸出會顯示正在執行的 Pod：

- `istio-*` pod
- `prometheus-*` 附加元件計量 pod
- `grafana-*` 附加元件分析和監視儀表板 pod
- `kiali` 附加元件服務網格儀表板 pod

```console
NAME                                          READY   STATUS    RESTARTS   AGE
grafana-6bc97ff99-k9sk4                       1/1     Running   0          92s
istio-citadel-6b5c754454-tb8nf                1/1     Running   0          94s
istio-galley-7d6d78d7c5-zshsd                 2/2     Running   0          94s
istio-ingressgateway-85869c5cc7-x5d76         1/1     Running   0          95s
istio-pilot-787d6995b5-n5vrj                  2/2     Running   0          94s
istio-policy-6cf4fbc8dc-sdsg5                 2/2     Running   2          94s
istio-sidecar-injector-5d5b978668-wrz2s       1/1     Running   0          94s
istio-telemetry-5498db684-6kdnw               2/2     Running   1          94s
istio-tracing-78548677bc-74tx6                1/1     Running   0          96s
kiali-59b7fd7f68-92zrh                        1/1     Running   0          95s
prometheus-7c7cf9dbd6-rjxcv                   1/1     Running   0          94s
```

所有 pod 都應該顯示 `Running`的狀態。 如果您的 Pod 沒有這些狀態，請等候一兩分鐘直到其成為該狀態。 如果有任何 pod 回報問題，請使用[kubectl 描述 pod][kubectl-describe]命令來檢查其輸出和狀態。

## <a name="accessing-the-add-ons"></a>存取附加元件

在上述的安裝程式中，Istio 會安裝一些附加元件，以提供額外的功能。 附加元件的 web 應用程式**不**會透過外部 ip 位址公開公開。 

若要存取附加元件使用者介面，請使用 `istioctl dashboard` 命令。 此命令會利用[kubectl 的埠轉送][kubectl-port-forward]和隨機埠，在您的用戶端電腦和 AKS 叢集中的相關 pod 之間建立安全連線。 然後，它會在您的預設瀏覽器中自動開啟附加元件 web 應用程式。

我們為 Grafana 和 Kiali 新增了一層額外的安全性，方法是在本文稍早的指定認證。

### <a name="grafana"></a>Grafana

Istio 的分析和監視儀表板是由[Grafana][grafana]提供。 請記得在出現提示時，使用您稍早透過 Grafana 密碼所建立的認證。 以安全的方式開啟 Grafana 儀表板，如下所示：

```console
istioctl dashboard grafana
```

### <a name="prometheus"></a>Prometheus

[Prometheus][prometheus]會提供 Istio 的計量。 以安全的方式開啟 Prometheus 儀表板，如下所示：

```console
istioctl dashboard prometheus
```

### <a name="jaeger"></a>Jaeger

Istio 內的追蹤是由[Jaeger][jaeger]提供。 以安全的方式開啟 Jaeger 儀表板，如下所示：

```console
istioctl dashboard jaeger
```

### <a name="kiali"></a>Kiali

服務網格可檢視性儀表板是由[Kiali][kiali]提供。 請記得在出現提示時，使用您稍早透過 Kiali 密碼所建立的認證。 以安全的方式開啟 Kiali 儀表板，如下所示：

```console
istioctl dashboard kiali
```

### <a name="envoy"></a>Envoy

提供[Envoy][envoy] proxy 的簡單介面。 它會為在指定 pod 中執行的 Envoy proxy 提供設定資訊和計量。 安全地開啟 Envoy 介面，如下所示：

```console
istioctl dashboard envoy <pod-name>.<namespace>
```

## <a name="uninstall-istio-from-aks"></a>從 AKS 卸載 Istio

> [!WARNING]
> 從執行中的系統刪除 Istio 可能會導致您的服務之間發生流量相關的問題。 請確定您已在不 Istio 的情況下，將您的系統布建為正常運作，再繼續進行。

### <a name="remove-istio-components-and-namespace"></a>移除 Istio 元件和命名空間

若要從您的 AKS 叢集中移除 Istio，請使用 `istioctl manifest generate` 命令搭配 `istio.aks.yaml` Istio 控制平面規格檔案。 這會產生已部署的資訊清單，我們將透過管道傳送至 `kubectl delete`，以移除所有已安裝的元件和 `istio-system` 命名空間。

```console
istioctl manifest generate -f istio.aks.yaml -o istio-components-aks --logtostderr --set installPackagePath=./install/kubernetes/operator/charts 

kubectl delete -f istio-components-aks -R
```

### <a name="remove-istio-crds-and-secrets"></a>移除 Istio CRDs 和秘密

上述命令會刪除所有 Istio 元件和命名空間，但仍會保留產生的 Istio 秘密。 

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Bash - remove Istio secrets](includes/servicemesh/istio/uninstall-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [Bash - remove Istio secrets](includes/servicemesh/istio/uninstall-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [PowerShell - remove Istio secrets](includes/servicemesh/istio/uninstall-powershell.md)]

::: zone-end

## <a name="next-steps"></a>後續步驟

下列檔說明如何使用 Istio 來提供智慧型路由，以推出未排放的版本：

> [!div class="nextstepaction"]
> [AKS Istio 智慧型路由案例][istio-scenario-routing]

若要探索 Istio 的更多安裝和設定選項，請參閱下列官方 Istio 指導方針：

- [Istio-安裝指南][istio-installation-guides]

您也可以使用來遵循其他案例：

- [Istio Bookinfo 應用程式範例][istio-bookinfo-example]

若要瞭解如何使用 Application Insights 和 Istio 監視您的 AKS 應用程式，請參閱下列 Azure 監視器檔：

- [Kubernetes 託管應用程式的零檢測應用程式監視][app-insights]

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
[istio-configuration-profiles]: https://istio.io/docs/setup/additional-setup/config-profiles/
[istio-control-plane]: https://istio.io/docs/reference/config/istio.operator.v1alpha12.pb/#IstioControlPlane
[istio-bookinfo-example]: https://istio.io/docs/examples/bookinfo/

[istio-feature-stages]: https://istio.io/about/feature-stages/
[istio-feature-sds]: https://istio.io/docs/tasks/security/auth-sds/
[istio-feature-cni]: https://istio.io/docs/setup/additional-setup/cni/

[install-wsl]: https://docs.microsoft.com/windows/wsl/install-win10

[kubernetes-feature-sa-projected-volume]: https://kubernetes.io/docs/tasks/configure-pod-container/configure-service-account/#service-account-token-volume-projection
[kubernetes-crd]: https://kubernetes.io/docs/concepts/extend-kubernetes/api-extension/custom-resources/#customresourcedefinitions
[kubernetes-secrets]: https://kubernetes.io/docs/concepts/configuration/secret/
[kubernetes-node-selectors]: https://docs.microsoft.com/azure/aks/concepts-clusters-workloads#node-selectors
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-port-forward]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#port-forward

[grafana]: https://grafana.com/
[prometheus]: https://prometheus.io/
[jaeger]: https://www.jaegertracing.io/
[kiali]: https://www.kiali.io/
[envoy]: https://www.envoyproxy.io/

[app-insights]: https://docs.microsoft.com/azure/azure-monitor/app/kubernetes

<!-- LINKS - internal -->
[aks-quickstart]: ./kubernetes-walkthrough.md
[istio-scenario-routing]: ./servicemesh-istio-scenario-routing.md
