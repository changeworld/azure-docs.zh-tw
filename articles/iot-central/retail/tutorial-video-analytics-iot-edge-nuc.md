---
title: 教學課程 - 在 Azure IoT Central 中建立影片分析 IoT Edge 執行個體 (Intel NUC)
description: 本教學課程說明如何建立影片分析 IoT Edge 執行個體，以搭配影片分析 - 物件和動作偵測應用程式範本使用。
services: iot-central
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: tutorial
ms.author: nandab
author: KishorIoT
ms.date: 07/27/2020
ms.openlocfilehash: 4ecce689e287673a3b08f8f90f87c28e021106d6
ms.sourcegitcommit: bfeae16fa5db56c1ec1fe75e0597d8194522b396
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/10/2020
ms.locfileid: "88037837"
---
# <a name="tutorial-create-an-iot-edge-instance-for-video-analytics-intel-nuc"></a>教學課程：建立適用於影片分析的 IoT Edge 執行個體 (Intel NUC)

Azure IoT Edge 是完全受控的服務，可透過部署和執行下列項目，在本機提供雲端智慧：

* 自訂邏輯
* Azure 服務
* 人工智慧

在 IoT Edge 中，這些服務會直接在跨平台 IoT 裝置上執行，讓您在雲端或離線時，安全且大規模地執行 IoT 解決方案。

本教學課程說明如何在 Intel NUC 裝置上安裝和設定 IoT Edge 執行階段。

在本教學課程中，您會了解如何：
> [!div class="checklist"]
> * 更新和設定 IoT Edge
> * 設定 IoT Edge 閘道
> * 將與本機 ONVIF 相容的相機連結到您的 Intel NUC 裝置

## <a name="prerequisites"></a>先決條件

* 開始之前，您應該先完成先前的[在 Azure IoT Central 中建立即時影片分析應用程式](./tutorial-video-analytics-create-app.md)教學課程。
* 執行 Linux 的裝置，且可執行 Docker 容器並具有足夠處理能力來執行影片分析，例如 Intel NUC。
* [裝置上已安裝並執行 IoT Edge 執行階段](../../iot-edge/how-to-install-iot-edge-linux.md)。
* 若要從您的 Windows 電腦連線到 IoT Edge 裝置，您需要 [PuTTY SSH 用戶端](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html)或同等的公用程式。
* 您也需要 Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，則可以在 [Azure 註冊頁面](https://aka.ms/createazuresubscription)上免費建立一個。

## <a name="configure-the-iot-edge-device"></a>設定 IoT Edge 裝置

如果您未在 Intel NUC 機器上安裝 IoT Edge 執行階段，請參閱[在以 Debian 為基礎的 Linux 系統上安裝 Azure IoT Edge 執行階段](../../iot-edge/how-to-install-iot-edge-linux.md)的指示。

若要更新 IoT Edge 執行階段：

1. 使用 PuTTY 公用程式連線到 IoT Edge 裝置。

1. 執行下列命令來更新及檢查 IoT Edge 執行階段版本。 在撰寫本文時，版本為1.0.9：

    ```bash
    sudo apt-get update
    sudo apt-get install libiothsm iotedge
    sudo iotedge --version
    ```

1. 使用下列命令建立部署搭配所需權限使用的的資料夾：

    ```bash
    sudo mkdir -p /data/storage
    sudo mkdir -p /data/media
    sudo chmod -R 777 /data
    ```

若要將 state.json 設定檔新增至 IoT Edge 裝置上的 /data/storage 資料夾：

1. 使用文字編輯器，在本機電腦上的 lva-configuration 資料夾中開啟 state.json 檔案。

1. 以描述閘道裝置的字串值更新 `system` 和 `iotCentral > properties` 預留位置。 您稍後可以在 IoT Central 應用程式儀表板中檢視這些值。

1. 將 `iotCentral > appKeys` 預留位置取代為您在先前教學課程中，從 scratchpad.txt 檔案中所記下的值。 儲存變更。

1. 在命令提示字元中，使用 PuTTY `scp` 公用程式將您剛才編輯的 state.json 檔案複製到 IoT Edge 裝置上的 /data/storage 資料夾。 此範例會使用 `192.168.0.144` 做為範例 IP 位址，並將其取代為您 IoT Edge 裝置的 IP 位址：

    ```cmd
    scp state.json YourUserName@192.168.0.144:/data/storage/state.json`
    ```

設定 IoT Edge 以註冊並連線至您的 IoT Central 應用程式：

1. 使用 PuTTY 公用程式連線到 IoT Edge 裝置。

1. 使用文字編輯器 (例如 `nano`) 來開啟 IoT Edge config.yaml 檔案。

    ```bash
    sudo nano /etc/iotedge/config.yaml
    ```

    > [!WARNING]
    > YAML 檔案無法使用索引標籤進行縮排，請改用兩個空格。 最上層項目的開頭不能是空白。

1. 向下捲動，直到您看到 `# Manual provisioning configuration` 為止。 將接下來的三行註解化，如下列程式碼片段所示：

    ```yaml
    # Manual provisioning configuration
    #provisioning:
    #  source: "manual"
    #  device_connection_string: "temp"
    ```

1. 向下捲動，直到您看到 `# DPS symmetric key provisioning configuration` 為止。 將接下來的八行取消批註，如下列程式碼片段所示：

    ```yaml
    # DPS symmetric key provisioning configuration
    provisioning:
      source: "dps"
      global_endpoint: "https://global.azure-devices-provisioning.net"
      scope_id: "{scope_id}"
      attestation:
        method: "symmetric_key"
        registration_id: "{registration_id}"
        symmetric_key: "{symmetric_key}"
    ```

1. 將 `{scope_id}` 取代為上一個教學課程中，您從 scratchpad.txt 檔案中記下的**識別碼範圍**。

1. 將 `{registration_id}` 取代為 lva-gateway-001，也就是您在上一個教學課程中建立的裝置。

1. 將 `{symmetric_key}` 取代為上一個教學課程中，您從 scratchpad.txt 檔案中記下的 **lva-gateway-001** 裝置**主要金鑰**。

1. 執行下列命令以重新啟動 IoT Edge 精靈：

    ```bash
    sudo systemctl restart iotedge
    ```

1. 若要檢查 IoT Edge 模組的狀態，請執行下列命令：

    ```bash
    iotedge list
    ```

    先前命令的輸出會顯示五個執行中的模組。 您也可以在 IoT Central 應用程式中，檢視執行中模組的狀態。

    > [!TIP]
    > 您可以重新執行此命令來檢查狀態。 您可能需要等候所有模組開始執行。

如果 IoT Edge 模組無法正確啟動，請參閱[針對 IoT Edge 裝置進行疑難排解](../../iot-edge/troubleshoot.md)。

## <a name="collect-the-rstp-stream-from-your-camera"></a>從您的相機收集 RSTP 串流

針對連線到您 IoT Edge 裝置的相機，識別其 RTSP 串流 URL，例如：

`rtsp://192.168.1.64:554/Streaming/Channels/101/`

> [!TIP]
> 嘗試使用媒體撥放器 (例如 VLC) 來檢視 IoT Edge 電腦上的相機串流。

## <a name="next-steps"></a>後續步驟

您現在已將 IoT Edge 執行階段和 LVA 模組部署至 Intel NUC 閘道裝置。

若要管理相機，請遵循下一個教學課程：

> [!div class="nextstepaction"]
> [監視和管理 [影片分析 - 物件和動作偵測] 應用程式](./tutorial-video-analytics-manage.md)
