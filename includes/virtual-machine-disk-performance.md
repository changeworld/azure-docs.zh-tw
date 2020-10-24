---
title: 包含檔案
description: 包含檔案
services: virtual-machines
author: albecker1
ms.service: virtual-machines
ms.topic: include
ms.date: 09/25/2020
ms.author: albecker1
ms.custom: include file
ms.openlocfilehash: 4770ac0181c64ef800aa02ba87284c8add357e36
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/24/2020
ms.locfileid: "92518042"
---
本文有助於說明磁片效能，以及當您合併 Azure 虛擬機器與 Azure 磁片時的運作方式。 它也會說明如何診斷磁片 IO 的瓶頸，以及您可以進行的變更，以優化效能。

## <a name="how-does-disk-performance-work"></a>磁片效能如何運作？
Azure 虛擬機器的每秒輸入/輸出作業 (IOPS) 和輸送量效能限制，以虛擬機器類型和大小為基礎。 作業系統磁片和資料磁片可以連接至虛擬機器。 磁片具有自己的 IOPS 和輸送量限制。

當您的應用程式所要求的 IOPS 或輸送量超過虛擬機器或連接磁片所分配的數量時，其效能會獲得上限。 當限制時，應用程式會遇到效能不佳的情況。 這可能會造成負面的後果，例如增加延遲。 讓我們執行一些範例來說明這個概念。 為了讓這些範例易於遵循，我們只會查看 IOPS。 但是，相同的邏輯也適用于輸送量。

## <a name="disk-io-capping"></a>磁片 IO 上限

**設置：**

- Standard_D8s_v3
  - 未快取的 IOPS：12800
- E30 OS 磁片
  - IOPS：500
- 兩個 E30 資料磁片×2
  - IOPS：500

![顯示磁片層級上限的圖表。](media/vm-disk-performance/disk-level-throttling.jpg)

在虛擬機器上執行的應用程式會提出要求，要求將 10000 IOPS 提供給虛擬機器。 因為 Standard_D8s_v3 的虛擬機器最多可執行 12800 IOPS，所以 VM 允許所有這些 VM。

10000 IOPS 要求會分成三個不同的不同磁片要求：

- 系統會將 1000 IOPS 要求至作業系統磁片。
- 4500 IOPS 會要求至每個資料磁片。

所有連接的磁片都是 E30 磁片，而且只能處理 500 IOPS。 因此，它們會以 500 IOPS 回應。 應用程式的效能會受到連接磁片的上限，而且只能處理 1500 IOPS。 如果使用效能較佳的磁片（例如進階 SSD P30 磁片），應用程式可以在 10000 IOPS 的尖峰效能下運作。

## <a name="virtual-machine-io-capping"></a>虛擬機器 IO 上限

**設置：**

- Standard_D8s_v3
  - 未快取的 IOPS：12800
- P30 OS 磁片
  - IOPS：5000
- 兩個 P30 資料磁片×2
  - IOPS：5000

![顯示虛擬機器層級上限的圖表。](media/vm-disk-performance/vm-level-throttling.jpg)

在虛擬機器上執行的應用程式會提出要求，需要 15000 IOPS。 可惜的是，Standard_D8s_v3 的虛擬機器只會布建來處理 12800 IOPS。 應用程式會限制虛擬機器的限制，且必須配置分配的 12800 IOPS。

這些所要求的 12800 IOPS 會分成三個不同的不同磁片要求：

- 系統會將 4267 IOPS 要求至作業系統磁片。
- 4266 IOPS 會要求至每個資料磁片。

所有連接的磁片都是 P30 磁片，可處理 5000 IOPS。 因此，它們會回應其要求的數量。
