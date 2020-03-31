---
title: Azure 檔性能故障排除指南
description: Azure 檔共用和相關解決方法的已知性能問題。
author: gunjanj
ms.service: storage
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: gunjanj
ms.subservice: files
ms.openlocfilehash: 09e55abcd97317b87f8a272afa51c6b4ace572e8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77598080"
---
# <a name="troubleshoot-azure-files-performance-issues"></a>解決 Azure 檔性能問題

本文列出了與 Azure 檔共用相關的一些常見問題。 當遇到這些問題時，它提供了潛在的原因和解決方法。

## <a name="high-latency-low-throughput-and-general-performance-issues"></a>高延遲、低輸送量和一般性能問題

### <a name="cause-1-share-experiencing-throttling"></a>原因 1：共用體驗限制

高級共用的預設配額為 100 GiB，它提供了 100 個基準 IOPS（一小時可能會爆裂 300 個）。 有關預配及其與 IOPS 的關係的詳細資訊，請參閱規劃指南的[預配共用](storage-files-planning.md#understanding-provisioning-for-premium-file-shares)部分。

要確認共用是否受到限制，可以在門戶中利用 Azure 指標。

1. 登錄到 Azure[門戶](https://portal.azure.com)。

1. 選擇**所有服務**，然後搜索**指標**。

1. 選擇**指標**。

1. 選擇存儲帳戶作為資源。

1. 選擇 **"檔**"作為指標命名空間。

1. 選擇**交易記錄**作為指標。

1. 為**回應類型**添加篩選器，並檢查是否有任何請求具有 **"成功與旋轉**"（用於 SMB）或**用戶端旋轉錯誤**（用於 REST）的回應代碼。

![高級檔共用的指標選項](media/storage-troubleshooting-premium-fileshares/metrics.png)

> [!NOTE]
> 要在檔共用被限制時接收警報，請參閱[如何在檔共用被限制時創建警報](#how-to-create-an-alert-if-a-file-share-is-throttled)。

### <a name="solution"></a>解決方法

- 通過在共用上指定更高的配額來增加份額預配容量。

### <a name="cause-2-metadatanamespace-heavy-workload"></a>原因 2：中繼資料/命名空間繁重的工作負載

如果大多數請求以中繼資料為中心（例如創建檔/打開檔/關閉檔/查詢資訊/查詢目錄），則與讀/寫操作相比，延遲將更為嚴重。

要確認大多數請求是否以中繼資料為中心，可以使用與上述相同的步驟。 除了不添加**回應類型的**篩選器外，添加**API 名稱**的篩選器。

![指標中的 API 名稱篩選器](media/storage-troubleshooting-premium-fileshares/MetadataMetrics.png)

### <a name="workaround"></a>因應措施

- 檢查是否可以修改應用程式以減少中繼資料操作的數量。
- 在檔共用上添加 VHD，並從用戶端將 VHD 裝載到 SMB 上，以便對資料執行檔操作。 此方法適用于單個編寫器和多個讀取器方案，並允許中繼資料操作為本地操作，提供類似于本地直接連接存儲的性能。

### <a name="cause-3-single-threaded-application"></a>原因 3：單線程應用程式

如果客戶使用的應用程式是單線程的，則根據預配的共用大小，這可能導致 IOPS/輸送量明顯低於最大可能。

### <a name="solution"></a>解決方法

- 通過增加執行緒數來增加應用程式並行性。
- 切換到可能並行化的應用程式。 例如，對於複製操作，客戶可以使用 Windows 用戶端的 AzCopy 或 RoboCopy，也可以使用 Linux 用戶端上的**並行**命令。

## <a name="very-high-latency-for-requests"></a>請求延遲非常高

### <a name="cause"></a>原因

用戶端 VM 可以位於與檔共用不同的區域中。

### <a name="solution"></a>解決方法

- 從與檔共用位於同一區域的 VM 運行應用程式。

## <a name="client-unable-to-achieve-maximum-throughput-supported-by-the-network"></a>用戶端無法實現網路支援的最大輸送量

造成這種情況的一個潛在原因是缺乏離岸 SMB 多管道支援。 目前，Azure 檔共用僅支援單個通道，因此從用戶端 VM 到伺服器只有一個連接。 此單個連接與用戶端 VM 上的單個內核相關聯，因此從 VM 可實現的最大輸送量由單個內核綁定。

### <a name="workaround"></a>因應措施

- 使用更大的內核獲取 VM 可能有助於提高輸送量。
- 從多個 VM 運行用戶端應用程式將提高輸送量。

- 盡可能使用 REST API。

## <a name="throughput-on-linux-clients-is-significantly-lower-when-compared-to-windows-clients"></a>與 Windows 用戶端相比，Linux 用戶端的輸送量要低得多。

### <a name="cause"></a>原因

這是一個已知的問題，與在Linux上實現SMB用戶端。

### <a name="workaround"></a>因應措施

- 將負載分散到多個 VM 上。
- 在同一 VM 上，使用具有**nosharesock**選項的多個裝載點，並將負載分散到這些裝載點上。
- 在 Linux 上，嘗試安裝**帶有無嚴格同步**選項，以避免強制 SMB 刷新每個**fsync**調用。 對於 Azure 檔，此選項不會干擾資料一致性，但可能會導致目錄清單 **（ls-l**命令） 上的檔中繼資料陳舊。 直接查詢檔中繼資料 **（stat**命令）將返回最新的檔中繼資料。

## <a name="high-latencies-for-metadata-heavy-workloads-involving-extensive-openclose-operations"></a>涉及大量開/關操作的中繼資料繁重工作負載的延遲率高。

### <a name="cause"></a>原因

不支援目錄租約。

### <a name="workaround"></a>因應措施

- 如果可能，避免在短時間內在同一目錄上過多的打開和關閉控制碼。
- 對於 Linux VM，通過將**actimeo_\<sec>** 指定為裝載選項來增加目錄條目緩存超時。 預設情況下，它是一秒，因此像三或五個這樣的較大值可能會有所説明。
- 對於 Linux VM，將內核升級到 4.20 或更高版本。

## <a name="low-iops-on-centosrhel"></a>CentOS/RHEL 上的低 IOPS

### <a name="cause"></a>原因

CentOS/RHEL 不支援大於 1 的 IO 深度。

### <a name="workaround"></a>因應措施

- 升級到 CentOS 8 / RHEL 8。
- 更改為 Ubuntu。

## <a name="slow-file-copying-to-and-from-azure-files-in-linux"></a>從 Linux 中的 Azure 檔案服務複製檔案或將檔案複製到其中的速度變慢

如果遇到與 Azure 檔案複製和從 Azure 檔案複製的速度很慢，請查看 Linux 疑難排解指南中 Linux 部分[中的"緩慢檔案複製"和"從 Azure 檔案複製](storage-troubleshoot-linux-file-connection-problems.md#slow-file-copying-to-and-from-azure-files-in-linux)"。

## <a name="jitterysaw-tooth-pattern-for-iops"></a>IOPS 的抖動/鋸齒圖案

### <a name="cause"></a>原因

用戶端應用程式始終超過基線 IOPS。 目前，請求負載沒有服務端平滑，因此，如果用戶端超過基線 IOPS，則服務將限制它。 這種限制可能導致用戶端遇到抖動/鋸齒 IOPS 模式。 在這種情況下，用戶端實現的平均 IOPS 可能低於基線 IOPS。

### <a name="workaround"></a>因應措施

- 減少來自用戶端應用程式的請求負載，以便共用不會受到限制。
- 增加共用的配額，以便共用不會受到限制。

## <a name="excessive-directoryopendirectoryclose-calls"></a>目錄打開/目錄關閉呼叫過多

### <a name="cause"></a>原因

如果目錄 Open/DirectoryClose 調用的數量屬於頂級 API 呼叫，並且您不希望用戶端進行那麼多調用，則可能是 Azure 用戶端 VM 上安裝的防病毒問題。

### <a name="workaround"></a>因應措施

- 此問題的修復程式可在[4 月平臺更新的 Windows](https://support.microsoft.com/help/4052623/update-for-windows-defender-antimalware-platform)中提供。

## <a name="file-creation-is-slower-than-expected"></a>檔創建速度比預期慢

### <a name="cause"></a>原因

依賴于創建大量檔的工作負載不會看到高級檔共用和標準檔共用的性能之間存在顯著差異。

### <a name="workaround"></a>因應措施

- 無。

## <a name="slow-performance-from-windows-81-or-server-2012-r2"></a>Windows 8.1 或伺服器 2012 R2 的性能降低

### <a name="cause"></a>原因

訪問 Azure 檔的 Azure 檔的延遲高於預期，用於 IO 密集型工作負荷。

### <a name="workaround"></a>因應措施

- 安裝可用的[修補程式](https://support.microsoft.com/help/3114025/slow-performance-when-you-access-azure-files-storage-from-windows-8-1)。

## <a name="how-to-create-an-alert-if-a-file-share-is-throttled"></a>如果檔共用受到限制，如何創建警報

1. 在[Azure 門戶](https://portal.azure.com)中，按一下**監視器**。 

2. 按一下 **"警報"，** 然後按一下 **"新警報規則**"。

3. 按一下 **"選擇"** 以選擇包含要提醒的檔共用的**存儲帳戶/檔**資源，然後按一下"**完成**"。 例如，如果存儲帳戶名稱是 contoso，請選擇 contoso/檔資源。

4. 按一下"**添加**"以添加條件。

5. 您將看到存儲帳戶支援的信號清單，選擇**交易記錄**指標。

6. 在 **"配置信號邏輯**"邊欄選項卡上，轉到**回應類型**維度，按一下"**維度值**下拉"，然後選擇 **"成功與旋轉**（用於 SMB）"或 **"用戶端旋轉錯誤**"（用於 REST）。 

  > [!NOTE]
  > 如果未列出成功與旋轉或用戶端限制錯誤維度值，則表示資源未被限制。  要添加維度值，**+** 請按一下 **"維度值**"旁邊的下拉清單，鍵入 **"成功與旋轉**"或 **"用戶端旋轉錯誤**"，按一下"**確定**"，然後#6重複步驟。

7. 轉到 **"檔共用**"維度，按一下"**維度"值**下拉清單，然後選擇要提醒的檔共用。 

  > [!NOTE]
  > 如果檔共用是標準檔共用，則維度值下拉將為空，因為每股指標不適用於標準檔共用。 如果存儲帳戶中的任何檔共用受到限制，並且警報不會標識哪個檔共用被限制，將觸發標準檔共用的限制警報。 由於每股指標不適用於標準檔共用，因此建議每個存儲帳戶有一個檔共用。 

8. 定義用於評估指標警報規則的**警報參數**（閾值、運算子、聚合細微性和頻率），然後按一下 **"完成**"。

  > [!TIP]
  > 如果使用靜態閾值，則指標圖表可説明確定檔共用當前被限制的合理閾值。 如果使用動態閾值，則指標圖表將顯示基於最近資料的計算閾值。

9. 通過選擇現有操作組或創建新操作組，將**操作組**（電子郵件、SMS 等）添加到警報中。

10. 填寫**警報詳細資訊**，如**警報規則名稱**、**描述**和**嚴重性**。

11. 按一下 **"創建警報規則**"以創建警報。

要瞭解有關在 Azure 監視器中配置警報的詳細資訊，請參閱[Microsoft Azure 中的警報概述]( https://docs.microsoft.com/azure/azure-monitor/platform/alerts-overview)。
