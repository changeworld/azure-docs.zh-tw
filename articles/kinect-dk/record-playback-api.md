---
title: Azure Kinect 播放 API
description: 瞭解如何使用 Azure Kinect 感應器 SDK，以使用播放 API 來開啟記錄檔。
author: xthexder
ms.author: jawirth
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: kinect、azure、感應器、sdk、深度、rgb、記錄、播放、matroska、.mkv
ms.openlocfilehash: fe403f314c1df415537d090433f34627eb1249e7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "85276395"
---
# <a name="the-azure-kinect-playback-api"></a>Azure Kinect 播放 API

感應器 SDK 提供的 API 可讓您將裝置資料記錄到 Matroska ( .mkv) 檔。 Matroska 容器格式會儲存影片曲目、IMU 範例和裝置校正。 您可以使用提供的 [k4arecorder](record-sensor-streams-file.md) 命令列公用程式來產生錄製。 錄製也可以直接使用記錄 API 來自訂和記錄。

如需記錄 API 的詳細資訊，請參閱 [`k4a_record_create()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gae14f4181e9688e710d1c80b215413831.html#gae14f4181e9688e710d1c80b215413831) 。

如需有關 Matroska 檔案格式規格的詳細資訊，請參閱 [記錄檔案格式](record-file-format.md) 頁面。

## <a name="use-the-playback-api"></a>使用播放 API

您可以使用播放 API 來開啟記錄檔。 播放 API 可讓您存取感應器資料，其格式與感應器 SDK 的其餘部分相同。

### <a name="open-a-record-file"></a>開啟記錄檔

在下列範例中，我們使用開啟錄製 [`k4a_playback_open()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gacb254ac941b2ab3c202ca68f4537f368.html#gacb254ac941b2ab3c202ca68f4537f368) 、列印錄製長度，然後以關閉檔案 [`k4a_playback_close()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga76f415f2076f1c8c544e094a649306ff.html#ga76f415f2076f1c8c544e094a649306ff) 。

```C
k4a_playback_t playback_handle = NULL;
if (k4a_playback_open("recording.mkv", &playback_handle) != K4A_RESULT_SUCCEEDED)
{
    printf("Failed to open recording\n");
    return 1;
}

uint64_t recording_length = k4a_playback_get_last_timestamp_usec(playback_handle);
printf("Recording is %lld seconds long\n", recording_length / 1000000);

k4a_playback_close(playback_handle);
```

### <a name="read-captures"></a>讀取捕獲

檔案開啟後，就可以開始從錄製中讀取捕捉。 下一個範例會讀取檔案中的每個捕獲。

```C
k4a_capture_t capture = NULL;
k4a_stream_result_t result = K4A_STREAM_RESULT_SUCCEEDED;
while (result == K4A_STREAM_RESULT_SUCCEEDED)
{
    result = k4a_playback_get_next_capture(playback_handle, &capture);
    if (result == K4A_STREAM_RESULT_SUCCEEDED)
    {
        // Process capture here
        k4a_capture_release(capture);
    }
    else if (result == K4A_STREAM_RESULT_EOF)
    {
        // End of file reached
        break;
    }
}
if (result == K4A_STREAM_RESULT_FAILED)
{
    printf("Failed to read entire recording\n");
    return 1;
}
```

### <a name="seek-within-a-recording"></a>在錄製內搜尋

到達檔案結尾之後，我們可能想要返回並重新讀取。 這項處理常式可以使用向前讀取來完成 [`k4a_playback_get_previous_capture()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga54732e3aa0717e1ca4eb76ee385e878c.html#ga54732e3aa0717e1ca4eb76ee385e878c) ，但是這可能會非常慢，視錄製的長度而定。
相反地，我們可以使用函式移至檔案 [`k4a_playback_seek_timestamp()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gaea748994a121543bd77f90417cf428f6.html#gaea748994a121543bd77f90417cf428f6) 中的特定點。

在此範例中，我們會指定時間戳記（以微秒為單位）來搜尋檔案中的各個點。

```C
// Seek to the beginning of the file
if (k4a_playback_seek_timestamp(playback_handle, 0, K4A_PLAYBACK_SEEK_BEGIN) != K4A_RESULT_SUCCEEDED)
{
    return 1;
}

// Seek to the end of the file
if (k4a_playback_seek_timestamp(playback_handle, 0, K4A_PLAYBACK_SEEK_END) != K4A_RESULT_SUCCEEDED)
{
    return 1;
}

// Seek to 10 seconds from the start
if (k4a_playback_seek_timestamp(playback_handle, 10 * 1000000, K4A_PLAYBACK_SEEK_BEGIN) != K4A_RESULT_SUCCEEDED)
{
    return 1;
}

// Seek to 10 seconds from the end
if (k4a_playback_seek_timestamp(playback_handle, -10 * 1000000, K4A_PLAYBACK_SEEK_END) != K4A_RESULT_SUCCEEDED)
{
    return 1;
}
```

### <a name="read-tag-information"></a>讀取標記資訊

錄製也可以包含各種中繼資料，例如裝置序號和固件版本。 此中繼資料會儲存在錄製標記中，您可以使用函數來存取這些標記 [`k4a_playback_get_tag()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga320f966fc89b4ba0d758f787f70d5143.html#ga320f966fc89b4ba0d758f787f70d5143) 。

```C
// Print the serial number of the device used to record
char serial_number[256];
size_t serial_number_size = 256;
k4a_buffer_result_t buffer_result = k4a_playback_get_tag(playback_handle, "K4A_DEVICE_SERIAL_NUMBER", &serial_number, &serial_number_size);
if (buffer_result == K4A_BUFFER_RESULT_SUCCEEDED)
{
    printf("Device serial number: %s\n", serial_number);
}
else if (buffer_result == K4A_BUFFER_RESULT_TOO_SMALL)
{
    printf("Device serial number too long.\n");
}
else
{
    printf("Tag does not exist. Device serial number was not recorded.\n");
}
```

### <a name="record-tag-list"></a>記錄標記清單

以下是可能包含在記錄檔中的所有預設標記清單。 其中有許多值都可作為結構的一部分 [`k4a_record_configuration_t`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/structk4a__record__configuration__t.html) ，而且可以使用函式來讀取 [`k4a_playback_get_record_configuration()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gaab54a85c1f1e98d170d009042b449255.html#gaab54a85c1f1e98d170d009042b449255) 。

如果標記不存在，則會假設為預設值。

| 標記名稱                     | 預設值      | [`k4a_record_configuration_t`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/structk4a__record__configuration__t.html) 領域 | 注意     |
|------------------------------|--------------------|--------------------------------------|----------------------------------------------------------------------------------------------------------------|
| `K4A_COLOR_MODE`             | 偏離              | `color_format` / `color_resolution`  | 可能的值：「關閉」、「MJPG_1080P」、「NV12_720P」、「YUY2_720P」等等。                                      |
| `K4A_DEPTH_MODE`             | 偏離              | `depth_mode` / `depth_track_enabled` | 可能的值：「關閉」、「NFOV_UNBINNED」、「PASSIVE_IR」等等。                                                |
| `K4A_IR_MODE`                | 偏離              | `depth_mode` / `ir_track_enabled`    | 可能的值： "OFF"、"ACTIVE"、"被動"                                                                    |
| `K4A_IMU_MODE`               | 偏離              | `imu_track_enabled`                  | 可能的值：「開啟」、「關閉」                                                                                   |
| `K4A_CALIBRATION_FILE`       | "calibration.json" | N/A                                  | 請參閱 [`k4a_device_get_raw_calibration()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga8c4e46642cee3115aeb0b33e2b43b24f.html#ga8c4e46642cee3115aeb0b33e2b43b24f) |
| `K4A_DEPTH_DELAY_NS`         | "0"                | `depth_delay_off_color_usec`         | 以毫微秒儲存的值，API 提供微秒。                                                        |
| `K4A_WIRED_SYNC_MODE`        | 獨立       | `wired_sync_mode`                    | 可能的值： "獨立式"、"MASTER"、"從屬"                                                         |
| `K4A_SUBORDINATE_DELAY_NS`   | "0"                | `subordinate_delay_off_master_usec`  | 以毫微秒儲存的值，API 提供微秒。                                                        |
| `K4A_COLOR_FIRMWARE_VERSION` | ""                 | N/A                                  | 裝置顏色固件版本，例如 "1. x xx"                                                            |
| `K4A_DEPTH_FIRMWARE_VERSION` | ""                 | N/A                                  | 裝置深度固件版本，例如 "1.x"                                                            |
| `K4A_DEVICE_SERIAL_NUMBER`   | ""                 | N/A                                  | 錄製裝置序號                                                                                 |
| `K4A_START_OFFSET_NS`        | "0"                | `start_timestamp_offset_usec`        | 請參閱下面的 [時間戳記同步](record-playback-api.md#timestamp-synchronization) 處理。                       |
| `K4A_COLOR_TRACK`            | 無               | N/A                                  | 請參閱 [記錄檔案格式-識別追蹤](record-file-format.md#identifying-tracks)。                     |
| `K4A_DEPTH_TRACK`            | 無               | N/A                                  | 請參閱 [記錄檔案格式-識別追蹤](record-file-format.md#identifying-tracks)。                     |
| `K4A_IR_TRACK`               | 無               | N/A                                  | 請參閱 [記錄檔案格式-識別追蹤](record-file-format.md#identifying-tracks)。                     |
| `K4A_IMU_TRACK`              | 無               | N/A                                  | 請參閱 [記錄檔案格式-識別追蹤](record-file-format.md#identifying-tracks)。                     |

## <a name="timestamp-synchronization"></a>時間戳記同步處理

Matroska 格式需要錄製的開頭必須為零的時間戳記。 在 [外部同步相機](record-external-synchronized-units.md)時，每個裝置的第一個時間戳記可能為非零。

為了在錄製和播放之間保留來自裝置的原始時間戳記，檔案會儲存要套用至時間戳記的位移。

此 `K4A_START_OFFSET_NS` 標記可用來指定時間戳記位移，以便在記錄之後重新同步處理檔案。 這個時間戳記位移可以新增至檔案中的每個時間戳記，以重建原始的裝置時間戳記。

也可以在結構中使用起始位移 [`k4a_record_configuration_t`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/structk4a__record__configuration__t.html) 。
