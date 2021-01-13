---
title: 如何將裝置移至 IoT 中樞的 Azure IoT Central
description: 如何從 IoT 中樞將裝置移至 Azure IoT Central
author: TheRealJasonAndrew
ms.author: v-anjaso
ms.date: 12/20/2020
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 7898f842529b81b80febff444c97b199fbebba3c
ms.sourcegitcommit: 431bf5709b433bb12ab1f2e591f1f61f6d87f66c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/12/2021
ms.locfileid: "98146440"
---
# <a name="how-to-transfer-a-device-to-azure-iot-central-from-iot-hub"></a>如何從 IoT 中樞將裝置傳輸至 Azure IoT Central

*本文適用于操作員和裝置開發人員。*  

本文說明如何從 IoT 中樞將裝置傳輸至 Azure IoT Central 應用程式。 

裝置會先連線至 DPS 端點，以取得連線到應用程式所需的資訊。 就內部而言，您的 IoT Central 應用程式會使用 IoT 中樞來處理裝置連線能力。  

您可以使用連接字串或 DPS，直接將裝置連線到 IoT 中樞。 [Azure IoT 中樞裝置布建服務 (DPS) ](../../iot-dps/about-iot-dps.md) 是 IoT Central 的路由。

## <a name="to-move-the-device"></a>移動裝置

若要從 IOT 中樞將裝置連線到 IoT Central，必須使用下列內容更新裝置：

* IoT Central 應用程式的 [範圍識別碼](../../iot-dps/concepts-service.md) 。
* 從[群組 SAS](concepts-get-connected.md)金鑰或[x.509](../../iot-hub/iot-hub-x509ca-overview.md)憑證衍生的金鑰

若要與 IoT Central 互動，必須有裝置範本來建立裝置所要執行之屬性/遙測/命令的模型。 如需詳細資訊，請參閱 [連接到 IoT Central](concepts-get-connected.md) 以及 [什麼是裝置範本？](concepts-device-templates.md)

## <a name="next-steps"></a>後續步驟

如果您是裝置開發人員，則建議的後續步驟如下：

- 請參閱[教學課程：建立用戶端應用程式並將其連接到您的 Azure IoT Central 應用程式](tutorial-connect-device.md)，以瞭解如何使用 SAS 權杖的一些範例程式碼
- 瞭解如何 [使用適用于 IoT Central 應用程式 Node.js 裝置 SDK，將裝置連線到 x.509 憑證](how-to-connect-devices-x509.md)
- 了解如何[使用 Azure CLI 監視裝置連線能力](./howto-monitor-devices-azure-cli.md)
- 瞭解如何 [在您的 Azure IoT Central 應用程式中定義新的 IoT 裝置類型](./howto-set-up-template.md)
- 閱讀[Azure IoT Edge 裝置和 Azure IoT Central 的](./concepts-iot-edge.md)相關資訊