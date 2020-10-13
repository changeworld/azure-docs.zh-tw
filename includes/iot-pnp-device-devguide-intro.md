---
author: dominicbetts
ms.author: dobett
ms.service: iot-pnp
ms.topic: include
ms.date: 09/24/2020
ms.openlocfilehash: 706e936590f5f47ee2989bf77b3caad67a8f28fc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91579646"
---
IoT 隨插即用可讓您建立智慧型裝置，以將其功能公告至 Azure IoT 應用程式。 當客戶將裝置連線到已啟用 IoT 隨插即用的應用程式時，IoT 隨插即用裝置不需要手動設定。

智慧型裝置可能會直接實作為、使用 [模組](../articles/iot-hub/iot-hub-devguide-module-twins.md)，或使用 [IoT Edge 模組](../articles/iot-edge/about-iot-edge.md)。

本指南說明建立遵循 [IoT 隨插即用慣例](../articles/iot-pnp/concepts-convention.md)之裝置、模組或 IoT Edge 模組所需的基本步驟。

若要建立 IoT 隨插即用裝置、模組或 IoT Edge 模組，請遵循下列步驟：

1. 確定您的裝置使用 MQTT 或 MQTT over Websocket 通訊協定來連線到 Azure IoT 中樞。
1. 建立 [數位 Twins 定義語言 (DTDL) ](https://github.com/Azure/opendigitaltwins-dtdl) 模型來描述您的裝置。 若要深入瞭解，請參閱 [瞭解 IoT 隨插即用模型中的元件](../articles/iot-pnp/concepts-components.md)。
1. 更新您的裝置或模組，以宣告 `model-id` 為裝置連線的一部分。
1. 使用[IoT 隨插即用慣例](../articles/iot-pnp/concepts-convention.md)來執行遙測、屬性和命令

當您的裝置或模組實施就緒之後，請使用 [Azure IoT explorer](../articles/iot-pnp/howto-use-iot-explorer.md) 來驗證裝置是否遵循 IoT 隨插即用慣例。
