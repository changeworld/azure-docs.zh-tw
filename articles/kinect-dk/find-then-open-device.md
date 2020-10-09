---
title: 尋找並開啟 Azure Kinect 裝置
description: 瞭解如何使用 Azure Kinect 感應器 SDK 尋找和開啟 Azure Kinect 裝置。
author: cedmonds
ms.author: cedmonds
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: kinect、azure、感應器、sdk、深度、rgb、裝置、尋找、開啟
ms.openlocfilehash: 67fc93b924d5d663bb43098969c54d1975bd5895
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "85276462"
---
# <a name="find-then-open-the-azure-kinect-device"></a>尋找並開啟 Azure Kinect 裝置

本文說明您可以如何尋找，然後開啟您的 Azure Kinect DK。 本文說明如何處理有多個裝置連接到您電腦的情況。

您也可以參考 [SDK 列舉範例](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/tree/develop/examples/enumerate) ，以示範如何使用本文中的函式。

涵蓋的函式如下：
 * [`k4a_device_get_installed_count()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gaf7d19df0f73f8e4dfaa21e1b4b719ecc.html#gaf7d19df0f73f8e4dfaa21e1b4b719ecc)
 * [`k4a_device_open()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga3d4eb5dfbf4d576d4978b66ea419f113.html#ga3d4eb5dfbf4d576d4978b66ea419f113)
 * [`k4a_device_get_serialnum()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga798489af207ff1c99f2285ff6b08bc22.html#ga798489af207ff1c99f2285ff6b08bc22)
 * [`k4a_device_close()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga7a3931d9a690b3971caaac83b43f9423.html#ga7a3931d9a690b3971caaac83b43f9423)

## <a name="discover-the-number-of-connected-devices"></a>探索已連線的裝置數目

首先，使用來取得目前連線的 Azure Kinect 裝置計數 [`k4a_device_get_installed_count()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gaf7d19df0f73f8e4dfaa21e1b4b719ecc.html#gaf7d19df0f73f8e4dfaa21e1b4b719ecc) 。

```C
uint32_t device_count = k4a_device_get_installed_count();

printf("Found %d connected devices:\n", device_count);
```

## <a name="open-a-device"></a>開啟裝置

若要取得裝置的相關資訊或從中讀取資料，您必須先使用開啟裝置的控制碼 [`k4a_device_open()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga3d4eb5dfbf4d576d4978b66ea419f113.html#ga3d4eb5dfbf4d576d4978b66ea419f113) 。

```C
k4a_device_t device = NULL;

for (uint8_t deviceIndex = 0; deviceIndex < device_count; deviceIndex++)
{
    if (K4A_RESULT_SUCCEEDED != k4a_device_open(deviceIndex, &device))
    {
        printf("%d: Failed to open device\n", deviceIndex);
        continue;
    }

    ...

    k4a_device_close(device);
}
```

的 `index` 參數 [`k4a_device_open()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga3d4eb5dfbf4d576d4978b66ea419f113.html#ga3d4eb5dfbf4d576d4978b66ea419f113) 表示如果有一個以上的連線，要開啟的裝置。 如果您只預期要連接單一裝置，您可以傳遞或0的引數 `K4A_DEVICE_DEFAULT` 來表示第一個裝置。

當您開啟裝置時，您必須在 [`k4a_device_close()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga7a3931d9a690b3971caaac83b43f9423.html#ga7a3931d9a690b3971caaac83b43f9423) 完成使用控制碼時呼叫。 您必須關閉控制碼，才能在相同的裝置上開啟其他控制碼。

## <a name="identify-a-specific-device"></a>識別特定裝置

在連接或卸離裝置之前，依索引列舉的裝置順序不會變更。 若要識別實體裝置，您應該使用裝置的序號。

若要從裝置讀取序號，請在 [`k4a_device_get_serialnum()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga798489af207ff1c99f2285ff6b08bc22.html#ga798489af207ff1c99f2285ff6b08bc22) 開啟控制碼之後使用該函式。

此範例示範如何配置正確的記憶體數量來儲存序號。

```C
char *serial_number = NULL;
size_t serial_number_length = 0;

if (K4A_BUFFER_RESULT_TOO_SMALL != k4a_device_get_serialnum(device, NULL, &serial_number_length))
{
    printf("%d: Failed to get serial number length\n", deviceIndex);
    k4a_device_close(device);
    device = NULL;
    continue;
}

serial_number = malloc(serial_number_length);
if (serial_number == NULL)
{
    printf("%d: Failed to allocate memory for serial number (%zu bytes)\n", deviceIndex, serial_number_length);
    k4a_device_close(device);
    device = NULL;
    continue;
}

if (K4A_BUFFER_RESULT_SUCCEEDED != k4a_device_get_serialnum(device, serial_number, &serial_number_length))
{
    printf("%d: Failed to get serial number\n", deviceIndex);
    free(serial_number);
    serial_number = NULL;
    k4a_device_close(device);
    device = NULL;
    continue;
}

printf("%d: Device \"%s\"\n", deviceIndex, serial_number);
```

## <a name="open-the-default-device"></a>開啟預設裝置

在大部分的應用程式中，只會有單一 Azure Kinect DK 連接到同一部電腦。 如果您只需要連接到單一預期的裝置，您可以 [`k4a_device_open()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga3d4eb5dfbf4d576d4978b66ea419f113.html#ga3d4eb5dfbf4d576d4978b66ea419f113) 使用來呼叫 `index` `K4A_DEVICE_DEFAULT` 以開啟第一個裝置。

```C
k4a_device_t device = NULL;
uint32_t device_count = k4a_device_get_installed_count();

if (device_count != 1)
{
    printf("Unexpected number of devices found (%d)\n", device_count);
    goto Exit;
}

if (K4A_RESULT_SUCCEEDED != k4a_device_open(K4A_DEVICE_DEFAULT, &device))
{
    printf("Failed to open device\n");
    goto Exit;
}
```

## <a name="next-steps"></a>後續步驟

>[!div class="nextstepaction"]
>[取出影像](retrieve-images.md)

>[!div class="nextstepaction"]
>[取出 IMU 範例](retrieve-imu-samples.md)

