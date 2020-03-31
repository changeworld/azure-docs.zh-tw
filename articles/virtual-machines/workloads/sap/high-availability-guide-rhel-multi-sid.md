---
title: RHEL 多 SID 指南上的 SAP NW 的 Azure VM 高可用性 |微軟文檔
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
ms.date: 03/24/2020
ms.author: radeltch
ms.openlocfilehash: 4f1bfd58e27f0cd677980ff9351d32d91a68e3e6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80247430"
---
# <a name="high-availability-for-sap-netweaver-on-azure-vms-on-red-hat-enterprise-linux-for-sap-applications-multi-sid-guide"></a>用於 SAP 應用程式多 SID 指南的紅帽企業 Linux 上的 Azure VM 上的 SAP NetWeaver 的高可用性

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md

[anf-azure-doc]:https://docs.microsoft.com/azure/azure-netapp-files/
[anf-avail-matrix]:https://azure.microsoft.com/global-infrastructure/services/?products=storage&regions=all
[anf-register]:https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-register
[anf-sap-applications-azure]:https://www.netapp.com/us/media/tr-4746.pdf

[2002167]:https://launchpad.support.sap.com/#/notes/2002167
[2009879]:https://launchpad.support.sap.com/#/notes/2009879
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[1410736]:https://launchpad.support.sap.com/#/notes/1410736

[sap-swcenter]:https://support.sap.com/en/my-support/software-downloads.html

[template-multisid-xscs]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs-md%2Fazuredeploy.json

[sap-hana-ha]:sap-hana-high-availability-rhel.md
[glusterfs-ha]:high-availability-guide-rhel-glusterfs.md

本文介紹如何在 Azure VM 上的兩個節點群集中部署多個 SAP NetWeaver 高度可用的系統（即多 SID），以及用於 SAP 應用程式的紅帽企業 Linux 的兩個節點群集。  

在示例配置中，安裝命令等三個 SAP NetWeaver 7.50 系統部署在一個、兩個節點的高可用性群集中。 SAP 系統 S 是：
* **NW1：ASCS**實例編號**00**和虛擬主機名稱**msnw1ascs;** ERS 實例編號**02**和虛擬主機名稱**msnw1ers**。  
* **NW2：ASCS**實例編號**10**和虛擬主機名稱**msnw2ascs;** ERS實例編號**12**和虛擬主機名稱**msnw2ers。**  
* **NW3**：ASCS 實例編號**20**和虛擬主機名稱**msnw3ascs;** ERS實例編號**22**和虛擬主機名稱**msnw3ers。**  

本文不介紹資料庫層和 SAP NFS 共用的部署。 在本文中的示例中，我們使用[Azure NetApp 檔](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-volumes)卷**sapMSID**用於 NFS 共用，前提是該卷已部署。 我們還假設，Azure NetApp 檔卷是使用 NFSv3 協定部署的，並且 SAP 系統 NW1、NW2 和 NW3 的 ASCS 和 ERS 實例的群集資源存在以下檔路徑：  

* 體積汁液 （nfs://10.42.0.4/sapmnt<b>NW1</b>）
* 體積液化（nfs://10.42.0.4/usrsap<b>NW1</b>ascs）
* 體積汁液（nfs://10.42.0.4/usrsap<b>NW1</b>系統）
* 體積汁液（nfs://10.42.0.4/usrsap<b>NW1</b>ers）
* 體積汁液 （nfs://10.42.0.4/sapmnt<b>NW2</b>）
* 體積液化（nfs://10.42.0.4/usrsap<b>NW2</b>ascs）
* 體積汁液（nfs://10.42.0.4/usrsap<b>NW2</b>系統）
* 體積汁液（nfs://10.42.0.4/usrsap<b>NW2</b>ers）
* 體積液塞<b>（nfs://10.42.0.4/sapmntNW3）</b>
* 體積液化（nfs://10.42.0.4/usrsap<b>NW3</b>ascs）
* 體積汁液（nfs://10.42.0.4/usrsap<b>NW3</b>系統）
* 體積汁液（nfs://10.42.0.4/usrsap<b>NW3</b>ers）

在開始之前，請先參考以下 SAP 說明和論文：

* SAP Note [1928533]，其中包含：
  * SAP 軟體部署支援的 Azure VM 大小清單
  * Azure VM 大小的重要容量資訊
  * 支援的 SAP 軟體，以及作業系統 (OS) 與資料庫組合
  * Microsoft Azure 上 Windows 和 Linux 所需的 SAP 核心版本
* [Azure NetApp 檔文檔][anf-azure-doc]
* SAP Note [2015553] 列出 Azure 中 SAP 支援的 SAP 軟體部署先決條件。
* SAP Note [2002167] 建議適用於 Red Hat Enterprise Linux 的作業系統設定
* SAP Note [2009879] 提供適用於 Red Hat Enterprise Linux 的 SAP HANA 方針
* SAP Note [2178632] 包含在 Azure 中針對 SAP 回報的所有監視計量詳細資訊。
* SAP Note [2191498] 包含 Azure 中 Linux 所需的 SAP Host Agent 版本。
* SAP Note [2243692] 包含 Azure 中 Linux 上的 SAP 授權相關資訊。
* SAP Note [1999351] 包含 Azure Enhanced Monitoring Extension for SAP 的其他疑難排解資訊。
* [SAP Community WIKI](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) 包含 Linux 所需的所有 SAP Note。
* [適用於 SAP on Linux 的 Azure 虛擬機器規劃和實作][planning-guide]
* [在 Linux 上為 SAP 進行 Azure 虛擬機器部署][deployment-guide]
* [適用於 SAP on Linux 的 Azure 虛擬機器 DBMS 部署][dbms-guide]
* [Pacemaker 叢集中的 SAP Netweaver](https://access.redhat.com/articles/3150081)
* 一般 RHEL 文件
  * [高可用性附加元件概觀](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_overview/index)
  * [高可用性附加元件管理](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_administration/index)
  * [高可用性附加元件參考](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_reference/index)
  * [在 RHEL 7.5 中使用獨立資源為 SAP Netweaver 設定 ASCS/ERS](https://access.redhat.com/articles/3569681)
  * [在 RHEL 上使用獨立佇列伺服器 2 （ENSA2） 配置 SAP S/4HANA ASCS/ERS](https://access.redhat.com/articles/3974941)
* 特定于 Azure 的 RHEL 文檔：
  * [RHEL 高可用性叢集的支援原則：以 Microsoft Azure 虛擬機器作為叢集成員](https://access.redhat.com/articles/3131341)
  * [在 Microsoft Azure 上安裝和設定 Red Hat Enterprise Linux 7.4 (和更新版本) 高可用性叢集](https://access.redhat.com/articles/3252491)
* [使用 Azure NetApp 檔在 Microsoft Azure 上使用 NetApp SAP 應用程式][anf-sap-applications-azure]

## <a name="overview"></a>總覽

參與群集的虛擬機器必須調整大小才能運行所有資源，以防發生容錯移轉。 在多 SID 高可用性群集中，每個 SAP SID 可以獨立地進行容錯移轉。  

為了實現高可用性，SAP NetWeaver 需要高可用性股票。 在本文檔中，我們將使用[Azure NetApp 檔 NFS 卷](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-volumes)上部署的 SAP 共用來演示示例。 也可以託管在高可用[GlusterFS 群集](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-glusterfs)上的共用，該群集可供多個 SAP 系統使用。  

![SAP NetWeaver 高可用性概觀](./media/high-availability-guide-rhel/ha-rhel-multi-sid.png)

> [!IMPORTANT]
> 在 Azure VM 中，支援將帶有紅帽 Linux 的 SAP ASCS/ERS 作為客體作業系統的多 SID 群集僅限於同一群集上的**五**個 SAP SID。 每個新的 SID 都會增加複雜性。 不支援將 SAP 佇列複製伺服器 1 和同一群集上的佇列複製伺服器 2 混合**在**一起。 多 SID 群集描述在一個起搏器群集中安裝多個具有不同 SID 的 SAP ASCS/ERS 實例。 目前，僅支援 ASCS/ERS 的多 SID 群集。  

> [!TIP]
> SAP ASCS/ERS 的多 SID 群集是一種具有更高複雜性的解決方案。 實施工作更為複雜。 在執行維護活動（如作業系統修補）時，它還涉及更高的管理工作量。 在開始實際實現之前，請花些時間仔細規劃部署以及所有相關元件，如 VM、NFS 裝載、VIP、負載等化器配置等。  

SAP NetWeaver ASCS、SAP NetWeaver SCS 和 SAP NetWeaver ERS 使用虛擬主機名稱和虛擬 IP 位址。 在 Azure 上必須有負載平衡器才能使用虛擬 IP 位址。 我們建議使用[標準負載等化器](https://docs.microsoft.com/azure/load-balancer/quickstart-load-balancer-standard-public-portal)。  

下面的清單顯示了此多 SID 群集示例（具有三個 SAP 系統的）A）SCS 和 ERS 負載等化器的配置。 對於每個 ASCS 和 ERS 實例，您將需要單獨的前端 IP、運行狀況探測器和負載平衡規則。 將所有作為 ASCS/ASCS 群集一部分的 VM 分配給單個 ILB 的一個後端池。  

### <a name="ascs"></a>(A)SCS

* 前端組態
  * NW1 的 IP 位址： 10.3.1.50
  * NW2 的 IP 位址： 10.3.1.52
  * NW3 的 IP 位址： 10.3.1.54

* 探頭埠
  * 埠 620<strong>&lt;&gt;nr</strong>，因此用於 NW1、NW2 和 NW3 探頭埠 620**00、620****10**和 620**20**
* 負載平衡規則 - 為每個實例創建一個實例，即 NW1/ASCS、NW2/ASCS 和 NW3/ASCS。
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
  * NW1 10.3.1.51 的 IP 位址
  * NW2 10.3.1.53 的 IP 位址
  * NW3 10.3.1.55 的 IP 位址

* 探查連接埠
  * 埠 621<strong>&lt;&gt;nr</strong>，因此用於 NW1、NW2 和 N3 探頭埠 621**02、621****12**和 621**22**
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

## <a name="sap-shares"></a>SAP 股份

SAP NetWeaver 需要傳輸、設定檔目錄等的共用存儲。 對於高可用的 SAP 系統，擁有高可用共用非常重要。 您需要決定 SAP 共用的體系結構。 一個選項是在[Azure NetApp 檔 NFS 卷](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-volumes)上部署共用。  使用 Azure NetApp 檔，SAP NFS 共用將獲得內置的高可用性。

另一個選項是在[紅帽企業Linux上的 Azure VM 上為 SAP NetWeaver 構建 GlusterFS，](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-glusterfs)可在多個 SAP 系統之間共用。 

## <a name="deploy-the-first-sap-system-in-the-cluster"></a>部署群集中的第一個 SAP 系統

現在，您已經決定了 SAP 共用的體系結構，按照相應的文檔部署群集中的第一個 SAP 系統。

* 如果使用 Azure NetApp 檔 NFS 卷，請按照 Azure VM 在[紅帽企業 Linux 上使用用於 SAP 應用程式的 Azure NetApp 檔，在紅帽企業 Linux 上為 SAP NetWeaver 提供高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files)  
* 如果使用 GlusterFS 群集，請按照[紅帽企業 Linux 上的 Azure VM 上的 GlusterFS 進行 SAP NetWeaver。](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-glusterfs)  

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

1. 按照指示通過[Azure 門戶手動部署 Azure 負載等化器](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files#deploy-linux-manually-via-azure-portal)，將新部署的系統（即**NW2、NW3）** 的配置添加到現有的 Azure 負載等化器。 **NW3** 調整配置的 IP 位址、運行狀況探測埠和負載平衡規則。  

2. **[A]** 其他 SAP 系統的設置名稱解析。 您可以使用 DNS 伺服器或修改`/etc/hosts`所有節點。 此示例演示如何使用該檔`/etc/hosts`。  根據環境調整 IP 位址和主機名稱。 

    ```
    sudo vi /etc/hosts
    # IP address of the load balancer frontend configuration for NW2 ASCS
    10.3.1.52 msnw2ascs
    # IP address of the load balancer frontend configuration for NW3 ASCS
    10.3.1.54 msnw3ascs
    # IP address of the load balancer frontend configuration for NW2 ERS
    10.3.1.53 msnw2ers
    # IP address of the load balancer frontend configuration for NW3 ERS
    10.3.1.55 msnw3ers
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

4. **[A]** 為要部署到群集的其他 SAP 系統添加 /sapmnt/SID 和 /usr/sap/SID/SYS 檔案系統的裝載條目。 在此示例中 **，NW2**和**NW3**。  

   使用檔案系統`/etc/fstab`更新要部署到群集的其他 SAP 系統的檔。  

   * 如果使用 Azure NetApp 檔，請按照[此處](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files#prepare-for-sap-netweaver-installation)的說明操作  
   * 如果使用 GlusterFS 群集，請按照[此處](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel#prepare-for-sap-netweaver-installation)的說明操作  

### <a name="install-ascs--ers"></a>安裝 ASCS / ERS

1. 為要部署到群集的其他 SAP 系統的 ASCS 實例創建虛擬 IP 和運行狀況探測群集資源。 此處顯示的示例適用于**NW2**和**NW3** ASCS，使用具有 NFSv3 協定的 Azure NetApp 檔卷上的 NFS。  

    ```
    sudo pcs resource create fs_NW2_ASCS Filesystem device='10.42.0.4:/sapMSIDR/usrsapNW2ascs' \
    directory='/usr/sap/NW2/ASCS10' fstype='nfs' force_unmount=safe \
    op start interval=0 timeout=60 op stop interval=0 timeout=120 op monitor interval=200 timeout=40 \
     --group g-NW2_ASCS

    sudo pcs resource create vip_NW2_ASCS IPaddr2 \
    ip=10.3.1.52 cidr_netmask=24 \
     --group g-NW2_ASCS
    
    sudo pcs resource create nc_NW2_ASCS azure-lb port=62010 \
     --group g-NW2_ASCS

    sudo pcs resource create fs_NW3_ASCS Filesystem device='10.42.0.4:/sapMSIDR/usrsapNW3ascs' \
    directory='/usr/sap/NW3/ASCS20' fstype='nfs' force_unmount=safe \
    op start interval=0 timeout=60 op stop interval=0 timeout=120 op monitor interval=200 timeout=40 \
    --group g-NW3_ASCS

    sudo pcs resource create vip_NW3_ASCS IPaddr2 \
    ip=10.3.1.54 cidr_netmask=24 \
    --group g-NW3_ASCS

    sudo pcs resource create nc_NW3_ASCS azure-lb port=62020 \
    --group g-NW3_ASCS
    ```

   確保群集狀態正常，並且所有資源都已啟動。 資源在哪一個節點上執行並不重要。  

2. **[1]** 安裝 SAP NetWeaver ASCS  

   使用映射到 ASCS 負載等化器前端配置的 IP 位址的虛擬主機名稱，將 SAP NetWeaver ASCS 安裝為根。 例如，對於系統**NW2，** 虛擬主機名稱為 msnw2ascs，10.3.1.52 和用於負載等化器探測的實例編號，例如<b>10</b>。 <b>msnw2ascs</b> <b>10.3.1.52</b> 對於系統**NW3，** 虛擬主機名稱是 msnw3ascs，10.3.1.54 和用於負載等化器探測的實例編號，例如<b>msnw3ascs</b><b>20</b>。 <b>10.3.1.54</b> 請記下為每個 SAP SID 安裝 ASCS 的叢集節點。  

   您可以使用 sapinst 參數 SAPINST_REMOTE_ACCESS_USER 來允許非 root 使用者連線到 sapinst。 您可以使用參數SAPINST_USE_HOSTNAME使用虛擬主機名稱安裝 SAP。  

    ```
    # Allow access to SWPM. This rule is not permanent. If you reboot the machine, you have to run the command again
    sudo firewall-cmd --zone=public --add-port=4237/tcp
    sudo swpm/sapinst SAPINST_REMOTE_ACCESS_USER=sapadmin SAPINST_USE_HOSTNAME=virtual_hostname
    ```

   如果安裝未能在 /usr/sap/SID /ASCS**SID****實例*** 中創建子資料夾，請嘗試將擁有者設置為**sid**adm 和組以 SCS**實例*** 的 sapsys 進行重試。

3. **[1]** 為要部署到群集的其他 SAP 系統的 ERS 實例創建虛擬 IP 和運行狀況探測群集資源。 此處顯示的示例適用于**NW2**和**NW3** ERS，使用具有 NFSv3 協定的 Azure NetApp 檔卷上的 NFS。  

    ```
    sudo pcs resource create fs_NW2_AERS Filesystem device='10.42.0.4:/sapMSIDR/usrsapNW2ers' \
    directory='/usr/sap/NW2/ERS12' fstype='nfs' force_unmount=safe \
    op start interval=0 timeout=60 op stop interval=0 timeout=120 op monitor interval=200 timeout=40 \
     --group g-NW2_AERS

    sudo pcs resource create vip_NW2_AERS IPaddr2 \
    ip=10.3.1.53 cidr_netmask=24 \
     --group g-NW2_AERS

    sudo pcs resource create nc_NW2_AERS azure-lb port=62112 \
     --group g-NW2_AERS

    sudo pcs resource create fs_NW3_AERS Filesystem device='10.42.0.4:/sapMSIDR/usrsapNW3ers' \
    directory='/usr/sap/NW3/ERS22' fstype='nfs' force_unmount=safe \
    op start interval=0 timeout=60 op stop interval=0 timeout=120 op monitor interval=200 timeout=40 \
     --group g-NW3_AERS

    sudo pcs resource create vip_NW3_AERS IPaddr2 \
    ip=10.3.1.55 cidr_netmask=24 \
     --group g-NW3_AERS

    sudo pcs resource create nc_NW3_AERS azure-lb port=62122 \
     --group g-NW3_AERS
   ```

   確保群集狀態正常，並且所有資源都已啟動。  

   接下來，確保新創建的 ERS 組的資源在叢集節點上運行，與安裝同一 SAP 系統的 ASCS 實例的叢集節點相反。  例如，如果 NW2 ASCS 安裝在`rhelmsscl1`上，則請確保 NW2 ERS 組`rhelmsscl2`在 上運行。  您可以通過為組中的一個群集資源運行`rhelmsscl2`以下命令，將 NW2 ERS 組遷移到： 

    ```
      pcs resource move fs_NW2_AERS rhelmsscl2
    ```

4. **[2]** 安裝 SAP NetWeaver ERS

   使用虛擬主機名稱將 SAP NetWeaver ERS 作為根安裝到 ERS 的負載等化器前端配置的 IP 位址。 例如，對於系統**NW2，** 虛擬主機名稱將是 msnw2ers，10.3.1.53 和用於負載等化器探測的實例編號，例如<b>msnw2ers</b><b>12</b>。 <b>10.3.1.53</b> 對於系統**NW3，** 虛擬主機名稱<b>msnw3ers</b> <b>，10.3.1.55</b>和用於負載等化器探測的實例編號，例如<b>22</b>。 

   您可以使用 sapinst 參數 SAPINST_REMOTE_ACCESS_USER 來允許非 root 使用者連線到 sapinst。 您可以使用參數SAPINST_USE_HOSTNAME使用虛擬主機名稱安裝 SAP。  

    ```
    # Allow access to SWPM. This rule is not permanent. If you reboot the machine, you have to run the command again
    sudo firewall-cmd --zone=public --add-port=4237/tcp
    sudo swpm/sapinst SAPINST_REMOTE_ACCESS_USER=sapadmin SAPINST_USE_HOSTNAME=virtual_hostname
    ```

   > [!NOTE]
   > 請使用 SWPM SP 20 PL 05 或更高版本。 較低版本無法正確設定權限，因而會讓安裝失敗。

   如果安裝未能在 /usr/sap/**NW2**/ERS**實例*** 中創建子資料夾，請嘗試將擁有者設置為**sid**adm，並將組設置為 ERS**實例*** 資料夾的 sapsys 並重試。

   如果需要將新部署的 SAP 系統的 ERS 組遷移到其他叢集節點，不要忘記刪除 ERS 組的位置約束。 可以通過運行以下命令來刪除約束（為 SAP 系統**NW2**和**NW3**提供了示例）。 請確保刪除用於移動 ERS 群集組的命令中使用的同一資源的臨時約束。

    ```
      pcs resource clear fs_NW2_AERS
      pcs resource clear fs_NW3_AERS
    ```

5. **[1]** 為新安裝的 SAP 系統調整 ASCS/SCS 和 ERS 實例設定檔。 下面顯示的示例用於 NW2。 您需要調整添加到群集的所有 SAP 實例的 ASCS/SCS 和 ERS 設定檔。  
 
   * ASCS/SCS 設定檔

      ```
      sudo vi /sapmnt/NW2/profile/NW2_ASCS10_msnw2ascs
   
      # Change the restart command to a start command
      #Restart_Program_01 = local $(_EN) pf=$(_PF)
      Start_Program_01 = local $(_EN) pf=$(_PF)

      # Add the keep alive parameter
      enque/encni/set_so_keepalive = true
      ```

   * ERS 設定檔

      ```
      sudo vi /sapmnt/NW2/profile/NW2_ERS12_msnw2ers
   
      # Change the restart command to a start command
      #Restart_Program_00 = local $(_ER) pf=$(_PFL) NR=$(SCSID)
      Start_Program_00 = local $(_ER) pf=$(_PFL) NR=$(SCSID)
   
      # remove Autostart from ERS profile
      # Autostart = 1
      ```

6. **[A]** 更新 /usr/sap/sapservices 檔案

   為了防止 sapinit 啟動腳本啟動實例，必須從`/usr/sap/sapservices`檔中注釋掉 Pacemaker 管理的所有實例。  下面所示的示例適用于 SAP 系統**NW2**和**NW3**。  

   ```
    # On the node where ASCS was installed, comment out the line for the ASCS instacnes
    #LD_LIBRARY_PATH=/usr/sap/NW2/ASCS10/exe:$LD_LIBRARY_PATH; export LD_LIBRARY_PATH; /usr/sap/NW2/ASCS10/exe/sapstartsrv pf=/usr/sap/NW2/SYS/profile/NW2_ASCS10_msnw2ascs -D -u nw2adm
    #LD_LIBRARY_PATH=/usr/sap/NW3/ASCS20/exe:$LD_LIBRARY_PATH; export LD_LIBRARY_PATH; /usr/sap/NW3/ASCS20/exe/sapstartsrv pf=/usr/sap/NW3/SYS/profile/NW3_ASCS20_msnw3ascs -D -u nw3adm

    # On the node where ERS was installed, comment out the line for the ERS instacnes
    #LD_LIBRARY_PATH=/usr/sap/NW2/ERS12/exe:$LD_LIBRARY_PATH; export LD_LIBRARY_PATH; /usr/sap/NW2/ERS12/exe/sapstartsrv pf=/usr/sap/NW2/ERS12/profile/NW2_ERS12_msnw2ers -D -u nw2adm
    #LD_LIBRARY_PATH=/usr/sap/NW3/ERS22/exe:$LD_LIBRARY_PATH; export LD_LIBRARY_PATH; /usr/sap/NW3/ERS22/exe/sapstartsrv pf=/usr/sap/NW3/ERS22/profile/NW3_ERS22_msnw3ers -D -u nw3adm
   ```

7. **[1]** 為新安裝的 SAP 系統創建 SAP 群集資源。  

   如果使用排隊伺服器 1 體系結構 （ENSA1），請定義 SAP 系統**NW2**和**NW3**的資源，如下所示：

    ```
     sudo pcs property set maintenance-mode=true

    sudo pcs resource create rsc_sap_NW2_ASCS10 SAPInstance \
    InstanceName=NW2_ASCS10_msnw2ascs START_PROFILE="/sapmnt/NW2/profile/NW2_ASCS10_msnw2ascs" \
    AUTOMATIC_RECOVER=false \
    meta resource-stickiness=5000 migration-threshold=1 failure-timeout=60 \
    op monitor interval=20 on-fail=restart timeout=60 \
    op start interval=0 timeout=600 op stop interval=0 timeout=600 \
     --group g-NW2_ASCS

    sudo pcs resource create rsc_sap_NW2_ERS12 SAPInstance \
    InstanceName=NW2_ERS12_msnw2ers START_PROFILE="/sapmnt/NW2/profile/NW2_ERS12_msnw2ers" \
    AUTOMATIC_RECOVER=false IS_ERS=true \
    op monitor interval=20 on-fail=restart timeout=60 op start interval=0 timeout=600 op stop interval=0 timeout=600 \
     --group g-NW2_AERS

    sudo pcs constraint colocation add g-NW2_AERS with g-NW2_ASCS -5000
    sudo pcs constraint location rsc_sap_NW2_ASCS10 rule score=2000 runs_ers_NW2 eq 1
    sudo pcs constraint order g-NW2_ASCS then g-NW2_AERS kind=Optional symmetrical=false

    sudo pcs resource create rsc_sap_NW3_ASCS20 SAPInstance \
    InstanceName=NW3_ASCS20_msnw3ascs START_PROFILE="/sapmnt/NW3/profile/NW3_ASCS20_msnw3ascs" \
    AUTOMATIC_RECOVER=false \
    meta resource-stickiness=5000 migration-threshold=1 failure-timeout=60 \
    op monitor interval=20 on-fail=restart timeout=60 \
    op start interval=0 timeout=600 op stop interval=0 timeout=600 \
     --group g-NW3_ASCS

    sudo pcs resource create rsc_sap_NW3_ERS22 SAPInstance \
    InstanceName=NW3_ERS22_msnw3ers START_PROFILE="/sapmnt/NW3/profile/NW2_ERS22_msnw3ers" \
    AUTOMATIC_RECOVER=false IS_ERS=true \
    op monitor interval=20 on-fail=restart timeout=60 op start interval=0 timeout=600 op stop interval=0 timeout=600 \
     --group g-NW3_AERS

    sudo pcs constraint colocation add g-NW3_AERS with g-NW3_ASCS -5000
    sudo pcs constraint location rsc_sap_NW3_ASCS20 rule score=2000 runs_ers_NW3 eq 1
    sudo pcs constraint order g-NW3_ASCS then g-NW3_AERS kind=Optional symmetrical=false

    sudo pcs property set maintenance-mode=false
    ```

   SAP 引入了對自 SAP NW 7.52 起對排隊伺服器 2（包括複製）的支援。 從 ABAP 平臺 1809 開始，預設情況下安裝排隊伺服器 2。 有關排隊伺服器 2 支援，請參閱 SAP 注釋[2630416。](https://launchpad.support.sap.com/#/notes/2630416)
   如果使用排隊伺服器 2 體系結構[（ENSA2](https://help.sap.com/viewer/cff8531bc1d9416d91bb6781e628d4e0/1709%20001/en-US/6d655c383abf4c129b0e5c8683e7ecd8.html)），請定義 SAP 系統**NW2**和**NW3**的資源，如下所示：

    ```
     sudo pcs property set maintenance-mode=true

    sudo pcs resource create rsc_sap_NW2_ASCS10 SAPInstance \
    InstanceName=NW2_ASCS10_msnw2ascs START_PROFILE="/sapmnt/NW2/profile/NW2_ASCS10_msnw2ascs" \
    AUTOMATIC_RECOVER=false \
    meta resource-stickiness=5000 migration-threshold=1 failure-timeout=60 \
    op monitor interval=20 on-fail=restart timeout=60 \
    op start interval=0 timeout=600 op stop interval=0 timeout=600 \
     --group g-NW2_ASCS

    sudo pcs resource create rsc_sap_NW2_ERS12 SAPInstance \
    InstanceName=NW2_ERS12_msnw2ers START_PROFILE="/sapmnt/NW2/profile/NW2_ERS12_msnw2ers" \
    AUTOMATIC_RECOVER=false IS_ERS=true \
    op monitor interval=20 on-fail=restart timeout=60 op start interval=0 timeout=600 op stop interval=0 timeout=600 \
     --group g-NW2_AERS

    sudo pcs constraint colocation add g-NW2_AERS with g-NW2_ASCS -5000
    sudo pcs constraint order g-NW2_ASCS then g-NW2_AERS kind=Optional symmetrical=false
    sudo pcs constraint order start g-NW2_ASCS then stop g-NW2_AERS symmetrical=false

    sudo pcs resource create rsc_sap_NW3_ASCS20 SAPInstance \
    InstanceName=NW3_ASCS20_msnw3ascs START_PROFILE="/sapmnt/NW3/profile/NW3_ASCS20_msnw3ascs" \
    AUTOMATIC_RECOVER=false \
    meta resource-stickiness=5000 migration-threshold=1 failure-timeout=60 \
    op monitor interval=20 on-fail=restart timeout=60 \
    op start interval=0 timeout=600 op stop interval=0 timeout=600 \
     --group g-NW3_ASCS

    sudo pcs resource create rsc_sap_NW3_ERS22 SAPInstance \
    InstanceName=NW3_ERS22_msnw3ers START_PROFILE="/sapmnt/NW3/profile/NW2_ERS22_msnw3ers" \
    AUTOMATIC_RECOVER=false IS_ERS=true \
    op monitor interval=20 on-fail=restart timeout=60 op start interval=0 timeout=600 op stop interval=0 timeout=600 \
     --group g-NW3_AERS

    sudo pcs constraint colocation add g-NW3_AERS with g-NW3_ASCS -5000
    sudo pcs constraint order g-NW3_ASCS then g-NW3_AERS kind=Optional symmetrical=false
    sudo pcs constraint order start g-NW3_ASCS then stop g-NW3_AERS symmetrical=false

    sudo pcs property set maintenance-mode=false
    ```

   如果要從舊版本升級到排隊伺服器 2，請參閱 SAP 注釋[2641019](https://launchpad.support.sap.com/#/notes/2641019)。 

   > [!NOTE]
   > 上述配置中的超時只是示例，可能需要適應特定的 SAP 設置。 

   請確定叢集狀態正常，且所有資源皆已啟動。 資源在哪一個節點上執行並不重要。
   下面的示例顯示 SAP 系統**NW2**和**NW3**添加到群集後群集資源狀態。 

    ```
     sudo pcs status

    Online: [ rhelmsscl1 rhelmsscl2 ]

    Full list of resources:

    rsc_st_azure   (stonith:fence_azure_arm):      Started rhelmsscl1
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
    Resource Group: g-NW2_ASCS
        fs_NW2_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW2_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW2_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW2_ASCS10 (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW2_AERS
        fs_NW2_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW2_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW2_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW2_ERS12  (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW3_ASCS
        fs_NW3_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW3_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW3_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW3_ASCS20 (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW3_AERS
        fs_NW3_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW3_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW3_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW3_ERS22  (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    ```

8. **[A]** 在兩個節點上添加 ASCS 和 ERS 的防火牆規則。  下面的示例顯示了 SAP 系統**NW2**和**NW3**的防火牆規則。  

   ```
    # NW2 - ASCS
    sudo firewall-cmd --zone=public --add-port=62010/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=62010/tcp
    sudo firewall-cmd --zone=public --add-port=3210/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=3210/tcp
    sudo firewall-cmd --zone=public --add-port=3610/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=3610/tcp
    sudo firewall-cmd --zone=public --add-port=3910/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=3910/tcp
    sudo firewall-cmd --zone=public --add-port=8110/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=8110/tcp
    sudo firewall-cmd --zone=public --add-port=51013/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=51013/tcp
    sudo firewall-cmd --zone=public --add-port=51014/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=51014/tcp
    sudo firewall-cmd --zone=public --add-port=51016/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=51016/tcp
    # NW2 - ERS
    sudo firewall-cmd --zone=public --add-port=62112/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=62112/tcp
    sudo firewall-cmd --zone=public --add-port=3312/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=3312/tcp
    sudo firewall-cmd --zone=public --add-port=51213/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=51213/tcp
    sudo firewall-cmd --zone=public --add-port=51214/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=51214/tcp
    sudo firewall-cmd --zone=public --add-port=51216/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=51216/tcp
    # NW3 - ASCS
    sudo firewall-cmd --zone=public --add-port=62020/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=62020/tcp
    sudo firewall-cmd --zone=public --add-port=3220/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=3220/tcp
    sudo firewall-cmd --zone=public --add-port=3620/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=3620/tcp
    sudo firewall-cmd --zone=public --add-port=3920/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=3920/tcp
    sudo firewall-cmd --zone=public --add-port=8120/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=8120/tcp
    sudo firewall-cmd --zone=public --add-port=52013/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=52013/tcp
    sudo firewall-cmd --zone=public --add-port=52014/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=52014/tcp
    sudo firewall-cmd --zone=public --add-port=52016/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=52016/tcp
    # NW3 - ERS
    sudo firewall-cmd --zone=public --add-port=62122/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=62122/tcp
    sudo firewall-cmd --zone=public --add-port=3322/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=3322/tcp
    sudo firewall-cmd --zone=public --add-port=52213/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=52213/tcp
    sudo firewall-cmd --zone=public --add-port=52214/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=52214/tcp
    sudo firewall-cmd --zone=public --add-port=52216/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=52216/tcp
   ```

### <a name="proceed-with-the-sap-installation"></a>繼續 SAP 安裝 

通過：

* [準備 SAP NetWeaver 應用程式伺服器](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files#2d6008b0-685d-426c-b59e-6cd281fd45d7)
* [安裝 DBMS 實例](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files#install-database)
* [安裝主 SAP 應用程式伺服器](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files#sap-netweaver-application-server-installation)
* 安裝一個或多個附加的 SAP 應用程式實例

## <a name="test-the-multi-sid-cluster-setup"></a>測試多 SID 群集設置

以下測試是紅帽最佳實踐指南中的測試案例的子集。 它們包含在您的方便。 有關群集測試的完整清單，請參閱以下文檔：

* 如果使用 Azure NetApp 檔 NFS 卷，請關注[AZURE VM 在 RHEL 上為 SAP NetWeaver 提供高可用性，以及用於 SAP 應用程式的 Azure NetApp 檔](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files)
* 如果使用高可用性`GlusterFS`，請按照[AZURE VM 在 RHEL 上的 SAP NetWeaver 的高可用性進行 SAP 應用程式](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel)操作。  

始終閱讀紅帽最佳實踐指南，並執行可能添加的所有其他測試。  
提供的測試位於兩個節點多 SID 群集中，安裝了三個 SAP 系統。  

1. 手動遷移 ASCS 實例。 該示例顯示遷移 SAP 系統 NW3 的 ASCS 實例。

   開始測試之前的資源狀態：

   ```
    Online: [ rhelmsscl1 rhelmsscl2 ]

    Full list of resources:

    rsc_st_azure   (stonith:fence_azure_arm):      Started rhelmsscl1
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
    Resource Group: g-NW2_ASCS
        fs_NW2_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW2_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW2_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW2_ASCS10 (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
    Resource Group: g-NW2_AERS
        fs_NW2_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW2_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW2_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW2_ERS12  (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW3_ASCS
        fs_NW3_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW3_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW3_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW3_ASCS20 (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
    Resource Group: g-NW3_AERS
        fs_NW3_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW3_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW3_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW3_ERS22  (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
   ```

   將以下命令作為根運行以遷移 NW3 ASCS 實例。

   ```
    pcs resource move rsc_sap_NW3_ASCS200
    # Clear temporary migration constraints
    pcs resource clear rsc_sap_NW3_ASCS20

    # Remove failed actions for the ERS that occurred as part of the migration
    pcs resource cleanup rsc_sap_NW3_ERS22
   ```

   測試完成之後的資源狀態：

   ```
    Online: [ rhelmsscl1 rhelmsscl2 ]

    Full list of resources:

    rsc_st_azure   (stonith:fence_azure_arm):      Started rhelmsscl1
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
    Resource Group: g-NW2_ASCS
        fs_NW2_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW2_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW2_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW2_ASCS10 (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
    Resource Group: g-NW2_AERS
        fs_NW2_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW2_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW2_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW2_ERS12  (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW3_ASCS
        fs_NW3_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW3_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW3_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW3_ASCS20 (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW3_AERS
        fs_NW3_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW3_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW3_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW3_ERS22  (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
   ```

1. 模擬節點損毀

   開始測試之前的資源狀態：

   ```
    Online: [ rhelmsscl1 rhelmsscl2 ]

    Full list of resources:

    rsc_st_azure   (stonith:fence_azure_arm):      Started rhelmsscl1
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
    Resource Group: g-NW2_ASCS
        fs_NW2_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW2_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW2_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW2_ASCS10 (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW2_AERS
        fs_NW2_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW2_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW2_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW2_ERS12  (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
    Resource Group: g-NW3_ASCS
        fs_NW3_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW3_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW3_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW3_ASCS20 (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW3_AERS
        fs_NW3_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW3_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW3_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW3_ERS22  (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
   ```

   在節點上運行以下命令作為根，其中至少有一個 ASCS 實例正在運行。 在此示例中，我們在 上`rhelmsscl1`執行命令，其中 NW1、NW2 和 NW3 的 ASCS 實例正在運行。  

   ```
   echo c > /proc/sysrq-trigger
   ```

   測試後的狀態，以及節點之後，已崩潰的再次啟動，應如下所示。

   ```
    Full list of resources:

    rsc_st_azure    (stonith:fence_azure_arm):      Started rhelmsscl2
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW2_ASCS
        fs_NW2_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW2_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW2_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW2_ASCS10 (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
    Resource Group: g-NW2_AERS
        fs_NW2_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW2_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW2_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW2_ERS12  (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW3_ASCS
        fs_NW3_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW3_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW3_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW3_ASCS20 (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
    Resource Group: g-NW3_AERS
        fs_NW3_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW3_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW3_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW3_ERS22  (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
   ```

   如果有失敗資源的消息，請清除失敗資源的狀態。 例如：

   ```
   pcs resource cleanup rsc_sap_NW1_ERS02
   ```

## <a name="next-steps"></a>後續步驟

* [適用於 SAP 的 Azure 虛擬機器規劃和實作][planning-guide]
* [適用於 SAP 的 Azure 虛擬機器部署][deployment-guide]
* [適用於 SAP 的 Azure 虛擬機器 DBMS 部署][dbms-guide]
* 若要了解如何建立高可用性並為 Azure VM 上的 SAP HANA 規劃災害復原，請參閱 [Azure 虛擬機器 (VM) 上 SAP HANA 的高可用性][sap-hana-ha]
