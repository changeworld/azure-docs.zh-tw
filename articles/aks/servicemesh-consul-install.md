---
title: 在 Azure Kubernetes Service 中安裝 Consul （AKS）
description: 瞭解如何安裝和使用 Consul，以在 Azure Kubernetes Service （AKS）叢集中建立服務網格
author: dstrebel
ms.topic: article
ms.date: 10/09/2019
ms.author: dastrebe
zone_pivot_groups: client-operating-system
ms.openlocfilehash: 06ca2327b2859ffb0f5b314d7b92082d5a83dc48
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/25/2020
ms.locfileid: "77594232"
---
# <a name="install-and-use-consul-in-azure-kubernetes-service-aks"></a>在 Azure Kubernetes Service 中安裝和使用 Consul （AKS）

[Consul][consul-github]是一種開放原始碼服務網格，可在 Kubernetes 叢集中跨微服務提供一組重要的功能。 這些功能包括服務探索、健康情況檢查、服務分割和可檢視性。 如需有關 Consul 的詳細資訊，請參閱官方[什麼是 Consul？][consul-docs-concepts]檔。

本文說明如何安裝 Consul。 Consul 元件會安裝在 AKS 上的 Kubernetes 叢集中。

> [!NOTE]
> 這些指示會參考 Consul 版本 `1.6.0`，並使用至少 Helm 版本 `2.14.2`。
>
> 可以針對 Kubernetes 版本 `1.13+`執行 Consul `1.6.x` 版本。 您可以在[GitHub Consul 版本][consul-github-releases]中找到其他 Consul 版本，並在[Consul 版本][consul-release-notes]資訊中找到每個版本的相關資訊。

在本文中，您將學會如何：

> [!div class="checklist"]
> * 在 AKS 上安裝 Consul 元件
> * 驗證 Consul 安裝
> * 從 AKS 卸載 Consul

## <a name="before-you-begin"></a>開始之前

本文中詳述的步驟假設您已建立 AKS 叢集（已啟用 RBAC 的 Kubernetes `1.13` 和更新版本），並已建立與叢集的 `kubectl` 連接。 如果您需要這些專案的協助，請參閱[AKS 快速入門][aks-quickstart]。 請確定您的叢集在 Linux 節點集區中至少有3個節點。

您將需要[Helm][helm] ，才能遵循這些指示並安裝 Consul。 建議您在叢集中正確安裝和設定最新的穩定版本。 如果您需要安裝 Helm 的協助，請參閱[AKS Helm 安裝指引][helm-install]。 所有 Consul pod 也必須排程在 Linux 節點上執行。

本文將 Consul 安裝指引分成幾個不同的步驟。 最終結果在結構上與官方 Consul 安裝[指導][consul-install-k8]方針相同。

### <a name="install-the-consul-components-on-aks"></a>在 AKS 上安裝 Consul 元件

我們會從下載 Consul Helm 圖表版本 `v0.10.0` 開始。 這一版的圖表包含 Consul 版本 `1.6.0`。

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Linux - download](includes/servicemesh/consul/download-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [MacOS - download](includes/servicemesh/consul/download-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [Windows - download](includes/servicemesh/consul/download-powershell.md)]

::: zone-end

使用 Helm 和下載的 `consul-helm` 圖表，將 Consul 元件安裝到 AKS 叢集中的 `consul` 命名空間。 

> [!NOTE]
> **安裝選項**
> 
> 我們會在安裝過程中使用下列選項：
> - `connectInject.enabled=true`-啟用要插入 pod 中的 proxy
> - `client.enabled=true`-讓 Consul 用戶端在每個節點上執行
> - `client.grpc=true`-啟用 connectInject 的 gRPC 接聽程式
> - `syncCatalog.enabled=true` 同步 Kubernetes 和 Consul 服務
>
> **節點選取器**
>
> Consul 目前必須排程在 Linux 節點上執行。 如果您的叢集中有 Windows Server 節點，您必須確定 Consul pod 只排程在 Linux 節點上執行。 我們將使用[節點選取器][kubernetes-node-selectors]來確定 pod 已排程至正確的節點。

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Bash - install Istio components](includes/servicemesh/consul/install-components-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [Bash - install Istio components](includes/servicemesh/consul/install-components-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [PowerShell - install Istio components](includes/servicemesh/consul/install-components-powershell.md)]

::: zone-end

`Consul` Helm 圖會部署一些物件。 您可以從上述 `helm install` 命令的輸出查看清單。 根據您的叢集環境，Consul 元件的部署可能需要約3分鐘的時間才能完成。

此時，您已將 Consul 部署至 AKS 叢集。 為了確保我們已成功部署 Consul，讓我們繼續進行下一節，以驗證 Consul 安裝。

## <a name="validate-the-consul-installation"></a>驗證 Consul 安裝

確認已成功建立資源。 使用[kubectl get svc][kubectl-get]和[kubectl get pod][kubectl-get]命令來查詢 `consul` 命名空間，其中 Consul 元件是由 `helm install` 命令所安裝：

```console
kubectl get svc --namespace consul --output wide
kubectl get pod --namespace consul --output wide
```

下列範例輸出顯示現在應該正在執行的服務和 pod （已排程在 Linux 節點上）：

```console
NAME                                 TYPE           CLUSTER-IP    EXTERNAL-IP             PORT(S)                                                                   AGE     SELECTOR
consul                               ExternalName   <none>        consul.service.consul   <none>                                                                    38s     <none>
consul-consul-connect-injector-svc   ClusterIP      10.0.98.102   <none>                  443/TCP                                                                   3m26s   app=consul,component=connect-injector,release=consul
consul-consul-dns                    ClusterIP      10.0.46.194   <none>                  53/TCP,53/UDP                                                             3m26s   app=consul,hasDNS=true,release=consul
consul-consul-server                 ClusterIP      None          <none>                  8500/TCP,8301/TCP,8301/UDP,8302/TCP,8302/UDP,8300/TCP,8600/TCP,8600/UDP   3m26s   app=consul,component=server,release=consul
consul-consul-ui                     ClusterIP      10.0.50.188   <none>                  80/TCP                                                                    3m26s   app=consul,component=server,release=consul

NAME                                                              READY   STATUS    RESTARTS   AGE    IP            NODE                            NOMINATED NODE   READINESS GATES
consul-consul-connect-injector-webhook-deployment-99f74fdbcr5zj   1/1     Running   0          3m9s   10.240.0.68   aks-linux-92468653-vmss000002   <none>           <none>
consul-consul-jbksc                                               1/1     Running   0          3m9s   10.240.0.44   aks-linux-92468653-vmss000001   <none>           <none>
consul-consul-jkwtq                                               1/1     Running   0          3m9s   10.240.0.70   aks-linux-92468653-vmss000002   <none>           <none>
consul-consul-server-0                                            1/1     Running   0          3m9s   10.240.0.91   aks-linux-92468653-vmss000002   <none>           <none>
consul-consul-server-1                                            1/1     Running   0          3m9s   10.240.0.38   aks-linux-92468653-vmss000001   <none>           <none>
consul-consul-server-2                                            1/1     Running   0          3m9s   10.240.0.10   aks-linux-92468653-vmss000000   <none>           <none>
consul-consul-sync-catalog-d846b79c-8ssr8                         1/1     Running   2          3m9s   10.240.0.94   aks-linux-92468653-vmss000002   <none>           <none>
consul-consul-tz2t5                                               1/1     Running   0          3m9s   10.240.0.12   aks-linux-92468653-vmss000000   <none>           <none>
```

所有 pod 都應該顯示 `Running`的狀態。 如果您的 Pod 沒有這些狀態，請等候一兩分鐘直到其成為該狀態。 如果有任何 pod 回報問題，請使用[kubectl 描述 pod][kubectl-describe]命令來檢查其輸出和狀態。

## <a name="accessing-the-consul-ui"></a>存取 Consul UI

Consul UI 已安裝在上述的安裝程式中，並提供以 UI 為基礎的 Consul 設定。 Consul 的 UI 不會透過外部 ip 位址公開公開。 若要存取 Consul 使用者介面，請使用[kubectl 埠轉送][kubectl-port-forward]命令。 此命令會在您的用戶端電腦與 AKS 叢集中的相關 pod 之間建立安全連線。

```azurecli
kubectl port-forward -n consul svc/consul-consul-ui 8080:80
```

您現在可以開啟瀏覽器，並將它指向 `http://localhost:8080/ui` 以開啟 Consul UI。 當您開啟 UI 時，應該會看到下列內容：

![Consul UI](./media/servicemesh/consul/consul-ui.png)

## <a name="uninstall-consul-from-aks"></a>從 AKS 卸載 Consul

> [!WARNING]
> 從執行中的系統刪除 Consul 可能會導致您的服務之間發生流量相關的問題。 請確定您已在不 Consul 的情況下，將您的系統布建為正常運作，再繼續進行。

### <a name="remove-consul-components-and-namespace"></a>移除 Consul 元件和命名空間

若要從您的 AKS 叢集中移除 Consul，請使用下列命令。 `helm delete` 命令會移除 `consul` 的圖表，而 `kubectl delete namespace` 命令會移除 `consul` 命名空間。

```azurecli
helm delete --purge consul
kubectl delete namespace consul
```

## <a name="next-steps"></a>後續步驟

若要探索 Consul 的更多安裝和設定選項，請參閱下列官方 Consul 文章：

- [Consul-Helm 安裝指南][consul-install-k8]
- [Consul-Helm 安裝選項][consul-install-helm-options]

您也可以使用來遵循其他案例：

- [Consul 範例應用程式][consul-app-example]

<!-- LINKS - external -->
[Hashicorp]: https://hashicorp.com
[cosul-github]: https://github.com/hashicorp/consul
[helm]: https://helm.sh

[consul-docs-concepts]: https://www.consul.io/docs/platform/k8s/index.html
[consul-github]: https://github.com/hashicorp/consul
[consul-github-releases]: https://github.com/hashicorp/consul/releases
[consul-release-notes]: https://github.com/hashicorp/consul/blob/master/CHANGELOG.md
[consul-install-download]: https://www.consul.io/downloads.html
[consul-install-k8]: https://www.consul.io/docs/platform/k8s/run.html
[consul-install-helm-options]: https://www.consul.io/docs/platform/k8s/helm.html#configuration-values-
[consul-app-example]: https://github.com/hashicorp/demo-consul-101/tree/master/k8s
[install-wsl]: https://docs.microsoft.com/windows/wsl/install-win10

[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-port-forward]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#port-forward
[kubernetes-node-selectors]: https://docs.microsoft.com/azure/aks/concepts-clusters-workloads#node-selectors

<!-- LINKS - internal -->
[aks-quickstart]: ./kubernetes-walkthrough.md
[consul-scenario-mtls]: ./consul-mtls.md
[helm-install]: ./kubernetes-helm.md
