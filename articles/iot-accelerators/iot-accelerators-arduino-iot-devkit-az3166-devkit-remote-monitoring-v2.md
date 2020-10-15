---
title: 將 IoT DevKit 連線到遠端監視解決方案-Azure |Microsoft Docs
description: 在本操作指南中，您會了解如何將來自 IoT DevKit AZ3166 裝置上感應器的遙測資料傳送至遠端監視解決方案加速器，以進行監視和呈現視覺效果。
author: isabelcabezasm
manager: ''
ms.service: iot-accelerators
services: iot-accelerators
ms.devlang: c
ms.topic: conceptual
ms.date: 11/29/2018
ms.author: isacabe
ms.openlocfilehash: 7a9285ebe2e749f5ac7f248a2703518c8ac237bc
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/15/2020
ms.locfileid: "92071671"
---
# <a name="connect-an-iot-devkit-device-to-the-remote-monitoring-solution-accelerator"></a>將 IoT DevKit 裝置連線到遠端監視解決方案加速器

[!INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

本操作指南會示範如何在您的 IoT DevKit 裝置上執行範例應用程式。 範例程式碼會將來自 DevKit 裝置上感應器的遙測資料傳送至您的解決方案加速器。

[MXChip IoT DevKit](https://aka.ms/iot-devkit) 是全方位 Arduino 相容板，具有豐富的週邊設備和感應器。 您可以使用 Visual Studio Code 中的 [Azure IoT Device Workbench](https://aka.ms/iot-workbench) 或 [Azure IoT Tools](https://aka.ms/azure-iot-tools) 擴充套件來對它進行開發。 [專案目錄](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/)包含範例應用程式，可協助您設計 IoT 解決方案原型。

## <a name="before-you-begin"></a>開始之前

若要完成本教學課程中的步驟，請先執行下列工作：

* 遵循[將 IoT DevKit AZ3166 連線到雲端中的 Azure IoT 中樞](../iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started.md)中的步驟來準備您的 DevKit。

## <a name="open-sample-project"></a>開啟範例專案

若要在 VS Code 中開啟遠端監視範例：

1. 確定 IoT DevKit 未連線至您的電腦。 先啟動 VS Code，然後將 DevKit 連線至您的電腦。

1. 按一下 `F1` 以開啟命令選擇區，輸入並選取 [Azure IoT Device Workbench：**開啟範例...]** 。然後選取 [IoT DevKit] 作為面板。

1. 找出 [遠端監視]****，並按一下 [開啟範例]****。 新的 VS Code 視窗隨即開啟，其中顯示專案資料夾：

   ![IoT Workbench，選取遠端監視範例](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/iot-workbench-example.png)

## <a name="configure-the-device"></a>設定裝置

若要在您的 DevKit 裝置上設定 IoT 中樞裝置連接字串：

1. 將 IoT DevKit 切換至設定 **模式**：

    * 按住 **A** 按鈕。
    * 推送並放開 [ **重設** ] 按鈕。

1. 畫面會顯示 DevKit 識別碼和 `Configuration`。

    ![IoT DevKit 設定模式](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/devkit-configuration-mode.png)

1. 按下 **F1** 以開啟命令選擇區，輸入並選取 [ **Azure IoT 裝置工作臺：設定裝置設定 ...] > Config 裝置連接字串**。

1. 貼上您先前複製的連接字串，然後按 **Enter** 鍵設定裝置。

## <a name="build-the-code"></a>建置程式碼

若要建置並上傳裝置程式碼：

1. 按下 `F1` 以開啟命令選擇區，輸入並選取 **Azure IoT 裝置工作臺：上傳裝置程式碼**：

1. VS Code 會編譯程式碼並上傳到您的 DevKit 裝置：

    ![IoT Workbench：裝置 - > 已上傳](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/iot-workbench-device-uploaded.png)

1. DevKit 裝置會重新開機並執行您所上傳的程式碼。

## <a name="test-the-sample"></a>測試範例

若要確認您上傳至 DevKit 裝置的範例應用程式正常運作，請完成下列步驟：

### <a name="view-the-telemetry-sent-to-remote-monitoring-solution"></a>檢視傳送至遠端監視解決方案的遙測資料

當範例應用程式執行時，DevKit 裝置會透過 Wi-Fi 將來自其感應器的遙測資料傳送至您的解決方案加速器。 若要查看遙測資料：

1. 移至您的解決方案儀表板，然後按一下 [ **Device Explorer**]。

1. 按一下 DevKit 裝置的裝置名稱。 在右邊索引標籤上，您可以即時看到來自 DevKit 的遙測資料：

    ![Azure IoT 套件中的感應器資料](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/azure-iot-suite-dashboard.png)

### <a name="control-the-devkit-device"></a>控制 DevKit 裝置

遠端監視解決方案加速器可讓您從遠端控制裝置。 當您在 [ **Device Explorer** ] 頁面上選取裝置時，範例程式碼會執行三個您可以在 [**方法**] 區段中看到的方法：

![IoT DevKit 方法](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/azure-iot-suite-methods.png)

若要變更其中一個 DevKit LED 的色彩，請使用 **LedColor** 方法：

1. 從裝置清單中選取裝置名稱，然後按一下 [作業]****：

    ![建立作業](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/azure-iot-suite-job.png)

1. 使用下列值設定作業，然後按一下 [套用]****：

   * 選取作業：**執行方法**
   * 方法名稱：**LedColor**
   * 作業名稱：**ChangeLedColor**

     ![工作設定](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/iot-suite-change-color.png)

1. 在幾秒鐘內，您 DevKit 上的 RGB LED 色彩 (A 按鈕下方) 會變更：

    ![IoT DevKit 紅色 LED](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/azure-iot-suite-devkit-led.png)

## <a name="clean-up-resources"></a>清除資源

如果您打算繼續進行教學課程，請將已部署的遠端監視解決方案加速器保留下來。

如果您不再需要解決方案加速器，可選取該加速器，然後按一下 [刪除解決方案]，從已佈建的解決方案頁面中加以刪除：

![刪除解決方案](media/quickstart-remote-monitoring-deploy/deletesolution.png)

## <a name="problems-and-feedback"></a>問題與意見反應

如果遇到任何問題，請參閱 [IoT DevKit 常見問題集](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/)，或使用下列管道與我們連絡：

* [Gitter.im](https://gitter.im/Microsoft/azure-iot-developer-kit)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="next-steps"></a>後續步驟

現在您已了解如何將 DevKit 裝置連線到遠端監視解決方案加速器，以下是一些建議的後續步驟：

* [Azure IoT 解決方案加速器概觀](./index.yml)
* [自訂 UI](iot-accelerators-remote-monitoring-customize.md)