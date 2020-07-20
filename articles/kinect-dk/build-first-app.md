---
title: 快速入門 - 建置第一個 Azure Kinect 應用程式
description: 本快速入門會引導 Azure Kinect DK 使用者完成建立新應用程式的程序。
author: tesych
ms.author: tesych
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: quickstart
keywords: kinect, azure, 感應器, sdk, 麥克風, 存取麥克風, 麥克風資料
ms.openlocfilehash: 3632145b3f3b63023e0c66e3cf99903231802edf
ms.sourcegitcommit: fe6c9a35e75da8a0ec8cea979f9dec81ce308c0e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/26/2020
ms.locfileid: "85277769"
---
# <a name="quickstart-build-your-first-azure-kinect-application"></a>快速入門：建置第一個 Azure Kinect 應用程式

想要開始使用 Azure Kinect DK 嗎？ 本快速入門可讓您啟動並執行裝置！

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

涵蓋的函式如下：

- [`k4a_device_get_installed_count()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gaf7d19df0f73f8e4dfaa21e1b4b719ecc.html#gaf7d19df0f73f8e4dfaa21e1b4b719ecc)
- [`k4a_device_open()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga3d4eb5dfbf4d576d4978b66ea419f113.html#ga3d4eb5dfbf4d576d4978b66ea419f113)
- [`k4a_device_get_serialnum()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga798489af207ff1c99f2285ff6b08bc22.html#ga798489af207ff1c99f2285ff6b08bc22)
- [`k4a_device_start_cameras()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gaad7a85e1e5471810262442fc4a8e217a.html#gaad7a85e1e5471810262442fc4a8e217a)
- [`k4a_device_stop_cameras()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga4fa0e0a011a7105309ad97f081a5d6b8.html#ga4fa0e0a011a7105309ad97f081a5d6b8)
- [`k4a_device_close()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga7a3931d9a690b3971caaac83b43f9423.html#ga7a3931d9a690b3971caaac83b43f9423)

## <a name="prerequisites"></a>必要條件

1. [設定 Azure Kinect DK 裝置](set-up-azure-kinect-dk.md)。
2. [下載](sensor-sdk-download.md)並安裝 Azure Kinect 感應器 SDK。

## <a name="headers"></a>headers

您只需要一個標頭，那就是 `k4a.h`。 請確定您選擇的編譯器已設定了 SDK 的程式庫並包含資料夾。 您也需要連結 `k4a.lib` 和 `k4a.dll` 檔案。 您可以參考[將 Azure Kinect 程式庫新增至專案](add-library-to-project.md)。

```C
#include <k4a/k4a.h>
```

## <a name="finding-an-azure-kinect-dk-device"></a>尋找 Azure Kinect DK 裝置

可能有多個 Azure Kinect DK 裝置連線到您的電腦。 我們會先了解數量有多少，或使用 [`k4a_device_get_installed_count()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gaf7d19df0f73f8e4dfaa21e1b4b719ecc.html#gaf7d19df0f73f8e4dfaa21e1b4b719ecc) 函式來了解究竟是否有連線的裝置。 此函式應該能立即運作，而不需要任何額外的設定。

```C
uint32_t count = k4a_device_get_installed_count();
```

一旦您確定有裝置連線到電腦後，就可以使用 [`k4a_device_open()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga3d4eb5dfbf4d576d4978b66ea419f113.html#ga3d4eb5dfbf4d576d4978b66ea419f113) 來加以開啟。 您可以提供所要開啟裝置的索引，也可以直接使用第一個裝置的 `K4A_DEVICE_DEFAULT`。

```C
// Open the first plugged in Kinect device
k4a_device_t device = NULL;
k4a_device_open(K4A_DEVICE_DEFAULT, &device);
```
和 Azure Kinect 程式庫中的大部分項目一樣，當您開啟某個項目時，就也應該在使用完後將其關閉！ 當您關閉時，請記得對 [`k4a_device_close()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga7a3931d9a690b3971caaac83b43f9423.html#ga7a3931d9a690b3971caaac83b43f9423) 進行呼叫。

```C
k4a_device_close(device);
```

裝置開啟後，我們可以進行測試以確保其運作正常。 讓我們讀取裝置的序號吧！

```C
// Get the size of the serial number
size_t serial_size = 0;
k4a_device_get_serialnum(device, NULL, &serial_size);

// Allocate memory for the serial, then acquire it
char *serial = (char*)(malloc(serial_size));
k4a_device_get_serialnum(device, serial, &serial_size);
printf("Opened device: %s\n", serial);
free(serial);
```

## <a name="starting-the-cameras"></a>啟動相機

開啟裝置之後，您必須使用 [`k4a_device_configuration_t`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/structk4a__device__configuration__t.html) 物件來設定相機。 相機設定有許多不同選項。 請選擇最適合您自己案例的設定。

```C
// Configure a stream of 4096x3072 BRGA color data at 15 frames per second
k4a_device_configuration_t config = K4A_DEVICE_CONFIG_INIT_DISABLE_ALL;
config.camera_fps       = K4A_FRAMES_PER_SECOND_15;
config.color_format     = K4A_IMAGE_FORMAT_COLOR_BGRA32;
config.color_resolution = K4A_COLOR_RESOLUTION_3072P;

// Start the camera with the given configuration
k4a_device_start_cameras(device, &config);

// ...Camera capture and application specific code would go here...

// Shut down the camera when finished with application logic
k4a_device_stop_cameras(device);
```

## <a name="error-handling"></a>錯誤處理

為求簡潔明瞭，我們不會在某些內嵌範例中顯示如何處理錯誤。 不過，處理錯誤的方式一向很重要！ 許多函式會傳回一般的成功/失敗類型 [`k4a_result_t`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___enumerations_ga4b419a99aa2220b076a4520dc2afd1e5.html#ga4b419a99aa2220b076a4520dc2afd1e5)，有時也會傳回有詳細資訊的更具體變體，例如 [`k4a_wait_result_t`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___enumerations_ga44c7c0c1cfba7c879e9e2da1a869e4ee.html#ga44c7c0c1cfba7c879e9e2da1a869e4ee)。 請查看每個函式的文件或 IntelliSense，以了解您預期會看到的錯誤訊息！

您可以使用 [`K4A_SUCCEEDED`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___macros_ga8e5b48150bc243c6052793bd830c2fcd.html#ga8e5b48150bc243c6052793bd830c2fcd) 和 [`K4A_FAILED`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___macros_ga7c2e32349135d008b6f836c571d434b4.html#ga7c2e32349135d008b6f836c571d434b4) 巨集來檢查函式的結果。 因此，我們可能會以下列方式保護函式呼叫，而不只是開啟 Azure Kinect DK 裝置：

```C
// Open the first plugged in Kinect device
k4a_device_t device = NULL;
if ( K4A_FAILED( k4a_device_open(K4A_DEVICE_DEFAULT, &device) ) )
{
    printf("Failed to open k4a device!\n");
    return;
}
```

## <a name="full-source"></a>完整來源

```C
#pragma comment(lib, "k4a.lib")
#include <k4a/k4a.h>

#include <stdio.h>
#include <stdlib.h>

int main()
{
    uint32_t count = k4a_device_get_installed_count();
    if (count == 0)
    {
        printf("No k4a devices attached!\n");
        return 1;
    }

    // Open the first plugged in Kinect device
    k4a_device_t device = NULL;
    if (K4A_FAILED(k4a_device_open(K4A_DEVICE_DEFAULT, &device)))
    {
        printf("Failed to open k4a device!\n");
        return 1;
    }

    // Get the size of the serial number
    size_t serial_size = 0;
    k4a_device_get_serialnum(device, NULL, &serial_size);

    // Allocate memory for the serial, then acquire it
    char *serial = (char*)(malloc(serial_size));
    k4a_device_get_serialnum(device, serial, &serial_size);
    printf("Opened device: %s\n", serial);
    free(serial);

    // Configure a stream of 4096x3072 BRGA color data at 15 frames per second
    k4a_device_configuration_t config = K4A_DEVICE_CONFIG_INIT_DISABLE_ALL;
    config.camera_fps       = K4A_FRAMES_PER_SECOND_15;
    config.color_format     = K4A_IMAGE_FORMAT_COLOR_BGRA32;
    config.color_resolution = K4A_COLOR_RESOLUTION_3072P;

    // Start the camera with the given configuration
    if (K4A_FAILED(k4a_device_start_cameras(device, &config)))
    {
        printf("Failed to start cameras!\n");
        k4a_device_close(device);
        return 1;
    }

    // Camera capture and application specific code would go here

    // Shut down the camera when finished with application logic
    k4a_device_stop_cameras(device);
    k4a_device_close(device);

    return 0;
}

```

## <a name="next-steps"></a>後續步驟

了解如何使用感應器 SDK 來尋找和開啟 Azure Kinect DK 裝置
> [!div class="nextstepaction"]
>[尋找並開啟裝置](find-then-open-device.md)
