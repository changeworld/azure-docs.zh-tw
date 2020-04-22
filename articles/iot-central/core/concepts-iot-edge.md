---
title: Azure IoT 邊緣和 Azure IoT 中心 |微軟文件
description: 瞭解如何將 Azure IoT 邊緣與 IoT 中央應用程式一起使用。
author: dominicbetts
ms.author: dobett
ms.date: 12/12/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 89d068fbd835a5b4f8886a2ed77691f94a701bb0
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/22/2020
ms.locfileid: "81759099"
---
# <a name="connect-azure-iot-edge-devices-to-an-azure-iot-central-application"></a>將 Azure IoT 邊緣裝置連接到 Azure IoT 中心應用程式

*本文適用於解決方案生成器和設備開發人員。*

IoT Edge 是由三個元件組成：

* **IoT Edge 模組**是執行 Azure 服務、夥伴服務或自有程式碼的容器。 這類模組會部署到 IoT Edge 裝置，並在這些裝置本機上執行。
* **IoT Edge 執行時**在每個 IoT Edge 設備上運行,並管理部署到每個設備的模組。
* **雲端式介面**可讓您在遠端監視及管理 IoT Edge 裝置。 IoT Central 是雲端介面。

**Azure IoT Edge** 裝置可以作為閘道裝置，具有連線到 IoT Edge 裝置的下游裝置。 本文分享有關下游設備連接模式的詳細資訊。

**裝置範本**會定義裝置和 IoT Edge 模組的功能。 功能包括了模組傳送的遙測資料、模組屬性，以及模組回應的命令。

## <a name="downstream-device-relationships-with-a-gateway-and-modules"></a>下游裝置與閘道和模組的關聯性

下游裝置可以透過 `$edgeHub` 模組連接到 IoT Edge 閘道裝置。 此 IoT Edge 裝置在此案例中會變成透明閘道。

![透明閘道的圖表](./media/concepts-iot-edge/gateway-transparent.png)

下游裝置也可以透過自訂模組連接到 IoT Edge 閘道裝置。 在下列案例中，下游裝置會透過 Modbus 自訂模組來連接。

![自訂模組連接的圖表](./media/concepts-iot-edge/gateway-module.png)

下圖顯示透過兩種模組類型 (自訂和 `$edgeHub`) 連接到 IoT Edge 閘道裝置的連線。  

![透過兩個連接模組連接的圖表](./media/concepts-iot-edge/gateway-module-transparent.png)

最後，下游裝置可以透過多個自訂模組連接到 IoT Edge 閘道裝置。 下圖顯示透過 Modbus 自訂模組、BLE 自訂模組和 `$edgeHub` 模組連接的下游裝置。 

![透過多個自訂模組連接的圖表](./media/concepts-iot-edge/gateway-module2-transparent.png)

## <a name="deployment-manifests-and-device-templates"></a>部署清單和裝置樣本

在 IoT Edge 中，您可以透過模組形式來部署和管理商務邏輯。 IoT Edge 模組是 IoT Edge 管理的最小計算單位，可以包含 Azure 服務 (例如 Azure 串流分析) 或您自己的解決方案特定程式碼。 若要了解如何開發、部署和維護模組，請參閱 [IoT Edge 模組](../../iot-edge/iot-edge-modules.md)。

概括而言，部署資訊清單是以其所需屬性設定的模組對應項清單。 部署資訊清單會告知 IoT Edge 裝置 (或裝置群組) 要安裝哪些模組，以及如何設定。 部署資訊清單包含每個模組對應項的所需屬性。 IoT Edge 裝置會回報每個模組的報告屬性。

請使用 Visual Studio Code 建立部署資訊清單， 若要深入了解，請參閱[適用於 Visual Studio Code 的 Azure IoT Edge](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge)。

在 Azure IoT 中心中,可以導入部署清單以創建設備範本。 下列流程圖說明 IoT Central 中的部署資訊清單生命週期。

![部署資訊清單生命週期的流程圖](./media/concepts-iot-edge/dmflow.png)

IoT 隨插即用(預覽)對 IoT 邊緣裝置進行建模,如下所示:

* 每個 IoT Edge 裝置範本都有一個裝置功能模型。
* 部署資訊清單中列出的每個自訂模組，都會產生模組功能模型。
* 系統會在每個模組功能模型和裝置功能模型之間建立關聯性。
* 模組功能模型會實作模組介面。
* 每個模組介面都包含遙測、屬性和命令。

![IoT Edge 模型的圖表](./media/concepts-iot-edge/edgemodelling.png)

## <a name="iot-edge-gateway-devices"></a>IoT 邊緣閘道裝置

如果您選取 IoT Edge 裝置作為閘道裝置，對於要連接到閘道裝置的裝置，則可以將下游關聯性新增到裝置功能模型上。

## <a name="next-steps"></a>後續步驟

如果您是裝置開發人員,建議的下一步是瞭解[IoT 中央中的閘道裝置類型](./tutorial-define-gateway-device-type.md)。
