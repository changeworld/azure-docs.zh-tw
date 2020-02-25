---
title: Azure Kinect DK 硬體規格
description: 了解 Azure Kinect DK 的元件、規格和功能。
author: tesych
ms.author: tesych
ms.reviewer: jarrettr
ms.prod: kinect-dk
ms.date: 02/14/2020
ms.topic: article
keywords: azure, kinect, 規格, 硬體, DK, 功能, 深度, 彩色, RGB, IMU, 麥克風, 陣列, 深度
ms.custom:
- CI 114092
- CSSTroubleshooting
audience: ITPro
manager: dcscontentpm
ms.localizationpriority: high
ms.openlocfilehash: dbe4cac64e324c1188ec5a01b4da3935a5d5b479
ms.sourcegitcommit: f255f869c1dc451fd71e0cab340af629a1b5fb6b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/16/2020
ms.locfileid: "77371285"
---
# <a name="azure-kinect-dk-hardware-specifications"></a>Azure Kinect DK 硬體規格

本文提供 Azure Kinect 硬體如何將 Microsoft 的最新感應器技術整合到單一 USB 連接配件中的詳細資訊。

![Azure Kinect DK](./media/resources/hardware-specs-media/device-wire.png)

## <a name="terms"></a>詞彙

本文會使用下列縮寫詞彙。

- NFOV (窄視野深度模式)
- NFOV (寬視野深度模式)
- FOV (視野)
- FPS (每秒畫面格數)
- IMU (慣性測量單位)
- FoI (感興趣的領域)

## <a name="product-dimensions-and-weight"></a>產品尺寸和重量

Azure Kinect 裝置是由下列尺寸和重量維度所組成。

- **維度**：103 x 39 x 126 毫米
- **重量**：440 克

![Azure Kinect DK 維度](./media/resources/hardware-specs-media/dimensions.png)

## <a name="operating-environment"></a>作業環境

Azure Kinect DK 適用於在下列環境條件下操作的開發人員和商務企業：

- **溫度**：10-25⁰C
- **濕度**：8-90% (非凝固) 相對溼度

> [!NOTE]
> 在環境條件之外使用可能會導致裝置失敗和/或運作不正常。 這些環境條件適用於裝置在所有作業條件下的周遭環境。 搭配外部機殼使用時，建議使用主動溫度控制及 (或) 其他冷卻解決方案，以確保在這些範圍內維護裝置。 裝置設計的特色是正面區段與後端套筒之間有一個冷卻通道。 當您實作裝置時，請確定此冷卻通道不受到阻礙。

請參閱其他產品[安全性資訊](https://support.microsoft.com/help/4023454/safety-information)。

## <a name="depth-camera-supported-operating-modes"></a>深度相機支援的作業模式

Azure Kinect DK 整合了 Microsoft 設計的 1 百萬像素飛行時間 (ToF) 深度相機，其使用[在 ISSCC 2018 呈現的影像感應器](https://docs.microsoft.com/windows/mixed-reality/ISSCC-2018)。 深度相機支援以下所示的模式：

 | [模式]            | 解決方案 | FoI       | FPS                | 作業範圍* | 曝光時間 |
|-----------------|------------|-----------|--------------------|------------------|---------------|
| NFOV 未分類收納   | 640x576    | 75°x65°   | 0、5、15、30       | 0.5 - 3.86 米       | 12.8 毫秒        |
| NFOV 2x2 分類收納 (SW) | 320x288    | 75°x65°   | 0、5、15、30       | 0.5 - 5.46 米       | 12.8 毫秒        |
| WFOV 2x2 分類收納 | 512x512    | 120°x120° | 0、5、15、30       | 0.25 - 2.88 米      | 12.8 毫秒        |
| WFOV 未分類收納   | 1024x1024  | 120°x120° | 0、5、15           | 0.25 - 2.21 米      | 20.3 毫秒        |
| 被動式 IR      | 1024x1024  | N/A       | 0、5、15、30       | N/A              | 1.6 毫秒         |

\*15% 到 95% 反射率：850nm 波長，2.2 μW/cm<sup>2</sup>/nm，隨機誤差的標準差 ≤ 17 毫米，典型系統誤差 < 11 毫米 + 0.1% 的差距，但沒有多路徑干擾。 根據物件反射率，提供指定範圍外的深度。

## <a name="color-camera-supported-operating-modes"></a>彩色相機支援的作業模式

Azure Kinect DK 包含 OV12A10 12MP CMOS 滾動式快門感應器。 原生作業模式如下所列：

|             RGB 相機解析度 (HxV)  |          外觀比例  |          格式選項   |          畫面播放速率 (FPS)  |          Nominal FOV (HxV)(經過後處理)  |
|------------------------------------------|------------------------|---------------------------|-----------------------------|---------------------------------------------|
|       3840x2160                          |          16:9          |          MJPEG            |          0、5、15、30       |          90°x59°                              |
|       2560x1440                          |          16:9          |          MJPEG            |          0、5、15、30       |          90°x59°                              |
|       1920x1080                          |          16:9          |          MJPEG            |          0、5、15、30       |          90°x59°                              |
|       1280x720                           |          16:9          |          MJPEG/YUY2/NV12  |          0、5、15、30       |          90°x59°                              |
|       4096x3072                          |          4:3           |          MJPEG             |          0、5、15           |          90°x74.3°                            |
|       2048x1536                          |          4:3           |          MJPEG             |          0、5、15、30       |          90°x74.3°                            |

RGB 相機與 USB Video class (UVC) 相容，而且可在沒有感應器 SDK 的情況下使用。 RGB 相機色彩空間：BT.601 全幅 [0..255]。 

> [!NOTE]
> 感應器 SDK 可以提供 BGRA 像素格式的彩色影像。 這不是裝置支援的原生模式，使用時會造成額外的 CPU 負載。 主機 CPU 用於從接收自裝置的 MJPEG 影像進行轉換。

## <a name="rgb-camera-exposure-time-values"></a>RGB 相機曝光時間值

以下是可接受的 RGB 相機手動曝光值對應：

| exp| 2^exp | 50Hz   |60Hz    |
|----|-------|--------|--------|
| -11|     488|    500|    500 |
| -10|     977|   1250|   1250 |
|  -9|    1953|   2500|   2500 |
|  -8|    3906|  10000|   8330 |
|  -7|    7813|  20000|  16670 |
|  -6|   15625|  30000|  33330 |
|  -5|   31250|  40000|  41670 |
|  -4|   62500|  50000|  50000 |
|  -3|  125000|  60000|  66670 |
|  -2|  250000|  80000|  83330 |
|  -1|  500000| 100000| 100000 |
|   0| 1000000| 120000| 116670 |
|   1| 2000000| 130000| 133330 |

## <a name="depth-sensor-raw-timing"></a>深度感應器原始計時

深度模式 | IR <br>脈衝 | 脈衝 <br>寬度  | 閒置 <br>期間| 閒置時間 | 曝光 <br> Time
-|-|-|-|-|-
NFOV 未分類收納 <br>  NFOV 2xx 分類收納 <br> WFOV 2x2 分類收納 | 9 | 125 us | 8 | 1450 us | 12.8 毫秒 
WFOV 未分類收納                                            | 9 | 125 us | 8 | 2390 us | 20.3 毫秒


## <a name="camera-field-of-view"></a>相機視野

下圖顯示深度和 RGB 相機視野，或感應器所「看到」的角度。 下圖顯示處於4:3 模式的 RGB 相機。

![相機 FOV](./media/resources/hardware-specs-media/camera-fov.png)

此圖示範從正面距離 2000 毫米所看到的相機視野。

![相機 FOV 正面](./media/resources/hardware-specs-media/fov-front.png)

> [!NOTE]
> 當深度處於 NFOV 模式時，相較於 16:9 解析度，RGB 相機在 4:3 中有更佳的像素重疊。

## <a name="motion-sensor-imu"></a>動作感應器 (IMU)

內嵌的慣性測量單位 (IMU) 為 LSM6DSMUS，同時包含加速計和陀螺儀。 加速計和陀螺儀會同時在 1.6 kHz 取樣。 系統會向 208 Hz 的主機回報這些範例。

## <a name="microphone-array"></a>麥克風陣列

Azure Kinect DK 內嵌高品質的七隻麥克風環形陣列，可視為標準 USB 音訊類別 2.0 裝置。 全部 7 個通道都可以存取。 效能規格如下：

- 敏感度：-22 dBFS (94 dB SPL，1 kHz)
- 訊噪比 > 65 dB
- 聲學過載點：116 dB

![麥克風音頭](./media/resources/hardware-specs-media/mic-bubble.png)

## <a name="usb"></a>USB

Azure Kinect DK 是 USB3 複合裝置，會向作業系統公開下列硬體端點：

廠商識別碼為 0x045E (Microsoft)，產品識別碼表如下：

|    USB 介面        |    PNP IP    |     注意            |
|-------------------------|--------------|----------------------|
|    USB3.1 Gen1 Hub    |    0x097A    |    主要中樞    |
|    USB2.0 Hub         |    0x097B    |    HS USB          |
|    深度相機       |    0x097C    |    USB3.0            |
|    彩色相機       |    0x097D    |    USB3.0            |
|    麥克風        |    0x097E    |    HS USB          |

## <a name="indicators"></a>指標

裝置正面有相機串流指示器，可使用感應器 SDK 以程式設計方式加以停用。

裝置後側的狀態 LED 可指出裝置狀態：

| 當燈號為     | 則表示                                                   |
|-----------------------|------------------------------------------------------------|
| 純白色           | 裝置已開啟且運作正常。                         |
| 閃爍白色        | 裝置已開啟，但沒有 USB 3.0 資料連線。   |
| 閃爍琥珀色        | 裝置沒有足夠的電源可運作。               |
| 琥珀色閃爍白色  | 韌體正在進行更新或復原                    |

## <a name="power-device"></a>電源裝置

您可透過兩種方式為裝置供電：

1. 使用內建電源供應器。 資料是經由個別的 USB Type-C 對 Type-A 纜線進行連線。
2. 同時對電源和資料使用 Type-C 對 Type-C 纜線。

Azure Kinect DK 未隨附 Type-C 對 Type-C 纜線。

> [!NOTE]
> - 內建電源供應器纜線是 USB Type-A 對單一圓柱式連接器。 使用所提供的插牆式電源供應器搭配此纜線。 裝置能夠汲取的電力超過兩個標準 USB Type-A 連接埠所提供的電力。
> - USB 纜線很重要，我們建議使用高品質的纜線並先驗證其功能，然後再從遠端部署該單元。

> [!TIP]
> 若要選取良好 Type-C 對 Type-C 纜線：
> - [USB 認證纜線](https://www.usb.org/products)必須同時支援電源和資料。
> - 被動式纜線的長度應該小於 1.5 米。 如果較長，請使用主動式纜線。 
> - 纜線必須支援不大於 1.5A。 否則，您必須連接外部電源供應器。

確認纜線：

- 透過纜線將裝置連接到主機電腦。
- 驗證 Windows 裝置管理員中的所有裝置都正確列舉。 深度和 RGB 相機應會出現，如下列範例所示。

  ![裝置管理員中的 Azure Kinect DK](./media/resources/hardware-specs-media/device-manager.png)

- 使用下列設定，在 Azure Kinect 檢視器中驗證是否可在所有感應器上確實串接纜線：

  - 深度相機：NFOV 未分類收納
  - RGB 相機：2160p
  - 已啟用麥克風和 IMU

## <a name="what-does-the-light-mean"></a>燈號的意義

電源指示器是 Azure Kinect DK 背面的 LED。 LED 的顏色會隨著裝置的狀態改變。

![此影像顯示 Azure Kinect DK 的背面。 有三個編號的圖說文字：一個用於 LED 指示器，其下方的兩個則用於纜線。](./media/quickstarts/azure-kinect-dk-power-indicator.png)

此圖標示了下列元件：

1. 電源指示器
1. 電源線 (連接到電源)
1. USB-C 資料傳輸線 (連接到電腦)

請確定已連接纜線，如下所示。 然後檢查下表，以了解電源燈所指示的各種狀態。

|當燈號為： |則表示： |您應該： |
| ---| --- | --- |
|純白色 |裝置已開啟且運作正常。 |使用裝置。 |
|未亮起 |裝置未連接到電腦。 |請確定圓形電源連接器纜線已連接到裝置和 USB 電源配接器。<br /><br />請確定 USB-C 纜線已連接到裝置和您的電腦。 |
|閃爍白色 |裝置已開啟電源，但沒有 USB 3.0 資料連線。 |請確定圓形電源連接器纜線已連接到裝置和 USB 電源配接器。<br /><br />請確定 USB-C 纜線已連接到裝置以及您電腦上的 USB 3.0 連接埠。<br /><br />將裝置連接到電腦的其他 USB 3.0 連接埠。<br /><br />在您的電腦上，開啟裝置管理員 ([開始]   > [控制台]   > [裝置管理員]  ，並確認您的電腦具有支援的 USB 3.0 主機控制器。 |
|閃爍琥珀色 |裝置沒有足夠的電源可運作。 |請確定圓形電源連接器纜線已連接到裝置和 USB 電源配接器。<br /><br />請確定 USB-C 纜線已連接到裝置和您的電腦。 |
|琥珀色，然後閃爍白色 |裝置已開啟電源且正在接收韌體更新，或裝置正在恢復原廠設定。 |等待電源指示燈變成純白色。 如需詳細資訊，請參閱[重設 Azure Kinect DK](reset-azure-kinect-dk.md)。 |

## <a name="power-consumption"></a>耗電量

Azure Kinect DK 最多耗用 5.9 W；特定耗電量與使用案例相關。

## <a name="calibration"></a>校正

Azure Kinect DK 是在原廠校正。 您可透過感應器 SDK 以程式設計方式查詢視覺和慣性感應器的校正參數。

## <a name="device-recovery"></a>裝置復原

您可使用鎖緊銷底下的按鈕，將裝置韌體重設為原始韌體。

![Azure Kinect DK 復原按鈕](./media/resources/hardware-specs-media/recovery.png)

若要復原裝置，請參閱[這裡的指示](reset-azure-kinect-dk.md)。

## <a name="next-steps"></a>後續步驟

- [使用 Azure Kinect 感應器 SDK](about-sensor-sdk.md)
- [設定硬體](set-up-azure-kinect-dk.md)
