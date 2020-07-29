---
title: 使用適用于 VSCode 的 Azure IoT Tools 來管理 IT 中樞訊息
description: 了解如何使用適用於 Visual Studio Code 的 Azure IoT Tools 來監視 Azure IoT 中樞的裝置到雲端訊息，以及傳送雲端到裝置訊息。
author: formulahendry
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 01/18/2019
ms.author: junhan
ms.custom:
- 'Role: Cloud Development'
ms.openlocfilehash: b9d8f0c2a573ba1749b414a389cd7181ddd5f95a
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87321678"
---
# <a name="use-azure-iot-tools-for-visual-studio-code-to-send-and-receive-messages-between-your-device-and-iot-hub"></a>使用適用於 Visual Studio Code 的 Azure IoT Tools 來傳送和接收裝置與 IoT 中樞之間的訊息

![端對端圖表](./media/iot-hub-vscode-iot-toolkit-cloud-device-messaging/e-to-e-diagram.png)

[Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit) \(英文\) 是實用的 Visual Studio Code 擴充功能，可讓 IoT 中樞的管理和 IoT 應用程式開發變得更輕鬆。 本文章重點介紹如何使用適用於 Visual Studio Code 的 Azure IoT Tools 來傳送和接收裝置與 IoT 中樞之間的訊息。

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

## <a name="what-you-will-learn"></a>學習內容

您將了解如何使用適用於 Visual Studio Code 的 Azure IoT Tools 來監視 Azure IoT 中樞的裝置到雲端訊息，以及傳送雲端到裝置訊息。 裝置到雲端的訊息可能是您的裝置所收集，然後傳送到 IoT 中樞的感應器資料。 雲端到裝置的訊息可能是 IoT 中樞傳送到裝置以使連接到裝置的 LED 閃爍的命令。

## <a name="what-you-will-do"></a>將執行的作業

* 使用適用於 Visual Studio Code 的 Azure IoT Tools 來監視裝置到雲端訊息。

* 使用適用於 Visual Studio Code 的 Azure IoT Tools 來傳送雲端到裝置訊息。

## <a name="what-you-need"></a>必要條件

* 有效的 Azure 訂用帳戶。

* 位於您訂用帳戶中的 Azure IoT 中樞。

* [Visual Studio Code](https://code.visualstudio.com/) \(英文\)

* [Azure IoT Tools，VS Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)或將此 URL 複製並貼到瀏覽器視窗中：`vscode:extension/vsciot-vscode.azure-iot-tools`

## <a name="sign-in-to-access-your-iot-hub"></a>登入以存取 IoT 中樞

1. 在 VS Code 的 [總管]**** 檢視中，展開左下角的 [Azure IoT Hub Devices]**** \(Azure IoT 中樞裝置\) 部分。

2. 按一下快顯功能表中的 [Select IoT Hub]**** \(選取 IoT 中樞\)。

3. 快顯視窗會顯示在右下角，讓您第一次登入 Azure。

4. 登入之後，系統會顯示您的 Azure 訂用帳戶清單，然後選取 [Azure 訂用帳戶] 和 [IoT 中樞]。

5. 在幾秒鐘的時間內，裝置清單會顯示在 [Azure IoT Hub Devices]**** \(Azure IoT 中樞裝置\) 索引標籤中。

   > [!Note]
   > 您也可以選擇 [設定 IoT 中樞連接字串]**** 來完成設定。 在快顯視窗中，輸入 IoT 裝置所連接之 IoT 中樞的**iothubowner**原則連接字串。

## <a name="monitor-device-to-cloud-messages"></a>監視裝置到雲端的訊息

若要監視從您的裝置傳送到IoT 中樞的訊息，請遵循下列步驟：

1. 以滑鼠右鍵按一下您的裝置，然後選取 [**開始監視內建事件端點**]。

2. 受監視的訊息會顯示在 [**輸出**  >  **Azure IoT 中樞**] 視圖中。

3. 若要停止監視，請以滑鼠右鍵按一下**輸出**視圖，然後選取 [**停止監視內建事件端點**]。

## <a name="send-cloud-to-device-messages"></a>傳送雲端到裝置訊息

若要從您的 IoT 中樞將訊息傳送到裝置，請遵循下列步驟：

1. 以滑鼠右鍵按一下您的裝置，然後選取 [Send C2D Message to Device]**** \(將 C2D 訊息傳送至裝置\)。

2. 在輸入方塊中輸入訊息。

3. 結果會顯示在 [**輸出**  >  **Azure IoT 中樞**] 視圖中。

## <a name="next-steps"></a>後續步驟

您已了解如何監視裝置到雲端的訊息，以及在 IoT 裝置和 Azure IoT 中樞之間傳送雲端到裝置的訊息。

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]