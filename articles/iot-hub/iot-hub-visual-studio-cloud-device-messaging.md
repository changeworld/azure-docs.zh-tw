---
title: 使用 VS 雲資源管理器管理 Azure IoT 中心設備消息傳送
description: 了解如何使用適用於 Visual Studio 的 Cloud Explorer，在 Azure IoT 中樞監視裝置到雲端訊息和雲端到裝置訊息。
author: shizn
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/20/2019
ms.author: xshi
ms.openlocfilehash: c56bb7030b2ebc12e3afc24e2d8cb29ce2dda0bf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74079484"
---
# <a name="use-cloud-explorer-for-visual-studio-to-send-and-receive-messages-between-your-device-and-iot-hub"></a>使用適用於 Visual Studio 的 Cloud Explorer，在您的裝置和 IoT 中樞之間傳送及接收訊息

![端對端圖表](./media/iot-hub-visual-studio-cloud-device-messaging/e-to-e-diagram.png)

[Cloud Explorer](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.CloudExplorerForVS) 是非常有用的 Visual Studio 延伸模組，可讓您在 Visual Studio 內檢視您的 Azure 資源、檢查其屬性，以及執行重要的開發人員動作。 本文重點介紹如何使用雲資源管理器在設備和中心之間發送和接收消息。

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

## <a name="what-you-learn"></a>您學到什麼

在本文中，您將瞭解如何使用 Visual Studio 的雲資源管理器來監視設備到雲的消息以及發送雲到設備的消息。 裝置到雲端的訊息可能是您的裝置所收集，然後傳送到 IoT 中樞的感應器資料。 雲端到裝置訊息可能是您的 IoT 中樞傳送到裝置的命令。 例如，閃爍連接到您裝置的 LED。

## <a name="what-you-do"></a>您要做什麼

在本文中，您將執行以下任務：

- 使用適用於 Visual Studio 的 Cloud Explorer 來監視裝置到雲端訊息。

- 使用適用於 Visual Studio 的 Cloud Explorer 來傳送雲端到裝置訊息。

## <a name="what-you-need"></a>您需要什麼

您必須符合下列必要條件：

- 有效的 Azure 訂用帳戶。

- 位於您訂用帳戶中的 Azure IoT 中樞。

- 微軟視覺工作室 2017 更新 9 或更高版本。 本文使用[視覺工作室2019。](https://www.visualstudio.com/vs/)

- 視覺化工作室安裝程式中的雲資源管理器元件，預設情況下使用 Azure 工作負荷選擇該元件。

## <a name="update-cloud-explorer-to-latest-version"></a>將 Cloud Explorer 更新至最新版本

Visual Studio 2017 視覺化工作室安裝程式的雲資源管理器元件僅支援監控設備到雲和雲到設備的消息。 要使用 Visual Studio 2017，請下載並安裝最新的[雲資源管理器](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.CloudExplorerForVS)。

## <a name="sign-in-to-access-your-hub"></a>登錄以訪問您的中心

要訪問中心，請按照以下步驟操作：

1. 在視覺化工作室中，選擇 **"查看** > **雲資源管理器**"以打開雲資源管理器。

1. 選擇"帳戶管理"圖示以顯示訂閱。

    ![帳戶管理圖示](media/iot-hub-visual-studio-cloud-device-messaging/account-management-icon.png)

1. 如果登錄到 Azure，將顯示您的帳戶。 要首次登錄到 Azure，請選擇"**添加帳戶**"。

1. 選擇要使用的 Azure 訂閱，然後選擇 **"應用**"。

1. 展開訂閱，然後展開**IoT 中心**。  在每個中心下，您可以看到該集線器的設備。

    ![裝置清單](media/iot-hub-visual-studio-cloud-device-messaging/hub-device-list.png)

## <a name="monitor-device-to-cloud-messages"></a>監視裝置到雲端的訊息

若要監視從您的裝置傳送到 IoT 中樞的訊息，請遵循下列步驟：

1. 在裝置或 IoT 中樞上按滑鼠右鍵，然後選取 [Start Monitoring D2C Message]**** \(開始監視 D2C 訊息\)。

    ![開始監視 D2C 訊息](media/iot-hub-visual-studio-cloud-device-messaging/start-monitoring-d2c-message-vs2019.png)

1. 受監視的消息顯示在 **"輸出**"下。

    ![監視 D2C 訊息結果](media/iot-hub-visual-studio-cloud-device-messaging/monitor-d2c-message-result-vs2019.png)

1. 若要停止監視，請以滑鼠右鍵按一下任何 IoT 中樞或裝置，並選取 [Stop Monitoring D2C Message]**** \(停止監視 D2C 訊息\)。

## <a name="send-cloud-to-device-messages"></a>傳送雲端到裝置訊息

若要從您的 IoT 中樞將訊息傳送到裝置，請遵循這些步驟：

1. 以滑鼠右鍵按一下您的裝置，然後選取 [Send C2D Message]**** \(傳送 C2D 訊息\)。

1. 在輸入方塊中輸入訊息。

    ![傳送 C2D 訊息](media/iot-hub-visual-studio-cloud-device-messaging/send-c2d-message-test.png)

    結果顯示在 **"輸出**"下。

    ![傳送 C2D 訊息結果](media/iot-hub-visual-studio-cloud-device-messaging/send-c2d-message-result-vs2019.png)

## <a name="next-steps"></a>後續步驟

您已了解如何監視裝置到雲端的訊息，以及在 IoT 裝置和 Azure IoT 中樞之間傳送雲端到裝置的訊息。

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]