---
title: 將 Azure IoT Edge 裝置新增至 Azure IoT Central | Microsoft Docs
description: 以操作員的身分將 Azure IoT Edge 裝置新增至 Azure IoT Central 應用程式
author: rangv
ms.author: rangv
ms.date: 05/29/2020
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
ms.openlocfilehash: 1b90364bee42b31843ac8d84f5a692a3eeb6d3f1
ms.sourcegitcommit: 8e5b4e2207daee21a60e6581528401a96bfd3184
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/04/2020
ms.locfileid: "84417528"
---
# <a name="tutorial-add-an-azure-iot-edge-device-to-your-azure-iot-central-application"></a>教學課程：將 Azure IoT Edge 裝置新增至 Azure IoT Central 應用程式

*本文適用於操作員、解決方案建置人員和裝置開發人員。*

本教學課程說明如何在 Azure IoT Central 應用程式中設定和新增 Azure IoT Edge 裝置。 本教學課程採用支援 IoT Edge 的 Linux 虛擬機器 (VM) 來模擬 IoT Edge 裝置。 IoT Edge 裝置會使用可產生模擬環境遙測的模組。 請在 IoT Central 應用程式的儀表板上檢視遙測。

在本教學課程中，您會了解如何：

> [!div class="checklist"]
> * 為 IoT Edge 裝置建立裝置範本
> * 在 IoT Central 中建立 IoT Edge 裝置
> * 將模擬的 IoT Edge 裝置部署到 Linux VM

## <a name="prerequisites"></a>必要條件

完成[建立 Azure IoT Central 應用程式](./quick-deploy-iot-central.md)快速入門，以使用 [自訂應用程式] > [自訂應用程式] 範本建立 IoT Central 應用程式。

若要完成本教學課程中的步驟，您需要準備有效的 Azure 訂用帳戶。

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

從 GitHub 下載 IoT Edge 資訊清單檔。 對下列連結按一下滑鼠右鍵，然後選取 [另存連結]：[EnvironmentalSensorManifest.json](https://raw.githubusercontent.com/Azure-Samples/iot-central-docs-samples/master/iotedge/EnvironmentalSensorManifest.json)

## <a name="create-device-template"></a>建立裝置範本

在本節中，您會建立 IoT Edge 裝置的 IoT Central 裝置範本。 您會匯入要開始使用的 IoT Edge 資訊清單，然後修改範本以新增遙測定義和檢視：

### <a name="import-manifest-to-create-template"></a>匯入資訊清單以建立範本

若要從 IoT Edge 資訊清單建立裝置範本：

1. 在 IoT Central 應用程式中，瀏覽至 [裝置範本]，然後選取 [+ 新增]。

1. 在 [選取範本類型] 頁面上，選取 [Azure IoT Edge] 圖格。 然後，選取 **[下一步：** 自訂]。

1. 在 [上傳 Azure IoT Edge 部署資訊清單] 頁面上，輸入*環境感應器邊緣裝置*做為裝置範本名稱。 然後選取 [瀏覽] 上傳您先前下載的 **EnvironmentalSensorManifest.json**。 然後，選取 **下一步：:** 。

1. 在 [檢閱] 頁面上，選取 [建立]。

1. 在 **SimulatedTemperatureSensor** 模組中選取 [管理] 介面，以檢視資訊清單中定義的兩個屬性：

:::image type="content" source="media/tutorial-add-edge-as-leaf-device/imported-manifest.png" alt-text="從 IoT Edge 資訊清單建立的裝置範本":::

### <a name="add-telemetry-to-manifest"></a>在資訊清單中新增遙測

IoT Edge 資訊清單不會定義模組所傳送的遙測。 在 IoT Central 中，您會將遙測定義新增至裝置範本。 **SimulatedTemperatureSensor** 模組會傳送類似下列 JSON 的遙測訊息：

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

1. 在 [環境感應器邊緣裝置] 範本中選取 [管理] 介面。

1. 選取 [+ 新增功能]。 輸入「機器」作為 [顯示名稱]，然後確定 [功能類型] 是 [遙測]。

1. 選取 [物件] 作為 [結構描述類型]，然後選取 [定義]。 在 [物件定義] 頁面上，新增類型為 [雙精確度] 的 [溫度] 和 [壓力] 屬性，然後選取 [套用]。

1. 選取 [+ 新增功能]。 輸入「環境」作為 [顯示名稱]，然後確定 [功能類型] 是 [遙測]。

1. 選取 [物件] 作為 [結構描述類型]，然後選取 [定義]。 在 [物件定義] 頁面上，新增類型為 [雙精確度] 的 [溫度] 和 [濕度] 屬性，然後選取 [套用]。

1. 選取 [+ 新增功能]。 輸入「建立時間」作為 [顯示名稱]，然後確定 [功能類型] 是 [遙測]。

1. 選取 [日期時間] 作為結構描述類型。

1. 選取 [儲存] 以更新範本。

[管理] 介面現在包含 [機器]、[環境] 和 [建立時間] 遙測類型：

:::image type="content" source="media/tutorial-add-edge-as-leaf-device/manage-interface.png" alt-text="具有機器和環境遙測類型的介面":::

### <a name="add-views-to-template"></a>在範本中新增檢視

裝置範本尚未擁有可讓操作員查看 IoT Edge 裝置所傳來遙測資料的檢視。 若要在裝置範本中新增檢視：

1. 在 [環境感應器邊緣裝置] 範本中選取 [檢視]。

1. 在 [選取以新增檢視] 頁面上，選取 [將裝置視覺化] 圖格。

1. 將檢視名稱變更為「檢視 IoT Edge 裝置遙測」。

1. 選取 [環境] 和 [機器] 遙測類型。 然後選取 [新增圖格]。

1. 選取 [儲存] 以儲存 [檢視 IoT Edge 裝置遙測] 檢視。

:::image type="content" source="media/tutorial-add-edge-as-leaf-device/template-telemetry-view.png" alt-text="具有遙測檢視的裝置範本":::

### <a name="publish-the-template"></a>發佈範本

您必須先發佈範本，才能新增使用**環境感應器邊緣裝置**範本的裝置。

瀏覽至 [環境感應器邊緣裝置] 範本，然後選取 [發佈]。 在 [將此裝置範本發佈至應用程式] 面板上，選取 [發佈] 以發佈範本：

:::image type="content" source="media/tutorial-add-edge-as-leaf-device/publish-template.png" alt-text="發佈裝置範本":::

## <a name="add-iot-edge-device"></a>新增 IoT Edge 裝置

現在您已發佈**環境感應器邊緣裝置**範本，接著您可以在 IoT Central 應用程式中新增裝置：

1. 在 IoT Central 應用程式中，瀏覽至 [裝置] 頁面，然後在可用範本清單中選取 [環境感應器邊緣裝置]。

1. 選取 [+ 新增]，從範本新增裝置。 在 [建立新裝置] 頁面上，選取 [建立]。

現在，您已擁有一個狀態為**已註冊**的新裝置：

:::image type="content" source="media/tutorial-add-edge-as-leaf-device/new-device.png" alt-text="已註冊的新裝置":::

### <a name="get-the-device-credentials"></a>取得裝置認證

當您稍後在本教學課程中部署 IoT Edge 裝置時，您需要擁有可讓裝置連線至 IoT Central 應用程式的認證。 若要取得裝置認證：

1. 在 [裝置] 頁面上，選取您建立好的裝置。

1. 選取 [連接]。

1. 在 [裝置連線] 頁面上，記下 [識別碼範圍]、[裝置識別碼] 和 [主要金鑰]。 您稍後會用到這些值。

1. 選取 [關閉]。

您現在已完成 IoT Central 應用程式的設定，而讓 IoT Edge 裝置能夠連線。

## <a name="deploy-an-iot-edge-device"></a>部署 IoT Edge 裝置

在本教學課程中，您會使用已在 Azure 上建立、並已啟用 Azure IoT Edge 的 Linux VM 來模擬 IoT Edge 裝置。 若要在 Azure 訂閱中建立支援 IoT Edge 的 VM，請按一下：

[![[部署至 Azure] 按鈕以進行 iotedge-vm-deploy](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fazure%2Fiotedge-vm-deploy%2Fmaster%2FedgeDeploy.json)

在 [自訂部署] 頁面上：

1. 選取 Azure 訂用帳戶。

1. 選取 [新建] 以建立名為 *central-edge-rg* 的新資源群組。

1. 選擇接近您的區域。

1. 新增唯一的 **DNS 標籤前置詞**，例如 *contoso-central edge*。

1. 選擇虛擬機器的系統管理員使用者名稱。

1. 輸入 *temp* 做為連線字串。 稍後，您會設定裝置以使用 DPS 進行連線。

1. 接受 VM 大小、Ubuntu 版本和位置的預設值。

1. 選取**密碼**做為驗證類型。

1. 輸入 VM 的密碼。

1. 然後選取 [檢閱 + 建立]。

1. 檢閱您的選擇，然後選取 [建立]：

    :::image type="content" source="media/tutorial-add-edge-as-leaf-device/vm-deployment.png" alt-text="建立 IoT Edge VM":::

部署需要幾分鐘的時間才能完成。 部署完成時，瀏覽至 Azure 入口網站中的 **central edge-rg** 資源群組。

### <a name="configure-the-iot-edge-vm"></a>設定 IoT Edge VM

若要將 VM 中的 IoT Edge 設定為使用 DPS 來註冊並連線至您的 IoT Central 應用程式：

1. 在 **contoso-edge-rg** 資源群組中，選取虛擬機器執行個體。

1. 在 [支援 + 疑難排解] 區段中，選取 [序列主控台]。 如果系統提示您設定開機診斷，請遵循入口網站中的指示執行。

1. 按 **Enter** 以查看 `login:` 提示字元。 輸入使用者名稱和密碼來登入。

1. 執行下列命令來檢查 IoT Edge 執行階段版本。 在撰寫本文時，版本為1.0.9.1：

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

    > [!TIP]
    > 請確定 `provisioning:` 前面沒有剩餘的空間

1. 將 `{scope_id}` 取代為您先前所記下的**識別碼範圍**。

1. 將 `{registration_id}` 取代為您先前所記下的 [裝置識別碼]。

1. 將 `{symmetric_key}` 取代為您先前所記下的 [主要金鑰]。

1. 儲存變更 (**Ctrl-O**) 並結束 (**Ctrl-X**) `nano` 編輯器。

1. 執行下列命令以重新啟動 IoT Edge 精靈：

    ```bash
    sudo systemctl restart iotedge
    ```

1. 若要檢查 IoT Edge 模組的狀態，請執行下列命令：

    ```bash
    iotedge list
    ```

    下列範例輸出顯示執行中的模組：

    ```bash
    NAME                        STATUS           DESCRIPTION      CONFIG
    SimulatedTemperatureSensor  running          Up 20 seconds    mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0
    edgeAgent                   running          Up 27 seconds    mcr.microsoft.com/azureiotedge-agent:1.0
    edgeHub                     running          Up 22 seconds    mcr.microsoft.com/azureiotedge-hub:1.0
    ```

    > [!TIP]
    > 您可能需要等候所有模組開始執行。

## <a name="view-the-telemetry"></a>檢視遙測資料

模擬的 IoT Edge 裝置現在正在 VM 中執行。 在 IoT Central 應用程式的 [裝置] 頁面上，裝置的狀態現在會是 [已佈建]：

:::image type="content" source="media/tutorial-add-edge-as-leaf-device/provisioned-device.png" alt-text="已佈建的 IoT Edge 裝置":::

您可以在 [檢視 IoT Edge 裝置遙測] 頁面上看到來自裝置的遙測：

:::image type="content" source="media/tutorial-add-edge-as-leaf-device/device-telemetry-view.png" alt-text="裝置遙測":::

[模組] 頁面會顯示裝置上 IoT Edge 模組的狀態：

:::image type="content" source="media/tutorial-add-edge-as-leaf-device/edge-module-status.png" alt-text="裝置模組狀態":::

## <a name="clean-up-resources"></a>清除資源

如果打算繼續使用 IoT Edge VM，您可以保留並重複使用自己在本教學課程中使用的資源。 否則，您可以刪除自己在本教學課程中建立的資源，以避免產生額外費用：

* 若要刪除 IoT Edge VM 及其相關聯的資源，請刪除 Azure 入口網站中的 **contoso-edge-rg** 資源群組。
* 若要刪除 IoT Central 應用程式，請瀏覽至應用程式的 [管理] 區段中的 [您的應用程式] 頁面，然後選取 [刪除]。

## <a name="next-steps"></a>後續步驟

身為裝置開發人員，您現在已了解如何在 IoT Central 中使用和管理 IoT Edge 裝置，建議的後續步驟是閱讀：

> [!div class="nextstepaction"]
> [開發 IoT Edge 模組](../../iot-edge/tutorial-develop-for-linux.md)

身為解決方案開發人員或操作員，現在您已瞭解如何在 IoT Central 中使用和管理 IoT Edge 裝置，建議執行的下一個步驟是：

> [!div class="nextstepaction"]
> [使用裝置群組來分析裝置遙測](./tutorial-use-device-groups.md)
