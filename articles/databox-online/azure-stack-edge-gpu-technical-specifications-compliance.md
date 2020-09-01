---
title: 使用 GPU 技術規格和合規性 Microsoft Azure Stack Edge |Microsoft Docs
description: 深入瞭解使用 GPU Azure Stack Edge 裝置的技術規格和合規性
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 08/26/2020
ms.author: alkohli
ms.openlocfilehash: 3f354655a612d4085b0a0de45ae1a6e5ee097ade
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/01/2020
ms.locfileid: "89266658"
---
# <a name="technical-specifications-and-compliance-for-azure-stack-edge-with-gpu"></a>使用 GPU Azure Stack Edge 的技術規格和合規性 

您 Azure Stack Edge 的硬體元件具有上架圖形處理單位 (GPU) 遵守本文中所述的技術規格與法規標準。 技術規格說明硬體、電源供應器 (Psu) 、存放裝置容量、主機殼和環境標準。

## <a name="compute-and-memory-specifications"></a>計算和記憶體規格

Azure Stack Edge 裝置的計算和記憶體規格如下：

| 規格           | 值                  |
|-------------------------|----------------------------|
| CPU                     | 2 X Intel 以上的銀級 4214 (串聯 Lake) CPU            |
| 記憶體                  | 128 (8x16 GB) GB RAM                     |


## <a name="compute-acceleration-specifications"></a>計算加速規格

Kubernetes、深度學習和機器學習案例的每個 Azure Stack Edge 裝置都包含圖形處理單元 (GPU) 。

| 規格           | 值                  |
|-------------------------|----------------------------|
| GPU   | 一或兩個 nVidia T4 Gpu <br> 如需詳細資訊，請參閱 [NVIDIA T4](https://www.nvidia.com/en-us/data-center/tesla-t4/)。| 


## <a name="power-supply-unit-specifications"></a>電源供應器裝置規格

Azure Stack Edge 裝置有兩個 100-240 V 電源供應器單位 (Psu) 具有高效能風扇。 這兩個 PSU 提供了備援電源設定。 如果 PSU 故障，裝置會繼續在其他 PSU 上正常運作，直到更換故障的模組為止。 下表列出 PSU 的技術規格。

| 規格           | 750 W PSU                  |
|-------------------------|----------------------------|
| 最大輸出電力    | 750 W                     |
| 頻率               | 50/60 Hz                   |
| 選取電壓範圍 | 自動設定範圍：100-240 V AC |
| 隨插即用           | 是                        |


## <a name="network-interface-specifications"></a>網路介面規格

您的 Azure Stack Edge 裝置有六個網路介面 PORT1-PORT6。

| 規格           | 描述                 |
|-------------------------|----------------------------|
|  網路介面    | **2 X 1 GbE 介面** -1 管理介面埠1用於初始設定，預設為靜態。 初始設定完成後，您可以將介面用於具有任何 IP 位址的資料。 不過，在重設時，介面會還原回靜態 IP。 <br>另一個介面埠2是使用者可設定的，可用於資料傳輸，且預設為 DHCP。 <br>**4 X 25 GbE 介面** -這些資料介面（埠3到埠6）可由使用者設定為 DHCP (預設) 或靜態。 這些也可以當作 10 GbE 介面來運作。  | 

您的 Azure Stack Edge 裝置具有下列網路硬體：

* **自訂 Microsoft Qlogic >cavium 25G NDC 介面卡** -埠1至埠4。
* **Mellanox 雙重埠 25G ConnectX-4 通道網路介面卡** -埠5和埠6。

以下是 Mellanox 卡的詳細資料：

| 參數           | 說明                 |
|-------------------------|----------------------------|
| 型號    | ConnectX®-4 Lx EN 網路介面卡                      |
| 模型描述               | 25GbE 雙埠 SFP28;PCIe 3.0 x8;ROHS R6                    |
| 裝置零件編號 (R640)  | MCX4121A-ACAT  |
| PSID (R640)            | MT_2420110034                         |

如需這些網路卡支援的纜線、交換器及收發器的完整清單，請移至：

- [Qlogic >cavium 25G NDC 介面卡互通性矩陣](https://www.marvell.com/documents/xalflardzafh32cfvi0z/)。
- [Mellanox 雙埠 25G ConnectX-4 通道網路介面卡相容產品](https://docs.mellanox.com/display/ConnectX4LxFirmwarev14271016/Firmware+Compatible+Products)。  

## <a name="storage-specifications"></a>儲存體規格

Azure Stack Edge 裝置有五個 2.5 "NVMe DC P4610 Ssd，每個都有 1.6 TB 的容量。 開機磁片磁碟機是 240 GB SATA SSD。 裝置的可用容量總計約為 8.28 TB。 下表列出裝置的儲存體容量。

|     規格                          |     值             |
|--------------------------------------------|-----------------------|
|    開機 SATA 固態硬碟 (SSD)       |    1                  |
|    NVMe Ssd 數目                     |    5                  |
|    開機 SSD 容量                       |    240 GB             |
|    單一 NVMe SSD 容量                |    1.6 TB             |
|    容量總計                          |    8.28 TB            |
|    可用容量總計*                  |    ~ 7.95 TB          |
|    SAS 控制器                          |    HBA330 12 Gbps     |


**部分空間保留供內部使用。*

<!--Remove based on feedback from Ravi
## Other hardware specifications

Your Azure Stack Edge device also contains the following hardware:

* iDRAC baseboard management
* Performance fans
* Custom ID module-->

## <a name="enclosure-dimensions-and-weight-specifications"></a>機箱尺寸和重量規格

下表列出各種機箱的尺寸和重量規格。

### <a name="enclosure-dimensions"></a>機箱尺寸

下表列出1U 裝置主機殼的尺寸（以毫米和英寸為單位）。

|     機箱     |     公釐     |     英吋     |
|-------------------|---------------------|----------------|
|    高度         |    44.45            |    1.75"       |
|    寬度          |    434.1            |    17.09"      |
|    長度         |    740.4            |    29.15"      |

下表列出出貨包裝尺寸 (以公釐和英吋為單位)。

|     Package     |     公釐     |     英吋     |
|-------------------|---------------------|----------------|
|    高度         |    311.2            |    12.25"          |
|    寬度          |    642.8            |    25.31"          |
|    長度         |    1,051.1          |    41.38"          |

### <a name="enclosure-weight"></a>機箱重量

裝置套件會將66磅的重量。 需要兩個人員來處理。 裝置重量視機箱設定而定。

|     機箱                                 |     Weight          |
|-----------------------------------------------|---------------------|
|    總重量 (包含包裝)       |    61 磅          |
|    裝置重量                       |    35 磅          |

## <a name="enclosure-environment-specifications"></a>機箱環境規格

本節將列出機箱環境的相關規格，例如溫度、濕度和高度。

### <a name="temperature-and-humidity"></a>溫度和溼度

|     機箱         |     周圍    溫度範圍     |     周圍相對    溼度     |     最大露點     |
|-----------------------|--------------------------------------|--------------------------------------|---------------------------|
|    運作        |    10°C - 35°C (50°F - 86°F)         |    10% - 80% 無凝結。         |    29°C (84°F)            |
|    無法運作    |    -40°C 到 65°C (-40°F - 149°F)     |    5% - 95% 無凝結。          |    33°C (91°F)            |

### <a name="airflow-altitude-shock-vibration-orientation-safety-and-emc"></a>氣流、海拔高度、震盪、振動、方向、安全和 EMC

|     機箱                           |     運作規格                                                                                                                                                                                         |
|-----------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|    氣流                              |    系統氣流是從前面到後面。 系統必須以低壓、尾段排氣安裝操作。 <!--Back pressure created by rack doors and obstacles should not exceed 5 pascals (0.5 mm water gauge).-->    |
|    最高高度，可運作        |    3048 公尺 (10000 英呎)，其最高操作溫度降額是由[操作溫度降額規格](#operating-temperature-de-rating-specifications)決定。                                                                                |
|    最高高度，無法運作    |    12,000 公尺 (39,370 英呎)                                                                                                                                                                                         |
|    可運作的震盪                   |    6 G，持續 11 毫秒 (6 個方向)                                                                                                                                                                         |
|    無法運作的震盪               |    71 G，持續 2 毫秒 (6 個方向)                                                                                                                                                                           |
|    可運作的振動               |    0.26 G<sub>RMS</sub> 5 Hz 至 350 Hz 隨機                                                                                                                                                                                     |
|    無法運作的振動           |    1.88 G<sub>RMS</sub> 10 Hz 至 500 Hz，持續 15 分鐘 (六側皆測試)。                                                                                                                                                  |
|    定位和掛接             |    標準19「機架掛接 (1U)                                                                                                                                                                                        |
|    安全與核可                 |    EN 60950-1:2006 +A1:2010 +A2:2013 +A11:2009 +A12:2011/IEC 60950-1:2005 ed2 +A1:2009 +A2:2013 EN 62311:2008                                                                                                                                                                       |
|    EMC                                  |    FCC A, ICES-003 <br>EN 55032:2012/CISPR 32:2012  <br>EN 55032:2015/CISPR 32:2015  <br>EN 55024:2010 +A1:2015/CISPR 24:2010 +A1:2015  <br>EN 61000-3-2:2014/IEC 61000-3-2:2014 (Class D)   <br>EN 61000-3-3:2013/IEC 61000-3-3:2013                                                                                                                                                                                         |
|    能源             |    委員會條例 (EU) No. 617/2013                                                                                                                                                                                        |
|    RoHS           |    EN 50581:2012                                                                                                                                                                                        |

### <a name="operating-temperature-de-rating-specifications"></a>操作溫度降額規格

|     操作    溫度降額     |     周圍    溫度範圍                                                         |
|--------------------------------------------|------------------------------------------------------------------------------------------|
|    最高至 35°C (95°F)                       |    在 950 公尺 (3,117 英呎) 之上，最高溫度會依 1°C/300 公尺 (1°F/547 英呎) 比率下滑。    |
|    35°C 到 40°C (95°F 到 104°F)            |    在 950 公尺 (3,117 英呎) 之上，最高溫度會依 1°C/175 公尺 (1°F/319 英呎) 比率下滑。    |
|    40°C 到 45°C (104°F 到 113°F)           |    在 950 公尺 (3,117 英呎) 之上，最高溫度會依 1°C/125 公尺 (1°F/228 英呎) 比率下滑。    |

## <a name="next-steps"></a>後續步驟

[部署您的 Azure Stack Edge](azure-stack-edge-gpu-deploy-prep.md)
