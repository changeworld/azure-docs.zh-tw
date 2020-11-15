---
title: 使用公用 Load Balancer
titleSuffix: Azure Kubernetes Service
description: 瞭解如何搭配標準 SKU 使用公用負載平衡器，以 Azure Kubernetes Service (AKS) 公開您的服務。
services: container-service
ms.topic: article
ms.date: 06/14/2020
ms.author: jpalma
author: palma21
ms.openlocfilehash: 51cb79e942b9d92876bd4d0e2cc27bb5ee0337bf
ms.sourcegitcommit: 295db318df10f20ae4aa71b5b03f7fb6cba15fc3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/15/2020
ms.locfileid: "94634866"
---
# <a name="use-a-public-standard-load-balancer-in-azure-kubernetes-service-aks"></a>使用 Azure Kubernetes Service (AKS 中的公用 Standard Load Balancer) 

Azure Load Balancer 是在同時支援輸入和輸出案例的開放系統互相連線 (OSI) 模型的 L4 上。 它會將抵達負載平衡器前端的輸入流量分散至後端集區實例。

與 AKS 整合時的 **公用** Load Balancer 有兩個用途：

1. 提供對 AKS 虛擬網路內叢集節點的輸出連線。 它會將節點私人 IP 位址轉譯為其 *輸出集* 區一部分的公用 ip 位址，以達到此目標。
2. 若要透過類型的 Kubernetes 服務來提供應用程式的存取權 `LoadBalancer` 。 有了它，您就可以輕鬆地調整應用程式，並建立高可用性的服務。

**內部 (或私用)** 負載平衡器會在只允許私人 ip 作為前端時使用。 內部負載平衡器可用來對虛擬網路內的流量進行負載平衡。 負載平衡器前端也可以在混合式案例中從內部部署網路存取。

本檔涵蓋與公用負載平衡器的整合。 如需內部 Load Balancer 整合，請參閱 [AKS 內部負載平衡器檔](internal-lb.md)。

## <a name="before-you-begin"></a>開始之前

Azure Load Balancer 有兩種 SKU -「基本」和「標準」。 依預設，當您建立 AKS 叢集時，會使用 *標準* SKU。 使用 *標準* SKU 可存取新增的功能，例如較大的後端集區、 [**多個節點**](use-multiple-node-pools.md)集區，以及 [**可用性區域**](availability-zones.md)。 這是 AKS 的建議 Load Balancer SKU。

如需「基本」和「標準」SKU 的詳細資訊，請參閱 [Azure Load Balancer SKU][azure-lb-comparison]。

本文假設您有一個具有 *標準* SKU AZURE LOAD BALANCER 的 AKS 叢集，並逐步解說如何使用和設定負載平衡器的一些功能和功能。 如果您需要 AKS 叢集，請參閱[使用 Azure CLI][aks-quickstart-cli] 或[使用 Azure 入口網站][aks-quickstart-portal]的 AKS 快速入門。

> [!IMPORTANT]
> 如果您不想要利用 Azure Load Balancer 來提供輸出連線，而改為使用自己的閘道、防火牆或 proxy 來進行該目的，您可以使用 [**輸出類型作為 UserDefinedRouting (UDR)**](egress-outboundtype.md)，以略過建立負載平衡器輸出集區和個別前端 IP。 輸出類型會定義叢集的輸出方法，且預設為類型：負載平衡器。

## <a name="use-the-public-standard-load-balancer"></a>使用公用標準負載平衡器

使用輸出類型建立 AKS 叢集之後： Load Balancer (預設) ，叢集也可以使用負載平衡器來公開服務。

若要這樣做，您可以建立型別的公用服務， `LoadBalancer` 如下列範例所示。 首先，建立名為的服務資訊清單 `public-svc.yaml` ：

```yaml
apiVersion: v1
kind: Service
metadata:
  name: public-svc
spec:
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: public-app
```

使用 [kubectl apply][kubectl-apply] 部署公用服務資訊清單，並指定 YAML 資訊清單的名稱：

```azurecli-interactive
kubectl apply -f public-svc.yaml
```

Azure Load Balancer 將會使用新的公用 IP 進行設定，這個新的公用 IP 將會在這個新服務之前。 由於 Azure Load Balancer 可以有多個前端 ip，因此每個部署的新服務將會取得新的專用前端 IP 以供唯一存取。

您可以確認已建立服務，並藉由執行來設定負載平衡器，例如：

```azurecli-interactive
kubectl get service public-svc
```

```console
NAMESPACE     NAME          TYPE           CLUSTER-IP     EXTERNAL-IP     PORT(S)         AGE
default       public-svc    LoadBalancer   10.0.39.110    52.156.88.187   80:32068/TCP    52s
```

當您查看服務詳細資料時，在負載平衡器上為此服務建立的公用 IP 位址會顯示在 [ *外部 IP* ] 資料行中。 IP 位址可能需要一到兩分鐘的時間才能變更 *\<pending\>* 為實際的公用 IP 位址，如上述範例所示。

## <a name="configure-the-public-standard-load-balancer"></a>設定公用標準負載平衡器

使用標準 SKU 公用負載平衡器時，有一組可在建立時自訂或藉由更新叢集來自訂的選項。 這些選項可讓您自訂 Load Balancer 以符合您的工作負載需求，且應據以進行檢查。 使用標準負載平衡器，您可以：

* 設定或調整受控輸出 Ip 的數目
* 攜帶您自己的自訂 [輸出 ip 或輸出 IP 首碼](#provide-your-own-outbound-public-ips-or-prefixes)
* 自訂配置給叢集每個節點的輸出埠數目
* 設定閒置連接的 timeout 設定

### <a name="scale-the-number-of-managed-outbound-public-ips"></a>調整受控輸出公用 Ip 的數目

除了輸入之外，Azure Load Balancer 還提供來自虛擬網路的輸出連線。 輸出規則可以簡單地設定公用 Standard Load Balancer 的輸出網路位址轉譯。

如同所有 Load Balancer 規則，輸出規則會遵循與負載平衡和輸入 NAT 規則相同的熟悉語法：

***前端 ip + 參數 + 後端集** 區 _

輸出規則會將「後端集區所識別的所有虛擬機器」的輸出 NAT 設定為轉譯成「前端」。 而且，「參數」提供對輸出 NAT 演算法的額外精細控制。

雖然輸出規則只能與單一公用 IP 位址搭配使用，但是輸出規則可減輕擴充輸出 NAT 的設定負擔。 您可以使用多個 IP 位址來規劃大規模案例，也可以使用輸出規則來減輕易於 SNAT 消耗模式。 前端提供的每個額外 IP 位址都會提供64k 暫時埠，以供 Load Balancer 用來作為 SNAT 埠。 

使用 _Standard * SKU 負載平衡器搭配受管理的輸出公用 Ip （預設會建立）時，您可以使用參數來調整受管理的輸出公用 Ip 數目 **`load-balancer-managed-ip-count`** 。

若要更新現有的叢集，請執行下列命令。 也可以在叢集建立時設定此參數，即可擁有多個受控的輸出公用 IP。

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-managed-outbound-ip-count 2
```

對於 myResourceGroup 中的 myAKSCluster 叢集，上述範例會將受控輸出公用 IP 的數目設定為「2」。 

您也可以在建立叢集時，使用 **`load-balancer-managed-ip-count`** 參數來設定受控輸出公用 ip 的初始數目，方法是附加 **`--load-balancer-managed-outbound-ip-count`** 參數，並將其設定為所需的值。 受控輸出公用 IP 的預設數目為 1。

### <a name="provide-your-own-outbound-public-ips-or-prefixes"></a>提供您自己的輸出公用 Ip 或首碼

當您使用 *標準* SKU 負載平衡器時，AKS 叢集預設會自動在 AKS 管理的基礎結構資源群組中建立公用 IP，並將其指派給負載平衡器輸出集區。

AKS 所建立的公用 IP 會被視為 AKS 受控資源。 這表示該公用 IP 的生命週期應由 AKS 管理，而且不需要直接在公用 IP 資源上執行任何使用者動作。 或者，您可以在建立叢集時，指派自己的自訂公用 IP 或公用 IP 首碼。 您也可以在現有叢集的負載平衡器屬性上更新您的自訂 Ip。

> [!NOTE]
> 自訂公用 IP 位址必須由使用者建立及擁有。 AKS 所建立的受控公用 IP 位址不能作為自備自訂 IP 來重複使用，因為它可能會導致管理衝突。

執行這項操作之前，請確定您符合設定輸出 ip 或輸出 IP 前置詞所需的必要條件 [和條件約束](../virtual-network/public-ip-address-prefix.md#constraints) 。

#### <a name="update-the-cluster-with-your-own-outbound-public-ip"></a>使用您自己的輸出公用 IP 來更新叢集

使用 [az network public-ip show][az-network-public-ip-show] 命令列出公用 IP 的識別碼。

```azurecli-interactive
az network public-ip show --resource-group myResourceGroup --name myPublicIP --query id -o tsv
```

上述命令會顯示 myResourceGroup 資源群組中的 myPublicIP 公用 IP 所用的識別碼。

使用 `az aks update` 命令搭配 **`load-balancer-outbound-ips`** 參數，以使用您的公用 ip 來更新您的叢集。

下列範例會使用 `load-balancer-outbound-ips` 參數搭配先前命令的識別碼。

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-outbound-ips <publicIpId1>,<publicIpId2>
```

#### <a name="update-the-cluster-with-your-own-outbound-public-ip-prefix"></a>使用您自己的輸出公用 IP 首碼來更新叢集

您也可以使用輸出的公用 IP 首碼搭配您的「標準」SKU 負載平衡器。 下列範例使用 [az network public-ip prefix show][az-network-public-ip-prefix-show] 命令列出公用 IP 首碼的識別碼：

```azurecli-interactive
az network public-ip prefix show --resource-group myResourceGroup --name myPublicIPPrefix --query id -o tsv
```

上述命令會顯示 myResourceGroup 資源群組中的 myPublicIPPrefix 公用 IP 首碼所用的識別碼。

下列範例會使用 load-balancer-outbound-ip-prefixes 參數搭配前一個命令的識別碼。

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-outbound-ip-prefixes <publicIpPrefixId1>,<publicIpPrefixId2>
```

#### <a name="create-the-cluster-with-your-own-public-ip-or-prefixes"></a>使用您自己的公用 IP 或首碼來建立叢集

您可能會想要在叢集建立期間攜帶您自己的 IP 位址或 IP 首碼以進行輸出，以支援將輸出端點新增至允許清單等案例。 將上面顯示的相同參數附加至叢集建立步驟，以便在叢集生命週期開始時定義您自己的公用 Ip 和 IP 首碼。

使用 az aks create 命令搭配 load-balancer-outbound-ips 參數，以您的公用 IP 在開始時建立新的叢集。

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-outbound-ips <publicIpId1>,<publicIpId2>
```

使用 az aks create 命令搭配 load-balancer-outbound-ip-prefixes 參數，以您的公用 IP 首碼在開始時建立新的叢集。

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --load-balancer-outbound-ip-prefixes <publicIpPrefixId1>,<publicIpPrefixId2>
```

### <a name="configure-the-allocated-outbound-ports"></a>設定配置的輸出埠

> [!IMPORTANT]
> 如果您的叢集上有應用程式，而這些應用程式預期會建立與一組較小目的地的大量連接，例如 許多連接到 SQL DB 的前端實例都有很容易遇到 SNAT 埠耗盡的情況， (用來從) 連接的埠。 在這些情況下，強烈建議您在負載平衡器上增加配置的輸出埠和輸出前端 Ip。 增加時應考慮一個 (1) 額外的 IP 位址會新增64k 個額外的埠，以散發到所有叢集節點上。


除非另有指定，否則 AKS 會使用 Standard Load Balancer 在設定時所定義之已配置輸出埠的預設值。 AKS API 上的此值為 **null** ，或在 SLB API 上為 **0** ，如下列命令所示：

```azurecli-interactive
NODE_RG=$(az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv)
az network lb outbound-rule list --resource-group $NODE_RG --lb-name kubernetes -o table
```

先前的命令會列出負載平衡器的輸出規則，例如：

```console
AllocatedOutboundPorts    EnableTcpReset    IdleTimeoutInMinutes    Name             Protocol    ProvisioningState    ResourceGroup
------------------------  ----------------  ----------------------  ---------------  ----------  -------------------  -------------
0                         True              30                      aksOutboundRule  All         Succeeded            MC_myResourceGroup_myAKSCluster_eastus  
```

此輸出並不表示您有0個埠，但是您正在利用 [根據後端集區大小的自動輸出埠指派][azure-lb-outbound-preallocatedports]，因此例如，如果叢集有50或更少的節點，則會配置每個節點的1024埠，而當您增加節點數目時，就會為每個節點逐漸取得較少的埠。


若要定義或增加配置的輸出埠數目，您可以遵循下列範例：


```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-managed-outbound-ip-count 7 \
    --load-balancer-outbound-ports 4000
```

此範例會為您的叢集中的每個節點提供4000配置的輸出埠，而7個 Ip 則會有 *每個節點4000個埠 * 100 node = 400k 的埠總數 < = 448k total 埠 = 7 個 ip * 每個 IP 的64k 埠* 。 這可讓您安全地擴充至100節點，並擁有預設的升級作業。 針對升級和其他作業所需的其他節點配置足夠的埠是很重要的。 AKS 預設為一個要升級的緩衝區節點，在此範例中，這需要在任何給定的時間點都有4000的可用埠。 如果使用 [maxSurge 值](upgrade-cluster.md#customize-node-surge-upgrade-preview)，請將每個節點的輸出埠乘以 maxSurge 值。

若要安全地前往100節點，您必須新增更多 Ip。


> [!IMPORTANT]
> 您必須在自訂 *allocatedOutboundPorts* 之前 [，先計算所需的配額並檢查需求][requirements]，以避免連線或調整問題。

您也可以在 **`load-balancer-outbound-ports`** 建立叢集時使用參數，但也必須指定 **`load-balancer-managed-outbound-ip-count`** 、 **`load-balancer-outbound-ips`** 或 **`load-balancer-outbound-ip-prefixes`** 。  例如：

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-sku standard \
    --load-balancer-managed-outbound-ip-count 2 \
    --load-balancer-outbound-ports 1024 
```

### <a name="configure-the-load-balancer-idle-timeout"></a>設定負載平衡器閒置時間

當 SNAT 連接埠資源耗盡時，輸出流程會失敗，直到現有的流程釋出 SNAT 連接埠為止。 Load Balancer 在流程關閉時回收 SNAT 埠，且 AKS 設定的負載平衡器會使用30分鐘的閒置 timeout，從閒置流程回收 SNAT 埠。
您也可以使用傳輸 (例如 **`TCP keepalives`** ) 或重新整理 **`application-layer keepalives`** 閒置流程，並視需要重設此閒置的超時時間。 您可以依照下列範例設定此超時時間： 


```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-idle-timeout 4
```

如果您預期會有許多短暫的連接，而且沒有長期存在且可能有很長閒置的連線，例如利用 `kubectl proxy` 或 `kubectl port-forward` 考慮使用低 timeout 值（例如4分鐘）。 此外，使用 TCP keepalive 時，就足以在連接的一端啟用它們。 例如，在伺服器端上啟用它們只是為了重設流程的閒置計時器，而且這兩個邊都不需要啟動 TCP keepalive 就夠了。 應用程式層也有類似概念，包括資料庫用戶端-伺服器組態。 檢查伺服器端是否有應用程式特定 keepalive 的選項。

> [!IMPORTANT]
> AKS 預設會啟用預設的 TCP 重設，並建議您保留此設定，並將其運用於您的案例中，以獲得更可預測的應用程式行為。
> TCP RST 只會在 TCP 連線處於已建立狀態時傳送。 請在[此處](../load-balancer/load-balancer-tcp-reset.md)閱讀相關資訊。

### <a name="requirements-for-customizing-allocated-outbound-ports-and-idle-timeout"></a>自訂已配置的輸出埠和閒置超時的需求

- 指定給 allocatedOutboundPorts 的值也必須是 8 的倍數。
- 您必須有足夠的輸出 IP 容量，取決於您節點 Vm 的數目和所需的已配置輸出埠。 若要確認是否有足夠的輸出 IP 容量，請使用下列公式： 
 
outboundIPs \* 64,000 \> nodeVMs \* *desiredAllocatedOutboundPorts*.
 
例如，如果您有 3 個 nodeVMs 和 50,000 個 desiredAllocatedOutboundPorts，您至少必須有 3 個 outboundIPs。 建議納入額外的輸出 IP 容量，超過所需範圍外。 此外，計算輸出 IP 容量時，必須考慮叢集自動調整程式，以及節點集區升級的可能性。 針對叢集自動調整程式，請檢查目前的節點計數和最大節點計數，並使用較高的值。 若要進行升級，請針對允許升級的每個節點集區考慮其他節點 VM。

- 將 IdleTimeoutInMinutes 設定為不同於預設 30 分鐘的值時，請考慮您的工作負載需要輸出連線的時間長度。 也請考慮在 AKS 以外使用「標準」SKU 負載平衡器的預設逾時值為 4 分鐘。 IdleTimeoutInMinutes 值，可以更精確反映您的特定 AKS 工作負載，有助於減少佔用不再使用的連線而造成的 SNAT 耗盡。

> [!WARNING]
> 改變 *AllocatedOutboundPorts* 和 *IdleTimeoutInMinutes* 的值，可能會大幅變更您負載平衡器的輸出規則行為，且不應輕量地執行，而不需要瞭解取捨和您應用程式的連線模式，請檢查 [下方的 SNAT 疑難排解區段][troubleshoot-snat]，並在更新這些值之前先查看 [Azure 中][azure-lb-outbound-connections] [Load Balancer 的輸出規則][azure-lb-outbound-rules-overview]和輸出連線，以充分瞭解變更的影響。

## <a name="restrict-inbound-traffic-to-specific-ip-ranges"></a>限制特定 IP 範圍的輸入流量

下列資訊清單會使用 loadBalancerSourceRanges 指定輸入外部流量的新 IP 範圍：

```yaml
apiVersion: v1
kind: Service
metadata:
  name: azure-vote-front
spec:
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: azure-vote-front
  loadBalancerSourceRanges:
  - MY_EXTERNAL_IP_RANGE
```

> [!NOTE]
> 輸入，外部流量會從負載平衡器流向您 AKS 叢集的虛擬網路。 虛擬網路具有 (NSG) 的網路安全性群組，可允許來自負載平衡器的所有輸入流量。 此 NSG 會使用 *LoadBalancer* 類型的 [服務標記][service-tags]來允許來自負載平衡器的流量。

## <a name="maintain-the-clients-ip-on-inbound-connections"></a>在輸入連接上維護用戶端的 IP

根據預設，Kubernetes 和 AKS 中的型別服務將 `LoadBalancer` [in Kubernetes](https://kubernetes.io/docs/tutorials/services/source-ip/#source-ip-for-services-with-type-loadbalancer)不會在 pod 的連線上保存用戶端的 IP 位址。 傳遞至 pod 的封包上的來源 IP 將會是該節點的私人 IP。 若要維護用戶端的 IP 位址，您必須 `service.spec.externalTrafficPolicy` `local` 在服務定義中將設定為。 下列資訊清單顯示範例：

```yaml
apiVersion: v1
kind: Service
metadata:
  name: azure-vote-front
spec:
  type: LoadBalancer
  externalTrafficPolicy: Local
  ports:
  - port: 80
  selector:
    app: azure-vote-front
```

## <a name="additional-customizations-via-kubernetes-annotations"></a>透過 Kubernetes 批註的其他自訂

以下是使用類型 Kubernetes 服務所支援的注釋清單 `LoadBalancer` ，這些批註只適用 **于輸入** 流程：

| Annotation | 值 | 描述
| ----------------------------------------------------------------- | ------------------------------------- | ------------------------------------------------------------ 
| `service.beta.kubernetes.io/azure-load-balancer-internal`         | `true` 或 `false`                     | 指定負載平衡器是否應為內部負載平衡器。 如果未設定，則會預設為 public。
| `service.beta.kubernetes.io/azure-load-balancer-internal-subnet`  | 子網的名稱                    | 指定應系結內部負載平衡器的子網。 如果未設定，則會預設為雲端設定檔中設定的子網。
| `service.beta.kubernetes.io/azure-dns-label-name`                 | 公用 Ip 上的 DNS 標籤名稱   | 指定 **公用** 服務的 DNS 標籤名稱。 如果設定為空字串，將不會使用公用 IP 中的 DNS 專案。
| `service.beta.kubernetes.io/azure-shared-securityrule`            | `true` 或 `false`                     | 指定應使用可與另一個服務共用的 Azure 安全性規則來公開服務，並針對可公開的服務數目增加，交易明確的規則。 此注釋依賴網路安全性群組的 Azure 增強型 [安全性規則](../virtual-network/network-security-groups-overview.md#augmented-security-rules) 功能。 
| `service.beta.kubernetes.io/azure-load-balancer-resource-group`   | 資源群組的名稱            | 指定負載平衡器公用 Ip 的資源群組，而這些公用 Ip 與叢集基礎結構 (節點資源群組) 位於相同的資源群組中。
| `service.beta.kubernetes.io/azure-allowed-service-tags`           | 允許的服務標記清單          | 指定允許的 [服務標記][service-tags] 清單（以逗號分隔）。
| `service.beta.kubernetes.io/azure-load-balancer-tcp-idle-timeout` | TCP 閒置超時（分鐘）          | 指定負載平衡器上發生 TCP 連接閒置超時的時間（以分鐘為單位）。 預設值和最小值為4。 最大值為30。 必須是整數。
|`service.beta.kubernetes.io/azure-load-balancer-disable-tcp-reset` | `true`                                | `enableTcpReset`針對 SLB 停用


## <a name="troubleshooting-snat"></a>針對 SNAT 進行疑難排解

如果您知道您正在開始許多連至相同目的地 IP 位址和埠的輸出 TCP 或 UDP 連線，而且觀察到失敗的連出連線，或您正在耗盡 (的 SNAT 埠（由 PAT) 所使用的預先配置暫時埠），則會有數個一般緩和選項。 請檢閱這些選項並判斷哪一個可用且最適合您的案例。 可能會有一或多個選項有助於管理此案例。 如需詳細資訊，請參閱 [輸出連接疑難排解指南](../load-balancer/troubleshoot-outbound-connection.md)。

SNAT 耗盡的根本原因通常是一種反模式，說明如何建立、管理或設定計時器從其預設值變更。 請仔細檢閱這一節。

### <a name="steps"></a>步驟
1. 檢查您的連線是否持續閒置一段時間，並且依賴預設的閒置超時時間來釋放該埠。 如果是這樣，則可能需要針對您的案例減少30分鐘的預設超時。
2. 調查您的應用程式如何建立輸出連線 (例如，程式碼檢閱或封包擷取)。
3. 判斷此活動是否為預期的行為，或應用程式是否運作不正常。 使用 Azure 監視器中的 [計量](../load-balancer/load-balancer-standard-diagnostics.md) 和 [記錄](../load-balancer/load-balancer-monitor-log.md) 來證實您的結果。 例如，使用「失敗」類別進行 SNAT 連線度量。
4. 評估是否遵循適當的 [模式](#design-patterns) 。
5. 使用 [額外的輸出 IP 位址 + 額外配置的輸出埠](#configure-the-allocated-outbound-ports) ，評估是否應減輕 SNAT 埠耗盡。

### <a name="design-patterns"></a>設計模式
可能的話，一律利用連線重複使用和連線共用。 這些模式會避免發生資源耗盡問題，並產生可預測的行為。 您可以在許多開發程式庫和架構中找到這些模式的基元。

- 不可部分完成的要求 (每個連線) 一個要求通常不是不錯的設計選擇。 這種反向模式會限制規模調整、降低效能，並降低可靠性。 相反地，應重複使用 HTTP/S 連線以減少連線數目和關聯的 SNAT 連接埠。 應用程式規模將會提高，並提升效能，因為使用 TLS 時，會降低交握、額外負荷和密碼編譯作業成本。
- 如果您使用的不是叢集/自訂 DNS，或 coreDNS 上的自訂上游伺服器，請記住，當用戶端未快取 DNS 解析程式結果時，DNS 可能會在磁片區引進許多個別的流程。 請務必先自訂 coreDNS，而不是使用自訂 DNS 伺服器，並定義良好的快取值。
- UDP 流程 (例如 DNS 查閱) 會在閒置逾時期間配置 SNAT 連接埠。 閒置的逾時時間愈長，SNAT 連接埠上的壓力愈高。 使用較短的閒置時間 (例如4分鐘)。
使用連接集區來塑造您的連線磁碟區。
- 永遠不要以無訊息方式放棄 TCP 流程，並依賴 TCP 計時器來清除流程。 如果您未讓 TCP 明確關閉連線，則狀態會保持分配於中繼系統和端點，並讓 SNAT 連接埠無法供其他連線使用。 此模式可能會觸發應用程式失敗和 SNAT 耗盡的問題。
- 在沒有從專業角度了解可能產生的影響時，請勿變更 作業系統層級 TCP 關閉相關的計時器值。 當 TCP 堆疊復原時，當連接的端點有不相符的預期時，您的應用程式效能可能會受到負面影響。 希望變更計時器通常是基礎設計問題的正負號。 請檢閱下列建議。


上述範例會將規則更新為只允許來自 MY_EXTERNAL_IP_RANGE 範圍的輸入外部流量。 如果您將 *MY_EXTERNAL_IP_RANGE* 取代為內部子網 IP 位址，則會將流量限制為僅限叢集的內部 ip。 這將不允許來自 Kubernetes 叢集外部的用戶端存取負載平衡器。

## <a name="moving-from-a-basic-sku-load-balancer-to-standard-sku"></a>從基本 SKU 負載平衡器移至標準 SKU

如果您現有的叢集使用的是基本 SKU 負載平衡器，而想要進行遷移以便使用具有標準 SKU 負載平衡器的叢集，需要留意一些重要的行為差異。

舉例來說，常見的一種做法是進行藍色/綠色部署來遷移叢集，因為只能在叢集建立時定義叢集的 `load-balancer-sku` 類型。 不過， *基本 sku* 負載平衡器會使用 *基本 sku* ip 位址，因為它們需要 *標準 sku* ip 位址，因此不相容于 *標準 sku* 負載平衡器。 為了升級 Load Balancer SKU 而遷移叢集時，新 IP 位址需要具有相容的 IP 位址 SKU。

如需如何遷移叢集的詳細考量，請參閱[我們的遷移考量文件](aks-migration.md)，查看遷移時需要注意的重要主題清單。 在 AKS 中使用標準 SKU 負載平衡器時，以下限制也是需要留意的重要行為差異。

## <a name="limitations"></a>限制

您建立和管理可支援「標準」SKU 負載平衡器的 AKS 叢集時，需遵守下列限制：

* 必須至少有一個公用 IP 或 IP 首碼，才能允許來自 AKS 叢集的輸出流量。 您也必須要有公用 IP 或 IP 前置詞，才能維持控制平面和代理程式節點之間的連線能力，並維持與舊版 AKS 的相容性。 可使用下列選項來指定「標準」SKU 負載平衡器的公用 IP 或 IP 首碼：
    * 提供您自己的公用 IP。
    * 提供您自己的公用 IP 首碼。
    * 指定一個 100 以內的數字，以允許 AKS 叢集在與 AKS 叢集相同的資源群組中建立相同數量的「標準」SKU 公用 IP，AKS 叢集的名稱通常是以「MC_」開頭。 AKS 會將公用 IP 指派給「標準」SKU 負載平衡器。 根據預設，如果未指定公用 IP、公用 IP 首碼或 IP 數目，則會在與 AKS 叢集相同的資源群組中自動建立一個公用 IP。 請務必允許公用位址，並避免建立任何禁止 IP 建立的 Azure 原則。
* AKS 所建立的公用 IP 無法重複使用為自訂攜帶您自己的公用 IP 位址。 所有自訂 IP 位址都必須由使用者建立及管理。
* 只有在建立 AKS 叢集時，才可以定義負載平衡器 SKU。 建立 AKS 叢集之後，您就無法變更負載平衡器 SKU。
* 在單一叢集中，只能使用一種類型的負載平衡器 SKU (基本或標準)。
* 「標準」SKU 負載平衡器只支援「標準」SKU IP 位址。


## <a name="next-steps"></a>後續步驟

在 [Kubernetes 服務文件][kubernetes-services]中深入了解 Kubernetes 服務。

若要深入瞭解如何使用內部 Load Balancer，請 [參閱 AKS 內部 Load Balancer 檔](internal-lb.md)中的輸入流量。

<!-- LINKS - External -->
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-delete]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#delete
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubernetes-services]: https://kubernetes.io/docs/concepts/services-networking/service/
[aks-engine]: https://github.com/Azure/aks-engine

<!-- LINKS - Internal -->
[advanced-networking]: configure-azure-cni.md
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[aks-sp]: kubernetes-service-principal.md#delegate-access-to-other-azure-resources
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-aks-install-cli]: /cli/azure/aks?view=azure-cli-latest#az-aks-install-cli
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-group-create]: /cli/azure/group#az-group-create
[az-provider-register]: /cli/azure/provider#az-provider-register
[az-network-lb-outbound-rule-list]: /cli/azure/network/lb/outbound-rule?view=azure-cli-latest#az-network-lb-outbound-rule-list
[az-network-public-ip-show]: /cli/azure/network/public-ip?view=azure-cli-latest#az-network-public-ip-show
[az-network-public-ip-prefix-show]: /cli/azure/network/public-ip/prefix?view=azure-cli-latest#az-network-public-ip-prefix-show
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[azure-lb]: ../load-balancer/load-balancer-overview.md
[azure-lb-comparison]: ../load-balancer/skus.md
[azure-lb-outbound-rules]: ../load-balancer/load-balancer-outbound-connections.md#outboundrules
[azure-lb-outbound-connections]: ../load-balancer/load-balancer-outbound-connections.md
[azure-lb-outbound-preallocatedports]: ../load-balancer/load-balancer-outbound-connections.md#preallocatedports
[azure-lb-outbound-rules-overview]: ../load-balancer/load-balancer-outbound-connections.md#outboundrules
[install-azure-cli]: /cli/azure/install-azure-cli
[internal-lb-yaml]: internal-lb.md#create-an-internal-load-balancer
[kubernetes-concepts]: concepts-clusters-workloads.md
[use-kubenet]: configure-kubenet.md
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[requirements]: #requirements-for-customizing-allocated-outbound-ports-and-idle-timeout
[use-multiple-node-pools]: use-multiple-node-pools.md
[troubleshoot-snat]: #troubleshooting-snat
[service-tags]: ../virtual-network/network-security-groups-overview.md#service-tags
