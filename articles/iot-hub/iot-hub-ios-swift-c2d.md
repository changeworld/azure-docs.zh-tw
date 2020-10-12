---
title: 使用 Azure IoT 中樞傳送雲端到裝置訊息 (iOS) | Microsoft Docs
description: 如何使用適用於 iOS 的 Azure IoT SDK，將雲端到裝置訊息從 Azure IoT 中樞傳送至裝置。
author: kgremban
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/19/2018
ms.author: kgremban
ms.custom: mqtt
ms.openlocfilehash: d8552391e8e8c389a44174595305b8f28224a833
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "81732544"
---
# <a name="send-cloud-to-device-messages-with-iot-hub-ios"></a>使用 IoT 中樞傳送雲端到裝置訊息 (iOS)

[!INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

Azure IoT 中樞是一項完全受控的服務，有助於讓數百萬個裝置和一個解決方案後端進行可靠且安全的雙向通訊。 [將遙測從裝置傳送至 IoT 中樞](quickstart-send-telemetry-ios.md)快速入門說明如何建立 IoT 中樞、在其中佈建裝置身分識別，以及撰寫模擬裝置應用程式來傳送裝置到雲端訊息。

本教學課程說明如何：

* 從您的解決方案後端，透過 IoT 中樞將雲端到裝置訊息傳送給單一裝置。

* 接收裝置上的雲端到裝置訊息。

* 從解決方案後端，要求確認收到從 IoT 中樞傳送到裝置的訊息 (「意見反應」)。

您可以在 [IoT 中樞開發人員指南的訊息區段](iot-hub-devguide-messaging.md)中，找到有關雲端到裝置訊息的詳細資訊。

在本文最後，您會執行兩個 Swift iOS 專案：

* **sample-device**：在[將遙測資料從裝置傳送到 IoT 中樞](quickstart-send-telemetry-ios.md)中建立的範例應用程式，其可連線到您的 IoT 中樞並接收雲端到裝置的訊息。

* **範例-服務**，此服務會透過 IoT 中樞將雲端到裝置訊息傳送至模擬裝置應用程式，然後接收其傳遞通知。

> [!NOTE]
> IoT 中樞會透過 Azure IoT 裝置 SDK 為許多裝置平台和語言 (包括 C、Java、Python 及 JavaScript) 提供 SDK 支援。 如需有關如何將您的裝置與本教學課程中的程式碼連接 (通常是連接到「Azure IoT 中樞」) 的逐步指示，請參閱 [Azure IoT 開發人員中樞](https://www.azure.com/develop/iot)。

## <a name="prerequisites"></a>必要條件

* 使用中的 Azure 帳戶。 (如果您沒有帳戶，只需要幾分鐘的時間就可以建立[免費帳戶](https://azure.microsoft.com/pricing/free-trial/)。)

* Azure 中的使用中 IoT 中樞。

* [Azure 範例](https://github.com/Azure-Samples/azure-iot-samples-ios/archive/master.zip)中的程式碼範例。

* 最新版的 [XCode](https://developer.apple.com/xcode/)，其執行最新版的 iOS SDK。 本快速入門已使用 XCode 9.3 和 iOS 11.3 進行測試。

* 最新版的 [CocoaPods](https://guides.cocoapods.org/using/getting-started.html)。

* 請確定您的防火牆已開啟連接埠 8883。 本文中的裝置範例會使用 MQTT 通訊協定，其會透過連接埠 8883 進行通訊。 某些公司和教育網路環境可能會封鎖此連接埠。 如需此問題的詳細資訊和解決方法，請參閱[連線至 IoT 中樞 (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub)。

## <a name="simulate-an-iot-device"></a>模擬 IoT 裝置

在這一節中，您會模擬執行 Swift 應用程式的 iOS 裝置，以接收來自 IoT 中樞的雲端到裝置訊息。 

這是您在[將遙測資料從裝置傳送到 IoT 中樞](quickstart-send-telemetry-ios.md)一文中建立的範例裝置。 如果您已經執行該裝置，可以跳過這一節。

### <a name="install-cocoapods"></a>安裝 CocoaPods

CocoaPods 可針對使用第三方程式庫的 iOS 專案管理相依性。

在終端機視窗中，巡覽至您在必要條件中下載的 Azure-IoT-Samples-iOS 資料夾。 然後，巡覽至範例專案：

```sh
cd quickstart/sample-device
```

確定已關閉 XCode，然後執行下列命令，以安裝在 **podfile** 檔案中宣告的 CocoaPods：

```sh
pod install
```

在安裝您的專案所需的 pod 時，安裝命令也建立了 XCode 工作區檔案，而該檔案已經設定為將 pod 使用於相依性。

### <a name="run-the-sample-device-application"></a>執行範例裝置應用程式

1. 擷取您裝置的連接字串。 您可以從 [Azure 入口網站](https://portal.azure.com)的 [裝置詳細資料] 刀鋒視窗中複製這個字串，或使用下列 CLI 命令擷取它：

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name {YourIoTHubName} --device-id {YourDeviceID} --output table
    ```

1. 在 XCode 中開啟範例工作區。

   ```sh
   open "MQTT Client Sample.xcworkspace"
   ```

2. 展開 [MQTT 用戶端範例]**** 專案，然後展開同名的資料夾。  

3. 開啟 **ViewController.swift** 以便在 XCode 中編輯。 

4. 搜尋 **connectionString** 變數，並使用您在第一個步驟中複製的裝置連接字串來更新此值。

5. 儲存您的變更。 

6. 使用 [建置並執行]  按鈕或 **Command + r** 按鍵組合，在裝置模擬器中執行專案。

   ![執行專案](media/iot-hub-ios-swift-c2d/run-sample.png)

## <a name="get-the-iot-hub-connection-string"></a>取得 IoT 中樞連接字串

在本文中，您會建立後端服務，透過在[將遙測從裝置傳送至 IoT 中樞](quickstart-send-telemetry-ios.md)內建立的 IoT 中樞來傳送雲端到裝置訊息。 若要傳送雲端到裝置訊息，則服務需要**服務連線**權限。 根據預設，每個 IoT 中樞都是透過授與此權限且名為**服務**的共用存取原則所建立。

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="simulate-a-service-device"></a>模擬服務裝置

在這一節中，您會使用 Swift 應用程式模擬次要 iOS 裝置，以透過 IoT 中樞傳送雲端到裝置的訊息。 此組態在下列 IoT 案例中很實用：以一個 iPhone 或 iPad 作為其他連線到 IoT 中樞之 iOS 裝置的控制器。

### <a name="install-cocoapods"></a>安裝 CocoaPods

CocoaPods 可針對使用第三方程式庫的 iOS 專案管理相依性。

巡覽至您在必要條件中下載的 Azure IoT iOS 範例資料夾。 然後，巡覽至範例服務專案：

```sh
cd quickstart/sample-service
```

確定已關閉 XCode，然後執行下列命令，以安裝在 **podfile** 檔案中宣告的 CocoaPods：

```sh
pod install
```

在安裝您的專案所需的 pod 時，安裝命令也建立了 XCode 工作區檔案，而該檔案已經設定為將 pod 使用於相依性。

### <a name="run-the-sample-service-application"></a>執行範例服務應用程式

1. 在 XCode 中開啟範例工作區。

   ```sh
   open AzureIoTServiceSample.xcworkspace
   ```

2. 展開 [AzureIoTServiceSample]**** 專案，然後展開同名的資料夾。  

3. 開啟 **ViewController.swift** 以便在 XCode 中編輯。 

4. 搜尋 **connectionString** 變數，並使用您先前在 [取得 IoT 中樞連接字串](#get-the-iot-hub-connection-string)中所複製的服務連接字串來更新此值。

5. 儲存您的變更。

6. 在 Xcode 中，將模擬器設定變更為與您用來執行 IoT 裝置的不同 iOS 裝置。 XCode 無法執行多個同類型的模擬器。

   ![變更模擬器裝置](media/iot-hub-ios-swift-c2d/change-device.png)

7. 使用 [ **組建] 和 [執行** ] 按鈕或按鍵組合 **命令 + r**，在裝置模擬器中執行專案。

   ![執行專案](media/iot-hub-ios-swift-c2d/run-app.png)

## <a name="send-a-cloud-to-device-message"></a>傳送雲端到裝置訊息

您現在可以使用兩個應用程式來傳送和接收雲端到裝置的訊息。

1. 在模擬 IoT 裝置上執行的 [iOS 應用程式範例]**** 應用程式中，按一下 [啟動]****。 應用程式會開始傳送裝置到雲端的訊息，但也會開始接聽雲端到裝置的訊息。

   ![檢視範例 IoT 裝置應用程式](media/iot-hub-ios-swift-c2d/view-d2c.png)

2. 在模擬服務裝置上執行的 [IoT 中樞服務用戶端範例]**** 應用程式中，針對您要傳送訊息的目標 IoT 裝置輸入識別碼。 

3. 撰寫純文字訊息，然後按一下 [傳送]****。

    當您按一下 [傳送] 時就會發生數個動作。 服務範例會將訊息傳送到您的 IoT 中樞，而應用程式因為您所提供的服務連線字串而具有該 IoT 中樞的存取權。 IoT 中樞會檢查裝置識別碼、將訊息傳送到目的地裝置，並將確認回條傳送給來源裝置。 在模擬 IoT 裝置上執行的應用程式會檢查來自 IoT 中樞的訊息，並且在畫面上顯示最新一則訊息的文字。

    您的輸出看起來會如下列範例所示：

   ![檢視雲端到裝置的訊息](media/iot-hub-ios-swift-c2d/view-c2d.png)

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何傳送和接收雲端到裝置的訊息。

若要查看使用 IoT 中樞的完整端對端解決方案範例，請參閱 [Azure IoT 解決方案加速器](https://azure.microsoft.com/documentation/suites/iot-suite/)文件。

若要深入了解如何使用 IoT 中樞開發解決方案，請參閱 [IoT 中樞開發人員指南](iot-hub-devguide.md)。
