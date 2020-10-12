---
title: Azure Vm 在 RHEL 多重 SID 上適用于 SAP NW 的高可用性指南 |Microsoft Docs
description: 在 Azure 虛擬機器上建立 SAP NW 的高可用性， (Vm) RHEL 多重 SID。
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
ms.date: 08/04/2020
ms.author: radeltch
ms.openlocfilehash: 612bd019dc7a4bdf481fde4511084245fabd1620
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91319957"
---
# <a name="high-availability-for-sap-netweaver-on-azure-vms-on-red-hat-enterprise-linux-for-sap-applications-multi-sid-guide"></a>Azure Vm 上的 SAP NetWeaver 的高可用性，適用于 SAP 應用程式的多 SID 指南 Red Hat Enterprise Linux

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

本文說明如何部署多個 SAP NetWeaver 高可用性系統 (也就是 Azure Vm 上的兩個節點叢集中的多重 SID) ，並 Red Hat Enterprise Linux 適用于 SAP 應用程式。  

在範例設定中，安裝命令等會將三個 SAP NetWeaver 7.50 系統部署在單一的雙節點高可用性叢集中。 SAP 系統 Sid 如下：
* **NW1**： ASCS 實例號碼 **00** 和虛擬主機名稱 **msnw1ascs**;ERS 實例號碼 **02** 和虛擬主機名稱 **msnw1ers**。  
* **NW2**： ASCS 實例號碼 **10** 和虛擬主機名稱 **msnw2ascs**;ERS 實例號碼 **12** 和虛擬主機名稱 **msnw2ers**。  
* **NW3**： ASCS 實例號碼 **20** 和虛擬主機名稱 **msnw3ascs**;ERS 實例號碼 **22** 和虛擬主機名稱 **msnw3ers**。  

本文未涵蓋資料庫層和 SAP NFS 共用的部署。 在本文的範例中，我們會使用 [Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-create-volumes.md)  volume **SAPMSID** 來作為 NFS 共用，並假設已部署磁片區。 我們也假設 Azure NetApp Files 磁片區會使用 NFSv3 通訊協定來部署，而且 SAP systems NW1、NW2 和 NW3 的 ASCS 和 ERS 實例的叢集資源會有下列檔案路徑：  

* volume sapMSID (nfs://10.42.0.4/sapmnt<b>NW1</b>) 
* volume sapMSID (nfs://10.42.0.4/usrsap<b>NW1</b>ascs) 
* volume sapMSID (nfs://10.42.0.4/usrsap<b>NW1</b>sys) 
* volume sapMSID (nfs://10.42.0.4/usrsap<b>NW1</b>ers) 
* volume sapMSID (nfs://10.42.0.4/sapmnt<b>NW2</b>) 
* volume sapMSID (nfs://10.42.0.4/usrsap<b>NW2</b>ascs) 
* volume sapMSID (nfs://10.42.0.4/usrsap<b>NW2</b>sys) 
* volume sapMSID (nfs://10.42.0.4/usrsap<b>NW2</b>ers) 
* volume sapMSID (nfs://10.42.0.4/sapmnt<b>NW3</b>) 
* volume sapMSID (nfs://10.42.0.4/usrsap<b>NW3</b>ascs) 
* volume sapMSID (nfs://10.42.0.4/usrsap<b>NW3</b>sys) 
* volume sapMSID (nfs://10.42.0.4/usrsap<b>NW3</b>ers) 

開始之前，請先參閱下列 SAP 附注和論文：

* SAP Note [1928533]，其中包含：
  * SAP 軟體部署支援的 Azure VM 大小清單
  * Azure VM 大小的重要容量資訊
  * 支援的 SAP 軟體，以及作業系統 (OS) 與資料庫組合
  * Microsoft Azure 上 Windows 和 Linux 所需的 SAP 核心版本
* [Azure NetApp Files 文件][anf-azure-doc]
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
  * [在 RHEL 上的 Pacemaker 中使用獨立的佇列伺服器 2 (ENSA2) 來設定 SAP S/4HANA ASCS/ERS](https://access.redhat.com/articles/3974941)
* Azure 專用 RHEL 文件：
  * [RHEL 高可用性叢集的支援原則：以 Microsoft Azure 虛擬機器作為叢集成員](https://access.redhat.com/articles/3131341)
  * [在 Microsoft Azure 上安裝和設定 Red Hat Enterprise Linux 7.4 (和更新版本) 高可用性叢集](https://access.redhat.com/articles/3252491)
* [使用 Azure NetApp Files 在 Microsoft Azure 上的 NetApp SAP 應用程式][anf-sap-applications-azure]

## <a name="overview"></a>概觀

如果發生容錯移轉，則參與叢集的虛擬機器必須調整大小，才能執行所有資源。 每個 SAP SID 都可在多重 SID 高可用性叢集中彼此獨立地進行容錯移轉。  

為了達到高可用性，SAP NetWeaver 需要高度可用的共用。 在此檔中，我們會提供在 [Azure NetApp FILES NFS 磁片](../../../azure-netapp-files/azure-netapp-files-create-volumes.md)區上部署 SAP 共用的範例。 您也可以在高可用性的 [GlusterFS](./high-availability-guide-rhel-glusterfs.md)叢集上裝載共用，以供多個 SAP 系統使用。  

![SAP NetWeaver 高可用性概觀](./media/high-availability-guide-rhel/ha-rhel-multi-sid.png)

> [!IMPORTANT]
> 支援使用 Red Hat Linux 作為 Azure Vm 中的客體作業系統的 SAP ASCS/ERS 的多重 SID 叢集，在相同的叢集上限制為 **五個** SAP sid。 每個新的 SID 都會增加複雜度。 **不支援**在相同的叢集上混用 SAP 排入佇列複寫伺服器1和排入佇列複寫伺服器2。 多重 SID 叢集描述在一個 Pacemaker 叢集中安裝具有不同 Sid 的多個 SAP ASCS/ERS 實例。 目前只支援 ASCS/ERS 的多重 SID 叢集。  

> [!TIP]
> SAP ASCS/ERS 的多重 SID 叢集是複雜性較高的解決方案。 更複雜的方式是執行。 在執行維護活動時，也需要較高的系統管理工作， (例如 OS 修補) 。 開始實際執行之前，請花一些時間仔細規劃部署以及所有相關的元件，例如 Vm、NFS 掛接、Vip、負載平衡器設定等等。  

SAP NetWeaver ASCS、SAP NetWeaver SCS 和 SAP NetWeaver ERS 會使用虛擬主機名稱和虛擬 IP 位址。 在 Azure 上必須有負載平衡器才能使用虛擬 IP 位址。 我們建議使用 [tandard Load Balancer](../../../load-balancer/quickstart-load-balancer-standard-public-portal.md)。  

下列清單顯示具有三個 SAP 系統的這個多重 SID 叢集範例 () SCS 和 ERS 負載平衡器的設定。 每個 Sid 的每個 ASCS 和 ERS 實例都需要個別的前端 IP、健康情況探查和負載平衡規則。 將屬於 ASCS/ASCS 叢集中的所有 Vm 指派給單一 ILB 的一個後端集區。  

### <a name="ascs"></a>(A)SCS

* 前端組態
  * NW1 的 IP 位址：10.3.1.50
  * NW2 的 IP 位址：10.3.1.52
  * NW3 的 IP 位址：10.3.1.54

* 探查埠
  * 埠 620<strong> &lt; nr &gt; </strong>，因此適用于 NW1、NW2 和 NW3 探查埠 620**00**、620**10**和 620**20**
* 負載平衡規則-為每個實例建立一個規則，也就是 NW1/ASCS、NW2/ASCS 和 NW3/ASCS。
  * 若使用 Standard Load Balancer，請選取 [HA 連接埠]
  * 若使用基本負載平衡器，請為下列連接埠建立負載平衡規則
    * 32<strong>&lt;nr&gt;</strong> TCP
    * 36<strong>&lt;nr&gt;</strong> TCP
    * 39<strong>&lt;nr&gt;</strong> TCP
    * 81<strong>&lt;nr&gt;</strong> TCP
    * 5<strong>&lt;nr&gt;</strong>13 TCP
    * 5<strong>&lt;nr&gt;</strong>14 TCP
    * 5<strong>&lt;nr&gt;</strong>16 TCP

### <a name="ers"></a>ERS

* 前端組態
  * NW1 10.3.1.51 的 IP 位址
  * NW2 10.3.1.53 的 IP 位址
  * NW3 10.3.1.55 的 IP 位址

* 探查連接埠
  * 埠 621<strong> &lt; nr &gt; </strong>，因此適用于 NW1、NW2 和 N3 探查埠 621**02**、621**12**和 621**22**
* 負載平衡規則-為每個實例建立一個規則，也就是 NW1/ERS、NW2/ERS 和 NW3/ERS。
  * 若使用 Standard Load Balancer，請選取 [HA 連接埠]
  * 若使用基本負載平衡器，請為下列連接埠建立負載平衡規則
    * 32<strong>&lt;nr&gt;</strong> TCP
    * 33<strong>&lt;nr&gt;</strong> TCP
    * 5<strong>&lt;nr&gt;</strong>13 TCP
    * 5<strong>&lt;nr&gt;</strong>14 TCP
    * 5<strong>&lt;nr&gt;</strong>16 TCP

* 後端組態
  * 連線到應該屬於 (A)SCS/ERS 叢集一部分之所有虛擬機器的主要網路介面

> [!Note]
> 當不具公用 IP 位址的 VM 放在內部 (沒有公用 IP 位址) Standard Azure Load Balancer 的後端集區時，除非另外設定來允許路由傳送至公用端點，否則不會有輸出網際網路連線能力。 如需如何實現輸出連線能力的詳細資料，請參閱[在 SAP 高可用性案例中使用 Azure Standard Load Balancer 實現虛擬機器的公用端點連線能力](./high-availability-guide-standard-load-balancer-outbound-connections.md)。  

> [!IMPORTANT]
> 請勿在位於 Azure Load Balancer 後方的 Azure VM 上啟用 TCP 時間戳記。 啟用 TCP 時間戳記會導致健康狀態探查失敗。 將參數 **net.ipv4.tcp_timestamps** 設定為 **0**。 如需詳細資料，請參閱 [Load Balancer 健康狀態探查](../../../load-balancer/load-balancer-custom-probe-overview.md)。

## <a name="sap-shares"></a>SAP 共用

SAP NetWeaver 需要傳輸和設定檔目錄等的共用儲存體。 若為高可用性的 SAP 系統，請務必擁有高可用性的共用。 您將需要決定 SAP 共用的架構。 其中一個選項是在 [Azure NetApp FILES NFS 磁片](../../../azure-netapp-files/azure-netapp-files-create-volumes.md)區上部署共用。  使用 Azure NetApp Files，您將可取得 SAP NFS 共用的內建高可用性。

另一個選項是在 [適用于 Sap NetWeaver Red Hat Enterprise Linux 的 Azure vm](./high-availability-guide-rhel-glusterfs.md)上建立 GlusterFS，可在多個 sap 系統之間共用。 

## <a name="deploy-the-first-sap-system-in-the-cluster"></a>在叢集中部署第一個 SAP 系統

既然您已決定 SAP 共用的架構，請在叢集中部署第一個 SAP 系統，並遵循對應的檔。

* 如果使用 Azure NetApp Files NFS 磁片區，請遵循 sap [NetWeaver 的 Azure vm 高可用性，Red Hat Enterprise Linux 搭配適用于 sap 應用程式的 Azure Netapp Files](./high-availability-guide-rhel-netapp-files.md)  
* 如果使用 GlusterFS 叢集，請 [在 SAP NetWeaver 的 Red Hat Enterprise Linux 上，于 Azure vm 上執行 GlusterFS](./high-availability-guide-rhel-glusterfs.md)。  

上列檔將引導您完成準備必要基礎結構、建立叢集、準備 OS 以執行 SAP 應用程式的步驟。  

> [!TIP]
> 請一律先測試叢集的容錯移轉功能，在部署第一個系統之後，再將額外的 SAP Sid 新增至叢集。 如此一來，您就知道叢集功能可正常運作，然後再將其他 SAP 系統的複雜性新增至叢集。   

## <a name="deploy-additional-sap-systems-in-the-cluster"></a>在叢集中部署其他 SAP 系統

在此範例中，我們假設已在叢集中部署系統 **NW1** 。 我們將示範如何在叢集 SAP systems **NW2** 和 **NW3**中進行部署。 

下列項目會加上下列其中一個前置詞： **[A]** - 適用於所有節點、 **[1]** - 僅適用於節點 1 或 **[2]** - 僅適用於節點 2。

### <a name="prerequisites"></a>必要條件 

> [!IMPORTANT]
> 依照指示，在叢集中部署其他 SAP 系統之前，請遵循指示，在叢集中部署第一個 SAP 系統，因為只有在第一次部署系統時才需要執行這些步驟。  

本檔假設：
* Pacemaker 叢集已設定且正在執行。  
* 至少有一個 SAP 系統 (ASCS/ERS 實例) 已部署且正在叢集中執行。  
* 已測試叢集容錯移轉功能。  
* 所有 SAP 系統的 NFS 共用都已部署。  

### <a name="prepare-for-sap-netweaver-installation"></a>準備 SAP NetWeaver 安裝

1. 將新部署之系統的設定 (也就是 **NW2**、 **NW3**) 到現有的 Azure Load Balancer，並遵循指示以 [手動方式部署 Azure Load Balancer 透過 Azure 入口網站](./high-availability-guide-rhel-netapp-files.md#deploy-linux-manually-via-azure-portal)。 調整 IP 位址、健康情況探查埠、設定的負載平衡規則。  

2. **[A]** 其他 SAP 系統的設定名稱解析。 您可以使用 DNS 伺服器，或 `/etc/hosts` 在所有節點上修改。 此範例顯示如何使用檔案 `/etc/hosts` 。  將 IP 位址和主機名稱調整為您的環境。 

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

3. **[A]** 為您要部署到叢集的其他 **NW2** 和 **NW3** SAP 系統建立共用目錄。 

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

4. **[A]** 針對您要部署到叢集的其他 sap 系統，新增/Sapmnt/SID 和/usr/sap/SID/SYS 檔案系統的掛接專案。 在此範例中為 **NW2** 和 **NW3**。  

   `/etc/fstab`針對您要部署到叢集的其他 SAP 系統，使用檔案系統更新檔案。  

   * 如果使用 Azure NetApp Files，請依照[此處](./high-availability-guide-rhel-netapp-files.md#prepare-for-sap-netweaver-installation)的指示執行。  
   * 如果使用 GlusterFS 叢集，請遵循[此處](./high-availability-guide-rhel.md#prepare-for-sap-netweaver-installation)的指示  

### <a name="install-ascs--ers"></a>安裝 ASCS/ERS

1. 針對您要部署到叢集的其他 SAP 系統，建立 ASCS 實例的虛擬 IP 和健康情況探查叢集資源。 此處顯示的範例適用于 **NW2** 和 **NW3** ASCS，在 Azure NetApp Files 磁片區上使用 NFS 搭配 NFSv3 通訊協定。  

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

   請確定叢集狀態為 [確定]，而且所有資源皆已啟動。 資源在哪一個節點上執行並不重要。  

2. **[1]** 安裝 SAP NetWeaver ASCS  

   使用對應至 ASCS 負載平衡器前端設定 IP 位址的虛擬主機名稱，將 SAP NetWeaver ASCS 安裝為 root。 例如，在系統 **NW2**中，虛擬主機名稱為 <b>msnw2ascs</b>、 <b>10.3.1.52</b> 和您用於探查負載平衡器的實例號碼，例如 <b>10</b>。 針對系統 **NW3**，虛擬主機名稱為 <b>msnw3ascs</b>、 <b>10.3.1.54</b> 和您用於探查負載平衡器的實例號碼，例如 <b>20</b>。 請注意您針對每個 SAP SID 安裝 ASCS 的叢集節點。  

   您可以使用 sapinst 參數 SAPINST_REMOTE_ACCESS_USER 來允許非 root 使用者連線到 sapinst。 您可以使用參數 SAPINST_USE_HOSTNAME，使用虛擬主機名稱來安裝 SAP。  

    ```
    # Allow access to SWPM. This rule is not permanent. If you reboot the machine, you have to run the command again
    sudo firewall-cmd --zone=public --add-port=4237/tcp
    sudo swpm/sapinst SAPINST_REMOTE_ACCESS_USER=sapadmin SAPINST_USE_HOSTNAME=virtual_hostname
    ```

   如果安裝無法在/usr/sap/**SID**/ASCS**實例 #** 中建立子資料夾，請嘗試將 [擁有者] 設為 [ **sid**adm]，並將 [群組] 設定為 ASCS**實例 #** 的 sapsys，然後重試。

3. **[1]** 針對您要部署到叢集的額外 SAP 系統，建立 ERS 實例的虛擬 IP 和健康情況探查叢集資源。 此處顯示的範例適用于 **NW2** 和 **NW3** ERS，在 Azure NetApp Files 磁片區上使用 NFS 搭配 NFSv3 通訊協定。  

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

   請確定叢集狀態為 [確定]，而且所有資源皆已啟動。  

   接下來，請確定新建立的 ERS 群組的資源正在叢集節點上執行，而與安裝相同 SAP 系統之 ASCS 實例的叢集節點相反。  例如，如果已在上安裝 NW2 ASCS `rhelmsscl1` ，請確定 NW2 ERS 群組正在執行 `rhelmsscl2` 。  您可以 `rhelmsscl2` 針對群組中的其中一個叢集資源執行下列命令，以將 NW2 ERS 群組遷移至： 

    ```
      pcs resource move fs_NW2_AERS rhelmsscl2
    ```

4. **[2]** 安裝 SAP NetWeaver ERS

   使用對應至 ERS 負載平衡器前端設定 IP 位址的虛擬主機名稱，將 SAP NetWeaver ERS 安裝為另一個節點上的根節點。 例如，在系統 **NW2**中，虛擬主機名稱為 <b>msnw2ers</b>、 <b>10.3.1.53</b> 和您用於探查負載平衡器的實例號碼，例如 <b>12</b>。 若為系統 **NW3**，則為虛擬主機名稱 <b>msnw3ers</b>、 <b>10.3.1.55</b> ，以及用於探查負載平衡器的實例號碼，例如 <b>22</b>。 

   您可以使用 sapinst 參數 SAPINST_REMOTE_ACCESS_USER 來允許非 root 使用者連線到 sapinst。 您可以使用參數 SAPINST_USE_HOSTNAME，使用虛擬主機名稱來安裝 SAP。  

    ```
    # Allow access to SWPM. This rule is not permanent. If you reboot the machine, you have to run the command again
    sudo firewall-cmd --zone=public --add-port=4237/tcp
    sudo swpm/sapinst SAPINST_REMOTE_ACCESS_USER=sapadmin SAPINST_USE_HOSTNAME=virtual_hostname
    ```

   > [!NOTE]
   > 請使用 SWPM SP 20 PL 05 或更高版本。 較低版本無法正確設定權限，因而會讓安裝失敗。

   如果安裝無法在/usr/sap/**NW2**/ERS**實例 #** 中建立子資料夾，請嘗試將 [擁有者] 設定為 [ **sid**adm]，並將群組設定為 [ERS**實例 #** ] 資料夾的 sapsys，然後再試一次。

   如果您需要將新部署的 SAP 系統的 ERS 群組遷移至不同的叢集節點，請記得移除 ERS 群組的位置限制式。 您可以藉由執行下列命令來移除條件約束 (針對 SAP systems **NW2** 和 **NW3**) 提供範例。 請務必移除您在命令中使用的相同資源的暫時條件約束，以移動 ERS 叢集群組。

    ```
      pcs resource clear fs_NW2_AERS
      pcs resource clear fs_NW3_AERS
    ```

5. **[1]** 針對新安裝的 SAP system (s) ，調整 ASCS/SCS 和 ERS 實例設定檔。 以下顯示的範例適用于 NW2。 您將需要針對新增至叢集的所有 SAP 實例，調整 ASCS/SCS 和 ERS 設定檔。  
 
   * ASCS/SCS 設定檔

      ```
      sudo vi /sapmnt/NW2/profile/NW2_ASCS10_msnw2ascs
   
      # Change the restart command to a start command
      #Restart_Program_01 = local $(_EN) pf=$(_PF)
      Start_Program_01 = local $(_EN) pf=$(_PF)

      # Add the keep alive parameter, if using ENSA1
      enque/encni/set_so_keepalive = true
      ```

     針對 ENSA1 和 ENSA2，請確定 `keepalive` 作業系統參數已設定為 SAP note [1410736](https://launchpad.support.sap.com/#/notes/1410736)中所述。    

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

   為了防止 sapinit 啟動腳本啟動實例，Pacemaker 管理的所有實例都必須從檔案標記為批註 `/usr/sap/sapservices` 。  以下顯示的範例適用于 SAP 系統 **NW2** 和 **NW3**。  

   ```
    # On the node where ASCS was installed, comment out the line for the ASCS instacnes
    #LD_LIBRARY_PATH=/usr/sap/NW2/ASCS10/exe:$LD_LIBRARY_PATH; export LD_LIBRARY_PATH; /usr/sap/NW2/ASCS10/exe/sapstartsrv pf=/usr/sap/NW2/SYS/profile/NW2_ASCS10_msnw2ascs -D -u nw2adm
    #LD_LIBRARY_PATH=/usr/sap/NW3/ASCS20/exe:$LD_LIBRARY_PATH; export LD_LIBRARY_PATH; /usr/sap/NW3/ASCS20/exe/sapstartsrv pf=/usr/sap/NW3/SYS/profile/NW3_ASCS20_msnw3ascs -D -u nw3adm

    # On the node where ERS was installed, comment out the line for the ERS instacnes
    #LD_LIBRARY_PATH=/usr/sap/NW2/ERS12/exe:$LD_LIBRARY_PATH; export LD_LIBRARY_PATH; /usr/sap/NW2/ERS12/exe/sapstartsrv pf=/usr/sap/NW2/ERS12/profile/NW2_ERS12_msnw2ers -D -u nw2adm
    #LD_LIBRARY_PATH=/usr/sap/NW3/ERS22/exe:$LD_LIBRARY_PATH; export LD_LIBRARY_PATH; /usr/sap/NW3/ERS22/exe/sapstartsrv pf=/usr/sap/NW3/ERS22/profile/NW3_ERS22_msnw3ers -D -u nw3adm
   ```

7. **[1]** 建立新安裝之 sap 系統的 sap 叢集資源。  

   如果使用佇列伺服器1架構 (ENSA1) ，請定義 SAP systems **NW2** 和 **NW3** 的資源，如下所示：

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

   SAP 在 SAP NW 7.52 中引進了加入佇列伺服器 2 的支援 (包括複寫)。 從 ABAP 平台 1809 開始，根據預設會安裝加入佇列伺服器 2。 如需加入佇列伺服器 2 的支援，請參閱 SAP Note [2630416](https://launchpad.support.sap.com/#/notes/2630416)。
   如果使用佇列伺服器2架構 ([ENSA2](https://help.sap.com/viewer/cff8531bc1d9416d91bb6781e628d4e0/1709%20001/en-US/6d655c383abf4c129b0e5c8683e7ecd8.html)) ，請定義 SAP systems **NW2** 和 **NW3** 的資源，如下所示：

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

   若正在從舊版本升級並切換到加入佇列伺服器 2，請參閱 SAP Note [2641019](https://launchpad.support.sap.com/#/notes/2641019)。 

   > [!NOTE]
   > 上述設定中的超時只是範例，可能需要針對特定的 SAP 安裝程式進行調整。 

   請確定叢集狀態正常，且所有資源皆已啟動。 資源在哪一個節點上執行並不重要。
   下列範例會顯示在將 SAP 系統 **NW2** 和 **NW3** 新增至叢集之後，叢集資源的狀態。 

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

8. **[A]** 在兩個節點上新增 ASCS 和 ERS 的防火牆規則。  下列範例顯示 SAP 系統 **NW2** 和 **NW3**的防火牆規則。  

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

### <a name="proceed-with-the-sap-installation"></a>繼續進行 SAP 安裝 

完成 SAP 安裝：

* [準備您的 SAP NetWeaver 應用程式伺服器](./high-availability-guide-rhel-netapp-files.md#2d6008b0-685d-426c-b59e-6cd281fd45d7)
* [安裝 DBMS 實例](./high-availability-guide-rhel-netapp-files.md#install-database)
* [安裝主要 SAP 應用程式伺服器](./high-availability-guide-rhel-netapp-files.md#sap-netweaver-application-server-installation)
* 安裝一或多個額外的 SAP 應用程式實例

## <a name="test-the-multi-sid-cluster-setup"></a>測試多重 SID 叢集設定

下列測試是 Red Hat 最佳作法指南中測試案例的子集。 為了方便起見，我們會包含它們。 如需完整的叢集測試清單，請參考下列檔：

* 如果使用 Azure NetApp Files NFS 磁片區，請遵循適用于 sap [NetWeaver ON RHEL 的 Azure vm 高可用性（適用于 sap 應用程式的 Azure Netapp files](./high-availability-guide-rhel-netapp-files.md) ）
* 如果使用高 `GlusterFS` 可用性，請遵循 [sap NETWEAVER on RHEL for sap 應用程式的 Azure vm 高可用性](./high-availability-guide-rhel.md)。  

請一律閱讀 Red Hat 最佳作法指南，並執行可能已新增的所有其他測試。  
所呈現的測試是在兩個節點中，已安裝三個 SAP 系統的多重 SID 叢集。  

1. 手動遷移 ASCS 實例。 此範例顯示如何遷移 SAP system NW3 的 ASCS 實例。

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

   以 root 身份執行下列命令，以遷移 NW3 ASCS 實例。

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

   在節點上以 root 身份執行下列命令，其中至少有一個 ASCS 實例正在執行。 在此範例中，我們已在上執行命令 `rhelmsscl1` ，其中 NW1、NW2 和 NW3 的 ASCS 實例正在執行。  

   ```
   echo c > /proc/sysrq-trigger
   ```

   測試之後，以及在節點之後，當機的狀態重新開機時，看起來應該像這樣。

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

   如果有失敗資源的訊息，請清除失敗資源的狀態。 例如：

   ```
   pcs resource cleanup rsc_sap_NW1_ERS02
   ```

## <a name="next-steps"></a>後續步驟

* [適用於 SAP 的 Azure 虛擬機器規劃和實作][planning-guide]
* [適用於 SAP 的 Azure 虛擬機器部署][deployment-guide]
* [適用於 SAP 的 Azure 虛擬機器 DBMS 部署][dbms-guide]
* 若要了解如何建立高可用性，並為 Azure VM 上的 SAP HANA 規劃災害復原，請參閱 [Azure 虛擬機器 (VM) 上 SAP HANA 的高可用性][sap-hana-ha]
