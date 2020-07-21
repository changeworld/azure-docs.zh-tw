---
title: 管理及更新 Azure HPC Cache
description: 如何使用 Azure 入口網站或 Azure CLI 管理及更新 Azure HPC 快取
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 07/08/2020
ms.author: v-erkel
ms.openlocfilehash: 66b084cca3d1cd54362a538423988755a3d31ced
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/20/2020
ms.locfileid: "86497213"
---
# <a name="manage-your-cache"></a>管理您的快取

[Azure 入口網站中的 [快取總覽] 頁面會顯示您快取的專案詳細資料、快取狀態和基本統計資料。 它也有控制項可以停止或啟動快取、刪除快取、將資料排清至長期儲存區，以及更新軟體。

本文也會說明如何使用 Azure CLI 執行這些基本工作。

若要開啟 [總覽] 頁面，請在 [Azure 入口網站中選取您的快取資源。 例如，載入 [**所有資源**] 頁面，然後按一下快取名稱。

![Azure HPC 快取實例 [總覽] 頁面的螢幕擷取畫面](media/hpc-cache-overview.png)

頁面頂端的按鈕可協助您管理快取：

* **啟動**和[**停止**](#stop-the-cache)-繼續或暫停快取操作
* [**Flush**](#flush-cached-data) -將變更的資料寫入儲存體目標
* [**升級**](#upgrade-cache-software)-更新快取軟體
* 重新**整理-重載**[總覽] 頁面
* [**刪除**](#delete-the-cache)-永久終結快取

如需這些選項的詳細資訊，請參閱下方。

按一下下方影像觀看示範快取管理工作的[影片](https://azure.microsoft.com/resources/videos/managing-hpc-cache/)。

[![影片縮圖： Azure HPC 快取：管理（按一下以流覽影片頁面）](media/video-5-manage.png)](https://azure.microsoft.com/resources/videos/managing-hpc-cache/)

## <a name="stop-the-cache"></a>停止快取

您可以停止快取，以降低非使用中期間的成本。 當快取停止時，您不需支付執行時間費用，但會向您收取快取已配置磁片儲存體的費用。 （如需詳細資訊，請參閱[定價](https://aka.ms/hpc-cache-pricing)頁面）。

已停止的快取不會回應用戶端要求。 您應該先卸載用戶端，然後再停止快取。

### <a name="portal"></a>[入口網站](#tab/azure-portal)

[**停止**] 按鈕會暫停作用中的快取。 當快取狀態為 [**狀況良好**] 或 [**降級**] 時，就可以使用 [**停止**] 按鈕。

![具有 [停止] 反白顯示之頂端按鈕的螢幕擷取畫面，以及描述停止動作並詢問「您要繼續嗎？」的快顯訊息 具有 [是] （預設值）和 [無] 按鈕](media/stop-cache.png)

當您按一下 [是] 確認停止快取之後，快取會自動將其內容排清到儲存體目標。 此程式可能需要一些時間，但可確保資料的一致性。 最後，快取狀態會變更為 [**已停止**]。

若要重新開機已停止的快取，請按一下 [**開始**] 按鈕。 不需要確認。

![[開始] 反白顯示之頂端按鈕的螢幕擷取畫面](media/start-cache.png)

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[!INCLUDE [cli-reminder.md](includes/cli-reminder.md)]

使用[az hpc-cache stop](/cli/azure/ext/hpc-cache/hpc-cache#ext-hpc-cache-az-hpc-cache-stop)命令暫時暫停快取。 只有當快取狀態為**狀況良好**或**降級**時，此動作才有效。

快取會在停止之前，自動將其內容排清到儲存體目標。 此程式可能需要一些時間，但可確保資料的一致性。

當動作完成時，快取狀態會變更為 [**已停止**]。

使用[az hpc-cache start 重新開機](/cli/azure/ext/hpc-cache/hpc-cache#ext-hpc-cache-az-hpc-cache-start)已停止的快取。

當您發出 start 或 stop 命令時，命令列會顯示「正在執行」狀態訊息，直到作業完成為止。

```azurecli
$ az hpc-cache start --name doc-cache0629
 - Running ..
```

完成時，訊息會更新為「已完成」，並顯示傳回碼和其他資訊。

```azurecli
$ az hpc-cache start --name doc-cache0629
{- Finished ..
  "endTime": "2020-07-01T18:46:43.6862478+00:00",
  "name": "c48d320f-f5f5-40ab-8b25-0ac065984f62",
  "properties": {
    "output": "success"
  },
  "startTime": "2020-07-01T18:40:28.5468983+00:00",
  "status": "Succeeded"
}
```

---

## <a name="flush-cached-data"></a>清除快取的資料

[總覽] 頁面上的 [**清除**] 按鈕會指示快取立即將儲存在快取中的所有已變更資料寫入後端儲存體目標。 快取會定期將資料儲存到儲存體目標，因此，除非您想要確定後端儲存系統是最新的，否則不需要手動執行此動作。 例如，您可以在建立儲存體快照集之前使用**Flush** ，或檢查資料集大小。

> [!NOTE]
> 在清除程式期間，快取無法服務用戶端要求。 在作業完成之後，快取存取已暫停並繼續。

當您啟動快取排清作業時，快取會停止接受用戶端要求，而且 [總覽] 頁面上的快取狀態會變更為 [排清 **]。**

快取中的資料會儲存至適當的儲存體目標。 視需要排清的資料量而定，此程式可能需要幾分鐘或一小時的時間。

將所有資料儲存至儲存體目標之後，快取會自動開始再次取得用戶端要求。 快取狀態會恢復為**狀況良好**。

### <a name="portal"></a>[入口網站](#tab/azure-portal)

若要排清快取，請按一下 [**清除**] 按鈕，然後按一下 **[是]** 確認該動作。

![反白顯示醒目提示的頂端按鈕螢幕擷取畫面，以及描述清除動作並詢問「您要繼續嗎？」的快顯訊息 具有 [是] （預設值）和 [無] 按鈕](media/hpc-cache-flush.png)

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[!INCLUDE [cli-reminder.md](includes/cli-reminder.md)]

使用[az hpc-cache flush](/cli/azure/ext/hpc-cache/hpc-cache#ext-hpc-cache-az-hpc-cache-flush)來強制快取將所有已變更的資料寫入儲存體目標。

範例：

```azurecli
$ az hpc-cache flush --name doc-cache0629 --resource-group doc-rg
 - Running ..
```

當清除完成時，就會傳回成功訊息。

```azurecli
{- Finished ..
  "endTime": "2020-07-09T17:26:13.9371983+00:00",
  "name": "c22f8e12-fcf0-49e5-b897-6a6e579b6489",
  "properties": {
    "output": "success"
  },
  "startTime": "2020-07-09T17:25:21.4278297+00:00",
  "status": "Succeeded"
}
$
```

---

## <a name="upgrade-cache-software"></a>升級快取軟體

如果有新的軟體版本可用，[**升級**] 按鈕就會變成作用中狀態。 您也應該會在頁面頂端看到關於更新軟體的訊息。

![已啟用 [升級] 按鈕之按鈕頂端列的螢幕擷取畫面](media/hpc-cache-upgrade-button.png)

在軟體升級期間，用戶端存取不會中斷，但快取效能會變慢。 規劃在非尖峰使用時間或預定的維護期間升級軟體。

軟體更新可能需要數小時的時間。 以較高的輸送量設定的快取所需的升級時間比具有較低尖峰輸送量值的快取記憶體長。

當軟體升級可供使用時，您將會有一周的時間，或以手動方式套用。 [結束日期] 會列在 [升級] 訊息中。 如果您在這段時間內未升級，Azure 會自動將更新套用至您的快取。 自動升級的時間無法設定。 如果您擔心快取效能的影響，您應該在時間週期到期之前自行升級軟體。

如果您的快取在結束日期通過時停止，則快取會在下一次啟動時自動升級軟體。 （更新可能不會立即啟動，但會在第一個小時內啟動）。

### <a name="portal"></a>[入口網站](#tab/azure-portal)

按一下 [**升級**] 按鈕以開始軟體更新。 快取狀態會變更為 [**升級**]，直到作業完成為止。

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[!INCLUDE [cli-reminder.md](includes/cli-reminder.md)]

在 [Azure CLI 上，快取狀態報表的結尾會包含新的軟體資訊。 （使用[az hpc-cache show](/cli/azure/ext/hpc-cache/hpc-cache#ext-hpc-cache-az-hpc-cache-show)進行檢查）。在訊息中尋找 "upgradeStatus" 字串。

使用[az hpc-cache upgrade-固件](/cli/azure/ext/hpc-cache/hpc-cache#ext-hpc-cache-az-hpc-cache-upgrade-firmware)來套用更新（如果有的話）。

如果沒有可用的更新，此作業就不會有任何作用。

這個範例會顯示快取狀態（沒有可用的更新）和 [升級-固件] 命令的結果。

```azurecli
$ az hpc-cache show --name doc-cache0629
{
  "cacheSizeGb": 3072,
  "health": {
    "state": "Healthy",
    "statusDescription": "The cache is in Running state"
  },

<...>

  "tags": null,
  "type": "Microsoft.StorageCache/caches",
  "upgradeStatus": {
    "currentFirmwareVersion": "5.3.61",
    "firmwareUpdateDeadline": "0001-01-01T00:00:00+00:00",
    "firmwareUpdateStatus": "unavailable",
    "lastFirmwareUpdate": "2020-06-29T22:18:32.004822+00:00",
    "pendingFirmwareVersion": null
  }
}
$ az hpc-cache upgrade-firmware --name doc-cache0629
$
```

---

## <a name="delete-the-cache"></a>刪除快取

[**刪除**] 按鈕會損毀快取。 當您刪除快取時，其所有資源都會被終結，而不會再產生帳戶費用。

當您刪除快取時，用來做為儲存體目標的後端存放磁片區不會受到影響。 您可以稍後再將它們新增至未來的快取，或分別將它們解除委任。

> [!NOTE]
> 在刪除快取之前，Azure HPC 快取不會自動將已變更的資料從快取寫入後端儲存體系統。
>
> 若要確定快取中的所有資料都已寫入長期儲存體，請先[停止](#stop-the-cache)快取，再刪除它。 請確定它顯示狀態為 [**已停止**]，然後再刪除。

### <a name="portal"></a>[入口網站](#tab/azure-portal)

停止快取之後，請按一下 [**刪除**] 按鈕以永久移除快取。

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[!INCLUDE [cli-reminder.md](includes/cli-reminder.md)]

使用 Azure CLI 命令[az hpc-cache delete](/cli/azure/ext/hpc-cache/hpc-cache#ext-hpc-cache-az-hpc-cache-delete)以永久移除快取。

範例：
```azurecli
$ az hpc-cache delete --name doc-cache0629
 - Running ..

<...>

{- Finished ..
  "endTime": "2020-07-09T22:24:35.1605019+00:00",
  "name": "7d3cd0ba-11b3-4180-8298-d9cafc9f22c1",
  "startTime": "2020-07-09T22:13:32.0732892+00:00",
  "status": "Succeeded"
}
$
```

---

## <a name="cache-metrics-and-monitoring"></a>快取計量和監視

[總覽] 頁面會顯示一些基本快取統計資料的圖表-快取輸送量、每秒的作業數和延遲。

![三個折線圖的螢幕擷取畫面，其中顯示針對範例快取所述的統計資料](media/hpc-cache-overview-stats.png)

這些圖表是 Azure 內建監視和分析工具的一部分。 您可以從入口網站提要欄位中 [**監視**] 標題底下的頁面取得其他工具和警示。 若要深入瞭解，請[參閱 Azure 監視檔](../azure-monitor/insights/monitor-azure-resource.md#monitoring-in-the-azure-portal)的入口網站一節。

## <a name="next-steps"></a>後續步驟

* 深入瞭解[Azure 計量和統計資料工具](../azure-monitor/index.yml)
* 取得[AZURE HPC 快取的協助](hpc-cache-support-ticket.md)
