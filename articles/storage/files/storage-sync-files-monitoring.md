---
title: 監視 Azure 檔案同步 | Microsoft Docs
description: 如何監視 Azure 檔案同步。
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: ac09f9b59bc6f47adc9311cc910352c1a0d73b5d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "68699295"
---
# <a name="monitor-azure-file-sync"></a>監視 Azure 檔案同步

使用 Azure 檔案同步，將組織的檔案共用集中在 Azure 檔案服務中，同時保有內部部署檔案伺服器的彈性、效能及相容性。 Azure 檔案同步會將 Windows Server 轉換成 Azure 檔案共用的快速快取。 您可以使用 Windows Server 上可用的任何通訊協定來從本機存取資料，包括 SMB、NFS 和 FTPS。 您可以視需要存取多個散佈於世界各地的快取。

本文介紹如何使用 Azure 監視器、存儲同步服務和 Windows 伺服器監視 Azure 檔同步部署。

以下監視選項當前可用。

## <a name="azure-monitor"></a>Azure 監視器

使用[Azure 監視器](https://docs.microsoft.com/azure/azure-monitor/overview)查看指標並配置同步、雲分層和伺服器連接的警報。  

### <a name="metrics"></a>計量

依預設會啟用 Azure 檔案同步的計量，並且每 15 分鐘傳送至 Azure 監視器一次。

要查看 Azure 監視器中的 Azure 檔同步指標，請選擇**存儲同步服務**資源類型。

以下是 Azure 監視器中提供的 Azure 檔案同步計量：

| 度量名稱 | 描述 |
|-|-|
| 同步的位元組 | 傳輸的資料大小 (上傳和下載)。<br><br>單位：位元組<br>聚合類型：總和<br>適用維度：伺服器終結點名稱、同步方向、同步組名稱 |
| 雲端階層處理重新叫用 | 重新叫用的資料大小。<br><br>**注意**：此指標將在將來刪除。 使用雲分層召回大小指標監視召回的資料大小。<br><br>單位：位元組<br>聚合類型：總和<br>適用維度：伺服器名稱 |
| 雲分層召回大小 | 重新叫用的資料大小。<br><br>單位：位元組<br>聚合類型：總和<br>適用維度：伺服器名稱、同步組名稱 |
| 按應用程式劃分的雲分層召回大小 | 應用程式調用的資料的大小。<br><br>單位：位元組<br>聚合類型：總和<br>適用維度：應用程式名稱、伺服器名稱、同步組名稱 |
| 雲分層召回輸送量 | 資料撤回輸送量的大小。<br><br>單位：位元組<br>聚合類型：總和<br>適用維度：伺服器名稱、同步組名稱 |
| 檔案無法同步 | 無法同步的檔案計數。<br><br>單位：計數<br>聚合類型：總和<br>適用維度：伺服器終結點名稱、同步方向、同步組名稱 |
| 同步的檔案 | 傳輸的檔案計數 (上傳和下載)。<br><br>單位：計數<br>聚合類型：總和<br>適用維度：伺服器終結點名稱、同步方向、同步組名稱 |
| 伺服器線上狀態 | 從伺服器接收到的活動訊號計數。<br><br>單位：計數<br>彙總類型：最大<br>適用維度：伺服器名稱 |
| 同步工作階段結果 | 同步工作階段結果 (1=成功的同步工作階段；0=失敗的同步工作階段)<br><br>單位：計數<br>聚合類型：最大值<br>適用維度：伺服器終結點名稱、同步方向、同步組名稱 |

### <a name="alerts"></a>警示

要在 Azure 監視器中配置警報，請選擇存儲同步服務，然後選擇用於警報的[Azure 檔同步指標](https://docs.microsoft.com/azure/storage/files/storage-sync-files-monitoring#metrics)。  

下表列出了要監視的一些示例方案和用於警報的正確指標：

| 狀況 | 用於警報的指標 |
|-|-|
| 門戶中的伺服器終結點運行狀況 = 錯誤 | 同步工作階段結果 |
| 檔無法同步到伺服器或雲終結點 | 檔案無法同步 |
| 已註冊伺服器無法與存儲同步服務通信 | 伺服器線上狀態 |
| 雲分層召回規模在一天內已超過 500GiB  | 雲分層召回大小 |

要瞭解有關在 Azure 監視器中配置警報的詳細資訊，請參閱[Microsoft Azure 中的警報概述]( https://docs.microsoft.com/azure/azure-monitor/platform/alerts-overview)。

## <a name="storage-sync-service"></a>儲存體同步服務

要查看已註冊的伺服器運行狀況、伺服器終結點運行狀況和指標，請訪問 Azure 門戶中的存儲同步服務。 您可以在 **"已註冊伺服器"** 邊欄選項卡和"**同步組"** 邊欄選項卡中查看已註冊的伺服器運行狀況和伺服器終結點運行狀況。

### <a name="registered-server-health"></a>已註冊伺服器運行狀況

- 如果 **"已註冊伺服器**"狀態**為"連線**"，則伺服器將成功與服務通信。
- 如果 **"已註冊伺服器**"狀態**顯示為離線**，請驗證服務器上的存儲同步監視器 （AzureStorageSyncMonitor.exe） 進程是否正在運行。 如果伺服器位於防火牆或代理後面，請參閱[本文](https://docs.microsoft.com/azure/storage/files/storage-sync-files-firewall-and-proxy)以配置防火牆和代理。

### <a name="server-endpoint-health"></a>伺服器終結點運行狀況

- 入口網站中的伺服器端點健康情況，取決於伺服器上的遙測事件記錄 (識別碼 9102 和 9302) 中所記錄的同步事件。 如果同步會話由於暫時性錯誤（如已取消錯誤）而失敗，只要當前同步會話正在取得進展，同步在門戶中可能仍顯示為正常。 事件 ID 9302 用於確定是否應用檔。 有關詳細資訊，請參閱[同步運行狀況](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#broken-sync)和[同步進度](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#how-do-i-monitor-the-progress-of-a-current-sync-session)。
- 如果門戶顯示同步錯誤，因為同步未取得進展，請參閱[故障排除文檔](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#common-sync-errors)以獲得指導。

### <a name="metric-charts"></a>指標圖表

- 以下指標圖表可查看存儲同步服務門戶：

  | 度量名稱 | 描述 | 刀片名稱 |
  |-|-|-|
  | 同步的位元組 | 傳輸的資料大小 (上傳和下載) | 同步群組、伺服器端點 |
  | 雲端階層處理重新叫用 | 重新叫用的資料大小 | 已註冊的伺服器 |
  | 檔案無法同步 | 無法同步的檔案計數 | 伺服器端點 |
  | 同步的檔案 | 傳輸的檔案計數 (上傳和下載) | 同步群組、伺服器端點 |
  | 伺服器線上狀態 | 從伺服器接收到的活動訊號計數 | 已註冊的伺服器 |

- 要瞭解更多資訊，請參閱[Azure 監視器](https://docs.microsoft.com/azure/storage/files/storage-sync-files-monitoring#azure-monitor)。

  > [!Note]  
  > 儲存體同步服務入口網站中的圖表時間範圍為 24 小時。 若要檢視不同的時間範圍或維度，請使用 Azure 監視器。

## <a name="windows-server"></a>Windows Server

在 Windows 伺服器上，您可以查看雲分層、已註冊的伺服器和同步運行狀況。

### <a name="event-logs"></a>事件記錄

使用伺服器上的遙測事件記錄，可監視已註冊的伺服器、同步、和雲端階層處理健康情況。 遙測事件日誌位於*應用程式和服務下的事件檢視器中\Microsoft_FileSync_代理*。

同步運行狀況：

- 事件 ID 9102 在同步會話完成後記錄。 使用此事件可確定同步會話是否成功 **（HResult = 0**），以及是否存在每個專案同步錯誤。 有關詳細資訊，請參閱[同步運行狀況](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#broken-sync)和[每個專案錯誤](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#how-do-i-see-if-there-are-specific-files-or-folders-that-are-not-syncing)文檔。

  > [!Note]  
  > 有時，同步會話整體失敗或每個專案錯誤計數為非零。 但是，它們仍向前推進，並且某些檔成功同步。 您可以在應用檔計數、應用十分計數、應用墓碑計數和應用大小位元組等應用欄位中看到這一點。 這些欄位告訴您會話成功了多少。 如果您看到一行中的多個同步會話失敗，並且它們的應用計數不斷增加，請給同步時間在打開支援票證之前重試。

- 如果有使用中的同步工作階段，則會每隔 5 到 10 分鐘記錄事件識別碼 9302 一次。 使用此事件可確定當前同步會話是否正在取得進展（**應用專案計數> 0**）。 如果同步未取得進展，同步會話最終將失敗，並且事件 ID 9102 將記錄錯誤。 有關詳細資訊，請參閱[同步進度文檔](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#how-do-i-monitor-the-progress-of-a-current-sync-session)。

已註冊伺服器運行狀況：

- 當伺服器查詢服務中的作業時，將會每 30 秒記錄事件識別碼 9301 一次。 如果 GetNextJob 以**狀態 = 0**結束，則伺服器能夠與服務通信。 如果 GetNextJob 出現錯誤，請查看[故障排除文檔](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#common-sync-errors)以獲得指導。

雲分層運行狀況：

- 要監視伺服器上的分層活動，請使用遙測事件日誌中的事件 ID 9003、9016 和 9029，該日誌位於*應用程式和服務下的事件檢視器中。微軟_FileSync_Agent*。

  - 事件識別碼 9003 會提供伺服器端點的錯誤分布。 例如：總錯誤計數和錯誤代碼。 每個錯誤代碼記錄一個事件。
  - 事件識別碼 9016 會提供磁碟區的建立映像結果。 例如：可用空間百分比是，會話中重影的檔數和檔數未能重影。
  - 事件識別碼 9029 會提供伺服器端點的虛像化工作階段資訊。 例如：會話中嘗試的檔數、會話中階層式檔數和已階層式檔數。
  
- 要監視伺服器上的召回活動，請使用遙測事件日誌中的事件 ID 9005、9006、9009 和 9059，該日誌位於*應用程式和服務下的事件檢視器中[Microsoft_FileSync_代理*]

  - 事件識別碼 9005 會提供伺服器端點的回收可靠性。 例如：訪問的唯一檔總數，以及訪問失敗的總唯一檔。
  - 事件識別碼 9006 會提供伺服器端點的回收錯誤分布。 例如：失敗請求總數和錯誤代碼。 每個錯誤代碼記錄一個事件。
  - 事件識別碼 9009 會提供伺服器端點的回收工作階段資訊。 例如：持續時間秒、計數檔撤回成功和計數檔撤回失敗。
  - 事件識別碼 9059 會提供伺服器端點的應用程式回收分布。 例如：共用 Id、應用程式名稱和總計 Gress 網路位元組。

### <a name="performance-counters"></a>效能計數器

使用伺服器上的 Azure 檔案同步效能計數器，可監視同步活動。

要查看伺服器上的 Azure 檔同步效能計數器，打開效能監視器 （Perfmon.exe）。 您可以在**AFS 位元組傳輸**和**AFS 同步操作**物件下找到計數器。

以下是效能監視器中為 Azure 檔案同步提供的效能計數器：

| 效能物件\計數器名稱 | 描述 |
|-|-|
| 傳輸的 AFS 位元組\每秒下載的位元組 | 每秒下載的位元組數。 |
| 傳輸的 AFS 位元組\每秒上傳的位元組 | 每秒上傳的位元組數。 |
| 傳輸的 AFS 位元組\每秒的位元組總數 | 每秒的位元組總數 (上傳和下載)。 |
| AFS 同步作業\每秒下載的同步檔案 | 每秒下載的檔案數。 |
| AFS 同步作業\每秒上傳的同步檔案 | 每秒上傳的檔案數。 |
| AFS 同步作業\每秒的同步檔案作業總數 | 同步的檔案總數 (上傳和下載)。 |

## <a name="next-steps"></a>後續步驟
- [規劃 Azure 檔同步部署](storage-sync-files-planning.md)
- [考量防火牆和 Proxy 設定](storage-sync-files-firewall-and-proxy.md)
- [部署 Azure 檔案同步](storage-sync-files-deployment-guide.md)
- [針對 Azure 檔案同步進行移難排解](storage-sync-files-troubleshoot.md)
- [Azure 檔案服務常見問題集](storage-files-faq.md)
