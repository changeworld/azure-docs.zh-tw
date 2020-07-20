---
title: Azure Kinect DK 錄製器
description: 瞭解如何使用 Azure Kinect 錄製器，將資料流程從感應器 SDK 記錄到檔案。
author: tesych
ms.author: tesych
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: kinect，錄製，播放，讀取器，matroska，.mkv，串流，深度，rgb，相機，色彩，imu，音訊
ms.openlocfilehash: 5fb6895d4102956a991c67ffab836a26b7a3abb0
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85276490"
---
# <a name="azure-kinect-dk-recorder"></a>Azure Kinect DK 錄製器

本文涵蓋如何使用 `k4arecorder` 命令列公用程式，將來自感應器 SDK 的資料流程記錄到檔案。

>[!NOTE]
>Azure Kinect 錄製器不會錄製音訊。

## <a name="recorder-options"></a>錄製器選項

`k4arecorder`具有各種命令列引數，可指定輸出檔和錄製模式。

錄製會以[Matroska. .mkv 格式](record-file-format.md)儲存。 錄製會使用多個影片軌來取得色彩和深度，以及其他資訊，例如相機校正和中繼資料。

```console
k4arecorder [options] output.mkv

 Options:
  -h, --help              Prints this help
  --list                  List the currently connected K4A devices
  --device                Specify the device index to use (default: 0)
  -l, --record-length     Limit the recording to N seconds (default: infinite)
  -c, --color-mode        Set the color sensor mode (default: 1080p), Available options:
                            3072p, 2160p, 1536p, 1440p, 1080p, 720p, 720p_NV12, 720p_YUY2, OFF
  -d, --depth-mode        Set the depth sensor mode (default: NFOV_UNBINNED), Available options:
                            NFOV_2X2BINNED, NFOV_UNBINNED, WFOV_2X2BINNED, WFOV_UNBINNED, PASSIVE_IR, OFF
  --depth-delay           Set the time offset between color and depth frames in microseconds (default: 0)
                            A negative value means depth frames will arrive before color frames.
                            The delay must be less than 1 frame period.
  -r, --rate              Set the camera frame rate in Frames per Second
                            Default is the maximum rate supported by the camera modes.
                            Available options: 30, 15, 5
  --imu                   Set the IMU recording mode (ON, OFF, default: ON)
  --external-sync         Set the external sync mode (Master, Subordinate, Standalone default: Standalone)
  --sync-delay            Set the external sync delay off the master camera in microseconds (default: 0)
                            This setting is only valid if the camera is in Subordinate mode.
  -e, --exposure-control  Set manual exposure value (-11 to 1) for the RGB camera (default: auto exposure)
```

## <a name="record-files"></a>記錄檔

範例 1. Record Depth NFOV 包含尚未拋棄（640x576）模式，RGB 1080p 為 30 fps with IMU。
按**ctrl-c**鍵停止錄製。

```
k4arecorder.exe output.mkv
```

範例 2. 記錄 WFOV 非分類收納（1MP），RGB 3072p 為 15 fps，不含 IMU，10秒。

```
k4arecorder.exe -d WFOV_UNBINNED -c 3072p -r 15 -l 10 --imu OFF output.mkv
```

範例 3. 記錄 WFOV 2x2 分類收納 30 fps 5 秒，並儲存至輸出 .mkv。

```
k4arecorder.exe -d WFOV_2X2BINNED -c OFF --imu OFF -l 5 output.mkv
```

>[!TIP]
>您可以使用[Azure Kinect Viewer](azure-kinect-viewer.md) ，在錄製之前設定 RGB 相機控制項（例如，設定手動白色餘額）。

## <a name="verify-recording"></a>確認錄製

您可以使用[Azure Kinect Viewer](azure-kinect-viewer.md)開啟 .mkv 檔案。

若要解壓縮追蹤或查看檔案資訊，如 MKVToolNix 工具組中所提供的工具，例如 `mkvinfo` 。 [MKVToolNix](https://mkvtoolnix.download/)

## <a name="next-steps"></a>後續步驟

[搭配外部同步處理單位使用錄製器](record-external-synchronized-units.md)