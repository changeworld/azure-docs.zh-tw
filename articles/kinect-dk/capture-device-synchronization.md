---
title: 捕獲 Azure Kinect 裝置同步處理
description: 瞭解如何使用 Azure Kinect 感應器 SDK 同步處理 Azure Kinect capture 裝置。
author: xthexder
ms.author: jawirth
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: kinect，azure，感應器，sdk，深度，rgb，內部，外部，同步處理，菊輪鍊，階段位移
ms.openlocfilehash: 914a29882277c11c388d3f81f0b1fe827fc2f156
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85276489"
---
# <a name="capture-azure-kinect-device-synchronization"></a>捕獲 Azure Kinect 裝置同步處理

Azure Kinect 硬體可以對齊色彩和深度影像的拍攝時間。 相同裝置上的相機與**內部同步**處理之間的對齊方式。 跨多個已連線裝置的捕捉時間對齊是**外部同步**處理。

## <a name="device-internal-synchronization"></a>裝置內部同步處理

個別相機之間的影像捕捉會在硬體中進行同步處理。 在每個包含彩色和深度感應器影像的[k4a_capture_t](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/structk4a__capture__t.html)中，映射的時間戳記會根據硬體的作業模式來對齊。 根據預設，捕捉的影像會對齊曝光中心。 深度和色捕捉的相對時間可以使用 k4a_device_configuration_t 的欄位來調整 `depth_delay_off_color_usec` 。 [k4a_device_configuration_t](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/structk4a__device__configuration__t.html)

## <a name="device-external-synchronization"></a>裝置外部同步處理

請參閱設定硬體設定的[外部同步](https://support.microsoft.com/help/4494429/sync-multiple-azure-kinect-dk-devices)處理。

每個連線裝置的軟體都必須設定為以**主要**或**從屬**模式運作。 此設定是在[k4a_device_configuration_t](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/structk4a__device__configuration__t.html)上設定。

使用外部同步處理時，應該一律在主要相機之前啟動附屬攝影機，時間戳記才會正確對齊。

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

### <a name="retrieving-synchronization-jack-state"></a>正在抓取同步處理插座狀態

若要以程式設計方式取出同步處理輸入和同步處理輸出插孔的目前狀態，請使用[k4a_device_get_sync_jack](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga0209ac87bfd055163677321b0304e962.html#ga0209ac87bfd055163677321b0304e962)函式。

## <a name="next-steps"></a>後續步驟

現在您已瞭解如何啟用和捕獲裝置同步處理。 您也可以查看如何使用 

>[!div class="nextstepaction"]
>[Azure Kinect 感應器 SDK 記錄和播放 API](record-playback-api.md)
