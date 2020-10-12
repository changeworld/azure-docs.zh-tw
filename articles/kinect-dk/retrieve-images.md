---
title: 取出 Azure Kinect 影像資料
description: 瞭解如何使用 Kinect 感應器 SDK 來取得 Azure Kinect 影像資料。
author: cdedmonds
ms.author: cedmonds
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: kinect、azure、取出、感應器、相機、sdk、深度、rgb、影像、色彩、捕捉、解析度、緩衝區
ms.openlocfilehash: fed5c7340d287d9103ba35f0fd3d80c0fff6e3ec
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "87538909"
---
# <a name="retrieve-azure-kinect-image-data"></a>取出 Azure Kinect 影像資料

此頁面提供有關如何從 Azure Kinect 取出影像的詳細資料。 本文示範如何捕獲和存取在裝置的色彩和深度之間協調的影像。 若要存取影像，您必須先開啟並設定裝置，然後才能抓取映射。
設定和捕獲映射之前，您必須先 [尋找並開啟裝置](find-then-open-device.md)。

您也可以參考 [SDK 串流範例](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/tree/develop/examples/streaming) ，以示範如何使用本文中的函式。

涵蓋的函式如下：

- [`k4a_device_start_cameras()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gaad7a85e1e5471810262442fc4a8e217a.html#gaad7a85e1e5471810262442fc4a8e217a)
- [`k4a_device_get_capture()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga4dac757a33657f4d3dbf1ae8b21c158a.html#ga4dac757a33657f4d3dbf1ae8b21c158a)
- [`k4a_capture_get_depth_image()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gafa03513da96bf6b8c254fc11a04ee6d6.html#gafa03513da96bf6b8c254fc11a04ee6d6)
- [`k4a_image_get_buffer()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga2ef070cf4e543cd0f726478af332546e.html#ga2ef070cf4e543cd0f726478af332546e)
- [`k4a_image_release()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga27c81863b13fafc3934a32935a014e9f.html#ga27c81863b13fafc3934a32935a014e9f)
- [`k4a_capture_release()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga0ed6f74ec403c3eac1b8ef3afb32cee6.html#ga0ed6f74ec403c3eac1b8ef3afb32cee6)
- [`k4a_device_stop_cameras()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga4fa0e0a011a7105309ad97f081a5d6b8.html#ga4fa0e0a011a7105309ad97f081a5d6b8)
  
## <a name="configure-and-start-the-device"></a>設定並啟動裝置

您 Kinect 裝置上提供的兩種攝影機支援多種模式、解析度和輸出格式。 如需完整清單，請參閱 Azure Kinect 開發工具組 [硬體規格](hardware-specification.md)。

串流設定會使用結構中的值來設定 [`k4a_device_configuration_t`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/structk4a__device__configuration__t.html) 。

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

攝影機啟動後，它們會繼續捕捉資料，直到 [`k4a_device_stop_cameras()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga4fa0e0a011a7105309ad97f081a5d6b8.html#ga4fa0e0a011a7105309ad97f081a5d6b8) 呼叫或關閉裝置為止。

## <a name="stabilization"></a>穩定

使用多重裝置同步處理功能啟動裝置時，強烈建議使用固定的公開設定來進行。
透過手動公開設定，在影像和畫面播放速率穩定之前，最多可能需要從裝置八個捕獲。 使用自動公開時，最多可能需要20個捕獲，才能讓影像和畫面播放速率穩定。

## <a name="get-a-capture-from-the-device"></a>從裝置取得捕獲

系統會以相互關聯的方式從裝置捕獲映射。 每個已捕獲映射都包含深度影像、IR 影像、色彩影像或影像的組合。

根據預設，API 只會在接收到所有針對串流模式所要求的映射時，才會傳回 capture。 您可以藉由清除的參數，將 API 設定為只傳回具有深度或彩色影像的部分捕捉 [`synchronized_images_only`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/structk4a__device__configuration__t_a8208974f05d89fc1362c6a0900bdef4d.html#a8208974f05d89fc1362c6a0900bdef4d) [`k4a_device_configuration_t`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/structk4a__device__configuration__t.html) 。

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

一旦 API 成功傳回捕捉之後，您必須在 [`k4a_capture_release()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga0ed6f74ec403c3eac1b8ef3afb32cee6.html#ga0ed6f74ec403c3eac1b8ef3afb32cee6) 使用 capture 物件完成時呼叫。

## <a name="get-an-image-from-the-capture"></a>從 capture 取得映射

若要抓取已捕捉的映射，請針對每個影像類型呼叫適當的函式。 值為下列其中之一：

- [`k4a_capture_get_color_image()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga683e440b5f22215a2de58d7fa140488c.html#ga683e440b5f22215a2de58d7fa140488c)
- [`k4a_capture_get_depth_image()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gafa03513da96bf6b8c254fc11a04ee6d6.html#gafa03513da96bf6b8c254fc11a04ee6d6)
- [`k4a_capture_get_ir_image()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga1531c3fa76a7c174b8f2eab24de91794.html#ga1531c3fa76a7c174b8f2eab24de91794)

[`k4a_image_release()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga27c81863b13fafc3934a32935a014e9f.html#ga27c81863b13fafc3934a32935a014e9f) [`k4a_image_t`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/structk4a__image__t.html) 當您使用完影像之後，您必須在這些函式所傳回的任何控制碼上呼叫。

## <a name="access-image-buffers"></a>存取映射緩衝區

[`k4a_image_t`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/structk4a__image__t.html) 有許多存取子函數可取得影像的屬性。

若要存取映射的記憶體緩衝區，請使用 [k4a_image_get_buffer](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga2ef070cf4e543cd0f726478af332546e.html#ga2ef070cf4e543cd0f726478af332546e)。

下列範例示範如何存取已捕捉的深度影像。 同樣的原則也適用于其他映射類型。 不過，請務必以正確的影像類型（例如 IR 或色彩）來取代映射類型變數。

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

現在您已瞭解如何使用 Azure Kinect 裝置，在色彩和深度間捕捉和協調攝影機的影像。 您也可以：

>[!div class="nextstepaction"]
>[取出 IMU 範例](retrieve-imu-samples.md)

>[!div class="nextstepaction"]
>[存取麥克風](access-mics.md)
