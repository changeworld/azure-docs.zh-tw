---
title: 建立和測試 IoT 隨插即用預覽版裝置 | Microsoft Docs
description: 身為裝置開發人員，請了解如何使用 VS Code 來建立及測試 IoT 隨插即用預覽版裝置的新裝置功能模型。
author: dominicbetts
ms.author: dobett
ms.date: 12/30/2019
ms.topic: tutorial
ms.custom: mvc
ms.service: iot-pnp
services: iot-pnp
manager: philmea
ms.openlocfilehash: 720b3e56e1dd45bd2940b337adefa6ebdaa2e5a1
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/24/2020
ms.locfileid: "76719716"
---
# <a name="tutorial-create-and-test-a-device-capability-model-using-visual-studio-code"></a>教學課程：使用 Visual Studio Code 建立和測試裝置功能模型

本教學課程說明如何以裝置開發人員的身分使用 Visual Studio Code 建立_裝置功能模型_。 您可以使用此模型產生基本架構程式碼，以在連線至雲端中的 Azure IoT 中樞執行個體的裝置上執行。

本教學課程中的相關章節在說明如何建置產生的基本架構程式碼時，會假設您使用的是 Windows。

在本教學課程中，您會了解如何：

> [!div class="checklist"]
> * 建立裝置功能模型
> * 從模型產生基本架構裝置程式碼
> * 在產生的程式碼中實作 Stub
> * 執行程式碼以測試與 IoT 中樞的互動

## <a name="prerequisites"></a>Prerequisites

若要使用本教學課程中的裝置功能模型，您需要：

* [Visual Studio Code](https://code.visualstudio.com/download)：VS Code 適用於多個平台
* [適用於 VS Code 的 Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) \(英文\) 擴充套件。 請使用下列步驟，在 VS Code 中安裝擴充套件：

    1. 在 VS Code 中，選取 [延伸模組]  索引標籤。
    1. 搜尋 **Azure IoT Tools**。
    1. 選取 [安裝]  。

在本教學課程中，若要在 Windows 上建置產生的 C 程式碼，您需要：

* 具有 **C++ 建置工具**和 **NuGet 套件管理員元件**工作負載的 [Visual Studio 建置工具](https://visualstudio.microsoft.com/thank-you-downloading-visual-studio/?sku=BuildTools&rel=16)。 或者如果您已安裝具有相同工作負載的 [Visual Studio (Community、Professional 或 Enterprise)](https://visualstudio.microsoft.com/downloads/) 2019、2017 或 2015。
* [Git](https://git-scm.com/download)
* [CMake](https://cmake.org/download/)

若要在本教學課程中測試您的裝置程式碼，您需要：

* [Azure IoT 檔案總管](https://github.com/Azure/azure-iot-explorer/releases)。
* Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="model-your-device"></a>建立裝置模型

您可以使用_數位對應項定義語言_來建立裝置功能模型。 模型通常由多個_介面_定義檔和單一模型檔案所組成。 **適用於 VS Code 的 Azure IoT Tools** 包含可協助您建立和編輯這些 JSON 檔案的工具。

### <a name="create-the-interface-file"></a>建立介面檔案

若要建立介面檔案，以在 VS Code 中定義 IoT 裝置的功能：

1. 建立名為 **devicemodel** 的資料夾。

1. 啟動 VS Code，然後使用 **Ctrl+Shift+P** 開啟命令選擇區。

1. 輸入**隨插即用**，然後選取 **[IoT 隨插即用：建立介面]** 命令。

1. 瀏覽至您建立的 **devicemodel** 資料夾，並加以選取。

1. 接著，輸入 **EnvironmentalSensor** 作為介面的名稱，然後按 **Enter** 鍵。 VS Code 會建立名為 **EnvironmentalSensor.interface.json** 的範例介面檔案。

1. 將此檔案的內容取代為下列 JSON，並將 `@id` 欄位中的 `{your name}` 取代為唯一值。 您只能使用字元 a-z、A-Z、0-9 和底線。 如需詳細資訊，請參閱[數位對應項識別碼格式](https://github.com/Azure/IoTPlugandPlay/tree/master/DTDL#digital-twin-identifier-format)。 介面識別碼必須是唯一的，才能將介面儲存在存放庫中：

    ```json
    {
      "@id": "urn:{your name}:EnvironmentalSensor:1",
      "@type": "Interface",
      "displayName": "Environmental Sensor",
      "description": "Provides functionality to report temperature, humidity. Provides telemetry, commands and read-write properties",
      "comment": "Requires temperature and humidity sensors.",
      "contents": [
        {
          "@type": "Property",
          "displayName": "Device State",
          "description": "The state of the device. Two states online/offline are available.",
          "name": "state",
          "schema": "boolean"
        },
        {
          "@type": "Property",
          "displayName": "Customer Name",
          "description": "The name of the customer currently operating the device.",
          "name": "name",
          "schema": "string",
          "writable": true
        },
        {
          "@type": "Property",
          "displayName": "Brightness Level",
          "description": "The brightness level for the light on the device. Can be specified as 1 (high), 2 (medium), 3 (low)",
          "name": "brightness",
          "writable": true,
          "schema": "long"
        },
        {
          "@type": [
            "Telemetry",
            "SemanticType/Temperature"
          ],
          "description": "Current temperature on the device",
          "displayName": "Temperature",
          "name": "temp",
          "schema": "double",
          "unit": "Units/Temperature/fahrenheit"
        },
        {
          "@type": [
            "Telemetry",
            "SemanticType/Humidity"
          ],
          "description": "Current humidity on the device",
          "displayName": "Humidity",
          "name": "humid",
          "schema": "double",
          "unit": "Units/Humidity/percent"
        },
        {
          "@type": "Telemetry",
          "name": "magnetometer",
          "displayName": "Magnetometer",
          "comment": "This shows a complex telemetry that contains a magnetometer reading.",
          "schema": {
            "@type": "Object",
            "fields": [
              {
                "name": "x",
                "schema": "integer"
              },
              {
                "name": "y",
                "schema": "integer"
              },
              {
                "name": "z",
                "schema": "integer"
              }
            ]
          }
        },
        {
          "@type": "Command",
          "name": "turnon",
          "response": {
            "name": "turnon",
            "schema": "string"
          },
          "comment": "This Commands will turn-on the LED light on the device.",
          "commandType": "synchronous"
        },
        {
          "@type": "Command",
          "name": "turnoff",
          "comment": "This Commands will turn-off the LED light on the device.",
          "response": {
            "name": "turnoff",
            "schema": "string"
          },
          "commandType": "synchronous"
        }
      ],
      "@context": "http://azureiot.com/v1/contexts/IoTModel.json"
    }
    ```

    此介面會定義裝置屬性 (例如**客戶名稱**)、遙測類型 (例如**溫度**) 和命令 (例如 **turnon**)。

1. 在此介面檔案的結尾處新增名為 **blink** 的命令功能。 新增命令之前，請務必先加上逗號。 請嘗試輸入定義，以了解 IntelliSense、自動完成和驗證如何協助您編輯介面定義：

    ```json
    {
      "@type": "Command",
      "description": "This command will begin blinking the LED for given time interval.",
      "name": "blink",
      "request": {
        "name": "blinkRequest",
        "schema": {
          "@type": "Object",
          "fields": [
            {
              "name": "interval",
              "schema": "long"
            }
          ]
        }
      },
      "response": {
        "name": "blinkResponse",
        "schema": "string"
      },
      "commandType": "synchronous"
    }
    ```

1. 儲存檔案。

### <a name="create-the-model-file"></a>建立模型檔案

模型檔案會指定您的 IoT 隨插即用裝置所實作的介面。 模型中通常至少有兩個介面 - 一或多個介面定義裝置的特定功能，和所有 IoT 隨插即用裝置都必須實作的一個標準介面。

若要建立模型檔案，以指定您的 IoT 隨插即用裝置在 VS Code 中實作的介面：

1. 使用 **Ctrl+Shift+P** 開啟命令選擇區。

1. 輸入**隨插即用**，然後選取 **[IoT 隨插即用：建立功能模型]** 命令。 然後，輸入 **SensorboxModel** 作為模型的名稱。 VS Code 會建立名為 **SensorboxModel.capabilitymodel.json** 的範例介面檔案。

1. 將此檔案的內容取代為下列 JSON，並將`@id` 欄位和 `EnvironmentalSensor` 介面中的 `{your name}` 取代為您在 **EnvironmentalSensor.interface.json** 檔案中使用的相同值。 介面識別碼必須是唯一的，才能將介面儲存在存放庫中：

    ```json
    {
      "@id": "urn:{your name}:SensorboxModel:1",
      "@type": "CapabilityModel",
      "displayName": "Environmental Sensorbox Model",
      "implements": [
        {
          "schema": "urn:{your name}:EnvironmentalSensor:1",
          "name": "environmentalSensor"
        },
        {
          "schema": "urn:azureiot:DeviceManagement:DeviceInformation:1",
          "name": "deviceinfo"
        }
      ],
      "@context": "http://azureiot.com/v1/contexts/IoTModel.json"
    }
    ```

    此模型會定義可實作 **EnvironmentalSensor** 介面和標準 **DeviceInformation** 介面的裝置。

1. 儲存檔案。

### <a name="download-the-deviceinformation-interface"></a>下載 DeviceInformation 介面

您必須先從*公用模型存放庫*建立 **DeviceInformation** 的本機複本，才能從模型產生基本架構程式碼。 公用模型存放庫已包含 **DeviceInformation** 介面。

若要使用 VS Code 從公用模型存放庫下載 **DeviceInformation** 介面：

1. 使用 **Ctrl+Shift+P** 開啟命令選擇區。

1. 輸入**隨插即用**，選取 [開啟模型存放庫]  命令，然後選取 [開啟公用模型存放庫]  。

1. 選取 [介面]  ，然後選取識別碼為 `urn:azureiot:DeviceManagement:DeviceInformation:1` 的裝置資訊介面，然後選取 [下載]  。

您現在有三個組成裝置功能模型的檔案：

* urn_azureiot_DeviceManagement_DeviceInformation_1.interface.json
* EnvironmentalSensor.interface.json
* SensorboxModel.capabilitymodel.json

## <a name="publish-the-model"></a>發行模型

若要讓 Azure IoT 檔案總管工具讀取您的裝置功能模型，您必須將其發佈至公司存放庫。 若要從 VS Code 發佈，您需要公司存放庫的連接字串：

1. 瀏覽至 [Azure IoT 認證入口網站](https://aka.ms/ACFI)。

1. 使用您的 Microsoft _工作帳戶_登入此入口網站。

1. 依序選取 [公司存放庫]  和 [連接字串]  。

1. 複製連接字串。

若要在 VS Code 中開啟您的公司存放庫：

1. 使用 **Ctrl+Shift+P** 開啟命令選擇區。

1. 輸入**隨插即用**，然後選取 **[IoT 隨插即用：開啟模型存放庫]** 命令。

1. 選取 [開啟組織模型存放庫]  ，並貼入您的連接字串。

1. 按 **Enter** 鍵以開啟您的公司存放庫。

若要將您的裝置功能模型和介面發佈至公司存放庫：

1. 使用 **Ctrl+Shift+P** 開啟命令選擇區。

1. 輸入**隨插即用**，然後選取 **[IoT 隨插即用：將檔案提交至模型存放庫]** 命令。

1. 選取 **EnvironmentalSensor.interface.json** 和 **SensorboxModel.capabilitymodel.json** 檔案，然後選取 [確定]  。

您的檔案現在會儲存在您的公司存放庫中。

## <a name="generate-code"></a>產生程式碼

您可以使用**適用於 VS Code 的 Azure IoT Tools**，從您的模型產生基本架構 C 程式碼。 若要在 VS Code 中產生基本架構程式碼：

1. 使用 **Ctrl+Shift+P** 開啟命令選擇區。

1. 輸入**隨插即用**，然後選取 **[IoT 隨插即用：產生裝置程式碼 Stub]** 命令。

1. 選取您的 **SensorboxModel.capabilitymodel.json** 功能模型檔案。

1. 輸入**sensorbox_app** 作為專案名稱。

1. 選擇 [ANSI C]  作為語言。

1. 選擇 [透過 IoT 中樞裝置連接字串]  作為連線的方式。

1. 選擇 [Windows 上的 CMake 專案]  作為專案範本。

1. 選擇 [透過 Vcpkg]  作為包含裝置 SDK 的方式。

VS Code 會產生基本架構 C 程式碼，並將檔案儲存在 **modelcode** 資料夾的 **sensorbox_app** 資料夾中。 VS Code 會開啟新視窗，其中包含產生的程式碼檔案。

## <a name="update-the-generated-code"></a>更新產生的程式碼

您必須先實作 Stub 屬性、遙測和命令，才能建置和執行程式碼。

若要在 VS Code 中提供 Stub 程式碼的實作：

1. 開啟 **SensorboxModel_impl.c** 檔案。

1. 新增用來實作 Stub 函式的程式碼。

1. 儲存您的變更。

## <a name="build-the-code"></a>建置程式碼

在您執行程式碼以使用 Azure IoT 中樞來測試 IoT 隨插即用裝置之前，您必須先編譯程式碼。

依照 **sensorbox_app** 資料夾中的 **Readme.md** 檔案提供的指示，在Windows 上建置並執行程式碼。 下一節包含的指示可讓您在執行裝置程式碼時，擷取要使用的裝置連接字串。

## <a name="test-the-code"></a>測試程式碼

當您執行程式碼時，它會連線至 IoT 中樞，並開始傳送範例遙測資料和屬性值。 裝置也會回應從 IoT 中樞傳送的命令。 若要驗證此行為：

1. 若要建立 IoT 中樞：

    ```azurecli-interactive
    az group create --name environmentalsensorresources --location centralus
    az iot hub create --name {your iot hub name} \
      --resource-group environmentalsensorresources --sku F1
    ```

1. 將裝置新增至 IoT 中樞，並擷取其連接字串：

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {your iot hub name} --device-id MyPnPDevice
    az iot hub device-identity show-connection-string --hub-name {your iot hub name} --device-id MyPnPDevice --output table
    ```

    記下連接字串的名稱。

1. 在命令提示字元中，瀏覽至您的 SDK 和範例建置所在的 **azure-iot-sdk-c** 資料夾。 然後，瀏覽至 **cmake\\sensorbox_app\\Release** 資料夾。

1. 執行以下命令：

    ```cmd
    sensorbox_app.exe {your device connection string}
    ```

1. 使用 Azure IoT 檔案總管工具，與連線至 IoT 中樞的 IoT 隨插即用裝置進行互動。 如需詳細資訊，請參閱[安裝和使用 Azure IoT 檔案總管](./howto-install-iot-explorer.md)。

## <a name="next-steps"></a>後續步驟

您已建置可供認證的 IoT 隨插即用裝置，接下來請了解如何：

> [!div class="nextstepaction"]
> [建置可供認證的裝置](tutorial-build-device-certification.md)
