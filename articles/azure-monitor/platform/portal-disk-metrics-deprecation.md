---
title: Azure 門戶中的磁片指標棄用 |微軟文檔
description: 瞭解已棄用哪些磁片指標以及如何更新指標警報以使用新指標。
services: azure-monitor
ms.subservice: metrics
ms.topic: conceptual
author: albecker1
ms.author: albecker
ms.date: 03/12/2020
ms.openlocfilehash: f2b960c2198800e04da77ad6b5be78d7b4762354
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79299799"
---
# <a name="disk-metrics-deprecation-in-the-azure-portal"></a>Azure 門戶中的磁片指標棄用

已棄用的磁片相關指標將很快從 Azure 門戶中刪除。 每個棄用指標的新版本可供您使用。 本文介紹哪些指標是新的指標，以及如何更新指標警報以使用它們。

## <a name="list-of-new-metrics"></a>新指標清單

此表將每個棄用指標映射到相應的新指標。 

|已棄用的指標|新的（替換）指標|
|----|----|
|資料磁片 QD（已棄用）|資料磁片佇列深度（預覽）|
|資料磁片讀取位元組/秒（已棄用）|資料磁碟讀取位元組/秒 (預覽)|
|資料磁片讀取操作/秒（已棄用）|資料磁碟讀取作業/秒 (預覽)|
|資料磁片寫入位元組/秒（已棄用）|資料磁碟寫入位元組/秒 (預覽)|
|資料磁片寫入操作/秒（已棄用）|資料磁碟寫入作業/秒 (預覽)|
|作業系統 QD（已棄用）|作業系統佇列深度（預覽）|
|作業系統讀取位元組/秒（已棄用）|作業系統讀取位元組/秒（預覽）|
|作業系統讀取操作/秒（已棄用）|作業系統讀取操作/秒（預覽）|
|作業系統寫入位元組/秒（已棄用）|作業系統寫入位元組/秒（預覽）|
|作業系統寫入操作/秒（已棄用）|作業系統寫入操作/秒（預覽）|

<a id="update-metrics" />

## <a name="migrate-metrics-in-your-metric-alerts"></a>遷移指標警報中的指標

更新指標警報以使用新指標。

1. 在 Azure 門戶中，搜索**警報**。 然後，在 **"服務"** 部分中，選擇 **"警報**"。

   > [!div class="mx-imgBorder"]
   > ![圖像描述](./media/portal-disk-metrics-deprecation/alert-service-azure-portal.png)

2. 在 **"警報"** 頁中，選擇"**管理警報規則"** 按鈕。 

   > [!div class="mx-imgBorder"]
   > ![圖像描述](./media/portal-disk-metrics-deprecation/manage-alert-rules-button.png)

3. 在 **"資源組**下拉清單"中，選擇 **"虛擬機器**"核取方塊，在 **"信號類型**"下拉清單中，選擇 **"指標"** 核取方塊。 

   > [!div class="mx-imgBorder"]
   > ![圖像描述](./media/portal-disk-metrics-deprecation/filter-alerts.png)

4. 在指標清單中，標識與磁片相關的條件。 按一下規則的名稱。 

   該名稱在表的 **"名稱"** 列中顯示為超連結。

   > [!div class="mx-imgBorder"]
   > ![圖像描述](./media/portal-disk-metrics-deprecation/find-disk-conditions.png)

5. 在 **"規則管理**"頁**的條件**部分中，按一下警報的條件。 

   條件顯示為超連結。  

   > [!div class="mx-imgBorder"]
   > ![圖像描述](./media/portal-disk-metrics-deprecation/adjust-condition.png)

   將顯示 **"配置信號邏輯**"頁，並且條件的設置將顯示在該頁的 **"警報"邏輯**部分中。

6. 記錄這些設置，因為當您刪除已棄用的指標時，這些設置將消失。

   > [!div class="mx-imgBorder"]
   > ![圖像描述](./media/portal-disk-metrics-deprecation/condition-rules.png)

   > [!TIP] 
   > 請考慮在螢幕截圖或文字檔中捕獲這些設置。 

7. 按一下 **"返回"到信號選擇**連結。

   > [!div class="mx-imgBorder"]
   > ![圖像描述](./media/portal-disk-metrics-deprecation/back-to-signal-selection.png)

8. 在 **"配置信號邏輯**"頁中，選擇適當的替換指標（新指標）。 使用本文前面顯示的[表](#update-metrics)來標識新指標的名稱。

   > [!TIP] 
   > 開始在搜索欄中鍵入以縮小指標名稱清單的範圍。 

   > [!div class="mx-imgBorder"]
   > ![圖像描述](./media/portal-disk-metrics-deprecation/choose-new-metric.png)

9. 選擇"**完成"** 按鈕。 

   > [!div class="mx-imgBorder"]
   > ![圖像描述](./media/portal-disk-metrics-deprecation/set-new-metric.png)

10. 通過選擇 **"保存**"按鈕提交更改。 

    > [!div class="mx-imgBorder"]
    > ![圖像描述](./media/portal-disk-metrics-deprecation/save-new-metric.png)






