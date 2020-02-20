---
title: 預覽環境中的資料模型-Azure 時間序列深入解析 |Microsoft Docs
description: 深入瞭解 Azure 時間序列深入解析 Preview 中的資料模型。
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
ms.sourcegitcommit: 64def2a06d4004343ec3396e7c600af6af5b12bb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/19/2020
ms.locfileid: "77470746"
---
# <a name="data-modeling-in-azure-time-series-insights-preview"></a>Azure 時間序列深入解析預覽版中的資料模型

本文說明如何使用 Azure 時間序列深入解析 Preview 中的時間序列模型。 它詳細說明數個常見資料案例。

> [!TIP]
> * 閱讀預覽[時間序列模型](time-series-insights-update-tsm.md)的相關資訊。
> * 深入瞭解如何在[Azure 時間序列深入解析 preview explorer](./time-series-insights-update-explorer.md)中流覽預覽 UI。

## <a name="instances"></a>執行個體

Azure 時間序列深入解析 explorer 支援瀏覽器中的實例**建立**、**讀取**、**更新**和**刪除**作業。 

若要開始，請從時間序列深入解析 explorer 的 [**分析**] 視圖中選取**模型**view。

### <a name="create-a-single-instance"></a>建立單一執行個體

1. 移至時間序列模型選取器面板，然後從功能表中選取 [**實例**]。 將會顯示與您所選時間序列深入解析環境相關聯的所有實例。

    [![先選取 [實例] 來建立單一實例。](media/v2-update-how-to-tsm/how-to-tsm-instances-panel.png)](media/v2-update-how-to-tsm/how-to-tsm-instances-panel.png#lightbox)

1. 選取 [+ 新增]。

    [選取 [+ 新增] 按鈕，![加入實例。](media/v2-update-how-to-tsm/how-to-tsm-add-instance.png)](media/v2-update-how-to-tsm/how-to-tsm-add-instance.png#lightbox)

1. 輸入執行個體詳細資料，選取類型和階層關聯，然後選取 [建立]。

### <a name="bulk-upload-one-or-more-instances"></a>大量上傳一或多個執行個體

> [!TIP]
> 您可以將您的實例以 JSON 格式儲存到您的桌面。接著，您可以透過下列步驟來上傳下載的 JSON 檔案。

1. 選取 [上傳 JSON]。
1. 選取包含執行個體承載的檔案。

    [![透過 JSON 大量上傳實例。](media/v2-update-how-to-tsm/how-to-tsm-bulk-upload-instances.png)](media/v2-update-how-to-tsm/how-to-tsm-bulk-upload-instances.png#lightbox)

1. 選取 [上傳]。

### <a name="edit-a-single-instance"></a>編輯單一執行個體

1. 選取實例，然後選取 [**編輯**] 或 [**鉛筆] 圖示**。 
1. 進行必要的變更，然後選取 [儲存]。

    [![編輯單一實例。](media/v2-update-how-to-tsm/how-to-tsm-edit-instance.png)](media/v2-update-how-to-tsm/how-to-tsm-edit-instance.png#lightbox)

### <a name="delete-an-instance"></a>刪除執行個體

1. 選取實例，然後選取 [**刪除**] 或 [**垃圾的 bin] 圖示**。

   [![選取 [刪除] 來刪除實例。](media/v2-update-how-to-tsm/how-to-tsm-delete-instance.png)](media/v2-update-how-to-tsm/how-to-tsm-delete-instance.png#lightbox)

1. 選取 [**刪除**] 以確認刪除。

> [!NOTE]
> 實例必須成功傳遞要刪除的欄位驗證檢查。

## <a name="hierarchies"></a>階層

Azure 時間序列深入解析 explorer 支援在瀏覽器中**建立**、**讀取**、**更新**和**刪除**作業的階層。 

若要開始，請從時間序列深入解析 explorer 的 [**分析**] 視圖中選取**模型**view。

### <a name="create-a-single-hierarchy"></a>建立單一階層

1. 移至時間序列模型選取器面板，**然後從功能表**中選取 [階層]。 將會顯示與您所選時間序列深入解析環境相關聯的所有階層。

    [![透過窗格建立階層。](media/v2-update-how-to-tsm/how-to-tsm-hierarchy-panel.png)](media/v2-update-how-to-tsm/how-to-tsm-hierarchy-panel.png#lightbox)

1. 選取 [+ 新增]。

    [![階層 + 新增 按鈕。](media/v2-update-how-to-tsm/how-to-tsm-add-new-hierarchy.png)](media/v2-update-how-to-tsm/how-to-tsm-add-new-hierarchy.png#lightbox)

1. 在右窗格中選取 [ **+ 新增層級**]。

    [![在階層中加入層級。](media/v2-update-how-to-tsm/how-to-tsm-save-hierarchy-levels.png)](media/v2-update-how-to-tsm/how-to-tsm-save-hierarchy-levels.png#lightbox)

1. 輸入階層詳細資料，然後選取 [**儲存**]。

    [![指定階層詳細資料。](media/v2-update-how-to-tsm/how-to-tsm-add-hierarchy-level.png)](media/v2-update-how-to-tsm/how-to-tsm-add-hierarchy-level.png#lightbox)

### <a name="bulk-upload-one-or-more-hierarchies"></a>大量上傳一或多個階層

> [!TIP]
> 您可以將階層以 JSON 格式儲存到您的桌面。接著，您可以透過下列步驟來上傳下載的 JSON 檔案。

1. 選取 [上傳 JSON]。
1. 選取包含階層承載的檔案。
1. 選取 [上傳]。

    [大量上傳階層的 ![選項。](media/v2-update-how-to-tsm/how-to-tsm-bulk-upload-hierarchies.png)](media/v2-update-how-to-tsm/how-to-tsm-bulk-upload-hierarchies.png#lightbox)

### <a name="edit-a-single-hierarchy"></a>編輯單一階層

1. 選取階層，然後選取 [**編輯**] 或 [**鉛筆] 圖示**。
1. 進行必要的變更，然後選取 [儲存]。

    [![選取以編輯單一階層。](media/v2-update-how-to-tsm/how-to-tsm-edit-hierarchy.png)](media/v2-update-how-to-tsm/how-to-tsm-edit-hierarchy.png#lightbox)

### <a name="delete-a-hierarchy"></a>刪除階層

1. 選取階層，然後選取 [**刪除**] 或 [**廢 bin] 圖示**。 

    [![選取 [刪除] 按鈕來刪除階層。](media/v2-update-how-to-tsm/how-to-tsm-delete-hierarchy.png)](media/v2-update-how-to-tsm/how-to-tsm-delete-hierarchy.png#lightbox)

1. 選取 [**刪除**] 以確認刪除。

## <a name="types"></a>類型

Azure 時間序列深入解析 explorer 支援瀏覽器中的類型**建立**、**讀取**、**更新**和**刪除**作業。 

若要開始，請從時間序列深入解析 explorer 的 [**分析**] 視圖中選取**模型**view。

### <a name="create-a-single-type"></a>建立單一類型

1. 移至時間序列模型選取器面板，然後從功能表中選取 [**類型**]。 將會顯示與您所選時間序列深入解析環境相關聯的所有類型。

    [![時間序列模型類型 窗格。](media/v2-update-how-to-tsm/how-to-tsm-type-panel.png)](media/v2-update-how-to-tsm/how-to-tsm-type-panel.png#lightbox)

1. 選取 [ **+ 新增**] 以顯示 [**加入新的類型**] 快顯視窗強制回應。
1. 輸入類型的屬性和變數。 輸入後，選取 [**儲存**]。 

    [![的設定來新增類型。](media/v2-update-how-to-tsm/how-to-tsm-add-new-type.png)](media/v2-update-how-to-tsm/how-to-tsm-add-new-type.png#lightbox)

### <a name="bulk-upload-one-or-more-types"></a>大量上傳一或多個類型

> [!TIP]
> 您可以將類型以 JSON 格式儲存到您的桌面。接著，您可以透過下列步驟來上傳下載的 JSON 檔案。

1. 選取 [上傳 JSON]。
1. 選取包含類型承載的檔案。
1. 選取 [上傳]。

    [![大量類型上傳選項。](media/v2-update-how-to-tsm/how-to-tsm-bulk-upload-types-json.png)](media/v2-update-how-to-tsm/how-to-tsm-bulk-upload-types-json.png#lightbox)

### <a name="edit-a-single-type"></a>編輯單一類型

1. 選取類型，然後選取 [**編輯**] 或 [**鉛筆] 圖示**。
1. 進行必要的變更，然後選取 [儲存]。

    [![編輯窗格中的類型。](media/v2-update-how-to-tsm/how-to-tsm-edit-type.png)](media/v2-update-how-to-tsm/how-to-tsm-edit-type.png#lightbox)

### <a name="delete-a-type"></a>刪除類型

1. 選取類型，然後選取 [**刪除**] 或 [**廢 bin] 圖示**。 。

   [![選取 [刪除] 來刪除類型。](media/v2-update-how-to-tsm/how-to-tsm-delete-type.png)](media/v2-update-how-to-tsm/how-to-tsm-delete-type.png#lightbox)

1. 選取 [**刪除**] 以確認刪除。

## <a name="next-steps"></a>後續步驟

- 如需時間序列模型的詳細資訊，請參閱[資料模型](./time-series-insights-update-tsm.md)化。

- 若要深入了解預覽版，請參閱[在 Azure 時間序列深入解析預覽版總管中將資料視覺化](./time-series-insights-update-explorer.md)。

- 若要瞭解支援的 JSON 圖形，請閱讀[支援的 json 圖形](./time-series-insights-send-events.md#supported-json-shapes)。
