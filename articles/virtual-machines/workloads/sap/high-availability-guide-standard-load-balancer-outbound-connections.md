---
title: SAP HA 方案中為 Azure VM&標準 ILB 的公共終結點連接
description: 在 SAP 高可用性方案中使用 Azure 標準負載等化器的虛擬機器的公共終結點連接
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
ms.date: 02/07/2020
ms.author: radeltch
ms.openlocfilehash: 4fd01764c183098a8bd78d502eea7ab173fa22cc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80293904"
---
# <a name="public-endpoint-connectivity-for-virtual-machines-using-azure-standard-load-balancer-in-sap-high-availability-scenarios"></a>在 SAP 高可用性方案中使用 Azure 標準負載等化器的虛擬機器的公共終結點連接

本文的範圍是描述配置，這些配置將啟用到公共終結點的出站連接。 配置主要在高可用性的情況下與起搏器為SUSE / RHEL。  

如果在高可用性解決方案中使用起搏器與 Azure 圍欄代理，則 VM 必須具有與 Azure 管理 API 的出站連接。  
本文提供了幾個選項，使您能夠選擇最適合您的方案的選項。  

## <a name="overview"></a>總覽

通過群集實現 SAP 解決方案的高可用性時，必要的元件之一是[Azure 負載等化器](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview)。 Azure 提供兩個負載等化器 SKU：標準和基本負載。

標準 Azure 負載等化器具有基本負載等化器的一些優勢。 例如，它跨 Azure 可用性區域工作，它具有更好的監視和日誌記錄功能，以便更輕鬆地進行故障排除，減少延遲。 "HA 埠"功能涵蓋所有埠，即不再需要列出所有單個埠。  

Azure 負載等化器的基本 SKU 和標準 SKU 之間存在一些重要區別。 其中之一是處理到公共終點的出站流量。 有關完全基本與標準 SKU 負載等化器的比較，請參閱[負載等化器 SKU 比較](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview)。  
 
當沒有公共 IP 位址的 VM 放置在內部（無公共 IP 位址）標準 Azure 負載等化器的後端池中時，除非執行其他配置，否則不會與公共終結點進行出站連接。  

如果為 VM 分配了公共 IP 位址，或者 VM 位於具有公共 IP 位址的負載等化器的後端池中，則 VM 將具有與公共終結點的出站連接。  

SAP 系統通常包含敏感的業務資料。 託管 SAP 系統的 VM 很少接受具有公共 IP 位址。 同時，在某些情況下，需要從 VM 到公共終結點的出站連接。  

需要訪問 Azure 公共終結點的方案示例包括：  
- 在起搏器群集中使用 Azure 圍欄代理作為隔離機制
- Azure 備份
- Azure Site Recovery  
- 使用公共存儲庫修補作業系統
- SAP 應用程式資料流程可能需要與公共端點進行出站連接

如果您的 SAP 部署不需要與公共終結點的出站連接，則不需要實現其他配置。 假定也不需要從公共終結點進行入站連接，因此為高可用性方案創建內部標準 SKU Azure 負載等化器就足夠了。  

> [!Note]
> 當沒有公共 IP 位址的 VM 放置在內部（無公共 IP 位址）標準 Azure 負載等化器的後端池中時，將沒有出站互聯網連接，除非執行其他配置以允許路由到公共終結點。  
>如果 VM 具有公共 IP 位址，或者已在具有公共 IP 位址的 Azure 負載等化器的後端池中，則 VM 已具有與公共終結點的出站連接。


請先閱讀以下論文：

* Azure 標準負載等化器
  * [Azure 標準負載等化器概述](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview)- Azure 標準負載等化器的全面概述、重要原則、概念和教程 
  * [Azure 中的出站連接](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#scenarios)- 有關如何在 Azure 中實現出站連接的方案
  * [負載等化器出站規則](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-rules-overview)- 解釋負載等化器出站規則的概念以及如何創建出站規則
* Azure 防火牆
  * [Azure 防火牆概述](https://docs.microsoft.com/azure/firewall/overview)- Azure 防火牆概述
  * [教程：部署和配置 Azure 防火牆](https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal)- 有關如何通過 Azure 門戶配置 Azure 防火牆的說明
* [虛擬網路 - 使用者定義的規則](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#user-defined)- Azure 路由概念和規則  
* [安全性群組服務標記](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags)- 如何使用服務標記簡化網路安全性群組和防火牆配置

## <a name="additional-external-azure-standard-load-balancer-for-outbound-connections-to-internet"></a>用於向外站連接到 Internet 的其他外部 Azure 標準負載等化器

實現與公共端點的出站連接（不允許從公共端點與 VM 的入站連接）的一個選項是創建具有公共 IP 位址的第二個負載等化器，將 VM 添加到第二個負載等化器的後端池，並僅定義[出站規則](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-rules-overview)。  
使用[網路安全性群組](https://docs.microsoft.com/azure/virtual-network/security-overview)控制公共終結點，這些終結點可從 VM 的出站呼叫訪問。  
有關詳細資訊，請參閱文檔[出站連接](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#scenarios)中的方案 2。  
配置如下所示：  

![使用網路安全性群組控制與公共端點的連接](./media/high-availability-guide-standard-load-balancer/high-availability-guide-standard-load-balancer-public.png)

### <a name="important-considerations"></a>重要考量︰

- 您可以為同一子網中的多個 VM 使用一個額外的公共負載等化器來實現與公共端點的出站連接並優化成本  
- 使用[網路安全性群組](https://docs.microsoft.com/azure/virtual-network/security-overview)控制可從 VM 訪問哪些公共終結點。 您可以將網路安全性群組分配給子網或每個 VM。 在可能的情況下，使用[服務標記](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags)來降低安全規則的複雜性。  
- 具有公共 IP 位址和出站規則的 Azure 標準負載等化器允許直接存取公共終結點。 如果您有通過集中式公司解決方案通過所有出站流量進行審核和日誌記錄的公司安全要求，則可能無法通過此方案滿足該要求。  

>[!TIP]
>在可能的情況下，使用[服務標記](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags)來降低網路安全性群組的複雜性。 

### <a name="deployment-steps"></a>部署步驟

1. 建立負載平衡器  
   1. 在[Azure 門戶](https://portal.azure.com)中，按一下"所有資源"，添加，然後搜索**負載等化器**  
   1. 按一下 **"創建"** 
   1. 負載等化器名稱**MyPublicILB**  
   1. 選擇 **"公共**"作為類型，**標準**為 SKU  
   1. 選擇 **"創建公共 IP 位址**"，並將名稱指定為 **"MyPublicILBFroendIP"**  
   1. 選擇 **"區域冗余**"作為可用性區域  
   1. 按一下"審閱並創建"，然後按一下"創建"  
2. 創建後端池**MyBackend 公共ILB池**並添加 VM。  
   1. 選擇虛擬網路  
   1. 選擇 VM 及其 IP 位址並將其添加到後端池  
3. [創建出站規則](https://docs.microsoft.com/azure/load-balancer/configure-load-balancer-outbound-cli#create-outbound-rule)。 目前無法從 Azure 門戶創建出站規則。 您可以使用[Azure CLI](https://docs.microsoft.com/azure/cloud-shell/overview?view=azure-cli-latest)創建出站規則。  

   ```azurecli
    az network lb outbound-rule create --address-pool MyBackendPoolOfPublicILB --frontend-ip-configs MyPublicILBFrondEndIP --idle-timeout 30 --lb-name MyPublicILB --name MyOutBoundRules  --outbound-ports 10000 --enable-tcp-reset true --protocol All --resource-group MyResourceGroup
   ```

4. 創建網路安全性群組規則以限制對特定公共終結點的訪問。 如果存在現有的網路安全性群組，則可以調整它。 下面的示例演示如何啟用對 Azure 管理 API 的訪問： 
   1. 導航到網路安全性群組
   1. 按一下出站安全規則
   1. 添加規則以**拒絕**所有出站訪問**互聯網**。
   1. 添加**允許**訪問**AzureCloud**的規則，優先順序低於拒絕所有 Internet 訪問的優先順序。


   出站安全規則如下所示： 

   ![與具有公共 IP 的第二負載等化器的出站連接](./media/high-availability-guide-standard-load-balancer/high-availability-guide-standard-load-balancer-network-security-groups.png)

   有關 Azure 網路安全性群組的詳細資訊，請參閱[安全性群組](https://docs.microsoft.com/azure/virtual-network/security-overview)。 

## <a name="azure-firewall-for-outbound-connections-to-internet"></a>用於向外站連接到 Internet 的 Azure 防火牆

實現與公共終結點的出站連接（不允許從公共終結點與 VM 的入站連接）的另一個選項是使用 Azure 防火牆。 Azure 防火牆是一個託管服務，具有內置的高可用性，它可以跨越多個可用性區域。  
您還需要部署[使用者定義的路由](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#custom-routes)（與部署 VM 和 Azure 負載等化器的子閘道聯的子網，指向 Azure 防火牆），以便通過 Azure 防火牆路由流量。  
有關如何部署 Azure 防火牆的詳細資訊，請參閱[部署和配置 Azure 防火牆](https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal)。  

架構如下所示：

![與 Azure 防火牆的出站連接](./media/high-availability-guide-standard-load-balancer/high-availability-guide-standard-load-balancer-firewall.png)

### <a name="important-considerations"></a>重要考量︰

- Azure 防火牆是雲原生服務，具有內置的高可用性，並且支援地區部署。
- 需要必須命名為 Azure 防火牆子網的其他子網。 
- 如果將 SAP VM 所在的虛擬網路的大型資料集出站轉移到另一個虛擬網路中的 VM 或公共端點，則可能不具有成本效益的解決方案。 例如，跨虛擬網路複製大型備份。 有關詳細資訊，請參閱 Azure 防火牆定價。  
- 如果公司防火牆解決方案不是 Azure 防火牆，並且具有通過集中式公司解決方案通過所有出站流量的安全要求，則此解決方案可能不實用。  

>[!TIP]
>在可能的情況下，使用[服務標記](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags)來降低 Azure 防火牆規則的複雜性。  

### <a name="deployment-steps"></a>部署步驟

1. 部署步驟假定您已經為 VM 定義了虛擬網路和子網。  
2. 在同一虛擬網路中創建子**網 Azure 防火牆子網**，其中部署了 VMS 和標準負載等化器。  
   1. 在 Azure 門戶中，導航到虛擬網路：按一下所有資源，搜索虛擬網路，按一下虛擬網路，選擇子網。  
   1. 按一下"添加子網"。 輸入**Azure 防火牆子網**作為名稱。 輸入適當的位址範圍。 儲存。  
3. 創建 Azure 防火牆。  
   1. 在 Azure 門戶中選擇"所有資源"，按一下"添加、防火牆、創建"。 選擇資源組（選擇虛擬網路所在的同一資源組）。  
   1. 輸入 Azure 防火牆資源的名稱。 例如 **，MyAzure 防火牆**。  
   1. 選擇"區域"並選擇至少兩個可用區域，與部署 VM 的可用性區域保持一致。  
   1. 選擇虛擬網路，部署 SAP VM 和 Azure 標準負載等化器。  
   1. 公共 IP 位址：按一下"創建並輸入名稱"。 例如 **，我的防火牆公共IP**。  
4. 創建 Azure 防火牆規則以允許出站連接到指定的公共終結點。 該示例演示如何允許訪問 Azure 管理 API 公共終結點。  
   1. 選擇規則，網路規則集合，然後按一下"添加網路規則集合"。  
   1. 名稱：**我的出站規則**，輸入優先順序，選擇操作**允許**。  
   1. 服務：名稱**到 AzureAPI**。  協定：選擇**任意**。 源位址：輸入子網的範圍，其中例如，VM 和標準負載等化器的部署範圍為 **：11.97.0.0/24**。 目標埠：輸入<b>*</b>。  
   1. 儲存
   1. 當您仍位於 Azure 防火牆上時，請選擇"概述"。 記下 Azure 防火牆的私人 IP 位址。  
5. 創建 Azure 防火牆路由  
   1. 在 Azure 門戶中選擇"所有資源"，然後按一下"添加、路由表、創建"。  
   1. 輸入名稱 MyRouteTable，選擇訂閱、資源組和位置（匹配虛擬網路和防火牆的位置）。  
   1. 儲存  

   防火牆規則如下所示：![與 Azure 防火牆的出站連接](./media/high-availability-guide-standard-load-balancer/high-availability-guide-standard-load-balancer-firewall-rule.png)

6. 從 VM 的子網創建使用者定義的路由到**MyAzure 防火牆**的專用 IP。
   1. 當您定位在路線表上時，按一下"路線"。 選取 [新增]。 
   1. 路由名稱：到 MyAzure 防火牆，位址首碼： **0.0.0.0/0**。 下一個躍點類型：選擇虛擬裝置。 下一個躍點位址：輸入您配置的防火牆的私人 IP 位址 **：11.97.1.4**。  
   1. 儲存

## <a name="using-proxy-for-pacemaker-calls-to-azure-management-api"></a>使用代表對起搏器調用 Azure 管理 API

您可以使用代理來允許起搏器調用 Azure 管理 API 公共終結點。  

### <a name="important-considerations"></a>重要考量︰

  - 如果已有公司代理，則可以通過該代理將出站呼叫路由到公共終結點。 對公共終結點的出站呼叫將經過公司控制點。  
  - 確保代理配置允許向外站連接到 Azure 管理 API：`https://management.azure.com`  
  - 確保從 VM 到代理的路由  
  - 代理將僅處理 HTTP/HTTPS 調用。 如果需要通過不同的協定（如 RFC）對公共端點進行出站調用，則需要替代解決方案  
  - 代理解決方案必須高度可用，以避免起搏器群集中的不穩定性  
  - 根據代理的位置，它可能會在從 Azure 圍欄代理到 Azure 管理 API 的調用中引入額外的延遲。 如果公司代理仍在內部，而 Pacemaker 群集位於 Azure 中，請測量延遲並考慮，如果此解決方案適合您  
  - 如果還沒有高度可用的公司代理，我們不推薦此選項，因為客戶將產生額外的成本和複雜性。 但是，如果您決定部署其他代理解決方案，以便允許從 Pacemaker 到 Azure 管理公共 API 的出站連接，請確保代理高度可用，並且從 VM 到代理的延遲較低。  

### <a name="pacemaker-configuration-with-proxy"></a>使用代理的起搏器配置 

行業中有多種不同的代理選項。 代理部署的分步說明不在本文檔的範圍之內。 在下面的示例中，我們假定您的代理正在回應**MyProxyService 服務**並偵聽 Port **MyProxyPort**。  
要允許心臟起搏器與 Azure 管理 API 通信，請對所有叢集節點執行以下步驟：  

1. 編輯起搏器設定檔 /etc/系統組態/起搏器並添加以下行（所有叢集節點）：

   ```console
   sudo vi /etc/sysconfig/pacemaker
   # Add the following lines
   http_proxy=http://MyProxyService:MyProxyPort
   https_proxy=http://MyProxyService:MyProxyPort
   ```

2. 在所有**叢集節點**上重新開機心臟起搏器服務。  
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

## <a name="next-steps"></a>後續步驟

* [瞭解如何在 Azure 中的 SUSE 上配置起搏器](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker)
* [瞭解如何在 Azure 中的紅帽上配置起搏器](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-pacemaker)
