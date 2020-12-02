---
title: Microsoft Azure Stack Edge Pro R 技術規格和合規性 |Microsoft Docs
description: 瞭解您 Azure Stack Edge Pro R 裝置的技術規格和合規性
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: alkohli
ms.openlocfilehash: 56d301762fde41f727b0b425d6c41a423f08103c
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/01/2020
ms.locfileid: "96466249"
---
# <a name="azure-stack-edge-pro-r-technical-specifications"></a>Azure Stack Edge Pro R 技術規格

您 Azure Stack Edge Pro R 裝置的硬體元件符合本文中所述的技術規格。 技術規格說明電源供應器裝置 (PSU)、儲存體容量、機箱和環境標準。


## <a name="compute-memory-specifications"></a>計算、記憶體規格

Azure Stack Edge Pro R 裝置具有下列計算和記憶體的規格：

| 規格       | 值                  |
|---------------------|------------------------|
| CPU    | 2 X 10 核心 CPU，Intel 第4級的銀級4114 |
| Memory              | 256 GB RAM (2666 MT/s)      |


## <a name="compute-acceleration-specifications"></a>計算加速規格

圖形處理器 (GPU) 包含在 Kubernetes、深度學習和機器學習案例的每一部裝置上。

| 規格           | 值                  |
|-------------------------|----------------------------|
| GPU   | 一個 nVidia T4 GPU <br> 如需詳細資訊，請參閱 [NVIDIA T4](https://www.nvidia.com/en-us/data-center/tesla-t4/)。| 

## <a name="power-supply-unit-specifications"></a>電源供應器裝置規格

Azure Stack Edge Pro R 裝置有兩個 100-240 V 電源供應器單位 (Psu) 具有高效能風扇。 這兩個 PSU 提供了備援電源設定。 如果 PSU 故障，裝置會繼續在其他 PSU 上正常運作，直到更換故障的模組為止。 下表列出 PSU 的技術規格。

| 規格           | 550 W PSU                  |
|-------------------------|----------------------------|
| 最大輸出電力    | 550 W                      |
|  (最大) 的熱度散熱                   | 2891 BTU/小時                |
| 頻率               | 50/60 Hz                   |
| 選取電壓範圍 | 自動範圍： 115-230 V AC |
| 隨插即用           | 是                        |

## <a name="network-specifications"></a>網路規格

Azure Stack Edge Pro R 裝置有四個網路介面： PORT1-PORT4。 


|規格  |描述                              |
|----------------------|----------------------------------|
|網路介面    |**2 x 1 Gbe RJ45** <br> 埠1做為初始設定的管理介面，預設為靜態。 初始設定完成後，您可以將介面用於具有任何 IP 位址的資料。 不過，在重設時，介面會還原回靜態 IP。 <br>另一個介面埠2是使用者可設定的，可用於資料傳輸，且預設為 DHCP。     |
|網路介面    |**2 x 25 Gbe SFP28** <br> 這些資料介面埠3和埠4可以設定為 DHCP (預設) 或靜態。            |

您 Azure Stack Edge Pro R 裝置具有下列網路硬體：

* **Mellanox 雙重埠 25G ConnectX-4 通道網路介面卡** -埠3和埠4。 

<!--Here are the details for the Mellanox card: MCX4421A-ACAN

| Parameter           | Description                 |
|-------------------------|----------------------------|
| Model    | ConnectX®-4 Lx EN network interface card                      |
| Model Description               | 25GbE dual-port SFP28; PCIe3.0 x8; ROHS R6                    |
| Device Part Number (XR2) | MCX4421A-ACAN  |
| PSID (R640)           | MT_2420110034                         |-->
<!-- confirm w/ Ravi what is this-->

如需這些網路卡支援的纜線、交換器及收發器的完整清單，請移至： [Mellanox 雙埠 25G ConnectX-4 通道網路介面卡相容產品](https://docs.mellanox.com/display/ConnectX4LxFirmwarev14271016/Firmware+Compatible+Products)。

## <a name="storage-specifications"></a>儲存體規格

Azure Stack Edge Pro R 裝置有8個數據磁片和2個2個 SATA 磁片，作為作業系統磁片。 如需詳細資訊，請移至 [ [M. 2 SATA 磁片](https://en.wikipedia.org/wiki/M.2)]。

#### <a name="storage-for-1-node-device"></a>1個節點裝置的儲存體

下表包含1個節點裝置之儲存體容量的詳細資料。

|     規格                          |     值             |
|--------------------------------------------|-----------------------|
|    固態硬碟 (SSD) 數     |    8                  |
|    單一 SSD 容量                     |    8 TB               |
|    容量總計                          |    64 TB              |
|    可用容量總計*                  |    ~ 42 TB          |

**部分空間保留供內部使用。*

<!--#### Storage for 4-node device

The following table has the details for the storage capacity of the 4-node device.

|     Specification                          |     Value             |
|--------------------------------------------|-----------------------|
|    Number of solid-state drives (SSDs)     |    32 (4 X 8 disks for 4 devices)                |
|    Single SSD capacity                     |    8 TB               |
|    Total capacity                          |    256 TB              |
|    Total usable capacity*                  |    ~ 163 TB          |

**After mirroring and parity, and reserving some space for internal use.* -->


## <a name="enclosure-dimensions-and-weight-specifications"></a>機箱尺寸和重量規格

下表列出各種機箱的尺寸和重量規格。

### <a name="enclosure-dimensions"></a>機箱尺寸 

下表列出裝置的尺寸，以及以毫米和英寸為堅固的情況的 UPS。

|     機箱     |     公釐     |     英吋     |
|-------------------|---------------------|----------------|
|    高度         |    301.2            |    11.86       |
|    寬度          |    604.5            |    23.80       |
|    長度         |    740.4            |    35.50       |

<!--#### For the 4-node system

For the 4-node system, the servers and the heater are shipped in a 5U case and the UPS are shipped in a 4U case.

The following table lists the dimensions of the 5U device case:  

|     Enclosure     |     Millimeters   |     Inches     |
|-------------------|-------------------|----------------|
|    Height         |    387.4          |    15.25       |
|    Width          |    604.5          |    23.80       |
|    Length         |    901.7          |    35.50       |

The following table lists the dimensions of the 4U UPS case: 

|     Enclosure     |     Millimeters   |     Inches    |
|-------------------|-------------------|---------------|
|    Height         |    342.9          |    13.5       |
|    Width          |    604.5          |   23.80       |
|    Length         |    901.7          |   35.50       |
-->

### <a name="enclosure-weight"></a>機箱重量 

裝置重量視機箱設定而定。

|     機箱                                 |     Weight          |
|-----------------------------------------------|---------------------|
|    總權數（1個節點裝置 + 具有一般大小的堅固案例）     |    ~ 114 磅。          |

<!--#### For the 4-node system

|     Enclosure                                 |     Weight          |
|-----------------------------------------------|---------------------|
|   Approximate weight of fully populated 4 devices + heater in 5U case     |    ~200 lbs.          |
|   Approximate weight of fully populated 4 UPS in 4U case    |    ~145 lbs.          |
-->

## <a name="enclosure-environment-specifications"></a>機箱環境規格

本節列出與主機殼環境相關的規格，例如溫度、震動、震動和高度。


|     規格              |     值    |
|--------------------------------|-------------------------------------------------------------------|
|     溫度範圍          |     0–43° C (操作)     |
|     震動                  |     >.MIL-STD-810 方法 514.7 *<br>程式 I CAT 4，20                  |
|     休克                      |     >.MIL-STD-810 方法 516.7 *<br>程式 IV，羅吉斯                 |
|     海拔                   |     操作：10000英尺<br>無法運作：40000英尺          |

**所有參考都是 >.MIL-STD-810G Change 1 (2014)*

## <a name="next-steps"></a>後續步驟

- [部署您的 Azure Stack Edge](azure-stack-edge-placeholder.md)
