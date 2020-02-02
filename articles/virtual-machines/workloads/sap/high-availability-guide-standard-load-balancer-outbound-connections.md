---
title: 在 SAP 高可用性案例中使用 Azure Standard Load Balancer 虛擬機器的公用端點連線能力
description: 在 SAP 高可用性案例中使用 Azure Standard Load Balancer 虛擬機器的公用端點連線能力
services: virtual-machines-windows,virtual-network,storage,
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-windows
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 10/28/2019
ms.author: radeltch
ms.openlocfilehash: 179df26eb0cc75899c9b509ebe00410ffa916dc8
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/01/2020
ms.locfileid: "76935187"
---
# <a name="public-endpoint-connectivity-for-virtual-machines-using-azure-standard-load-balancer-in-sap-high-availability-scenarios"></a>在 SAP 高可用性案例中使用 Azure Standard Load Balancer 虛擬機器的公用端點連線能力

本文的範圍是說明設定，這些設定會啟用對公用端點的輸出連線能力。 這些設定主要是以 Pacemaker for SUSE/RHEL 的高可用性內容來進行。  

如果您在高可用性解決方案中使用 Pacemaker 搭配 Azure 隔離代理程式，則 Vm 必須具有 Azure 管理 API 的輸出連線能力。  
本文提供數個選項，可讓您選取最適合您案例的選項。  

## <a name="overview"></a>概觀

透過叢集實現 SAP 解決方案的高可用性時， [Azure Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview)其中一個必要的元件。 Azure 提供兩個負載平衡器 Sku： standard 和 basic。

標準 Azure 負載平衡器提供了一些優於基本負載平衡器的優點。 例如，它可跨 Azure 可用性區域運作，其具有更佳的監視和記錄功能，可更輕鬆地進行疑難排解、減少延遲。 「HA 埠」功能涵蓋所有埠，也就是說，不再需要列出所有個別埠。  

Azure 負載平衡器的基本和標準 SKU 之間有一些重要的差異。 其中一個是處理公用端點的輸出流量。 如需完整的基本與標準 SKU 負載平衡器的比較，請參閱[LOAD BALANCER SKU 比較](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview)。  
 
如果沒有公用 IP 位址的 Vm 放在內部（沒有公用 IP 位址）標準 Azure 負載平衡器的後端集區中，則不會對公用端點進行輸出連線，除非完成額外設定。  

如果已將公用 IP 位址指派給 VM，或 VM 位於具有公用 IP 位址的負載平衡器後端集區中，則會具有公用端點的輸出連線能力。  

SAP 系統通常包含機密的商務資料。 裝載 SAP 系統的 Vm 很少可接受公用 IP 位址。 同時，也有一些案例需要從 VM 到公用端點的輸出連線能力。  

需要存取 Azure 公用端點的案例範例如下：  
- 在 Pacemaker 叢集中使用 Azure 隔離代理程式做為隔離機制
- Azure 備份
- Azure Site Recovery  
- 使用公用存放庫來修補作業系統
- SAP 應用程式資料流程可能需要對公用端點的輸出連線能力

如果您的 SAP 部署不需要對公用端點的輸出連線能力，您就不需要執行額外的設定。 假設也不需要來自公用端點的輸入連線，就足以為您的高可用性案例建立內部標準 SKU Azure Load Balancer。  

> [!Note]
> 當沒有公用 IP 位址的 Vm 放在內部（沒有公用 IP 位址）標準 Azure 負載平衡器的後端集區中時，除非執行額外設定以允許路由傳送至公用端點，否則將不會有輸出網際網路連線能力。  
>如果 Vm 具有公用 IP 位址，或已在具有公用 IP 位址的 Azure 負載平衡器後端集區中，則 VM 將會具有公用端點的輸出連線能力。


請先閱讀下列文章：

* Azure Standard Load Balancer
  * [Azure Standard Load Balancer 總覽](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview)-全面概覽 Azure 標準負載平衡器、重要原則、概念和教學課程 
  * [Azure 中的輸出](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#scenarios)連線-如何在 azure 中達到輸出連線能力的案例
  * [負載平衡器輸出規則](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-rules-overview)-說明負載平衡器輸出規則的概念，以及如何建立輸出規則
* Azure 防火牆
  * [Azure 防火牆總覽](https://docs.microsoft.com/azure/firewall/overview)-azure 防火牆的總覽
  * [教學課程：部署和設定 Azure 防火牆](https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal)-有關如何透過 Azure 入口網站來設定 Azure 防火牆的指示
* [虛擬網路-使用者定義規則](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#user-defined)-Azure 路由概念和規則  
* [安全性群組服務](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags)標籤-如何使用服務標籤來簡化您的網路安全性群組和防火牆設定

## <a name="additional-external-azure-standard-load-balancer-for-outbound-connections-to-internet"></a>適用于網際網路輸出連線的其他外部 Azure Standard Load Balancer

若要達到公用端點的輸出連線能力，而不允許從公用端點對 VM 進行輸入連線，有一個選項是建立具有公用 IP 位址的第二個負載平衡器，將 Vm 新增至第二個負載平衡器的後端集區，並只定義[輸出規則](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-rules-overview)。  
使用[網路安全性群組](https://docs.microsoft.com/azure/virtual-network/security-overview)來控制可從 VM 撥出電話存取的公用端點。  
如需詳細資訊，請參閱檔[輸出連接](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#scenarios)中的案例2。  
設定看起來會像這樣：  

![使用網路安全性群組控制與公用端點的連線能力](./media/high-availability-guide-standard-load-balancer/high-availability-guide-standard-load-balancer-public.png)

### <a name="important-considerations"></a>重要考量︰

- 您可以針對相同子網中的多個 Vm 使用一個額外的公用 Load Balancer，以達到公用端點的輸出連線能力並將成本優化  
- 使用[網路安全性群組](https://docs.microsoft.com/azure/virtual-network/security-overview)來控制可從 vm 存取的公用端點。 您可以將網路安全性群組指派給子網或每個 VM。 可能的話，請使用[服務標記](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags)來降低安全性規則的複雜性。  
- 具有公用 IP 位址和輸出規則的 Azure 標準負載平衡器允許直接存取公用端點。 如果您有公司安全性需求，讓所有輸出流量通過集中式公司解決方案進行審核和記錄，您可能無法在此案例中滿足此需求。  

>[!TIP]
>可能的話，請使用[服務標記](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags)來降低網路安全性群組的複雜性。 

### <a name="deployment-steps"></a>部署步驟

1. 建立負載平衡器  
   1. 在  [Azure 入口網站](https://portal.azure.com)中，按一下 [所有資源]、[新增]，然後搜尋**Load Balancer**  
   1. 按一下 [建立] 
   1. Load Balancer 名稱**MyPublicILB**  
   1. 選取 [**公用**] 作為類型，[**標準**] 作為 SKU  
   1. 選取 [**建立公用 IP 位址**]，並指定為名稱**MyPublicILBFrondEndIP**  
   1. 選取 [**區域冗余**] 做為可用性區域  
   1. 按一下 [審查並建立]，然後按一下 [建立]  
2. 建立後端集區**MyBackendPoolOfPublicILB**並新增 vm。  
   1. 選取虛擬網路  
   1. 選取 Vm 及其 IP 位址，並將其新增至後端集區  
3. [建立輸出規則](https://docs.microsoft.com/azure/load-balancer/configure-load-balancer-outbound-cli#create-outbound-rule)。 目前無法從 Azure 入口網站建立輸出規則。 您可以使用[Azure CLI](https://docs.microsoft.com/azure/cloud-shell/overview?view=azure-cli-latest)來建立輸出規則。  

   ```
    az network lb outbound-rule create --address-pool MyBackendPoolOfPublicILB --frontend-ip-configs MyPublicILBFrondEndIP --idle-timeout 30 --lb-name MyPublicILB --name MyOutBoundRules  --outbound-ports 10000 --enable-tcp-reset true --protocol All --resource-group MyResourceGroup
   ```

4. 建立網路安全性群組規則，以限制對特定公用端點的存取。 如果有現有的網路安全性群組，您可以調整它。 下列範例顯示如何僅允許存取 Azure 管理 API： 
   1. 流覽至網路安全性群組
   1. 按一下 [輸出安全性規則]
   1. 新增規則以**拒絕**對**網際網路**的所有輸出存取。
   1. 新增規則以**允許**存取**AzureCloud**，其優先順序低於拒絕所有網際網路存取的規則優先順序。


   輸出安全性規則看起來會像這樣： 

   ![具有公用 IP 之第二個 Load Balancer 的輸出連線](./media/high-availability-guide-standard-load-balancer/high-availability-guide-standard-load-balancer-network-security-groups.png)

   如需 Azure 網路安全性群組的詳細資訊，請參閱[安全性群組](https://docs.microsoft.com/azure/virtual-network/security-overview)。 

## <a name="azure-firewall-for-outbound-connections-to-internet"></a>適用于網際網路輸出連線的 Azure 防火牆

另一個可讓您達到公用端點輸出連線的選項，而不允許從公用端點對 VM 進行輸入連線，則是使用 Azure 防火牆。 Azure 防火牆是受控服務，具有內建的高可用性，而且可以跨越多個可用性區域。  
您也需要部署[使用者定義的路由](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#custom-routes)，與 Vm 和 azure 負載平衡器部署所在的子網相關聯，並指向 azure 防火牆，以透過 Azure 防火牆路由傳送流量。  
如需如何部署 Azure 防火牆的詳細資訊，請參閱[部署和設定 Azure 防火牆](https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal)。  

架構如下所示：

![使用 Azure 防火牆的輸出連線](./media/high-availability-guide-standard-load-balancer/high-availability-guide-standard-load-balancer-firewall.png)

### <a name="important-considerations"></a>重要考量︰

- Azure 防火牆是雲端原生服務，具有內建的高可用性，而且支援區域部署。
- 需要其他必須命名為 AzureFirewallSubnet 的子網。 
- 如果將 SAP Vm 所在的虛擬網路的大量資料集輸出到另一個虛擬網路中的 VM，或傳送至公用端點，則可能不是符合成本效益的解決方案。 其中一個範例是跨虛擬網路複製大型備份。 如需詳細資訊，請參閱 Azure 防火牆價格。  
- 如果公司防火牆解決方案不是 Azure 防火牆，而且您有安全性需求，可透過集中式企業解決方案通過所有輸出流量，則此解決方案可能不可行。  

>[!TIP]
>可能的話，請使用[服務標記](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags)來降低 Azure 防火牆規則的複雜性。  

### <a name="deployment-steps"></a>部署步驟

1. 部署步驟假設您已經為您的 Vm 定義虛擬網路和子網。  
2. 在部署 VM 和 Standard Load Balancer 的相同虛擬網路中，建立子網**AzureFirewallSubnet** 。  
   1. 在 Azure 入口網站中，流覽至 虛擬網路：按一下 所有資源，搜尋虛擬網路，按一下 虛擬網路，選取 子網。  
   1. 按一下 [新增子網]。 輸入**AzureFirewallSubnet**作為 [名稱]。 輸入適當的 [位址範圍]。 儲存。  
3. 建立 Azure 防火牆。  
   1. 在 Azure 入口網站選取 [所有資源]，按一下 [新增]、[防火牆]、[建立]。 選取 [資源群組] （選取相同的資源群組，其中虛擬網路為）。  
   1. 輸入 Azure 防火牆資源的 [名稱]。 例如， **MyAzureFirewall**。  
   1. 選取 [區域]，然後選取至少兩個可用性區域，並與您的 Vm 部署所在的可用性區域一致。  
   1. 選取您的虛擬網路，其中會部署 SAP Vm 和 Azure 標準負載平衡器。  
   1. [公用 IP 位址]：按一下 [建立]，然後輸入名稱。 例如**MyFirewallPublicIP**。  
4. 建立 Azure 防火牆規則以允許對指定之公用端點的輸出連線能力。 此範例說明如何允許存取 Azure 管理 API 公用端點。  
   1. 選取 [規則]、[網路規則集合]，然後按一下 [新增網路規則集合]。  
   1. 名稱： **MyOutboundRule**，輸入優先順序，選取 [動作] [**允許**]。  
   1. 服務：名稱**ToAzureAPI**。  通訊協定：選取 [**任何**]。 來源位址：輸入您的子網範圍，其中會為實例部署 Vm 和 Standard Load Balancer，例如： **11.97.0.0/24**。 目的地埠：輸入<b>*</b>。  
   1. 儲存
   1. 當您仍位於 Azure 防火牆上時，請選取 [總覽]。 記下 Azure 防火牆的私人 IP 位址。  
5. 建立 Azure 防火牆的路由  
   1. 在 Azure 入口網站選取 [所有資源]，然後按一下 [新增]、[路由表]、[建立]。  
   1. 輸入 [名稱 MyRouteTable]、選取 [訂用帳戶]、[資源群組] 和 [位置] （符合虛擬網路和防火牆的位置）。  
   1. 儲存  

   防火牆規則看起來像： ![與 Azure 防火牆的輸出連線](./media/high-availability-guide-standard-load-balancer/high-availability-guide-standard-load-balancer-firewall-rule.png)

6. 從您 Vm 的子網建立使用者定義的路由到**MyAzureFirewall**的私人 IP。
   1. 當您定位於路由表時，請按一下 [路由]。 選取 [新增]。 
   1. 路由名稱： ToMyAzureFirewall，位址首碼： **0.0.0.0/0**。 下一個躍點類型：選取 [虛擬裝置]。 下一個躍點位址：輸入您所設定之防火牆的私人 IP 位址： **11.97.1.4**。  
   1. 儲存

## <a name="using-proxy-for-pacemaker-calls-to-azure-management-api"></a>使用 Proxy 對 Azure 管理 API 進行 Pacemaker 呼叫

您可以使用 proxy 來允許對 Azure 管理 API 公用端點的 Pacemaker 呼叫。  

### <a name="important-considerations"></a>重要考量︰

  - 如果已有公司 proxy，您可以將輸出呼叫路由至其上的公用端點。 對公用端點的輸出呼叫將會經過公司控制點。  
  - 請確定 proxy 設定允許 Azure 管理 API 的輸出連線能力： https://management.azure.com  
  - 請確定有從 Vm 到 Proxy 的路由  
  - Proxy 只會處理 HTTP/HTTPS 呼叫。 如果需要對不同通訊協定（例如 RFC）的公用端點進行輸出呼叫，則需要替代解決方案  
  - Proxy 解決方案必須是高可用性，以避免 Pacemaker 叢集中的不穩定  
  - 視 proxy 的位置而定，它可能會在從 Azure 隔離代理程式到 Azure 管理 API 的呼叫中引進額外的延遲。 如果您的公司 proxy 仍在內部部署，而您的 Pacemaker 叢集位於 Azure 中，請測量延遲並考慮，如果此解決方案適合您  
  - 如果沒有高可用性的公司 proxy，則不建議使用此選項，因為客戶會產生額外的成本和複雜度。 不過，如果您決定要部署額外的 proxy 解決方案，以允許從 Pacemaker 至 Azure 管理公用 API 的輸出連線，請確定 proxy 具有高可用性，且從 Vm 到 proxy 的延遲很低。  

### <a name="pacemaker-configuration-with-proxy"></a>使用 Proxy 的 Pacemaker 設定 

業界有許多不同的 Proxy 選項可供使用。 Proxy 部署的逐步指示超出本檔的範圍。 在下列範例中，我們假設您的 proxy 回應**MyProxyService**並接聽埠**我**。  
若要允許 pacemaker 與 Azure 管理 API 進行通訊，請在所有叢集節點上執行下列步驟：  

1. 編輯 pacemaker 設定檔/etc/sysconfig/pacemaker，並新增下列幾行（所有叢集節點）：  
   ```
   sudo vi /etc/sysconfig/pacemaker
   # Add the following lines
   http_proxy=http://MyProxyService:MyProxyPort
   https_proxy=http://MyProxyService:MyProxyPort
   ```

2. 重新開機**所有**叢集節點上的 pacemaker 服務。  
  - SUSE  
     ```
     # Place the cluster in maintenance mode
     sudo crm configure property maintenance-mode=true
     #Restart on all nodes
     sudo systemctl restart pacemaker
     # Take the cluster out of maintenance mode
     sudo crm configure property maintenance-mode=true
     ```

  - Red Hat  
     ```
     # Place the cluster in maintenance mode
     sudo pcs property set maintenance-mode=true
     #Restart on all nodes
     sudo systemctl restart pacemaker
     # Take the cluster out of maintenance mode
     sudo pcs property set maintenance-mode=false
     ```

## <a name="next-steps"></a>後續步驟

* [瞭解如何在 Azure 中設定 SUSE 的 Pacemaker](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker)
* [瞭解如何在 Azure 中的 Red Hat 上設定 Pacemaker](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-pacemaker)
