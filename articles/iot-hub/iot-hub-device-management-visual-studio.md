---
title: Azure IoT 裝置管理，帶視覺化工作室雲資源管理器
description: 使用適用於 Visual Studio 的 Cloud Explorer 來管理 Azure IoT 中樞裝置，並採用直接方法和對應項所需的屬性管理選項。
author: shizn
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/20/2019
ms.author: xshi
ms.openlocfilehash: 6fe5a45dda6632c56b3c6714827950e25e7d26af
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "73953188"
---
# <a name="use-cloud-explorer-for-visual-studio-for-azure-iot-hub-device-management"></a>使用適用於 Visual Studio 的 Cloud Explorer 來管理 Azure IoT 中樞裝置

![端對端圖表](media/iot-hub-device-management-visual-studio/iot-e2e-simple.png)

[Cloud Explorer](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.CloudExplorerForVS) 是非常有用的 Visual Studio 延伸模組，可讓您在 Visual Studio 內檢視您的 Azure 資源、檢查其屬性，以及執行重要的開發人員動作。 它隨附的管理選項可供您用來執行各種工作。

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

| 管理選項          | Task                    |
|----------------------------|--------------------------------|
| 直接方法             | 使裝置執行動作，例如啟動或停止傳送訊息，或是將裝置重新開機。                                        |
| 讀取裝置對應項           | 取得裝置的報告狀態。 例如，裝置會回報 LED 現在正閃爍不停。                                    |
| 更新裝置對應項         | 讓裝置進入特定狀態，例如將 LED 設定為綠色，或將遙測傳送間隔設定為 30 分鐘。         |
| 雲端到裝置的訊息   | 將通知傳送至裝置。 例如，「今天很可能會下雨。 別忘了帶傘。」              |

如需差異的詳細說明和使用這些選項的相關指引，請參閱[裝置對雲端通訊指引](iot-hub-devguide-d2c-guidance.md)和[雲端對裝置通訊指引](iot-hub-devguide-c2d-guidance.md)。

裝置對應項是存放裝置狀態資訊的 JSON 文件，包括中繼資料、設定和條件。 IoT 中樞會為其連線的每個裝置保存裝置對應項。 如需裝置對應項的詳細資訊，請參閱[開始使用裝置對應項](iot-hub-node-node-twin-getstarted.md)。

## <a name="what-you-learn"></a>您學到什麼

在本文中，您將瞭解如何在開發電腦上使用 Visual Studio 雲資源管理器以及各種管理選項。

## <a name="what-you-do"></a>您要做什麼

在本文中，使用各種管理選項為 Visual Studio 運行雲資源管理器。

## <a name="what-you-need"></a>您需要什麼

您必須符合下列必要條件：

- 有效的 Azure 訂用帳戶。

- 位於您訂用帳戶中的 Azure IoT 中樞。

- 微軟視覺工作室 2017 更新 9 或更高版本。 本文使用[視覺工作室 2017 或視覺工作室 2019](https://www.visualstudio.com/vs/).

- 視覺化工作室安裝程式中的雲資源管理器元件，預設情況下使用 Azure 工作負荷選擇。

## <a name="update-cloud-explorer-to-latest-version"></a>將 Cloud Explorer 更新至最新版本

Visual Studio 2017 視覺化工作室安裝程式的雲資源管理器元件僅支援監控設備到雲和雲到設備的消息。 要使用 Visual Studio 2017，請下載並安裝最新的[雲資源管理器](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.CloudExplorerForVS)。

## <a name="sign-in-to-access-your-hub"></a>登錄以訪問您的中心

1. 在視覺化工作室中，選擇 **"查看** > **雲資源管理器**"以打開雲資源管理器。

1. 選擇"帳戶管理"圖示以顯示訂閱。

    ![帳戶管理圖示](media/iot-hub-visual-studio-cloud-device-messaging/account-management-icon.png)

1. 如果登錄到 Azure，將顯示您的帳戶。 要首次登錄到 Azure，請選擇"**添加帳戶**"。

1. 選擇要使用的 Azure 訂閱，然後選擇 **"應用**"。

1. 展開訂閱，然後展開**IoT 中心**。  在每個中心下，您可以看到該集線器的設備。 以滑鼠右鍵按一下某個裝置，以存取管理選項。

    ![管理選項](media/iot-hub-device-management-visual-studio/management-options-vs2019.png)

## <a name="direct-methods"></a>直接方法

要使用直接方法，請使用以下步驟：

1. 以滑鼠右鍵按一下您的裝置，然後選取 [叫用裝置直接方法]****。

1. 在 **"調用直接方法"** 中輸入方法名稱和有效負載，然後選擇 **"確定**"。

    結果顯示在**輸出**中。

## <a name="update-device-twin"></a>更新裝置對應項

要編輯設備孿生，請執行以下步驟：

1. 以滑鼠右鍵按一下您的裝置，然後選取 [編輯裝置對應項]****。

   將打開一個**azure-iot 設備-twin.json**檔，其中將打開設備孿生的內容。

1. 對**標記**或屬性進行一些編輯 **。** **azure-iot-device-twin.json**

1. 按 **Ctrl+S** 來更新裝置對應項。

   結果顯示在**輸出**中。

## <a name="send-cloud-to-device-messages"></a>傳送雲端到裝置訊息

若要從您的 IoT 中樞將訊息傳送到裝置，請遵循這些步驟：

1. 以滑鼠右鍵按一下您的裝置，然後選取 [Send C2D Message]**** \(傳送 C2D 訊息\)。

1. 在**發送 C2D 消息**中輸入消息，然後選擇 **"確定**"。

   結果顯示在**輸出**中。

## <a name="next-steps"></a>後續步驟

您已了解如何搭配各種管理選項來使用適用於 Visual Studio 的 Cloud Explorer。

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
