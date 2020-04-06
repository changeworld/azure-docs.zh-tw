---
title: 建立透明閘道裝置 - 使用 Azure IoT Edge | Microsoft Docs
description: 使用 Azure IoT Edge 裝置作為可處理來自下游裝置之資訊的透明閘道
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 04/03/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 3b3aeff595671c5f924d01599b572b6b938ef09d
ms.sourcegitcommit: 67addb783644bafce5713e3ed10b7599a1d5c151
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/05/2020
ms.locfileid: "80666670"
---
# <a name="configure-an-iot-edge-device-to-act-as-a-transparent-gateway"></a>設定 IoT Edge 裝置作為透明閘道

本文提供了有關配置 IoT Edge 設備以充當其他設備與 IoT 中心通信的透明閘道的詳細說明。 本文使用術語*IoT Edge 閘道*來引用配置為透明閘道的 IoT Edge 裝置。 有關詳細資訊,請參閱[如何將 IoT 邊緣裝置用作閘道](./iot-edge-as-gateway.md)。

>[!NOTE]
>目前狀況：
>
> * 已啟用 Edge 的裝置無法連線到 IoT Edge 閘道。
> * 下游裝置無法使用檔案上傳。

有三個常規步驟來設置成功的透明閘道連接。 本文介紹第一步:

1. **閘道需要能夠安全地連接到下游設備,接收來自下游設備的通信,並將消息路由到正確的目標。**
2. 下游設備需要具有設備標識才能使用IoT中心進行身份驗證,並知道透過閘道裝置進行通訊。 有關詳細資訊,請參閱[將下游設備驗證為 Azure IoT 中心](how-to-authenticate-downstream-device.md)。
3. 下游設備需要安全地連接到其閘道設備。 如需詳細資訊，請參閱[將下游裝置連線到 Azure IoT Edge 閘道](how-to-connect-downstream-device.md)。

設備要充當閘道,需要能夠安全地連接到其下游設備。 Azure IoT Edge 可讓您使用公開金鑰基礎結構 (PKI)，設定這些裝置之間的安全連線。 在這種情況下,我們允許下游設備連接到充當透明閘道的IoT Edge設備。 為了維護合理的安全,下游設備應確認網關設備的身份。 此標識檢查可防止您的設備連接到潛在的惡意閘道。

透明閘道方案中的下游設備可以是具有使用[Azure IoT Hub](https://docs.microsoft.com/azure/iot-hub)雲端服務創建標識的任何應用程式或平臺。 在許多情況下，這些應用程式都會使用 [Azure IoT 裝置 SDK](../iot-hub/iot-hub-devguide-sdks.md)。 就所有實際用途而言，下游裝置甚至可以是在 IoT Edge 閘道裝置本身執行的應用程式。 但是,IoT 邊緣設備不能是IoT邊緣閘道的下游。

您可以建立任何憑證基礎結構，來啟用裝置閘道拓撲所需的信任。 在本文中,我們假定與在 IoT 中心中啟用[X.509 CA 安全性](../iot-hub/iot-hub-x509ca-overview.md)的證書設置相同,該設置涉及與特定 IoT 中心(IoT 中心根 CA)關聯的 X.509 CA 證書、與此 CA 簽名的一系列證書以及 IoT Edge 設備的 CA。

![閘道憑證設定](./media/how-to-create-transparent-gateway/gateway-setup.png)

>[!NOTE]
>本文中使用的術語「根 CA」是指 PKI 證書鏈的最頂層頒發機構公共證書,不一定是聯合證書頒發機構的證書根。 在許多情況下,它實際上是一個中間的CA公共證書。

IoT Edge 安全守護程式使用 IoT Edge 裝置 CA 憑證對工作負載 CA 憑證進行簽名,該證書又為 IoT Edge 中心簽名。 閘道在連接啟動期間向下游設備顯示其伺服器證書。 下游設備檢查以確保伺服器證書是卷到根 CA 證書的證書鏈的一部分。 此過程允許下游設備確認網關來自受信任的源。 有關詳細資訊,請參閱瞭解[Azure IoT 邊緣如何使用憑證](iot-edge-certs.md)。

以下步驟將引導您完成創建證書並將其安裝在閘道正確位置的過程。 您可以使用任何電腦產生憑證，然後再將其複製到您的 IoT Edge 裝置。

## <a name="prerequisites"></a>Prerequisites

使用[生產證書](how-to-manage-device-certificates.md)配置的 Azure IoT 邊緣設備。

## <a name="deploy-edgehub-to-the-gateway"></a>將邊緣 Hub 部署到閘道

首次在設備上安裝 IoT Edge 時,只有一個系統模組自動啟動:IoT Edge 代理。 創建第一個部署後,將啟動第二個系統模組 IoT Edge 中心。

IoT Edge 中心負責接收來自下游設備的傳入消息,並將它們路由到下一個目標。 如果**EdgeHub**模組未在設備上運行,請為設備創建初始部署。 部署將顯得為空,因為您不添加任何模組,但它將確保兩個系統模塊都運行。

您可以通過在 Azure 門戶中檢查其裝置詳細資訊、在 Visual Studio 或 Visual Studio 代碼中查看裝置`iotedge list`狀態,或在裝置本身上執行命令 來檢查設備上正在執行的模組。

如果**edgeAgent**模組在沒有**邊緣Hub**模組的情況下運行,請使用以下步驟:

1. 在 Azure 入口網站中，瀏覽至您的 IoT 中樞。

2. 移至 [IoT Edge]****，並選取要作為閘道的 IoT Edge 裝置。

3. 選擇**設定模組**。

4. 選取 [下一步]  。

5. 在 [指定路由]**** 頁面中，您應該會有可將所有模組中的所有訊息傳送至 IoT 中樞的預設路由。 如果沒有，請新增下列程式碼，然後選取 [下一步]****。

   ```JSON
   {
       "routes": {
           "route": "FROM /messages/* INTO $upstream"
       }
   }
   ```

6. 在 [檢閱範本]**** 頁面中，選取 [提交]****。

## <a name="open-ports-on-gateway-device"></a>閘道上的開啟連接埠

標準 IoT Edge 裝置不需要任何入站連接即可正常工作,因為與 IoT 中心的所有通訊都是通過出站連接完成的。 閘道不同,因為它們需要接收來自其下游設備的消息。 如果防火牆位於下游設備和閘道設備之間,則還需要通過防火牆進行通信。

要使閘道方案正常工作,必須至少為來自下游設備的入站流量打開 IoT Edge 中心支援的協定之一。 支援的協定包括 MQTT、AMQP、HTTPS、WebSocket 上的 MQTT 和 WebSocket 上的 AMQP。

| 連接埠 | 通訊協定 |
| ---- | -------- |
| 8883 | MQTT |
| 5671 | AMQP |
| 443 | HTTPS <br> MQTT_WS <br> AMQP_WS |

## <a name="route-messages-from-downstream-devices"></a>從下游裝置路由傳送訊息

IoT Edge 執行階段可以路由傳送從下游裝置送來的訊息，就像路由傳送從模組送來的訊息一樣。 此功能允許您在將任何數據發送到雲之前,在閘道上運行的模組中執行分析。

目前，針對下游裝置所傳送的訊息，您進行路由傳送的方式是將它們與模組所傳送的訊息做區分。 模組所傳送的訊息全都包含名為 **connectionModuleId** 系統屬性，但下游裝置所傳送的訊息則無此屬性。 您可以使用路由的 WHERE 子句來排除任何包含該系統屬性的訊息。

下面的路由是一個示例,它將消息從任何下游設備發送到名為`ai_insights`的模組,然後`ai_insights`從 發送到 IoT 中心。

```json
{
    "routes":{
        "sensorToAIInsightsInput1":"FROM /messages/* WHERE NOT IS_DEFINED($connectionModuleId) INTO BrokeredEndpoint(\"/modules/ai_insights/inputs/input1\")",
        "AIInsightsToIoTHub":"FROM /messages/modules/ai_insights/outputs/output1 INTO $upstream"
    }
}
```

如需有關訊息路由的詳細資訊，請參閱[部署模組及建立路由](./module-composition.md#declare-routes)。

## <a name="enable-extended-offline-operation"></a>開啟延伸的離線操作

從 IoT Edge 運行時的[v1.0.4 版本](https://github.com/Azure/azure-iotedge/releases/tag/1.0.4)開始,可以配置連接到它的閘道裝置和下游設備,以便擴展離線操作。

借助此功能,本地模組或下游設備可以根據需要使用 IoT Edge 設備重新進行身份驗證,並且即使與 IoT 中心斷開連接,也可以使用消息和方法相互通訊。 如需詳細資訊，請參閱[了解適用於 IoT Edge 裝置、模組及子裝置的擴充離線功能](offline-capabilities.md)。

要啟用延伸離線功能,您需要在 IoT Edge 閘道裝置與將連接到它的下游設備之間建立父子關係。 這些步驟在[將下游設備驗證為 Azure IoT 中心](how-to-authenticate-downstream-device.md)時進行了更詳細的解釋。

## <a name="next-steps"></a>後續步驟

既然您讓 IoT Edge 裝置當作透明閘道運作，您需要將下游裝置設定為信任閘道，並將訊息傳送到該裝置。 繼續將[下游設備驗證為 Azure IoT 中心](how-to-authenticate-downstream-device.md),以便執行設置透明閘道方案的後續步驟。
