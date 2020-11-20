---
title: 使用 SLES on Azure Vm 進行 SAP Hana 相應放大 HSR-Pacemaker |Microsoft Docs
description: 在 Azure 虛擬機器執行的 SLES 12 SP3 上，針對根據 SAP HANA System Replication (HSR) 和 Pacemaker 的複雜 SAP HANA scale-out 高可用性設定進行檢查和疑難排解的指南
services: virtual-machines-linux
documentationcenter: ''
author: hermanndms
manager: juergent
editor: ''
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/24/2018
ms.author: hermannd
ms.openlocfilehash: 7cf18e2d375d7a45c3641876b8a3ed5974882927
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/20/2020
ms.locfileid: "94965420"
---
# <a name="verify-and-troubleshoot-sap-hana-scale-out-high-availability-setup-on-sles-12-sp3"></a>在 SLES 12 SP3 上驗證 SAP HANA scale-out 高可用性設定並為其進行疑難排解 

[sles-pacemaker-ha-guide]:high-availability-guide-suse-pacemaker.md
[sles-hana-scale-out-ha-paper]:https://www.suse.com/documentation/suse-best-practices/singlehtml/SLES4SAP-hana-scaleOut-PerfOpt-12/SLES4SAP-hana-scaleOut-PerfOpt-12.html
[sap-hana-iaas-list]:https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html
[suse-pacemaker-support-log-files]:https://www.suse.com/support/kb/doc/?id=7022702
[azure-linux-multiple-nics]:https://docs.microsoft.com/azure/virtual-machines/linux/multiple-nics
[suse-cloud-netconfig]:https://www.suse.com/c/multi-nic-cloud-netconfig-ec2-azure/
[sap-list-port-numbers]:https://help.sap.com/viewer/ports
[sles-12-ha-paper]:https://www.suse.com/documentation/sle-ha-12/pdfdoc/book_sleha/book_sleha.pdf
[sles-zero-downtime-paper]:https://www.suse.com/media/presentation/TUT90846_towards_zero_downtime%20_how_to_maintain_sap_hana_system_replication_clusters.pdf
[sap-nw-ha-guide-sles]:high-availability-guide-suse.md
[sles-12-for-sap]:https://www.suse.com/media/white-paper/suse_linux_enterprise_server_for_sap_applications_12_sp1.pdf


本文可協助您檢查 Azure 虛擬機器 (VM) 上執行之 SAP HANA scale-out 的 Pacemaker 叢集設定。 叢集設定是與 SAP HANA System Replication (HSR) 和 SUSE RPM 套件 SAPHanaSR-ScaleOut 一起完成。 所有測試都只是在 SUSE SLES 12 SP3 上完成。 本文的章節涵蓋不同區域，並包含來自設定檔的範例命令和摘要。 建議將這些範例用為驗證和檢查整個叢集設定的方法。



## <a name="important-notes"></a>重要事項

SAP HANA scale-out 與 SAP HANA System Replication 和 Pacemaker 的所有測試都僅使用 SAP HANA 2.0 完成。 作業系統版本是適用於 SAP 應用程式的 SUSE Linux Enterprise Server 12 SP3。 使用了 SUSE 中的最新 RPM 套件 SAPHanaSR-ScaleOut 來設定 pacemaker 叢集。
SUSE 已發佈[此效能最佳化設定的詳細描述][sles-hana-scale-out-ha-paper]。

針對 SAP HANA scale-out 所支援的虛擬機器類型，檢查 [SAP HANA 認證 IaaS 目錄][sap-hana-iaas-list]。

搭配使用 SAP HANA scale-out 與多個子網路和 vNIC 並設定 HSR 時發生技術問題。 必須使用已修正此問題的最新 SAP HANA 2.0 修補程式。 支援的 SAP HANA 版本如下： 

* rev2.00.024.04 或更新版本 
* rev2.00.032 或更新版本

如果您需要 SUSE 的支援，請遵循此[指南][suse-pacemaker-support-log-files]。 收集所有 SAP HANA 高可用性 (HA) 叢集的資訊，如本文中所述。 SUSE 支援需要這項資訊，以進一步分析。

在內部測試期間，叢集設定會與透過 Azure 入口網站的一般正常 VM 關機混淆。 因此，建議您以其他方法來測試叢集容錯移轉。 請使用強制核心異常或關閉網路這類方法，或遷移 **msl** 資源。 請參閱下列各節中的詳細資料。 假設蓄意發生標準關機。 蓄意關機的最佳範例是進行維護。 請參閱[計劃性維護](#planned-maintenance)中的詳細資料。

此外，在內部測試期間，叢集設定於手動 SAP HANA 接管之後會混淆，而叢集處於維護模式。 建議您在結束叢集維護模式前，再次手動將其切換回來。 另一個選項是，讓叢集進入維護模式前，先觸發容錯移轉。 如需詳細資訊，請參閱[計劃性維護](#planned-maintenance)。 SUSE 文件說明您可以如何使用 **crm** 命令以此方式來重設叢集。 但是，在內部測試期間，先前所述的方法較為穩固，而且絕不會顯示任何未預期的副作用。

使用 **crm 遷移** 命令時，請務必清除叢集設定。 該設定會新增您可能不知道的位置條件約束。 這些條件約束會影響叢集的行為。 請參閱[計劃性維護](#planned-maintenance)中的詳細資料。



## <a name="test-system-description"></a>測試系統描述

 一項設定已用於 SAP HANA scale-out 的 HA 驗證和認證。 該設定以兩個系統組成，每個系統具有三個 SAP HANA 節點：一個主要節點及兩個背景工作節點。 下表列出 VM 名稱和內部 IP 位址。 其後的所有驗證範例都是在這些 VM 上完成。 在命令範例中使用這些 VM 名稱和 IP 位址，可讓您深入了解命令和其輸出：


| 節點類型 | VM 名稱 | IP 位址 |
| --- | --- | --- |
| 站台 1 上的主要節點 | hso-hana-vm-s1-0 | 10.0.0.30 |
| 站台 1 上的背景工作節點 1 | hso-hana-vm-s1-1 | 10.0.0.31 |
| 站台 1 上的背景工作節點 2 | hso-hana-vm-s1-2 | 10.0.0.32 |
| | | |
| 站台 2 上的主要節點 | hso-hana-vm-s2-0 | 10.0.0.40 |
| 站台 2 上的背景工作節點 1 | hso-hana-vm-s2-1 | 10.0.0.41 |
| 站台 2 上的背景工作節點 2 | hso-hana-vm-s2-2  | 10.0.0.42 |
| | | |
| 多數製作者節點 | hso-hana-dm | 10.0.0.13 |
| SBD 裝置伺服器 | hso-hana-sbd | 10.0.0.19 |
| | | |
| NFS 伺服器 1 | hso-nfs-vm-0 | 10.0.0.15 |
| NFS 伺服器 2 | hso-nfs-vm-1 | 10.0.0.14 |



## <a name="multiple-subnets-and-vnics"></a>多個子網路和 vNIC

在遵循 SAP HANA 網路建議之後，會在一個 Azure 虛擬網路內建立三個子網路。 Azure 上的 SAP HANA scale-out 必須以非共用模式安裝。 這表示每個節點都會使用 **/hana/data** 和 **hana/log** 的本機磁碟區。 因為節點僅使用本機磁碟區，所以不需要定義儲存體的個別子網路：

- 10.0.2.0/24 適用於 SAP HANA 節點間通訊
- 10.0.1.0/24 適用於 SAP HANA System Replication (HSR)
- 10.0.0.0/24 適用於任何其他作業

如需使用多個網路的 SAP HANA 設定資訊，請參閱 [SAP HANA global.ini](#sap-hana-globalini)。

叢集中的每個 VM，都具有對應至子網路數目的三個 vNIC。 [如何以多個網路介面卡在 Azure 中建立 Linux 虛擬機器][azure-linux-multiple-nics]描述在部署 Linux VM 時，Azure 上的潛在路由問題。 本特定路由文章僅適用於使用多個 vNIC。 根據預設，在 SLES 12 SP3 中，是由 SUSE 來解決問題。 如需詳細資訊，請參閱 [EC2 和 Azure 中搭配雲端 netconfig 的多個 NIC][suse-cloud-netconfig]。


若要確認 SAP HANA 已正確設定為使用多個網路，請執行下列命令。 首先檢查所有三個子網路的所有三個內部 IP 位址均為使用中的 OS 層級。 如果您定義具有不同 IP 位址範圍的子網路，則必須調整命令：

<pre><code>
ifconfig | grep "inet addr:10\."
</code></pre>

下列是來自站台 2 第二個背景工作節點的範例輸出。 您可以看到來自 eth0、eth1 和 eth2 的三個不同內部 IP 位址：

<pre><code>
inet addr:10.0.0.42  Bcast:10.0.0.255  Mask:255.255.255.0
inet addr:10.0.1.42  Bcast:10.0.1.255  Mask:255.255.255.0
inet addr:10.0.2.42  Bcast:10.0.2.255  Mask:255.255.255.0
</code></pre>


接下來，確認名稱伺服器和 HSR 的 SAP HANA 連接埠。 SAP HANA 應該接聽對應的子網路。 根據 SAP HANA 執行個體數目，您必須調整命令。 在測試系統中，執行個體數目是 **00**。 有幾種不同方法可以找出所使用的連接埠。 

下列 SQL 陳述式會傳回執行個體識別碼、執行個體號碼和其他資訊：

<pre><code>
select * from "SYS"."M_SYSTEM_OVERVIEW"
</code></pre>

例如，若要尋找正確的埠號碼，您可以在 HANA Studio 中的 [設定 **] 或 [** 透過 SQL 語句] 下查看：

<pre><code>
select * from M_INIFILE_CONTENTS WHERE KEY LIKE 'listen%'
</code></pre>

若要尋找 SAP 軟體堆疊中使用的每個連接埠 (包含 SAP HANA)，請搜尋[所有 SAP 產品的 TCP/IP 連接埠][sap-list-port-numbers]。

假設 SAP HANA 2.0 測試系統中的執行個體數目為 **00**，則名稱伺服器的連接埠號碼為 **30001**。 HSR 中繼資料通訊的連接埠號碼為 **40002**。 其中一個選項是登入背景工作節點，然後檢查主要節點服務。 在本文中，我們檢查了站台 2 上的背景工作節點 2，嘗試連線至站台 2 上的主要節點。

檢查名稱伺服器連接埠：

<pre><code>
nc -vz 10.0.0.40 30001
nc -vz 10.0.1.40 30001
nc -vz 10.0.2.40 30001
</code></pre>

若要證明節點間通訊使用子網路 **10.0.2.0/24**，結果應該類似下列範例輸出。
應該只有透過子網路 **10.0.2.0/24** 的連線會成功：

<pre><code>
nc: connect to 10.0.0.40 port 30001 (tcp) failed: Connection refused
nc: connect to 10.0.1.40 port 30001 (tcp) failed: Connection refused
Connection to 10.0.2.40 30001 port [tcp/pago-services1] succeeded!
</code></pre>

現在，檢查 HSR 連接埠 **40002**：

<pre><code>
nc -vz 10.0.0.40 40002
nc -vz 10.0.1.40 40002
nc -vz 10.0.2.40 40002
</code></pre>

若要證明 HSR通訊使用子網路 **10.0.1.0/24**，結果應該類似下列範例輸出。
應該只有透過子網路 **10.0.1.0/24** 的連線會成功：

<pre><code>
nc: connect to 10.0.0.40 port 40002 (tcp) failed: Connection refused
Connection to 10.0.1.40 40002 port [tcp/*] succeeded!
nc: connect to 10.0.2.40 port 40002 (tcp) failed: Connection refused
</code></pre>



## <a name="corosync"></a>Corosync


在叢集的每個節點上，**corosync** 設定檔都必須正確 (包含多數製作者節點)。 如果節點的叢集聯結未如預期運作，請手動在所有節點上建立或複製 **/etc/corosync/corosync.conf**，並重新啟動服務。 

測試系統的 **corosync.conf** 內容即為範例。

第一個區段為 **totem**，如 [叢集安裝](./high-availability-guide-suse-pacemaker.md#cluster-installation)步驟 11 中所述。 您可以忽略 **mcastaddr** 的值。 只需要保留現有項目。 必須根據 [Microsoft Azure SAP HANA 文件][sles-pacemaker-ha-guide]設定 **token** 和 **consensus** 的項目。

<pre><code>
totem {
    version:    2
    secauth:    on
    crypto_hash:    sha1
    crypto_cipher:  aes256
    cluster_name:   hacluster
    clear_node_high_bit: yes

    token:      30000
    token_retransmits_before_loss_const: 10
    join:       60
    consensus:  36000
    max_messages:   20

    interface {
        ringnumber:     0
        bindnetaddr: 10.0.0.0
        mcastaddr:  239.170.19.232
        mcastport:  5405

        ttl:        1
    }
    transport:      udpu

}
</code></pre>

第二個區段（ **記錄**）未從指定的預設值變更：

<pre><code>
logging {
    fileline:   off
    to_stderr:  no
    to_logfile:     no
    logfile:    /var/log/cluster/corosync.log
    to_syslog:  yes
    debug:      off
    timestamp:  on
    logger_subsys {
        subsys:     QUORUM
        debug:  off
    }
}
</code></pre>

第三個區段顯示 **nodelist**。 必須以 **節點識別碼** 顯示叢集的所有節點：

<pre><code>
nodelist {
  node {
   ring0_addr:hso-hana-vm-s1-0
   nodeid: 1
   }
  node {
   ring0_addr:hso-hana-vm-s1-1
   nodeid: 2
   }
  node {
   ring0_addr:hso-hana-vm-s1-2
   nodeid: 3
   }
  node {
   ring0_addr:hso-hana-vm-s2-0
   nodeid: 4
   }
  node {
   ring0_addr:hso-hana-vm-s2-1
   nodeid: 5
   }
  node {
   ring0_addr:hso-hana-vm-s2-2
   nodeid: 6
   }
  node {
   ring0_addr:hso-hana-dm
   nodeid: 7
   }
}
</code></pre>

在最後一節 **仲裁** 中，請務必正確地設定 **expected_votes** 的值。 它必須是節點數目 (包含多數製作者節點)。 而且 **two_node** 的值必須是 **0**。 請不要整個移除項目。 只需要將值設定為 **0**。

<pre><code>
quorum {
    # Enable and configure quorum subsystem (default: off)
    # see also corosync.conf.5 and votequorum.5
    provider: corosync_votequorum
    expected_votes: 7
    two_node: 0
}
</code></pre>


透過 **systemctl** 重新啟動服務：

<pre><code>
systemctl restart corosync
</code></pre>




## <a name="sbd-device"></a>SBD 裝置

[SBD 隔離](./high-availability-guide-suse-pacemaker.md#sbd-fencing)描述如何在 Azure VM 上設定 SBD 裝置。

首先檢查 SBD 伺服器 VM，叢集中的每個節點是否都具有 ACL 項目。 在 SBD 伺服器 VM 上執行下列命令：


<pre><code>
targetcli ls
</code></pre>


在測試系統上，命令的輸出與下列範例類似。 必須輸入 **iqn.2006-04.hso-db-0.local:hso-db-0** 這類 ACL 名稱，作為 VM 上的對應啟動器名稱。 每個 VM 都需要不同的項目。

<pre><code>
 | | o- sbddbhso ................................................................... [/sbd/sbddbhso (50.0MiB) write-thru activated]
  | |   o- alua ................................................................................................... [ALUA Groups: 1]
  | |     o- default_tg_pt_gp ....................................................................... [ALUA state: Active/optimized]
  | o- pscsi .................................................................................................. [Storage Objects: 0]
  | o- ramdisk ................................................................................................ [Storage Objects: 0]
  o- iscsi ............................................................................................................ [Targets: 1]
  | o- iqn.2006-04.dbhso.local:dbhso ..................................................................................... [TPGs: 1]
  |   o- tpg1 ............................................................................................... [no-gen-acls, no-auth]
  |     o- acls .......................................................................................................... [ACLs: 7]
  |     | o- iqn.2006-04.hso-db-0.local:hso-db-0 .................................................................. [Mapped LUNs: 1]
  |     | | o- mapped_lun0 ............................................................................. [lun0 fileio/sbddbhso (rw)]
  |     | o- iqn.2006-04.hso-db-1.local:hso-db-1 .................................................................. [Mapped LUNs: 1]
  |     | | o- mapped_lun0 ............................................................................. [lun0 fileio/sbddbhso (rw)]
  |     | o- iqn.2006-04.hso-db-2.local:hso-db-2 .................................................................. [Mapped LUNs: 1]
  |     | | o- mapped_lun0 ............................................................................. [lun0 fileio/sbddbhso (rw)]
  |     | o- iqn.2006-04.hso-db-3.local:hso-db-3 .................................................................. [Mapped LUNs: 1]
  |     | | o- mapped_lun0 ............................................................................. [lun0 fileio/sbddbhso (rw)]
  |     | o- iqn.2006-04.hso-db-4.local:hso-db-4 .................................................................. [Mapped LUNs: 1]
  |     | | o- mapped_lun0 ............................................................................. [lun0 fileio/sbddbhso (rw)]
  |     | o- iqn.2006-04.hso-db-5.local:hso-db-5 .................................................................. [Mapped LUNs: 1]
  |     | | o- mapped_lun0 ............................................................................. [lun0 fileio/sbddbhso (rw)]
  |     | o- iqn.2006-04.hso-db-6.local:hso-db-6 .................................................................. [Mapped LUNs: 1]
</code></pre>

然後，請檢查所有 VM 上的啟動器名稱都不同，並且對應至先前顯示的項目。 此範例為站台 1 上的背景工作節點 1：

<pre><code>
cat /etc/iscsi/initiatorname.iscsi
</code></pre>

輸出看起來會如下列範例所示：

<pre><code>
##
## /etc/iscsi/iscsi.initiatorname
##
## Default iSCSI Initiatorname.
##
## DO NOT EDIT OR REMOVE THIS FILE!
## If you remove this file, the iSCSI daemon will not start.
## If you change the InitiatorName, existing access control lists
## may reject this initiator.  The InitiatorName must be unique
## for each iSCSI initiator.  Do NOT duplicate iSCSI InitiatorNames.
InitiatorName=iqn.2006-04.hso-db-1.local:hso-db-1
</code></pre>

接下來，確認 **探索** 正常運作。 使用 SBD 伺服器 VM 的 IP 位址，在每個叢集節點上執行下列命令：

<pre><code>
iscsiadm -m discovery --type=st --portal=10.0.0.19:3260
</code></pre>

輸出看起來應如下列範例所示：

<pre><code>
10.0.0.19:3260,1 iqn.2006-04.dbhso.local:dbhso
</code></pre>

下一個證明點是確認節點可看到 SDB 裝置。 在每個節點 (包括多數製作者節點) 上檢查它：

<pre><code>
lsscsi | grep dbhso
</code></pre>

輸出看起來應如下列範例所示。 不過，名稱可能會不同。 VM 重新開機之後，裝置名稱可能也會變更：

<pre><code>
[6:0:0:0]    disk    LIO-ORG  sbddbhso         4.0   /dev/sdm
</code></pre>

根據系統的狀態，重新啟動 iSCSI 服務有時可能有助於解決問題。 然後，執行下列命令：

<pre><code>
systemctl restart iscsi
systemctl restart iscsid
</code></pre>


從任何節點中，您可以檢查所有節點是否 **clear**。 請確認您在特定節點上使用正確的裝置名稱：

<pre><code>
sbd -d /dev/sdm list
</code></pre>

針對叢集中的每個節點，輸出應該顯示 **clear**：

<pre><code>
0       hso-hana-vm-s1-0        clear
1       hso-hana-vm-s2-2        clear
2       hso-hana-vm-s2-1        clear
3       hso-hana-dm     clear
4       hso-hana-vm-s1-1        clear
5       hso-hana-vm-s2-0        clear
6       hso-hana-vm-s1-2        clear
</code></pre>


另一項 SBD 檢查是 **sbd** 命令的 **dump** 選項。 在此範例中，命令和輸出來自裝置名稱不是 **sdm** 而是 **sdd** 的多數製作者節點：

<pre><code>
sbd -d /dev/sdd dump
</code></pre>

所有節點上的輸出 (除了裝置名稱之外) 應該都相同：

<pre><code>
==Dumping header on disk /dev/sdd
Header version     : 2.1
UUID               : 9fa6cc49-c294-4f1e-9527-c973f4d5a5b0
Number of slots    : 255
Sector size        : 512
Timeout (watchdog) : 60
Timeout (allocate) : 2
Timeout (loop)     : 1
Timeout (msgwait)  : 120
==Header on disk /dev/sdd is dumped
</code></pre>

另一項 SBD 檢查是將訊息傳送至另一個節點的可能性。 若要將訊息傳送至站台 2 上的背景工作節點 2，請在站台 2 上的背景工作節點 1 執行下列命令：

<pre><code>
sbd -d /dev/sdm message hso-hana-vm-s2-2 test
</code></pre>

在目標 VM 端 (在此範例中為 **hso-hana-vm-s2-2**)，您可以於 **/var/log/messages** 中找到下列項目：

<pre><code>
/dev/disk/by-id/scsi-36001405e614138d4ec64da09e91aea68:   notice: servant: Received command test from hso-hana-vm-s2-1 on disk /dev/disk/by-id/scsi-36001405e614138d4ec64da09e91aea68
</code></pre>

檢查 **/etc/sysconfig/sbd** 中的項目，是否對應於 [在 Azure 中的 SUSE Linux Enterprise Server 上設定 pacemaker](./high-availability-guide-suse-pacemaker.md#sbd-fencing) 的描述。 驗證 **/etc/iscsi/iscsid.conf** 中的啟動設定設定為 automatic (自動)。

下列項目在 **/etc/sysconfig/sbd** 中都很重要。 視需要調整 **識別碼** 值：

<pre><code>
SBD_DEVICE="/dev/disk/by-id/scsi-36001405e614138d4ec64da09e91aea68;"
SBD_PACEMAKER=yes
SBD_STARTMODE=always
SBD_WATCHDOG=yes
</code></pre>


檢查 **/etc/iscsi/iscsid.conf** 中的啟動設定。 下列 **iscsiadm** 命令應該已經執行必要設定，如文件中所述。 如有不同，請以 **vi** 驗證並手動進行調整。

此命令會設定啟動行為：

<pre><code>
iscsiadm -m node --op=update --name=node.startup --value=automatic
</code></pre>

在 **/etc/iscsi/iscsid.conf** 中建立此項目：

<pre><code>
node.startup = automatic
</code></pre>

在重新啟動 VM 後的測試和驗證期間，某些情況下將無法再看到 SBD 裝置。 啟動設定與 yast2 所顯示的內容有所差異。 若要檢查設定，請採取下列步驟：

1. 啟動 YaST2。
2. 選取左側的 [網路服務]。
3. 在右側，向下捲動至 [iSCSI 啟動器] 並將其選取。
4. 在下一個畫面的 [服務] 索引標籤下方，您會看到節點的唯一啟動器名稱。
5. 在啟動器名稱上方，確定 [啟動服務] 值設定為 [開機時]。
6. 如果尚未設定，則請將其設定為 [開機時]，而不是 [手動]。
7. 接下來，將最上層索引標籤切換至 [連線的目標]。
8. 在 [ **已連線的目標** ] 畫面上，您應該會看到 SBD 裝置的專案，如下列範例所示： **10.0.0.19： 3260 iqn. 2006-04. dbhso. local： dbhso**。
9. 檢查 [啟動] 值是否設定為 **on boot**。
10. 若不是，請選擇 [編輯] 予以變更。
11. 儲存變更並結束 YaST2。



## <a name="pacemaker"></a>Pacemaker

在正確設定所有項目之後，即可在每個節點上執行下列命令，以檢查 Pacemaker 服務的狀態：

<pre><code>
systemctl status pacemaker
</code></pre>

輸出的頂端看起來應如下列範例所示。 [使用中] 後的狀態必須顯示為 [loaded] \(已載入\) 和 [active (running)] \(使用中 (執行)\)。 [已載入] 後的狀態必須顯示為 [已啟用]。

<pre><code>
  pacemaker.service - Pacemaker High Availability Cluster Manager
   Loaded: loaded (/usr/lib/systemd/system/pacemaker.service; enabled; vendor preset: disabled)
   Active: active (running) since Fri 2018-09-07 05:56:27 UTC; 4 days ago
     Docs: man:pacemakerd
           http://clusterlabs.org/doc/en-US/Pacemaker/1.1-pcs/html/Pacemaker_Explained/index.html
 Main PID: 4496 (pacemakerd)
    Tasks: 7 (limit: 4915)
   CGroup: /system.slice/pacemaker.service
           ├─4496 /usr/sbin/pacemakerd -f
           ├─4499 /usr/lib/pacemaker/cib
           ├─4500 /usr/lib/pacemaker/stonithd
           ├─4501 /usr/lib/pacemaker/lrmd
           ├─4502 /usr/lib/pacemaker/attrd
           ├─4503 /usr/lib/pacemaker/pengine
           └─4504 /usr/lib/pacemaker/crmd
</code></pre>

如果設定仍然為 [已停用]，則請執行下列命令：

<pre><code>
systemctl enable pacemaker
</code></pre>

若要在 Pacemaker 中查看所有已設定的資源，請執行下列命令：

<pre><code>
crm status
</code></pre>

輸出看起來應如下列範例所示。 多數製作者 VM (**hso-hana-dm**) 上的 **cln** 和 **msl** 資源顯示為已停止是正常的。 多數製作者節點上並未安裝 SAP HANA。 因此，**cln** 和 **msl** 資源會顯示為已停止。 請務必顯示正確的 Vm 總數（ **7**）。 所有屬於叢集一部分的 VM，都必須以 [連線] 狀態列出。 目前主要的主要節點必須正確辨識。 在此範例中為 **hso-hana-vm-s1-0**：

<pre><code>
Stack: corosync
Current DC: hso-hana-dm (version 1.1.16-4.8-77ea74d) - partition with quorum
Last updated: Tue Sep 11 15:56:40 2018
Last change: Tue Sep 11 15:56:23 2018 by root via crm_attribute on hso-hana-vm-s1-0

7 nodes configured
17 resources configured

Online: [ hso-hana-dm hso-hana-vm-s1-0 hso-hana-vm-s1-1 hso-hana-vm-s1-2 hso-hana-vm-s2-0 hso-hana-vm-s2-1 hso-hana-vm-s2-2 ]

Full list of resources:

 stonith-sbd    (stonith:external/sbd): Started hso-hana-dm
 Clone Set: cln_SAPHanaTop_HSO_HDB00 [rsc_SAPHanaTop_HSO_HDB00]
     Started: [ hso-hana-vm-s1-0 hso-hana-vm-s1-1 hso-hana-vm-s1-2 hso-hana-vm-s2-0 hso-hana-vm-s2-1 hso-hana-vm-s2-2 ]
     Stopped: [ hso-hana-dm ]
 Master/Slave Set: msl_SAPHanaCon_HSO_HDB00 [rsc_SAPHanaCon_HSO_HDB00]
     Masters: [ hso-hana-vm-s1-0 ]
     Slaves: [ hso-hana-vm-s1-1 hso-hana-vm-s1-2 hso-hana-vm-s2-0 hso-hana-vm-s2-1 hso-hana-vm-s2-2 ]
     Stopped: [ hso-hana-dm ]
 Resource Group: g_ip_HSO_HDB00
     rsc_ip_HSO_HDB00   (ocf::heartbeat:IPaddr2):       Started hso-hana-vm-s1-0
     rsc_nc_HSO_HDB00   (ocf::heartbeat:anything):      Started hso-hana-vm-s1-0
</code></pre>

Pacemaker 的重要功能是維護模式。 在此模式中，您可以在不誘發立即叢集動作的情況下進行修改。 例如 VM 重新啟動。 已計劃的 OS 或 Azure 基礎結構維護即為典型使用案例。 請參閱[計劃性維護](#planned-maintenance)。 使用下列命令，讓 Pacemaker 進入維護模式：

<pre><code>
crm configure property maintenance-mode=true
</code></pre>

檢查 **crm status** 時，您會在輸出中注意到所有資源都標示為 [非受控]。 在此狀態中，叢集不會反應任何變更，例如啟動或停止 SAP HANA。
下列範例顯示叢集處於維護模式時的 **crm status** 命令輸出：

<pre><code>
Stack: corosync
Current DC: hso-hana-dm (version 1.1.16-4.8-77ea74d) - partition with quorum
Last updated: Wed Sep 12 07:48:10 2018
Last change: Wed Sep 12 07:46:54 2018 by root via cibadmin on hso-hana-vm-s2-1

7 nodes configured
17 resources configured

              **_ Resource management is DISABLED _*_
  The cluster will not attempt to start, stop or recover services

Online: [ hso-hana-dm hso-hana-vm-s1-0 hso-hana-vm-s1-1 hso-hana-vm-s1-2 hso-hana-vm-s2-0 hso-hana-vm-s2-1 hso-hana-vm-s2-2 ]

Full list of resources:

 stonith-sbd    (stonith:external/sbd): Started hso-hana-dm (unmanaged)
 Clone Set: cln_SAPHanaTop_HSO_HDB00 [rsc_SAPHanaTop_HSO_HDB00] (unmanaged)
     rsc_SAPHanaTop_HSO_HDB00   (ocf::suse:SAPHanaTopology):    Started hso-hana-vm-s1-1 (unmanaged)
     rsc_SAPHanaTop_HSO_HDB00   (ocf::suse:SAPHanaTopology):    Started hso-hana-vm-s1-0 (unmanaged)
     rsc_SAPHanaTop_HSO_HDB00   (ocf::suse:SAPHanaTopology):    Started hso-hana-vm-s1-2 (unmanaged)
     rsc_SAPHanaTop_HSO_HDB00   (ocf::suse:SAPHanaTopology):    Started hso-hana-vm-s2-1 (unmanaged)
     rsc_SAPHanaTop_HSO_HDB00   (ocf::suse:SAPHanaTopology):    Started hso-hana-vm-s2-2 (unmanaged)
     rsc_SAPHanaTop_HSO_HDB00   (ocf::suse:SAPHanaTopology):    Started hso-hana-vm-s2-0 (unmanaged)
     Stopped: [ hso-hana-dm ]
 Master/Slave Set: msl_SAPHanaCon_HSO_HDB00 [rsc_SAPHanaCon_HSO_HDB00] (unmanaged)
     rsc_SAPHanaCon_HSO_HDB00   (ocf::suse:SAPHanaController):  Slave hso-hana-vm-s1-1 (unmanaged)
     rsc_SAPHanaCon_HSO_HDB00   (ocf::suse:SAPHanaController):  Slave hso-hana-vm-s1-2 (unmanaged)
     rsc_SAPHanaCon_HSO_HDB00   (ocf::suse:SAPHanaController):  Slave hso-hana-vm-s2-1 (unmanaged)
     rsc_SAPHanaCon_HSO_HDB00   (ocf::suse:SAPHanaController):  Slave hso-hana-vm-s2-2 (unmanaged)
     rsc_SAPHanaCon_HSO_HDB00   (ocf::suse:SAPHanaController):  Master hso-hana-vm-s2-0 (unmanaged)
     Stopped: [ hso-hana-dm hso-hana-vm-s1-0 ]
 Resource Group: g_ip_HSO_HDB00
     rsc_ip_HSO_HDB00   (ocf::heartbeat:IPaddr2):       Started hso-hana-vm-s2-0 (unmanaged)
     rsc_nc_HSO_HDB00   (ocf::heartbeat:anything):      Started hso-hana-vm-s2-0 (unmanaged)
</code></pre>


此命令範例顯示如何結束叢集維護模式：

<pre><code>
crm configure property maintenance-mode=false
</code></pre>


另一個 _ *crm** 命令會在編輯器中取得完整的叢集設定，讓您可以編輯它。 儲存變更之後，叢集會啟動適當的動作：

<pre><code>
crm configure edit
</code></pre>

若要查看完整的叢集設定，請使用 **crm show** 選項：

<pre><code>
crm configure show
</code></pre>



叢集資源失敗之後，**crm status** 命令會顯示 [失敗的動作] 清單。 請參閱下列輸入範例：


<pre><code>
Stack: corosync
Current DC: hso-hana-dm (version 1.1.16-4.8-77ea74d) - partition with quorum
Last updated: Thu Sep 13 07:30:44 2018
Last change: Thu Sep 13 07:30:20 2018 by root via crm_attribute on hso-hana-vm-s1-0

7 nodes configured
17 resources configured

Online: [ hso-hana-dm hso-hana-vm-s1-0 hso-hana-vm-s1-1 hso-hana-vm-s1-2 hso-hana-vm-s2-0 hso-hana-vm-s2-1 hso-hana-vm-s2-2 ]

Full list of resources:

 stonith-sbd    (stonith:external/sbd): Started hso-hana-dm
 Clone Set: cln_SAPHanaTop_HSO_HDB00 [rsc_SAPHanaTop_HSO_HDB00]
     Started: [ hso-hana-vm-s1-0 hso-hana-vm-s1-1 hso-hana-vm-s1-2 hso-hana-vm-s2-0 hso-hana-vm-s2-1 hso-hana-vm-s2-2 ]
     Stopped: [ hso-hana-dm ]
 Master/Slave Set: msl_SAPHanaCon_HSO_HDB00 [rsc_SAPHanaCon_HSO_HDB00]
     Masters: [ hso-hana-vm-s1-0 ]
     Slaves: [ hso-hana-vm-s1-1 hso-hana-vm-s1-2 hso-hana-vm-s2-1 hso-hana-vm-s2-2 ]
     Stopped: [ hso-hana-dm hso-hana-vm-s2-0 ]
 Resource Group: g_ip_HSO_HDB00
     rsc_ip_HSO_HDB00   (ocf::heartbeat:IPaddr2):       Started hso-hana-vm-s1-0
     rsc_nc_HSO_HDB00   (ocf::heartbeat:anything):      Started hso-hana-vm-s1-0

Failed Actions:
* rsc_SAPHanaCon_HSO_HDB00_monitor_60000 on hso-hana-vm-s2-0 'unknown error' (1): call=86, status=complete, exitreason='none',
    last-rc-change='Wed Sep 12 17:01:28 2018', queued=0ms, exec=277663ms
</code></pre>

在失敗之後，必須執行叢集清理。 再次使用 **crm** 命令，並使用 **cleanup** 命令選項去除這些失敗動作項目。 將對應的叢集資源命名如下：

<pre><code>
crm resource cleanup rsc_SAPHanaCon_HSO_HDB00
</code></pre>

命令應會傳回類似於下列範例的輸出：

<pre><code>
Cleaned up rsc_SAPHanaCon_HSO_HDB00:0 on hso-hana-dm
Cleaned up rsc_SAPHanaCon_HSO_HDB00:0 on hso-hana-vm-s1-0
Cleaned up rsc_SAPHanaCon_HSO_HDB00:0 on hso-hana-vm-s1-1
Cleaned up rsc_SAPHanaCon_HSO_HDB00:0 on hso-hana-vm-s1-2
Cleaned up rsc_SAPHanaCon_HSO_HDB00:0 on hso-hana-vm-s2-0
Cleaned up rsc_SAPHanaCon_HSO_HDB00:0 on hso-hana-vm-s2-1
Cleaned up rsc_SAPHanaCon_HSO_HDB00:0 on hso-hana-vm-s2-2
Waiting for 7 replies from the CRMd....... OK
</code></pre>



## <a name="failover-or-takeover"></a>容錯移轉或接管

如[重要事項](#important-notes)中所述，您不應該使用標準正常關機來測試叢集容錯移轉或 SAP HANA HSR 接管。 反之，建議您觸發核心異常、強制資源移轉或關閉 VM 之 OS 層級上的所有網路。 另一個方法是 **crm \<node\> 待命** 命令。 請參閱 [SUSE 文件][sles-12-ha-paper]。 

下列三個範例命令可強制叢集容錯移轉：

<pre><code>
echo c &gt /proc/sysrq-trigger

crm resource migrate msl_SAPHanaCon_HSO_HDB00 hso-hana-vm-s2-0 force

wicked ifdown eth0
wicked ifdown eth1
wicked ifdown eth2
......
wicked ifdown eth&ltn&gt
</code></pre>

如 **計劃性維護** 所述，監視叢集活動的一項好方式是搭配執行 [SAPHanaSR-showAttr](#planned-maintenance) 與 **watch** 命令：

<pre><code>
watch SAPHanaSR-showAttr
</code></pre>

此外，查看來自 SAP Python 指令碼的 SAP HANA 橫向狀態也有幫助。 叢集設定會尋找此狀態值。 考慮背景工作節點失敗，就會十分清楚。 如果背景工作節點關閉，則 SAP HANA 不會立即傳回整個向外延展系統的健康狀態錯誤。 

需要重試數次，避免不必要的容錯移轉。 只有在狀態從 [正常] (傳回值 **4**) 變更為 [錯誤] (傳回值 **1**) 時，叢集才會做出反應。 因此，如果 **SAPHanaSR-showAttr** 的輸出顯示狀態為 **離線** 的 VM，即為正確。 但目前並沒有活動可切換主要和次要。 只要 SAP HANA 未傳回錯誤，就不會觸發任何叢集活動。

您可以藉由呼叫 SAP Python 腳本，以使用者 **\<HANA SID\> adm** 的形式監視 SAP Hana 的環境健康情況狀態，如下所示。 您可能必須調整路徑：

<pre><code>
watch python /hana/shared/HSO/exe/linuxx86_64/HDB_2.00.032.00.1533114046_eeaf4723ec52ed3935ae0dc9769c9411ed73fec5/python_support/landscapeHostConfiguration.py
</code></pre>

此命令的輸出應該與下列範例類似。 [主機狀態] 資料行，以及 [整體主機狀態] 兩者都很重要。 實際的輸出會具有額外資料行，因此會較寬。
為了更容易閱讀本文件內的輸出資料表，已移除右側的大部分資料行：

<pre><code>
| Host             | Host   | Host   | Failover | Remove | 
|                  | Active | Status | Status   | Status | 
|                  |        |        |          |        | 
| ---------------- | ------ | ------ | -------- | ------ |    .......
| hso-hana-vm-s2-0 | yes    | ok     |          |        |        
| hso-hana-vm-s2-1 | yes    | ok     |          |        |         
| hso-hana-vm-s2-2 | yes    | ok     |          |        |        

overall host status: ok
</code></pre>


還有另一個命令可檢查目前的叢集活動。 在終止主要站台的主要節點之後，請參閱下列命令和輸出結尾。 您可以看到轉換動作清單，例如將先前的次要主要節點 **hso-hana-vm-s2-0**，**升階** 為新主要的主要節點。 如果一切正常，且所有活動都已完成，則此 [轉換摘要] 清單必須為空白。

<pre><code>
 crm_simulate -Ls

...........

Transition Summary:
 * Fence hso-hana-vm-s1-0
 * Stop    rsc_SAPHanaTop_HSO_HDB00:1   (hso-hana-vm-s1-0)
 * Demote  rsc_SAPHanaCon_HSO_HDB00:1   (Master -> Stopped hso-hana-vm-s1-0)
 * Promote rsc_SAPHanaCon_HSO_HDB00:5   (Slave -> Master hso-hana-vm-s2-0)
 * Move    rsc_ip_HSO_HDB00     (Started hso-hana-vm-s1-0 -> hso-hana-vm-s2-0)
 * Move    rsc_nc_HSO_HDB00     (Started hso-hana-vm-s1-0 -> hso-hana-vm-s2-0)
</code></pre>



## <a name="planned-maintenance"></a>預定的維修 

進入計劃性維護時，有不同的使用案例。 其中一個問題是，此維護僅為基礎結構維護 (例如 OS 層級和磁碟設定的變更) 或是 HANA 升級。
您可以從 SUSE 的文件中，例如[朝向零停機時間][sles-zero-downtime-paper] 或 [SAP HANA SR 效能最佳化案例][sles-12-for-sap]，找到其他資訊。 這些文件也包含示範如何手動遷移主要站台的範例。

密集內部測試是要驗證基礎結構維護使用案例。 為了避免任何關於遷移主要站台的問題，我們決定一律先遷移主要站台，再讓叢集進入維護模式。 藉由此方法，不需要讓叢集忘記先前的情況：哪一端是主要站台，哪一端是次要站台。

在這部分有兩種不同的情況：

- **目前次要的預定維修**。 在此情況下，您只能讓叢集進入維護模式，以及在次要上執行工作，而不影響叢集。

- **目前主要站台上的計劃性維護**。 若要讓使用者可以在維護期間繼續工作，您需要強制容錯移轉。 使用此方式，您必須由 Pacemaker 觸發叢集容錯移轉，而不只是在 SAP HANA HSR 層級上。 Pacemaker 設定會自動觸發 SAP HANA 接管。 您也需要在讓叢集進入維護模式前，先完成容錯移轉。

目前次要站台的維護程序如下：

1. 讓叢集進入維護模式。
2. 完成次要站台上的工作。 
3. 結束叢集維護模式。

目前主要站台維護程序更為複雜：

1. 透過 Pacemaker 資源移轉，手動觸發容錯移轉或 SAP HANA 接管。 請參閱下列詳細資料。
2. 叢集設定會關閉先前主要站台上的 SAP HANA。
3. 讓叢集進入維護模式。
4. 維護工作完成之後，請將先前的主要站台註冊為新的次要站台。
5. 清除叢集設定。 請參閱下列詳細資料。
6. 結束叢集維護模式。


遷移資源會在叢集設定中新增項目。 範例為強制容錯移轉。 您必須先清除這些項目，再結束維護模式。 請參閱下列範例。

首先，將 **msl** 資源遷移至目前次要主要節點，來強制叢集容錯移轉。 此命令會提供已建立的 **移動條件約束** 警告：

<pre><code>
crm resource migrate msl_SAPHanaCon_HSO_HDB00 force

INFO: Move constraint created for msl_SAPHanaCon_HSO_HDB00
</code></pre>


透過 **SAPHanaSR-showAttr** 命令，檢查容錯移轉程序。 若要監視叢集狀態，請開啟專用殼層視窗，並以 **watch** 來啟動命令：

<pre><code>
watch SAPHanaSR-showAttr
</code></pre>

輸出應該會顯示手動容錯移轉。 先前的此要主要節點會 **升階**，在此範例中為 **hso-hana vm-s2-0**。 先前的主要站台已停止，先前主要的主要節點 **hso-hana vm-s1-0****lss** 值 **1**： 

<pre><code>
Global cib-time                 prim  sec srHook sync_state
------------------------------------------------------------
global Wed Sep 12 07:40:02 2018 HSOS2 -   SFAIL  SFAIL


Sites lpt        lss mns              srr
------------------------------------------
HSOS1 10         1   hso-hana-vm-s1-0 P
HSOS2 1536738002 4   hso-hana-vm-s2-0 P


Hosts            clone_state node_state roles                        score  site
----------------------------------------------------------------------------------
hso-hana-dm                  online
hso-hana-vm-s1-0 UNDEFINED   online     master1::worker:             150    HSOS1
hso-hana-vm-s1-1 DEMOTED     online     slave::worker:               -10000 HSOS1
hso-hana-vm-s1-2 DEMOTED     online     slave::worker:               -10000 HSOS1
hso-hana-vm-s2-0 PROMOTED    online     master1:master:worker:master 150    HSOS2
hso-hana-vm-s2-1 DEMOTED     online     slave:slave:worker:slave     -10000 HSOS2
hso-hana-vm-s2-2 DEMOTED     online     slave:slave:worker:slave     -10000 HSOS2
</code></pre>

在叢集容錯移轉和 SAP HANA 接管之後，請讓叢集進入維護模式，如 [Pacemaker](#pacemaker) 中所述。

**SAPHanaSR-showAttr** 和 **crm status** 命令不會指出與資源移轉所建立條件約束相關的任何資訊。 顯示這些條件約束的其中一個選項是使用下列命令來顯示完整叢集資源設定：

<pre><code>
crm configure show
</code></pre>

在叢集設定內，您會找到先前手動資源移轉所導致的新位置條件約束。 此範例項目以 **location cli-** 開頭：

<pre><code>
location cli-ban-msl_SAPHanaCon_HSO_HDB00-on-hso-hana-vm-s1-0 msl_SAPHanaCon_HSO_HDB00 role=Started -inf: hso-hana-vm-s1-0
</code></pre>

不幸的是，這類條件約束可能會影響整體叢集行為。 因此，必須再次將其移除，再備份整個系統。 使用 **unmigrate** 命令，可以清除先前建立的位置條件約束。 命名方式可能會讓人混淆。 不會嘗試將資源遷移回其從中遷移的原始 VM。 只會移除位置條件約束，並在您執行命令時一併傳回對應的資訊：


<pre><code>
crm resource unmigrate msl_SAPHanaCon_HSO_HDB00

INFO: Removed migration constraints for msl_SAPHanaCon_HSO_HDB00
</code></pre>

在維護工作結束時，請停止叢集維護模式，如 [Pacemaker](#pacemaker) 區段中所述。



## <a name="hb_report-to-collect-log-files"></a>hb_report 以收集記錄檔

為了分析 Pacemaker 叢集問題，這十分有用，並且受 SUSE 支援人員要求以執行 **hb_report** 公用程式。 會收集所需的所有重要記錄檔，讓您用於分析發生的情況。 此範例呼叫會使用特定事件發生時的開始和結束時間。 另請參閱[重要事項](#important-notes)：

<pre><code>
hb_report -f "2018/09/13 07:36" -t "2018/09/13 08:00" /tmp/hb_report_log
</code></pre>

此命令會告訴您壓縮記錄檔的放置位置：

<pre><code>
The report is saved in /tmp/hb_report_log.tar.bz2
Report timespan: 09/13/18 07:36:00 - 09/13/18 08:00:00
</code></pre>

您接著可以透過標準 **tar** 命令來解壓縮個別檔案：

<pre><code>
tar -xvf hb_report_log.tar.bz2
</code></pre>

查看解壓縮檔案時，您會找到所有記錄檔。 其中大部分都會放入叢集中每個節點的個別目錄：

<pre><code>
-rw-r--r-- 1 root root  13655 Sep 13 09:01 analysis.txt
-rw-r--r-- 1 root root  14422 Sep 13 09:01 description.txt
-rw-r--r-- 1 root root      0 Sep 13 09:01 events.txt
-rw-r--r-- 1 root root 275560 Sep 13 09:00 ha-log.txt
-rw-r--r-- 1 root root     26 Sep 13 09:00 ha-log.txt.info
drwxr-xr-x 4 root root   4096 Sep 13 09:01 hso-hana-dm
drwxr-xr-x 3 root root   4096 Sep 13 09:01 hso-hana-vm-s1-0
drwxr-xr-x 3 root root   4096 Sep 13 09:01 hso-hana-vm-s1-1
drwxr-xr-x 3 root root   4096 Sep 13 09:01 hso-hana-vm-s1-2
drwxr-xr-x 3 root root   4096 Sep 13 09:01 hso-hana-vm-s2-0
drwxr-xr-x 3 root root   4096 Sep 13 09:01 hso-hana-vm-s2-1
drwxr-xr-x 3 root root   4096 Sep 13 09:01 hso-hana-vm-s2-2
-rw-r--r-- 1 root root 264726 Sep 13 09:00 journal.log
</code></pre>


在指定的時間範圍內，會終止目前主要節點 **hso-hana-vm-s1-0**。 您可以在 **journal.log** 中找到與此事件相關的項目：

<pre><code>
2018-09-13T07:38:01+0000 hso-hana-vm-s2-1 su[93494]: (to hsoadm) root on none
2018-09-13T07:38:01+0000 hso-hana-vm-s2-1 su[93494]: pam_unix(su-l:session): session opened for user hsoadm by (uid=0)
2018-09-13T07:38:01+0000 hso-hana-vm-s2-1 systemd[1]: Started Session c44290 of user hsoadm.
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 corosync[28302]:   [TOTEM ] A new membership (10.0.0.13:120996) was formed. Members left: 1
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 corosync[28302]:   [TOTEM ] Failed to receive the leave message. failed: 1
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 attrd[28313]:   notice: Node hso-hana-vm-s1-0 state is now lost
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 attrd[28313]:   notice: Removing all hso-hana-vm-s1-0 attributes for peer loss
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 attrd[28313]:   notice: Purged 1 peer with id=1 and/or uname=hso-hana-vm-s1-0 from the membership cache
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 stonith-ng[28311]:   notice: Node hso-hana-vm-s1-0 state is now lost
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 stonith-ng[28311]:   notice: Purged 1 peer with id=1 and/or uname=hso-hana-vm-s1-0 from the membership cache
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 cib[28310]:   notice: Node hso-hana-vm-s1-0 state is now lost
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 corosync[28302]:   [QUORUM] Members[6]: 7 2 3 4 5 6
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 corosync[28302]:   [MAIN  ] Completed service synchronization, ready to provide service.
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 crmd[28315]:   notice: Node hso-hana-vm-s1-0 state is now lost
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 pacemakerd[28308]:   notice: Node hso-hana-vm-s1-0 state is now lost
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 cib[28310]:   notice: Purged 1 peer with id=1 and/or uname=hso-hana-vm-s1-0 from the membership cache
2018-09-13T07:38:03+0000 hso-hana-vm-s2-1 su[93494]: pam_unix(su-l:session): session closed for user hsoadm
</code></pre>

另一個範例是次要主要節點上的 Pacemaker 記錄檔，該節點已成為新主要的主要節點。 此摘要顯示已終止之主要的主要節點狀態已設定為 **離線**：

<pre><code>
Sep 13 07:38:02 [4178] hso-hana-vm-s2-0 stonith-ng:     info: pcmk_cpg_membership:      Node 3 still member of group stonith-ng (peer=hso-hana-vm-s1-2, counter=5.1)
Sep 13 07:38:02 [4178] hso-hana-vm-s2-0 stonith-ng:     info: pcmk_cpg_membership:      Node 4 still member of group stonith-ng (peer=hso-hana-vm-s2-0, counter=5.2)
Sep 13 07:38:02 [4178] hso-hana-vm-s2-0 stonith-ng:     info: pcmk_cpg_membership:      Node 5 still member of group stonith-ng (peer=hso-hana-vm-s2-1, counter=5.3)
Sep 13 07:38:02 [4178] hso-hana-vm-s2-0 stonith-ng:     info: pcmk_cpg_membership:      Node 6 still member of group stonith-ng (peer=hso-hana-vm-s2-2, counter=5.4)
Sep 13 07:38:02 [4178] hso-hana-vm-s2-0 stonith-ng:     info: pcmk_cpg_membership:      Node 7 still member of group stonith-ng (peer=hso-hana-dm, counter=5.5)
Sep 13 07:38:02 [4184] hso-hana-vm-s2-0       crmd:     info: pcmk_cpg_membership:      Node 1 left group crmd (peer=hso-hana-vm-s1-0, counter=5.0)
Sep 13 07:38:02 [4184] hso-hana-vm-s2-0       crmd:     info: crm_update_peer_proc:     pcmk_cpg_membership: Node hso-hana-vm-s1-0[1] - corosync-cpg is now offline
Sep 13 07:38:02 [4184] hso-hana-vm-s2-0       crmd:     info: peer_update_callback:     Client hso-hana-vm-s1-0/peer now has status [offline] (DC=hso-hana-dm, changed=4000000)
Sep 13 07:38:02 [4184] hso-hana-vm-s2-0       crmd:     info: pcmk_cpg_membership:      Node 2 still member of group crmd (peer=hso-hana-vm-s1-1, counter=5.0)
</code></pre>





## <a name="sap-hana-globalini"></a>SAP HANA global.ini


下列摘要來自叢集站台 2 上的 SAP HANA **global.ini** 檔案。 此範例顯示針對 SAP HANA 節點間通訊和 HSR 使用不同網路的主機名稱解析項目：

<pre><code>
[communication]
tcp_keepalive_interval = 20
internal_network = 10.0.2/24
listeninterface = .internal
</code></pre>


<pre><code>
[internal_hostname_resolution]
10.0.2.40 = hso-hana-vm-s2-0
10.0.2.42 = hso-hana-vm-s2-2
10.0.2.41 = hso-hana-vm-s2-1
</code></pre>

<pre><code>
[ha_dr_provider_SAPHanaSR]
provider = SAPHanaSR
path = /hana/shared/myHooks
execution_order = 1
</code></pre>

<pre><code>
[system_replication_communication]
listeninterface = .internal

[system_replication_hostname_resolution]
10.0.1.30 = hso-hana-vm-s1-0
10.0.1.31 = hso-hana-vm-s1-1
10.0.1.32 = hso-hana-vm-s1-2
10.0.1.40 = hso-hana-vm-s2-0
10.0.1.41 = hso-hana-vm-s2-1
10.0.1.42 = hso-hana-vm-s2-2
</code></pre>



## <a name="hawk"></a>Hawk

叢集解決方案可提供瀏覽器介面，為偏好功能表和圖形 (相較於殼層層級上的所有命令) 的人員提供 GUI。
若要使用瀏覽器介面，請將取代為 **\<node\>** 下列 URL 中的實際 SAP Hana 節點。 然後輸入叢集的認證 (使用者 **叢集**)：

<pre><code>
https://&ltnode&gt:7630
</code></pre>

此螢幕擷取畫面顯示叢集儀表板：


![Hawk 叢集儀表板](media/hana-vm-scale-out-HA-troubleshooting/hawk-1.png)


此範例顯示叢集資源移轉所導致的位置條件約束，如[計劃性維護](#planned-maintenance)中所述：


![Hawk 清單條件約束](media/hana-vm-scale-out-HA-troubleshooting/hawk-2.png)


您也可以在 Hawk 的 **歷程記錄** 下方，上傳 **hb_report** 輸出，如下所示。 請參閱 hb_report 以收集記錄檔： 

![Hawk 上傳 hb_report 輸出](media/hana-vm-scale-out-HA-troubleshooting/hawk-3.png)

藉由 **History Explorer**，您可以接著執行 **hb_report** 輸出中所含的所有叢集轉換：

![hb_report 輸出中的 Hawk 轉換](media/hana-vm-scale-out-HA-troubleshooting/hawk-4.png)

此最後一個螢幕擷取畫面顯示單一轉換的 [詳細資料] 區段。 叢集在主要的主要節點當機時作出回應，節點 **hso-hana vm-s1-0**。 會將該次要節點升階為新的主要節點，**hso-hana vm-s2-0**：

![Hawk 單一轉換](media/hana-vm-scale-out-HA-troubleshooting/hawk-5.png)


## <a name="next-steps"></a>後續步驟

本疑難排解指南描述向外延展設定中的 SAP HANA 高可用性。 除了資料庫之外，SAP 橫向中的另一個重要元件是 SAP NetWeaver 堆疊。 了解[使用 SUSE Enterprise Linux Server 之 Azure 虛擬機器上的 SAP NetWeaver 高可用性][sap-nw-ha-guide-sles]。
