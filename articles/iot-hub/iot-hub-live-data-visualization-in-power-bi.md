---
title: 資料 frm Azure IoT 中心的即時資料視覺化 – Power BI
description: 使用 Power BI 來視覺化收集自感應器並傳送至 Azure IoT 中樞的溫度和溼度資料。
author: robinsh
keywords: 即時資料視覺效果, 即時資料視覺效果, 感應器資料視覺效果
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 6/06/2019
ms.author: robinsh
ms.openlocfilehash: f0b909d10790511408e090546fd3359889ea5aca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "73954632"
---
# <a name="visualize-real-time-sensor-data-from-azure-iot-hub-using-power-bi"></a>使用 Power BI 將 Azure IoT 中樞的即時感應器資料視覺化

![端對端圖表](./media/iot-hub-live-data-visualization-in-power-bi/1_end-to-end-diagram.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

## <a name="what-you-learn"></a>您學到什麼

您可了解如何藉由使用 Power BI 將 Azure IoT 中樞收到的即時感應器資料視覺化。 如果要嘗試使用 Web 應用視覺化 IoT 中心中的資料，請參閱[使用 Web 應用視覺化 Azure IoT 中心的即時感應器資料](iot-hub-live-data-visualization-in-web-apps.md)。

## <a name="what-you-do"></a>您要做什麼

* 新增取用者群組，讓您的 IoT 中樞準備好進行資料存取。

* 建立、設定和執行串流分析作業，以便將資料從 IoT 中樞傳輸至 Power BI 帳戶。

* 建立及發佈 Power BI 報告，以將資料視覺化。

## <a name="what-you-need"></a>您需要什麼

* 完成[樹莓派線上模擬器](iot-hub-raspberry-pi-web-simulator-get-started.md)教程或設備教程之一;例如，[樹莓派與node.js。](iot-hub-raspberry-pi-kit-node-get-started.md) 這些文章涵蓋以下要求：
  
  * 有效的 Azure 訂用帳戶。
  * 位於您訂用帳戶中的 Azure IoT 中樞。
  * 將訊息傳送到您 Azure IoT 中樞的用戶端應用程式。

* Power BI 帳戶。 （[嘗試免費電源 BI](https://powerbi.microsoft.com/)）

[!INCLUDE [iot-hub-get-started-create-consumer-group](../../includes/iot-hub-get-started-create-consumer-group.md)]

## <a name="create-configure-and-run-a-stream-analytics-job"></a>設定、設定及執行串流分析作業

一開始先建立串流分析作業。 建立作業之後，定義輸入、輸出和用來擷取資料的查詢。

### <a name="create-a-stream-analytics-job"></a>建立串流分析作業

1. 在 [Azure 入口網站](https://portal.azure.com)中，選取 [建立資源]**** > [物聯網]**** > [串流分析作業]****。

2. 輸入作業的以下資訊。

   **作業名稱**：作業的名稱。 此名稱必須是全域唯一的。

   **資源組**：使用 IoT 中心使用的相同資源組。

   **位置**︰使用與資源群組相同的位置。

   ![在 Azure 中建立串流分析作業](./media/iot-hub-live-data-visualization-in-power-bi/create-stream-analytics-job-azure.png)

3. 選取 [建立]****。

### <a name="add-an-input-to-the-stream-analytics-job"></a>將輸入新增至串流分析作業

1. 開啟串流分析作業。

2. 在 [作業拓撲]**** 下方，選取 [輸入]****。

3. 在 **"輸入"** 窗格中，選擇 **"添加流輸入**"，然後從下拉清單中選擇**IoT 中心**。 在新的輸入窗格中，輸入以下資訊：

   **輸入別名**：輸入的唯一別名。

   **從訂閱中提供 IoT 中心**：選擇此選項按鈕。

   **訂閱**：選擇用於本教程的 Azure 訂閱。

   **IoT 中心**：選擇用於本教程的 IoT 中心。

   **端點**：選取 [傳訊]****。

   **共用訪問策略名稱**：選擇要將流分析作業用於 IoT 中心的共用訪問策略的名稱。 在本教程中，您可以選擇*服務*。 預設情況下 *，服務策略*在新的 IoT 中心上創建，並授予在 IoT 中心公開的雲端終結點上發送和接收的許可權。 要瞭解更多資訊，請參閱[存取控制和許可權](iot-hub-devguide-security.md#access-control-and-permissions)。

   **共用訪問策略鍵**：此欄位根據對共用訪問策略名稱的選擇自動填滿。

   **消費者組**：選擇以前創建的消費者組。

   將所有其他欄位保留為預設值。

   ![在 Azure 中將輸入新增至串流分析作業](./media/iot-hub-live-data-visualization-in-power-bi/add-input-to-stream-analytics-job-azure.png)

4. 選取 [儲存]****。

### <a name="add-an-output-to-the-stream-analytics-job"></a>將輸出新增至串流分析作業

1. 在 [作業拓撲]**** 下方，選取 [輸出]****。

2. 在 **"輸出"** 窗格中，選擇 **"添加**"和 **"電源 BI**"。

3. 在**Power BI - 新輸出**窗格中，選擇 **"授權"** 並按照提示登錄到 Power BI 帳戶。

4. 登錄到 Power BI 後，請輸入以下資訊：

   **輸出別名**：輸出的唯一別名。

   **群組工作區**︰選取您的目標群組工作區。

   **資料集名稱**︰輸入資料集名稱。

   **資料表名稱**︰輸入資料表名稱。

   ![在 Azure 中將輸出新增至串流分析作業](./media/iot-hub-live-data-visualization-in-power-bi/add-output-to-stream-analytics-job-azure.png)

5. 選取 [儲存]****。

### <a name="configure-the-query-of-the-stream-analytics-job"></a>設定串流分析作業的查詢

1. 在 [作業拓撲]**** 下方，選取 [查詢]****。

2. 使用作業的輸入別名取代 `[YourInputAlias]`。

3. 使用作業的輸出別名取代 `[YourOutputAlias]`。

   ![在 Azure 中將查詢新增至串流分析作業](./media/iot-hub-live-data-visualization-in-power-bi/add-query-stream-analytics-job-azure.png)

4. 選取 [儲存]****。

### <a name="run-the-stream-analytics-job"></a>執行串流分析作業

在"流分析"作業中，選擇 **"概述**"，然後選擇 **"立即** > **開始**"。 **Start**  >  成功啟動作業後，作業狀態會從 [已停止]**** 變更為 [執行中]****。

![在 Azure 中執行串流分析作業](./media/iot-hub-live-data-visualization-in-power-bi/run-stream-analytics-job-azure.png)

## <a name="create-and-publish-a-power-bi-report-to-visualize-the-data"></a>建立及發佈 Power BI 報告，以將資料視覺化。

1. 確保範例應用程式正在您的裝置上執行。 如果沒有，您可以參考[設定您的裝置](https://docs.microsoft.com/azure/iot-hub/iot-hub-raspberry-pi-kit-node-get-started)下的教學課程。

2. 登入您的 [Power BI](https://powerbi.microsoft.com/en-us/) 帳戶。

3. 選擇您使用的工作區"**我的工作區**"。

4. 選擇**資料集**。

   您應該會看到在建立串流分析作業輸出時所指定的資料集。

5. 對於創建的資料集，選擇 **"添加報表**"（資料集名稱右側的第一個圖示）。

   ![建立 Microsoft Power BI 報告](./media/iot-hub-live-data-visualization-in-power-bi/start-power-bi.png)

6. 建立折線圖以顯示一段時間的即時溫度。

   1. 在報表創建頁的 **"視覺化"** 窗格中，選擇折線圖圖示以添加折線圖。

   2. 在 [欄位]**** 窗格上，展開您建立串流分析作業輸出時指定的資料表。

   3. 將 **EventEnqueuedUtcTime** 拖放至 [視覺效果]**** 窗格上的 [軸]****。

   4. 將 **temperature** 拖放至 [值]****。

      折線圖已建立。 x 軸會顯示 UTC 時區的日期和時間。 y 軸會顯示感應器的溫度。

      ![將溫度的折線圖新增至 Microsoft Power BI 報告](./media/iot-hub-live-data-visualization-in-power-bi/power-bi-add-temp.png)

7. 建立另一個折線圖以顯示一段時間的即時溼度。 若要這麼做，請遵循上述相同步驟，並將 **EventEnqueuedUtcTime** 放在 x 軸上，將 **humidity** 放在 y 軸上。

   ![將溼度的折線圖新增至 Microsoft Power BI 報告](./media/iot-hub-live-data-visualization-in-power-bi/power-bi-add-humidity.png)

8. 選取 [儲存]**** 以儲存報告。

9. 選擇左側窗格上的 **"報表**"，然後選擇剛剛創建的報表。

10. 選擇**檔** > **發佈到 Web**。

11. 選擇 **"創建嵌入代碼**"，然後選擇 **"發佈**"。

您將獲得報告連結，您可以與任何人共用以進行報表訪問，以及可用於將報表集成到博客或網站的程式碼片段。

![發佈 Microsoft Power BI 報告](./media/iot-hub-live-data-visualization-in-power-bi/power-bi-publish.png)

Microsoft 也會提供 [Power BI 行動應用程式](https://powerbi.microsoft.com/en-us/documentation/powerbi-power-bi-apps-for-mobile-devices/)，以供檢視並與您行動裝置上的 Power BI 儀表板和報告互動。

## <a name="next-steps"></a>後續步驟

您已成功使用 Power BI 將 Azure IoT 中樞的即時感應器資料視覺化。

有關從 Azure IoT 中心視覺化資料的另一種方法，請參閱[使用 Web 應用視覺化來自 Azure IoT 中心的即時感應器資料](iot-hub-live-data-visualization-in-web-apps.md)。

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
