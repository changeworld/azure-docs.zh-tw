---
title: 在 Azure 庫伯奈斯服務 （AKS） 中安裝連結
description: 瞭解如何安裝和使用 Linkerd 在 Azure 庫伯奈斯服務 （AKS） 群集中創建服務網格
author: paulbouwer
ms.topic: article
ms.date: 10/09/2019
ms.author: pabouwer
zone_pivot_groups: client-operating-system
ms.openlocfilehash: 419b61527b68299c82dec4f2f5da6b0220859cc1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77593703"
---
# <a name="install-linkerd-in-azure-kubernetes-service-aks"></a>在 Azure 庫伯奈斯服務 （AKS） 中安裝連結

[林克德][linkerd-github]是一個開源服務網格和[CNCF孵化專案][linkerd-cncf]。 Linkerd 是一個超輕服務網格，提供包括流量管理、服務標識和安全性、可靠性和可觀察性等功能。 有關 Linkerd 的詳細資訊，請參閱官方的[Linkerd 常見問題解答][linkerd-faq]和[連結器體系結構][linkerd-architecture]文檔。

本文介紹如何安裝 Linkerd。 Linkerd`linkerd`用戶端二進位檔案安裝在用戶端電腦上，Linkerd 元件安裝在 AKS 上的 Kubernetes 群集中。

> [!NOTE]
> 這些說明引用連結版本`stable-2.6.0`。
>
> Linkerd`stable-2.6.x`可以針對庫伯內斯版本`1.13+`運行。 您可以在[GitHub - Linkerd 版本中][linkerd-github-releases]找到其他穩定和邊緣連結版本。

在本文中，您將學會如何：

> [!div class="checklist"]
> * 下載並安裝連結連結用戶端二進位檔案
> * 在 AKS 上安裝連結
> * 驗證連結安裝
> * 訪問儀表板
> * 從 AKS 卸載連結

## <a name="before-you-begin"></a>開始之前

本文中詳述的步驟假定您已經創建了 AKS 群集（啟用了 RBAC 的`1.13`Kubernetes 及以上群集），並建立了與`kubectl`群集的連接。 如果您需要前述任何方面的協助，請參閱 [AKS 快速入門][aks-quickstart]。

所有 Linkerd pod 都必須安排在 Linux 節點上運行 - 此設置是下面詳述的安裝方法中的預設值，無需其他配置。

本文將 Linkerd 安裝指南分為幾個離散步驟。 結果與官方的Linkerd開始[指導][linkerd-getting-started]的結構相同。

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Linux - download and install client binary](includes/servicemesh/linkerd/install-client-binary-linux.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [MacOS - download and install client binary](includes/servicemesh/linkerd/install-client-binary-macos.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [Windows - download and install client binary](includes/servicemesh/linkerd/install-client-binary-windows.md)]

::: zone-end

## <a name="install-linkerd-on-aks"></a>在 AKS 上安裝連結

在安裝 Linkerd 之前，我們將運行安裝前檢查，以確定控制平面是否可以安裝在我們的 AKS 群集上：

```console
linkerd check --pre
```

您應該會看到類似以下內容，以指示 AKS 群集是 Linkerd 的有效安裝目標：

```console
kubernetes-api
--------------
√ can initialize the client
√ can query the Kubernetes API

kubernetes-version
------------------
√ is running the minimum Kubernetes API version
√ is running the minimum kubectl version

pre-kubernetes-setup
--------------------
√ control plane namespace does not already exist
√ can create Namespaces
√ can create ClusterRoles
√ can create ClusterRoleBindings
√ can create CustomResourceDefinitions
√ can create PodSecurityPolicies
√ can create ServiceAccounts
√ can create Services
√ can create Deployments
√ can create CronJobs
√ can create ConfigMaps
√ no clock skew detected

pre-kubernetes-capability
-------------------------
√ has NET_ADMIN capability
√ has NET_RAW capability

pre-linkerd-global-resources
----------------------------
√ no ClusterRoles exist
√ no ClusterRoleBindings exist
√ no CustomResourceDefinitions exist
√ no MutatingWebhookConfigurations exist
√ no ValidatingWebhookConfigurations exist
√ no PodSecurityPolicies exist

linkerd-version
---------------
√ can determine the latest version
√ cli is up-to-date

Status check results are √
```

現在是時候安裝 Linkerd 元件了。 使用`linkerd`和`kubectl`二進位檔案將 Linkerd 元件安裝到 AKS 群集中。 將自動`linkerd`創建一個命名空間，並將元件安裝到此命名空間中。

```console
linkerd install | kubectl apply -f -
```

連結部署多個物件。 您將從上面`linkerd install`的命令輸出中看到清單。 根據群集環境，連結元件的部署大約需要 1 分鐘才能完成。

此時，您已將 Linkerd 部署到 AKS 群集。 為了確保我們成功部署 Linkerd，讓我們繼續下一節以[驗證 Linkerd 安裝](#validate-the-linkerd-installation)。

## <a name="validate-the-linkerd-installation"></a>驗證連結安裝

確認已成功創建資源。 使用[kubectl get svc][kubectl-get]和[kubectl][kubectl-get] get `linkerd` pod 命令來查詢命名空間，`linkerd install`該名稱空間由命令安裝 Linkerd 元件的位置：

```console
kubectl get svc --namespace linkerd --output wide
kubectl get pod --namespace linkerd --output wide
```

以下示例輸出顯示了現在應該運行的服務和 pod（在 Linux 節點上安排）：

```console
NAME                     TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)             AGE  SELECTOR
linkerd-controller-api   ClusterIP   10.0.110.67    <none>        8085/TCP            66s  linkerd.io/control-plane-component=controller
linkerd-destination      ClusterIP   10.0.224.29    <none>        8086/TCP            66s  linkerd.io/control-plane-component=controller
linkerd-dst              ClusterIP   10.0.225.148   <none>        8086/TCP            66s  linkerd.io/control-plane-component=destination
linkerd-grafana          ClusterIP   10.0.61.124    <none>        3000/TCP            65s  linkerd.io/control-plane-component=grafana
linkerd-identity         ClusterIP   10.0.6.104     <none>        8080/TCP            67s  linkerd.io/control-plane-component=identity
linkerd-prometheus       ClusterIP   10.0.27.168    <none>        9090/TCP            65s  linkerd.io/control-plane-component=prometheus
linkerd-proxy-injector   ClusterIP   10.0.100.133   <none>        443/TCP             64s  linkerd.io/control-plane-component=proxy-injector
linkerd-sp-validator     ClusterIP   10.0.221.5     <none>        443/TCP             64s  linkerd.io/control-plane-component=sp-validator
linkerd-tap              ClusterIP   10.0.18.14     <none>        8088/TCP,443/TCP    64s  linkerd.io/control-plane-component=tap
linkerd-web              ClusterIP   10.0.37.108    <none>        8084/TCP,9994/TCP   66s  linkerd.io/control-plane-component=web

NAME                                      READY   STATUS    RESTARTS   AGE   IP            NODE                            NOMINATED NODE   READINESS GATES
linkerd-controller-66ddc9f94f-cm9kt       3/3     Running   0          66s   10.240.0.50   aks-linux-16165125-vmss000001   <none>           <none>
linkerd-destination-c94bc454-qpkng        2/2     Running   0          66s   10.240.0.78   aks-linux-16165125-vmss000002   <none>           <none>
linkerd-grafana-6868fdcb66-4cmq2          2/2     Running   0          65s   10.240.0.69   aks-linux-16165125-vmss000002   <none>           <none>
linkerd-identity-74d8df4b85-tqq8f         2/2     Running   0          66s   10.240.0.48   aks-linux-16165125-vmss000001   <none>           <none>
linkerd-prometheus-699587cf8-k8ghg        2/2     Running   0          65s   10.240.0.41   aks-linux-16165125-vmss000001   <none>           <none>
linkerd-proxy-injector-6556447f64-n29wr   2/2     Running   0          64s   10.240.0.32   aks-linux-16165125-vmss000000   <none>           <none>
linkerd-sp-validator-56745cd567-v4x7h     2/2     Running   0          64s   10.240.0.6    aks-linux-16165125-vmss000000   <none>           <none>
linkerd-tap-5cd9fc566-ct988               2/2     Running   0          64s   10.240.0.15   aks-linux-16165125-vmss000000   <none>           <none>
linkerd-web-774c79b6d5-dhhwf              2/2     Running   0          65s   10.240.0.70   aks-linux-16165125-vmss000002   <none>           <none>
```

Linkerd 通過`linkerd`用戶端二進位檔案提供命令，以驗證林克德控制平面是否已成功安裝和配置。

```console
linkerd check
```

您應該會看到類似以下內容，以指示您的安裝成功：

```console
kubernetes-api
--------------
√ can initialize the client
√ can query the Kubernetes API

kubernetes-version
------------------
√ is running the minimum Kubernetes API version
√ is running the minimum kubectl version

linkerd-config
--------------
√ control plane Namespace exists
√ control plane ClusterRoles exist
√ control plane ClusterRoleBindings exist
√ control plane ServiceAccounts exist
√ control plane CustomResourceDefinitions exist
√ control plane MutatingWebhookConfigurations exist
√ control plane ValidatingWebhookConfigurations exist
√ control plane PodSecurityPolicies exist

linkerd-existence
-----------------
√ 'linkerd-config' config map exists
√ heartbeat ServiceAccount exist
√ control plane replica sets are ready
√ no unschedulable pods
√ controller pod is running
√ can initialize the client
√ can query the control plane API

linkerd-api
-----------
√ control plane pods are ready
√ control plane self-check
√ [kubernetes] control plane can talk to Kubernetes
√ [prometheus] control plane can talk to Prometheus
√ no invalid service profiles

linkerd-version
---------------
√ can determine the latest version
√ cli is up-to-date

control-plane-version
---------------------
√ control plane is up-to-date
√ control plane and cli versions match

Status check results are √
```

## <a name="access-the-dashboard"></a>訪問儀表板

Linkerd 附帶一個儀表板，用於深入瞭解服務網格和工作負載。 要訪問儀表板，請使用 命令`linkerd dashboard`。 此命令利用[kubectl 埠轉發][kubectl-port-forward]在用戶端電腦和 AKS 群集中的相關 pod 之間創建安全連線。 然後，它將自動打開預設瀏覽器中的儀表板。

```console
linkerd dashboard
```

該命令還將創建一個埠轉發，並返回 Grafana 儀表板的連結。

```console
Linkerd dashboard available at:
http://127.0.0.1:50750
Grafana dashboard available at:
http://127.0.0.1:50750/grafana
Opening Linkerd dashboard in the default browser
```

## <a name="uninstall-linkerd-from-aks"></a>從 AKS 卸載連結

> [!WARNING]
> 從正在運行的系統中刪除 Linkerd 可能會導致服務之間的流量相關問題。 在繼續之前，請確保您已為系統在不使用 Linkerd 的情況下仍正常運行做出了規定。

首先，您需要刪除資料平面代理。 從工作負載命名空間中刪除任何自動代理注入[注釋][linkerd-automatic-proxy-injection]，並推出工作負載部署。 您的工作負載不應再具有任何關聯的資料平面元件。

最後，按照如下方式拆下控制平面：

```console
linkerd install --ignore-cluster | kubectl delete -f -
```

## <a name="next-steps"></a>後續步驟

要探索 Linkerd 的更多安裝和配置選項，請參閱以下官方連結指南：

- [連結 - 頭盔安裝][linkerd-install-with-helm]
- [連結 - 多階段安裝，以滿足角色特權][linkerd-multi-stage-installation]

您還可以使用以下其他方案：

- [連結表情符號演示][linkerd-demo-emojivoto]
- [連結書籍演示][linkerd-demo-books]

<!-- LINKS - external -->

[linkerd]: https://linkerd.io/
[linkerd-cncf]: https://landscape.cncf.io/selected=linkerd
[linkerd-faq]: https://linkerd.io/2/faq/
[linkerd-architecture]: https://linkerd.io/2/reference/architecture/
[linkerd-getting-started]: https://linkerd.io/2/getting-started/
[linkerd-overview]: https://linkerd.io/2/overview/
[linkerd-github]: https://github.com/linkerd/linkerd2
[linkerd-github-releases]: https://github.com/linkerd/linkerd2/releases/

[linkerd-install-with-helm]: https://linkerd.io/2/tasks/install-helm/
[linkerd-multi-stage-installation]: https://linkerd.io/2/tasks/install/#multi-stage-install
[linkerd-automatic-proxy-injection]: https://linkerd.io/2/features/proxy-injection/

[linkerd-demo-emojivoto]: https://linkerd.io/2/getting-started/#step-5-install-the-demo-app
[linkerd-demo-books]: https://linkerd.io/2/tasks/books/

[helm]: https://helm.sh

[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-port-forward]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#port-forward

<!-- LINKS - internal -->
[aks-quickstart]: ./kubernetes-walkthrough.md
