---
title: 使用 Azure Kinect 校正功能
description: 瞭解如何使用 Azure Kinect DK 的校正函數。
author: cdedmonds
ms.author: cedmonds
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: kinect，azure，感應器，sdk，座標系統，校正，函式，攝影機，內部，外部，專案，unproject，轉換，rgb-d，點雲端
ms.openlocfilehash: a501765222154bde4ed35e878a9d18042893fca2
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85276477"
---
# <a name="use-azure-kinect-calibration-functions"></a>使用 Azure Kinect 校正功能

校正功能允許在 Azure Kinect 裝置上的每個感應器座標系統之間轉換點。 需要轉換整個影像的應用程式可能會利用[轉換](use-image-transformation.md)函式中可用的加速作業。

## <a name="retrieve-calibration-data"></a>取出校正資料

您必須抓取裝置校正，才能執行座標系統轉換。 校正資料會儲存在[k4a_calibration_t](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/structk4a__calibration__t.html#details)資料類型中。 它是透過函式[k4a_device_get_calibration （）](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga4e43940d8d8db48da266c7a7842c8d78.html#ga4e43940d8d8db48da266c7a7842c8d78)從裝置取得。 校正資料並非僅適用于每個裝置，也是相機的作業模式。 因此[k4a_device_get_calibration （）](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga4e43940d8d8db48da266c7a7842c8d78.html#ga4e43940d8d8db48da266c7a7842c8d78)需要 `depth_mode` 和 `color_resolution` 參數作為輸入。

### <a name="opencv-compatibility"></a>OpenCV 相容性

校正參數與[OpenCV](https://opencv.org/)相容。 如需個別相機校正參數的詳細資訊，另請參閱[OpenCV 檔](https://docs.opencv.org/3.2.0/d9/d0c/group__calib3d.html#gga7041b2a9c8f9f8ee93a2796981bc5546a204766e24f2e413e7a7c9f8b9e93f16c)。 另請參閱 SDK 的[OpenCV 相容性範例](https://github.com/Microsoft/Azure-Kinect-Sensor-SDK/tree/develop/examples/opencv_compatibility)，其示範如何在[k4a_calibration_t](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/structk4a__calibration__t.html#details)類型與對應的 OpenCV 資料結構之間進行轉換。

## <a name="coordinate-transformation-functions"></a>座標轉換函數

下圖顯示 Azure Kinect 的不同座標系統，以及要在其間轉換的函式。 我們省略了陀螺儀和加速計的3D 座標系統，讓圖表保持簡單。

   ![座標轉換](./media/how-to-guides/coordinate-transformation.png)

鏡頭扭曲的備註：2D 座標一律會參考 SDK 中扭曲的影像。 SDK 的[undistortion 範例](https://github.com/Microsoft/Azure-Kinect-Sensor-SDK/tree/develop/examples/undistort)會示範影像 undistortion。 一般而言，3D 點永遠不會受到鏡頭扭曲的影響。

### <a name="convert-between-3d-coordinate-systems"></a>3D 座標系統之間的轉換

函式[k4a_calibration_3d_to_3d （）](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gaacd1eab997ef964b5e436afa5295726e.html#gaacd1eab997ef964b5e436afa5295726e)會使用相機的外部校正，將來源座標系統的3d 點轉換成目標座標系統的3d 點。 [來源] 和 [目標] 可以設定為四個3D 座標系統的任一種，也就是相機、深度相機、陀螺儀或加速計。 如果來源和目標相同，則會傳回未修改的輸入3D 點做為輸出。

### <a name="convert-between-2d-and-3d-coordinate-systems"></a>2D 和3D 座標系統之間的轉換

函數[k4a_calibration_3d_to_2d （）](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga2ed8b51d727425caa942aab190fc2ba9.html#ga2ed8b51d727425caa942aab190fc2ba9)會將來源座標系統的3d 點轉換成目標相機的2d 圖元座標。 此函式通常稱為「專案函式」（project function）。 雖然來源可以設定為四個3D 座標系統的任一種，目標必須是深度或彩色攝影機。 如果來源和目標不同，則會使用[k4a_calibration_3d_to_3d （）](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gaacd1eab997ef964b5e436afa5295726e.html#gaacd1eab997ef964b5e436afa5295726e)，將輸入3d 點轉換成目標相機的3d 座標系統。 在目標相機座標系統中表示3D 點之後，就會使用目標相機的內建校正來計算對應的2D 圖元座標。 如果3D 點落在目標相機的可見區域外，有效的值會設定為0。

函數[k4a_calibration_2d_to_3d （）](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga664602bdb48dab38117a6c1d14b880de.html#ga664602bdb48dab38117a6c1d14b880de)會將來源攝影機的2d 圖元座標轉換成目標相機座標系統的3d 點。 來源必須是彩色或深度攝影機。 目標可以設定為四個3D 座標系統中的任何一個。 除了2D 圖元座標以外，來源相機影像中的圖元深度值（以毫米為單位）是函式的輸入，其中一種方法就是使用函式[k4a_transformation_depth_image_to_color_camera （）](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gafacffb5f781a9c2df30d4a16241cd514.html#gafacffb5f781a9c2df30d4a16241cd514)。 函式會使用來源攝影機的內建校正，透過指定的圖元座標來計算從來源攝影機焦點開始的3D 光線。 然後，會使用 [深度] 值來尋找此光線上3D 點的確切位置。 這種作業通常稱為 unproject 函數。 如果來源和目標相機不同，函式會透過[k4a_calibration_3d_to_3d （）](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gaacd1eab997ef964b5e436afa5295726e.html#gaacd1eab997ef964b5e436afa5295726e)，將3d 點轉換成目標的座標系統。 如果2D 圖元座標落在來源攝影機的可見區域中，則有效的值會設定為0。

### <a name="converting-between-2d-coordinate-systems"></a>在2D 座標系統之間轉換

函數[k4a_calibration_2d_to_2d （）](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga3b6bf6dedbfe67468e2f895dcce68ed4.html#ga3b6bf6dedbfe67468e2f895dcce68ed4)會將來源攝影機的2d 圖元座標轉換成目標相機的2d 圖元座標。 [來源] 和 [目標] 必須設定為 [彩色] 或 [深度相機]。 函式需要來源相機影像中的圖元深度值（以毫米為單位）做為輸入，在 [相機幾何] 中衍生深度值的方法之一，是使用函數[k4a_transformation_depth_image_to_color_camera （）](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gafacffb5f781a9c2df30d4a16241cd514.html#gafacffb5f781a9c2df30d4a16241cd514)。 它會呼叫[k4a_calibration_2d_to_3d （）](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga664602bdb48dab38117a6c1d14b880de.html#ga664602bdb48dab38117a6c1d14b880de)以轉換成來源相機系統的3d 點。 然後，它會呼叫[k4a_calibration_3d_to_2d （）](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga2ed8b51d727425caa942aab190fc2ba9.html#ga2ed8b51d727425caa942aab190fc2ba9) ，以轉換為目標相機影像的2d 圖元座標。 如果[k4a_calibration_2d_to_3d （）](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga664602bdb48dab38117a6c1d14b880de.html#ga664602bdb48dab38117a6c1d14b880ded)或[k4a_calibration_3d_to_2d （）](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga2ed8b51d727425caa942aab190fc2ba9.html#ga2ed8b51d727425caa942aab190fc2ba9)傳回不正確結果，有效的值會設定為0。

## <a name="related-samples"></a>相關範例

- [OpenCV 相容性範例](https://github.com/Microsoft/Azure-Kinect-Sensor-SDK/tree/develop/examples/opencv_compatibility)
- [Undistortion 範例](https://github.com/Microsoft/Azure-Kinect-Sensor-SDK/tree/develop/examples/undistort)
- [快速點雲端範例](https://github.com/Microsoft/Azure-Kinect-Sensor-SDK/tree/develop/examples/fastpointcloud)

## <a name="next-steps"></a>後續步驟

現在您已瞭解相機 calibrations，您也可以瞭解如何
>[!div class="nextstepaction"]
>[擷取裝置同步處理](capture-device-synchronization.md)

此外，您還可以複習

[座標系統](coordinate-systems.md)
