---
author: wesmc7777
ms.author: wesmc
ms.service: iot-hub
ms.topic: include
ms.date: 08/20/2019
ms.openlocfilehash: c412f7f3e20e4d04083e457bfb245b850b65e126
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "70050466"
---
> [!div class="op_single_selector"]
> * [Node.js](../articles/iot-hub/iot-hub-node-node-twin-getstarted.md)
> * [.NET](../articles/iot-hub/iot-hub-csharp-csharp-twin-getstarted.md)
> * [JAVA](../articles/iot-hub/iot-hub-java-java-twin-getstarted.md)
> * [Python](../articles/iot-hub/iot-hub-python-twin-getstarted.md)

裝置對應項是存放裝置狀態資訊的 JSON 文件，包括中繼資料、設定和條件。 IoT 中樞會為其連線的每個裝置保存裝置對應項。

[!INCLUDE [iot-hub-basic](iot-hub-basic-whole.md)]

使用裝置對應項可以：

* 從您的解決方案後端儲存裝置中繼資料。

* 從設備應用中報告目前狀態資訊，如可用功能和條件，例如所使用的連接方法。

* 在設備應用和後端應用之間同步長時間運行的工作流的狀態，如固件和配置更新。

* 查詢裝置中繼資料、組態或狀態。

裝置對應項是設計來進行同步處理和查詢裝置組態和條件。 如需何時使用裝置對應項的詳細資訊，請參閱[了解裝置對應項](../articles/iot-hub/iot-hub-devguide-device-twins.md)。

設備孿生存儲在 IoT 中心中，包含以下元素：

* **標記**。 設備中繼資料只能由解決方案後端訪問。

* **所需屬性**。 JSON 物件可按解決方案後端進行修改，並且可按設備應用觀察。

* **報告屬性**。 JSON 物件可按設備應用進行修改，並且可按解決方案後端讀取。

標籤和屬性不能包含陣列，但物件可以是巢狀的。

下圖顯示了設備孿生組織：

![顯示功能的裝置對應項影像](./media/iot-hub-selector-twin-get-started/twin.png)

此外，解決方案後端可以根據上述的所有資料查詢裝置對應項。
有關設備孿生的詳細資訊，請參閱[瞭解設備孿生](../articles/iot-hub/iot-hub-devguide-device-twins.md)。 有關查詢的詳細資訊，請參閱[IoT 中心查詢語言](../articles/iot-hub/iot-hub-devguide-query-language.md)。


本教學課程說明如何：

* 建立後端應用程式，將「標籤」新增至裝置對應項，以及建立模擬裝置應用程式，以裝置對應項上的「報告屬性」來報告其連線通道。

* 使用先前建立的標籤和屬性上的篩選器，從您的後端應用程式查詢裝置。
