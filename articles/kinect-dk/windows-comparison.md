---
title: Azure Kinect DK Windows 比較
description: Azure Kinect DK 與 Kinect for Windows v2 之間的硬體和軟體差異
author: tesych
ms.author: tesych
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: article
keywords: Kinect、Windows、v2、Azure Kinect、比較、SDK、差異、硬體、軟體
ms.openlocfilehash: 0a8d399370f354524858bdd658ffd65c0494dd4d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "87031569"
---
# <a name="azure-kinect-and-kinect-windows-v2-comparison"></a>Azure Kinect 和 Kinect Windows v2 比較

Azure Kinect DK 的硬體和軟體發展工具組與 Kinect for Windows v2 有所差異。 任何現有的 Kinect for Windows v2 應用程式都不會直接使用 Azure Kinect DK，且需要移植至新的 SDK。  

## <a name="hardware"></a>硬體

下表列出 Azure Kinect 開發工具組和 Kinect for Windows v2 之間的高層級差異。

| 功能 | 類型 | Azure Kinect DK | 適用于 Windows v2 的 Kinect |
| ------- | ---- | --------------- | --------------------- |
| **音訊** | 詳細資料  | 7-mic 迴圈陣列 | 4-mic 線性分階段陣列 |
| **動作感應器** | 詳細資料 | 3軸加速計 3-軸回轉 | 3軸加速計 |
| **RGB 相機**    | 詳細資料 | 3840 x 2160 px @30 fps | 1920 x 1080 px @30 fps |
| **深度相機**  | 方法   | 航班時間 | 航班時間 |
|                   | 解決方案 | 640 x 576 px @30 fps | 512 x 424 px @ 30 fps |
|                   |            | 512 x 512 px @30 fps |                       |
|                   |            | 1024x1024 px @15 fps |                       |
| **連線能力** | 資料 | USB 3.1 Gen 1 類型 USB-C  | USB 3.1 gen 1|
|  | 電源 | 外部 PSU 或 USB-C | 外部 PSU |
|  | 同步處理 | RGB & 深度內部、外部裝置到裝置| RGB & 深度僅限內部 |
| **機械** | 維度 | 103 x 39 x 126 mm | 249 x 66 x 67 mm |
|  | 品質 | 440 g | 970 g |
| | 安裝 | 一個 1/4-20 UNC。 四個內部螺釘點 | 一個 1/4-20 UNC |

在 [Azure Kinect DK 硬體](hardware-specification.md) 檔中尋找其他詳細資料。

## <a name="sensor-access"></a>感應器存取

下表提供低層級的「裝置感應器」存取功能比較。

| **功能**| **Azure Kinect** | **適用于 Windows 的 Kinect** | **備註** |
|---------|---------|------------|---------|
| **Depth** | ✔️ | ✔️ |    |   |
| **IR** | ✔️ | ✔️ |  |
| **色彩** | ✔️ | ✔️ | 色彩格式支援差異，Azure Kinect DK 支援這些相機控制項：曝光、白色餘額、亮度、對比、飽和度、清晰度和增益控制 |
| **音訊** | ✔️ | ✔️ | Azure Kinect DK 麥克風可透過語音 SDK 或 Windows 原生 API 來存取 |
| **Imu** | ✔️ |  | Azure Kinect DK 有完整的6軸 IMU，且 Kinect for Windows 僅提供1軸 |
| **校正資料** | ✔️ | ✔️ | OpenCV 相容的相機模型校正 |
| **深度-RGB 內部同步處理** | ✔️ | ✔️ |  |
| **外部同步處理**| ✔️|  | Azure Kinect DK 允許外部同步的可程式化延遲 |
| **與多個用戶端共用存取權** | | ✔️ | Azure Kinect 感應器 SDK 依賴 WinUSB/libUSB 來存取裝置，而且沒有實行服務來啟用使用多個進程的共用裝置存取權 |
| **串流記錄/播放工具** | ✔️ | ✔️ | Azure Kinect DK 使用開放原始碼 Matroska 容器型的實作為基礎 |

## <a name="features"></a>特性

Azure Kinect SDK 功能集與 Kinect for Windows v2 不同，如下所述：

| **Kinect v2 功能** | **Kinect v2 資料類型** | **Azure Kinect SDK/服務** |
|--------|--------|------|
| 感應器資料存取 |DepthFrame| [感應器 SDK-取出影像](retrieve-images.md) 
| |InfraredFrame | [感應器 SDK-取出影像](retrieve-images.md) 
| | ColorFrame | [感應器 SDK-取出影像](retrieve-images.md) | 
| | AudioBeamFrame |目前不支援 
| 主體追蹤 | BodyFrame | 主體追蹤 SDK |
| | BodyIndexFrame | 主體追蹤 SDK  |
| 座標組應|CoordinateMapper| [感應器 SDK-影像轉換](use-image-transformation.md) |
|臉部追蹤 | FaceFrame | [認知服務：臉部](https://azure.microsoft.com/services/cognitive-services/face/)       |
|    語音辨識    |    N/A                      |    [認知服務：語音](https://azure.microsoft.com/services/cognitive-services/directory/speech/)     |

## <a name="next-steps"></a>接下來的步驟

[適用于 Windows 開發人員的 Kinect 頁面](https://developer.microsoft.com/windows/kinect)
