---
title: 預覽環境中的資料建模 - Azure 時間序列見解 |微軟文檔
description: 在 Azure 時間序列見解預覽中瞭解資料建模。
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 02/18/2020
ms.custom: seodec18
ms.openlocfilehash: 39ebbf99ad31cce20eabc20fbdc056c889235713
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77470746"
---
# <a name="data-modeling-in-azure-time-series-insights-preview"></a>Azure 時間序列深入解析預覽版中的資料模型

本文介紹如何在 Azure 時間序列見解預覽中使用時序模型。 它詳細說明數個常見資料案例。

> [!TIP]
> * 閱讀預覽[時間序列模型](time-series-insights-update-tsm.md)。
> * 詳細瞭解在[Azure 時間序列預覽資源管理器](./time-series-insights-update-explorer.md)中導航預覽 UI。

## <a name="instances"></a>執行個體

Azure 時間序列見解資源管理器支援瀏覽器中的實例**創建**、**讀取**、**更新**和**刪除**操作。 

首先，從時序見解資源管理器分析視圖中選擇 **"模型****"** 視圖。

### <a name="create-a-single-instance"></a>建立單一執行個體

1. 轉到時間序列模型選擇器面板，並從功能表中選擇 **"實例**"。 將顯示與所選時間序列見解環境關聯的所有實例。

    [![通過首次選擇實例創建單個實例。](media/v2-update-how-to-tsm/how-to-tsm-instances-panel.png)](media/v2-update-how-to-tsm/how-to-tsm-instances-panel.png#lightbox)

1. 選擇 **= 添加**。

    [![通過選擇 " 添加"按鈕添加實例。](media/v2-update-how-to-tsm/how-to-tsm-add-instance.png)](media/v2-update-how-to-tsm/how-to-tsm-add-instance.png#lightbox)

1. 輸入執行個體詳細資料，選取類型和階層關聯，然後選取 [建立]****。

### <a name="bulk-upload-one-or-more-instances"></a>大量上傳一或多個執行個體

> [!TIP]
> 您可以在 JSON 中將實例保存到桌面。然後，可以通過以下步驟上載下載的 JSON 檔。

1. 選取 [上傳 JSON]****。
1. 選取包含執行個體承載的檔案。

    [![通過 JSON 批量上載實例。](media/v2-update-how-to-tsm/how-to-tsm-bulk-upload-instances.png)](media/v2-update-how-to-tsm/how-to-tsm-bulk-upload-instances.png#lightbox)

1. 選取 [上傳]****。

### <a name="edit-a-single-instance"></a>編輯單一執行個體

1. 選擇實例，然後選擇**編輯**或**鉛筆圖示**。 
1. 進行必要的變更，然後選取 [儲存]****。

    [![編輯單個實例。](media/v2-update-how-to-tsm/how-to-tsm-edit-instance.png)](media/v2-update-how-to-tsm/how-to-tsm-edit-instance.png#lightbox)

### <a name="delete-an-instance"></a>刪除執行個體

1. 選擇實例，然後選擇**刪除**或**垃圾箱圖示**。

   [![通過選擇"刪除"刪除實例。](media/v2-update-how-to-tsm/how-to-tsm-delete-instance.png)](media/v2-update-how-to-tsm/how-to-tsm-delete-instance.png#lightbox)

1. 通過選擇 **"刪除"** 確認刪除。

> [!NOTE]
> 實例必須成功通過要刪除的欄位驗證檢查。

## <a name="hierarchies"></a>階層

Azure 時間序列見解資源管理器支援瀏覽器中的層次結構**創建**、**讀取**、**更新**和**刪除**操作。 

首先，從時序見解資源管理器分析視圖中選擇 **"模型****"** 視圖。

### <a name="create-a-single-hierarchy"></a>建立單一階層

1. 轉到時間序列模型選擇器面板，並從功能表中選擇**層次結構**。 將顯示與所選時間序列見解環境關聯的所有層次結構。

    [![通過窗格創建層次結構。](media/v2-update-how-to-tsm/how-to-tsm-hierarchy-panel.png)](media/v2-update-how-to-tsm/how-to-tsm-hierarchy-panel.png#lightbox)

1. 選擇 **= 添加**。

    [![層次結構 = 添加按鈕。](media/v2-update-how-to-tsm/how-to-tsm-add-new-hierarchy.png)](media/v2-update-how-to-tsm/how-to-tsm-add-new-hierarchy.png#lightbox)

1. 選擇 = 在右側窗格中**添加級別**。

    [![向層次結構添加級別。](media/v2-update-how-to-tsm/how-to-tsm-save-hierarchy-levels.png)](media/v2-update-how-to-tsm/how-to-tsm-save-hierarchy-levels.png#lightbox)

1. 輸入層次結構詳細資訊，然後選擇 **"保存**"。

    [![指定層次結構詳細資訊。](media/v2-update-how-to-tsm/how-to-tsm-add-hierarchy-level.png)](media/v2-update-how-to-tsm/how-to-tsm-add-hierarchy-level.png#lightbox)

### <a name="bulk-upload-one-or-more-hierarchies"></a>大量上傳一或多個階層

> [!TIP]
> 您可以在 JSON 中將層次結構保存到桌面。然後，可以通過以下步驟上載下載的 JSON 檔。

1. 選取 [上傳 JSON]****。
1. 選取包含階層承載的檔案。
1. 選取 [上傳]****。

    [![批量上載層次結構的選擇。](media/v2-update-how-to-tsm/how-to-tsm-bulk-upload-hierarchies.png)](media/v2-update-how-to-tsm/how-to-tsm-bulk-upload-hierarchies.png#lightbox)

### <a name="edit-a-single-hierarchy"></a>編輯單一階層

1. 選擇層次結構，然後選擇**編輯**或**鉛筆圖示**。
1. 進行必要的變更，然後選取 [儲存]****。

    [![用於編輯單個層次結構的選擇。](media/v2-update-how-to-tsm/how-to-tsm-edit-hierarchy.png)](media/v2-update-how-to-tsm/how-to-tsm-edit-hierarchy.png#lightbox)

### <a name="delete-a-hierarchy"></a>刪除階層

1. 選擇層次結構，然後選擇**刪除**或**垃圾箱圖示**。 

    [![通過選擇"刪除"按鈕刪除層次結構。](media/v2-update-how-to-tsm/how-to-tsm-delete-hierarchy.png)](media/v2-update-how-to-tsm/how-to-tsm-delete-hierarchy.png#lightbox)

1. 通過選擇 **"刪除"** 確認刪除。

## <a name="types"></a>型別

Azure 時間序列見解資源管理器支援瀏覽器中的"**類型創建**"、**讀取**、**更新**和**刪除**操作。 

首先，從時序見解資源管理器分析視圖中選擇 **"模型****"** 視圖。

### <a name="create-a-single-type"></a>建立單一類型

1. 轉到時間序列模型選擇器面板，並從功能表中選擇 **"類型**"。 將顯示與所選時間序列見解環境關聯的所有類型。

    [![時間序列模型類型窗格。](media/v2-update-how-to-tsm/how-to-tsm-type-panel.png)](media/v2-update-how-to-tsm/how-to-tsm-type-panel.png#lightbox)

1. 選擇 **" 添加"** 以顯示 **"添加新類型**彈出模式"。
1. 輸入類型的屬性和變數。 輸入後，選擇 **"保存**"。 

    [![要添加類型的配置設置。](media/v2-update-how-to-tsm/how-to-tsm-add-new-type.png)](media/v2-update-how-to-tsm/how-to-tsm-add-new-type.png#lightbox)

### <a name="bulk-upload-one-or-more-types"></a>大量上傳一或多個類型

> [!TIP]
> 您可以在 JSON 中將類型保存到桌面。然後，可以通過以下步驟上載下載的 JSON 檔。

1. 選取 [上傳 JSON]****。
1. 選取包含類型承載的檔案。
1. 選取 [上傳]****。

    [![批量類型上載選項。](media/v2-update-how-to-tsm/how-to-tsm-bulk-upload-types-json.png)](media/v2-update-how-to-tsm/how-to-tsm-bulk-upload-types-json.png#lightbox)

### <a name="edit-a-single-type"></a>編輯單一類型

1. 選擇類型，然後選擇**編輯**或**鉛筆圖示**。
1. 進行必要的變更，然後選取 [儲存]****。

    [![在窗格中編輯類型。](media/v2-update-how-to-tsm/how-to-tsm-edit-type.png)](media/v2-update-how-to-tsm/how-to-tsm-edit-type.png#lightbox)

### <a name="delete-a-type"></a>刪除類型

1. 選擇類型，然後選擇**刪除**或**垃圾箱圖示**。 .

   [![通過選擇"刪除"刪除類型。](media/v2-update-how-to-tsm/how-to-tsm-delete-type.png)](media/v2-update-how-to-tsm/how-to-tsm-delete-type.png#lightbox)

1. 通過選擇 **"刪除"** 確認刪除。

## <a name="next-steps"></a>後續步驟

- 有關時間序列模型的詳細資訊，請閱讀[資料建模](./time-series-insights-update-tsm.md)。

- 若要深入了解預覽版，請參閱[在 Azure 時間序列深入解析預覽版總管中將資料視覺化](./time-series-insights-update-explorer.md)。

- 要瞭解支援的 JSON 形狀，請閱讀[支援的 JSON 形狀](./time-series-insights-send-events.md#supported-json-shapes)。
