---
title: Visual Studio Cloud Explorer 的 Azure IoT 裝置管理
description: 使用適用於 Visual Studio 的 Cloud Explorer 來管理 Azure IoT 中樞裝置，並採用直接方法和對應項所需的屬性管理選項。
author: shizn
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/20/2019
ms.author: xshi
ms.openlocfilehash: 6fe5a45dda6632c56b3c6714827950e25e7d26af
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
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

裝置對應項是存放包括中繼資料、設定和條件此類裝置狀態資訊的 JSON 文件。 IoT 中樞會為其連線的每個裝置保存裝置對應項。 如需裝置對應項的詳細資訊，請參閱[開始使用裝置對應項](iot-hub-node-node-twin-getstarted.md)。

## <a name="what-you-learn"></a>您學到什麼

在本文中，您將瞭解如何在開發電腦上使用適用于 Visual Studio 的 Cloud Explorer 搭配各種管理選項。

## <a name="what-you-do"></a>您要做什麼

在本文中，請使用各種管理選項來執行 Visual Studio 的 Cloud Explorer。

## <a name="what-you-need"></a>必要條件

您必須符合下列必要條件：

- 有效的 Azure 訂用帳戶。

- 位於您訂用帳戶中的 Azure IoT 中樞。

- Microsoft Visual Studio 2017 Update 9 或更新版本。 本文使用 [Visual Studio 2017 或 Visual Studio 2019](https://www.visualstudio.com/vs/)。

- 從預設選取的 Azure 工作負載 Visual Studio 安裝程式 Cloud Explorer 元件。

## <a name="update-cloud-explorer-to-latest-version"></a>將 Cloud Explorer 更新至最新版本

Visual Studio 2017 的 Visual Studio 安裝程式 Cloud Explorer 元件僅支援監視裝置到雲端和雲端到裝置的訊息。 若要使用 Visual Studio 2017，請下載並安裝最新的 [Cloud Explorer](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.CloudExplorerForVS)。

## <a name="sign-in-to-access-your-hub"></a>登入以存取您的中樞

1. 在 Visual Studio 中，選取 [ **View**]  >  **Cloud Explorer**開啟 Cloud Explorer。

1. 選取 [帳戶管理] 圖示，以顯示您的訂用帳戶。

    ![帳戶管理圖示](media/iot-hub-visual-studio-cloud-device-messaging/account-management-icon.png)

1. 如果您已登入 Azure，則會顯示您的帳戶。 若要第一次登入 Azure，請選擇 [ **新增帳戶**]。

1. 選取您要使用的 Azure 訂用帳戶，然後選擇 [套用 **]。**

1. 展開您的訂用帳戶，然後展開 [ **IoT 中樞**]。  在每個中樞下，您可以看到該中樞的裝置。 以滑鼠右鍵按一下某個裝置，以存取管理選項。

    ![管理選項](media/iot-hub-device-management-visual-studio/management-options-vs2019.png)

## <a name="direct-methods"></a>直接方法

若要使用直接方法，請執行下列步驟：

1. 以滑鼠右鍵按一下您的裝置，然後選取 [叫用裝置直接方法]****。

1. 在 [叫用 **直接方法**] 中輸入方法名稱和承載，然後選取 **[確定]**。

    結果會顯示在 **輸出**中。

## <a name="update-device-twin"></a>更新裝置對應項

若要編輯裝置對應項，請執行下列步驟：

1. 以滑鼠右鍵按一下您的裝置，然後選取 [編輯裝置對應項]****。

   檔案 ** 上的azure-iot-device-twin.js** 隨即開啟，其中包含裝置對應項的內容。

1. 對標籤或屬性進行一些編輯 **。需要** **azure-iot-device-twin.js** **檔案的欄位**。

1. 按 **Ctrl+S** 來更新裝置對應項。

   結果會顯示在 **輸出**中。

## <a name="send-cloud-to-device-messages"></a>傳送雲端到裝置訊息

若要從您的 IoT 中樞將訊息傳送到裝置，請遵循這些步驟：

1. 以滑鼠右鍵按一下您的裝置，然後選取 [Send C2D Message]**** \(傳送 C2D 訊息\)。

1. 在 [ **傳送 C2D] 訊息** 中輸入訊息，然後選取 **[確定]**。

   結果會顯示在 **輸出**中。

## <a name="next-steps"></a>後續步驟

您已了解如何搭配各種管理選項來使用適用於 Visual Studio 的 Cloud Explorer。

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
