---
title: 監視 Azure 檔案同步 | Microsoft Docs
description: 瞭解如何使用 Azure 監視器、儲存體同步服務和 Windows Server 來監視您的 Azure 檔案同步部署。
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 09/28/2020
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 1ef24522f688c5ae1176630a2f370cd7ee7c3cd7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91448057"
---
# <a name="monitor-azure-file-sync"></a>監視 Azure 檔案同步

使用 Azure 檔案同步，將組織的檔案共用集中在 Azure 檔案服務中，同時保有內部部署檔案伺服器的彈性、效能及相容性。 Azure 檔案同步會將 Windows Server 轉換成 Azure 檔案共用的快速快取。 您可以使用 Windows Server 上可用的任何通訊協定來從本機存取資料，包括 SMB、NFS 和 FTPS。 您可以視需要存取多個散佈於世界各地的快取。

本文說明如何使用 Azure 監視器、儲存體同步服務和 Windows Server 來監視您的 Azure 檔案同步部署。

本指南涵蓋下列案例： 
- 查看 Azure 監視器中的 Azure 檔案同步計量。
- 在 Azure 監視器中建立警示，以主動通知您重大情況。
- 使用 Azure 入口網站來查看 Azure 檔案同步部署的健康情況。
- 如何使用 Windows 伺服器上的事件記錄檔和效能計數器來監視 Azure 檔案同步部署的健康情況。 

## <a name="azure-monitor"></a>Azure 監視器

使用 [Azure 監視器](https://docs.microsoft.com/azure/azure-monitor/overview) 來查看計量，並設定同步處理、雲端階層和伺服器連線的警示。  

### <a name="metrics"></a>計量

依預設會啟用 Azure 檔案同步的計量，並且每 15 分鐘傳送至 Azure 監視器一次。

**如何在 Azure 監視器中查看 Azure 檔案同步計量**
1. 移至**Azure 入口網站**中的**儲存體同步服務**，然後按一下 [**計量**]。
2. 按一下 [計量] 下拉式清單，然後選取您想要查看**的度量。**

![Azure 檔案同步計量的螢幕擷取畫面](media/storage-sync-files-troubleshoot/file-sync-metrics.png)

以下是 Azure 監視器中提供的 Azure 檔案同步計量：

| 度量名稱 | 描述 |
|-|-|
| 同步的位元組 | 傳輸的資料大小 (上傳和下載)。<br><br>單位：位元組<br>匯總類型： Sum<br>適用維度：伺服器端點名稱、同步處理方向、同步組名 |
| 雲端階層處理重新叫用 | 重新叫用的資料大小。<br><br>**注意**：未來將會移除此度量。 使用「雲端階層處理召回大小」計量來監視重新叫用的資料大小。<br><br>單位：位元組<br>匯總類型： Sum<br>適用的維度：伺服器名稱 |
| 雲端階層處理重新叫用大小 | 重新叫用的資料大小。<br><br>單位：位元組<br>匯總類型： Sum<br>適用的維度：伺服器名稱、同步組名 |
| 雲端階層處理重新叫用大小 (依應用程式) | 應用程式重新叫用的資料大小。<br><br>單位：位元組<br>匯總類型： Sum<br>適用的維度：應用程式名稱、伺服器名稱、同步組名 |
| 雲端階層處理重新叫用輸送量 | 資料重新叫用輸送量的大小。<br><br>單位：位元組<br>匯總類型： Sum<br>適用的維度：伺服器名稱、同步組名 |
| 檔案無法同步 | 無法同步的檔案計數。<br><br>單位：Count<br>匯總類型： Sum<br>適用維度：伺服器端點名稱、同步處理方向、同步組名 |
| 同步的檔案 | 傳輸的檔案計數 (上傳和下載)。<br><br>單位：Count<br>匯總類型： Sum<br>適用維度：伺服器端點名稱、同步處理方向、同步組名 |
| 伺服器線上狀態 | 從伺服器接收到的活動訊號計數。<br><br>單位：Count<br>彙總類型：最大值<br>適用的維度：伺服器名稱 |
| 同步工作階段結果 | 同步工作階段結果 (1=成功的同步工作階段；0=失敗的同步工作階段)<br><br>單位：Count<br>匯總類型：最大值<br>適用維度：伺服器端點名稱、同步處理方向、同步組名 |

### <a name="alerts"></a>警示

當您的監視資料中發現重要條件時，警示會主動通知您。 若要深入瞭解如何在 Azure 監視器中設定警示，請參閱 [Microsoft Azure 中的警示總覽](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-overview)。

**如何建立 Azure 檔案同步的警示**

1. 在**Azure 入口網站**中，移至您的**儲存體同步服務**。 
2. 按一下 [監視] 區段中的 [ **警示** ]，然後按一下 [ **+ 新增警示規則**]。
3. 按一下 [ **選取條件** ]，並為警示提供下列資訊： 
    - **計量**
    - **維度名稱**
    - **警示邏輯**
4. 按一下 [ **選取動作群組** ]，然後透過選取現有的動作群組或建立新的動作群組，將動作群組 (電子郵件、SMS 等 ) 至警示。
5. 填入警示 **詳細資料** ，例如 **警示規則名稱**、 **描述** 和 **嚴重性**。
6. 按一下 [ **建立警示規則** ] 以建立警示。  

下表列出一些要監視的範例案例，以及要用於警示的適當計量：

| 狀況 | 用於警示的度量 |
|-|-|
| 伺服器端點健全狀況會在入口網站中顯示錯誤 | 同步工作階段結果 |
| 檔案無法同步至伺服器或雲端端點 | 檔案無法同步 |
| 已註冊的伺服器無法與儲存體同步服務通訊 | 伺服器線上狀態 |
| 雲端階層處理重新叫用大小已超過一天的500GiB  | 雲端階層處理重新叫用大小 |

如需有關如何建立這些案例警示的指示，請參閱 [警示範例](#alert-examples) 一節。

## <a name="storage-sync-service"></a>儲存體同步服務

若要在 **Azure 入口網站**中查看 Azure 檔案同步部署的健康情況，請流覽至 **儲存體同步服務** ，並提供下列資訊：

- 已註冊的伺服器健全狀況
- 伺服器端點健全狀況
    - 檔案無法同步
    - 同步活動
    - 雲端階層處理效率
    - 檔案未分層
    - 召回錯誤
- 計量

### <a name="registered-server-health"></a>已註冊的伺服器健全狀況

若要在入口網站中查看**已註冊的伺服器健全狀況**，請流覽至**儲存體同步服務**的 [**已註冊的伺服器**] 區段。

![已註冊伺服器健全狀況的螢幕擷取畫面](media/storage-sync-files-troubleshoot/file-sync-registered-servers.png)

- 如果 **已註冊的伺服器** 狀態為 **線上**，則伺服器會成功與服務通訊。
- 如果 **已註冊的伺服器** 狀態為 **[離線**]，則儲存體同步監視器進程 ( # A0) 並未執行，或伺服器無法存取 Azure 檔案同步服務。 請參閱 [疑難排解檔](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#server-endpoint-noactivity) 以取得指導方針。

### <a name="server-endpoint-health"></a>伺服器端點健全狀況

若要在入口網站中查看**伺服器端點**的健康情況，請流覽至**儲存體同步服務**的 [**同步處理群組**] 區段，然後選取 [同步處理**群組**]。

![伺服器端點健全狀況的螢幕擷取畫面](media/storage-sync-files-troubleshoot/file-sync-server-endpoint-health.png)

- 入口網站中的 **伺服器端點健全狀況** 和 **同步活動** 是根據記錄在伺服器上的遙測事件記錄檔中的同步事件 (識別碼9102和 9302) 。 如果同步處理會話因為暫時性錯誤（例如錯誤取消）而失敗，只要目前的同步會話正在進行中，伺服器端點仍會在入口網站中顯示為 **狀況良好** ， (檔案會套用) 。 事件識別碼9302是同步處理進度事件，而在同步處理會話完成後，會記錄事件識別碼9102。  如需詳細資訊，請參閱 [同步健康](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#broken-sync) 情況與 [同步處理進度](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#how-do-i-monitor-the-progress-of-a-current-sync-session)。 如果伺服器端點健全狀況顯示 **錯誤** 或 **沒有任何活動**，請參閱 [疑難排解檔](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#common-sync-errors) 以取得指導方針。
- 入口網站中的 [檔案 **未同步** 計數] 是根據記錄在伺服器上的遙測事件記錄檔中的事件識別碼9121。 當同步會話完成時，每個專案的錯誤都會記錄此事件。 若要解決每個專案的錯誤，請參閱 [如何? 查看是否有特定的檔案或資料夾未同步？](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#how-do-i-see-if-there-are-specific-files-or-folders-that-are-not-syncing)。
- 若要在入口網站中查看雲端階層處理 **效率** ，請移至 **伺服器端點屬性** ，然後流覽至 [ **雲端** 階層處理] 區段。 針對雲端階層處理效率所提供的資料，是根據記錄在伺服器上遙測事件記錄檔中的事件識別碼9071。 若要深入了解，請參閱[雲端階層處理概觀](https://docs.microsoft.com/azure/storage/files/storage-sync-cloud-tiering)。
- 若要在入口網站中查看 **無法分層** 處理和重新 **叫用錯誤** 的檔案，請移至 **伺服器端點屬性** ，然後流覽至 [ **雲端** 階層處理] 區段。 **未** 進行階層處理的檔案是根據記錄在伺服器上的遙測事件記錄檔中的事件識別碼9003，而重新 **叫用錯誤** 是以事件識別碼9006為基礎。 若要調查無法分層或召回的檔案，請參閱 [如何疑難排解無法階層式](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#how-to-troubleshoot-files-that-fail-to-tier) 檔案，以及如何針對無法重新叫 [用的檔案進行疑難排解](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#how-to-troubleshoot-files-that-fail-to-be-recalled)。

### <a name="metric-charts"></a>度量圖表

- 您可以在儲存體同步服務入口網站中查看下列度量圖表：

  | 度量名稱 | 描述 | Blade 名稱 |
  |-|-|-|
  | 同步的位元組 | 傳輸的資料大小 (上傳和下載) | 同步群組、伺服器端點 |
  | 雲端階層處理重新叫用 | 重新叫用的資料大小 | 已註冊的伺服器 |
  | 檔案無法同步 | 無法同步的檔案計數 | 伺服器端點 |
  | 同步的檔案 | 傳輸的檔案計數 (上傳和下載) | 同步群組、伺服器端點 |
  | 伺服器線上狀態 | 從伺服器接收到的活動訊號計數 | 已註冊的伺服器 |

- 若要深入瞭解，請參閱 [Azure 監視器](https://docs.microsoft.com/azure/storage/files/storage-sync-files-monitoring#azure-monitor)。

  > [!Note]  
  > 儲存體同步服務入口網站中的圖表時間範圍為 24 小時。 若要檢視不同的時間範圍或維度，請使用 Azure 監視器。

## <a name="windows-server"></a>Windows Server

在安裝 Azure 檔案同步代理程式的 **Windows Server** 上，您可以使用 **事件記錄** 檔和 **效能計數器**，在該伺服器上查看伺服器端點的健全狀況。

### <a name="event-logs"></a>事件記錄檔

使用伺服器上的遙測事件記錄，可監視已註冊的伺服器、同步、和雲端階層處理健康情況。 遙測事件記錄檔位於 [ *應用程式和 and services\microsoft\filesync\agent*] 下的 [事件檢視器]。

同步健康情況

- 同步工作階段完成後，會記錄事件識別碼 9102。 您可以使用此事件來判斷同步會話是否成功 (**HResult = 0**) 如果有每個專案的同步錯誤 (**PerItemErrorCount**) 。 如需詳細資訊，請參閱 [同步健康](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#broken-sync) 情況和  [每個專案的錯誤](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#how-do-i-see-if-there-are-specific-files-or-folders-that-are-not-syncing) 檔。

  > [!Note]  
  > 有時候同步會話會整體失敗或有非零的 PerItemErrorCount。 不過，它們仍會繼續進行，而某些檔案會成功同步。 您可以在套用的欄位（例如看出 appliedfilecount、AppliedDirCount、AppliedTombstoneCount 和 AppliedSizeBytes）中看到此情況。 這些欄位會告訴您會話的成功程度。 如果您在某個資料列中看到多個同步會話失敗，而且它們有增加的已套用計數，請在您開啟支援票證之前，提供同步處理時間再試一次。

- 當同步會話完成時，每個專案的錯誤都會記錄事件識別碼9121。 您可以使用此事件來判斷與此錯誤 (**PersistentCount** 和 **TransientCount**) 同步處理失敗的檔案數目。 應調查持續性的每個專案錯誤，請參閱 [如何? 查看是否有特定的檔案或資料夾未同步？](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#how-do-i-see-if-there-are-specific-files-or-folders-that-are-not-syncing)。

- 如果有使用中的同步工作階段，則會每隔 5 到 10 分鐘記錄事件識別碼 9302 一次。 您可以使用此事件來判斷要同步處理的專案數目 (**TotalItemCount**) 、已同步至目前為止的專案數目 (**AppliedItemCount**) 和由於每個專案的錯誤而無法同步的專案數目 (**PerItemErrorCount**) 。 如果同步處理沒有進度 (**AppliedItemCount = 0**) ，同步處理會話最終將會失敗，而且將會記錄事件識別碼9102與錯誤。 如需詳細資訊，請參閱 [同步處理進度檔](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#how-do-i-monitor-the-progress-of-a-current-sync-session)。

已註冊的伺服器健全狀況

- 當伺服器查詢服務中的作業時，將會每 30 秒記錄事件識別碼 9301 一次。 如果 GetNextJob 完成，且 **狀態 = 0**，則伺服器能夠與服務通訊。 如果 GetNextJob 完成但發生錯誤，請參閱 [疑難排解檔](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#server-endpoint-noactivity) 以取得指導方針。

雲端階層處理健全狀況

- 若要監視伺服器上的分層活動，請在遙測事件記錄檔中使用事件識別碼9003、9016和9029，該記錄檔位於 [ *應用程式] 和 [and services\microsoft\filesync\agent*] 底下的事件檢視器。

  - 事件識別碼 9003 會提供伺服器端點的錯誤分布。 例如：錯誤計數總計和 ErrorCode。 每個錯誤碼會記錄一個事件。
  - 事件識別碼 9016 會提供磁碟區的建立映像結果。 例如：可用空間百分比為、會話中幻影的檔案數目，以及無法准刪除的檔案數目。
  - 事件識別碼 9029 會提供伺服器端點的虛像化工作階段資訊。 例如：在會話中嘗試的檔案數目、會話中階層式檔案數目，以及已階層式檔案數目。
  
- 若要監視伺服器上的召回活動，請使用遙測事件記錄檔中的事件識別碼9005、9006、9009、9059和9071，該記錄檔位於 [ *應用程式和 and services\microsoft\filesync\agent*] 下的事件檢視器。

  - 事件識別碼 9005 會提供伺服器端點的回收可靠性。 例如：存取的唯一檔案總數，以及具有失敗存取權的唯一檔案總數。
  - 事件識別碼 9006 會提供伺服器端點的回收錯誤分布。 例如：失敗的要求總數和 ErrorCode。 每個錯誤碼會記錄一個事件。
  - 事件識別碼 9009 會提供伺服器端點的回收工作階段資訊。 例如： DurationSeconds、CountFilesRecallSucceeded 和 CountFilesRecallFailed。
  - 事件識別碼 9059 會提供伺服器端點的應用程式回收分布。 例如： ShareId、應用程式名稱和 TotalEgressNetworkBytes。
  - 事件識別碼9071會提供伺服器端點的雲端階層處理效率。 例如： TotalDistinctFileCountCacheHit、TotalDistinctFileCountCacheMiss、TotalCacheHitBytes 和 TotalCacheMissBytes。

### <a name="performance-counters"></a>效能計數器

使用伺服器上的 Azure 檔案同步效能計數器，可監視同步活動。

若要查看伺服器上 Azure 檔案同步效能計數器，請開啟效能監視器 ( # A0) 。 您可以在已 **傳送的 Afs 位元組** 和 **Afs 同步處理作業** 物件下找到計數器。

以下是效能監視器中為 Azure 檔案同步提供的效能計數器：

| 效能物件\計數器名稱 | 描述 |
|-|-|
| 傳輸的 AFS 位元組\每秒下載的位元組 | 每秒下載的位元組數。 |
| 傳輸的 AFS 位元組\每秒上傳的位元組 | 每秒上傳的位元組數。 |
| 傳輸的 AFS 位元組\每秒的位元組總數 | 每秒的位元組總數 (上傳和下載)。 |
| AFS 同步作業\每秒下載的同步檔案 | 每秒下載的檔案數。 |
| AFS 同步作業\每秒上傳的同步檔案 | 每秒上傳的檔案數。 |
| AFS 同步作業\每秒的同步檔案作業總數 | 同步的檔案總數 (上傳和下載)。 |

## <a name="alert-examples"></a>警示範例
本節提供 Azure 檔案同步的一些範例警示。

  > [!Note]  
  > 如果您建立警示，而它的雜訊太多，請調整臨界值和警示邏輯。
  
### <a name="how-to-create-an-alert-if-the-server-endpoint-health-shows-an-error-in-the-portal"></a>當伺服器端點健全狀況在入口網站中顯示錯誤時，如何建立警示

1. 在 **Azure 入口網站**中，流覽至個別的 **儲存體同步服務**。 
2. 移至 [ **監視** ] 區段，然後按一下 [ **警示**]。 
3. 按一下 [ **+ 新增警示規則** ]，以建立新的警示規則。 
4. 按一下 [ **選取條件**] 來設定條件。
5. 在 [ **設定信號邏輯** ] 分頁中，按一下 [信號名稱] 底下的 [ **同步會話結果** ]。  
6. 選取下列維度設定： 
    - 維度名稱： **伺服器端點名稱**  
    - 運算元： **=** 
    - 維度值： **所有目前和未來的值**  
7. 流覽至 **警示邏輯** ，然後完成下列步驟： 
    - 閾值設定為 **靜態** 
    - 運算子： **小於** 
    - 匯總類型： **最大值**  
    - 臨界值： **1** 
    - 評估依據：匯總細微性 = **24 小時** |評估頻率 = **每小時** 
    - 按一下 [ **完成]。** 
8. 按一下 [ **選取動作群組** ]，透過選取現有的動作群組或建立新的動作群組，將動作群組新增 (電子郵件、SMS 等 ) 至警示。
9. 填入警示 **詳細資料** ，例如 **警示規則名稱**、 **描述** 和 **嚴重性**。
10. 按一下 [建立警示規則]。 

### <a name="how-to-create-an-alert-if-files-are-failing-to-sync-to-a-server-or-cloud-endpoint"></a>如何在檔案無法同步至伺服器或雲端端點時建立警示

1. 在 **Azure 入口網站**中，流覽至個別的 **儲存體同步服務**。 
2. 移至 [ **監視** ] 區段，然後按一下 [ **警示**]。 
3. 按一下 [ **+ 新增警示規則** ]，以建立新的警示規則。 
4. 按一下 [ **選取條件**] 來設定條件。
5. 在 [ **設定信號邏輯** ] 分頁中，按一下 [在信號名稱下 **未同步** 的檔案]。  
6. 選取下列維度設定： 
     - 維度名稱： **伺服器端點名稱**  
     - 運算元： **=** 
     - 維度值： **所有目前和未來的值**  
7. 流覽至 **警示邏輯** ，然後完成下列步驟： 
     - 閾值設定為 **靜態** 
     - 運算子： **大於** 
     - 匯總類型： **總計**  
     - 臨界值： **100** 
     - 評估依據：匯總細微性 = **5 分鐘** |評估頻率 = **每5分鐘** 
     - 按一下 [ **完成]。** 
8. 按一下 [ **選取動作群組** ]，透過選取現有的動作群組或建立新的動作群組，將動作群組新增 (電子郵件、SMS 等 ) 至警示。
9. 填入警示 **詳細資料** ，例如 **警示規則名稱**、 **描述** 和 **嚴重性**。
10. 按一下 [建立警示規則]。 

### <a name="how-to-create-an-alert-if-a-registered-server-is-failing-to-communicate-with-the-storage-sync-service"></a>如何在已註冊的伺服器無法與儲存體同步服務進行通訊時建立警示

1. 在 **Azure 入口網站**中，流覽至個別的 **儲存體同步服務**。 
2. 移至 [ **監視** ] 區段，然後按一下 [ **警示**]。 
3. 按一下 [ **+ 新增警示規則** ]，以建立新的警示規則。 
4. 按一下 [ **選取條件**] 來設定條件。
5. 在 [ **設定信號邏輯** ] 分頁中，按一下 [信號名稱] 底下的 [ **伺服器線上狀態** ]。  
6. 選取下列維度設定： 
     - 維度名稱： **伺服器名稱**  
     - 運算元： **=** 
     - 維度值： **所有目前和未來的值**  
7. 流覽至 **警示邏輯** ，然後完成下列步驟： 
     - 閾值設定為 **靜態** 
     - 運算子： **小於** 
     - 匯總類型： **最大值**  
     - 臨界值 (位元組) ： **1** 
     - 評估依據：匯總細微性 = **1 小時** |評估頻率 = **每30分鐘** 
        - 請注意，計量會每隔15到20分鐘傳送給 Azure 監視器。 請勿將 **評估頻率** 設定為小於30分鐘 (將產生錯誤警示) 。
     - 按一下 [ **完成]。** 
8. 按一下 [ **選取動作群組** ]，透過選取現有的動作群組或建立新的動作群組，將動作群組新增 (電子郵件、SMS 等 ) 至警示。
9. 填入警示 **詳細資料** ，例如 **警示規則名稱**、 **描述** 和 **嚴重性**。
10. 按一下 [建立警示規則]。 

### <a name="how-to-create-an-alert-if-the-cloud-tiering-recall-size-has-exceeded-500gib-in-a-day"></a>如何在一天內的雲端階層處理重新叫用大小超過500GiB 時建立警示

1. 在 **Azure 入口網站**中，流覽至個別的 **儲存體同步服務**。 
2. 移至 [ **監視** ] 區段，然後按一下 [ **警示**]。 
3. 按一下 [ **+ 新增警示規則** ]，以建立新的警示規則。 
4. 按一下 [ **選取條件**] 來設定條件。
5. 在 [**設定信號邏輯**] 分頁中，按一下 [信號名稱] 底下的 [雲端階層處理重新設定**大小**  
6. 選取下列維度設定： 
     - 維度名稱： **伺服器名稱**  
     - 運算元： **=** 
     - 維度值： **所有目前和未來的值**  
7. 流覽至 **警示邏輯** ，然後完成下列步驟： 
     - 閾值設定為 **靜態** 
     - 運算子： **大於** 
     - 匯總類型： **總計**  
     - 臨界值 (位元組) ： **67108864000** 
     - 評估依據：匯總細微性 = **24 小時** |評估頻率 = **每小時** 
    - 按一下 [ **完成]。** 
8. 按一下 [ **選取動作群組** ]，透過選取現有的動作群組或建立新的動作群組，將動作群組新增 (電子郵件、SMS 等 ) 至警示。
9. 填入警示 **詳細資料** ，例如 **警示規則名稱**、 **描述** 和 **嚴重性**。
10. 按一下 [建立警示規則]。 

## <a name="next-steps"></a>後續步驟
- [規劃 Azure 檔案同步部署](storage-sync-files-planning.md)
- [考量防火牆和 Proxy 設定](storage-sync-files-firewall-and-proxy.md)
- [部署 Azure 檔案同步](storage-sync-files-deployment-guide.md) \(部分機器翻譯\)
- [針對 Azure 檔案同步問題進行疑難排解](storage-sync-files-troubleshoot.md) \(部分機器翻譯\)
- [Azure 檔案服務常見問題集](storage-files-faq.md)
