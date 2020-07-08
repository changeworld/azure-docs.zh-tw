---
title: 取得 Azure Kinect IMU 範例
description: 瞭解如何使用 Azure Kinect SDK 來取得 Azure Kinect IMU 範例。
author: qm13
ms.author: cedmonds
ms.date: 06/26/2019
ms.prod: kinect-dk
ms.topic: conceptual
keywords: kinect，azure，設定，深度，色彩，RBG，攝影機，感應器，sdk，IMU，動作感應器，動作，陀螺儀，gyro，加速計，FPS
ms.openlocfilehash: 649dd5b9de62d43d59e74d53adff1ec7de8dfd32
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85276478"
---
# <a name="retrieve-azure-kinect-imu-samples"></a>取得 Azure Kinect IMU 範例

Azure Kinect 裝置可讓您存取「慣性運動單位」（IMUs），包括加速計和陀螺儀類型。 若要存取 IMUs 範例，您必須先開啟並設定您的裝置，然後再捕捉 IMU 資料。 如需詳細資訊，請參閱[尋找並開啟裝置](find-then-open-device.md)。

IMU 範例會以比影像更高的頻率產生。 以低於取樣的速率向主機報告範例。 等候 IMU 範例時，多個範例經常會同時可供使用。

如需 IMU 報告費率的詳細資訊，請參閱 Azure Kinect DK[硬體規格](hardware-specification.md)。

## <a name="configure-and-start-cameras"></a>設定和啟動攝影機

> [!NOTE]
> 只有當色彩和/或深度相機正在執行時，IMU 感應器才能正常運作。 IMU 感應器無法單獨使用。

若要啟動相機，請使用[k4a_device_start_cameras （）](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gaad7a85e1e5471810262442fc4a8e217a.html#gaad7a85e1e5471810262442fc4a8e217a)。

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

 每個[k4a_imu_sample_t](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/structk4a__imu__sample__t.html#details)都包含在幾乎相同時間所捕捉到的加速計和陀螺儀讀取。

您可以在取得影像捕捉的相同執行緒上，或在不同的執行緒上取得 IMU 範例。

若要在 IMU 範例可供使用時立即取得，您可以 [`k4a_device_get_imu_sample()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga8e5913b3bb94a453c7143bbd6e399a0e.html#ga8e5913b3bb94a453c7143bbd6e399a0e) 在自己的執行緒上呼叫。 API 也有足夠的內部佇列，可讓您只在每個影像捕獲傳回之後才檢查樣本。

因為有一些內部佇列的 IMU 範例，所以您可以使用下列模式，而不需要卸載任何資料：

1. 以任何畫面播放速率等候捕捉。
2. 處理捕捉。
3. 取出所有已排入佇列的 IMU 範例。
4. 重複等待下一個捕獲。

若要取得所有目前佇列的 IMU 範例，您可以 [`k4a_device_get_imu_sample()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga8e5913b3bb94a453c7143bbd6e399a0e.html#ga8e5913b3bb94a453c7143bbd6e399a0e) `timeout_in_ms` 在迴圈中呼叫具有0的，直到函式傳回為止 `K4A_WAIT_RESULT_TIMEOUT` 。 `K4A_WAIT_RESULT_TIMEOUT`表示沒有已排入佇列的樣本，而且沒有到達指定的超時時間。

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

現在您知道如何使用 IMU 範例，您也可以
>[!div class="nextstepaction"]
>[存取麥克風輸入資料](access-mics.md)
