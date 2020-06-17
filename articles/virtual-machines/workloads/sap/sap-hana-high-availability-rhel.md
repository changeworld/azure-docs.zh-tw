---
title: RHEL 上 Azure VM 的 SAP Hana 高可用性
description: 在 Azure 虛擬機器 (VM) 上，建立 SAP HANA 的高可用性。
services: virtual-machines-linux
documentationcenter: ''
author: rdeltcheva
manager: juergent
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/21/2020
ms.author: radeltch
ms.openlocfilehash: ed53b77587e307926689b2c20d7223212f3394d4
ms.sourcegitcommit: cf7caaf1e42f1420e1491e3616cc989d504f0902
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/22/2020
ms.locfileid: "83800268"
---
# <a name="high-availability-of-sap-hana-on-azure-vms-on-red-hat-enterprise-linux"></a>Red Hat Enterprise Linux 上 Azure VM 的 SAP HANA 高可用性

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md

[2205917]:https://launchpad.support.sap.com/#/notes/2205917
[1944799]:https://launchpad.support.sap.com/#/notes/1944799
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[1984787]:https://launchpad.support.sap.com/#/notes/1984787
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2388694]:https://launchpad.support.sap.com/#/notes/2388694
[2292690]:https://launchpad.support.sap.com/#/notes/2292690
[2455582]:https://launchpad.support.sap.com/#/notes/2455582
[2002167]:https://launchpad.support.sap.com/#/notes/2002167
[2009879]:https://launchpad.support.sap.com/#/notes/2009879

[sap-swcenter]:https://launchpad.support.sap.com/#/softwarecenter
[template-multisid-db]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db-md%2Fazuredeploy.json

進行內部部署開發時，您可以使用 HANA 系統複寫或使用共用儲存體來建立 SAP HANA 的高可用性。
在 Azure 虛擬機器 (VM) 上，Azure 上的 HANA 系統複寫是目前為止唯一支援的高可用性功能。
SAP HANA 複寫包含一個主要節點以及至少一個次要節點。 對主要節點上資料的變更，會以同步或非同步方式複寫到次要節點。

本文說明如何部署和設定虛擬機器、安裝叢集架構，以及安裝和設定 SAP HANA 系統複寫。
在組態範例中，會使用安裝命令、執行個體號碼 **03** 和 HANA 系統識別碼 **HN1**。

請先閱讀下列 SAP Note 和文件：

* SAP Note [1928533]，其中包含：
  * SAP 軟體部署支援的 Azure VM 大小清單。
  * Azure VM 大小的重要容量資訊。
  * 支援的 SAP 軟體，以及作業系統 (OS) 與資料庫組合。
  * Microsoft Azure 上 Windows 和 Linux 所需的 SAP 核心版本。
* SAP Note [2015553] 列出 Azure 中 SAP 支援的 SAP 軟體部署先決條件。
* SAP Note [2002167] 建議適用於 Red Hat Enterprise Linux 的作業系統設定
* SAP Note [2009879] 提供適用於 Red Hat Enterprise Linux 的 SAP HANA 方針
* SAP Note [2178632] 包含在 Azure 中針對 SAP 回報的所有監視計量詳細資訊。
* SAP Note [2191498] 包含 Azure 中 Linux 所需的 SAP Host Agent 版本。
* SAP Note [2243692] 包含 Azure 中 Linux 上的 SAP 授權相關資訊。
* SAP Note [1999351] 包含 Azure Enhanced Monitoring Extension for SAP 的其他疑難排解資訊。
* [SAP Community WIKI](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) 包含 Linux 所需的所有 SAP Note。
* [適用於 SAP on Linux 的 Azure 虛擬機器規劃和實作][planning-guide]
* [適用於 SAP on Linux 的 Azure 虛擬機器部署 (本文)][deployment-guide]
* [適用於 SAP on Linux 的 Azure 虛擬機器 DBMS 部署][dbms-guide]
* [Pacemaker 叢集中的 SAP HANA 系統複寫](https://access.redhat.com/articles/3004101)
* 一般 RHEL 文件
  * [高可用性附加元件概觀](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_overview/index)
  * [高可用性附加元件管理](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_administration/index)
  * [高可用性附加元件參考](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_reference/index)
* Azure 專用 RHEL 文件：
  * [RHEL 高可用性叢集的支援原則：以 Microsoft Azure 虛擬機器作為叢集成員](https://access.redhat.com/articles/3131341)
  * [在 Microsoft Azure 上安裝和設定 Red Hat Enterprise Linux 7.4 (和更新版本) 高可用性叢集](https://access.redhat.com/articles/3252491)
  * [在 Red Hat Enterprise Linux 上安裝 SAP HANA 以用於 Microsoft Azure](https://access.redhat.com/solutions/3193782)

## <a name="overview"></a>概觀

為了達到高可用性，SAP HANA 會安裝在兩個虛擬機器上。 資料會使用「HANA 系統複寫」進行複寫。

![SAP HANA 高可用性概觀](./media/sap-hana-high-availability-rhel/ha-hana.png)

SAP HANA 系統複寫設定會使用專用的虛擬主機名稱和虛擬 IP 位址。 在 Azure 上必須有負載平衡器才能使用虛擬 IP 位址。 下列清單顯示負載平衡器的組態：

* 前端組態：hn1-db 的 IP 位址 10.0.0.13
* 後端組態：連線到應屬於 HANA 系統複寫一部分的所有虛擬機器的主要網路介面
* 探查連接埠：連接埠 62503
* 負載平衡規則：30313 TCP、30315 TCP、30317 TCP、30340 TCP、30341 TCP、30342 TCP

## <a name="deploy-for-linux"></a>針對 Linux 進行部署

Azure Marketplace 包含 Red Hat Enterprise Linux for SAP HANA 7.4 的映像，您可用來部署新的虛擬機器。

### <a name="deploy-with-a-template"></a>透過範本進行部署

您可以使用 GitHub 上的其中一個快速入門範本來部署所有必要資源。 範本會部署虛擬機器、負載平衡器、可用性設定組等。
若要部署範本，請遵循下列步驟：

1. 在 Azure 入口網站上開啟[資料庫範本][template-multisid-db]。
1. 輸入下列參數：
    * **SAP 系統識別碼**：輸入您想要安裝的 SAP 系統之 SAP 系統識別碼。 該識別碼會作為所部署之資源的前置詞。
    * **OS 類型**：選取一個 Linux 發行版本。 在此範例中，請選取 [RHEL 7]。
    * **DB 類型**：選取 **HANA**。
    * **SAP 系統大小**：輸入新系統要提供的 SAP 數量。 如果您不確定系統需要多少 SAP，請詢問您的 SAP 技術合作夥伴或系統整合者。
    * **系統可用性**：選取 **HA**。
    * **管理員使用者名稱、管理員密碼或 SSH 金鑰**：將建立可用來登入電腦的新使用者。
    * **子網路識別碼**：如果您想將 VM 部署至現有的 VNet (其中具有定義 VM 應指派的目的子網路)，請說明該特定子網路的 ID。 識別碼通常如下所示： **/subscriptions/\<訂用帳戶識別碼>/resourceGroups/\<資源群組名稱>/providers/Microsoft.Network/virtualNetworks/\<虛擬網路名稱>/subnets/\<子網路名稱>** 。 如果您想要建立新的虛擬網路，請保留空白

### <a name="manual-deployment"></a>手動部署

1. 建立資源群組。
1. 建立虛擬網路。
1. 建立可用性設定組。  
   設定更新網域上限。
1. 建立負載平衡器 (內部)。 建議[標準負載平衡器](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview)。
   * 選取步驟 2 所建立的虛擬網路。
1. 建立虛擬機器 1。  
   至少使用 Red Hat Enterprise Linux for SAP HANA 7.4。 這個範例會使用 Red Hat Enterprise Linux for SAP HANA 7.4 的映像 <https://portal.azure.com/#create/RedHat.RedHatEnterpriseLinux75forSAP-ARM> 選取在步驟 3 建立的可用性設定組。
1. 建立虛擬機器 2。  
   至少使用 Red Hat Enterprise Linux for SAP HANA 7.4。 這個範例會使用 Red Hat Enterprise Linux for SAP HANA 7.4 的映像 <https://portal.azure.com/#create/RedHat.RedHatEnterpriseLinux75forSAP-ARM> 選取在步驟 3 建立的可用性設定組。
1. 新增資料磁碟。
1. 如果使用標準負載平衡器，請遵循下列設定步驟：
   1. 首先，建立前端 IP 集區：

      1. 開啟負載平衡器，選取 [前端 IP 集區]，然後選取 [新增]。
      1. 輸入新前端 IP 集區的名稱 (例如 **hana-frontend**)。
      1. 將 [指派] 設定為 [靜態]，然後輸入 IP 位址 (例如 **10.0.0.13**)。
      1. 選取 [確定]。
      1. 建立新的前端 IP 集區之後，請記下集區 IP 位址。

   1. 接下來，建立後端集區：

      1. 開啟負載平衡器，選取 [後端集區]，然後選取 [新增]。
      1. 輸入新後端集區的名稱 (例如 **hana-backend**)。
      1. 選取 [新增虛擬機器]。
      1. 選取 [虛擬機器]。
      1. 選取 SAP Hana 叢集的虛擬機器及其 IP 位址。
      1. 選取 [新增]。

   1. 接下來，建立健康情況探查：

      1. 開啟負載平衡器，選取 [健康情況探查]，然後選取 [新增]。
      1. 輸入新健康情況探查的名稱 (例如 **hana-hp**)。
      1. 選取 [TCP] 通訊協定和連接埠 **62503**。 讓 [間隔] 值保持設定為 5，而讓 [狀況不良閾值] 值保持設定為 2。
      1. 選取 [確定]。

   1. 接下來，建立負載平衡規則：
   
      1. 開啟負載平衡器，選取 [負載平衡規則]，然後選取 [新增]。
      1. 輸入新負載平衡器規則的名稱 (例如 **hana-lb**)。
      1. 選取您稍早建立的前端 IP 位址、後端集區及健康情況探查 (例如，**hana-frontend**、**hana-backend** 和 **hana-hp**)。
      1. 選取 [HA 連接埠]。
      1. 將 [閒置逾時] 增加為 30 分鐘。
      1. 務必**啟用浮動 IP**。
      1. 選取 [確定]。

   > [!Note]
   > 當不具公用 IP 位址的 VM 放在內部 (沒有公用 IP 位址) 標準 Azure 負載平衡器的後端集區時，除非另外設定來允許路由傳送至公用端點，否則不會有輸出網際網路連線能力。 如需如何實現輸出連線能力的詳細資訊，請參閱[在 SAP 高可用性情節中使用 Azure Standard Load Balancer 實現虛擬機器的公用端點連線能力](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-standard-load-balancer-outbound-connections)。  

1. 或者，如果您的情節要求使用基本負載平衡器，請遵循下列設定步驟：
   1. 設定負載平衡器。 首先，建立前端 IP 集區：

      1. 開啟負載平衡器，選取 [前端 IP 集區]，然後選取 [新增]。
      1. 輸入新前端 IP 集區的名稱 (例如 **hana-frontend**)。
      1. 將 [指派] 設定為 [靜態]，然後輸入 IP 位址 (例如 **10.0.0.13**)。
      1. 選取 [確定]。
      1. 建立新的前端 IP 集區之後，請記下集區 IP 位址。

   1. 接下來，建立後端集區：

      1. 開啟負載平衡器，選取 [後端集區]，然後選取 [新增]。
      1. 輸入新後端集區的名稱 (例如 **hana-backend**)。
      1. 選取 [新增虛擬機器]。
      1. 選取步驟 3 所建立的可用性設定組。
      1. 選取 SAP HANA 叢集的虛擬機器。
      1. 選取 [確定]。

   1. 接下來，建立健康情況探查：

      1. 開啟負載平衡器，選取 [健康情況探查]，然後選取 [新增]。
      1. 輸入新健康情況探查的名稱 (例如 **hana-hp**)。
      1. 選取 [TCP] 通訊協定和連接埠 **62503**。 讓 [間隔] 值保持設定為 5，而讓 [狀況不良閾值] 值保持設定為 2。
      1. 選取 [確定]。

   1. 針對 SAP HANA 1.0，建立負載平衡規則：

      1. 開啟負載平衡器，選取 [負載平衡規則]，然後選取 [新增]。
      1. 輸入新負載平衡器規則的名稱 (例如 hana-lb-3**03**15)。
      1. 選取您稍早建立的前端 IP 位址、後端集區及健康情況探查 (例如 **hana-frontend**)。
      1. 讓 [通訊協定] 保持設定為 [TCP]，然後輸入連接埠 3**03**15。
      1. 將 [閒置逾時] 增加為 30 分鐘。
      1. 務必**啟用浮動 IP**。
      1. 選取 [確定]。
      1. 對連接埠 3**03**17 重複執行這些步驟。

   1. 若為 SAP Hana 2.0，請針對系統資料庫建立負載平衡規則：

      1. 開啟負載平衡器，選取 [負載平衡規則]，然後選取 [新增]。
      1. 輸入新負載平衡器規則的名稱 (例如 hana-lb-3**03**13)。
      1. 選取您稍早建立的前端 IP 位址、後端集區及健康情況探查 (例如 **hana-frontend**)。
      1. 讓 [通訊協定] 保持設定為 [TCP]，然後輸入連接埠 3**03**13。
      1. 將 [閒置逾時] 增加為 30 分鐘。
      1. 務必**啟用浮動 IP**。
      1. 選取 [確定]。
      1. 針對連接埠 3**03**14 重複這些步驟。

   1. 若為 SAP Hana 2.0，請先針對租用戶資料庫建立負載平衡規則：

      1. 開啟負載平衡器，選取 [負載平衡規則]，然後選取 [新增]。
      1. 輸入新負載平衡器規則的名稱 (例如 hana-lb-3**03**40)。
      1. 選取您稍早建立的前端 IP 位址、後端集區及健康情況探查 (例如 **hana-frontend**)。
      1. 讓 [通訊協定] 保持設定為 [TCP]，然後輸入連接埠 3**03**40。
      1. 將 [閒置逾時] 增加為 30 分鐘。
      1. 務必**啟用浮動 IP**。
      1. 選取 [確定]。
      1. 針對連接埠 3**03**41 和 3**03**42 重複這些步驟。

如需 SAP Hana 所需連接埠的詳細資訊，請參閱 [SAP Hana 租用戶資料庫](https://help.sap.com/viewer/78209c1d3a9b41cd8624338e42a12bf6)指南的[連線到租用戶資料庫](https://help.sap.com/viewer/78209c1d3a9b41cd8624338e42a12bf6/latest/en-US/7a9343c9f2a2436faa3cfdb5ca00c052.html)一章，或 [SAP Note 2388694][2388694]。

> [!IMPORTANT]
> 請勿在位於 Azure Load Balancer 後方的 Azure VM 上啟用 TCP 時間戳記。 啟用 TCP 時間戳記會導致健康情況探查失敗。 將參數 **net.ipv4.tcp_timestamps** 設定為 **0**。 如需詳細資料，請參閱[負載平衡器健康情況探查](https://docs.microsoft.com/azure/load-balancer/load-balancer-custom-probe-overview)。
> 另請參閱 SAP Note [2382421](https://launchpad.support.sap.com/#/notes/2382421)。 

## <a name="install-sap-hana"></a>安裝 SAP HANA

本節中的步驟會使用下列首碼：

* **[A]** ：此步驟適用於所有節點。
* **[1]** ：此步驟僅適用於節點 1。
* **[2]** ：此步驟僅適用於 Pacemaker 叢集的節點 2。

1. **[A]** 設定磁碟配置：**邏輯磁碟區管理 (LVM)** 。

   建議您針對儲存資料和記錄檔的磁碟區使用 LVM。 下列範例假設虛擬機器已連接四個資料磁碟，這些資料磁碟會用來建立兩個磁碟區。

   列出所有可用的磁碟：

   <pre><code>ls /dev/disk/azure/scsi1/lun*
   </code></pre>

   範例輸出︰

   <pre><code>
   /dev/disk/azure/scsi1/lun0  /dev/disk/azure/scsi1/lun1  /dev/disk/azure/scsi1/lun2  /dev/disk/azure/scsi1/lun3
   </code></pre>

   針對您想要使用的所有磁碟建立實體磁碟區：

   <pre><code>sudo pvcreate /dev/disk/azure/scsi1/lun0
   sudo pvcreate /dev/disk/azure/scsi1/lun1
   sudo pvcreate /dev/disk/azure/scsi1/lun2
   sudo pvcreate /dev/disk/azure/scsi1/lun3
   </code></pre>

   建立資料檔案的磁碟區群組。 一個磁碟區群組用於記錄檔，一個用於 SAP HANA 的共用目錄：

   <pre><code>sudo vgcreate vg_hana_data_<b>HN1</b> /dev/disk/azure/scsi1/lun0 /dev/disk/azure/scsi1/lun1
   sudo vgcreate vg_hana_log_<b>HN1</b> /dev/disk/azure/scsi1/lun2
   sudo vgcreate vg_hana_shared_<b>HN1</b> /dev/disk/azure/scsi1/lun3
   </code></pre>

   建立邏輯磁碟區。 當您使用 `lvcreate` 卻未搭配 `-i` 參數時，會建立線性磁碟區。 建議您建立等量磁碟區以提高 I/O 效能，並將等量大小調整為 [SAP Hana VM 儲存體設定](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage)中記載的值。 `-i` 引數應該為基礎實體磁碟區的數目，`-I` 引數是等量大小。 本文件會使用 2 個實體磁碟區來作為資料磁碟區，因此 `-i` 參數引數會設定為 **2**。 資料磁碟區的等量大小是 **256KiB**。 有一個實體磁碟區作為記錄磁碟區，因此，記錄磁碟區命令中不需要明確使用 `-i` 或 `-I` 參數。  

   > [!IMPORTANT]
   > 當您要對每個資料、記錄或共用磁碟區使用多個實體磁碟區時，請使用 `-i` 參數，並將其值設定為基礎實體磁碟區的數目。 建立等量磁碟區時，請使用 `-I` 參數來指定等量大小。  
   > 請參閱 [SAP Hana VM 儲存體設定](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage)，以取得建議的儲存體設定，包括等量大小和磁碟數目。  

   <pre><code>sudo lvcreate <b>-i 2</b> <b>-I 256</b> -l 100%FREE -n hana_data vg_hana_data_<b>HN1</b>
   sudo lvcreate -l 100%FREE -n hana_log vg_hana_log_<b>HN1</b>
   sudo lvcreate -l 100%FREE -n hana_shared vg_hana_shared_<b>HN1</b>
   sudo mkfs.xfs /dev/vg_hana_data_<b>HN1</b>/hana_data
   sudo mkfs.xfs /dev/vg_hana_log_<b>HN1</b>/hana_log
   sudo mkfs.xfs /dev/vg_hana_shared_<b>HN1</b>/hana_shared
   </code></pre>

   建立掛接目錄，並複製所有邏輯磁碟區的 UUID：

   <pre><code>sudo mkdir -p /hana/data/<b>HN1</b>
   sudo mkdir -p /hana/log/<b>HN1</b>
   sudo mkdir -p /hana/shared/<b>HN1</b>
   # Write down the ID of /dev/vg_hana_data_<b>HN1</b>/hana_data, /dev/vg_hana_log_<b>HN1</b>/hana_log, and /dev/vg_hana_shared_<b>HN1</b>/hana_shared
   sudo blkid
   </code></pre>

   針對這三個邏輯磁碟區建立 `fstab` 項目：

   <pre><code>sudo vi /etc/fstab
   </code></pre>

   在 `/etc/fstab` 檔案中插入下列行：

   <pre><code>/dev/disk/by-uuid/<b>&lt;UUID of /dev/mapper/vg_hana_data_<b>HN1</b>-hana_data&gt;</b> /hana/data/<b>HN1</b> xfs  defaults,nofail  0  2
   /dev/disk/by-uuid/<b>&lt;UUID of /dev/mapper/vg_hana_log_<b>HN1</b>-hana_log&gt;</b> /hana/log/<b>HN1</b> xfs  defaults,nofail  0  2
   /dev/disk/by-uuid/<b>&lt;UUID of /dev/mapper/vg_hana_shared_<b>HN1</b>-hana_shared&gt;</b> /hana/shared/<b>HN1</b> xfs  defaults,nofail  0  2
   </code></pre>

   掛接新的磁碟區：

   <pre><code>sudo mount -a
   </code></pre>

1. **[A]** 設定磁碟配置：**一般磁碟**。

   針對示範系統，您可以將 HANA 資料和記錄檔放在一個磁碟上。 在 /dev/disk/azure/scsi1/lun0 上建立磁碟分割，並使用 xfs 將其格式化：

   <pre><code>sudo sh -c 'echo -e "n\n\n\n\n\nw\n" | fdisk /dev/disk/azure/scsi1/lun0'
   sudo mkfs.xfs /dev/disk/azure/scsi1/lun0-part1
   
   # Write down the ID of /dev/disk/azure/scsi1/lun0-part1
   sudo /sbin/blkid
   sudo vi /etc/fstab
   </code></pre>

   將這行插入至 /etc/fstab 檔案：

   <pre><code>/dev/disk/by-uuid/<b>&lt;UUID&gt;</b> /hana xfs  defaults,nofail  0  2
   </code></pre>

   建立目標目錄並掛接磁碟：

   <pre><code>sudo mkdir /hana
   sudo mount -a
   </code></pre>

1. **[A]** 設定所有主機的主機名稱解析。

   您可以使用 DNS 伺服器，或修改所有節點上的 /etc/hosts 檔案。 這個範例示範如何使用 /etc/hosts 檔案。
   請取代下列命令中的 IP 位址和主機名稱：

   <pre><code>sudo vi /etc/hosts
   </code></pre>

   將下列幾行插入至 /etc/hosts 檔案。 變更 IP 位址和主機名稱以符合您的環境：

   <pre><code><b>10.0.0.5 hn1-db-0</b>
   <b>10.0.0.6 hn1-db-1</b>
   </code></pre>

1. **[A]** RHEL for HANA 設定

   按照 SAP Note [2292690]、[2455582] 和 <https://access.redhat.com/solutions/2447641> 中的描述來設定 RHEL。

1. **[A]** 安裝 SAP HANA

   若要安裝 SAP HANA 系統複寫，請遵循 <https://access.redhat.com/articles/3004101>。

   * 從 HANA DVD 執行 **hdblcm** 程式。 在提示上輸入下列值：
   * 選擇安裝：輸入 **1**。
   * 選取要安裝的其他元件：輸入 **1**。
   * 輸入安裝路徑 [/hana/shared]：選取 Enter 鍵。
   * 輸入本機主機名稱 [..]：選取 Enter 鍵。
   * 您是否要將其他主機新增至系統？ (y/n) [n]：選取 Enter 鍵。
   * 輸入 SAP HANA 系統識別碼︰輸入 HANA 的 SID，例如：**HN1**。
   * 輸入執行個體號碼 [00]：輸入 HANA 執行個體編號。 如果您使用了 Azure 範本或遵循了本文的手動部署章節，請輸入 **03**。
   * 選取資料庫模式 / 輸入索引 [1]：選取 Enter 鍵。
   * 選取系統使用量 / 輸入索引 [4]：選取系統使用量。
   * 輸入資料磁碟區的位置 [/hana/data/HN1]：選取 Enter 鍵。
   * 輸入記錄磁碟區的位置 [/hana/log/HN1]：選取 Enter 鍵。
   * 是否限制記憶體配置上限？ [n]：選取 Enter 鍵。
   * 輸入主機的憑證主機名稱 '...' [...]：選取 Enter 鍵。
   * 輸入 SAP 主機代理程式使用者 (sapadm) 密碼：輸入主機代理程式使用者密碼。
   * 確認 SAP 主機代理程式使用者 (sapadm) 密碼：再次輸入主機代理程式使用者密碼以進行確認。
   * 輸入系統管理員 (hdbadm) 密碼：輸入系統管理員密碼。
   * 確認系統管理員 (hdbadm) 密碼：再次輸入系統管理員密碼以進行確認。
   * 輸入系統管理員主目錄 [/usr/sap/HN1/home]：選取 Enter 鍵。
   * 輸入系統管理員登入殼層 [/bin/sh]：->選取 Enter 鍵。
   * 輸入系統管理員使用者識別碼 [1001]：選取 Enter 鍵。
   * 輸入使用者群組 (sapsys) 的識別碼 [79]：選取 Enter 鍵。
   * 輸入資料庫使用者 (SYSTEM) 密碼：輸入資料庫使用者密碼。
   * 確認資料庫使用者 (SYSTEM) 密碼：再次輸入資料庫使用者密碼以進行確認。
   * 是否在電腦重新開機後重新啟動系統？ [n]：選取 Enter 鍵。
   * 是否要繼續？ (y/n)：驗證摘要。 輸入 **y** 繼續。

1. **[A]** 升級 SAP 主機代理程式。

   從 [SAP 軟體中心][sap-swcenter]下載最新的 SAP 主機代理程式封存檔，然後執行下列命令來升級代理程式。 取代封存檔的路徑以指向您所下載的檔案：

   <pre><code>sudo /usr/sap/hostctrl/exe/saphostexec -upgrade -archive &lt;path to SAP Host Agent SAR&gt;
   </code></pre>

1. **[A]** 設定防火牆

   建立 Azure 負載平衡器探查連接埠的防火牆規則。

   <pre><code>sudo firewall-cmd --zone=public --add-port=625<b>03</b>/tcp
   sudo firewall-cmd --zone=public --add-port=625<b>03</b>/tcp --permanent
   </code></pre>

## <a name="configure-sap-hana-20-system-replication"></a>設定 SAP HANA 2.0 系統複寫

本節中的步驟會使用下列首碼：

* **[A]** ：此步驟適用於所有節點。
* **[1]** ：此步驟僅適用於節點 1。
* **[2]** ：此步驟僅適用於 Pacemaker 叢集的節點 2。

1. **[A]** 設定防火牆

   建立防火牆規則以允許 HANA 系統複寫和用戶端流量。 [所有 SAP 產品的 TCP/IP 通訊埠](https://help.sap.com/viewer/ports)會列出必要的連接埠。 下列命令只是允許 HANA 2.0 系統複寫和用戶端流量傳送到資料庫 SYSTEMDB、HN1 和 NW1 的範例。

   <pre><code>sudo firewall-cmd --zone=public --add-port=40302/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=40302/tcp
   sudo firewall-cmd --zone=public --add-port=40301/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=40301/tcp
   sudo firewall-cmd --zone=public --add-port=40307/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=40307/tcp
   sudo firewall-cmd --zone=public --add-port=40303/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=40303/tcp
   sudo firewall-cmd --zone=public --add-port=40340/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=40340/tcp
   sudo firewall-cmd --zone=public --add-port=30340/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=30340/tcp
   sudo firewall-cmd --zone=public --add-port=30341/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=30341/tcp
   sudo firewall-cmd --zone=public --add-port=30342/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=30342/tcp
   </code></pre>

1. **[1]** 建立租用戶資料庫。

   如果您使用 SAP HANA 2.0 或 MDC，請為您的 SAP NetWeaver 系統建立租用戶資料庫。 請將 **NW1** 取代為您 SAP 系統的 SID。

   以 <hanasid\>adm 身分執行下列命令：

   <pre><code>hdbsql -u SYSTEM -p "<b>passwd</b>" -i <b>03</b> -d SYSTEMDB 'CREATE DATABASE <b>NW1</b> SYSTEM USER PASSWORD "<b>passwd</b>"'
   </code></pre>

1. **[1]** 在第一個節點上設定系統複寫：

   以 <hanasid\>adm 身分備份資料庫：

   <pre><code>hdbsql -d SYSTEMDB -u SYSTEM -p "<b>passwd</b>" -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackupSYS</b>')"
   hdbsql -d <b>HN1</b> -u SYSTEM -p "<b>passwd</b>" -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackupHN1</b>')"
   hdbsql -d <b>NW1</b> -u SYSTEM -p "<b>passwd</b>" -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackupNW1</b>')"
   </code></pre>

   將系統 PKI 檔案複製到次要網站：

   <pre><code>scp /usr/sap/<b>HN1</b>/SYS/global/security/rsecssfs/data/SSFS_<b>HN1</b>.DAT   <b>hn1-db-1</b>:/usr/sap/<b>HN1</b>/SYS/global/security/rsecssfs/data/
   scp /usr/sap/<b>HN1</b>/SYS/global/security/rsecssfs/key/SSFS_<b>HN1</b>.KEY  <b>hn1-db-1</b>:/usr/sap/<b>HN1</b>/SYS/global/security/rsecssfs/key/
   </code></pre>

   建立主要網站：

   <pre><code>hdbnsutil -sr_enable --name=<b>SITE1</b>
   </code></pre>

1. **[2]** 在第二個節點上設定系統複寫：
    
   註冊第二個節點，以啟動系統複寫。 以 <hanasid\>adm 身分執行下列命令：

   <pre><code>sapcontrol -nr <b>03</b> -function StopWait 600 10
   hdbnsutil -sr_register --remoteHost=<b>hn1-db-0</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE2</b>
   </code></pre>

1. **[1]** 檢查複寫狀態

   檢查複寫狀態，並等到所有資料庫都同步為止。如果狀態保持為 UNKNOWN，請檢查您的防火牆設定。

   <pre><code>sudo su - <b>hn1</b>adm -c "python /usr/sap/<b>HN1</b>/HDB<b>03</b>/exe/python_support/systemReplicationStatus.py"
   # | Database | Host     | Port  | Service Name | Volume ID | Site ID | Site Name | Secondary | Secondary | Secondary | Secondary | Secondary     | Replication | Replication | Replication    |
   # |          |          |       |              |           |         |           | Host      | Port      | Site ID   | Site Name | Active Status | Mode        | Status      | Status Details |
   # | -------- | -------- | ----- | ------------ | --------- | ------- | --------- | --------- | --------- | --------- | --------- | ------------- | ----------- | ----------- | -------------- |
   # | SYSTEMDB | <b>hn1-db-0</b> | 30301 | nameserver   |         1 |       1 | <b>SITE1</b>     | <b>hn1-db-1</b>  |     30301 |         2 | <b>SITE2</b>     | YES           | SYNC        | ACTIVE      |                |
   # | <b>HN1</b>      | <b>hn1-db-0</b> | 30307 | xsengine     |         2 |       1 | <b>SITE1</b>     | <b>hn1-db-1</b>  |     30307 |         2 | <b>SITE2</b>     | YES           | SYNC        | ACTIVE      |                |
   # | <b>NW1</b>      | <b>hn1-db-0</b> | 30340 | indexserver  |         2 |       1 | <b>SITE1</b>     | <b>hn1-db-1</b>  |     30340 |         2 | <b>SITE2</b>     | YES           | SYNC        | ACTIVE      |                |
   # | <b>HN1</b>      | <b>hn1-db-0</b> | 30303 | indexserver  |         3 |       1 | <b>SITE1</b>     | <b>hn1-db-1</b>  |     30303 |         2 | <b>SITE2</b>     | YES           | SYNC        | ACTIVE      |                |
   #
   # status system replication site "2": ACTIVE
   # overall system replication status: ACTIVE
   #
   # Local System Replication State
   # ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
   #
   # mode: PRIMARY
   # site id: 1
   # site name: <b>SITE1</b>
   </code></pre>

## <a name="configure-sap-hana-10-system-replication"></a>設定 SAP HANA 1.0 系統複寫

本節中的步驟會使用下列首碼：

* **[A]** ：此步驟適用於所有節點。
* **[1]** ：此步驟僅適用於節點 1。
* **[2]** ：此步驟僅適用於 Pacemaker 叢集的節點 2。

1. **[A]** 設定防火牆

   建立防火牆規則以允許 HANA 系統複寫和用戶端流量。 [所有 SAP 產品的 TCP/IP 通訊埠](https://help.sap.com/viewer/ports)會列出必要的連接埠。 下列命令只是允許 HANA 2.0 系統複寫的範例。 請將其調整成您的 SAP HANA 1.0 安裝。

   <pre><code>sudo firewall-cmd --zone=public --add-port=40302/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=40302/tcp
   </code></pre>

1. **[1]** 建立必要的使用者。

   以 root 身分執行下列命令。 請務必以 SAP HANA 安裝的值取代粗體字串 (HANA 系統識別碼 **HN1** 和執行個體號碼 **03**)：

   <pre><code>PATH="$PATH:/usr/sap/<b>HN1</b>/HDB<b>03</b>/exe"
   hdbsql -u system -i <b>03</b> 'CREATE USER <b>hdb</b>hasync PASSWORD "<b>passwd</b>"'
   hdbsql -u system -i <b>03</b> 'GRANT DATA ADMIN TO <b>hdb</b>hasync'
   hdbsql -u system -i <b>03</b> 'ALTER USER <b>hdb</b>hasync DISABLE PASSWORD LIFETIME'
   </code></pre>

1. **[A]** 建立金鑰儲存區項目。

   以 root 身分執行下列命令，建立新的金鑰儲存區項目：

   <pre><code>PATH="$PATH:/usr/sap/<b>HN1</b>/HDB<b>03</b>/exe"
   hdbuserstore SET <b>hdb</b>haloc localhost:3<b>03</b>15 <b>hdb</b>hasync <b>passwd</b>
   </code></pre>

1. **[1]** 備份資料庫。

   以 root 身分備份資料庫：

   <pre><code>PATH="$PATH:/usr/sap/<b>HN1</b>/HDB<b>03</b>/exe"
   hdbsql -d SYSTEMDB -u system -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackup</b>')"
   </code></pre>

   如果您使用多租用戶安裝，請同時備份租用戶資料庫：

   <pre><code>hdbsql -d <b>HN1</b> -u system -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackup</b>')"
   </code></pre>

1. **[1]** 在第一個節點上設定系統複寫。

   以 <hanasid\>adm 身分建立主要網站：

   <pre><code>su - <b>hdb</b>adm
   hdbnsutil -sr_enable –-name=<b>SITE1</b>
   </code></pre>

1. **[2]** 在次要節點上設定系統複寫。

   以 <hanasid\>adm 身分註冊次要網站：

   <pre><code>HDB stop
   hdbnsutil -sr_register --remoteHost=<b>hn1-db-0</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE2</b>
   HDB start
   </code></pre>

## <a name="create-a-pacemaker-cluster"></a>建立 Pacemaker 叢集

遵循[在 Azure 的 Red Hat Enterprise Linux 上設定 Pacemaker](high-availability-guide-rhel-pacemaker.md) 中的步驟，建立此 HANA 伺服器的基本 Pacemaker 叢集。

## <a name="create-sap-hana-cluster-resources"></a>建立 SAP HANA 叢集資源

在**所有節點**上安裝 SAP HANA 資源代理程式。 請務必啟用包含套件的存放庫。

<pre><code># Enable repository that contains SAP HANA resource agents
sudo subscription-manager repos --enable="rhel-sap-hana-for-rhel-7-server-rpms"
   
sudo yum install -y resource-agents-sap-hana
</code></pre>

接下來，建立 HANA 拓撲。 在其中一個 Pacemaker 叢集節點上執行下列命令：

<pre><code>sudo pcs property set maintenance-mode=true

# Replace the bold string with your instance number and HANA system ID
sudo pcs resource create SAPHanaTopology_<b>HN1</b>_<b>03</b> SAPHanaTopology SID=<b>HN1</b> InstanceNumber=<b>03</b> \
op start timeout=600 op stop timeout=300 op monitor interval=10 timeout=600 \
--clone clone-max=2 clone-node-max=1 interleave=true
</code></pre>

接下來，建立 HANA 資源：

<pre><code># Replace the bold string with your instance number, HANA system ID, and the front-end IP address of the Azure load balancer.

sudo pcs resource create SAPHana_<b>HN1</b>_<b>03</b> SAPHana SID=<b>HN1</b> InstanceNumber=<b>03</b> PREFER_SITE_TAKEOVER=true DUPLICATE_PRIMARY_TIMEOUT=7200 AUTOMATED_REGISTER=false \
op start timeout=3600 op stop timeout=3600 \
op monitor interval=61 role="Slave" timeout=700 \
op monitor interval=59 role="Master" timeout=700 \
op promote timeout=3600 op demote timeout=3600 \
master notify=true clone-max=2 clone-node-max=1 interleave=true

sudo pcs resource create vip_<b>HN1</b>_<b>03</b> IPaddr2 ip="<b>10.0.0.13</b>"

sudo pcs resource create nc_<b>HN1</b>_<b>03</b> azure-lb port=625<b>03</b>

sudo pcs resource group add g_ip_<b>HN1</b>_<b>03</b> nc_<b>HN1</b>_<b>03</b> vip_<b>HN1</b>_<b>03</b>

sudo pcs constraint order SAPHanaTopology_<b>HN1</b>_<b>03</b>-clone then SAPHana_<b>HN1</b>_<b>03</b>-master symmetrical=false

sudo pcs constraint colocation add g_ip_<b>HN1</b>_<b>03</b> with master SAPHana_<b>HN1</b>_<b>03</b>-master 4000

sudo pcs property set maintenance-mode=false
</code></pre>

請確定叢集狀態正常，且所有資源皆已啟動。 資源在哪一個節點上執行並不重要。

> [!NOTE]
> 上述設定中的逾時只是例子，可能需要針對特定的 HANA 安裝來調整。 比方說，如果啟動 SAP Hana 資料庫需要較長的時間，您可能需要延長啟動逾時。  

<pre><code>sudo pcs status

# Online: [ hn1-db-0 hn1-db-1 ]
#
# Full list of resources:
#
# azure_fence     (stonith:fence_azure_arm):      Started hn1-db-0
#  Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
#      Started: [ hn1-db-0 hn1-db-1 ]
#  Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
#      Masters: [ hn1-db-0 ]
#      Slaves: [ hn1-db-1 ]
#  Resource Group: g_ip_HN1_03
#      nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hn1-db-0
#      vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hn1-db-0
</code></pre>

## <a name="test-the-cluster-setup"></a>測試叢集設定

本節將說明如何測試您的設定。 開始測試之前，請先確定 Pacemaker 沒有任何失敗的動作 (透過 pcs status)、沒有未預期的位置條件約束 (例如，移轉測試的剩餘項目)，且 HANA 是同步狀態，例如使用 systemReplicationStatus：

<pre><code>[root@hn1-db-0 ~]# sudo su - hn1adm -c "python /usr/sap/HN1/HDB03/exe/python_support/systemReplicationStatus.py"
</code></pre>

### <a name="test-the-migration"></a>測試移轉

開始測試之前的資源狀態：

<pre><code>Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
    Started: [ hn1-db-0 hn1-db-1 ]
Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
    Masters: [ hn1-db-0 ]
    Slaves: [ hn1-db-1 ]
Resource Group: g_ip_HN1_03
    nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hn1-db-0
    vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hn1-db-0
</code></pre>

您可以執行下列命令來移轉 SAP HANA 主要節點：

<pre><code>[root@hn1-db-0 ~]# pcs resource move SAPHana_HN1_03-master
</code></pre>

如果您設定 `AUTOMATED_REGISTER="false"`，此命令應該會將 SAP HANA 主要節點以及包含虛擬 IP 位址的群組移轉到 hn1-db-1。

完成移轉之後，'sudo pcs status' 輸出看起來像這樣

<pre><code>Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
    Started: [ hn1-db-0 hn1-db-1 ]
Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
    Masters: [ hn1-db-1 ]
    Stopped: [ hn1-db-0 ]
Resource Group: g_ip_HN1_03
    nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hn1-db-1
    vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hn1-db-1
</code></pre>

hn1-db-0 上的 SAP HANA 資源即會停止。 在此情況下，執行下列命令來將 HANA 執行個體設為次要：

<pre><code>[root@hn1-db-0 ~]# su - hn1adm

# Stop the HANA instance just in case it is running
hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> sapcontrol -nr 03 -function StopWait 600 10
hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=hn1-db-1 --remoteInstance=03 --replicationMod
e=sync --name=SITE1
</code></pre>

移轉會建立需要再次刪除的位置條件約束：

<pre><code># Switch back to root
exit
[root@hn1-db-0 ~]# pcs resource clear SAPHana_HN1_03-master
</code></pre>

使用 'pcs status' 監視 HANA 資源的狀態。 hn1-db-0 上啟動 HANA 之後，輸出應該看起來像這樣

<pre><code>Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
    Started: [ hn1-db-0 hn1-db-1 ]
Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
    Masters: [ hn1-db-1 ]
    Slaves: [ hn1-db-0 ]
Resource Group: g_ip_HN1_03
    nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hn1-db-1
    vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hn1-db-1
</code></pre>

### <a name="test-the-azure-fencing-agent"></a>測試 Azure 隔離代理程式

開始測試之前的資源狀態：

<pre><code>Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
    Started: [ hn1-db-0 hn1-db-1 ]
Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
    Masters: [ hn1-db-1 ]
    Slaves: [ hn1-db-0 ]
Resource Group: g_ip_HN1_03
    nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hn1-db-1
    vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hn1-db-1
</code></pre>

您可以藉由停用 SAP HANA 執行主要節點上的網路介面，來測試 Azure 隔離代理程式的設定。
如需如何模擬網路失敗的說明，請參閱 [Red Hat 知識庫文章 79523](https://access.redhat.com/solutions/79523)。 在此範例中，我們會使用 net_breaker 指令碼來封鎖所有對網路的存取。

<pre><code>[root@hn1-db-1 ~]# sh ./net_breaker.sh BreakCommCmd 10.0.0.6
</code></pre>

根據您的叢集組態，虛擬機器現在應該會重新啟動或停止。
如果您將 `stonith-action` 設定設為 off，虛擬機器會停止，並將資源移轉到執行中的虛擬機器。

當您重新啟動虛擬機器之後，如果您設定 `AUTOMATED_REGISTER="false"`，SAP HANA 資源就無法啟動為次要。 在此情況下，執行下列命令來將 HANA 執行個體設為次要：

<pre><code>su - <b>hn1</b>adm

# Stop the HANA instance just in case it is running
hn1adm@hn1-db-1:/usr/sap/HN1/HDB03> sapcontrol -nr <b>03</b> -function StopWait 600 10
hn1adm@hn1-db-1:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=<b>hn1-db-0</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE2</b>

# Switch back to root and clean up the failed state
exit
[root@hn1-db-1 ~]# pcs resource cleanup SAPHana_HN1_03-master
</code></pre>

測試完成之後的資源狀態：

<pre><code>Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
    Started: [ hn1-db-0 hn1-db-1 ]
Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
    Masters: [ hn1-db-0 ]
    Slaves: [ hn1-db-1 ]
Resource Group: g_ip_HN1_03
    nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hn1-db-0
    vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hn1-db-0
</code></pre>

### <a name="test-a-manual-failover"></a>測試手動容錯移轉

開始測試之前的資源狀態：

<pre><code>Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
    Started: [ hn1-db-0 hn1-db-1 ]
Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
    Masters: [ hn1-db-0 ]
    Slaves: [ hn1-db-1 ]
Resource Group: g_ip_HN1_03
    nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hn1-db-0
    vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hn1-db-0
</code></pre>

您可以藉由停止 hn1-db-0 節點上的叢集，來測試手動容錯移轉：

<pre><code>[root@hn1-db-0 ~]# pcs cluster stop
</code></pre>

容錯移轉之後，您可以重新啟動叢集。 如果您設定 `AUTOMATED_REGISTER="false"`，hn1-db-0 節點上的 SAP HANA 資源會無法以次要身分啟動。 在此情況下，執行下列命令來將 HANA 執行個體設為次要：

<pre><code>[root@hn1-db-0 ~]# pcs cluster start
[root@hn1-db-0 ~]# su - hn1adm

# Stop the HANA instance just in case it is running
hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> sapcontrol -nr 03 -function StopWait 600 10
hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=<b>hn1-db-1</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE1</b>

# Switch back to root and clean up the failed state
hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> exit
[root@hn1-db-1 ~]# pcs resource cleanup SAPHana_HN1_03-master
</code></pre>

測試完成之後的資源狀態：

<pre><code>Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
    Started: [ hn1-db-0 hn1-db-1 ]
Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
    Masters: [ hn1-db-1 ]
     Slaves: [ hn1-db-0 ]
Resource Group: g_ip_HN1_03
    nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hn1-db-1
    vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hn1-db-1
</code></pre>

### <a name="test-a-manual-failover"></a>測試手動容錯移轉

開始測試之前的資源狀態：

<pre><code>Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
    Started: [ hn1-db-0 hn1-db-1 ]
Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
    Masters: [ hn1-db-0 ]
    Slaves: [ hn1-db-1 ]
Resource Group: g_ip_HN1_03
    nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hn1-db-0
    vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hn1-db-0
</code></pre>

您可以藉由停止 hn1-db-0 節點上的叢集，來測試手動容錯移轉：

<pre><code>[root@hn1-db-0 ~]# pcs cluster stop
</code></pre>


## <a name="next-steps"></a>後續步驟

* [適用於 SAP 的 Azure 虛擬機器規劃和實作][planning-guide]
* [適用於 SAP 的 Azure 虛擬機器部署][deployment-guide]
* [適用於 SAP 的 Azure 虛擬機器 DBMS 部署][dbms-guide]
* [SAP Hana VM 儲存體設定](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage)