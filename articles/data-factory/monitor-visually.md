---
title: 以視覺化方式監視 Azure Data Factory
description: 瞭解如何以視覺化方式監視 Azure data factory
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 11/19/2018
ms.openlocfilehash: 40b1b8d040c4b3ea76372920f88551fba35c5f26
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81419437"
---
# <a name="visually-monitor-azure-data-factory"></a>以視覺化方式監視 Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

在 Azure Data Factory 中建立併發布管線之後，您可以將它與觸發程式建立關聯，或手動啟動臨機操作執行。 您可以在 Azure Data Factory 使用者體驗中，以原生方式監視您所有的管線執行。 若要開啟監視體驗，請在[Azure 入口網站](https://portal.azure.com/)的 [data factory] 分頁中選取 [**監視] & [管理**] 磚。 如果您已經在 ADF UX 中，請按一下左側邊欄上的 [**監視**] 圖示。

所有的資料處理站執行都會顯示在瀏覽器的當地時區中。 如果您變更時區，所有日期/時間欄位都會貼齊您選取的區域。

## <a name="monitor-pipeline-runs"></a>監視管線回合

預設監視視圖是所選時段內的管線執行清單。 系統會顯示下列資料行：

| **資料行名稱** | **說明** |
| --- | --- |
| 管線名稱 | 管線的名稱 |
| 動作 | 可讓您查看活動詳細資料、取消或重新執行管線的圖示 |
| 回合開始 | 管線執行的開始日期和時間（MM/DD/YYYY、HH： MM： SS AM/PM） |
| Duration | 回合持續時間 (HH:MM:SS) |
| 觸發方式 | 啟動管線之觸發程式的名稱 |
| 狀態 | **失敗**、**成功**、**進行中**、**已取消或已****排入佇列** |
| 標註 | 與管線相關聯的可篩選標記  |
| 參數 | 管線執行的參數（名稱/值配對） |
| 錯誤 | 如果管線失敗，則執行錯誤 |
| 回合識別碼 | 管線執行的識別碼 |

![監視管線執行的清單視圖](media/monitor-visually/pipeline-runs.png)

您需要手動選取 [重新整理]**按鈕，以**重新整理管線和活動執行的清單。 目前不支援 Autorefresh。

![重新整理按鈕](media/monitor-visually/refresh.png)

## <a name="monitor-activity-runs"></a>監視活動回合

若要查看每個管線執行的活動執行，請選取 [**動作**] 資料行底下的 [ **view activity 執行**] 圖示。 清單視圖會顯示對應至每個管線執行的活動執行。

| **資料行名稱** | **說明** |
| --- | --- |
| 活動名稱 | 管線內的活動名稱 |
| 活動類型 | 活動的類型，例如**Copy**、 **ExecuteDataFlow**或**AzureMLExecutePipeline** |
| 動作 | 圖示，可讓您查看 JSON 輸入資訊、JSON 輸出資訊，或詳細的活動特定監視體驗 | 
| 回合開始 | 活動執行的開始日期和時間（MM/DD/YYYY，HH： MM： SS AM/PM） |
| Duration | 回合持續時間 (HH:MM:SS) |
| 狀態 | **失敗**、**成功**、**進行中**或**已取消** |
| Integration Runtime | 活動執行的 Integration Runtime |
| 使用者屬性 | 活動的使用者定義屬性 |
| 錯誤 | 如果活動失敗，則執行錯誤 |
| 回合識別碼 | 活動執行的識別碼 |

![監視活動執行的清單視圖](media/monitor-visually/activity-runs.png)

### <a name="promote-user-properties-to-monitor"></a>將使用者屬性升階以便監視

將任何管線活動屬性升階為使用者屬性，使其成為您所監視的實體。 例如，您可以將管線中複製活動的 [**來源**] 和 [**目的地**] 屬性升階為使用者屬性。 選取 [**自動產生**] 以產生複製活動的**來源**和**目的地**使用者屬性。

![建立使用者屬性](media/monitor-visually/monitor-user-properties-image1.png)

> [!NOTE]
> 您最多隻能將五個管線活動屬性升階為使用者屬性。

建立使用者內容之後，您可以在 [監視] 清單視圖中監視這些屬性。 如果複製活動的來源是資料表名稱，您可以在活動執行的清單視圖中，監視來源資料表名稱的資料行。

![活動執行清單沒有使用者屬性](media/monitor-visually/monitor-user-properties-image2.png)

![將使用者屬性的資料行新增至活動執行清單](media/monitor-visually/monitor-user-properties-image3.png)

![活動執行清單具有使用者屬性的資料行](media/monitor-visually/monitor-user-properties-image4.png)

## <a name="configure-the-list-view"></a>設定清單檢視

### <a name="order-and-filter"></a>訂單和篩選

根據執行開始時間，切換管線執行是遞減還是遞增的。 使用下列資料行篩選管線執行：

| **資料行名稱** | **說明** |
| --- | --- |
| 管線名稱 | 依管線的名稱篩選。 |
| 回合開始 |  決定顯示管線執行的時間範圍。 選項包括**過去24小時**、**過去一周**和**過去30天**的快速篩選，或是選取自訂的日期和時間。 |
| 回合狀態 | 依狀態篩選執行：**成功**、**失敗**、已**排入佇列**、**已取消**或**進行中**。 |
| 標註 | 依套用至每個管線的標記篩選 |
| 執行 | 篩選是否要查看不見了管線 |

![篩選選項](media/monitor-visually/filter.png)

### <a name="add-or-remove-columns"></a>新增或移除資料行
以滑鼠右鍵按一下清單視圖標題，然後選擇您想要在清單視圖中顯示的資料行。

![資料行的選項](media/monitor-visually/columns.png)

### <a name="adjust-column-widths"></a>調整資料行寬度
將滑鼠停留在資料行標頭上方，以增加和減少清單視圖中的資料行寬度。

## <a name="rerun-activities-inside-a-pipeline"></a>在管線中重新執行活動

您可以重新執行管線內的活動。 選取 [ **View activity 執行**]，然後選取您的管線中要重新執行管線的活動。

![檢視活動執行](media/monitor-visually/rerun-activities-image1.png)

![選取活動執行](media/monitor-visually/rerun-activities-image2.png)

### <a name="rerun-from-failed-activity"></a>從失敗的活動重新執行

如果活動失敗、超時或已取消，您可以選取 [**從失敗的活動重新**執行]，從該失敗的活動重新執行管線。

![重新執行失敗的活動](media/monitor-visually/rerun-failed-activity.png)

### <a name="view-rerun-history"></a>檢視重新執行歷程記錄

您可以在清單檢視中檢視所有管線執行的重新執行歷程記錄。

![檢視歷程記錄](media/monitor-visually/rerun-history-image1.png)

您也可以檢視特定管線執行的重新執行歷程記錄。

![檢視管線執行的歷程記錄](media/monitor-visually/rerun-history-image2.png)

## <a name="gantt-views"></a>甘特圖視圖

使用 [甘特圖] 視圖以快速視覺化您的管線和活動執行。

![甘特圖的範例](media/monitor-visually/gantt1.png)

您可以查看每個管線的 [甘特圖] 或 [依您在管線上建立的批註/標記群組]。

![甘特圖注釋](media/monitor-visually/gantt2.png)

橫條的長度會通知管線的持續時間。 您也可以選取橫條以查看更多詳細資料。

![甘特圖持續時間](media/monitor-visually/gantt3.png)

## <a name="guided-tours"></a>導覽
選取左下方的**資訊**圖示。 然後選取 [**引導式導覽**]，以取得如何監視管線和活動執行的逐步指示。

![導覽](media/monitor-visually/guided-tours.png)

## <a name="alerts"></a>警示

您可以針對 Data Factory 中支援的計量提出警示。 選取 [Data Factory 監視] 頁面上的 [**監視** > **警示 & 計量**] 以開始使用。

![Data factory [監視] 頁面](media/monitor-visually/alerts01.png)

如需此功能的 7 分鐘簡介與示範，請觀看下列影片：

> [!VIDEO https://channel9.msdn.com/shows/azure-friday/Monitor-your-Azure-Data-Factory-pipelines-proactively-with-alerts/player]

### <a name="create-alerts"></a>建立警示

1.  選取 [**新增警示規則**] 以建立新的警示。

    ![[新增警示規則] 按鈕](media/monitor-visually/alerts02.png)

1.  指定規則名稱，然後選取警示嚴重性。

    ![規則名稱和嚴重性的方塊](media/monitor-visually/alerts03.png)

1.  選取警示準則。

    ![目標準則的 Box](media/monitor-visually/alerts04.png)

    ![準則清單](media/monitor-visually/alerts05.png)

1.  設定警示邏輯。 您可以針對所有管線和對應活動，建立所選計量的警示。 您也可以選取特定的 [活動類型]、[活動名稱]、[管線名稱] 或 [失敗類型]。

    ![設定警示邏輯的選項](media/monitor-visually/alerts06.png)

1.  設定警示的電子郵件、SMS、推播和語音通知。 為警示通知建立動作群組，或選擇現有的群組。

    ![設定通知的選項](media/monitor-visually/alerts07.png)

    ![新增通知的選項](media/monitor-visually/alerts08.png)

1.  建立警示規則。

    ![建立警示規則的選項](media/monitor-visually/alerts09.png)

## <a name="next-steps"></a>後續步驟

若要瞭解如何監視和管理管線，請參閱以程式設計[方式監視和管理管線](https://docs.microsoft.com/azure/data-factory/monitor-programmatically)一文。
