---
title: B 系列高載-Azure 虛擬機器
description: 描述 B 系列高載 Azure VM 的大小。
services: virtual-machines
ms.subservice: sizes
author: styli365
ms.service: virtual-machines
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: sttsinar
ms.openlocfilehash: 819654ef88584cb91d6032e46256258aaed524fd
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/02/2020
ms.locfileid: "96500303"
---
# <a name="b-series-burstable-virtual-machine-sizes"></a>B 系列高載虛擬機器大小

B 系列 Vm 非常適合不需要持續完整 CPU 效能的工作負載，例如 web 伺服器、概念證明、小型資料庫和開發組建環境。 這些工作負載通常具有高載的效能需求。 B 系列可讓您購買具有基準效能的 VM 大小，以在使用低於其基準時建立點數。 當 VM 已累積點數時，VM 可以在您的應用程式需要較高的 CPU 效能時，使用最多100% 的 vCPU 來高載高於基準。

B 系列有下列 VM 大小：

[Azure 計算單位 (ACU) ](./acu.md?bc=%2fazure%2fvirtual-machines%2flinux%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)：改變 *<br>
[進階儲存體](premium-storage-performance.md)：支援<br>
[進階儲存體](premium-storage-performance.md)快取：不支援<br>
[即時移轉](maintenance-and-updates.md)：支援<br>
[記憶體保留更新](maintenance-and-updates.md)：支援<br>
[VM 世代支援](generation-2.md)：第1代和第2代<br>
<br>
* B 系列 Vm 是高載的，因此 ACU 數位會隨著工作負載和核心使用量而有所不同。

| 大小 | vCPU | 記憶體：GiB | 暫存儲存體 (SSD) GiB | VM 的基礎 CPU 效能 | VM 的最大 CPU 效能 | 初始點數 | 信用額度存款/小時 | 最大累積點數 | 最大資料磁碟 | 最大快取和暫存儲存體輸送量IOPS/MBps | 最大取消快取的磁碟輸送量：IOPS/MBps | 最大 NIC |
|---|---|---|---|---|---|---|---|---|---|---|---|---|
| Standard_B1ls<sup>1</sup> | 1  | 0.5 | 4   | 5%   | 100%  | 30  | 3   | 72   | 2  | 200/10    | 160/10    | 2  |
| Standard_B1s              | 1  | 1   | 4   | 10%  | 100%  | 30  | 6   | 144  | 2  | 400/10    | 320/10    | 2  |
| Standard_B1ms             | 1  | 2   | 4   | 20%  | 100%  | 30  | 12  | 288  | 2  | 800/10    | 640/10    | 2  |
| Standard_B2s              | 2  | 4   | 8   | 40%  | 200%  | 60  | 24  | 576  | 4  | 1600/15   | 1280/15   | 3  |
| Standard_B2ms             | 2  | 8   | 16  | 60%  | 200%  | 60  | 36  | 864  | 4  | 2400/22。5 | 1920/22。5 | 3  |
| Standard_B4ms             | 4  | 16  | 32  | 90%  | 400%  | 120 | 54  | 1296 | 8  | 3600/35   | 2880/35   | 4  |
| Standard_B8ms             | 8  | 32  | 64  | 135% | 800%  | 240 | 81  | 1944 | 16 | 4320/50   | 4320/50   | 4  |
| Standard_B12ms            | 12 | 48  | 96  | 202% | 1200% | 360 | 121 | 2909 | 16 | 6480/75   | 4320/50   | 6  |
| Standard_B16ms            | 16 | 64  | 128 | 270% | 1600% | 480 | 162 | 3888 | 32 | 8640/100  | 4320/50   | 8  |
| Standard_B20ms            | 20 | 80  | 160 | 337% | 2000% | 600 | 203 | 4860 | 32 | 10800/125 | 4320/50   | 8  |

只有 Linux 上支援<sup>1</sup> B1ls

## <a name="workload-example"></a>工作負載範例

考慮使用 office 簽入/輸出應用程式。 應用程式在上班時間需要 CPU 高載，但在下班時間不需要大量運算能力。 在此範例中，工作負載需要具有 64GiB RAM 的16vCPU 虛擬機器才能有效率地運作。

資料表會顯示每小時的流量資料，而圖表則是該流量的視覺標記法。

B16 特性：

最大 CPU 效能： 16vCPU * 100% = 1600%

基準：270%

![每小時流量資料的圖表](./media/b-series-burstable/office-workload.png)

| 案例 | Time | CPU 使用量 (% )  | 累積的點數<sup>1</sup> | 可用的點數 |
| --- | --- | --- | --- | --- |
| B16ms 部署 | 部署 | 部署  | 480 (初始點數)  | 480 |
| 沒有流量 | 0:00 | 0 | 162 | 642 |
| 沒有流量 | 1:00 | 0 | 162 | 804 |
| 沒有流量 | 2:00 | 0 | 162 | 966 |
| 沒有流量 | 3:00 | 0 | 162 | 1128 |
| 沒有流量 | 4:00 | 0 | 162 | 1290 |
| 沒有流量 | 5:00 | 0 | 162 | 1452 |
| 低流量 | 6:00 | 270 | 0 | 1452 |
| 員工進入 office (應用程式需要 80% vCPU)  | 7:00 | 1280 | -606 | 846 |
| 員工持續進入 office (應用程式需要 80% vCPU)  | 8:00 | 1280 | -606 | 240 |
| 低流量 | 9:00 | 270 | 0 | 240 |
| 低流量 | 10:00 | 100 | 102 | 342 |
| 低流量 | 11:00 | 50 | 132 | 474 |
| 低流量 | 12:00 | 100 | 102 | 576 |
| 低流量 | 13:00 | 100 | 102 | 678 |
| 低流量 | 14:00 | 50 | 132 | 810 |
| 低流量 | 15:00 | 100 | 102 | 912 |
| 低流量 | 16:00 | 100 | 102 | 1014 |
| 員工簽出 (應用程式需要 100% vCPU)  | 17:00 | 1600 | -798 | 216 |
| 低流量 | 18:00 | 270 | 0 | 216 |
| 低流量 | 19:00 | 270 | 0 | 216 |
| 低流量 | 20:00 | 50 | 132 | 348 |
| 低流量 | 下午 9 點 | 50 | 132 | 480 |
| 沒有流量 | 22:00 | 0 | 162 | 642 |
| 沒有流量 | 23:00 | 0 | 162 | 804 |

<sup>1</sup> 小時的累積點數/點數相當於： `((Base CPU perf of VM - CPU Usage) / 100) * 60 minutes` 。  

對於具有16個 vcpu 和 64 GiB 記憶體的 D16s_v3，每小時的每小時費率為 $0.936 (每月 $673.92) 以及16個 vcpu 和 64 GiB 記憶體的 B16ms，速率為每小時 $0.794 (每月 $547.86) 。 <b> 這會節省15% 的費用！</b>

## <a name="q--a"></a>問答集

### <a name="q-what-happens-when-my-credits-run-out"></a>問：當我的點數用盡時，會發生什麼事？
**答**：當點數用盡時，VM 會回到基準效能。

### <a name="q-how-do-you-get-135-baseline-performance-from-a-vm"></a>問：如何從 VM 取得 135% 的基準效能？

**答**：會在構成 VM 大小的 8 個 vCPU 之間共用 135%。 例如，如果您的應用程式將 8 個核心其中 4 個用於批次處理，而這 4 個 vCPU 每個都以 30% 的使用率執行，VM CPU 效能的總數就會等於 120%。  這表示 VM 會以您基準效能的 15% 差異作為基礎來建置點數時間。  但這也表示，當您有可用點數時，該相同 VM 可以 100% 使用所有 8 個 vCPU，使該 VM 擁有 800% 的最大 CPU 效能。

### <a name="q-how-can-i-monitor-my-credit-balance-and-consumption"></a>問：如何監視我的點數餘額和耗用量？

**答**：點數計量可讓您查看 vm 已累積 **的點數，** 而 **ConsumedCredit** 計量會顯示您的 vm 已從銀行取用多少個 CPU 點數。    您可以在入口網站中的 [計量] 窗格檢視這些計量，或透過 Azure 監視器 API 以程式設計方式檢視。

如需如何存取 Azure 計量資料的詳細資訊，請參閱 [Microsoft Azure 的計量概觀](../azure-monitor/platform/data-platform.md)。

### <a name="q-how-are-credits-accumulated-and-consumed"></a>問：點數如何累積和取用？

**答**：VM 累積與消耗率設定的方式，是以基礎效能層級執行的 VM 會具有高載點數的淨累積或耗用量。  每當 VM 在其基礎效能層級下執行時，點數淨值都會增加，且每當 VM 利用 CPU 超過其基礎效能層級時，點數淨值就會降低。

**範例**：我針對短暫的時間使用 B1ms 大小及現身資料庫應用程式來部署 VM。 這個大小可讓我的應用程式使用最多 20% 的 vCPU 作為基準，也就是我每分鐘可以使用或累積 0.2 個點數。

我的應用程式從我員工的工作日開始運作到員工下班，時間是上午 7:00-9:00 和下午 4:00 - 6:00 之間。 一天的其他 20 小時內，我的應用程式通常為閒置狀態，只會使用 10% 的 vCPU。 在非尖峰時間，我每分鐘獲得 0.2 個點數，但每分鐘只使用 0.1 個點數，因此我的 VM 每小時會累積 0.1 x 60 = 6 個點數。  在離峰的 20 小時內，我會累積 120 個點數。  

在尖峰時間內，我的應用程式平均會有 60% 的 vCPU 使用率，我每分鐘仍可獲得 0.2 個點數，但我每分鐘使用 0.6 個點數，一分鐘的淨成本是 0.4 個點數，也就是每小時 0.4 x 60 = 24 個點數。 我每日有 4 小時的尖峰使用量，因此尖峰使用量的成本是 4 x 24 = 96 個點數。

如果我用離峰時間所獲得的 120 個點數減去尖峰時間使用的 96 個點數，每天就可以額外累積 24 個點數，能用於其他高載的活動。

### <a name="q-how-can-i-calculate-credits-accumulated-and-used"></a>問：如何計算已累積和使用的點數？

**答**：您可以使用下列公式：

VM 的 (基礎 CPU 效能-CPU 使用量) /100 = 點數 bank 或每分鐘使用

例如，在上述情況中，您的基準為20%，如果您使用10% 的 CPU，則 (20%-10% ) /100 = 0.1 點數（每分鐘）累積。

### <a name="q-does-the-b-series-support-premium-storage-data-disks"></a>問：B 系列是否支援進階儲存體資料磁碟？

**答**：是的，所有 B 系列大小都支援進階儲存體資料磁碟。

### <a name="q-why-is-my-remaining-credit-set-to-0-after-a-redeploy-or-a-stopstart"></a>問：為何在重新部署或停止/啟動之後，剩餘信用額度會設定為 0？

**答**：當虛擬機器「重新部署」且虛擬機器移到另一個節點時，累積的信用額度會遺失。 如果虛擬機器停止/啟動，但仍留在相同節點上，則虛擬機器會保留累積的信用額度。 每當 VM 在節點上啟動時，它就會取得初始點數，Standard_B8ms 為240。

### <a name="q-what-happens-if-i-deploy-an-unsupported-os-image-on-b1ls"></a>問：如果我在 B1ls 上部署不支援的 OS 映射，會發生什麼事？

**答** ： B1ls 僅支援 Linux 映射，如果您部署任何其他的作業系統映射，則可能無法獲得最佳的客戶體驗。

## <a name="other-sizes-and-information"></a>其他大小和資訊

- [一般用途](sizes-general.md)
- [計算最佳化](sizes-compute.md)
- [記憶體最佳化](sizes-memory.md)
- [儲存體最佳化](sizes-storage.md)
- [GPU 最佳化](sizes-gpu.md)
- [高效能計算](sizes-hpc.md)

定價計算機： [定價計算機](https://azure.microsoft.com/pricing/calculator/)

磁片類型的詳細資訊： [磁片類型](./disks-types.md#ultra-disk)

## <a name="next-steps"></a>後續步驟

深入了解 [Azure 計算單位 (ACU)](acu.md) 如何協助您比較各個 Azure SKU 的計算效能。
