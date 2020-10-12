---
title: 連接 Azure IoT Central 中的 Rigado Cascade 500 |Microsoft Docs
description: 瞭解如何將 Rigado 串聯500閘道裝置連線到您的 IoT Central 應用程式。
services: iot-central
ms.service: iot-central
ms.topic: how-to
ms.custom:
- iot-storeAnalytics-conditionMonitor
- iot-p0-scenario
ms.author: avneets
author: avneet723
ms.date: 11/27/2019
ms.openlocfilehash: 0000e7690ab92f469a7417e82cb375c524e0b343
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "90016838"
---
# <a name="connect-a-rigado-cascade-500-gateway-device-to-your-azure-iot-central-application"></a>將 Rigado 串聯500閘道裝置連線到您的 Azure IoT Central 應用程式

*本文適用於解決方案建置人員。*

本文說明如何以解決方案產生器的方式，將 Rigado 串聯500閘道裝置連線至 Microsoft Azure IoT Central 應用程式。 

## <a name="what-is-cascade-500"></a>什麼是 Cascade 500？

串聯 500 IoT 閘道是 Rigado 中的硬體供應專案，隨附于其串聯 Edge 即服務解決方案中。 它供應商業 IoT 專案和產品小組彈性的邊緣運算能力、穩固的容器化應用程式環境，以及各種不同的無線裝置連線選項，包括 Bluetooth 5、LTE & Wi-fi。

串聯500經預先認證，適用于 Azure IoT 隨插即用 (preview) 讓解決方案產生器輕鬆地將裝置上線至其端對端解決方案。 串聯網關可讓您以無線方式連線到與閘道裝置相近的各種狀況監控感應器。 您可以透過閘道裝置將這些感應器上線至 IoT Central。

## <a name="prerequisites"></a>必要條件
若要逐步執行本作法指南，您需要下列資源：

* Rigado 500 級的裝置。 如需詳細資訊，請造訪 [Rigado](https://www.rigado.com/)。
* Azure IoT Central 應用程式。 如需詳細資訊，請參閱 [建立新的應用程式](./quick-deploy-iot-central.md)。

## <a name="add-a-device-template"></a>新增裝置範本

為了將串聯500閘道裝置上線至 Azure IoT Central 應用程式實例，您必須在應用程式中設定對應的裝置範本。

新增 Cascade 500 裝置範本： 

1. 流覽至左窗格中的 [ ***裝置範本*** ] 索引標籤，選取 [ **+ 新增**： ![ 建立新的裝置範本]](./media/howto-connect-rigado-cascade-500/device-template-new.png)
1. 此頁面可讓您選擇 ***建立自訂範本*** ，或 ***使用預先設定的裝置範本***
1. 從預先設定的裝置範本清單中選取 C500 裝置範本，如下所示： ![ 選取 C500 裝置範本](./media/howto-connect-rigado-cascade-500/device-template-preconfigured.png)
1. 選取 ***[下一步：自訂]*** 繼續進行下一個步驟。 
1. 在下一個畫面中，選取 [ ***建立*** ] 以將 C500 裝置範本上線至 IoT Central 應用程式。

## <a name="retrieve-application-connection-details"></a>取出應用程式連接詳細資料

您現在需要取出 Azure IoT Central 應用程式的 **範圍識別碼** 和 **主要金鑰** ，才能連接串聯500裝置。 

1. 流覽至左窗格中的 [系統 **管理**  ]，然後按一下 [ **裝置**連線]。 
2. 記下您的 IoT Central 應用程式的 **範圍識別碼** 。
![應用程式範圍識別碼](./media/howto-connect-rigado-cascade-500/app-scope-id.png)
3. 現在，按一下 [**視圖鍵**] 並記下**主鍵** 
 ![ 主要金鑰](./media/howto-connect-rigado-cascade-500/primary-key-sas.png)  

## <a name="contact-rigado-to-connect-the-gateway"></a>聯絡 Rigado 以連接閘道 

為了將串聯500裝置連線到您的 IoT Central 應用程式，您必須聯絡 Rigado 並提供上述步驟中的應用程式連線詳細資料。 

裝置連線到網際網路之後，Rigado 就能夠透過安全通道向下推送設定更新至串聯500閘道裝置。 

此更新會將 IoT Central 連線詳細資料套用至串聯500裝置，而且會出現在您的裝置清單中。 

![裝置清單](./media/howto-connect-rigado-cascade-500/devices-list-c500.png)  

您現在已準備好在 IoT Central 應用程式中使用您的 C500 裝置！

## <a name="next-steps"></a>後續步驟

如果您是裝置開發人員，則建議的後續步驟如下：

- 閱讀 [Azure IoT Central 中的裝置連線能力](./concepts-get-connected.md)
- 了解如何[使用 Azure CLI 監視裝置連線能力](./howto-monitor-devices-azure-cli.md)
