---
title: 使用 Azure Kinect 錄製器搭配外部同步處理的裝置
description: 瞭解如何使用 Azure Kinect 錄製器來記錄已設定進行外部同步處理的裝置資料。
author: tesych
ms.author: tesych
ms.reviewer: jawirth
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: Kinect、感應器、檢視器、外部同步處理、階段延遲、深度、RGB、攝影機、音訊纜線、錄製器
ms.openlocfilehash: 052f6f1ac9f90e764de25d1d4d1b25b3d50a848d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "85276461"
---
# <a name="use-azure-kinect-recorder-with-external-synchronized-devices"></a>使用 Azure Kinect 錄製器搭配外部同步處理的裝置

本文提供 [Azure Kinect 錄製](azure-kinect-recorder.md) 器如何記錄資料外部同步處理設定裝置的指引。

## <a name="prerequisites"></a>Prerequisites

- [針對外部同步處理設定多個 Azure Kinect DK 單位](https://support.microsoft.com/help/4494429)。

## <a name="external-synchronization-constraints"></a>外部同步處理條件約束

- 主要裝置無法在連接纜線時進行同步處理。
- 主要裝置必須串流 RGB 攝影機才能啟用同步處理。
- 所有單位都必須使用相同的相機設定 (的畫面播放速率和解析度) 。
- 所有單位都必須執行相同的裝置固件 ([更新固件](update-device-firmware.md) 指示) 。
- 所有次級裝置都必須在主要裝置之前啟動。
- 您應該在所有裝置上設定相同的曝光值。
- 每個從屬的 *延遲主要* 設定都是相對於主要裝置。

## <a name="record-when-each-unit-has-a-host-pc"></a>記錄每個單位有主機電腦的時間

在下列範例中，每個裝置都有專屬的專用主機電腦。
建議您將裝置連線到專用電腦，以防止 USB 頻寬和 CPU/GPU 使用量發生問題。

### <a name="subordinate-1"></a>次級-1

1. 設定第一個單元的錄製器

      `k4arecorder.exe --external-sync sub -e -8 -r 5 -l 10 sub1.mkv`

2. 裝置開始等待

    ```console
    Device serial number: 000011590212
    Device version: Rel; C: 1.5.78; D: 1.5.60[6109.6109]; A: 1.5.13
    Device started
    [subordinate mode] Waiting for signal from master
    ```

### <a name="subordinate-2"></a>次級-2

1. 設定第二個單位的錄製器

    `k4arecorder.exe --external-sync sub -e -8 -r 5 -l 10 sub2.mkv`

2. 裝置開始等待

    ```console
    Device serial number: 000011590212
    Device version: Rel; C: 1.5.78; D: 1.5.60[6109.6109]; A: 1.5.13
    Device started
    [subordinate mode] Waiting for signal from master
    ```

### <a name="master"></a>Master

1. 開始在 master 上錄製

    `>k4arecorder.exe --external-sync master -e -8 -r 5 -l 10 master.mkv`

2. 等候記錄完成

## <a name="recording-when-multiple-units-connected-to-single-host-pc"></a>多個單位連接到單一主機電腦時錄製

您可以有多個 Azure Kinect DKs 連接到單一主機電腦。 不過，這可能會非常要求 USB 頻寬和主機計算。 若要減少需求：

- 將每部裝置連線到自己的 USB 主機控制器。
- 具有強大的 GPU，可處理每個裝置的深度引擎。
- 只記錄所需的感應器，並使用較低的畫面播放速率。

一律先啟動次級裝置，最後再啟動主要裝置。

## <a name="subordinate-1"></a>次級-1

1. 在次級上啟動錄製器

    `>k4arecorder.exe --device 1 --external-sync subordinate --imu OFF -e -8 -r 5 -l 5 output-2.mkv`

2. 裝置進入等候狀態

## <a name="master"></a>Master

1. 啟動主要裝置

    `>k4arecorder.exe --device 0 --external-sync master --imu OFF -e -8 -r 5 -l 5 output-1.mkv`

2. 等候錄製完成

## <a name="playing-recording"></a>播放錄製

您可以使用 [Azure Kinect 檢視器](azure-kinect-viewer.md) 播放錄製。



## <a name="tips"></a>提示

- 使用手動曝光來錄製同步攝影機。 RGB 相機自動曝光可能會影響時間同步處理。
- 重新開機次級裝置會導致同步處理遺失。
- 某些 [相機模式](hardware-specification.md#depth-camera-supported-operating-modes) 支援 15 fps max。建議您不要在裝置之間混合使用模式/畫面播放速率
- 將多個單位連接到單一電腦可以輕鬆地將 USB 頻寬飽和，請考慮針對每部裝置使用不同的主機電腦。 也請留意 CPU/GPU 計算。
- 如果不需要麥克風和 IMU 來改善可靠性，請加以停用。

如有任何問題，請參閱 [疑難排解](troubleshooting.md)

## <a name="see-also"></a>請參閱

- [設定外部同步處理](https://support.microsoft.com/help/4494429/sync-multiple-devices)
- 適用于錄製器設定的[Azure Kinect 錄製](azure-kinect-recorder.md)器和其他資訊。
- 適用于播放錄製的[Azure Kinect 檢視器](azure-kinect-viewer.md)，或設定無法透過錄製器使用的 RGB 相機屬性。
- 用來更新裝置固件的[Azure Kinect 固件工具](azure-kinect-firmware-tool.md)。
