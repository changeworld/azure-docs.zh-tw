---
title: 關於 Azure Kinect 感應器 SDK
description: 概述 Azure Kinect 感應器軟體發展工具組 (SDK) 、其功能和工具。
author: tesych
ms.author: tesych
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: article
keywords: azure、kinect、rgb、IR、錄製、感應器、sdk、存取、深度、影片、相機、imu、移動、感應器、音訊、麥克風、matroska、感應器 sdk、下載
ms.openlocfilehash: 17c1b33120eacb5d0c6d3c02e692d1488ef474e6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "85276506"
---
# <a name="about-azure-kinect-sensor-sdk"></a>關於 Azure Kinect 感應器 SDK

本文概要說明 Azure Kinect 感應器軟體發展工具組 (SDK) 、其功能和工具。

## <a name="features"></a>特性

Azure Kinect 感應器 SDK 為 Azure Kinect 裝置設定和硬體感應器串流提供跨平臺低層級的存取權，包括：

- 景深相機存取與模式控制 (被動 IR 模式，加上寬和窄視角景深模式) 
- RGB 相機存取與控制 (例如，曝光和白平衡) 
- 動作感應器 (陀螺儀和加速計) 存取 
- 同步的景深 RGB 相機串流，並可設定相機之間的延遲 
- 外部裝置同步控制，並可設定裝置之間的延遲偏移 
- 相機框架中繼資料存取，可用於影像解析度、時間戳記等。 
- 裝置校正資料存取 

## <a name="tools"></a>工具

- [Azure Kinect 檢視器](azure-kinect-viewer.md)，可監視裝置資料流程並設定不同的模式。
- 使用[Matroska 容器格式](record-file-format.md)的[Azure Kinect 錄製](azure-kinect-recorder.md)器和播放讀取器 API。
- Azure Kinect DK 的 [固件更新工具](azure-kinect-firmware-tool.md)。

## <a name="sensor-sdk"></a>感應器 SDK

- [下載感應器 SDK](sensor-sdk-download.md)。
- 您可以在 [GitHub 上的開放原始](https://github.com/microsoft/Azure-Kinect-Sensor-SDK)碼中取得感應器 SDK。
- 如需使用方式的詳細資訊，請參閱 [感應器 SDK API 檔](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/index.html)。

## <a name="next-steps"></a>後續步驟

現在您已瞭解 Azure Kinect 感應器 SDK，您也可以：
>[!div class="nextstepaction"]
>[下載感應器 SDK 程式碼](sensor-sdk-download.md)

>[!div class="nextstepaction"]
>[尋找並開啟裝置](find-then-open-device.md)
