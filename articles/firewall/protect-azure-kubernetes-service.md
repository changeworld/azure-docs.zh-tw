---
title: 使用 Azure 防火牆來保護 Azure Kubernetes Service (AKS) 部署
description: 瞭解如何使用 Azure 防火牆來保護 Azure Kubernetes Service (AKS) 部署
author: vhorne
ms.service: firewall
services: firewall
ms.topic: how-to
ms.date: 09/03/2020
ms.author: victorh
ms.openlocfilehash: 43755b312a64c429b38a07c8c4fad8c85b08342a
ms.sourcegitcommit: bf1340bb706cf31bb002128e272b8322f37d53dd
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/03/2020
ms.locfileid: "89437848"
---
# <a name="use-azure-firewall-to-protect-azure-kubernetes-service-aks-deployments"></a>使用 Azure 防火牆來保護 Azure Kubernetes Service (AKS) 部署

Azure Kubernetes Service (AKS) 在 Azure 上提供受控 Kubernetes 叢集。 藉由將大部分的責任卸載到 Azure，可降低管理 Kubernetes 的複雜性和操作額外負荷。 AKS 可為您處理重要的工作，例如健康情況監視及維護，並透過促進的治理提供企業級和安全的叢集。

Kubernetes 會根據可用的計算資源及每個容器的資源需求，來協調虛擬機器的叢集，並排程容器在這些虛擬機器上執行。 容器會分組為 pod、Kubernetes 的基本操作單位，而這些 pod 會調整成您想要的狀態。

基於管理和操作目的，AKS 叢集中的節點需要存取特定連接埠和完整網域名稱 (FQDN)。 這些動作可能是與 API 伺服器通訊，或下載並安裝核心 Kubernetes 叢集元件和節點安全性更新。 Azure 防火牆可協助您鎖定環境並篩選輸出流量。

遵循本文中的指導方針，使用 Azure 防火牆為您的 Azure Kubernetes 叢集提供額外的保護。

## <a name="prerequisites"></a>必要條件

- 具有正在執行之應用程式的已部署 Azure Kubernetes 叢集。

   如需詳細資訊，請參閱 [教學課程：部署 Azure Kubernetes Service (AKS) ](../aks/tutorial-kubernetes-deploy-cluster.md) 叢集和 [教學課程：在 Azure Kubernetes Service 中執行應用程式 (AKS) ](../aks/tutorial-kubernetes-deploy-application.md)。


## <a name="securing-aks"></a>保護 AKS

Azure 防火牆提供 AKS FQDN 標記以簡化設定。 使用下列步驟來允許輸出 AKS 平臺流量：

- 當您使用 Azure 防火牆來限制輸出流量，並建立使用者定義的路由 (UDR) 將所有輸出流量導向時，請務必在防火牆中建立適當的 DNAT 規則，以正確地允許連入流量。 

   使用具有 UDR 的 Azure 防火牆會中斷輸入設定，因為非對稱式路由。 如果 AKS 子網具有前往防火牆私人 IP 位址的預設路由，但您使用的是公用負載平衡器，就會發生此問題。 例如，類型為 *LoadBalancer*的輸入或 Kubernetes 服務。

   在此情況下，系統會透過傳入負載平衡器流量的公用 IP 位址接收它，但傳回路徑則會通過防火牆的私人 IP 位址。 由於防火牆是具狀態，其會捨棄傳回封包，因為防火牆並不知道已建立的工作階段。 若要了解如何整合 Azure 防火牆與您的連入或服務負載平衡器，請參閱[整合 Azure 防火牆與 Azure Standard Load Balancer](integrate-lb.md) \(部分機器翻譯\)。
- 建立應用程式規則集合，並新增規則以啟用 *AzureKubernetesService* FQDN 標記。 來源 IP 位址範圍是主機集區虛擬網路，通訊協定是 HTTPs，目的地是 AzureKubernetesService。
- AKS 叢集需要下列輸出連接埠/網路規則：

   - TCP 通訊埠 443
   - 如果您有需要與 API 伺服器通訊的應用程式，則需要 TCP [*IPAddrOfYourAPIServer*]：443。 此變更可以在建立叢集之後設定。
   - TCP 埠9000和 UDP 埠1194，通道前端 pod 可與 API 伺服器上的通道進行通訊。

      若要更明確，請參閱 **。 <location>* 下表中的 azmk8s.io 和位址：

   | 目的地端點                                                             | 通訊協定 | 連接埠    | 使用  |
   |----------------------------------------------------------------------------------|----------|---------|------|
   | **`*:1194`** <br/> *Or* <br/> [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) - **`AzureCloud.<Region>:1194`** <br/> *Or* <br/> [區域 Cidr](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files) - **`RegionCIDRs:1194`** <br/> *Or* <br/> **`APIServerIP:1194`** `(only known after cluster creation)`  | UDP           | 1194      | 用於節點和控制平面之間的通道通訊安全通訊。 |
   | **`*:9000`** <br/> *Or* <br/> [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) - **`AzureCloud.<Region>:9000`** <br/> *Or* <br/> [區域 Cidr](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files) - **`RegionCIDRs:9000`** <br/> *Or* <br/> **`APIServerIP:9000`** `(only known after cluster creation)`  | TCP           | 9000      | 用於節點和控制平面之間的通道通訊安全通訊。 |

   - UDP 連接埠 123，用於網路時間通訊協定 (NTP) 時間同步處理 (Linux 節點)。
   - 如果您有 Pod 會直接存取 API 伺服器，則也需要針對 DNS 使用 UDP 連接埠 53。

   如需詳細資訊，請參閱 [Azure Kubernetes Service (AKS) 中控制叢集節點的輸出流量 ](../aks/limit-egress-traffic.md)。
- 設定 AzureMonitor 和儲存體服務標記。 Azure 監視器接收 log analytics 資料。

   您也可以個別允許您的工作區 URL： `<worksapceguid>.ods.opinsights.azure.com` 、和 `<worksapceguid>.oms.opinsights.azure.com` 。 您可以透過下列其中一種方式來解決這個情況：

    - 允許從您的主機集區子網進行 HTTPs 存取 `*. ods.opinsights.azure.com` ，以及 `*.oms. opinsights.azure.com` 。 這些萬用字元 Fqdn 會啟用必要的存取權，但較不嚴格。
    - 使用下列 log analytics 查詢來列出確切的必要 Fqdn，然後在您的防火牆應用程式規則中明確地允許它們：
   ```
   AzureDiagnostics 
   | where Category == "AzureFirewallApplicationRule" 
   | search "Allow" 
   | search "*. ods.opinsights.azure.com" or "*.oms. opinsights.azure.com"
   | parse msg_s with Protocol " request from " SourceIP ":" SourcePort:int " to " FQDN ":" * 
   | project TimeGenerated,Protocol,FQDN 
   ```


## <a name="next-steps"></a>接下來的步驟

- 若要深入瞭解 Azure Kubernetes Service，請參閱 [Azure Kubernetes Service (AKS) 的 Kubernetes 核心概念 ](../aks/concepts-clusters-workloads.md)。
