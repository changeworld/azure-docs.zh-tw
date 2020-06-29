---
author: wesmc7777
ms.author: wesmc
ms.service: iot-hub
ms.topic: include
ms.date: 08/20/2019
ms.openlocfilehash: c412f7f3e20e4d04083e457bfb245b850b65e126
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "70050466"
---
> [!div class="op_single_selector"]
> * [Node.js](../articles/iot-hub/iot-hub-node-node-twin-getstarted.md)
> * [.NET](../articles/iot-hub/iot-hub-csharp-csharp-twin-getstarted.md)
> * [Java](../articles/iot-hub/iot-hub-java-java-twin-getstarted.md)
> * [Python](../articles/iot-hub/iot-hub-python-twin-getstarted.md)

裝置對應項是存放包括中繼資料、設定和條件此類裝置狀態資訊的 JSON 文件。 IoT 中樞會為其連線的每個裝置保存裝置對應項。

[!INCLUDE [iot-hub-basic](iot-hub-basic-whole.md)]

使用裝置對應項可以：

* 從您的解決方案後端儲存裝置中繼資料。

* 從裝置應用程式報告目前狀態資訊，例如可用功能和狀況 (例如，使用的連線方法)。

* 同步處理裝置應用程式與後端應用程式之間，長時間執行的工作流程狀態 (例如韌體和設定更新)。

* 查詢裝置中繼資料、組態或狀態。

裝置對應項是設計來進行同步處理和查詢裝置組態和條件。 如需何時使用裝置對應項的詳細資訊，請參閱[了解裝置對應項](../articles/iot-hub/iot-hub-devguide-device-twins.md)。

裝置對應項儲存在 IoT 中樞，且包含下列項目︰

* **標籤**。 只有解決方案後端可存取裝置中繼資料。

* **所需屬性**。 JSON 物件可由解決方案後端修改，並依裝置應用程式查看。

* **報告屬性**。 JSON 物件可由裝置應用程式修改，並由解決方案後端讀取。

標籤和屬性不能包含陣列，但物件可以是巢狀的。

下圖顯示裝置對應項的組織：

![顯示功能的裝置對應項影像](./media/iot-hub-selector-twin-get-started/twin.png)

此外，解決方案後端可以根據上述的所有資料查詢裝置對應項。
如需裝置對應項的詳細資訊，請參閱[了解裝置對應項](../articles/iot-hub/iot-hub-devguide-device-twins.md)。 如需查詢的詳細資訊，請參閱 [IoT 中樞查詢語言](../articles/iot-hub/iot-hub-devguide-query-language.md)。


本教學課程說明如何：

* 建立後端應用程式，將標籤新增至裝置對應項，以及建立模擬裝置應用程式，將其連線通道報告為裝置對應項上的報告屬性。

* 使用先前建立的標籤和屬性上的篩選器，從您的後端應用程式查詢裝置。
