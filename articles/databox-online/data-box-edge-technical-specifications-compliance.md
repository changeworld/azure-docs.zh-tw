---
title: 微軟 Azure 資料盒 邊緣技術規範和合規性*微軟文檔
description: 瞭解 Azure 資料框邊緣的技術規範和合規性
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 03/02/2020
ms.author: alkohli
ms.openlocfilehash: b646ee9b727d5adf4ec1c8b5c769b3d8f5c0fc1c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78252040"
---
# <a name="azure-data-box-edge-technical-specifications"></a>Azure 資料框邊緣技術規範

Microsoft Azure 資料盒邊緣設備的硬體元件符合本文中概述的技術規範和法規標準。 技術規範描述了電源單元 （PSUs）、存儲容量、外殼和環境標準。 

## <a name="compute-memory-specifications"></a>計算、記憶體規格

資料框邊緣設備具有以下計算和記憶體規範：

| 規格           | 值                  |
|-------------------------|----------------------------|
| CPU    | 2 X 10 核心 CPU                     |
| 記憶體              | 128 GB RAM                  |


## <a name="fpga-specifications"></a>FPGA 規格

支援機器學習 （ML） 方案的每個資料盒邊緣設備上都包含現場可程式設計閘陣列 （FPGA）。 

| 規格           | 值                  |
|-------------------------|----------------------------|
| Fpga   | 英特爾阿裡亞 10 <br> 可用的深度神經網路 （DNN） 模型與[雲 FPGA 實例支援的](https://docs.microsoft.com/azure/machine-learning/how-to-deploy-fpga-web-service#whats-supported-on-azure)模型相同。| 


## <a name="power-supply-unit-specifications"></a>電源單元規格

資料盒邊緣設備有兩台 100-240 V 電源單元 （PSUs），具有高性能風扇。 兩個 PSUs 提供冗余電源配置。 如果 PSU 發生故障，設備將繼續在其他 PSU 上正常運行，直到更換故障模組。 下表列出了 PSUs 的技術規格。

| 規格           | 750 W PSU                  |
|-------------------------|----------------------------|
| 最大輸出電力    | 750 W                     |
| 頻率               | 50/60 Hz                   |
| 選取電壓範圍 | 自動測距：100-240 V 交流 |
| 隨插即用           | 是                        |

<!--## Power consumption statistics

The following table lists the typical power consumption data (actual values may vary from the published) for the Data Box Edge device.-->

## <a name="network-interface-specifications"></a>網路介面規範

您的資料盒邊緣設備有 6 個網路介面，PORT1- PORT6。

| 規格           | 描述                 |
|-------------------------|----------------------------|
|  網路介面    | 2 個 1 GbE 介面 - 1 個為管理用途 (使用者無法設定)，用於初始設定。 其他介面則可由使用者設定、可用於傳輸資料，且預設為 DHCP。 <br>2 個 25 GbE 介面 - 這些也能以 10 GbE 介面的形式運作。 這些資料介面可由使用者設定為 DHCP (預設) 或靜態。 <br> 2 個 25 GbE 介面 - 這些資料介面可由使用者設定為 DHCP (預設) 或靜態。                  |

## <a name="storage-specifications"></a>儲存體規格

資料盒邊緣設備具有 9 X 2.5 英寸 NVMe SSD，每個設備容量為 1.6 TB。 在這些 SSD 中，1 是作業系統磁片，其他 8 個是資料磁片。 該設備的總可用容量約為 12.5 TB。 下表提供了設備的存儲容量的詳細資訊。

|     規格                          |     值             |
|--------------------------------------------|-----------------------|
|    固態硬碟 (SSD) 數     |    8                  |
|    單一 SSD 容量                     |    1.6 TB             |
|    容量總計                          |    12.8 TB            |
|    可用容量總計*                  |    ± 12.5 TB            |

**保留一些空間供內部使用。*

## <a name="enclosure-dimensions-and-weight-specifications"></a>機箱尺寸和重量規格

下表列出各種機箱的尺寸和重量規格。

### <a name="enclosure-dimensions"></a>機箱尺寸

下表列出機箱尺寸 (以公釐和英吋為單位)。

|     機箱     |     公釐     |     英吋     |
|-------------------|---------------------|----------------|
|    高度         |    44.45            |    1.75"          |
|    寬度          |    434.1           |    17.09"          |
|    長度          |    740.4           |    29.15"          |

下表列出了裝運包的尺寸（以毫米和英寸為單位）。

|     Package     |     公釐     |     英吋     |
|-------------------|---------------------|----------------|
|    高度         |    311.2            |    12.25"          |
|    寬度          |    642.8          |    25.31"          |
|    長度          |   1,051.1          |    41.38"          |

### <a name="enclosure-weight"></a>機箱重量

設備封裝重 66 磅。 並要求兩個人來處理它。 設備的重量取決於機櫃的配置。

|     機箱                                 |     Weight          |
|-----------------------------------------------|---------------------|
|    總重量，包括包裝       |    61 磅          |
|    設備重量                       |    35 磅          |

## <a name="enclosure-environment-specifications"></a>機箱環境規格

本節列出了與機櫃環境相關的規範，如溫度、濕度和海拔高度。

### <a name="temperature-and-humidity"></a>溫度和溼度

|     機箱         |     環境溫度範圍     |     環境相對濕度     |     最大露點     |
|-----------------------|--------------------------------------|--------------------------------------|---------------------------|
|    運作        |    10°C - 35°C （50°F - 86°F）         |    10% - 80% 非冷凝。         |    29°C (84°F)            |
|    無法運作    |    -40°C 至 65°C （-40°F - 149°F）     |    5% - 95% 非冷凝。          |    33°C (91°F)            |

### <a name="airflow-altitude-shock-vibration-orientation-safety-and-emc"></a>氣流、海拔高度、震盪、振動、方向、安全和 EMC

|     機箱                           |     運作規格                                                                                                                                                                                         |
|-----------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|    氣流                              |    系統氣流是從前面到後面。 系統必須以低壓、尾段排氣安裝操作。 <!--Back pressure created by rack doors and obstacles should not exceed 5 pascals (0.5 mm water gauge).-->    |
|    最大海拔高度，可操作        |    3048 米（10，000 英尺），最高工作溫度除位根據[工作溫度去額定值規格](#operating-temperature-de-rating-specifications)確定。                                                                                |
|    最大海拔，不可操作    |    12，000公尺（39，370英尺）                                                                                                                                                                                         |
|    可運作的震盪                   |    6 G，6 個方向為 11 毫秒                                                                                                                                                                         |
|    無法運作的震盪               |    71 G，6 個方向為 2 毫秒                                                                                                                                                                           |
|    可運作的振動               |    0.26 G<sub>RMS</sub> 5 Hz 至 350 Hz 隨機                                                                                                                                                                                     |
|    無法運作的振動           |    1.88 G<sub>RMS</sub> 10 Hz 至 500 Hz，15 分鐘（所有六個面均經過測試）。                                                                                                                                                  |
|    定位和掛接             |    19" 機架安裝                                                                                                                                                                                        |
|    安全與核可                 |    EN 60950-1：2006 +A1：2010 +A2：2013 +A11：2009 +A12：2011/IEC 60950-1：2005 ed2 +A1：2009 +A2：2013 EN 62311：2008                                                                                                                                                                       |
|    EMC                                  |    FCC A, ICES-003 <br>EN 55032:2012/CISPR 32:2012  <br>EN 55032:2015/CISPR 32:2015  <br>EN 55024:2010 +A1:2015/CISPR 24:2010 +A1:2015  <br>EN 61000-3-2:2014/IEC 61000-3-2:2014 (Class D)   <br>EN 61000-3-3:2013/IEC 61000-3-3:2013                                                                                                                                                                                         |
|    能源             |    委員會條例 (EU) No. 617/2013                                                                                                                                                                                        |
|    RoHS           |    EN 50581:2012                                                                                                                                                                                        |


### <a name="operating-temperature-de-rating-specifications"></a>工作溫度去額定值規格

|     工作溫度去額定值     |     環境溫度範圍                                                         |
|--------------------------------------------|------------------------------------------------------------------------------------------|
|    高達 35°C （95°F）                       |    最高溫度降低 1°C/300 米（1°F/547 英尺），高於 950 米（3，117 英尺）。    |
|    35°C 至 40°C （95°F 至 104°F）            |    最高溫度降低 1°C/175 米（1°F/319 英尺），高於 950 米（3，117 英尺）。    |
|    40°C 至 45°C （104°F 至 113°F）           |    最高溫度降低 1°C/125 米（1°F/228 英尺），高於 950 米（3，117 英尺）。    |


## <a name="next-steps"></a>後續步驟

- [部署 Azure Data Box Edge](data-box-edge-deploy-prep.md)
