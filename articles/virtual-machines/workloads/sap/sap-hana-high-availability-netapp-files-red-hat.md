---
title: 在 RHEL 上使用 ANF 擴大 SAP Hana 的高可用性 |Microsoft Docs
description: " (Vm) 的 Azure 虛擬機器上的 ANF，建立 SAP Hana 的高可用性。"
services: virtual-machines-linux
documentationcenter: ''
author: rdeltcheva
manager: juergent
editor: ''
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/16/2020
ms.author: radeltch
ms.openlocfilehash: cfa68bde2462cefd6f690247cfd1e3bd2e3dbc74
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/02/2020
ms.locfileid: "96489202"
---
# <a name="high-availability-of-sap-hana-scale-up-with-azure-netapp-files-on-red-hat-enterprise-linux"></a>SAP Hana Red Hat Enterprise Linux 上的 Azure NetApp Files 向上擴充的高可用性

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md

[anf-azure-doc]:https://docs.microsoft.com/azure/azure-netapp-files/
[anf-avail-matrix]:https://azure.microsoft.com/global-infrastructure/services/?products=netapp&regions=all 
[anf-register]:https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-register
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
[1900823]:https://launchpad.support.sap.com/#/notes/1900823
[2292690]:https://launchpad.support.sap.com/#/notes/2292690
[2455582]:https://launchpad.support.sap.com/#/notes/2455582
[2593824]:https://launchpad.support.sap.com/#/notes/2593824
[2009879]:https://launchpad.support.sap.com/#/notes/2009879

[sap-swcenter]:https://support.sap.com/en/my-support/software-downloads.html

[sap-hana-ha]:sap-hana-high-availability.md
[nfs-ha]:high-availability-guide-suse-nfs.md

本文說明如何使用 Azure NetApp Files (ANF) ，以在相應增加部署中設定 SAP Hana 系統複寫。 在範例設定和安裝命令中，會使用實例號碼 **03** 和 HANA 系統識別碼 **HN1** 。 SAP HANA 複寫包含一個主要節點以及至少一個次要節點。

當這份檔中的步驟標示了下列前置詞時，其意義如下：

- **[A]**：此步驟適用于所有節點
- **[1]**：步驟僅適用于節點1
- **[2]**：步驟僅適用于節點2
 
請先閱讀下列 SAP Note 和文件：

- SAP Note [1928533](https://launchpad.support.sap.com/#/notes/1928533)，其中包含：
    - SAP 軟體部署支援的 Azure VM 大小清單。
    - Azure VM 大小的重要容量資訊。
    - 支援的 SAP 軟體，以及作業系統 (OS) 與資料庫組合。
    - Microsoft Azure 上 Windows 和 Linux 所需的 SAP 核心版本。
- SAP Note [2015553](https://launchpad.support.sap.com/#/notes/2015553) 列出 Azure 中 SAP 支援的 SAP 軟體部署先決條件。
- SAP Note [405827](https://launchpad.support.sap.com/#/notes/405827) 列出適用于 HANA 環境的建議檔案系統。
- SAP Note [2002167](https://launchpad.support.sap.com/#/notes/2002167) 有建議的 Red Hat Enterprise Linux 作業系統設定。
- SAP Note [2009879](https://launchpad.support.sap.com/#/notes/2009879) 具有 Red Hat Enterprise Linux 的 SAP Hana 指導方針。
- SAP Note [2178632](https://launchpad.support.sap.com/#/notes/2178632) 包含在 Azure 中針對 SAP 回報的所有監視計量詳細資訊。
- SAP Note [2191498](https://launchpad.support.sap.com/#/notes/2191498) 包含 Azure 中 Linux 所需的 SAP Host Agent 版本。
- SAP Note [2243692](https://launchpad.support.sap.com/#/notes/2243692) 包含 Azure 中 Linux 上的 SAP 授權相關資訊。
- SAP Note [1999351](https://launchpad.support.sap.com/#/notes/1999351) 包含 Azure Enhanced Monitoring Extension for SAP 的其他疑難排解資訊。
- [Sap 社區 Wiki](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) 具有適用于 Linux 的所有必要 sap 附注。
- [適用於 SAP on Linux 的 Azure 虛擬機器規劃和實作][planning-guide]
- [在 Linux 上為 SAP 進行 Azure 虛擬機器部署][deployment-guide]
- [適用於 SAP on Linux 的 Azure 虛擬機器 DBMS 部署][dbms-guide]
- [Pacemaker 叢集中的 SAP Hana 系統複寫。](https://access.redhat.com/articles/3004101)
- 一般 RHEL 文件
    - [高可用性附加元件概觀](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_overview/index)
    - [高可用性 Add-On 管理。](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_administration/index)
    - [高可用性 Add-On 參考。](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_reference/index)
    - [當 HANA 檔案系統位於 NFS 共用時，在 Pacemaker 叢集中的 Scale-Up 設定 SAP Hana 系統複寫](https://access.redhat.com/solutions/5156571)
- Azure 專用 RHEL 文件：
    - [RHEL 高可用性叢集的支援原則-Microsoft Azure 虛擬機器為叢集成員。](https://access.redhat.com/articles/3131341)
    - [安裝和設定 Red Hat Enterprise Linux 7.4 (和更新版本) Microsoft Azure 上的 High-Availability 叢集。](https://access.redhat.com/articles/3252491)
    - [在 Red Hat Enterprise Linux 上安裝 SAP Hana 以用於 Microsoft Azure。](https://access.redhat.com/solutions/3193782)
    - [設定當 HANA 檔案系統位於 NFS 共用時，SAP Hana 向上擴充系統複寫 Pacemaker 叢集](https://access.redhat.com/solutions/5156571)
- [使用 Azure NetApp Files 在 Microsoft Azure 上的 NetApp SAP 應用程式](https://www.netapp.com/us/media/tr-4746.pdf)

## <a name="overview"></a>概觀

傳統上，在擴大環境中，SAP Hana 的所有檔案系統都會從本機儲存體掛接。 在 Red Hat Enterprise Linux 上設定 SAP Hana 系統複寫的高可用性時，已發佈于在[RHEL 上設定 SAP Hana 系統](./sap-hana-high-availability-rhel.md)複寫的指南

為了在 [Azure NetApp Files](../../../azure-netapp-files/index.yml) NFS 共用上達成高可用性系統的 SAP Hana 高可用性，我們需要叢集中的一些額外資源設定，以在一個節點失去 ANF 上 NFS 共用的存取權時，將 HANA 資源復原。  叢集會管理 NFS 裝載，讓它能夠監視資源的健康情況。 系統會強制執行檔案系統裝載和 SAP Hana 資源之間的相依性。  

![ANF 上的 HA 擴大 SAP Hana](./media/sap-hana-high-availability-rhel/sap-hana-scale-up-netapp-files-red-hat.png)

SAP Hana 的檔案系統會使用每個節點上的 Azure NetApp Files 掛接在 NFS 共用上。 檔案系統/hana/data、/hana/log 和/hana/shared 對每個節點都是唯一的。 

裝載在節點 1 (**hanadb1**) 

- 10.32.2.4：/**hanadb1**-data-mnt00001 on/hana/data
- 10.32.2.4：/**hanadb1**-log-mnt00001 on/hana/log
- /hana/shared 上的10.32.2.4：/**hanadb1**-shared-mnt00001

裝載在節點2上 (**hanadb2**) 

- 10.32.2.4：/**hanadb2**-data-mnt00001 on/hana/data
- 10.32.2.4：/**hanadb2**-log-mnt00001 on/hana/log
- /hana/shared 上的10.32.2.4：/**hanadb2**-shared-mnt00001

> [!NOTE]
> 檔案系統/hana/shared、/hana/data 和/hana/log 不會在兩個節點之間共用。 每個叢集節點都有各自獨立的檔案系統。   

SAP Hana 系統複寫設定會使用專用的虛擬主機名稱和虛擬 IP 位址。 在 Azure 上必須有負載平衡器才能使用虛擬 IP 位址。 下列清單顯示負載平衡器的組態：

- 前端設定：適用于 hn1 的 IP 位址 10.32.0.10-db
- 後端組態：連線到應屬於 HANA 系統複寫一部分的所有虛擬機器的主要網路介面
- 探查連接埠：連接埠 62503
- 負載平衡規則： 30313 TCP、30315 TCP、30317 TCP、30340 TCP、30341 TCP、30342 TCP (（如果使用基本的 Azure 負載平衡器）)   

## <a name="set-up-the-azure-netapp-file-infrastructure"></a>設定 Azure NetApp File 基礎結構

繼續進行 Azure NetApp Files 基礎結構的設定之前，請先熟悉 Azure [Netapp Files 檔](../../../azure-netapp-files/index.yml)。

Azure NetApp Files 可在數個 [azure 區域](https://azure.microsoft.com/global-infrastructure/services/?products=netapp)中使用。 查看您選取的 Azure 區域是否提供 Azure NetApp Files。

如需 azure 區域之 Azure NetApp Files 可用性的詳細資訊，請參閱 azure [區域的 Azure Netapp Files 可用性](https://azure.microsoft.com/global-infrastructure/services/?products=netapp&regions=all)。

在您部署 Azure NetApp Files 之前，請先向 Azure netapp files 註冊以 [註冊](../../../azure-netapp-files/azure-netapp-files-register.md)azure netapp files 指示，以要求登入 Azure netapp files。

### <a name="deploy-azure-netapp-files-resources"></a>部署 Azure NetApp Files 資源

下列指示假設您已部署 [Azure 虛擬網路](../../../virtual-network/virtual-networks-overview.md)。 將裝載 Azure NetApp Files 資源的 Azure NetApp Files 資源和 Vm，必須部署在相同的 Azure 虛擬網路或對等互連 Azure 虛擬網路中。

1. 如果您尚未部署資源，請要求在 [Azure NetApp Files 上架](../../../azure-netapp-files/azure-netapp-files-register.md)。

2. 遵循 [建立 netapp 帳戶](../../../azure-netapp-files/azure-netapp-files-create-netapp-account.md)中的指示，在您選取的 Azure 區域中建立 NetApp 帳戶。

3.  遵循 [設定 Azure Netapp files 容量](../../../azure-netapp-files/azure-netapp-files-set-up-capacity-pool.md)集區中的指示，設定 Azure netapp files 容量集區。

    本文所提供的 HANA 架構在 *Ultra* 服務層級使用單一 Azure NetApp Files 容量集區。 針對 Azure 上的 HANA 工作負載，我們建議使用 Azure NetApp Files *Ultra* 或 *Premium* [服務層級](../../../azure-netapp-files/azure-netapp-files-service-levels.md)。

4.  將子網委派給 Azure NetApp Files，如將 [子網委派至 Azure Netapp files](../../../azure-netapp-files/azure-netapp-files-delegate-subnet.md)中的指示所述。

5.  遵循 [建立適用于 Azure Netapp files 的 NFS 磁片](../../../azure-netapp-files/azure-netapp-files-create-volumes.md)區中的指示，部署 Azure netapp files 磁片區。

    當您部署磁片區時，請務必選取 Nfsv4.1 4.1 版。 在指定的 Azure NetApp Files 子網路中部署磁碟區。 Azure NetApp 磁碟區的 IP 位址會自動指派。

    請記住，Azure NetApp Files 資源和 Azure Vm 必須位於相同的 Azure 虛擬網路或對等互連 Azure 虛擬網路中。 例如，hanadb1-mnt00001、hanadb1-log-mnt00001 等是磁片區名稱和 nfs://10.32.2.4/hanadb1-data-mnt00001、nfs://10.32.2.4/hanadb1-log-mnt00001 等等，都是 Azure NetApp Files 磁片區的檔案路徑。
    
    在 **hanadb1** 上

    - 磁片區 hanadb1-資料-mnt00001 (nfs://10.32.2.4:/hanadb1-data-mnt00001)  
    - 磁片區 hanadb1-log-mnt00001 (nfs://10.32.2.4:/hanadb1-log-mnt00001) 
    - Volume hanadb1-shared-mnt00001 (nfs://10.32.2.4:/hanadb1-shared-mnt00001) 
    
    在 **hanadb2** 上

    - 磁片區 hanadb2-資料-mnt00001 (nfs://10.32.2.4:/hanadb2-data-mnt00001) 
    - 磁片區 hanadb2-log-mnt00001 (nfs://10.32.2.4:/hanadb2-log-mnt00001) 
    - Volume hanadb2-shared-mnt00001 (nfs://10.32.2.4:/hanadb2-shared-mnt00001) 

### <a name="important-considerations"></a>重要考量

當您建立適用于 SAP Hana 相應增加系統的 Azure NetApp Files 時，請注意下列考慮：

- 容量集區的最小值是 4 tib (TiB) 。
- 磁片區大小下限為 100 32,767 gib (GiB) 。
- Azure NetApp Files 和將掛接 Azure NetApp Files 磁片區的所有虛擬機器，都必須位於相同的 Azure 虛擬網路或相同區域中的 [對等互連虛擬網路](../../../virtual-network/virtual-network-peering-overview.md) 中。
- 選取的虛擬網路必須有委派給 Azure NetApp Files 的子網。
- Azure NetApp Files 磁片區的輸送量是磁片區配額和服務層級的功能，如 [Azure NetApp Files 的服務層級](../../../azure-netapp-files/azure-netapp-files-service-levels.md)中所述。 當您調整 HANA Azure NetApp 磁片區的大小時，請確定產生的輸送量符合 HANA 系統需求。
- 使用 Azure NetApp Files [匯出原則](../../../azure-netapp-files/azure-netapp-files-configure-export-policy.md)，您可以控制允許的用戶端、存取類型 (讀寫、唯讀等) 。
- Azure NetApp Files 功能尚未感知區域。 目前，此功能不會部署在 Azure 區域中的所有可用性區域。 請留意某些 Azure 區域中可能出現的延遲情形。

> [!IMPORTANT]
> 針對 SAP Hana 工作負載，低延遲很重要。 請與您的 Microsoft 代表合作，以確保虛擬機器和 Azure NetApp Files 磁片區會以鄰近的方式部署。

### <a name="sizing-of-hana-database-on-azure-netapp-files"></a>Azure NetApp Files 上 HANA 資料庫的大小調整

Azure NetApp Files 磁片區的輸送量是磁片區大小和服務層級的功能，如 [Azure NetApp Files 的服務層級](../../../azure-netapp-files/azure-netapp-files-service-levels.md)中所述。

當您在 Azure 中設計 SAP 的基礎結構時，請留意 SAP 的一些最低儲存體需求，這些需求會轉譯為最小輸送量特性：

- /Hana/log 的讀寫（每秒 250 mb） (MB/s) 具有 1 MB 的 i/o 大小。
- 至少 400 MB/s 的讀取活動，以/hana/data 16 MB 和 64 MB 的 i/o 大小。
- 至少 250 MB/s 的寫入活動，適用于/hana/data 16 MB 和 64 MB 的 i/o 大小。

每 1 TiB 磁碟區配額的 [Azure NetApp Files 輸送量限制](../../../azure-netapp-files/azure-netapp-files-service-levels.md)如下：

- 進階儲存體層-64 MiB/s。
- Ultra 儲存層-128 MiB/s。

為了滿足/hana/data 和/hana/log 的 SAP 最小輸送量需求，以及/hana/shared 的指導方針，建議的大小為：

|    磁碟區    | 進階儲存體層的大小 | Ultra 儲存層的大小 | 支援的 NFS 通訊協定 |
| :----------: | :--------------------------: | :------------------------: | :--------------------: |
|  /hana/log   |            4 TiB             |           2 TiB            |          v4.1          |
|  /hana/data  |           6.3 TiB            |          3.2 TiB           |          v4.1          |
| HANA/shared |           1 x RAM            |          1 x RAM           |          v3 或 4.1 版    |


> [!NOTE]
> 此處所述的 Azure NetApp Files 大小調整建議的目標是符合 SAP 針對其基礎結構提供者所建議的最低需求。 在實際的客戶部署和工作負載案例中，這些大小可能不足。 請將這些建議當作起點，並且根據特定工作負載的需求來調整。

> [!TIP]
> 您可以動態調整 Azure NetApp Files 磁片區的大小，而不需要 *卸載* 磁片區、停止虛擬機器或停止 SAP Hana。 這種方法可讓您彈性地滿足應用程式預期和非預期的輸送量需求。

> [!NOTE]
> 本文中所有裝載/hana/shared 的命令都是針對 Nfsv4.1 4.1/hana/shared 磁片區所提供。
> 如果您將/hana/shared 磁片區部署為 NFSv3 磁片區，請別忘了調整/hana/shared for NFSv3 的掛接命令。


## <a name="deploy-linux-virtual-machine-via-azure-portal"></a>透過 Azure 入口網站部署 Linux 虛擬機器 

首先您需要建立 Azure NetApp Files 磁碟區。 然後，執行下列步驟：

1.  建立資源群組。
2.  建立虛擬網路。
3.  建立可用性設定組。 設定更新網域上限。
4.  建立負載平衡器 (內部)。 建議標準負載平衡器。
    選取步驟 2 所建立的虛擬網路。
5.  建立虛擬機器 1 (**hanadb1**) 。 
6.  建立虛擬機器 2 (**hanadb2**) 。  
7.  建立虛擬機器時，我們將不會新增任何磁片，因為所有的掛接點都是來自 Azure NetApp Files 的 NFS 共用。 

> [!IMPORTANT]
> 負載平衡案例中的 NIC 次要 IP 設定不支援浮動 IP。 如需詳細資訊，請參閱 [Azure 負載平衡器的限制](../../../load-balancer/load-balancer-multivip-overview.md#limitations)。 如果您需要 VM 的其他 IP 位址，請部署第二個 NIC。    

> [!NOTE] 
> 當不具公用 IP 位址的 VM 放在內部 (沒有公用 IP 位址) Standard Azure Load Balancer 的後端集區時，除非另外設定來允許路由傳送至公用端點，否則不會有輸出網際網路連線能力。 如需如何實現輸出連線能力的詳細資料，請參閱[在 SAP 高可用性案例中使用 Azure Standard Load Balancer 實現虛擬機器的公用端點連線能力](./high-availability-guide-standard-load-balancer-outbound-connections.md)。

8.  如果使用標準負載平衡器，請遵循下列設定步驟：
    1.  首先，建立前端 IP 集區：
        1.  開啟負載平衡器，選取 [前端 IP 集區]，然後選取 [新增]。
        1.  輸入新前端 IP 集區的名稱 (例如 **hana-frontend**)。
        1.  將 [ **指派** ] 設定為 [ **靜態** ]，然後輸入 IP 位址 (例如 **10.32.0.10**) 。
        1.  選取 [確定]。
        1.  建立新的前端 IP 集區之後，請記下集區 IP 位址。
    1.  接下來，建立後端集區：
        1.  開啟負載平衡器，選取 [後端集區]，然後選取 [新增]。
        1.  輸入新後端集區的名稱 (例如 **hana-backend**)。
        1.  選取 [新增虛擬機器]。
        1.  選取 [虛擬機器]。
        1.  選取 SAP Hana 叢集的虛擬機器及其 IP 位址。
        1.  選取 [新增]。
    1.  接下來，建立健康情況探查：
        1.  開啟負載平衡器，選取 [健康情況探查]，然後選取 [新增]。
        1.  輸入新健康情況探查的名稱 (例如 **hana-hp**)。
        1.  選取 [TCP] 通訊協定和連接埠 62503。 讓 [間隔] 值保持設定為 5，而讓 [狀況不良閾值] 值保持設定為 2。
        1.  選取 [確定]。
    1.  接下來，建立負載平衡規則：
        1.  開啟負載平衡器，選取 [負載平衡規則]，然後選取 [新增]。
        1.  輸入新負載平衡器規則的名稱 (例如 **hana-lb**)。
        1.  選取您稍早建立的前端 IP 位址、後端集區及健康情況探查 (例如，**hana-frontend**、**hana-backend** 和 **hana-hp**)。
        1.  選取 [HA 連接埠]。
        1.  將 [閒置逾時] 增加為 30 分鐘。
        1.  務必 **啟用浮動 IP**。
        1.  選取 [確定]。


9. 或者，如果您的情節要求使用基本負載平衡器，請遵循下列設定步驟：
    1.  設定負載平衡器。 首先，建立前端 IP 集區：
        1.  開啟負載平衡器，選取 [前端 IP 集區]，然後選取 [新增]。
        1.  輸入新前端 IP 集區的名稱 (例如 **hana-frontend**)。
        1.  將 [ **指派** ] 設定為 [ **靜態** ]，然後輸入 IP 位址 (例如 **10.32.0.10**) 。
        1.  選取 [確定]。
        1.  建立新的前端 IP 集區之後，請記下集區 IP 位址。
    1.  接下來，建立後端集區：
        1.  開啟負載平衡器，選取 [後端集區]，然後選取 [新增]。
        1.  輸入新後端集區的名稱 (例如 **hana-backend**)。
        1.  選取 [新增虛擬機器]。
        1.  選取步驟 3 所建立的可用性設定組。
        1.  選取 SAP HANA 叢集的虛擬機器。
        1.  選取 [確定]。
    1.  接下來，建立健康情況探查：
        1.  開啟負載平衡器，選取 [健康情況探查]，然後選取 [新增]。
        1.  輸入新健康情況探查的名稱 (例如 **hana-hp**)。
        1.  選取 [TCP] 通訊協定和連接埠 **62503**。 讓 [間隔] 值保持設定為 5，而讓 [狀況不良閾值] 值保持設定為 2。
        1.  選取 [確定]。
    1.  針對 SAP HANA 1.0，建立負載平衡規則：
        1.  開啟負載平衡器，選取 [負載平衡規則]，然後選取 [新增]。
        1.  輸入新負載平衡器規則的名稱 (例如 hana-lb-3 **03** 15)。
        1.  選取您稍早建立的前端 IP 位址、後端集區及健康情況探查 (例如 **hana-frontend**)。
        1.  讓 [通訊協定] 保持設定為 [TCP]，然後輸入連接埠 3 **03** 15。
        1.  將 [閒置逾時] 增加為 30 分鐘。
        1.  務必 **啟用浮動 IP**。
        1.  選取 [確定]。
        1.  對連接埠 3 **03** 17 重複執行這些步驟。
    1.  若為 SAP Hana 2.0，請針對系統資料庫建立負載平衡規則：
        1.  開啟負載平衡器，選取 [負載平衡規則]，然後選取 [新增]。
        1.  輸入新負載平衡器規則的名稱 (例如 hana-lb-3 **03** 13)。
        1.  選取您稍早建立的前端 IP 位址、後端集區及健康情況探查 (例如 **hana-frontend**)。
        1.  讓 [通訊協定] 保持設定為 [TCP]，然後輸入連接埠 3 **03** 13。
        1.  將 [閒置逾時] 增加為 30 分鐘。
        1.  務必 **啟用浮動 IP**。
        1.  選取 [確定]。
        1.  針對連接埠 3 **03** 14 重複這些步驟。
    1.  若為 SAP Hana 2.0，請先針對租用戶資料庫建立負載平衡規則：
        1.  開啟負載平衡器，選取 [負載平衡規則]，然後選取 [新增]。
        1.  輸入新負載平衡器規則的名稱 (例如 hana-lb-3 **03** 40)。
        1.  選取您稍早建立的前端 IP 位址、後端集區及健康情況探查 (例如 **hana-frontend**)。
        1.  讓 [通訊協定] 保持設定為 [TCP]，然後輸入連接埠 3 **03** 40。
        1.  將 [閒置逾時] 增加為 30 分鐘。
        1.  務必 **啟用浮動 IP**。
        1.  選取 [確定]。
        1.  針對連接埠 3 **03** 41 和 3 **03** 42 重複這些步驟。

如需 SAP Hana 所需埠的詳細資訊，請參閱[SAP Hana 租使用者資料庫](https://help.sap.com/viewer/78209c1d3a9b41cd8624338e42a12bf6)指南或 SAP 附注[2388694](https://launchpad.support.sap.com/#/notes/2388694)中的[租使用者資料庫](https://help.sap.com/viewer/78209c1d3a9b41cd8624338e42a12bf6/latest/en-US/7a9343c9f2a2436faa3cfdb5ca00c052.html)連線一章。

> [!IMPORTANT]
> 請勿在位於 Azure Load Balancer 後方的 Azure VM 上啟用 TCP 時間戳記。 啟用 TCP 時間戳記會導致健康狀態探查失敗。 將參數 **net.ipv4.tcp_timestamps** 設定為 **0**。 如需詳細資料，請參閱[負載平衡器健康情況探查](../../../load-balancer/load-balancer-custom-probe-overview.md)。 另請參閱 SAP Note [2382421](https://launchpad.support.sap.com/#/notes/2382421)。

## <a name="mount-the-azure-netapp-files-volume"></a>掛接 Azure NetApp Files 磁片區

1. **[A]** 建立 HANA 資料庫磁片區的掛接點。 

    ```
    mkdir -p /hana/data
    mkdir -p /hana/log
    mkdir -p /hana/shared
    ```

2. **[A]** 確認 NFS 網域設定。 請確定網域已設定為預設的 Azure NetApp Files 網域，也就是 **defaultv4iddomain.com** ，而且對應設定為無 **人**。

    ```
    sudo cat /etc/idmapd.conf
    # Example
    [General]
    Domain = defaultv4iddomain.com
    [Mapping]
    Nobody-User = nobody
    Nobody-Group = nobody
    ```

    > [!IMPORTANT]    
    > 請務必將 VM 上/etc/idmapd.conf 中的 NFS 網域設定為符合 Azure NetApp Files 上的預設網域設定： **defaultv4iddomain.com**。 如果 NFS 用戶端上的網域設定不符 (亦即 VM) 和 NFS 伺服器（亦即 Azure NetApp configuration），則裝載在 Vm 上的 Azure NetApp 磁片區上的檔案許可權將會顯示為未使用。
    

3. **[1]** 在節點 1 (**hanadb1** 上掛接節點特定磁片區)  

    ```
    sudo mount -o rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys 10.32.2.4:/hanadb1-shared-mnt00001 /hana/shared
    sudo mount -o rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys 10.32.2.4:/hanadb1-log-mnt00001 /hana/log
    sudo mount -o rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys 10.32.2.4:/hanadb1-data-mnt00001 /hana/data
    ```
    
4.  **[2]** 在節點節點上掛接節點特定磁片區 (**hanadb2**) 
    
    ```
    sudo mount -o rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys 10.32.2.4:/hanadb2-shared-mnt00001 /hana/shared
    sudo mount -o rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys 10.32.2.4:/hanadb2-log-mnt00001 /hana/log
    sudo mount -o rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys 10.32.2.4:/hanadb2-data-mnt00001 /hana/data
    ```

5. **[A]** 確認所有 HANA 磁片區都是使用 NFS 通訊協定版本 NFSv4 掛接。

    ```
    sudo nfsstat -m
    
    # Verify that flag vers is set to 4.1 
    # Example from hanadb1
    
    /hana/log from 10.32.2.4:/hanadb1-log-mnt00001
    Flags: rw,noatime,vers=4.1,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.32.0.4,local_lock=none,addr=10.32.2.4
    /hana/data from 10.32.2.4:/hanadb1-data-mnt00001
    Flags: rw,noatime,vers=4.1,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.32.0.4,local_lock=none,addr=10.32.2.4
    /hana/shared from 10.32.2.4:/hanadb1-shared-mnt00001
    Flags: rw,noatime,vers=4.1,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.32.0.4,local_lock=none,addr=10.32.2.4
    ```

6. **[A]** 驗證 **nfs4_disable_idmapping**。 它應該設定為 **Y**。若要建立 **nfs4_disable_idmapping** 所在的目錄結構，請執行掛接命令。 您將無法手動在 /sys/modules 下建立目錄，因為其存取已保留給核心/驅動程式。

    ```
    # Check nfs4_disable_idmapping 
    cat /sys/module/nfs/parameters/nfs4_disable_idmapping
    
    # If you need to set nfs4_disable_idmapping to Y
    echo "Y" > /sys/module/nfs/parameters/nfs4_disable_idmapping
    
    # Make the configuration permanent
    echo "options nfs nfs4_disable_idmapping=Y" >> /etc/modprobe.d/nfs.conf
    ```

   如需如何變更 **nfs_disable_idmapping** 參數的詳細資訊，請參閱 [https://access.redhat.com/solutions/1749883](https://access.redhat.com/solutions/1749883) 。 


## <a name="sap-hana-installation"></a>SAP HANA 安裝

1. **[A]** 設定所有主機的主機名稱解析。

   您可以使用 DNS 伺服器，或修改所有節點上的 /etc/hosts 檔案。 這個範例示範如何使用 /etc/hosts 檔案。 請取代下列命令中的 IP 位址和主機名稱：

   ```
   sudo vi /etc/hosts
   # Insert the following lines in the /etc/hosts file. Change the IP address and hostname to match your environment  
   10.32.0.4   hanadb1
   10.32.0.5   hanadb2
   ```

2. **[A]** RHEL for HANA 設定

   根據您的 RHEL 版本，依照下列 SAP Note 所述設定 RHEL

   - [2292690 - SAP HANA DB：適用於 RHEL 7 的建議作業系統設定](https://launchpad.support.sap.com/#/notes/2292690)
   - [2777782-SAP Hana DB：適用于 RHEL 8 的建議作業系統設定](https://launchpad.support.sap.com/#/notes/2777782)
   - [2455582-Linux：執行以 GCC 6.x 編譯的 SAP 應用程式](https://launchpad.support.sap.com/#/notes/2455582)
   - [2593824-Linux：執行以 GCC 7.x 編譯的 SAP 應用程式](https://launchpad.support.sap.com/#/notes/2593824) 
   - [2886607-Linux：執行以 GCC 6.x 編譯的 SAP 應用程式](https://launchpad.support.sap.com/#/notes/2886607)

3. **[A]** 安裝 SAP HANA

   從 HANA 2.0 SPS 01 開始，[MDC] 是預設選項。 當您安裝 HANA 系統時，將會同時建立 SYSTEMDB 和具有相同 SID 的租使用者。 在某些情況下，您不需要預設租使用者。 在此情況下，如果您不想要建立初始租使用者以及安裝，可以遵循 SAP 附注 [2629711](https://launchpad.support.sap.com/#/notes/2629711)

    從 HANA DVD 執行 **hdblcm** 程式。 在提示上輸入下列值：  
    選擇安裝：輸入 **1** (安裝)   
    選取要安裝的其他元件：輸入 **1**。  
    輸入安裝路徑 [/hana/shared]：按 Enter 以接受預設值  
    輸入本機主機名稱 [...]：按 Enter 接受預設值  
    您是否要將其他主機新增至系統？  (y/n) [n]： **n**  
    輸入 SAP Hana 系統識別碼：輸入 **HN1**。  
    輸入實例號碼 [00]：輸入 **03**  
    選取資料庫模式/輸入索引 [1]：按下 Enter 以接受預設值  
    選取系統使用量/輸入索引 [4]：輸入 **4** (自訂)   
    輸入資料磁片區的位置 [/hana/data]：按下 Enter 以接受預設值  
    輸入記錄磁片區的位置 [/hana/log]：按下 Enter 以接受預設值  
    是否限制記憶體配置上限？ [n]：按 Enter 接受預設值  
    輸入主機的憑證主機名稱 ' ... '[...]：按 Enter 接受預設值  
    輸入 SAP 主機代理程式使用者 (sapadm) 密碼：輸入主機代理程式使用者密碼  
    確認 SAP 主機代理程式使用者 (sapadm) 密碼：再次輸入主機代理程式使用者密碼以確認  
    輸入系統管理員 (hn1adm) 密碼：輸入系統管理員密碼  
    確認系統管理員 (hn1adm) 密碼：再次輸入系統管理員密碼以確認  
    輸入系統管理員主目錄 [/usr/sap/HN1/home]：按下 Enter 以接受預設值  
    輸入系統管理員登入 Shell [/bin/sh]：按下 Enter 以接受預設值  
    輸入系統管理員使用者識別碼 [1001]：按下 Enter 以接受預設值  
    輸入使用者群組 (sapsys) [79] 的識別碼：按下 Enter 接受預設值  
    輸入資料庫使用者 (系統) 密碼：輸入資料庫使用者密碼  
    確認資料庫使用者 (系統) 密碼：再次輸入資料庫使用者密碼以確認  
    是否在電腦重新開機後重新啟動系統？ [n]：按 Enter 接受預設值  
    是否要繼續？ (y/n)：驗證摘要。 輸入 **y** 以繼續  

4. **[A]** 升級 SAP 主機代理程式

   從 [SAP 軟體中心](https://launchpad.support.sap.com/#/softwarecenter)下載最新的 SAP 主機代理程式封存檔，然後執行下列命令來升級代理程式。 取代封存檔的路徑以指向您所下載的檔案：

   ```
   sudo /usr/sap/hostctrl/exe/saphostexec -upgrade -archive <path to SAP Host Agent SAR>
   ```

5. **[A]** 設定防火牆

   建立 Azure 負載平衡器探查連接埠的防火牆規則。

   ```
   sudo firewall-cmd --zone=public --add-port=62503/tcp
   sudo firewall-cmd --zone=public --add-port=62503/tcp –permanent
   ```

## <a name="configure-sap-hana-system-replication"></a>設定 SAP Hana 系統複寫

依照設定 [SAP Hana 系統](./sap-hana-high-availability-rhel.md#configure-sap-hana-20-system-replication) 複寫中的步驟來設定 SAP Hana 系統複寫。 

## <a name="cluster-configuration"></a>叢集組態

本節說明使用 Azure NetApp Files 在 NFS 共用上安裝 SAP Hana 時，叢集順利運作所需的必要步驟。 

### <a name="create-a-pacemaker-cluster"></a>建立 Pacemaker 叢集

遵循在 Azure [Red Hat Enterprise Linux 上設定 Pacemaker](./high-availability-guide-rhel-pacemaker.md) 中的步驟，建立此 HANA 伺服器的基本 Pacemaker 叢集。

### <a name="configure-filesystem-resources"></a>設定 filesystem 資源

在此範例中，每個叢集節點都有自己的 HANA NFS 檔案系統/hana/shared、/hana/data 和/hana/log。   

1. **[1]** 讓叢集進入維護模式。

   ```
   pcs property set maintenance-mode=true
   ```

2. **[1]** 建立 **Hanadb1** 裝載的檔案系統資源。

    ```
    pcs resource create hana_data1 ocf:heartbeat:Filesystem device=10.32.2.4:/hanadb1-data-mnt00001 directory=/hana/data fstype=nfs options=rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys op monitor interval=20s on-fail=fence timeout=40s OCF_CHECK_LEVEL=20 --group hanadb1_nfs
    pcs resource create hana_log1 ocf:heartbeat:Filesystem device=10.32.2.4:/hanadb1-log-mnt00001 directory=/hana/log fstype=nfs options=rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys op monitor interval=20s on-fail=fence timeout=40s OCF_CHECK_LEVEL=20 --group hanadb1_nfs
    pcs resource create hana_shared1 ocf:heartbeat:Filesystem device=10.32.2.4:/hanadb1-shared-mnt00001 directory=/hana/shared fstype=nfs options=rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys op monitor interval=20s on-fail=fence timeout=40s OCF_CHECK_LEVEL=20 --group hanadb1_nfs
    ```

3. **[2]** 建立 **Hanadb2** 裝載的檔案系統資源。

    ```
    pcs resource create hana_data2 ocf:heartbeat:Filesystem device=10.32.2.4:/hanadb2-data-mnt00001 directory=/hana/data fstype=nfs options=rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys op monitor interval=20s on-fail=fence timeout=40s OCF_CHECK_LEVEL=20 --group hanadb2_nfs
    pcs resource create hana_log2 ocf:heartbeat:Filesystem device=10.32.2.4:/hanadb2-log-mnt00001 directory=/hana/log fstype=nfs options=rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys op monitor interval=20s on-fail=fence timeout=40s OCF_CHECK_LEVEL=20 --group hanadb2_nfs
    pcs resource create hana_shared2 ocf:heartbeat:Filesystem device=10.32.2.4:/hanadb2-shared-mnt00001 directory=/hana/shared fstype=nfs options=rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys op monitor interval=20s on-fail=fence timeout=40s OCF_CHECK_LEVEL=20 --group hanadb2_nfs
    ```

    `OCF_CHECK_LEVEL=20` 屬性會新增至監視作業，讓每個監視器在檔案系統上執行讀取/寫入測試。 如果沒有這個屬性，監視器作業只會驗證檔案系統是否已裝載。 這可能會造成問題，因為當連線中斷時，檔案系統可能會因為無法存取而保持裝載。

    `on-fail=fence` 屬性也會新增至監視作業。 使用這個選項時，如果節點上的監視作業失敗，則會立即隔離該節點。 如果沒有此選項，預設行為是停止相依于失敗資源的所有資源，然後重新開機失敗的資源，然後啟動相依于失敗資源的所有資源。 當 SAPHana 資源相依于失敗的資源時，這種行為不只會花很長的時間，但它也可以全部失敗。 如果無法存取保存 HANA 可執行檔的 NFS 伺服器，SAPHana 資源就無法順利停止。

4. **[1] 設定** 位置條件約束

   設定位置條件約束，以確保管理 hanadb1 唯一裝載的資源絕不能在 hanadb2 上執行，反之亦然。

    ```
    pcs constraint location hanadb1_nfs rule score=-INFINITY resource-discovery=never \#uname eq hanadb2
    pcs constraint location hanadb2_nfs rule score=-INFINITY resource-discovery=never \#uname eq hanadb1
    ```

    `resource-discovery=never`因為每個節點的唯一裝載都共用相同掛接點，所以會設定此選項。 例如， `hana_data1` 使用掛接點 `/hana/data` ，而且 `hana_data2` 也使用掛接點 `/hana/data` 。 這可能會導致探查作業發生錯誤，在叢集啟動時檢查資源狀態，而這可能會導致不必要的復原行為。 這可以藉由設定來避免 `resource-discovery=never`

5. **[1] 設定** 屬性資源

   設定屬性資源。 如果所有節點的 NFS 掛接 (/hana/data、/hana/log 和/hana/data) 掛接，這些屬性將設定為 true，否則會設為 false。

   ```
   pcs resource create hana_nfs1_active ocf:pacemaker:attribute active_value=true inactive_value=false name=hana_nfs1_active
   pcs resource create hana_nfs2_active ocf:pacemaker:attribute active_value=true inactive_value=false name=hana_nfs2_active
   ```

6. **[1] 設定** 位置條件約束

   設定位置條件約束，以確保 hanadb1's 屬性資源永遠不會在 hanadb2 上執行，反之亦然。

   ```
   pcs constraint location hana_nfs1_active avoids hanadb2
   pcs constraint location hana_nfs2_active avoids hanadb1
   ```

7. **[1]** 建立排序條件約束

   設定順序條件約束，讓節點的屬性資源只在所有節點的 NFS 裝載都已掛接之後才開始。
   
    ```
    pcs constraint order hanadb1_nfs then hana_nfs1_active
    pcs constraint order hanadb2_nfs then hana_nfs2_active
    ```

   > [!TIP]
   > 如果您的設定包含群組或以外的檔案系統， `hanadb1_nfs` `hanadb2_nfs` 則請包含 `sequential=false` 選項，如此一來，檔案系統之間就不會有順序相依性。 所有檔案系統都必須先啟動 `hana_nfs1_active` ，但不需要以相對於彼此的任何順序啟動。 如需詳細資訊，請參閱[如何? 在 HANA 檔案系統位於 NFS 共用時，于 Pacemaker 叢集中的 Scale-Up 設定 SAP Hana 系統](https://access.redhat.com/solutions/5156571)複寫

### <a name="configure-sap-hana-cluster-resources"></a>設定 SAP Hana 叢集資源

1. 遵循 [建立 SAP Hana 叢集資源](./sap-hana-high-availability-rhel.md#create-sap-hana-cluster-resources) 中的步驟，在叢集中建立 SAP Hana 資源。 建立 SAP Hana 資源之後，我們必須在 SAP Hana 資源和 (NFS 裝載的檔案系統之間建立位置規則條件約束) 

2. **[1]** 設定 SAP Hana 資源與 NFS 裝載之間的條件約束

   將設定位置規則條件約束，只有在裝載所有節點的 NFS 掛接時，才可以在節點上執行 SAP Hana 資源。

    ```
    pcs constraint location SAPHanaTopology_HN1_03-clone rule score=-INFINITY hana_nfs1_active ne true and hana_nfs2_active ne true
    # On RHEL 7.x
    pcs constraint location SAPHana_HN1_03-master rule score=-INFINITY hana_nfs1_active ne true and hana_nfs2_active ne true
    # On RHEL 8.x
    pcs constraint location SAPHana_HN1_03-clone rule score=-INFINITY hana_nfs1_active ne true and hana_nfs2_active ne true
    # Take the cluster out of maintenance mode
    sudo pcs property set maintenance-mode=false
    ```

   檢查叢集和所有資源的狀態
   > [!NOTE]
   > 本文包含詞彙 *從屬* 的參考，這是 Microsoft 不再使用的詞彙。 從軟體移除字詞時，我們會將它從本文中移除。
   
    ```
    sudo pcs status
    
    Online: [ hanadb1 hanadb2 ]
    
    Full list of resources:
    
    rsc_hdb_azr_agt(stonith:fence_azure_arm):  Started hanadb1
    
    Resource Group: hanadb1_nfs
    hana_data1 (ocf::heartbeat:Filesystem):Started hanadb1
    hana_log1  (ocf::heartbeat:Filesystem):Started hanadb1
    hana_shared1   (ocf::heartbeat:Filesystem):Started hanadb1
    
    Resource Group: hanadb2_nfs
    hana_data2 (ocf::heartbeat:Filesystem):Started hanadb2
    hana_log2  (ocf::heartbeat:Filesystem):Started hanadb2
    hana_shared2   (ocf::heartbeat:Filesystem):Started hanadb2
    
    hana_nfs1_active   (ocf::pacemaker:attribute): Started hanadb1
    hana_nfs2_active   (ocf::pacemaker:attribute): Started hanadb2
    
    Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
    Started: [ hanadb1 hanadb2 ]
    
    Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
    Masters: [ hanadb1 ]
    Slaves: [ hanadb2 ]
    
    Resource Group: g_ip_HN1_03
    nc_HN1_03  (ocf::heartbeat:azure-lb):  Started hanadb1
    vip_HN1_03 (ocf::heartbeat:IPaddr2):   Started hanadb1
    ```

## <a name="test-the-cluster-setup"></a>測試叢集設定

本節將說明如何測試您的設定。 

1. 開始測試之前，請確定 Pacemaker 沒有任何失敗的動作 (透過電腦狀態) ，沒有未預期的位置限制 (例如，leftovers 的遷移測試) ，且 HANA 系統複寫是同步狀態，例如 systemReplicationStatus：

    ```
    sudo su - hn1adm -c "python /usr/sap/HN1/HDB03/exe/python_support/systemReplicationStatus.py"
    ```

2. 當節點失去 NFS 共用 (/hana/shared 的存取權時，請確認叢集設定失敗案例) 

   SAP Hana 資源代理程式相依于二進位檔，並儲存在上， `/hana/shared` 以在容錯移轉期間執行作業。 `/hana/shared`在呈現的案例中，檔案系統會透過 NFS 掛接。  
   很難模擬失敗，其中一部伺服器會失去 NFS 共用的存取權。 可以執行的測試是以唯讀方式將檔案系統重新掛接。
   如果作用中節點上的存取遺失，此方法會驗證叢集是否能夠容錯移轉 `/hana/shared` 。     


   **預期的結果：** 在設 `/hana/shared` 為唯讀檔案系統時，在 `OCF_CHECK_LEVEL` `hana_shared1` 檔案系統上執行讀取/寫入作業的資源屬性將會失敗，因為它無法在檔案系統上寫入任何資訊，而且會執行 HANA 資源容錯移轉。  當您的 HANA 節點失去 NFS 共用的存取權時，預期會有相同的結果。 

   開始測試之前的資源狀態：

    ```
    sudo pcs status
    # Example output
    Full list of resources:
     rsc_hdb_azr_agt        (stonith:fence_azure_arm):      Started hanadb1

     Resource Group: hanadb1_nfs
         hana_data1 (ocf::heartbeat:Filesystem):    Started hanadb1
         hana_log1  (ocf::heartbeat:Filesystem):    Started hanadb1
         hana_shared1       (ocf::heartbeat:Filesystem):    Started hanadb1

    Resource Group: hanadb2_nfs
         hana_data2 (ocf::heartbeat:Filesystem):    Started hanadb2
         hana_log2  (ocf::heartbeat:Filesystem):    Started hanadb2
         hana_shared2       (ocf::heartbeat:Filesystem):    Started hanadb2

     hana_nfs1_active       (ocf::pacemaker:attribute):     Started hanadb1
     hana_nfs2_active       (ocf::pacemaker:attribute):     Started hanadb2

     Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
         Started: [ hanadb1 hanadb2 ]

     Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
         Masters: [ hanadb1 ]
         Slaves: [ hanadb2 ]

     Resource Group: g_ip_HN1_03
         nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hanadb1
         vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hanadb1
    ```

   您可以使用下列命令，將/hana/shared 以唯讀模式放置在主動叢集節點上：

    ```
    sudo mount -o ro 10.32.2.4:/hanadb1-shared-mnt00001 /hana/shared
    ```

   hanadb1 會根據 stonith () 上的動作集來重新開機或關機 `pcs property show stonith-action` 。  一旦伺服器 (hanadb1) 停止運作，HANA 資源就會移至 hanadb2。 您可以從 hanadb2 檢查叢集的狀態。

    ```
    pcs status

    Full list of resources:

     rsc_hdb_azr_agt        (stonith:fence_azure_arm):      Started hanadb2

     Resource Group: hanadb1_nfs
         hana_data1 (ocf::heartbeat:Filesystem):    Stopped
         hana_log1  (ocf::heartbeat:Filesystem):    Stopped
         hana_shared1       (ocf::heartbeat:Filesystem):    Stopped

     Resource Group: hanadb2_nfs
         hana_data2 (ocf::heartbeat:Filesystem):    Started hanadb2
         hana_log2  (ocf::heartbeat:Filesystem):    Started hanadb2
         hana_shared2       (ocf::heartbeat:Filesystem):    Started hanadb2

     hana_nfs1_active       (ocf::pacemaker:attribute):     Stopped
     hana_nfs2_active       (ocf::pacemaker:attribute):     Started hanadb2

     Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
         Started: [ hanadb2 ]
         Stopped: [ hanadb1 ]

     Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
         Masters: [ hanadb2 ]
         Stopped: [ hanadb1 ]

     Resource Group: g_ip_HN1_03
         nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hanadb2
         vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hanadb2
    ```

   我們建議您也在 [RHEL 上執行安裝 SAP Hana 系統](./sap-hana-high-availability-rhel.md#test-the-cluster-setup)複寫中所述的測試，以徹底測試 SAP Hana 叢集設定。