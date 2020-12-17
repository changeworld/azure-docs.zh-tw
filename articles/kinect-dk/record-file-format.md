---
title: 使用 Azure Kinect 感應器 SDK 記錄檔案格式
description: 瞭解如何使用 Azure Kinect 感應器 SDK 記錄的檔案格式。
author: xthexder
ms.author: jawirth
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: reference
keywords: kinect、azure、感應器、sdk、深度、rgb、記錄、播放、matroska、.mkv
ms.openlocfilehash: f4fa14b0841cb76b2ba191310ecbca312d29f805
ms.sourcegitcommit: ad677fdb81f1a2a83ce72fa4f8a3a871f712599f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97654586"
---
# <a name="use-azure-kinect-sensor-sdk-to-record-file-format"></a>使用 Azure Kinect 感應器 SDK 記錄檔案格式

若要記錄感應器資料，會使用 Matroska (. .mkv) 容器格式，讓您可以使用各種編解碼器來儲存多個曲目。 錄製檔案包含儲存色彩、深度、IR 影像和 IMU 的追蹤。

您可以在 [Matroska 網站](https://www.matroska.org/index.html)上找到 .mkv 容器格式的低層級詳細資料。

| 追蹤名稱 | 編解碼器格式                          |
|------------|---------------------------------------|
| 顏色      | Mode-Dependent (MJPEG、NV12 或 YUY2)  |
| DEPTH      | b16g (16 位灰階、位元組由大到小)    |
| IR         | b16g (16 位灰階、位元組由大到小)    |
| Imu        | 自訂結構，請參閱下面的 [IMU 範例結構](record-file-format.md#imu-sample-structure) 。 |

## <a name="using-third-party-tools"></a>使用協力廠商工具

您 `ffmpeg` 可以使用 MKVToolNix 工具組中的工具（例如或 `mkvinfo` 命令）來查看和解壓縮記錄檔中的資訊。 [](https://mkvtoolnix.download/)

例如，下列命令會將深度軌解壓縮為相同資料夾的16位 Png 序列：

```
ffmpeg -i output.mkv -map 0:1 -vsync 0 depth%04d.png
```

`-map 0:1`參數將會解壓縮追蹤索引1，大部分的錄音將會是深度。 如果錄製不包含色彩軌，則會 `-map 0:0` 使用。

`-vsync 0`參數會強制 ffmpeg 依原樣解壓縮框架，而不是嘗試比對 30 fps、15 fps 或 5 fps 的畫面播放速率。

## <a name="imu-sample-structure"></a>IMU 範例結構

如果 IMU 資料是從檔案解壓縮，而不使用播放 API，則資料會採用二進位格式。
IMU 資料的結構如下。 所有欄位都是位元組由小到小。

| 欄位                        | 類型     |
|------------------------------|----------|
| 加速計 (μs) 的時間戳記 | uint64   |
| 加速資料 (x、y、z)  | float [3] |
| 陀螺儀 Timestamp (μs)      | uint64   |
| 陀螺儀資料 (x、y、z)      | float [3] |

## <a name="identifying-tracks"></a>識別追蹤

可能需要找出哪些曲目包含色彩、深度、IR 等。 使用協力廠商工具來讀取 Matroska 檔案時，需要識別這些追蹤。
追蹤號碼會根據相機模式和已啟用的曲目集而有所不同。 標記是用來識別每個追蹤的意義。

下列標記清單分別附加至特定的 Matroska 專案，可用來查詢對應的曲目或附件。

這些標記可透過和之類的工具來查看 `ffmpeg` `mkvinfo` 。
標記的完整清單會列在 [ [記錄和播放](record-playback-api.md) ] 頁面上。

| 標記名稱             | 標記目標             | 標記值             |
|----------------------|------------------------|-----------------------|
| K4A_COLOR_TRACK      | 色彩軌            | Matroska 追蹤 UID    |
| K4A_DEPTH_TRACK      | 深度追蹤            | Matroska 追蹤 UID    |
| K4A_IR_TRACK         | IR 追蹤               | Matroska 追蹤 UID    |
| K4A_IMU_TRACK        | IMU Track              | Matroska 追蹤 UID    |
| K4A_CALIBRATION_FILE | 校正附件 | 附件檔案名   |

## <a name="next-steps"></a>後續步驟

[錄製和播放](record-playback-api.md)
