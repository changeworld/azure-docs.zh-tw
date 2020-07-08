---
title: 資料以及 Azure IoT 中樞的即時資料視覺效果-Power BI
description: 使用 Power BI 來視覺化收集自感應器並傳送至 Azure IoT 中樞的溫度和溼度資料。
author: robinsh
keywords: 即時資料視覺效果, 即時資料視覺效果, 感應器資料視覺效果
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 6/08/2020
ms.author: robinsh
ms.openlocfilehash: ed429d2f584da20439b0cb0eedcf4742b9ae4599
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84634364"
---
# <a name="visualize-real-time-sensor-data-from-azure-iot-hub-using-power-bi"></a>使用 Power BI 將 Azure IoT 中樞的即時感應器資料視覺化

![端對端圖表](./media/iot-hub-live-data-visualization-in-power-bi/end-to-end-diagram.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

## <a name="what-you-learn"></a>您學到什麼

您可了解如何藉由使用 Power BI 將 Azure IoT 中樞收到的即時感應器資料視覺化。 如果您想要嘗試使用 web 應用程式將 IoT 中樞的資料視覺化，請參閱[使用 web 應用程式將來自 Azure IoT 中樞的即時感應器資料視覺化](iot-hub-live-data-visualization-in-web-apps.md)。

## <a name="what-you-do"></a>您要做什麼

* 新增取用者群組，讓您的 IoT 中樞準備好進行資料存取。

* 建立、設定和執行串流分析作業，以便將資料從 IoT 中樞傳輸至 Power BI 帳戶。

* 建立及發佈 Power BI 報告，以將資料視覺化。

## <a name="what-you-need"></a>您需要什麼

* 完成 [Raspberry Pi 線上模擬器](iot-hub-raspberry-pi-web-simulator-get-started.md)教學課程，或其中一個裝置教學課程；例如，[Raspberry Pi with node.js](iot-hub-raspberry-pi-kit-node-get-started.md)。 這些文章涵蓋下列需求：
  
  * 有效的 Azure 訂用帳戶。
  * 位於您訂用帳戶中的 Azure IoT 中樞。
  * 將訊息傳送到您 Azure IoT 中樞的用戶端應用程式。

* Power BI 帳戶。 （[免費試用 Power BI](https://powerbi.microsoft.com/)）

[!INCLUDE [iot-hub-get-started-create-consumer-group](../../includes/iot-hub-get-started-create-consumer-group.md)]

## <a name="create-configure-and-run-a-stream-analytics-job"></a>設定、設定及執行串流分析作業

一開始先建立串流分析作業。 建立作業之後，定義輸入、輸出和用來擷取資料的查詢。

### <a name="create-a-stream-analytics-job"></a>建立串流分析作業

1. 在 [Azure 入口網站](https://portal.azure.com)中，選取 [建立資源]**** > [物聯網]**** > [串流分析作業]****。

2. 輸入作業的以下資訊。

   **作業名稱**：作業的名稱。 此名稱必須是全域唯一的。

   **資源群組**：使用 IoT 中樞所用的相同資源群組。

   **位置**：使用與資源群組相同的位置。

   ![在 Azure 中建立串流分析作業](./media/iot-hub-live-data-visualization-in-power-bi/create-stream-analytics-job.png)

3. 選取 [建立]。

### <a name="add-an-input-to-the-stream-analytics-job"></a>將輸入新增至串流分析作業

1. 開啟串流分析作業。

2. 在 [**作業拓撲**] 下，選取 [**輸入**]。

3. 在 [**輸入**] 窗格中，選取 [**新增資料流程輸入**]，然後從下拉式清單中選取 [ **IoT 中樞**]。 在 [新增輸入] 窗格中，輸入下列資訊：

   **輸入別名**：輸入輸入的唯一別名。

   **從您的訂用帳戶選取 IoT 中樞**：選取此選項按鈕。

   **訂**用帳戶：選取您在本教學課程中使用的 Azure 訂用帳戶。

   **IoT 中樞**：選取您在本教學課程中使用的 IoT 中樞。

   **端點**：選取 [傳訊]****。

   **共用存取原則名稱**：選取您要串流分析作業用於 IoT 中樞的共用存取原則名稱。 在本教學課程中，您可以選取 [*服務*]。 *服務*原則預設會在新的 iot 中樞上建立，並授與在 IoT 中樞所公開的雲端端端點上傳送和接收的許可權。 若要深入瞭解，請參閱[存取控制和許可權](iot-hub-devguide-security.md#access-control-and-permissions)。

   **共用存取原則金鑰**：此欄位會根據您選取的共用存取原則名稱自動填入。

   取用**者群組**：選取您先前建立的取用者群組。

   將所有其他欄位保留為預設值。

   ![在 Azure 中將輸入新增至串流分析作業](./media/iot-hub-live-data-visualization-in-power-bi/add-input-to-stream-analytics-job.png)

4. 選取 [儲存]。

### <a name="add-an-output-to-the-stream-analytics-job"></a>將輸出新增至串流分析作業

1. 在 [**作業拓撲**] 下，選取 [**輸出**]。

2. 在 [**輸出**] 窗格中，選取 [**新增**]，然後**Power BI**]。

3. 在 [ **Power BI-新增輸出**] 窗格上，選取 [**授權**]，並遵循提示來登入您的 Power BI 帳戶。

4. 登入 Power BI 之後，請輸入下列資訊：

   **輸出別名**：輸出的唯一別名。

   **群組工作區**：選取您的目標群組工作區。

   **資料集名稱**：輸入資料集名稱。

   **資料表名稱**：輸入資料表名稱。

   **驗證模式**：保留預設值。

   ![在 Azure 中將輸出新增至串流分析作業](./media/iot-hub-live-data-visualization-in-power-bi/add-output-to-stream-analytics-job.png)

5. 選取 [儲存]。

### <a name="configure-the-query-of-the-stream-analytics-job"></a>設定串流分析作業的查詢

1. 在 [**作業拓撲**] 下，選取 [**查詢**]。

2. 使用作業的輸入別名取代 `[YourInputAlias]`。

3. 使用作業的輸出別名取代 `[YourOutputAlias]`。

   ![在 Azure 中將查詢新增至串流分析作業](./media/iot-hub-live-data-visualization-in-power-bi/add-query-to-stream-analytics-job.png)

4. 選取 [**儲存查詢**]。

### <a name="run-the-stream-analytics-job"></a>執行串流分析作業

在串流分析作業中選取 **[總覽**]，然後選取 [立即**啟動**]  >  **Now**  >  ** **。 成功啟動作業後，作業狀態會從 [已停止] 變更為 [執行中]。

![在 Azure 中執行串流分析作業](./media/iot-hub-live-data-visualization-in-power-bi/run-stream-analytics-job.png)

## <a name="create-and-publish-a-power-bi-report-to-visualize-the-data"></a>建立及發佈 Power BI 報告，以將資料視覺化。

下列步驟示範如何使用 Power BI 服務來建立和發行報表。 如果您想要在 Power BI 中使用「新外觀」，您可以遵循下列步驟來進行修改。 若要瞭解差異，以及如何在「新外觀」中流覽，請參閱[Power BI 服務的「新外觀](https://docs.microsoft.com/power-bi/consumer/service-new-look)」。

1. 確保範例應用程式正在您的裝置上執行。 如果沒有，您可以參考[設定您的裝置](https://docs.microsoft.com/azure/iot-hub/iot-hub-raspberry-pi-kit-node-get-started)下的教學課程。

2. 登入您的 [Power BI](https://powerbi.microsoft.com/en-us/) 帳戶。

3. 選取您使用的工作區 [**我的工作區**]。

4. 選取 [資料集]。

   您應該會看到在建立串流分析作業輸出時所指定的資料集。

5. 針對您所建立的資料集，選取 [**加入報表**] （資料集名稱右邊的第一個圖示）。

   ![建立 Microsoft Power BI 報告](./media/iot-hub-live-data-visualization-in-power-bi/power-bi-create-report.png)

6. 建立折線圖以顯示一段時間的即時溫度。

   1. 在報表建立頁面的 [**視覺效果**] 窗格上，選取 [折線圖] 圖示以加入折線圖。

   2. 在 [欄位]**** 窗格上，展開您建立串流分析作業輸出時指定的資料表。

   3. 將 **EventEnqueuedUtcTime** 拖放至 [視覺效果]**** 窗格上的 [軸]****。

   4. 將 **temperature** 拖放至 [值]****。

      折線圖已建立。 x 軸會顯示 UTC 時區的日期和時間。 y 軸會顯示感應器的溫度。

      ![將溫度的折線圖新增至 Microsoft Power BI 報告](./media/iot-hub-live-data-visualization-in-power-bi/power-bi-add-temperature.png)

7. 建立另一個折線圖以顯示一段時間的即時溼度。 若要這麼做，請按一下畫布的空白部分，然後遵循上述相同步驟，將**EventEnqueuedUtcTime**放置在 X 軸上，並將**濕度**放在 y 軸上。

   ![將溼度的折線圖新增至 Microsoft Power BI 報告](./media/iot-hub-live-data-visualization-in-power-bi/power-bi-add-humidity.png)

8. 選取 [儲存]**** 以儲存報告。

9. 選取左窗格中的 [**報表**]，然後選取您剛建立的報表。

10. 選取 **[** 檔案] [  >  **發佈至網路**]。

    ![針對 Microsoft Power BI 報表選取 [發佈至網路]](./media/iot-hub-live-data-visualization-in-power-bi/power-bi-select-publish-to-web.png)

    > [!NOTE]
    > 如果您收到通知，詢問您的系統管理員以啟用內嵌程式碼建立，您可能需要與他們聯繫。 您必須先啟用內嵌程式碼建立，才能完成此步驟。
    >
    > ![聯絡您的系統管理員通知](./media/iot-hub-live-data-visualization-in-power-bi/contact-admin.png)

11. 選取 [**建立內嵌程式碼**]，然後選取 [**發佈**]。

系統會提供您可以與任何人共用報表存取權的報告連結，以及可用於將報表整合到您的 blog 或網站的程式碼片段。

![發佈 Microsoft Power BI 報告](./media/iot-hub-live-data-visualization-in-power-bi/power-bi-web-output.png)

Microsoft 也會提供 [Power BI 行動應用程式](https://powerbi.microsoft.com/en-us/documentation/powerbi-power-bi-apps-for-mobile-devices/)，以供檢視並與您行動裝置上的 Power BI 儀表板和報告互動。

## <a name="next-steps"></a>後續步驟

您已成功使用 Power BI 將 Azure IoT 中樞的即時感應器資料視覺化。

如需從 Azure IoT 中樞將資料視覺化的另一種方式，請參閱[使用 web 應用程式將來自 Azure IoT 中樞的即時感應器資料視覺化](iot-hub-live-data-visualization-in-web-apps.md)。

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
