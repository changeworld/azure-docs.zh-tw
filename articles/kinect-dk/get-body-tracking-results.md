---
title: Azure Kinect 取得本文追蹤結果
description: 瞭解如何使用 Azure Kinect 主體追蹤 SDK 來取得本文追蹤結果。
author: qm13
ms.prod: kinect-dk
ms.author: quentinm
ms.reviewer: yijwan
ms.date: 06/26/2019
ms.topic: conceptual
keywords: kinect，azure，感應器，sdk，主體，追蹤，聯合
ms.openlocfilehash: 1b62022242144d5db51455a32ac04b67c3e5dd7a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85276533"
---
# <a name="get-body-tracking-results"></a>取得全身追蹤結果

本文追蹤 SDK 會使用本文追蹤器物件來處理 Azure Kinect DK 的捕獲，並產生內文追蹤結果。 它也會維護追蹤程式、處理佇列和輸出佇列的全域狀態。 使用主體追蹤程式有三個步驟：

- 建立追蹤程式
- 使用 Azure Kinect DK 來捕捉深度和 IR 影像
- 將捕捉排入佇列，並將結果彈出。

## <a name="create-a-tracker"></a>建立追蹤程式


使用本文追蹤的第一個步驟是建立追蹤程式，並要求在感應器校準[k4a_calibration_t](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/structk4a__calibration__t.html)結構中傳遞。 您可以使用 Azure Kinect 感應器 SDK [k4a_device_get_calibration （）](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga4e43940d8d8db48da266c7a7842c8d78.html#ga4e43940d8d8db48da266c7a7842c8d78)函數來查詢感應器校正。

```C
k4a_calibration_t sensor_calibration;
if (K4A_RESULT_SUCCEEDED != k4a_device_get_calibration(device, device_config.depth_mode, K4A_COLOR_RESOLUTION_OFF, &sensor_calibration))
{
    printf("Get depth camera calibration failed!\n");
    return 0;
}

k4abt_tracker_t tracker = NULL;
k4abt_tracker_configuration_t tracker_config = K4ABT_TRACKER_CONFIG_DEFAULT;
if (K4A_RESULT_SUCCEEDED != k4abt_tracker_create(&sensor_calibration, tracker_config, &tracker))
{
    printf("Body tracker initialization failed!\n");
    return 0;
}
```

## <a name="capture-depth-and-ir-images"></a>捕捉深度和 IR 影像

使用 Azure Kinect DK 的影像捕獲會在 [抓取[映射](retrieve-images.md)] 頁面中說明。

>[!NOTE]
> `K4A_DEPTH_MODE_NFOV_UNBINNED``K4A_DEPTH_MODE_WFOV_2X2BINNED`建議使用或模式，以獲得最佳效能和精確度。 請勿使用 `K4A_DEPTH_MODE_OFF` 或 `K4A_DEPTH_MODE_PASSIVE_IR` 模式。

Azure Kinect DK[硬體規格](hardware-specification.md)和[k4a_depth_mode_t](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___enumerations_ga3507ee60c1ffe1909096e2080dd2a05d.html#ga3507ee60c1ffe1909096e2080dd2a05d)列舉中會描述支援的 Azure Kinect DK 模式。

```C
// Capture a depth frame
switch (k4a_device_get_capture(device, &capture, TIMEOUT_IN_MS))
{
case K4A_WAIT_RESULT_SUCCEEDED:
    break;
case K4A_WAIT_RESULT_TIMEOUT:
    printf("Timed out waiting for a capture\n");
    continue;
    break;
case K4A_WAIT_RESULT_FAILED:
    printf("Failed to read a capture\n");
    goto Exit;
}
```

## <a name="enqueue-the-capture-and-pop-the-results"></a>將擷取排入佇列並快顯結果

追蹤程式會在內部維持輸入佇列和輸出佇列，以便透過非同步方式更有效率地處理 Azure Kinect DK 的擷取。 使用[k4abt_tracker_enqueue_capture （）](https://microsoft.github.io/Azure-Kinect-Body-Tracking/release/1.x.x/group__btfunctions_ga093becd9bb4a63f5f4d56f58097a7b1e.html#ga093becd9bb4a63f5f4d56f58097a7b1e)函數，將新的 capture 新增至輸入佇列。 使用[k4abt_tracker_pop_result （）](https://microsoft.github.io/Azure-Kinect-Body-Tracking/release/1.x.x/group__btfunctions_gaaf446fb1579cbbe0b6af824ee0a7458b.html#gaaf446fb1579cbbe0b6af824ee0a7458b)函數 o pop 作為輸出佇列的結果。 Timeout 值的使用取決於應用程式，並控制佇列等候時間。

### <a name="real-time-processing"></a>即時處理
針對需要即時結果的單一執行緒應用程式使用此模式，並可容納已捨棄的畫面。 `simple_3d_viewer`位於[GitHub Azure-Kinect](https://github.com/microsoft/Azure-Kinect-Samples)範例中的範例是即時處理的範例。

```C
k4a_wait_result_t queue_capture_result = k4abt_tracker_enqueue_capture(tracker, sensor_capture, 0);
k4a_capture_release(sensor_capture); // Remember to release the sensor capture once you finish using it
if (queue_capture_result == K4A_WAIT_RESULT_FAILED)
{
    printf("Error! Adding capture to tracker process queue failed!\n");
    break;
}

k4abt_frame_t body_frame = NULL;
k4a_wait_result_t pop_frame_result = k4abt_tracker_pop_result(tracker, &body_frame, 0);
if (pop_frame_result == K4A_WAIT_RESULT_SUCCEEDED)
{
    // Successfully popped the body tracking result. Start your processing
    ...

    k4abt_frame_release(body_frame); // Remember to release the body frame once you finish using it
}
```

### <a name="synchronous-processing"></a>同步處理
若應用程式不需要即時結果，或無法容納已捨棄的框架，請使用此模式。

處理輸送量可能會受到限制。

`simple_sample.exe`位於[GitHub Azure-Kinect](https://github.com/microsoft/Azure-Kinect-Samples)範例中的範例是同步處理的範例。

```C
k4a_wait_result_t queue_capture_result = k4abt_tracker_enqueue_capture(tracker, sensor_capture, K4A_WAIT_INFINITE);
k4a_capture_release(sensor_capture); // Remember to release the sensor capture once you finish using it
if (queue_capture_result != K4A_WAIT_RESULT_SUCCEEDED)
{
    // It should never hit timeout or error when K4A_WAIT_INFINITE is set.
    printf("Error! Adding capture to tracker process queue failed!\n");
    break;
}

k4abt_frame_t body_frame = NULL;
k4a_wait_result_t pop_frame_result = k4abt_tracker_pop_result(tracker, &body_frame, K4A_WAIT_INFINITE);
if (pop_frame_result != K4A_WAIT_RESULT_SUCCEEDED)
{
    // It should never hit timeout or error when K4A_WAIT_INFINITE is set.
    printf("Error! Popping body tracking result failed!\n");
    break;
}
// Successfully popped the body tracking result. Start your processing
...

k4abt_frame_release(body_frame); // Remember to release the body frame once you finish using it
```

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
>[存取全身框架中的資料](access-data-body-frame.md)
