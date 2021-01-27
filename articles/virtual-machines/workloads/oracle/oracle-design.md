---
title: 在 Azure 上設計和實作 Oracle 資料庫 | Microsoft Docs
description: 在 Azure 環境中設計和實作 Oracle 資料庫。
author: dbakevlar
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.date: 12/17/2020
ms.author: kegorman
ms.reviewer: tigorman
ms.openlocfilehash: 0b6f4e652ca8fef7bee4165bcd0673be2fa11eac
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/27/2021
ms.locfileid: "98890759"
---
# <a name="design-and-implement-an-oracle-database-in-azure"></a>在 Azure 中設計和實作 Oracle 資料庫

## <a name="assumptions"></a>假設

- 您打算將 Oracle 資料庫從內部部署環境移轉至 Azure。
- 您的 [診斷套件](https://docs.oracle.com/cd/E11857_01/license.111/e11987/database_management.htm) 或 [自動工作負載存放庫](https://www.oracle.com/technetwork/database/manageability/info/other-manageability/wp-self-managing-database18c-4412450.pdf) 適用于您想要遷移的 Oracle Database
- 您已瞭解 Oracle 中的各種計量。
- 您已基本了解應用程式效能和平台使用量。

## <a name="goals"></a>目標

- 了解如何將 Azure 中的 Oracle 部署最佳化。
- 探索 Azure 環境中 Oracle 資料庫的效能調整選項。
- 透過架構和優點，以及資料庫程式碼的邏輯微調、 (SQL) 和整體資料庫設計，清楚瞭解實體微調的限制。

## <a name="the-differences-between-an-on-premises-and-azure-implementation"></a>內部部署和 Azure 實作之間的差異 

以下是一些您在將內部部署應用程式移轉至 Azure 時應該謹記的重要事項。 

其中的一項重要差異是，Azure 實作中的資源 (例如 VM、磁碟和虛擬網路) 會與其他用戶端共用。 此外，您可以根據需求來節流資源。 Azure 更聚焦在讓失敗存活 (MTTR)，而不是聚焦於避免失敗 (MTBF)。

下表列出 Oracle 資料庫在內部部署實作和 Azure 實作之間的一些差異。


|  | 內部部署實作 | Azure 實作 |
| --- | --- | --- |
| **網路功能** |LAN/WAN  |SDN (軟體定義網路)|
| **安全性群組** |IP/連接埠限制工具 |[ (NSG) 的網路安全性群組 ](https://azure.microsoft.com/blog/network-security-groups) |
| **恢復功能** |MTBF (平均失敗時間) |MTTR (平均復原時間)|
| **預定的維修** |修補/升級|[可用性設定組](/previous-versions/azure/virtual-machines/windows/infrastructure-example) (Azure 所管理的修補/升級) |
| **Resource** |專用  |與其他用戶端共用|
| **區域** |資料中心 |[區域配對](../../regions.md#region-pairs)|
| **儲存體** |SAN/實體磁碟 |[Azure 受控儲存體](https://azure.microsoft.com/pricing/details/managed-disks/?v=17.23h)|
| **縮放** |垂直調整 |水平調整|


### <a name="requirements"></a>需求

- 判斷實際的 CPU 使用率，因為 Oracle 是由核心授權，所以調整 vCPU 需求的大小可能是節省成本的必要做法。 
- 決定資料庫大小、備份儲存體和成長率。
- 判斷您可以根據 Oracle Statspack 和 AWR 報表或作業系統層級儲存體監視工具來預估的 IO 需求。

## <a name="configuration-options"></a>設定選項

有四個可能的區域可供您進行調整，以改善 Azure 環境中的效能：

- 虛擬機器大小
- 網路輸送量
- 磁碟類型和設定
- 磁碟快取設定

### <a name="generate-an-awr-report"></a>產生 AWR 報表

如果您有現有的 Oracle Enterprise Edition 資料庫，而且打算遷移至 Azure，您有數個選項。 如果您有 Oracle 實例的 [診斷套件](https://www.oracle.com/technetwork/oem/pdf/511880.pdf) ，您可以執行 oracle AWR 報表，以取得度量 (IOPS、Mbps、gib 等) 。 針對沒有診斷套件授權或 Standard Edition 資料庫的資料庫，在收集手動快照集之後，可以使用 Statspack 報告來收集相同的重要計量。  這兩種報告方法的主要差異在於，AWR 會自動收集並提供有關資料庫的詳細資訊，而不是 Statspack 的前置報告選項。

您可以考慮在一般和尖峰工作負載期間執行 AWR 報表，以進行比較。 若要收集更精確的工作負載，請考慮為期一周的延伸視窗報表（與24小時的執行時間），並瞭解 AWR 確實會在報表的計算過程中提供平均值。  對於資料中心的遷移，建議您收集報告以在生產系統上調整規模，並預估用於使用者測試、測試、開發等的剩餘資料庫複本， (UAT 等於生產、測試和開發50% 的生產大小調整等 ) 

根據預設，AWR 存放庫會保留8天的資料，並依每小時的間隔拍攝快照集。  若要從命令列執行 AWR 報表，您可以從終端機執行下列動作：

```bash
$ sqlplus / as sysdba
SQL> @$ORACLE_HOME/rdbms/admin/awrrpt.sql;
```

### <a name="key-metrics"></a>重要計量

報表會提示您輸入下列資訊：
- 報表類型： HTML 或文字，在12.1 中 (HTML，並提供比文字格式更多的資訊。 ) 
- 要顯示的快照的天數， (一小時的間隔內，一周的報表在快照集識別碼中會是168不同的值) 
- 報表視窗的起始 SnapshotID。
- 報表視窗的結束 SnapshotId。
- AWR 腳本所要建立的報表名稱。

如果在實際的應用程式叢集上執行 AWR， (RAC) 命令列報告為 awrgrpt，而不是 awrrpt .sql。  "G" 報表將會在單一報表中建立 RAC 資料庫中所有節點的報告，而不需要在每個 RAC 節點上執行一個報告。

以下是您可以從 AWR 報表取得的計量：

- 資料庫名稱、實例名稱和主機名稱
- Oracle) 的資料庫版本， (的可支援性
- CPU/核心
- SGA/PGA、 (和顧問，讓您知道是否有太小的) 
- 記憶體總計 (GB)
- CPU% 忙碌
- 資料庫 Cpu
- IOPs (讀取/寫入) 
- MBPs (讀取/寫入) 
- 網路輸送量
- 網路延遲速率 (低/高)
- 前幾名等候事件 
- 資料庫的參數設定
- 是資料庫 RAC、Exadata、使用 advanced 功能或設定

### <a name="virtual-machine-size"></a>虛擬機器大小

#### <a name="1-estimate-vm-size-based-on-cpu-memory-and-io-usage-from-the-awr-report"></a>1. 根據 AWR 報表中的 CPU、記憶體和 i/o 使用量來預估 VM 大小

您可以查看的一個事項是前五個定時前景事件，其指出系統瓶頸位置。

例如在下圖中，記錄檔案同步在頂端。 這代表等候 LGWR 將記錄緩衝區寫入重做記錄檔的次數。 這些結果代表您需要效能更好的儲存體或磁碟。 此外，此圖也會顯示 CPU (核心) 數目和記憶體數量。

![顯示資料表頂端記錄檔同步的螢幕擷取畫面。](./media/oracle-design/cpu_memory_info.png)

下圖顯示讀取和寫入的 I/O 總計。 報表統計期間共有 59 GB 的讀取和 247.3 GB 的寫入。

![顯示讀取和寫入總 i/o 的螢幕擷取畫面。](./media/oracle-design/io_info.png)

#### <a name="2-choose-a-vm"></a>2. 選擇 VM

根據您從 AWR 報表收集到的資訊，下一個步驟是選擇符合您需求且大小類似的 VM。 您可以在[記憶體最佳化](../../sizes-memory.md)一文中找到可用 VM 的清單。

#### <a name="3-fine-tune-the-vm-sizing-with-a-similar-vm-series-based-on-the-acu"></a>3. 根據 ACU 以類似的 VM 系列微調 VM 大小調整

在您選擇 VM 之後，請注意 VM 的 ACU。 您可以根據較適合您需求的 ACU 值，選擇不同的 VM。 如需詳細資訊，請參閱 [Azure 計算單位](../../acu.md)。

![ACU 單位頁面的螢幕擷取畫面](./media/oracle-design/acu_units.png)

### <a name="network-throughput"></a>網路輸送量

下圖顯示輸送量與 IOPS 之間的關聯性：

![輸送量的螢幕擷取畫面](./media/oracle-design/throughput.png)

總網路輸送量是根據下列資訊進行預估：
- SQL*Net 流量
- MBps x 伺服器數目 (輸出串流，例如 Oracle Data Guard)
- 其他因素，例如應用程式複寫

![SQL*Net 輸送量的螢幕擷取畫面](./media/oracle-design/sqlnet_info.png)

根據您的網路頻寬需求，有各種閘道類型可供您選擇。 這些類型包括基本、VpnGw 和 Azure ExpressRoute。 如需詳細資訊，請參閱 [VPN 閘道定價頁面](https://azure.microsoft.com/pricing/details/vpn-gateway/?v=17.23h)。

**建議**

- 與內部部署相較之下，網路延遲較高。 減少網路來回行程可以大幅改善效能。
- 若要減少來回行程，請合併相同虛擬機器上具有高交易或 “Chatty” 應用程式的應用程式。
- 使用具有 [加速網路](../../../virtual-network/create-vm-accelerated-networking-cli.md) 的虛擬機器，以獲得更佳的網路效能。
- 針對某些 Linux 發行版本，請考慮啟用 [TRIM/](/previous-versions/azure/virtual-machines/linux/configure-lvm#trimunmap-support)取消對應支援。
- 在不同的虛擬機器上安裝 [Oracle Enterprise Manager](https://www.oracle.com/technetwork/oem/enterprise-manager/overview/index.html) 。
- 預設不會在 linux 上啟用大量頁面。 請考慮啟用大量頁面並設定 `use_large_pages = ONLY` Oracle DB。 這可能有助於提升效能。 您可以在[這裡](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/refrn/USE_LARGE_PAGES.html#GUID-1B0F4D27-8222-439E-A01D-E50758C88390)找到詳細資訊。

### <a name="disk-types-and-configurations"></a>磁碟類型和設定

- 預設的 OS 磁碟：這些磁碟類型可提供持續性資料和快取。 它們最適合用在啟動時的 OS 存取，但其設計目的並非用於交易式或資料倉儲 (分析) 工作負載。

- 受控磁碟：Azure 會管理您用於 VM 磁碟的儲存體帳戶。 您可以針對 Oracle 工作負載) 和您需要的磁片大小，指定最常 (premium SSD 的磁片類型。 Azure 會為您建立並管理該磁碟。  進階 SSD 受控磁片僅適用于經記憶體優化且特別設計的 VM 系列。 在選擇特定的 VM 大小之後，功能表只會根據該 VM 大小顯示可用的進階儲存體 SKU。

![受控磁碟頁面的螢幕擷取畫面](./media/oracle-design/premium_disk01.png)

在 VM 上設定儲存體之後，您可能會想要在建立資料庫之前對磁碟進行負載測試。 知道延遲和輸送量方面的 I/O 速率可以協助您判斷 VM 是否支援具有延遲目標的預期輸送量。

有一些工具可用來進行應用程式負載測試，例如 Oracle Orion、Sysbench、SLOB 和 Fio。

在部署好 Oracle 資料庫之後，請再次執行負載測試。 啟動您的一般和尖峰工作負載，其結果會顯示您環境的基準數據。  在工作負載測試中很實際-執行的工作負載不像您將在 VM 上執行的工作負載，是不合理的。

由於 Oracle 是許多需要大量 IO 的資料庫，因此請務必根據 IOPS 速率（而非儲存體大小）調整儲存體大小。 例如，如果所需的 IOPS 是 5000，但您只需要 200 GB，則您可能仍會得到 P30 等級的進階磁碟，即使它隨附 200 GB 以上的儲存體。

IOPS 速率可以從 AWR 報表取得。 此速率是由重做記錄、實體讀取和寫入速率所決定。  務必確認所選的 VM 系列也能夠處理工作負載的 IO 需求。  如果 VM 的 IO 限制低於儲存體，則 VM 會設定最大限制。

![AWR 報表頁面的螢幕擷取畫面](./media/oracle-design/awr_report.png)

例如：重做大小是每秒 12,200,000 個位元組，相當於 11.63 MBPs。
IOPS 是 12,200,000 / 2,358 = 5,174。

在清楚了解 I/O 需求之後，即可選擇最符合這些需求的磁碟機組合。

**建議**

- 針對資料的資料表空間，使用受控儲存體或 Oracle ASM，將 I/O 工作負載分散到一些磁碟。
- 使用 Oracle advanced 壓縮來減少資料和索引) 的 i/o (。
- 分隔不同資料磁片上的重做記錄、暫存和復原資料表空間。
- 不要將任何應用程式檔案放在預設 OS 磁碟 (/dev/sda)。 這些磁碟不適合用於快速 VM 啟動階段，因此可能不會為您的應用程式提供良好的效能。
- 在 Premium 儲存體上使用 M 系列 Vm 時，請啟用 [重做記錄磁片] [寫入加速器](../../how-to-enable-write-accelerator.md) 。
- 請考慮將具有高延遲的重做記錄移至 ultra 磁片。

### <a name="disk-cache-settings"></a>磁碟快取設定

主機快取有三個選項，但針對 Oracle 資料庫，建議您只針對資料庫工作負載使用唯讀快取。  ReadWrite 可能會對資料檔案帶來重大弱點，其中資料庫寫入的目標是要將資料記錄到資料檔案，而不是快取資訊。

與檔案系統或應用程式不同的是，對於資料庫而言，主機快取的建議是 *ReadOnly*：系統會快取所有要求，以供未來讀取。 所有寫入都會繼續寫入磁片。

**建議**

若要將輸送量最大化，建議您盡可能從 **ReadOnly** 開始進行主機快取。 針對進階儲存體，請記住，當您使用 **ReadOnly** 選項掛接檔案系統時，必須停用「阻礙」。 將具有 UUID 的 /etc/fstab 檔案更新到磁碟。

![[受控磁片] 頁面的螢幕擷取畫面，其中顯示 [ReadOnly] 和 [無] 選項。](./media/oracle-design/premium_disk02.png)

- 針對 OS 磁片，使用預設的 **讀取/寫入** 快取，並針對 Oracle 工作負載 vm 使用 premium SSD。  此外，也請確定用於交換的磁片區也是在 premium SSD 上。
- 針對所有資料檔案，請使用 **ReadOnly** 進行快取。 唯讀快取僅適用于高階受控磁片（P30 和更新版本）。  可以搭配 ReadOnly 快取使用的4095GiB 磁片區有限制。  任何較大配置都會預設停用主機快取。

如果您的工作負載在 day 和晚上之間有很大的差異，而且 IO 工作負載可以支援它，則具有高載的 P1 P20 進階 SSD 可能會提供在夜間批次載入或有限 IO 要求期間所需的效能。  

## <a name="security"></a>安全性

在安裝並設定 Azure 環境之後，下一個步驟是保護您的網路。 以下是一些建議：

- NSG 原則：可以透過子網路或 NIC 來定義 NSG。 在子網層級控制存取是比較簡單的方式，可用於安全性和強制路由傳送應用程式防火牆等專案。

- Jumpbox：基於更安全的存取，系統管理員不應該直接連線至應用程式服務或資料庫。 Jumpbox 作為系統管理員機器與 Azure 資源之間的媒體。
![Jumpbox 拓撲頁面的螢幕擷取畫面](./media/oracle-design/jumpbox.png)

    系統管理員機器只應該對 Jumpbox 提供 IP 受限的存取權。 Jumpbox 應該要能夠存取應用程式和資料庫。

- 私人網路 (子網路)：我們建議您將應用程式服務和資料庫放在不同的子網路上，讓 NSG 原則可以設定更好的控制。


## <a name="additional-reading"></a>延伸閱讀

- [設定 Oracle ASM](configure-oracle-asm.md)
- [設定 Oracle Data Guard](configure-oracle-dataguard.md)
- [設定 Oracle Golden Gate](configure-oracle-golden-gate.md)
- [Oracle 備份和復原](./oracle-overview.md)

## <a name="next-steps"></a>後續步驟

- [教學課程︰建立高可用性 VM](../../linux/create-cli-complete.md)
- [瀏覽 VM 部署 Azure CLI 範例](https://github.com/Azure-Samples/azure-cli-samples/tree/master/virtual-machine)
