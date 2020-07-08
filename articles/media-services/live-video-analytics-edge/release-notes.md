---
title: IoT Edge 版本資訊的即時影片分析-Azure
description: 本主題提供有關 IoT Edge 版本、改良功能、bug 修正和已知問題的即時影片分析版本資訊。
ms.topic: conceptual
ms.date: 04/27/2020
ms.openlocfilehash: fd607ee027b46cf386180338440e6530da911302
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84260321"
---
# <a name="live-video-analytics-on-iot-edge-release-notes"></a>關於 IoT Edge 版本資訊的即時影片分析

>將 URL `https://docs.microsoft.com/api/search/rss?search=%22Live+Video+Analytics+on+IoT+Edge+release+notes%22&locale=en-us` 複製並貼到 RSS 摘要閱讀程式中，以獲知何時該重新造訪此頁面來取得最新消息。

本文提供以下相關資訊：

* 最新版本
* 已知問題
* 錯誤修正
* 已被取代的功能

## <a name="june-1-2020"></a>2020 年 6 月 1 日

此版本是 IoT Edge 上即時影片分析的第一個公開預覽版本。 發行標記為

```
     mcr.microsoft.com/media/live-video-analytics:1.0.0
```

### <a name="supported-functionalities"></a>支援的功能
* 使用您選擇的 AI 模組來分析即時影片串流，並選擇性地將影片錄製到 edge 裝置或雲端
* 在 IoT Edge[支援](https://docs.microsoft.com/azure/iot-edge/support)的 Linux AMD64 作業系統上使用
* 使用 Azure 入口網站或 Visual Studio Code 透過 IoT 中樞部署和設定模組
* 透過下列直接方法呼叫，從遠端或本機管理[圖形拓撲和圖形實例](media-graph-concept.md#media-graph-topologies-and-instances)

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
