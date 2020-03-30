---
title: 透過 Istio 在 Azure Kubernetes Service (AKS) 中使用智慧型路由和 Canary 版本
description: 了解如何使用 Istio 在 Azure Kubernetes Service (AKS) 叢集中提供智慧型路由及部署 Canary 版本
author: paulbouwer
ms.topic: article
ms.date: 10/09/2019
ms.author: pabouwer
zone_pivot_groups: client-operating-system
ms.openlocfilehash: 4a695957c287e69ff6b40e5a01254a729eaae441
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78272996"
---
# <a name="use-intelligent-routing-and-canary-releases-with-istio-in-azure-kubernetes-service-aks"></a>透過 Istio 在 Azure Kubernetes Service (AKS) 中使用智慧型路由和 Canary 版本

[Istio][istio-github] 是一種開放原始碼服務網格，可在 Kubernetes 叢集中提供跨微服務的主要功能集。 這些功能包括流量管理、服務識別和安全性、原則強制執行和可檢視性。 如需 Istio 的詳細資訊，請參閱官方文件[什麼是 Istio？][istio-docs-concepts]。

本文將說明如何使用 Istio 的流量管理功能。 我們將使用範例 AKS 投票應用程式來探索智慧型路由和 Canary 版本。

在本文中，您將學會如何：

> [!div class="checklist"]
> * 部署應用程式
> * 更新應用程式
> * 推出應用程式的 Canary 版本
> * 完成推出

## <a name="before-you-begin"></a>開始之前

> [!NOTE]
> 此方案已針對 Istio 版本`1.3.2`進行了測試。

本文中詳述的步驟假定您已經創建了 AKS 群集（啟用了 RBAC`1.13`的 Kubernetes 及以上群集），並建立了`kubectl`與群集的連接。 您還需要在群集中安裝 Istio。

如果您需要任何這些專案的説明，請參閱 AKS 指南中的[AKS 快速入門][aks-quickstart]和[安裝 Istio。][istio-install]

## <a name="about-this-application-scenario"></a>關於此應用程式的案例

示例 AKS 投票應用為使用者提供了兩個投票選項 （**貓**或**狗**）。 有一項儲存體元件會保存各個選項的投票數。 此外，還有一項分析元件會提供與每個選項的投票有關的詳細資料。

在此應用程式方案中，首先部署投票應用的版本`1.0`和分析元件的版本。 `1.0` 此分析元件會提供簡單的投票計數。 投票應用和分析元件與由 Redis`1.0`支援的存儲元件版本進行交互。

將分析元件升級到提供計數的版本`1.1`，現在提供總計和百分比。

使用者子集通過金絲雀`2.0`版本測試應用的版本。 這個新版本會使用由 MySQL 資料庫支援的儲存體元件。

確信版本`2.0`在使用者子集上按預期工作後，將版本`2.0`推出給所有使用者。

## <a name="deploy-the-application"></a>部署應用程式

首先我們將在您的 Azure Kubernetes Service (AKS) 叢集中部署應用程式。 下圖顯示了本節末尾運行的內容 - 所有元件的版本`1.0`，這些元件都通過 Istio 入口閘道提供服務的入站請求：

![AKS 投票應用程式元件和路由。](media/servicemesh/istio/scenario-routing-components-01.png)

您依照本文指示操作時所需的成品，可從 [Azure-Samples/aks-voting-app][github-azure-sample] GitHub 存放庫取得。 您可以下載成品或複製存放庫，如下所示：

```console
git clone https://github.com/Azure-Samples/aks-voting-app.git
```

在下載/複製的存放庫中切換至下列資料夾，並從這個資料夾執行所有後續步驟：

```console
cd aks-voting-app/scenarios/intelligent-routing-with-istio
```

首先，在 AKS 群集中為示例 AKS 投票應用創建命名`voting`空間，如下所示：

```console
kubectl create namespace voting
```

為此命名空間加上 `istio-injection=enabled` 的標籤。 此標籤會指示 istio 在此命名空間內的所有 Pod 中自動插入 istio-proxy 作為側車。

```console
kubectl label namespace voting istio-injection=enabled
```

現在，我們將建立 AKS 投票應用程式的元件。 在上一步中`voting`創建的命名空間中創建這些元件。

```console
kubectl apply -f kubernetes/step-1-create-voting-app.yaml --namespace voting
```

以下示例輸出顯示了正在創建的資源：

```output
deployment.apps/voting-storage-1-0 created
service/voting-storage created
deployment.apps/voting-analytics-1-0 created
service/voting-analytics created
deployment.apps/voting-app-1-0 created
service/voting-app created
```

> [!NOTE]
> Istio 在 Pod 和服務方面會有某些特定需求。 如需詳細資訊，請參閱 [Istio 的 Pod 和服務需求文件][istio-requirements-pods-and-services]。

若要查看已建立的 Pod，請使用 [kubectl get pods][kubectl-get] 命令，如下所示：

```console
kubectl get pods -n voting --show-labels
```

下面的示例輸出顯示`voting-app`，有三個窗格實例和`voting-analytics`和`voting-storage`pod 的單個實例。 每個 Pod 有兩個容器。 其中一個容器是元件，另一個是 ： `istio-proxy`

```output
NAME                                    READY     STATUS    RESTARTS   AGE   LABELS
voting-analytics-1-0-57c7fccb44-ng7dl   2/2       Running   0          39s   app=voting-analytics,pod-template-hash=57c7fccb44,version=1.0
voting-app-1-0-956756fd-d5w7z           2/2       Running   0          39s   app=voting-app,pod-template-hash=956756fd,version=1.0
voting-app-1-0-956756fd-f6h69           2/2       Running   0          39s   app=voting-app,pod-template-hash=956756fd,version=1.0
voting-app-1-0-956756fd-wsxvt           2/2       Running   0          39s   app=voting-app,pod-template-hash=956756fd,version=1.0
voting-storage-1-0-5d8fcc89c4-2jhms     2/2       Running   0          39s   app=voting-storage,pod-template-hash=5d8fcc89c4,version=1.0
```

要查看有關 pod 的資訊，我們將使用[kubectl 描述帶][kubectl-describe]標籤選擇器的 pod 命令來`voting-analytics`選擇該窗格。 我們將篩選輸出以顯示窗格中存在的兩個容器的詳細資訊：

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Bash - routing scenario - show autoinjected proxy](includes/servicemesh/istio/scenario-routing-show-proxy-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [Bash - routing scenario - show autoinjected proxy](includes/servicemesh/istio/scenario-routing-show-proxy-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [PowerShell - routing scenario - show autoinjected proxy](includes/servicemesh/istio/scenario-routing-show-proxy-powershell.md)]

::: zone-end

在建立 Istio [閘道][istio-reference-gateway]和[虛擬服務][istio-reference-virtualservice]前，您無法連線至投票應用程式。 這些 Istio 資源會將來自預設 Istio 輸入閘道的流量路由至我們的應用程式。

> [!NOTE]
> **閘道**是位於服務網格邊緣的元件，會接收輸入或輸出的 HTTP 和 TCP 流量。
> 
> **虛擬服務**會為一或多個目的地服務定義一組路由規則。

使用`kubectl apply`命令部署閘道和虛擬服務 yaml。 請記住指定這些資源部署到的命名空間。

```console
kubectl apply -f istio/step-1-create-voting-app-gateway.yaml --namespace voting
```

以下示例輸出顯示正在創建的新閘道和虛擬服務：

```output
virtualservice.networking.istio.io/voting-app created
gateway.networking.istio.io/voting-app-gateway created
```

使用下列命令取得 Istio 輸入閘道的 IP 位址：

```output
kubectl get service istio-ingressgateway --namespace istio-system -o jsonpath='{.status.loadBalancer.ingress[0].ip}'
```

下列範例輸出顯示輸入閘道的 IP 位址：

```output
20.188.211.19
```

開啟瀏覽器並貼上 IP 位址。 範例 AKS 投票應用程式隨即顯示。

![在已啟用 Istio 的 AKS 叢集中執行的 AKS 投票應用程式。](media/servicemesh/istio/scenario-routing-deploy-app-01.png)

螢幕底部的資訊顯示應用使用`1.0`和`voting-app``1.0``voting-storage`版本 （Redis）。

## <a name="update-the-application"></a>更新應用程式

讓我們部署分析元件的新版本。 此新版本`1.1`除了顯示每個類別的計數外，還顯示總計和百分比。

下圖顯示了本節末尾將運行的內容 - 只有元件`1.1``voting-analytics`的版本具有從元件路由的`voting-app`流量。 即使我們`1.0``voting-analytics`的元件版本繼續運行並由`voting-analytics`服務引用，Istio 代理也不允許流量來自它。

![AKS 投票應用程式元件和路由。](media/servicemesh/istio/scenario-routing-components-02.png)

讓我們部署元件`1.1`的版本`voting-analytics`。 在命名空間中`voting`創建此元件：

```console
kubectl apply -f kubernetes/step-2-update-voting-analytics-to-1.1.yaml --namespace voting
```

以下示例輸出顯示了正在創建的資源：

```output
deployment.apps/voting-analytics-1-1 created
```

使用在上一個步驟中取得的 Istio 輸入閘道 IP 位址，再次在瀏覽器中開啟範例 AKS 投票應用程式。

您的瀏覽器會在兩個檢視之間交替顯示，如下所示。 由於您對只有一個標籤選擇器[Service][kubernetes-service]（）`voting-analytics``app: voting-analytics`的元件使用 Kubernetes 服務，因此 Kubernetes 使用與該選擇器匹配的窗格之間的預設迴圈行為。 在這種情況下，它既是版本`1.0`，也是`1.1`您的`voting-analytics`窗格。

![在 AKS 投票應用程式中執行的 1.0 版分析元件。](media/servicemesh/istio/scenario-routing-deploy-app-01.png)

![在 AKS 投票應用程式中執行的 1.1 版分析元件。](media/servicemesh/istio/scenario-routing-update-app-01.png)

您可以按如下方式視覺化`voting-analytics`元件的兩個版本之間的切換。 請務必使用您個人 Istio 輸入閘道的 IP 位址。

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Bash - routing scenario - loop through results](includes/servicemesh/istio/scenario-routing-loop-results-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [Bash - routing scenario - loop through results](includes/servicemesh/istio/scenario-routing-loop-results-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [PowerShell - routing scenario - loop through results](includes/servicemesh/istio/scenario-routing-loop-results-powershell.md)]

::: zone-end

下列範例輸出顯示在不同版本間切換網站時，所傳回網站的對應部分：

```output
  <div id="results"> Cats: 2 | Dogs: 4 </div>
  <div id="results"> Cats: 2 | Dogs: 4 </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
  <div id="results"> Cats: 2 | Dogs: 4 </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
```

### <a name="lock-down-traffic-to-version-11-of-the-application"></a>鎖定對 1.1 版應用程式的流量

現在，讓我們將流量鎖定`1.1`為元件的`voting-analytics`只版本和元件的版本。 `1.0` `voting-storage` 接著，您會定義所有其他元件的路由規則。

> * **虛擬服務**會為一或多個目的地服務定義一組路由規則。
> * **目的地規則**會定義流量原則和版本特定原則。
> * **原則**會定義可用於工作負載的驗證方法。

使用`kubectl apply`命令替換虛擬`voting-app`服務定義，並為其他元件添加[目標規則][istio-reference-destinationrule]和[虛擬服務][istio-reference-virtualservice]。 您將向`voting`命名空間添加[策略][istio-reference-policy]，以確保使用相互 TLS 和用戶端憑證保護服務之間的所有通信。

* 策略已`peers.mtls.mode`設置為`STRICT`確保在`voting`命名空間中的服務之間強制執行相互 TLS。
* 我們還`ISTIO_MUTUAL`在所有目的地規則`trafficPolicy.tls.mode`中設置了 Istio 可提供具有增強式身分識別的服務，並使用 Istio 可明確管理的相互 TLS 和用戶端憑證保護服務之間的通訊。

```console
kubectl apply -f istio/step-2-update-and-add-routing-for-all-components.yaml --namespace voting
```

以下示例輸出顯示正在更新/創建的新策略、目標規則和虛擬服務：

```output
virtualservice.networking.istio.io/voting-app configured
policy.authentication.istio.io/default created
destinationrule.networking.istio.io/voting-app created
destinationrule.networking.istio.io/voting-analytics created
virtualservice.networking.istio.io/voting-analytics created
destinationrule.networking.istio.io/voting-storage created
virtualservice.networking.istio.io/voting-storage created
```

如果在瀏覽器中再次打開 AKS 投票應用，則元件僅使用`1.1``voting-analytics`該元件的`voting-app`新版本。

![在 AKS 投票應用程式中執行的 1.1 版分析元件。](media/servicemesh/istio/scenario-routing-update-app-01.png)

您可以視覺化您現在僅路由到`1.1``voting-analytics`元件版本，如下所示。 請務必使用您個人 Istio 輸入閘道的 IP 位址：

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Bash - routing scenario - loop through results](includes/servicemesh/istio/scenario-routing-loop-results-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [Bash - routing scenario - loop through results](includes/servicemesh/istio/scenario-routing-loop-results-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [PowerShell - routing scenario - loop through results](includes/servicemesh/istio/scenario-routing-loop-results-powershell.md)]

::: zone-end

下列範例輸出顯示所傳回網站的對應部分：

```output
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
```

現在，讓我們確認 Istio 正在使用相互 TLS 來保護我們每個服務之間的通信。 為此，我們將在`istioctl`用戶端二進位檔案中使用[authn tls-check][istioctl-authn-tls-check]命令，該命令採用以下形式。

```console
istioctl authn tls-check <pod-name[.namespace]> [<service>]
```

這組命令提供有關訪問指定服務的資訊，這些資訊來自命名空間中的所有 Pod，並匹配一組標籤：

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Bash - routing scenario - verify mtls](includes/servicemesh/istio/scenario-routing-verify-mtls-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [Bash - routing scenario - verify mtls](includes/servicemesh/istio/scenario-routing-verify-mtls-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [PowerShell - routing scenario - verify mtls](includes/servicemesh/istio/scenario-routing-verify-mtls-powershell.md)]

::: zone-end

以下示例輸出顯示，對於上述每個查詢，都強制執行了相互 TLS。 輸出還顯示執行相互 TLS 的策略和目標規則：

```output
# mTLS configuration between istio ingress pods and the voting-app service
HOST:PORT                                    STATUS     SERVER     CLIENT     AUTHN POLICY       DESTINATION RULE
voting-app.voting.svc.cluster.local:8080     OK         mTLS       mTLS       default/voting     voting-app/voting

# mTLS configuration between each of the voting-app pods and the voting-analytics service
HOST:PORT                                          STATUS     SERVER     CLIENT     AUTHN POLICY       DESTINATION RULE
voting-analytics.voting.svc.cluster.local:8080     OK         mTLS       mTLS       default/voting     voting-analytics/voting
HOST:PORT                                          STATUS     SERVER     CLIENT     AUTHN POLICY       DESTINATION RULE
voting-analytics.voting.svc.cluster.local:8080     OK         mTLS       mTLS       default/voting     voting-analytics/voting
HOST:PORT                                          STATUS     SERVER     CLIENT     AUTHN POLICY       DESTINATION RULE
voting-analytics.voting.svc.cluster.local:8080     OK         mTLS       mTLS       default/voting     voting-analytics/voting

# mTLS configuration between each of the voting-app pods and the voting-storage service
HOST:PORT                                        STATUS     SERVER     CLIENT     AUTHN POLICY       DESTINATION RULE
voting-storage.voting.svc.cluster.local:6379     OK         mTLS       mTLS       default/voting     voting-storage/voting
HOST:PORT                                        STATUS     SERVER     CLIENT     AUTHN POLICY       DESTINATION RULE
voting-storage.voting.svc.cluster.local:6379     OK         mTLS       mTLS       default/voting     voting-storage/voting
HOST:PORT                                        STATUS     SERVER     CLIENT     AUTHN POLICY       DESTINATION RULE
voting-storage.voting.svc.cluster.local:6379     OK         mTLS       mTLS       default/voting     voting-storage/voting

# mTLS configuration between each of the voting-analytics version 1.1 pods and the voting-storage service
HOST:PORT                                        STATUS     SERVER     CLIENT     AUTHN POLICY       DESTINATION RULE
voting-storage.voting.svc.cluster.local:6379     OK         mTLS       mTLS       default/voting     voting-storage/voting
```

## <a name="roll-out-a-canary-release-of-the-application"></a>推出應用程式的 Canary 版本

現在，`2.0`讓我們部署 的`voting-app``voting-analytics`新版本 的 和 元件`voting-storage`。 新`voting-storage`元件使用 MySQL 而不是 Redis，並`voting-app`更新`voting-analytics`和 元件以允許他們使用此新`voting-storage`元件。

元件`voting-app`現在支援功能標誌功能。 此功能旗標可讓您針對某部分的使用者測試 Istio 的 Canary 版本功能。

下圖顯示了您將在本節末尾運行的內容。

* `1.0` `voting-app`元件的版本`1.1`、`voting-analytics`元件的版本和`1.0``voting-storage`元件的版本能夠相互通信。
* `2.0` `voting-app`元件的版本`2.0`、`voting-analytics`元件的版本和`2.0``voting-storage`元件的版本能夠相互通信。
* `2.0`只有具有特定`voting-app`功能標誌集的使用者才能訪問元件的版本。 這項變更可透過 Cookie 使用功能旗標來管理。

![AKS 投票應用程式元件和路由。](media/servicemesh/istio/scenario-routing-components-03.png)

首先，請更新 Istio 目的地規則和虛擬服務，以因應這些新元件的需求。 這些更新可確保您不會錯誤地將流量路由至新元件，且使用者不會獲得非預期的存取：

```console
kubectl apply -f istio/step-3-add-routing-for-2.0-components.yaml --namespace voting
```

以下示例輸出顯示正在更新的目標規則和虛擬服務：

```output
destinationrule.networking.istio.io/voting-app configured
virtualservice.networking.istio.io/voting-app configured
destinationrule.networking.istio.io/voting-analytics configured
virtualservice.networking.istio.io/voting-analytics configured
destinationrule.networking.istio.io/voting-storage configured
virtualservice.networking.istio.io/voting-storage configured
```

接下來，讓我們為新版本`2.0`元件添加 Kubernetes 物件。 您還可以更新`voting-storage`服務以包括 MySQL`3306`的埠：

```console
kubectl apply -f kubernetes/step-3-update-voting-app-with-new-storage.yaml --namespace voting
```

下列範例輸出顯示 Kubernetes 物件已成功更新或建立：

```output
service/voting-storage configured
secret/voting-storage-secret created
deployment.apps/voting-storage-2-0 created
persistentvolumeclaim/mysql-pv-claim created
deployment.apps/voting-analytics-2-0 created
deployment.apps/voting-app-2-0 created
```

等待，直到所有版本`2.0`窗格都運行。 使用`-w`帶有手錶開關的[kubectl get pod 命令][kubectl-get]來監視命名空間中所有窗格上的`voting`更改：

```console
kubectl get pods --namespace voting -w
```

現在，您應該能夠在投票應用程式的版本`1.0`和版本`2.0`（金絲雀）之間切換。 畫面底部的功能旗標切換會設定 Cookie。 `voting-app`此 Cookie 被虛擬服務用於將使用者路由到新版本`2.0`。

![1.0 版的 AKS 投票應用程式 -「不會」設定功能旗標。](media/servicemesh/istio/scenario-routing-canary-release-01.png)

![2.0 版的 AKS 投票應用程式 -「會」設定功能旗標。](media/servicemesh/istio/scenario-routing-canary-release-02.png)

兩個應用程式版本的投票計數並不相同。 此差異凸顯出您使用了兩個不同的儲存體後端。

## <a name="finalize-the-rollout"></a>完成推出

成功測試金絲雀版本後，更新`voting-app`虛擬服務以將所有流量路由到`2.0``voting-app`元件的版本。 然後，所有使用者都會看到`2.0`應用程式的版本，無論功能標誌是否設置：

![AKS 投票應用程式元件和路由。](media/servicemesh/istio/scenario-routing-components-04.png)

更新所有目的地規則，以移除您不想再啟用的元件版本。 接著，更新所有虛擬服務以停止參考這些版本。

由於再也不會有任何流量進入任何較舊版本的元件，您現在可以安全地刪除這些元件的所有部署。

![AKS 投票應用程式元件和路由。](media/servicemesh/istio/scenario-routing-components-05.png)

您現在已成功推出新版的 AKS 投票應用程式。

## <a name="clean-up"></a>清除 

您可以通過刪除`voting`命名空間，如下所示，從 AKS 群集中刪除我們在此方案中使用的 AKS 投票應用：

```console
kubectl delete namespace voting
```

以下示例輸出顯示 AKS 投票應用的所有元件都已從 AKS 群集中刪除。

```output
namespace "voting" deleted
```

## <a name="next-steps"></a>後續步驟

您可以使用 [Istio Bookinfo 應用程式範例][istio-bookinfo-example]探索其他案例。

<!-- LINKS - external -->
[github-azure-sample]: https://github.com/Azure-Samples/aks-voting-app
[istio-github]: https://github.com/istio/istio

[istio]: https://istio.io
[istio-docs-concepts]: https://istio.io/docs/concepts/what-is-istio/
[istio-requirements-pods-and-services]: https://istio.io/docs/setup/kubernetes/prepare/requirements/
[istio-reference-gateway]: https://istio.io/docs/reference/config/networking/v1alpha3/gateway/
[istio-reference-policy]: https://istio.io/docs/reference/config/istio.authentication.v1alpha1/#Policy
[istio-reference-virtualservice]: https://istio.io/docs/reference/config/networking/v1alpha3/virtual-service/
[istio-reference-destinationrule]: https://istio.io/docs/reference/config/networking/v1alpha3/destination-rule/
[istio-bookinfo-example]: https://istio.io/docs/examples/bookinfo/
[istioctl-authn-tls-check]: https://istio.io/docs/reference/commands/istioctl/#istioctl-authn-tls-check

[kubernetes-service]: https://kubernetes.io/docs/concepts/services-networking/service/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe

<!-- LINKS - internal -->
[aks-quickstart]: ./kubernetes-walkthrough.md
[istio-install]: ./servicemesh-istio-install.md
