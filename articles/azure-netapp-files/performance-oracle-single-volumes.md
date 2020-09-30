---
title: Azure NetApp Files 單一磁片區上的 Oracle 資料庫效能 |Microsoft Docs
description: 說明 Azure NetApp Files 單一磁片區在 Oracle 資料庫上的效能測試結果。
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/30/2020
ms.author: b-juche
ms.openlocfilehash: c6cdf2f6dada0aa4dea2f70f18237b7ee39e3ea1
ms.sourcegitcommit: f796e1b7b46eb9a9b5c104348a673ad41422ea97
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/30/2020
ms.locfileid: "91571299"
---
# <a name="oracle-database-performance-on-azure-netapp-files-single-volumes"></a>Azure NetApp Files 單一磁片區上的 Oracle 資料庫效能

本文討論有關雲端中 Oracle 的下列主題。 這些主題對於資料庫管理員、雲端架構設計師或存放裝置設計人員而言，可能特別感興趣：   

* 當您在線上交易處理 (OLTP) 工作負載 (大多是隨機 i/o) 或線上分析處理 (OLAP) 工作負載 (大多是順序 i/o) 時，效能看起來像什麼？   
* 一般 Linux 核心 NFS (kNFS) 用戶端和 Oracle 自己的直接 NFS 用戶端之間的效能有何差異？
* 至於頻寬，單一 Azure NetApp Files 磁片區的效能是否足夠？

## <a name="testing-environment-and-components"></a>測試環境和元件

下圖說明用於測試的環境。 為了保持一致性和簡單性，Ansible 腳本是用來部署測試平臺的所有元素。

![Oracle 測試環境](../media/azure-netapp-files/performance-oracle-test-environment.png)  

### <a name="virtual-machine-configuration"></a>虛擬機器組態

測試會針對虛擬機器使用下列設定：
* 作業系統：   
    RedHat Enterprise Linux 7.8 (wle-ora01) 
* 實例類型：   
    測試中使用了兩個模型– D32s_v3 和 D64s_v3
* 網路介面計數：   
    一個 (1) 放置於子網3  
* 磁片：   
    Oracle 二進位檔和 OS 都放在單一 premium 磁片中

### <a name="azure-netapp-files-configuration"></a><a name="anf_config"></a>Azure NetApp Files 設定
測試會使用下列 Azure NetApp Files 設定：   

* 容量集區大小：  
    已設定集區的各種大小： 4 TiB、8 TiB、16 TiB、32 TiB 
* 服務層級：  
    Ultra 128 (每 1 TiB 配置磁片區容量的頻寬) MiB/秒
* 磁碟區：  
    已評估一和兩個大量測試

### <a name="workload-generator"></a>工作負載產生器 

測試使用的工作負載產生了 SLOB 2.5.4.2。 SLOB (更愚蠢的 Oracle 基準測試) 是 Oracle 空間中知名的工作負載產生器，其設計目的是要使用 SGA 緩衝的實體 i/o 工作負載來壓力和測試 i/o 子系統。  

SLOB 2.5.4.2 不支援 (PDB) 的可插入資料庫。 因此，已將變更新增至 `setup.sh` 和腳本， `runit.sh` 以在其中加入 PDB 支援。  

下列各節將說明測試中使用的 SLOB 變數。

#### <a name="workload-80-select-20-update--random-io--slobconf-variables"></a>工作負載 80% SELECT，20% 更新 |隨機 i/o- `slob.conf` 變數   

`UPDATE_PCT=20`   
`SCAN_PCT=0`   
`RUN_TIME=600`   
`WORK_LOOP=0`   
`SCALE=75G`   
`SCAN_TABLE_SZ=50G`   
`WORK_UNIT=64`   
`REDO_STRESS=LITE`   
`LOAD_PARALLEL_DEGREE=12`   

#### <a name="workload-100-select--sequential-io--slobconf-variables"></a>工作負載 100% SELECT |順序 i/o – `slob.conf` 變數

`UPDATE_PCT=0`   
`SCAN_PCT=100`   
`RUN_TIME=600`   
`WORK_LOOP=0`   
`SCALE=75G`   
`SCAN_TABLE_SZ=50G`   
`WORK_UNIT=64`   
`REDO_STRESS=LITE`   
`LOAD_PARALLEL_DEGREE=12`   

### <a name="database"></a>資料庫

測試所使用的 Oracle 版本是 Oracle Database Enterprise Edition 19.3.0.0。

Oracle 參數如下所示：  
* `sga_max_size`: 4096M
* `sga_target`：4096
* `db_writer_processes`：12
* `awr_pdb_autoflush_enabled`： true
* `filesystemio_options`: SETALL
* `log_buffer`：134217728

已為 SLOB 資料庫建立 PDB。

下圖顯示名為 PERFIO 的資料表空間，大小為 600 GB (20 個資料檔案，每個) 建立 30 GB，以裝載四個 SLOB 使用者架構。 每個使用者架構的大小為 125 GB。

![Oracle 資料庫](../media/azure-netapp-files/performance-oracle-tablespace.png)  

## <a name="performance-metrics"></a>效能度量

其目標是要報告應用程式所經歷的 IO 效能。 因此，本文中的所有圖表都會使用 Oracle 資料庫透過其自動工作負載存放庫（ (AWR) 報表）回報的計量。 圖表中使用的計量如下：   

* **平均 IO 要求數/秒**   
    對應至負載設定檔區段中平均讀取 IO 要求數/秒和平均寫入 IO 要求數的總和
* **平均 IO MB/秒**   
    對應于負載設定檔區段中平均讀取 IO MB/秒和平均寫入 IO MB/秒的總和
* **平均讀取延遲**   
    對應至 Oracle 等候事件「資料庫檔案連續讀取」的平均延遲（以毫秒為單位）
* **執行緒/架構的數目**   
    對應于每個使用者架構的 SLOB 執行緒數目

## <a name="performance-measurement-results"></a>效能測量結果  

本節說明效能測量結果。

### <a name="linux-knfs-client-vs-oracle-direct-nfs"></a>Linux kNFS 用戶端與 Oracle Direct NFS

此案例正在 Azure VM 上執行，Standard_D32s_v3 (Intel E5-2673 v4 @ 2.30 GHz) 。 工作負載是 75% SELECT 和25% 更新，大多是隨機 i/o，而資料庫緩衝區的點擊率為 ~ 7.5%。 

如下圖所示，Oracle DNFS 用戶端最多可提供 2.8 x 比一般 Linux kNFS 用戶端更多的輸送量：  

![與 Oracle Direct NFS 相較之下的 Linux kNFS 用戶端](../media/azure-netapp-files/performance-oracle-kfns-compared-dnfs.png)  

下圖顯示讀取作業的延遲曲線。 在此內容中，kNFS 用戶端的瓶頸是在用戶端與 NFS 伺服器之間建立的單一 NFS TCP 通訊端連線， (Azure NetApp Files 磁片區) 。  

![與 Oracle Direct NFS 延遲曲線相較之下的 Linux kNFS 用戶端](../media/azure-netapp-files/performance-oracle-latency-curve.png)  

DNFS 用戶端由於能夠建立數百個 TCP 通訊端連線，因此能夠利用平行處理原則，因此每秒可推送更多 IO 要求。 如 [Azure NetApp Files](#anf_config)設定中所述，每個額外的容量 TiB 都允許額外的 128MiB/秒頻寬。 DNFS 上面鋪會以1個 GiB/s 的輸送量為上限，也就是 8 TiB 容量選擇所加諸的限制。 有更多的容量，則會提高輸送量。

輸送量只是其中一項考慮。 另一個考慮是延遲，這對使用者體驗有主要影響。 如下圖所示，kNFS 的延遲增加可能會比使用 DNFS 快得多。 

![與 Oracle Direct NFS 讀取延遲相較之下的 Linux kNFS 用戶端](../media/azure-netapp-files/performance-oracle-read-latency.png)  

長條圖提供資料庫延遲的絕佳見解。 下圖提供從記錄的「資料庫檔案連續讀取」的觀點來看，在最高並行資料點上使用 DNFS (32 執行緒/架構) 的完整觀點。 如下圖所示，47% 的所有讀取作業都是在512微秒和1000微秒之間接受，而所有讀取作業的90% 在低於2毫秒的延遲時間提供。

![與 Oracle Direct NFS 長條圖相較之下的 Linux kNFS 用戶端](../media/azure-netapp-files/performance-oracle-histogram-read-latency.png)  

總而言之，在改善 NFS 上 Oracle 資料庫實例的效能時，DNFS 是必須具備的結果。

### <a name="single-volume-performance-limits"></a>單一磁片區效能限制

本節說明單一磁片區具有隨機 i/o 和連續 i/o 的效能限制。 

#### <a name="random-io"></a>隨機 i/o

DNFS 能夠耗用的頻寬遠超過 8 TB Azure NetApp Files 效能配額所提供的頻寬。 藉由將 Azure NetApp Files 磁片區容量提高到 16 TiB （這是瞬間變更），磁片區頻寬的數量會從 1024 MiB/秒增加到 2048 MiB/秒。 

下圖顯示 80% select 和20% 更新工作負載的設定，以及資料庫緩衝區點擊率為8% 的設定。 SLOB 能夠將單一磁片區驅動到每秒200000個 NFS i/o 要求。 考慮每項作業的大小為 8 KiB，受測試的系統可以傳遞 ~ 200000 IO 要求/秒或 1600 MiB/秒。
 
![Oracle DNFS 輸送量](../media/azure-netapp-files/performance-oracle-dnfs-throughput.png)  

下列讀取延遲曲線會顯示，當讀取輸送量增加時，延遲會在 1-ms 行的正下方增加，並在大約165000平均讀取 IO 要求數/秒（平均讀取延遲為 ~ 1.3 毫秒）到達曲線的 knee brace。  對於 Azure 雲端中幾乎任何其他技術所無法達成的 i/o 速率，此值是非常驚人的延遲值。 

![Oracle DNFS 延遲曲線](../media/azure-netapp-files/performance-oracle-dnfs-latency-curve.png)  

#### <a name="sequential-io"></a>順序 i/o  

如下圖所示，並非所有的 i/o 都是隨機的，因為它會考慮使用 RMAN 備份或完整資料表掃描，例如，當工作負載需要的頻寬可達到時。  使用先前所述的相同設定，但將磁片區調整大小為 32 TiB，下圖顯示單一 Oracle DB 實例可驅動高達 3900 MB/秒的輸送量，非常接近 Azure NetApp Files 磁片區的效能配額（32 TB (128 MB/s * 32 = 4096 MB/s) ）。

![Oracle DNFS i/o](../media/azure-netapp-files/performance-oracle-dnfs-io.png)  

總而言之，Azure NetApp Files 可協助您將 Oracle 資料庫帶到雲端。 它會在資料庫要求時提供效能。 您可以隨時動態且無中斷地調整音量配額的大小。

## <a name="next-steps"></a>後續步驟

- [適用於Azure NetApp Files 的效能基準測試建議](azure-netapp-files-performance-metrics-volumes.md)
- [Linux 的效能評定](performance-benchmarks-linux.md)