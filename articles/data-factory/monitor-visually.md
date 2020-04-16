---
title: 視覺化監視 Azure 資料工廠
description: 瞭解如何直覺地監視 Azure 資料工廠
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
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81419437"
---
# <a name="visually-monitor-azure-data-factory"></a>視覺化監視 Azure 資料工廠

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

在 Azure 數據工廠中創建和發佈管道後,可以將其與觸發器關聯,或手動啟動臨時運行。 您可以在 Azure 數據工廠使用者體驗中監視本機運行的所有管道。 要打開監視體驗,請在[Azure 門戶](https://portal.azure.com/)的數據工廠邊欄選項卡中選擇 **「監視器&管理**磁貼」。。 如果您已經在 ADF UX 中,請按一邊邊列上的 **「監視器」** 圖示。

所有數據工廠運行都顯示在瀏覽器的本地時區中。 如果更改時區,則所有日期/時間欄位將捕捉到您選擇的時間欄位。

## <a name="monitor-pipeline-runs"></a>監視管線回合

默認監視檢視是所選時間段內運行的管道的清單。 系統會顯示下列資料行：

| **資料行名稱** | **說明** |
| --- | --- |
| 管線名稱 | 管線的名稱 |
| 動作 | 允許您檢視活動詳細資訊、取消或重新執行管道的圖示 |
| 回合開始 | 管機執行的開始日期和時間(MM/DD/YYYY,HH:MM:SS AM/PM) |
| Duration | 回合持續時間 (HH:MM:SS) |
| 觸發方式 | 啟動導管的觸發器名稱 |
| 狀態 | **失敗**,**成功**,**正在取消**或已重新排。 已**取消**或**已排入**。 |
| 註解 | 與導管關聯的可篩選標記  |
| 參數 | 管線執行的參數(名稱/值對) |
| 錯誤 | 如果導管失敗,則執行錯誤 |
| 回合識別碼 | 管線執行的識別碼 |

![監視導管執行的列表檢視](media/monitor-visually/pipeline-runs.png)

您需要手動選擇 **「刷新**」按鈕以刷新管道和活動運行的清單。 當前不支援自動刷新。

![重新整理按鈕](media/monitor-visually/refresh.png)

## <a name="monitor-activity-runs"></a>監視活動回合

要檢視每個管道運行的活動,請在 **「操作」** 列下選擇 **「檢視活動運行**」圖示。 清單檢視顯示對應於每個管道運行的活動運行。

| **資料行名稱** | **說明** |
| --- | --- |
| 活動名稱 | 導管內活動的名稱 |
| 活動類型 | 作用的類型,如**複製**、**執行資料串**流或**AzureML 執行導管** |
| 動作 | 允許您檢視 JSON 輸入資訊、JSON 輸出資訊或特定於活動的詳細監視體驗的圖示 | 
| 回合開始 | 活動執行的開始日期和時間(MM/DD/YYYY,HH:MM:SS AM/PM) |
| Duration | 回合持續時間 (HH:MM:SS) |
| 狀態 | **失敗**,**成功**,**正在取消****或取消** |
| 整合執行時 | 作用在哪一個整合的執行時執行 |
| 使用者屬性 | 作用的使用者定義屬性 |
| 錯誤 | 如果作用失敗,則執行錯誤 |
| 回合識別碼 | 活動執行的識別碼 |

![監控活動執行的列表檢視](media/monitor-visually/activity-runs.png)

### <a name="promote-user-properties-to-monitor"></a>將使用者屬性升階以便監視

將任何管道活動屬性提升為使用者屬性,以便它成為您監視的實體。 例如,您可以將管道中複製活動的**源****和目標**屬性提升為使用者屬性。 選擇 **「自動產生**」可產生複製活動的**源****和目標**使用者屬性。

![建立使用者屬性](media/monitor-visually/monitor-user-properties-image1.png)

> [!NOTE]
> 您只能將最多五個管道活動屬性提升為使用者屬性。

創建使用者屬性後,可以在監視清單檢視中監視它們。 如果複製活動的源是表名稱,則可以在運行活動的列表檢視中將源表名稱作為列進行監視。

![活動執行清單沒有使用者屬性](media/monitor-visually/monitor-user-properties-image2.png)

![將使用者屬性的欄位到活動執行清單](media/monitor-visually/monitor-user-properties-image3.png)

![活動執行清單具有使用者屬性的資料行](media/monitor-visually/monitor-user-properties-image4.png)

## <a name="configure-the-list-view"></a>設定清單檢視

### <a name="order-and-filter"></a>訂購和篩選

根據運行開始時間切換管道運行是降序還是提升。 過濾導管使用以下欄位執行:

| **資料行名稱** | **說明** |
| --- | --- |
| 管線名稱 | 按管道的名稱進行篩選。 |
| 回合開始 |  確定顯示的管道運行的時間範圍。 選項包括**最近 24 小時**、**上周**和**最近 30 天**的快速篩選器,或選擇自訂日期和時間。 |
| 回合狀態 | 篩選器按狀態運行:**成功**「、」**失敗**「、」**已排隊**「、」**已取消「** 或 **」 正在進行**」。 |
| 註解 | 按套用於每個導管的標記進行篩選 |
| 執行 | 篩選是否要檢視重新執行的導管 |

![以篩選的選項](media/monitor-visually/filter.png)

### <a name="add-or-remove-columns"></a>新增或移除資料行
右鍵按一下列表檢視標題並選擇要顯示在清單檢視中的欄。

![欄位選項](media/monitor-visually/columns.png)

### <a name="adjust-column-widths"></a>調整資料行寬度
通過懸停在列標題上,增加和減少列表視圖中的列寬度。

## <a name="rerun-activities-inside-a-pipeline"></a>在管線中重新執行活動

您可以在管道中重新執行活動。 選擇 **「查看活動運行**」,然後選擇管道中要重新執行管道的活動。

![檢視活動執行](media/monitor-visually/rerun-activities-image1.png)

![選取活動執行](media/monitor-visually/rerun-activities-image2.png)

### <a name="rerun-from-failed-activity"></a>從失敗活動重新執行

如果活動失敗、超時或已取消,則可以通過**從失敗活動中選擇"重新運行**"來從該失敗活動重新運行管道。

![重新執行失敗活動](media/monitor-visually/rerun-failed-activity.png)

### <a name="view-rerun-history"></a>檢視重新執行歷程記錄

您可以在清單檢視中檢視所有管線執行的重新執行歷程記錄。

![檢視歷程記錄](media/monitor-visually/rerun-history-image1.png)

您也可以檢視特定管線執行的重新執行歷程記錄。

![檢視管線執行的歷程記錄](media/monitor-visually/rerun-history-image2.png)

## <a name="gantt-views"></a>甘特檢視

使用甘特檢視快速可視化管道和活動運行。

![甘特圖表示例](media/monitor-visually/gantt1.png)

您可以查看每個管道的 Gantt 檢視,也可以通過您在管道上創建的註釋/標記進行分組。

![甘特圖註解](media/monitor-visually/gantt2.png)

條形的長度通知管道的持續時間。 您還可以選擇該欄以查看更多詳細資訊。

![甘特圖表持續時間](media/monitor-visually/gantt3.png)

## <a name="guided-tours"></a>導覽
選擇左下角**的資訊圖示。** 然後選擇 **「引導之旅」** 取得有關如何監視管道和活動運行的分步說明。

![導覽](media/monitor-visually/guided-tours.png)

## <a name="alerts"></a>警示

您可以針對 Data Factory 中支援的計量提出警示。 在數據工廠監視頁上選擇 **「監視** > **警報&指標**以開始。

![資料工廠監視器頁面](media/monitor-visually/alerts01.png)

如需此功能的 7 分鐘簡介與示範，請觀看下列影片：

> [!VIDEO https://channel9.msdn.com/shows/azure-friday/Monitor-your-Azure-Data-Factory-pipelines-proactively-with-alerts/player]

### <a name="create-alerts"></a>建立警示

1.  選擇 **「新警報規則**」以創建新警報。

    ![新警示規則按鈕](media/monitor-visually/alerts02.png)

1.  指定規則名稱並選擇警報嚴重性。

    ![規則名稱與嚴重性框](media/monitor-visually/alerts03.png)

1.  選擇警報條件。

    ![目標條件框](media/monitor-visually/alerts04.png)

    ![條件清單](media/monitor-visually/alerts05.png)

1.  配置警報邏輯。 您可以針對所有管線和對應活動，建立所選計量的警示。 您還可以選擇特定的活動類型、活動名稱、管道名稱或失敗類型。

    ![設定警示邏輯的選項](media/monitor-visually/alerts06.png)

1.  為警報配置電子郵件、簡訊、推送和語音通知。 為警報通知創建操作組或選擇現有操作組。

    ![設定通知的選項](media/monitor-visually/alerts07.png)

    ![新增通知的選項](media/monitor-visually/alerts08.png)

1.  建立警示規則。

    ![建立警示規則的選項](media/monitor-visually/alerts09.png)

## <a name="next-steps"></a>後續步驟

要瞭解如何監視和管理管道,請參閱[監視器並以程式設計方式管理管道](https://docs.microsoft.com/azure/data-factory/monitor-programmatically)一文。
