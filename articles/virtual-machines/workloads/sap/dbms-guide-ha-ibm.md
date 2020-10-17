---
title: 在 Azure 虛擬機器上設定 IBM Db2 HADR (Vm) |Microsoft Docs
description: 在 (Vm) 的 Azure 虛擬機器上，建立 IBM Db2 LUW 的高可用性。
author: msjuergent
ms.service: virtual-machines
ms.topic: article
ms.date: 10/16/2020
ms.author: juergent
ms.reviewer: cynthn
ms.openlocfilehash: 88a84cd90efb42ea096cad647d75f1c3736426f4
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/17/2020
ms.locfileid: "92146444"
---
# <a name="high-availability-of-ibm-db2-luw-on-azure-vms-on-suse-linux-enterprise-server-with-pacemaker"></a>使用 Pacemaker SUSE Linux Enterprise Server 的 Azure Vm 上的 IBM Db2 LUW 高可用性

IBM Db2 for Linux、UNIX 和 Windows (LUW) 在 [高可用性和嚴重損壞修復 (HADR) ](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_10.5.0/com.ibm.db2.luw.admin.ha.doc/doc/c0011267.html) 設定包含一個節點來執行主資料庫實例，以及至少一個執行次要資料庫實例的節點。 主資料庫實例的變更會以同步或非同步方式複寫至次要資料庫實例，視您的設定而定。 

本文說明如何 (Vm) 部署及設定 Azure 虛擬機器、安裝叢集架構，以及使用 HADR 設定安裝 IBM Db2 LUW。 

本文不會說明如何安裝和設定具有 HADR 或 SAP 軟體安裝的 IBM Db2 LUW。 為了協助您完成這些工作，我們提供 SAP 和 IBM 安裝手冊的參考。 本文著重于 Azure 環境專屬的部分。 

支援的 IBM Db2 版本為10.5 和更新版本，如 SAP note [1928533]中所述。

開始安裝之前，請參閱下列 SAP 附注和檔：

| SAP 附注 | 描述 |
| --- | --- |
| [1928533] | Azure 上的 SAP 應用程式：支援的產品和 Azure VM 類型 |
| [2015553] | Azure 上的 SAP：支援必要條件 |
| [2178632] | Azure 上的 SAP 的重要監視計量 |
| [2191498] | Linux 上搭配 Azure 的 SAP：增強型監視 |
| [2243692] | Azure 上的 Linux (IaaS) VM： SAP 授權問題 |
| [1984787] | SUSE LINUX Enterprise Server 12：安裝注意事項 |
| [1999351] | 對適用於 SAP 的增強型 Azure 監視功能進行疑難排解 |
| [2233094] | DB6： Azure 上的 SAP 應用程式，使用 IBM Db2 for Linux、UNIX 和 Windows-其他資訊 |
| [1612105] | DB6： Db2 與 HADR 的常見問題 |


| 文件 | 
| --- |
| [Sap 社區 Wiki](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes)：擁有適用于 Linux 的所有必要 SAP 附注 |
| [適用于 SAP On Linux 的 Azure 虛擬機器規劃和執行][planning-guide] 指南 |
| [適用于 SAP On Linux 的 Azure 虛擬機器部署][deployment-guide] (本文)  |
| [適用于 SAP On Linux 的 Azure 虛擬機器資料庫管理系統 (DBMS) 部署][dbms-guide] 指南 |
| [Azure 上 SAP 工作負載的規劃和部署檢查清單][azr-sap-plancheck] |
| [SAP Applications 12 SP4 最佳作法指南的 SUSE Linux Enterprise Server][sles-for-sap-bp] |
| [SUSE Linux Enterprise 高可用性擴充功能 12 SP4][sles-ha-guide] |
| [適用於 SAP 工作負載的 IBM DB2 Azure 虛擬機器 DBMS 部署][dbms-db2] |
| [IBM Db2 HADR 11。1][db2-hadr-11.1] |
| [IBM Db2 HADR R 10。5][db2-hadr-10.5] |

## <a name="overview"></a>概觀
為了達到高可用性，使用 HADR 的 IBM Db2 LUW 會安裝在至少兩部 Azure 虛擬機器，這些虛擬機器部署在 [azure 可用性設定組](../../windows/tutorial-availability-sets.md) 或 [Azure 可用性區域](./sap-ha-availability-zones.md)之間。 

下列圖形顯示兩部資料庫伺服器 Azure Vm 的設定。 這兩個資料庫伺服器 Azure Vm 都有自己的儲存空間，且已啟動且正在執行。 在 HADR 中，其中一個 Azure Vm 中的一個資料庫實例具有主要實例的角色。 所有用戶端都已連接到這個主要實例。 資料庫交易中的所有變更都會保存在本機的 Db2 交易記錄檔中。 當交易記錄檔記錄保存在本機時，會透過 TCP/IP 將記錄傳送至第二部資料庫伺服器、待命伺服器或待命實例上的資料庫實例。 待命實例會藉由向前復原傳送的交易記錄檔記錄，來更新本機資料庫。 如此一來，待命伺服器會與主伺服器保持同步。

HADR 只是複寫功能。 它沒有失敗偵測和自動接管或容錯移轉功能。 接管或傳送至待命伺服器必須由資料庫管理員手動起始。 若要完成自動接管和失敗偵測，您可以使用 Linux Pacemaker 叢集功能。 Pacemaker 會監視兩個資料庫伺服器實例。 當主資料庫伺服器實例當機時，Pacemaker 會起始待命伺服器的 *自動* HADR 接管。 Pacemaker 也可確保將虛擬 IP 位址指派給新的主伺服器。

![IBM Db2 高可用性總覽](./media/dbms-guide-ha-ibm/ha-db2-hadr-lb.png)

若要讓 SAP 應用程式伺服器連線到主資料庫，您需要虛擬主機名稱和虛擬 IP 位址。 如果發生容錯移轉，SAP 應用程式伺服器會連接到新的主資料庫實例。 在 Azure 環境中， [azure 負載平衡器](https://microsoft.sharepoint.com/teams/WAG/AzureNetworking/Wiki/Load%20Balancing.aspx) 需要使用虛擬 IP 位址，如 IBM Db2 HADR 所需的方式。 

下圖提供以 IBM Db2 資料庫為基礎之 SAP 系統的高可用性設定，以協助您完全瞭解 IBM Db2 LUW 與 HADR 和 Pacemaker 如何融入高可用性 SAP 系統設定。 本文僅涵蓋 IBM Db2，但它會提供有關如何設定 SAP 系統之其他元件的其他文章參考。

![IBM DB2 高可用性完整環境總覽](.//media/dbms-guide-ha-ibm/end-2-end-ha.png)


### <a name="high-level-overview-of-the-required-steps"></a>必要步驟的高階總覽
若要部署 IBM Db2 設定，您必須遵循下列步驟：

  + 規劃您的環境。
  + 部署 VM。
  + 更新 SUSE Linux 並設定檔案系統。
  + 安裝和設定 Pacemaker。
  + 安裝 [高可用性 NFS][nfs-ha]。
  + [在不同的叢集上安裝 ASCS/ERS][ascs-ha]。
  + 使用分散式/高可用性選項安裝 IBM Db2 資料庫 (SWPM) 。
  + 安裝並建立次要資料庫節點和實例，並設定 HADR。
  + 確認 HADR 正在運作。
  + 套用 Pacemaker 設定以控制 IBM Db2。
  + 設定 Azure Load Balancer。
  + 安裝主要和對話應用程式伺服器。
  + 檢查並調整 SAP 應用程式伺服器的設定。
  + 執行容錯移轉和接管測試。



## <a name="plan-azure-infrastructure-for-hosting-ibm-db2-luw-with-hadr"></a>使用 HADR 規劃託管 IBM Db2 LUW 的 Azure 基礎結構

執行部署之前，請先完成規劃程式。 規劃建立在 Azure 中部署 Db2 與 HADR 設定的基礎。 下表列出了 SAP 環境)  (資料庫的規劃 IMB Db2 LUW 的重要元素：

| 主題 | 簡短描述 |
| --- | --- |
| 定義 Azure 資源群組 | 您部署 VM、VNet、Azure Load Balancer 和其他資源的資源群組。 可以是現有的或新的。 |
| 虛擬網路/子網定義 | 其中部署 IBM Db2 和 Azure Load Balancer 的 Vm。 可以是現有或新建立的。 |
| 裝載 IBM Db2 LUW 的虛擬機器 | VM 大小、儲存體、網路、IP 位址。 |
| IBM Db2 資料庫的虛擬主機名稱和虛擬 IP| 用於連接 SAP 應用程式伺服器的虛擬 IP 或主機名稱。 **virt-hostname**， **db-virt-ip**。 |
| Azure 隔離 | Azure 隔離或 SBD 防護 (強烈建議的) 。 避免分裂腦情況的方法。 |
| SBD VM | SBD 虛擬機器大小、儲存體、網路。 |
| Azure Load Balancer | 使用基本或標準 (建議的) 、適用于 Db2 資料庫的探查埠 (我們的建議 62500) **探查埠**。 |
| 名稱解析| 名稱解析在環境中的運作方式。 強烈建議使用 DNS 服務。 您可以使用本機主機檔案。 |
    
如需 Azure 中 Linux Pacemaker 的詳細資訊，請參閱在 [azure 中 SUSE Linux Enterprise Server 設定 Pacemaker](./high-availability-guide-suse-pacemaker.md)。

## <a name="deployment-on-suse-linux"></a>SUSE Linux 上的部署

IBM Db2 LUW 的資源代理套裝程式含于 SAP 應用程式的 SUSE Linux Enterprise Server 中。 針對本檔中所述的設定，您必須使用適用于 SAP 應用程式的 SUSE Linux Server。 Azure Marketplace 包含 SUSE Enterprise Server for SAP Applications 12 的影像，您可用來部署新的 Azure 虛擬機器。 當您在 Azure VM Marketplace 中選擇 VM 映射時，請留意 SUSE 透過 Azure Marketplace 提供的各種支援或服務模型。 

### <a name="hosts-dns-updates"></a>主機： DNS 更新
建立所有主機名稱的清單，包括虛擬主機名稱，以及更新您的 DNS 伺服器，以啟用主機名稱解析的適當 IP 位址。 如果 DNS 伺服器不存在，或您無法更新和建立 DNS 專案，您必須使用參與此案例之個別 Vm 的本機主機檔案。 如果您使用的是主機檔案專案，請確定已將這些專案套用至 SAP 系統內容中的所有 Vm。 不過，我們建議您使用您的 DNS，在理想的情況下，會將它延伸至 Azure


### <a name="manual-deployment"></a>手動部署

請確定 ibm/SAP for IBM Db2 LUW 支援所選的 OS。 SAP note [1928533]中提供適用于 Azure Vm 和 Db2 版本的支援作業系統版本清單。 SAP 產品可用性對照表中提供個別 Db2 版本的 OS 版本清單。 因為此版或更新版本的 SUSE Linux 版本中有 Azure 相關的效能改進，所以強烈建議至少使用 SLES 12 SP4。

1. 建立或選取資源群組。
1. 建立或選取虛擬網路和子網。
1. 建立 Azure 可用性設定組或部署可用性區域。
    + 針對可用性設定組，將更新網域上限設定為2。
1. 建立虛擬機器1。
    + 在 Azure Marketplace 中使用 SLES for SAP 映射。
    + 選取您在步驟3中建立的 Azure 可用性設定組，或選取 [可用性區域]。
1.  建立虛擬機器2。
    + 在 Azure Marketplace 中使用 SLES for SAP 映射。
    + 選取您在步驟3中建立的 Azure 可用性設定組，或選取 [可用性區域] (與步驟 3) 中的區域不同。
1. 將資料磁片新增至 Vm，然後在 [IBM Db2 Azure 虛擬機器 DBMS 部署適用于 SAP 工作負載][dbms-db2]的文章中，查看檔案系統設定的建議。

## <a name="create-the-pacemaker-cluster"></a>建立 Pacemaker 叢集
    
若要為此 IBM Db2 伺服器建立基本的 Pacemaker 叢集，請參閱 [在 Azure 中 SUSE Linux Enterprise Server 上設定 Pacemaker][sles-pacemaker]。 

## <a name="install-the-ibm-db2-luw-and-sap-environment"></a>安裝 IBM Db2 LUW 和 SAP 環境

開始安裝以 IBM Db2 LUW 為基礎的 SAP 環境之前，請先參閱下列檔：

+ Azure 文件
+ SAP 檔
+ IBM 檔

本文的簡介章節將提供此檔的連結。

請參閱 SAP 安裝手冊，以瞭解如何在 IBM Db2 LUW 上安裝 NetWeaver 型應用程式。

您可以使用 [Sap 安裝指南搜尋工具][sap-instfind]，在 sap 說明入口網站上找到指南。

您可以藉由設定下列篩選來減少入口網站中顯示的指南數目：

- 我想要：「安裝新系統」
- 我的資料庫：「IBM Db2 for Linux，Unix，and Windows」
- 適用于 SAP NetWeaver 版本、堆疊設定或作業系統的其他篩選器

### <a name="installation-hints-for-setting-up-ibm-db2-luw-with-hadr"></a>使用 HADR 設定 IBM Db2 LUW 的安裝提示

若要設定主要 IBM Db2 LUW 資料庫實例：

- 使用高可用性或分散式選項。
- 安裝 SAP ASCS/ERS 和資料庫實例。
- 製作新安裝之資料庫的備份。


> [!IMPORTANT] 
> 寫下在安裝期間設定的「資料庫通訊連接埠」。 這兩個資料庫實例的埠號碼必須相同

若要使用 SAP 同質系統複製程式來設定待命資料庫伺服器，請執行下列步驟：

1. 選取 [**系統複製**] 選項 > [**目標系統**  >  **分散式**  >  **資料庫實例**]。
1. 若為複製方法，請選取 [ **同質系統** ]，讓您可以使用備份來還原待命伺服器實例上的備份。
1. 當您到達結束步驟來還原適用于同質系統複製的資料庫時，請結束安裝程式。 從主要主機的備份還原資料庫。 所有後續的安裝階段都已在主資料庫伺服器上執行。
1. 設定適用于 IBM Db2 的 HADR。

   > [!NOTE]
   > 針對 Azure 和 Pacemaker 專屬的安裝和設定：透過 SAP 軟體布建管理員進行安裝程式期間，會有關于 IBM Db2 LUW 高可用性的明確問題：
   >+ 請勿選取 [ **IBM Db2 pureScale**]。
   >+ 請勿選取 [ **安裝 IBM Tivoli System Automation For Multiplatforms**]。
   >+ 請勿選取 [ **產生叢集設定檔**]。

   當您使用適用于 Linux Pacemaker 的 SBD 裝置時，請設定下列 Db2 HADR 參數：
   + HADR 對等視窗持續時間 (秒)  (HADR_PEER_WINDOW) = 300  
   + HADR timeout 值 (HADR_TIMEOUT) = 60

   當您使用 Azure Pacemaker 隔離代理程式時，請設定下列參數：
   + HADR 對等視窗持續時間 (秒)  (HADR_PEER_WINDOW) = 900  
   + HADR timeout 值 (HADR_TIMEOUT) = 60

建議您根據初始的容錯移轉/接管測試來採用上述參數。 您必須使用這些參數設定來測試容錯移轉和接管的適當功能。 由於個別設定可能不同，因此參數可能需要調整。 

> [!IMPORTANT]
> 使用具有正常啟動之 HADR 設定的 IBM Db2 專用：次要或待命資料庫實例必須已啟動且正在執行，您才能啟動主資料庫實例。

為了方便示範，以及本文中所述的程式，資料庫 SID 是 **PTR**。

#### <a name="ibm-db2-hadr-check"></a>IBM Db2 HADR 檢查
在您設定 HADR 且狀態為「對等」且已連線到主要和待命節點之後，請執行下列檢查：

<pre><code>
Execute command as db2&lt;sid&gt; db2pd -hadr -db &lt;SID&gt;

#Primary output:
# Database Member 0 -- Database PTR -- Active -- Up 1 days 01:51:38 -- Date 2019-02-06-15.35.28.505451
# 
#                             <b>HADR_ROLE = PRIMARY
#                           REPLAY_TYPE = PHYSICAL
#                         HADR_SYNCMODE = NEARSYNC
#                            STANDBY_ID = 1
#                         LOG_STREAM_ID = 0
#                            HADR_STATE = PEER
#                            HADR_FLAGS = TCP_PROTOCOL
#                   PRIMARY_MEMBER_HOST = azibmdb02
#                      PRIMARY_INSTANCE = db2ptr
#                        PRIMARY_MEMBER = 0
#                   STANDBY_MEMBER_HOST = azibmdb01
#                      STANDBY_INSTANCE = db2ptr
#                        STANDBY_MEMBER = 0
#                   HADR_CONNECT_STATUS = CONNECTED</b>
#              HADR_CONNECT_STATUS_TIME = 02/05/2019 13:51:47.170561 (1549374707)
#           HEARTBEAT_INTERVAL(seconds) = 15
#                      HEARTBEAT_MISSED = 0
#                    HEARTBEAT_EXPECTED = 6137
#                 HADR_TIMEOUT(seconds) = 60
#         TIME_SINCE_LAST_RECV(seconds) = 13
#              PEER_WAIT_LIMIT(seconds) = 0
#            LOG_HADR_WAIT_CUR(seconds) = 0.000
#     LOG_HADR_WAIT_RECENT_AVG(seconds) = 0.000025
#    LOG_HADR_WAIT_ACCUMULATED(seconds) = 434.595
#                   LOG_HADR_WAIT_COUNT = 223713
# SOCK_SEND_BUF_REQUESTED,ACTUAL(bytes) = 0, 46080
# SOCK_RECV_BUF_REQUESTED,ACTUAL(bytes) = 0, 374400
#             PRIMARY_LOG_FILE,PAGE,POS = S0000280.LOG, 15571, 27902548040
#             STANDBY_LOG_FILE,PAGE,POS = S0000280.LOG, 15571, 27902548040
#                   HADR_LOG_GAP(bytes) = 0
#      STANDBY_REPLAY_LOG_FILE,PAGE,POS = S0000280.LOG, 15571, 27902548040
#        STANDBY_RECV_REPLAY_GAP(bytes) = 0
#                      PRIMARY_LOG_TIME = 02/06/2019 15:34:39.000000 (1549467279)
#                      STANDBY_LOG_TIME = 02/06/2019 15:34:39.000000 (1549467279)
#               STANDBY_REPLAY_LOG_TIME = 02/06/2019 15:34:39.000000 (1549467279)
#          STANDBY_RECV_BUF_SIZE(pages) = 2048
#              STANDBY_RECV_BUF_PERCENT = 0
#            STANDBY_SPOOL_LIMIT(pages) = 0
#                 STANDBY_SPOOL_PERCENT = NULL
#                    STANDBY_ERROR_TIME = NULL
#                  PEER_WINDOW(seconds) = 300
#                       PEER_WINDOW_END = 02/06/2019 15:40:25.000000 (1549467625)
#              READS_ON_STANDBY_ENABLED = N

#Secondary output:
# Database Member 0 -- Database PTR -- Standby -- Up 1 days 01:46:43 -- Date 2019-02-06-15.38.25.644168
# 
#                             <b>HADR_ROLE = STANDBY
#                           REPLAY_TYPE = PHYSICAL
#                         HADR_SYNCMODE = NEARSYNC
#                            STANDBY_ID = 0
#                         LOG_STREAM_ID = 0
#                            HADR_STATE = PEER
#                            HADR_FLAGS = TCP_PROTOCOL
#                   PRIMARY_MEMBER_HOST = azibmdb02
#                      PRIMARY_INSTANCE = db2ptr
#                        PRIMARY_MEMBER = 0
#                   STANDBY_MEMBER_HOST = azibmdb01
#                      STANDBY_INSTANCE = db2ptr
#                        STANDBY_MEMBER = 0
#                   HADR_CONNECT_STATUS = CONNECTED</b>
#              HADR_CONNECT_STATUS_TIME = 02/05/2019 13:51:47.205067 (1549374707)
#           HEARTBEAT_INTERVAL(seconds) = 15
#                      HEARTBEAT_MISSED = 0
#                    HEARTBEAT_EXPECTED = 6186
#                 HADR_TIMEOUT(seconds) = 60
#         TIME_SINCE_LAST_RECV(seconds) = 5
#              PEER_WAIT_LIMIT(seconds) = 0
#            LOG_HADR_WAIT_CUR(seconds) = 0.000
#     LOG_HADR_WAIT_RECENT_AVG(seconds) = 0.000023
#    LOG_HADR_WAIT_ACCUMULATED(seconds) = 434.595
#                   LOG_HADR_WAIT_COUNT = 223725
# SOCK_SEND_BUF_REQUESTED,ACTUAL(bytes) = 0, 46080
# SOCK_RECV_BUF_REQUESTED,ACTUAL(bytes) = 0, 372480
#             PRIMARY_LOG_FILE,PAGE,POS = S0000280.LOG, 15574, 27902562173
#             STANDBY_LOG_FILE,PAGE,POS = S0000280.LOG, 15574, 27902562173
#                   HADR_LOG_GAP(bytes) = 0
#      STANDBY_REPLAY_LOG_FILE,PAGE,POS = S0000280.LOG, 15574, 27902562173
#        STANDBY_RECV_REPLAY_GAP(bytes) = 155
#                      PRIMARY_LOG_TIME = 02/06/2019 15:37:34.000000 (1549467454)
#                      STANDBY_LOG_TIME = 02/06/2019 15:37:34.000000 (1549467454)
#               STANDBY_REPLAY_LOG_TIME = 02/06/2019 15:37:34.000000 (1549467454)
#          STANDBY_RECV_BUF_SIZE(pages) = 2048
#              STANDBY_RECV_BUF_PERCENT = 0
#            STANDBY_SPOOL_LIMIT(pages) = 0
#                 STANDBY_SPOOL_PERCENT = NULL
#                    STANDBY_ERROR_TIME = NULL
#                  PEER_WINDOW(seconds) = 300
#                       PEER_WINDOW_END = 02/06/2019 15:43:19.000000 (1549467799)
#              READS_ON_STANDBY_ENABLED = N
</code></pre>



## <a name="db2-pacemaker-configuration"></a>Db2 Pacemaker 設定

當您在發生節點失敗時使用 Pacemaker 進行自動容錯移轉時，您需要設定您的 Db2 實例並據以 Pacemaker。 本節說明這種類型的設定。

下列專案前面會加上下列其中一項：

- **[A]**：適用于所有節點
- **[1]**：僅適用于節點1 
- **[2]**：僅適用于節點2

**[A] Pacemaker 設定** 的必要條件：
1. 使用 db2stop 將兩部資料庫伺服器與使用者 db2 一起關閉 \<sid> 。
1. 將 db2 使用者的 shell 環境變更 \<sid> 為 */bin/ksh*。 建議您使用 Yast 工具。 


### <a name="pacemaker-configuration"></a>Pacemaker 設定

> [!IMPORTANT]
> 最近測試顯示 netcat 會因待處理項目及其僅處理單一連線的限制，而停止回應要求的狀況。 Netcat 資源會停止接聽 Azure Load Balancer 要求，使浮動 IP 無法使用。  
> 針對現有的 Pacemaker 叢集，我們在過去建議將 netcat 取代成 socat。 目前建議使用 azure-lb 資源代理程式，其為 resource-agents 套件的一部分，並包含下列套件版本需求：
> - 針對 SLES 12 SP4/SP5，版本必須至少為 resource-agents-4.3.018.a7fb5035-3.30.1。  
> - 針對 SLES 15/15 SP1，版本必須至少為 resource-agents-4.3.0184.6ee15eb2-4.13.1。  
>
> 請注意，變更將會需要短暫的停機。  
> 針對現有的 Pacemaker 叢集，若設定已按照 [Azure Load Balancer 偵測強化](https://www.suse.com/support/kb/doc/?id=7024128)中的描述變更為使用 socat，則沒有立即切換至 azure-lb 資源代理程式的需求。

**[1]** IBM Db2 HADR 特定的 Pacemaker 設定：
<pre><code># Put Pacemaker into maintenance mode
sudo crm configure property maintenance-mode=true
</code></pre>

**[1]** 建立 IBM Db2 資源：
<pre><code># Replace **bold strings** with your instance name db2sid, database SID, and virtual IP address/Azure Load Balancer.

sudo crm configure primitive rsc_Db2_db2ptr_<b>PTR</b> db2 \
        params instance="<b>db2ptr</b>" dblist="<b>PTR</b>" \
        op start interval="0" timeout="130" \
        op stop interval="0" timeout="120" \
        op promote interval="0" timeout="120" \
        op demote interval="0" timeout="120" \
        op monitor interval="30" timeout="60" \
        op monitor interval="31" role="Master" timeout="60"

# Configure virtual IP - same as Azure Load Balancer IP
sudo crm configure primitive rsc_ip_db2ptr_<b>PTR</b> IPaddr2 \
        op monitor interval="10s" timeout="20s" \
        params ip="<b>10.100.0.10</b>"

# Configure probe port for Azure load Balancer
sudo crm configure primitive rsc_nc_db2ptr_<b>PTR</b> azure-lb port=<b>62500</b>

sudo crm configure group g_ip_db2ptr_<b>PTR</b> rsc_ip_db2ptr_<b>PTR</b> rsc_nc_db2ptr_<b>PTR</b>

sudo crm configure ms msl_Db2_db2ptr_<b>PTR</b> rsc_Db2_db2ptr_<b>PTR</b> \
        meta target-role="Started" notify="true"

sudo crm configure colocation col_db2_db2ptr_<b>PTR</b> inf: g_ip_db2ptr_<b>PTR</b>:Started msl_Db2_db2ptr_<b>PTR</b>:Master

sudo crm configure order ord_db2_ip_db2ptr_<b>PTR</b> inf: msl_Db2_db2ptr_<b>PTR</b>:promote g_ip_db2ptr_<b>PTR</b>:start

sudo crm configure rsc_defaults resource-stickiness=1000
sudo crm configure rsc_defaults migration-threshold=5000
</code></pre>

**[1]** 啟動 IBM Db2 資源：
* 將 Pacemaker 移出維護模式。
<pre><code># Put Pacemaker out of maintenance-mode - that start IBM Db2
sudo crm configure property maintenance-mode=false</pre></code>

**[1]** 請確定叢集狀態為 [確定]，而且所有資源皆已啟動。 資源執行所在的節點並不重要。
<pre><code>sudo crm status</code>

# <a name="2-nodes-configured"></a>已設定2個節點
# <a name="5-resources-configured"></a>已設定5個資源

# <a name="online--azibmdb01-azibmdb02-"></a>線上： [azibmdb01 azibmdb02]

# <a name="full-list-of-resources"></a>資源的完整清單：

#  <a name="stonith-sbd----stonithexternalsbd-started-azibmdb02"></a>stonith-sbd (stonith： external/sbd) ：已啟動 azibmdb02
#  <a name="resource-group-g_ip_db2ptr_ptr"></a>資源群組： g_ip_db2ptr_PTR
#      <a name="rsc_ip_db2ptr_ptr--ocfheartbeatipaddr2-------started-azibmdb02"></a>rsc_ip_db2ptr_PTR (ocf：：心跳： IPaddr2) ：已開始 azibmdb02
#      <a name="rsc_nc_db2ptr_ptr--ocfheartbeatazure-lb------started-azibmdb02"></a>rsc_nc_db2ptr_PTR (ocf：：心跳： azure-lb) ：已啟動 azibmdb02
#  <a name="masterslave-set-msl_db2_db2ptr_ptr-rsc_db2_db2ptr_ptr"></a>主要/從屬集： msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
#      <a name="masters--azibmdb02-"></a>Master： [azibmdb02]
#      <a name="slaves--azibmdb01-"></a>從屬： [azibmdb01]
</pre>

> [!IMPORTANT]
> 您必須使用 Pacemaker 工具來管理 Pacemaker 叢集 Db2 實例。 如果您使用 db2 命令（例如 db2stop），Pacemaker 會將動作偵測為資源失敗。 如果您正在執行維護，您可以讓節點或資源進入維護模式。 Pacemaker 會暫停監視資源，然後您就可以使用一般的 db2 系統管理命令。


### <a name="configure-azure-load-balancer"></a>設定 Azure 負載平衡器
若要設定 Azure Load Balancer，建議您使用 [Azure STANDARD LOAD BALANCER SKU](../../../load-balancer/load-balancer-overview.md) ，然後執行下列動作：

> [!NOTE]
> Standard Load Balancer SKU 具有從 Load Balancer 下的節點存取公用 IP 位址的限制。 在 [SAP 高可用性案例中使用 Azure Standard Load Balancer 之虛擬機器的公開端點](./high-availability-guide-standard-load-balancer-outbound-connections.md) 連線檔，說明如何啟用這些節點來存取公用 IP 位址的方法

> [!IMPORTANT]
> 負載平衡案例中的 NIC 次要 IP 設定不支援浮動 IP。 如需詳細資訊，請參閱 [Azure 負載平衡器的限制](https://docs.microsoft.com/azure/load-balancer/load-balancer-multivip-overview#limitations)。 如果您需要 VM 的其他 IP 位址，請部署第二個 NIC。  

1. 建立前端 IP 集區：

   a. 在 [Azure 入口網站中，開啟 [Azure Load Balancer]，選取 [ **前端 IP 集**區]，然後選取 [ **新增**]。

   b. 輸入新前端 IP 集區的名稱 (例如，[ **Db2 連接**) 。

   c. 將 **指派** 設定為 [ **靜態**]，然後輸入在一開始定義的 IP 位址 **虛擬 ip** 。

   d. 選取 [確定]。

   e. 建立新的前端 IP 集區之後，請記下集區 IP 位址。

1. 建立後端集區：

   a. 在 [Azure 入口網站中，開啟 [Azure Load Balancer]，選取 [ **後端**集區]，然後選取 [ **新增**]。

   b. 輸入新後端集區的名稱 (例如， **Db2 後** 端) 。

   c. 選取 [新增虛擬機器]。

   d. 選取在上一個步驟中建立的可用性設定組或裝載 IBM Db2 資料庫的虛擬機器。

   e. 選取 IBM Db2 叢集的虛擬機器。

   f. 選取 [確定]  。

1. 建立健康情況探查：

   a. 在 Azure 入口網站中，開啟 [Azure Load Balancer]，選取 [ **健康情況探查**]，然後選取 [ **新增**]。

   b. 輸入新健康情況探查的名稱 (例如， **Db2-hp**) 。

   c. 選取 [ **TCP** ] 作為通訊協定和埠 **62500**。 將 [ **間隔** ] 值保持設定為 [ **5**]，並將 [ **狀況不良臨界** 值] 設定為 [ **2**]。

   d. 選取 [確定]。

1. 建立負載平衡規則：

   a. 在 [Azure 入口網站中，開啟 [Azure Load Balancer]，選取 [ **負載平衡規則**]，然後選取 [ **新增**]。

   b. 輸入新的 Load Balancer 規則的名稱 (例如， **DB2 SID**) 。

   c. 選取前端 IP 位址、後端集區，以及您稍早建立的健康情況探查 (例如， **Db2-前端**) 。

   d. 將 **通訊協定** 設定為 **TCP**，並輸入埠 *資料庫通訊連接埠*。

   e. 將 [閒置逾時] 增加為 30 分鐘。

   f. 務必**啟用浮動 IP**。

   g. 選取 [確定]。


### <a name="make-changes-to-sap-profiles-to-use-virtual-ip-for-connection"></a>對 SAP 設定檔進行變更，以使用虛擬 IP 進行連接
若要連線到 HADR 設定的主要實例，SAP 應用層必須使用您為 Azure Load Balancer 定義和設定的虛擬 IP 位址。 需要進行下列變更：

/sapmnt/ \<SID> /profile/DEFAULT。DEFAULT.PFL
<pre><code>SAPDBHOST = db-virt-hostname
j2ee/dbhost = db-virt-hostname
</code></pre>

/sapmnt/ \<SID> /global/db6/db2cli.ini
<pre><code>Hostname=db-virt-hostname
</code></pre>



## <a name="install-primary-and-dialog-application-servers"></a>安裝主要和對話應用程式伺服器

當您針對 Db2 HADR 設定安裝主要和對話應用程式伺服器時，請使用您為設定所挑選的虛擬主機名稱。 

如果您在建立 Db2 HADR 設定之前執行了安裝，請依照上一節所述的方式進行變更，如下所示： SAP JAVA 堆疊。

### <a name="abapjava-or-java-stack-systems-jdbc-url-check"></a>ABAP + JAVA 或 JAVA stack 系統 JDBC URL 檢查

使用 J2EE 設定工具來檢查或更新 JDBC URL。 因為 J2EE 設定工具是圖形化工具，所以您必須安裝 X 伺服器：
 
1. 登入 J2EE 實例的主要應用程式伺服器，然後執行：   `sudo /usr/sap/*SID*/*Instance*/j2ee/configtool/configtool.sh`
1. 在左側框架中，選擇 [ **安全性存放區**]。
1. 在右邊的框架中，選擇關鍵 jdbc/pool/ \<SAPSID> /url。
1. 將 JDBC URL 中的主機名稱變更為虛擬主機名稱。
     `jdbc:db2://db-virt-hostname:5912/TSP:deferPrepares=0`
1. 選取 [新增]  。
1. 若要儲存變更，請選取左上方的磁片圖示。
1. 關閉設定工具。
1. 重新開機 JAVA 實例。

## <a name="configure-log-archiving-for-hadr-setup"></a>設定 HADR 設定的記錄檔封存
若要設定 HADR 設定的 Db2 記錄檔封存，建議您同時設定主要和待命資料庫，使其具有來自所有記錄檔封存位置的自動記錄檔抓取功能。 主要和待命資料庫都必須能夠從所有記錄檔封存位置取出記錄檔封存檔案，其中一個資料庫實例可能會封存記錄檔。 

只有主資料庫會執行記錄檔封存。 如果您變更資料庫伺服器的 HADR 角色，或發生失敗，則新的主資料庫會負責記錄檔封存。 如果您已設定多個記錄檔封存位置，您的記錄可能會保存兩次。 如果發生本機或遠端的問題，您可能也必須手動將封存的記錄檔從舊的主伺服器複製到新主伺服器的使用中記錄位置。

建議您設定一般 NFS 共用，其中記錄是從兩個節點寫入。 NFS 共用必須具備高可用性。 

您可以使用現有的高可用性 NFS 共用來進行傳輸或設定檔目錄。 如需詳細資訊，請參閱

- [SUSE Linux Enterprise Server 上 Azure VM 的 NFS 高可用性][nfs-ha] 
- [Azure Vm 上的 SAP NetWeaver 的高可用性，適用于 SAP 應用程式的 Azure NetApp Files SUSE Linux Enterprise Server](./high-availability-guide-suse-netapp-files.md)
- [Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-introduction.md) (建立 NFS 共用) 


## <a name="test-the-cluster-setup"></a>測試叢集設定

本節說明如何測試您的 Db2 HADR 設定。 *每項測試都假設您是以使用者根目錄登入* ，而 IBM Db2 主要複本正在 *azibmdb01* 虛擬機器上執行。

所有測試案例的初始狀態會在此說明： (crm_mon-r 或 crm 狀態) 

- **crm 狀態** 是在執行時間 Pacemaker 狀態的快照集 
- **crm_mon-r** 是 Pacemaker 狀態的連續輸出

<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb02
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Stopped
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Stopped
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     rsc_Db2_db2ptr_PTR      (ocf::heartbeat:db2):   Promoting azibmdb01
     Slaves: [ azibmdb02 ]
</code></pre>

SAP 系統中的原始狀態記載于交易 DBACOCKPIT > Configuration > 總覽，如下圖所示：

![DBACockpit-預先遷移](./media/dbms-guide-ha-ibm/hadr-sap-mgr-org.png)




### <a name="test-takeover-of-ibm-db2"></a>測試接管 IBM Db2


> [!IMPORTANT] 
> 開始測試之前，請確定：
> * Pacemaker 沒有任何失敗的動作 (crm 狀態) 。
> *  (遷移測試的 leftovers 沒有位置限制) 
> * IBM Db2 HADR 同步處理正在運作。 檢查使用者 db2\<sid> <pre><code>db2pd -hadr -db \<DBSID></code></pre>


藉由執行下列命令，遷移執行主要 Db2 資料庫的節點：
<pre><code>crm resource migrate msl_<b>Db2_db2ptr_PTR</b> azibmdb02</code></pre>

完成遷移之後，crm 狀態輸出看起來會像這樣：
<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb02
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb02
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Started azibmdb02
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb02 ]
     Slaves: [ azibmdb01 ]
</code></pre>

SAP 系統中的原始狀態記載于交易 DBACOCKPIT > Configuration > 總覽，如下圖所示：

![DBACockpit-遷移後](./media/dbms-guide-ha-ibm/hadr-sap-mgr-post.png)

使用「crm 資源遷移」的資源遷移會建立位置條件約束。 應刪除位置條件約束。 如果未刪除位置條件約束，則資源無法容錯回復，否則您可能會遇到不必要的接管。 

將資源遷移回 *azibmdb01* ，並清除位置條件約束
<pre><code>crm resource migrate msl_<b>Db2_db2ptr_PTR</b> azibmdb01
crm resource clear msl_<b>Db2_db2ptr_PTR</b>
</code></pre>

- **crm 資源遷移 \<res_name> \<host> ：** 建立位置條件約束，並可能造成接管問題
- **crm 資源清除 \<res_name> **：清除位置條件約束
- **crm 資源清除 \<res_name> **：清除資源的所有錯誤

### <a name="test-the-fencing-agent"></a>測試隔離代理程式

在此情況下，我們會測試 SBD 隔離，我們建議您在使用 SUSE Linux 時執行此作業。

<pre><code>
azibmdb01:~ # ps -ef|grep sbd
root       2374      1  0 Feb05 ?        00:00:17 sbd: inquisitor
root       2378   2374  0 Feb05 ?        00:00:40 sbd: watcher: /dev/disk/by-id/scsi-36001405fbbaab35ee77412dacb77ae36 - slot: 0 - uuid: 27cad13a-0bce-4115-891f-43b22cfabe65
root       2379   2374  0 Feb05 ?        00:01:51 sbd: watcher: Pacemaker
root       2380   2374  0 Feb05 ?        00:00:18 sbd: watcher: Cluster

azibmdb01:~ # kill -9 2374
</code></pre>

叢集節點 *azibmdb01* 應該重新開機。 IBM Db2 主要 HADR 角色即將移至 *azibmdb02*。 當 *azibmdb01* 恢復連線時，Db2 實例將會以次要資料庫實例的角色來移動。 

如果 Pacemaker 服務未在重新開機的先前主要複本上自動啟動，請務必以手動方式啟動它：

<code><pre>sudo service pacemaker start</code></pre>

### <a name="test-a-manual-takeover"></a>測試手動接管

您可以藉由停止 *azibmdb01* 節點上的 Pacemaker 服務，來測試手動接管：
<pre><code>service pacemaker stop</code></pre>

*azibmdb02*上的狀態
<pre><code>
2 nodes configured
5 resources configured

Online: [ azibmdb02 ]
OFFLINE: [ azibmdb01 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb02
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb02
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Started azibmdb02
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb02 ]
     Stopped: [ azibmdb01 ]
</code></pre>

在容錯移轉之後，您可以在 *azibmdb01*上再次啟動該服務。
<pre><code>service pacemaker start</code></pre>


### <a name="kill-the-db2-process-on-the-node-that-runs-the-hadr-primary-database"></a>終止執行 HADR 主資料庫之節點上的 Db2 進程

<pre><code>#Kill main db2 process - db2sysc
azibmdb01:~ # ps -ef|grep db2s
db2ptr    34598  34596  8 14:21 ?        00:00:07 db2sysc 0

azibmdb01:~ # kill -9 34598
</code></pre>

Db2 實例即將失敗，Pacemaker 會報告下列狀態：

<pre><code>
2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

 stonith-sbd    (stonith:external/sbd): Started azibmdb01
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Stopped
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Stopped
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Slaves: [ azibmdb02 ]
     Stopped: [ azibmdb01 ]

Failed Actions:
* rsc_Db2_db2ptr_PTR_demote_0 on azibmdb01 'unknown error' (1): call=157, status=complete, exitreason='',
    last-rc-change='Tue Feb 12 14:28:19 2019', queued=40ms, exec=223ms

</code></pre>

Pacemaker 會在相同的節點上重新開機 Db2 主資料庫實例，或將它容錯移轉到執行次要資料庫實例的節點，並回報錯誤。

<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

 stonith-sbd    (stonith:external/sbd): Started azibmdb01
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb01
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Started azibmdb01
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb01 ]
     Slaves: [ azibmdb02 ]

Failed Actions:
* rsc_Db2_db2ptr_PTR_demote_0 on azibmdb01 'unknown error' (1): call=157, status=complete, exitreason='',
    last-rc-change='Tue Feb 12 14:28:19 2019', queued=40ms, exec=223ms
</code></pre>


### <a name="kill-the-db2-process-on-the-node-that-runs-the-secondary-database-instance"></a>在執行次要資料庫實例的節點上終止 Db2 進程

<pre><code>azibmdb02:~ # ps -ef|grep db2s
db2ptr    65250  65248  0 Feb11 ?        00:09:27 db2sysc 0

azibmdb02:~ # kill -9</code></pre>

節點進入失敗的陳述和回報的錯誤
<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

 stonith-sbd    (stonith:external/sbd): Started azibmdb01
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb01
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Started azibmdb01
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     rsc_Db2_db2ptr_PTR      (ocf::heartbeat:db2):   FAILED azibmdb02
     Masters: [ azibmdb01 ]

Failed Actions:
* rsc_Db2_db2ptr_PTR_monitor_30000 on azibmdb02 'not running' (7): call=144, status=complete, exitreason='',
last-rc-change='Tue Feb 12 14:36:59 2019', queued=0ms, exec=0ms</code></pre>

Db2 實例會以先前指派的次要角色重新開機。

<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb01
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb01
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Started azibmdb01
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb01 ]
     Slaves: [ azibmdb02 ]

Failed Actions:
* rsc_Db2_db2ptr_PTR_monitor_30000 on azibmdb02 'not running' (7): call=144, status=complete, exitreason='',
    last-rc-change='Tue Feb 12 14:36:59 2019', queued=0ms, exec=0ms</code></pre>



### <a name="stop-db-via-db2stop-force-on-the-node-that-runs-the-hadr-primary-database-instance"></a>在執行 HADR 主資料庫實例的節點上，透過 db2stop force 來停止 DB

<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb01
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb01
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Started azibmdb01
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb01 ]
     Slaves: [ azibmdb02 ]</code></pre>

當使用者 db2 \<sid> execute 命令 db2stop force：
<pre><code>azibmdb01:~ # su - db2ptr
azibmdb01:db2ptr> db2stop force</code></pre>

偵測到失敗
<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

 stonith-sbd    (stonith:external/sbd): Started azibmdb01
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Stopped
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Stopped
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     rsc_Db2_db2ptr_PTR      (ocf::heartbeat:db2):   FAILED azibmdb01
     Slaves: [ azibmdb02 ]

Failed Actions:
* rsc_Db2_db2ptr_PTR_demote_0 on azibmdb01 'unknown error' (1): call=201, status=complete, exitreason='',
    last-rc-change='Tue Feb 12 14:45:25 2019', queued=1ms, exec=150ms</code></pre>

Db2 HADR 次要資料庫實例已升級為主要角色
<pre><code> nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb01
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb02
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Started azibmdb02
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb02 ]
     Stopped: [ azibmdb01 ]

Failed Actions:
* rsc_Db2_db2ptr_PTR_start_0 on azibmdb01 'unknown error' (1): call=205, stat
us=complete, exitreason='',
    last-rc-change='Tue Feb 12 14:45:27 2019', queued=0ms, exec=865ms</pre></code>


### <a name="crash-vm-with-restart-on-the-node-that-runs-the-hadr-primary-database-instance"></a>在執行 HADR 主資料庫實例的節點上重新開機時損毀 VM

<pre><code>#Linux kernel panic - with OS restart
azibmdb01:~ # echo b > /proc/sysrq-trigger</code></pre>

Pacemaker 會將次要實例升級為主要實例角色。 當 vm 重新開機之後，舊的主要實例將移至次要角色，且所有服務都已完全還原：

<pre><code> nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb02
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb01
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Started azibmdb01
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb01 ]
     Slaves: [ azibmdb02 ]</code></pre>



### <a name="crash-the-vm-that-runs-the-hadr-primary-database-instance-with-halt"></a>使用「停止」讓執行 HADR 主資料庫實例的 VM 損毀

<pre><code>#Linux kernel panic - halts OS
azibmdb01:~ # echo b > /proc/sysrq-trigger</code></pre>

在這種情況下，Pacemaker 會偵測到執行主資料庫實例的節點沒有回應。

<pre><code>2 nodes configured
5 resources configured

Node azibmdb01: UNCLEAN (online)
Online: [ azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb02
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb01
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Started azibmdb01
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb01 ]
     Slaves: [ azibmdb02 ]</code></pre>

下一步是檢查 *分裂大腦* 的情況。 當存活的節點判定上次執行主資料庫實例的節點關閉之後，就會執行資源的容錯移轉。
<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb02 ]
OFFLINE: [ azibmdb01 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb02
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb02
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Started azibmdb02
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb02 ]
     Stopped: [ azibmdb01 ] </code></pre>


當節點的「停止」時，必須透過 Azure 入口網站、PowerShell 或 Azure CLI) 中的 Azure 管理工具 (重新開機失敗的節點。 當失敗的節點重新上線之後，它會將 Db2 實例啟動至次要角色。

<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb02
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb02
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Started azibmdb02
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb02 ]
     Slaves: [ azibmdb01 ]</code></pre>

## <a name="next-steps"></a>後續步驟
- [SAP NetWeaver 的高可用性架構和案例](./sap-high-availability-architecture-scenarios.md)
- [在 Azure 中 SUSE Linux Enterprise Server 設定 Pacemaker](./high-availability-guide-suse-pacemaker.md)

[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[1984787]:https://launchpad.support.sap.com/#/notes/1984787
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2233094]:https://launchpad.support.sap.com/#/notes/2233094
[1612105]:https://launchpad.support.sap.com/#/notes/1612105

[sles-for-sap-bp]:https://www.suse.com/documentation/sles-for-sap-12/
[db2-hadr-11.1]:https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.admin.ha.doc/doc/c0011267.html
[db2-hadr-10.5]:https://www.ibm.com/support/knowledgecenter/en/SSEPGG_10.5.0/com.ibm.db2.luw.admin.ha.doc/doc/c0011267.html
[dbms-db2]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_ibm
[sles-pacemaker]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker
[sap-instfind]:https://help.sap.com/viewer/9e41ead9f54e44c1ae1a1094b0f80712/ALL/en-US/576f5c1808de4d1abecbd6e503c9ba42.html
[nfs-ha]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs
[sles-ha-guide]:https://www.suse.com/releasenotes/x86_64/SLE-HA/12-SP4/
[ascs-ha]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md
[azr-sap-plancheck]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-deployment-checklist
