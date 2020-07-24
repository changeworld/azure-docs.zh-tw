---
title: 適用於 SAP HA 案例中 Azure VM 和 Standard ILB 的公用端點連線能力
description: 適用於 SAP 高可用性案例中使用 Azure Standard Load Balancer 之虛擬機器的公用端點連線能力
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
ms.date: 06/16/2020
ms.author: radeltch
ms.openlocfilehash: eca36a2c13fcdc232d4d06ca6e59598fe9a611f2
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87082132"
---
# <a name="public-endpoint-connectivity-for-virtual-machines-using-azure-standard-load-balancer-in-sap-high-availability-scenarios"></a>適用於 SAP 高可用性案例中使用 Azure Standard Load Balancer 之虛擬機器的公用端點連線能力

此文章的範圍是說明能為公用端點啟用輸出連線能力的設定。 設定的內容主要為針對 SUSE / RHEL 搭配 Pacemaker 來取得高可用性。  

如果您的高可用性解決方案中是搭配 Azure 柵欄代理程式使用 Pacemaker，則 VM 必須針對 Azure 管理 API 具有輸出連線能力。  
此文章會提供數個選項，以讓您選取最適合您案例的選項。  

## <a name="overview"></a>概觀

透過叢集為 SAP 解決方案實作高可用性時，其中一個必要的元件為 [Azure Load Balancer](../../../load-balancer/load-balancer-overview.md)。 Azure 提供兩種 Load Balancer SKU：Standard (標準) 和 Basic (基本)。

Standard Azure Load Balancer 能提供一些優於 Basic Load Balancer 的優點。 例如，其可以跨 Azure 可用性區域運作、具有較佳的監視和記錄功能來使疑難排解更加輕鬆，以及具有較低的延遲。 「HA 連接埠」功能會涵蓋所有連接埠，也就是說使用者不再需要列出所有個別的連接埠。  

Azure Load Balancer 的 Basic 和 Standard SKU 之間還有一些重要差異。 其中一個是處理傳送到公用端點的連出流量。 如需 Basic 與 Standard SKU 負載平衡器之間的完整比較，請參閱 [Load Balancer SKU 的比較](../../../load-balancer/load-balancer-overview.md)。  
 
當不具公用 IP 位址的 VM 放在內部 (沒有公用 IP 位址) Standard Azure Load Balancer 的後端集區時，除非另行設定，否則將不會有對公用端點的輸出連線能力。  

如果為 VM 指派公用 IP 位址，或是 VM 位於具有公用 IP 位址之負載平衡器的後端集區，其將會有對公用端點的輸出連線能力。  

SAP 系統通常會包含敏感性的商務資料。 此情況通常無法讓裝載 SAP 系統的 VM 具有公用 IP 位址。 同時，也會存在需要從 VM 輸出連線到公用端點的案例。  

需要存取 Azure 公用端點的案例範例為：  
- 使用 Azure 柵欄代理程式在 Pacemaker 叢集中作為柵欄機制
- Azure 備份
- Azure Site Recovery  
- 使用公用存放庫來修補作業系統
- SAP 應用程式資料流程可能需要對公用端點的輸出連線能力

如果您的 SAP 部署不需要對公用端點的輸出連線能力，您便不需要實作其他設定。 針對您的高可用性案例建立內部 Standard SKU Azure Load Balancer 便以足夠，前提是您也不需要來自公用端點的輸入連線能力。  

> [!Note]
> 當不具公用 IP 位址的 VM 放在內部 (沒有公用 IP 位址) Standard Azure Load Balancer 的後端集區時，除非另外設定來允許路由傳送至公用端點，否則不會有輸出網際網路連線能力。  
>如果 VM 有公用 IP 位址，或是已經位於具有公用 IP 位址之 Azure Load Balancer 的後端集區，該 VM 將已具備對公用端點的輸出連線能力。


請先參閱下列文章：

* Azure Standard Load Balancer
  * [Azure Standard Load Balancer 概觀](../../../load-balancer/load-balancer-overview.md) - Azure Standard Load Balancer、重要原則、概念及教學課程的完整概觀 
  * [Azure 中的輸出連線](../../../load-balancer/load-balancer-outbound-connections.md#scenarios) \(部分機器翻譯\) - 如何在 Azure 中達成輸出連線能力的案例
  * [Load Balancer 連出規則](../../../load-balancer/load-balancer-outbound-connections.md#outboundrules) \(部分機器翻譯\) - 說明 Load Balancer 連出規則的概念，以及如何建立連出規則
* Azure 防火牆
  * [Azure 防火牆概觀](../../../firewall/overview.md) - Azure 防火牆的概觀
  * [教學課程：部署和設定 Azure 防火牆](../../../firewall/tutorial-firewall-deploy-portal.md) - 如何透過 Azure 入口網站設定 Azure 防火牆的指示
* [虛擬網路 - 使用者定義規則](../../../virtual-network/virtual-networks-udr-overview.md#user-defined) \(部分機器翻譯\) - Azure 路由概念和規則  
* [安全性群組服務標籤](../../../virtual-network/security-overview.md#service-tags) \(部分機器翻譯\) - 如何使用服務標籤來簡化網路安全性群組和防火牆設定

## <a name="additional-external-azure-standard-load-balancer-for-outbound-connections-to-internet"></a>透過額外的外部 Azure Standard Load Balancer 來對網際網路進行輸出連線

在不允許公用端點對 VM 之輸入連線能力的情況下，達成對公用端點之輸出連線能力的其中一個選項，便是建立具有公用 IP 位址的第二個 Load Balancer，將 VM 新增到第二個 Load Balancer 的後端集區，然後僅定義[連出規則](../../../load-balancer/load-balancer-outbound-connections.md#outboundrules) \(部分機器翻譯\)。  
使用[網路安全性群組](../../../virtual-network/security-overview.md) \(部分機器翻譯\) 以控制來自 VM 的輸出呼叫可存取的公用端點。  
如需詳細資訊，請參閱[輸出連線](../../../load-balancer/load-balancer-outbound-connections.md#scenarios) \(部分機器翻譯\) 文件中的案例 2。  
該設定看起來會像這樣：  

![使用網路安全性群組來控制對公用端點的連線能力](./media/high-availability-guide-standard-load-balancer/high-availability-guide-standard-load-balancer-public.png)

### <a name="important-considerations"></a>重要考量︰

- 您可以針對位於相同子網路中的多部 VM，使用一個額外的公用 Load Balancer 來達成對公用端點的輸出連線能力，並將成本最佳化  
- 使用[網路安全性群組](../../../virtual-network/security-overview.md) \(部分機器翻譯\) 以控制可以從 VM 存取的公用端點。 您可以將網路安全性群組指派到子網路，或是指派到每部 VM。 可能的話，請使用[服務標籤](../../../virtual-network/security-overview.md#service-tags) \(部分機器翻譯\) 來減少安全性規則的複雜度。  
- 具有公用 IP 位址及連出規則的 Azure Standard Load Balancer，允許對公用端點進行直接存取。 如果您有公司安全性需求，必須讓所有連出流量通過集中式的公司解決方案以進行稽核和記錄，您可能無法使用此案例來滿足該需求。  

>[!TIP]
>可能的話，請使用[服務標籤](../../../virtual-network/security-overview.md#service-tags) \(部分機器翻譯\) 來減少網路安全性群組的複雜度。 

### <a name="deployment-steps"></a>部署步驟

1. 建立負載平衡器  
   1. 在 [Azure 入口網站](https://portal.azure.com)中，依序按一下 [所有資源] 和 [新增]，然後搜尋 **Load Balancer**  
   1. 按一下 [建立]  
   1. 負載平衡器名稱 **MyPublicILB**  
   1. 選取 [公用] 作為 [類型]，然後選取 [標準] 作為 [SKU]  
   1. 選取 [建立公用 IP 位址] 並指定名稱 **MyPublicILBFrondEndIP**  
   1. 選取 [區域備援] 作為 [可用性區域]  
   1. 按一下 [檢閱及建立]，然後按一下 [建立]  
2. 建立後端集區 **MyBackendPoolOfPublicILB** 並新增 VM。  
   1. 選取虛擬網路  
   1. 選取 VM 及其 IP 位址，並將其新增至後端集區  
3. [建立連出規則](../../../load-balancer/configure-load-balancer-outbound-cli.md#create-outbound-rule) \(部分機器翻譯\)。 目前並無法從 Azure 入口網站建立連出規則。 您可以使用 [Azure CLI](../../../cloud-shell/overview.md?view=azure-cli-latest) \(部分機器翻譯\) 來建立連出規則。  

   ```azurecli
    az network lb outbound-rule create --address-pool MyBackendPoolOfPublicILB --frontend-ip-configs MyPublicILBFrondEndIP --idle-timeout 30 --lb-name MyPublicILB --name MyOutBoundRules  --outbound-ports 10000 --enable-tcp-reset true --protocol All --resource-group MyResourceGroup
   ```

4. 建立網路安全性群組規則來將存取限制為特定的公用端點。 如果有現有的網路安全性群組，您可以加以調整。 下面的範例示範如何啟用對 Azure 管理 API 的存取： 
   1. 瀏覽到 [網路安全性群組]
   1. 按一下 [輸出安全性規則]
   1. 新增規則以 [拒絕] 所有針對 [網際網路] 的存取。
   1. 新增規則以 [允許] 針對 [AzureCloud] 的存取，並使其優先順序低於拒絕所有網際網路存取的規則。


   輸出安全性規則看起來會像這樣： 

   ![使用具有公用 IP 的第二個 Load Balancer 進行輸出連線](./media/high-availability-guide-standard-load-balancer/high-availability-guide-standard-load-balancer-network-security-groups.png)

   如需 Azure 網路安全性群組的詳細資訊，請參閱[安全性群組](../../../virtual-network/security-overview.md) \(部分機器翻譯\)。 

## <a name="azure-firewall-for-outbound-connections-to-internet"></a>透過 Azure 防火牆來對網際網路進行輸出連線

在不允許公用端點對 VM 之輸入連線能力的情況下，達成對公用端點之輸出連線能力的另一個選項，便是使用 Azure 防火牆。 Azure 防火牆是受管理的服務，其具有內建的高可用性，且可以橫跨多個可用性區域。  
您也必須部署[使用者定義路由](../../../virtual-network/virtual-networks-udr-overview.md#custom-routes) \(部分機器翻譯\)，與 VM 和 Azure Load Balancer 部署所在的子網路建立關聯，並指向 Azure 防火牆，才能透過 Azure 防火牆路由流量。  
如需如何部署 Azure 防火牆的詳細資料，請參閱[部署和設定 Azure 防火牆](../../../firewall/tutorial-firewall-deploy-portal.md)。  

架構如下所示：

![使用 Azure 防火牆進行輸出連線](./media/high-availability-guide-standard-load-balancer/high-availability-guide-standard-load-balancer-firewall.png)

### <a name="important-considerations"></a>重要考量︰

- Azure 防火牆是雲端原生服務，其具有內建的高可用性，且支援跨區域部署。
- 需要額外的子網路，且該子網路必須命名為 AzureFirewallSubnet。 
- 在下列情況下傳輸大型資料集時，其可能不是符合成本效益的解決方案：從 SAP VM 所在的虛擬網路以輸出方式進行傳輸、傳輸至位於另一個虛擬網路中的 VM，或是傳輸至公用端點。 其中一個範例是跨虛擬網路複製大型備份。 如需詳細資料，請參閱《Azure 防火牆定價》。  
- 如果公司防火牆解決方案不是 Azure 防火牆，且您具有需要讓所有連出流量通過集中式公司解決方案的安全性需求，則此解決方案可能不實際。  

>[!TIP]
>可能的話，請使用[服務標籤](../../../virtual-network/security-overview.md#service-tags) \(部分機器翻譯\) 來減少 Azure 防火牆規則的複雜度。  

### <a name="deployment-steps"></a>部署步驟

1. 部署步驟假設您已經針對您的 VM 定義虛擬網路和子網路。  
2. 在 VM 和 Standard Load Balancer 部署所在的相同虛擬網路中，建立子網路 **AzureFirewallSubnet**。  
   1. 在 Azure 入口網站中，瀏覽到該虛擬網路：按一下 [所有資源]，搜尋該虛擬網路，按一下該虛擬網路，選取子網路。  
   1. 按一下 [加入子網路]。 輸入 **AzureFirewallSubnet** 作為名稱。 輸入適當的 [位址範圍]。 儲存。  
3. 建立 Azure 防火牆。  
   1. 在 Azure 入口網站中，選取 [所有資源]，依序按一下 [新增]，[防火牆]，[建立]。 選取資源群組 (請選取和虛擬網路所在相同的資源群組)。  
   1. 為 Azure 防火牆資源輸入名稱。 例如 **MyAzureFirewall**。  
   1. 選取 [區域]，然後至少選取兩個 [可用性區域]，同時符合您 VM 部署所在的可用性區域。  
   1. 選取您的 [虛擬網路]，其為 SAP VM 和 Azure Standard Load Balancer 部署所在的位置。  
   1. 公用 IP 位址：按一下 [建立] 並輸入名稱。 例如 **MyFirewallPublicIP**。  
4. 建立 Azure 防火牆規則來允許對指定公用端點的連出連線能力。 下面的範例示範如何允許對 Azure 管理 API 公用端點的存取。  
   1. 依序選取 [規則] 和 [網路規則集合]，然後按一下 [新增網路規則集合]。  
   1. 名稱：**MyOutboundRule**，輸入 [優先順序]，選取 [允許] 作為 [動作]。  
   1. 服務：名稱 **ToAzureAPI**。  通訊協定：選取 [任何]。 來源位址：輸入 VM 和 Standard Load Balancer 部署所在之子網路的範圍，例如：**11.97.0.0/24**。 目的地連接埠：輸入 <b>*</b>。  
   1. 儲存
   1. 在您仍位於 Azure 防火牆的情況下，選取 [概觀]。 記下 Azure 防火牆的私人 IP 位址。  
5. 針對 Azure 防火牆建立路由  
   1. 在 Azure 入口網站中，選取 [所有資源]，然後依序按一下 [新增]，[路由表]，[建立]。  
   1. 輸入 MyRouteTable 作為 [名稱]，選取 [訂用帳戶]、[資源群組] 及 [位置] (使其符合您虛擬網路和防火牆的位置)。  
   1. 儲存  

   防火牆規則看起來會像這樣：![使用 Azure 防火牆進行輸出連線](./media/high-availability-guide-standard-load-balancer/high-availability-guide-standard-load-balancer-firewall-rule.png)

6. 從您 VM 的子網路到 **MyAzureFirewall** 的私人 IP 建立使用者定義路由。
   1. 在您仍位於路由表的情況下，按一下 [路由]。 選取 [新增]。 
   1. 路由名稱：ToMyAzureFirewall，位址首碼：**0.0.0.0/0**。 下一個躍點類型：選取 [虛擬設備]。 下一個躍點位址：輸入您所設定之防火牆的私人 IP 位址：**11.97.1.4**。  
   1. 儲存

## <a name="using-proxy-for-pacemaker-calls-to-azure-management-api"></a>使用 Proxy 來針對 Azure 管理 API 進行 Pacemaker 呼叫

您可以使用 Proxy 來允許對 Azure 管理 API 公用端點進行 Pacemaker 呼叫。  

### <a name="important-considerations"></a>重要考量︰

  - 如果已經存在公司 Proxy，您可以透過該 Proxy 將輸出呼叫路由到公用端點。 對公用端點的輸出呼叫將會通過公司控制點。  
  - 確定 Proxy 設定會允許對 Azure 管理 API 的輸出連線能力：`https://management.azure.com` 和 `https://login.microsoftonline.com`  
  - 確定存在從 VM 到 Proxy 的路由  
  - Proxy 只會處理 HTTP/HTTPS 呼叫。 如果有透過不同通訊協定 (例如 RFC) 進行對公用端點之輸出呼叫的其他需求，便會需要其他解決方案  
  - Proxy 解決方案必須具有高可用性，以避免 Pacemaker 叢集中的不穩定性  
  - 視 Proxy 的位置而定，其可能會在從 Azure 柵欄代理程式到 Azure 管理 API 的呼叫中產生額外延遲。 如果您的公司 Proxy 仍為內部部署，而您的 Pacemaker 叢集位於 Azure 中，請測量延遲並考慮此解決方案是否適合您  
  - 如果尚未存在具有高可用性的公司 Proxy，我們並不建議使用此選項，因為這會為客戶產生額外成本及複雜度。 不過，如果您決定部署額外的 Proxy 解決方案，以允許從 Pacemaker 到 Azure 管理公用 API 的輸出連線能力，請確保 Proxy 具有高可用性，且從 VM 到 Proxy 的延遲很低。  

### <a name="pacemaker-configuration-with-proxy"></a>使用 Proxy 的 Pacemaker 設定 

業界有許多不同的 Proxy 選項可供使用。 Proxy 部署的逐步指示並未涵蓋在此文件的範圍內。 在下面的範例中，我們假設您的 Proxy 能回應 **MyProxyService**，而且正在接聽 **MyProxyPort** 連接埠。  
若要讓 Pacemaker 與 Azure 管理 API 通訊，請在所有叢集節點上執行下列步驟：  

1. 編輯 Pacemaker 設定檔 /etc/sysconfig/pacemaker，然後新增下列行 (所有叢集節點)：

   ```console
   sudo vi /etc/sysconfig/pacemaker
   # Add the following lines
   http_proxy=http://MyProxyService:MyProxyPort
   https_proxy=http://MyProxyService:MyProxyPort
   ```

2. 在**所有**叢集節點上重新啟動 Pacemaker 服務。  
  - SUSE
 
     ```console
     # Place the cluster in maintenance mode
     sudo crm configure property maintenance-mode=true
     #Restart on all nodes
     sudo systemctl restart pacemaker
     # Take the cluster out of maintenance mode
     sudo crm configure property maintenance-mode=true
     ```

  - Red Hat  

     ```console
     # Place the cluster in maintenance mode
     sudo pcs property set maintenance-mode=true
     #Restart on all nodes
     sudo systemctl restart pacemaker
     # Take the cluster out of maintenance mode
     sudo pcs property set maintenance-mode=false
     ```

## <a name="other-solutions"></a>其他解決方案

如果輸出流量是透過協力廠商防火牆路由傳送：

- 如果使用 Azure 隔離代理程式，請確定防火牆設定允許 Azure 管理 API 的輸出連線能力： `https://management.azure.com` 和`https://login.microsoftonline.com`   
- 如果使用 SUSE 的 Azure 公用雲端更新基礎結構來套用更新和修補程式，請參閱[Azure 公用雲端更新基礎結構 101](https://suse.com/c/azure-public-cloud-update-infrastructure-101/)

## <a name="next-steps"></a>後續步驟

* [了解如何在 Azure 中於 SUSE 上設定 Pacemaker](./high-availability-guide-suse-pacemaker.md) \(部分機器翻譯\)
* [了解如何在 Azure 中於 Red Hat 上設定 Pacemaker](./high-availability-guide-rhel-pacemaker.md) \(部分機器翻譯\)
