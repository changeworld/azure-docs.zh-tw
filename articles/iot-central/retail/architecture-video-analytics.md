---
title: Azure IoT Central 影片分析物件和動作偵測 |Microsoft Docs
description: 瞭解如何使用 IoT Central 中的影片分析-物件和動作偵測應用程式範本來建立 IoT Central 應用程式。 此範本使用即時影片分析和連線的攝影機。
author: KishorIoT
ms.author: nandab
ms.date: 07/27/2020
ms.topic: conceptual
ms.service: iot-central
ms.subservice: iot-central-retail
services: iot-central
ms.openlocfilehash: 087c5e5716a4c8c640faebd7470905fde85d0136
ms.sourcegitcommit: bfeae16fa5db56c1ec1fe75e0597d8194522b396
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/10/2020
ms.locfileid: "88038212"
---
# <a name="video-analytics---object-and-motion-detection-application-architecture"></a>影片分析-物件和動作偵測應用程式架構

**影片分析-物件和動作偵測**應用程式範本可讓您建立 IoT 解決方案，包括即時影片分析功能。

:::image type="content" source="media/architecture-video-analytics/architecture.png" alt-text="架構":::

影片分析解決方案的主要元件包括：

## <a name="live-video-analytics-lva"></a>即時影片分析 (LVA) 

LVA 提供一個平臺，讓您建立橫跨邊緣和雲端的智慧型影片應用程式。 平臺可讓您建立跨越邊緣和雲端的智慧型影片應用程式。 此平臺可讓您在 Azure 服務中捕捉、記錄、分析直播影片，並將結果（可能是影片或影片分析）發佈到 Azure 服務。 Azure 服務可能正在雲端或邊緣執行。 此平台可用於搭配影片分析，增強 IoT 解決方案。

如需詳細資訊，請參閱 GitHub 上的[即時影片分析](https://github.com/Azure/live-video-analytics)。

## <a name="iot-edge-lva-gateway-module"></a>IoT Edge LVA 閘道模組

IoT Edge LVA 閘道模組會將攝影機具現化為新裝置，並使用 IoT 裝置用戶端 SDK 直接將其連接到 IoT Central。

在此參考的執行中，裝置會使用邊緣的對稱金鑰連接到解決方案。 如需有關裝置連線能力的詳細資訊，請參閱[連接到 Azure IoT Central](../core/concepts-get-connected.md)

## <a name="media-graph"></a>媒體圖表

Media graph 可讓您定義要從何處捕獲媒體、如何處理，以及在何處傳遞結果。 您可以透過連接元件或節點，以所需的方式來設定 media graph。 如需詳細資訊，請參閱 GitHub 上的[Media Graph](https://github.com/Azure/live-video-analytics/tree/master/MediaGraph) 。

## <a name="next-steps"></a>後續步驟

建議的下一個步驟是瞭解如何[在 Azure IoT Central 中建立影片分析應用程式](tutorial-video-analytics-create-app.md)。
