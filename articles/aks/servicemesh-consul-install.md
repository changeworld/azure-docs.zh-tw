---
title: 在 Azure 庫伯奈斯服務 （AKS） 中安裝領事
description: 瞭解如何在 Azure 庫伯奈斯服務 （AKS） 群集中安裝和使用 Consul 創建服務網格
author: dstrebel
ms.topic: article
ms.date: 10/09/2019
ms.author: dastrebe
zone_pivot_groups: client-operating-system
ms.openlocfilehash: 1601ab6d81b888fd2247e95f22c58e1fc91df698
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78273728"
---
# <a name="install-and-use-consul-in-azure-kubernetes-service-aks"></a>安裝和使用駐 Azure 庫伯奈斯服務 （AKS）

[Consul][consul-github]是一個開源服務網格，在 Kubernetes 群集中提供一組關鍵功能。 這些功能包括服務發現、運行狀況檢查、服務細分和可觀察性。 有關領事的更多資訊，請參閱官方["什麼是領事"][consul-docs-concepts]文檔。

本文介紹如何安裝領事。 領事元件安裝在 AKS 上的 Kubernetes 群集中。

> [!NOTE]
> 這些說明參考領事版本`1.6.0`，並使用至少赫爾姆版本。 `2.14.2`
>
> 領事`1.6.x`版本可以運行對庫伯內斯版本`1.13+`。 您可以在[GitHub - 領事版本][consul-github-releases]找到其他領事版本，以及[有關領事版本資訊][consul-release-notes]中每個版本的資訊。

在本文中，您將學會如何：

> [!div class="checklist"]
> * 在 AKS 上安裝駐外應器元件
> * 驗證領事安裝
> * AKS 卸載領事

## <a name="before-you-begin"></a>開始之前

本文中詳述的步驟假定您已經創建了 AKS 群集（啟用了 RBAC 的`1.13`Kubernetes 及以上群集），並建立了與`kubectl`群集的連接。 如果您需要前述任何方面的協助，請參閱 [AKS 快速入門][aks-quickstart]。 確保群集在 Linux 節點池中至少有 3 個節點。

您需要[Helm][helm]遵循這些說明並安裝領事。 建議您在群集中正確安裝和配置最新的穩定版本。 如果您需要安裝頭盔的説明，請參閱[AKS 頭盔安裝指南][helm-install]。 還必須安排所有領事窗格在 Linux 節點上運行。

本文將領事安裝指南分成幾個離散步驟。 最終結果與官方領事安裝[指南][consul-install-k8]的結構相同。

### <a name="install-the-consul-components-on-aks"></a>在 AKS 上安裝駐外應器元件

我們將首先下載領事赫爾姆圖的版本`v0.10.0`。 此版本的圖表包括領事版本`1.6.0`。

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Linux - download](includes/servicemesh/consul/download-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [macOS - download](includes/servicemesh/consul/download-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [Windows - download](includes/servicemesh/consul/download-powershell.md)]

::: zone-end

使用 Helm 和下載`consul-helm`的圖表將駐線器元件安裝`consul`到 AKS 群集中的命名空間中。 

> [!NOTE]
> **安裝選項**
> 
> 我們正在使用以下選項作為安裝的一部分：
> - `connectInject.enabled=true`- 使代理被注入到吊艙中
> - `client.enabled=true`- 使領事用戶端能夠在每個節點上運行
> - `client.grpc=true`- 啟用 gRPC 攔截器進行連接注入
> - `syncCatalog.enabled=true`- 同步庫伯內特斯和領事服務
>
> **節點選擇器**
>
> 當前必須安排在 Linux 節點上運行領事。 如果群集中具有 Windows Server 節點，則必須確保"領事"窗格僅計畫在 Linux 節點上運行。 我們將使用[節點選擇器][kubernetes-node-selectors]確保將 pod 安排到正確的節點。

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Bash - install Istio components](includes/servicemesh/consul/install-components-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [Bash - install Istio components](includes/servicemesh/consul/install-components-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [PowerShell - install Istio components](includes/servicemesh/consul/install-components-powershell.md)]

::: zone-end

Helm`Consul`圖表部署多個物件。 從上面`helm install`的命令的輸出中可以看到清單。 根據群集環境，使用"領事"元件大約需要 3 分鐘才能完成。

此時，您已將"領事"部署到 AKS 群集。 為確保我們成功部署領事，讓我們繼續下一節以驗證領事的安裝。

## <a name="validate-the-consul-installation"></a>驗證領事安裝

確認已成功創建資源。 使用[kubectl get svc][kubectl-get]和[kubectl][kubectl-get] get `consul` pod 命令來查詢名稱空間，`helm install`該名稱空間由命令安裝領事元件的位置：

```console
kubectl get svc --namespace consul --output wide
kubectl get pod --namespace consul --output wide
```

以下示例輸出顯示了現在應該運行的服務和 pod（在 Linux 節點上安排）：

```output
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

所有窗格都應顯示 的狀態`Running`。 如果您的 Pod 沒有這些狀態，請等候一兩分鐘直到其成為該狀態。 如果有任何 Pod 回報發生問題，請使用 [kubectl describe pod][kubectl-describe] 命令檢閱其輸出和狀態。

## <a name="accessing-the-consul-ui"></a>訪問領事 UI

領事 UI 安裝在我們的設置中，並為領事提供了基於 UI 的配置。 領事的 UI 不會通過外部 ip 位址公開公開。 要訪問領事使用者介面，請使用[庫布克特爾埠轉發][kubectl-port-forward]命令。 此命令在用戶端電腦和 AKS 群集中的相關 pod 之間創建安全連線。

```console
kubectl port-forward -n consul svc/consul-consul-ui 8080:80
```

您現在可以打開瀏覽器並將其`http://localhost:8080/ui`指向打開領事 UI。 打開 UI 時，應看到以下內容：

![領事 UI](./media/servicemesh/consul/consul-ui.png)

## <a name="uninstall-consul-from-aks"></a>AKS 卸載領事

> [!WARNING]
> 從正在運行的系統中刪除"領事"可能會導致服務之間的流量相關問題。 確保您已為系統在繼續操作之前仍正常運行做出規定。

### <a name="remove-consul-components-and-namespace"></a>刪除駐港伺服器元件和命名空間

要從 AKS 群集中刪除"領事"，請使用以下命令。 命令`helm delete`將刪除`consul`圖表，`kubectl delete namespace`該命令將刪除`consul`命名空間。

```console
helm delete --purge consul
kubectl delete namespace consul
```

## <a name="next-steps"></a>後續步驟

要探索領事的更多安裝和配置選項，請參閱以下正式領事文章：

- [領事 - 頭盔安裝指南][consul-install-k8]
- [領事 - 頭盔安裝選項][consul-install-helm-options]

您還可以使用以下其他方案：

- [領事應用程式範例][consul-app-example]

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
