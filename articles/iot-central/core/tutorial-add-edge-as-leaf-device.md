---
title: 將 Azure IoT Edge 裝置新增至 Azure IoT Central | Microsoft Docs
description: 以操作員的身分將 Azure IoT Edge 裝置新增至 Azure IoT Central 應用程式
author: rangv
ms.author: rangv
ms.date: 12/09/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: peterpr
ms.openlocfilehash: bf74784998de6bbad6310c48c24d6353512bff13
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/05/2020
ms.locfileid: "77026391"
---
# <a name="tutorial-add-an-azure-iot-edge-device-to-your-azure-iot-central-application"></a>教學課程：將 Azure IoT Edge 裝置新增至 Azure IoT Central 應用程式

本教學課程說明如何在 Azure IoT Central 應用程式中設定和新增 Azure IoT Edge 裝置。 本教學課程使用來自 Azure Marketplace、已啟用 IoT Edge 的 Linux 虛擬機器 (VM) 來模擬 IoT Edge 裝置。 IoT Edge 裝置會使用可產生模擬環境遙測的模組。 請在 IoT Central 應用程式的儀表板上檢視遙測。

在本教學課程中，您會了解如何：

> [!div class="checklist"]
> * 為 IoT Edge 裝置建立裝置範本
> * 在 IoT Central 中建立 IoT Edge 裝置
> * 將模擬的 IoT Edge 裝置部署到 Linux VM

## <a name="prerequisites"></a>Prerequisites

完成[建立 Azure IoT Central 應用程式](./quick-deploy-iot-central.md)快速入門，以使用 [自訂應用程式] > [自訂應用程式]  範本建立 IoT Central 應用程式。

若要完成本教學課程中的步驟，您需要準備有效的 Azure 訂用帳戶。

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

從 GitHub 下載 IoT Edge 資訊清單檔。 對下列連結按一下滑鼠右鍵，然後選取 [另存連結]  ：[EnvironmentalSensorManifest.json](https://raw.githubusercontent.com/Azure-Samples/iot-central-docs-samples/master/iotedge/EnvironmentalSensorManifest.json)

## <a name="create-device-template"></a>建立裝置範本

在本節中，您會為連線至 IoT Central 應用程式的 IoT Edge 裝置建立裝置範本。 您會匯入要開始使用的 IoT Edge 資訊清單，然後修改範本以新增遙測定義和檢視：

### <a name="import-manifest-to-create-template"></a>匯入資訊清單以建立範本

若要從 IoT Edge 資訊清單建立裝置範本：

1. 在 IoT Central 應用程式中，瀏覽至 [裝置範本]  ，然後選取 [+ 新增]  。

1. 在 [選取範本類型]  頁面上，選取 [Azure IoT Edge]  圖格。 然後，選取 **[下一步：** 自訂]。

1. 在 [上傳 Azure IoT Edge 部署資訊清單]  頁面上，選取 [瀏覽]  ，以上傳您先前下載的 **EnvironmentalSensorManifest.json**。 然後，選取 **下一步：:** 。

1. 在 [檢閱]  頁面上，選取 [建立]  。

1. 建立好範本後，請將其名稱變更為「環境感應器邊緣裝置」  。

1. 在 **SimulatedTemperatureSensor** 模組中選取 [管理]  介面，以檢視資訊清單中定義的兩個屬性：

![從 IoT Edge 資訊清單建立的裝置範本](./media/tutorial-add-edge-as-leaf-device/imported-manifest.png)

### <a name="add-telemetry-to-manifest"></a>在資訊清單中新增遙測

IoT Edge 資訊清單不會定義模組所傳送的遙測。 您必須在裝置範本中新增遙測定義。 **SimulatedTemperatureSensor** 模組會傳送類似下列 JSON 的遙測訊息：

```json
{
    "machine": {
        "temperature": 75.0,
        "pressure": 40.2
    },
    "ambient": {
        "temperature": 23.0,
        "humidity": 30.0
    },
    "timeCreated": ""
}
```

若要在裝置範本中新增遙測定義：

1. 在 [環境感應器邊緣裝置]  範本中選取 [管理]  介面。

1. 選取 [+ 新增功能]  。 輸入「機器」  作為 [顯示名稱]  ，然後確定 [功能類型]  是 [遙測]  。

1. 選取 [物件]  作為 [結構描述類型]，然後選取 [定義]  。 在 [物件定義] 頁面上，新增類型為 [雙精確度]  的 [溫度]  和 [壓力]  屬性，然後選取 [套用]  。

1. 選取 [+ 新增功能]  。 輸入「環境」  作為 [顯示名稱]  ，然後確定 [功能類型]  是 [遙測]  。

1. 選取 [物件]  作為 [結構描述類型]，然後選取 [定義]  。 在 [物件定義] 頁面上，新增類型為 [雙精確度]  的 [溫度]  和 [濕度]  屬性，然後選取 [套用]  。

1. 選取 [+ 新增功能]  。 輸入「建立時間」  作為 [顯示名稱]  ，然後確定 [功能類型]  是 [遙測]  。

1. 選取 [日期時間]  作為結構描述類型。

1. 選取 [儲存]  以更新範本。

[管理]  介面現在包含 [機器]  、[環境]  和 [建立時間]  遙測類型：

![具有 [機器] 和 [環境] 遙測類型的介面](./media/tutorial-add-edge-as-leaf-device/manage-interface.png)

### <a name="add-views-to-template"></a>在範本中新增檢視

裝置範本尚未擁有可讓操作員查看 IoT Edge 裝置所傳來遙測資料的檢視。 若要在裝置範本中新增檢視：

1. 在 [環境感應器邊緣裝置]  範本中選取 [檢視]  。

1. 在 [選取以新增檢視]  頁面上，選取 [將裝置視覺化]  圖格。

1. 將檢視名稱變更為「檢視 IoT Edge 裝置遙測」  。

1. 選取 [環境]  和 [機器]  遙測類型。 然後選取 [新增圖格]  。

1. 選取 [儲存]  以儲存 [檢視 IoT Edge 裝置遙測]  檢視。

![具有遙測檢視的裝置範本](./media/tutorial-add-edge-as-leaf-device/template-telemetry-view.png)

### <a name="publish-the-template"></a>發佈範本

您必須先發佈範本，才能新增使用**環境感應器邊緣裝置**範本的裝置。

瀏覽至 [環境感應器邊緣裝置]  範本，然後選取 [發佈]  。 然後選取 [發佈]  以發佈範本：

![發佈裝置範本](./media/tutorial-add-edge-as-leaf-device/publish-template.png)

## <a name="add-iot-edge-device"></a>新增 IoT Edge 裝置

現在您已發佈**環境感應器邊緣裝置**範本，接著您可以在 IoT Central 應用程式中新增裝置：

1. 在 IoT Central 應用程式中，瀏覽至 [裝置]  頁面，然後在可用範本清單中選取 [環境感應器邊緣裝置]  。

1. 選取 [+]  以從範本新增裝置。 在 [建立新裝置]  頁面上，選取 [建立]  。

現在，您已擁有一個狀態為**已註冊**的新裝置：

![發佈裝置範本](./media/tutorial-add-edge-as-leaf-device/new-device.png)

### <a name="get-the-device-credentials"></a>取得裝置認證

當您稍後在本教學課程中部署 IoT Edge 裝置時，您需要擁有可讓裝置連線至 IoT Central 應用程式的認證。 若要取得裝置認證：

1. 在 [裝置]  頁面上，選取您建立好的裝置。

1. 選取 [連接]  。

1. 在 [裝置連線]  頁面上，記下 [識別碼範圍]  、[裝置識別碼]  和 [主要金鑰]  。 您稍後會用到這些值。

1. 選取 [關閉]  。

您現在已完成 IoT Central 應用程式的設定，而讓 IoT Edge 裝置能夠連線。

## <a name="deploy-an-iot-edge-device"></a>部署 IoT Edge 裝置

在本教學課程中，您會使用已在 Azure 上建立、並已啟用 Azure IoT Edge 的 Linux VM 來模擬 IoT Edge 裝置。 若要建立已啟用 IoT Edge 的 VM：

1. 在 Azure Marketplace 中瀏覽至 [Ubuntu 上的 Azure IoT Edge](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft_iot_edge.iot_edge_vm_ubuntu?tab=Overview)。 然後選取 [立即取得]  。

1. 在 [在 Azure 中建立此應用程式]  頁面上，選取 [繼續]  。 此連結會將您引導至 Azure 入口網站，您可能需要在其中登入 Azure 訂用帳戶。

1. 在 Azure 入口網站中的 [Ubuntu 上的 Azure IoT Edge]  頁面上，選取 [建立]  。

1. 在 [建立虛擬機器 > 基本資料]  頁面上：

    - 選取 Azure 訂用帳戶。
    - 建立名為 **iot-edge-devices** 的新資源群組。
    - 使用虛擬機器名稱：**iotedgevm**。
    - 選擇與您最接近的區域。
    - 將 [驗證類型] 設定為 [密碼]  。
    - 選擇 [使用者名稱] 和 [密碼]。
    - 您可以讓其他選項保持使用預設值。
    - 選取 [檢閱 + 建立]  。

1. 當驗證完成時，選取 [建立]  。

幾分鐘後，當部署完成時，請選取 [移至資源]  。

### <a name="provision-vm-as-an-iot-edge-device"></a>將 VM 佈建為 IoT Edge 裝置 

若要將 VM 佈建為 IoT Edge 裝置：

1. 在 [支援 + 疑難排解]  區段中，選取 [序列主控台]  。

1. 按 **Enter** 以查看 `login:` 提示字元。 輸入使用者名稱和密碼來登入。

1. 執行下列命令來檢查 IoT Edge 執行階段版本。 本文撰寫當下的版本為 1.0.8：

    ```bash
    sudo iotedge --version
    ```

1. 使用 `nano` 編輯器來開啟 IoT Edge 的 config.yaml 檔案：

    ```bash
    sudo nano /etc/iotedge/config.yaml
    ```

1. 向下捲動，直到您看到 `# Manual provisioning configuration` 為止。 將接下來的三行註解化，如下列程式碼片段所示：

    ```yaml
    # Manual provisioning configuration
    #provisioning:
    #  source: "manual"
    #  device_connection_string: "<ADD DEVICE CONNECTION STRING HERE>"
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

1. 將 `{scope_id}` 取代為您先前所記下的 [識別碼範圍]  。

1. 將 `{registration_id}` 取代為您先前所記下的 [裝置識別碼]  。

1. 將 `{symmetric_key}` 取代為您先前所記下的 [主要金鑰]  。

1. 儲存變更 (**Ctrl-O**) 並結束 (**Ctrl-X**) `nano` 編輯器。

1. 執行下列命令以重新啟動 IoT Edge 精靈：

    ```bash
    sudo systemctl restart iotedge
    ```

1. 若要檢查 IoT Edge 模組的狀態，請執行下列命令：

    ```bash
    iotedge list
    ```

    輸出顯示如下：

    ```bash
    NAME                        STATUS           DESCRIPTION      CONFIG
    SimulatedTemperatureSensor  running          Up 20 seconds    mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0
    edgeAgent                   running          Up 27 seconds    mcr.microsoft.com/azureiotedge-agent:1.0
    edgeHub                     running          Up 22 seconds    mcr.microsoft.com/azureiotedge-hub:1.0
    ```

## <a name="view-the-telemetry"></a>檢視遙測資料

模擬的 IoT Edge 裝置現在正在 VM 中執行。 在 IoT Central 應用程式的 [裝置]  頁面上，裝置的狀態現在會是 [已佈建]  ：

![已佈建的裝置](./media/tutorial-add-edge-as-leaf-device/provisioned-device.png)

您可以在 [檢視 IoT Edge 裝置遙測]  頁面上看到遙測：

![裝置遙測](./media/tutorial-add-edge-as-leaf-device/device-telemetry-view.png)

[模組]  頁面會顯示 IoT Edge 模組的狀態：

![裝置遙測](./media/tutorial-add-edge-as-leaf-device/edge-module-status.png)

## <a name="next-steps"></a>後續步驟

您現在已了解如何在 IoT Central 中管理 IoT Edge 裝置，以下是建議的後續步驟：

<!-- Next how-tos in the sequence -->

> [!div class="nextstepaction"]
> [設定透明閘道](../../iot-edge/how-to-create-transparent-gateway.md)
