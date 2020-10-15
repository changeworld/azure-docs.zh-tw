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
ms.openlocfilehash: 60a62733a17d1a3dcc4ba80ed7ceb1c37c8ac5d6
ms.sourcegitcommit: fbb620e0c47f49a8cf0a568ba704edefd0e30f81
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91876627"
---
## <a name="create-the-azure-iot-edge-gateway-device"></a>建立 Azure IoT Edge 閘道裝置

影片分析 - 物件和動作偵測應用程式包含 **LVA Edge 物件偵測器**裝置範本和 **LVA Edge 動作偵測**裝置範本。 在本節中，您會使用部署資訊清單來建立閘道裝置範本，並將閘道裝置新增至您的 IoT Central 應用程式。

### <a name="create-a-device-template-for-the-lva-edge-gateway"></a>建立 LVA Edge 閘道的裝置範本

若要匯入部署資訊清單並建立 **LVA Edge 閘道**裝置範本：

1. 在 IoT Central 應用程式中，瀏覽至 [裝置範本]，然後選取 [+ 新增]。

1. 在 [選取範本類型] 頁面上，選取 [Azure IoT Edge] 圖格。 然後，選取 **[下一步：** 自訂]。

1. 在 [上傳 Azure IoT Edge 部署資訊清單] 頁面上，輸入「LVA Edge 閘道」作為範本名稱，然後核取 [具有下游裝置的閘道裝置]。

    請還不要瀏覽部署資訊清單。 如果您這樣做，部署精靈會預期每個模組都要有介面，但您只需要公開 **LvaEdgeGatewayModule**的介面。 您會在稍後的步驟中上傳資訊清單。

    :::image type="content" source="./media/iot-central-video-analytics-part3/upload-deployment-manifest.png" alt-text="請勿上傳部署資訊清單":::

    完成時，選取 下一步: **:** 。

1. 在 [檢閱]  頁面上，選取 [建立]  。

### <a name="import-the-device-capability-model"></a>匯入裝置功能模型

裝置範本必須包含裝置功能模型。 在 [LVA Edge 閘道] 頁面上，選取 [匯入功能模型] 磚。 瀏覽至您先前建立的 lva-configuration 資料夾，然後選取 LvaEdgeGatewayDcm.json 檔案。

**LVA Edge 閘道**裝置範本現在包含 **LVA Edge 閘道模組**和三個介面：**裝置資訊**、**LVA Edge 閘道設定**及 **LVA Edge 閘道介面**。
