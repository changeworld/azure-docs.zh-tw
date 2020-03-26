---
title: 教學課程：監視 IoT 裝置空間 - Azure Digital Twins| Microsoft Docs
description: 了解如何依照本教學課程中的步驟，使用 Azure Digital Twins 來佈建空間資源及監視運作狀況。
services: digital-twins
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.custom: seodec18
ms.service: digital-twins
ms.topic: tutorial
ms.date: 01/10/2020
ms.openlocfilehash: 6cf6a8f7de181a81d60028e33ba2631815c8ca04
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/24/2020
ms.locfileid: "75895346"
---
# <a name="tutorial-provision-your-building-and-monitor-working-conditions-with-azure-digital-twins-preview"></a>教學課程：使用 Azure Digital Twins 預覽版來佈建建築物及監視運作狀況

本教學課程會示範如何使用 Azure Digital Twins 預覽版來監視您的空間是否有所需的溫度條件和舒適程度。 [設定範例建築物](tutorial-facilities-setup.md)後，您可以佈建您的建築物，並且依照本教學課程中的步驟對感應器資料執行自訂函式。

在本教學課程中，您會了解如何：

> [!div class="checklist"]
> * 定義要監視的狀況。
> * 建立使用者定義的函式 (UDF)。
> * 模擬感應器資料。
> * 取得使用者定義的函式結果。

## <a name="prerequisites"></a>Prerequisites

本教學課程假設您已經[完成 Azure Digital Twins 設定](tutorial-facilities-setup.md)。 繼續之前，請確定您有：

- [Azure 帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- 執行中的 Digital Twins 執行個體。 
- 在工作電腦上下載並解壓縮 [Digital Twins C# 範例](https://github.com/Azure-Samples/digital-twins-samples-csharp)。 
- 在開發電腦上安裝 [.NET Core SDK 2.1.403 版或更新版本](https://www.microsoft.com/net/download)，以建置和執行範例。 執行 `dotnet --version` 來確認是否已安裝正確版本。 
- [Visual Studio Code](https://code.visualstudio.com/) 以探索範例程式碼。 

>[!TIP]
> 如果您要佈建新的執行個體，請使用唯一的 Digital Twins 執行個體名稱。

## <a name="define-conditions-to-monitor"></a>定義要監視的狀況

您可以定義一組要在裝置或感應器資料中監視的特定狀況，稱之為「比對器」  。 接著，您可以定義稱為「使用者定義的函式」  的函式。 如果發生比對器所指定的狀況，這些函式可對來自空間和裝置的資料執行自訂邏輯。 如需詳細資訊，請閱讀[資料處理與使用者定義的函式](concepts-user-defined-functions.md)。 

從 **occupancy-quickstart** 範例專案，在 Visual Studio Code 中開啟 **src\actions\provisionSample.yaml** 檔案。 請注意，以 **matchers** 類型開頭的區段。 此類型下的每個項目都會建立具有指定**名稱**的比對器。 比對器會監視 **dataTypeValue** 類型的感應器。 請注意，它與名為「聚焦會議室 A1」  的空間有何關聯，它具有包含幾個感應器的 **devices** 節點。 若要佈建會追蹤其中一個感應器的比對器，請確定其 **dataTypeValue** 符合該感應器的 **dataType**。 

在現有比對器下面，新增下列比對器。 確定索引鍵相符且並未以 Tab 取代空格。 這幾行也會出現在 provisionSample.yaml  檔案中，並已註解化。 您可以藉由移除每一行前面的 `#` 字元來將該行取消註解。

```yaml
      - name: Matcher Temperature
        dataTypeValue: Temperature
```

這個比對器會追蹤您在[第一個教學課程](tutorial-facilities-setup.md)中新增的 `SAMPLE_SENSOR_TEMPERATURE` 感應器。 

## <a name="create-a-user-defined-function"></a>建立使用者定義的函式

您可以使用使用者定義的函式來自訂感應器資料的處理方式。 這些函式是在發生比對器所描述的特定狀況時，可以在 Azure Digital Twins 執行個體內執行的自訂 JavaScript 程式碼。 您可以針對您想要監視的每個感應器，建立比對器和使用者定義的函式。 如需詳細資訊，請閱讀[資料處理與使用者定義的函式](concepts-user-defined-functions.md)。 

在範例 provisionSample.yaml  檔案中，尋找以 **userdefinedfunctions** 類型開頭的區段。 這一節會佈建具有指定 **Name** 的使用者定義函式。 該函式會對 **matcherNames** 之下的比對器清單採取動作。 請注意，如何針對 UDF 提供您自己的 JavaScript 檔案作為 **script**。

也請注意名為 **roleassignments** 的區段。 它會將「空間管理員」角色指派給使用者定義的函式。 此角色可讓它存取來自任何已佈建空間的事件。 

1. 在 *provisionSample.yaml* 檔案的 `matcherNames` 節點中新增或取消註解下列這行，將 UDF 設定為包含溫度比對器：

    ```yaml
            - Matcher Temperature
    ```

1. 在編輯器中開啟 **src\actions\userDefinedFunctions\availability.js** 檔案。 這是 provisionSample.yaml  的 **script** 元素中參考的檔案。 此檔案中的使用者定義函式會尋找以下狀況：在會議室中偵測不到任何移動，且二氧化碳濃度低於 1,000 ppm。 

   修改 JavaScript 檔案，以監視溫度以及其他狀況。 新增下列幾行程式碼以尋找下列狀況：在會議室中偵測不到任何移動、二氧化碳濃度低於 1,000 ppm，以及溫度低於華氏 78 度。

   >[!NOTE]
   > 這一節會修改 src\actions\userDefinedFunctions\availability.js  檔案，讓您深入了解用於撰寫使用者定義函式的一種方法。 不過，您可以選擇在您的設定中直接使用 [src\actions\userDefinedFunctions\availabilityForTutorial.js](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/actions/userDefinedFunctions/availabilityForTutorial.js) 檔案。 此檔案具有本教學課程需要的所有變更。 如果您改為使用此檔案，請務必使用 [src\actions\provisionSample.yaml](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/actions/provisionSample.yaml) 中 **script** 索引鍵的正確檔名。

    a. 在檔案頂端，針對註解 `// Add your sensor type here` 下方的溫度新增下列幾行：

    ```JavaScript
        var temperatureType = "Temperature";
        var temperatureThreshold = 78;
    ```

    b. 在定義 `var motionSensor` 的陳述式 (位於註解 `// Add your sensor variable here` 下方) 之後新增下列幾行：

     ```JavaScript
        var temperatureSensor = otherSensors.find(function(element) {
            return element.DataType === temperatureType;
        });
    ```

    c. 在定義 `var carbonDioxideValue` 的陳述式 (位於註解 `// Add your sensor latest value here` 下方) 之後新增下列幾行：

    ```JavaScript
        var temperatureValue = getFloatValue(temperatureSensor.Value().Value);
    ```

    d. 移除註解 `// Modify this line to monitor your sensor value` 下方的下列幾行程式碼：

     ```JavaScript
        if(carbonDioxideValue === null || motionValue === null) {
            sendNotification(telemetry.SensorId, "Sensor", "Error: Carbon dioxide or motion are null, returning");
            return;
        }
    ```

    使用以下幾行取代它們：

    ```JavaScript
        if(carbonDioxideValue === null || motionValue === null || temperatureValue === null){
            sendNotification(telemetry.SensorId, "Sensor", "Error: Carbon dioxide, motion, or temperature are null, returning");
            return;
        }
    ```

    e. 移除註解 `// Modify these lines as per your sensor` 下方的下列幾行程式碼：

    ```JavaScript
        var availableFresh = "Room is available and air is fresh";
        var noAvailableOrFresh = "Room is not available or air quality is poor";
    ```

    使用以下幾行取代它們：

    ```JavaScript
        var alert = "Room with fresh air and comfortable temperature is available.";
        var noAlert = "Either room is occupied, or working conditions are not right.";
    ```

    f. 移除註解 `// Modify this code block for your sensor` 之後的下列 *if-else* 程式碼區塊：

    ```JavaScript
        // If carbonDioxide less than threshold and no presence in the room => log, notify and set parent space computed value
        if(carbonDioxideValue < carbonDioxideThreshold && !presence) {
            log(`${availableFresh}. Carbon Dioxide: ${carbonDioxideValue}. Presence: ${presence}.`);
            setSpaceValue(parentSpace.Id, spaceAvailFresh, availableFresh);
        }
        else {
            log(`${noAvailableOrFresh}. Carbon Dioxide: ${carbonDioxideValue}. Presence: ${presence}.`);
            setSpaceValue(parentSpace.Id, spaceAvailFresh, noAvailableOrFresh);

            // Set up custom notification for poor air quality
            parentSpace.Notify(JSON.stringify(noAvailableOrFresh));
        }
    ```

    並將它取代為下列 *if-else* 區塊：

    ```JavaScript
        // If sensor values are within range and room is available
        if(carbonDioxideValue < carbonDioxideThreshold && temperatureValue < temperatureThreshold && !presence) {
            log(`${alert}. Carbon Dioxide: ${carbonDioxideValue}. Temperature: ${temperatureValue}. Presence: ${presence}.`);

            // log, notify and set parent space computed value
            setSpaceValue(parentSpace.Id, spaceAvailFresh, alert);

            // Set up notification for this alert
            parentSpace.Notify(JSON.stringify(alert));
        }
        else {
            log(`${noAlert}. Carbon Dioxide: ${carbonDioxideValue}. Temperature: ${temperatureValue}. Presence: ${presence}.`);

            // log, notify and set parent space computed value
            setSpaceValue(parentSpace.Id, spaceAvailFresh, noAlert);
        }
    ```

    已修改的 UDF 會尋找會議室可以使用，而且二氧化碳和溫度在可忍受限制內的狀況。 符合此狀況時，它就會使用 `parentSpace.Notify(JSON.stringify(alert));` 陳述式產生通知。 不論狀況是否符合，它都會以對應的訊息設定受監視空間的值。

    g. 儲存檔案。

1. 開啟命令視窗，然後移至 **occupancy-quickstart\src** 資料夾。 執行下列命令來佈建空間智慧圖表和使用者定義的函式：

    ```cmd/sh
    dotnet run ProvisionSample
    ```

   >[!IMPORTANT]
   > 若要防止未經授權存取您的 Digital Twins 管理 API，**occupancy-quickstart** 應用程式會要求您使用您的 Azure 帳戶認證登入。 它會儲存您的認證一小段時間，因此您不需在每次執行它時登入。 此程式第一次執行時，以及當您儲存的認證之後到期時，該應用程式都會將您導向登入頁面並提供工作階段專用程式碼，以在該頁面上輸入。 依照提示使用您的 Azure 帳戶登入。

1. 您的帳戶經過驗證後，應用程式就會開始建立如 provisionSample.yaml  中所設定的範例空間圖形。 等候佈建完成。 可能需要數分鐘的時間。 之後，觀察命令視窗中的訊息，並且注意空間圖表的建立方式。 請注意，應用程式會在根節點或 `Venue` 建立 IoT 中樞。

1. 從命令視窗中的輸出，將 `ConnectionString` 的值 (位於`Devices` 區段之下) 複製到您的剪貼簿。 您需要此值，以在下一節中模擬裝置連線。

    [![佈建範例](./media/tutorial-facilities-udf/run-provision-sample.png)](./media/tutorial-facilities-udf/run-provision-sample.png#lightbox)

>[!TIP]
> 如果您在佈建過程中，收到類似於「I/O 作業因為執行緒結束或應用程式要求而中止」的錯誤訊息，請再次嘗試執行命令。 如果 HTTP 用戶端因為網路問題而逾時，就可能發生這種情形。

## <a name="simulate-sensor-data"></a>模擬感應器資料

在本節中，您會使用範例中名為 device-connectivity  的專案。 您會模擬用於偵測動作、溫度和二氧化碳的感應器資料。 此專案會為感應器產生隨機值，然後使用裝置連接字串將這些值傳送到 IoT 中樞。

1. 在個別的命令視窗中，移至 Azure Digital Twins 範例，然後移至 device-connectivity  資料夾。

1. 執行此命令，以確定專案的相依性是否正確：

    ```cmd/sh
    dotnet restore
    ```

1. 在您的編輯器中開啟 [appsettings.json](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/device-connectivity/appsettings.json) 檔案，然後編輯下列值：

   a. **DeviceConnectionString**：指派上一節輸出視窗中的 `ConnectionString` 值。 請完整複製這個字串 (在括號內)，讓模擬器可以與 IoT 中樞正確地連線。

   b. **Sensors** 陣列內的 **HardwareId**：因為您要模擬的事件來自已佈建到 Azure Digital Twins 執行個體的感應器，所以此檔案中的硬體識別碼和感應器名稱應該符合 provisionSample.yaml  檔案的 `sensors` 節點。

      為溫度感應器新增項目。 appsettings.json  中的 **Sensors** 節點看起來應該如下所示：

      ```JSON
      "Sensors": [{
        "DataType": "Motion",
        "HardwareId": "SAMPLE_SENSOR_MOTION"
      },{
        "DataType": "CarbonDioxide",
        "HardwareId": "SAMPLE_SENSOR_CARBONDIOXIDE"
      },{
        "DataType": "Temperature",
        "HardwareId": "SAMPLE_SENSOR_TEMPERATURE"
      }]
      ```

1. 執行此命令來開始模擬溫度、移動和二氧化碳的裝置事件：

    ```cmd/sh
    dotnet run
    ```

   >[!NOTE]
   > 模擬範例不會直接與您的 Digital Twins 執行個體通訊，因此不會要求您進行驗證。

## <a name="get-results-of-the-user-defined-function"></a>取得使用者定義函式的結果

每次您的執行個體收到裝置和感應器資料時，就會執行使用者定義的函式。 這一節會查詢您的 Azure Digital Twins 執行個體，以取得使用者定義的函式結果。 當會議室空出來、空氣新鮮且溫度適宜時，您會近乎即時地收到通知。 

1. 開啟您用來佈建範例的命令視窗或新的命令視窗，再次移至範例的 **occupancy-quickstart\src** 資料夾。

1. 執行下列命令並在出現提示時登入：

    ```cmd/sh
    dotnet run GetAvailableAndFreshSpaces
    ```

輸出視窗會顯示使用者定義函式的執行情形，並攔截來自裝置模擬的事件。 

   [![UDF 的輸出](./media/tutorial-facilities-udf/adt-tutorial-udf-running.png)](./media/tutorial-facilities-udf/adt-tutorial-udf-running.png#lightbox)

如果所監視的狀況符合，則使用者定義的函式會以我們在[稍早](#create-a-user-defined-function)看到的相關訊息設定空間的值。 `GetAvailableAndFreshSpaces` 函式會在主控台上列印出訊息。

## <a name="clean-up-resources"></a>清除資源

至此，如果您不想要再探索 Azure Digital Twins，請放心地刪除在本教學課程中建立的資源：

1. 從 [Azure 入口網站](https://portal.azure.com)的左側功能表中，選取 [所有資源]  ，選取您的 Digital Twins 資源群組，然後選取 [刪除]  。

    >[!TIP]
    > 如果您在刪除 Digital Twins 執行個體時遇到問題，已推出的服務更新中具有修正程式。 請重試刪除執行個體。

2. 如有必要，請刪除工作電腦上的應用程式範例。

## <a name="next-steps"></a>後續步驟

您已佈建空間並建立了一套架構來觸發自訂通知，接下來您可以前往下列任一教學課程：

> [!div class="nextstepaction"]
> [教學課程：使用 Logic Apps 接收來自 Azure Digital Twins 空間的通知](tutorial-facilities-events.md)

> [!div class="nextstepaction"]
> [教學課程：使用時間序列深入解析對來自 Azure Digital Twins 空間的事件進行視覺化檢視和分析](tutorial-facilities-analyze.md)
