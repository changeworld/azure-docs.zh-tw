---
title: Azure Kinect DK Windows 比較
description: Azure Kinect DK 和 Kinect for Windows v2 之間的硬體和軟體差異
author: tesych
ms.author: tesych
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: article
keywords: Kinect，Windows，v2，Azure Kinect，比較，SDK，差異，硬體，軟體
ms.openlocfilehash: 0a8d399370f354524858bdd658ffd65c0494dd4d
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87031569"
---
# <a name="azure-kinect-and-kinect-windows-v2-comparison"></a>Azure Kinect 和 Kinect Windows v2 的比較

Azure Kinect DK 的硬體和軟體發展工具組與 Kinect for Windows v2 有不同之處。 任何現有的 Kinect for Windows v2 應用程式都不會直接與 Azure Kinect DK 搭配使用，而且需要移植至新的 SDK。  

## <a name="hardware"></a>硬體

下表列出 Azure Kinect 開發工具組和 Kinect for Windows v2 之間的高階差異。

| 功能 | 類型 | Azure Kinect DK | Kinect for Windows v2 |
| ------- | ---- | --------------- | --------------------- |
| **音訊** | 詳細資料  | 7-mic 迴圈陣列 | 4-mic 線性分段陣列 |
| **動作感應器** | 詳細資料 | 3軸加速計 3-軸 gyro | 3軸加速計 |
| **RGB 相機**    | 詳細資料 | 3840 x 2160 px @30 fps | 1920 x 1080 px @30 fps |
| **深度攝影機**  | 方法   | 航班時間 | 航班時間 |
|                   | 解決方案 | 640 x 576 px @30 fps | 512 x 424 px @ 30 fps |
|                   |            | 512 x 512 px @30 fps |                       |
|                   |            | 1024x1024 px @15 fps |                       |
| **連線能力** | 資料 | USB 3.1 Gen 1 （類型為 USB-C）  | USB 3.1 gen 1|
|  | 電源 | 外部 PSU 或 USB-C | 外部 PSU |
|  | 同步處理 | RGB & 深度內部、外部裝置到裝置| RGB & 深度僅限內部 |
| **散熱** | 維度 | 103 x 39 x 126 mm | 249 x 66 x 67 mm |
|  | 大規模 | 440 g | 970 g |
| | 掛接 | 一個 1/4-20 UNC。 四個內部螺絲點 | 一個 1/4-20 UNC |

尋找[Azure Kinect DK 硬體](hardware-specification.md)檔中的其他詳細資料。

## <a name="sensor-access"></a>感應器存取

下表提供低層級的裝置感應器存取功能比較。

| **功能**| **Azure Kinect** | **Kinect for Windows** | **備註** |
|---------|---------|------------|---------|
| **Depth** | ✔️ | ✔️ |    |   |
| **IR** | ✔️ | ✔️ |  |
| **色彩** | ✔️ | ✔️ | 色彩格式支援差異，Azure Kinect DK 支援下列相機控制項：曝光、白平衡、亮度、對比、飽和度、清晰度和增益控制 |
| **音訊** | ✔️ | ✔️ | Azure Kinect DK 麥克風可透過語音 SDK 或 Windows 原生 API 存取 |
| **IMU** | ✔️ |  | Azure Kinect DK 具有完整的6軸 IMU，而 Kinect for Windows 僅提供1軸 |
| **校正資料** | ✔️ | ✔️ | OpenCV 相容相機模型校正 |
| **深度-RGB 內部同步處理** | ✔️ | ✔️ |  |
| **外部同步**| ✔️|  | Azure Kinect DK 允許外部同步的可程式化延遲 |
| **共用多個用戶端的存取權** | | ✔️ | Azure Kinect 感應器 SDK 依賴 WinUSB/libUSB 來存取裝置，而且沒有已執行的服務可讓您以多個進程共用裝置存取 |
| **串流記錄/播放工具** | ✔️ | ✔️ | Azure Kinect DK 使用以開放原始碼 Matroska 容器為基礎的執行 |

## <a name="features"></a>功能

Azure Kinect SDK 功能集與 Kinect for Windows v2 不同，如下所述：

| **Kinect v2 功能** | **Kinect v2 資料類型** | **Azure Kinect SDK/服務** |
|--------|--------|------|
| 感應器資料存取 |DepthFrame| [感應器 SDK-取出影像](retrieve-images.md) 
| |InfraredFrame | [感應器 SDK-取出影像](retrieve-images.md) 
| | ColorFrame | [感應器 SDK-取出影像](retrieve-images.md) | 
| | AudioBeamFrame |目前不支援 
| 主體追蹤 | BodyFrame | Body 追蹤 SDK |
| | BodyIndexFrame | Body 追蹤 SDK  |
| 座標組應|CoordinateMapper| [感應器 SDK-影像轉換](use-image-transformation.md) |
|臉部追蹤 | FaceFrame | [認知服務：臉部](https://azure.microsoft.com/services/cognitive-services/face/)       |
|    語音辨識    |    不適用                      |    [認知服務：語音](https://azure.microsoft.com/services/cognitive-services/directory/speech/)     |

## <a name="next-steps"></a>後續步驟

[Kinect for Windows 開發人員頁面](https://developer.microsoft.com/windows/kinect)
