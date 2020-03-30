---
title: 在 Azure IoT 中心連接 RuuviTag |微軟文檔
description: 瞭解如何將 RuuviTag 環境感應器連接到 IoT 中心應用。
services: iot-central
ms.service: iot-central
ms.topic: how-to
ms.custom:
- iot-storeAnalytics-conditionMonitor
- iot-p0-scenario
ms.author: avneets
author: avneet723
ms.date: 11/27/2019
ms.openlocfilehash: e8d1c4a605e8db2e9753bb80c9712dd6c2be7b59
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80158218"
---
# <a name="connect-a-ruuvitag-sensor-to-your-azure-iot-central-application"></a>將 RuuviTag 感應器連接到 Azure IoT 中心應用程式

本文介紹作為解決方案產生器，如何將 RuuviTag 感應器連接到 Microsoft Azure IoT 中心應用程式。

什麼是魯維標籤？

RuuviTag 是一個先進的開源感應器信標平臺，旨在滿足商業客戶、開發人員、製造商、學生和業餘愛好者的需求。 設備設置為在您從開箱即用後立即工作，並準備好在您需要的地方部署該設備。 它是一個藍牙LE信標，內置了環境感應器和加速度計。

RuuviTag 通過 BLE（藍牙低功耗）進行通信，並且需要閘道設備才能與 Azure IoT 中心通信。 請確保您有閘道設備（如 Rigado 級聯 500）設置，以使 RuuviTag 能夠連接到 IoT 中心。

如果您想設置里加多級聯 500 閘道設備，請按照[此處的說明](./howto-connect-rigado-cascade-500.md)操作。

## <a name="prerequisites"></a>Prerequisites

要連接 RuuviTag 感應器，您需要以下資源：

* 魯維塔格感應器。 欲瞭解更多資訊，請訪問[魯維塔格](https://ruuvi.com/)。
* 里加多級聯 500 設備或其他 BLE 閘道。 欲瞭解更多資訊，請訪問[里加多](https://www.rigado.com/)。
* Azure IoT Central 應用程式。 有關詳細資訊，請參閱[創建新應用程式](./quick-deploy-iot-central.md)。

## <a name="add-a-ruuvitag-device-template"></a>添加 RuuviTag 設備範本

要將 RuuviTag 感應器連接到 Azure IoT 中央應用程式實例中，需要在應用程式中配置相應的設備範本。

要添加 RuuviTag 設備範本：

1. 導航到左側窗格中的 ***"設備範本***"選項卡，選擇 **"** 新建![："創建新設備範本](./media/howto-connect-ruuvi/devicetemplate-new.png)頁面為您提供***創建自訂範本***或使用***預配置設備範本***的選項
1. 從預配置的設備範本清單中選擇 RuuviTag 設備範本，如下所示：![選擇 RuuviTag 設備範本](./media/howto-connect-ruuvi/devicetemplate-preconfigured.png)
1. 選擇 ***"下一步"：自訂***以繼續下一步。
1. 在下一個螢幕上，選擇 ***"創建***"以將 C500 設備範本納入 IoT 中央應用程式。

## <a name="connect-a-ruuvitag-sensor"></a>連接 RuuviTag 感應器

如前所述，要將 RuuviTag 連接到 IoT 中央應用程式，您需要設置閘道設備。 以下步驟假定您已設置了里加多級聯 500 閘道設備。  

1. 打開 Rigado 級聯 500 設備電源並將其連接到網路連接（通過乙太網或無線）
1. 將 RuuviTag 的蓋彈出並拉取塑膠卡舌，以固定與電池的連接。
1. 將 RuuviTag 放置在 IoT 中央應用程式中已配置的 Rigado 級聯 500 閘道附近。
1. 幾秒鐘後，您的 RuuviTag 應顯示在 IoT Central 中的設備清單中。  
    ![魯維塔格設備清單](./media/howto-connect-ruuvi/ruuvi-devicelist.png)

您現在可以在 IoT 中央應用程式中使用此 RuuviTag。  

## <a name="create-a-simulated-ruuvitag"></a>創建類比 RuuviTag

如果沒有物理 RuuviTag 設備，則可以創建類比 RuuviTag 感應器，用於在 Azure IoT 中央應用程式中進行測試。

要創建類比 RuuviTag：

1. 選擇 **"魯維塔格">設備**。
1. 選取 [+ 新增]****。
1. 指定唯一**的裝置識別碼**和友好的**設備名稱**。  
1. 啟用**類比**設置。
1. 選取 [建立]****。  

## <a name="next-steps"></a>後續步驟

現在，您已經瞭解如何將 RuuviTag 連接到 Azure IoT 中央應用程式，建議的下一步是瞭解如何[自訂 IoT 中央應用程式](../retail/tutorial-in-store-analytics-customize-dashboard-pnp.md)以構建端到端解決方案。
