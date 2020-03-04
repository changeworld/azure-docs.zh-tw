---
title: Microsoft Azure Data Box Edge 技術規格和合規性 |Microsoft Docs
description: 瞭解您 Azure Data Box Edge 的技術規格和合規性
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 03/02/2020
ms.author: alkohli
ms.openlocfilehash: b646ee9b727d5adf4ec1c8b5c769b3d8f5c0fc1c
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/03/2020
ms.locfileid: "78252040"
---
# <a name="azure-data-box-edge-technical-specifications"></a>Azure Data Box Edge 技術規格

Microsoft Azure Data Box Edge 裝置的硬體元件會遵循本文所述的技術規格與法規標準。 技術規格描述電源供應器（Psu）、存放裝置容量、主機殼和環境標準。 

## <a name="compute-memory-specifications"></a>計算，記憶體規格

Data Box Edge 裝置的計算和記憶體規格如下：

| 規格           | 值                  |
|-------------------------|----------------------------|
| CPU    | 2 X 10 核心 CPU                     |
| 記憶體              | 128 GB RAM                  |


## <a name="fpga-specifications"></a>FPGA 規格

可程式化閘道陣列（FPGA）會包含在每個 Data Box Edge 裝置上，以啟用 Machine Learning （ML）案例。 

| 規格           | 值                  |
|-------------------------|----------------------------|
| FPGA   | Intel Arria 10 <br> 可用的深度類神經網路（DNN）模型與[雲端 FPGA 實例所支援](https://docs.microsoft.com/azure/machine-learning/how-to-deploy-fpga-web-service#whats-supported-on-azure)的模型相同。| 


## <a name="power-supply-unit-specifications"></a>電源供應器單位規格

Data Box Edge 裝置具有具有高效能風扇的兩個 100-240 V 電源供應器（Psu）。 這兩個 Psu 提供了多餘的電源設定。 如果 PSU 失敗，裝置會繼續在另一個 PSU 上正常運作，直到更換故障的模組為止。 下表列出 Psu 的技術規格。

| 規格           | 750 W PSU                  |
|-------------------------|----------------------------|
| 最大輸出電力    | 750 W                     |
| 頻率               | 50/60 Hz                   |
| 選取電壓範圍 | 自動範圍： 100-240 V AC |
| 隨插即用           | 是                        |

<!--## Power consumption statistics

The following table lists the typical power consumption data (actual values may vary from the published) for the Data Box Edge device.-->

## <a name="network-interface-specifications"></a>網路介面規格

您 Data Box Edge 裝置有6個網路介面（PORT1-PORT6）。

| 規格           | 描述                 |
|-------------------------|----------------------------|
|  網路介面    | 2 個 1 GbE 介面 - 1 個為管理用途 (使用者無法設定)，用於初始設定。 其他介面則可由使用者設定、可用於傳輸資料，且預設為 DHCP。 <br>2 個 25 GbE 介面 - 這些也能以 10 GbE 介面的形式運作。 這些資料介面可由使用者設定為 DHCP (預設) 或靜態。 <br> 2 個 25 GbE 介面 - 這些資料介面可由使用者設定為 DHCP (預設) 或靜態。                  |

## <a name="storage-specifications"></a>儲存體規格

Data Box Edge 裝置有 9 X 2.5 "NVMe Ssd，每個都有 1.6 TB 的容量。 在這些 Ssd 中，1是作業系統磁片，另8則是資料磁片。 裝置的可用容量總計約為 12.5 TB。 下表包含裝置儲存體容量的詳細資料。

|     規格                          |     值             |
|--------------------------------------------|-----------------------|
|    固態硬碟 (SSD) 數     |    8                  |
|    單一 SSD 容量                     |    1.6 TB             |
|    容量總計                          |    12.8 TB            |
|    可用容量總計*                  |    ~ 12.5 TB            |

**部分空間會保留供內部使用。*

## <a name="enclosure-dimensions-and-weight-specifications"></a>機箱尺寸和重量規格

下表列出各種機箱的尺寸和重量規格。

### <a name="enclosure-dimensions"></a>機箱尺寸

下表列出機箱尺寸 (以公釐和英吋為單位)。

|     機箱     |     公釐     |     英吋     |
|-------------------|---------------------|----------------|
|    高度         |    44.45            |    1.75 "          |
|    寬度          |    434.1           |    17.09 "          |
|    Length          |    740.4           |    29.15 "          |

下表列出出貨套件的尺寸（以毫米和英寸為單位）。

|     套件     |     公釐     |     英吋     |
|-------------------|---------------------|----------------|
|    高度         |    311.2            |    12.25"          |
|    寬度          |    642.8          |    25.31"          |
|    Length          |   1,051.1          |    41.38"          |

### <a name="enclosure-weight"></a>機箱重量

裝置套件會權衡66磅。 和需要兩個人員來處理它。 裝置的權數視主機殼的設定而定。

|     機箱                                 |     加權          |
|-----------------------------------------------|---------------------|
|    總權數（包括封裝）       |    61磅。          |
|    裝置的權數                       |    35磅。          |

## <a name="enclosure-environment-specifications"></a>機箱環境規格

本節列出與主機殼環境相關的規格，例如溫度、濕度和高度。

### <a name="temperature-and-humidity"></a>溫度和溼度

|     機箱         |     環境溫度範圍     |     環境相對濕度     |     最大露點     |
|-----------------------|--------------------------------------|--------------------------------------|---------------------------|
|    操作        |    10° C-35 ° C （50° F-86 ° F）         |    10%-80% 非冷凝。         |    29°C (84°F)            |
|    無法運作    |    -40 ° C 至65° C （-40 ° F-149 ° F）     |    5%-95% 非冷凝。          |    33°C (91°F)            |

### <a name="airflow-altitude-shock-vibration-orientation-safety-and-emc"></a>氣流、海拔高度、震盪、振動、方向、安全和 EMC

|     機箱                           |     運作規格                                                                                                                                                                                         |
|-----------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|    氣流                              |    系統氣流是從前面到後面。 系統必須以低壓、尾段排氣安裝操作。 <!--Back pressure created by rack doors and obstacles should not exceed 5 pascals (0.5 mm water gauge).-->    |
|    最高高度，營運        |    3048計量（10000英尺），其最大操作溫度已由[操作溫度解除評等規格](#operating-temperature-de-rating-specifications)決定。                                                                                |
|    最大高度，不可操作    |    12000計量（39370英尺）                                                                                                                                                                                         |
|    可運作的震盪                   |    6 G 為6個方向的11毫秒                                                                                                                                                                         |
|    無法運作的震盪               |    71 G 用於6個方向的2毫秒                                                                                                                                                                           |
|    可運作的振動               |    0.26 G<sub>RMS</sub> 5 Hz 至 350 Hz 隨機                                                                                                                                                                                     |
|    無法運作的振動           |    1.88 G<sub>RMS</sub> 10 hz 至 500 hz 15 分鐘（所有六方皆已測試）。                                                                                                                                                  |
|    定位和掛接             |    19「機架掛接」                                                                                                                                                                                        |
|    安全與核可                 |    EN 60950-1:2006 + A1： 2010 + A2： 2013 + A11： 2009 + A12： 2011/IEC 60950-1:2005 ed2 + A1： 2009 + A2： 2013 EN 62311:2008                                                                                                                                                                       |
|    EMC                                  |    FCC A, ICES-003 <br>EN 55032:2012/CISPR 32:2012  <br>EN 55032:2015/CISPR 32:2015  <br>EN 55024:2010 +A1:2015/CISPR 24:2010 +A1:2015  <br>EN 61000-3-2:2014/IEC 61000-3-2:2014 (Class D)   <br>EN 61000-3-3:2013/IEC 61000-3-3:2013                                                                                                                                                                                         |
|    能源             |    委員會條例 (EU) No. 617/2013                                                                                                                                                                                        |
|    RoHS           |    EN 50581:2012                                                                                                                                                                                        |


### <a name="operating-temperature-de-rating-specifications"></a>操作溫度取消評等規格

|     操作溫度解除評等     |     環境溫度範圍                                                         |
|--------------------------------------------|------------------------------------------------------------------------------------------|
|    最多35° C （95° F）                       |    溫度上限會減少1° C/300 m （1° F/547 ft）以上 950 m （3117 ft）。    |
|    35° C 至40° C （95° F 到104° F）            |    最高溫度會減少1° C/175 m （1° F/319 ft）以上 950 m （3117 ft）。    |
|    40° C 至45° C （104° F 至113° F）           |    高於 950 m （3117 ft）的溫度上限為1° C/125 m （1° F/228 ft）。    |


## <a name="next-steps"></a>後續步驟

- [部署 Azure Data Box Edge](data-box-edge-deploy-prep.md)
