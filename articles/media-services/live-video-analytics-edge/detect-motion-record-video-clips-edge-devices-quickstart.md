---
title: 在邊緣裝置上偵測動作及錄製影片 - Azure
description: 本快速入門說明如何在 IoT Edge 上使用 Live Video Analytics，從 (模擬的) IP 攝影機分析即時影片摘要、偵測是否有任何動作，如果有，則將 MP4 視訊剪輯錄製到邊緣裝置上的本機檔案系統。
ms.topic: quickstart
ms.date: 04/27/2020
zone_pivot_groups: ams-lva-edge-programming-languages
ms.openlocfilehash: e4334cdd14242337a2a870f31886606020654685
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/02/2020
ms.locfileid: "96498348"
---
# <a name="quickstart-detect-motion-and-record-video-on-edge-devices"></a>快速入門：在邊緣裝置上偵測動作及錄製影片
 
本快速入門說明如何在 IoT Edge 上使用 Live Video Analytics，從 (模擬) IP 攝影機分析即時影片摘要。 其會示範如何偵測是否有任何動作存在，如果是的話，則會將 MP4 影片剪輯錄製到邊緣裝置上的本機檔案系統中。 快速入門會使用 Azure VM 作為 IoT Edge 裝置，以及使用模擬的即時影片串流。 

::: zone pivot="programming-language-csharp"
[!INCLUDE [header](includes/detect-motion-record-video-clips-edge-devices-quickstart/csharp/header.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [header](includes/detect-motion-record-video-clips-edge-devices-quickstart/python/header.md)]
::: zone-end

## <a name="prerequisites"></a>先決條件

::: zone pivot="programming-language-csharp"
[!INCLUDE [prerequisites](includes/detect-motion-record-video-clips-edge-devices-quickstart/csharp/prerequisites.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [prerequisites](includes/detect-motion-record-video-clips-edge-devices-quickstart/python/prerequisites.md)]
::: zone-end

## <a name="review-the-sample-video"></a>檢閱範例影片

::: zone pivot="programming-language-csharp"
[!INCLUDE [review-sample-video](includes/detect-motion-record-video-clips-edge-devices-quickstart/csharp/review-sample-video.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [review-sample-video](includes/detect-motion-record-video-clips-edge-devices-quickstart/python/review-sample-video.md)]
::: zone-end

## <a name="overview"></a>概觀

::: zone pivot="programming-language-csharp"
[!INCLUDE [overview](includes/detect-motion-record-video-clips-edge-devices-quickstart/csharp/overview.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [overview](includes/detect-motion-record-video-clips-edge-devices-quickstart/python/overview.md)]
::: zone-end

## <a name="examine-and-edit-the-sample-files"></a>查看和編輯範例檔案

::: zone pivot="programming-language-csharp"
[!INCLUDE [examine-edit-sample-files](includes/detect-motion-record-video-clips-edge-devices-quickstart/csharp/examine-edit-sample-files.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [examine-edit-sample-files](includes/detect-motion-record-video-clips-edge-devices-quickstart/python/examine-edit-sample-files.md)]
::: zone-end

## <a name="review---check-the-modules-status"></a>檢閱 - 檢查模組的狀態

::: zone pivot="programming-language-csharp"
[!INCLUDE [check-modules-status](includes/detect-motion-record-video-clips-edge-devices-quickstart/csharp/check-modules-status.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [check-modules-status](includes/detect-motion-record-video-clips-edge-devices-quickstart/python/check-modules-status.md)]
::: zone-end

## <a name="review---prepare-for-monitoring-events"></a>檢閱 - 準備監視事件

::: zone pivot="programming-language-csharp"
[!INCLUDE [prepare-monitoring-events](includes/detect-motion-record-video-clips-edge-devices-quickstart/csharp/prepare-monitoring-events.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [prepare-monitoring-events](includes/detect-motion-record-video-clips-edge-devices-quickstart/python/prepare-monitoring-events.md)]
::: zone-end

## <a name="run-the-sample-program"></a>執行範例程式

::: zone pivot="programming-language-csharp"
[!INCLUDE [run-sample-program](includes/detect-motion-record-video-clips-edge-devices-quickstart/csharp/run-sample-program.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [run-sample-program](includes/detect-motion-record-video-clips-edge-devices-quickstart/python/run-sample-program.md)]
::: zone-end

## <a name="interpret-results"></a>解譯結果 

::: zone pivot="programming-language-csharp"
[!INCLUDE [interpret-results](includes/detect-motion-record-video-clips-edge-devices-quickstart/csharp/interpret-results.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [interpret-results](includes/detect-motion-record-video-clips-edge-devices-quickstart/python/interpret-results.md)]
::: zone-end

## <a name="play-the-mp4-clip"></a>播放 MP4 剪輯

::: zone pivot="programming-language-csharp"
[!INCLUDE [play-mp4-clip](includes/detect-motion-record-video-clips-edge-devices-quickstart/csharp/play-mp4-clip.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [play-mp4-clip](includes/detect-motion-record-video-clips-edge-devices-quickstart/python/play-mp4-clip.md)]
::: zone-end

## <a name="clean-up-resources"></a>清除資源

如果您想要嘗試其他快速入門，請保留您建立的資源。 否則，請在 Azure 入口網站中移至您的資源群組、選取您執行本快速入門時所用的資源群組，然後刪除所有資源。

## <a name="next-steps"></a>後續步驟

* 遵循[使用您自己的模型執行 Live Video Analytics](use-your-model-quickstart.md) 快速入門，將 AI 應用於即時影片摘要。
* 檢閱進階使用者的其他挑戰：

    * 使用支援 RTSP 的 [IP 攝影機](https://en.wikipedia.org/wiki/IP_camera)，而非使用 RTSP 模擬器。 您可以在[符合 ONVIF 標準的](https://www.onvif.org/conformant-products)產品頁面上，尋找支援 RTSP 的 IP 攝影機。 尋找符合設定檔 G、S 或 T 的裝置。
    * 使用 AMD64 或 x64 Linux 裝置，而不是在 Azure 中使用 Linux VM。 此裝置必須與 IP 攝影機位於相同的網路中。 遵循[在 Linux 上安裝 Azure IoT Edge 執行階段](../../iot-edge/how-to-install-iot-edge.md)中的指示。 然後按照[將您的第一個 IoT Edge 模組部署至虛擬 Linux 裝置](../../iot-edge/quickstart-linux.md)中的指示操作，向 Azure IoT 中樞註冊裝置。