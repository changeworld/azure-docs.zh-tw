---
title: Azure 檔案儲存體效能疑難排解指南
description: 針對 Azure 檔案共用的已知效能問題進行疑難排解。 當遇到這些問題時，探索潛在的原因和相關的因應措施。
author: gunjanj
ms.service: storage
ms.topic: troubleshooting
ms.date: 08/24/2020
ms.author: gunjanj
ms.subservice: files
ms.openlocfilehash: fe1460d4353addff1b8e3095cfe06c1fcb3b7bd0
ms.sourcegitcommit: 9c3cfbe2bee467d0e6966c2bfdeddbe039cad029
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/24/2020
ms.locfileid: "88782365"
---
# <a name="troubleshoot-azure-files-performance-issues"></a>針對 Azure 檔案儲存體效能問題進行疑難排解

本文列出一些與 Azure 檔案共用相關的常見問題。 當遇到這些問題時，它會提供潛在的原因和因應措施。

## <a name="high-latency-low-throughput-and-general-performance-issues"></a>高延遲、低輸送量和一般效能問題

### <a name="cause-1-share-was-throttled"></a>原因1：已節流共用

當達到檔案共用的 IOPS、輸入或輸出限制時，就會對要求進行節流。 若要瞭解標準和 premium 檔案共用的限制，請參閱檔案 [共用和檔案調整目標](https://docs.microsoft.com/azure/storage/files/storage-files-scale-targets#file-share-and-file-scale-targets)。

若要確認您的共用是否正在進行節流處理，您可以在入口網站中運用 Azure 計量。

1. 登入 [Azure 入口網站](https://portal.azure.com)。

1. 選取 [ **所有服務** ]，然後搜尋 **度量**。

1. 選取 [計量]。

1. 選取您的儲存體帳戶做為資源。

1. 選取 **[** 檔案] 作為度量命名空間。

1. 選取 **交易** 作為度量。

1. 新增 **>responsetype** 的篩選器，並檢查是否有任何要求的 **SuccessWithThrottling** (的回應碼，適用于 SMB) 或 **ClientThrottlingError** (適用于 REST) 。

![Premium 檔案共用的計量選項](media/storage-troubleshooting-premium-fileshares/metrics.png)

> [!NOTE]
> 若要在檔案共用受到節流時收到警示，請參閱如何在檔案 [共用受到節流時建立警示](#how-to-create-an-alert-if-a-file-share-is-throttled)。

### <a name="solution"></a>解決方案

- 如果您使用標準檔案共用，請在儲存體帳戶上啟用 [大型檔案共用](https://docs.microsoft.com/azure/storage/files/storage-files-how-to-create-large-file-share?tabs=azure-portal) 。 大型檔案共用支援每個共用最高 10000 IOPS。
- 如果您使用 premium 檔案共用，請增加布建的檔案共用大小，以增加 IOPS 限制。 若要深入瞭解，請參閱《 Azure 檔案儲存體規劃指南》中的 [瞭解 premium 檔案共用](https://docs.microsoft.com/azure/storage/files/storage-files-planning#understanding-provisioning-for-premium-file-shares) 的布建一節。

### <a name="cause-2-metadatanamespace-heavy-workload"></a>原因2：中繼資料/命名空間繁重的工作負載

如果大部分的要求都是以中繼資料為中心， (例如 createfile/openfile/closefile/queryinfo/querydirectory) 則相較于讀取/寫入作業，延遲會較差。

若要確認您大部分的要求都是以中繼資料為中心，您可以使用與上述相同的步驟。 除了新增 **>responsetype**的篩選以外，請新增 **API 名稱**的篩選。

![篩選計量中的 API 名稱](media/storage-troubleshooting-premium-fileshares/MetadataMetrics.png)

### <a name="workaround"></a>因應措施

- 檢查是否可以修改應用程式，以減少中繼資料作業的數目。
- 在檔案共用上新增 VHD，並從用戶端透過 SMB 掛接 VHD，以對資料執行檔案作業。 這種方法適用于單一寫入器和多個讀取器案例，並允許在本機進行中繼資料作業，以提供與本機直接連接儲存體類似的效能。

### <a name="cause-3-single-threaded-application"></a>原因3：單一執行緒應用程式

如果客戶使用的應用程式是單一執行緒，這可能會導致 IOPS/輸送量明顯低於根據您布建的共用大小的最大可能值。

### <a name="solution"></a>解決方案

- 藉由增加執行緒數目來提高應用程式平行處理原則。
- 切換至可以平行處理的應用程式。 例如，針對複製作業，客戶可以從 Windows 用戶端使用 AzCopy 或 RoboCopy，或在 Linux 用戶端上使用 **parallel** 命令。

## <a name="very-high-latency-for-requests"></a>非常高的要求延遲

### <a name="cause"></a>原因

用戶端 VM 可能位於與檔案共用不同的區域。

### <a name="solution"></a>解決方案

- 從與檔案共用位於相同區域的 VM 執行應用程式。

## <a name="client-unable-to-achieve-maximum-throughput-supported-by-the-network"></a>用戶端無法達到網路支援的最大輸送量

其中一個可能的原因是缺少 SMB 多通道支援。 Azure 檔案共用目前僅支援單一通道，因此只有一個從用戶端 VM 到伺服器的連線。 此單一連線會限定為用戶端 VM 上的單一核心，因此可從 VM 達到的最大輸送量會受到單一核心的限制。

### <a name="workaround"></a>因應措施

- 取得具有較大核心的 VM 可能有助於提升輸送量。
- 從多個 Vm 執行用戶端應用程式會增加輸送量。

- 盡可能使用 REST Api。

## <a name="throughput-on-linux-clients-is-significantly-lower-when-compared-to-windows-clients"></a>相較于 Windows 用戶端，Linux 用戶端上的輸送量會大幅降低。

### <a name="cause"></a>原因

這是在 Linux 上執行 SMB 用戶端的已知問題。

### <a name="workaround"></a>因應措施

- 將負載分散到多個 Vm。
- 在相同的 VM 上，使用多個掛接點搭配 **nosharesock** 選項，並將負載分散到這些掛接點。
- 在 Linux 上，請嘗試裝載 **nostrictsync** 選項，以避免在每個 **fsync** 呼叫上強制執行 SMB 排清。 針對 Azure 檔案儲存體，此選項不會干擾資料一致性，但可能會導致目錄清單 (**ls-l** 命令) 的過時檔案中繼資料。 直接查詢檔案的中繼資料 (**stat** 命令) 將會傳回最新的檔案中繼資料。

## <a name="high-latencies-for-metadata-heavy-workloads-involving-extensive-openclose-operations"></a>大量中繼資料工作負載的高延遲性，涉及大量的開啟/關閉作業。

### <a name="cause"></a>原因

缺少目錄租用的支援。

### <a name="workaround"></a>因應措施

- 可能的話，請在短時間內避免相同目錄的過度開啟/結束控制碼。
- 針對 Linux Vm，請將**actimeo = \<sec> **指定為掛接選項，以增加目錄專案快取超時。 根據預設，這會是一秒，因此較大的值（例如三或五）可能會有所説明。
- 若為 Linux Vm，請將核心升級為4.20 或更高版本。

## <a name="low-iops-on-centosrhel"></a>CentOS/RHEL 的低 IOPS

### <a name="cause"></a>原因

CentOS/RHEL 不支援大於1的 IO 深度。

### <a name="workaround"></a>因應措施

- 升級至 CentOS 8/RHEL 8。
- 變更為 Ubuntu。

## <a name="slow-file-copying-to-and-from-azure-files-in-linux"></a>從 Linux 中的 Azure 檔案服務複製檔案或將檔案複製到其中的速度變慢

如果您在 Azure 檔案儲存體中遇到檔案複製的速度緩慢，請參閱 Linux 疑難排解指南中的 [Azure 檔案儲存體在 linux 中來回複製的速度慢](storage-troubleshoot-linux-file-connection-problems.md#slow-file-copying-to-and-from-azure-files-in-linux) 一節。

## <a name="jitterysaw-tooth-pattern-for-iops"></a>IOPS 的抖動/看到模式

### <a name="cause"></a>原因

用戶端應用程式一致地超過基準 IOPS。 目前不會對要求負載進行服務端平滑處理，因此，如果用戶端超過基準 IOPS，服務就會進行節流。 該節流可能會導致用戶端遇到抖動/已看到的 IOPS 模式。 在此情況下，用戶端所達到的平均 IOPS 可能會低於基準 IOPS。

### <a name="workaround"></a>因應措施

- 從用戶端應用程式減少要求負載，如此共用就不會受到節流。
- 增加共用的配額，讓共用不會受到節流。

## <a name="excessive-directoryopendirectoryclose-calls"></a>過多的 DirectoryOpen/DirectoryClose 呼叫

### <a name="cause"></a>原因

如果 DirectoryOpen/DirectoryClose 呼叫數目是最常呼叫的 API，而您不希望用戶端進行許多呼叫，則可能是安裝在 Azure 用戶端 VM 上的防毒軟體問題。

### <a name="workaround"></a>因應措施

- 適用于 [Windows 的四月平臺更新](https://support.microsoft.com/help/4052623/update-for-windows-defender-antimalware-platform)有提供此問題的修正。

## <a name="file-creation-is-slower-than-expected"></a>檔案建立速度低於預期

### <a name="cause"></a>原因

依賴建立大量檔案的工作負載不會看到 premium 檔案共用和標準檔案共用的效能有顯著的差異。

### <a name="workaround"></a>因應措施

- 無。

## <a name="slow-performance-from-windows-81-or-server-2012-r2"></a>Windows 8.1 或伺服器 2012 R2 的效能變慢

### <a name="cause"></a>原因

針對需要大量 IO 的工作負載，存取 Azure 檔案儲存體的預期延遲高於預期。

### <a name="workaround"></a>因應措施

- 安裝可用的修正 [程式](https://support.microsoft.com/help/3114025/slow-performance-when-you-access-azure-files-storage-from-windows-8-1)。

## <a name="how-to-create-an-alert-if-a-file-share-is-throttled"></a>如何在檔案共用已節流時建立警示

1. 移至您在**Azure 入口網站**中的**儲存體帳戶**。
2. 在 [監視] 區段中，按一下 [ **警示** ]，然後按一下 [ **+ 新增警示規則**]。
3. 按一下 [ **編輯資源**]，選取儲存體帳戶的檔案 **資源類型** ，然後按一下 [ **完成**]。 例如，如果儲存體帳戶名稱是 contoso，請選取 contoso/file 資源。
4. 按一下 [ **選取條件** ] 以新增條件。
5. 您將會看到儲存體帳戶所支援的信號清單，請選取 **交易** 度量。
6. 在 [ **設定信號邏輯** ] 分頁上，按一下 [ **維度名稱** ] 下拉式清單，然後選取 [ **回應類型**]。
7. 按一下 [ **維度值** ] 下拉式清單，然後選取 [ **SuccessWithThrottling** (（適用于 SMB）) 或 **ClientThrottlingError** (REST) 。

  > [!NOTE]
  > 如果未列出 SuccessWithThrottling 或 ClientThrottlingError 維度值，這表示資源尚未進行節流處理。 若要加入維度值，請按一下 [**維度值**] 下拉式清單旁的 [**加入自訂值**]，輸入**SuccessWithThrottling**或**ClientThrottlingError**，按一下 **[確定]** ，然後重複步驟 #7。

8. 按一下 [ **維度名稱** ] 下拉式清單，然後選取 [檔案 **共用**]。
9. 按一下 [ **維度值** ] 下拉式清單，然後選取您想要警示的檔案共用 (s) 。

  > [!NOTE]
  > 如果檔案共用是標準檔案共用，請選取 **所有目前及未來的值**。 [維度值] 下拉式清單不會列出檔案共用 (s) 因為標準檔案共用無法使用個別共用的計量。 如果儲存體帳戶內的任何檔案共用受到節流處理，而警示將不會識別已節流的檔案共用，則會觸發標準檔案共用的節流警示。 因為標準檔案共用未提供個別共用的計量，所以建議每個儲存體帳戶都有一個檔案共用。

10. 定義 **警示參數** (臨界值、運算子、匯總細微性和評估的頻率) 然後按一下 [ **完成**]。

  > [!TIP]
  > 如果您使用的是靜態閾值，則如果檔案共用目前正在進行節流，則度量圖表有助於判斷合理的臨界值。 如果您使用的是動態閾值，計量圖表會顯示以最近資料為基礎的計算臨界值。

11. 按一下 [ **選取動作群組** ]，透過選取現有的動作群組或建立新的動作群組，將 **動作群組** 新增 (電子郵件、SMS 等 ) 至警示。
12. 填入警示 **詳細資料** ，例如 **警示規則名稱**、 **描述** 和 **嚴重性**。
13. 按一下 [ **建立警示規則** ] 以建立警示。

若要深入瞭解如何在 Azure 監視器中設定警示，請參閱 [Microsoft Azure 中的警示總覽]( https://docs.microsoft.com/azure/azure-monitor/platform/alerts-overview)。

## <a name="see-also"></a>另請參閱
* [針對 Windows 中的 Azure 檔案服務進行疑難排解](storage-troubleshoot-windows-file-connection-problems.md)
* [針對 Linux 中的 Azure 檔案服務進行疑難排解](storage-troubleshoot-linux-file-connection-problems.md)
* [關於 Azure 檔案服務的常見問題集 (FAQ)](storage-files-faq.md)
