---
title: 在 RHEL 上的 Azure 虛擬機器 （VM） 上設置 IBM Db2 HADR |微軟文檔
description: 在 Azure 虛擬機器 （VM） RHEL 上建立 IBM Db2 LUW 的高可用性。
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: SAP
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/13/2020
ms.author: juergent
ms.openlocfilehash: c6a230f6abeab45c56aab2db40b8b1defcc06d90
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77598692"
---
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[2002167]:https://launchpad.support.sap.com/#/notes/2002167
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2233094]:https://launchpad.support.sap.com/#/notes/2233094
[1612105]:https://launchpad.support.sap.com/#/notes/1612105
[2694118]:https://launchpad.support.sap.com/#/notes/2694118


[db2-hadr-11.1]:https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.admin.ha.doc/doc/c0011267.html
[db2-hadr-10.5]:https://www.ibm.com/support/knowledgecenter/en/SSEPGG_10.5.0/com.ibm.db2.luw.admin.ha.doc/doc/c0011267.html
[dbms-db2]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_ibm

[sap-instfind]:https://help.sap.com/viewer/9e41ead9f54e44c1ae1a1094b0f80712/ALL/en-US/576f5c1808de4d1abecbd6e503c9ba42.html
[rhel-ha-addon]:https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_overview/index
[rhel-ha-admin]:https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_administration/index
[rhel-ha-ref]:https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_reference/index
[rhel-azr-supp]:https://access.redhat.com/articles/3131341
[rhel-azr-inst]:https://access.redhat.com/articles/3252491
[rhel-db2-supp]:https://access.redhat.com/articles/3144221
[ascs-ha-rhel]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel
[glusterfs]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-glusterfs
[rhel-pcs-azr]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-pacemaker
[anf-rhel]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md
[azr-sap-plancheck]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-deployment-checklist



# <a name="high-availability-of-ibm-db2-luw-on-azure-vms-on-red-hat-enterprise-linux-server"></a>Red Hat Enterprise Linux Server 上 Azure VM 的 IBM Db2 LUW 高可用性

適用于 Linux、UNIX 和 Windows （LUW） 的高[可用性和災害復原 （HADR） 配置](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_10.5.0/com.ibm.db2.luw.admin.ha.doc/doc/c0011267.html)的 IBM Db2 由運行主資料庫實例的一個節點和至少一個運行次要資料庫實例的節點組成。 對主資料庫實例的更改會同步或非同步複製到次要資料庫實例，具體取決於您的配置。 

本文介紹如何部署和配置 Azure 虛擬機器 （VM）、安裝群集框架以及使用 HADR 配置安裝 IBM Db2 LUW。 

本文不介紹如何使用 HADR 或 SAP 軟體安裝來安裝和配置 IBM Db2 LUW。 為了説明您完成這些任務，我們提供了 SAP 和 IBM 安裝手冊的參考。 本文重點介紹特定于 Azure 環境的部件。 

支援的 IBM Db2 版本是 10.5 及更高版本，如 SAP 注釋[1928533]中所述。

在開始安裝之前，請參閱以下 SAP 說明和文檔：

| SAP 注釋 | 描述 |
| --- | --- |
| [1928533] | Azure 上的 SAP 應用程式：受支援的產品和 Azure VM 類型 |
| [2015553] | Azure 上的 SAP：支援先決條件 |
| [2178632] | Azure 上 SAP 的關鍵監視指標 |
| [2191498] | 使用 Azure 在 Linux 上 SAP：增強的監視 |
| [2243692] | Azure （IaaS） VM 上的 Linux：SAP 許可證問題 |
| [2002167] | Red Hat Enterprise Linux 7.x：安裝和升級 |
| [2694118] | 紅帽企業 Linux HA 附加元件在 Azure 上 |
| [1999351] | 對適用於 SAP 的增強型 Azure 監視功能進行疑難排解 |
| [2233094] | DB6：在 Azure 上使用 IBM Db2 進行 Linux、UNIX 和 Windows 的 SAP 應用程式 - 其他資訊 |
| [1612105] | DB6：DB2 上的常見問題解答，帶 HADR |


| 文件 | 
| --- |
| [SAP社區維琪](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes)：擁有所有必需的 LINUX SAP 說明 |
| [在 Linux 指南上為 SAP 規劃和實現的 Azure 虛擬機器][planning-guide] |
| [在 Linux 上為 SAP 部署的 Azure 虛擬機器][deployment-guide]（本文） |
| [Azure 虛擬機器資料庫管理系統 （DBMS） 在 Linux 指南上為 SAP 部署][dbms-guide] |
| [Azure 上 SAP 工作負載的規劃和部署檢查清單][azr-sap-plancheck] |
| [紅帽企業 Linux 7 的高可用性附加元件概述][rhel-ha-addon] |
| [高可用性附加元件管理][rhel-ha-admin] |
| [高可用性附加元件參考][rhel-ha-ref] |
| [RHEL 高可用性叢集的支援原則：以 Microsoft Azure 虛擬機器作為叢集成員][rhel-azr-supp]
| [在 Microsoft Azure 上安裝和設定 Red Hat Enterprise Linux 7.4 (和更新版本) 高可用性叢集][rhel-azr-inst]
| [IBM Db2 Azure 虛擬機器 DBMS 部署，用於 SAP 工作負荷][dbms-db2] |
| [IBM Db2 HADR 11.1][db2-hadr-11.1] |
| [IBM Db2 HADR 10.5][db2-hadr-10.5] |
| [RHEL 高可用性群集的支援策略 - 群集中 Linux、Unix 和 Windows 的 IBM Db2 管理][rhel-db2-supp]



## <a name="overview"></a>總覽
為了實現高可用性，IBM Db2 LUW 與 HADR 安裝在至少兩個 Azure 虛擬機器上，它們部署在[Azure 可用性集中](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets)或跨[Azure 可用性區域](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-ha-availability-zones)。 

下圖顯示兩個資料庫伺服器 Azure VM 的設置。 兩個資料庫伺服器 Azure VM 都附加了自己的存儲，並且已啟動並運行。 在 HADR 中，其中一個 Azure VM 中的一個資料庫實例具有主實例的角色。 所有用戶端都連接到主實例。 資料庫事務中的所有更改都保存在 Db2 事務日誌中本地。 當事務日誌記錄在本地保留時，記錄通過 TCP/IP 傳輸到第二個資料庫伺服器、待命伺服器或備用實例上的資料庫實例。 備用實例通過向前滾動傳輸的事務日誌記錄來更新本機資料庫。 這樣，待命伺服器與主伺服器保持同步。

HADR 只是一種複製功能。 它沒有故障檢測，也沒有自動接管或容錯移轉設施。 接管或傳輸到待命伺服器必須由資料庫管理員手動啟動。 要實現自動接管和故障檢測，可以使用 Linux 起搏器聚類功能。 起搏器監視兩個資料庫伺服器實例。 當主資料庫伺服器實例崩潰時，Pacemaker 會啟動待命伺服器*的自動*HADR 接管。 起搏器還確保將虛擬 IP 位址分配給新的主伺服器。

![IBM Db2 高可用性概述](./media/high-availability-guide-rhel-ibm-db2-luw/ha-db2-hadr-lb-rhel.png)

要使 SAP 應用程式伺服器連接到主資料庫，您需要一個虛擬主機名稱和一個虛擬 IP 位址。 如果發生容錯移轉，SAP 應用程式伺服器將連接到新的主資料庫實例。 在 Azure 環境中，需要[Azure 負載等化器](https://microsoft.sharepoint.com/teams/WAG/AzureNetworking/Wiki/Load%20Balancing.aspx)以 IBM Db2 的 HADR 所需的方式使用虛擬 IP 位址。 

為了説明您完全理解 IBM Db2 LUW 與 HADR 和起搏器如何適應高可用 SAP 系統設置，下圖概述了基於 IBM Db2 資料庫的 SAP 系統的高度可用設置。 本文僅介紹 IBM Db2，但它提供了有關如何設置 SAP 系統其他元件的其他文章的參考。

![IBM DB2 高可用性全環境概述](./media/high-availability-guide-rhel-ibm-db2-luw/end-2-end-ha-rhel.png)


### <a name="high-level-overview-of-the-required-steps"></a>所需步驟的高級概述
要部署 IBM Db2 配置，您需要執行以下步驟：

  + 規劃您的環境。
  + 部署 VM。
  + 更新 RHEL Linux 並配置檔案系統。
  + 安裝和配置起搏器。
  + 設置[glusterfs 群集][glusterfs]或[Azure NetApp 檔][anf-rhel]
  + 在[單獨的群集上安裝 ASCS/ERS。][ascs-ha-rhel]
  + 使用分散式/高可用性選項 （SWPM） 安裝 IBM Db2 資料庫。
  + 安裝並創建次要資料庫節點和實例，並配置 HADR。
  + 確認 HADR 工作。
  + 應用起搏器配置來控制 IBM Db2。
  + 配置 Azure 負載等化器。
  + 安裝主應用程式伺服器和對話方塊應用程式伺服器。
  + 檢查和調整 SAP 應用程式伺服器的配置。
  + 執行容錯移轉和接管測試。



## <a name="plan-azure-infrastructure-for-hosting-ibm-db2-luw-with-hadr"></a>規劃 Azure 基礎結構，以便使用 HADR 託管 IBM Db2 LUW

在執行部署之前完成規劃過程。 規劃為在 Azure 中部署具有 HADR 的 Db2 配置奠定了基礎。 下表列出了需要作為 IMB Db2 LUW（SAP 環境資料庫部分）規劃一部分的關鍵元素：

| 主題 | 簡短描述 |
| --- | --- |
| 定義 Azure 資源組 | 部署 VM、VNet、Azure 負載平衡器和其他資源的資源組。 可以是現有的，也可以是新的。 |
| 虛擬網路/子網定義 | 部署 IBM Db2 和 Azure 負載等化器的 VM 的位置。 可以現有或新創建。 |
| 託管 IBM Db2 LUW 的虛擬機器 | VM 大小、存儲、網路、IP 位址。 |
| IBM Db2 資料庫的虛擬主機名稱和虛擬 IP| 用於連接 SAP 應用程式伺服器的虛擬 IP 或主機名稱。 **db-virt-主機名稱** **db-virt-ip**。 |
| Azure 遮罩 | 防止了避免大腦分裂的方法。 |
| Azure Load Balancer | 使用 Db2 資料庫的基本或標準（建議）探測埠（我們的建議 62500）**探針埠**。 |
| 名稱解析| 名稱解析在環境中的工作原理。 強烈建議提供 DNS 服務。 可以使用本地主機檔。 |
    
有關 Azure 中的 Linux 起搏器的詳細資訊，請參閱[在 Azure 中的紅帽企業 Linux 上設置起搏器][rhel-pcs-azr]。

## <a name="deployment-on-red-hat-enterprise-linux"></a>在紅帽企業 Linux 上部署

IBM Db2 LUW 的資源代理包含在紅帽企業 Linux 伺服器 HA 載入項中。 對於本文檔中描述的設置，應使用紅帽企業 Linux 進行 SAP。 Azure 應用商店包含用於 SAP 或更高版本紅帽企業 Linux 7.4 的圖像，可用於部署新的 Azure 虛擬機器。 在 Azure VM 應用商店中選擇 VM 映射時，請注意紅帽通過 Azure 應用商店提供的各種支援或服務模型。

### <a name="hosts-dns-updates"></a>主機：DNS 更新
列出所有主機名稱（包括虛擬主機名稱），並更新 DNS 伺服器以啟用正確的 IP 位址以解析主機名稱。 如果 DNS 伺服器不存在，或者您無法更新和創建 DNS 條目，則需要使用參與此方案的各個 VM 的本地主機檔。 如果使用主機檔條目，請確保這些條目已應用於 SAP 系統內容中的所有 VM。 但是，我們建議您使用 DNS，理想情況下，DNS 會擴展到 Azure


### <a name="manual-deployment"></a>手動部署

確保 IBM/SAP 支援 IBM Db2 LUW 的選定作業系統。 Azure VM 和 Db2 版本的受支援作業系統版本清單在 SAP 注釋[1928533 中]提供。 各個 Db2 版本的作業系統版本清單在 SAP 產品可用性矩陣中提供。 我們強烈建議 SAP 至少使用紅帽企業 Linux 7.4，因為在此或更高版本中與 Azure 相關的性能改進。

1. 創建或選擇資源組。
1. 創建或選擇虛擬網路和子網。
1. 創建 Azure 可用性集或部署可用性區域。
    + 對於可用性集，將最大更新域設置為 2。
1. 創建虛擬機器 1。
    + 在 Azure 應用商店中為 SAP 映射使用紅帽企業 Linux。
    + 選擇在步驟 3 中創建的 Azure 可用性集，或選擇"可用性區域"。
1.  創建虛擬機器 2。
    + 在 Azure 應用商店中為 SAP 映射使用紅帽企業 Linux。
    + 選擇在步驟 3 中創建的 Azure 可用性集，或選擇可用性區域（與步驟 3 中不同的區域）。
1. 將資料磁片添加到 VM，然後檢查文章 IBM [Db2 Azure 虛擬機器 DBMS 部署 SAP 工作負載][dbms-db2]中的檔案系統設置的建議。

## <a name="create-the-pacemaker-cluster"></a>建立 Pacemaker 叢集
    
要為此 IBM Db2 伺服器創建基本起搏器群集，請參閱 [在 Azure 中的紅帽企業 Linux 上設置起搏器][rhel-pcs-azr]。 

## <a name="install-the-ibm-db2-luw-and-sap-environment"></a>安裝 IBM Db2 LUW 和 SAP 環境

在開始安裝基於 IBM Db2 LUW 的 SAP 環境之前，請查看以下文檔：

+ Azure 文件
+ SAP 文檔
+ IBM 文檔

本文檔的連結在本文的介紹部分提供。

查看有關在 IBM Db2 LUW 上安裝基於 NetWeaver 的應用程式的 SAP 安裝手冊。
您可以使用[SAP 安裝指南查找器][sap-instfind]在 SAP 説明門戶上找到指南。

您可以通過設置以下篩選器來減少門戶中顯示的輔助線數量：
- 我想："安裝一個新系統"
- 我的資料庫："用於 Linux、Unix 和 Windows 的 IBM Db2"
- 用於 SAP NetWeaver 版本、堆疊配置或作業系統的其他篩選器

#### <a name="red-hat-firewall-rules"></a>紅帽防火牆規則
紅帽企業 Linux 預設啟用了防火牆。 

<pre><code>#Allow access to SWPM tool. Rule is not permanent.
sudo firewall-cmd --add-port=4237/tcp</code></pre>

### <a name="installation-hints-for-setting-up-ibm-db2-luw-with-hadr"></a>安裝提示，用於使用 HADR 設置 IBM Db2 LUW

要設置主 IBM Db2 LUW 資料庫實例，

- 使用高可用性或分散式選項。
- 安裝 SAP ASCS/ERS 和資料庫實例。
- 備份新安裝的資料庫。

> [!IMPORTANT] 
> 記下安裝期間設置的"資料庫通訊連接埠"。 對於兩個資料庫實例，它必須是相同的埠號。
>![SAP SWPM 埠定義](./media/high-availability-guide-rhel-ibm-db2-luw/hadr-swpm-db2-port.png)

### <a name="ibm-db2-hadr-settings-for-azure"></a>用於 Azure 的 IBM Db2 HADR 設置

   使用 Azure 起搏器擊劍代理時，設置以下參數：

   - HADR 對等視窗持續時間（秒） （HADR_PEER_WINDOW） = 240  
   - HADR 超時值 （HADR_TIMEOUT） = 45

我們建議基於初始容錯移轉/接管測試的上述參數。 您必須使用這些參數設置測試容錯移轉和接管的正確功能。 由於單個配置可能有所不同，因此參數可能需要調整。 

> [!NOTE]
> 特定于具有 HADR 配置且具有正常啟動的 IBM Db2：次要資料庫或備用資料庫實例必須啟動並運行，然後才能啟動主資料庫實例。

   
> [!NOTE]
> 對於特定于 Azure 和起搏器的安裝和配置：在通過 SAP 軟體組態管理員進行安裝過程中，存在關於 IBM Db2 LUW 高可用性的明確問題：
>+ 不要選擇**IBM Db2 純標額**。
>+ 不要選擇**為多平臺安裝 IBM Tivoli 系統自動化**。
>+ 不要選擇 **"生成群集設定檔**"。
>![SAP SWPM - DB2 HA 選項](./media/high-availability-guide-rhel-ibm-db2-luw/swpm-db2ha-opt.png)


要使用 SAP 同質系統複製過程設置備用資料庫伺服器，請執行以下步驟：

1. 選擇 **"系統複製**選項>**目標系統** > **分散式** > **資料庫實例**。
1. 作為複製方法，選擇 **"同質系統"，** 以便可以使用備份還原待命伺服器實例上的備份。
1. 當您到達用於還原資料庫以進行同質系統複製的退出步驟時，請退出安裝程式。 從主主機的備份還原資料庫。 所有後續安裝階段已在主資料庫伺服器上執行。

#### <a name="red-hat-firewall-rules-for-db2-hadr"></a>DB2 HADR 的紅帽防火牆規則
添加防火牆規則以允許流量到 DB2 和 DB2 之間，以便 HADR 正常工作：
+ 資料庫通訊連接埠。 如果使用分區，則添加這些埠。
+ HADR 埠（DB2 參數的值HADR_LOCAL_SVC）
+ Azure 探測埠
<pre><code>sudo firewall-cmd --add-port=&lt;port&gt;/tcp --permanent
sudo firewall-cmd --reload</code></pre>

#### <a name="ibm-db2-hadr-check"></a>IBM Db2 哈德哈德檢查
為了演示目的和本文中描述的過程，資料庫 SID 是**ID2**。

配置 HADR 後，狀態為"PEER"和"在主節點和備用節點上連接"，請執行以下檢查：

<pre><code>
Execute command as db2&lt;sid&gt; db2pd -hadr -db &lt;SID&gt;

#Primary output:
Database Member 0 -- Database ID2 -- Active -- Up 1 days 15:45:23 -- Date 2019-06-25-10.55.25.349375

                            <b>HADR_ROLE = PRIMARY
                          REPLAY_TYPE = PHYSICAL
                        HADR_SYNCMODE = NEARSYNC
                           STANDBY_ID = 1
                        LOG_STREAM_ID = 0
                           HADR_STATE = PEER
                           HADR_FLAGS =
                  PRIMARY_MEMBER_HOST = az-idb01
                     PRIMARY_INSTANCE = db2id2
                       PRIMARY_MEMBER = 0
                  STANDBY_MEMBER_HOST = az-idb02
                     STANDBY_INSTANCE = db2id2
                       STANDBY_MEMBER = 0
                  HADR_CONNECT_STATUS = CONNECTED</b>
             HADR_CONNECT_STATUS_TIME = 06/25/2019 10:55:05.076494 (1561460105)
          HEARTBEAT_INTERVAL(seconds) = 7
                     HEARTBEAT_MISSED = 5
                   HEARTBEAT_EXPECTED = 52
                HADR_TIMEOUT(seconds) = 30
        TIME_SINCE_LAST_RECV(seconds) = 5
             PEER_WAIT_LIMIT(seconds) = 0
           LOG_HADR_WAIT_CUR(seconds) = 0.000
    LOG_HADR_WAIT_RECENT_AVG(seconds) = 598.000027
   LOG_HADR_WAIT_ACCUMULATED(seconds) = 598.000
                  LOG_HADR_WAIT_COUNT = 1
SOCK_SEND_BUF_REQUESTED,ACTUAL(bytes) = 0, 46080
SOCK_RECV_BUF_REQUESTED,ACTUAL(bytes) = 0, 369280
            PRIMARY_LOG_FILE,PAGE,POS = S0000012.LOG, 14151, 3685322855
            STANDBY_LOG_FILE,PAGE,POS = S0000012.LOG, 14151, 3685322855
                  HADR_LOG_GAP(bytes) = 132242668
     STANDBY_REPLAY_LOG_FILE,PAGE,POS = S0000012.LOG, 14151, 3685322855
       STANDBY_RECV_REPLAY_GAP(bytes) = 0
                     PRIMARY_LOG_TIME = 06/25/2019 10:45:42.000000 (1561459542)
                     STANDBY_LOG_TIME = 06/25/2019 10:45:42.000000 (1561459542)
              STANDBY_REPLAY_LOG_TIME = 06/25/2019 10:45:42.000000 (1561459542)
         STANDBY_RECV_BUF_SIZE(pages) = 2048
             STANDBY_RECV_BUF_PERCENT = 0
           STANDBY_SPOOL_LIMIT(pages) = 1000
                STANDBY_SPOOL_PERCENT = 0
                   STANDBY_ERROR_TIME = NULL
                 PEER_WINDOW(seconds) = 300
                      PEER_WINDOW_END = 06/25/2019 11:12:03.000000 (1561461123)
             READS_ON_STANDBY_ENABLED = N



#Secondary output:
Database Member 0 -- Database ID2 -- Standby -- Up 1 days 15:45:18 -- Date 2019-06-25-10.56.19.820474

                            <b>HADR_ROLE = STANDBY
                          REPLAY_TYPE = PHYSICAL
                        HADR_SYNCMODE = NEARSYNC
                           STANDBY_ID = 0
                        LOG_STREAM_ID = 0
                           HADR_STATE = PEER
                           HADR_FLAGS =
                  PRIMARY_MEMBER_HOST = az-idb01
                     PRIMARY_INSTANCE = db2id2
                       PRIMARY_MEMBER = 0
                  STANDBY_MEMBER_HOST = az-idb02
                     STANDBY_INSTANCE = db2id2
                       STANDBY_MEMBER = 0
                  HADR_CONNECT_STATUS = CONNECTED</b>
             HADR_CONNECT_STATUS_TIME = 06/25/2019 10:55:05.078116 (1561460105)
          HEARTBEAT_INTERVAL(seconds) = 7
                     HEARTBEAT_MISSED = 0
                   HEARTBEAT_EXPECTED = 10
                HADR_TIMEOUT(seconds) = 30
        TIME_SINCE_LAST_RECV(seconds) = 1
             PEER_WAIT_LIMIT(seconds) = 0
           LOG_HADR_WAIT_CUR(seconds) = 0.000
    LOG_HADR_WAIT_RECENT_AVG(seconds) = 598.000027
   LOG_HADR_WAIT_ACCUMULATED(seconds) = 598.000
                  LOG_HADR_WAIT_COUNT = 1
SOCK_SEND_BUF_REQUESTED,ACTUAL(bytes) = 0, 46080
SOCK_RECV_BUF_REQUESTED,ACTUAL(bytes) = 0, 367360
            PRIMARY_LOG_FILE,PAGE,POS = S0000012.LOG, 14151, 3685322855
            STANDBY_LOG_FILE,PAGE,POS = S0000012.LOG, 14151, 3685322855
                  HADR_LOG_GAP(bytes) = 0
     STANDBY_REPLAY_LOG_FILE,PAGE,POS = S0000012.LOG, 14151, 3685322855
       STANDBY_RECV_REPLAY_GAP(bytes) = 0
                     PRIMARY_LOG_TIME = 06/25/2019 10:45:42.000000 (1561459542)
                     STANDBY_LOG_TIME = 06/25/2019 10:45:42.000000 (1561459542)
              STANDBY_REPLAY_LOG_TIME = 06/25/2019 10:45:42.000000 (1561459542)
         STANDBY_RECV_BUF_SIZE(pages) = 2048
             STANDBY_RECV_BUF_PERCENT = 0
           STANDBY_SPOOL_LIMIT(pages) = 1000
                STANDBY_SPOOL_PERCENT = 0
                   STANDBY_ERROR_TIME = NULL
                 PEER_WINDOW(seconds) = 1000
                      PEER_WINDOW_END = 06/25/2019 11:12:59.000000 (1561461179)
             READS_ON_STANDBY_ENABLED = N

</code></pre>



## <a name="db2-pacemaker-configuration"></a>Db2 起搏器配置

在節點發生故障時，使用起搏器進行自動容錯移轉時，需要相應地配置 Db2 實例和起搏器。 本節介紹這種類型的配置。

以下項以以下任一項為預綴：

- **[A]**： 適用于所有節點
- **[1]**： 僅適用于節點 1 
- **[2]**： 僅適用于節點 2

**[A]** 起搏器配置的先決條件：
1. 使用 db2stop 關閉使用者\<db2 sid>兩個資料庫伺服器。
1. 將 db2\<sid>使用者的 shell 環境更改為 */bin/ksh*：
<pre><code># Install korn shell:
sudo yum install ksh
# Change users shell:
sudo usermod -s /bin/ksh db2&lt;sid&gt;</code></pre>
   

### <a name="pacemaker-configuration"></a>起搏器配置

**[1]** IBM Db2 特定于 HADR 的起搏器配置：
<pre><code># Put Pacemaker into maintenance mode
sudo pcs property set maintenance-mode=true 
</code></pre>

**[1]** 創建 IBM Db2 資源：
<pre><code># Replace <b>bold strings</b> with your instance name db2sid, database SID, and virtual IP address/Azure Load Balancer.
sudo pcs resource create Db2_HADR_<b>ID2</b> db2 instance='<b>db2id2</b>' dblist='<b>ID2</b>' master meta notify=true resource-stickiness=5000

#Configure resource stickiness and correct cluster notifications for master resoruce
sudo pcs resource update Db2_HADR_<b>ID2</b>-master meta notify=true resource-stickiness=5000

# Configure virtual IP - same as Azure Load Balancer IP
sudo pcs resource create vip_<b>db2id2</b>_<b>ID2</b> IPaddr2 ip='<b>10.100.0.40</b>'

# Configure probe port for Azure load Balancer
sudo pcs resource create nc_<b>db2id2</b>_<b>ID2</b> azure-lb port=<b>62500</b>

#Create a group for ip and Azure loadbalancer probe port
sudo pcs resource group add g_ipnc_<b>db2id2</b>_<b>ID2</b> vip_<b>db2id2</b>_<b>ID2</b> nc_<b>db2id2</b>_<b>ID2</b>

#Create colocation constrain - keep Db2 HADR Master and Group on same node
sudo pcs constraint colocation add g_ipnc_<b>db2id2</b>_<b>ID2</b> with master Db2_HADR_<b>ID2</b>-master

#Create start order constrain
sudo pcs constraint order promote Db2_HADR_<b>ID2</b>-master then g_ipnc_<b>db2id2</b>_<b>ID2</b>
</code></pre>

**[1]** 啟動 IBM Db2 資源：
* 將起搏器置於維護模式。
<pre><code># Put Pacemaker out of maintenance-mode - that start IBM Db2
sudo pcs property set maintenance-mode=false</pre></code>

**{1}** 確保群集狀態正常，並且所有資源都已啟動。 資源運行的節點並不重要。
<pre><code>sudo pcs status</code>
2 nodes configured
5 resources configured

線上： [ az-idb01 az-idb02 ]

資源的完整清單：

 rsc_st_azure（石：fence_azure_arm）：開始 az-idb01 主/從集：Db2_HADR_ID2主 [Db2_HADR_ID2] 主控： [ az-idb01] 從屬： [ az-idb02 ] 資源組： g_ipnc_db2id2_ID2vip_db2id2_ID2 （ocf：isid：IPaddr2）： 開始 az-idb01 nc_db2id2_ID2 （ocf：心跳：azure-lb）： 啟動 az-idb01

守護進程狀態：共同步：活動/禁用心臟起搏器：活動/禁用 pcsd：活動/啟用
</pre>

> [!IMPORTANT]
> 您必須使用起搏器工具管理起搏器群集 Db2 實例。 如果使用 db2 命令（如 db2stop），Pacemaker 會將該操作檢測為資源故障。 如果執行維護，可以將節點或資源置於維護模式。 起搏器將暫停監視資源，然後您可以使用正常的 db2 管理命令。


### <a name="configure-azure-load-balancer"></a>設定 Azure 負載平衡器
要配置 Azure 負載等化器，我們建議您使用 Azure 標準負載等化器 SKU，然後執行以下操作;因此，請使用[Azure 標準負載等化器 SKU。](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview)

> [!NOTE]
> 標準負載等化器 SKU 具有從負載等化器下方的節點訪問公共 IP 位址的限制。 在[SAP 高可用性方案中使用 Azure 標準負載等化器的虛擬機器的公共終結點連接](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-standard-load-balancer-outbound-connections)文章介紹了如何啟用這些節點訪問公共 IP 位址的方法



1. 創建前端 IP 池：

   a. 在 Azure 門戶中，打開 Azure 負載等化器，選擇**前端 IP 池**，然後選擇 **"添加**"。

   b. 輸入新的前端 IP 池的名稱（例如 **，Db2 連接**）。

   c. 將**分配**設置為**靜態**，並輸入在開頭定義的 IP 位址**虛擬 IP。**

   d. 選取 [確定]****。

   e. 建立新的前端 IP 集區之後，請記下集區 IP 位址。

1. 創建後端池：

   a. 在 Azure 門戶中，打開 Azure 負載等化器，選擇**後端池**，然後選擇 **"添加**"。

   b. 輸入新後端池的名稱（例如 **，Db2 後端**）。

   c. 選取 [新增虛擬機器]****。

   d. 選擇上一步中創建的可用性集或承載 IBM Db2 資料庫的虛擬機器。

   e. 選擇 IBM Db2 群集的虛擬機器。

   f. 選取 [確定]****。

1. 創建運行狀況探測：

   a. 在 Azure 門戶中，打開 Azure 負載等化器，選擇**運行狀況探測**，然後選擇 **"添加**"。

   b. 輸入新運行狀況探測的名稱（例如 **，Db2-hp**）。

   c. 選擇**TCP**作為協定和埠**62500**。 將**間隔**值設置為**5**，並將**不正常閾值**設置為**2**。

   d. 選取 [確定]****。

1. 創建負載平衡規則：

   a. 在 Azure 門戶中，打開 Azure 負載平衡器，選擇 **"負載平衡規則**"，然後選擇 **"添加**"。

   b. 輸入新的負載等化器規則的名稱（例如 **，Db2-SID）。**

   c. 選擇前端 IP 位址、後端池和您之前創建的運行狀況探測（例如 **，Db2 前端**）。

   d. 將**協定**設置為**TCP**，然後輸入埠*資料庫通訊連接埠*。

   e. 將 [閒置逾時]**** 增加為 30 分鐘。

   f. 請確保**啟用浮動 IP**。

   g. 選取 [確定]****。

**[A]** 為探測埠添加防火牆規則：
<pre><code>sudo firewall-cmd --add-port=<b><probe-port></b>/tcp --permanent
sudo firewall-cmd --reload</code></pre>

### <a name="make-changes-to-sap-profiles-to-use-virtual-ip-for-connection"></a>更改 SAP 設定檔以使用虛擬 IP 進行連接
要連接到 HADR 配置的主實例，SAP 應用程式層需要使用為 Azure 負載等化器定義和配置的虛擬 IP 位址。 需要進行以下更改：

/sapmnt/SID\<>/設定檔/DEFAULT。人陣
<pre><code>SAPDBHOST = db-virt-hostname
j2ee/dbhost = db-virt-hostname
</code></pre>

/sapmnt/\<SID>/全域/db6/db2cli.ini
<pre><code>Hostname=db-virt-hostname
</code></pre>



## <a name="install-primary-and-dialog-application-servers"></a>安裝主應用程式伺服器和對話方塊應用程式伺服器

安裝針對 Db2 HADR 配置的主應用程式伺服器和對話方塊應用程式伺服器時，請使用為配置選擇的虛擬主機名稱。 

如果在創建 Db2 HADR 配置之前執行了安裝，則按照上一節所述進行更改，如下如下 SAP JAVA 堆疊。

### <a name="abapjava-or-java-stack-systems-jdbc-url-check"></a>ABAP_JAVA 或 JAVA 堆疊系統 JDBC URL 檢查

使用 J2EE 組態工具檢查或更新 JDBC URL。 由於 J2EE 組態工具是一個圖形工具，因此您需要安裝 X 伺服器：
 
1. 登錄到 J2EE 實例的主應用程式伺服器並執行：
    
    <pre><code>sudo /usr/sap/*SID*/*Instance*/j2ee/configtool/configtool.sh</code></pre>  
    
1. 在左側框架中，選擇**安全存儲**。
1. 在正確的框架中，選擇鍵`jdbc/pool/\<SAPSID>/url`。
1. 將 JDBC URL 中的主機名稱更改為虛擬主機名稱。
    
    <pre><code>jdbc:db2://db-virt-hostname:5912/TSP:deferPrepares=0</code></pre>  
    
1. 選取 [加入]****。
1. 要保存更改，請選擇左上角的磁片圖示。
1. 關閉組態工具。
1. 重新開機 JAVA 實例。

## <a name="configure-log-archiving-for-hadr-setup"></a>為 HADR 設置配置日誌存檔
要為 HADR 設置配置 Db2 日誌存檔，我們建議您將主資料庫和備用資料庫配置為具有來自所有日誌存檔位置的自動日誌檢索功能。 主資料庫和備用資料庫都必須能夠從資料庫實例之一可能存檔日誌檔的所有日誌存檔位置檢索日誌檔。 

日誌存檔僅由主資料庫執行。 如果更改資料庫伺服器的 HADR 角色，或者發生故障，則新的主資料庫負責日誌存檔。 如果設置了多個日誌存檔位置，則日誌可能會存檔兩次。 如果發生本地或遠端追趕，您可能還必須手動將存檔的日誌從舊主伺服器複製到新主伺服器的活動日誌位置。

我們建議配置公共 NFS 共用或 GlusterFS，其中日誌是從兩個節點寫入的。 NFS 共用或 GlusterFS 必須高度可用。 

您可以使用現有的高可用 NFS 共用或 GlusterFS 進行傳輸或設定檔目錄。 如需詳細資訊，請參閱

- [Red Hat Enterprise Linux for SAP NetWeaver 上的 GlusterFS on Azure VM][glusterfs] 
- [用於 SAP 應用程式的 Azure NetApp 檔，在紅帽企業 Linux 上的 Azure VM 上的 SAP NetWeaver 的高可用性][anf-rhel]
- [Azure NetApp 檔](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-introduction)（創建 NFS 共用）

## <a name="test-the-cluster-setup"></a>測試叢集設定

本節介紹如何測試 Db2 HADR 設置。 每個測試都假定 IBM Db2 主資料庫在*az-idb01*虛擬機器上運行。 必須使用具有 sudo 許可權或根（不推薦）的使用者。

此處說明了所有測試案例的初始狀態：（crm_mon -r 或 pcs 狀態）

- **pcs 狀態**是起搏器執行時狀態的快照 
- **crm_mon -r**是起搏器狀態的連續輸出

<pre><code>2 nodes configured
5 resources configured

Online: [ az-idb01 az-idb02 ]

Full list of resources:

 rsc_st_azure   (stonith:fence_azure_arm):      Started az-idb01
 Master/Slave Set: Db2_HADR_ID2-master [Db2_HADR_ID2]
     Masters: [ az-idb01 ]
     Slaves: [ az-idb02 ]
 Resource Group: g_ipnc_db2id2_ID2
     vip_db2id2_ID2     (ocf::heartbeat:IPaddr2):       Started az-idb01
     nc_db2id2_ID2      (ocf::heartbeat:azure-lb):      Started az-idb01

Daemon Status:
  corosync: active/disabled
  pacemaker: active/disabled
  pcsd: active/enabled
</code></pre>

SAP 系統中的原始狀態記錄在事務 DBACOCKPIT > 配置>概述中，如下圖所示：

![DBACockpit - 遷移前](./media/high-availability-guide-rhel-ibm-db2-luw/hadr-sap-mgr-org-rhel.png)




### <a name="test-takeover-of-ibm-db2"></a>測試 IBM Db2 的接管


> [!IMPORTANT] 
> 在開始測試之前，請確保：
> * 起搏器沒有任何失敗操作（pcs 狀態）。
> * 沒有位置約束（遷移測試的剩下）
> * IBM Db2 HADR 同步工作。 請與使用者 db2\<sid> <pre><code>db2pd -hadr -db \<DBSID></code></pre>


通過執行以下命令遷移運行主 Db2 資料庫的節點：
<pre><code>sudo pcs resource move Db2_HADR_<b>ID2</b>-master</code></pre>

遷移完成後，crm 狀態輸出如下所示：
<pre><code>2 nodes configured
5 resources configured

Online: [ az-idb01 az-idb02 ]

Full list of resources:

 rsc_st_azure   (stonith:fence_azure_arm):      Started az-idb01
 Master/Slave Set: Db2_HADR_ID2-master [Db2_HADR_ID2]
     Masters: [ az-idb02 ]
     Stopped: [ az-idb01 ]
 Resource Group: g_ipnc_db2id2_ID2
     vip_db2id2_ID2     (ocf::heartbeat:IPaddr2):       Started az-idb02
     nc_db2id2_ID2      (ocf::heartbeat:azure-lb):      Started az-idb02

</code></pre>

SAP 系統中的原始狀態記錄在事務 DBACOCKPIT > 配置>概述中，如下圖所示：

![DBACockpit - 遷移後](./media/high-availability-guide-rhel-ibm-db2-luw/hadr-sap-mgr-post-rhel.png)

使用"pcs 資源移動"的資源遷移會創建位置約束。 在這種情況下，位置約束正在阻止在 az-idb01 上運行 IBM Db2 實例。 如果未刪除位置約束，則資源不能失敗。

刪除位置約束，備用節點將在 az-idb01 上啟動。
<pre><code>sudo pcs resource clear Db2_HADR_<b>ID2</b>-master</code></pre>
群集狀態更改為：
<pre><code>2 nodes configured
5 resources configured

Online: [ az-idb01 az-idb02 ]

Full list of resources:

 rsc_st_azure   (stonith:fence_azure_arm):      Started az-idb01
 Master/Slave Set: Db2_HADR_ID2-master [Db2_HADR_ID2]
     Masters: [ az-idb02 ]
     Slaves: [ az-idb01 ]
 Resource Group: g_ipnc_db2id2_ID2
     vip_db2id2_ID2     (ocf::heartbeat:IPaddr2):       Started az-idb02
     nc_db2id2_ID2      (ocf::heartbeat:azure-lb):      Started az-idb02</code></pre>

![DBACockpit - 刪除的位置約束](./media/high-availability-guide-rhel-ibm-db2-luw/hadr-sap-mgr-clear-rhel.png)


將資源遷移回*az-idb01*並清除位置約束
<pre><code>sudo pcs resource move Db2_HADR_<b>ID2</b>-master az-idb01
sudo pcs resource clear Db2_HADR_<b>ID2</b>-master
</code></pre>

- **pcs 資源\<移動res_name> <host>：** 創建位置約束，並可能導致收購問題
- **pcs 資源\<清除res_name>：** 清除位置約束
- **pcs 資源\<清理res_name>：** 清除資源的所有錯誤

### <a name="test-a-manual-takeover"></a>測試手動接管

您可以通過在*az-idb01*節點上停止起搏器服務來測試手動接管：
<pre><code>systemctl stop pacemaker</code></pre>

*az-ibdb02*上的狀態
<pre><code>2 nodes configured
5 resources configured

Node az-idb01: pending
Online: [ az-idb02 ]

Full list of resources:

 rsc_st_azure   (stonith:fence_azure_arm):      Started az-idb02
 Master/Slave Set: Db2_HADR_ID2-master [Db2_HADR_ID2]
     Masters: [ az-idb02 ]
     Stopped: [ az-idb01 ]
 Resource Group: g_ipnc_db2id2_ID2
     vip_db2id2_ID2     (ocf::heartbeat:IPaddr2):       Started az-idb02
     nc_db2id2_ID2      (ocf::heartbeat:azure-lb):      Started az-idb02

Daemon Status:
  corosync: active/disabled
  pacemaker: active/disabled
  pcsd: active/enabled</code></pre>

容錯移轉後，您可以在*az-idb01*上再次啟動服務。
<pre><code>systemctl start  pacemaker</code></pre>


### <a name="kill-the-db2-process-on-the-node-that-runs-the-hadr-primary-database"></a>在運行 HADR 主資料庫的節點上終止 Db2 進程

<pre><code>#Kill main db2 process - db2sysc
[sapadmin@az-idb02 ~]$ sudo ps -ef|grep db2sysc
db2ptr    34598  34596  8 14:21 ?        00:00:07 db2sysc 0
[sapadmin@az-idb02 ~]$ sudo kill -9 34598
</code></pre>

Db2 實例將失敗，起搏器將移動主節點並報告以下狀態：

<pre><code>2 nodes configured
5 resources configured

Online: [ az-idb01 az-idb02 ]

Full list of resources:

 rsc_st_azure   (stonith:fence_azure_arm):      Started az-idb02
 Master/Slave Set: Db2_HADR_ID2-master [Db2_HADR_ID2]
     Masters: [ az-idb02 ]
     Stopped: [ az-idb01 ]
 Resource Group: g_ipnc_db2id2_ID2
     vip_db2id2_ID2     (ocf::heartbeat:IPaddr2):       Started az-idb02
     nc_db2id2_ID2      (ocf::heartbeat:azure-lb):      Started az-idb02

Failed Actions:
* Db2_HADR_ID2_demote_0 on az-idb01 'unknown error' (1): call=49, status=complete, exitreason='none',
    last-rc-change='Wed Jun 26 09:57:35 2019', queued=0ms, exec=362ms</code></pre>

Pacemaker 將重新開機同一節點上的 Db2 主資料庫實例，否則它將容錯移轉到運行次要資料庫實例的節點，並報告錯誤。

### <a name="kill-the-db2-process-on-the-node-that-runs-the-secondary-database-instance"></a>在運行次要資料庫實例的節點上終止 Db2 進程

<pre><code>[sapadmin@az-idb02 ~]$ sudo ps -ef|grep db2sysc
db2id2    23144  23142  2 09:53 ?        00:00:13 db2sysc 0
[sapadmin@az-idb02 ~]$ sudo kill -9 23144
</code></pre>

節點進入未遂聲明和報告錯誤
<pre><code>2 nodes configured
5 resources configured

Online: [ az-idb01 az-idb02 ]

Full list of resources:

 rsc_st_azure   (stonith:fence_azure_arm):      Started az-idb02
 Master/Slave Set: Db2_HADR_ID2-master [Db2_HADR_ID2]
     Masters: [ az-idb01 ]
     Slaves: [ az-idb02 ]
 Resource Group: g_ipnc_db2id2_ID2
     vip_db2id2_ID2     (ocf::heartbeat:IPaddr2):       Started az-idb01
     nc_db2id2_ID2      (ocf::heartbeat:azure-lb):      Started az-idb01

Failed Actions:
* Db2_HADR_ID2_monitor_20000 on az-idb02 'not running' (7): call=144, status=complete, exitreason='none',
    last-rc-change='Wed Jun 26 10:02:09 2019', queued=0ms, exec=0ms</code></pre>

Db2 實例在以前分配的輔助角色中重新開機。

### <a name="stop-db-via-db2stop-force-on-the-node-that-runs-the-hadr-primary-database-instance"></a>通過運行 HADR 主資料庫實例的節點上的 db2stop 力停止資料庫

作為使用者 db2\<sid>執行命令 db2stop 力：
<pre><code>az-idb01:db2ptr> db2stop force</code></pre>

檢測到故障：

<pre><code>2 nodes configured
5 resources configured

Online: [ az-idb01 az-idb02 ]

Full list of resources:

 rsc_st_azure   (stonith:fence_azure_arm):      Started az-idb02
 Master/Slave Set: Db2_HADR_ID2-master [Db2_HADR_ID2]
     Slaves: [ az-idb02 ]
     Stopped: [ az-idb01 ]
 Resource Group: g_ipnc_db2id2_ID2
     vip_db2id2_ID2     (ocf::heartbeat:IPaddr2):       Stopped
     nc_db2id2_ID2      (ocf::heartbeat:azure-lb):      Stopped

Failed Actions:
* Db2_HADR_ID2_demote_0 on az-idb01 'unknown error' (1): call=110, status=complete, exitreason='none',
    last-rc-change='Wed Jun 26 14:03:12 2019', queued=0ms, exec=355ms</code></pre>

Db2 HADR 次要資料庫實例已提升到主要角色。
<pre><code>2 nodes configured
5 resources configured

Online: [ az-idb01 az-idb02 ]

Full list of resources:

 rsc_st_azure   (stonith:fence_azure_arm):      Started az-idb02
 Master/Slave Set: Db2_HADR_ID2-master [Db2_HADR_ID2]
     Masters: [ az-idb02 ]
     Slaves: [ az-idb01 ]
 Resource Group: g_ipnc_db2id2_ID2
     vip_db2id2_ID2     (ocf::heartbeat:IPaddr2):       Started az-idb02
     nc_db2id2_ID2      (ocf::heartbeat:azure-lb):      Started az-idb02

Failed Actions:
* Db2_HADR_ID2_demote_0 on az-idb01 'unknown error' (1): call=110, status=complete, exitreason='none',
    last-rc-change='Wed Jun 26 14:03:12 2019', queued=0ms, exec=355ms</pre></code>


### <a name="crash-the-vm-that-runs-the-hadr-primary-database-instance-with-halt"></a>將運行 HADR 主資料庫實例的 VM 與"halt"崩潰

<pre><code>#Linux kernel panic. 
sudo echo b > /proc/sysrq-trigger</code></pre>

在這種情況下，Pacemaker 將檢測運行主資料庫實例的節點未回應。

<pre><code>2 nodes configured
5 resources configured

Node az-idb01: UNCLEAN (online)
Online: [ az-idb02 ]

Full list of resources:

rsc_st_azure    (stonith:fence_azure_arm):      Started az-idb02
 Master/Slave Set: Db2_HADR_ID2-master [Db2_HADR_ID2]
     Masters: [ az-idb01 ]
     Slaves: [ az-idb02 ]
 Resource Group: g_ipnc_db2id2_ID2
     vip_db2id2_ID2     (ocf::heartbeat:IPaddr2):       Started az-idb01
     nc_db2id2_ID2      (ocf::heartbeat:azure-lb):      Started az-idb01</code></pre>

下一步是檢查*分裂大腦*的情況。 在倖存的節點確定上次運行主資料庫實例的節點已關閉後，將執行資源容錯移轉。

<pre><code>2 nodes configured
5 resources configured

Online: [ az-idb02 ]
OFFLINE: [ az-idb01 ]

Full list of resources:

rsc_st_azure    (stonith:fence_azure_arm):      Started az-idb02
 Master/Slave Set: Db2_HADR_ID2-master [Db2_HADR_ID2]
     Masters: [ az-idb02 ]
     Stopped: [ az-idb01 ]
 Resource Group: g_ipnc_db2id2_ID2
     vip_db2id2_ID2     (ocf::heartbeat:IPaddr2):       Started az-idb02
     nc_db2id2_ID2      (ocf::heartbeat:azure-lb):      Started az-idb02 </code></pre>


如果出現內核死機，故障節點將由遮罩代理恢復。 故障節點重新連線後，您必須啟動起搏器群集
<pre><code>sudo pcs cluster start</code></pre> 它將 Db2 實例啟動到輔助角色中。

<pre><code>2 nodes configured
5 resources configured

Online: [ az-idb01 az-idb02 ]

Full list of resources:

rsc_st_azure    (stonith:fence_azure_arm):      Started az-idb02
 Master/Slave Set: Db2_HADR_ID2-master [Db2_HADR_ID2]
     Masters: [ az-idb02 ]
     Slaves: [ az-idb01 ]
 Resource Group: g_ipnc_db2id2_ID2
     vip_db2id2_ID2     (ocf::heartbeat:IPaddr2):       Started az-idb02
     nc_db2id2_ID2      (ocf::heartbeat:azure-lb):      Started az-idb02</code></pre>

## <a name="next-steps"></a>後續步驟
- [SAP NetWeaver 的高可用性架構和案例](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-architecture-scenarios)
- [在 Azure 中的 Red Hat Enterprise Linux 上設定 Pacemaker][rhel-pcs-azr]
