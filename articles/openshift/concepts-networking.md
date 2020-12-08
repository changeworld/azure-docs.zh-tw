---
title: 概念 - Azure Red Hat OpenShift 4 網路功能圖表
description: Azure Red Hat OpenShift 網路的網路功能圖表與概觀
author: sakthi-vetrivel
ms.author: suvetriv
ms.topic: tutorial
ms.service: container-service
ms.date: 11/23/2020
ms.openlocfilehash: 2d9169e836b5819756e716c64ed9d41094f08c5e
ms.sourcegitcommit: df66dff4e34a0b7780cba503bb141d6b72335a96
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/02/2020
ms.locfileid: "96512355"
---
# <a name="network-concepts-for-azure-red-hat-openshift-aro"></a>Azure Red Hat OpenShift (ARO) 的網路概念

本指南說明 OpenShift 4 叢集上 Azure Red Hat OpenShift 的網路功能，以及重要端點的圖表和清單。 如需關於核心 OpenShift 網路功能概念的詳細資訊，請參閱 [Azure Red Hat OpenShift 4 網路功能文件](https://docs.openshift.com/aro/4/networking/understanding-networking.html)。

![Azure Red Hat OpenShift 4 網路功能圖表](./media/concepts-networking/aro4-networking-diagram.png)

當您在 OpenShift 4 上部署 Azure Red Hat 時，整個叢集會包含在虛擬網路中。 在此虛擬網路中，您的主要節點和背景工作角色節點都位於自己的子網路中。 每個子網路都會使用內部負載平衡器和公用負載平衡器。

## <a name="networking-components"></a>網路功能元件

下列清單涵蓋 Azure Red Hat OpenShift 叢集中的重要網路功能元件。

* **aro-pls**
    * 這是 Microsoft 和 Red Hat 網站可靠性工程師用來管理叢集的 Azure Private Link 端點。
* **aro-internal-lb**
    * 此端點會平衡 API 伺服器的流量。 針對此負載平衡器，主要節點位於後端集區中。
* **aro-public-lb**
    * 如果 API 是公用的，此端點會路由傳送並平衡 API 伺服器的流量。 此端點會指派公用傳出 IP，讓主機可以存取 Azure Resource Manager 並回報叢集健康情況。
* **aro-internal**
    * 此端點會平衡內部服務流量。 針對此負載平衡器，背景工作角色節點位於後端集區中。
    * 依預設不會建立此負載平衡器。 在您使用正確的註釋建立類型為 LoadBalancer 的服務之後，就會建立此負載平衡器。 例如：service.beta.kubernetes.io/azure-load-balancer-internal: "true"。
* **aro-internal-lb**
    * 此端點會用於任何公用流量。 當您建立應用程式和路由時，這就是輸入流量的路徑。
    * 此負載平衡器也會透過 Azure Load Balancer 輸出規則，涵蓋來自執行於背景工作角色節點中任何 Pod 的輸出網際網路連線能力。
        * 輸出規則目前無法設定。 其會將 1,024 個 TCP 通訊埠配置到每個節點。
        * LB 規則中未設定 DisableOutboundSnat，因此 Pod 可以取得此 ALB 中所設定的任何公用 IP 做為輸出 IP。
        * 這兩個先前的重點會因此將公用的 LoadBalancer-type 服務新增到 ARO，此為新增暫時 SNAT 連接埠的唯一方法。
* **aro-outbound-pip**
    * 此端點可做為背景工作角色節點的公用 IP (PIP)。
    * 此端點可讓服務將來自 Azure Red Hat OpenShift 叢集的特定 IP 新增至允許清單。
* **aro-nsg**
    * 當您公開服務時，API 會在此網路安全性群組中建立規則，讓流量流經並到達控制平面和節點。
    * 根據預設，此網路安全性群組會允許所有輸出流量。 目前，輸出流量只能限制為 Azure Red Hat OpenShift 控制平面。
* **aro-controlplane-nsg**
  * 此端點僅允許透過連接埠 6443 輸入主要節點的流量。
* **Azure Container Registry**
    * 這是 Microsoft 內部所提供及使用的容器登錄。 此登錄是唯讀的，不適合供 Azure Red Hat OpenShift 使用者使用。
        * 此登錄提供主機平台映像和叢集元件。 例如，監視或記錄容器。
        * 此登錄的連線會透過服務端點 (Azure 服務之間的內部連線) 發生。
        * 根據預設，不會在叢集外部提供此內部登錄。
* **私人連結**
    * 允許從管理平面到叢集的網路連線，以便 Microsoft 和 Red Hat 網站可靠性工程師協助管理您的叢集。

## <a name="networking-policies"></a>網路原則

* **輸入**：輸入網路原則被視為 [OpenShift SDN](https://docs.openshift.com/container-platform/4.5/networking/openshift_sdn/about-openshift-sdn.html) 的一部分而受到支援。 依預設會啟用此網路原則，而由使用者強制執行。 雖然輸入網路原則符合 V1 NetworkPolicy 規範，但並不支援輸出和 IPBlock 類型。

* **輸出**：使用 OpenShift 中的[輸出防火牆](https://docs.openshift.com/container-platform/4.5/networking/openshift_sdn/configuring-egress-firewall.html)功能可支援輸出原則。 每個命名空間/專案只有一個輸出原則。 「預設」命名空間不支援輸出原則，且會依序加以評估 (從第一個到最後一個)。

## <a name="networking-basics-in-openshift"></a>OpenShift 中的網路功能基本概念

OpenShift 軟體定義網路 [(SDN)](https://docs.openshift.com/container-platform/4.5/networking/openshift_sdn/about-openshift-sdn.html) 是用來設定使用 Open vSwitch [(OVS)](https://www.openvswitch.org/) 的重疊網路，這是以容器網路介面 (CNI) 規格為基礎的 OpenFlow 實作。 SDN 支援不同的外掛程式 - 網路原則是在 Azure Red Hat OpenShift 4 中使用的外掛程式。 所有網路通訊都是由 SDN 管理，因此您的虛擬網路上不需要額外的路由，即可達到 Pod 對 Pod 的通訊。

## <a name="networking--for-azure-red-hat-openshift"></a>Azure Red Hat OpenShift 的網路功能

以下是 Azure Red Hat OpenShift 特有的網路功能：
* 使用者可在現有的虛擬網路中建立其 ARO 叢集，或在建立其 ARO 叢集時建立虛擬網路。
* Pod 和服務網路 CIDR 是可設定的。
* 節點和主機位於不同的子網路中。
* 節點和主機虛擬網路子網路應為最小 /27。
* Pod CIDR 大小應為最小 /18 (Pod 網路是不可路由傳送的 IP，且只會在 OpenShift SDN 內使用)。
* 每個節點會為其 Pod 配置 /23 個子網 (512 個 IP)。 這項值不能被改變。
* 您無法將 Pod 連接到多個網路。
* 您無法設定輸出靜態 IP。 (這是 OpenShift 功能。 如需詳細資訊，請參閱[設定輸出 IP](https://docs.openshift.com/aro/4/networking/openshift_sdn/assigning-egress-ips.html))。

## <a name="network-settings"></a>網路設定

以下是適用於 Azure Red Hat OpenShift 4 叢集的網路設定：

* **API 可見度** - 在執行 [az aro create command](tutorial-create-cluster.md#create-the-cluster) 時，設定 API 可見度。
    * 「公用」- API 伺服器可由外部網路存取。
    * 「私人」- API 伺服器已從「主要」子網路指派私人 IP，只能使用連線的網路 (對等互連虛擬網路、叢集中的其他子網路) 進行存取。 系統會代表客戶建立私人 DNS 區域。
* **輸入可見度** - 在執行 [az aro create command](tutorial-create-cluster.md#create-the-cluster) 時，設定 API 可見度。
    * 「公用」路由會預設為公用 Standard Load Balancer (此設定可以變更)。
    * 「私人」路由會預設為內部負載平衡器 (這可以變更)。

## <a name="network-security-groups"></a>網路安全性群組
網路安全性群組建立於節點的資源群組中，會對使用者鎖定。 網路安全性群組會直接指派給不在節點 NIC 上的子網路。 網路安全性群組是不可變的，且使用者沒有權限可對其進行變更。

使用公開可見的 API 伺服器時，您無法建立網路安全性群組，並將其指派給 NIC。

## <a name="domain-forwarding"></a>網域轉送
Azure Red Hat OpenShift 會使用 CoreDNS。 您可以設定網域轉送。 您無法將自己的 DNS 導入您的虛擬網路。 如需詳細資訊，請參閱關於[使用 DNS 轉送](https://docs.openshift.com/aro/4/networking/dns-operator.html#nw-dns-forward_dns-operator)的文件。

## <a name="whats-new-in-openshift-45"></a>OpenShift 4.5 的新功能

透過 OpenShift 4.5 的支援，Azure Red Hat OpenShift 導入了一些重大的架構變更。 這些變更僅適用於執行 OpenShift 4.5 的新建叢集。 對於已升級至 OpenShift 4.5 的現有叢集，升級程序將不會變更其網路架構。 使用者必須重新建立其叢集，才能使用這個新架構。

![Azure Red Hat OpenShift 4.5 網路功能圖表](./media/concepts-networking/aro-4-5-networking-diagram.png)

如上圖所示，您會注意到幾項變更：
* 過去，ARO 使用兩個公用 LoadBalancer：一個用於 API 伺服器，另一個用於背景工作節點集區。 經此架構更新後，兩者已合併在單一 LoadBalancer 下。 
* 為了降低複雜性，已移除專用的外部 IP 位址資源。
* ARO 控制平面現在與 ARO 背景工作節點共用相同的網路安全性群組。

如需 OpenShift 4.5 的詳細資訊，請參閱 [OpenShift 4.5 版本資訊](https://docs.openshift.com/container-platform/4.5/release_notes/ocp-4-5-release-notes.html)。

## <a name="next-steps"></a>後續步驟
如需輸出流量的詳細資訊，以及 Azure Red Hat OpenShift 針對輸出所支援的功能，請參閱[支援原則](support-policies-v4.md)文件。
