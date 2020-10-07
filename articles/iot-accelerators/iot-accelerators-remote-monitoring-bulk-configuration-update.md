---
title: 管理已連線到遠端監視的大量裝置 - Azure | Microsoft Docs
description: 在本教學課程中，您會了解如何管理已連線到遠端監視解決方案的大量裝置。
author: Philmea
manager: philmea
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: tutorial
ms.date: 11/29/2018
ms.author: philmea
ms.openlocfilehash: 8309b625a590131c5f521335e180967ab2c2667c
ms.sourcegitcommit: d95cab0514dd0956c13b9d64d98fdae2bc3569a0
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91363149"
---
# <a name="tutorial-manage-your-connected-devices-in-bulk"></a>教學課程：管理已連線的大量裝置

在本教學課程中，您會使用遠端監視解決方案加速器來管理已連線的大量裝置組態。

身為 Contoso 的操作員，您必須設定使用新韌體版本的裝置群組。 您不希望必須在每個裝置上個別地更新軔體。 若要更新裝置群組上的韌體，您可以在遠端監視解決方案加速器中使用裝置群組和自動裝置管理。 只要裝置上線，任何您新增至裝置群組的裝置都會取得最新的韌體。

在本教學課程中，您：

>[!div class="checklist"]
> * 建立裝置群組
> * 準備及裝載韌體
> * 在 Azure 入口網站中建立裝置組態
> * 在遠端監視解決方案中匯入裝置組態
> * 將組態部署至裝置群組中的裝置
> * 監視部署

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

<!--
If this is going to be a tutorial - we need to split this include into two so that we can accommodate the additional prerequisites:

[!INCLUDE [iot-accelerators-tutorial-prereqs](../../includes/iot-accelerators-tutorial-prereqs.md)]
-->

## <a name="prerequisites"></a>Prerequisites

若要依循本教學課程進行操作，您需要在 Azure 訂用帳戶中有一個已部署的遠端監視解決方案加速器執行個體。

如果您尚未部署遠端監視解決方案加速器，則應該先完成[部署雲端式遠端監視解決方案](quickstart-remote-monitoring-deploy.md)快速入口。

您需要 Azure 儲存體帳戶來裝載韌體檔案。 您可以使用現有儲存體帳戶，或在訂用帳戶中[建立新的儲存體帳戶](../storage/common/storage-account-create.md)。

本教學課程會使用 [IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/) 裝置作為範例裝置。

您必須在本機電腦上安裝下列軟體︰

* [Visual Studio Code (VS Code)](https://code.visualstudio.com/)。
* [Azure IoT Workbench](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-iot-workbench) VS Code 擴充功能。

開始之前：

* 請確定 [IoT DevKit 裝置上的開機載入器為 1.4.0 版或更高版本](https://microsoft.github.io/azure-iot-developer-kit/docs/firmware-upgrading/)。
* 請確定 IoT DevKit SDK 與開機載入器的版本相同。 您可以在 VS Code 中使用 Azure IoT Workbench 來更新 IoT DevKit SDK。 開啟命令選擇區，並輸入 **[Arduino:** Board Manager]。 如需詳細資訊，請參閱[準備開發環境](../iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started.md#prepare-the-development-environment)。

您也需要將至少一個 IoT DevKit 裝置連線到遠端監視解決方案加速器。 如果您尚未連線到 IoT DevKit 裝置，請參閱[將 MXChip IoT DevKit AZ3166 連線到 IoT 遠端監視解決方案加速器](iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2.md)。

## <a name="navigate-to-the-dashboard"></a>瀏覽至儀表板

若要在您的瀏覽器中檢視遠端監視解決方案儀表板，請先瀏覽至 [Microsoft Azure IoT 解決方案加速器](https://www.azureiotsolutions.com/Accelerators#dashboard)。 系統可能會要求您使用 Azure 訂用帳戶認證來登入。

接著，請針對您在[快速入門](quickstart-remote-monitoring-deploy.md)中部署的遠端監視解決方案加速器，按一下其圖格上的 [啟動]  。

## <a name="create-a-device-group"></a>建立裝置群組

若要自動更新裝置群組上的韌體，裝置必須是遠端監視解決方案中裝置群組的成員：

1. 在 [裝置]  頁面上，選取您已連線到解決方案加速器的所有 **IoT DevKit** 裝置。 然後，按一下 [作業]  。

1. 在 [作業]  面板中選取 [標記]  ，將作業名稱設定為 **AddDevKitTag**，然後使用 **Y** 值新增名為 **IsDevKitDevice** 的文字標記。然後按一下 [套用]  。

1. 現在，您可以使用標記值來建立裝置群組。 在 [裝置]  頁面上，按一下 [管理裝置群組]  。

1. 建立文字篩選條件，其在條件中使用 **IsDevKitDevice** 和 **Y** 值。 將裝置群組儲存為 **IoT DevKit 裝置**。

稍後在本教學課程中中，您可使用此裝置群組以套用可更新所有成員韌體的裝置組態。

## <a name="prepare-and-host-the-firmware"></a>準備及裝載韌體

[Azure IoT Workbench](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-iot-workbench) VS Code 擴充功能包含韌體更新的範例裝置程式碼。

### <a name="open-the-firmware-ota-sample-in-vs-code"></a>在 VS Code 中開啟韌體 OTA 範例

1. 確定 IoT DevKit 未連線至您的電腦。 啟動 VS Code，然後將 DevKit 連線至您的電腦。

1. 按 **F1** 開啟命令選擇區，然後輸入並選取 **[IoT Workbench：範例]** 。 然後選取 [IoT DevKit]  作為面板。

1. 尋找 [韌體 OTA]  ，然後按一下 [開啟範例]  。 新的 VS Code 視窗隨即開啟並顯示 **firmware_ota** 專案資料夾：

    ![IoT Workbench，選取韌體 OTA 範例](media/iot-accelerators-remote-monitoring-bulk-configuration-update/iot-workbench-firmware-example.png)

### <a name="build-the-new-firmware"></a>建置新韌體

裝置韌體的初始版本為 1.0.0。 新的韌體應具有更高的版本號碼。

1. 在 VS Code 中開啟 **FirmwareOTA.ino** 檔案，並將 `currentFirmwareVersion` 從 `1.0.0` 變更為 `1.0.1`：

    ![變更韌體版本](media/iot-accelerators-remote-monitoring-bulk-configuration-update/version-1-0-1.png)

1. 開啟命令選擇區，然後輸入並選取 **[IoT Workbench：裝置]** 。 然後選取 [裝置編譯]  來編譯程式碼：

    ![裝置編譯](media/iot-accelerators-remote-monitoring-bulk-configuration-update/iot-workbench-device-compile.png)

    VS Code 會在專案的 `.build` 資料夾中儲存已編譯的檔案。 根據您的設定，VS Code 可能會在總管檢視中隱藏 `.build` 資料夾。

### <a name="generate-the-crc-value-and-calculate-the-firmware-file-size"></a>產生 CRC 值及計算韌體檔案大小

1. 開啟命令選擇區，然後輸入並選取 **[IoT Workbench：裝置]** 。 然後選取 [產生 CRC]  ：

    ![產生 CRC](media/iot-accelerators-remote-monitoring-bulk-configuration-update/iot-workbench-device-crc.png)

1. VS Code 會在輸出視窗中，產生並輸出 CRC 值、韌體檔名和路徑以及檔案大小。 記下這些值以便稍後使用：

    ![CRC 資訊](media/iot-accelerators-remote-monitoring-bulk-configuration-update/crc-info.png)

### <a name="upload-the-firmware-to-the-cloud"></a>將韌體上傳至雲端

使用 Azure 儲存體帳戶，以在雲端裝載新的韌體檔案。

1. 在 Azure 入口網站中巡覽至您的儲存體帳戶。 在 [服務] 區段中，選取 [Blob]  。 建立稱為 **firmware** 的公用容器來儲存韌體檔案：

    ![建立資料夾](media/iot-accelerators-remote-monitoring-bulk-configuration-update/blob-folder.png)

1. 若要將韌體檔案上傳至容器，請選取 **firmware** 容器，然後按一下 [上傳]  。

1. 選取 **FirmwareOTA.ino.bin**。 您在上一節中記下了這個檔案的完整路徑。

1. 韌體檔案上傳完成後，請記下檔案 URL。

### <a name="build-and-upload-the-original-firmware-to-the-iot-devkit-device"></a>建置原始韌體並上傳到 IoT DevKit 裝置

1. 在 VS Code 中開啟 **FirmwareOTA.ino** 檔案，並將 `currentFirmwareVersion` 變回 `1.0.0`：

    ![1\.0.0 版](media/iot-accelerators-remote-monitoring-bulk-configuration-update/version-1-0-1.png)

1. 開啟命令選擇區，然後輸入並選取 **[IoT Workbench：裝置]** 。 然後選取 [裝置上傳]  ：

    ![裝置上傳](media/iot-accelerators-remote-monitoring-bulk-configuration-update/device-upload.png)

1. VS Code 會驗證程式碼並上傳到您的 IoT DevKit 裝置。

1. 上傳完成時，IoT DevKit 裝置會重新開機。 重新開機完成時，IoT DevKit 的畫面會顯示 **[FW 版本：1.0.0]** ，而且會檢查是否有新韌體：

    ![相片顯示 I o T DevKit 裝置，其畫面會顯示韌體版本，並檢查是否有新的韌體。](media/iot-accelerators-remote-monitoring-bulk-configuration-update/ota-1.jpg)

## <a name="create-a-device-configuration"></a>建立裝置組態

裝置組態會指定您裝置所需的狀態。 一般而言，開發人員會在 Azure 入口網站的 [IoT 裝置組態]  頁面上[建立組態](../iot-hub/iot-hub-automatic-device-management.md#create-a-configuration)。 裝置組態是一個 JSON 文件，可指定您裝置所需的狀態和一組計量。

本機電腦上，將下列組態儲存為稱為 **firmware-update.json** 的檔案。 以您先前記下的值取代 `YOURSTRORAGEACCOUNTNAME`、`YOURCHECKSUM` 和 `YOURPACKAGESIZE` 預留位置：

```json
{
  "id": "firmware-update",
  "schemaVersion": null,
  "labels": {
    "Version": "1.0.1",
    "Devices": "IoT DevKit"
  },
  "content": {
    "deviceContent": {
      "properties.desired.firmware": {
        "fwVersion": "1.0.1",
        "fwPackageURI": "https://YOURSTRORAGEACCOUNTNAME.blob.core.windows.net/firmware/FirmwareOTA.ino.bin",
        "fwPackageCheckValue": "YOURCHECKSUM",
        "fwSize": YOURPACKAGESIZE
      }
    }
  },
  "targetCondition": "",
  "priority": 10,
  "systemMetrics": {
    "results": {
      "targetedCount": 0,
      "appliedCount": 0
    },
    "queries": {
      "targetedCount": "",
      "appliedCount": "select deviceId from devices where configurations.[[firmware-update]].status = 'Applied'"
    }
  },
  "metrics": {
    "results": {
      "Current": 1
    },
    "queries": {
      "Current": "SELECT deviceId FROM devices WHERE properties.reported.firmware.fwUpdateStatus='Current' AND properties.reported.firmware.type='IoTDevKit'",
      "Downloading": "SELECT deviceId FROM devices WHERE properties.reported.firmware.fwUpdateStatus='Downloading' AND properties.reported.firmware.type='IoTDevKit'",
      "Verifying": "SELECT deviceId FROM devices WHERE properties.reported.firmware.fwUpdateStatus='Verifying' AND properties.reported.firmware.type='IoTDevKit'",
      "Applying": "SELECT deviceId FROM devices WHERE properties.reported.firmware.fwUpdateStatus='Applying' AND properties.reported.firmware.type='IoTDevKit'",
      "Error": "SELECT deviceId FROM devices WHERE properties.reported.firmware.fwUpdateStatus='Error' AND properties.reported.firmware.type='IoTDevKit'"
    }
  }
}
```

您可以在下一節中使用此組態檔。

## <a name="import-a-configuration"></a>匯入組態

在本節中，您會將裝置組態當作套件匯入遠端監視解決方案加速器中。 一般來說，操作員會完成這項工作。

1. 在遠端監視 Web UI 中瀏覽至 [套件]  頁面，然後按一下 [+ 新增套件]  ：

    ![新增套件](media/iot-accelerators-remote-monitoring-bulk-configuration-update/packagepage.png)

1. 在 [新增套件]  面板中，選擇 [裝置組態]  作為套件類型，並選擇 [韌體]  作為組態類型。 按一下 [瀏覽]  以在本機電腦上尋找 **firmware-update.json** 檔案，然後按一下 [上載]  ：

    ![上傳套件](media/iot-accelerators-remote-monitoring-bulk-configuration-update/uploadpackage.png)

1. 套件清單現在包含 **firmware-update** 套件。

## <a name="deploy-the-configuration-to-your-devices"></a>將組態部署至您的裝置

在本節中，您會建立並執行部署，以將裝置組態套用到您的 IoT DevKit 裝置。

1. 在遠端監視 Web UI 中瀏覽至 [部署]  頁面，然後按一下 [+ 新增部署]  ：

    ![新增部署](media/iot-accelerators-remote-monitoring-bulk-configuration-update/deploymentpage.png)

1. 在 [新增部署]  面板中，使用下列設定建立部署：

    |選項|值|
    |---|---|
    |名稱|部署韌體更新|
    |套件類型|裝置組態|
    |組態類型|韌體|
    |Package|firmware-update.json|
    |裝置群組|IoT DevKit 裝置|
    |優先順序|10|

    ![建立部署](media/iot-accelerators-remote-monitoring-bulk-configuration-update/newdeployment.png)

    按一下 [套用]  。 您會在 [部署]  頁面中看見新的部署，其中顯示下列計量：

    * [已設定目標]  會顯示裝置群組中的裝置數目。
    * [已套用]  顯示以部署內容更新的裝置數目。
    * [已成功]  顯示部署中回報成功的裝置數目。
    * [已失敗]  顯示部署中回報失敗的裝置數目。

## <a name="monitor-the-deployment"></a>監視部署

幾分鐘後，IoT DevKit 會擷取新的韌體資訊，並開始將資料下載到裝置：

![相片顯示 I o T DevKit 裝置，其畫面顯示新的韌體版本下載中。](media/iot-accelerators-remote-monitoring-bulk-configuration-update/ota-2.jpg)

根據您的網路速度，下載可能需要幾分鐘的時間。 下載韌體之後，裝置會確認檔案大小和 CRC 值。 如果驗證成功，則 MXChip 的畫面會顯示**通過**。

![相片顯示 I o T DevKit 裝置，其畫面會顯示韌體版本，並成功通過驗證。](media/iot-accelerators-remote-monitoring-bulk-configuration-update/ota-3.jpg)

如果檢查成功，則裝置會重新開機。 在發生重新啟動前，您會看到從 **5** 到 **0** 的倒數計時。

![相片顯示準備重新開機的 I o T DevKit 裝置。](media/iot-accelerators-remote-monitoring-bulk-configuration-update/ota-4.jpg)

重新開機後，IoT DevKit 開機載入器會將韌體升級為新的版本。 升級可能需要數秒鐘的時間。 在這個階段中，裝置中的 RGB LED 為紅色且螢幕空白。

![相片顯示執行升級的 I o T DevKit 裝置。 沒有顯示，但有 R G B L E D 發光的紅色。](media/iot-accelerators-remote-monitoring-bulk-configuration-update/ota-5.jpg)

完成重新開機後，您的 IoT DevKit 裝置現在會執行 1.0.1 版的韌體。

![相片顯示 I o T DevKit 裝置，其畫面顯示新的韌體版本。](media/iot-accelerators-remote-monitoring-bulk-configuration-update/ota-6.jpg)

在 [部署]**** 頁面上，按一下部署可查看裝置更新時的狀態。 您可以在您的裝置群組和您所定義的自訂度量中，看到每個裝置的狀態。

![部署詳細資料](media/iot-accelerators-remote-monitoring-bulk-configuration-update/deploymentstatus.png)

[!INCLUDE [iot-accelerators-tutorial-cleanup](../../includes/iot-accelerators-tutorial-cleanup.md)]

## <a name="next-steps"></a>後續步驟

本教學課程示範了如何為已連線到您解決方案的裝置群組更新韌體。 為了更新裝置，您的解決方案會使用自動裝置管理。 若要深入了解您解決方案的基礎 IoT 中樞內的自動裝置管理功能，請參閱[使用 Azure 入口網站大規模設定及監視 IoT 裝置](../iot-hub/iot-hub-auto-device-config.md)。