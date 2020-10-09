---
title: 捕獲 Azure Kinect 裝置同步處理
description: 瞭解如何使用 Azure Kinect 感應器 SDK 來同步處理 Azure Kinect capture 裝置。
author: xthexder
ms.author: jawirth
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: kinect、azure、感應器、sdk、深度、rgb、內部、外部、同步處理、菊輪鍊、階段位移
ms.openlocfilehash: 914a29882277c11c388d3f81f0b1fe827fc2f156
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "85276489"
---
# <a name="capture-azure-kinect-device-synchronization"></a>捕獲 Azure Kinect 裝置同步處理

Azure Kinect 硬體可以對齊色彩和深度影像的捕獲時間。 相同裝置上的相機之間的對齊是 **內部同步**處理。 跨多個連線裝置的捕獲時間調整為 **外部同步**處理。

## <a name="device-internal-synchronization"></a>裝置內部同步處理

個別相機之間的影像捕捉會在硬體中同步處理。 在每個包含色彩和深度感應器影像的 [k4a_capture_t](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/structk4a__capture__t.html) 中，映射的時間戳記會根據硬體的操作模式來對齊。 根據預設，捕捉的影像會對齊曝光中心。 您可以使用 k4a_device_configuration_t 的欄位來調整深度和色彩捕捉的相對時機 `depth_delay_off_color_usec` 。 [k4a_device_configuration_t](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/structk4a__device__configuration__t.html)

## <a name="device-external-synchronization"></a>裝置外部同步處理

請參閱設定硬體設定的 [外部同步](https://support.microsoft.com/help/4494429/sync-multiple-azure-kinect-dk-devices) 處理。

每個連線裝置的軟體都必須設定為以 **主要** 或 **次級** 模式運作。 這項設定是在 [k4a_device_configuration_t](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/structk4a__device__configuration__t.html)上進行設定。

使用外部同步處理時，應該一律先啟動附屬攝影機，才能讓時間戳記正確對齊。

### <a name="subordinate-mode"></a>從屬模式

```C
k4a_device_configuration_t deviceConfig;
deviceConfig.wired_sync_mode = K4A_WIRED_SYNC_MODE_SUBORDINATE
```

### <a name="master-mode"></a>主要模式

```C
k4a_device_configuration_t deviceConfig;
deviceConfig.wired_sync_mode = K4A_WIRED_SYNC_MODE_MASTER;
```

### <a name="retrieving-synchronization-jack-state"></a>正在抓取同步處理插孔狀態

若要以程式設計方式取得同步處理輸入和同步處理輸出的目前狀態，請使用 [k4a_device_get_sync_jack](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga0209ac87bfd055163677321b0304e962.html#ga0209ac87bfd055163677321b0304e962) 函數。

## <a name="next-steps"></a>後續步驟

現在您已瞭解如何啟用和捕獲裝置同步處理。 您也可以複習如何使用 

>[!div class="nextstepaction"]
>[Azure Kinect 感應器 SDK 記錄和播放 API](record-playback-api.md)
