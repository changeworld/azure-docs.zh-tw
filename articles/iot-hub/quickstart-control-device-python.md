---
title: 快速入門-從 Azure IoT 中樞快速入門 (Python) 來控制裝置 |Microsoft Docs
description: 在此快速入門中，您會執行兩個範例 Python 應用程式。 其中一個應用程式是後端應用程式，可以從遠端控制連線到中樞的裝置。 另一個應用程式則是模擬可以從遠端控制且連線到中樞的裝置。
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: quickstart
ms.custom:
- mvc
- mqtt
- devx-track-python
- devx-track-azurecli
ms.date: 09/14/2020
ms.openlocfilehash: 7ee30398ccfdad3ea7b28f277e45c2224de827e8
ms.sourcegitcommit: a0c1d0d0906585f5fdb2aaabe6f202acf2e22cfc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/21/2021
ms.locfileid: "98624654"
---
# <a name="quickstart-control-a-device-connected-to-an-iot-hub-python"></a>快速入門：控制連線到 IoT 中樞的裝置 (Python)

[!INCLUDE [iot-hub-quickstarts-2-selector](../../includes/iot-hub-quickstarts-2-selector.md)]

在此快速入門中，您可以使用直接方法來控制連線到 IoT 中樞的模擬裝置。 IoT 中樞是一項 Azure 服務，可讓您從雲端管理您的 IoT 裝置，並將大量的裝置遙測內嵌到雲端進行儲存或處理。 您可以使用直接方法，針對連線到 IoT 中樞的裝置，從遠端變更裝置的行為。 本快速入門使用兩個 Python 應用程式：可回應從後端應用程式所呼叫直接方法的模擬裝置應用程式，以及在模擬裝置上呼叫直接方法的後端應用程式。

## <a name="prerequisites"></a>Prerequisites

* 具有有效訂用帳戶的 Azure 帳戶。 [建立免費帳戶](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。

* [Python 3.7+](https://www.python.org/downloads/)。 如需其他支援的 Python 版本，請參閱 [Azure IoT 裝置功能](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device#azure-iot-device-features)。

* [範例 Python 專案](https://github.com/Azure-Samples/azure-iot-samples-python/archive/master.zip)。

* 在您的防火牆中開啟的連接埠 8883。 本快速入門中的裝置範例會使用 MQTT 通訊協定，其會透過連接埠 8883 進行通訊。 某些公司和教育網路環境可能會封鎖此連接埠。 如需此問題的詳細資訊和解決方法，請參閱[連線至 IoT 中樞 (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub)。

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

## <a name="create-an-iot-hub"></a>建立 IoT 中樞

如果您已完成先前的[快速入門：將遙測從裝置傳送到 IoT 中樞](quickstart-send-telemetry-python.md)，則可以略過此步驟。

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>註冊裝置

如果您已完成先前的[快速入門：將遙測從裝置傳送到 IoT 中樞](quickstart-send-telemetry-python.md)，則可以略過此步驟。

裝置必須向的 IoT 中樞註冊，才能進行連線。 在本快速入門中，您會使用 Azure Cloud Shell 來註冊模擬的裝置。

1. 在 Azure Cloud Shell 中執行下列命令，以建立裝置身分識別。

    **YourIoTHubName**：以您為 IoT 中樞選擇的名稱取代此預留位置。

    **MyPythonDevice**：這是您要註冊之裝置的名稱。 建議您使用 **MyPythonDevice**，如下所示。 如果您為裝置選擇不同的名稱，則也必須在本文中使用該名稱，並先在範例應用程式中更新該裝置名稱，再執行應用程式。

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id MyPythonDevice
    ```

2. 在 Azure Cloud Shell 中執行下列命令，以針對您剛註冊的裝置取得 _裝置連接字串_：

    **YourIoTHubName**：以您為 IoT 中樞選擇的名稱取代此預留位置。

    ```azurecli-interactive
    az iot hub device-identity connection-string show --hub-name {YourIoTHubName} --device-id MyPythonDevice --output table
    ```

    記下裝置連接字串，它看起來如下：

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyNodeDevice;SharedAccessKey={YourSharedAccessKey}`

    您稍後會在快速入門中使用此值。

3. 您也需要 _服務連接字串_，讓後端應用程式能夠連線到您的 IoT 中樞並擷取訊息。 下列命令可擷取 IoT 中樞的服務連接字串：

    **YourIoTHubName**：以您為 IoT 中樞選擇的名稱取代此預留位置。

    ```azurecli-interactive
    az iot hub show-connection-string \
      --policy-name service \
      --name {YourIoTHubName} \
      --output table
    ```

    記下服務連接字串，它看起來如下：

   `HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}`

    您稍後會在快速入門中使用此值。 服務連接字符串與您在上一個步驟中記下的裝置連接字串不同。

## <a name="listen-for-direct-method-calls"></a>接聽直接方法呼叫

模擬裝置應用程式會連線到 IoT 中樞上的特定裝置端點、傳送模擬的遙測，並接聽來自中樞的直接方法呼叫。 在此快速入門中，來自中樞的直接方法呼叫會告知裝置變更其傳送遙測的間隔。 模擬的裝置在執行直接方法後，會將通知傳送回您的中樞。

1. 在本機終端機視窗中，瀏覽至範例 Python 專案的根資料夾。 然後瀏覽至 **iot-hub\Quickstarts\simulated-device-2** 資料夾。

1. 在您選擇的文字編輯器中開啟 **SimulatedDevice.py** 檔案。

    使用您稍早所記錄的裝置連接字串來取代 `CONNECTION_STRING` 變數的值。 然後將變更儲存到 **SimulatedDevice.py** 檔案。

1. 在本機終端機視窗中，執行下列命令以安裝模擬裝置應用程式所需的程式庫：

    ```cmd/sh
    pip install azure-iot-device
    ```

1. 在本機終端機視窗中，執行下列命令以執行模擬裝置應用程式：

    ```cmd/sh
    python SimulatedDevice.py
    ```

    下列螢幕擷取畫面顯示模擬裝置應用程式將遙測傳送到 IoT 中樞時的輸出：

    ![執行模擬的裝置](./media/quickstart-control-device-python/simulated-device-1.png)

## <a name="call-the-direct-method"></a>呼叫直接方法

後端應用程式會連線到 IoT 中樞上的服務端端點。 應用程式會透過您的 IoT 中樞對裝置進行直接方法呼叫，並接聽通知。 IoT 中樞後端應用程式通常會在雲端中執行。

1. 在另一個本機終端機視窗中，瀏覽至範例 Python 專案的根資料夾。 然後瀏覽至 **iot-hub\Quickstarts\back-end-application** 資料夾。

1. 在您選擇的文字編輯器中開啟 **BackEndApplication.py** 檔案。

    使用稍早所記錄的服務連接字串來取代 `CONNECTION_STRING` 變數的值。 然後將您的變更儲存到 **BackEndApplication.py**。

1. 在本機終端機視窗中，執行下列命令以安裝模擬裝置應用程式所需的程式庫：

    ```cmd/sh
    pip install azure-iot-hub
    ```

1. 在本機終端機視窗中，執行下列命令以執行後端應用程式：

    ```cmd/sh
    python BackEndApplication.py
    ```

    下列螢幕擷取畫面顯示應用程式對裝置進行直接方法呼叫並接收通知時的輸出：

    ![執行後端應用程式](./media/quickstart-control-device-python/backend-application.png)

    執行後端應用程式之後，在執行模擬裝置的主控台視窗中將會出現一則訊息，且它傳送訊息的速率也會變更：

    ![模擬用戶端的變更](./media/quickstart-control-device-python/simulated-device-2.png)

## <a name="clean-up-resources"></a>清除資源

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>後續步驟

在此快速入門中，您從後端應用程式對裝置進行直接方法呼叫，並在模擬裝置應用程式中回應直接方法呼叫。

若要了解如何將「裝置到雲端」訊息路由傳送至雲端中的不同目的地，請繼續下一個教學課程。

> [!div class="nextstepaction"]
> [教學課程：將遙測路由傳送至不同的端點以進行處理](tutorial-routing.md)
