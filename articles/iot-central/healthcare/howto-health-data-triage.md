---
title: 使用 Azure IoT Central 建立健康情況資料分級儀表板 | Microsoft Docs
description: 了解如何使用 Azure IoT Central 應用程式範本建置健康情況資料分級儀表板。
author: philmea
ms.author: philmea
ms.date: 10/23/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
manager: eliotgra
ms.openlocfilehash: 5175575bcd968ab9d9bb9db7e284eb332bc7f675
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/26/2020
ms.locfileid: "92542415"
---
# <a name="tutorial-build-a-power-bi-provider-dashboard"></a>教學課程：建置 Power BI 提供者儀表板



建置持續性患者監視解決方案時，您也可以為醫院護理團隊建立儀表板，將患者資料視覺化。 此教學課程將逐步引導您完成從 IoT Central 持續性患者監視應用程式範本建立 Power BI 即時串流儀表板的步驟。 如果您的使用案例不需要存取即時資料，可以使用 [IoT Central Power BI 儀表板](../core/howto-connect-powerbi.md)，其具有簡化的部署流程。 

>[!div class="mx-imgBorder"]
>![儀表板 GIF](media/dashboard-gif-3.gif)

基本架構將遵循下列結構：

>[!div class="mx-imgBorder"] 
>![提供者分級儀表板](media/dashboard-architecture.png)

在本教學課程中，您會了解如何：

> [!div class="checklist"]
> * 將資料從 Azure IoT Central 匯出至 Azure 事件中樞
> * 設定 Power BI 串流資料集
> * 將邏輯應用程式連線至 Azure 事件中樞
> * 從邏輯應用程式將資料串流至 Power BI
> * 建置適用於患者重要器官的即時儀表板

## <a name="prerequisites"></a>先決條件

* Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請先[註冊免費的 Azure 帳戶](https://azure.microsoft.com/free/)。

* Azure IoT Central 持續性患者監視應用程式範本。 如果您還沒有範本，可以遵循[部署應用程式範本](overview-iot-central-healthcare.md)的步驟進行。

* Azure [事件中樞命名空間與事件中樞](../../event-hubs/event-hubs-create.md)。

* 您要存取事件中樞的邏輯應用程式。 若要使用 Azure 事件中樞觸發程序啟動邏輯應用程式，您需要一個[空白的邏輯應用程式](../../logic-apps/quickstart-create-first-logic-app-workflow.md)。

* Power BI 服務帳戶。 如果您還沒有帳戶，可以[為 Power BI 服務建立免費的試用帳戶](https://app.powerbi.com/)。 如果您之前未曾用過 Power BI，瀏覽[開始使用 Power BI](/power-bi/service-get-started) 可能會很有幫助。

## <a name="set-up-a-continuous-data-export-to-azure-event-hubs"></a>設定 Azure 事件中樞的連續資料匯出
您將必須先設定從 Azure IoT Central 應用程式範本到訂用帳戶中的 Azure 事件中樞的連續資料匯出。 您可以遵循本 Azure IoT Central 教學課程中的步驟，[匯出至事件中樞](../core/howto-export-data.md)。 基於此教學課程的目的，您只需要針對遙測匯出。

## <a name="create-a-power-bi-streaming-dataset"></a>建立 Power BI 串流資料集

1. 登入您的 Power BI 帳戶。

2. 在您慣用的工作區中，選取工具列右上角的 [+ 建立] 按鈕，以建立新的串流資料集。 您將必須針對您想要在儀表板上擁有的每個患者，建立個別的資料集。

    >[!div class="mx-imgBorder"] 
    >![建立串流資料集](media/create-streaming-dataset.png)

3. 針對您資料集的來源，選擇 **API** 。

4. 針對您的資料集輸入 **名稱** (例如，患者的姓名)，然後從您的串流填入值。 您可以在持續性患者監視應用程式範本中，根據來自模擬裝置的值，查看下列範例。 此範例有兩個患者：

    * Teddy Silvers，其中包含來自 Smart Knee Brace 的資料
    * Yesenia Sanford，其中包含來自 Smart Vitals Patch 的資料

    >[!div class="mx-imgBorder"] 
    >![輸入資料集值](media/enter-dataset-values.png)

若要深入了解 Power BI 中的串流資料集，您可以閱讀此文件的 [Power BI 中的即時串流](/power-bi/service-real-time-streaming)。

## <a name="connect-your-logic-app-to-azure-event-hubs"></a>將邏輯應用程式連線至 Azure 事件中樞
若要將邏輯應用程式連線至 Azure 事件中樞，您可以依照此文件的[使用 Azure 事件中樞與 Azure Logic Apps 傳送事件](../../connectors/connectors-create-api-azure-event-hubs.md#add-event-hubs-action) \(部分機器翻譯\) 中所述的指示進行。 以下是一些建議的參數：

|參數|值|
|---|---|
|內容類型|application/json|
|間隔|3|
|頻率|Second|

在此步驟結束時，您的邏輯應用程式設計工具看起來應該像這樣：

>[!div class="mx-imgBorder"] 
>![Logic Apps 連線至事件中樞](media/eh-logic-app.png)

## <a name="stream-data-to-power-bi-from-your-logic-app"></a>從邏輯應用程式將資料串流至 Power BI
下一個步驟將要剖析來自事件中樞的資料，將其串流至您先前建立的 Power BI 資料集。

1. 在執行此動作之前，您將必須了解從裝置傳送到事件中樞的 JSON 承載。 方法是，查看此[範例結構描述](../core/howto-export-data.md#telemetry-format)並加以修改，以符合您的結構描述，或使用[服務匯流排總管](https://github.com/paolosalvatori/ServiceBusExplorer)檢查訊息。 如果您使用的是持續性患者監視應用程式，您的訊息看起來會像這樣：

**Smart Vitals Patch 遙測**

```json
{
  "HeartRate": 80,
  "RespiratoryRate": 12,
  "HeartRateVariability": 64,
  "BodyTemperature": 99.08839032397609,
  "BloodPressure": {
    "Systolic": 23,
    "Diastolic": 34
  },
  "Activity": "walking"
}
```

**Smart Knee Brace 遙測**

```json
{
  "Acceleration": {
    "x": 72.73510947763711,
    "y": 72.73510947763711,
    "z": 72.73510947763711
  },
  "RangeOfMotion": 123,
  "KneeBend": 3
}
```

**屬性**

```json
{
  "iothub-connection-device-id": "1qsi9p8t5l2",
  "iothub-connection-auth-method": "{\"scope\":\"device\",\"type\":\"sas\",  \"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}",
  "iothub-connection-auth-generation-id": "637063718586331040",
  "iothub-enqueuedtime": 1571681440990,
  "iothub-message-source": "Telemetry",
  "iothub-interface-name": "Patient_health_data_3bk",
  "x-opt-sequence-number": 7,
  "x-opt-offset": "3672",
  "x-opt-enqueued-time": 1571681441317
}
```

2. 既然您已經檢查過您的 JSON 承載，請回到邏輯應用程式設計工具，然後選取 [+ 新增步驟]。 搜尋並新增 [初始化變數] 作為下一個步驟，並輸入下列參數：

    |參數|值|
    |---|---|
    |名稱|介面名稱|
    |類型|String|

    按 [儲存]。 

3. 新增另一個名為 **Body** 且 [類型] 為 [字串] 的變數。 您的邏輯應用程式將會新增下列動作：

    >[!div class="mx-imgBorder"]
    >![初始化變數](media/initialize-string-variables.png)
    
4. 選取 [+ 新增步驟]，然後新增 [剖析 JSON] 動作。 將此重新命名為 **Parse Properties** 。 針對 [內容]，選擇來自事件中樞的 [內容]。 選取底部的 [請使用範例承載產生結構描述]，然後從上方的 [內容] 區段貼上範例承載。

5. 接下來，選擇 [設定變數] 動作，並使用剖析的 JSON 屬性中的 **iothub-interface-name** 更新您的 [介面名稱] 變數。

6. 新增 **Split** 控制項作為下一個動作，然後選擇 [介面名稱] 變數作為 On 參數。 您將使用此參數，將資料注入正確的資料集。

7. 在您的 Azure IoT Central 應用程式中，從 [裝置範本] 檢視尋找 Smart Vitals Patch 健康情況資料與 Smart Knee Brace 健康情況資料的介面名稱。 為每個 [介面名稱] 建立兩個不同的 **Switch** 控制項案例，並適當地重新命名該控制項。 您可以設定預設案例使用 **Terminate** 控制項，並選擇您想要顯示的狀態。

    >[!div class="mx-imgBorder"] 
    >![Split 控制項](media/split-by-interface.png)

8. 針對 **Smart Vitals Patch** 案例，新增 [剖析 JSON] 動作。 針對 [內容]，選擇來自事件中樞的 [內容]。 複製並貼上適用於上述 Smart Vitals Patch 的範例承載，以產生結構描述。

9. 新增 [設定變數] 動作，並以步驟 7 中剖析的 JSON 的 **Body** 更新 **Body** 變數。

10. 新增 **Condition** 控制項作為下一個動作，並將條件設定為 **Body** 、 **contains** 、 **HeartRate** 。 這可確保您在填入 Power BI 資料集之前，有一組來自 Smart Vitals Patch 的正確資料。 步驟 7-9 看起來會像下面這樣：

    >[!div class="mx-imgBorder"] 
    >![Smart Vitals 新增條件](media/smart-vitals-pbi.png)

11. 針對 [條件] 的 **True** 案例，新增一個可呼叫 [將資料列新增至資料集] Power BI 功能的動作。 為此，您將必須登入 Power BI。 **False** 案例可以再次使用 **Terminate** 控制項。

12. 選擇適當的 [工作區]、[資料集] 與 [資料表]。 將您在 Power BI 建立串流資料集時所指定的參數，對應至來自事件中樞的已剖析 JSON 值。 填入的動作看起來應該像這樣：

    >[!div class="mx-imgBorder"] 
    >![將資料列新增至 Power BI](media/add-rows-yesenia.png)

13. 針對 **Smart Knee Brace** 切換案例，新增 [剖析 JSON] 動作來剖析內容，類似於步驟 7。 接著， **將資料列新增至資料集** ，以便在 Power BI 中更新您的 Teddy Silvers 資料集。

    >[!div class="mx-imgBorder"] 
    >![螢幕擷取畫面：顯示如何將資料列新增至資料集。](media/knee-brace-pbi.png)

14. 按 [儲存]，然後執行您的邏輯應用程式。

## <a name="build-a-real-time-dashboard-for-patient-vitals"></a>建置適用於患者重要器官的即時儀表板
現在返回 Power BI 並選取 [+ 建立]，以建立新的 [儀表板]。 為儀表板命名，然後按 [建立]。

選取上方瀏覽列中的三個點，然後選取 [+ 新增磚]。

>[!div class="mx-imgBorder"] 
>![將圖格新增至儀表板](media/add-tile.png)

選擇您想要新增的圖格類型，並以您想要的方式自訂您的應用程式。

## <a name="clean-up-resources"></a>清除資源

如果您不打算繼續使用此應用程式，請使用下列步驟刪除您的資源：

1. 從 Azure 入口網站中，您可以刪除您所建立的事件中樞與 Logic Apps 資源。

2. 針對您的 IoT Central 應用程式，請移至 [系統管理] 索引標籤，然後選取 [刪除]。

## <a name="next-steps"></a>後續步驟

* 檢閱[持續性患者監視架構指導方針](concept-continuous-patient-monitoring-architecture.md)。