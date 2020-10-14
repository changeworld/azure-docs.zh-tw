---
title: 建立透明閘道裝置 - 使用 Azure IoT Edge | Microsoft Docs
description: 使用 Azure IoT Edge 裝置作為可處理來自下游裝置之資訊的透明閘道
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 08/12/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: ae01fc2ef8761305c2096904471ce75b69d1150d
ms.sourcegitcommit: 2e72661f4853cd42bb4f0b2ded4271b22dc10a52
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/14/2020
ms.locfileid: "92048401"
---
# <a name="configure-an-iot-edge-device-to-act-as-a-transparent-gateway"></a>設定 IoT Edge 裝置作為透明閘道

本文提供詳細的指示，說明如何設定 IoT Edge 裝置作為透明閘道，讓其他裝置與 IoT 中樞進行通訊。 本文使用 *IoT Edge 閘道* 的詞彙來參考設定為透明閘道的 IoT Edge 裝置。 如需詳細資訊，請參閱 [如何使用 IoT Edge 裝置作為閘道](./iot-edge-as-gateway.md)。

>[!NOTE]
>目前狀況：
>
> * 已啟用 Edge 的裝置無法連線到 IoT Edge 閘道。
> * 下游裝置無法使用檔案上傳。

設定成功的透明閘道連線有三個一般步驟。 本文涵蓋第一個步驟：

1. **將閘道裝置設定為伺服器，讓下游裝置可以安全地連接到該裝置。設定閘道以接收來自下游裝置的訊息，並將其路由傳送至適當的目的地。**
2. 建立下游裝置的裝置身分識別，使其可以向 IoT 中樞進行驗證。 設定下游裝置以透過閘道裝置傳送訊息。 如需詳細資訊，請參閱 [驗證下游裝置以 Azure IoT 中樞](how-to-authenticate-downstream-device.md)。
3. 將下游裝置連線到閘道裝置，並開始傳送訊息。 如需詳細資訊，請參閱[將下游裝置連線到 Azure IoT Edge 閘道](how-to-connect-downstream-device.md)。

若要讓裝置作為閘道，則必須安全地連接到其下游裝置。 Azure IoT Edge 可讓您使用公開金鑰基礎結構 (PKI)，設定這些裝置之間的安全連線。 在此情況下，我們將允許下游裝置連線至作為透明閘道的 IoT Edge 裝置。 為了維持合理的安全性，下游裝置應確認閘道裝置的身分識別。 此身分識別檢查可防止您的裝置連線至可能是惡意的閘道。

下游裝置可以是使用 [Azure IoT 中樞](../iot-hub/index.yml)雲端服務建立身分識別的任何應用程式或平台。 這些應用程式通常會使用 [Azure IoT 裝置 SDK](../iot-hub/iot-hub-devguide-sdks.md)。 下游裝置甚至可以是在 IoT Edge 閘道裝置上執行的應用程式。 不過，IoT Edge 的裝置不能是 IoT Edge 閘道的下游。

您可以建立任何憑證基礎結構，來啟用裝置閘道拓撲所需的信任。 在本文中，我們假設您要用來在 IoT 中樞啟用 [X.509 ca 安全性](../iot-hub/iot-hub-x509ca-overview.md) 的相同憑證設定，包括與特定 iot 中樞相關聯的 x.509 CA 憑證 (iot 中樞根 CA) 、一系列使用此 CA 簽署的憑證，以及 IoT Edge 裝置的 ca。

>[!NOTE]
>在這些文章中使用的 *根 CA 憑證* 一詞是指 PKI 憑證鏈的最上層公開憑證，而不一定是同步發行的憑證授權單位單位的憑證根。 在許多情況下，它實際上是中繼 CA 公開憑證。

下列步驟將逐步引導您建立憑證，並將其安裝在閘道上的適當位置。 您可以使用任何電腦產生憑證，然後再將其複製到您的 IoT Edge 裝置。

## <a name="prerequisites"></a>先決條件

已安裝 IoT Edge 的 Linux 或 Windows 裝置。

## <a name="set-up-the-device-ca-certificate"></a>設定裝置 CA 憑證

所有 IoT Edge 閘道都需要在其上安裝裝置 CA 憑證。 IoT Edge 的安全性背景程式會使用 IoT Edge 裝置 CA 憑證來簽署工作負載 CA 憑證，進而簽署 IoT Edge hub 的伺服器憑證。 閘道會在連線起始期間，將其伺服器憑證呈現給下游裝置。 下游裝置會進行檢查，以確定伺服器憑證是匯總至根 CA 憑證的憑證鏈的一部分。 此程式可讓下游裝置確認閘道來自受信任的來源。 如需詳細資訊，請參閱 [瞭解 Azure IoT Edge 如何使用憑證](iot-edge-certs.md)。

![閘道憑證設定](./media/how-to-create-transparent-gateway/gateway-setup.png)

根 CA 憑證和裝置 CA 憑證 (與其私密金鑰) 必須存在於 IoT Edge 閘道裝置上，並在 IoT Edge yaml 檔中設定。 請記住，在此案例中， *根 CA 憑證* 表示此 IoT Edge 案例的最上層憑證授權單位單位。 閘道裝置 CA 憑證和下游裝置憑證需要匯總至相同的根 CA 憑證。

>[!TIP]
>在 IoT Edge 裝置上安裝根 CA 憑證和裝置 CA 憑證的程式，也會在 [IoT Edge 裝置上的 [管理憑證](how-to-manage-device-certificates.md)] 中更詳細地說明。

備妥下列檔案：

* 根 CA 憑證
* 裝置 CA 憑證
* 裝置 CA 私密金鑰

針對生產案例，您應該使用您自己的憑證授權單位單位來產生這些檔案。 針對開發和測試案例，您可以使用示範憑證。

1. 如果您使用的是示範憑證，請使用下列一組步驟來建立您的檔案：
   1. [建立根 CA 憑證](how-to-create-test-certificates.md#create-root-ca-certificate)。 在這些指示的結尾，您將會有根 CA 憑證檔案：
      * `<path>/certs/azure-iot-test-only.root.ca.cert.pem`.

   2. [建立 IoT Edge 的裝置 CA 憑證](how-to-create-test-certificates.md#create-iot-edge-device-ca-certificates)。 在這些指示的結尾，您將會有兩個檔案，也就是裝置 CA 憑證及其私密金鑰：
      * `<path>/certs/iot-edge-device-<cert name>-full-chain.cert.pem`和
      * `<path>/private/iot-edge-device-<cert name>.key.pem`

2. 如果您在不同的電腦上建立這些檔案，請將它們複製到您的 IoT Edge 裝置。

3. 在您的 IoT Edge 裝置上，開啟安全 daemon 設定檔案。
   * Windows：`C:\ProgramData\iotedge\config.yaml`
   * Linux：`/etc/iotedge/config.yaml`

4. 尋找檔案的 **憑證** 區段，並提供檔案 uri 給您的三個檔案，作為下列屬性的值：
   * **device_ca_cert**：裝置 ca 憑證
   * **device_ca_pk**：裝置 ca 私密金鑰
   * **trusted_ca_certs**：根 ca 憑證

5. 儲存並關閉檔案。

6. 重新開機 IoT Edge。
   * Windows：`Restart-Service iotedge`
   * Linux：`sudo systemctl restart iotedge`

## <a name="deploy-edgehub-and-route-messages"></a>部署 edgeHub 和路由訊息

下游裝置會將遙測和訊息傳送至閘道裝置，其中 IoT Edge 中樞模組負責將資訊路由至其他模組或 IoT 中樞。 若要準備閘道裝置以進行此功能，請確定：

* IoT Edge 中樞模組會部署到裝置。

  當您第一次在裝置上安裝 IoT Edge 時，只會自動啟動一個系統模組： IoT Edge 代理程式。 當您建立裝置的第一個部署之後，第二個系統模組（IoT Edge 中樞）也會啟動。 如果 **edgeHub** 模組未在您的裝置上執行，請為您的裝置建立部署。

* IoT Edge 中樞模組已設定路由，以處理來自下游裝置的傳入訊息。

  閘道裝置必須要有路由來處理來自下游裝置的訊息，否則這些訊息將不會被處理。 您可以將訊息傳送至閘道裝置上的模組，或直接傳送到 IoT 中樞。

若要部署 IoT Edge hub 模組並使用路由進行設定，以處理來自下游裝置的傳入訊息，請遵循下列步驟：

1. 在 Azure 入口網站中，瀏覽至您的 IoT 中樞。

2. 移至 [IoT Edge]****，並選取要作為閘道的 IoT Edge 裝置。

3. 選取 [ **設定模組**]。

4. 在 [ **模組** ] 頁面上，您可以新增任何您想要部署到閘道裝置的模組。 基於本文的目的，我們將著重于設定和部署 edgeHub 模組，此模組不需要在此頁面上明確設定。

5. 選取 **[下一步：路由]**。

6. 在 [ **路由** ] 頁面上，確定有路由可處理來自下游裝置的訊息。 例如︰

   * 從模組或下游裝置將所有訊息傳送至 IoT 中樞的路由：
       * **名稱**：`allMessagesToHub`
       * **值**：`FROM /messages/* INTO $upstream`

   * 將所有下游裝置的所有訊息傳送到 IoT 中樞的路由：
      * **名稱**：`allDownstreamToHub`
      * **值**：`FROM /messages/* WHERE NOT IS_DEFINED ($connectionModuleId) INTO $upstream`

      此路由的運作方式是因為不同于來自 IoT Edge 模組的訊息，來自下游裝置的訊息沒有與其相關聯的模組識別碼。 使用路由的 **WHERE** 子句可讓我們篩選掉具有該系統屬性的任何訊息。

      如需有關訊息路由的詳細資訊，請參閱[部署模組及建立路由](./module-composition.md#declare-routes)。

7. 建立路由或路由之後，請選取 [ **審核 + 建立**]。

8. 在 [ **審核 + 建立** ] 頁面上，選取 [ **建立**]。

## <a name="open-ports-on-gateway-device"></a>在閘道裝置上開啟埠

標準 IoT Edge 裝置不需要任何輸入連線功能，因為所有與 IoT 中樞的通訊都是透過輸出連線來完成。 閘道裝置不同，因為它們需要從其下游裝置接收訊息。 如果防火牆是在下游裝置與閘道裝置之間，則也必須透過防火牆進行通訊。

若要讓閘道案例運作，至少必須針對來自下游裝置的輸入流量開啟其中一個 IoT Edge 中樞支援的通訊協定。 支援的通訊協定為 MQTT、AMQP、HTTPS、透過 Websocket 的 MQTT，以及透過 Websocket 的 AMQP。

| 連接埠 | 通訊協定 |
| ---- | -------- |
| 8883 | MQTT |
| 5671 | AMQP |
| 443 | HTTPS <br> MQTT + WS <br> AMQP + WS |

## <a name="enable-extended-offline-operation"></a>啟用擴充離線操作

從 IoT Edge 執行時間的 [1.0.4 版本](https://github.com/Azure/azure-iotedge/releases/tag/1.0.4) 開始，連線到該閘道的閘道裝置和下游裝置可設定為擴充離線作業。

使用這項功能時，本機模組或下游裝置可以視需要向 IoT Edge 裝置重新驗證，並使用訊息和方法彼此通訊，即使是從 IoT 中樞中斷連線時也一樣。 如需詳細資訊，請參閱[了解適用於 IoT Edge 裝置、模組及子裝置的擴充離線功能](offline-capabilities.md)。

若要啟用擴充的離線功能，您可以在 IoT Edge 閘道裝置與將連接到該裝置的下游裝置之間建立父子式關聯性。 本系列的下一篇文章會更詳細地說明這些步驟，並將 [下游裝置驗證至 Azure IoT 中樞](how-to-authenticate-downstream-device.md)。

## <a name="next-steps"></a>後續步驟

現在您已將 IoT Edge 裝置設定為透明閘道，您必須設定下游裝置以信任閘道，並將訊息傳送給它。 繼續進行 [驗證下游裝置，以 Azure IoT 中樞](how-to-authenticate-downstream-device.md) 下一步設定透明閘道案例。