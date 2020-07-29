---
title: Microsoft Azure Stack Edge 技術規格和合規性 | Microsoft Docs
description: 了解您 Azure Stack Edge 的技術規格和合規性
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 03/02/2020
ms.author: alkohli
ms.openlocfilehash: 5a4ac2342ca36d83d1a579851c090c4713814c8d
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/19/2020
ms.locfileid: "83652046"
---
# <a name="azure-stack-edge-technical-specifications"></a>Azure Stack Edge 技術規格

Microsoft Azure Stack Edge 裝置的硬體元件會遵循本文中概述的技術規格與法規標準。 技術規格說明電源供應器裝置 (PSU)、儲存體容量、機箱和環境標準。

## <a name="compute-memory-specifications"></a>計算、記憶體規格

Azure Stack Edge 裝置的計算和記憶體規格如下：

| 規格           | 值                  |
|-------------------------|----------------------------|
| CPU    | 2 X 10 核心 CPU                     |
| 記憶體              | 128 GB RAM                  |

## <a name="fpga-specifications"></a>FPGA 規格

可現場程式化閘道陣列 (FPGA) 包含在每個 Azure Stack Edge 裝置上，可以啟用 Machine Learning (ML) 案例。

| 規格           | 值                  |
|-------------------------|----------------------------|
| FPGA   | Intel Arria 10 <br> 可用的深度神經網路 (DNN) 模型與[雲端 FPGA 執行個體支援](https://docs.microsoft.com/azure/machine-learning/how-to-deploy-fpga-web-service#whats-supported-on-azure)的那些模型相同。|

## <a name="power-supply-unit-specifications"></a>電源供應器裝置規格

Azure Stack Edge 裝置有兩個具有高效能風扇的 100-240 V 電源供應器裝置 (PSU)。 這兩個 PSU 提供了備援電源設定。 如果 PSU 故障，裝置會繼續在其他 PSU 上正常運作，直到更換故障的模組為止。 下表列出 PSU 的技術規格。

| 規格           | 750 W PSU                  |
|-------------------------|----------------------------|
| 最大輸出電力    | 750 W                     |
| 頻率               | 50/60 Hz                   |
| 選取電壓範圍 | 自動設定範圍：100-240 V AC |
| 隨插即用           | 是                        |

### <a name="azure-stack-edge-power-cord-specifications-by-region"></a>依區域的 Azure Stack Edge 電源線規格

您的 Azure Stack Edge 裝置需要電源線，視您的 Azure 區域而定。
如需所有支援電源線的技術規格，請參閱[依區域的 Azure Stack Edge 電源線規格](azure-stack-edge-technical-specifications-power-cords-regional.md)。

<!--## Power consumption statistics

The following table lists the typical power consumption data (actual values may vary from the published) for the Azure Stack Edge device.-->

## <a name="network-interface-specifications"></a>網路介面規格

您的 Azure Stack Edge 裝置有 6 個網路介面，PORT1 至 PORT6。

| 規格           | 描述                 |
|-------------------------|----------------------------|
|  網路介面    | 2 個 1 GbE 介面 - 1 個為管理用途 (使用者無法設定)，用於初始設定。 其他介面則可由使用者設定、可用於傳輸資料，且預設為 DHCP。 <br>2 個 25 GbE 介面 - 這些也能以 10 GbE 介面的形式運作。 這些資料介面可由使用者設定為 DHCP (預設) 或靜態。 <br> 2 個 25 GbE 介面 - 這些資料介面可由使用者設定為 DHCP (預設) 或靜態。                  |

## <a name="storage-specifications"></a>儲存體規格

Azure Stack Edge 裝置有 9 X 2.5" NVMe SSD，每個都具有 1.6 TB 的容量。 在這些 SSD 中，1 個是作業系統磁碟，另外 8 個則是資料磁碟。 裝置的可用容量總計約為 12.5 TB。 下表包含裝置儲存體容量的詳細資料。

|     規格                          |     值             |
|--------------------------------------------|-----------------------|
|    固態硬碟 (SSD) 數     |    8                  |
|    單一 SSD 容量                     |    1.6 TB             |
|    容量總計                          |    12.8 TB            |
|    可用容量總計*                  |    ~ 12.5 TB            |

**部分空間保留供內部使用。*

## <a name="enclosure-dimensions-and-weight-specifications"></a>機箱尺寸和重量規格

下表列出各種機箱的尺寸和重量規格。

### <a name="enclosure-dimensions"></a>機箱尺寸

下表列出機箱尺寸 (以公釐和英吋為單位)。

|     機箱     |     公釐     |     英吋     |
|-------------------|---------------------|----------------|
|    高度         |    44.45            |    1.75"          |
|    寬度          |    434.1           |    17.09"          |
|    長度          |    740.4           |    29.15"          |

下表列出出貨包裝尺寸 (以公釐和英吋為單位)。

|     Package     |     公釐     |     英吋     |
|-------------------|---------------------|----------------|
|    高度         |    311.2            |    12.25"          |
|    寬度          |    642.8          |    25.31"          |
|    長度          |   1,051.1          |    41.38"          |

### <a name="enclosure-weight"></a>機箱重量

裝置包裝重量 61 磅。 需要兩個人員來處理。 裝置重量視機箱設定而定。

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
|    定位和掛接             |    19" 機架掛接                                                                                                                                                                                        |
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

- [部署您的 Azure Stack Edge](azure-stack-edge-deploy-prep.md)
