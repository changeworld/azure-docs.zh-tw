---
title: 使用 VS Cloud Explorer 管理 Azure IoT 中樞裝置訊息
description: 了解如何使用適用於 Visual Studio 的 Cloud Explorer，在 Azure IoT 中樞監視裝置到雲端訊息和雲端到裝置訊息。
author: shizn
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/20/2019
ms.author: xshi
ms.openlocfilehash: c56bb7030b2ebc12e3afc24e2d8cb29ce2dda0bf
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "74079484"
---
# <a name="use-cloud-explorer-for-visual-studio-to-send-and-receive-messages-between-your-device-and-iot-hub"></a>使用適用於 Visual Studio 的 Cloud Explorer，在您的裝置和 IoT 中樞之間傳送及接收訊息

![端對端圖表](./media/iot-hub-visual-studio-cloud-device-messaging/e-to-e-diagram.png)

[Cloud Explorer](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.CloudExplorerForVS) 是非常有用的 Visual Studio 延伸模組，可讓您在 Visual Studio 內檢視您的 Azure 資源、檢查其屬性，以及執行重要的開發人員動作。 本文著重于如何使用 Cloud Explorer，在您的裝置與中樞之間傳送和接收訊息。

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

## <a name="what-you-learn"></a>您學到什麼

在本文中，您將瞭解如何使用 Visual Studio 的 Cloud Explorer 來監視裝置到雲端訊息，以及傳送雲端到裝置的訊息。 裝置到雲端的訊息可能是您的裝置所收集，然後傳送到 IoT 中樞的感應器資料。 雲端到裝置訊息可能是您的 IoT 中樞傳送到裝置的命令。 例如，閃爍連接到您裝置的 LED。

## <a name="what-you-do"></a>您要做什麼

在本文中，您會執行下列工作：

- 使用適用於 Visual Studio 的 Cloud Explorer 來監視裝置到雲端訊息。

- 使用適用於 Visual Studio 的 Cloud Explorer 來傳送雲端到裝置訊息。

## <a name="what-you-need"></a>您需要什麼

您必須符合下列必要條件：

- 有效的 Azure 訂用帳戶。

- 位於您訂用帳戶中的 Azure IoT 中樞。

- Microsoft Visual Studio 2017 Update 9 或更新版本。 本文使用[Visual Studio 2019](https://www.visualstudio.com/vs/)。

- Visual Studio 安裝程式的 Cloud Explorer 元件，預設會使用 Azure 工作負載來選取。

## <a name="update-cloud-explorer-to-latest-version"></a>將 Cloud Explorer 更新至最新版本

來自 Visual Studio 2017 Visual Studio 安裝程式的 Cloud Explorer 元件僅支援監視裝置到雲端和雲端到裝置的訊息。 若要使用 Visual Studio 2017，請下載並安裝最新的[Cloud Explorer](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.CloudExplorerForVS)。

## <a name="sign-in-to-access-your-hub"></a>登入以存取您的中樞

若要存取您的中樞，請遵循下列步驟：

1. 在 Visual Studio 中，選取 [ **View**  >  **Cloud Explorer** ] 以開啟 Cloud Explorer。

1. 選取 [帳戶管理] 圖示，以顯示您的訂閱。

    ![帳戶管理圖示](media/iot-hub-visual-studio-cloud-device-messaging/account-management-icon.png)

1. 如果您已登入 Azure，就會顯示您的帳戶。 若要第一次登入 Azure，請選擇 [**新增帳戶**]。

1. 選取您想要使用的 Azure 訂用帳戶，然後選擇 [套用 **]。**

1. 依序展開您的訂用帳戶和 [ **IoT 中樞**]。  在每個中樞底下，您可以看到該中樞的裝置。

    ![裝置清單](media/iot-hub-visual-studio-cloud-device-messaging/hub-device-list.png)

## <a name="monitor-device-to-cloud-messages"></a>監視裝置到雲端的訊息

若要監視從您的裝置傳送到 IoT 中樞的訊息，請遵循下列步驟：

1. 在裝置或 IoT 中樞上按滑鼠右鍵，然後選取 [Start Monitoring D2C Message]**** \(開始監視 D2C 訊息\)。

    ![開始監視 D2C 訊息](media/iot-hub-visual-studio-cloud-device-messaging/start-monitoring-d2c-message-vs2019.png)

1. 受監視的訊息會顯示在 [**輸出**] 之下。

    ![監視 D2C 訊息結果](media/iot-hub-visual-studio-cloud-device-messaging/monitor-d2c-message-result-vs2019.png)

1. 若要停止監視，請以滑鼠右鍵按一下任何 IoT 中樞或裝置，並選取 [Stop Monitoring D2C Message]**** \(停止監視 D2C 訊息\)。

## <a name="send-cloud-to-device-messages"></a>傳送雲端到裝置訊息

若要從您的 IoT 中樞將訊息傳送到裝置，請遵循這些步驟：

1. 以滑鼠右鍵按一下您的裝置，然後選取 [Send C2D Message]**** \(傳送 C2D 訊息\)。

1. 在輸入方塊中輸入訊息。

    ![傳送 C2D 訊息](media/iot-hub-visual-studio-cloud-device-messaging/send-c2d-message-test.png)

    結果會顯示在 [**輸出**] 之下。

    ![傳送 C2D 訊息結果](media/iot-hub-visual-studio-cloud-device-messaging/send-c2d-message-result-vs2019.png)

## <a name="next-steps"></a>後續步驟

您已了解如何監視裝置到雲端的訊息，以及在 IoT 裝置和 Azure IoT 中樞之間傳送雲端到裝置的訊息。

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]