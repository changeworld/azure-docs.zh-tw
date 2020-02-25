---
title: RHEL 上 SAP NW 的 Azure Vm 高可用性 |Microsoft Docs
description: SAP NetWeaver on Red Hat Enterprise Linux 的 Azure 虛擬機器高可用性
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 02/21/2020
ms.author: radeltch
ms.openlocfilehash: 8f2de656473d52c7a40bef83237bf2aed563e111
ms.sourcegitcommit: f27b045f7425d1d639cf0ff4bcf4752bf4d962d2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/23/2020
ms.locfileid: "77566160"
---
# <a name="azure-virtual-machines-high-availability-for-sap-netweaver-on-red-hat-enterprise-linux"></a>SAP NetWeaver on Red Hat Enterprise Linux 的 Azure 虛擬機器高可用性

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md

[2002167]: https://launchpad.support.sap.com/#/notes/2002167
[2009879]: https://launchpad.support.sap.com/#/notes/2009879
[1928533]: https://launchpad.support.sap.com/#/notes/1928533
[2015553]: https://launchpad.support.sap.com/#/notes/2015553
[2178632]: https://launchpad.support.sap.com/#/notes/2178632
[2191498]: https://launchpad.support.sap.com/#/notes/2191498
[2243692]: https://launchpad.support.sap.com/#/notes/2243692
[1999351]: https://launchpad.support.sap.com/#/notes/1999351
[1410736]:https://launchpad.support.sap.com/#/notes/1410736

[sap-swcenter]:https://support.sap.com/en/my-support/software-downloads.html

[template-multisid-xscs]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs-md%2Fazuredeploy.json

[sap-hana-ha]:sap-hana-high-availability-rhel.md
[glusterfs-ha]:high-availability-guide-rhel-glusterfs.md

本文說明如何部署虛擬機器、設定虛擬機器、安裝叢集架構，以及安裝高可用性的 SAP NetWeaver 7.50 系統。
在範例設定中，安裝命令等。使用 ASCS 實例號碼00、ERS 實例號碼02和 SAP 系統識別碼 NW1。 範例中資源（例如虛擬機器、虛擬網路）的名稱會假設您已使用[ASCS/SCS 範本][template-multisid-xscs]搭配資源前置詞 NW1 來建立資源。

請先閱讀下列 SAP Note 和文件

* SAP Note [1928533]，其中包含：
  * SAP 軟體部署支援的 Azure VM 大小清單
  * Azure VM 大小的重要容量資訊
  * 支援的 SAP 軟體，以及作業系統 (OS) 與資料庫組合
  * Microsoft Azure 上 Windows 和 Linux 所需的 SAP 核心版本

* SAP Note [2015553] 列出 Azure 中 SAP 支援的 SAP 軟體部署先決條件。
* SAP Note [2002167] 建議適用於 Red Hat Enterprise Linux 的作業系統設定
* SAP Note [2009879] 提供適用於 Red Hat Enterprise Linux 的 SAP HANA 方針
* SAP Note [2178632] 包含在 Azure 中針對 SAP 回報的所有監視計量詳細資訊。
* SAP Note [2191498] 包含 Azure 中 Linux 所需的 SAP Host Agent 版本。
* SAP Note [2243692] 包含 Azure 中 Linux 上的 SAP 授權相關資訊。
* SAP Note [1999351] 包含 Azure Enhanced Monitoring Extension for SAP 的其他疑難排解資訊。
* [SAP Community WIKI](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) 包含 Linux 所需的所有 SAP Note。
* [適用于 SAP on Linux 的 Azure 虛擬機器規劃和執行][planning-guide]
* [適用于 SAP on Linux 的 Azure 虛擬機器部署][deployment-guide]
* [適用于 SAP on Linux 的 Azure 虛擬機器 DBMS 部署][dbms-guide]
* [Red Hat Gluster Storage 產品文件](https://access.redhat.com/documentation/red_hat_gluster_storage/)
* [Pacemaker 叢集中的 SAP Netweaver](https://access.redhat.com/articles/3150081)
* 一般 RHEL 文件
  * [高可用性附加元件概觀](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_overview/index)
  * [高可用性附加元件管理](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_administration/index)
  * [高可用性附加元件參考](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_reference/index)
  * [在 RHEL 7.5 中使用獨立資源為 SAP Netweaver 設定 ASCS/ERS](https://access.redhat.com/articles/3569681)
  * [在 RHEL 上的 Pacemaker 中使用獨立的排入佇列伺服器2（ENSA2）設定 SAP S/4HANA ASCS/ERS](https://access.redhat.com/articles/3974941)
* Azure 專用 RHEL 文件：
  * [RHEL 高可用性叢集的支援原則：以 Microsoft Azure 虛擬機器作為叢集成員](https://access.redhat.com/articles/3131341)
  * [在 Microsoft Azure 上安裝和設定 Red Hat Enterprise Linux 7.4 (和更新版本) 高可用性叢集](https://access.redhat.com/articles/3252491)

## <a name="overview"></a>概觀

為了實現高可用性，SAP NetWeaver 需要使用共用儲存體。 GlusterFS 會於獨立的叢集中設定，且可供多個 SAP 系統使用。

![SAP NetWeaver 高可用性概觀](./media/high-availability-guide-rhel/ha-rhel.png)

SAP NetWeaver ASCS、SAP NetWeaver SCS、SAP NetWeaver ERS 和 SAP Hana 資料庫會使用虛擬主機名稱和虛擬 IP 位址。 在 Azure 上必須有負載平衡器才能使用虛擬 IP 位址。 我們建議使用[標準負載平衡器](https://docs.microsoft.com/azure/load-balancer/quickstart-load-balancer-standard-public-portal)。 下列清單顯示 (A)SCS 和 ERS 負載平衡器的組態。

> [!IMPORTANT]
> **不支援**在 Azure vm 中使用具有 Red Hat Linux 作為客體作業系統的 SAP ASCS/ERS 多 SID 叢集。 多 SID 叢集描述在一個 Pacemaker 叢集中安裝多個具有不同 Sid 的 SAP ASCS/ERS 實例。

### <a name="ascs"></a>(A)SCS

* 前端組態
  * IP 位址 10.0.0.7
* 後端組態
  * 連線到應該屬於 (A)SCS/ERS 叢集一部分之所有虛擬機器的主要網路介面
* 探查連接埠
  * 連接埠 620<strong>&lt;nr&gt;</strong>
* 負載平衡規則
  * 如果使用 Standard Load Balancer，請選取 [ **HA 埠**]
  * 如果使用基本 Load Balancer，請建立下列埠的負載平衡規則
    * 32<strong>&lt;nr&gt;</strong> TCP
    * 36<strong>&lt;nr&gt;</strong> TCP
    * 39<strong>&lt;nr&gt;</strong> TCP
    * 81<strong>&lt;nr&gt;</strong> TCP
    * 5<strong>&lt;nr&gt;</strong>13 TCP
    * 5<strong>&lt;nr&gt;</strong>14 TCP
    * 5<strong>&lt;nr&gt;</strong>16 TCP

### <a name="ers"></a>ERS

* 前端組態
  * IP 位址 10.0.0.8
* 後端組態
  * 連線到應該屬於 (A)SCS/ERS 叢集一部分之所有虛擬機器的主要網路介面
* 探查連接埠
  * 連接埠 621<strong>&lt;nr&gt;</strong>
* 負載平衡規則
  * 如果使用 Standard Load Balancer，請選取 [ **HA 埠**]
  * 如果使用基本 Load Balancer，請建立下列埠的負載平衡規則
    * 32<strong>&lt;nr&gt;</strong> TCP
    * 33<strong>&lt;nr&gt;</strong> TCP
    * 5<strong>&lt;nr&gt;</strong>13 TCP
    * 5<strong>&lt;nr&gt;</strong>14 TCP
    * 5<strong>&lt;nr&gt;</strong>16 TCP

## <a name="setting-up-glusterfs"></a>設定 GlusterFS

SAP NetWeaver 需要傳輸和設定檔目錄的共用儲存體。 如需如何設定 SAP NetWeaver 的 GlusterFS，請參閱[Sap NetWeaver Red Hat Enterprise Linux 上的 Azure vm 上的 GlusterFS][glusterfs-ha] 。

## <a name="setting-up-ascs"></a>設定 (A)SCS

您可以使用來自 GitHub 的 Azure 範本來部署所有必要的 Azure 資源，包括虛擬機器、可用性設定組及負載平衡器，也可以手動部署資源。

### <a name="deploy-linux-via-azure-template"></a>透過 Azure 範本部署 Linux

Azure Marketplace 包含 Red Hat Enterprise Linux 的映像，您可用來部署新的虛擬機器。 您可以使用 GitHub 上的其中一個快速入門範本來部署所有必要資源。 此範本會部署虛擬機器、負載平衡器、可用性設定組等。請遵循下列步驟來部署範本：

1. 在 Azure 入口網站上開啟[ASCS/SCS 範本][template-multisid-xscs]  
1. 輸入下列參數
   1. 資源前置詞  
      輸入您想要使用的前置詞。 該值會作為所部署之資源的前置詞。
   1. 堆疊類型  
      選取 SAP NetWeaver 堆疊類型
   1. OS 類型  
      選取一個 Linux 發行版本。 在此範例中，請選取 RHEL 7
   1. DB 類型  
      選取 HANA
   1. SAP 系統計數  
      在此叢集中執行的 SAP 系統數目。 選取 1。
   1. 系統可用性  
      選取 HA
   1. 管理員使用者名稱、管理員密碼或 SSH 金鑰  
      隨即會建立新的使用者，以用來登入電腦。
   1. 子網路識別碼  
   如果您想將 VM 部署至現有的 VNet (其中具有定義 VM 應指派的目的子網路)，請說明該特定子網路的 ID。 識別碼通常如下所示：/subscriptions/ **&lt;訂用帳戶識別碼&gt;** /resourceGroups/ **&lt;資源群組名稱&gt;** /providers/Microsoft.Network/virtualNetworks/ **&lt;虛擬網路名稱&gt;** /subnets/ **&lt;子網路名稱&gt;**

### <a name="deploy-linux-manually-via-azure-portal"></a>透過 Azure 入口網站手動部署 Linux

您必須先為此叢集建立虛擬機器。 之後，您需建立負載平衡器，然後使用後端集區中的虛擬機器。

1. 建立資源群組
1. 建立虛擬網路
1. 建立可用性設定組  
   設定更新網域上限
1. 建立虛擬機器 1  
   至少使用 RHEL 7，本範例中使用 Red Hat Enterprise Linux 7.4 映像 <https://portal.azure.com/#create/RedHat.RedHatEnterpriseLinux74-ARM>  
   選取稍早建立的「可用性設定組」  
1. 建立虛擬機器 2  
   至少使用 RHEL 7，本範例中使用 Red Hat Enterprise Linux 7.4 映像 <https://portal.azure.com/#create/RedHat.RedHatEnterpriseLinux74-ARM>  
   選取稍早建立的「可用性設定組」  
1. 將至少一個資料磁碟新增至兩部虛擬機器  
   資料磁碟用於 /usr/sap/`<SAPSID`> 目錄
1. 建立負載平衡器（內部、標準）：  
   1. 建立前端 IP 位址
      1. 針對 ASCS 是 IP 位址 10.0.0.7
         1. 開啟負載平衡器，選取前端 IP 集區，然後按一下 [新增]
         1. 輸入新前端 IP 集區的名稱 (例如 **nw1-ascs-frontend**)
         1. 將 [指派] 設定為 [靜態]，然後輸入 IP 位址 (例如 **10.0.0.7**)
         1. Click OK
      1. 針對 ASCS ERS 是 IP 位址 10.0.0.8
         * 重複上述步驟以建立 ERS 的 IP 位址 (例如 **10.0.0.8** 和 **nw1-aers-backend**)
   1. 建立後端集區
      1. 建立 ASCS 的後端集區
         1. 開啟負載平衡器，選取後端集區，然後按一下 [新增]
         1. 輸入新後端集區的名稱 (例如 **nw1-ascs-backend**)
         1. 按一下 [新增虛擬機器]。
         1. 選取 [虛擬機器]。
         1. 選取（A） SCS 叢集及其 IP 位址的虛擬機器。
         1. 按一下 [新增]
      1. 建立 ASCS ERS 的後端集區
         * 重複上述步驟以建立 ERS 的後端集區 (例如 **nw1-aers-backend**)
   1. 建立健康狀態探查
      1. 針對 ASCS 是連接埠 620**00**
         1. 開啟負載平衡器，選取健康情況探查，然後按一下 [新增]
         1. 輸入新健康情況探查的名稱 (例如 **nw1-ascs-hp**)
         1. 選取 [TCP] 作為通訊協定、連接埠 620**00**，保留 [間隔] 5 和 [狀況不良閾值] 2
         1. Click OK
      1. 針對 ASCS ERS 是連接埠 621**02**
         * 重複上述步驟以建立 ERS 的健康情況探查 (例如 621**02** 和 **nw1-aers-hp**)
   1. 負載平衡規則
      1. ASCS 的負載平衡規則
         1. 開啟負載平衡器，選取 [負載平衡規則]，然後按一下 [新增]
         1. 輸入新負載平衡器規則的名稱（例如**nw1-lb-ascs**）
         1. 選取您稍早建立的前端 IP 位址、後端集區及健康情況探查（例如**nw1-ascs-前端**、 **nw1-ascs-後端**和**nw1-ascs-hp**）
         1. 選取**HA 埠**
         1. 將閒置逾時增加為 30 分鐘
         1. **務必啟用浮動 IP**
         1. Click OK
         * 重複上述步驟以建立 ERS 的負載平衡規則（例如**nw1-lb-ERS**）
1. 或者，如果您的案例需要基本負載平衡器（內部），請遵循下列步驟：  
   1. 建立前端 IP 位址
      1. 針對 ASCS 是 IP 位址 10.0.0.7
         1. 開啟負載平衡器，選取前端 IP 集區，然後按一下 [新增]
         1. 輸入新前端 IP 集區的名稱 (例如 **nw1-ascs-frontend**)
         1. 將 [指派] 設定為 [靜態]，然後輸入 IP 位址 (例如 **10.0.0.7**)
         1. Click OK
      1. 針對 ASCS ERS 是 IP 位址 10.0.0.8
         * 重複上述步驟以建立 ERS 的 IP 位址 (例如 **10.0.0.8** 和 **nw1-aers-backend**)
   1. 建立後端集區
      1. 建立 ASCS 的後端集區
         1. 開啟負載平衡器，選取後端集區，然後按一下 [新增]
         1. 輸入新後端集區的名稱 (例如 **nw1-ascs-backend**)
         1. 按一下 [新增虛擬機器]。
         1. 選取您稍早建立的可用性設定組
         1. 選取 (A)SCS 叢集的虛擬機器
         1. Click OK
      1. 建立 ASCS ERS 的後端集區
         * 重複上述步驟以建立 ERS 的後端集區 (例如 **nw1-aers-backend**)
   1. 建立健康狀態探查
      1. 針對 ASCS 是連接埠 620**00**
         1. 開啟負載平衡器，選取健康情況探查，然後按一下 [新增]
         1. 輸入新健康情況探查的名稱 (例如 **nw1-ascs-hp**)
         1. 選取 [TCP] 作為通訊協定、連接埠 620**00**，保留 [間隔] 5 和 [狀況不良閾值] 2
         1. Click OK
      1. 針對 ASCS ERS 是連接埠 621**02**
         * 重複上述步驟以建立 ERS 的健康情況探查 (例如 621**02** 和 **nw1-aers-hp**)
   1. 負載平衡規則
      1. 針對 ASCS 是 32**00** TCP
         1. 開啟負載平衡器，選取 [負載平衡規則]，然後按一下 [新增]
         1. 輸入新負載平衡器規則的名稱 (例如 **nw1-lb-3200**)
         1. 選取您稍早建立的前端 IP 位址、後端集區及健康情況探查 (例如 **nw1-ascs-frontend**)
         1. 保留通訊協定 [TCP]，輸入連接埠 **3200**
         1. 將閒置逾時增加為 30 分鐘
         1. **務必啟用浮動 IP**
         1. Click OK
      1. ASCS 的其他連接埠
         * 重複上述步驟來為 ASCS 設定連接埠 36**00**、39**00**、81**00**、5**00**13、5**00**14、5**00**16 和 TCP
      1. ASCS ERS 的其他連接埠
         * 重複上述步驟來為 ASCS ERS 設定連接埠 33**02**、5**02**13、5**02**14、5**02**16 和 TCP

> [!Note]
> 當沒有公用 IP 位址的 Vm 放在內部（沒有公用 IP 位址）標準 Azure 負載平衡器的後端集區中時，除非執行額外設定以允許路由傳送至公用端點，否則將不會有輸出網際網路連線能力。 如需如何達到輸出連線能力的詳細資訊，請參閱[在 SAP 高可用性案例中使用 Azure Standard Load Balancer 虛擬機器的公用端點連線能力](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-standard-load-balancer-outbound-connections)。  

> [!IMPORTANT]
> 請勿在位於 Azure Load Balancer 後方的 Azure Vm 上啟用 TCP 時間戳記。 啟用 TCP 時間戳記會導致健康情況探查失敗。 將參數**net.tcp. tcp_timestamps**設定為**0**。 如需詳細資訊，請參閱[Load Balancer 健康情況探查](https://docs.microsoft.com/azure/load-balancer/load-balancer-custom-probe-overview)。

### <a name="create-pacemaker-cluster"></a>建立 Pacemaker 叢集

依照[在 Azure 中於 Red Hat Enterprise Linux 上設定 Pacemaker](high-availability-guide-rhel-pacemaker.md) 中的步驟，建立此 (A)SCS 伺服器的基本 Pacemaker 叢集。

### <a name="prepare-for-sap-netweaver-installation"></a>準備進行 SAP NetWeaver 安裝

下列項目會加上下列其中一個前置詞： **[A]** - 適用於所有節點、 **[1]** - 僅適用於節點 1 或 **[2]** - 僅適用於節點 2。

1. **[A]** 設定主機名稱解析

   您可以使用 DNS 伺服器，或修改所有節點上的 /etc/hosts。 這個範例示範如何使用 /etc/hosts 檔案。
   取代下列命令中的 IP 位址和主機名稱

   <pre><code>sudo vi /etc/hosts
   </code></pre>

   將下列幾行插入至 /etc/hosts。 變更 IP 位址和主機名稱以符合您的環境

   <pre><code># IP addresses of the GlusterFS nodes
   <b>10.0.0.40 glust-0</b>
   <b>10.0.0.41 glust-1</b>
   <b>10.0.0.42 glust-2</b>
   # IP address of the load balancer frontend configuration for SAP NetWeaver ASCS
   <b>10.0.0.7 nw1-ascs</b>
   # IP address of the load balancer frontend configuration for SAP NetWeaver ASCS ERS
   <b>10.0.0.8 nw1-aers</b>
   </code></pre>

1. **[A]** 建立共用目錄

   <pre><code>sudo mkdir -p /sapmnt/<b>NW1</b>
   sudo mkdir -p /usr/sap/trans
   sudo mkdir -p /usr/sap/<b>NW1</b>/SYS
   sudo mkdir -p /usr/sap/<b>NW1</b>/ASCS<b>00</b>
   sudo mkdir -p /usr/sap/<b>NW1</b>/ERS<b>02</b>

   sudo chattr +i /sapmnt/<b>NW1</b>
   sudo chattr +i /usr/sap/trans
   sudo chattr +i /usr/sap/<b>NW1</b>/SYS
   sudo chattr +i /usr/sap/<b>NW1</b>/ASCS<b>00</b>
   sudo chattr +i /usr/sap/<b>NW1</b>/ERS<b>02</b>
   </code></pre>

1. **[A]** 安裝 GlusterFS 用戶端和其他需求

   <pre><code>sudo yum -y install glusterfs-fuse resource-agents resource-agents-sap
   </code></pre>

1. **[A]** 檢查 resource-agents-sap 的版本

   請確定已安裝的 resource-agents-sap 套件版本至少為 3.9.5-124.el7
   <pre><code>sudo yum info resource-agents-sap
   
   # Loaded plugins: langpacks, product-id, search-disabled-repos
   # Repodata is over 2 weeks old. Install yum-cron? Or run: yum makecache fast
   # Installed Packages
   # Name        : resource-agents-sap
   # Arch        : x86_64
   # Version     : <b>3.9.5</b>
   # Release     : <b>124.el7</b>
   # Size        : 100 k
   # Repo        : installed
   # From repo   : rhel-sap-for-rhel-7-server-rpms
   # Summary     : SAP cluster resource agents and connector script
   # URL         : https://github.com/ClusterLabs/resource-agents
   # License     : GPLv2+
   # Description : The SAP resource agents and connector script interface with
   #          : Pacemaker to allow SAP instances to be managed in a cluster
   #          : environment.
   </code></pre>


1. **[A]** 新增掛接項目

   <pre><code>sudo vi /etc/fstab
   
   # Add the following lines to fstab, save and exit
   <b>glust-0</b>:/<b>NW1</b>-sapmnt /sapmnt/<b>NW1</b> glusterfs backup-volfile-servers=<b>glust-1:glust-2</b> 0 0
   <b>glust-0</b>:/<b>NW1</b>-trans /usr/sap/trans glusterfs backup-volfile-servers=<b>glust-1:glust-2</b> 0 0
   <b>glust-0</b>:/<b>NW1</b>-sys /usr/sap/<b>NW1</b>/SYS glusterfs backup-volfile-servers=<b>glust-1:glust-2</b> 0 0
   </code></pre>

   掛接新的共用項目

   <pre><code>sudo mount -a
   </code></pre>

1. **[A]** 設定分頁檔

   <pre><code>sudo vi /etc/waagent.conf
   
   # Set the property ResourceDisk.EnableSwap to y
   # Create and use swapfile on resource disk.
   ResourceDisk.EnableSwap=<b>y</b>
   
   # Set the size of the SWAP file with property ResourceDisk.SwapSizeMB
   # The free space of resource disk varies by virtual machine size. Make sure that you do not set a value that is too big. You can check the SWAP space with command swapon
   # Size of the swapfile.
   ResourceDisk.SwapSizeMB=<b>2000</b>
   </code></pre>

   重新啟動代理程式以啟動變更

   <pre><code>sudo service waagent restart
   </code></pre>

1. **[A]** RHEL 設定

   按照 SAP Note [2002167] 中的說明設定 RHEL

### <a name="installing-sap-netweaver-ascsers"></a>安裝 SAP NetWeaver ASCS/ERS

1. **[1]** 為 ASCS 執行個體建立虛擬 IP 資源和健康情況探查

   <pre><code>sudo pcs node standby <b>nw1-cl-1</b>
   
   sudo pcs resource create fs_<b>NW1</b>_ASCS Filesystem device='<b>glust-0</b>:/<b>NW1</b>-ascs' \
     directory='/usr/sap/<b>NW1</b>/ASCS<b>00</b>' fstype='glusterfs' \
     options='backup-volfile-servers=<b>glust-1:glust-2</b>' \
     --group g-<b>NW1</b>_ASCS
   
   sudo pcs resource create vip_<b>NW1</b>_ASCS IPaddr2 \
     ip=<b>10.0.0.7</b> cidr_netmask=<b>24</b> \
     --group g-<b>NW1</b>_ASCS
   
   sudo pcs resource create nc_<b>NW1</b>_ASCS azure-lb port=620<b>00</b> \
     --group g-<b>NW1</b>_ASCS
   </code></pre>

   請確定叢集狀態正常，且所有資源皆已啟動。 資源在哪一個節點上執行並不重要。

   <pre><code>sudo pcs status
   
   # Node <b>nw1-cl-1</b>: standby
   # Online: [ <b>nw1-cl-0</b> ]
   #
   # Full list of resources:
   #
   # rsc_st_azure    (stonith:fence_azure_arm):      Started <b>nw1-cl-0</b>
   #  Resource Group: g-<b>NW1</b>_ASCS
   #      fs_<b>NW1</b>_ASCS        (ocf::heartbeat:Filesystem):    Started <b>nw1-cl-0</b>
   #      nc_<b>NW1</b>_ASCS        (ocf::heartbeat:azure-lb):      Started <b>nw1-cl-0</b>
   #      vip_<b>NW1</b>_ASCS       (ocf::heartbeat:IPaddr2):       Started <b>nw1-cl-0</b>
   </code></pre>

1. **[1]** 安裝 SAP NetWeaver ASCS  

   以 root 身分使用虛擬主機名稱 (對應至 ASCS 負載平衡器前端組態的 IP 位址，例如 <b>nw1-ascs</b>、<b>10.0.0.7</b>) 和您用於負載平衡器探查的執行個體號碼 (例如 <b>00</b>)，在第一個節點上安裝 SAP NetWeaver ASCS。

   您可以使用 sapinst 參數 SAPINST_REMOTE_ACCESS_USER 來允許非 root 使用者連線到 sapinst。

   <pre><code># Allow access to SWPM. This rule is not permanent. If you reboot the machine, you have to run the command again.
   sudo firewall-cmd --zone=public  --add-port=4237/tcp
   
   sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b>
   </code></pre>

   如果安裝作業無法在 /usr/sap/**NW1**/ASCS**00** 中建立子資料夾，請嘗試設定 ASCS**00** 資料夾的擁有者和群組，然後重試。

   <pre><code>sudo chown nw1adm /usr/sap/<b>NW1</b>/ASCS<b>00</b>
   sudo chgrp sapsys /usr/sap/<b>NW1</b>/ASCS<b>00</b>
   </code></pre>

1. **[1]** 為 ERS 執行個體建立虛擬 IP 資源和健康情況探查

   <pre><code>sudo pcs node unstandby <b>nw1-cl-1</b>
   sudo pcs node standby <b>nw1-cl-0</b>
   
   sudo pcs resource create fs_<b>NW1</b>_AERS Filesystem device='<b>glust-0</b>:/<b>NW1</b>-aers' \
     directory='/usr/sap/<b>NW1</b>/ERS<b>02</b>' fstype='glusterfs' \
     options='backup-volfile-servers=<b>glust-1:glust-2</b>' \
    --group g-<b>NW1</b>_AERS
   
   sudo pcs resource create vip_<b>NW1</b>_AERS IPaddr2 \
     ip=<b>10.0.0.8</b> cidr_netmask=<b>24</b> \
    --group g-<b>NW1</b>_AERS
   
   sudo pcs resource create nc_<b>NW1</b>_AERS azure-lb port=621<b>02</b> \
    --group g-<b>NW1</b>_AERS
   </code></pre>
 
   請確定叢集狀態正常，且所有資源皆已啟動。 資源在哪一個節點上執行並不重要。

   <pre><code>sudo pcs status
   
   # Node <b>nw1-cl-0</b>: standby
   # Online: [ <b>nw1-cl-1</b> ]
   #
   # Full list of resources:
   #
   # rsc_st_azure    (stonith:fence_azure_arm):      Started <b>nw1-cl-1</b>
   #  Resource Group: g-<b>NW1</b>_ASCS
   #      fs_<b>NW1</b>_ASCS        (ocf::heartbeat:Filesystem):    Started <b>nw1-cl-1</b>
   #      nc_<b>NW1</b>_ASCS        (ocf::heartbeat:azure-lb):      Started <b>nw1-cl-1</b>
   #      vip_<b>NW1</b>_ASCS       (ocf::heartbeat:IPaddr2):       Started <b>nw1-cl-1</b>
   #  Resource Group: g-<b>NW1</b>_AERS
   #      fs_<b>NW1</b>_AERS        (ocf::heartbeat:Filesystem):    Started <b>nw1-cl-1</b>
   #      nc_<b>NW1</b>_AERS        (ocf::heartbeat:azure-lb):      Started <b>nw1-cl-1</b>
   #      vip_<b>NW1</b>_AERS       (ocf::heartbeat:IPaddr2):       Started <b>nw1-cl-1</b>
   </code></pre>

1. **[2]** 安裝 SAP NetWeaver ERS  

   以 root 身分使用虛擬主機名稱 (對應至 ERS 負載平衡器前端組態的 IP 位址，例如 <b>nw1-aers</b>、<b>10.0.0.8</b>) 和您用於負載平衡器探查的執行個體號碼 (例如 <b>02</b>)，在第二個節點上安裝 SAP NetWeaver ERS。

   您可以使用 sapinst 參數 SAPINST_REMOTE_ACCESS_USER 來允許非 root 使用者連線到 sapinst。

   <pre><code># Allow access to SWPM. This rule is not permanent. If you reboot the machine, you have to run the command again.
   sudo firewall-cmd --zone=public  --add-port=4237/tcp

   sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b>
   </code></pre>

   如果安裝作業無法在 /usr/sap/**NW1**/ERS**02** 中建立子資料夾，請嘗試設定 ERS**02** 資料夾的擁有者和群組，然後重試。

   <pre><code>sudo chown nw1adm /usr/sap/<b>NW1</b>/ERS<b>02</b>
   sudo chgrp sapsys /usr/sap/<b>NW1</b>/ERS<b>02</b>
   </code></pre>

1. **[1]** 調整 ASCS/SCS 和 ERS 執行個體設定檔

   * ASCS/SCS 設定檔

   <pre><code>sudo vi /sapmnt/<b>NW1</b>/profile/<b>NW1</b>_<b>ASCS00</b>_<b>nw1-ascs</b>
   
   # Change the restart command to a start command
   #Restart_Program_01 = local $(_EN) pf=$(_PF)
   Start_Program_01 = local $(_EN) pf=$(_PF)
   
   # Add the keep alive parameter
   enque/encni/set_so_keepalive = true
   </code></pre>

   * ERS 設定檔

   <pre><code>sudo vi /sapmnt/<b>NW1</b>/profile/<b>NW1</b>_ERS<b>02</b>_<b>nw1-aers</b>
   
   # Change the restart command to a start command
   #Restart_Program_00 = local $(_ER) pf=$(_PFL) NR=$(SCSID)
   Start_Program_00 = local $(_ER) pf=$(_PFL) NR=$(SCSID)
   
   # remove Autostart from ERS profile
   # Autostart = 1
   </code></pre>


1. **[A]** 設定保持運作

   SAP NetWeaver 應用程式伺服器和 ASCS/SCS 之間的通訊是透過軟體負載平衡器來路由傳送。 在逾時時間 (可設定) 過後，負載平衡器就會將非作用中的連線中斷。 若要避免這種情況，您必須在 SAP NetWeaver ASCS/SCS 設定檔中設定參數，並變更 Linux 系統設定。 如需詳細資訊，[請參閱 SAP 附注 1410736][1410736] 。

   ASCS/SCS 設定檔參數 enque/encni/set_so_keepalive 已在最後一個步驟中新增。

   <pre><code># Change the Linux system configuration
   sudo sysctl net.ipv4.tcp_keepalive_time=120
   </code></pre>

1. **[A]** 更新 /usr/sap/sapservices 檔案

   若要避免 sapinit 啟動指令碼啟動執行個體，Pacemaker 所管理的所有執行個體都必須從 /usr/sap/sapservices 檔案加上註解。 如果 SAP Hana 執行個體將搭配 Hana SR 使用，請勿為其加上註解。

   <pre><code>
   sudo vi /usr/sap/sapservices
   
   # On the node where you installed the ASCS, comment out the following line
   # LD_LIBRARY_PATH=/usr/sap/<b>NW1</b>/ASCS<b>00</b>/exe:$LD_LIBRARY_PATH; export LD_LIBRARY_PATH; /usr/sap/<b>NW1</b>/ASCS<b>00</b>/exe/sapstartsrv pf=/usr/sap/<b>NW1</b>/SYS/profile/<b>NW1</b>_ASCS<b>00</b>_<b>nw1-ascs</b> -D -u <b>nw1</b>adm
   
   # On the node where you installed the ERS, comment out the following line
   # LD_LIBRARY_PATH=/usr/sap/<b>NW1</b>/ERS<b>02</b>/exe:$LD_LIBRARY_PATH; export LD_LIBRARY_PATH; /usr/sap/<b>NW1</b>/ERS<b>02</b>/exe/sapstartsrv pf=/usr/sap/<b>NW1</b>/ERS<b>02</b>/profile/<b>NW1</b>_ERS<b>02</b>_<b>nw1-aers</b> -D -u <b>nw1</b>adm
   </code></pre>

1. **[1]** 建立 SAP 叢集資源

  如果使用佇列伺服器1架構（ENSA1），請定義資源，如下所示：

   <pre><code>sudo pcs property set maintenance-mode=true
   
   sudo pcs resource create rsc_sap_<b>NW1</b>_ASCS00 SAPInstance \
    InstanceName=<b>NW1</b>_ASCS00_<b>nw1-ascs</b> START_PROFILE="/sapmnt/<b>NW1</b>/profile/<b>NW1</b>_ASCS00_<b>nw1-ascs</b>" \
    AUTOMATIC_RECOVER=false \
    meta resource-stickiness=5000 migration-threshold=1 failure-timeout=60 \
    op monitor interval=20 on-fail=restart timeout=60 \
    op start interval=0 timeout=600 op stop interval=0 timeout=600 \
    --group g-<b>NW1</b>_ASCS
   
   sudo pcs resource create rsc_sap_<b>NW1</b>_ERS<b>02</b> SAPInstance \
    InstanceName=<b>NW1</b>_ERS02_<b>nw1-aers</b> START_PROFILE="/sapmnt/<b>NW1</b>/profile/<b>NW1</b>_ERS02_<b>nw1-aers</b>" \
    AUTOMATIC_RECOVER=false IS_ERS=true \
    op monitor interval=20 on-fail=restart timeout=60 op start interval=0 timeout=600 op stop interval=0 timeout=600 \
    --group g-<b>NW1</b>_AERS
      
   sudo pcs constraint colocation add g-<b>NW1</b>_AERS with g-<b>NW1</b>_ASCS -5000
   sudo pcs constraint location rsc_sap_<b>NW1</b>_ASCS<b>00</b> rule score=2000 runs_ers_<b>NW1</b> eq 1
   sudo pcs constraint order g-<b>NW1</b>_ASCS then g-<b>NW1</b>_AERS kind=Optional symmetrical=false
   
   sudo pcs node unstandby <b>nw1-cl-0</b>
   sudo pcs property set maintenance-mode=false
   </code></pre>

   SAP 引進了對佇列伺服器2的支援，包括複寫（從 SAP NW 7.52 開始）。 從 ABAP Platform 1809 開始，預設會安裝排入佇列的伺服器2。 請參閱適用于排入佇列伺服器2支援的 SAP 附注[2630416](https://launchpad.support.sap.com/#/notes/2630416) 。
   如果使用佇列伺服器2架構（[ENSA2](https://help.sap.com/viewer/cff8531bc1d9416d91bb6781e628d4e0/1709%20001/en-US/6d655c383abf4c129b0e5c8683e7ecd8.html)），請安裝資源代理程式 resource-agents-sap-4.1.1 12-01.2.2. el7. x86_64 或更新版本，並定義資源，如下所示：

<pre><code>sudo pcs property set maintenance-mode=true
   
   sudo pcs resource create rsc_sap_<b>NW1</b>_ASCS00 SAPInstance \
    InstanceName=<b>NW1</b>_ASCS00_<b>nw1-ascs</b> START_PROFILE="/sapmnt/<b>NW1</b>/profile/<b>NW1</b>_ASCS00_<b>nw1-ascs</b>" \
    AUTOMATIC_RECOVER=false \
    meta resource-stickiness=5000 migration-threshold=1 failure-timeout=60 \
    op monitor interval=20 on-fail=restart timeout=60 \
    op start interval=0 timeout=600 op stop interval=0 timeout=600 \
    --group g-<b>NW1</b>_ASCS
   
   sudo pcs resource create rsc_sap_<b>NW1</b>_ERS<b>02</b> SAPInstance \
    InstanceName=<b>NW1</b>_ERS02_<b>nw1-aers</b> START_PROFILE="/sapmnt/<b>NW1</b>/profile/<b>NW1</b>_ERS02_<b>nw1-aers</b>" \
    AUTOMATIC_RECOVER=false IS_ERS=true \
    op monitor interval=20 on-fail=restart timeout=60 op start interval=0 timeout=600 op stop interval=0 timeout=600 \
    --group g-<b>NW1</b>_AERS
      
   sudo pcs constraint colocation add g-<b>NW1</b>_AERS with g-<b>NW1</b>_ASCS -5000
   sudo pcs constraint order g-<b>NW1</b>_ASCS then g-<b>NW1</b>_AERS kind=Optional symmetrical=false
   sudo pcs constraint order start g-<b>NW1</b>_ASCS then stop g-<b>NW1</b>_AERS symmetrical=false
   
   sudo pcs node unstandby <b>nw1-cl-0</b>
   sudo pcs property set maintenance-mode=false
   </code></pre>

   如果您要從舊版升級並切換至排入佇列伺服器2，請參閱 SAP 附注[2641322](https://launchpad.support.sap.com/#/notes/2641322)。 

   > [!NOTE]
   > 上述設定中的超時只是範例，可能需要針對特定的 SAP 設定進行調整。 

   請確定叢集狀態正常，且所有資源皆已啟動。 資源在哪一個節點上執行並不重要。

   <pre><code>sudo pcs status
   
   # Online: [ <b>nw1-cl-0</b> <b>nw1-cl-1</b> ]
   #
   # Full list of resources:
   #
   # rsc_st_azure    (stonith:fence_azure_arm):      Started <b>nw1-cl-0</b>
   #  Resource Group: g-<b>NW1</b>_ASCS
   #      fs_<b>NW1</b>_ASCS        (ocf::heartbeat:Filesystem):    Started <b>nw1-cl-1</b>
   #      nc_<b>NW1</b>_ASCS        (ocf::heartbeat:azure-lb):      Started <b>nw1-cl-1</b>
   #      vip_<b>NW1</b>_ASCS       (ocf::heartbeat:IPaddr2):       Started <b>nw1-cl-1</b>
   #      rsc_sap_<b>NW1</b>_ASCS00 (ocf::heartbeat:SAPInstance):   Started <b>nw1-cl-1</b>
   #  Resource Group: g-<b>NW1</b>_AERS
   #      fs_<b>NW1</b>_AERS        (ocf::heartbeat:Filesystem):    Started <b>nw1-cl-0</b>
   #      nc_<b>NW1</b>_AERS        (ocf::heartbeat:azure-lb):      Started <b>nw1-cl-0</b>
   #      vip_<b>NW1</b>_AERS       (ocf::heartbeat:IPaddr2):       Started <b>nw1-cl-0</b>
   #      rsc_sap_<b>NW1</b>_ERS02  (ocf::heartbeat:SAPInstance):   Started <b>nw1-cl-0</b>
   </code></pre>

1. **[A]** 在兩個節點上為 ASCS 和 ERS 新增防火牆規則

   <pre><code># Probe Port of ASCS
   sudo firewall-cmd --zone=public --add-port=620<b>00</b>/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=620<b>00</b>/tcp
   sudo firewall-cmd --zone=public --add-port=32<b>00</b>/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=32<b>00</b>/tcp
   sudo firewall-cmd --zone=public --add-port=36<b>00</b>/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=36<b>00</b>/tcp
   sudo firewall-cmd --zone=public --add-port=39<b>00</b>/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=39<b>00</b>/tcp
   sudo firewall-cmd --zone=public --add-port=81<b>00</b>/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=81<b>00</b>/tcp
   sudo firewall-cmd --zone=public --add-port=5<b>00</b>13/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=5<b>00</b>13/tcp
   sudo firewall-cmd --zone=public --add-port=5<b>00</b>14/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=5<b>00</b>14/tcp
   sudo firewall-cmd --zone=public --add-port=5<b>00</b>16/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=5<b>00</b>16/tcp
   # Probe Port of ERS
   sudo firewall-cmd --zone=public --add-port=621<b>02</b>/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=621<b>02</b>/tcp
   sudo firewall-cmd --zone=public --add-port=33<b>02</b>/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=33<b>02</b>/tcp
   sudo firewall-cmd --zone=public --add-port=5<b>02</b>13/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=5<b>02</b>13/tcp
   sudo firewall-cmd --zone=public --add-port=5<b>02</b>14/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=5<b>02</b>14/tcp
   sudo firewall-cmd --zone=public --add-port=5<b>02</b>16/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=5<b>02</b>16/tcp
   </code></pre>

## <a name="2d6008b0-685d-426c-b59e-6cd281fd45d7"></a>SAP NetWeaver 應用程式伺服器準備

某些資料庫需要在應用程式伺服器上執行資料庫執行個體安裝。 準備應用程式伺服器虛擬機器，以便在這些情況下使用它們。

以下步驟假設您將應用程式伺服器安裝在與 ASCS/SCS 和 HANA 伺服器不同的伺服器上。 否則，您就不必進行以下某些步驟 (例如設定主機名稱解析)。

1. 設定主機名稱解析

   您可以使用 DNS 伺服器，或修改所有節點上的 /etc/hosts。 這個範例示範如何使用 /etc/hosts 檔案。
   取代下列命令中的 IP 位址和主機名稱

   <pre><code>sudo vi /etc/hosts
   </code></pre>

   將下列幾行插入至 /etc/hosts。 變更 IP 位址和主機名稱以符合您的環境

   <pre><code># IP addresses of the GlusterFS nodes
   <b>10.0.0.40 glust-0</b>
   <b>10.0.0.41 glust-1</b>
   <b>10.0.0.42 glust-2</b>
   # IP address of the load balancer frontend configuration for SAP NetWeaver ASCS
   <b>10.0.0.7 nw1-ascs</b>
   # IP address of the load balancer frontend configuration for SAP NetWeaver ASCS ERS
   <b>10.0.0.8 nw1-aers</b>
   # IP address of the load balancer frontend configuration for database
   <b>10.0.0.13 nw1-db</b>
   </code></pre>

1. 建立 sapmnt 目錄

   <pre><code>sudo mkdir -p /sapmnt/<b>NW1</b>
   sudo mkdir -p /usr/sap/trans

   sudo chattr +i /sapmnt/<b>NW1</b>
   sudo chattr +i /usr/sap/trans
   </code></pre>

1. 安裝 GlusterFS 用戶端和其他需求

   <pre><code>sudo yum -y install glusterfs-fuse uuidd
   </code></pre>

1. 新增掛接項目

   <pre><code>sudo vi /etc/fstab
   
   # Add the following lines to fstab, save and exit
   <b>glust-0</b>:/<b>NW1</b>-sapmnt /sapmnt/<b>NW1</b> glusterfs backup-volfile-servers=<b>glust-1:glust-2</b> 0 0
   <b>glust-0</b>:/<b>NW1</b>-trans /usr/sap/trans glusterfs backup-volfile-servers=<b>glust-1:glust-2</b> 0 0
   </code></pre>

   掛接新的共用項目

   <pre><code>sudo mount -a
   </code></pre>

1. 設定分頁檔
 
   <pre><code>
   sudo vi /etc/waagent.conf
   
   # Set the property ResourceDisk.EnableSwap to y
   # Create and use swapfile on resource disk.
   ResourceDisk.EnableSwap=<b>y</b>
   
   # Set the size of the SWAP file with property ResourceDisk.SwapSizeMB
   # The free space of resource disk varies by virtual machine size. Make sure that you do not set a value that is too big. You can check the SWAP space with command swapon
   # Size of the swapfile.
   ResourceDisk.SwapSizeMB=<b>2000</b>
   </code></pre>

   重新啟動代理程式以啟動變更

   <pre><code>
   sudo service waagent restart
   </code></pre>

## <a name="install-database"></a>安裝資料庫

在此範例中，SAP NetWeaver 安裝在 SAP Hana 上。 您可以針對此安裝使用每個支援的資料庫。 如需有關如何在 Azure 中安裝 SAP Hana 的詳細資訊，請參閱[Red Hat Enterprise Linux. For a list of supported databases, see [SAP Note 1928533][1928533]上的 Azure vm SAP Hana 高可用性][sap-hana-ha]。

1. 執行 SAP 資料庫執行個體安裝

   以 root 身分使用虛擬主機名稱 (對應至資料庫負載平衡器前端組態的 IP 位址，例如 <b>nw1-db</b> 和 <b>10.0.0.13</b>) 來安裝 SAP NetWeaver 資料庫執行個體。

   您可以使用 sapinst 參數 SAPINST_REMOTE_ACCESS_USER 來允許非 root 使用者連線到 sapinst。

   <pre><code>
   sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b>
   </code></pre>

## <a name="sap-netweaver-application-server-installation"></a>SAP NetWeaver 應用程式伺服器安裝

請遵循下列步驟來安裝 SAP 應用程式伺服器。

1. 準備應用程式伺服器

   依照上述 [SAP NetWeaver 應用程式伺服器準備](high-availability-guide-rhel.md#2d6008b0-685d-426c-b59e-6cd281fd45d7)章節中的步驟，準備應用程式伺服器。

1. 安裝 SAP NetWeaver 應用程式伺服器

   安裝主要或其他的 SAP NetWeaver 應用程式伺服器。

   您可以使用 sapinst 參數 SAPINST_REMOTE_ACCESS_USER 來允許非 root 使用者連線到 sapinst。

   <pre><code>
   sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b>
   </code></pre>

1. 更新 SAP HANA 安全存放區

   將 SAP HANA 安全存放區更新為指向 SAP HANA 系統複寫設定的虛擬名稱。

   執行下列命令來將項目列出為 \<sapsid>adm

   <pre><code>hdbuserstore List
   </code></pre>

   這樣應該會列出所有項目，且看起來應該類似
   <pre><code>
   DATA FILE       : /home/nw1adm/.hdb/nw1-di-0/SSFS_HDB.DAT
   KEY FILE        : /home/nw1adm/.hdb/nw1-di-0/SSFS_HDB.KEY
   
   KEY DEFAULT
     ENV : 10.0.0.14:<b>30313</b>
     USER: <b>SAPABAP1</b>
     DATABASE: <b>NW1</b>
   </code></pre>

   輸出會顯示預設項目的 IP 位址指向虛擬機器，而不是指向負載平衡器的 IP 位址。 這個項目必須變更才能指向負載平衡器的虛擬機器主機名稱。 請務必使用相同的連接埠 (上述輸出中的 **30313**) 和資料庫名稱 (上述輸出中的 **HN1**)！

   <pre><code>su - <b>nw1</b>adm
   hdbuserstore SET DEFAULT <b>nw1-db</b>:<b>30313@NW1</b> <b>SAPABAP1</b> <b>&lt;password of ABAP schema&gt;</b>
   </code></pre>

## <a name="test-the-cluster-setup"></a>測試叢集設定

1. 手動移轉 ASCS 執行個體

   開始測試之前的資源狀態：

   <pre><code>rsc_st_azure    (stonith:fence_azure_arm):      Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
   </code></pre>

   以 root 身份執行下列命令，來遷移 ASCS 執行個體。

   <pre><code>[root@nw1-cl-0 ~]# pcs resource move rsc_sap_NW1_ASCS00
   
   [root@nw1-cl-0 ~]# pcs resource clear rsc_sap_NW1_ASCS00
   
   # Remove failed actions for the ERS that occurred as part of the migration
   [root@nw1-cl-0 ~]# pcs resource cleanup rsc_sap_NW1_ERS02
   </code></pre>

   測試完成之後的資源狀態：

   <pre><code>rsc_st_azure    (stonith:fence_azure_arm):      Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
   </code></pre>

1. 模擬節點損毀

   開始測試之前的資源狀態：

   <pre><code>rsc_st_azure    (stonith:fence_azure_arm):      Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
   </code></pre>

   以 root 身份在執行 ASCS 執行個體的節點上執行下列命令

   <pre><code>[root@nw1-cl-1 ~]# echo b > /proc/sysrq-trigger
   </code></pre>

   節點在重新啟動後的狀態應該會像下面這樣。

   <pre><code>Online: [ nw1-cl-0 nw1-cl-1 ]
   
   Full list of resources:
   
   rsc_st_azure    (stonith:fence_azure_arm):      Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
   
   Failed Actions:
   * rsc_sap_NW1_ERS02_monitor_11000 on nw1-cl-0 'not running' (7): call=45, status=complete, exitreason='',
       last-rc-change='Tue Aug 21 13:52:39 2018', queued=0ms, exec=0ms
   </code></pre>

   使用下列命令來清除失敗的資源。

   <pre><code>[root@nw1-cl-0 ~]# pcs resource cleanup rsc_sap_NW1_ERS02
   </code></pre>

   測試完成之後的資源狀態：

   <pre><code>rsc_st_azure    (stonith:fence_azure_arm):      Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
   </code></pre>

1. 終止訊息伺服器處理程序

   開始測試之前的資源狀態：

   <pre><code>rsc_st_azure    (stonith:fence_azure_arm):      Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
   </code></pre>

   以 root 身份執行下列命令，找出訊息伺服器的處理程序，並將其終止。

   <pre><code>[root@nw1-cl-0 ~]# pgrep ms.sapNW1 | xargs kill -9
   </code></pre>

   如果您只終止訊息伺服器一次，則會 `sapstart`重新開機它。 如果您終止伺服器的次數足夠，則 Pacemaker 最終會將 ASCS 執行個體移到另一個節點。 以 root 身份執行下列命令，以在測試之後清除 ASCS 和 ERS 執行個體的資源狀態。

   <pre><code>[root@nw1-cl-0 ~]# pcs resource cleanup rsc_sap_NW1_ASCS00
   [root@nw1-cl-0 ~]# pcs resource cleanup rsc_sap_NW1_ERS02
   </code></pre>

   測試完成之後的資源狀態：

   <pre><code>rsc_st_azure    (stonith:fence_azure_arm):      Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
   </code></pre>

1. 終止佇列伺服器處理程序

   開始測試之前的資源狀態：

   <pre><code>rsc_st_azure    (stonith:fence_azure_arm):      Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
   </code></pre>

   以 root 身份在執行 ASCS 執行個體的節點上執行下列命令，以終止佇列伺服器。

   <pre><code>[root@nw1-cl-1 ~]# pgrep en.sapNW1 | xargs kill -9
   </code></pre>

   ASCS 執行個體應會立即容錯移轉到另一個節點。 在 ASCS 執行個體啟動之後，ERS 執行個體應該也會進行容錯移轉。 以 root 身份執行下列命令，以在測試之後清除 ASCS 和 ERS 執行個體的資源狀態。

   <pre><code>[root@nw1-cl-0 ~]# pcs resource cleanup rsc_sap_NW1_ASCS00
   [root@nw1-cl-0 ~]# pcs resource cleanup rsc_sap_NW1_ERS02
   </code></pre>

   測試完成之後的資源狀態：

   <pre><code>rsc_st_azure    (stonith:fence_azure_arm):      Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
   </code></pre>

1. 終止佇列複寫伺服器處理程序

   開始測試之前的資源狀態：

   <pre><code>rsc_st_azure    (stonith:fence_azure_arm):      Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
   </code></pre>

   以 root 身份在執行 ERS 執行個體的節點上執行下列命令，以終止佇列複寫伺服器處理程序。

   <pre><code>[root@nw1-cl-1 ~]# pgrep er.sapNW1 | xargs kill -9
   </code></pre>

   如果您只執行一次命令，`sapstart` 將會重新開機進程。 如果您經常執行它，`sapstart` 將不會重新開機進程，而且資源會處於停止狀態。 以 root 身份執行下列命令，以在測試之後清除 ERS 執行個體的資源狀態。

   <pre><code>[root@nw1-cl-0 ~]# pcs resource cleanup rsc_sap_NW1_ERS02
   </code></pre>

   測試完成之後的資源狀態：

   <pre><code>rsc_st_azure    (stonith:fence_azure_arm):      Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
   </code></pre>

1. 終止佇列 sapstartsrv 程序

   開始測試之前的資源狀態：

   <pre><code>rsc_st_azure    (stonith:fence_azure_arm):      Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
   </code></pre>

   以 root 身份在執行 ASCS 的節點上執行下列命令。

   <pre><code>[root@nw1-cl-0 ~]# pgrep -fl ASCS00.*sapstartsrv
   # 59545 sapstartsrv
   
   [root@nw1-cl-0 ~]# kill -9 59545
   </code></pre>

   應做為監視程序的一部分，一律由 Pacemaker 資源代理程式來重新啟動 sapstartsrv 處理序。 測試完成之後的資源狀態：

   <pre><code>rsc_st_azure    (stonith:fence_azure_arm):      Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
   </code></pre>

## <a name="next-steps"></a>後續步驟

* [適用于 SAP 的 Azure 虛擬機器規劃和執行][planning-guide]
* [適用于 SAP 的 Azure 虛擬機器部署][deployment-guide]
* [適用于 SAP 的 Azure 虛擬機器 DBMS 部署][dbms-guide]
* 若要了解如何建立高可用性並為 Azure 上的 SAP HANA 規劃災害復原，請參閱 [Azure 上的 SAP HANA (大型執行個體) 高可用性和災害復原](hana-overview-high-availability-disaster-recovery.md)。
* 若要瞭解如何建立高可用性並規劃 Azure Vm 上 SAP Hana 的嚴重損壞修復，請參閱[azure 虛擬機器（vm）上 SAP Hana 的高可用性][sap-hana-ha]
