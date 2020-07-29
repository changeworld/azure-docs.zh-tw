---
title: 監視 Azure 檔案同步 | Microsoft Docs
description: 如何監視 Azure 檔案同步。
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 06/28/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 0232a0c6526d6dcdfec86dedec437c71e7e21080
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85515210"
---
# <a name="monitor-azure-file-sync"></a>監視 Azure 檔案同步

使用 Azure 檔案同步，將組織的檔案共用集中在 Azure 檔案服務中，同時保有內部部署檔案伺服器的彈性、效能及相容性。 Azure 檔案同步會將 Windows Server 轉換成 Azure 檔案共用的快速快取。 您可以使用 Windows Server 上可用的任何通訊協定來從本機存取資料，包括 SMB、NFS 和 FTPS。 您可以視需要存取多個散佈於世界各地的快取。

本文說明如何使用 Azure 監視器、儲存體同步服務和 Windows Server 監視您的 Azure 檔案同步部署。

目前可使用下列監視選項。

## <a name="azure-monitor"></a>Azure 監視器

使用[Azure 監視器](https://docs.microsoft.com/azure/azure-monitor/overview)來查看計量，並設定同步處理、雲端階層處理和伺服器連線的警示。  

### <a name="metrics"></a>計量

依預設會啟用 Azure 檔案同步的計量，並且每 15 分鐘傳送至 Azure 監視器一次。

若要在 Azure 監視器中查看 Azure 檔案同步計量，請選取**儲存體同步服務**資源類型。

以下是 Azure 監視器中提供的 Azure 檔案同步計量：

| 度量名稱 | Description |
|-|-|
| 同步的位元組 | 傳輸的資料大小 (上傳和下載)。<br><br>單位：位元組<br>匯總類型：總和<br>適用的維度：伺服器端點名稱、同步方向、同步組名 |
| 雲端階層處理重新叫用 | 重新叫用的資料大小。<br><br>**注意**：未來將會移除此度量。 使用 [雲端階層處理重新叫用大小] 計量來監視重新叫用的資料大小。<br><br>單位：位元組<br>匯總類型：總和<br>適用的維度：伺服器名稱 |
| 雲端階層處理重新叫用大小 | 重新叫用的資料大小。<br><br>單位：位元組<br>匯總類型：總和<br>適用的維度：伺服器名稱、同步組名 |
| 雲端階層處理重新叫用大小 (依應用程式) | 應用程式所回收的資料大小。<br><br>單位：位元組<br>匯總類型：總和<br>適用的維度：應用程式名稱、伺服器名稱、同步組名 |
| 雲端階層處理重新叫用輸送量 | 資料重新叫用輸送量的大小。<br><br>單位：位元組<br>匯總類型：總和<br>適用的維度：伺服器名稱、同步組名 |
| 檔案無法同步 | 無法同步的檔案計數。<br><br>單位：Count<br>匯總類型：總和<br>適用的維度：伺服器端點名稱、同步方向、同步組名 |
| 同步的檔案 | 傳輸的檔案計數 (上傳和下載)。<br><br>單位：Count<br>匯總類型：總和<br>適用的維度：伺服器端點名稱、同步方向、同步組名 |
| 伺服器線上狀態 | 從伺服器接收到的活動訊號計數。<br><br>單位：Count<br>彙總類型：最大值<br>適用的維度：伺服器名稱 |
| 同步工作階段結果 | 同步工作階段結果 (1=成功的同步工作階段；0=失敗的同步工作階段)<br><br>單位：Count<br>匯總類型：最大值<br>適用的維度：伺服器端點名稱、同步方向、同步組名 |

### <a name="alerts"></a>警示

若要在 Azure 監視器中設定警示，請選取儲存體同步服務，然後選取要用於警示的[Azure 檔案同步](https://docs.microsoft.com/azure/storage/files/storage-sync-files-monitoring#metrics)計量。  

下表列出一些要監視的範例案例，以及要用於警示的適當計量：

| 狀況 | 用於警示的度量 |
|-|-|
| 入口網站中的伺服器端點健康情況 = 錯誤 | 同步工作階段結果 |
| 檔案無法同步處理到伺服器或雲端端點 | 檔案無法同步 |
| 已註冊的伺服器無法與儲存體同步服務進行通訊 | 伺服器線上狀態 |
| 雲端階層處理重新叫用大小已超過一天的500GiB  | 雲端階層處理重新叫用大小 |

若要深入瞭解如何在 Azure 監視器中設定警示，請參閱[Microsoft Azure 中的警示總覽]( https://docs.microsoft.com/azure/azure-monitor/platform/alerts-overview)。

## <a name="storage-sync-service"></a>儲存體同步服務

若要查看已註冊的伺服器健康情況、伺服器端點健康情況和計量，請移至 Azure 入口網站中的儲存體同步服務。 您可以在 [**同步群組**] 分頁的 [**已註冊的伺服器**] 分頁和伺服器端點健全狀況中，查看已註冊的伺服器健全狀況。

### <a name="registered-server-health"></a>已註冊的伺服器健全狀況

- 如果**已註冊的伺服器**狀態為 [**線上**]，伺服器就會成功與服務進行通訊。
- 如果 [**已註冊的伺服器**] 狀態顯示為 [**離線**]，請確認伺服器上的儲存體同步監視器（AzureStorageSyncMonitor.exe）進程正在執行。 如果伺服器位於防火牆或 proxy 後方，請參閱[這篇文章](https://docs.microsoft.com/azure/storage/files/storage-sync-files-firewall-and-proxy)以設定防火牆和 proxy。

### <a name="server-endpoint-health"></a>伺服器端點健全狀況

- 入口網站中的伺服器端點健康情況，取決於伺服器上的遙測事件記錄 (識別碼 9102 和 9302) 中所記錄的同步事件。 如果同步會話因為暫時性錯誤而失敗（例如取消錯誤），則只要目前的同步會話正在進行中，同步處理可能仍會在入口網站中顯示為狀況良好。 事件識別碼9302是用來判斷是否已套用檔案。 如需詳細資訊，請參閱[同步處理健康](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#broken-sync)情況和[同步處理進度](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#how-do-i-monitor-the-progress-of-a-current-sync-session)。
- 如果入口網站顯示同步錯誤，因為同步處理未進行進度，請參閱[疑難排解檔](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#common-sync-errors)以取得指導方針。

### <a name="metric-charts"></a>度量圖表

- 下列計量圖表可在儲存體同步服務入口網站中看到：

  | 度量名稱 | Description | 分頁名稱 |
  |-|-|-|
  | 同步的位元組 | 傳輸的資料大小 (上傳和下載) | 同步群組、伺服器端點 |
  | 雲端階層處理重新叫用 | 重新叫用的資料大小 | 已註冊的伺服器 |
  | 檔案無法同步 | 無法同步的檔案計數 | 伺服器端點 |
  | 同步的檔案 | 傳輸的檔案計數 (上傳和下載) | 同步群組、伺服器端點 |
  | 伺服器線上狀態 | 從伺服器接收到的活動訊號計數 | 已註冊的伺服器 |

- 若要深入瞭解，請參閱[Azure 監視器](https://docs.microsoft.com/azure/storage/files/storage-sync-files-monitoring#azure-monitor)。

  > [!Note]  
  > 儲存體同步服務入口網站中的圖表時間範圍為 24 小時。 若要檢視不同的時間範圍或維度，請使用 Azure 監視器。

## <a name="windows-server"></a>Windows Server

在 Windows Server 上，您可以查看雲端階層處理、已註冊的伺服器，以及同步處理健康情況。

### <a name="event-logs"></a>事件記錄檔

使用伺服器上的遙測事件記錄，可監視已註冊的伺服器、同步、和雲端階層處理健康情況。 遙測事件記錄檔位於 [*應用程式] 和 [and services\microsoft\filesync\agent*] 底下的事件檢視器。

同步健全狀況：

- 同步會話完成後，會記錄事件識別碼9102。 使用此事件判斷同步會話是否成功（**HResult = 0**），以及是否有每個專案的同步錯誤。 如需詳細資訊，請參閱[同步處理健康](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#broken-sync)情況和[每個專案的錯誤](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#how-do-i-see-if-there-are-specific-files-or-folders-that-are-not-syncing)檔。

  > [!Note]  
  > 有時候同步會話會整體失敗，或具有非零的 PerItemErrorCount。 不過，它們仍會繼續進行，而某些檔案也會順利同步。 您可以在套用的欄位（例如看出 appliedfilecount、AppliedDirCount、AppliedTombstoneCount 和 AppliedSizeBytes）中看到此功能。 這些欄位會告訴您會話成功的程度。 如果您在某個資料列中看到多個同步會話失敗，而且其已套用計數增加，請在開啟支援票證之前，提供同步處理時間再試一次。

- 如果有使用中的同步工作階段，則會每隔 5 到 10 分鐘記錄事件識別碼 9302 一次。 使用此事件判斷目前的同步會話是否正在進行中（**AppliedItemCount > 0**）。 如果同步處理未進行進度，同步會話最後應該會失敗，而且會記錄事件識別碼9102並產生錯誤。 如需詳細資訊，請參閱[同步處理進度檔](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#how-do-i-monitor-the-progress-of-a-current-sync-session)。

已註冊的伺服器健全狀況：

- 當伺服器查詢服務中的作業時，將會每 30 秒記錄事件識別碼 9301 一次。 如果 GetNextJob 以**status = 0**完成，伺服器就能夠與服務通訊。 如果 GetNextJob 完成並出現錯誤，請參閱[疑難排解檔](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#common-sync-errors)以取得指導方針。

雲端階層處理健全狀況：

- 若要監視伺服器上的分層活動，請使用遙測事件記錄檔中的事件識別碼9003、9016和9029，其位於 [*應用程式] 和 [and services\microsoft\filesync\agent*] 底下的事件檢視器中。

  - 事件識別碼 9003 會提供伺服器端點的錯誤分布。 例如：總錯誤計數和 ErrorCode。 每個錯誤碼會記錄一個事件。
  - 事件識別碼 9016 會提供磁碟區的建立映像結果。 例如：可用空間百分比是、會話中的已幻影檔案數目，以及無法准刪除的檔案數目。
  - 事件識別碼 9029 會提供伺服器端點的虛像化工作階段資訊。 例如：會話中嘗試的檔案數目、會話中階層式檔案數目，以及已階層式檔案數目。
  
- 若要監視伺服器上的回收活動，請使用遙測事件記錄檔中的事件識別碼9005、9006、9009和9059，其位於 [*應用程式和 and services\microsoft\filesync\agent*] 底下的事件檢視器中。

  - 事件識別碼 9005 會提供伺服器端點的回收可靠性。 例如：存取的唯一檔案總數，以及存取失敗的唯一檔案總數。
  - 事件識別碼 9006 會提供伺服器端點的回收錯誤分布。 例如：失敗的要求總數和 ErrorCode。 每個錯誤碼會記錄一個事件。
  - 事件識別碼 9009 會提供伺服器端點的回收工作階段資訊。 例如： DurationSeconds、CountFilesRecallSucceeded 和 CountFilesRecallFailed。
  - 事件識別碼 9059 會提供伺服器端點的應用程式回收分布。 例如： ShareId、應用程式名稱和 TotalEgressNetworkBytes。

### <a name="performance-counters"></a>效能計數器

使用伺服器上的 Azure 檔案同步效能計數器，可監視同步活動。

若要在伺服器上查看 Azure 檔案同步效能計數器，請開啟 [效能監視器（Perfmon.exe）]。 您可以在 [已傳送的**Afs 位元組**] 和 [ **afs 同步作業**] 物件底下找到計數器。

以下是效能監視器中為 Azure 檔案同步提供的效能計數器：

| 效能物件\計數器名稱 | Description |
|-|-|
| 傳輸的 AFS 位元組\每秒下載的位元組 | 每秒下載的位元組數。 |
| 傳輸的 AFS 位元組\每秒上傳的位元組 | 每秒上傳的位元組數。 |
| 傳輸的 AFS 位元組\每秒的位元組總數 | 每秒的位元組總數 (上傳和下載)。 |
| AFS 同步作業\每秒下載的同步檔案 | 每秒下載的檔案數。 |
| AFS 同步作業\每秒上傳的同步檔案 | 每秒上傳的檔案數。 |
| AFS 同步作業\每秒的同步檔案作業總數 | 同步的檔案總數 (上傳和下載)。 |

## <a name="next-steps"></a>後續步驟
- [規劃 Azure 檔案同步部署](storage-sync-files-planning.md)
- [考量防火牆和 Proxy 設定](storage-sync-files-firewall-and-proxy.md)
- [部署 Azure 檔案同步](storage-sync-files-deployment-guide.md)
- [針對 Azure 檔案同步進行移難排解](storage-sync-files-troubleshoot.md)
- [Azure 檔案服務常見問題集](storage-files-faq.md)
