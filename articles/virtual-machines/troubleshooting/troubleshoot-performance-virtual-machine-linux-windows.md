---
title: 在 Linux 或 Windows 上對 Azure 虛擬機器性能進行故障排除
description: 本文介紹了虛擬機器 （VM） 通過監視和觀察瓶頸進行通用性能故障排除，並為可能出現的問題提供了可能的補救措施。
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
ms.openlocfilehash: 176b0634fe2c7ee2f47162e439c4ea16bde77a8a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75772613"
---
# <a name="troubleshoot-azure-virtual-machine-performance-on-linux-or-windows"></a>在 Linux 或 Windows 上對 Azure 虛擬機器性能進行故障排除

本文介紹了虛擬機器 （VM） 通過監視和觀察瓶頸進行通用性能故障排除，並為可能出現的問題提供了可能的補救措施。 除了監視之外，您還可以使用 Perfinsights，它可以提供包含最佳實踐建議和 IO/CPU/記憶體關鍵瓶頸的報告。 Perfinsights 可用於 Azure 中的[Windows](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/how-to-use-perfInsights)和[Linux](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/how-to-use-perfinsights-linux) VM。

本文將演練使用監視來診斷性能瓶頸。

## <a name="enabling-monitoring"></a>啟用監視

### <a name="azure-iaas-virtual-machine-monitoring"></a>Azure IAAS 虛擬機器監視

要監視來賓 VM，請使用 Azure VM 監視，這將提醒您某些高級資源條件。 要檢查是否已啟用 VM 診斷，請參閱[Azure 資源日誌概述](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-resource-logs)。 如果您看到以下內容，則很可能未啟用診斷：

![未啟用監視](media/troubleshoot-performance-virtual-machine-linux-windows/1-virtual-machines-monitoring-not-enabled.png)
 
### <a name="enable-vm-diagnostics-through-microsoft-azure-portal"></a>通過 Microsoft Azure 門戶啟用 VM 診斷

要啟用 VM 診斷：

1. 轉到 VM
2. 按一下**診斷設置**
3. 選擇存儲帳戶，然後按一下**啟用來賓級監視**。

   ![按一下"設置"，然後按一下診斷](media/troubleshoot-performance-virtual-machine-linux-windows/2-virtual-machines-diagnostics.png)

您可以在 **"診斷設置**"下從 **"代理"** 選項卡檢查用於診斷設置的存儲帳戶。

![檢查存儲帳戶](media/troubleshoot-performance-virtual-machine-linux-windows/3-check-storage-account.png)

### <a name="enable-storage-account-diagnostics-through-azure-portal"></a>通過 Azure 門戶啟用存儲帳戶診斷

當我們打算分析 Azure 中的虛擬機器的 IO 性能時，存儲是一個非常重要的層。 對於存儲相關指標，我們需要啟用診斷作為附加步驟。 如果我們只想分析與存儲相關的計數器，也可以啟用此功能。

1. 通過選擇 VM 確定 VM 正在使用哪些存儲帳戶（或帳戶）。 按一下 **"設置"，** 然後按一下 **"磁片**" ：

   ![按一下"設置"，然後按一下磁片](media/troubleshoot-performance-virtual-machine-linux-windows/4-storage-disks-disks-selection.png)

2. 在門戶中，轉到 VM 的存儲帳戶（或帳戶）並完成以下步驟：

   1. 按一下使用上面的步驟找到的存儲帳戶的概述。
   2. 將顯示預設指標。 

    ![預設度量](media/troubleshoot-performance-virtual-machine-linux-windows/5-default-metrics.png)

3. 按一下任何指標，這將顯示另一個包含更多選項來配置和添加指標的邊欄選項卡。

   ![Add metrics](media/troubleshoot-performance-virtual-machine-linux-windows/6-add-metrics.png)

要配置這些選項：

1.  選擇**指標**。
2.  選擇**資源**（存儲帳戶）。
3.  選擇**命名空間**
4.  選擇**指標**。
5.  選擇**聚合**類型
6.  您可以將此視圖固定在儀表板上。

## <a name="observing-bottlenecks"></a>觀察瓶頸

完成所需指標的初始設置過程，並在啟用 VM 和相關存儲帳戶的診斷後，我們可以轉移到分析階段。

### <a name="accessing-the-monitoring"></a>訪問監視

選擇要調查的 Azure VM 並選擇 **"監視**"。

![選擇監視](media/troubleshoot-performance-virtual-machine-linux-windows/7-select-monitoring.png)
 
### <a name="timelines-of-observation"></a>觀測時間軸

要確定是否有任何資源瓶頸，請查看資料。 如果您發現電腦運行良好，但已報告性能最近下降，請查看在報告更改之前、問題期間和之後包含性能指標資料的時間範圍。

### <a name="check-for-cpu-bottleneck"></a>檢查 CPU 瓶頸

![檢查 CPU 瓶頸](media/troubleshoot-performance-virtual-machine-linux-windows/8-cpu-bottleneck-time-range.png)

1. 編輯圖形。
2. 設置時間範圍。
3. 然後，您需要在計數器中添加：CPU 百分比客體作業系統
4. 儲存。

### <a name="cpu-observe-trends"></a>CPU 觀察趨勢

在查看性能問題時，瞭解趨勢並瞭解這些趨勢是否影響您。 在下一節中，我們將使用門戶中的"監視"圖表來顯示趨勢。 它們對於在同一時間段內交叉引用差異資源行為也很有用。 要自訂圖形，請按一下[Azure 監視器資料平臺](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform)。

尖峰 – 尖峰可能與計畫的任務/已知事件有關。 如果可以標識任務，請確定任務是否在所需的性能級別運行。 如果性能可以接受，則可能不需要增加資源。

向上和持續 = 通常表示新的工作負載。 如果它不是公認的工作負載，則在 VM 中啟用監視，以找出導致該行為的進程（或進程）。 識別流程後，確定增加的消耗量是由低效代碼還是正常消耗引起的。 如果正常消耗，則確定流程是否在所需的性能級別運行。

常量 - 確定 VM 是否始終在此級別運行，或者它是否僅在啟用診斷後才在該級別運行。 如果是這樣，請確定導致問題的進程（或進程），並考慮添加更多資源。

穩步增長 – 持續增加消耗通常是低效的代碼，或者是承擔更多使用者工作負載的過程。

### <a name="high-cpu-utilization-remediation"></a>CPU 利用率高修正

如果應用程式或進程未以正確的性能級別運行，並且看到 95% = CPU 利用率常數，則可以執行以下任一任務：

* 為立即緩解 - 將 VM 的大小增加到具有更多內核的大小
* 瞭解問題 – 找到應用程式/流程並相應地進行故障排除。

如果增加了 VM，並且 CPU 仍在運行 95%，則確定此設置是提供更好的性能還是更高的應用程式輸送量到可接受的水準。 如果沒有，請對單個應用程式\進程進行故障排除。

您可以使用[Windows](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/how-to-use-perfInsights)或[Linux](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/how-to-use-perfinsights-linux)的 Perfinsights 來分析哪個進程在推動 CPU 限定。 

## <a name="check-for-memory-bottleneck"></a>檢查記憶體瓶頸

要查看指標：

1. 添加節。
2. 添加磁貼。
3. 打開庫。
4. 選擇"記憶體使用方式"並拖動。 當磁貼停靠時，按右鍵並選擇**6x4**。

### <a name="memory-observe-trends"></a>記憶觀察趨勢

記憶體使用方式顯示 VM 正在消耗多少記憶體。 瞭解趨勢及其是否映射到您看到問題的時間。 您應始終具有超過 100 MB 的可用記憶體。

峰值和恒定/恒定穩定消耗 - 高記憶體利用率可能不是性能不佳的原因，因為某些應用程式（如關係資料庫引擎）分配了大量記憶體，並且此利用率可能並不顯著。 但是，如果有多個需要記憶體的應用程式，您可能會看到記憶體爭用導致修剪和分頁/交換到磁片的性能較差。 這種性能差通常是應用程式性能影響的明顯原因。

不斷增加的消耗量 – 一個可能的應用程式"預熱"，這種消耗在資料庫引擎啟動中很常見。 不過，它也可能是應用程式中記憶體流失的跡象。 確定應用程式並瞭解該行為是否預期。

頁面或交換檔使用方式 - 檢查您是否正在大量使用 Windows 分頁檔（位於\)D： 或 Linux`/dev/sdb`交換檔（位於 上）。 如果這些卷上除了這些檔之外，沒有任何內容，請檢查這些磁片上的高讀取/寫入。 此問題表示記憶體不足。

### <a name="high-memory-utilization-remediation"></a>高記憶體利用率修正

要解決高記憶體利用率，應執行以下任一任務：

* 要立即進行救濟或頁面或交換檔使用方式 - 將 VM 大小增加到具有更多記憶體的 VM 大小，然後進行監視。
* 瞭解問題 – 查找應用程式/進程和疑難排解，以確定高消耗記憶體應用程式。
* 如果您知道應用程式，請參閱是否可以限制記憶體分配。

如果在升級到更大的 VM 後，您發現您仍有持續穩步上升，直到 100%， 確定應用程式/進程和故障排除。

您可以使用[Windows](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/how-to-use-perfInsights)或[Linux](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/how-to-use-perfinsights-linux)的 Perfinsights 來分析哪個進程推動了記憶體消耗。 

## <a name="check-for-disk-bottleneck"></a>檢查磁碟瓶頸

要檢查 VM 的儲存子系統，請使用 VM 診斷中的計數器和存儲帳戶診斷中計數器檢查 Azure VM 級別的診斷。

對於特定于 VM 的故障排除，可以使用[適用于 Windows](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/how-to-use-perfInsights)或[Linux](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/how-to-use-perfinsights-linux)的 Perfinsights，這有助於分析哪個進程推動了 IO。 

請注意，我們沒有區域冗余和高級存儲帳戶的計數器。 對於與這些計數器相關的問題，提出支援案例。

### <a name="viewing-storage-account-diagnostics-in-monitoring"></a>在監視中查看存儲帳戶診斷

要處理以下專案，請參閱門戶中 VM 的存儲帳戶：

![在監視中查看存儲帳戶診斷](media/troubleshoot-performance-virtual-machine-linux-windows/9-virtual-machine-storage-account.png)

1. 編輯監視圖。
2. 設置時間範圍。
3. 添加以下步驟中描述的計數器。
4. 儲存變更。

### <a name="disk-observe-trends-standard-storage-only"></a>磁片觀察趨勢（僅限標準存儲）

要識別存儲問題，請查看存儲帳戶診斷和 VM 診斷中的性能指標。

對於下面的每一次檢查，在問題的時間範圍內出現問題時，查找關鍵趨勢。

#### <a name="check-azure-storage-availability--add-the-storage-account-metric-availability"></a>檢查 Azure 存儲可用性 • 添加存儲帳戶指標：可用性

如果看到可用性下降，則平臺可能有問題，請檢查[Azure 狀態](https://azure.microsoft.com/status/)。 如果那裡未顯示問題，請提出新的支援請求。

#### <a name="check-for-azure-storage-timeout---add-the-storage-account-metrics"></a>檢查 Azure 存儲超時 - 添加存儲帳戶指標：

* 用戶端逾時錯誤
* 伺服器逾時錯誤
* AverageE2ELatency
* AverageServerLatency
* TotalRequests

@TimeOutError 指標中的值表示 IO 操作耗時過長且超時。完成後續步驟將有助於確定潛在原因。

平均伺服器延遲在逾時錯誤時同時增加可能是一個平臺問題。 在此情況下提出新的支援請求。

平均E2E延遲表示用戶端延遲。 驗證應用程式如何執行 IOPS。 查找增加或持續高的"總請求"指標。 此指標表示 IOPS。 如果您開始達到存儲帳戶或單個 VHD 的限制，則延遲可能與限制有關。

#### <a name="check-for-azure-storage-throttling---add-the-storage-account-metrics-throttlingerror"></a>檢查 Azure 存儲限制 - 添加存儲帳戶指標：限制錯誤

限制值表示您在存儲帳戶級別受到限制，這意味著您達到帳戶的 IOPS 限制。 您可以通過檢查指標**Total 請求**來確定是否達到 IP 閾值。

請注意，每個 VHD 的限制為 500 IOPS 或 60 MBits，但受每個存儲帳戶的累積限制 20000 IOPS 的約束。

使用此指標，您無法判斷哪個 Blob 導致限制，哪些受其影響。 但是，您要麼達到 IOPS 或存儲帳戶的入口/出口限制。

要確定是否達到 IOPS 限制，請進入存儲帳戶診斷並檢查"總請求"，查看是否接近 20000 個總計請求。 確定模式中的更改、是否首次看到限制，或者此限制是否在特定時間發生。

對於標準存儲下的新磁片產品，IOPS 和輸送量限制可能不同，但標準存儲帳戶的累積限制為 20000 IOPS（高級存儲在帳戶或磁片級別具有不同的限制）。 閱讀有關不同標準存儲磁片產品/磁片和每個磁片限制的更多內容：

* [Windows 上 VM 磁片的可伸縮性和性能目標](https://docs.microsoft.com/azure/virtual-machines/windows/disk-scalability-targets)。

#### <a name="references"></a>參考

* [高級頁面 Blob 存儲帳戶的可擴充性和性能目標](../../storage/blobs/scalability-targets-premium-page-blobs.md)

存儲帳戶的頻寬由存儲帳戶指標：總入口和總出口來衡量。 根據冗余類型和區域的不同，頻寬閾值不同。

* [標準儲存體帳戶的延展性和效能目標](../../storage/common/scalability-targets-standard-account.md)

根據存儲帳戶冗余類型和地區的入口和出口限制檢查"總入口"和"總出口"。

檢查附加到 VM 的 VHD 的輸送量限制。 添加 VM 指標磁片讀取和寫入。

標準存儲下的新磁片產品具有不同的 IOPS 和輸送量限制（IOPS 不會根據 VHD 公開）。 查看資料，查看是否達到 VHD 級別 VHD 組合輸送量 MB 的限制，使用磁片讀取和寫入，然後優化 VM 存儲配置以擴展超過單個 VHD 限制。 閱讀有關不同標準存儲磁片產品/磁片和每個磁片限制的更多內容：

* [Windows 上 VM 磁片的可伸縮性和性能目標](https://docs.microsoft.com/azure/virtual-machines/windows/disk-scalability-targets)。

### <a name="high-disk-utilizationlatency-remediation"></a>高磁片利用率/延遲修正

減少用戶端延遲並優化 VM IO 以擴展超過 VHD 限制

* [優化 Azure 中 Windows 的 IO](https://azure.microsoft.com/documentation/articles/virtual-machines-sql-server-performance-best-practices/)

* [在 Azure 中優化 Linux 的 IO](https://blogs.msdn.microsoft.com/igorpag/2014/10/23/azure-storage-secrets-and-linux-io-optimizations/)

#### <a name="reduce-throttling"></a>減少限制

如果達到存儲帳戶的上限，則在存儲帳戶之間重新平衡 VHD。 請參閱[Azure 存儲可伸縮性和性能目標](https://azure.microsoft.com/documentation/articles/storage-scalability-targets/)。

### <a name="increase-throughput-and-reduce-latency"></a>提高輸送量並減少延遲

如果您有延遲敏感應用程式並且需要高輸送量，請使用 DS 和 GS 系列 VM 將 VHD 遷移到 Azure 高級存儲。

這些文章討論具體方案：

* [移轉到 Azure 進階儲存體](https://azure.microsoft.com/documentation/articles/storage-migration-to-premium-storage/)

* [將 Azure 高級存儲與 SQL 伺服器一起使用](https://azure.microsoft.com/documentation/articles/virtual-machines-sql-server-use-premium-storage/)

## <a name="next-steps"></a>後續步驟

如果本文中的任何一點都需要更多説明，請與[MSDN Azure 和堆疊溢位論壇上的](https://azure.microsoft.com/support/forums/)Azure 專家聯繫。

或者，提交 Azure 支援事件。 轉到[Azure 支援網站](https://azure.microsoft.com/support/options/)並選擇 **"獲取支援**"。
