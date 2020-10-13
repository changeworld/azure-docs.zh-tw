---
title: 以視覺化方式監視 Azure Data Factory
description: 了解如何以視覺化方式監視 Azure 資料處理站
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 06/30/2020
ms.openlocfilehash: fcbf48081484941fd93f209bee21eb14c2c38310
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91570137"
---
# <a name="visually-monitor-azure-data-factory"></a>以視覺化方式監視 Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

一旦在 Azure Data Factory 中建立並發佈管道後，您就可以將其與觸發程序建立關聯，或手動啟動特定回合。 您可以在 Azure Data Factory 使用者體驗中以原生方式監視所有管線回合。 若要開啟監視體驗，請在 [Azure 入口網站](https://portal.azure.com/)的資料處理站刀鋒視窗中選取 [監視及管理] 圖格。 如果您已經在 ADF UX 中，請按一下左側提要欄位上的 [監視] 圖示。

所有的資料處理站回合都會顯示在瀏覽器的本地時區中。 如果您變更時區，所有日期/時間欄位都會調整至選取的時區。

## <a name="monitor-pipeline-runs"></a>監視管線回合

預設監視檢視是所選取時段中的管線回合清單。 系統會顯示下列資料行：

| **資料行名稱** | **說明** |
| --- | --- |
| 管線名稱 | 管線的名稱 |
| 動作 | 可讓您檢視活動詳細資料、取消或重新執行管線的圖示 |
| 回合開始 | 管線回合的開始日期和時間 (MM/DD/YYYY，HH:MM:SS AM/PM) |
| Duration | 回合持續時間 (HH:MM:SS) |
| 觸發方式 | 啟動管線的觸發程序名稱 |
| 狀態 | **失敗**、**成功**、**進行中**、**已取消**或**已排入佇列** |
| 註解 | 與管線相關聯的可篩選標籤  |
| 參數 | 管線回合的參數 (名稱/值組) |
| 錯誤 | 如果管線失敗，則回合錯誤 |
| 回合識別碼 | 管線執行的識別碼 |

![用於監視管線回合的清單檢視](media/monitor-visually/pipeline-runs.png)

您必須手動選取 [重新整理] 按鈕，才能重新整理管線和活動回合的清單。 目前不支援自動重新整理。

![[重新整理] 按鈕](media/monitor-visually/refresh.png)

## <a name="monitor-activity-runs"></a>監視活動回合

若要檢視每個管線回合的活動回合，請按一下位於 [活動] 資料行底下的 [檢視活動回合] 圖示。 清單檢視會顯示對應至每個管線回合的活動回合。

| **資料行名稱** | **說明** |
| --- | --- |
| 活動名稱 | 管線內的活動名稱 |
| 活動類型 | 活動的類型，例如 **Copy**、**ExecuteDataFlow** 或 **AzureMLExecutePipeline** |
| 動作 | 圖示，可讓您查看 JSON 輸入資訊、JSON 輸出資訊，或詳細的活動特定監視體驗 | 
| 回合開始 | 活動回合的開始日期和時間 (MM/DD/YYYY，HH:MM:SS AM/PM) |
| Duration | 回合持續時間 (HH:MM:SS) |
| 狀態 | **失敗**、**成功**、**進行中**或**已取消** |
| 整合執行階段 | 活動是在哪個 Integration Runtime 上執行 |
| 使用者屬性 | 活動的使用者定義屬性 |
| 錯誤 | 如果活動失敗，則回合錯誤 |
| 回合識別碼 | 活動執行的識別碼 |

![用於監視活動回合的清單檢視](media/monitor-visually/activity-runs.png)

### <a name="promote-user-properties-to-monitor"></a>將使用者屬性升階以便監視

將任何管線活動屬性升階為使用者屬性，讓其變成您監視的實體。 例如，您可以將管線中複製活動的 **Source** 和 **Destination** 屬性升階為使用者屬性。 選取 [自動產生]，為複製活動產生 **Source** 和 **Destination** 使用者屬性。

![建立使用者屬性](media/monitor-visually/monitor-user-properties-image1.png)

> [!NOTE]
> 您最多只能將五個管線活動屬性升階為使用者屬性。

建立使用者屬性之後，您就可以在監視清單檢視中進行監視。 如果複製活動的來源是資料表名稱，您可以將來源資料表名稱當作活動回合清單檢視中的資料行進行監視。

![活動執行清單沒有使用者屬性](media/monitor-visually/monitor-user-properties-image2.png)

![將使用者屬性的資料行新增到活動回合清單](media/monitor-visually/monitor-user-properties-image3.png)

![活動執行清單具有使用者屬性的資料行](media/monitor-visually/monitor-user-properties-image4.png)

## <a name="configure-the-list-view"></a>設定清單檢視

### <a name="order-and-filter"></a>排序和篩選

根據回合開始時間，切換管線回合是遞減還是遞增。 使用下列資料行篩選管線回合：

| **資料行名稱** | **說明** |
| --- | --- |
| 管線名稱 | 依管線的名稱篩選。 |
| 回合開始 |  判定所顯示管線回合的時間範圍。 選項包括針對 [過去 24 小時]、[上週] 和 [過去 30 天] 的快速篩選，或是選取自訂日期和時間。 |
| 回合狀態 | 依狀態篩選回合：**成功**、**失敗**、**已排入佇列**、**已取消**或**進行中**。 |
| 註解 | 依套用至每個管線的標籤篩選 |
| 執行 | 篩選您是否要查看重新執行的管線 |

![用於篩選的選項](media/monitor-visually/filter.png)

### <a name="add-or-remove-columns"></a>新增或移除資料行
以滑鼠右鍵按一下清單檢視標頭，然後選擇要顯示於清單檢視中的資料行。

![資料行選項](media/monitor-visually/columns.png)

### <a name="adjust-column-widths"></a>調整資料行寬度
若要增加或減少清單檢視中的資料行寬度，請將滑鼠游標暫留於資料行標頭上方。

## <a name="rerun-activities-inside-a-pipeline"></a>在管線中重新執行活動

您現在可以在管線內重新執行活動。 選取 [檢視活動回合]，然後選取管線中您想要重新執行管線的活動。

![檢視活動執行](media/monitor-visually/rerun-activities-image1.png)

![選取活動執行](media/monitor-visually/rerun-activities-image2.png)

### <a name="rerun-from-failed-activity"></a>從失敗的活動重新執行

如果活動失敗、逾時或已取消，您可以選取 [從失敗的活動重新執行]，從失敗的活動重新執行管線。

![重新執行失敗的活動](media/monitor-visually/rerun-failed-activity.png)

### <a name="view-rerun-history"></a>檢視重新執行歷程記錄

您可以在清單檢視中檢視所有管線執行的重新執行歷程記錄。

![檢視歷程記錄](media/monitor-visually/rerun-history-image1.png)

您也可以檢視特定管線執行的重新執行歷程記錄。

![檢視管線執行的歷程記錄](media/monitor-visually/rerun-history-image2.png)

## <a name="monitor-consumption"></a>監視耗用量

您可以按一下回合旁的耗用量圖示，查看管線回合所耗用的資源。 

![顯示管線所耗用資源的螢幕擷取畫面。](media/monitor-visually/monitor-consumption-1.png)

按一下圖示即可開啟該管線回合所使用的資源耗用量報告。 

![監視耗用量](media/monitor-visually/monitor-consumption-2.png)

您可以將這些值插入 [Azure 定價計算機](https://azure.microsoft.com/pricing/details/data-factory/)，以估算管線回合的成本。 如需 Azure Data Factory 定價的詳細資訊，請參閱[了解定價](pricing-concepts.md)。

> [!NOTE]
> 定價計算機所傳回的這些值是預估值。 其不會反映 Azure Data Factory 要您支付的確切金額 

## <a name="gantt-views"></a>Gantt 檢視

使用甘特圖檢視，以快速視覺化您的管線和活動回合。

![甘特圖的範例](media/monitor-visually/gantt1.png)

您可以查看每個管線的甘特圖，或依您在管線上建立的註譯/標籤進行分組。

![甘特圖註釋](media/monitor-visually/gantt2.png)

橫條的長度會告知管線的持續時間。 您也可以選取橫條以查看更多詳細資料。

![甘特圖持續時間](media/monitor-visually/gantt3.png)

## <a name="guided-tours"></a>導覽
選取左下方的 [資訊] 圖示。 然後選取 [導覽] 以取得監視管線及活動回合的逐步指示。

![導覽](media/monitor-visually/guided-tours.png)

## <a name="alerts"></a>警示

您可以針對 Data Factory 中支援的計量提出警示。 選取 [Data Factory 監視] 頁面上的 [監視] > [警示和計量]，即可開始使用。

![[Data factory 監視] 頁面](media/monitor-visually/start-page.png)

如需此功能的 7 分鐘簡介與示範，請觀看下列影片：

> [!VIDEO https://channel9.msdn.com/shows/azure-friday/Monitor-your-Azure-Data-Factory-pipelines-proactively-with-alerts/player]

### <a name="create-alerts"></a>建立警示

1.  選取 [新增警示規則] 來新建警示。

    ![[新增警示規則] 按鈕](media/monitor-visually/new-alerts.png)

1.  指定規則名稱，然後選取警示嚴重性。

    ![規則名稱和嚴重性的方塊](media/monitor-visually/name-and-severity.png)

1.  選取警示準則。

    ![目標準則的方塊](media/monitor-visually/add-criteria-1.png)

    ![螢幕擷取畫面，顯示您選取一個度量來設定警示條件的位置。](media/monitor-visually/add-criteria-2.png)

    ![準則清單](media/monitor-visually/add-criteria-3.png)

    您可以建立各種計量的警示，包括 ADF 實體計數/大小、活動/管線/觸發程式執行、Integration Runtime (IR) CPU 使用率/記憶體/節點計數/佇列，以及 SSIS 封裝執行和 SSIS IR 啟動/停止作業。

1.  設定警示邏輯。 您可以針對所有管線和對應活動，建立所選計量的警示。 您也可以選取特定活動類型、活動名稱、管線名稱或失敗類型。

    ![設定警示邏輯的選項](media/monitor-visually/alert-logic.png)

1.  設定警示的電子郵件、簡訊、推送和語音通知。 為警示通知建立動作群組，或選擇現有的動作群組。

    ![設定通知的選項](media/monitor-visually/configure-notification-1.png)

    ![新增通知的選項](media/monitor-visually/configure-notification-2.png)

1.  建立警示規則。

    ![建立警示規則的選項](media/monitor-visually/create-alert-rule.png)

## <a name="next-steps"></a>後續步驟

若要了解如何監視和管理管線，請參閱[以程式設計方式監視和管理管線](https://docs.microsoft.com/azure/data-factory/monitor-programmatically)一文。
