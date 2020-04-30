---
title: 連接 Azure IoT Central 中的 RuuviTag |Microsoft Docs
description: 瞭解如何將 RuuviTag 環境感應器連線到 IoT Central 應用程式。
services: iot-central
ms.service: iot-central
ms.topic: how-to
ms.custom:
- iot-storeAnalytics-conditionMonitor
- iot-p0-scenario
ms.author: avneets
author: avneet723
ms.date: 11/27/2019
ms.openlocfilehash: 93e4d3d0bed9090573d2b6ee87a29b86ccd72e42
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81758949"
---
# <a name="connect-a-ruuvitag-sensor-to-your-azure-iot-central-application"></a>將 RuuviTag 感應器連線到您的 Azure IoT Central 應用程式

*本文適用於解決方案建置人員和裝置開發人員。*

本文說明如何以解決方案產生器的形式，將 RuuviTag 感應器連線至 Microsoft Azure IoT Central 應用程式。

什麼是 Ruuvi 標記？

RuuviTag 是一個先進的開放原始碼感應器指標平臺，專為滿足企業客戶、開發人員、製造商、學生和業餘人士的需求而設計。 當您將裝置從其方塊中取出時，就會設定為可運作，並可讓您在需要的地方進行部署。 這是具有內建環境感應器和加速計的藍牙 LE 信號。

RuuviTag 會透過 BLE （藍牙低功耗）進行通訊，並要求閘道裝置與 Azure IoT Central 交談。 請確定您有閘道裝置，例如 Rigado Cascade 500，安裝程式可讓 RuuviTag 連線到 IoT Central。

如果您想要設定 Rigado Cascade 500 閘道裝置，請遵循[這裡的指示](./howto-connect-rigado-cascade-500.md)。

## <a name="prerequisites"></a>先決條件

若要連接 RuuviTag 感應器，您需要下列資源：

* RuuviTag 感應器。 如需詳細資訊，請造訪[RuuviTag](https://ruuvi.com/)。
* Rigado Cascade 500 裝置或其他 BLE 閘道。 如需詳細資訊，請造訪[Rigado](https://www.rigado.com/)。
* Azure IoT Central 應用程式。 如需詳細資訊，請參閱[建立新的應用程式](./quick-deploy-iot-central.md)。

## <a name="add-a-ruuvitag-device-template"></a>新增 RuuviTag 裝置範本

若要將 RuuviTag 感應器上線至您的 Azure IoT Central 應用程式實例，您必須在應用程式中設定對應的裝置範本。

若要新增 RuuviTag 裝置範本：

1. 流覽至左窗格中的 [***裝置範本***] 索引標籤，選取![[ **+ 新增**]](./media/howto-connect-ruuvi/devicetemplate-new.png) ： [建立新的裝置範本] 頁面可讓您選擇***建立自訂範本***，或***使用預先設定的裝置範本***
1. 從預先設定的裝置範本清單中選取 [RuuviTag 裝置] 範本，如下![所示：選取 [RuuviTag] [裝置範本]](./media/howto-connect-ruuvi/devicetemplate-preconfigured.png)
1. 選取 ***[下一步]： [自訂***] 繼續進行下一個步驟。
1. 在下一個畫面上，選取 [***建立***]，將 C500 裝置範本上架到 IoT Central 應用程式中。

## <a name="connect-a-ruuvitag-sensor"></a>連接 RuuviTag 感應器

如先前所述，若要將 RuuviTag 與 IoT Central 應用程式連線，您需要設定閘道裝置。 下列步驟假設您已設定 Rigado Cascade 500 閘道裝置。  

1. 開啟 Rigado Cascade 500 裝置的電源，並將它連線到您的網路連線（透過 Ethernet 或無線）
1. 彈出 RuuviTag 的封面，並提取 [塑膠] 索引標籤，以保護與電池的連線。
1. 將 RuuviTag 放在您的 IoT Central 應用程式中已設定的 Rigado Cascade 500 閘道附近。
1. 只有幾秒鐘的時間，您的 RuuviTag 應該會出現在 IoT Central 內的裝置清單中。  
    ![RuuviTag 裝置清單](./media/howto-connect-ruuvi/ruuvi-devicelist.png)

您現在可以在 IoT Central 應用程式中使用此 RuuviTag。  

## <a name="create-a-simulated-ruuvitag"></a>建立模擬 RuuviTag

如果您沒有實體 RuuviTag 裝置，您可以建立模擬 RuuviTag 感應器，以用於在 Azure IoT Central 應用程式內進行測試。

若要建立模擬 RuuviTag：

1. 選取 [**裝置] > RuuviTag**。
1. 選取 [+ 新增]  。
1. 指定唯一的**裝置識別碼**和易記的**裝置名稱**。  
1. 啟用**模擬**設定。
1. 選取 [建立]  。  

## <a name="next-steps"></a>後續步驟

如果您是裝置開發人員，建議的後續步驟如下：

- 閱讀[Azure IoT Central 中的裝置連線能力](./concepts-get-connected.md)
- 瞭解如何[使用 Azure CLI 監視裝置連線能力](./howto-monitor-devices-azure-cli.md)
