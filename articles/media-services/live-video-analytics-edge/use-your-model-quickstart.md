---
title: 使用您自己的 HTTP 模型分析即時影片 - Azure
description: 在本快速入門中，您將套用電腦視覺來分析來自 (模擬) IP 攝影機的即時影片摘要。
ms.topic: quickstart
ms.date: 04/27/2020
zone_pivot_groups: ams-lva-edge-programming-languages
ms.openlocfilehash: 5fd7f1a7db323e3aa7971c1ced6fc588d6e60e37
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/20/2020
ms.locfileid: "88684152"
---
# <a name="quickstart-analyze-live-video-by-using-your-own-http-model"></a>快速入門：使用您自己的 HTTP 模型分析即時影片

本快速入門說明如何在 IoT Edge 上使用 Live Video Analytics，從 (模擬) IP 攝影機分析即時影片摘要。 您將了解如何套用電腦視覺模型來偵測物件。 即時影片摘要中的框架子集會傳送至推斷服務。 結果會傳送至 IoT Edge 中樞。 

本快速入門會使用 Azure VM 作為 IoT Edge 裝置，以及使用模擬的即時影片串流。 其根據的是以 C# 撰寫的範例程式碼，並且會在[偵測動作並發出事件](detect-motion-emit-events-quickstart.md)快速入門的基礎上來建置。 

::: zone pivot="programming-language-csharp"
[!INCLUDE [header](includes/analyze-live-video-your-http-model-quickstart/csharp/header.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [header](includes/analyze-live-video-your-http-model-quickstart/python/header.md)]
::: zone-end

## <a name="prerequisites"></a>必要條件

::: zone pivot="programming-language-csharp"
[!INCLUDE [prerequisites](includes/analyze-live-video-your-http-model-quickstart/csharp/prerequisites.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [prerequisites](includes/analyze-live-video-your-http-model-quickstart/python/prerequisites.md)]
::: zone-end

## <a name="review-the-sample-video"></a>檢閱範例影片

::: zone pivot="programming-language-csharp"
[!INCLUDE [review-sample-video](includes/analyze-live-video-your-http-model-quickstart/csharp/review-sample-video.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [review-sample-video](includes/analyze-live-video-your-http-model-quickstart/python/review-sample-video.md)]
::: zone-end

## <a name="overview"></a>概觀

::: zone pivot="programming-language-csharp"
[!INCLUDE [overview](includes/analyze-live-video-your-http-model-quickstart/csharp/overview.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [overview](includes/analyze-live-video-your-http-model-quickstart/python/overview.md)]
::: zone-end

## <a name="create-and-deploy-the-media-graph"></a>建立和部署媒體圖表

::: zone pivot="programming-language-csharp"
[!INCLUDE [create-deploy-media-graph](includes/analyze-live-video-your-http-model-quickstart/csharp/create-deploy-media-graph.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [create-deploy-media-graph](includes/analyze-live-video-your-http-model-quickstart/python/create-deploy-media-graph.md)]
::: zone-end

## <a name="interpret-results"></a>解譯結果

::: zone pivot="programming-language-csharp"
[!INCLUDE [interpret-results](includes/analyze-live-video-your-http-model-quickstart/csharp/interpret-results.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [interpret-results](includes/analyze-live-video-your-http-model-quickstart/python/interpret-results.md)]
::: zone-end

## <a name="clean-up-resources"></a>清除資源

如果您想要嘗試其他快速入門，請保留您建立的資源。 否則，請移至 Azure 入口網站、移至您的資源群組、選取您執行本快速入門時所用的資源群組，然後刪除所有資源。

## <a name="next-steps"></a>後續步驟

* 試用[安全版的 YoloV3 模型](https://github.com/Azure/live-video-analytics/blob/master/utilities/video-analysis/tls-yolov3-onnx/readme.md)，並將其部署至 IoT Edge 裝置。 

檢閱進階使用者的其他挑戰：

* 使用具有 RTSP 支援的 [IP 攝影機](https://en.wikipedia.org/wiki/IP_camera)，而非使用 RTSP 模擬器。 您可以在[符合 ONVIF 標準的](https://www.onvif.org/conformant-products/)產品頁面上，搜尋支援 RTSP 的 IP 攝影機。 尋找符合設定檔 G、S 或 T 的裝置。
* 使用 AMD64 或 x64 Linux 裝置 (而非使用 Azure Linux VM)。 此裝置必須與 IP 攝影機位於相同的網路中。 您可以遵循[在 Linux 上安裝 Azure IoT Edge 執行階段](../../iot-edge/how-to-install-iot-edge-linux.md)中的指示。 然後遵循[將您的第一個 IoT Edge 模組部署至虛擬 Linux 裝置](../../iot-edge/quickstart-linux.md)中的指示，向 Azure IoT 中樞註冊裝置。
