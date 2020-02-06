---
title: 連接 Azure IoT Central 中的 Rigado Cascade 500 |Microsoft Docs
description: 瞭解如何將 Rigado Cascade 500 閘道裝置連線到您的 IoT Central 應用程式。
services: iot-central
ms.service: iot-central
ms.topic: conceptual
ms.custom:
- iot-storeAnalytics-conditionMonitor
- iot-p0-scenario
ms.author: avneets
author: avneet723
ms.date: 11/27/2019
ms.openlocfilehash: bd96d2b9f2220c4eecb653e0764c381235c62157
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/05/2020
ms.locfileid: "77026922"
---
# <a name="connect-a-rigado-cascade-500-gateway-device-to-your-azure-iot-central-application"></a>將 Rigado Cascade 500 閘道裝置連線到您的 Azure IoT Central 應用程式


本文說明如何以解決方案產生器的形式，將 Rigado Cascade 500 閘道裝置連線到您的 Microsoft Azure IoT Central 應用程式。 

## <a name="what-is-cascade-500"></a>什麼是 Cascade 500？

Cascade 500 IoT 閘道是 Rigado 的硬體供應專案，隨附于其串聯式邊緣即服務解決方案中。 它為商業 IoT 專案和產品小組提供彈性的邊緣運算能力、強大的容器化應用程式環境，以及各種不同的無線裝置連線選項，包括藍牙5、LTE、& 的 Wi-fi。

Cascade 500 是預先認證的 Azure IoT 隨插即用（預覽），可讓我們的解決方案產生器輕鬆地將裝置上線至其端對端解決方案。 Cascade 閘道可讓您以無線方式連線到與閘道裝置鄰近的各種條件監視感應器。 這些感應器可以透過閘道裝置上架到 IoT Central。

## <a name="prerequisites"></a>必要條件
若要逐步執行本作法指南，您需要下列資源：

* Rigado Cascade 500 裝置。 如需詳細資訊，請造訪[Rigado](https://www.rigado.com/)。
* Azure IoT Central 應用程式。 如需詳細資訊，請參閱[建立新的應用程式](./quick-deploy-iot-central.md)。

## <a name="add-a-device-template"></a>新增裝置範本

為了將串聯500閘道裝置上線至您的 Azure IoT Central 應用程式實例，您必須在應用程式中設定對應的裝置範本。

若要新增 Cascade 500 裝置範本： 

1. 流覽至左窗格中的 [***裝置範本***] 索引標籤，選取 **+ 新增**： ![建立新的裝置範本](./media/howto-connect-rigado-cascade-500/device-template-new.png)
1. 此頁面可讓您選擇***建立自訂範本***，或***使用預先設定的裝置範本***
1. 從預先設定的裝置範本清單中選取 [C500 裝置] 範本，如下所示： ![選取 [C500] [裝置範本]](./media/howto-connect-rigado-cascade-500/device-template-preconfigured.png)
1. 選取 ***[下一步]： [自訂***] 繼續進行下一個步驟。 
1. 在下一個畫面上，選取 [***建立***]，將 C500 裝置範本上架到 IoT Central 應用程式中。

## <a name="retrieve-application-connection-details"></a>取得應用程式連線詳細資料

您現在必須取得 Azure IoT Central 應用程式的**範圍識別碼**和**主要金鑰**，才能連接 Cascade 500 裝置。 

1. 流覽至左窗格中的 [系統**管理**]，然後按一下 [**裝置**連線]。 
2. 記下 IoT Central 應用程式的**範圍識別碼**。
![應用程式範圍識別碼](./media/howto-connect-rigado-cascade-500/app-scope-id.png)
3. 現在，按一下 [ **View Keys** ]，並記下**主要金鑰**
![主鍵](./media/howto-connect-rigado-cascade-500/primary-key-sas.png)  

## <a name="contact-rigado-to-connect-the-gateway"></a>聯絡 Rigado 以連接閘道 

若要將 Cascade 500 裝置連線到 IoT Central 應用程式，您必須聯絡 Rigado，並提供上述步驟中的應用程式連線詳細資料。 

一旦裝置連線到網際網路，Rigado 就能夠透過安全通道向下推至串聯500閘道裝置。 

此更新將會在 Cascade 500 裝置上套用 IoT Central 連線詳細資料，且會出現在您的裝置清單中。 

![主索引鍵](./media/howto-connect-rigado-cascade-500/devices-list-c500.png)  

您現在已準備好在 IoT Central 應用程式中使用您的 C500 裝置！

## <a name="next-steps"></a>後續步驟

既然您已瞭解如何將 Rigado Cascade 500 連線到您的 Azure IoT Central 應用程式，建議的下一個步驟是瞭解如何[建立存放庫內的分析應用程式](../retail/tutorial-in-store-analytics-create-app-pnp.md)，以建立端對端解決方案。 