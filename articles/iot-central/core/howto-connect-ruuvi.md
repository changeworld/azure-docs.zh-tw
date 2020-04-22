---
title: 在 Azure IoT 中心連接 RuuviTag |微軟文件
description: 瞭解如何將 RuuviTag 環境感測器連接到 IoT 中心應用。
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
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/22/2020
ms.locfileid: "81758949"
---
# <a name="connect-a-ruuvitag-sensor-to-your-azure-iot-central-application"></a>將 RuuviTag 感應器連接到 Azure IoT 中心應用程式

*本文適用於解決方案生成器和設備開發人員。*

本文介紹作為解決方案生成器,如何將 RuuviTag 感測器連接到 Microsoft Azure IoT 中心應用程式。

什麼是魯維標籤?

RuuviTag 是一個先進的開源感測器信標平臺,旨在滿足商業客戶、開發人員、製造商、學生和業餘愛好者的需求。 設備設置為在您從開箱即用後立即工作,並準備好在您需要的地方部署該設備。 它是一個藍牙LE信標,內置了環境感測器和加速度計。

RuuviTag 通過 BLE(藍牙低功耗)進行通信,並且需要網路設備才能與 Azure IoT 中心通訊。 請確保您有閘道裝置(如Rigado級聯500)設置,以使RuuviTag能夠連接到IoT中心。

如果您想設置里加多級聯 500 閘道裝置,請按照[此處的說明](./howto-connect-rigado-cascade-500.md)操作。

## <a name="prerequisites"></a>Prerequisites

要連線 RuuviTag 感應器,您需要以下資源:

* 魯維塔格感測器。 欲瞭解更多資訊,請造[訪 魯維塔格](https://ruuvi.com/)。
* 里加多級聯 500 設備或其他 BLE 閘道。 欲瞭解更多資訊,請造[訪 里加多](https://www.rigado.com/)。
* Azure IoT Central 應用程式。 有關詳細資訊,請參閱[建立新應用程式](./quick-deploy-iot-central.md)。

## <a name="add-a-ruuvitag-device-template"></a>新增 RuuviTag 裝置樣本

要將 RuuviTag 感測器連接到 Azure IoT 中央應用程式實例中,需要在應用程式中配置相應的設備範本。

要新增 RuuviTag 裝置樣本:

1. 瀏覽到左方視窗格中的 ***「裝置樣本***」選項卡 **+ New**,選擇![「新增 」...](./media/howto-connect-ruuvi/devicetemplate-new.png)建立新裝置範本 頁面為您提供***建立自訂樣本***或使用***預先設定裝置範本***選項
1. 從預先設定的裝置樣本清單中選擇 RuuviTag 裝置樣本,如下所示![: 選擇 RuuviTag 裝置樣本](./media/howto-connect-ruuvi/devicetemplate-preconfigured.png)
1. 選擇 ***「下一步」:自定義***以繼續下一步。
1. 在下一個螢幕上,選擇 ***「創建***」以將 C500 裝置樣本納入 IoT 中央應用程式。

## <a name="connect-a-ruuvitag-sensor"></a>連線 RuuviTag 感應器

如前所述,要將 RuuviTag 連接到 IoT 中央應用程式,您需要設置閘道裝置。 以下步驟假定您已設置了里加多級聯 500 閘道設備。  

1. 開啟 Rigado 的 G- 500 裝置電源並將連線到網路連線(透過乙太網路或無線)
1. 將 RuuviTag 的蓋彈出並拉取塑膠卡舌,以固定與電池的連接。
1. 將 RuuviTag 放置在 IoT 中央應用程式中已配置的 Rigado 級聯 500 閘道附近。
1. 幾秒鐘後,您的 RuuviTag 應顯示在 IoT Central 中的設備清單中。  
    ![魯維塔格裝置清單](./media/howto-connect-ruuvi/ruuvi-devicelist.png)

您現在可以在 IoT 中央應用程式中使用此 RuuviTag。  

## <a name="create-a-simulated-ruuvitag"></a>建立模擬 RuuviTag

如果沒有物理 RuuviTag 設備,則可以創建類比 RuuviTag 感測器,用於在 Azure IoT 中央應用程式中進行測試。

要建立類比 RuuviTag:

1. 選擇 **「魯維塔格」 >设备**。
1. 選取 [+ 新增]  。
1. 指定唯一**的裝置識別碼**與易易的**裝置名稱**。  
1. 啟用**模擬**設置。
1. 選取 [建立]  。  

## <a name="next-steps"></a>後續步驟

如果您是設備開發人員,建議的後續步驟是:

- 閱讀[有關 Azure IoT 中心中的裝置連線](./concepts-get-connected.md)
- 瞭解如何使用[Azure CLI 監視裝置連線](./howto-monitor-devices-azure-cli.md)
