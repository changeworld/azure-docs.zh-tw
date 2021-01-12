---
title: Azure Kubernetes Service (AKS) 的常見問題集
description: 尋找一些關於 Azure Kubernetes Service (AKS) 的常見問題解答。
ms.topic: conceptual
ms.date: 08/06/2020
ms.openlocfilehash: 7fc348ae7b3edb79e75aa1acd08941fec447da6f
ms.sourcegitcommit: 02b1179dff399c1aa3210b5b73bf805791d45ca2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/12/2021
ms.locfileid: "98127629"
---
# <a name="frequently-asked-questions-about-azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS) 的常見問題集

本文將說明關於 Azure Kubernetes Service (AKS) 的常見問題。

## <a name="which-azure-regions-currently-provide-aks"></a>哪些 Azure 區域目前提供 AKS？

如需可用區域的完整清單，請參閱 [AKS 區域和可用性][aks-regions]。

## <a name="can-i-spread-an-aks-cluster-across-regions"></a>我可以跨區域散佈 AKS 叢集嗎？

否。 AKS 叢集是區域資源，無法跨區域。 如需如何建立包含多個區域之架構的指導方針，請參閱[商務持續性和災害復原的最佳做法][bcdr-bestpractices]。

## <a name="can-i-spread-an-aks-cluster-across-availability-zones"></a>我可以在可用性區域之間散佈 AKS 叢集嗎？

是。 您可以在[支援 AKS 叢集的區域][az-regions]中，將某個 AKS 叢集部署到一或多個[可用性區域][availability-zones]。

## <a name="can-i-limit-who-has-access-to-the-kubernetes-api-server"></a>我可以限制有權存取 Kubernetes API 伺服器的人員嗎？

是。 有兩個選項可限制對 API 伺服器的存取：

- 如果您想要維護 API 伺服器的公用端點，但限制只能存取一組信任的 IP 範圍，請使用 [API 伺服器授權的 IP 範圍][api-server-authorized-ip-ranges]。
- 如果您想要將 API 伺服器限制為「僅」可從虛擬網路中存取，請使用[私人叢集][private-clusters]。

## <a name="can-i-have-different-vm-sizes-in-a-single-cluster"></a>單一叢集中是否可以有不同的 VM 大小？

是，您可以在 AKS 叢集中建立[多個節點集區][multi-node-pools]，以使用不同的虛擬機器大小。

## <a name="are-security-updates-applied-to-aks-agent-nodes"></a>安全性更新是否會套用至 AKS 代理程式節點？

Azure 會透過夜間排程，將安全性修補程式自動套用至叢集中的 Linux 節點。 不過，您必須負責確保這些 Linux 節點會視需要重新開機。 您有多個將節點重新開機的選項：

- 手動、透過 Azure 入口網站，或透過 Azure CLI。
- 藉由升級 AKS 叢集。 叢集會自動升級 [cordon 和 drain 節點][cordon-drain]，然後使用最新的 Ubuntu 映像和新的修補程式版本或 Kubernetes 次要版本來讓新節點上線。 如需詳細資訊，請參閱[升級 AKS 叢集][aks-upgrade]。
- 使用 [節點映射升級](node-image-upgrade.md)。

### <a name="windows-server-nodes"></a>Windows Server 節點

對於 Windows Server 節點，Windows Update 不會自動執行並套用最新的更新。 依照 Windows Update 發行週期和您自己驗證程序的定期排程，您應該在 AKS 叢集中的叢集和 Windows Server 節點集區上執行升級。 此升級程序會建立節點來執行最新的 Windows Server 映像和修補程式，然後移除較舊的節點。 如需此程序的詳細資訊，請參閱[在 AKS 中升級節點集區][nodepool-upgrade]。

## <a name="why-are-two-resource-groups-created-with-aks"></a>為何會使用 AKS 建立兩個資源群組？

AKS 會根據一些 Azure 基礎結構資源來建置，包括虛擬機器擴展集、虛擬網路和受控磁碟。 這可讓您在 AKS 所提供的受控 Kubernetes 環境中，利用 Azure 平台的許多核心功能。 例如，大部分的 Azure 虛擬機器類型均可直接與 AKS 搭配使用，而 Azure 保留可用來自動接收那些資源的折扣。

為了啟用此架構，每個 AKS 部署皆會跨越兩個資源群組：

1. 您會建立第一個資源群組。 此群組僅包含 Kubernetes 服務資源。 AKS 資源提供者會在部署期間自動建立第二個資源群組。 第二個資源群組的範例是 *MC_myResourceGroup_myAKSCluster_eastus*。 如需如何指定這第二個資源群組名稱的相關資訊，請參閱下一節。
1. 第二個資源群組 (稱為「節點資源群組」) 包含所有與該叢集相關聯的基礎結構資源。 這些資源包括 Kubernetes 節點 VM、虛擬網路和儲存體。 根據預設，節點資源群組具有類似 *MC_myResourceGroup_myAKSCluster_eastus* 的名稱。 AKS 會在刪除叢集時自動刪除節點資源，因此它只能用於共用叢集生命週期的資源。

## <a name="can-i-provide-my-own-name-for-the-aks-node-resource-group"></a>我可以為 AKS 節點資源群組提供自己的名稱嗎？

是。 根據預設，AKS 會將節點資源群組命名為 *MC_resourcegroupname_clustername_location*，但您也可以自行命名。

若要指定您自己的資源群組名稱，請安裝 [aks-preview][aks-preview-cli] Azure CLI 延伸模組 *0.3.2* 版或更新版本。 當您使用 [az AKS create][az-aks-create] 命令建立 AKS 叢集時，請使用 `--node-resource-group` 參數並指定資源群組的名稱。 如果您 [使用 Azure Resource Manager 範本][aks-rm-template]來部署 AKS 叢集，則可使用 *nodeResourceGroup* 屬性來定義資源群組名稱。

* 您自己訂用帳戶中的 Azure 資源提供者會自動建立第二個資源群組。
* 只有在建立叢集時，才能指定自訂資源群組名稱。

當使用節點資源群組時，您無法進行以下動作：

* 指定現有的資源群組做為節點資源群組。
* 為節點資源群組指定不同的訂閱。
* 在建立叢集之後，變更節點資源群組名稱。
* 指定節點資源群組內受控資源的名稱。
* 修改或刪除節點資源群組內由 Azure 建立的受控資源標籤。 (請參閱下一節的其他資訊)。

## <a name="can-i-modify-tags-and-other-properties-of-the-aks-resources-in-the-node-resource-group"></a>我可以修改節點資源群組中 AKS 資源的標記和其他屬性嗎？

如果您修改或刪除節點資源群組中 Azure 所建立的標記和其他資源屬性，可能就會得到非預期的結果，例如，調整和升級錯誤。 AKS 可讓您建立和修改使用者所建立的自訂標籤，也可以在 [建立節點集](use-multiple-node-pools.md#specify-a-taint-label-or-tag-for-a-node-pool)區時新增這些標記。 例如，您可以建立或修改自訂標記，以指派業務單位或成本中心。 這也可以透過在受控資源群組上建立具有範圍的 Azure 原則來達成。

不過，在 AKS 叢集中的節點資源群組下，修改資源的任何 **Azure 建立** 的標籤，都是不受支援的動作，它會中斷服務等級目標 (SLO) 。 如需詳細資訊，請參閱 [AKS 是否提供服務等級協定？](#does-aks-offer-a-service-level-agreement)

## <a name="what-kubernetes-admission-controllers-does-aks-support-can-admission-controllers-be-added-or-removed"></a>AKS 支援哪些 Kubernetes 許可控制器？ 是否可以新增或移除許可控制器？

AKS 支援下列[許可控制器][admission-controllers]：

- *NamespaceLifecycle*
- *LimitRanger*
- *ServiceAccount*
- *DefaultStorageClass*
- *DefaultTolerationSeconds*
- *MutatingAdmissionWebhook*
- *ValidatingAdmissionWebhook*
- *ResourceQuota*
- *PodNodeSelector*
- *PodTolerationRestriction*
- *ExtendedResourceToleration*

您目前無法修改 AKS 中的許可控制器清單。

## <a name="can-i-use-admission-controller-webhooks-on-aks"></a>我可以在 AKS 上使用許可控制站 Webhook 嗎？

是，您可以在 AKS 上使用許可控制站 Webhook。 建議您排除內部 AKS 命名空間，這些命名空間是以 **控制平面標籤標示。** 例如，將下列內容新增至 Webhook 設定：

```
namespaceSelector:
    matchExpressions:
    - key: control-plane
      operator: DoesNotExist
```

將 API 伺服器輸出 AKS 防火牆，讓您的許可控制站 webhook 必須可從叢集記憶體取。

## <a name="can-admission-controller-webhooks-impact-kube-system-and-internal-aks-namespaces"></a>許可控制站 Webhook 會影響 kube 系統和內部 AKS 命名空間嗎？

為了保護系統的穩定性，並防止自訂的許可控制器影響 kube 系統中的內部服務，命名空間 AKS 具有 **許可強制器**，可自動排除 kube 系統和 AKS 內部命名空間。 此服務確保自訂的許可控制器不會影響在 kube 系統中執行的服務。

如果您有一個關鍵使用案例，需要在 kube 系統上部署某些項目 (不建議)，以讓自訂許可 Webhook 涵蓋於其中，則您可以新增下列標籤或註釋，讓許可強制器能夠加以忽略。

標籤：```"admissions.enforcer/disabled": "true"``` 或註釋：```"admissions.enforcer/disabled": true```

## <a name="is-azure-key-vault-integrated-with-aks"></a>Azure Key Vault 是否會與 AKS 整合？

AKS 目前並未原生整合到 Azure Key Vault。 不過，[適用於 CSI 祕密存放區的 Azure Key Vault 提供者][csi-driver]能讓 Kubernetes Pod 直接與 KeyVault 祕密整合。

## <a name="can-i-run-windows-server-containers-on-aks"></a>我是否可以在 AKS 上執行 Windows Server 容器？

是，Windows Server 容器可在 AKS 上取得。 若要在 AKS 中執行 Windows Server 容器，請建立執行 Windows Server 的節點集區作為客體 OS。 Windows Server 容器只能使用 Windows Server 2019。 若要開始使用，請參閱[建立具有 Windows Server 節點集區的 AKS 叢集][aks-windows-cli]。

對於節點集區的 Windows Server 支援，包含在 Kubernetes 專案中屬於上游 Windows Server 的一些限制。 如需這些限制的詳細資訊，請參閱 [Windows Server 容器的 AKS 限制][aks-windows-limitations]。

## <a name="does-aks-offer-a-service-level-agreement"></a>AKS 是否提供服務等級協定？

AKS 會以 [執行時間 SLA][uptime-sla]的選擇性附加元件功能提供 SLA 保證。

## <a name="can-i-apply-azure-reservation-discounts-to-my-aks-agent-nodes"></a>我可以將 Azure 保留折扣套用到我的 AKS 代理程式節點嗎？

AKS 代理程式節點會以標準 Azure 虛擬機器計費，因此，如果您已針對要在 AKS 中使用的 VM 大小購買 [Azure 保留][reservation-discounts] ，則會自動套用這些折扣。

## <a name="can-i-movemigrate-my-cluster-between-azure-tenants"></a>我可以在 Azure 租用戶之間移動/移轉叢集嗎？

目前不支援在租使用者之間移動您的 AKS 叢集。

## <a name="can-i-movemigrate-my-cluster-between-subscriptions"></a>我可以在訂用帳戶之間移動/移轉叢集嗎？

目前不支援在訂用帳戶之間移動叢集。

## <a name="can-i-move-my-aks-clusters-from-the-current-azure-subscription-to-another"></a>我可以將 AKS 叢集從目前的 Azure 訂用帳戶移至另一個嗎？

不支援在 Azure 訂用帳戶之間移動您的 AKS 叢集及其相關聯的資源。

## <a name="can-i-move-my-aks-cluster-or-aks-infrastructure-resources-to-other-resource-groups-or-rename-them"></a>我可以將 AKS 叢集或 AKS 基礎結構資源移至其他資源群組或重新命名嗎？

不支援移動或重新命名您的 AKS 叢集與其相關聯的資源。

## <a name="why-is-my-cluster-delete-taking-so-long"></a>為何叢集刪除如此耗時？

大部分的叢集都會在使用者要求時刪除；在某些情況下，特別是當客戶帶入自己的資源群組，或進行跨 RG 工作刪除時，可能需要額外的時間或失敗。 如果您有刪除的問題，請仔細檢查您沒有 RG 的鎖定、RG 以外的任何資源是否與 RG 解除關聯，依此類推。

## <a name="if-i-have-pod--deployments-in-state-nodelost-or-unknown-can-i-still-upgrade-my-cluster"></a>如果我的 Pod / 部署處於「節點遺失」或「未知」狀態，我仍然可以升級叢集嗎？

您可以，但 AKS 不建議這樣做。 當叢集狀態為已知且狀況良好時，應執行升級。

## <a name="if-i-have-a-cluster-with-one-or-more-nodes-in-an-unhealthy-state-or-shut-down-can-i-perform-an-upgrade"></a>如果我的叢集有一或多個節點處於狀況不良狀態或已關閉，我可以執行升級嗎？

否，刪除/移除處於失敗狀態的任何節點，或在升級之前從叢集中移除。

## <a name="i-ran-a-cluster-delete-but-see-the-error-errno-11001-getaddrinfo-failed"></a>我已執行叢集刪除，但看到錯誤 `[Errno 11001] getaddrinfo failed`

最常見的原因是，使用者有一或多個網路安全性群組 (NSG) 仍在使用中且與叢集相關聯。  請將它們移除，然後再次嘗試刪除。

## <a name="i-ran-an-upgrade-but-now-my-pods-are-in-crash-loops-and-readiness-probes-fail"></a>我已執行升級，但現在我的 Pod 處於損毀迴圈，整備度探查失敗了嗎？

確認您的服務主體未過期。  請參閱： [AKS 服務主體](./kubernetes-service-principal.md) 和 [AKS 更新認證](./update-credentials.md)。

## <a name="my-cluster-was-working-but-suddenly-cant-provision-loadbalancers-mount-pvcs-etc"></a>我的叢集正常運作，但突然無法布建 LoadBalancers、裝載 Pvc 等？

確認您的服務主體未過期。  請參閱： [AKS 服務主體](./kubernetes-service-principal.md)  和 [AKS 更新認證](./update-credentials.md)。

## <a name="can-i-scale-my-aks-cluster-to-zero"></a>我可以將 AKS 叢集調整為零嗎？
您可以完全 [停止執行中的 AKS](start-stop-cluster.md)叢集，並節省各自的計算成本。 此外，您也可以選擇將 [所有或特定 `User` 節點集區調整或自動調整](scale-cluster.md#scale-user-node-pools-to-0) 為0，只維護必要的叢集設定。
您無法直接將 [系統節點](use-system-pools.md) 集區調整為零。

## <a name="can-i-use-the-virtual-machine-scale-set-apis-to-scale-manually"></a>我可以使用虛擬機器擴展集 API 進行手動調整嗎？

否，不支援使用虛擬機器擴展集 API 進行調整作業。 請使用 AKS API (`az aks scale`)。

## <a name="can-i-use-virtual-machine-scale-sets-to-manually-scale-to-zero-nodes"></a>我可以使用虛擬機器擴展集手動調整為零個節點嗎？

否，不支援使用虛擬機器擴展集 API 進行調整作業。 您可以使用 AKS API 來調整為零個非系統節點集區，或改為 [停止您](start-stop-cluster.md) 的叢集。

## <a name="can-i-stop-or-de-allocate-all-my-vms"></a>我可以停止或解除配置我的所有 VM 嗎？

雖然 AKS 具有可承受這類設定並從中復原的恢復機制，但這並不是支援的設定。 請改為[停止您的](start-stop-cluster.md)叢集。

## <a name="can-i-use-custom-vm-extensions"></a>我可以使用自訂 VM 延伸模組嗎？

支援 Log Analytics 代理程式，因為它是由 Microsoft 管理的延伸模組。 否則，AKS 是受控服務，不支援操作 IaaS 資源。 若要安裝自訂群組件，請使用 Kubernetes Api 和機制。 例如，使用 Daemonset 來安裝必要的元件。

## <a name="does-aks-store-any-customer-data-outside-of-the-clusters-region"></a>AKS 是否會將任何客戶資料儲存在叢集區域以外？

啟用在單一區域中儲存客戶資料的功能目前僅適用于東南亞區域 (新加坡) 的亞太地區地區。 至於其他所有區域，客戶資料會儲存在地區中。

## <a name="are-aks-images-required-to-run-as-root"></a>AKS 映射是否需要以 root 的形式執行？

除了下列兩個映射以外，AKS 映射不需要以 root 的形式執行：

- *mcr.microsoft.com/oss/kubernetes/coredns*
- *mcr.microsoft.com/azuremonitor/containerinsights/ciprod*

## <a name="what-is-azure-cni-transparent-mode-vs-bridge-mode"></a>什麼是 Azure CNI 透明模式與橋接器模式？

從 v 1.2.0 Azure CNI 將會有以透明模式作為單一租用 Linux CNI 部署的預設值。 透明模式正在取代橋接器模式。 在本節中，我們將詳細討論這兩種模式的差異，以及在 Azure CNI 中使用透明模式的優點/限制為何。

### <a name="bridge-mode"></a>橋接器模式

如其名所示，bridge 模式 Azure CNI 以「即時」的方式，將會建立名為 "azure0" 的 L2 橋接器。 所有主機端 pod 組 `veth` 介面都會連接到這個橋接器。 因此 Pod-Pod VM 間通訊，其餘的流量會通過此橋樑。 有問題的橋樑是第2層虛擬裝置，除非您將一或多個真實裝置系結到該裝置，否則它本身無法接收或傳輸任何裝置。 基於這個理由，Linux VM 的 eth0 必須轉換成「azure0」橋接器的從屬。 這會在 Linux VM 內建立複雜的網路拓撲，而且 CNI 必須處理其他網路功能（例如 DNS 伺服器更新等）的徵兆。

:::image type="content" source="media/faq/bridge-mode.png" alt-text="橋接器模式拓撲":::

以下是 ip 路由設定在橋接器模式中的外觀範例。 無論節點擁有多少個 pod，都只會有兩個路由。 第一個說法是，azure0 上的 local 以外的所有流量，都會透過具有 ip "src 10.240.0.4 (" 的介面（節點主要 IP) ，而第二個則是「>10.20. x」 Pod 空間至核心的核心來決定），移至子網的預設閘道。

```bash
default via 10.240.0.1 dev azure0 proto dhcp src 10.240.0.4 metric 100
10.240.0.0/12 dev azure0 proto kernel scope link src 10.240.0.4
172.17.0.0/16 dev docker0 proto kernel scope link src 172.17.0.1 linkdown
root@k8s-agentpool1-20465682-1:/#
```

### <a name="transparent-mode"></a>透明模式
透明模式採用簡單的方法來設定 Linux 網路功能。 在此模式中，Azure CNI 不會變更 Linux VM 中 eth0 介面的任何屬性。 這種變更 Linux 網路屬性的最基本方法，有助於減少叢集可能會在橋接器模式下面對的複雜角落案例問題。 在透明模式中，Azure CNI 會建立並新增 `veth` 將新增至主機網路的主機端 pod 組介面。 VM Pod 到 Pod 之間的通訊是透過 CNI 將新增的 ip 路由。 基本上，Pod 對 Pod 的通訊是超過第3層，而 pod 流量則是由 L3 路由規則路由傳送。

:::image type="content" source="media/faq/transparent-mode.png" alt-text="透明模式拓撲":::

以下是透明模式的範例 ip 路由設定，每個 Pod 的介面都會取得附加的靜態路由，讓以目的地 IP 為 Pod 的流量會直接傳送至 Pod 的主機端 `veth` 配對介面。

```bash
10.240.0.216 dev azv79d05038592 proto static
10.240.0.218 dev azv8184320e2bf proto static
10.240.0.219 dev azvc0339d223b9 proto static
10.240.0.222 dev azv722a6b28449 proto static
10.240.0.223 dev azve7f326f1507 proto static
10.240.0.224 dev azvb3bfccdd75a proto static
168.63.129.16 via 10.240.0.1 dev eth0 proto dhcp src 10.240.0.4 metric 100
169.254.169.254 via 10.240.0.1 dev eth0 proto dhcp src 10.240.0.4 metric 100
172.17.0.0/16 dev docker0 proto kernel scope link src 172.17.0.1 linkdown
```

### <a name="benefits-of-transparent-mode"></a>透明模式的優點

- 可緩和 `conntrack` dns 平行競爭情形，並避免5秒的 dns 延遲問題，而不需要設定節點本機 dns (您仍可基於效能考慮使用節點本機 dns) 。
- 因為「及時」橋接器設定，所以會排除今日最初的5秒 DNS 延遲 CNI 橋接器模式。
- 橋接器模式中的其中一個角落案例是，Azure CNI 無法持續更新新增至 VNET 或 NIC 的自訂 DNS 伺服器清單。 這會導致 CNI 只挑選 DNS 伺服器清單的第一個實例。 因為 CNI 不會變更任何 eth0 屬性，所以在透明模式中解決。 請參閱 [這裡](https://github.com/Azure/azure-container-networking/issues/713)的詳細資訊。
- 提供較佳的 UDP 流量處理，並在 ARP 超時時降低 UDP 洪水洪水的衝擊。在橋接模式中，當橋接器在 VM 內 Pod 對 Pod 之間的通訊中不知道目的地 pod 的 MAC 位址時，這會導致封包在所有埠上都不存在。 因為路徑中沒有任何 L2 裝置，所以在透明模式中解決。 請參閱 [這裡](https://github.com/Azure/azure-container-networking/issues/704)的詳細資訊。
- 相較于橋接模式，透明模式在 VM Pod 與 Pod 之間的通訊中，會在輸送量和延遲方面發揮效果。

## <a name="how-to-avoid-permission-ownership-setting-slow-issues-when-the-volume-has-a-lot-of-files"></a>當磁片區有大量檔案時，如何避免許可權擁有權設定變慢問題？

傳統上，如果您的 pod 以非根使用者的形式執行 (您應該) ，您必須在 `fsGroup` pod 的安全性內容內指定，讓 pod 可以讀取和寫入磁片區。 [這裡](https://kubernetes.io/docs/tasks/configure-pod-container/security-context/)有更詳細的說明這項需求。

但設定的副作用 `fsGroup` 是，每次掛接磁片區時，Kubernetes 必須以遞迴方式 `chown()` 以及磁片區 `chmod()` 內的所有檔案和目錄，但有幾個例外，如下所示。 即使磁片區的群組擁有權已經符合要求，也會發生這種情況， `fsGroup` 而且具有許多小型檔案的大型磁片區可能相當昂貴，因為這會導致 pod 啟動花費很長的時間。 在 v 1.20 之前，此案例是已知的問題，而因應措施是將 Pod 以 root 的形式執行：

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: security-context-demo
spec:
  securityContext:
    runAsUser: 0
    fsGroup: 0
```

Kubernetes v 1.20 已解決此問題，如需詳細資料，請參閱 [Kubernetes 1.20：磁片區許可權變更的細微控制](https://kubernetes.io/blog/2020/12/14/kubernetes-release-1.20-fsgroupchangepolicy-fsgrouppolicy/) 。


<!-- LINKS - internal -->

[aks-upgrade]: ./upgrade-cluster.md
[aks-cluster-autoscale]: ./cluster-autoscaler.md
[aks-advanced-networking]: ./configure-azure-cni.md
[aks-rbac-aad]: ./azure-ad-integration-cli.md
[node-updates-kured]: node-updates-kured.md
[aks-preview-cli]: /cli/azure/ext/aks-preview/aks
[az-aks-create]: /cli/azure/aks#az-aks-create
[aks-rm-template]: /azure/templates/microsoft.containerservice/2019-06-01/managedclusters
[aks-cluster-autoscaler]: cluster-autoscaler.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
[aks-windows-cli]: windows-container-cli.md
[aks-windows-limitations]: ./windows-faq.md
[reservation-discounts]:../cost-management-billing/reservations/save-compute-costs-reservations.md
[api-server-authorized-ip-ranges]: ./api-server-authorized-ip-ranges.md
[multi-node-pools]: ./use-multiple-node-pools.md
[availability-zones]: ./availability-zones.md
[private-clusters]: ./private-clusters.md
[bcdr-bestpractices]: ./operator-best-practices-multi-region.md#plan-for-multiregion-deployment
[availability-zones]: ./availability-zones.md
[az-regions]: ../availability-zones/az-region.md
[uptime-sla]: ./uptime-sla.md

<!-- LINKS - external -->
[aks-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
[auto-scaler]: https://github.com/kubernetes/autoscaler
[cordon-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/
[admission-controllers]: https://kubernetes.io/docs/reference/access-authn-authz/admission-controllers/
[private-clusters-github-issue]: https://github.com/Azure/AKS/issues/948
[csi-driver]: https://github.com/Azure/secrets-store-csi-driver-provider-azure
[vm-sla]: https://azure.microsoft.com/support/legal/sla/virtual-machines/
