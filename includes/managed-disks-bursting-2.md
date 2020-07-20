---
title: 包含檔案
description: 包含檔案
services: virtual-machines
author: albecker1
ms.service: virtual-machines
ms.topic: include
ms.date: 04/27/2020
ms.author: albecker1
ms.custom: include file
ms.openlocfilehash: 0b278841fc3693d79821d25caf7c9a208341dea1
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85242148"
---
## <a name="common-scenarios"></a>常見案例
下列案例可大幅受益于負載平衡：
- **改善開機時間**–有了負載平衡，您的實例將會以明顯更快的速度啟動。 例如，啟用 premium 之 Vm 的預設 OS 磁片是 P4 磁片，這是高達 120 IOPS 和 25 MB/秒的布建效能。 透過高載，P4 最高可達 3500 IOPS 和 170 MB/s，讓開機時間能夠加速6X。
- **處理批次作業**–某些應用程式的工作負載本質上是迴圈的，而且大部分的時間都需要基準效能，而且短時間內需要較高的效能。 其中一個範例是會計程式，每日處理需要少量磁片流量的交易。 然後，在該月結束時，會協調需要更高磁片流量量的報告。
- **準備流量尖峰**– Web 服務器及其應用程式可能會隨時遇到流量激增。 如果您的 web 伺服器是由使用負載平衡的 Vm 或磁片所支援，則伺服器較適合用來處理流量尖峰。 

## <a name="bursting-flow"></a>負載平衡流程
在虛擬機器層級和磁片層級上，高載點數系統的套用方式相同。 您的資源（VM 或磁片）將會以完全貯存的點數開始。 這些點數可讓您以最大的高載速率，以30分鐘為單位。 當您的資源在其效能磁片儲存體限制之下執行時，會累積高載點數。 對於您的資源使用低於效能限制的所有 IOPS 和 MB/s，您會開始累積點數。 如果您的資源有用於高載的已累積點數，而您的工作負載需要額外的效能，則您的資源可以使用這些信用額度來高於您的效能限制，讓它達到滿足需求所需的磁片 IO 效能。



![高載 bucket 圖表](media/managed-disks-bursting/bucket-diagram.jpg)

最多可讓您瞭解如何使用30分鐘的高載。 您可以在一天內連續30分鐘或偶爾使用它。 當產品部署完成時，就會備妥完整的點數，而當它 depletes 信用額度時，將會花費不到一天的時間，再次取得完整的點數。 您可以依自己的需求累積和支出其高載點數，而30分鐘的值區不需要再次填滿。 有關高載累積的一件事，就是每個資源都不同，因為它是以未使用的 IOPS 和低於其效能量的 MB/s 為基礎。 這表示較高的基準效能產品可以比執行較低基準的產品更快地累積其負載平衡。 例如，沒有活動的 P1 磁片閒置下來會每秒累積 120 IOPS，而 P20 磁片則會在閒置下來時每秒產生 2300 IOPS，而不會有任何活動。

## <a name="bursting-states"></a>高載狀態
有三種狀態：您的資源可以在啟用高載功能時使用。。
- **累積**–資源的 IO 流量使用低於效能目標。 IOPS 和 MB/s 的累積高載點數，會彼此分開完成。 您的資源可能會產生 IOPS 點數和消費 MB/s 點數，反之亦然。
- 負載**平衡–資源**的流量使用的效能目標超過一個。 高載流量會獨立使用 IOPS 或頻寬的點數。
- **常數**–資源的流量會與效能目標完全相同。

## <a name="examples-of-bursting"></a>高載的範例
下列範例顯示高載如何與各種虛擬機器和磁片組合搭配運作。 為了讓範例更容易遵循，我們將著重于 MB/s，但相同的邏輯會獨立套用至 IOPS。

### <a name="non-burstable-virtual-machine-with-burstable-disks"></a>具有高載磁片的非高載虛擬機器
**VM 和磁片組合：** 
- Standard_D8as_v4 
    - 未快取的 MB/s：192
- P4 OS 磁片
    - 布建的 MB/s：25
    - 最大高載 MB/秒：170 
- 2 P10 資料磁片 
    - 布建的 MB/s：25
    - 最大高載 MB/秒：170

 當 VM 開機時，它會從 OS 磁片取得資料。 因為 OS 磁片是開始使用之 VM 的一部分，所以 OS 磁片將會滿高載點數。 這些點數可讓 OS 磁片在 170 MB/秒的啟動時間高載，如下所示：

![非負載平衡 vm 負載平衡磁片啟動](media/managed-disks-bursting/nonbursting-vm-busting-disk/nonbusting-vm-bursting-disk-startup.jpg)

開機完成後，應用程式就會在 VM 上執行，並具有非關鍵性的工作負載。 此工作負載需要 15 MB/S，可平均分散到所有磁片：

![非負載平衡 vm 負載平衡磁片閒置](media/managed-disks-bursting/nonbursting-vm-busting-disk/nonbusting-vm-bursting-disk-idling.jpg)

然後，應用程式需要處理需要 192 MB/s 的批次作業。 2 MB/s 是由 OS 磁片使用，其餘部分則平均分割于資料磁片：

![非負載平衡 vm 負載平衡磁片負載平衡](media/managed-disks-bursting/nonbursting-vm-busting-disk/nonbusting-vm-bursting-disk-bursting.jpg)

### <a name="burstable-virtual-machine-with-non-burstable-disks"></a>具有非高載磁片的高載虛擬機器
**VM 和磁片組合：** 
- Standard_L8s_v2 
    - 未快取的 MB/s：160
    - 最大高載 MB/秒：1280
- P50 OS 磁片
    - 布建的 MB/s：250 
- 2 P10 資料磁片 
    - 布建的 MB/s：250

 在初始開機之後，應用程式會在 VM 上執行，並具有非關鍵性的工作負載。 此工作負載需要 30 MB/s，可平均分散到所有磁片：負載平衡 ![ vm 非負載平衡磁片閒置](media/managed-disks-bursting/bursting-vm-nonbursting-disk/burst-vm-nonbursting-disk-normal.jpg)

然後，應用程式需要處理需要 600 MB/s 的批次作業。 Standard_L8s_v2 高載以符合此需求，然後對磁片的要求會平均分散到 P50 磁片：

![負載平衡 vm 非負載平衡磁片負載平衡](media/managed-disks-bursting/bursting-vm-nonbursting-disk/burst-vm-nonbursting-disk-bursting.jpg)
### <a name="burstable-virtual-machine-with-burstable-disks"></a>具有高載磁片的高載虛擬機器
**VM 和磁片組合：** 
- Standard_L8s_v2 
    - 未快取的 MB/s：160
    - 最大高載 MB/秒：1280
- P4 OS 磁片
    - 布建的 MB/s：25
    - 最大高載 MB/秒：170 
- 2 P4 資料磁片 
    - 布建的 MB/s：25
    - 最大高載 MB/秒：170 

當 VM 開機時，它會高載，要求其從 OS 磁片的高載限制為 1280 MB/秒，而 OS 磁片會回應其高載效能 170 MB/秒：

![負載平衡 vm 負載平衡磁片啟動](media/managed-disks-bursting/bursting-vm-bursting-disk/burst-vm-burst-disk-startup.jpg)

等到開機完成後，就會在 VM 上執行應用程式。 應用程式有非關鍵性的工作負載，需要 15 MB/s，可平均分散到所有磁片：

![虛擬機器負載平衡磁片閒置](media/managed-disks-bursting/bursting-vm-bursting-disk/burst-vm-burst-disk-idling.jpg)

然後，應用程式需要處理需要 360 MB/s 的批次作業。 Standard_L8s_v2 高載以符合此要求，然後要求。 OS 磁片只需要 20 MB/秒。 剩餘的 340 MB/秒是由高載 P4 資料磁片所處理：  

![平衡 vm 負載平衡磁片負載平衡](media/managed-disks-bursting/bursting-vm-bursting-disk/burst-vm-burst-disk-bursting.jpg)