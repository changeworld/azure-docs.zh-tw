---
title: 在 Azure Kubernetes Service (AKS) 中安裝 Istio
description: 了解如何在 Azure Kubernetes Service (AKS) 叢集中安裝和使用 Istio 以建立服務網格
author: paulbouwer
ms.topic: article
ms.date: 02/19/2020
ms.author: pabouwer
zone_pivot_groups: client-operating-system
ms.openlocfilehash: f0fe4ab46bfe5c0c0c2ea67aa2e2694321628be5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79136358"
---
# <a name="install-and-use-istio-in-azure-kubernetes-service-aks"></a>在 Azure Kubernetes Service (AKS) 中安裝和使用 Istio

[Istio][istio-github] 是一種開放原始碼服務網格，可在 Kubernetes 叢集中提供跨微服務的主要功能集。 這些功能包括流量管理、服務識別和安全性、原則強制執行和可檢視性。 如需 Istio 的詳細資訊，請參閱官方文件[什麼是 Istio？][istio-docs-concepts]。

本文說明如何安裝 Istio。 Istio`istioctl`用戶端二進位檔案安裝在用戶端電腦上，Istio 元件安裝在 AKS 上的 Kubernetes 群集中。

> [!NOTE]
> 以下說明引用 Istio 版本`1.4.0`。
>
> 伊西奧`1.4.x`版本已經測試了伊西奧團隊對庫伯內特斯版本`1.13`， `1.14`. `1.15`. 您可以在[GitHub - Istio 版本][istio-github-releases]中找到其他 Istio 版本，有關[Istio News][istio-release-notes]上每個版本的資訊，以及[Istio 通用常見問題解答][istio-faq]中支援的庫貝內茨版本。

在本文中，您將學會如何：

> [!div class="checklist"]
> * 下載並安裝 Istio istioctl 用戶端二進位
> * 在 AKS 上安裝 Istio
> * 驗證 Istio 安裝
> * 訪問載入項
> * 從 AKS 卸載 Istio

## <a name="before-you-begin"></a>開始之前

本文中詳述的步驟假定您已經創建了 AKS 群集（啟用了 RBAC 的`1.13`Kubernetes 及以上群集），並建立了與`kubectl`群集的連接。 如果您需要前述任何方面的協助，請參閱 [AKS 快速入門][aks-quickstart]。

請確保您已閱讀[Istio 性能和可擴充性](https://istio.io/docs/concepts/performance-and-scalability/)文檔，以瞭解在 AKS 群集中運行 Istio 的其他資源要求。 核心和記憶體要求將根據您的特定工作負載而變化。 選擇適當數量的節點和 VM 大小，以滿足您的設置。

本文將 Istio 安裝指引分成數個獨立的步驟。 最終結果的結構與官方的 Istio 安裝[指引][istio-install-istioctl]相同。

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

我們將安裝[格拉法納][grafana]和[基阿利][kiali]作為 Istio 安裝的一部分。 Grafana 提供分析和監控儀表板，Kiali 提供服務網格可觀察性儀表板。 在我們的設置中，每個元件都需要必須作為[機密][kubernetes-secrets]提供的憑據。

在安裝 Istio 元件之前，我們必須為格拉法納和基亞利創建秘密。 這些機密需要安裝到 Istio 將使用的`istio-system`命名空間中，因此我們也需要創建命名空間。 在創建`--save-config``kubectl create`命名空間時，我們需要使用 該選項，以便 Istio 安裝程式將來可以`kubectl apply`在此物件上運行。

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

現在，我們已經在我們的 AKS 群集中成功創建了 Grafana 和 Kiali 機密，是時候安裝 Istio 元件了。 

Istio 的[Helm][helm]安裝方法將在未來被棄用。 Istio 的新安裝方法利用了`istioctl`用戶端二進位檔案[、Istio 設定檔][istio-configuration-profiles]和新的[Istio 控制平面規格和 api][istio-control-plane]。 這種新方法是我們將使用來安裝 Istio 的方法。

> [!NOTE]
> Istio 當前必須計畫在 Linux 節點上運行。 如果群集中具有 Windows Server 節點，則必須確保 Istio pod 僅計畫在 Linux 節點上運行。 我們將使用[節點選擇器][kubernetes-node-selectors]確保將 pod 安排到正確的節點。

> [!CAUTION]
> [SDS（秘密發現服務）][istio-feature-sds]和[Istio CNI][istio-feature-cni] Istio功能目前位於[阿爾法][istio-feature-stages]中，因此在啟用這些功能之前應考慮。 此外，服務[帳戶權杖卷投影][kubernetes-feature-sa-projected-volume]庫伯奈斯功能（SDS 的要求）在當前 AKS 版本中未啟用。
創建使用以下內容`istio.aks.yaml`調用的檔。 此檔將保存用於配置 Istio 的[Istio 控制平面規範][istio-control-plane]詳細資訊。

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

使用`istioctl apply`命令和上述`istio.aks.yaml`Istio 控制平面規範檔安裝 istio，如下所示：

```console
istioctl manifest apply -f istio.aks.yaml --logtostderr --set installPackagePath=./install/kubernetes/operator/charts
```

安裝程式將部署許多[CRD，][kubernetes-crd]然後管理依賴項以安裝為此 Istio 配置定義的所有相關物件。 您應該會看到類似以下輸出程式碼片段的內容。

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

此時，您已將 Istio 部署到 AKS 群集。 為了確保我們成功部署 Istio，讓我們繼續下一節以[驗證 Istio 安裝](#validate-the-istio-installation)。

## <a name="validate-the-istio-installation"></a>驗證 Istio 安裝

先確認是否已建立預期的服務。 使用 [kubectl get svc][kubectl-get] 命令來檢視執行中的服務。 查詢`istio-system`命名空間，其中`istio`Helm 圖表安裝了 Istio 和載入項元件：

```console
kubectl get svc --namespace istio-system --output wide
```

下列範例輸出會顯示現在應該正在執行的服務：

- `istio-*` 服務
- `jaeger-*`和`tracing``zipkin`附加跟蹤服務
- `prometheus`附加指標服務
- `grafana`附加分析和監控儀表板服務
- `kiali`附加服務網格儀表板服務

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

接著，確認是否已建立所需的 Pod。 使用[kubectl 獲取窗格][kubectl-get]命令，並重新查詢`istio-system`命名空間：

```console
kubectl get pods --namespace istio-system
```

下列範例輸出會顯示正在執行的 Pod：

- `istio-*`吊艙
- `prometheus-*`附加指標窗格
- `grafana-*`附加分析和監控儀表板窗格
- `kiali`附加服務網格儀表板窗格

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

所有窗格都應顯示 的狀態`Running`。 如果您的 Pod 沒有這些狀態，請等候一兩分鐘直到其成為該狀態。 如果有任何 Pod 回報發生問題，請使用 [kubectl describe pod][kubectl-describe] 命令檢閱其輸出和狀態。

## <a name="accessing-the-add-ons"></a>存取附加元件

Istio 在上面的設置中安裝了許多附加元件，這些附加元件提供了其他功能。 載入項的 Web 應用程式**不會**通過外部 ip 位址公開公開。 

要訪問載入項使用者介面，請使用 命令`istioctl dashboard`。 此命令利用[kubectl 埠轉發][kubectl-port-forward]和隨機埠在用戶端電腦和 AKS 群集中的相關 pod 之間創建安全連線。 然後，它將自動在預設瀏覽器中打開載入項 Web 應用程式。

本文前面之前為 Grafana 和 Kiali 添加了一層額外的安全層， 為它們指定憑據。

### <a name="grafana"></a>Grafana

Istio 的分析和監視儀表板由[格拉法納][grafana]提供。 請記住，在提示時，請提前使用通過 Grafana 機密創建的憑據。 安全打開格拉法納儀表板，如下所示：

```console
istioctl dashboard grafana
```

### <a name="prometheus"></a>Prometheus

伊西奧的指標由[普羅米圖斯][prometheus]提供。 安全打開 Prometheus 儀表板，如下所示：

```console
istioctl dashboard prometheus
```

### <a name="jaeger"></a>Jaeger

伊西奧內部的追蹤由[傑格][jaeger]提供。 按照以下方式安全地打開 Jaeger 儀表板：

```console
istioctl dashboard jaeger
```

### <a name="kiali"></a>Kiali

服務網格可檢視性儀表板會由 [Kiali][kiali] 來提供。 請記住，在提示時，請提前使用通過 Kiali 機密創建的憑據。 安全打開 Kiali 儀表板，如下所示：

```console
istioctl dashboard kiali
```

### <a name="envoy"></a>Envoy

提供[與特使][envoy]代理人的簡單介面。 它為在指定窗格中運行的特使代理提供配置資訊和指標。 安全打開特使介面，如下所示：

```console
istioctl dashboard envoy <pod-name>.<namespace>
```

## <a name="uninstall-istio-from-aks"></a>從 AKS 卸載 Istio

> [!WARNING]
> 從正在運行的系統中刪除 Istio 可能會導致服務之間的流量相關問題。 在繼續操作之前，請確保您已為系統在不使用 Istio 的情況下仍正常運行做出了規定。

### <a name="remove-istio-components-and-namespace"></a>刪除 Istio 元件和命名空間

要從 AKS 群集中刪除 Istio，`istioctl manifest generate`請使用`istio.aks.yaml`Istio 控制平面規範檔的命令。 這將生成已部署的清單，我們將管道到`kubectl delete`該清單以刪除所有已安裝的元件和`istio-system`命名空間。

```console
istioctl manifest generate -f istio.aks.yaml -o istio-components-aks --logtostderr --set installPackagePath=./install/kubernetes/operator/charts 

kubectl delete -f istio-components-aks -R
```

### <a name="remove-istio-crds-and-secrets"></a>刪除 Istio CRD 和機密

上述命令刪除所有 Istio 元件和命名空間，但我們仍保留生成的 Istio 機密。 

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

以下文檔介紹如何使用 Istio 提供智慧路由來推出金絲雀版本：

> [!div class="nextstepaction"]
> [AKS Istio 智慧型路由案例][istio-scenario-routing]

要探索 Istio 的更多安裝和配置選項，請參閱以下官方的 Istio 指南：

- [伊斯特奧 - 安裝指南][istio-installation-guides]

您還可以使用以下其他方案：

- [Istio Bookinfo 應用程式示例][istio-bookinfo-example]

要瞭解如何使用應用程式見解和 Istio 監視 AKS 應用程式，請參閱以下 Azure 監視器文檔：

- [庫伯內斯托管應用程式為零檢測應用程式監控][app-insights]

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
[istio-control-plane]: https://istio.io/docs/reference/config/istio.operator.v1alpha1/
[istio-bookinfo-example]: https://istio.io/docs/examples/bookinfo/

[istio-feature-stages]: https://istio.io/about/feature-stages/
[istio-feature-sds]: https://istio.io/docs/tasks/traffic-management/ingress/secure-ingress-sds/
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
