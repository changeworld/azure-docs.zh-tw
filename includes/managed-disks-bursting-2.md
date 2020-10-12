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
ms.openlocfilehash: cc0dd5e76227c0814659b74afc2ac46c8ca4de73
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "87102516"
---
## <a name="common-scenarios"></a>常見的案例
下列案例可以從高載大幅獲益：
- **改善開機時間**  –透過高載，您的實例會以更快的速度啟動。 例如，啟用高階 Vm 的預設 OS 磁片是 P4 磁片，其布建的效能高達 120 IOPS 和 25 MB/s。 使用負載平衡時，P4 最高可達 3500 IOPS 和 170 MB/秒，讓開機時間能夠加速6X。
- **處理批次作業** 時，某些應用程式的工作負載本質上是迴圈的，且在大部分的時間都需要基準效能，且需要較短的時間才會需要更高的效能。 其中一個範例是一種會計程式，每天處理需要少量磁片流量的交易。 然後，在本月結束時，會協調需要更多磁片流量的報告。
- **準備流量尖峰** – Web 服務器和其應用程式可能會隨時遇到流量激增。 如果您的 web 伺服器是由 Vm 或使用高載的磁片所支援，則伺服器更適合用來處理流量尖峰。 

## <a name="bursting-flow"></a>高載流程
在虛擬機器層級和磁片層級，高載點數系統的套用方式相同。 您的資源（VM 或磁片）會從完整的額度開始。 這些點數可讓您以最大的高載速率來高載30分鐘。 當您的資源在其效能磁片儲存體限制下執行時，會累積高載點數。 針對您的資源使用低於效能限制的所有 IOPS 和 MB/s，您會開始累積點數。 如果您的資源已累積要用於高載的點數，而您的工作負載需要額外的效能，您的資源可以使用這些信用額度來達到您的效能限制，以提供符合需求所需的磁片 IO 效能。



![高載 bucket 圖](media/managed-disks-bursting/bucket-diagram.jpg)

最多可讓您完全瞭解您要如何使用30分鐘的負載平衡。 您可以將它連續30分鐘，或在一整天的偶爾使用。 當產品部署完成時，它會備妥完整的點數，並在 depletes 點數時，不到一天就會再次取得完整的額度。 您可以依自己的意願累積並支出其高載信用額度，而30分鐘的值區不需要再填滿高載。 高載累積的其中一件事是，每個資源都不同，因為它是以低於其效能量的未使用 IOPS 和 MB/s 為基礎。 這表示較高的基準效能產品可以比較低的基準執行產品更快地產生高載量。 例如，不含活動的 P1 磁片閒置會每秒累積 120 IOPS，而 P20 磁片則會在閒置但沒有活動時，每秒產生 2300 IOPS。

## <a name="bursting-states"></a>高載狀態
在啟用高載的情況下，您的資源可以有三種狀態：
- **累積** –資源的 IO 流量使用的效能低於效能目標。 IOPS 和 MB/s 的累積高載點數是彼此分開的。 您的資源可能會累積 IOPS 點數和消費 MB/s 的信用額度，反之亦然。
- 高**載–資源**的流量使用的效能高於效能目標。 高載流量將會獨立使用 IOPS 或頻寬的點數。
- **常數** –資源的流量完全位於效能目標。

## <a name="examples-of-bursting"></a>高載範例
下列範例顯示高載如何搭配各種虛擬機器和磁片組合使用。 為了讓範例更容易遵循，我們將著重于 MB/s，但相同的邏輯會獨立套用至 IOPS。

### <a name="non-burstable-virtual-machine-with-burstable-disks"></a>具有高載磁片的非高載虛擬機器
**VM 和磁片組合：** 
- Standard_D8as_v4 
    - 未快取的 MB/秒：192
- P4 OS 磁片
    - 布建的 MB/秒：25
    - 最大高載 MB/秒：170 
- 2 P10 資料磁片 
    - 布建的 MB/秒：25
    - 最大高載 MB/秒：170

 當 VM 開機時，它會從 OS 磁片取出資料。 因為 OS 磁片是開始使用的 VM 的一部分，所以 OS 磁片將會填滿高載點數。 這些點數可讓 OS 磁片高載啟動的時間為 170 MB/秒，如下所示：

![非高載 vm 高載磁片啟動](media/managed-disks-bursting/nonbursting-vm-busting-disk/nonbusting-vm-bursting-disk-startup.jpg)

開機完成後，應用程式就會在 VM 上執行，並具有非關鍵性的工作負載。 此工作負載需要 15 MB/S，以平均分配到所有磁片：

![非高載 vm 高載磁片閒置](media/managed-disks-bursting/nonbursting-vm-busting-disk/nonbusting-vm-bursting-disk-idling.jpg)

然後，應用程式必須處理需要 192 MB/s 的批次作業。 OS 磁片會使用 2 MB/s，其餘資料磁片會平均分割：

![非高載 vm 負載平衡磁片高載](media/managed-disks-bursting/nonbursting-vm-busting-disk/nonbusting-vm-bursting-disk-bursting.jpg)

### <a name="burstable-virtual-machine-with-non-burstable-disks"></a>高載具有非高載磁片的虛擬機器
**VM 和磁片組合：** 
- Standard_L8s_v2 
    - 未快取的 MB/秒：160
    - 最大高載 MB/秒：1280
- P50 OS 磁片
    - 布建的 MB/秒：250 
- 2 P10 資料磁片 
    - 布建的 MB/秒：250

 初始開機之後，應用程式會在 VM 上執行，並具有非關鍵性的工作負載。 此工作負載需要 30 MB/s，以平均分配所有磁片： ![ 高載 vm 非高載磁片閒置](media/managed-disks-bursting/bursting-vm-nonbursting-disk/burst-vm-nonbursting-disk-normal.jpg)

然後，應用程式必須處理需要 600 MB/s 的批次作業。 Standard_L8s_v2 高載以符合此需求，然後將對磁片的要求平均分配給 P50 磁片：

![高載 vm 非高載磁片高載](media/managed-disks-bursting/bursting-vm-nonbursting-disk/burst-vm-nonbursting-disk-bursting.jpg)
### <a name="burstable-virtual-machine-with-burstable-disks"></a>高載具有高載磁片的虛擬機器
**VM 和磁片組合：** 
- Standard_L8s_v2 
    - 未快取的 MB/秒：160
    - 最大高載 MB/秒：1280
- P4 OS 磁片
    - 布建的 MB/秒：25
    - 最大高載 MB/秒：170 
- 2 P4 資料磁片 
    - 布建的 MB/秒：25
    - 最大高載 MB/秒：170 

當 VM 開機時，它會高載以要求其從 OS 磁片的高載限制 1280 MB/秒，而 OS 磁片將會回應其高載效能 170 MB/s：

![高載 vm 高載磁片啟動](media/managed-disks-bursting/bursting-vm-bursting-disk/burst-vm-burst-disk-startup.jpg)

完成開機之後，應用程式就會在 VM 上執行。 應用程式具有非關鍵性的工作負載，需要 15 MB/s，以平均分配到所有磁片：

![高載 vm 高載磁片閒置](media/managed-disks-bursting/bursting-vm-bursting-disk/burst-vm-burst-disk-idling.jpg)

然後，應用程式必須處理需要 360 MB/s 的批次作業。 Standard_L8s_v2 高載，以符合此要求，然後要求。 OS 磁片只需要 20 MB/s。 剩餘的 340 MB/秒是由高載 P4 資料磁片處理：  

![高載 vm 高載磁片高載](media/managed-disks-bursting/bursting-vm-bursting-disk/burst-vm-burst-disk-bursting.jpg)
