---
title: 概念 - Azure Red Hat OpenShift 4 網路功能圖表
description: Azure Red Hat OpenShift 網路的網路功能圖表與概觀
author: sakthi-vetrivel
ms.author: suvetriv
ms.topic: tutorial
ms.service: container-service
ms.date: 06/22/2020
ms.openlocfilehash: fb81405e85d6e2653e0cf6c007c363493992161a
ms.sourcegitcommit: e71da24cc108efc2c194007f976f74dd596ab013
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/29/2020
ms.locfileid: "87419966"
---
# <a name="networking-in-azure-red-hat-on-openshift-4"></a>Azure Red Hat OpenShift 4 網路功能

本指南涵蓋 OpenShift 4 叢集上 Azure Red Hat 的網路功能，以及重要端點的圖表和清單。

如需有關核心 OpenShift 網路功能概念的詳細資訊，請參閱 [Azure Red Hat OpenShift 4 網路功能文件](https://docs.openshift.com/aro/4/networking/understanding-networking.html)。

## <a name="networking-concepts-in-azure-red-hat-openshift"></a>Azure Red Hat OpenShift 中的網路功能概念

![Azure Red Hat OpenShift 4 網路功能圖表](./media/concepts-networking/aro4-networking-diagram.png)

當您在 OpenShift 4 上部署 Azure Red Hat 時，整個叢集會包含在虛擬網路中。 在此虛擬網路中，您的主要節點和背景工作角色節點都位於自己的子網路中。 每個子網路都會使用公用和內部負載平衡器。

## <a name="explanation-of-endpoints"></a>端點的說明

下列清單涵蓋 Azure Red Hat OpenShift 叢集中的重要端點。

* **aro-pls**
    * 這是 Microsoft 和 Red Hat 網站可靠性工程師用來協助管理叢集的 Azure Private Link 端點。
* **aro-internal-lb**
    * 此端點會平衡 API 伺服器的流量。 針對此負載平衡器，主要節點位於後端集區中。
* **aro-public-lb**
    * 如果 API 是公用的，此端點會路由傳送並平衡 API 伺服器的流量。 此端點會指派公用傳出 IP，讓主機可以存取 Azure Resource Manager 並回報叢集健康情況。
* **aro-internal**
    * 此端點會平衡內部服務流量。 針對此負載平衡器，背景工作角色節點位於後端集區中。
    * 依預設不會建立此負載平衡器。 在您使用正確的註釋建立類型為 LoadBalancer 的服務之後，就會建立此負載平衡器。 例如：service.beta.kubernetes.io/azure-load-balancer-internal: "true"。
* **網路原則 (輸入)**
    * 支援做為 OpenShift SDN 的一部分
    * 預設為啟用，由客戶完成的強制執行
    * 符合 V1 NetworkPolicy 規範，但是尚不支援「輸出與 IPBlock」類型
    * **aro**
    * 此端點會平衡 API 伺服器的流量。 針對此負載平衡器，主要節點位於後端集區中。
  * **aro-internal-lb**
    * 此端點會用於任何公用流量。 當您建立應用程式和路由時，這就是輸入流量的路徑。
    * 此負載平衡器也會透過 Azure Load Balancer 輸出規則，涵蓋來自執行於背景工作角色節點中任何 Pod 的輸出網際網路連線能力。
        * 目前無法設定輸出規則。 其會將 1,024 個 TCP 通訊埠配置到每個節點。
        * LB 規則中未設定 DisableOutboundSnat，因此 Pod 可以取得此 ALB 中所設定的任何公用 IP 做為輸出 IP。
        * 這兩個先前的重點會因此將公用的 LoadBalancer-type 服務新增到 ARO，此為新增暫時 SNAT 連接埠的唯一方法。
* **網路原則 (輸出)**
    * 使用 OpenShift 中的輸出防火牆功能可支援輸出原則。
    * 每個命名空間/專案只能有一個。
    * 「預設」命名空間不支援輸出原則。
    * 輸出原則規則會依序進行評估 (從第一個到最後一個)。
    * **aro-outbound-pip**
        * 此端點可做為背景工作角色節點的公用 IP (PIP)。
        * 此端點可讓服務將來自 Azure Red Hat OpenShift 叢集的特定 IP 新增至允許清單。
* **aro-node-nsg**
    * 當您公開服務時，API 會在此網路安全性群組中建立規則，讓流量流經並到達節點。
    * 根據預設，此網路安全性群組會允許所有輸出流量。 目前，輸出流量只能限制為 Azure Red Hat OpenShift 控制平面。
* **aro-controlplane-nsg**
    * 此端點僅允許透過連接埠 6443 輸入主要節點的流量。
* **Azure Container Registry**
    * 這是 Microsoft 內部所提供及使用的容器登錄。
        * 此登錄提供主機平台映像和叢集元件。 例如，監視或記錄容器。
        * 不適合供 Azure Red Hat OpenShift 客戶使用。  
        * 唯讀。
        * 此登錄的連線會透過服務端點 (Azure 服務之間的內部連線) 發生。
        * 根據預設，不會在叢集外部提供此內部登錄。
* **私人連結**
    * 允許從管理平面到叢集進行叢集管理的網路連線。
    * Microsoft 和 Red Hat 網站可靠性工程師可協助管理您的叢集。

## <a name="networking-basics-in-openshift"></a>OpenShift 中的網路功能基本概念

OpenShift 軟體定義網路 (SDN) 是用來設定使用 Open vSwitch (OVS) 的重疊網路，這是以容器網路介面 (CNI) 規格為基礎的 OpenFlow 實作。 SDN 支援不同的外掛程式，而網路原則則是在 Azure Red Hat OpenShift 4 中使用的外掛程式。 所有網路通訊都是由 SDN 管理，因此您的虛擬網路上不需要額外的路由，即可達到 Pod 對 Pod 的通訊。

## <a name="azure-red-hat-openshift-networking-specifics"></a>Azure Red Hat OpenShift 網路功能的詳細資訊

下列是 Azure Red Hat OpenShift 特有的功能：
* 支援攜帶您自己的虛擬網路。
* Pod 和服務網路 CIDR 是可設定的。
* 節點和主機位於不同的子網路中。
* 節點和主機虛擬網路子網路應為最小 /27。
* Pod CIDR 大小應為最小 /18 (Pod 網路是不可路由傳送的 IP，且只會在 OpenShift SDN 內使用)。
* 每個節點會為其 Pod 配置 /23 個子網 (512 個 IP)。 這項值不能被改變。
* 您無法將 Pod 連接到多個網路。
* 您無法設定輸出靜態 IP。 (這是 OpenShift 功能。 如需詳細資訊，請參閱[設定輸出 IP](https://docs.openshift.com/aro/4/networking/openshift_sdn/assigning-egress-ips.html))。

## <a name="network-settings"></a>網路設定

Azure Red Hat OpenShift 4 中提供下列網路設定：

* **API 可見度** - 在執行 [az aro create command](tutorial-create-cluster.md#create-the-cluster) 時，設定 API 可見度。
    * 「公用」- API 伺服器可由外部網路存取。
    * 「私人」- API 伺服器已從「主要」子網路指派私人 IP，只能使用連線的網路 (對等互連虛擬網路、叢集中的其他子網路) 進行存取。 系統會代表客戶建立私人 DNS 區域。
* **輸入可見度** - 在執行 [az aro create command](tutorial-create-cluster.md#create-the-cluster) 時，設定 API 可見度。
    * 「公用」路由會預設為公用 Azure Standard Load Balancer (這可以變更)。
    * 「私人」路由會預設為內部負載平衡器 (這可以變更)。

## <a name="network-security-groups"></a>網路安全性群組
系統會在節點的資源群組中建立網路安全性群組 (已鎖定)。 網路安全性群組會直接指派給不在節點 NIC 上的子網路。 網路安全性群組是不可變的，這表示您沒有權限可對其進行變更。 

不過，使用公開可見的 API 伺服器時，您無法建立網路安全性群組，並將其指派給 NIC。

## <a name="domain-forwarding"></a>網域轉送
Azure Red Hat OpenShift 會使用 CoreDNS。 可以設定網域轉送 (如需詳細資料，請參閱[使用 DNS 轉送](https://docs.openshift.com/aro/4/networking/dns-operator.html#nw-dns-forward_dns-operator)上的文件)。

目前，您無法將自己的 DNS 帶入您的虛擬網路。


如需輸出流量的詳細資訊，以及 Azure Red Hat OpenShift 針對輸出所支援的功能，請參閱[支援原則](support-policies-v4.md)文件。
