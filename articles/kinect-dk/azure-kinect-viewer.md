---
title: Azure Kinect 檢視器
description: 瞭解如何使用 Azure Kinect 檢視器將所有裝置資料流程視覺化。
author: tesych
ms.author: tesych
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: azure，kinect，感應器，檢視器，視覺效果，深度，rgb，色彩，imu，音訊，麥克風，點雲端
ms.openlocfilehash: 57cf7df831e97da4143a7f196b69a3a10609a017
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85276470"
---
# <a name="azure-kinect-viewer"></a>Azure Kinect 檢視器

Azure Kinect 檢視器位於 [已安裝的工具] 目錄下 `k4aviewer.exe` （例如， `C:\Program Files\Azure Kinect SDK vX.Y.Z\tools\k4aviewer.exe` 其中 `X.Y.Z` 是已安裝的 SDK 版本），可以用來將所有裝置資料流程視覺化至：

* 確認感應器運作正常。
* 協助定位裝置。
* 使用相機設定進行實驗。
* 讀取裝置設定。
* 使用[Azure Kinect 錄製](azure-kinect-recorder.md)器進行播放錄製。

如需 Azure Kinect 檢視器的詳細資訊，請觀看[如何使用 Azure kinect 影片](https://www.microsoft.com/videoplayer/embed/RE3hNwG)。

Azure Kinect Viewer 是[開放原始](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/tree/develop/tools/k4aviewer)碼，可作為如何使用 api 的範例。

## <a name="use-viewer"></a>使用檢視器

檢視器可以在兩種模式下運作：使用來自感應器的即時資料，或從記錄的資料（[Azure Kinect 錄製](azure-kinect-recorder.md)器）。

### <a name="start-application"></a>啟動應用程式

執行來啟動應用程式 `k4aviewer.exe` 。

![使用檢視器檢查裝置固件版本](./media/how-to-guides/open-viewer.png)

### <a name="use-the-viewer-with-live-data"></a>搭配使用檢視器與即時資料

1. 在 [**開啟裝置**] 區段中，選取要開啟的裝置**序號**。 然後，如果裝置遺失，**請選取 [** 重新整理]。
2. 選取 [**開啟裝置**] 按鈕。
3. 選取 [**啟動**] 以使用預設設定開始串流資料。

### <a name="use-the-viewer-with-recorded-data"></a>搭配記錄的資料使用檢視器

在 [**開啟錄製**] 區段中，流覽至錄製的檔案，然後選取它。

## <a name="check-device-firmware-version"></a>檢查裝置固件版本

在 [設定] 視窗中存取裝置固件版本，如下圖所示。

![使用檢視器檢查裝置固件版本](./media/how-to-guides/check-firmware-update.png)

例如，在此案例中，「攝影機」的深度相機會執行 [FW 1.5.63]。

## <a name="depth-camera"></a>深度相機

深度相機檢視器會顯示兩個視窗：

* 其中一個稱為作用中的*亮度*，這是顯示 IR 亮度的灰階影像。
* 第二個稱為*深度*，其具有深度資料的以色彩標示標記法。

將游標停留在 [深度] 視窗中的圖元，以查看深度感應器的值，如下所示。

![深度視圖](./media/how-to-guides/depth-camera.png)

## <a name="rgb-camera"></a>RGB 相機

下圖顯示 [彩色相機] 視圖。

![RGB 視圖](./media/how-to-guides/viewer-rgb-camera.png)

您可以在串流處理期間，從 [設定] 視窗控制 RGB 攝影機設定。

![RGB 相機控制項](./media/how-to-guides/rgb-camera-settings.png)

## <a name="inertial-measurement-unit-imu"></a>慣性測量單位（IMU）

IMU 視窗有兩個元件：加速計和陀螺儀。

上半部是加速計，並以計量/秒<sup>2</sup>顯示線性加速。  它包含從重力加速，因此如果它在資料表上平面化，Z 軸可能會顯示在-9.8 9.81/s<sup>2</sup>。

下半部是陀螺儀部分，顯示以弧度/秒為單位的旋轉移動

![動作感應器視圖](./media/how-to-guides/viewer-mu-settings.png)

## <a name="microphone-input"></a>麥克風輸入

[麥克風] 視圖會顯示每個麥克風聽到的聲音標記法。 如果沒有音效，圖形就會顯示為空白，否則，您會看到深藍色波形，並在其上方重迭淺藍色波形。

暗波代表麥克風在該時間配量上觀察到的最小值和最大值。 光線代表麥克風在該時間配量上觀察到的值的根平均值。

![麥克風輸入視圖](./media/how-to-guides/microphone-data.png)

## <a name="point-cloud-visualization"></a>指向雲端視覺效果

3D 中的深度視覺化可讓您使用指示的按鍵在影像中移動。

![深度點雲端](./media/how-to-guides/depth-point-cloud.png)

## <a name="synchronization-control"></a>同步控制

設定多重裝置同步處理時，您可以使用此檢視器將裝置設為獨立（預設）、主要或附屬模式。
變更設定或插入/移除同步處理纜線時，**請選取 [** 重新整理] 以更新。

![外部同步處理控制](./media/how-to-guides/sync-control.png)

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
>[外部同步處理設定指南](https://support.microsoft.com/help/4494429/sync-multiple-azure-kinect-dk-devices)
