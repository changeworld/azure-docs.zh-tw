---
title: 使用 Azure Kinect 感應器 SDK 記錄檔案格式
description: 瞭解如何使用 Azure Kinect 感應器 SDK 錄製的檔案格式。
author: xthexder
ms.author: jawirth
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: reference
keywords: kinect，azure，感應器，sdk，深度，rgb，記錄，播放，matroska，.mkv
ms.openlocfilehash: d0f7653afe3cc92e059b2615ebef18312faa716b
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85276485"
---
# <a name="use-azure-kinect-sensor-sdk-to-record-file-format"></a>使用 Azure Kinect 感應器 SDK 記錄檔案格式

若要記錄感應器資料，會使用 Matroska （. .mkv）容器格式，讓您可以儲存多個追蹤。
使用廣泛的編解碼器。 記錄檔包含儲存色彩、深度、IR 影像和 IMU 的追蹤。

您可以在[Matroska 網站](https://www.matroska.org/index.html)上找到 .mkv 容器格式的低層級詳細資料。

| 追蹤名稱 | 編解碼器格式                          |
|------------|---------------------------------------|
| 顏色      | 模式相依（MJPEG、NV12 或 YUY2） |
| DEPTH      | b16g （16位灰階，位元組由大到小）   |
| IR         | b16g （16位灰階，位元組由大到小）   |
| IMU        | 自訂結構，請參閱下面的[IMU 範例結構](record-file-format.md#imu-sample-structure)。 |

## <a name="using-third-party-tools"></a>使用協力廠商工具

像是 `ffmpeg` 或來自 MKVToolNix 工具組的 `mkvinfo` 命令，可以用來從記錄檔中查看和解壓縮資訊。 [MKVToolNix](https://mkvtoolnix.download/)

例如，下列命令會將深度追蹤以16位 Png 的序列解壓縮至相同的資料夾：

```
ffmpeg -i output.mkv -map 0:1 -vsync 0 depth%04d.png
```

`-map 0:1`參數將會解壓縮追蹤索引1，而大部分的錄製內容都是深度。 如果錄製不包含彩色播放軌，則會 `-map 0:0` 使用。

`-vsync 0`參數會強制 ffmpeg 將框架依目前方式解壓縮，而不會嘗試比對 30 fps、15 fps 或 5 fps 的畫面播放速率。

## <a name="imu-sample-structure"></a>IMU 範例結構

如果未使用播放 API 從檔案解壓縮 IMU 資料，資料將會採用二進位格式。
IMU 資料的結構如下所示。 所有欄位都是以位元組為小。

| 欄位                        | 類型     |
|------------------------------|----------|
| 加速計時間戳記（μs） | uint64   |
| 加速計資料（x、y、z） | float [3] |
| 陀螺儀時間戳記（μs）     | uint64   |
| 陀螺儀資料（x、y、z）     | float [3] |

## <a name="identifying-tracks"></a>識別追蹤

可能需要識別哪一個追蹤包含色彩、深度、IR 等等。 使用協力廠商工具來讀取 Matroska 檔案時，需要識別追蹤。
追蹤數位會根據相機模式和一組啟用的曲目而有所不同。 標記是用來識別每個追蹤的意義。

下列標記清單會分別附加至特定的 Matroska 元素，並可用來查閱對應的追蹤或附件。

這些標記可透過和之類的工具來查看 `ffmpeg` `mkvinfo` 。
標記的完整清單會列在 [[記錄和播放](record-playback-api.md)] 頁面上。

| 標記名稱             | 標記目標             | 標記值             |
|----------------------|------------------------|-----------------------|
| K4A_COLOR_TRACK      | 色彩追蹤            | Matroska 追蹤 UID    |
| K4A_DEPTH_TRACK      | 深度追蹤            | Matroska 追蹤 UID    |
| K4A_IR_TRACK         | IR 追蹤               | Matroska 追蹤 UID    |
| K4A_IMU_TRACK        | IMU 追蹤              | Matroska 追蹤 UID    |
| K4A_CALIBRATION_FILE | 校正附件 | 附件檔案名   |

## <a name="next-steps"></a>後續步驟

[錄製和播放](record-playback-api.md)
