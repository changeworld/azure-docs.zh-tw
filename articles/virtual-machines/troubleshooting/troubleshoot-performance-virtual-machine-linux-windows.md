---
title: 針對 Linux 或 Windows 上的 Azure 虛擬機器效能進行疑難排解
description: 本文說明虛擬機器 (VM) 透過監視和觀察瓶頸的一般效能疑難排解，並針對可能發生的問題提供可能的補救措施。
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: top-support-issue, azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 09/18/2019
ms.author: v-miegge
ms.openlocfilehash: 53fd2332224d903c5a4b33563470cf3569f82b13
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "86526651"
---
# <a name="troubleshoot-azure-virtual-machine-performance-on-linux-or-windows"></a>針對 Linux 或 Windows 上的 Azure 虛擬機器效能進行疑難排解

本文說明虛擬機器 (VM) 透過監視和觀察瓶頸的一般效能疑難排解，並針對可能發生的問題提供可能的補救措施。 除了監視之外，您也可以使用 Perfinsights，它可以提供具有最佳作法建議的報告，以及 IO/CPU/記憶體的主要瓶頸。 Perfinsights 適用于 Azure 中的 [Windows](./how-to-use-perfinsights.md) 和 [Linux](./how-to-use-perfinsights-linux.md) VM。

本文將逐步解說如何使用監視來診斷效能瓶頸。

## <a name="enabling-monitoring"></a>啟用監視

### <a name="azure-iaas-virtual-machine-monitoring"></a>Azure IAAS 虛擬機器監視

若要監視來賓 VM，請使用 Azure VM 監視，這會向您發出特定高階資源條件的警示。 若要檢查是否已啟用 VM 診斷，請參閱 [Azure 資源記錄檔總覽](../../azure-monitor/learn/tutorial-resource-logs.md)。 如果您看到下列內容，表示您很可能未啟用診斷：

![未啟用監視](media/troubleshoot-performance-virtual-machine-linux-windows/1-virtual-machines-monitoring-not-enabled.png)
 
### <a name="enable-vm-diagnostics-through-microsoft-azure-portal"></a>透過 microsoft Azure 入口網站啟用 VM 診斷

若要啟用 VM 診斷：

1. 移至 VM
2. 按一下 [**診斷設定**]
3. 選取儲存體帳戶，然後按一下 [ **啟用來賓層級監視**]。

   ![按一下 [設定]，然後按一下 [診斷]](media/troubleshoot-performance-virtual-machine-linux-windows/2-virtual-machines-diagnostics.png)

您可以在 [**診斷設定**] 下的 [**代理**程式] 索引標籤中，檢查用於診斷設定的儲存體帳戶。

![檢查儲存體帳戶](media/troubleshoot-performance-virtual-machine-linux-windows/3-check-storage-account.png)

### <a name="enable-storage-account-diagnostics-through-azure-portal"></a>透過 Azure 入口網站啟用儲存體帳戶診斷

當我們想要分析 Azure 中虛擬機器的 IO 效能時，儲存體是非常重要的一層。 針對儲存體相關計量，我們需要啟用診斷，以做為額外的步驟。 如果我們只想要分析儲存體相關的計數器，也可以啟用此功能。

1. 選取 VM，以找出您的 VM) 的儲存體帳戶 (或帳戶。 按一下 [ **設定**]，然後按一下 [ **磁片**：

   ![按一下 [設定]，然後按一下 [磁片]](media/troubleshoot-performance-virtual-machine-linux-windows/4-storage-disks-disks-selection.png)

2. 在入口網站中，移至 VM 的儲存體帳戶 (或帳戶) ，然後執行下列步驟：

   1. 針對您在上述步驟中找到的儲存體帳戶，按一下 [總覽]。
   2. 會顯示預設計量。 

    ![預設度量](media/troubleshoot-performance-virtual-machine-linux-windows/5-default-metrics.png)

3. 按一下任何計量，它會顯示另一個具有設定和新增度量選項的分頁。

   ![Add metrics](media/troubleshoot-performance-virtual-machine-linux-windows/6-add-metrics.png)

若要設定這些選項：

1.  選取 [計量]。
2.  選取 **資源** (儲存體帳戶) 。
3.  選取 **命名空間**
4.  選取 [ **度量**]。
5.  選取**匯總**類型
6.  您可以在儀表板上釘選此視圖。

## <a name="observing-bottlenecks"></a>觀察瓶頸

當我們完成所需計量的初始設定程式，並針對 VM 和相關儲存體帳戶啟用診斷之後，我們可以轉移至分析階段。

### <a name="accessing-the-monitoring"></a>存取監視

選取您要調查的 Azure VM，然後選取 [ **監視**]。

![選取監視](media/troubleshoot-performance-virtual-machine-linux-windows/7-select-monitoring.png)
 
### <a name="timelines-of-observation"></a>觀察的時間軸

若要識別您是否有任何資源瓶頸，請檢查您的資料。 如果您發現您的電腦已正常運作，但已回報效能最近降級，請在問題發生期間和之後，檢查包含效能度量資料的時間範圍。

### <a name="check-for-cpu-bottleneck"></a>檢查 CPU 瓶頸

![檢查 CPU 瓶頸](media/troubleshoot-performance-virtual-machine-linux-windows/8-cpu-bottleneck-time-range.png)

1. 編輯圖形。
2. 設定時間範圍。
3. 然後，您必須新增計數器： CPU% 的來賓 OS
4. 儲存。

### <a name="cpu-observe-trends"></a>CPU 觀察趨勢

查看效能問題時，請留意趨勢，並瞭解它們是否會對您造成影響。 在接下來的幾節中，我們將使用入口網站中的監視圖形來顯示趨勢。 它們也適用于在相同時間內交叉參考差異的資源行為。 若要自訂圖形，請按一下 [ [Azure 監視器資料平臺](../../azure-monitor/platform/data-platform.md)]。

變高–變高可能與排程的工作/已知事件相關。 如果您可以識別工作，請判斷工作是否在所需的效能層級執行。 如果可接受效能，您可能不需要增加資源。

尖峰和固定-通常表示新的工作負載。 如果它不是可辨識的工作負載，請在 VM 中啟用監視，以找出哪些處理常式 (或進程) 造成此行為。 在辨識程式之後，請判斷增加的耗用量是因為程式碼效率不佳或正常使用量而造成。 如果正常耗用量，請決定進程是否會在所需的效能層級上運作。

常數–判斷您的 VM 是否永遠在此層級執行，或是否只有在啟用診斷之後才在該層級上執行。 若是如此，請找出造成問題的程式 (或處理常式) ，然後考慮新增更多資源。

穩定增加–耗用量的持續增加通常是效率不佳的程式碼，或是處理更多使用者工作負載的進程。

### <a name="high-cpu-utilization-remediation"></a>高 CPU 使用率補救

如果您的應用程式或進程未以正確的效能層級執行，而且您看到 95% + CPU 使用率常數，則可以執行下列其中一項作業：

* 立即緩解-將 VM 的大小增加為具有更多核心的大小
* 瞭解問題–找出應用程式/處理常式，並據以進行疑難排解。

如果您已增加 VM，且 CPU 仍在執行95%，請判斷這項設定是否為可接受的層級提供更佳的效能或更高的應用程式輸送量。 如果不是，請針對個別 application\process. 進行疑難排解

您可以使用適用于 [Windows](./how-to-use-perfinsights.md) 或 [Linux](./how-to-use-perfinsights-linux.md) 的 Perfinsights 來分析正在驅動 CPU 耗用量的進程。 

## <a name="check-for-memory-bottleneck"></a>檢查記憶體瓶頸

若要查看度量：

1. 加入區段。
2. 新增圖格。
3. 開啟資源庫。
4. 選取 [記憶體使用量]，然後拖曳。 當磚停駐時，以滑鼠右鍵按一下並選取 [ **6x4**]。

### <a name="memory-observe-trends"></a>記憶體觀察趨勢

記憶體使用量會顯示 VM 耗用多少記憶體。 瞭解趨勢，以及它是否對應到您看到問題的時間。 您應一律擁有超過 100 MB 的可用記憶體。

尖峰和常數/常數穩定耗用量-高記憶體使用量可能不會造成效能不佳，因為某些應用程式（例如關係資料庫引擎）會配置大量的記憶體，而這種使用方式可能不重要。 但是，如果有多個記憶體中的應用程式，您可能會發現記憶體爭用效能不佳，而導致修剪和分頁/交換至磁片。 效能不佳通常是應用程式效能影響的明顯原因。

穩定增加耗用量–可能的應用程式「準備」，這項耗用量在資料庫引擎啟動時很常見。 不過，它也可能是應用程式中記憶體流失的跡象。 識別應用程式，並瞭解是否預期行為。

分頁檔案使用方式-檢查您使用的是 Windows 分頁檔 (位於 D： \) 或 Linux 交換檔案 (位於 `/dev/sdb`) 的大量使用。 如果這些檔案以外的磁片區上沒有任何內容，請檢查這些磁片上的高讀取/寫入量。 此問題表示記憶體不足的情況。

### <a name="high-memory-utilization-remediation"></a>高記憶體使用量補救

若要解決高記憶體使用量，請執行下列任何一項工作：

* 如需立即的緩解或頁面或交換檔案使用方式，請將 VM 大小增加到一個具有更多記憶體的 VM 大小，然後再監視。
* 瞭解問題–找出應用程式/程式和疑難排解，以找出高耗用的記憶體應用程式。
* 如果您知道該應用程式，請查看記憶體配置是否可以有上限。

如果在升級至較大的 VM 之後，您發現您的持續穩定增加到100%，請找出應用程式/進程並進行疑難排解。

您可以使用適用于 [Windows](./how-to-use-perfinsights.md) 或 [Linux](./how-to-use-perfinsights-linux.md) 的 Perfinsights 來分析哪些進程正在驅動記憶體耗用量。 

## <a name="check-for-disk-bottleneck"></a>檢查磁碟瓶頸

若要檢查 VM 的儲存子系統，請使用 VM 診斷中的計數器，以及儲存體帳戶診斷，檢查 Azure VM 層級的診斷。

針對 VM 特定的疑難排解，您可以使用適用于 [Windows](./how-to-use-perfinsights.md) 或 [Linux](./how-to-use-perfinsights-linux.md)的 Perfinsights，這有助於分析哪個進程正在驅動 IO。 

請注意，我們沒有用於區域多餘和進階儲存體帳戶的計數器。 針對與這些計數器相關的問題，請提出支援案例。

### <a name="viewing-storage-account-diagnostics-in-monitoring"></a>在監視中查看儲存體帳戶診斷

若要處理下列專案，請在入口網站中移至 VM 的儲存體帳戶：

![在監視中查看儲存體帳戶診斷](media/troubleshoot-performance-virtual-machine-linux-windows/9-virtual-machine-storage-account.png)

1. 編輯監視圖形。
2. 設定時間範圍。
3. 新增下列步驟中所述的計數器。
4. 儲存變更。

### <a name="disk-observe-trends-standard-storage-only"></a>磁片只會觀察 (標準儲存體的趨勢) 

若要識別儲存體的問題，請查看儲存體帳戶診斷和 VM 診斷的效能計量。

針對以下每項檢查，在問題的時間範圍內發生問題時，尋找重要趨勢。

#### <a name="check-azure-storage-availability--add-the-storage-account-metric-availability"></a>檢查 Azure 儲存體可用性-新增儲存體帳戶度量：可用性

如果您看到可用性下降，可能是平臺發生問題，請檢查 [Azure 狀態](https://azure.microsoft.com/status/)。 如果沒有顯示任何問題，請提出新的支援要求。

#### <a name="check-for-azure-storage-timeout---add-the-storage-account-metrics"></a>檢查 Azure 儲存體超時：新增儲存體帳戶計量：

* ClientTimeOutError
* ServerTimeOutError
* AverageE2ELatency
* AverageServerLatency
* TotalRequests

* >timeouterror 計量中的值表示 IO 操作花費的時間太長，且超時。進行後續步驟將有助於找出可能的原因。

AverageServerLatency 會在 >timeouterrors 時同時增加，這可能是平臺問題。 在此情況下提出新的支援要求。

AverageE2ELatency 代表用戶端延遲。 確認應用程式如何執行 IOPS。 尋找增加或經常的高 TotalRequests 計量。 此度量代表 IOPS。 如果您要開始達到儲存體帳戶或單一 VHD 的限制，延遲可能與節流相關。

#### <a name="check-for-azure-storage-throttling---add-the-storage-account-metrics-throttlingerror"></a>檢查 Azure 儲存體節流設定-新增儲存體帳戶計量： ThrottlingError

節流的值表示您會在儲存體帳戶層級進行節流處理，這表示您已達到帳戶的 IOPS 限制。 您可以藉由檢查計量 **TotalRequests**來判斷是否達到 IOPs 閾值。

請注意，每個 VHD 的限制為 500 IOPS 或 60 Mb，但受限於每個儲存體帳戶 20000 IOPS 的累計限制。

使用此計量時，您無法分辨哪些 blob 造成節流，以及受其影響。 不過，您可以達到儲存體帳戶的 IOPS 或輸入/輸出限制。

若要識別您是否達到 IOPS 限制，請移至儲存體帳戶診斷並檢查 TotalRequests，查看您是否已接近 20000 TotalRequests。 識別模式中的變更、是否看到第一次的限制，或是否在特定時間發生這項限制。

使用標準儲存體下的新磁片供應專案時，IOPS 和輸送量限制可能不同，但標準儲存體帳戶的累計限制為 20000 IOPS (Premium 儲存體的帳戶或磁片層級) 有不同的限制。 深入瞭解不同的標準儲存體磁片供應專案，以及每個磁片的限制：

* [Windows 上 VM 磁片的擴充性和效能目標](../windows/disk-scalability-targets.md)。

#### <a name="references"></a>參考

* [Premium 頁面 blob 儲存體帳戶的擴充性和效能目標](../../storage/blobs/scalability-targets-premium-page-blobs.md)

儲存體帳戶的頻寬會以儲存體帳戶計量來測量： >totalingress 和 >totalegress。 根據不同的冗余和區欄位型別，頻寬有不同的閾值。

* [標準儲存體帳戶的延展性和效能目標](../../storage/common/scalability-targets-standard-account.md)

檢查 >totalingress 和 >totalegress，以瞭解儲存體帳戶冗余類型和區域的輸入和輸出限制。

檢查連接至 VM 之 Vhd 的輸送量限制。 新增 VM 計量磁片的讀取和寫入。

標準儲存體下的新磁片供應專案具有不同的 IOPS 和輸送量限制 (IOPS 不會針對每個 VHD) 公開。 查看資料，以查看您是否使用磁片讀取和寫入來達到 VHD (s) 的合併輸送量 MB 限制，然後將 VM 儲存體設定優化以調整超過單一 VHD 限制。 深入瞭解不同的標準儲存體磁片供應專案，以及每個磁片的限制：

* [Windows 上 VM 磁片的擴充性和效能目標](../windows/disk-scalability-targets.md)。

### <a name="high-disk-utilizationlatency-remediation"></a>高磁片使用量/延遲補救

減少用戶端延遲，並將 VM IO 優化以調整超過的 VHD 限制

* [在 Azure 中優化 Windows 的 IO](../../azure-sql/virtual-machines/windows/performance-guidelines-best-practices.md?toc=/azure/virtual-machines/windows/toc.json)

* [針對 Azure 中的 Linux 優化 IO](/archive/blogs/igorpag/azure-storage-secrets-and-linux-io-optimizations)

#### <a name="reduce-throttling"></a>減少節流

如果達到儲存體帳戶的上限，請在儲存體帳戶之間重新平衡 Vhd。 請參閱 [Azure 儲存體的擴充性和效能目標](../../storage/common/scalability-targets-standard-account.md)。

### <a name="increase-throughput-and-reduce-latency"></a>提高輸送量並減少延遲

如果您有延遲敏感性應用程式且需要高輸送量，請使用 DS 和 GS 系列 VM 將 Vhd 遷移至 Azure Premium 儲存體。

這些文章討論特定案例：

* [移轉到 Azure 進階儲存體](../windows/migrate-to-managed-disks.md)

* [搭配使用 Azure 進階儲存體與 SQL Server](/previous-versions/azure/virtual-machines/windows/sqlclassic/virtual-machines-windows-classic-sql-server-premium-storage)

## <a name="next-steps"></a>接下來的步驟

如果您在本文的任何時間點需要更多協助，請洽詢 [MSDN azure 和 Stack Overflow 論壇](https://azure.microsoft.com/support/forums/)上的 Azure 專家。

或者，提出 Azure 支援事件。 請移至 [Azure 支援網站](https://azure.microsoft.com/support/options/)，然後選取 [取得支援]。
