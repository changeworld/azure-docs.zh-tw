---
title: 包含檔案
description: 包含檔案
services: iot-central
author: dominicbetts
ms.service: iot-central
ms.topic: include
ms.date: 10/06/2020
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: 0b3ccc31c9159b5d7b1615add89e8fdc308bf8df
ms.sourcegitcommit: d6e92295e1f161a547da33999ad66c94cf334563
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/07/2020
ms.locfileid: "96763395"
---
### <a name="publish-the-device-template"></a>發佈裝置範本

您必須先發佈裝置範本，才能將裝置新增至應用程式：

1. 在 **LVA Edge 閘道 v2** 裝置範本中，選取 [發佈]。

1. 在 [將此裝置範本發佈至應用程式] 頁面上，選取 [發佈]。

**LVA Edge 閘道 v2** 現在可作為裝置類型，以在應用程式中的 [裝置] 頁面上使用。

## <a name="migrate-the-gateway-device"></a>遷移閘道裝置

現有的 **gateway-001** 裝置使用 **LVA Edge 閘道** 裝置範本。 若要使用新的部署資訊清單，請將裝置遷移至新的裝置範本：

若要遷移 **gateway-001** 裝置：

1. 瀏覽至 [裝置] 頁面，然後選取 [gateway-001] 裝置以在清單中醒目提示。

1. 選取 [移轉]。 如果看不到 [遷移] 圖示，請選取 [...] 以查看更多選項。

    :::image type="content" source="media/iot-central-video-analytics-part4/migrate-device.png" alt-text="將閘道裝置遷移至新版本":::

1. 在 [遷移] 對話方塊上的清單中，選取 [LVA Edge 閘道 v2]，然後選取 [遷移]。

幾秒之後，就會完成遷移。 您的裝置現在會使用 **LVA Edge 閘道 v2** 裝置範本搭配自訂的部署資訊清單。

現在沒有使用原始 **LVA Edge 閘道** 裝置範本的裝置。 刪除此裝置範本：

1. 瀏覽至 [裝置範本] 頁面，然後選取 [LVA Edge 閘道] 裝置範本。

1. 選取 [刪除] 以刪除裝置範本。

### <a name="get-the-device-credentials"></a>取得裝置認證

您需要可讓裝置連線到您 IoT Central 應用程式的認證。 若要取得裝置認證：

1. 在 [裝置] 頁面上，選取您建立的 **gateway-001** 裝置。

1. 選取 [連接]。

1. 在 [裝置連線] 頁面上，記下 scratchpad.txt 檔案中的 [識別碼範圍]、[裝置識別碼] 和裝置的 [主要金鑰]。 您稍後會用到這些值。

1. 請確定連線方法已設定為 [共用存取簽章]。

1. 選取 [關閉]。

## <a name="next-steps"></a>後續步驟

您現在已使用 **影片分析 - 物件和動作偵測** 應用程式範本建立了 IoT Central 應用程式、建立了閘道裝置的裝置範本，以及將閘道裝置新增至應用程式。

如果您想要使用執行雲端 VM 且具有模擬影片串流的 IoT Edge 模組，來試用影片分析 - 物件和動作偵測應用程式：

> [!div class="nextstepaction"]
> [建立適用於影片分析的 IoT Edge 執行個體 (Linux VM)](../articles/iot-central/retail/tutorial-video-analytics-iot-edge-vm.md)

如果您想要使用執行真實裝置且具有真實 **ONVIF** 相機的 IoT Edge 模組，來試用影片分析 - 物件和動作偵測應用程式：

> [!div class="nextstepaction"]
> [建立適用於影片分析的 IoT Edge 執行個體 (Intel NUC)](../articles/iot-central/retail/tutorial-video-analytics-iot-edge-nuc.md)
