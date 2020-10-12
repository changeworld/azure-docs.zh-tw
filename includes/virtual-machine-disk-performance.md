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
ms.openlocfilehash: 9f5a1010959658e75dcc809b2ee1d6d9222af056
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91539971"
---
本文可協助您在結合 Azure 虛擬機器與 Azure 磁片時，瞭解磁片效能以及其運作方式。 它也會說明如何診斷磁片 IO 的瓶頸，以及您可以進行的變更，以優化效能。

## <a name="how-does-disk-performance-work"></a>磁片效能如何運作？
Azure 虛擬機器具有以虛擬機器類型和大小為基礎的 IOPS 和輸送量效能限制。 可以連接至虛擬機器的作業系統磁片和資料磁片具有自己的 IOPs 和輸送量限制。 當您在虛擬機器上執行的應用程式所要求的 IOPS 或輸送量超過虛擬機器或連接磁片的分配數量時，您應用程式的效能會受到限制。 發生這種情況時，應用程式將會遇到效能不佳的情況，而且可能會導致一些負面的後果，例如增加延遲。 讓我們執行幾個範例，以將這項程式化。 為了方便遵循這些範例，我們只會查看 IOPs，但相同邏輯也會套用至輸送量。

## <a name="disk-io-capping"></a>磁片 IO 上限
設定：
- Standard_D8s_v3 
    - 未快取的 IOPS：12800
- E30 OS 磁片
    - IOPS：500 
- 2 E30 資料磁片
    - IOPS：500

![磁片層級上限](media/vm-disk-performance/disk-level-throttling.jpg)

在虛擬機器上執行的應用程式會提出要求，要求將 10000 IOPs 提供給虛擬機器。 因為 Standard_D8s_v3 的虛擬機器最多可執行 12800 IOPs，所以 VM 允許所有這些 VM。 然後，這些 10000 IOPs 要求會細分成不同磁片的三個不同要求。 1000 IOPs 會要求至作業系統磁片，並對每個資料磁片要求 4500 IOPs。 由於所有連接的磁片都是 E30 磁片，而且只能處理 500 IOPs，因此每個磁片都會以 500 IOPs 回應。 然後，應用程式的效能會受到連接磁片的上限，而且只能處理 1500 IOPs。 如果使用效能較佳的磁片（例如進階 SSD P30 磁片），它可能會以 10000 IOPS 的尖峰效能運作。

## <a name="virtual-machine-io-capping"></a>虛擬機器 IO 上限
設定：
- Standard_D8s_v3 
    - 未快取的 IOPS：12800
- P30 OS 磁片
    - IOPS：5000 
- 2 P30 資料磁片 
    - IOPS：5000

![虛擬機器層級上限](media/vm-disk-performance/vm-level-throttling.jpg)

在虛擬機器上執行的應用程式會提出要求，需要 15000 IOPs。 可惜的是，Standard_D8s_v3 的虛擬機器只會布建來處理 12800 IOPs。 如此一來，應用程式就會受到虛擬機器限制的限制，然後必須配置分配的 12800 IOPs。 這些所要求的 12800 IOPs 接著會細分成不同磁片的三個不同要求。 4267 IOPs 會要求至作業系統磁片，並對每個資料磁片要求 4266 IOPs。 由於所有連接的磁片都是 P30 磁片（可處理 5000 IOPs），因此會以其要求的數量回復。