---
title: 取出 Azure Kinect 影像資料
description: 瞭解如何使用 Kinect 感應器 SDK 來取出 Azure Kinect 影像資料。
author: cdedmonds
ms.author: cedmonds
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: kinect，azure，取得，感應器，相機，sdk，深度，rgb，影像，色彩，捕捉，解析度，緩衝區
ms.openlocfilehash: 84e678993f94c17bf58fb134234afaee4139aad5
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85276393"
---
# <a name="retrieve-azure-kinect-image-data"></a>取出 Azure Kinect 影像資料

此頁面提供如何從 Azure Kinect 取得影像的詳細資料。 本文示範如何在裝置的色彩與深度攝影機之間，捕捉和存取協調的影像。 若要存取映射，您必須先開啟並設定裝置，然後才能捕獲映射。
設定和抓取映射之前，您必須先[尋找並開啟 [裝置](find-then-open-device.md)]。

您也可以參考[SDK 串流範例](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/tree/develop/examples/streaming)，它會示範如何使用本文中的函式。

涵蓋的函式如下：

- [`k4a_device_start_cameras()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gaad7a85e1e5471810262442fc4a8e217a.html#gaad7a85e1e5471810262442fc4a8e217a)
- [`k4a_device_get_capture()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga4dac757a33657f4d3dbf1ae8b21c158a.html#ga4dac757a33657f4d3dbf1ae8b21c158a)
- [`k4a_capture_get_depth_image()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gafa03513da96bf6b8c254fc11a04ee6d6.html#gafa03513da96bf6b8c254fc11a04ee6d6)
- [`k4a_image_get_buffer()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga2ef070cf4e543cd0f726478af332546e.html#ga2ef070cf4e543cd0f726478af332546e)
- [`k4a_image_release()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga27c81863b13fafc3934a32935a014e9f.html#ga27c81863b13fafc3934a32935a014e9f)
- [`k4a_capture_release()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga0ed6f74ec403c3eac1b8ef3afb32cee6.html#ga0ed6f74ec403c3eac1b8ef3afb32cee6)
- [`k4a_device_stop_cameras()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga4fa0e0a011a7105309ad97f081a5d6b8.html#ga4fa0e0a011a7105309ad97f081a5d6b8)
  
## <a name="configure-and-start-the-device"></a>設定並啟動裝置

Kinect 裝置上提供的兩個相機支援多種模式、解析度和輸出格式。 如需完整清單，請參閱 Azure Kinect 開發工具組的[硬體規格](hardware-specification.md)。

串流設定是使用結構中的值來設定 [`k4a_device_configuration_t`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/structk4a__device__configuration__t.html) 。

```C
k4a_device_configuration_t config = K4A_DEVICE_CONFIG_INIT_DISABLE_ALL;
config.camera_fps = K4A_FRAMES_PER_SECOND_30;
config.color_format = K4A_IMAGE_FORMAT_COLOR_MJPG;
config.color_resolution = K4A_COLOR_RESOLUTION_2160P;
config.depth_mode = K4A_DEPTH_MODE_NFOV_UNBINNED;

if (K4A_RESULT_SUCCEEDED != k4a_device_start_cameras(device, &config))
{
    printf("Failed to start device\n");
    goto Exit;
}
```

啟動相機之後，它們會繼續捕獲資料，直到 [`k4a_device_stop_cameras()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga4fa0e0a011a7105309ad97f081a5d6b8.html#ga4fa0e0a011a7105309ad97f081a5d6b8) 呼叫或裝置關閉為止。

## <a name="stabilization"></a>穩定

使用多重裝置同步處理功能啟動裝置時，強烈建議使用固定的曝光設定來執行此動作。
在手動公開設定的情況下，最多可能需要從裝置進行八個捕獲，映射和畫面播放速率穩定。 在自動曝光的情況下，最多可能需要20個捕獲，映射和畫面播放速率才會穩定。

## <a name="get-a-capture-from-the-device"></a>從裝置取得 capture

映射會以相互關聯的方式從裝置中捕捉。 每個已捕獲的映射都包含深度影像、IR 影像、色彩影像或影像的組合。

根據預設，API 只會在接收到串流模式的所有要求影像之後，才傳回 capture。 您可以藉由清除的參數，將 API 設定為只傳回深度或色彩影像的部分捕捉 [`synchronized_images_only`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/structk4a__device__configuration__t_a8208974f05d89fc1362c6a0900bdef4d.html#a8208974f05d89fc1362c6a0900bdef4d) [`k4a_device_configuration_t`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/structk4a__device__configuration__t.html) 。

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

一旦 API 成功傳回 capture 之後，您就必須在 [`k4a_capture_release()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga0ed6f74ec403c3eac1b8ef3afb32cee6.html#ga0ed6f74ec403c3eac1b8ef3afb32cee6) 完成使用 capture 物件時呼叫。

## <a name="get-an-image-from-the-capture"></a>從 capture 取得映射

若要取出已捕獲的映射，請針對每個映射類型呼叫適當的函式。 值為下列其中之一：

- [`k4a_capture_get_color_image()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga683e440b5f22215a2de58d7fa140488c.html#ga683e440b5f22215a2de58d7fa140488c)
- [`k4a_capture_get_depth_image()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gafa03513da96bf6b8c254fc11a04ee6d6.html#gafa03513da96bf6b8c254fc11a04ee6d6)
- [`k4a_capture_get_ir_image()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga1531c3fa76a7c174b8f2eab24de91794.html#ga1531c3fa76a7c174b8f2eab24de91794)

一旦您使用完影像，就必須在這些函式所 [`k4a_image_release()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga27c81863b13fafc3934a32935a014e9f.html#ga27c81863b13fafc3934a32935a014e9f) [`k4a_image_t`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/structk4a__image__t.html) 傳回的任何控制碼上呼叫。

## <a name="access-image-buffers"></a>存取影像緩衝區

[`k4a_image_t`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/structk4a__image__t.html)有許多存取子函數可取得影像的屬性。

若要存取影像的記憶體緩衝區，請使用[k4a_image_get_buffer](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga2ef070cf4e543cd0f726478af332546e.html#ga2ef070cf4e543cd0f726478af332546e)。

下列範例示範如何存取已捕捉的深度影像。 這個相同的原則也適用于其他映射類型。 不過，請務必將映射類型變數取代為正確的映射類型，例如 IR 或 color。

```C
// Access the depth16 image
k4a_image_t image = k4a_capture_get_depth_image(capture);
if (image != NULL)
{
    printf(" | Depth16 res:%4dx%4d stride:%5d\n",
            k4a_image_get_height_pixels(image),
            k4a_image_get_width_pixels(image),
            k4a_image_get_stride_bytes(image));

    // Release the image
    k4a_image_release(image);
}

// Release the capture
k4a_capture_release(capture);
```

## <a name="next-steps"></a>後續步驟

現在您已瞭解如何使用 Azure Kinect 裝置，在色彩和深度之間捕捉和協調相機影像。 您也可以：

>[!div class="nextstepaction"]
>[取出 IMU 範例](retrieve-imu-samples.md)

>[!div class="nextstepaction"]
>[存取麥克風](access-mics.md)
