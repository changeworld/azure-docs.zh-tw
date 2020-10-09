---
title: 取出 Azure Kinect IMU 範例
description: 瞭解如何使用 Azure Kinect SDK 取出 Azure Kinect IMU 範例。
author: qm13
ms.author: cedmonds
ms.date: 06/26/2019
ms.prod: kinect-dk
ms.topic: conceptual
keywords: kinect、azure、設定、深度、色彩、RBG、相機、感應器、sdk、IMU、動作感應器、移動、陀螺儀、回轉、加速計、FPS
ms.openlocfilehash: 649dd5b9de62d43d59e74d53adff1ec7de8dfd32
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "85276478"
---
# <a name="retrieve-azure-kinect-imu-samples"></a>取出 Azure Kinect IMU 範例

Azure Kinect 裝置可讓您存取慣性移動單位 (IMUs) ，包括加速計和陀螺儀類型。 若要存取 IMUs 範例，您必須先開啟並設定您的裝置，然後捕獲 IMU 的資料。 如需詳細資訊，請參閱 [尋找和開啟裝置](find-then-open-device.md)。

IMU 範例的產生頻率遠高於影像。 系統會以較低的速率向主機回報範例。 等候 IMU 範例時，多個範例會經常變成可供使用。

如需 IMU 報告費率的詳細資訊，請參閱 Azure Kinect DK [硬體規格](hardware-specification.md) 。

## <a name="configure-and-start-cameras"></a>設定並啟動相機

> [!NOTE]
> 只有當色彩和/或深度相機正在執行時，IMU 感應器才能運作。 IMU 感應器無法單獨運作。

若要啟動相機，請使用 [k4a_device_start_cameras ( # B1 ](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gaad7a85e1e5471810262442fc4a8e217a.html#gaad7a85e1e5471810262442fc4a8e217a)。

```C
k4a_device_configuration_t config = K4A_DEVICE_CONFIG_INIT_DISABLE_ALL;
config.camera_fps = K4A_FRAMES_PER_SECOND_30;
config.color_format = K4A_IMAGE_FORMAT_COLOR_MJPG;
config.color_resolution = K4A_COLOR_RESOLUTION_2160P;

if (K4A_RESULT_SUCCEEDED != k4a_device_start_cameras(device, &config))
{
    printf("Failed to start cameras\n");
    goto Exit;
}

if (K4A_RESULT_SUCCEEDED != k4a_device_start_imu(device))
{
    printf("Failed to start imu\n");
    goto Exit;
}
```

## <a name="access-imu-samples"></a>存取 IMU 範例

 每個 [k4a_imu_sample_t](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/structk4a__imu__sample__t.html#details) 都包含在幾乎相同時間所捕獲的加速計和陀螺儀閱讀。

您可以在取得影像捕捉的相同執行緒上，或在個別的執行緒上取得 IMU 範例。

若要在 IMU 範例可供使用時立即取得，您可能會想要 [`k4a_device_get_imu_sample()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga8e5913b3bb94a453c7143bbd6e399a0e.html#ga8e5913b3bb94a453c7143bbd6e399a0e) 在自己的執行緒上呼叫。 此 API 也有足夠的內部佇列，可讓您只在每次傳回影像捕獲後檢查範例。

因為有一些 IMU 範例的內部佇列，所以您可以使用下列模式，而不需卸載任何資料：

1. 以任何畫面播放速率等候捕捉。
2. 處理 capture。
3. 取出所有已排入佇列的 IMU 範例。
4. 重複等候下一個捕獲。

若要抓取所有目前排入佇列的 IMU 範例，您可以 [`k4a_device_get_imu_sample()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga8e5913b3bb94a453c7143bbd6e399a0e.html#ga8e5913b3bb94a453c7143bbd6e399a0e) `timeout_in_ms` 在迴圈中以0的呼叫，直到函數傳回為止 `K4A_WAIT_RESULT_TIMEOUT` 。 `K4A_WAIT_RESULT_TIMEOUT` 指出沒有已排入佇列的範例，且未在指定的超時時間內到達。

## <a name="usage-example"></a>使用範例

```C
k4a_imu_sample_t imu_sample;

// Capture a imu sample
switch (k4a_device_get_imu_sample(device, &imu_sample, TIMEOUT_IN_MS))
{
case K4A_WAIT_RESULT_SUCCEEDED:
    break;
case K4A_WAIT_RESULT_TIMEOUT:
    printf("Timed out waiting for a imu sample\n");
    continue;
    break;
case K4A_WAIT_RESULT_FAILED:
    printf("Failed to read a imu sample\n");
    goto Exit;
}

// Access the accelerometer readings
if (imu_sample != NULL)
{
    printf(" | Accelerometer temperature:%.2f x:%.4f y:%.4f z: %.4f\n",
            imu_sample.temperature,
            imu_sample.acc_sample.xyz.x,
            imu_sample.acc_sample.xyz.y,
            imu_sample.acc_sample.xyz.z);
}

```

## <a name="next-steps"></a>後續步驟

現在您已瞭解如何使用 IMU 範例，您也可以
>[!div class="nextstepaction"]
>[存取麥克風輸入資料](access-mics.md)
