---
title: 存取主體框架中的 Azure Kinect DK 資料
description: 瞭解本文框架中的資料，以及在 Azure Kinect DK 中存取該資料的函數。
author: qm13
ms.author: quentinm
ms.reviewer: yijwan
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: how-to
keywords: 主體，框架，azure，kinect，本文，追蹤，秘訣
ms.openlocfilehash: be44f59cb84e99129bf526575293eee69ca64598
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85276536"
---
# <a name="access-data-in-body-frame"></a>存取全身框架中的資料

本文描述包含在主體框架中的資料，以及用來存取該資料的函數。

涵蓋的函式如下：

- [k4abt_frame_get_body_id （）](https://microsoft.github.io/Azure-Kinect-Body-Tracking/release/1.x.x/group__btfunctions_ga1d612404d133a279af847974e9359a92.html#ga1d612404d133a279af847974e9359a92)
- [k4abt_frame_get_body_index_map （）](https://microsoft.github.io/Azure-Kinect-Body-Tracking/release/1.x.x/group__btfunctions_ga0e4f2d0d7e330d444de7070fb1fee4f6.html#ga0e4f2d0d7e330d444de7070fb1fee4f6)
- [k4abt_frame_get_body_skeleton （）](https://microsoft.github.io/Azure-Kinect-Body-Tracking/release/1.x.x/group__btfunctions_gac7032ab06268253538556750775064fb.html#gac7032ab06268253538556750775064fb)
- [k4abt_frame_get_capture （）](https://microsoft.github.io/Azure-Kinect-Body-Tracking/release/1.x.x/group__btfunctions_gad9eef11f6496bbfe997536c374217d9a.html#gad9eef11f6496bbfe997536c374217d9a)
- [k4abt_frame_get_num_bodies （）](https://microsoft.github.io/Azure-Kinect-Body-Tracking/release/1.x.x/group__btfunctions_ga29ab088b1a0d1a246bdb5542e21aa3c3.html#ga29ab088b1a0d1a246bdb5542e21aa3c3)
- [k4abt_frame_get_device_timestamp_usec （）](https://microsoft.github.io/Azure-Kinect-Body-Tracking/release/1.x.x/group__btfunctions_ga04be7b814b40296cd6b97044ed7283e4.html#ga04be7b814b40296cd6b97044ed7283e4)

## <a name="key-components-of-a-body-frame"></a>主體框架的主要元件

每個主體框架都包含主體結構的集合、2D 主體索引對應，以及產生此結果的輸入捕獲。

![主體框架元件](./media/how-to-guides/body-frame.png)

## <a name="access-the-collection-of-body-structs"></a>存取主體結構的集合

在單一捕獲中，可能會偵測到多個主體。 您可以藉由呼叫[k4abt_frame_get_num_bodies （）](https://microsoft.github.io/Azure-Kinect-Body-Tracking/release/1.x.x/group__btfunctions_ga29ab088b1a0d1a246bdb5542e21aa3c3.html#ga29ab088b1a0d1a246bdb5542e21aa3c3)函數來查詢主體數目。

```C
size_t num_bodies = k4abt_frame_get_num_bodies(body_frame);
```

您可以使用[k4abt_frame_get_body_id （）](https://microsoft.github.io/Azure-Kinect-Body-Tracking/release/1.x.x/group__btfunctions_ga1d612404d133a279af847974e9359a92.html#ga1d612404d133a279af847974e9359a92)和[k4abt_frame_get_body_skeleton （）](https://microsoft.github.io/Azure-Kinect-Body-Tracking/release/1.x.x/group__btfunctions_gac7032ab06268253538556750775064fb.html#gac7032ab06268253538556750775064fb)函式來逐一查看每個主體索引，以尋找主體識別碼和聯合位置/方向資訊。

```C
for (size_t i = 0; i < num_bodies; i++)
{
    k4abt_skeleton_t skeleton;
    k4abt_frame_get_body_skeleton(body_frame, i, &skeleton);
    uint32_t id = k4abt_frame_get_body_id(body_frame, i);
}
```

## <a name="access-the-body-index-map"></a>存取主體索引對應

您可以使用[k4abt_frame_get_body_index_map （）](https://microsoft.github.io/Azure-Kinect-Body-Tracking/release/1.x.x/group__btfunctions_ga0e4f2d0d7e330d444de7070fb1fee4f6.html#ga0e4f2d0d7e330d444de7070fb1fee4f6)函數來存取內文索引對應。 如需主體索引對應的詳細說明，請參閱[主體索引對應](body-index-map.md)。 當不再需要時，請務必釋放主體索引對應。

```C
k4a_image_t body_index_map = k4abt_frame_get_body_index_map(body_frame);
...  // Do your work with the body index map
k4a_image_release(body_index_map);
```

## <a name="access-the-input-capture"></a>存取輸入捕獲

主體追蹤程式是非同步 API。 原始的 capture 可能已經在輸出結果時發行。 使用[k4abt_frame_get_capture （）](https://microsoft.github.io/Azure-Kinect-Body-Tracking/release/1.x.x/group__btfunctions_gad9eef11f6496bbfe997536c374217d9a.html#gad9eef11f6496bbfe997536c374217d9a)函數來查詢用來產生此主體追蹤結果的輸入捕獲。 每次呼叫此函式時，都會增加 k4a_capture_t 的參考計數。 當不再需要 capture 時，請使用[k4a_capture_release （）](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga0ed6f74ec403c3eac1b8ef3afb32cee6.html#ga0ed6f74ec403c3eac1b8ef3afb32cee6)函數。

```C
k4a_capture_t input_capture = k4abt_frame_get_capture(body_frame);
... // Do your work with the input capture
k4a_capture_release(input_capture);
```

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
>[Azure Kinect 全身追蹤 SDK](https://microsoft.github.io/Azure-Kinect-Body-Tracking/)
