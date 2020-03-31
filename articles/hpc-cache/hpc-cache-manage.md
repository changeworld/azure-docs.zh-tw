---
title: 管理和更新 Azure HPC 緩存
description: 如何使用 Azure 門戶管理和更新 Azure HPC 緩存
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 1/29/2020
ms.author: rohogue
ms.openlocfilehash: da260074fc69fac9e98d3698bb2d40fdf80d7118
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77252037"
---
# <a name="manage-your-cache-from-the-azure-portal"></a>從 Azure 門戶管理緩存

Azure 門戶中的緩存概覽頁顯示緩存的專案詳細資訊、緩存狀態和基本統計資訊。 它還具有停止或啟動緩存、刪除緩存、將資料刷新到長期存儲以及更新軟體的控制項。

要打開概覽頁面，請在 Azure 門戶中選擇緩存資源。 例如，載入 **"所有資源"** 頁並按一下緩存名稱。

![Azure HPC 緩存實例的概述頁面的螢幕截圖](media/hpc-cache-overview.png)

頁面頂部的按鈕可以説明您管理緩存：

* **啟動**和[**停止**](#stop-the-cache)- 掛起快取作業
* [**刷新**](#flush-cached-data)- 將更改的資料寫入存儲目標
* [**升級**](#upgrade-cache-software)- 更新緩存軟體
* **刷新**- 重新載入概覽頁面
* [**刪除**](#delete-the-cache)- 永久銷毀緩存

閱讀下面有關這些選項的詳細資訊。

## <a name="stop-the-cache"></a>停止緩存

您可以停止緩存以在非活動期間降低成本。 在停止緩存時，不會為停機時間付費，但會為緩存分配的磁片存儲付費。 （有關詳細資訊，請參閱[定價](https://aka.ms/hpc-cache-pricing)頁面。

已停止的緩存不回應用戶端請求。 在停止緩存之前，應卸載用戶端。

"**停止"** 按鈕將掛起活動緩存。 當緩存的狀態**為"正常**"或 **"已降級"** 時，"**停止"** 按鈕可用。

![頂部按鈕的螢幕截圖，其中突出顯示了"停止"，並彈出了一條描述停止操作的彈出消息，並詢問"是否要繼續？ 帶"是"（預設）和"否"按鈕](media/stop-cache.png)

按一下"是"以確認停止緩存後，緩存會自動將其內容刷新到存儲目標。 此過程可能需要一些時間，但它可確保資料一致性。 最後，緩存狀態更改為 **"已停止**"。

要重新啟動已停止的緩存，請按一下"**開始"** 按鈕。 無需確認。

![突出顯示"開始"的頂部按鈕的螢幕截圖](media/start-cache.png)

## <a name="flush-cached-data"></a>刷新緩存的資料

概覽頁上的 **"刷新"** 按鈕告訴緩存立即將緩存中存儲的所有已更改資料寫入後端存儲目標。 緩存會定期將資料保存到存儲目標，因此，除非您希望確保後端存儲系統是最新的，否則無需手動執行此操作。 例如，在拍攝存儲快照或檢查資料集大小之前，可以使用**Flush。**

> [!NOTE]
> 在刷新過程中，緩存無法為用戶端請求提供服務。 緩存訪問將掛起並在操作完成後繼續。

![頂部按鈕的螢幕截圖，其中突出顯示了 Flush，並彈出了一條描述刷新操作並詢問"是否要繼續？ 帶"是"（預設）和"否"按鈕](media/hpc-cache-flush.png)

啟動緩存刷新操作時，緩存將停止接受用戶端請求，概覽頁上的緩存狀態將更改為**法拉盛**。

緩存中的資料將保存到相應的存儲目標。 根據需要刷新的資料量，該過程可能需要幾分鐘或更長時間。

將所有資料保存到存儲目標後，緩存將自動開始再次獲取用戶端請求。 緩存狀態返回到 **"正常**"。

## <a name="upgrade-cache-software"></a>升級緩存軟體

如果新軟體版本可用，**升級**按鈕將變為活動狀態。 您還應在頁面頂部看到有關更新軟體的消息。

![啟用了"升級"按鈕的按鈕頂行螢幕截圖](media/hpc-cache-upgrade-button.png)

在軟體升級期間，用戶端存取不會中斷，但緩存性能會降低。 計畫在非高峰使用時間或計畫維護期間升級軟體。

軟體更新可能需要幾個小時。 配置具有更高輸送量的緩存比峰值輸送量值較小的緩存需要更長的時間來升級。

當軟體升級可用時，您將有一周左右時間手動應用。 結束日期列在升級消息中。 如果在此期間不升級，Azure 將自動將更新應用於緩存。 自動升級的時間不可配置。 如果您擔心緩存性能的影響，則應在時間段到期之前自行升級軟體。

如果緩存在結束日期過去時停止，則緩存將在下次啟動時自動升級軟體。 （更新可能不會立即啟動，但將在第一個小時內啟動。

按一下 **"升級**"按鈕開始軟體更新。 緩存狀態更改為 **"升級**"，直到操作完成。

## <a name="delete-the-cache"></a>刪除快取

"**刪除**"按鈕將銷毀緩存。 刪除緩存時，其所有資源都將銷毀，不再產生帳戶費用。

刪除緩存時，用作存儲目標的後端存儲卷不受影響。 您可以稍後將它們添加到將來的緩存中，也可以將它們分別停用。

> [!NOTE]
> Azure HPC 緩存在刪除緩存之前不會自動將更改的資料從緩存寫入後端存儲系統。
>
> 為了確保緩存中的所有資料已寫入長期存儲，請[停止緩存](#stop-the-cache)，然後再將其刪除。 在按一下刪除按鈕之前，請確保它顯示狀態 **"已停止**"。
<!--... written to long-term storage, follow this procedure:
>
> 1. [Remove](hpc-cache-edit-storage.md#remove-a-storage-target) each storage target from the Azure HPC Cache by using the delete button on the Storage targets page. The system automatically writes any changed data from the cache to the back-end storage system before removing the target.
> 1. Wait for the storage target to be completely removed. The process can take an hour or longer if there is a lot of data to write from the cache. When it is done, a portal notification says that the delete operation was successful, and the storage target disappears from the list.
> 1. After all affected storage targets have been deleted, it is safe to delete the cache.
>
> Alternatively, you can use the [flush](#flush-cached-data) option to save cached data, but there is a small risk of losing work if a client writes a change to the cache after the flush completes but before the cache instance is destroyed.-->

## <a name="cache-metrics-and-monitoring"></a>緩存指標和監視

概覽頁顯示一些基本緩存統計資訊的圖形 - 緩存輸送量、每秒操作和延遲。

![顯示上述示例緩存的統計資訊的三個線圖的螢幕截圖](media/hpc-cache-overview-stats.png)

這些圖表是 Azure 內置監視和分析工具的一部分。 門戶側邊欄中的 **"監視"** 標題下的頁面中提供了其他工具和警報。 在[Azure 監視文檔](../azure-monitor/insights/monitor-azure-resource.md#monitoring-in-the-azure-portal)的門戶部分中瞭解更多資訊。

## <a name="next-steps"></a>後續步驟

<!-- * Learn more about metrics and statistics for hpc cache -->
* 瞭解有關[Azure 指標和統計資訊工具的更多](../azure-monitor/index.yml)
* 獲取有關[Azure HPC 緩存的説明](hpc-cache-support-ticket.md)
