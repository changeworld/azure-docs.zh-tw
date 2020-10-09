---
title: 使用 Azure Kinect 感應器 SDK 映射轉換
description: 瞭解如何使用 Azure Kinect 感應器 SDK 映射轉換功能。
author: cdedmonds
ms.author: cedmonds
ms.prod: kinect-dk
ms.topic: conceptual
ms.date: 06/26/2019
keywords: kinect、azure、感應器、sdk、協調系統、校正、專案、unproject、轉換、rgb、點雲端
ms.openlocfilehash: df7f2aa13c0e9c0241494e96e720b30f3ff1d8a5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "85276392"
---
# <a name="use-azure-kinect-sensor-sdk-image-transformations"></a>使用 Azure Kinect 感應器 SDK 映射轉換

遵循特定的函式，在 Azure Kinect DK 中的協調攝影機系統之間使用和轉換映射。

## <a name="k4a_transformation-functions"></a>k4a_transformation 函式

 所有前面加上 *k4a_transformation* 的函式都是在整個映射上運作。 它們需要透過[k4a_transformation_create ( # B1](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga853a1a5b6d521bbdf523a69e890c4f10.html#ga853a1a5b6d521bbdf523a69e890c4f10)取得的轉換控制碼[k4a_transformation_t](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/structk4a__transformation__t.html) ，並透過[k4a_transformation_destroy ( # B3](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga7d3ecaae66f26c1a89da9042b1bc6d44.html#ga7d3ecaae66f26c1a89da9042b1bc6d44)未配置。 您也可以參考 SDK [轉換範例](https://github.com/Microsoft/Azure-Kinect-Sensor-SDK/tree/develop/examples/transformation) ，該範例會示範如何使用本主題中的三個函數。

涵蓋的函式如下：

* [`k4a_transformation_depth_image_to_color_camera()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gafacffb5f781a9c2df30d4a16241cd514.html#gafacffb5f781a9c2df30d4a16241cd514)
* [`k4a_transformation_depth_image_to_color_camera_custom()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gac00dd00e7612a86382e3d0a130f276bb.html#gac00dd00e7612a86382e3d0a130f276bb)
* [`k4a_transformation_color_image_to_depth_camera()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gaf3a941f07bb0185cd7a72699a648fc29.html#gaf3a941f07bb0185cd7a72699a648fc29)
* [`k4a_transformation_depth_image_to_point_cloud()`](
https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga7385eb4beb9d8892e8a88cf4feb3be70.html#ga7385eb4beb9d8892e8a88cf4feb3be70)

### <a name="k4a_transformation_depth_image_to_color_camera"></a>k4a_transformation_depth_image_to_color_camera

#### <a name="overview"></a>概觀

 [K4a_transformation_depth_image_to_color_camera ( # B1](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gafacffb5f781a9c2df30d4a16241cd514.html#gafacffb5f781a9c2df30d4a16241cd514)的函式會將深度圖的觀點轉換為彩色攝影機的觀點。 此函式的設計目的是要產生所謂的 RGB-D 影像，其中 D 代表記錄深度值的額外影像通道。 如下圖所示，色彩影像和 [k4a_transformation_depth_image_to_color_camera ( # B1 ](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gafacffb5f781a9c2df30d4a16241cd514.html#gafacffb5f781a9c2df30d4a16241cd514) 的輸出看起來就像是取自相同的觀點，也就是彩色攝影機的觀點。

   ![影像轉換](./media/how-to-guides/image-transformation.png)

#### <a name="implementation"></a>實作

 這個轉換函式比單純呼叫每個圖元的 [k4a_calibration_2d_to_2d ( # B1 ](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga3b6bf6dedbfe67468e2f895dcce68ed4.html#ga3b6bf6dedbfe67468e2f895dcce68ed4) 更為複雜。 它會從深度相機的幾何 warps 三角形網格至彩色攝影機的幾何。 三角形網格是用來避免在轉換的深度影像中產生漏洞。 Z 緩衝區可確保正確處理遮蔽。 預設會啟用此功能的 GPU 加速。

#### <a name="parameters"></a>參數

 輸入參數是轉換控制碼和深度影像。 深度影像解析度必須符合 ```depth_mode``` 建立轉換控制碼時所指定的。 例如，如果轉換控制碼是使用1024x1024 模式所建立 ```K4A_DEPTH_MODE_WFOV_UNBINNED``` ，則深度影像的解析度必須是1024x1024 圖元。 輸出是轉換的深度影像，必須由使用者透過呼叫 [k4a_image_create ( # B1 ](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga859554581bb97a620ff8e92a893e71ef.html#ga859554581bb97a620ff8e92a893e71ef)來配置。 轉換的深度影像解析必須符合 ```color_resolution``` 建立轉換控制碼時所指定的。 例如，如果色彩解析度設定為 `K4A_COLOR_RESOLUTION_1080P` ，輸出影像解析度必須是1920x1080 圖元。 輸出影像的 stride 會設定為 `width * sizeof(uint16_t)` ，因為映射會儲存16位的深度值。

### <a name="k4a_transformation_depth_image_to_color_camera_custom"></a>k4a_transformation_depth_image_to_color_camera_custom

#### <a name="overview"></a>概觀

 [K4a_transformation_depth_image_to_color_camera_custom ( # B1](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gac00dd00e7612a86382e3d0a130f276bb.html#gac00dd00e7612a86382e3d0a130f276bb)的函式會將深度地圖和自訂影像從深度相機的觀點轉換成彩色攝影機的觀點。 以 [k4a_transformation_depth_image_to_color_camera ( # B1 ](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gafacffb5f781a9c2df30d4a16241cd514.html#gafacffb5f781a9c2df30d4a16241cd514)的延伸模組而言，此函式的設計目的是要產生對應的自訂映射，其中每個圖元都符合轉換的深度影像的色彩相機對應圖元座標。

#### <a name="implementation"></a>實作

 此轉換函式會以與 [k4a_transformation_depth_image_to_color_camera ( # B1 ](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gafacffb5f781a9c2df30d4a16241cd514.html#gafacffb5f781a9c2df30d4a16241cd514)相同的方式產生轉換的深度影像。 若要轉換自訂影像，此函式會提供使用線性插補或最接近相鄰插補的選項。 使用線性插補可在轉換後的自訂映射中建立新的值。 使用最接近的相鄰插補將可防止原始影像中的值不會出現在輸出影像中，但會產生較不平滑的影像。 自訂映射應該是單一通道8位或16位。 預設會啟用此功能的 GPU 加速。

#### <a name="parameters"></a>參數

 輸入參數是轉換控制碼、深度影像、自訂影像和插補類型。 深度影像和自訂影像解析度必須符合 `depth_mode` 建立轉換控制碼時所指定的。 例如，如果轉換控制碼是使用1024x1024 模式所建立 `K4A_DEPTH_MODE_WFOV_UNBINNED` ，則深度影像和自訂影像的解析度必須是1024x1024 圖元。 `interpolation_type`必須是 `K4A_TRANSFORMATION_INTERPOLATION_TYPE_LINEAR` 或 `K4A_TRANSFORMATION_INTERPOLATION_TYPE_NEAREST` 。 輸出是轉換的深度影像和轉換過的自訂映射，必須由使用者透過呼叫 [k4a_image_create ( # B1 ](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga859554581bb97a620ff8e92a893e71ef.html#ga859554581bb97a620ff8e92a893e71ef)來配置。 轉換的深度影像和轉換過的自訂影像的解析度必須符合 `color_resolution` 建立轉換控制碼時所指定的。 例如，如果色彩解析度設定為 `K4A_COLOR_RESOLUTION_1080P` ，輸出影像解析度必須是1920x1080 圖元。 輸出深度影像 stride 會設定為 `width * sizeof(uint16_t)` ，因為映射會儲存16位的深度值。 輸入自訂映射和已轉換的自訂映射必須是格式 `K4A_IMAGE_FORMAT_CUSTOM8` 或 `K4A_IMAGE_FORMAT_CUSTOM16` ，應據以設定對應的影像 stride。 

### <a name="k4a_transformation_color_image_to_depth_camera"></a>k4a_transformation_color_image_to_depth_camera

#### <a name="overview"></a>概觀

 [K4a_transformation_color_image_to_depth_camera ( # B1](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gaf3a941f07bb0185cd7a72699a648fc29.html#gaf3a941f07bb0185cd7a72699a648fc29)的函式會將色彩影像從彩色攝影機的觀點轉換成深度相機的觀點 (請參閱上圖) 。 可以用來產生 RGB-D 映射。

#### <a name="implementation"></a>實作

 針對深度對應的每個圖元，此函式會使用圖元的深度值來計算色彩影像中的對應子圖元座標。 接著，我們會在色彩影像中查詢此座標的色彩值。 雙線性插補會在色彩影像中執行，以取得子圖元精確度的色彩值。 沒有相關聯深度讀取的圖元會指派給 `[0,0,0,0]` 輸出影像中的 BGRA 值。 預設會啟用此功能的 GPU 加速。 因為此方法會在已轉換的色彩影像中產生漏洞，而不會處理遮蔽，所以建議您改為使用函式 [k4a_transformation_depth_image_to_color_camera ( # B1 ](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gafacffb5f781a9c2df30d4a16241cd514.html#gafacffb5f781a9c2df30d4a16241cd514) 。

#### <a name="parameters"></a>參數

輸入參數是轉換控制碼、深度影像和彩色影像。 深度和色彩影像的解析度必須符合在建立轉換控制碼時所指定的 depth_mode 和 color_resolution。 輸出是轉換過的色彩影像，必須由使用者透過呼叫 [k4a_image_create ( # B1 ](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga859554581bb97a620ff8e92a893e71ef.html#ga859554581bb97a620ff8e92a893e71ef)來配置。 轉換的色彩影像解析度必須符合建立轉換控制碼時所指定的 depth_resolution。 輸出映射會儲存代表每個圖元 BGRA 的 4 8 位值。 因此，影像的 stride 為 ```width * 4 * sizeof(uint8_t)``` 。 資料順序是圖元交錯的，也就是 blue value-圖元0、綠值-圖元0、紅色值-圖元0、Alpha 值-圖元0、blue value-圖元1等等。

### <a name="k4a_transformation_depth_image_to_point_cloud"></a>k4a_transformation_depth_image_to_point_cloud

#### <a name="overview"></a>概觀

函式 [k4a_transformation_depth_image_to_point_cloud ( # B1 ](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga7385eb4beb9d8892e8a88cf4feb3be70.html#ga7385eb4beb9d8892e8a88cf4feb3be70) 會將相機拍攝的2d 深度地圖，轉換成相同相機座標系統中的3d 點雲端。 攝影機可以是深度或彩色相機。

#### <a name="implementation"></a>實作

 函式會為每個圖元提供執行 [k4a_calibration_2d_to_2d ( # B1 ](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga3b6bf6dedbfe67468e2f895dcce68ed4.html#ga3b6bf6dedbfe67468e2f895dcce68ed4) 的相等結果，但其計算效率更高。 當呼叫 [k4a_transformation_create ( # B1 ](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga853a1a5b6d521bbdf523a69e890c4f10.html#ga853a1a5b6d521bbdf523a69e890c4f10)時，我們會預先計算所謂的 xy lookup 資料表，以儲存每個影像圖元的 x 和 y 縮放比例。 呼叫 [k4a_transformation_depth_image_to_point_cloud ( # B1 ](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga7385eb4beb9d8892e8a88cf4feb3be70.html#ga7385eb4beb9d8892e8a88cf4feb3be70)時，我們會將圖元的 x 縮放比例乘以圖元的 Z 座標，以取得圖元的 3d X 座標。 類似，3D Y 座標是由 Y 縮放比例的乘法計算。 SDK 的 [快速點雲端範例](https://github.com/Microsoft/Azure-Kinect-Sensor-SDK/tree/develop/examples/fastpointcloud) 會示範如何計算 xy 資料表。 使用者可以遵循範例程式碼來執行自己的此函式版本，例如，以加速其 GPU 管線。

#### <a name="parameters"></a>參數

 輸入參數是轉換控制碼、攝影機規範和深度影像。 如果相機規範設定為深度，則深度影像的解析度必須符合建立轉換控制碼時所指定的 depth_mode。 否則，如果指定名稱是設定為彩色攝影機，則解析度必須符合所選 color_resolution 的解析度。 Output 參數是一種 XYZ 映射，必須由使用者透過呼叫 [k4a_image_create ( # B1 ](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga859554581bb97a620ff8e92a893e71ef.html#ga859554581bb97a620ff8e92a893e71ef)來配置。 XYZ 映射解析度必須符合輸入深度對應的解析度。 我們會為每個圖元儲存三個帶正負號16位座標值（以毫米為單位）。 因此，XYZ 映射 stride 會設定為 `width * 3 * sizeof(int16_t)` 。 資料順序是圖元交錯的，也就是 X 座標–圖元0、Y 座標–圖元0、Z 座標–圖元0、X 座標–圖元1等等。 如果無法將圖元轉換成3D，則函式會將值指派 `[0,0,0]` 給圖元。

## <a name="samples"></a>範例

[轉換範例](https://github.com/Microsoft/Azure-Kinect-Sensor-SDK/tree/develop/examples/transformation)

## <a name="next-steps"></a>後續步驟

現在您已瞭解如何使用 Azure Kinect 感應器 SDK 映射轉換功能，您也可以瞭解

>[!div class="nextstepaction"]
>[Azure Kinect 感應器 SDK 校正功能](use-calibration-functions.md)

您也可以複習

[座標系統](coordinate-systems.md)
