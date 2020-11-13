---
title: Azure 檔案共用效能疑難排解指南
description: 針對 Azure 檔案共用的已知效能問題進行疑難排解。 當遇到這些問題時，探索潛在的原因和相關的因應措施。
author: gunjanj
ms.service: storage
ms.topic: troubleshooting
ms.date: 09/15/2020
ms.author: gunjanj
ms.subservice: files
ms.openlocfilehash: 4d89fb4bcedb58aa156e75aaefc87479797dcd37
ms.sourcegitcommit: 1d6ec4b6f60b7d9759269ce55b00c5ac5fb57d32
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/13/2020
ms.locfileid: "94577967"
---
# <a name="troubleshoot-azure-file-shares-performance-issues"></a>針對 Azure 檔案共用效能問題進行疑難排解

本文列出一些與 Azure 檔案共用相關的常見問題。 當您遇到這些問題時，它會提供潛在的原因和因應措施。

## <a name="high-latency-low-throughput-and-general-performance-issues"></a>高延遲、低輸送量和一般效能問題

### <a name="cause-1-share-was-throttled"></a>原因1：已節流共用

當達到檔案共用的每秒 i/o 作業 (IOPS) 、輸入或輸出限制時，就會對要求進行節流。 若要瞭解標準和 premium 檔案共用的限制，請參閱檔案 [共用和檔案調整目標](https://docs.microsoft.com/azure/storage/files/storage-files-scale-targets#file-share-and-file-scale-targets)。

若要確認您的共用是否正在進行節流，您可以在入口網站中存取和使用 Azure 計量。

1. 在 Azure 入口網站中，移至您的儲存體帳戶。

1. 在左窗格的 [ **監視** ] 底下，選取 [ **計量** ]。

1. 選取 **[** 檔案] 作為儲存體帳戶範圍的計量命名空間。

1. 選取 **交易** 作為度量。

1. 新增 **回應類型** 的篩選器，然後檢查是否有任何要求具有下列其中一個回應碼：
   * **SuccessWithThrottling** ： For Server Message BLOCK (SMB) 
   * **ClientThrottlingError** ：適用于 REST

   ![適用于 premium 檔案共用的計量選項螢幕擷取畫面，其中顯示 [回應類型] 屬性篩選。](media/storage-troubleshooting-premium-fileshares/metrics.png)

   > [!NOTE]
   > 若要收到警示，請參閱本文稍後的「 [如何建立檔案共用的警示](#how-to-create-an-alert-if-a-file-share-is-throttled) 」一節。

### <a name="solution"></a>解決方案

- 如果您使用的是標準檔案共用，請在儲存體帳戶上啟用 [大型檔案共用](https://docs.microsoft.com/azure/storage/files/storage-files-how-to-create-large-file-share?tabs=azure-portal) 。 大型檔案共用支援每個共用最高 10000 IOPS。
- 如果您使用的是 premium 檔案共用，請增加布建的檔案共用大小，以增加 IOPS 限制。 若要深入瞭解，請參閱《 [Azure 檔案儲存體規劃指南》](https://docs.microsoft.com/azure/storage/files/storage-files-planning#understanding-provisioning-for-premium-file-shares)中的「瞭解 premium 檔案共用的布建」一節。

### <a name="cause-2-metadata-or-namespace-heavy-workload"></a>原因2：中繼資料或命名空間繁重的工作負載

如果大部分的要求都是以中繼資料為中心的 (例如 createfile、openfile、closefile、queryinfo 或 querydirectory) ，延遲會比讀取/寫入作業更糟。

若要判斷您大部分的要求是否以中繼資料為中心，請依照先前的原因1所述的步驟1-4 開始。 在步驟5中，請新增 **API 名稱** 的屬性篩選，而不是新增 **回應類型** 的篩選。

![適用于 premium 檔案共用的計量選項螢幕擷取畫面，其中顯示「API 名稱」屬性篩選。](media/storage-troubleshooting-premium-fileshares/MetadataMetrics.png)

### <a name="workaround"></a>因應措施

- 查看是否可以修改應用程式，以減少中繼資料作業的數目。
- 在檔案共用上新增 (VHD) 的虛擬硬碟，並從用戶端透過 SMB 掛接 VHD，以對資料執行檔案作業。 這種方法適用于單一寫入器和多個讀取器案例，並允許在本機進行中繼資料作業。 此安裝程式提供的效能類似于本機直接連接的儲存體。

### <a name="cause-3-single-threaded-application"></a>原因3：單一執行緒應用程式

如果您使用的應用程式是單一執行緒，則根據您布建的共用大小而定，此設定可能會產生比最大可能輸送量更低的 IOPS 輸送量。

### <a name="solution"></a>解決方案

- 藉由增加執行緒數目來提高應用程式平行處理原則。
- 切換至可以平行處理的應用程式。 例如，針對複製作業，您可以從 Windows 用戶端使用 AzCopy 或 RoboCopy，或從 Linux 用戶端使用 **parallel** 命令。

## <a name="very-high-latency-for-requests"></a>非常高的要求延遲

### <a name="cause"></a>原因

用戶端虛擬機器 (VM) 可能位於與檔案共用不同的區域。

### <a name="solution"></a>解決方案

- 從與檔案共用位於相同區域的 VM 執行應用程式。

## <a name="client-unable-to-achieve-maximum-throughput-supported-by-the-network"></a>用戶端無法達到網路支援的最大輸送量

### <a name="cause"></a>原因
其中一個可能的原因是缺少 SMB 多通道支援。 目前，Azure 檔案儲存體僅支援單一通道，因此只有一個從用戶端 VM 到伺服器的連線。 此單一連線會限定為用戶端 VM 上的單一核心，因此可從 VM 達到的最大輸送量會受到單一核心的限制。

### <a name="workaround"></a>因應措施

- 取得具有較大核心的 VM 可能有助於提升輸送量。
- 從多個 Vm 執行用戶端應用程式會增加輸送量。
- 盡可能使用 REST Api。

## <a name="throughput-on-linux-clients-is-significantly-lower-than-that-of-windows-clients"></a>Linux 用戶端上的輸送量明顯低於 Windows 用戶端的輸送量

### <a name="cause"></a>原因

這是在 Linux 上執行 SMB 用戶端的已知問題。

### <a name="workaround"></a>因應措施

- 將負載分散到多個 Vm。
- 在相同的 VM 上，使用多個掛接點搭配 **nosharesock** 選項，並將負載分散到這些掛接點。
- 在 Linux 上，請嘗試裝載 **nostrictsync** 選項，以避免在每個 **fsync** 呼叫上強制執行 SMB 排清。 針對 Azure 檔案儲存體，此選項不會干擾資料一致性，但可能會導致目錄清單 ( **ls-l** 命令) 的過時檔案中繼資料。 使用 **stat** 命令直接查詢檔案中繼資料，將會傳回最新的檔案中繼資料。

## <a name="high-latencies-for-metadata-heavy-workloads-involving-extensive-openclose-operations"></a>涉及大量開啟/關閉作業的中繼資料繁重工作負載的高延遲

### <a name="cause"></a>原因

缺少目錄租用的支援。

### <a name="workaround"></a>因應措施

- 可能的話，請避免在短時間內于相同的目錄中使用過多的開啟/關閉控制碼。
- 針對 Linux Vm，請將 **actimeo = \<sec>** 指定為掛接選項，以增加目錄專案快取超時。 根據預設，timeout 是1秒，因此較大的值（例如3或5秒）可能會有所説明。
- 針對 CentOS Linux 或 Red Hat Enterprise Linux (RHEL) Vm，請將系統升級至 CentOS Linux 8.2 或 RHEL 8.2。 針對其他 Linux Vm，請將核心升級至5.0 或更新版本。

## <a name="low-iops-on-centos-linux-or-rhel"></a>CentOS Linux 或 RHEL 上的低 IOPS

### <a name="cause"></a>原因

CentOS Linux 或 RHEL 不支援大於1的 i/o 深度。

### <a name="workaround"></a>因應措施

- 升級至 CentOS Linux 8 或 RHEL 8。
- 變更為 Ubuntu。

## <a name="slow-file-copying-to-and-from-azure-file-shares-in-linux"></a>從 Linux 中的 Azure 檔案共用複製到 Azure 檔案共用的速度緩慢

如果您遇到檔案複製緩慢的問題，請參閱《 [linux 疑難排解指南》](storage-troubleshoot-linux-file-connection-problems.md#slow-file-copying-to-and-from-azure-files-in-linux)中的「在 linux 中從 Azure 檔案共用複製到或複製到 Azure 檔案共用的速度緩慢」一節。

## <a name="jittery-or-sawtooth-pattern-for-iops"></a>IOPS 的抖動或鋸齒模式

### <a name="cause"></a>原因

用戶端應用程式一致地超過基準 IOPS。 目前不會對要求負載進行服務端平滑處理。 如果用戶端超過基準 IOPS，服務將會進行節流。 節流會導致用戶端遇到抖動或鋸齒的 IOPS 模式。 在此情況下，用戶端所達到的平均 IOPS 可能會低於基準 IOPS。

### <a name="workaround"></a>因應措施
- 從用戶端應用程式減少要求負載，如此共用就不會受到節流。
- 增加共用的配額，讓共用不會受到節流。

## <a name="excessive-directoryopendirectoryclose-calls"></a>過多的 DirectoryOpen/DirectoryClose 呼叫

### <a name="cause"></a>原因

如果 **DirectoryOpen/DirectoryClose** 呼叫數目是最常呼叫的 API，而您不希望用戶端進行許多呼叫，問題可能是安裝在 Azure 用戶端 VM 上的防毒軟體所造成。

### <a name="workaround"></a>因應措施

- 適用于 [Windows 的四月平臺更新](https://support.microsoft.com/help/4052623/update-for-windows-defender-antimalware-platform)有提供此問題的修正。

## <a name="file-creation-is-slower-than-expected"></a>檔案建立速度低於預期

### <a name="cause"></a>原因

依賴建立大量檔案的工作負載不會看到 premium 檔案共用與標準檔案共用之間的效能顯著差異。

### <a name="workaround"></a>因應措施

- 無。

## <a name="slow-performance-from-windows-81-or-server-2012-r2"></a>Windows 8.1 或伺服器 2012 R2 的效能變慢

### <a name="cause"></a>原因

針對需要大量 i/o 的工作負載，存取 Azure 檔案共用的預期延遲高於預期。

### <a name="workaround"></a>因應措施

- 安裝可用的修正 [程式](https://support.microsoft.com/help/3114025/slow-performance-when-you-access-azure-files-storage-from-windows-8-1)。

## <a name="how-to-create-an-alert-if-a-file-share-is-throttled"></a>如何在檔案共用已節流時建立警示

1. 在 Azure 入口網站中，移至您的儲存體帳戶。
1. 在 [ **監視** ] 區段中，選取 [ **警示** ]，然後選取 [ **新增警示規則** ]。
1. 選取 [ **編輯資源** ]，選取儲存體帳戶的檔案 **資源類型** ，然後選取 [ **完成** ]。 例如，如果儲存體帳戶名稱是 *contoso* ，請選取 contoso/file 資源。
1. 選取 [ **選取條件** ] 以新增條件。
1. 在儲存體帳戶支援的信號清單中，選取 [ **交易** ] 度量。
1. 在 [ **設定信號邏輯** ] 窗格的 [ **維度名稱** ] 下拉式清單中，選取 [ **回應類型** ]。
1. 在 [ **維度值** ] 下拉式清單中，選取 [適用于 SMB) 的 **SUCCESSWITHTHROTTLING** (或 REST) 的 **ClientThrottlingError** (。

   > [!NOTE]
   > 如果 **SuccessWithThrottling** 和 **ClientThrottlingError** 維度值都未列出，這表示資源尚未進行節流處理。 若要加入維度值，請選取 [ **維度值** ] 下拉式清單旁的 [ **新增自訂值** ]，輸入 **SuccessWithThrottling** 或 **ClientThrottlingError** ，選取 **[確定]** ，然後重複步驟7。

1. 在 [ **維度名稱** ] 下拉式清單中，選取 [檔案 **共用** ]。
1. 在 [ **維度值** ] 下拉式清單中，選取您要發出警示的檔案共用或共用。

   > [!NOTE]
   > 如果檔案共用是標準檔案共用，請選取 **所有目前及未來的值** 。 因為標準檔案共用無法使用個別共用的計量，所以 [維度值] 下拉式清單不會列出檔案共用。 如果儲存體帳戶內的任何檔案共用受到節流處理，而警示未識別已節流的檔案共用，則會觸發標準檔案共用的節流警示。 因為標準檔案共用無法使用個別共用的計量，所以建議您針對每個儲存體帳戶使用一個檔案共用。

1. 輸入 **臨界值** 、 **運算子** 、 **匯總細微性** 和 **評估頻率** 來定義警示參數，然後選取 [ **完成** ]。

    > [!TIP]
    > 如果您使用的是靜態閾值，則如果檔案共用目前正在進行節流，度量圖表可協助您判斷合理的臨界值。 如果您使用的是動態閾值，計量圖表會顯示以最近資料為基礎的計算臨界值。

1. 選取 [ **選取動作群組** ]，然後藉由選取現有的動作群組或建立新的動作群組，將動作群組 (例如，電子郵件或 SMS) 傳送至警示。
1. 輸入警示詳細資料，例如 [ **警示規則名稱** ]、[ **描述** ] 和 [ **嚴重性** ]。
1. 選取 [建立警示規則] 以建立警示。

若要深入瞭解如何在 Azure 監視器中設定警示，請參閱 [Microsoft Azure 中的警示總覽]( https://docs.microsoft.com/azure/azure-monitor/platform/alerts-overview)。

## <a name="how-to-create-alerts-if-a-premium-file-share-is-trending-toward-being-throttled"></a>如何在 premium 檔案共用的趨勢達到節流時建立警示

1. 在 Azure 入口網站中，移至您的儲存體帳戶。
1. 在 [ **監視** ] 區段中，選取 [ **警示** ]，然後選取 [ **新增警示規則** ]。
1. 選取 [ **編輯資源** ]，選取儲存體帳戶的檔案 **資源類型** ，然後選取 [ **完成** ]。 例如，如果儲存體帳戶名稱是 *contoso* ，請選取 contoso/file 資源。
1. 選取 [ **選取條件** ] 以新增條件。
1. 在儲存體帳戶支援的信號清單中，選取 **輸出度量。**

   > [!NOTE]
   > 當輸入、輸出或交易值超過您設定的閾值時，您必須建立三個不同的警示來發出警示。 這是因為只有在符合所有條件時才會觸發警示。 例如，如果您將所有條件放在一個警示中，只有當輸入、輸出和交易超過其閾值數量時，才會收到警示。

1. 向下捲動。 在 [ **維度名稱** ] 下拉式清單中，選取 [檔案 **共用** ]。
1. 在 [ **維度值** ] 下拉式清單中，選取您要發出警示的檔案共用或共用。
1. 藉由選取 [ **運算子** ]、[ **臨界值** ]、[ **匯總細微性** ] 和 [ **評估的頻率** ] 下拉式清單中的值，然後選取 [ **完成** ] 來定義警示參數。

   輸出、輸入和交易計量是以每分鐘表示，但每秒布建的輸出、輸入和 i/o。 因此，舉例來說，如果您布建的輸出是 &nbsp; 每秒90數量 (MiB/s) 而且您想要讓閾值成為布 &nbsp; 建輸出的80%，請選取下列警示參數： 
   - 針對 **臨界值** ： *75497472* 
   - For **運算子** ： *大於或等於*
   - 適用于 **匯總類型** ： *平均*
   
   根據您想要警示的雜訊程度，您也可以選取 **匯總細微性** 和 **評估頻率的** 值。 例如，如果您想要讓警示查看1小時期間的平均輸入，而且想要每小時執行一次警示規則，請選取下列各項：
   - 針對 **匯總細微性** ： *1 小時*
   - **評估頻率** ： *1 小時*

1. 選取 [ **選取動作群組** ]，然後藉由選取現有的動作群組或建立新的動作群組，將動作群組 (例如，電子郵件或 SMS) 傳送至警示。
1. 輸入警示詳細資料，例如 [ **警示規則名稱** ]、[ **描述** ] 和 [ **嚴重性** ]。
1. 選取 [建立警示規則] 以建立警示。

    > [!NOTE]
    > - 若要收到通知，表示您的 premium 檔案共用因為布 *建的* 輸入而接近進行節流處理，請遵循上述指示，但有下列變更：
    >    - 在步驟5中，選取 **輸入度量，而不是輸出** **。**
    >
    > - 若要收到通知，表示您的 premium 檔案共用因為布 *建的 IOPS* 而接近進行節流處理，請遵循上述指示，但有下列變更：
    >    - 在步驟5中，選取 **交易** 度量，而 **不是輸出。**
    >    - 在步驟10中，匯總 **類型** 的唯一選項是 *Total* 。 因此，臨界值取決於您選取的匯總細微性。 例如，如果您想要將閾值設為已布 &nbsp; 建基準 iops 的80%，而且您選取 *1 小時* 的 **匯總細微性** ，則您的 **臨界值** 會是基準 IOPS (位元組) &times; &nbsp; 0.8 &times; &nbsp; 3600。 

若要深入瞭解如何在 Azure 監視器中設定警示，請參閱 [Microsoft Azure 中的警示總覽]( https://docs.microsoft.com/azure/azure-monitor/platform/alerts-overview)。

## <a name="see-also"></a>另請參閱
- [針對 Windows 中的 Azure 檔案服務進行疑難排解](storage-troubleshoot-windows-file-connection-problems.md)  
- [針對 Linux 中的 Azure 檔案服務進行疑難排解](storage-troubleshoot-linux-file-connection-problems.md)  
- [Azure 檔案儲存體常見問題集](storage-files-faq.md)
