---
title: 開始使用 Azure IoT 中樞裝置對應項 (Node) | Microsoft Docs
description: 如何使用 Azure IoT 中樞裝置對應項來新增標籤，然後使用 IoT 中樞查詢。 您可以使用適用於 Node.js 的 Azure IoT SDK，實作模擬裝置應用程式和服務應用程式，以新增標籤和執行 IoT 中樞查詢。
author: fsautomata
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 08/26/2019
ms.author: elioda
ms.openlocfilehash: 55dc7f73a3e5bbff2e6e331ba0bd7d4088a86536
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77110841"
---
# <a name="get-started-with-device-twins-nodejs"></a>開始使用設備孿生（Node.js）

[!INCLUDE [iot-hub-selector-twin-get-started](../../includes/iot-hub-selector-twin-get-started.md)]

在本教學課程結尾端，您將會有兩個 Node.js 主控台應用程式：

* **AddTagsAndQuery.js**，這是 Node.js 後端應用程式，可新增標籤和查詢裝置對應項。

* **Twin類比設備.js**，Node.js 應用程式，它類比使用較早創建的設備標識連接到 IoT 中心的設備，並報告其連接條件。

> [!NOTE]
> [Azure IoT SDK](iot-hub-devguide-sdks.md) 一文提供可用來建置裝置和後端應用程式之 Azure IoT SDK 的相關資訊。
>

## <a name="prerequisites"></a>Prerequisites

若要完成本教學課程，您需要：

* Node.js 版本 10.0.x 或更高版本。

* 使用中的 Azure 帳戶。 (如果您沒有帳戶，只需要幾分鐘的時間就可以建立[免費帳戶](https://azure.microsoft.com/pricing/free-trial/)。)

* 請確定您的防火牆已開啟連接埠 8883。 本文中的設備示例使用 MQTT 協定，該協定通過埠 8883 進行通信。 某些公司和教育網路環境可能會封鎖此連接埠。 如需此問題的詳細資訊和解決方法，請參閱[連線至 IoT 中樞 (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub)。

## <a name="create-an-iot-hub"></a>建立 IoT 中樞

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>在 IoT 中樞註冊新的裝置

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="get-the-iot-hub-connection-string"></a>獲取 IoT 中心連接字串

[!INCLUDE [iot-hub-howto-twin-shared-access-policy-text](../../includes/iot-hub-howto-twin-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-custom-connection-string](../../includes/iot-hub-include-find-custom-connection-string.md)]

## <a name="create-the-service-app"></a>建立服務應用程式

在本節中，您將建立一個 Node.js 主控台應用程式，此應用程式會將位置中繼資料新增至與 **myDeviceId** 相關聯的裝置對應項。 接著，它會選取位於美國的裝置來查詢儲存在 IoT 中樞的裝置對應項，再查詢會報告行動電話連線的對應項。

1. 建立稱為 **addtagsandqueryapp** 的新空白資料夾。 在 **addtagsandqueryapp** 資料夾中，於命令提示字元使用下列命令建立新的 package.json 檔案。 參數`--yes`接受所有預設值。

    ```cmd/sh
    npm init --yes
    ```

2. 在命令提示字元下，於 **addtagsandqueryapp** 資料夾中執行下列命令以安裝 **azure-iothub** 套件：

    ```cmd/sh
    npm install azure-iothub --save
    ```

3. 使用文字編輯器，在 **addtagsandqueryapp** 資料夾中建立新的 **AddTagsAndQuery.js** 檔案。

4. 將以下代碼添加到**AddTags 和Query.js**檔。 替換為`{iot hub connection string}`在[獲取 IoT 中心連接字串中複製的 IoT 中心連接字串](#get-the-iot-hub-connection-string)。

   ``` javascript
        'use strict';
        var iothub = require('azure-iothub');
        var connectionString = '{iot hub connection string}';
        var registry = iothub.Registry.fromConnectionString(connectionString);

        registry.getTwin('myDeviceId', function(err, twin){
            if (err) {
                console.error(err.constructor.name + ': ' + err.message);
            } else {
                var patch = {
                    tags: {
                        location: {
                            region: 'US',
                            plant: 'Redmond43'
                      }
                    }
                };

                twin.update(patch, function(err) {
                  if (err) {
                    console.error('Could not update twin: ' + err.constructor.name + ': ' + err.message);
                  } else {
                    console.log(twin.deviceId + ' twin updated successfully');
                    queryTwins();
                  }
                });
            }
        });
   ```

    **Registry** 物件會公開從服務來與裝置對應項進行互動時所需的所有方法。 先前的程式碼會先初始化 **Registry** 物件，然後擷取 **myDeviceId** 的裝置對應項，最後會以所需的位置資訊來更新其標籤。

    更新標籤之後，它會呼叫 **queryTwins** 函式。

5. 在 **AddTagsAndQuery.js** 結尾處新增下列程式碼來實作 **queryTwins** 函式︰

   ```javascript
        var queryTwins = function() {
            var query = registry.createQuery("SELECT * FROM devices WHERE tags.location.plant = 'Redmond43'", 100);
            query.nextAsTwin(function(err, results) {
                if (err) {
                    console.error('Failed to fetch the results: ' + err.message);
                } else {
                    console.log("Devices in Redmond43: " + results.map(function(twin) {return twin.deviceId}).join(','));
                }
            });

            query = registry.createQuery("SELECT * FROM devices WHERE tags.location.plant = 'Redmond43' AND properties.reported.connectivity.type = 'cellular'", 100);
            query.nextAsTwin(function(err, results) {
                if (err) {
                    console.error('Failed to fetch the results: ' + err.message);
                } else {
                    console.log("Devices in Redmond43 using cellular network: " + results.map(function(twin) {return twin.deviceId}).join(','));
                }
            });
        };
   ```

    先前的程式碼會執行兩個查詢︰第一個只選取位於 **Redmond43** 工廠的裝置的裝置對應項，第二個會修改查詢，只選取也透過行動電話網路來連接的裝置。

    當代碼創建**查詢**物件時，它指定第二個參數中返回的文檔的最大數量。 **query** 物件包含 **hasMoreResults** 布林值屬性，可用來多次叫用 **nextAsTwin** 方法以擷取所有結果。 **調用下一個**方法可用於不是設備孿生的結果，例如彙總查詢的結果。

6. 使用下列命令執行應用程式：

    ```cmd/sh
        node AddTagsAndQuery.js
    ```

   如果是查詢所有位於 **Redmond43** 中的裝置，您在結果中會看到一個裝置，而如果查詢將結果限於使用行動電話網路的裝置，則您不會看到任何裝置。

   ![查看查詢結果中的一個設備](media/iot-hub-node-node-twin-getstarted/service1.png)

在下一節，您將建立一個裝置應用程式，以報告連線資訊並變更上一節的查詢結果。

## <a name="create-the-device-app"></a>建立裝置應用程式

在本節中，您將建立一個 Node.js 主控台應用程式，此應用程式會以 **myDeviceId** 來連接到您的中樞，然後更新其裝置對應項所報告的屬性，以包含資訊來指出目前使用行動電話網路來連線。

1. 建立稱為 **reportconnectivity** 的新空白資料夾。 在 **reportconnectivity** 資料夾中，於命令提示字元使用下列命令建立新的 package.json 檔案。 參數`--yes`接受所有預設值。

    ```cmd/sh
    npm init --yes
    ```

2. 在**報表連接**資料夾中的命令提示符下，運行以下命令以安裝**azure-iot 設備**，以及**azure-iot-設備-mqtt**包：

    ```cmd/sh
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```

3. 使用文字編輯器，在 **reportconnectivity** 資料夾中建立新的 **ReportConnectivity.js** 檔案。

4. 將以下代碼添加到**報表連接.js**檔。 在`{device connection string}` [IoT 中心註冊新設備](#register-a-new-device-in-the-iot-hub)時，使用**myDeviceId**您複製的設備連接字串替換。

    ```javascript
        'use strict';
        var Client = require('azure-iot-device').Client;
        var Protocol = require('azure-iot-device-mqtt').Mqtt;

        var connectionString = '{device connection string}';
        var client = Client.fromConnectionString(connectionString, Protocol);

        client.open(function(err) {
        if (err) {
            console.error('could not open IotHub client');
        }  else {
            console.log('client opened');

            client.getTwin(function(err, twin) {
            if (err) {
                console.error('could not get twin');
            } else {
                var patch = {
                    connectivity: {
                        type: 'cellular'
                    }
                };

                twin.properties.reported.update(patch, function(err) {
                    if (err) {
                        console.error('could not update twin');
                    } else {
                        console.log('twin state reported');
                        process.exit();
                    }
                });
            }
            });
        }
        });
    ```

    **Client** 物件會公開從裝置來與裝置對應項進行互動時所需的所有方法。 先前的程式碼在初始化 **Client** 物件之後，會擷取 **myDeviceId** 的裝置對應項，並以連線資訊來更新其報告屬性。

5. 執行裝置應用程式

    ```cmd/sh
        node ReportConnectivity.js
    ```

    您應該會看見訊息 `twin state reported`。

6. 現在，裝置已回報其連線資訊，它應該會出現在這兩個查詢中。 回到 **addtagsandqueryapp** 資料夾，並再次執行查詢︰

    ```cmd/sh
        node AddTagsAndQuery.js
    ```

    這次，**myDeviceId** 應該會出現在這兩個查詢結果中。

    ![在兩個查詢結果中顯示 myDeviceId](media/iot-hub-node-node-twin-getstarted/service2.png)

## <a name="next-steps"></a>後續步驟

在此教學課程中，您在 Azure 入口網站中設定了新的 IoT 中樞，然後在 IoT 中樞的身分識別登錄中建立了裝置身分識別。 您已從後端應用程式將裝置中繼資料新增為標籤，並撰寫模擬裝置應用程式來報告裝置對應項中的裝置連線資訊。 您也了解如何使 類似 SQL 的 IoT 中樞查詢語言來查詢此資訊。

使用下列資源來了解如何：

* 使用[IoT 中心](quickstart-send-telemetry-node.md)教程從設備發送遙測資料，

* 使用裝置對應項所需的屬性來設定裝置，請參閱[使用所需的屬性來設定裝置](tutorial-device-twins.md)教學課程，

* 使用["使用直接方法"](quickstart-control-device-node.md)教程，以對話模式控制設備（例如從使用者控制的應用程式打開風扇）。
