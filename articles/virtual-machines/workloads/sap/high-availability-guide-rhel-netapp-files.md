---
title: 使用 Azure NetApp 檔，在 RHEL 上為 SAP NW 提供高可用性的 Azure VM。微軟文檔
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
ms.date: 03/26/2020
ms.author: radeltch
ms.openlocfilehash: 11119d193cd08944bdff4737e8182cc7bece0abc
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "80351249"
---
# <a name="azure-virtual-machines-high-availability-for-sap-netweaver-on-red-hat-enterprise-linux-with-azure-netapp-files-for-sap-applications"></a>用於 SAP 應用程式的 Azure NetApp 檔的紅帽企業 Linux 上的 SAP NetWeaver 的 Azure 虛擬機器高可用性

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

本文介紹如何使用[Azure NetApp 檔](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-introduction/)部署虛擬機器、配置虛擬機器、安裝群集框架和安裝高度可用的 SAP NetWeaver 7.50 系統。
在示例配置、安裝命令等中。ASCS 實例為 00，ERS 實例為 01，主應用程式實例 （PAS） 為 02，應用程式實例 （AAS） 為 03。 使用 SAP 系統 ID QAS。 

本文未詳細介紹資料庫層。  

請先閱讀下列 SAP Note 和文件：

* [Azure NetApp 檔文檔][anf-azure-doc] 
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

SAP Netweaver 中心服務的高可用性 （HA） 需要共用存儲。
為了在紅帽Linux上實現這一目標，到目前為止，有必要構建單獨的高可用 GlusterFS 群集。 

現在，可以使用部署在 Azure NetApp 檔上的共用存儲來實現 SAP Netweaver HA。 將 Azure NetApp 檔用於共用存儲無需其他[GlusterFS 群集](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-glusterfs)。 SAP Netweaver 中央服務 （ASCS/SCS） 的 HA 仍然需要起搏器。

![SAP NetWeaver 高可用性概觀](./media/high-availability-guide-rhel/high-availability-guide-rhel-anf.png)

SAP NetWeaver ASCS、SAP NetWeaver SCS、SAP NetWeaver ERS 和 SAP Hana 資料庫會使用虛擬主機名稱和虛擬 IP 位址。 在 Azure 上必須有負載平衡器才能使用虛擬 IP 位址。 我們建議使用[標準負載等化器](https://docs.microsoft.com/azure/load-balancer/quickstart-load-balancer-standard-public-portal)。 下面的清單顯示了負載等化器的配置，具有單獨的前端 IP（A）SCS 和 ERS。

### <a name="ascs"></a>(A)SCS

* 前端組態
  * IP 位址 192.168.14.9
* 探查連接埠
  * 埠 620<strong>&lt;nr&gt;</strong>
* 負載平衡規則
  * 如果使用標準負載等化器，請選擇**HA 埠**
  * 32<strong> &lt; &gt; nr</strong> TCP
  * 36<strong> &lt; &gt; nr</strong> TCP
  * 39<strong> &lt; &gt; nr</strong> TCP
  * 81<strong> &lt; &gt; nr</strong> TCP
  * 5<strong>&lt;&gt;nr</strong>13 TCP
  * 5<strong>&lt;&gt;nr</strong>14 TCP
  * 5<strong>&lt;&gt;nr</strong>16 TCP

### <a name="ers"></a>ERS

* 前端組態
  * IP 位址 192.168.14.10
* 探查連接埠
  * 埠 621<strong>&lt;nr&gt;</strong>
* 負載平衡規則
  * 如果使用標準負載等化器，請選擇**HA 埠**
  * 32<strong> &lt; &gt; nr</strong> TCP
  * 33<strong> &lt; &gt; nr</strong> TCP
  * 5<strong>&lt;&gt;nr</strong>13 TCP
  * 5<strong>&lt;&gt;nr</strong>14 TCP
  * 5<strong>&lt;&gt;nr</strong>16 TCP

* 後端組態
  * 連線到應該屬於 (A)SCS/ERS 叢集一部分之所有虛擬機器的主要網路介面

## <a name="setting-up-the-azure-netapp-files-infrastructure"></a>設置 Azure NetApp 檔基礎結構 

SAP NetWeaver 需要傳輸和設定檔目錄的共用儲存體。  在繼續設置 Azure NetApp 檔基礎結構之前，請熟悉[Azure NetApp 檔文檔][anf-azure-doc]。 檢查所選 Azure 區域是否提供 Azure NetApp 檔。 以下連結按 Azure 區域顯示 Azure NetApp 檔的可用性[：Azure NetApp 檔可用性（按 Azure 區域][anf-avail-matrix]）。

Azure NetApp 檔在[多個 Azure 區域](https://azure.microsoft.com/global-infrastructure/services/?products=netapp)中可用。 在部署 Azure NetApp 檔之前，請按照[Azure NetApp 檔註冊說明][anf-register]請求載入 Azure NetApp 檔。 

### <a name="deploy-azure-netapp-files-resources"></a>部署 Azure NetApp 檔資源  

這些步驟假定您已經部署了 Azure[虛擬網路](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview)。 Azure NetApp 檔資源和 VM，其中將裝載 Azure NetApp 檔資源，必須部署在同一 Azure 虛擬網路或對等 Azure 虛擬網路中。  

1. 如果尚未這樣做，請請求載入 Azure [NetApp 檔](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-register)。  
2. 按照創建[NetApp 帳戶的說明](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-netapp-account)在選定的 Azure 區域中創建 NetApp 帳戶。  
3. 設置 Azure NetApp 檔容量池，按照[有關如何設置 Azure NetApp 檔容量池的說明進行設置](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-set-up-capacity-pool)。  
本文仲介紹的 SAP Netweaver 體系結構使用單個 Azure NetApp 檔容量池，高級 SKU。 我們建議 Azure NetApp 檔高級 SKU 用於 Azure 上的 SAP Netweaver 應用程式工作負荷。  
4. 按照說明中所述，將子網委派給 Azure NetApp[檔，將子網委派給 Azure NetApp 檔](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-delegate-subnet)。  

5. 部署 Azure NetApp 檔卷，按照[為 Azure NetApp 檔創建卷的說明進行部署](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-volumes)。 在指定的 Azure NetApp 檔[子網](https://docs.microsoft.com/rest/api/virtualnetwork/subnets)中部署卷。 請記住，Azure NetApp 檔資源和 Azure VM 必須位於同一 Azure 虛擬網路或對等 Azure 虛擬網路中。 在此示例中，我們使用兩個 Azure NetApp 檔卷：sap<b>QAS</b>和跨 SAP。 裝載到相應裝載點的檔路徑是 /ussap<b>qas</b>/sapmnt<b>QAS、/ussap</b><b>qas/ussap</b><b>QAS</b>sys 等。  

   1. 體積汁<b>QAS</b> （nfs://192.168.24.5/usrsap<b>qas</b>/sapmnt<b>QAS</b>）
   2. 體積汁<b>QAS（nfs://192.168.24.5/usrsap</b><b>個 qas</b>/ussap<b>QAS</b>ascs）
   3. 體積液<b>QAS（nfs://192.168.24.5/usrsap</b><b>卡斯</b>/rsap<b>QAS</b>系統）
   4. 體積汁<b>QAS</b> （nfs://192.168.24.5/usrsap<b>卡斯</b>/rsap<b>QAS</b>ers）
   5. 卷跨 SAP （nfs://192.168.24.4/transSAP）
   6. 體積汁<b>QAS</b> （nfs://192.168.24.5/usrsap<b>卡斯</b>/rsap<b>QAS</b>pas）
   7. 體積汁<b>QAS</b> （nfs://192.168.24.5/usrsap<b>卡斯</b>/rsap<b>QAS</b>aas）
  
在此示例中，我們為所有 SAP Netweaver 檔案系統使用 Azure NetApp 檔來演示如何使用 Azure NetApp 檔。 不需要通過 NFS 裝載的 SAP 檔案系統也可以作為[Azure 磁片存儲](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#premium-ssd)進行部署。 在此示例中<b>，a-e</b>必須位於 Azure NetApp 檔和<b>f-g（</b>即 /usr/sap/<b>QAS</b>/D 02、/usr/sap/<b>QAS</b>/D<b>03）</b>上，可以部署為 Azure 磁片存儲。<b> </b> 

### <a name="important-considerations"></a>重要考量︰

在 SUSE 高可用性體系結構上考慮 SAP Netweaver 的 Azure NetApp 檔時，請注意以下重要注意事項：

- 最小容量池為 4 TiB。 容量池大小可以 1 TiB 增量增加。
- 最小容量為 100 GiB
- Azure NetApp 檔和將裝載 Azure NetApp 檔卷的所有虛擬機器必須位於同一 Azure 虛擬網路或同一區域中的[對等虛擬網路中](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview)。 Azure NetApp 檔訪問通過 VNET 對等互連在同一區域現在支援。 目前不支援通過全域對等互連進行 Azure NetApp 訪問。
- 所選虛擬網路必須具有委派給 Azure NetApp 檔的子網。
- Azure NetApp 檔提供[匯出策略](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-configure-export-policy)：您可以控制允許的用戶端、訪問類型（讀取&寫入、僅讀取等）。 
- Azure NetApp 檔功能尚未了解區域。 當前，Azure NetApp 檔功能未部署在 Azure 區域中的所有可用性區域中。 請注意某些 Azure 區域的潛在延遲影響。 
- Azure NetApp 檔卷可以部署為 NFSv3 或 NFSv4.1 卷。 SAP 應用程式層（ASCS/ERS、SAP 應用程式伺服器）支援這兩種協定。 

## <a name="setting-up-ascs"></a>設定 (A)SCS

在此示例中，資源通過[Azure 門戶](https://portal.azure.com/#home)手動部署。

### <a name="deploy-linux-manually-via-azure-portal"></a>透過 Azure 入口網站手動部署 Linux

首先，您需要創建 Azure NetApp 檔卷。 部署 VM。 之後，您將創建一個負載等化器，並在後端池中使用虛擬機器。

1. 創建負載等化器（內部標準）：  
   1. 建立前端 IP 位址
      1. ASCS 的 IP 位址 192.168.14.9
         1. 開啟負載平衡器，選取前端 IP 集區，然後按一下 [新增]
         1. 輸入新的前端 IP 池的名稱（例如**前端）。QAS。ASCS**）
         1. 將"分配分配"設置為靜態並輸入 IP 位址（例如**192.168.14.9**）
         1. Click OK
      1. ASCS ERS 的 IP 位址 192.168.14.10
         * 在"a"下重複上述步驟，為 ERS 創建 IP 位址（例如**192.168.14.10**和**前端）。QAS。ERS**）
   1. 建立後端集區
      1. 開啟負載平衡器，選取後端集區，然後按一下 [新增]
      1. 輸入新後端池的名稱（例如**後端）。QAS**）
      1. 按一下 [新增虛擬機器]。
      1. 選取 [虛擬機器]。 
      1. 選擇 （A）SCS 群集的虛擬機器及其 IP 位址。
      1. 按一下 [新增]
   1. 建立健康狀態探查
      1. 針對 ASCS 是連接埠 620**00**
         1. 開啟負載平衡器，選取健康情況探查，然後按一下 [新增]
         1. 輸入新運行狀況探測的名稱（例如**運行狀況）。QAS。ASCS**）
         1. 選取 [TCP] 作為通訊協定、連接埠 620**00**，保留 [間隔] 5 和 [狀況不良閾值] 2
         1. Click OK
      1. 埠 621**01**用於 ASCS ERS
            * 在"c"下重複上述步驟，為 ERS 創建運行狀況探測（例如 621**01**和**運行狀況）。QAS。ERS**）
   1. 負載平衡規則
      1. ASCS 的負載平衡規則
         1. 打開負載平衡器，選擇負載平衡規則，然後按一下"添加"
         1. 輸入新的負載等化器規則的名稱（例如**lb。QAS。ASCS**）
         1. 為之前創建的 ASCS、後端池和運行狀況探測（例如前端）選擇前端 IP 位址 **。QAS。ASCS，****後端。QAS**和**運行狀況。QAS。ASCS**）
         1. 選擇**HA 埠**
         1. 將閒置逾時增加為 30 分鐘
         1. **務必啟用浮動 IP**
         1. Click OK
         * 重複上述步驟，為 ERS 創建負載平衡規則（例如**lb。QAS。ERS**）
1. 或者，如果您的方案需要基本的負載等化器（內部），請按照以下步驟操作：  
   1. 建立前端 IP 位址
      1. ASCS 的 IP 位址 192.168.14.9
         1. 開啟負載平衡器，選取前端 IP 集區，然後按一下 [新增]
         1. 輸入新的前端 IP 池的名稱（例如**前端）。QAS。ASCS**）
         1. 將"分配分配"設置為靜態並輸入 IP 位址（例如**192.168.14.9**）
         1. Click OK
      1. ASCS ERS 的 IP 位址 192.168.14.10
         * 在"a"下重複上述步驟，為 ERS 創建 IP 位址（例如**192.168.14.10**和**前端）。QAS。ERS**）
   1. 建立後端集區
      1. 開啟負載平衡器，選取後端集區，然後按一下 [新增]
      1. 輸入新後端池的名稱（例如**後端）。QAS**）
      1. 按一下 [新增虛擬機器]。
      1. 選擇您之前為 ASCS 創建的可用性集 
      1. 選取 (A)SCS 叢集的虛擬機器
      1. Click OK
   1. 建立健康狀態探查
      1. 針對 ASCS 是連接埠 620**00**
         1. 開啟負載平衡器，選取健康情況探查，然後按一下 [新增]
         1. 輸入新運行狀況探測的名稱（例如**運行狀況）。QAS。ASCS**）
         1. 選取 [TCP] 作為通訊協定、連接埠 620**00**，保留 [間隔] 5 和 [狀況不良閾值] 2
         1. Click OK
      1. 埠 621**01**用於 ASCS ERS
            * 在"c"下重複上述步驟，為 ERS 創建運行狀況探測（例如 621**01**和**運行狀況）。QAS。ERS**）
   1. 負載平衡規則
      1. 針對 ASCS 是 32**00** TCP
         1. 打開負載平衡器，選擇負載平衡規則，然後按一下"添加"
         1. 輸入新的負載等化器規則的名稱（例如**lb。QAS。ASCS.3200**）
         1. 為之前創建的 ASCS、後端池和運行狀況探測（例如前端）選擇前端 IP 位址 **。QAS。ASCS**）
         1. 保留通訊協定 [TCP]****，輸入連接埠 **3200**
         1. 將閒置逾時增加為 30 分鐘
         1. **務必啟用浮動 IP**
         1. Click OK
      1. ASCS 的其他連接埠
         * 對 ASCS 的埠 36**00、39****00、81****00、5****00**13、5**00**14、5**00**16 和 TCP 的埠重複上述步驟
      1. ASCS ERS 的其他連接埠
         * 對 ASCS ERS 的埠 32**01、33****01、5****01**13、5**01**14、5**01**16 和 TCP 的埠"d"下重複上述步驟

      > [!Note]
      > 當沒有公共 IP 位址的 VM 放置在內部（無公共 IP 位址）標準 Azure 負載等化器的後端池中時，將沒有出站互聯網連接，除非執行其他配置以允許路由到公共終結點。 有關如何實現出站連接的詳細資訊，請參閱[在 SAP 高可用性方案中使用 Azure 標準負載等化器的虛擬機器的公共終結點連接](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-standard-load-balancer-outbound-connections)。  

      > [!IMPORTANT]
      > 請勿在位於 Azure 負載等化器後面的 Azure VM 上啟用 TCP 時間戳記。 啟用 TCP 時間戳記將導致運行狀況探測失敗。 將參數**net.ipv4.tcp_timestamps**設置為**0**。 有關詳細資訊，請參閱[負載等化器運行狀況探測器](https://docs.microsoft.com/azure/load-balancer/load-balancer-custom-probe-overview)。

## <a name="disable-id-mapping-if-using-nfsv41"></a>禁用 ID 映射（如果使用 NFSv4.1）

本節中的說明僅適用于使用具有 NFSv4.1 協定的 Azure NetApp 檔卷。 在所有 VM 上執行配置，其中將裝載 Azure NetApp 檔 NFSv4.1 卷。  

1. 驗證 NFS 域設置。 確保域配置為預設的 Azure NetApp 檔域，即**`defaultv4iddomain.com`** 映射設置為**無人**。  

    > [!IMPORTANT]
    > 請確保在 VM 上`/etc/idmapd.conf`設置 NFS 域，以匹配 Azure NetApp 檔上的預設**`defaultv4iddomain.com`** 域配置： 。 如果 NFS 用戶端（即 VM）上的域配置與 NFS 伺服器（即 Azure NetApp 配置）不匹配，則安裝在 VM 上的 Azure NetApp 卷上的檔許可權將顯示為`nobody`。  

    <pre><code>
    sudo cat /etc/idmapd.conf
    # Example
    [General]
    Domain = <b>defaultv4iddomain.com</b>
    [Mapping]
    Nobody-User = <b>nobody</b>
    Nobody-Group = <b>nobody</b>
    </code></pre>

4. **[A]** `nfs4_disable_idmapping`驗證 。 應將其設置為**Y**。要創建位於的位置`nfs4_disable_idmapping`的目錄結構，請執行裝載命令。 您將無法在 /sys/模組下手動創建目錄，因為訪問是為內核/驅動程式保留的。  

    <pre><code>
    # Check nfs4_disable_idmapping 
    cat /sys/module/nfs/parameters/nfs4_disable_idmapping
    # If you need to set nfs4_disable_idmapping to Y
    mkdir /mnt/tmp
    mount 192.168.24.5:/sap<b>QAS</b>
    umount  /mnt/tmp
    echo "Y" > /sys/module/nfs/parameters/nfs4_disable_idmapping
    # Make the configuration permanent
    echo "options nfs nfs4_disable_idmapping=Y" >> /etc/modprobe.d/nfs.conf
    </code></pre>

   有關如何更改`nfs4_disable_idmapping`參數的更多詳細資訊，請參閱https://access.redhat.com/solutions/1749883。

### <a name="create-pacemaker-cluster"></a>建立 Pacemaker 叢集

依照[在 Azure 中於 Red Hat Enterprise Linux 上設定 Pacemaker](high-availability-guide-rhel-pacemaker.md) 中的步驟，建立此 (A)SCS 伺服器的基本 Pacemaker 叢集。

### <a name="prepare-for-sap-netweaver-installation"></a>準備進行 SAP NetWeaver 安裝

以下項預綴為 **[A]** - 適用于所有節點 **[1]** - 僅適用于節點 1 或 **[2]** - 僅適用于節點 2。

1. **[A]** 設定主機名稱解析

   您可以使用 DNS 伺服器，或修改所有節點上的 /etc/hosts。 這個範例示範如何使用 /etc/hosts 檔案。
   取代下列命令中的 IP 位址和主機名稱

    ```
    sudo vi /etc/hosts
    ```

   將下列幾行插入至 /etc/hosts。 變更 IP 位址和主機名稱以符合您的環境

    ```
    # IP address of cluster node 1
    192.168.14.5    anftstsapcl1
    # IP address of cluster node 2
    192.168.14.6     anftstsapcl2
    # IP address of the load balancer frontend configuration for SAP Netweaver ASCS
    192.168.14.9    anftstsapvh
    # IP address of the load balancer frontend configuration for SAP Netweaver ERS
    192.168.14.10    anftstsapers
    ```

1. **[1]** 在 Azure NetApp 檔卷中創建 SAP 目錄。  
   將 Azure NetApp 檔卷臨時裝載到其中一個 VM 上，並創建 SAP 目錄（檔路徑）。  

    ```
     # mount temporarily the volume
     sudo mkdir -p /saptmp
     # If using NFSv3
     sudo mount -t nfs -o rw,hard,rsize=65536,wsize=65536,vers=3,tcp 192.168.24.5:/sapQAS /saptmp
     # If using NFSv4.1
     sudo mount -t nfs -o rw,hard,rsize=65536,wsize=65536,vers=4.1,sec=sys,tcp 192.168.24.5:/sapQAS /saptmp
     # create the SAP directories
     sudo cd /saptmp
     sudo mkdir -p sapmntQAS
     sudo mkdir -p usrsapQASascs
     sudo mkdir -p usrsapQASers
     sudo mkdir -p usrsapQASsys
     sudo mkdir -p usrsapQASpas
     sudo mkdir -p usrsapQASaas
     # unmount the volume and delete the temporary directory
     sudo cd ..
     sudo umount /saptmp
     sudo rmdir /saptmp
    ``` 

1. **[A]** 建立共用目錄

   ```
   sudo mkdir -p /sapmnt/QAS
   sudo mkdir -p /usr/sap/trans
   sudo mkdir -p /usr/sap/QAS/SYS
   sudo mkdir -p /usr/sap/QAS/ASCS00
   sudo mkdir -p /usr/sap/QAS/ERS01
   
   sudo chattr +i /sapmnt/QAS
   sudo chattr +i /usr/sap/trans
   sudo chattr +i /usr/sap/QAS/SYS
   sudo chattr +i /usr/sap/QAS/ASCS00
   sudo chattr +i /usr/sap/QAS/ERS01
   ```

1. **[A]** 安裝 NFS 用戶端和其他要求

   ```
   sudo yum -y install nfs-utils resource-agents resource-agents-sap
   ```

1. **[A]** 檢查 resource-agents-sap 的版本

   請確定已安裝的 resource-agents-sap 套件版本至少為 3.9.5-124.el7
   ```
   sudo yum info resource-agents-sap
   
   # Loaded plugins: langpacks, product-id, search-disabled-repos
   # Repodata is over 2 weeks old. Install yum-cron? Or run: yum makecache fast
   # Installed Packages
   # Name        : resource-agents-sap
   # Arch        : x86_64
   # Version     : 3.9.5
   # Release     : 124.el7
   # Size        : 100 k
   # Repo        : installed
   # From repo   : rhel-sap-for-rhel-7-server-rpms
   # Summary     : SAP cluster resource agents and connector script
   # URL         : https://github.com/ClusterLabs/resource-agents
   # License     : GPLv2+
   # Description : The SAP resource agents and connector script interface with
   #          : Pacemaker to allow SAP instances to be managed in a cluster
   #          : environment.
   ```


1. **[A]** 新增掛接項目

   如果使用 NFSv3：
   ```
   sudo vi /etc/fstab
   
   # Add the following lines to fstab, save and exit
    192.168.24.5:/sapQAS/sapmntQAS /sapmnt/QAS nfs rw,hard,rsize=65536,wsize=65536,vers=3
    192.168.24.5:/sapQAS/usrsapQASsys /usr/sap/QAS/SYS nfs rw,hard,rsize=65536,wsize=65536,vers=3
    192.168.24.4:/transSAP /usr/sap/trans nfs rw,hard,rsize=65536,wsize=65536,vers=3
   ```

   如果使用 NFSv4.1：
   ```
   sudo vi /etc/fstab
   
   # Add the following lines to fstab, save and exit
    192.168.24.5:/sapQAS/sapmntQAS /sapmnt/QAS nfs rw,hard,rsize=65536,wsize=65536,vers=4.1,sec=sys
    192.168.24.5:/sapQAS/usrsapQASsys /usr/sap/QAS/SYS nfs rw,hard,rsize=65536,wsize=65536,vers=4.1,sec=sys
    192.168.24.4:/transSAP /usr/sap/trans nfs rw,hard,rsize=65536,wsize=65536,vers=4.1,sec=sys
   ```

   > [!NOTE]
   > 安裝卷時，請確保與 Azure NetApp 檔卷的 NFS 協定版本匹配。 如果 Azure NetApp 檔卷創建為 NFSv3 卷，請使用相應的 NFSv3 配置。 如果 Azure NetApp 檔卷創建為 NFSv4.1 卷，請按照說明禁用 ID 映射並確保使用相應的 NFSv4.1 配置。 在此示例中，Azure NetApp 檔卷創建為 NFSv3 卷。  

   掛接新的共用項目

   ```
   sudo mount -a  
   ```

1. **[A]** 設定分頁檔

   ```
   sudo vi /etc/waagent.conf
   
   # Set the property ResourceDisk.EnableSwap to y
   # Create and use swapfile on resource disk.
   ResourceDisk.EnableSwap=y
   
   # Set the size of the SWAP file with property ResourceDisk.SwapSizeMB
   # The free space of resource disk varies by virtual machine size. Make sure that you do not set a value that is too big. You can check the SWAP space with command swapon
   # Size of the swapfile.
   ResourceDisk.SwapSizeMB=2000
   ```

   重新啟動代理程式以啟動變更

   ```
   sudo service waagent restart
   ```

1. **[A]** RHEL 設定

   按照 SAP Note [2002167] 中的說明設定 RHEL

### <a name="installing-sap-netweaver-ascsers"></a>安裝 SAP NetWeaver ASCS/ERS

1. **[1]** 為 ASCS 執行個體建立虛擬 IP 資源和健康情況探查

   ```
   sudo pcs node standby anftstsapcl2
   # If using NFSv3
   sudo pcs resource create fs_QAS_ASCS Filesystem device='192.168.24.5:/sapQAS/usrsapQASascs' \
     directory='/usr/sap/QAS/ASCS00' fstype='nfs' force_unmount=safe \
     op start interval=0 timeout=60 op stop interval=0 timeout=120 op monitor interval=200 timeout=40 \
     --group g-QAS_ASCS
   
   # If using NFSv4.1
   sudo pcs resource create fs_QAS_ASCS Filesystem device='192.168.24.5:/sapQAS/usrsapQASascs' \
     directory='/usr/sap/QAS/ASCS00' fstype='nfs' force_unmount=safe options='sec=sys,vers=4.1' \
     op start interval=0 timeout=60 op stop interval=0 timeout=120 op monitor interval=200 timeout=40 \
     --group g-QAS_ASCS
   
   sudo pcs resource create vip_QAS_ASCS IPaddr2 \
     ip=192.168.14.9 cidr_netmask=24 \
     --group g-QAS_ASCS
   
   sudo pcs resource create nc_QAS_ASCS azure-lb port=62000 \
     --group g-QAS_ASCS
   ```

   請確定叢集狀態正常，且所有資源皆已啟動。 資源在哪一個節點上執行並不重要。

   ```
   sudo pcs status
   
   # Node anftstsapcl2: standby
   # Online: [ anftstsapcl1 ]
   #
   # Full list of resources:
   #
   # rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
   #  Resource Group: g-QAS_ASCS
   #      fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
   #      nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
   #      vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
   ```

1. **[1]** 安裝 SAP NetWeaver ASCS  

   使用虛擬主機名稱將 SAP NetWeaver ASCS 作為根安裝，該虛擬主機名稱映射到 ASCS 的負載等化器前端配置的 IP 位址，例如 aftstsapvh、192.168.14.9 以及用於負載等化器探測的實例編號（例如<b>00</b>）。 <b>anftstsapvh</b> <b>192.168.14.9</b>

   您可以使用 sapinst 參數 SAPINST_REMOTE_ACCESS_USER 來允許非 root 使用者連線到 sapinst。

   ```
   # Allow access to SWPM. This rule is not permanent. If you reboot the machine, you have to run the command again.
   sudo firewall-cmd --zone=public  --add-port=4237/tcp
   
   sudo <swpm>/sapinst SAPINST_REMOTE_ACCESS_USER=sapadmin SAPINST_USE_HOSTNAME=<virtual_hostname>
   ```

   如果安裝未能在 /usr/sap/**QAS**/ASCS**00**中創建子資料夾，請嘗試設置 ASCS**00**資料夾的擁有者和組並重試。

   ```
   sudo chown qasadm /usr/sap/QAS/ASCS00
   sudo chgrp sapsys /usr/sap/QAS/ASCS00
   ```

1. **[1]** 為 ERS 執行個體建立虛擬 IP 資源和健康情況探查

   ```
   sudo pcs node unstandby anftstsapcl2
   sudo pcs node standby anftstsapcl1
   
   # If using NFSv3
   sudo pcs resource create fs_QAS_AERS Filesystem device='192.168.24.5:/sapQAS/usrsapQASers' \
     directory='/usr/sap/QAS/ERS01' fstype='nfs' force_unmount=safe \
     op start interval=0 timeout=60 op stop interval=0 timeout=120 op monitor interval=200 timeout=40 \
    --group g-QAS_AERS
   
   # If using NFSv4.1
   sudo pcs resource create fs_QAS_AERS Filesystem device='192.168.24.5:/sapQAS/usrsapQASers' \
     directory='/usr/sap/QAS/ERS01' fstype='nfs' force_unmount=safe options='sec=sys,vers=4.1' \
     op start interval=0 timeout=60 op stop interval=0 timeout=120 op monitor interval=200 timeout=40 \
    --group g-QAS_AERS
   
   sudo pcs resource create vip_QAS_AERS IPaddr2 \
     ip=192.168.14.10 cidr_netmask=24 \
    --group g-QAS_AERS
   
   sudo pcs resource create nc_QAS_AERS azure-lb port=62101 \
    --group g-QAS_AERS
   ```
 
   請確定叢集狀態正常，且所有資源皆已啟動。 資源在哪一個節點上執行並不重要。

   ```
   sudo pcs status
   
   # Node anftstsapcl1: standby
   # Online: [ anftstsapcl2 ]
   #
   # Full list of resources:
   #
   # rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl2
   #  Resource Group: g-QAS_ASCS
   #      fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
   #      nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2<
   #      vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
   #  Resource Group: g-QAS_AERS
   #      fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
   #      nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
   #      vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
   ```

1. **[2]** 安裝 SAP NetWeaver ERS  

   使用虛擬主機名稱將 SAP NetWeaver ERS 作為根安裝，該虛擬主機名稱映射到 ERS 的負載等化器前端配置的 IP 位址，例如<b>，192.168.14.10</b>和用於負載等化器探測的實例編號（例如<b>01</b>）。 <b>anftstsapers</b>

   您可以使用 sapinst 參數 SAPINST_REMOTE_ACCESS_USER 來允許非 root 使用者連線到 sapinst。

   ```
   # Allow access to SWPM. This rule is not permanent. If you reboot the machine, you have to run the command again.
   sudo firewall-cmd --zone=public  --add-port=4237/tcp

   sudo <swpm>/sapinst SAPINST_REMOTE_ACCESS_USER=sapadmin SAPINST_USE_HOSTNAME=<virtual_hostname>
   ```

   如果安裝未能在 /usr/sap/**QAS**/ERS**01**中創建子資料夾，請嘗試設置 ERS**01**資料夾的擁有者和組並重試。

   ```
   sudo chown qaadm /usr/sap/QAS/ERS01
   sudo chgrp sapsys /usr/sap/QAS/ERS01
   ```

1. **[1]** 調整 ASCS/SCS 和 ERS 執行個體設定檔

   * ASCS/SCS 設定檔

   ```
   sudo vi /sapmnt/QAS/profile/QAS_ASCS00_anftstsapvh
   
   # Change the restart command to a start command
   #Restart_Program_01 = local $(_EN) pf=$(_PF)
   Start_Program_01 = local $(_EN) pf=$(_PF)
   
   # Add the keep alive parameter
   enque/encni/set_so_keepalive = true
   ```

   * ERS 設定檔

   ```
   sudo vi /sapmnt/QAS/profile/QAS_ERS01_anftstsapers
   
   # Change the restart command to a start command
   #Restart_Program_00 = local $(_ER) pf=$(_PFL) NR=$(SCSID)
   Start_Program_00 = local $(_ER) pf=$(_PFL) NR=$(SCSID)
   
   # remove Autostart from ERS profile
   # Autostart = 1
   ```


1. **[A]** 設定保持運作

   SAP NetWeaver 應用程式伺服器和 ASCS/SCS 之間的通訊是透過軟體負載平衡器來路由傳送。 在逾時時間 (可設定) 過後，負載平衡器就會將非作用中的連線中斷。 為了防止這種情況，您需要在 SAP NetWeaver ASCS/SCS 設定檔中設置參數並更改 Linux 系統設置。 如需詳細資訊，請閱讀 [SAP Note 1410736][1410736]。

   ASCS/SCS 設定檔參數 enque/encni/set_so_keepalive 已在最後一個步驟中新增。

   ```
   # Change the Linux system configuration
   sudo sysctl net.ipv4.tcp_keepalive_time=120
   ```

1. **[A]** 更新 /usr/sap/sapservices 檔案

   若要避免 sapinit 啟動指令碼啟動執行個體，Pacemaker 所管理的所有執行個體都必須從 /usr/sap/sapservices 檔案加上註解。 如果 SAP Hana 執行個體將搭配 Hana SR 使用，請勿為其加上註解。

   ```
   sudo vi /usr/sap/sapservices
   
   # On the node where you installed the ASCS, comment out the following line
   # LD_LIBRARY_PATH=/usr/sap/QAS/ASCS00/exe:$LD_LIBRARY_PATH; export LD_LIBRARY_PATH; /usr/sap/QAS/ASCS00/exe/sapstartsrv pf=/usr/sap/QAS/SYS/profile/QAS_ASCS00_anftstsapvh -D -u qasadm
   
   # On the node where you installed the ERS, comment out the following line
   # LD_LIBRARY_PATH=/usr/sap/QAS/ERS01/exe:$LD_LIBRARY_PATH; export LD_LIBRARY_PATH; /usr/sap/QAS/ERS01/exe/sapstartsrv pf=/usr/sap/QAS/ERS01/profile/QAS_ERS01_anftstsapers -D -u qasadm
   ```

1. **[1]** 建立 SAP 叢集資源

   如果使用排隊伺服器 1 體系結構 （ENSA1），請定義資源，如下所示：

   ```
   sudo pcs property set maintenance-mode=true
   
    sudo pcs resource create rsc_sap_QAS_ASCS00 SAPInstance \
    InstanceName=QAS_ASCS00_anftstsapvh START_PROFILE="/sapmnt/QAS/profile/QAS_ASCS00_anftstsapvh" \
    AUTOMATIC_RECOVER=false \
    meta resource-stickiness=5000 migration-threshold=1 failure-timeout=60 \
    op monitor interval=20 on-fail=restart timeout=60 \
    op start interval=0 timeout=600 op stop interval=0 timeout=600 \
    --group g-QAS_ASCS
   
    sudo pcs resource create rsc_sap_QAS_ERS01 SAPInstance \
    InstanceName=QAS_ERS01_anftstsapers START_PROFILE="/sapmnt/QAS/profile/QAS_ERS01_anftstsapers" \
    AUTOMATIC_RECOVER=false IS_ERS=true \
    op monitor interval=20 on-fail=restart timeout=60 op start interval=0 timeout=600 op stop interval=0 timeout=600 \
    --group g-QAS_AERS
      
    sudo pcs constraint colocation add g-QAS_AERS with g-QAS_ASCS -5000
    sudo pcs constraint location rsc_sap_QAS_ASCS00 rule score=2000 runs_ers_QAS eq 1
    sudo pcs constraint order g-QAS_ASCS then g-QAS_AERS kind=Optional symmetrical=false
    
    sudo pcs node unstandby anftstsapcl1
    sudo pcs property set maintenance-mode=false
    ```

   SAP 引入了對自 SAP NW 7.52 起對排隊伺服器 2（包括複製）的支援。 從 ABAP 平臺 1809 開始，預設情況下安裝排隊伺服器 2。 有關排隊伺服器 2 支援，請參閱 SAP 注釋[2630416。](https://launchpad.support.sap.com/#/notes/2630416)
   如果使用排隊伺服器 2 體系結構[（ENSA2），](https://help.sap.com/viewer/cff8531bc1d9416d91bb6781e628d4e0/1709%20001/en-US/6d655c383abf4c129b0e5c8683e7ecd8.html)請安裝資源代理資源代理-sap-4.1.1-12.el7.x86_64或更新，並定義資源如下：

    ```
    sudo pcs property set maintenance-mode=true
    
    sudo pcs resource create rsc_sap_QAS_ASCS00 SAPInstance \
    InstanceName=QAS_ASCS00_anftstsapvh START_PROFILE="/sapmnt/QAS/profile/QAS_ASCS00_anftstsapvh" \
    AUTOMATIC_RECOVER=false \
    meta resource-stickiness=5000 migration-threshold=1 failure-timeout=60 \
    op monitor interval=20 on-fail=restart timeout=60 \
    op start interval=0 timeout=600 op stop interval=0 timeout=600 \
    --group g-QAS_ASCS
   
    sudo pcs resource create rsc_sap_QAS_ERS01 SAPInstance \
    InstanceName=QAS_ERS01_anftstsapers START_PROFILE="/sapmnt/QAS/profile/QAS_ERS01_anftstsapers" \
    AUTOMATIC_RECOVER=false IS_ERS=true \
    op monitor interval=20 on-fail=restart timeout=60 op start interval=0 timeout=600 op stop interval=0 timeout=600 \
    --group g-QAS_AERS
      
    sudo pcs constraint colocation add g-QAS_AERS with g-QAS_ASCS -5000
    sudo pcs constraint order g-QAS_ASCS then g-QAS_AERS kind=Optional symmetrical=false
    sudo pcs constraint order start g-QAS_ASCS then stop g-QAS_AERS symmetrical=false
   
    sudo pcs node unstandby anftstsapcl1
    sudo pcs property set maintenance-mode=false
    ```

   如果要從舊版本升級到編佇列伺服器 2，請參閱 SAP 注釋[2641322](https://launchpad.support.sap.com/#/notes/2641322)。 

   > [!NOTE]
   > 上述配置中的超時只是示例，可能需要適應特定的 SAP 設置。 

   請確定叢集狀態正常，且所有資源皆已啟動。 資源在哪一個節點上執行並不重要。

    ```
    sudo pcs status
    
    # Online: [ anftstsapcl1 anftstsapcl2 ]
    #
    # Full list of resources:
    #
    # rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl2
    #  Resource Group: g-QAS_ASCS
    #      fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
    #      nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
    #      vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
    #      rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
    #  Resource Group: g-QAS_AERS
    #      fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
    #      nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
    #      vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
    #      rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   ```

1. **[A]** 在兩個節點上添加 ASCS 和 ERS 的防火牆規則 在兩個節點上添加 ASCS 和 ERS 的防火牆規則。
   ```
   # Probe Port of ASCS
   sudo firewall-cmd --zone=public --add-port=62000/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=62000/tcp
   sudo firewall-cmd --zone=public --add-port=3200/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=3200/tcp
   sudo firewall-cmd --zone=public --add-port=3600/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=3600/tcp
   sudo firewall-cmd --zone=public --add-port=3900/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=3900/tcp
   sudo firewall-cmd --zone=public --add-port=8100/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=8100/tcp
   sudo firewall-cmd --zone=public --add-port=50013/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=50013/tcp
   sudo firewall-cmd --zone=public --add-port=50014/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=50014/tcp
   sudo firewall-cmd --zone=public --add-port=50016/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=50016/tcp
   # Probe Port of ERS
   sudo firewall-cmd --zone=public --add-port=62101/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=62101/tcp
   sudo firewall-cmd --zone=public --add-port=3301/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=3301/tcp
   sudo firewall-cmd --zone=public --add-port=50113/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=50113/tcp
   sudo firewall-cmd --zone=public --add-port=50114/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=50114/tcp
   sudo firewall-cmd --zone=public --add-port=50116/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=50116/tcp
   ```

## <a name="sap-netweaver-application-server-preparation"></a><a name="2d6008b0-685d-426c-b59e-6cd281fd45d7"></a>SAP NetWeaver 應用程式伺服器準備

   某些資料庫需要在應用程式伺服器上執行資料庫執行個體安裝。 準備應用程式伺服器虛擬機器，以便在這些情況下使用它們。  

   以下步驟假設您將應用程式伺服器安裝在與 ASCS/SCS 和 HANA 伺服器不同的伺服器上。 否則，您就不必進行以下某些步驟 (例如設定主機名稱解析)。  

   以下專案以 **[A]** - 適用于 PAS 和 AAS，[P] - 僅適用于 PAS 或 **[P]****[S]** - 僅適用于 AAS。  

1. **[A]** 設置主機名稱解析 您可以使用 DNS 伺服器或修改所有節點上的 /etc/主機。 這個範例示範如何使用 /etc/hosts 檔案。
   請取代下列命令中的 IP 位址和主機名稱：  

   ```
   sudo vi /etc/hosts
   ```

   將下列幾行插入至 /etc/hosts。 更改 IP 位址和主機名稱以匹配您的環境。

   ```
   # IP address of the load balancer frontend configuration for SAP NetWeaver ASCS
   192.168.14.9 anftstsapvh
   # IP address of the load balancer frontend configuration for SAP NetWeaver ASCS ERS
   192.168.14.10 anftstsapers
   192.168.14.7 anftstsapa01
   192.168.14.8 anftstsapa02
   ```

1. **[A]** 創建 sapmnt 目錄 創建 sapmnt 目錄。
   ```
   sudo mkdir -p /sapmnt/QAS
   sudo mkdir -p /usr/sap/trans

   sudo chattr +i /sapmnt/QAS
   sudo chattr +i /usr/sap/trans
   ```

1. **[A]** 安裝 NFS 用戶端和其他要求  

   ```
   sudo yum -y install nfs-utils uuidd
   ```

1. **[A]** 新增掛接項目  
   如果使用 NFSv3：
   ```
   sudo vi /etc/fstab
   
   # Add the following lines to fstab, save and exit
   192.168.24.5:/sapQAS/sapmntQAS /sapmnt/QAS nfs rw,hard,rsize=65536,wsize=65536,vers=3
   192.168.24.4:/transSAP /usr/sap/trans nfs rw,hard,rsize=65536,wsize=65536,vers=3
   ```

   如果使用 NFSv4.1：
   ```
   sudo vi /etc/fstab
   
   # Add the following lines to fstab, save and exit
   192.168.24.5:/sapQAS/sapmntQAS /sapmnt/QAS nfs rw,hard,rsize=65536,wsize=65536,vers=4.1,sec=sys
   192.168.24.4:/transSAP /usr/sap/trans nfs rw,hard,rsize=65536,wsize=65536,vers=4.1,sec=sys
   ```

   掛接新的共用項目

   ```
   sudo mount -a
   ```

1. **[P]** 創建並裝載 PAS 目錄  
   如果使用 NFSv3：
   ```
   sudo mkdir -p /usr/sap/QAS/D02
   sudo chattr +i /usr/sap/QAS/D02
   
   sudo vi /etc/fstab
   # Add the following line to fstab
   92.168.24.5:/sapQAS/usrsapQASpas /usr/sap/QAS/D02 nfs rw,hard,rsize=65536,wsize=65536,vers=3
   
   # Mount
   sudo mount -a
   ```

   如果使用 NFSv4.1：
   ```
   sudo mkdir -p /usr/sap/QAS/D02
   sudo chattr +i /usr/sap/QAS/D02
   
   sudo vi /etc/fstab
   # Add the following line to fstab
   92.168.24.5:/sapQAS/usrsapQASpas /usr/sap/QAS/D02 nfs rw,hard,rsize=65536,wsize=65536,vers=4.1,sec=sys
   
   # Mount
   sudo mount -a
   ```

1. **[S]** 創建並裝載 AAS 目錄  
   如果使用 NFSv3：
   ```
   sudo mkdir -p /usr/sap/QAS/D03
   sudo chattr +i /usr/sap/QAS/D03
   
   sudo vi /etc/fstab
   # Add the following line to fstab
   92.168.24.5:/sapQAS/usrsapQASaas /usr/sap/QAS/D03 nfs rw,hard,rsize=65536,wsize=65536,vers=3
   
   # Mount
   sudo mount -a
   ```

   如果使用 NFSv4.1：
   ```
   sudo mkdir -p /usr/sap/QAS/D03
   sudo chattr +i /usr/sap/QAS/D03
   
   sudo vi /etc/fstab
   # Add the following line to fstab
   92.168.24.5:/sapQAS/usrsapQASaas /usr/sap/QAS/D03 nfs rw,hard,rsize=65536,wsize=65536,vers=4.1,sec=sys
   
   # Mount
   sudo mount -a
   ```

1. **[A]** 設定分頁檔
 
   ```
   sudo vi /etc/waagent.conf
   
   # Set the property ResourceDisk.EnableSwap to y
   # Create and use swapfile on resource disk.
   ResourceDisk.EnableSwap=y
   
   # Set the size of the SWAP file with property ResourceDisk.SwapSizeMB
   # The free space of resource disk varies by virtual machine size. Make sure that you do not set a value that is too big. You can check the SWAP space with command swapon
   # Size of the swapfile.
   ResourceDisk.SwapSizeMB=2000
   ```

   重新啟動代理程式以啟動變更

   ```
   sudo service waagent restart
   ```

## <a name="install-database"></a>安裝資料庫

在此範例中，SAP NetWeaver 安裝在 SAP Hana 上。 您可以針對此安裝使用每個支援的資料庫。 有關如何在 Azure 中安裝 SAP HANA 的詳細資訊，請參閱. For a list of supported databases, see [SAP Note 1928533][1928533][紅帽企業 Linux 上的 Azure VM 上 SAP HANA 的高可用性][sap-hana-ha]。

1. 執行 SAP 資料庫執行個體安裝

   使用映射到資料庫負載等化器前端配置的 IP 位址的虛擬主機名稱將 SAP NetWeaver 資料庫實例安裝為根。

   您可以使用 sapinst 參數 SAPINST_REMOTE_ACCESS_USER 來允許非 root 使用者連線到 sapinst。

   ```
   sudo <swpm>/sapinst SAPINST_REMOTE_ACCESS_USER=sapadmin
   ```

## <a name="sap-netweaver-application-server-installation"></a>SAP NetWeaver 應用程式伺服器安裝

請遵循下列步驟來安裝 SAP 應用程式伺服器。

1. 準備應用程式伺服器

   依照上述 [SAP NetWeaver 應用程式伺服器準備](high-availability-guide-rhel.md#2d6008b0-685d-426c-b59e-6cd281fd45d7)章節中的步驟，準備應用程式伺服器。

1. 安裝 SAP NetWeaver 應用程式伺服器

   安裝主要或其他的 SAP NetWeaver 應用程式伺服器。

   您可以使用 sapinst 參數 SAPINST_REMOTE_ACCESS_USER 來允許非 root 使用者連線到 sapinst。

   ```
   sudo <swpm>/sapinst SAPINST_REMOTE_ACCESS_USER=sapadmin
   ```

1. 更新 SAP HANA 安全存放區

   將 SAP HANA 安全存放區更新為指向 SAP HANA 系統複寫設定的虛擬名稱。

   執行下列命令來將項目列出為 \<sapsid>adm

   ```
   hdbuserstore List
   ```

   這樣應該會列出所有項目，且看起來應該類似
   ```
   DATA FILE       : /home/qasadm/.hdb/anftstsapa01/SSFS_HDB.DAT
   KEY FILE        : /home/qasadm/.hdb/anftstsapa01/SSFS_HDB.KEY
   
   KEY DEFAULT
     ENV : 192.168.14.4:30313
     USER: SAPABAP1
     DATABASE: QAS
   ```

   輸出會顯示預設項目的 IP 位址指向虛擬機器，而不是指向負載平衡器的 IP 位址。 這個項目必須變更才能指向負載平衡器的虛擬機器主機名稱。 請確保使用相同的埠（上面輸出中的**30313）** 和資料庫名稱（上面輸出中的**QAS）！**

   ```
   su - qasadm
   hdbuserstore SET DEFAULT qasdb:30313@QAS SAPABAP1 <password of ABAP schema>
   ```

## <a name="test-the-cluster-setup"></a>測試叢集設定

1. 手動移轉 ASCS 執行個體

   開始測試之前的資源狀態：

   ```
    rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   ```

   以 root 身份執行下列命令，來遷移 ASCS 執行個體。

   ```
   [root@anftstsapcl1 ~]# pcs resource move rsc_sap_QAS_ASCS00
   
   [root@anftstsapcl1 ~]# pcs resource clear rsc_sap_QAS_ASCS00
   
   # Remove failed actions for the ERS that occurred as part of the migration
   [root@anftstsapcl1 ~]# pcs resource cleanup rsc_sap_QAS_ERS01
   ```

   測試完成之後的資源狀態：

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   ```

1. 模擬節點損毀

   開始測試之前的資源狀態：

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   ```

   以 root 身份在執行 ASCS 執行個體的節點上執行下列命令

   ```
   [root@anftstsapcl2 ~]# echo b > /proc/sysrq-trigger
   ```

   節點在重新啟動後的狀態應該會像下面這樣。

   ```
   Online: [ anftstsapcl1 anftstsapcl2 ]
   
   Full list of resources:
   
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   
   Failed Actions:
   * rsc_sap_QAS_ERS01_monitor_11000 on anftstsapcl1 'not running' (7): call=45, status=complete, exitreason='',
   ```

   使用下列命令來清除失敗的資源。

   ```
   [root@anftstsapcl1 ~]# pcs resource cleanup rsc_sap_QAS_ERS01
   ```

   測試完成之後的資源狀態：

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   ```

1. 終止訊息伺服器處理程序

   開始測試之前的資源狀態：

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   ```
   
   以 root 身份執行下列命令，找出訊息伺服器的處理程序，並將其終止。

   ```
   [root@anftstsapcl1 ~]# pgrep ms.sapQAS | xargs kill -9
   ```

   如果只終止郵件伺服器一次，它將由`sapstart`重新開機。 如果您終止伺服器的次數足夠，則 Pacemaker 最終會將 ASCS 執行個體移到另一個節點。 以 root 身份執行下列命令，以在測試之後清除 ASCS 和 ERS 執行個體的資源狀態。

   ```
   [root@anftstsapcl1 ~]# pcs resource cleanup rsc_sap_QAS_ASCS00
   [root@anftstsapcl1 ~]# pcs resource cleanup rsc_sap_QAS_ERS01
   ```

   測試完成之後的資源狀態：

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   ```

1. 終止佇列伺服器處理程序

   開始測試之前的資源狀態：

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   ```

   以 root 身份在執行 ASCS 執行個體的節點上執行下列命令，以終止佇列伺服器。

   ```
   [root@anftstsapcl2 ~]# pgrep en.sapQAS | xargs kill -9
   ```

   ASCS 執行個體應會立即容錯移轉到另一個節點。 在 ASCS 執行個體啟動之後，ERS 執行個體應該也會進行容錯移轉。 以 root 身份執行下列命令，以在測試之後清除 ASCS 和 ERS 執行個體的資源狀態。

   ```
   [root@anftstsapcl2 ~]# pcs resource cleanup rsc_sap_QAS_ASCS00
   [root@anftstsapcl2 ~]# pcs resource cleanup rsc_sap_QAS_ERS01
   ```

   測試完成之後的資源狀態：

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   ```

1. 終止佇列複寫伺服器處理程序

   開始測試之前的資源狀態：

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   ```

   以 root 身份在執行 ERS 執行個體的節點上執行下列命令，以終止佇列複寫伺服器處理程序。

   ```
   [root@anftstsapcl2 ~]# pgrep er.sapQAS | xargs kill -9
   ```

   如果只運行該命令一次，`sapstart`將重新開機進程。 如果經常運行它，`sapstart`將不會重新開機進程，並且資源將處於已停止狀態。 以 root 身份執行下列命令，以在測試之後清除 ERS 執行個體的資源狀態。

   ```
   [root@anftstsapcl2 ~]# pcs resource cleanup rsc_sap_QAS_ERS01
   ```

   測試完成之後的資源狀態：

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   ```

1. 終止佇列 sapstartsrv 程序

   開始測試之前的資源狀態：

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   ```

   以 root 身份在執行 ASCS 的節點上執行下列命令。

   ```
   [root@anftstsapcl1 ~]# pgrep -fl ASCS00.*sapstartsrv
   # 59545 sapstartsrv
   
   [root@anftstsapcl1 ~]# kill -9 59545
   ```

   應做為監視程序的一部分，一律由 Pacemaker 資源代理程式來重新啟動 sapstartsrv 處理序。 測試完成之後的資源狀態：

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   ```

## <a name="next-steps"></a>後續步驟

* [用於 SAP 應用程式多 SID 指南的 RHEL 上的 Azure VM 上的 SAP NW HA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-multi-sid)
* [適用於 SAP 的 Azure 虛擬機器規劃和實作][planning-guide]
* [適用於 SAP 的 Azure 虛擬機器部署][deployment-guide]
* [適用於 SAP 的 Azure 虛擬機器 DBMS 部署][dbms-guide]
* 要瞭解如何在 Azure（大型實例）上建立 SAP HANA 的高可用性和災害復原計畫，請參閱[Azure 上的 SAP HANA（大型實例）高可用性和災害復原](hana-overview-high-availability-disaster-recovery.md)。
* 若要了解如何建立高可用性並為 Azure VM 上的 SAP HANA 規劃災害復原，請參閱 [Azure 虛擬機器 (VM) 上 SAP HANA 的高可用性][sap-hana-ha]
