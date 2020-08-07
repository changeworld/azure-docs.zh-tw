---
title: Azure 檔案儲存體效能疑難排解指南
description: 針對 Azure 檔案共用的已知效能問題進行疑難排解。 發現這些問題發生時，可能的原因和相關的因應措施。
author: gunjanj
ms.service: storage
ms.topic: troubleshooting
ms.date: 04/25/2019
ms.author: gunjanj
ms.subservice: files
ms.openlocfilehash: ceadc2d37b9d13502b5ae20605ff083edfd5c51f
ms.sourcegitcommit: 25bb515efe62bfb8a8377293b56c3163f46122bf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/07/2020
ms.locfileid: "87986996"
---
# <a name="troubleshoot-azure-files-performance-issues"></a>針對 Azure 檔案儲存體效能問題進行疑難排解

本文列出一些與 Azure 檔案共用相關的常見問題。 當遇到這些問題時，它會提供可能的原因和因應措施。

## <a name="high-latency-low-throughput-and-general-performance-issues"></a>高延遲、低輸送量和一般效能問題

### <a name="cause-1-share-experiencing-throttling"></a>原因1：共用遇到節流

Premium 共用上的預設配額為 100 GiB，可提供100基準 IOPS (，一小時內最高可達 300) 。 如需布建和其 IOPS 關聯性的詳細資訊，請參閱《規劃指南》中的布[建的共用](storage-files-planning.md#understanding-provisioning-for-premium-file-shares)一節。

若要確認您的共用是否正在進行節流處理，您可以在入口網站中利用 Azure 計量。

1. 登入 [Azure 入口網站](https://portal.azure.com)。

1. 選取 [**所有服務**]，然後搜尋 [**計量**]。

1. 選取 [計量]。

1. 選取您的儲存體帳戶作為資源。

1. 選取 **[** 檔案] 做為計量命名空間。

1. 選取 [**交易**] 作為 [度量]。

1. 新增**ResponseType**的篩選器，並查看是否有任何要求的回應碼為 SMB) 的**SuccessWithThrottling** (，或適用于 REST) 的**ClientThrottlingError** (。

![Premium 檔案共用的計量選項](media/storage-troubleshooting-premium-fileshares/metrics.png)

> [!NOTE]
> 若要在檔案共用受到節流處理時收到警示，請參閱如何在檔案[共用已節流時建立警示](#how-to-create-an-alert-if-a-file-share-is-throttled)。

### <a name="solution"></a>解決方法

- 藉由在您的共用上指定較高的配額，以增加共用布建的容量。

### <a name="cause-2-metadatanamespace-heavy-workload"></a>原因2：中繼資料/命名空間繁重的工作負載

如果大部分的要求都是以中繼資料為中心， (例如 createfile/openfile/對應 closefile/queryinfo/querydirectory) 則相較于讀取/寫入作業，延遲將會較差。

若要確認您的大部分要求是否以中繼資料為中心，您可以使用與上述相同的步驟。 除了新增**ResponseType**的篩選準則以外，請新增**API 名稱**的篩選。

![篩選計量中的 API 名稱](media/storage-troubleshooting-premium-fileshares/MetadataMetrics.png)

### <a name="workaround"></a>因應措施

- 檢查是否可以修改應用程式，以減少中繼資料作業的數目。
- 在檔案共用上新增 VHD，並從用戶端透過 SMB 掛接 VHD，以對資料執行檔案作業。 這種方法適用于單一寫入器和多個讀取器案例，可讓中繼資料作業成為本機，以提供類似本機直接附加儲存體的效能。

### <a name="cause-3-single-threaded-application"></a>原因3：單一執行緒應用程式

如果客戶使用的應用程式是單一執行緒，這可能會導致 IOPS/輸送量明顯低於根據您布建的共用大小所可能的最大值。

### <a name="solution"></a>解決方法

- 藉由增加執行緒的數目來增加應用程式平行處理原則。
- 切換至可以平行處理的應用程式。 例如，對於複製作業，客戶可以從 Windows 用戶端使用 AzCopy 或 RoboCopy，或在 Linux 用戶端上使用**parallel**命令。

## <a name="very-high-latency-for-requests"></a>非常高的要求延遲

### <a name="cause"></a>原因

用戶端 VM 可能位於與檔案共用不同的區域中。

### <a name="solution"></a>解決方法

- 從與檔案共用位於相同區域的 VM 執行應用程式。

## <a name="client-unable-to-achieve-maximum-throughput-supported-by-the-network"></a>用戶端無法達到網路支援的最大輸送量

其中一個可能的原因是缺少 SMB 多通道支援。 目前，Azure 檔案共用僅支援單一通道，因此只有一個從用戶端 VM 到伺服器的連線。 此單一連線已限定為用戶端 VM 上的單一核心，因此可從 VM 達到的最大輸送量是由單一核心所系結。

### <a name="workaround"></a>因應措施

- 取得具有較大核心的 VM 可能有助於改善輸送量。
- 從多個 Vm 執行用戶端應用程式將會增加輸送量。

- 盡可能使用 REST Api。

## <a name="throughput-on-linux-clients-is-significantly-lower-when-compared-to-windows-clients"></a>相較于 Windows 用戶端，Linux 用戶端上的輸送量會大幅降低。

### <a name="cause"></a>原因

這是在 Linux 上執行 SMB 用戶端的已知問題。

### <a name="workaround"></a>因應措施

- 將負載分散到多個 Vm。
- 在相同的 VM 上，使用多個掛接點搭配**nosharesock**選項，並將負載分散到這些掛接點。
- 在 Linux 上，請嘗試使用**nostrictsync**選項掛接，以避免在每次**fsync**呼叫時強制執行 SMB 清除。 針對 Azure 檔案儲存體，此選項不會影響資料一致性，但可能會導致目錄清單中的過時檔案中繼資料 (**ls-l**命令) 。 直接查詢 file (**stat**命令的中繼資料) 會傳回最新的檔案中繼資料。

## <a name="high-latencies-for-metadata-heavy-workloads-involving-extensive-openclose-operations"></a>包含大量開啟/關閉作業的中繼資料繁重工作負載的高延遲。

### <a name="cause"></a>原因

缺少目錄租用的支援。

### <a name="workaround"></a>因應措施

- 可能的話，請在短時間內避免在同一個目錄上有過度的開啟/關閉控制碼。
- 針對 Linux Vm，請將**actimeo = \<sec> **指定為掛接選項，以增加目錄專案快取超時。 根據預設，它是一秒，因此較大的值（例如三或五）可能會有説明。
- 針對 Linux Vm，請將核心升級為4.20 或更高版本。

## <a name="low-iops-on-centosrhel"></a>CentOS/RHEL 上的低 IOPS

### <a name="cause"></a>原因

CentOS/RHEL 不支援大於1的 IO 深度。

### <a name="workaround"></a>因應措施

- 升級至 CentOS 8/RHEL 8。
- 變更為 Ubuntu。

## <a name="slow-file-copying-to-and-from-azure-files-in-linux"></a>從 Linux 中的 Azure 檔案服務複製檔案或將檔案複製到其中的速度變慢

如果您遇到 Azure 檔案儲存體的檔案複製速度變慢，請參閱 Linux 疑難排解指南中的[從 linux Azure 檔案儲存體複製速度緩慢](storage-troubleshoot-linux-file-connection-problems.md#slow-file-copying-to-and-from-azure-files-in-linux)的檔案一節。

## <a name="jitterysaw-tooth-pattern-for-iops"></a>抖動/看到的 IOPS 模式

### <a name="cause"></a>原因

用戶端應用程式一致地超過基準 IOPS。 目前，要求負載沒有服務端平滑處理，因此，如果用戶端超過基準 IOPS，服務就會對其進行節流。 該節流會導致用戶端遇到抖動/已看到的 IOPS 模式。 在此情況下，用戶端所達到的平均 IOPS 可能低於基準 IOPS。

### <a name="workaround"></a>因應措施

- 減少用戶端應用程式的要求負載，讓共用不會受到節流。
- 增加共用的配額，讓共用不會受到節流。

## <a name="excessive-directoryopendirectoryclose-calls"></a>過多的 DirectoryOpen/DirectoryClose 呼叫

### <a name="cause"></a>原因

如果 DirectoryOpen/DirectoryClose 呼叫的數目是在最上層的 API 呼叫中，而您不希望用戶端進行多次呼叫，則可能是 Azure 用戶端 VM 上安裝了防毒軟體的問題。

### <a name="workaround"></a>因應措施

- [Windows 的四月平臺更新](https://support.microsoft.com/help/4052623/update-for-windows-defender-antimalware-platform)提供此問題的修正。

## <a name="file-creation-is-slower-than-expected"></a>檔案建立速度比預期慢

### <a name="cause"></a>原因

依賴建立大量檔案的工作負載不會在高階檔案共用與標準檔案共用的效能上看到明顯的差異。

### <a name="workaround"></a>因應措施

- 無。

## <a name="slow-performance-from-windows-81-or-server-2012-r2"></a>Windows 8.1 或伺服器 2012 R2 的效能變慢

### <a name="cause"></a>原因

高於存取 IO 密集型工作負載 Azure 檔案儲存體所需的延遲。

### <a name="workaround"></a>因應措施

- 安裝可用的[修補程式](https://support.microsoft.com/help/3114025/slow-performance-when-you-access-azure-files-storage-from-windows-8-1)。

## <a name="how-to-create-an-alert-if-a-file-share-is-throttled"></a>如何在檔案共用已節流時建立警示

1. 在**Azure 入口網站**中，移至您的**儲存體帳戶**。
2. 在 [監視] 區段中，按一下 [**警示**]，然後按一下 [ **+ 新增警示規則**]。
3. 按一下 [**編輯資源**]，選取儲存體帳戶的 [檔案]**資源類型**，然後按一下 [**完成**]。 例如，如果儲存體帳戶名稱是 contoso，請選取 [contoso/file] 資源。
4. 按一下 [**選取條件**] 來新增條件。
5. 您會看到儲存體帳戶支援的信號清單，請選取 [**交易**] 計量。
6. 在 [**設定信號邏輯**] 分頁上，按一下 [**維度名稱**] 下拉式選，然後選取 [**回應類型**]。
7. 按一下 [**維度值**] 下拉式選單，然後選取 [SMB) 的**SuccessWithThrottling** (] 或 [ **ClientThrottlingError** REST) 的 (。

> [!NOTE]
> 如果未列出 [SuccessWithThrottling] 或 [ClientThrottlingError] 維度值，這表示資源尚未進行節流。 若要加入維度值，請按一下 [**維度值**] 下拉式標題旁的 [**加入自訂值**]，輸入**SuccessWithThrottling**或**ClientThrottlingError**，按一下 **[確定]** ，然後重複步驟 #7。

8. 按一下 [**維度名稱**] 下拉式選單，然後選取 [檔案**共用**]。
9. 按一下 [**維度值**] 下拉式選單，然後選取您想要警示的檔案共用 (s) 。

> [!NOTE]
> 如果檔案共用是標準檔案共用，請選取 [**所有目前和未來的值**]。 [維度值] 下拉式清單不會列出檔案共用 () 因為標準檔案共用無法使用個別共用的計量。 如果儲存體帳戶中的任何檔案共用受到節流處理，且警示無法識別已節流的檔案共用，則會觸發標準檔案共用的節流警示。 由於標準檔案共用不提供每個共用的計量，因此建議每個儲存體帳戶都有一個檔案共用。

10. 定義**警示參數** (臨界值、運算子、匯總資料細微性和評估的頻率) 然後按一下 [**完成**]。

> [!TIP]
> 如果您使用靜態閾值，則如果檔案共用目前正在進行節流，計量圖表可以協助判斷合理的臨界值。 如果您使用的是動態閾值，計量圖表會顯示以最近資料為基礎的計算臨界值。

11. 按一下 [**選取動作群組**]，藉由選取現有的動作群組或建立新的動作群組，將**動作群組** (電子郵件、SMS 等 ) 新增至警示。
12. 填寫**警示詳細資料**，例如**警示規則名稱**、**描述**和**嚴重性**。
13. 按一下 [**建立警示規則**] 來建立警示。

若要深入瞭解如何在 Azure 監視器中設定警示，請參閱[Microsoft Azure 中的警示總覽]( https://docs.microsoft.com/azure/azure-monitor/platform/alerts-overview)。
