---
title: 透過 Istio 在 Azure Kubernetes Service (AKS) 中使用智慧型路由和 Canary 版本
description: 了解如何使用 Istio 在 Azure Kubernetes Service (AKS) 叢集中提供智慧型路由及部署 Canary 版本
author: paulbouwer
ms.topic: article
ms.date: 10/09/2019
ms.author: pabouwer
zone_pivot_groups: client-operating-system
ms.openlocfilehash: 4a695957c287e69ff6b40e5a01254a729eaae441
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/04/2020
ms.locfileid: "78272996"
---
# <a name="use-intelligent-routing-and-canary-releases-with-istio-in-azure-kubernetes-service-aks"></a>透過 Istio 在 Azure Kubernetes Service (AKS) 中使用智慧型路由和 Canary 版本

[Istio][istio-github]是一種開放原始碼服務網格，可在 Kubernetes 叢集中跨微服務提供一組重要的功能。 這些功能包括流量管理、服務識別和安全性、原則強制執行和可檢視性。 如需有關 Istio 的詳細資訊，請參閱官方[什麼是 Istio？][istio-docs-concepts]檔。

本文將說明如何使用 Istio 的流量管理功能。 我們將使用範例 AKS 投票應用程式來探索智慧型路由和 Canary 版本。

在本文中，您將學會如何：

> [!div class="checklist"]
> * 部署應用程式
> * 更新應用程式
> * 推出應用程式的 Canary 版本
> * 完成推出

## <a name="before-you-begin"></a>開始之前

> [!NOTE]
> 此案例已針對 Istio 版本 `1.3.2`進行測試。

本文中詳述的步驟假設您已建立 AKS 叢集（已啟用 RBAC 的 Kubernetes `1.13` 和更新版本），並已建立與叢集的 `kubectl` 連接。 您也需要在您的叢集中安裝 Istio。

如果您需要任何這些專案的協助，請參閱[AKS 快速入門][aks-quickstart]並[在 AKS 指引中安裝 Istio][istio-install] 。

## <a name="about-this-application-scenario"></a>關於此應用程式的案例

範例 AKS 投票應用程式會為使用者提供兩個投票選項（**貓**或**狗**）。 有一項儲存體元件會保存各個選項的投票數。 此外，還有一項分析元件會提供與每個選項的投票有關的詳細資料。

在此應用程式案例中，您一開始會先部署投票應用程式的版本 `1.0` 和分析元件的版本 `1.0`。 此分析元件會提供簡單的投票計數。 投票應用程式和分析元件會與儲存體元件的版本 `1.0` 互動，而 Redis 會提供支援。

您會將分析元件升級為版本 `1.1`，以提供計數，而現在總計和百分比。

使用者的子集會透過未測出的版本，來測試應用程式的版本 `2.0`。 這個新版本會使用由 MySQL 資料庫支援的儲存體元件。

一旦您確信版本 `2.0` 在您的使用者子集上如預期般運作，您就會將版本 `2.0` 推出給所有的使用者。

## <a name="deploy-the-application"></a>部署應用程式

首先我們將在您的 Azure Kubernetes Service (AKS) 叢集中部署應用程式。 下圖顯示本節結尾的執行內容-版本 `1.0` 的所有元件，以及透過 Istio 輸入閘道服務的輸入要求：

![AKS 投票應用程式元件和路由。](media/servicemesh/istio/scenario-routing-components-01.png)

您需要遵循這篇文章的成品可在[Azure 範例/aks-投票-應用程式][github-azure-sample]GitHub 存放庫中取得。 您可以下載成品或複製存放庫，如下所示：

```console
git clone https://github.com/Azure-Samples/aks-voting-app.git
```

在下載/複製的存放庫中切換至下列資料夾，並從這個資料夾執行所有後續步驟：

```console
cd aks-voting-app/scenarios/intelligent-routing-with-istio
```

首先，在 AKS 叢集中為名為 `voting` 的範例 AKS 投票應用程式建立命名空間，如下所示：

```console
kubectl create namespace voting
```

為此命名空間加上 `istio-injection=enabled` 的標籤。 此標籤會指示 istio 在此命名空間內的所有 Pod 中自動插入 istio-proxy 作為側車。

```console
kubectl label namespace voting istio-injection=enabled
```

現在，我們將建立 AKS 投票應用程式的元件。 在上一個步驟建立的 `voting` 命名空間中建立這些元件。

```console
kubectl apply -f kubernetes/step-1-create-voting-app.yaml --namespace voting
```

下列範例輸出顯示所建立的資源：

```output
deployment.apps/voting-storage-1-0 created
service/voting-storage created
deployment.apps/voting-analytics-1-0 created
service/voting-analytics created
deployment.apps/voting-app-1-0 created
service/voting-app created
```

> [!NOTE]
> Istio 在 Pod 和服務方面會有某些特定需求。 如需詳細資訊，請參閱 pod[和服務的 Istio 需求檔][istio-requirements-pods-and-services]。

若要查看已建立的 pod，請使用[kubectl get][kubectl-get] pod 命令，如下所示：

```console
kubectl get pods -n voting --show-labels
```

下列範例輸出顯示 `voting-app` pod 的三個實例，以及 `voting-analytics` 和 `voting-storage` pod 的單一實例。 每個 Pod 有兩個容器。 其中一個容器是元件，另一個則是 `istio-proxy`：

```output
NAME                                    READY     STATUS    RESTARTS   AGE   LABELS
voting-analytics-1-0-57c7fccb44-ng7dl   2/2       Running   0          39s   app=voting-analytics,pod-template-hash=57c7fccb44,version=1.0
voting-app-1-0-956756fd-d5w7z           2/2       Running   0          39s   app=voting-app,pod-template-hash=956756fd,version=1.0
voting-app-1-0-956756fd-f6h69           2/2       Running   0          39s   app=voting-app,pod-template-hash=956756fd,version=1.0
voting-app-1-0-956756fd-wsxvt           2/2       Running   0          39s   app=voting-app,pod-template-hash=956756fd,version=1.0
voting-storage-1-0-5d8fcc89c4-2jhms     2/2       Running   0          39s   app=voting-storage,pod-template-hash=5d8fcc89c4,version=1.0
```

若要查看 pod 的相關資訊，我們將使用[kubectl 說明 pod][kubectl-describe]命令搭配標籤選取器，以選取 `voting-analytics` pod。 我們會篩選輸出，以顯示 pod 中的兩個容器的詳細資料：

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Bash - routing scenario - show autoinjected proxy](includes/servicemesh/istio/scenario-routing-show-proxy-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [Bash - routing scenario - show autoinjected proxy](includes/servicemesh/istio/scenario-routing-show-proxy-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [PowerShell - routing scenario - show autoinjected proxy](includes/servicemesh/istio/scenario-routing-show-proxy-powershell.md)]

::: zone-end

您必須先建立 Istio[閘道][istio-reference-gateway]和[虛擬服務][istio-reference-virtualservice]，才能連接到投票應用程式。 這些 Istio 資源會將來自預設 Istio 輸入閘道的流量路由至我們的應用程式。

> [!NOTE]
> **閘道**是位於服務網格邊緣的元件，會接收輸入或輸出的 HTTP 和 TCP 流量。
> 
> **虛擬服務**會為一或多個目的地服務定義一組路由規則。

使用 `kubectl apply` 命令來部署閘道和虛擬服務 yaml。 請記得指定這些資源部署所在的命名空間。

```console
kubectl apply -f istio/step-1-create-voting-app-gateway.yaml --namespace voting
```

下列範例輸出顯示新的閘道和正在建立的虛擬服務：

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

畫面底部的資訊會顯示應用程式使用版本 `1.0` `voting-app` 和版本 `1.0` `voting-storage` （Redis）。

## <a name="update-the-application"></a>更新應用程式

讓我們部署新版本的分析元件。 這個新版本 `1.1` 除了每個類別的計數以外，還會顯示總計和百分比。

下圖顯示此章節的結尾所執行的內容-只有 `voting-analytics` 元件的 `1.1` 版本，才會從 `voting-app` 元件路由傳送流量。 雖然 `voting-analytics` 元件的版本 `1.0` 會繼續執行，且由 `voting-analytics` 服務所參考，但 Istio proxy 不允許進出它的流量。

![AKS 投票應用程式元件和路由。](media/servicemesh/istio/scenario-routing-components-02.png)

讓我們部署 `voting-analytics` 元件的版本 `1.1`。 在 `voting` 命名空間中建立此元件：

```console
kubectl apply -f kubernetes/step-2-update-voting-analytics-to-1.1.yaml --namespace voting
```

下列範例輸出顯示所建立的資源：

```output
deployment.apps/voting-analytics-1-1 created
```

使用在上一個步驟中取得的 Istio 輸入閘道 IP 位址，再次在瀏覽器中開啟範例 AKS 投票應用程式。

您的瀏覽器會在兩個檢視之間交替顯示，如下所示。 由於您針對只有單一標籤選取器（`app: voting-analytics`）的 `voting-analytics` 元件使用 Kubernetes[服務][kubernetes-service]，因此 Kubernetes 會在符合該選取器的 pod 之間，使用迴圈配置資源的預設行為。 在此情況下，它是 `voting-analytics` pod 的版本 `1.0` 和 `1.1`。

![在 AKS 投票應用程式中執行的 1.0 版分析元件。](media/servicemesh/istio/scenario-routing-deploy-app-01.png)

![在 AKS 投票應用程式中執行的 1.1 版分析元件。](media/servicemesh/istio/scenario-routing-update-app-01.png)

您可以將 `voting-analytics` 元件的兩個版本之間的切換視覺化，如下所示。 請務必使用您個人 Istio 輸入閘道的 IP 位址。

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

現在，讓我們將流量鎖定到 `voting-analytics` 元件的版本 `1.1`，以及 `voting-storage` 元件的版本 `1.0`。 接著，您會定義所有其他元件的路由規則。

> * **虛擬服務**會為一或多個目的地服務定義一組路由規則。
> * **目的地規則**會定義流量原則和版本特定原則。
> * **原則**會定義可用於工作負載的驗證方法。

使用 `kubectl apply` 命令來取代您 `voting-app` 上的虛擬服務定義，並為其他元件新增[目的地規則][istio-reference-destinationrule]和[虛擬服務][istio-reference-virtualservice]。 您會將[原則][istio-reference-policy]新增至 `voting` 命名空間，以確保服務之間的所有通訊都是使用相互 TLS 和用戶端憑證來保護。

* 原則 `peers.mtls.mode` 設定為 `STRICT`，以確保在 `voting` 命名空間內的服務之間強制執行相互 TLS。
* 我們也會將 `trafficPolicy.tls.mode` 設定為所有目的地規則中 `ISTIO_MUTUAL`。 Istio 可提供具有增強式身分識別的服務，並使用 Istio 可明確管理的相互 TLS 和用戶端憑證保護服務之間的通訊。

```console
kubectl apply -f istio/step-2-update-and-add-routing-for-all-components.yaml --namespace voting
```

下列範例輸出顯示要更新/建立的新原則、目的地規則和虛擬服務：

```output
virtualservice.networking.istio.io/voting-app configured
policy.authentication.istio.io/default created
destinationrule.networking.istio.io/voting-app created
destinationrule.networking.istio.io/voting-analytics created
virtualservice.networking.istio.io/voting-analytics created
destinationrule.networking.istio.io/voting-storage created
virtualservice.networking.istio.io/voting-storage created
```

如果您再次在瀏覽器中開啟 AKS 投票應用程式，`voting-app` 元件只會使用 `voting-analytics` 元件的新版本 `1.1`。

![在 AKS 投票應用程式中執行的 1.1 版分析元件。](media/servicemesh/istio/scenario-routing-update-app-01.png)

您可以將您現在只會路由至 `voting-analytics` 元件的版本 `1.1`，如下所示。 請務必使用您個人 Istio 輸入閘道的 IP 位址：

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

現在讓我們來確認 Istio 使用相互 TLS 來保護每個服務之間的通訊。 為此，我們將在 `istioctl` 用戶端二進位檔上使用[驗證的 tls 檢查][istioctl-authn-tls-check]命令，其採用下列格式。

```console
istioctl authn tls-check <pod-name[.namespace]> [<service>]
```

這組命令會從命名空間中的所有 pod，以及符合一組標籤的所有 pod，提供存取指定服務的相關資訊：

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Bash - routing scenario - verify mtls](includes/servicemesh/istio/scenario-routing-verify-mtls-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [Bash - routing scenario - verify mtls](includes/servicemesh/istio/scenario-routing-verify-mtls-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [PowerShell - routing scenario - verify mtls](includes/servicemesh/istio/scenario-routing-verify-mtls-powershell.md)]

::: zone-end

下列範例輸出顯示針對上述每個查詢強制執行相互 TLS。 輸出也會顯示強制執行相互 TLS 的原則和目的地規則：

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

現在讓我們來部署 `voting-app`、`voting-analytics`和 `voting-storage` 元件的新版本 `2.0`。 新的 `voting-storage` 元件會使用 MySQL，而不是 Redis，而且 `voting-app` 和 `voting-analytics` 元件會更新，讓他們能夠使用這個新的 `voting-storage` 元件。

`voting-app` 元件現在支援功能旗標功能。 此功能旗標可讓您針對某部分的使用者測試 Istio 的 Canary 版本功能。

下圖顯示您將在本節結尾處執行的內容。

* `voting-app` 元件的版本 `1.0`、`voting-analytics` 元件的版本 `1.1`，以及 `1.0` 元件的版本 `voting-storage` 能夠彼此通訊。
* `voting-app` 元件的版本 `2.0`、`voting-analytics` 元件的版本 `2.0`，以及 `2.0` 元件的版本 `voting-storage` 能夠彼此通訊。
* 只有已設定特定功能旗標的使用者，才能存取 `voting-app` 元件的版本 `2.0`。 這項變更可透過 Cookie 使用功能旗標來管理。

![AKS 投票應用程式元件和路由。](media/servicemesh/istio/scenario-routing-components-03.png)

首先，請更新 Istio 目的地規則和虛擬服務，以因應這些新元件的需求。 這些更新可確保您不會錯誤地將流量路由至新元件，且使用者不會獲得非預期的存取：

```console
kubectl apply -f istio/step-3-add-routing-for-2.0-components.yaml --namespace voting
```

下列範例輸出顯示要更新的目的地規則和虛擬服務：

```output
destinationrule.networking.istio.io/voting-app configured
virtualservice.networking.istio.io/voting-app configured
destinationrule.networking.istio.io/voting-analytics configured
virtualservice.networking.istio.io/voting-analytics configured
destinationrule.networking.istio.io/voting-storage configured
virtualservice.networking.istio.io/voting-storage configured
```

接下來，讓我們為新版本 `2.0` 元件新增 Kubernetes 物件。 您也會更新 `voting-storage` 服務，使其包含適用于 MySQL 的 `3306` 埠：

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

請等候所有版本 `2.0` pod 都在執行中。 使用[kubectl get][kubectl-get] pod 命令搭配 `-w` watch 參數，以監看 `voting` 命名空間中所有 pod 的變更：

```console
kubectl get pods --namespace voting -w
```

您現在應該可以在投票應用程式的版本 `1.0` 和版本 `2.0` （不帶）之間切換。 畫面底部的功能旗標切換會設定 Cookie。 此 cookie 是由 `voting-app` 虛擬服務用來將使用者路由至新版本 `2.0`。

![1\.0 版的 AKS 投票應用程式 -「不會」設定功能旗標。](media/servicemesh/istio/scenario-routing-canary-release-01.png)

![2\.0 版的 AKS 投票應用程式 -「會」設定功能旗標。](media/servicemesh/istio/scenario-routing-canary-release-02.png)

兩個應用程式版本的投票計數並不相同。 此差異凸顯出您使用了兩個不同的儲存體後端。

## <a name="finalize-the-rollout"></a>完成推出

當您成功測試了不好的版本後，請更新 `voting-app` 虛擬服務，將所有流量路由至 `voting-app` 元件的版本 `2.0`。 所有使用者接著會看到應用程式的版本 `2.0`，不論是否已設定功能旗標：

![AKS 投票應用程式元件和路由。](media/servicemesh/istio/scenario-routing-components-04.png)

更新所有目的地規則，以移除您不想再啟用的元件版本。 接著，更新所有虛擬服務以停止參考這些版本。

由於再也不會有任何流量進入任何較舊版本的元件，您現在可以安全地刪除這些元件的所有部署。

![AKS 投票應用程式元件和路由。](media/servicemesh/istio/scenario-routing-components-05.png)

您現在已成功推出新版的 AKS 投票應用程式。

## <a name="clean-up"></a>清除 

藉由刪除 `voting` 命名空間，您可以從 AKS 叢集移除我們在此案例中使用的 AKS 投票應用程式，如下所示：

```console
kubectl delete namespace voting
```

下列範例輸出顯示已從您的 AKS 叢集中移除 AKS 投票應用程式的所有元件。

```output
namespace "voting" deleted
```

## <a name="next-steps"></a>後續步驟

您可以使用[Istio Bookinfo 應用程式範例][istio-bookinfo-example]來探索其他案例。

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
