---
title: SLES 多 SID 指南上的 SAP NetWeaver 的 Azure VM 高可用性 |微軟文檔
description: 用於 SAP 應用程式的 SUSE Linux 企業伺服器上的 SAP NetWeaver 多 SID 高可用性指南
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 5e514964-c907-4324-b659-16dd825f6f87
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/26/2020
ms.author: radeltch
ms.openlocfilehash: 793851780e1154b6b6a21c88ea8cae063a277790
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "80350065"
---
# <a name="high-availability-for-sap-netweaver-on-azure-vms-on-suse-linux-enterprise-server-for-sap-applications-multi-sid-guide"></a>SUSE Linux 企業伺服器上的 Azure VM 上的 SAP NetWeaver 的高可用性，適用于 SAP 應用程式多 SID 指南

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md

[anf-sap-applications-azure]:https://www.netapp.com/us/media/tr-4746.pdf

[2205917]:https://launchpad.support.sap.com/#/notes/2205917
[1944799]:https://launchpad.support.sap.com/#/notes/1944799
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[1984787]:https://launchpad.support.sap.com/#/notes/1984787
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[1410736]:https://launchpad.support.sap.com/#/notes/1410736

[sap-swcenter]:https://support.sap.com/en/my-support/software-downloads.html

[suse-ha-guide]:https://www.suse.com/products/sles-for-sap/resource-library/sap-best-practices/
[suse-drbd-guide]:https://www.suse.com/documentation/sle-ha-12/singlehtml/book_sleha_techguides/book_sleha_techguides.html
[suse-ha-12sp3-relnotes]:https://www.suse.com/releasenotes/x86_64/SLE-HA/12-SP3/

[template-multisid-xscs]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs-md%2Fazuredeploy.json
[template-converged]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-converged-md%2Fazuredeploy.json
[template-file-server]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-file-server-md%2Fazuredeploy.json

[sap-hana-ha]:sap-hana-high-availability.md
[nfs-ha]:high-availability-guide-suse-nfs.md

本文介紹如何在 Azure VM 上的兩個節點群集中部署多個 SAP NetWeaver 或 S4HANA 高可用性系統（即多 SID），以及用於 SAP 應用程式的 SUSE Linux 企業伺服器。  

在示例配置中，安裝命令等三個 SAP NetWeaver 7.50 系統部署在一個、兩個節點的高可用性群集中。 SAP 系統 S 是：
* **NW1：ASCS**實例編號**00**和虛擬主機名稱**msnw1ascs;** ERS 實例編號**02**和虛擬主機名稱**msnw1ers**。  
* **NW2：ASCS**實例編號**10**和虛擬主機名稱**msnw2ascs;** ERS實例編號**12**和虛擬主機名稱**msnw2ers。**  
* **NW3**：ASCS 實例編號**20**和虛擬主機名稱**msnw3ascs;** ERS實例編號**22**和虛擬主機名稱**msnw3ers。**  

本文不介紹資料庫層和 SAP NFS 共用的部署。 在本文中的示例中，我們使用虛擬名稱 nw2-nfs 進行 NW2 NFS 共用，nw3-nfs 用於 NW3 NFS 共用，前提是已部署 NFS 群集。  

在開始之前，請先參考以下 SAP 說明和論文：

* SAP Note [1928533][1928533]，其中包含：
  * SAP 軟體部署支援的 Azure VM 大小清單
  * Azure VM 大小的重要容量資訊
  * 支援的 SAP 軟體，以及作業系統 (OS) 與資料庫組合
  * Microsoft Azure 上 Windows 和 Linux 所需的 SAP 核心版本

* SAP Note [2015553][2015553] 列出 Azure 中 SAP 支援的 SAP 軟體部署先決條件。
* SAP Note [2205917][2205917] 包含適用於 SUSE Linux Enterprise Server for SAP Applications 的建議 OS 設定
* SAP Note [1944799][1944799] 包含適用於 SUSE Linux Enterprise Server for SAP Applications 的 SAP HANA 指導方針
* SAP Note [2178632][2178632] 包含在 Azure 中針對 SAP 回報的所有監視計量詳細資訊。
* SAP Note [2191498][2191498] 包含 Azure 中 Linux 所需的 SAP Host Agent 版本。
* SAP Note [2243692][2243692] 包含 Azure 中 Linux 上的 SAP 授權相關資訊。
* SAP Note [1984787][1984787] 包含 SUSE LINUX Enterprise Server 12 的一般資訊。
* SAP Note [1999351][1999351] 包含 Azure Enhanced Monitoring Extension for SAP 的其他疑難排解資訊。
* [SAP Community WIKI](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) 包含 Linux 所需的所有 SAP Note。
* [適用於 SAP on Linux 的 Azure 虛擬機器規劃和實作][planning-guide]
* [在 Linux 上為 SAP 進行 Azure 虛擬機器部署][deployment-guide]
* [適用於 SAP on Linux 的 Azure 虛擬機器 DBMS 部署][dbms-guide]
* [SUSE SAP HA 最佳做法指南][suse-ha-guide] 此指南包含設定內部部署 Netweaver HA 和 SAP HANA 系統複寫的所有必要資訊。 請使用這些指南作為一般基準。 它們提供更詳細的資訊。
* [SUSE 高可用性擴充 12 SP3 版本資訊][suse-ha-12sp3-relnotes]
* [SLES 12 和 SLES 15 的 SUSE 多 SID 群集指南](https://documentation.suse.com/sbp/all/html/SBP-SAP-MULTI-SID/index.html)
* [使用 Azure NetApp 檔在 Microsoft Azure 上使用 NetApp SAP 應用程式][anf-sap-applications-azure]
## <a name="overview"></a>總覽

參與群集的虛擬機器必須調整大小才能運行所有資源，以防發生容錯移轉。 在多 SID 高可用性群集中，每個 SAP SID 可以獨立地進行容錯移轉。  如果使用 SBD 遮罩，SBD 設備可以在多個群集之間共用。  

為了實現高可用性，SAP NetWeaver 需要高可用性 NFS 股票。 在此示例中，我們假設 SAP NFS 共用託管在高可用[NFS 檔案伺服器上](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs)，該伺服器可供多個 SAP 系統使用。 或者共用部署在 Azure [NetApp 檔 NFS 卷](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-volumes)上。  

![SAP NetWeaver 高可用性概觀](./media/high-availability-guide-suse/ha-suse-multi-sid.png)

> [!IMPORTANT]
> 在 Azure VM 中，支援將 SAP ASCS/ERS 與 SUSE Linux 作為客體作業系統的 SAP ASCS/ERS 進行多 SID 群集，但僅限於同一群集上的**五**個 SAP SID。 每個新的 SID 都會增加複雜性。 不支援將 SAP 佇列複製伺服器 1 和同一群集上的佇列複製伺服器 2 混合**在**一起。 多 SID 群集描述在一個起搏器群集中安裝多個具有不同 SID 的 SAP ASCS/ERS 實例。 目前，僅支援 ASCS/ERS 的多 SID 群集。  

> [!TIP]
> SAP ASCS/ERS 的多 SID 群集是一種具有更高複雜性的解決方案。 實施工作更為複雜。 在執行維護活動（如作業系統修補）時，它還涉及更高的管理工作量。 在開始實際實現之前，請花些時間仔細規劃部署以及所有相關元件，如 VM、NFS 裝載、VIP、負載等化器配置等。  

NFS 伺服器、SAP NetWeaver ASCS、SAP NetWeaver SCS、SAP NetWeaver ERS 和 SAP HANA 資料庫會使用虛擬主機名稱和虛擬 IP 位址。 在 Azure 上必須有負載平衡器才能使用虛擬 IP 位址。 我們建議使用[標準負載等化器](https://docs.microsoft.com/azure/load-balancer/quickstart-load-balancer-standard-public-portal)。  

下面的清單顯示了此多 SID 群集示例（具有三個 SAP 系統的）A）SCS 和 ERS 負載等化器的配置。 對於每個 ASCS 和 ERS 實例，您將需要單獨的前端 IP、運行狀況探測器和負載平衡規則。 將所有作為 ASCS/ASCS 群集一部分的 VM 分配給一個後端池。  

### <a name="ascs"></a>(A)SCS

* 前端組態
  * NW1 的 IP 位址： 10.3.1.14
  * NW2 的 IP 位址： 10.3.1.16
  * NW3 的 IP 位址： 10.3.1.13
* 探頭埠
  * 埠 620<strong>&lt;&gt;nr</strong>，因此用於 NW1、NW2 和 NW3 探頭埠 620**00、620****10**和 620**20**
* 負載平衡規則 - 
* 為每個實例創建一個實例，即 NW1/ASCS、NW2/ASCS 和 NW3/ASCS。
  * 如果使用標準負載等化器，請選擇**HA 埠**
  * 如果使用基本負載平衡器，請為以下埠創建負載平衡規則
    * 32<strong> &lt; &gt; nr</strong> TCP
    * 36<strong> &lt; &gt; nr</strong> TCP
    * 39<strong> &lt; &gt; nr</strong> TCP
    * 81<strong> &lt; &gt; nr</strong> TCP
    * 5<strong>&lt;&gt;nr</strong>13 TCP
    * 5<strong>&lt;&gt;nr</strong>14 TCP
    * 5<strong>&lt;&gt;nr</strong>16 TCP

### <a name="ers"></a>ERS

* 前端組態
  * NW1 10.3.1.15 的 IP 位址
  * NW2 10.3.1.17 的 IP 位址
  * NW3 10.3.1.19 的 IP 位址
* 探查連接埠
  * 埠 621<strong>&lt;&gt;nr</strong>，因此用於 NW1、NW2 和 N# 探頭埠 621**02、621****12**和 621**22**
* 負載平衡規則 - 為每個實例創建一個實例，即 NW1/ERS、NW2/ERS 和 NW3/ERS。
  * 如果使用標準負載等化器，請選擇**HA 埠**
  * 如果使用基本負載平衡器，請為以下埠創建負載平衡規則
    * 32<strong> &lt; &gt; nr</strong> TCP
    * 33<strong> &lt; &gt; nr</strong> TCP
    * 5<strong>&lt;&gt;nr</strong>13 TCP
    * 5<strong>&lt;&gt;nr</strong>14 TCP
    * 5<strong>&lt;&gt;nr</strong>16 TCP

* 後端組態
  * 連線到應該屬於 (A)SCS/ERS 叢集一部分之所有虛擬機器的主要網路介面


> [!Note]
> 當沒有公共 IP 位址的 VM 放置在內部（無公共 IP 位址）標準 Azure 負載等化器的後端池中時，將沒有出站互聯網連接，除非執行其他配置以允許路由到公共終結點。 有關如何實現出站連接的詳細資訊，請參閱[在 SAP 高可用性方案中使用 Azure 標準負載等化器的虛擬機器的公共終結點連接](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-standard-load-balancer-outbound-connections)。  

> [!IMPORTANT]
> 請勿在位於 Azure 負載等化器後面的 Azure VM 上啟用 TCP 時間戳記。 啟用 TCP 時間戳記將導致運行狀況探測失敗。 將參數**net.ipv4.tcp_timestamps**設置為**0**。 有關詳細資訊，請參閱[負載等化器運行狀況探測器](https://docs.microsoft.com/azure/load-balancer/load-balancer-custom-probe-overview)。

## <a name="sap-nfs-shares"></a>SAP NFS 股票

SAP NetWeaver 需要傳輸、設定檔目錄等的共用存儲。 對於高可用的 SAP 系統，擁有高可用 NFS 共用非常重要。 您需要決定 SAP NFS 共用的體系結構。 一個選項是在[SUSE Linux 企業伺服器上的 Azure VM 上構建高可用性 NFS 群集][nfs-ha]，該群集可以在多個 SAP 系統之間共用。 

另一個選項是在[Azure NetApp 檔 NFS 卷](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-volumes)上部署共用。  使用 Azure NetApp 檔，SAP NFS 共用將獲得內置的高可用性。

## <a name="deploy-the-first-sap-system-in-the-cluster"></a>部署群集中的第一個 SAP 系統

現在，您已經決定了 SAP NFS 共用的體系結構，按照相應的文檔部署群集中的第一個 SAP 系統。

* 如果使用高可用性 NFS 伺服器，請按照 SAP 應用程式在[SUSE Linux 企業伺服器上的 Azure VM 上的 SAP NetWeaver 的高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse)操作。  
* 如果使用 Azure NetApp 檔 NFS 卷，請按照[SUSE Linux 企業伺服器上的 Azure VM 上的 SAP NetWeaver 的高可用性，以及用於 SAP 應用程式的 Azure NetApp 檔](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files)

上面列出的文檔將指導您完成準備必要基礎結構、構建群集、準備作業系統以運行 SAP 應用程式的步驟。  

> [!TIP]
> 在部署第一個系統後，始終測試群集的容錯移轉功能，然後再向群集添加其他 SAP S。" 這樣，在將其他 SAP 系統的複雜性添加到群集之前，您就知道群集功能有效。   

## <a name="deploy-additional-sap-systems-in-the-cluster"></a>在群集中部署其他 SAP 系統

在此示例中，我們假設系統**NW1**已部署在群集中。 我們將演示如何在群集 SAP 系統**NW2**和**NW3**中部署。 

以下項預綴為 **[A]** - 適用于所有節點 **[1]** - 僅適用于節點 1 或 **[2]** - 僅適用于節點 2。

### <a name="prerequisites"></a>Prerequisites 

> [!IMPORTANT]
> 在按照在群集中部署其他 SAP 系統的說明之前，請按照說明在群集中部署第一個 SAP 系統，因為有些步驟僅在第一個系統部署期間是必需的。  

本文檔假定：
* 起搏器群集已配置並運行。  
* 至少有一個 SAP 系統（ASCS / ERS 實例）已在群集中部署並運行。  
* 群集容錯移轉功能已過測試。  
* 部署了所有 SAP 系統的 NFS 共用。  

### <a name="prepare-for-sap-netweaver-installation"></a>為 SAP 網織機安裝做好準備

1. 按照指示通過[Azure 門戶手動部署 Azure 負載等化器](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files#deploy-azure-load-balancer-manually-via-azure-portal)，將新部署的系統（即**NW2、NW3）** 的配置添加到現有的 Azure 負載等化器。 **NW3** 調整配置的 IP 位址、運行狀況探測埠和負載平衡規則。  

2. **[A]** 為其他 SAP 系統設置名稱解析。 您可以使用 DNS 伺服器或修改`/etc/hosts`所有節點。 此示例演示如何使用該檔`/etc/hosts`。  根據環境調整 IP 位址和主機名稱。 

    ```
    sudo vi /etc/hosts
    # IP address of the load balancer frontend configuration for NW2 ASCS
    10.3.1.16 msnw2ascs
    # IP address of the load balancer frontend configuration for NW3 ASCS
    10.3.1.13 msnw3ascs
    # IP address of the load balancer frontend configuration for NW2 ERS
    10.3.1.17 msnw2ers
    # IP address of the load balancer frontend configuration for NW3 ERS
    10.3.1.19 msnw3ers
    # IP address for virtual host name for the NFS server for NW2
    10.3.1.31 nw2-nfs
    # IP address for virtual host name for the NFS server for NW3
    10.3.1.32 nw3-nfs
   ```

3. **[A]** 為要部署到群集的其他**NW2**和**NW3** SAP 系統創建共用目錄。 

    ```
    sudo mkdir -p /sapmnt/NW2
    sudo mkdir -p /usr/sap/NW2/SYS
    sudo mkdir -p /usr/sap/NW2/ASCS10
    sudo mkdir -p /usr/sap/NW2/ERS12
    sudo mkdir -p /sapmnt/NW3
    sudo mkdir -p /usr/sap/NW3/SYS
    sudo mkdir -p /usr/sap/NW3/ASCS20
    sudo mkdir -p /usr/sap/NW3/ERS22

    
    sudo chattr +i /sapmnt/NW2
    sudo chattr +i /usr/sap/NW2/SYS
    sudo chattr +i /usr/sap/NW2/ASCS10
    sudo chattr +i /usr/sap/NW2/ERS12
    sudo chattr +i /sapmnt/NW3
    sudo chattr +i /usr/sap/NW3/SYS
    sudo chattr +i /usr/sap/NW3/ASCS20
    sudo chattr +i /usr/sap/NW3/ERS22
   ```

4. **[A]** `autofs`配置為為要部署到群集的其他 SAP 系統安裝 /sapmnt/SID 和 /usr/sap/SID/SYS 檔案系統。 在此示例中 **，NW2**和**NW3**。  

   使用檔案系統`/etc/auto.direct`更新要部署到群集的其他 SAP 系統的檔。  

   * 如果使用 NFS 檔案伺服器，請按照[此處](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse#prepare-for-sap-netweaver-installation)的說明操作
   * 如果使用 Azure NetApp 檔，請按照[此處](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files#prepare-for-sap-netweaver-installation)的說明操作 

   您需要重新開機`autofs`服務以裝載新添加的共用。  

### <a name="install-ascs--ers"></a>安裝 ASCS / ERS

1. 為要部署到群集的其他 SAP 系統的 ASCS 實例創建虛擬 IP 和運行狀況探測群集資源。 此處顯示的示例適用于使用高可用 NFS 伺服器的**NW2**和**NW3** ASCS。  

   > [!IMPORTANT]
   > 最近的測試揭示了 Netcat 由於積壓和僅處理一個連接的限制而停止回應請求的情況。 netcat 資源停止偵聽 Azure 負載等化器請求，浮動 IP 將不可用。  
   > 對於現有的起搏器集群，我們過去曾建議用索卡特取代網貓。 目前，我們建議使用 Azure-lb 資源代理（這是包資源代理的一部分），具有以下包版本要求：
   > - 對於 SLES 12 SP4/SP5，版本必須至少為資源代理-4.3.018.a7fb5035-3.30.1。  
   > - 對於 SLES 15/15 SP1，版本必須至少為資源代理-4.3.0184.6ee15eb2-4.13.1。  
   >
   > 請注意，更改將需要短暫的停機時間。  
   > 對於現有的起搏器群集，如果配置已更改為使用[Azure 負載等化器檢測強化](https://www.suse.com/support/kb/doc/?id=7024128)中所述的 socat，則無需立即切換到 Azure-lb 資源代理。

    ```
      sudo crm configure primitive fs_NW2_ASCS Filesystem device='nw2-nfs:/NW2/ASCS' directory='/usr/sap/NW2/ASCS10' fstype='nfs4' \
       op start timeout=60s interval=0 \
       op stop timeout=60s interval=0 \
       op monitor interval=20s timeout=40s
   
      sudo crm configure primitive vip_NW2_ASCS IPaddr2 \
        params ip=10.3.1.16 cidr_netmask=24 \
        op monitor interval=10 timeout=20
   
      sudo crm configure primitive nc_NW2_ASCS azure-lb port=62010
   
      sudo crm configure group g-NW2_ASCS fs_NW2_ASCS nc_NW2_ASCS vip_NW2_ASCS \
         meta resource-stickiness=3000

      sudo crm configure primitive fs_NW3_ASCS Filesystem device='nw3-nfs:/NW3/ASCS' directory='/usr/sap/NW3/ASCS20' fstype='nfs4' \
        op start timeout=60s interval=0 \
        op stop timeout=60s interval=0 \
        op monitor interval=20s timeout=40s
   
      sudo crm configure primitive vip_NW3_ASCS IPaddr2 \
       params ip=10.3.1.13 cidr_netmask=24 \
       op monitor interval=10 timeout=20
   
      sudo crm configure primitive nc_NW3_ASCS azure-lb port=62020
   
      sudo crm configure group g-NW3_ASCS fs_NW3_ASCS nc_NW3_ASCS vip_NW3_ASCS \
        meta resource-stickiness=3000
    ```

   在創建資源時，它們可能會分配給不同的群集資源。 對它們進行分組時，它們將遷移到其中一個叢集節點。 確保群集狀態正常，並且所有資源都已啟動。 資源在哪一個節點上執行並不重要。

2. **[1]** 安裝 SAP NetWeaver ASCS  

   使用映射到 ASCS 負載等化器前端配置的 IP 位址的虛擬主機名稱，將 SAP NetWeaver ASCS 安裝為根。 例如，對於系統**NW2，** 虛擬主機名稱為 msnw2ascs，10.3.1.16 和用於負載等化器探測的實例編號，例如<b>10</b>。 <b>msnw2ascs</b> <b>10.3.1.16</b> 對於系統**NW3，** 虛擬主機名稱是<b>msnw3ascs，</b> <b>10.3.1.13</b>和用於負載等化器探測的實例編號， 例如<b>20</b>.

   您可以使用 sapinst 參數 SAPINST_REMOTE_ACCESS_USER 來允許非 root 使用者連線到 sapinst。 您可以使用參數SAPINST_USE_HOSTNAME使用虛擬主機名稱安裝 SAP。  

     ```
      sudo swpm/sapinst SAPINST_REMOTE_ACCESS_USER=sapadmin SAPINST_USE_HOSTNAME=virtual_hostname
     ```

   如果安裝未能在 /usr/sap/SID /ASCS**SID****實例*** 中創建子資料夾，請嘗試將擁有者設置為**sid**adm 和組以 SCS**實例*** 的 sapsys 進行重試。

3. **[1]** 為要部署到群集的其他 SAP 系統的 ERS 實例創建虛擬 IP 和運行狀況探測群集資源。 此處顯示的示例適用于使用高可用 NFS 伺服器的**NW2**和**NW3** ERS。 

   ```
    sudo crm configure primitive fs_NW2_ERS Filesystem device='nw2-nfs:/NW2/ASCSERS' directory='/usr/sap/NW2/ERS12' fstype='nfs4' \
      op start timeout=60s interval=0 \
      op stop timeout=60s interval=0 \
      op monitor interval=20s timeout=40s
   
    sudo crm configure primitive vip_NW2_ERS IPaddr2 \
      params ip=10.3.1.17 cidr_netmask=24 \
      op monitor interval=10 timeout=20
   
    sudo crm configure primitive nc_NW2_ERS azure-lb port=62112
   
    sudo crm configure group g-NW2_ERS fs_NW2_ERS nc_NW2_ERS vip_NW2_ERS

    sudo crm configure primitive fs_NW3_ERS Filesystem device='nw3-nfs:/NW3/ASCSERS' directory='/usr/sap/NW3/ERS22' fstype='nfs4' \
      op start timeout=60s interval=0 \
      op stop timeout=60s interval=0 \
      op monitor interval=20s timeout=40s
   
    sudo crm configure primitive vip_NW3_ERS IPaddr2 \
      params ip=10.3.1.19 cidr_netmask=24 \
      op monitor interval=10 timeout=20
   
    sudo crm configure primitive nc_NW3_ERS azure-lb port=62122
   
    sudo crm configure group g-NW3_ERS fs_NW3_ERS nc_NW3_ERS vip_NW3_ERS
   ```

   在創建資源時，可能會將它們分配給不同的叢集節點。 對它們進行分組時，它們將遷移到其中一個叢集節點。 確保群集狀態正常，並且所有資源都已啟動。  

   接下來，確保新創建的 ERS 組的資源在叢集節點上運行，與安裝同一 SAP 系統的 ASCS 實例的叢集節點相反。  例如，如果 NW2 ASCS 安裝在`slesmsscl1`上，則請確保 NW2 ERS 組`slesmsscl2`在 上運行。  您可以通過運行以下命令將 NW2 `slesmsscl2` ERS 組遷移到： 

    ```
      crm resource migrate g-NW2_ERS slesmsscl2 force
    ```

4. **[2]** 安裝 SAP NetWeaver ERS

   使用虛擬主機名稱將 SAP NetWeaver ERS 作為根安裝到 ERS 的負載等化器前端配置的 IP 位址。 例如，對於系統**NW2，** 虛擬主機名稱將是 msnw2ers，10.3.1.17 和用於負載等化器探測的實例編號，例如<b>msnw2ers</b><b>12</b>。 <b>10.3.1.17</b> 對於系統**NW3，** 虛擬主機名稱<b>msnw3ers</b> <b>，10.3.1.19</b>和用於負載等化器探測的實例編號，例如<b>22</b>。 

   您可以使用 sapinst 參數 SAPINST_REMOTE_ACCESS_USER 來允許非 root 使用者連線到 sapinst。 您可以使用參數SAPINST_USE_HOSTNAME使用虛擬主機名稱安裝 SAP。  

    ```
     sudo swpm/sapinst SAPINST_REMOTE_ACCESS_USER=sapadmin SAPINST_USE_HOSTNAME=virtual_hostname
    ```

   > [!NOTE]
   > 請使用 SWPM SP 20 PL 05 或更高版本。 較低版本無法正確設定權限，因而會讓安裝失敗。

   如果安裝未能在 /usr/sap/**NW2**/ERS**實例*** 中創建子資料夾，請嘗試將擁有者設置為**sid**adm，並將組設置為 ERS**實例*** 資料夾的 sapsys 並重試。

   如果需要將新部署的 SAP 系統的 ERS 組遷移到其他叢集節點，不要忘記刪除 ERS 組的位置約束。 可以通過運行以下命令來刪除約束（為 SAP 系統**NW2**和**NW3**提供了示例）。  

    ```
      crm resource unmigrate g-NW2_ERS
      crm resource unmigrate g-NW3_ERS
    ```

5. **[1]** 為新安裝的 SAP 系統調整 ASCS/SCS 和 ERS 實例設定檔。 下面顯示的示例用於 NW2。 您需要調整添加到群集的所有 SAP 實例的 ASCS/SCS 和 ERS 設定檔。  
 
 * ASCS/SCS 設定檔

   ```
   sudo vi /sapmnt/NW2/profile/NW2_ASCS10_msnw2ascs
   
   # Change the restart command to a start command
   #Restart_Program_01 = local $(_EN) pf=$(_PF)
   Start_Program_01 = local $(_EN) pf=$(_PF)
   
   # Add the following lines
   service/halib = $(DIR_CT_RUN)/saphascriptco.so
   service/halib_cluster_connector = /usr/bin/sap_suse_cluster_connector
   
   # Add the keep alive parameter
   enque/encni/set_so_keepalive = true
   ```

 * ERS 設定檔

   ```
   sudo vi /sapmnt/NW2/profile/NW2_ERS12_msnw2ers
   
   # Change the restart command to a start command
   #Restart_Program_00 = local $(_ER) pf=$(_PFL) NR=$(SCSID)
   Start_Program_00 = local $(_ER) pf=$(_PFL) NR=$(SCSID)
   
   # Add the following lines
   service/halib = $(DIR_CT_RUN)/saphascriptco.so
   service/halib_cluster_connector = /usr/bin/sap_suse_cluster_connector
   
   # remove Autostart from ERS profile
   # Autostart = 1
   ```

6. **[A]** 配置新部署的 SAP 系統的 SAP 使用者，在此示例中**為 NW2**和**NW3**。 

   ```
   # Add sidadm to the haclient group
   sudo usermod -aG haclient nw2adm
   sudo usermod -aG haclient nw3adm
   ```

7. 將新安裝的 SAP 系統的 ASCS 和 ERS `sapservice` SAP 服務添加到檔中。 下面所示的示例適用于 SAP 系統**NW2**和**NW3**。  

   在第二個節點中新增 ASCS 服務項目，並將 ERS 服務項目複製到第一個節點。 執行節點上每個 SAP 系統的命令，其中安裝了 SAP 系統的 ASCS 實例。  

    ```
     # Execute the following commands on slesmsscl1,assuming the NW2 ASCS instance was installed on slesmsscl1
     cat /usr/sap/sapservices | grep ASCS10 | sudo ssh slesmsscl2 "cat >>/usr/sap/sapservices"
     sudo ssh slesmsscl2 "cat /usr/sap/sapservices" | grep ERS12 | sudo tee -a /usr/sap/sapservices
     # Execute the following commands on slesmsscl2, assuming the NW3 ASCS instance was installed on slesmsscl2
     cat /usr/sap/sapservices | grep ASCS20 | sudo ssh slesmsscl1 "cat >>/usr/sap/sapservices"
     sudo ssh slesmsscl1 "cat /usr/sap/sapservices" | grep ERS22 | sudo tee -a /usr/sap/sapservices
    ```

8. **[1]** 為新安裝的 SAP 系統創建 SAP 群集資源。 

   如果使用排隊伺服器 1 體系結構 （ENSA1），請定義 SAP 系統**NW2**和**NW3**的資源，如下所示：

    ```
     sudo crm configure property maintenance-mode="true"
    
     sudo crm configure primitive rsc_sap_NW2_ASCS10 SAPInstance \
      operations \$id=rsc_sap_NW2_ASCS10-operations \
      op monitor interval=11 timeout=60 on-fail=restart \
      params InstanceName=NW2_ASCS10_msnw2ascs START_PROFILE="/sapmnt/NW2/profile/NW2_ASCS10_msnw2ascs" \
      AUTOMATIC_RECOVER=false \
      meta resource-stickiness=5000 failure-timeout=60 migration-threshold=1 priority=10
    
     sudo crm configure primitive rsc_sap_NW2_ERS12 SAPInstance \
      operations \$id=rsc_sap_NW2_ERS12-operations \
      op monitor interval=11 timeout=60 on-fail=restart \
      params InstanceName=NW2_ERS12_msnw2ers START_PROFILE="/sapmnt/NW2/profile/NW2_ERS12_msnw2ers" AUTOMATIC_RECOVER=false IS_ERS=true \
      meta priority=1000
    
     sudo crm configure modgroup g-NW2_ASCS add rsc_sap_NW2_ASCS10
     sudo crm configure modgroup g-NW2_ERS add rsc_sap_NW2_ERS12
    
     sudo crm configure colocation col_sap_NW2_no_both -5000: g-NW2_ERS g-NW2_ASCS
     sudo crm configure location loc_sap_NW2_failover_to_ers rsc_sap_NW2_ASCS10 rule 2000: runs_ers_NW2 eq 1
     sudo crm configure order ord_sap_NW2_first_start_ascs Optional: rsc_sap_NW2_ASCS10:start rsc_sap_NW2_ERS12:stop symmetrical=false
   
     sudo crm configure primitive rsc_sap_NW3_ASCS20 SAPInstance \
      operations \$id=rsc_sap_NW3_ASCS20-operations \
      op monitor interval=11 timeout=60 on-fail=restart \
      params InstanceName=NW3_ASCS10_msnw3ascs START_PROFILE="/sapmnt/NW3/profile/NW3_ASCS20_msnw3ascs" \
      AUTOMATIC_RECOVER=false \
      meta resource-stickiness=5000 failure-timeout=60 migration-threshold=1 priority=10
    
     sudo crm configure primitive rsc_sap_NW3_ERS22 SAPInstance \
      operations \$id=rsc_sap_NW3_ERS22-operations \
      op monitor interval=11 timeout=60 on-fail=restart \
      params InstanceName=NW3_ERS22_msnw3ers START_PROFILE="/sapmnt/NW3/profile/NW3_ERS22_msnw2ers" AUTOMATIC_RECOVER=false IS_ERS=true \
      meta priority=1000
    
     sudo crm configure modgroup g-NW3_ASCS add rsc_sap_NW3_ASCS20
     sudo crm configure modgroup g-NW3_ERS add rsc_sap_NW3_ERS22
    
     sudo crm configure colocation col_sap_NW3_no_both -5000: g-NW3_ERS g-NW3_ASCS
     sudo crm configure location loc_sap_NW3_failover_to_ers rsc_sap_NW3_ASCS10 rule 2000: runs_ers_NW3 eq 1
     sudo crm configure order ord_sap_NW3_first_start_ascs Optional: rsc_sap_NW3_ASCS20:start rsc_sap_NW3_ERS22:stop symmetrical=false
     sudo crm configure property maintenance-mode="false"
    ```

   SAP 引入了對自 SAP NW 7.52 起對排隊伺服器 2（包括複製）的支援。 從 ABAP 平臺 1809 開始，預設情況下安裝排隊伺服器 2。 有關排隊伺服器 2 支援，請參閱 SAP 注釋[2630416。](https://launchpad.support.sap.com/#/notes/2630416)
   如果使用排隊伺服器 2 體系結構[（ENSA2](https://help.sap.com/viewer/cff8531bc1d9416d91bb6781e628d4e0/1709%20001/en-US/6d655c383abf4c129b0e5c8683e7ecd8.html)），請定義 SAP 系統**NW2**和**NW3**的資源，如下所示：

    ```
     sudo crm configure property maintenance-mode="true"
    
     sudo crm configure primitive rsc_sap_NW2_ASCS10 SAPInstance \
      operations \$id=rsc_sap_NW2_ASCS10-operations \
      op monitor interval=11 timeout=60 on-fail=restart \
      params InstanceName=NW2_ASCS10_msnw2ascs START_PROFILE="/sapmnt/NW2/profile/NW2_ASCS10_msnw2ascs" \
      AUTOMATIC_RECOVER=false \
      meta resource-stickiness=5000 
    
     sudo crm configure primitive rsc_sap_NW2_ERS12 SAPInstance \
      operations \$id=rsc_sap_NW2_ERS12-operations \
      op monitor interval=11 timeout=60 on-fail=restart \
      params InstanceName=NW2_ERS12_msnw2ers START_PROFILE="/sapmnt/NW2/profile/NW2_ERS12_msnw2ers" AUTOMATIC_RECOVER=false IS_ERS=true 
    
     sudo crm configure modgroup g-NW2_ASCS add rsc_sap_NW2_ASCS10
     sudo crm configure modgroup g-NW2_ERS add rsc_sap_NW2_ERS12
    
     sudo crm configure colocation col_sap_NW2_no_both -5000: g-NW2_ERS g-NW2_ASCS
     sudo crm configure order ord_sap_NW2_first_start_ascs Optional: rsc_sap_NW2_ASCS10:start rsc_sap_NW2_ERS12:stop symmetrical=false
   
     sudo crm configure primitive rsc_sap_NW3_ASCS20 SAPInstance \
      operations \$id=rsc_sap_NW3_ASCS20-operations \
      op monitor interval=11 timeout=60 on-fail=restart \
      params InstanceName=NW3_ASCS10_msnw3ascs START_PROFILE="/sapmnt/NW3/profile/NW3_ASCS20_msnw3ascs" \
      AUTOMATIC_RECOVER=false \
      meta resource-stickiness=5000
    
     sudo crm configure primitive rsc_sap_NW3_ERS22 SAPInstance \
      operations \$id=rsc_sap_NW3_ERS22-operations \
      op monitor interval=11 timeout=60 on-fail=restart \
      params InstanceName=NW3_ERS22_msnw3ers START_PROFILE="/sapmnt/NW3/profile/NW3_ERS22_msnw2ers" AUTOMATIC_RECOVER=false IS_ERS=true
    
     sudo crm configure modgroup g-NW3_ASCS add rsc_sap_NW3_ASCS20
     sudo crm configure modgroup g-NW3_ERS add rsc_sap_NW3_ERS22
    
     sudo crm configure colocation col_sap_NW3_no_both -5000: g-NW3_ERS g-NW3_ASCS
     sudo crm configure order ord_sap_NW3_first_start_ascs Optional: rsc_sap_NW3_ASCS20:start rsc_sap_NW3_ERS22:stop symmetrical=false
     sudo crm configure property maintenance-mode="false"
    ```

   如果要從舊版本升級到排隊伺服器 2，請參閱 SAP 注釋[2641019](https://launchpad.support.sap.com/#/notes/2641019)。 

   請確定叢集狀態正常，且所有資源皆已啟動。 資源在哪一個節點上執行並不重要。
   下面的示例顯示 SAP 系統**NW2**和**NW3**添加到群集後群集資源狀態。 

    ```
     sudo crm_mon -r
    
    # Online: [ slesmsscl1 slesmsscl2 ]
    
    #Full list of resources:
    
    #stonith-sbd     (stonith:external/sbd): Started slesmsscl1
    # Resource Group: g-NW1_ASCS
    #     fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl2
    #     nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl2
    #     vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl2
    #     rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started slesmsscl2
    # Resource Group: g-NW1_ERS
    #     fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl1
    #     nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl1
    #     vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl1
    #     rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started slesmsscl1
    # Resource Group: g-NW2_ASCS
    #     fs_NW2_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl1
    #     nc_NW2_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl1
    #     vip_NW2_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl1
    #     rsc_sap_NW2_ASCS10 (ocf::heartbeat:SAPInstance):   Started slesmsscl1
    # Resource Group: g-NW2_ERS
    #     fs_NW2_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl2
    #     nc_NW2_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl2
    #     vip_NW2_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl2
    #     rsc_sap_NW2_ERS12  (ocf::heartbeat:SAPInstance):   Started slesmsscl2
    # Resource Group: g-NW3_ASCS
    #     fs_NW3_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl1
    #     nc_NW3_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl1
    #     vip_NW3_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl1
    #     rsc_sap_NW3_ASCS20 (ocf::heartbeat:SAPInstance):   Started slesmsscl1
    # Resource Group: g-NW3_ERS
    #     fs_NW3_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl2
    #     nc_NW3_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl2
    #     vip_NW3_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl2
    #     rsc_sap_NW3_ERS22  (ocf::heartbeat:SAPInstance):   Started slesmsscl2
    ```

   下圖顯示了在 HA Web Konsole（Hawk） 中資源的外觀，SAP 系統**NW2**的資源已擴展。  

   [![SAP NetWeaver 高可用性概觀](./media/high-availability-guide-suse/ha-suse-multi-sid-hawk.png)](./media/high-availability-guide-suse/ha-suse-multi-sid-hawk-detail.png#lightbox)

### <a name="proceed-with-the-sap-installation"></a>繼續 SAP 安裝 

通過：

* [準備 SAP NetWeaver 應用程式伺服器](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse#2d6008b0-685d-426c-b59e-6cd281fd45d7)
* [安裝 DBMS 實例](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse#install-database)
* [安裝主 SAP 應用程式伺服器](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse#sap-netweaver-application-server-installation)
* 安裝一個或多個附加的 SAP 應用程式實例

## <a name="test-the-multi-sid-cluster-setup"></a>測試多 SID 群集設置

以下測試是 SUSE 最佳實踐指南中的測試案例的子集。 它們包含在您的方便。 有關群集測試的完整清單，請參閱以下文檔：

* 如果使用高可用性 NFS 伺服器，請按照 SAP 應用程式在[SUSE Linux 企業伺服器上的 Azure VM 上的 SAP NetWeaver 的高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse)操作。  
* 如果使用 Azure NetApp 檔 NFS 卷，請按照[SUSE Linux 企業伺服器上的 Azure VM 上的 SAP NetWeaver 的高可用性，以及用於 SAP 應用程式的 Azure NetApp 檔](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files)

始終閱讀 SUSE 最佳實踐指南，並執行可能添加的所有其他測試。  
提供的測試位於兩個節點多 SID 群集中，安裝了三個 SAP 系統。  

1. 測試 HAGetFailover 配置和 HACheck 容錯移轉配置

   在 ASCS<sapsid>實例當前運行的節點上以 dm 身份運行以下命令。 如果命令失敗，並且出現「失敗：記憶體不足」，則可能是因為您的主機名稱中有短破折號。 這是已知問題，SUSE 會透過 sap-suse-cluster-connector 套件提供修正程式。

   ```
    slesmsscl1:nw1adm 57> sapcontrol -nr 00 -function HAGetFailoverConfig

   # 10.12.2019 21:33:08
   # HAGetFailoverConfig
   # OK
   # HAActive: TRUE
   # HAProductVersion: SUSE Linux Enterprise Server for SAP Applications 12 SP4
   # HASAPInterfaceVersion: SUSE Linux Enterprise Server for SAP Applications 12 SP4 (sap_suse_cluster_connector 3.1.0)
   # HADocumentation: https://www.suse.com/products/sles-for-sap/resource-library/sap-best-practices/
   # HAActiveNode: slesmsscl1
   # HANodes: slesmsscl1, slesmsscl2

    slesmsscl1:nw1adm 53> sapcontrol -nr 00 -function HACheckFailoverConfig

    # 19.12.2019 21:19:58
    # HACheckFailoverConfig
    # OK
    # state, category, description, comment
    # SUCCESS, SAP CONFIGURATION, SAPInstance RA sufficient version, SAPInstance includes is-ers patch

    slesmsscl2:nw2adm 35> sapcontrol -nr 10 -function HAGetFailoverConfig

   # 10.12.2019 21:37:09
   # HAGetFailoverConfig
   # OK
   # HAActive: TRUE
   # HAProductVersion: SUSE Linux Enterprise Server for SAP Applications 12 SP4
   # HASAPInterfaceVersion: SUSE Linux Enterprise Server for SAP Applications 12 SP4 (sap_suse_cluster_connector 3.1.0)
   # HADocumentation: https://www.suse.com/products/sles-for-sap/resource-library/sap-best-practices/
   # HAActiveNode: slesmsscl2
   # HANodes: slesmsscl2, slesmsscl1

    slesmsscl2:nw2adm 52> sapcontrol -nr 10 -function HACheckFailoverConfig

    # 19.12.2019 21:17:39
    # HACheckFailoverConfig
    # OK
    # state, category, description, comment
    # SUCCESS, SAP CONFIGURATION, SAPInstance RA sufficient version, SAPInstance includes is-ers patch

    slesmsscl1:nw3adm 49> sapcontrol -nr 20 -function HAGetFailoverConfig

   # 10.12.2019 23:35:36
   # HAGetFailoverConfig
   # OK
   # HAActive: TRUE
   # HAProductVersion: SUSE Linux Enterprise Server for SAP Applications 12 SP4
   # HASAPInterfaceVersion: SUSE Linux Enterprise Server for SAP Applications 12 SP4 (sap_suse_cluster_connector 3.1.0)
   # HADocumentation: https://www.suse.com/products/sles-for-sap/resource-library/sap-best-practices/
   # HAActiveNode: slesmsscl1
   # HANodes: slesmsscl1, slesmsscl2

    slesmsscl1:nw3adm 52> sapcontrol -nr 20 -function HACheckFailoverConfig

    # 19.12.2019 21:10:42
    # HACheckFailoverConfig
    # OK
    # state, category, description, comment
    # SUCCESS, SAP CONFIGURATION, SAPInstance RA sufficient version, SAPInstance includes is-ers patch
   ```

2. 手動遷移 ASCS 實例。 該示例顯示遷移 SAP 系統 NW2 的 ASCS 實例。  
   在開始測試之前，資源狀態：
   ```
    Full list of resources:
    stonith-sbd     (stonith:external/sbd): Started slesmsscl1
     Resource Group: g-NW1_ASCS
         fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started slesmsscl1
     Resource Group: g-NW1_ERS
         fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl2
         nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl2
         vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl2
         rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started slesmsscl2
     Resource Group: g-NW2_ASCS
         fs_NW2_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW2_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW2_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW2_ASCS10 (ocf::heartbeat:SAPInstance):   Started slesmsscl1
     Resource Group: g-NW2_ERS
         fs_NW2_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl2
         nc_NW2_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl2
         vip_NW2_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl2
         rsc_sap_NW2_ERS12  (ocf::heartbeat:SAPInstance):   Started slesmsscl2
     Resource Group: g-NW3_ASCS
         fs_NW3_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl2
         nc_NW3_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl2
         vip_NW3_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl2
         rsc_sap_NW3_ASCS20 (ocf::heartbeat:SAPInstance):   Started slesmsscl2
     Resource Group: g-NW3_ERS
         fs_NW3_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW3_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW3_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW3_ERS22  (ocf::heartbeat:SAPInstance):   Started slesmsscl1
   ```

   將以下命令作為根運行以遷移 NW2 ASCS 實例。

   ```
    crm resource migrate rsc_sap_NW2_ASCS10 force
    # INFO: Move constraint created for rsc_sap_NW2_ASCS10
    
    crm resource unmigrate rsc_sap_NW2_ASCS10
   # INFO: Removed migration constraints for rsc_sap_NW2_ASCS10
   
   # Remove failed actions for the ERS that occurred as part of the migration
    crm resource cleanup rsc_sap_NW2_ERS12
   ```

   測試完成之後的資源狀態：

   ```
    Full list of resources:
    stonith-sbd     (stonith:external/sbd): Started slesmsscl1
     Resource Group: g-NW1_ASCS
         fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started slesmsscl1
     Resource Group: g-NW1_ERS
         fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl2
         nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl2
         vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl2
         rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started slesmsscl2
     Resource Group: g-NW2_ASCS
         fs_NW2_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl2
         nc_NW2_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl2
         vip_NW2_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl2
         rsc_sap_NW2_ASCS10 (ocf::heartbeat:SAPInstance):   Started slesmsscl2
     Resource Group: g-NW2_ERS
         fs_NW2_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW2_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW2_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW2_ERS12  (ocf::heartbeat:SAPInstance):   Started slesmsscl1
     Resource Group: g-NW3_ASCS
         fs_NW3_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl2
         nc_NW3_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl2
         vip_NW3_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl2
         rsc_sap_NW3_ASCS20 (ocf::heartbeat:SAPInstance):   Started slesmsscl2
     Resource Group: g-NW3_ERS
         fs_NW3_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW3_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW3_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW3_ERS22  (ocf::heartbeat:SAPInstance):   Started slesmsscl1
   ```

1. 測試 HAFailovertonode。 此處提供的測試顯示遷移 SAP 系統 NW2 的 ASCS 實例。  

   開始測試之前的資源狀態：

   ```
    Full list of resources:
    stonith-sbd     (stonith:external/sbd): Started slesmsscl1
     Resource Group: g-NW1_ASCS
         fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started slesmsscl1
     Resource Group: g-NW1_ERS
         fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl2
         nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl2
         vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl2
         rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started slesmsscl2
     Resource Group: g-NW2_ASCS
         fs_NW2_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl2
         nc_NW2_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl2
         vip_NW2_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl2
         rsc_sap_NW2_ASCS10 (ocf::heartbeat:SAPInstance):   Started slesmsscl2
     Resource Group: g-NW2_ERS
         fs_NW2_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW2_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW2_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW2_ERS12  (ocf::heartbeat:SAPInstance):   Started slesmsscl1
     Resource Group: g-NW3_ASCS
         fs_NW3_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl2
         nc_NW3_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl2
         vip_NW3_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl2
         rsc_sap_NW3_ASCS20 (ocf::heartbeat:SAPInstance):   Started slesmsscl2
     Resource Group: g-NW3_ERS
         fs_NW3_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW3_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW3_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW3_ERS22  (ocf::heartbeat:SAPInstance):   Started slesmsscl1
   ```

   將以下命令運行為**nw2**adm 以遷移 NW2 ASCS 實例。

   ```
    slesmsscl2:nw2adm 53> sapcontrol -nr 10 -host msnw2ascs -user nw2adm password -function HAFailoverToNode ""
   
   # run as root
   # Remove failed actions for the ERS that occurred as part of the migration
   crm resource cleanup rsc_sap_NW2_ERS12
   # Remove migration constraints
   crm resource clear rsc_sap_NW2_ASCS10
   #INFO: Removed migration constraints for rsc_sap_NW2_ASCS10
   ```

   測試完成之後的資源狀態：

   ```
    Full list of resources:
    stonith-sbd     (stonith:external/sbd): Started slesmsscl1
     Resource Group: g-NW1_ASCS
         fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started slesmsscl1
     Resource Group: g-NW1_ERS
         fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl2
         nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl2
         vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl2
         rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started slesmsscl2
     Resource Group: g-NW2_ASCS
         fs_NW2_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW2_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW2_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW2_ASCS10 (ocf::heartbeat:SAPInstance):   Started slesmsscl1
     Resource Group: g-NW2_ERS
         fs_NW2_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl2
         nc_NW2_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl2
         vip_NW2_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl2
         rsc_sap_NW2_ERS12  (ocf::heartbeat:SAPInstance):   Started slesmsscl2
     Resource Group: g-NW3_ASCS
         fs_NW3_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl2
         nc_NW3_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl2
         vip_NW3_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl2
         rsc_sap_NW3_ASCS20 (ocf::heartbeat:SAPInstance):   Started slesmsscl2
     Resource Group: g-NW3_ERS
         fs_NW3_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW3_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW3_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW3_ERS22  (ocf::heartbeat:SAPInstance):   Started slesmsscl1
   ```

1. 模擬節點損毀

   開始測試之前的資源狀態：

   ```
    Full list of resources:
    stonith-sbd     (stonith:external/sbd): Started slesmsscl1
     Resource Group: g-NW1_ASCS
         fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl2
         nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl2
         vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl2
         rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started slesmsscl2
     Resource Group: g-NW1_ERS
         fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started slesmsscl1
     Resource Group: g-NW2_ASCS
         fs_NW2_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl2
         nc_NW2_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl2
         vip_NW2_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl2
         rsc_sap_NW2_ASCS10 (ocf::heartbeat:SAPInstance):   Started slesmsscl2
     Resource Group: g-NW2_ERS
         fs_NW2_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW2_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW2_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW2_ERS12  (ocf::heartbeat:SAPInstance):   Started slesmsscl1
     Resource Group: g-NW3_ASCS
         fs_NW3_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl2
         nc_NW3_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl2
         vip_NW3_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl2
         rsc_sap_NW3_ASCS20 (ocf::heartbeat:SAPInstance):   Started slesmsscl2
     Resource Group: g-NW3_ERS
         fs_NW3_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW3_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW3_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW3_ERS22  (ocf::heartbeat:SAPInstance):   Started slesmsscl1
   ```

   在至少運行一個 ASCS 實例的節點上將以下命令作為根運行。 在此示例中，我們在 上`slesmsscl2`執行命令，其中 NW1 和 NW3 的 ASCS 實例正在運行。  

   ```
    slesmsscl2:~ # echo b > /proc/sysrq-trigger
   ```

   如果您使用 SBD，Pacemaker 應不會在已終止的節點上自動啟動。 節點在重新啟動後的狀態應該會像下面這樣。

   ```
    Online: [ slesmsscl1 ]
    OFFLINE: [ slesmsscl2 ]
    Full list of resources:

    stonith-sbd     (stonith:external/sbd): Started slesmsscl1
     Resource Group: g-NW1_ASCS
         fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started slesmsscl1
     Resource Group: g-NW1_ERS
         fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started slesmsscl1
     Resource Group: g-NW2_ASCS
         fs_NW2_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW2_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW2_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW2_ASCS10 (ocf::heartbeat:SAPInstance):   Started slesmsscl1
     Resource Group: g-NW2_ERS
         fs_NW2_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW2_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW2_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW2_ERS12  (ocf::heartbeat:SAPInstance):   Started slesmsscl1
     Resource Group: g-NW3_ASCS
         fs_NW3_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW3_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW3_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW3_ASCS20 (ocf::heartbeat:SAPInstance):   Started slesmsscl1
     Resource Group: g-NW3_ERS
         fs_NW3_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW3_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW3_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW3_ERS22  (ocf::heartbeat:SAPInstance):   Started slesmsscl1
    
    Failed Resource Actions:
    * rsc_sap_NW1_ERS02_monitor_11000 on slesmsscl1 'not running' (7): call=125, status=complete, exitreason='',
        last-rc-change='Fri Dec 13 19:32:10 2019', queued=0ms, exec=0ms
    * rsc_sap_NW2_ERS12_monitor_11000 on slesmsscl1 'not running' (7): call=126, status=complete, exitreason='',
        last-rc-change='Fri Dec 13 19:32:10 2019', queued=0ms, exec=0ms
    * rsc_sap_NW3_ERS22_monitor_11000 on slesmsscl1 'not running' (7): call=127, status=complete, exitreason='',
        last-rc-change='Fri Dec 13 19:32:10 2019', queued=0ms, exec=0ms
   ```

   使用下列命令在已清除的節點上啟動 Pacemaker，並清除 SBD 訊息及失敗的資源。

   ```# run as root
   # list the SBD device(s)
   slesmsscl2:~ # cat /etc/sysconfig/sbd | grep SBD_DEVICE=
   # SBD_DEVICE="/dev/disk/by-id/scsi-36001405772fe8401e6240c985857e116;/dev/disk/by-id/scsi-36001405034a84428af24ddd8c3a3e9e1;/dev/disk/by-id/scsi-36001405cdd5ac8d40e548449318510c3"
   
   slesmsscl2:~ # sbd -d /dev/disk/by-id/scsi-36001405772fe8401e6240c985857e116 -d /dev/disk/by-id/scsi-36001405034a84428af24ddd8c3a3e9e1 -d /dev/disk/by-id/scsi-36001405cdd5ac8d40e548449318510c3 message slesmsscl2 clear
   
   slesmsscl2:~ # systemctl start pacemaker
   slesmsscl2:~ # crm resource cleanup rsc_sap_NW1_ERS02
   slesmsscl2:~ # crm resource cleanup rsc_sap_NW2_ERS12
   slesmsscl2:~ # crm resource cleanup rsc_sap_NW3_ERS22
   ```

   測試完成之後的資源狀態：

   ```
    Full list of resources:
    stonith-sbd     (stonith:external/sbd): Started slesmsscl1
     Resource Group: g-NW1_ASCS
         fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started slesmsscl1
     Resource Group: g-NW1_ERS
         fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl2
         nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl2
         vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl2
         rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started slesmsscl2
     Resource Group: g-NW2_ASCS
         fs_NW2_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW2_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW2_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW2_ASCS10 (ocf::heartbeat:SAPInstance):   Started slesmsscl1
     Resource Group: g-NW2_ERS
         fs_NW2_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl2
         nc_NW2_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl2
         vip_NW2_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl2
         rsc_sap_NW2_ERS12  (ocf::heartbeat:SAPInstance):   Started slesmsscl2
     Resource Group: g-NW3_ASCS
         fs_NW3_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW3_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW3_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW3_ASCS20 (ocf::heartbeat:SAPInstance):   Started slesmsscl1
     Resource Group: g-NW3_ERS
         fs_NW3_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl2
         nc_NW3_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl2
         vip_NW3_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl2
         rsc_sap_NW3_ERS22  (ocf::heartbeat:SAPInstance):   Started slesmsscl2
   ```

## <a name="next-steps"></a>後續步驟

* [適用於 SAP 的 Azure 虛擬機器規劃和實作][planning-guide]
* [適用於 SAP 的 Azure 虛擬機器部署][deployment-guide]
* [適用於 SAP 的 Azure 虛擬機器 DBMS 部署][dbms-guide]
* 若要了解如何建立高可用性並為 Azure VM 上的 SAP HANA 規劃災害復原，請參閱 [Azure 虛擬機器 (VM) 上 SAP HANA 的高可用性][sap-hana-ha]
