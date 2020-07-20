---
title: 使用 Azure NetApp Files 在 RHEL 上進行 SAP NW 的 Azure Vm 高可用性 |Microsoft Docs
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
ms.date: 04/24/2020
ms.author: radeltch
ms.openlocfilehash: 601194d3a8cc789c51b8e127001ab2367dceeee7
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "82148227"
---
# <a name="azure-virtual-machines-high-availability-for-sap-netweaver-on-red-hat-enterprise-linux-with-azure-netapp-files-for-sap-applications"></a>Azure 虛擬機器高可用性，適用于 Red Hat Enterprise Linux 上的 SAP NetWeaver，以及適用于 SAP 應用程式的 Azure NetApp Files

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

本文描述如何使用 [Azure NetApp Files](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-introduction/) 來部署虛擬機器、設定虛擬機器、安裝叢集架構，以及安裝高可用性的 SAP NetWeaver 7.50 系統。
在範例設定中，安裝命令等。ASCS 實例是數位00，ERS 實例是數位01，主要應用程式實例（PAS）是02，而應用程式實例（.AAS）是03。 其中會使用 SAP 系統識別碼 QAS。 

本文不會詳細描述資料庫層。  

請先閱讀下列 SAP Note 和文件：

* [Azure NetApp Files 文件][anf-azure-doc] 
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
  * [在 RHEL 上的 Pacemaker 中使用獨立的排入佇列伺服器2（ENSA2）設定 SAP S/4HANA ASCS/ERS](https://access.redhat.com/articles/3974941)
* Azure 專用 RHEL 文件：
  * [RHEL 高可用性叢集的支援原則：以 Microsoft Azure 虛擬機器作為叢集成員](https://access.redhat.com/articles/3131341)
  * [在 Microsoft Azure 上安裝和設定 Red Hat Enterprise Linux 7.4 (和更新版本) 高可用性叢集](https://access.redhat.com/articles/3252491)
* [使用 Azure NetApp Files 在 Microsoft Azure 上的 NetApp SAP 應用程式][anf-sap-applications-azure]

## <a name="overview"></a>概觀

SAP Netweaver 中央服務的高可用性 (HA) 需要共用儲存體。
若要在 Red Hat Linux 上達到此目標，您必須建立個別的高可用性 GlusterFS 叢集。 

現在您可使用在 Azure NetApp Files 上部署的共用儲存體來達到 SAP Netweaver HA。 將 Azure NetApp Files 用於共用存放裝置，就不需要額外的[GlusterFS](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-glusterfs)叢集。 SAP Netweaver 中央服務 (ASCS/SCS) 的 HA 仍然需要 Pacemaker。

![SAP NetWeaver 高可用性概觀](./media/high-availability-guide-rhel/high-availability-guide-rhel-anf.png)

SAP NetWeaver ASCS、SAP NetWeaver SCS、SAP NetWeaver ERS 和 SAP Hana 資料庫會使用虛擬主機名稱和虛擬 IP 位址。 在 Azure 上必須有負載平衡器才能使用虛擬 IP 位址。 我們建議使用 [tandard Load Balancer](https://docs.microsoft.com/azure/load-balancer/quickstart-load-balancer-standard-public-portal)。 下列清單顯示負載平衡器的設定，其中包含適用于（A） SCS 和 ERS 的個別前端 Ip。

### <a name="ascs"></a>(A)SCS

* 前端組態
  * IP 位址192.168.14。9
* 探查連接埠
  * 連接埠 620<strong>&lt;nr&gt;</strong>
* 負載平衡規則
  * 若使用 Standard Load Balancer，請選取 [HA 連接埠]
  * 32<strong>&lt;nr&gt;</strong> TCP
  * 36<strong>&lt;nr&gt;</strong> TCP
  * 39<strong>&lt;nr&gt;</strong> TCP
  * 81<strong>&lt;nr&gt;</strong> TCP
  * 5<strong>&lt;nr&gt;</strong>13 TCP
  * 5<strong>&lt;nr&gt;</strong>14 TCP
  * 5<strong>&lt;nr&gt;</strong>16 TCP

### <a name="ers"></a>ERS

* 前端組態
  * IP 位址192.168.14.10
* 探查連接埠
  * 連接埠 621<strong>&lt;nr&gt;</strong>
* 負載平衡規則
  * 若使用 Standard Load Balancer，請選取 [HA 連接埠]
  * 32<strong>&lt;nr&gt;</strong> TCP
  * 33<strong>&lt;nr&gt;</strong> TCP
  * 5<strong>&lt;nr&gt;</strong>13 TCP
  * 5<strong>&lt;nr&gt;</strong>14 TCP
  * 5<strong>&lt;nr&gt;</strong>16 TCP

* 後端組態
  * 連線到應該屬於 (A)SCS/ERS 叢集一部分之所有虛擬機器的主要網路介面

## <a name="setting-up-the-azure-netapp-files-infrastructure"></a>設定 Azure NetApp Files 基礎結構 

SAP NetWeaver 需要傳輸和設定檔目錄的共用儲存體。  在繼續設定 Azure NetApp 檔案基礎結構之前，請先熟悉 [Azure NetApp Files 文件][anf-azure-doc]。 檢查選取的 Azure 區域是否提供 Azure NetApp 檔案。 下列連結會依據 Azure 區域顯示 Azure NetApp Files 的可用性：[Azure NetApp Files 可用性 (依 Azure 區域)][anf-avail-matrix]。

Azure NetApp files 在數個[azure 區域](https://azure.microsoft.com/global-infrastructure/services/?products=netapp)中都有提供。 在部署 Azure NetApp Files 前，請遵循 [Azure NetApp 檔案指示][anf-register]要求上架至 Azure NetApp Files。 

### <a name="deploy-azure-netapp-files-resources"></a>部署 Azure NetApp Files 資源  

這些步驟假設已部署 [Azure 虛擬網路](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview)。 Azure NetApp Files 資源和 VM，其中將掛接的 Azure NetApp Files 資源必須部署到相同的 Azure 虛擬網路，或部署到對等互連 Azure 虛擬網路。  

1. 若尚未進行該操作，請要求[上架至 Azure NetApp Files](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-register)。  
2. 遵循[建立 NetApp 帳戶的指示](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-netapp-account)，在選取的 Azure 區域內建立 NetApp 帳戶。  
3. 遵循[設定 Azure NetApp Files 容量集區的指示](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-set-up-capacity-pool)，設定 Azure NetApp Files 容量集區。  
本文中呈現的 SAP Netweaver 架構使用單一 Azure NetApp Files 容量集區，以及進階 SKU。 我們建議針對 Azure 上的 SAP Netweaver 應用程式工作負載使用 Azure NetApp Files 進階 SKU。  
4. 遵循[將子網路委派給 Azure NetApp Files 中的指示](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-delegate-subnet)，將子網路委派給 Azure NetApp 檔案。  

5. 遵循[建立 Azure NetApp Files 磁碟區的指示](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-volumes)，部署 Azure NetApp Files 磁碟區。 在指定的 Azure NetApp Files [子網路](https://docs.microsoft.com/rest/api/virtualnetwork/subnets)中部署磁碟區。 Azure NetApp 磁碟區的 IP 位址會自動指派。 請記得，Azure NetApp Files 資源和 Azure VM 必須位於相同的 Azure 虛擬網路，或位於對等互連 Azure 虛擬網路中。 在此範例中，我們會使用兩個 Azure NetApp Files 磁片區： sap<b>QAS</b>和 transSAP。 裝載至對應掛接點的檔案路徑為/usrsap<b>qas</b>/sapmnt<b>qas</b>、/usrsap<b>qas</b><b>/usrsap qas sys 等等</b>。  

   1. volume sap<b>QAS</b> （nfs://192.168.24.5/usrsap<b>QAS</b>/sapmnt<b>QAS</b>）
   2. volume sap<b>QAS</b> （nfs://192.168.24.5/usrsap<b>QAS</b>/usrsap<b>QAS</b>ascs）
   3. volume sap<b>QAS</b> （nfs://192.168.24.5/usrsap<b>QAS</b>/usrsap<b>QAS</b>sys）
   4. volume sap<b>QAS</b> （nfs://192.168.24.5/usrsap<b>QAS</b>/usrsap<b>QAS</b>ers）
   5. 磁片區 transSAP （nfs://192.168.24.4/transSAP）
   6. volume sap<b>QAS</b> （nfs://192.168.24.5/usrsap<b>QAS</b>/usrsap<b>QAS</b>pas）
   7. volume sap<b>QAS</b> （nfs://192.168.24.5/usrsap<b>QAS</b>/usrsap<b>QAS</b>.aas）
  
在本範例中，我們針對所有 SAP Netweaver 檔案系統使用了 Azure NetApp Files 來示範使用 Azure NetApp Files 的方式。 不需要透過 NFS 掛接的 SAP 檔案系統也可作為 [Azure 磁碟儲存體](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#premium-ssd)來部署。 在本範例中，<b>a-e</b> 必須位於 Azure NetApp Files 上，<b>f-g</b> (即 /usr/sap/<b>QAS</b>/D<b>02</b>、/usr/sap<b>QAS</b>/D<b>03</b>) 可以作為 Azure 磁碟儲存體部署。 

### <a name="important-considerations"></a>重要考量︰

針對 SUSE 高可用性架構上的 SAP Netweaver 考慮 Azure NetApp Files 時，請注意下列重要考量：

- 最小容量集區為 4 TiB。 容量集區大小可以增加 1 TiB 增量。
- 最小磁碟區為 100 GiB
- Azure NetApp Files 和所有虛擬機器 (將掛接 Azure NetApp Files 磁碟區的位置) 必須位於相同 Azure 虛擬網路中，或位於相同區域的[對等互連虛擬網路](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview)中。 Azure NetApp Files 現在支援透過相同區域內的 VNET 對等互連進行存取。 Azure NetApp 尚不支援透過全域對等互連進行存取。
- 所選取虛擬網路必須具備委派給 Azure NetApp Files 的子網路。
- Azure NetApp Files 提供[匯出原則](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-configure-export-policy)：您可控制允許的用戶端、存取類型 (讀取及寫入、唯讀等)。 
- Azure NetApp Files 功能尚無法感知區域。 Azure NetApp Files 功能目前不會部署在 Azure 區域中的所有可用性區域。 請留意某些 Azure 區域中可能出現的延遲情形。 
- Azure NetApp Files 磁碟區可作為 NFSv3 或 NFSv4.1 磁碟區部署。 SAP 應用程式層 (ASCS/ERS、SAP 應用程式伺服器) 支援這兩個通訊協定。 

## <a name="setting-up-ascs"></a>設定 (A)SCS

在此範例中，會透過[Azure 入口網站](https://portal.azure.com/#home)手動部署資源。

### <a name="deploy-linux-manually-via-azure-portal"></a>透過 Azure 入口網站手動部署 Linux

首先您需要建立 Azure NetApp Files 磁碟區。 部署 VM。 之後，您需建立負載平衡器，然後使用後端集區中的虛擬機器。

1. 建立負載平衡器 (內部、標準)：  
   1. 建立前端 IP 位址
      1. ASCS 的 IP 位址192.168.14。9
         1. 開啟負載平衡器，選取前端 IP 集區，然後按一下 [新增]
         1. 輸入新前端 IP 集區的名稱 (例如 **frontend.QAS.ASCS**)
         1. 將 [指派] 設定為 [靜態]，然後輸入 IP 位址（例如**192.168.14.9**）
         1. Click OK
      1. ASCS ERS 的 IP 位址192.168.14.10
         * 在 "a" 底下重複上述步驟，以建立 ERS 的 IP 位址（例如**192.168.14.10**和前端） **。QAS.ERS**）
   1. 建立後端集區
      1. 開啟負載平衡器，選取後端集區，然後按一下 [新增]
      1. 輸入新後端集區的名稱 (例如 **backend.QAS**)
      1. 按一下 [新增虛擬機器]。
      1. 選取 [虛擬機器]。 
      1. 選取 (A)SCS 叢集的虛擬機器及其 IP 位址。
      1. 按一下 [新增]
   1. 建立健康狀態探查
      1. 針對 ASCS 是連接埠 620**00**
         1. 開啟負載平衡器，選取健康情況探查，然後按一下 [新增]
         1. 輸入新健康狀態探查的名稱 (例如 **health.QAS.ASCS**)
         1. 選取 [TCP] 作為通訊協定、連接埠 620**00**，保留 [間隔] 5 和 [狀況不良閾值] 2
         1. Click OK
      1. ASCS ERS 為連接埠 621**01**
            * 在 "c" 下方重複上述步驟來為 ERS 建立健康狀態探查 (例如 621**01** 和 **health.QAS.ERS**)
   1. 負載平衡規則
      1. ASCS 的負載平衡規則
         1. 開啟負載平衡器，選取 [負載平衡規則]，然後按一下 [新增]
         1. 輸入新負載平衡器規則的名稱 (例如 **lb.QAS.ASCS**)
         1. 選取 ASCS 的前端 IP 位址、後端集區，以及先前建立的健康狀態探查 (例如 **frontend.QAS.ASCS**、**backend.QAS** 和 **health.QAS.ASCS**)
         1. 選取 [HA 連接埠]
         1. 將閒置逾時增加為 30 分鐘
         1. **務必啟用浮動 IP**
         1. Click OK
         * 重複上述步驟來為 ERS 建立負載平衡規則 (例如 **lb.QAS.ERS**)
1. 或者，若案例需要基本負載平衡器 (內部)，請遵循這些步驟：  
   1. 建立前端 IP 位址
      1. ASCS 的 IP 位址192.168.14。9
         1. 開啟負載平衡器，選取前端 IP 集區，然後按一下 [新增]
         1. 輸入新前端 IP 集區的名稱 (例如 **frontend.QAS.ASCS**)
         1. 將 [指派] 設定為 [靜態]，然後輸入 IP 位址（例如**192.168.14.9**）
         1. Click OK
      1. ASCS ERS 的 IP 位址192.168.14.10
         * 在 "a" 底下重複上述步驟，以建立 ERS 的 IP 位址（例如**192.168.14.10**和前端） **。QAS.ERS**）
   1. 建立後端集區
      1. 開啟負載平衡器，選取後端集區，然後按一下 [新增]
      1. 輸入新後端集區的名稱 (例如 **backend.QAS**)
      1. 按一下 [新增虛擬機器]。
      1. 選取先前為 ASCS 建立的可用性設定組 
      1. 選取 (A)SCS 叢集的虛擬機器
      1. Click OK
   1. 建立健康狀態探查
      1. 針對 ASCS 是連接埠 620**00**
         1. 開啟負載平衡器，選取健康情況探查，然後按一下 [新增]
         1. 輸入新健康狀態探查的名稱 (例如 **health.QAS.ASCS**)
         1. 選取 [TCP] 作為通訊協定、連接埠 620**00**，保留 [間隔] 5 和 [狀況不良閾值] 2
         1. Click OK
      1. ASCS ERS 為連接埠 621**01**
            * 在 "c" 下方重複上述步驟來為 ERS 建立健康狀態探查 (例如 621**01** 和 **health.QAS.ERS**)
   1. 負載平衡規則
      1. 針對 ASCS 是 32**00** TCP
         1. 開啟負載平衡器，選取 [負載平衡規則]，然後按一下 [新增]
         1. 輸入新負載平衡器規則的名稱 (例如 **lb.QAS.ASCS.3200**)
         1. 選取先前為 ASCS 建立的前端 IP 位址、後端集區及先前建立的健康狀態探查 (例如 **frontend.QAS.ASCS**)
         1. 保留通訊協定 [TCP]，輸入連接埠 **3200**
         1. 將閒置逾時增加為 30 分鐘
         1. **務必啟用浮動 IP**
         1. Click OK
      1. ASCS 的其他連接埠
         * 在 "d" 下方針對 ASCS 的連接埠 36**00**、39**00**、81**00**、5**00**13、5**00**14、5**00**16 和 TCP 重複上述步驟
      1. ASCS ERS 的其他連接埠
         * 在 "d" 的下方針對 ASCS ERS 的連接埠 32**01**、33**01**、5**01**13、5**01**14、5**01**16 和 TCP 重複上述步驟

      > [!Note]
      > 當不具公用 IP 位址的 VM 放在內部 (沒有公用 IP 位址) Standard Azure Load Balancer 的後端集區時，除非另外設定來允許路由傳送至公用端點，否則不會有輸出網際網路連線能力。 如需如何實現輸出連線能力的詳細資料，請參閱[在 SAP 高可用性案例中使用 Azure Standard Load Balancer 實現虛擬機器的公用端點連線能力](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-standard-load-balancer-outbound-connections)。  

      > [!IMPORTANT]
      > 請勿在位於 Azure Load Balancer 後方的 Azure VM 上啟用 TCP 時間戳記。 啟用 TCP 時間戳記會導致健康狀態探查失敗。 將參數 **net.ipv4.tcp_timestamps** 設定為 **0**。 如需詳細資料，請參閱 [Load Balancer 健康狀態探查](https://docs.microsoft.com/azure/load-balancer/load-balancer-custom-probe-overview)。

## <a name="disable-id-mapping-if-using-nfsv41"></a>停用識別碼對應 (若使用 NFSv4.1)

本節中的指示只有在搭配 NFSv4.1 通訊協定使用 Azure NetApp Files 磁碟區時才適用。 在所有將掛接 Azure NetApp Files NFSv4.1 磁碟區的 VM 上執行設定。  

1. 驗證 NFS 網域設定。 確認網域已設為預設 Azure NetApp Files 網域，即將 **`defaultv4iddomain.com`** 和對應設為 **nobody**。  

    > [!IMPORTANT]
    > 確認在 VM 上的 `/etc/idmapd.conf` 內設定 NFS 網域，使其與 Azure NetApp Files 上的預設網域設定相符： **`defaultv4iddomain.com`** 。 若 NFS 用戶端 (即 VM) 上網域設定和 NFS 伺服器的網域設定 (即 Azure NetApp 設定) 不相符，則掛接在 VM 上 Azure NetApp 磁碟區上檔案的權限將會顯示為 `nobody`。  

    <pre><code>
    sudo cat /etc/idmapd.conf
    # Example
    [General]
    Domain = <b>defaultv4iddomain.com</b>
    [Mapping]
    Nobody-User = <b>nobody</b>
    Nobody-Group = <b>nobody</b>
    </code></pre>

4. **[A]** 驗證 `nfs4_disable_idmapping`。 其應設為 **Y**。若要建立 `nfs4_disable_idmapping` 所在的目錄結構，請執行掛接命令。 您將無法手動在 /sys/modules 下建立目錄，因為其存取已保留給核心/驅動程式。  

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

   如需如何變更參數的詳細資訊， `nfs4_disable_idmapping` 請參閱 https://access.redhat.com/solutions/1749883 。

### <a name="create-pacemaker-cluster"></a>建立 Pacemaker 叢集

依照[在 Azure 中於 Red Hat Enterprise Linux 上設定 Pacemaker](high-availability-guide-rhel-pacemaker.md) 中的步驟，建立此 (A)SCS 伺服器的基本 Pacemaker 叢集。

### <a name="prepare-for-sap-netweaver-installation"></a>準備進行 SAP NetWeaver 安裝

下列項目會加上下列其中一個前置詞： **[A]** - 適用於所有節點、 **[1]** - 僅適用於節點 1 或 **[2]** - 僅適用於節點 2。

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

1. **[1]** 在 Azure NetApp Files 磁碟區中建立 SAP 目錄。  
   將 Azure NetApp Files 磁碟區暫時掛接在其中一部 VM 上，然後建立 SAP 目錄 (檔案路徑)。  

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

1. **[A]** 安裝 NFS 用戶端和其他需求

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

   如果使用 NFSv 4.1：
   ```
   sudo vi /etc/fstab
   
   # Add the following lines to fstab, save and exit
    192.168.24.5:/sapQAS/sapmntQAS /sapmnt/QAS nfs rw,hard,rsize=65536,wsize=65536,vers=4.1,sec=sys
    192.168.24.5:/sapQAS/usrsapQASsys /usr/sap/QAS/SYS nfs rw,hard,rsize=65536,wsize=65536,vers=4.1,sec=sys
    192.168.24.4:/transSAP /usr/sap/trans nfs rw,hard,rsize=65536,wsize=65536,vers=4.1,sec=sys
   ```

   > [!NOTE]
   > 請務必在掛接磁碟區時比對 Azure NetApp Files 磁碟區的 NFS 通訊協定版本。 若 Azure NetApp Files 磁碟區是使用 NFSv3 磁碟區建立的，請使用對應的 NFSv3 設定。 若 Azure NetApp Files 磁碟區是使用 NFSv4.1 磁碟區建立的，請遵循指示來停用識別碼對應，並確認使用對應的 NFSv4.1 設定。 在本範例中，Azure NetApp Files 磁碟區是使用 NFSv3 磁碟區建立的。  

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

   使用對應至 ASCS 負載平衡器前端設定之 IP 位址的虛擬主機，在第一個節點上以 root 身分安裝 SAP NetWeaver ASCS，例如<b>anftstsapvh</b>、 <b>192.168.14.9</b>和您用於探查負載平衡器的實例編號，例如<b>00</b>。

   您可以使用 sapinst 參數 SAPINST_REMOTE_ACCESS_USER 來允許非 root 使用者連線到 sapinst。

   ```
   # Allow access to SWPM. This rule is not permanent. If you reboot the machine, you have to run the command again.
   sudo firewall-cmd --zone=public  --add-port=4237/tcp
   
   sudo <swpm>/sapinst SAPINST_REMOTE_ACCESS_USER=sapadmin SAPINST_USE_HOSTNAME=<virtual_hostname>
   ```

   如果安裝無法在/usr/sap/**QAS**/ASCS**00**中建立子資料夾，請嘗試設定 ASCS**00**資料夾的擁有者和群組，然後重試。

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

   使用對應至 ERS 負載平衡器前端設定之 IP 位址的虛擬主機（例如<b>anftstsapers</b>、 <b>192.168.14.10</b>和您用於探查負載平衡器的實例號碼），在第二個節點上以 Root 身分安裝 SAP NetWeaver ERS，例如<b>01</b>。

   您可以使用 sapinst 參數 SAPINST_REMOTE_ACCESS_USER 來允許非 root 使用者連線到 sapinst。

   ```
   # Allow access to SWPM. This rule is not permanent. If you reboot the machine, you have to run the command again.
   sudo firewall-cmd --zone=public  --add-port=4237/tcp

   sudo <swpm>/sapinst SAPINST_REMOTE_ACCESS_USER=sapadmin SAPINST_USE_HOSTNAME=<virtual_hostname>
   ```

   如果安裝無法在 /usr/sap/**QAS**/ERS**01** 中建立子資料夾，請嘗試設定 ERS**01** 資料夾的擁有者和群組，並進行重試。

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

   SAP NetWeaver 應用程式伺服器和 ASCS/SCS 之間的通訊是透過軟體負載平衡器來路由傳送。 在逾時時間 (可設定) 過後，負載平衡器就會將非作用中的連線中斷。 若要避免這種情況，您必須在 SAP NetWeaver ASCS/SCS 設定檔中設定參數，並變更 Linux 系統設定。 如需詳細資訊，請閱讀 [SAP Note 1410736][1410736]。

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

   若使用加入佇列伺服器 1 架構 (ENSA1)，請按照下列方式來定義資源：

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

   SAP 在 SAP NW 7.52 中引進了加入佇列伺服器 2 的支援 (包括複寫)。 從 ABAP 平台 1809 開始，根據預設會安裝加入佇列伺服器 2。 如需加入佇列伺服器 2 的支援，請參閱 SAP Note [2630416](https://launchpad.support.sap.com/#/notes/2630416)。
   如果使用佇列伺服器2架構（[ENSA2](https://help.sap.com/viewer/cff8531bc1d9416d91bb6781e628d4e0/1709%20001/en-US/6d655c383abf4c129b0e5c8683e7ecd8.html)），請安裝資源代理程式 resource-agents-sap-4.1.1 12-01.2.2. el7. x86_64 或更新版本，並定義資源，如下所示：

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

   如果您要從舊版升級並切換至排入佇列伺服器2，請參閱 SAP 附注[2641322](https://launchpad.support.sap.com/#/notes/2641322)。 

   > [!NOTE]
   > 上述設定中的超時只是範例，可能需要針對特定的 SAP 設定進行調整。 

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

1. **[A]** 在兩個節點上新增適用于 ASCS 和 ERS 的防火牆規則在這兩個節點上新增 ASCS 和 ERS 的防火牆規則。
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

   下列項目會加上下列其中一個前置 **[A]** (適用於 PAS 和 AAS)、 **[P]** (僅適用於 PAS)，或 **[S]** (僅適用於 AAS)。  

1. **[A]** 設定主機名稱解析您可以使用 DNS 伺服器，或修改所有節點上的/etc/hosts。 這個範例示範如何使用 /etc/hosts 檔案。
   請取代下列命令中的 IP 位址和主機名稱：  

   ```
   sudo vi /etc/hosts
   ```

   將下列幾行插入至 /etc/hosts。 變更 IP 位址和主機名稱以符合您的環境。

   ```
   # IP address of the load balancer frontend configuration for SAP NetWeaver ASCS
   192.168.14.9 anftstsapvh
   # IP address of the load balancer frontend configuration for SAP NetWeaver ASCS ERS
   192.168.14.10 anftstsapers
   192.168.14.7 anftstsapa01
   192.168.14.8 anftstsapa02
   ```

1. **[A]** 建立 Sapmnt 目錄建立 sapmnt 目錄。
   ```
   sudo mkdir -p /sapmnt/QAS
   sudo mkdir -p /usr/sap/trans

   sudo chattr +i /sapmnt/QAS
   sudo chattr +i /usr/sap/trans
   ```

1. **[A]** 安裝 NFS 用戶端和其他需求  

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

   如果使用 NFSv 4.1：
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

1. **[P]** 建立並掛接 PAS 目錄  
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

   如果使用 NFSv 4.1：
   ```
   sudo mkdir -p /usr/sap/QAS/D02
   sudo chattr +i /usr/sap/QAS/D02
   
   sudo vi /etc/fstab
   # Add the following line to fstab
   92.168.24.5:/sapQAS/usrsapQASpas /usr/sap/QAS/D02 nfs rw,hard,rsize=65536,wsize=65536,vers=4.1,sec=sys
   
   # Mount
   sudo mount -a
   ```

1. **[S]** 建立並掛接 .aas 目錄  
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

   如果使用 NFSv 4.1：
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

在此範例中，SAP NetWeaver 安裝在 SAP Hana 上。 您可以針對此安裝使用每個支援的資料庫。 如需有關如何在 Azure 中安裝 SAP Hana 的詳細資訊，請參閱[Red Hat Enterprise Linux 上 Azure vm 上 SAP Hana 的高可用性][sap-hana-ha] . For a list of supported databases, see [SAP Note 1928533][1928533] 。

1. 執行 SAP 資料庫執行個體安裝

   以 root 身分使用虛擬主機名稱 (對應至資料庫負載平衡器前端設定的 IP 位址) 來安裝 SAP NetWeaver 資料庫執行個體。

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

   執行下列命令，以將專案列出為 \<sapsid> adm

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

   輸出會顯示預設項目的 IP 位址指向虛擬機器，而不是指向負載平衡器的 IP 位址。 這個項目必須變更才能指向負載平衡器的虛擬機器主機名稱。 請務必使用相同的連接埠 (上述輸出中的 **30313**) 和資料庫名稱 (上述輸出中的 **QAS**)！

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

   若只終止訊息伺服器一次，則其將會由 `sapstart` 重新啟動。 如果您終止伺服器的次數足夠，則 Pacemaker 最終會將 ASCS 執行個體移到另一個節點。 以 root 身份執行下列命令，以在測試之後清除 ASCS 和 ERS 執行個體的資源狀態。

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

   若只執行命令一次，則 `sapstart` 將會重新啟動處理序。 若執行該項目的次數足夠，則 `sapstart` 將不會重新啟動處理序，且資源將會處於已停止的狀態。 以 root 身份執行下列命令，以在測試之後清除 ERS 執行個體的資源狀態。

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

* [適用於具備多個 SID SAP 應用程式 RHEL 上 Azure VM 中 SAP NW HA 的指南](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-multi-sid)
* [適用於 SAP 的 Azure 虛擬機器規劃和實作][planning-guide]
* [適用於 SAP 的 Azure 虛擬機器部署][deployment-guide]
* [適用於 SAP 的 Azure 虛擬機器 DBMS 部署][dbms-guide]
* 若要瞭解如何建立高可用性，並規劃 Azure （大型實例）上 SAP Hana 的嚴重損壞修復，請參閱[azure 上的 SAP Hana （大型實例）高可用性和嚴重損壞修復](hana-overview-high-availability-disaster-recovery.md)。
* 若要了解如何建立高可用性，並為 Azure VM 上的 SAP HANA 規劃災害復原，請參閱 [Azure 虛擬機器 (VM) 上 SAP HANA 的高可用性][sap-hana-ha]
