---
title: IoT Edge 版本資訊的即時影片分析-Azure
description: 本主題提供有關 IoT Edge 版本、增強功能、bug 修正和已知問題的即時影片分析版本資訊。
ms.topic: conceptual
ms.date: 08/19/2020
ms.openlocfilehash: e33a62891f9503a4f2ff907585316c3737c876e6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91250465"
---
# <a name="live-video-analytics-on-iot-edge-release-notes"></a>IoT Edge 版本資訊的即時影片分析

>將 URL `https://docs.microsoft.com/api/search/rss?search=%22Live+Video+Analytics+on+IoT+Edge+release+notes%22&locale=en-us` 複製並貼到 RSS 摘要閱讀程式中，以獲知何時該重新造訪此頁面來取得最新消息。

本文提供以下相關資訊：

* 最新版本
* 已知問題
* 錯誤修正
* 已被取代的功能

<hr width=100%>

## <a name="september-22-2020"></a>2020 年 9 月 22 日

此課程模組的2020年9月更新版本戳記為：

```
mcr.microsoft.com/media/live-video-analytics:1.0.4
```

> [!NOTE]
> 在快速入門和教學課程中，部署資訊清單會使用 1 (即時影片分析： 1) 的標記。 因此只要重新部署這類資訊清單，就會更新您的 edge > 裝置上的模組。

### <a name="module-updates"></a>模組更新

* 新的圖形擴充功能節點， [MediaGraphCognitiveServicesVisionExtension](spatial-analysis-tutorial.md) 可與認知服務中的 [空間分析](https://docs.microsoft.com/legal/cognitive-services/computer-vision/intro-to-spatial-analysis-public-preview) (預覽版) 模組整合。
* 已新增 Linux ARM64 裝置的支援-使用 [手動步驟](deploy-iot-edge-device.md) 部署到這類裝置。

### <a name="documentation-updates"></a>文件更新

* 您可以在 Azure Stack Edge 裝置上的 IoT Edge 上使用即時影片分析的[指示](deploy-azure-stack-edge-how-to.md)。
* 新的教學課程，說明如何使用 [自訂視覺服務](https://azure.microsoft.com/services/cognitive-services/custom-vision-service/) 來開發案例特定的電腦視覺模型，並使用它即時 [分析即時影片](custom-vision-tutorial.md) 。

<hr width=100%>

## <a name="august-19-2020"></a>2020年8月19日

此版本戳記適用于2020年8月的更新模組：

```
mcr.microsoft.com/media/live-video-analytics:1.0.3
```

> [!NOTE]
> 在快速入門和教學課程中，部署資訊清單會使用 1 (即時影片分析： 1) 的標記。 因此只要重新部署這類資訊清單，就會更新您的 edge > 裝置上的模組。

### <a name="module-updates"></a>模組更新

* 您現在可以使用 gRPC 架構，在 IoT Edge 的即時影片分析和您的自訂擴充功能之間取得高資料內容傳輸效能。 若 [要](analyze-live-video-use-your-grpc-model-quickstart.md) 開始使用，請參閱。
* 更廣泛的即時影片分析區域部署，且僅更新雲端服務。  
* 即時影片分析現已在全球25個額外的區域推出。 以下是所有可用區域的 [清單](https://azure.microsoft.com/global-infrastructure/services/?products=media-services) 。  
* 針對快速入門 [設定的設定](https://aka.ms/lva-edge/setup-resources-for-samples) 也會隨著新區域的支援而更新。
    * 任何已設定資源的人都不會呼叫動作

### <a name="bug-fixes"></a>錯誤修正 

* 移除在設定腳本中使用已被取代的 azure 延伸模組

<hr width=100%>

## <a name="july-13-2020"></a>2020年7月13日

此課程模組的2020年7月更新版本戳記為：

```
mcr.microsoft.com/media/live-video-analytics:1.0.2
```

> [!NOTE]
> 在快速入門和教學課程中，部署資訊清單會使用 1 (即時影片分析： 1) 的標記。 因此只要重新部署這類資訊清單，就會更新您的 edge > 裝置上的模組。

### <a name="module-updates"></a>模組更新

* 您現在可以建立具有資產接收器節點的圖形拓撲，以及信號閘道處理器節點下游的 file sink 節點。 如 [需範例，請參閱](https://github.com/Azure/live-video-analytics/tree/master/MediaGraph/topologies/evr-motion-assets-files) 。

### <a name="bug-fixes"></a>錯誤修正

* 對所需屬性驗證的改進

<hr width=100%>

## <a name="june-1-2020"></a>2020 年 6 月 1 日

此版本是 IoT Edge 上即時影片分析的第一個公開預覽版本。 發行標記為

```
     mcr.microsoft.com/media/live-video-analytics:1.0.0
```

### <a name="supported-functionalities"></a>支援的功能

* 使用您選擇的 AI 模組分析即時影片串流，並選擇性地在 edge 裝置或雲端中錄製影片
* 在 IoT Edge [支援](../../iot-edge/support.md) 的 Linux AMD64 作業系統上使用
* 使用 Azure 入口網站或 Visual Studio Code 透過 IoT 中樞部署及設定模組
* 透過下列直接方法呼叫，在遠端或本機管理[圖形拓撲和圖形實例](media-graph-concept.md#media-graph-topologies-and-instances)

    *   GraphTopologyGet
    *   GraphTopologySet
    *   GraphTopologyDelete
    *   GraphTopologyList
    *   GraphInstanceGet
    *   GraphInstanceSet
    *   GraphInstanceDelete
    *   GraphInstanceList

## <a name="next-steps"></a>後續步驟

[概觀](overview.md)
