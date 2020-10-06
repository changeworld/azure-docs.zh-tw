---
title: Azure 入口網站中的磁片計量淘汰 |Microsoft Docs
description: 瞭解哪些磁片計量已被取代，以及如何更新您的計量警示以使用新的計量。
services: azure-monitor
ms.subservice: metrics
ms.topic: conceptual
author: albecker1
ms.author: albecker
ms.date: 03/12/2020
ms.openlocfilehash: 623d9385b9ae6b13c8964f655fb973fe67a0918a
ms.sourcegitcommit: a07a01afc9bffa0582519b57aa4967d27adcf91a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/05/2020
ms.locfileid: "91743704"
---
# <a name="disk-metrics-deprecation-in-the-azure-portal"></a>Azure 入口網站中的磁片計量淘汰

即將淘汰的磁片相關計量即將從 Azure 入口網站中移除。 您可以使用每個已被取代度量的新版本。 本文說明有哪些計量是新的，以及如何更新您的計量警示以使用它們。

## <a name="list-of-new-metrics"></a>新計量的清單

此資料表會將每個已被取代的計量對應至其對應的新計量。 

|已淘汰的度量|新的 (取代) 度量|
|----|----|
|資料磁碟 QD (已淘汰)|資料磁碟佇列深度 (預覽)|
|資料磁碟讀取位元組數/秒 (已淘汰)|資料磁碟讀取位元組/秒 (預覽)|
|資料磁碟讀取作業數/秒 (已淘汰)|資料磁碟讀取作業/秒 (預覽)|
|資料磁碟寫入位元組數/秒 (已淘汰)|資料磁碟寫入位元組/秒 (預覽)|
|資料磁碟寫入作業數/秒 (已淘汰)|資料磁碟寫入作業/秒 (預覽)|
|OS QD (已淘汰) |OS 佇列深度 (預覽) |
|OS Read Bytes/Sec (已淘汰) |OS Read Bytes/Sec (Preview) |
|OS 讀取作業數/秒 (已淘汰) |OS 讀取作業數/秒 (預覽) |
|OS Write Bytes/Sec (已淘汰) |OS Write Bytes/Sec (Preview) |
|OS Write Operations/Sec (已淘汰) |OS Write Operations/Sec (Preview) |

<a id="update-metrics" />

## <a name="migrate-metrics-in-your-metric-alerts"></a>遷移計量警示中的計量

更新您的計量警示，以使用新的計量。

1. 在 Azure 入口網站中，搜尋 **警示**。 然後，在 [ **服務** ] 區段中，選擇 [ **警示**]。

   > [!div class="mx-imgBorder"]
   > ![警示服務](./media/portal-disk-metrics-deprecation/alert-service-azure-portal.png)

2. 在 [ **警示** ] 頁面中，選擇 [ **管理警示規則** ] 按鈕。 

   > [!div class="mx-imgBorder"]
   > ![管理警示規則](./media/portal-disk-metrics-deprecation/manage-alert-rules-button.png)

3. 在 [ **資源群組** ] 下拉式清單中，選取 [ **虛擬機器** ] 核取方塊，然後在 [ **信號類型** ] 下拉式清單中，選取 [ **計量** ] 核取方塊。 

   > [!div class="mx-imgBorder"]
   > ![篩選警示](./media/portal-disk-metrics-deprecation/filter-alerts.png)

4. 在計量清單中，找出與磁片相關的條件。 按一下規則的名稱。 

   名稱會顯示為數據表之 [ **名稱** ] 資料行中的超連結。

   > [!div class="mx-imgBorder"]
   > ![尋找磁片狀況](./media/portal-disk-metrics-deprecation/find-disk-conditions.png)

5. 在 [**規則管理**] 頁面的 [**條件**] 區段中，按一下警示的條件。 

   條件會顯示為超連結。  

   > [!div class="mx-imgBorder"]
   > ![調整條件](./media/portal-disk-metrics-deprecation/adjust-condition.png)

   [ **設定信號邏輯** ] 頁面隨即出現，且條件的設定會出現在該頁面的 [ **警示邏輯** ] 區段中。

6. 請記錄這些設定，因為當您移除已淘汰的計量時，這些設定將會消失。

   > [!div class="mx-imgBorder"]
   > ![條件規則](./media/portal-disk-metrics-deprecation/condition-rules.png)

   > [!TIP] 
   > 請考慮在螢幕擷取畫面或文字檔中捕捉這些設定。 

7. 按一下 [ **上一頁] 以表示選取專案** 連結。

   > [!div class="mx-imgBorder"]
   > ![返回信號選取範圍](./media/portal-disk-metrics-deprecation/back-to-signal-selection.png)

8. 在 [ **設定信號邏輯** ] 頁面中，選擇適當的取代度量 (新的度量) 。 使用本文稍早所示的 [表格](#update-metrics) 來識別新計量的名稱。

   > [!TIP] 
   > 開始在搜尋列中輸入，以縮小計量名稱的清單。 

   > [!div class="mx-imgBorder"]
   > ![選擇新度量](./media/portal-disk-metrics-deprecation/choose-new-metric.png)

9. 選擇 [ **完成** ] 按鈕。 

   > [!div class="mx-imgBorder"]
   > ![設定新的度量](./media/portal-disk-metrics-deprecation/set-new-metric.png)

10. 選擇 [ **儲存** ] 按鈕以認可您的變更。 

    > [!div class="mx-imgBorder"]
    > ![儲存新的度量](./media/portal-disk-metrics-deprecation/save-new-metric.png)






