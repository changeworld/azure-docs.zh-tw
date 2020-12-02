---
title: Microsoft Azure Stack Edge 迷你 R 技術規格和合規性 |Microsoft Docs
description: 瞭解 Azure Stack Edge 迷你 R 裝置的技術規格和合規性
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: alkohli
ms.openlocfilehash: e6dff06e92126e2fc4538273e229dcb0904e3101
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/01/2020
ms.locfileid: "96466275"
---
# <a name="azure-stack-edge-mini-r-technical-specifications"></a>Azure Stack Edge 迷你 R 技術規格

您 Microsoft Azure Stack Edge 迷你 R 裝置的硬體元件符合本文中所述的技術規格與法規標準。 技術規格描述 CPU、記憶體、電源供應單位 (Psu) 、儲存容量、主機殼尺寸和重量。


## <a name="compute-memory-specifications"></a>計算、記憶體規格

Azure Stack Edge 迷你 R 裝置具有下列計算和記憶體的規格：

| 規格           | 值                  |
|-------------------------|------------------------|
| CPU    | 16核心 CPU，Intel-3D 1577 |
| Memory              | 48 GB RAM (2400 MT/s)                   |


## <a name="compute-acceleration-specifications"></a>計算加速規格

視覺處理單位 (VPU) 包含在每一個 Azure Stack Edge 迷你 R 裝置上，可啟用 Kubernetes、深度類神經網路及電腦視覺應用程式。

| 規格           | 值                  |
|-------------------------|------------------------|
| 計算加速卡         | Intel Movidius 無數 X VPU <br> 如需詳細資訊，請參閱 [Intel Movidius 無數 X VPU](https://www.movidius.com/MyriadX) |


## <a name="storage-specifications"></a>儲存體規格

Azure Stack Edge 迷你 R 裝置有1個數據磁片和1個開機磁片 (，可作為作業系統存放裝置) 。 下表顯示裝置儲存容量的詳細資料。

|     規格                          |     值             |
|--------------------------------------------|-----------------------|
|    固態硬碟 (SSD) 數     |    2 X 1 TB 磁片 <br> 一個資料磁片和一個開機磁片                  |
|    單一 SSD 容量                     |    1 TB               |
|    總容量 (資料)               |    1 TB              |
|    可用容量總計*                  |    ~ 750 GB        |

**部分空間保留供內部使用。*

## <a name="network-specifications"></a>網路規格

Azure Stack Edge 迷你 R 裝置具有下列適用于網路的規格：


|規格  |值  |
|---------|---------|
|網路介面    |2 x 10 Gbe SFP + <br> 顯示為本機 UI 中的埠3和埠4           |
|網路介面    |2 x 1 Gbe RJ45 <br> 顯示為本機 UI 中的埠1和埠2          |
|Wi-Fi   |802.11ac         |


## <a name="power-supply-unit-specifications"></a>電源供應器裝置規格

Azure Stack Edge 迷你 R 裝置包含外部 85 W AC 介面卡，以提供電源並收取上線電池的電量。

下表顯示電源供應器單位規格：

| 規格           | 值                      |
|-------------------------|----------------------------|
| 最大輸出電力    | 85 W                       |
| 頻率               | 50/60 Hz                   |
| 選取電壓範圍 | 自動設定範圍：100-240 V AC |



## <a name="included-battery"></a>已包含電池

Azure Stack Edge 迷你 R 裝置也包含電源供應器所收取的上架電池。 

額外的2590電池類型可與上架電池搭配使用，以在費用之間擴充裝置的使用。 此電池應符合所有適用于使用規定的安全、運輸和環境法規。


| 規格           | 值                      |
|-------------------------|----------------------------|
| 上架電池容量 | 73瓦瓦                    |

## <a name="enclosure-dimensions-and-weight-specifications"></a>機箱尺寸和重量規格

下表列出各種機箱的尺寸和重量規格。

### <a name="enclosure-dimensions"></a>機箱尺寸

下表列出裝置的維度，以及具有堅固案例（毫米和英寸）的 USP。

|     機箱     |     公釐     |     英吋     |
|-------------------|---------------------|----------------|
|    高度         |    68            |    2.68          |
|    寬度          |    208          |      8.19          |
|    長度          |   259           |    10.20          |


### <a name="enclosure-weight"></a>機箱重量

下表列出裝置的權數，包括電池。

|     機箱                                 |     Weight          |
|-----------------------------------------------|---------------------|
|    裝置的總權數     |    7磅。          |

## <a name="enclosure-environment-specifications"></a>機箱環境規格


本節將列出機箱環境的相關規格，例如溫度、濕度和高度。


|     規格             |     描述                                                          |
|--------------------------------|--------------------------------------------------------------------------|
|     溫度範圍          |     0–43° C (操作)                                               |
|     震動                  |     >.MIL-STD-810 方法 514.7 *<br> 程式 I CAT 4，20                  |
|     休克                      |     >.MIL-STD-810 方法 516.7 *<br> 程式 IV，羅吉斯                 |
|     海拔                   |     操作：10000英尺<br> 無法運作：40000英尺          |

**所有參考都是 >.MIL-STD-810G Change 1 (2014)*


## <a name="next-steps"></a>後續步驟

- [部署您的 Azure Stack Edge 迷你 R](azure-stack-edge-placeholder.md)
