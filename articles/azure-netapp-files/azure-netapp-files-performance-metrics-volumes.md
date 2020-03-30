---
title: 推薦的性能基準測試 - Azure NetApp 檔
description: 使用 Azure NetApp 檔瞭解卷性能和指標的基準測試建議。
author: b-juche
ms.author: b-juche
ms.service: azure-netapp-files
ms.workload: storage
ms.topic: conceptual
ms.date: 08/07/2019
ms.openlocfilehash: 8f354152c23dd7ad0413f27585d724f8070ca003
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75551517"
---
# <a name="performance-benchmark-test-recommendations-for-azure-netapp-files"></a>適用於Azure NetApp Files 的效能基準測試建議

本文提供使用 Azure NetApp 檔提供的卷性能和指標的基準測試建議。

## <a name="overview"></a>總覽

要瞭解 Azure NetApp 檔卷的性能特徵，可以使用開源工具[FIO](https://github.com/axboe/fio)運行一系列基準測試來類比各種工作負載。 FIO 可以安裝在 Linux 和基於 Windows 的作業系統上。  它是獲取卷 IOPS 和輸送量的快速快照的絕佳工具。

### <a name="vm-instance-sizing"></a>VM 實例大小調整

為獲得最佳結果，請確保使用的虛擬機器 （VM） 實例的大小適合執行測試。 以下示例使用Standard_D32s_v3實例。 有關 VM 實例大小的詳細資訊，請參閱 Azure 中基於 Windows 的 VM 的[Windows 虛擬機器大小](https://docs.microsoft.com/azure/virtual-machines/windows/sizes?toc=%2fazure%2fvirtual-network%2ftoc.json)，以及 Azure 中基於 Linux 的 VM 的[Linux 虛擬機器的大小](https://docs.microsoft.com/azure/virtual-machines/linux/sizes?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。

### <a name="azure-netapp-files-volume-sizing"></a>Azure NetApp 檔卷大小調整

確保為預期性能級別選擇正確的服務等級和卷配額大小。 有關詳細資訊[，請參閱 Azure NetApp 檔的服務等級](azure-netapp-files-service-levels.md)。

### <a name="virtual-network-vnet-recommendations"></a>虛擬網路 （VNet） 建議

應在與 Azure NetApp 檔相同的 VNet 中執行基準測試。 下面的示例演示了以下建議：

![VNet 建議](../media/azure-netapp-files/azure-netapp-files-benchmark-testing-vnet.png)

## <a name="installation-of-fio"></a>國際職能指令手冊的安裝

FIO 在 Linux 和 Windows 中都提供二進位格式。 按照[FIO](https://github.com/axboe/fio)中的二進位包部分進行安裝，以適合您的平臺。

## <a name="fio-examples-for-iops"></a>IOPS 的 FIO 示例 

本節中的 FIO 示例使用以下設置：
* VM 實例大小：D32s_v3
* 容量池服務等級和大小：高級 / 50 TiB
* 卷配額大小： 48 TiB

以下示例顯示了 FIO 隨機讀取和寫入。

### <a name="fio-8k-block-size-100-random-reads"></a>FIO： 8k 塊大小 100% 隨機讀取

`fio --name=8krandomreads --rw=randread --direct=1 --ioengine=libaio --bs=8k --numjobs=4 --iodepth=128 --size=4G --runtime=600 --group_reporting`

### <a name="output-68k-read-iops-displayed"></a>輸出： 顯示 68k 讀取 IOPS

`Starting 4 processes`  
`Jobs: 4 (f=4): [r(4)][84.4%][r=537MiB/s,w=0KiB/s][r=68.8k,w=0 IOPS][eta 00m:05s]`

### <a name="fio-8k-block-size-100-random-writes"></a>FIO： 8k 塊大小 100% 隨機寫入

`fio --name=8krandomwrites --rw=randwrite --direct=1 --ioengine=libaio --bs=8k --numjobs=4 --iodepth=128  --size=4G --runtime=600 --group_reporting`

### <a name="output-73k-write-iops-displayed"></a>輸出：顯示 73k 寫入 IOPS

`Starting 4 processes`  
`Jobs: 4 (f=4): [w(4)][26.7%][r=0KiB/s,w=571MiB/s][r=0,w=73.0k IOPS][eta 00m:22s]`

## <a name="fio-examples-for-bandwidth"></a>頻寬 FIO 示例

本節中的示例顯示了 FIO 順序讀取和寫入。

### <a name="fio-64k-block-size-100-sequential-reads"></a>FIO： 64k 塊大小 100% 順序讀取

`fio --name=64kseqreads --rw=read --direct=1 --ioengine=libaio --bs=64k --numjobs=4 --iodepth=128  --size=4G --runtime=600 --group_reporting`

### <a name="output-118-gbits-throughput-displayed"></a>輸出：顯示 11.8 Gbit/s 輸送量

`Starting 4 processes`  
`Jobs: 4 (f=4): [R(4)][40.0%][r=1313MiB/s,w=0KiB/s][r=21.0k,w=0 IOPS][eta 00m:09s]`

### <a name="fio-64k-block-size-100-sequential-writes"></a>FIO：64k 塊大小 100% 順序寫入

`fio --name=64kseqwrites --rw=write --direct=1 --ioengine=libaio --bs=64k --numjobs=4 --iodepth=128  --size=4G --runtime=600 --group_reporting`

### <a name="output-122-gbits-throughput-displayed"></a>輸出：顯示 12.2 Gbit/s 輸送量

`Starting 4 processes`  
`Jobs: 4 (f=4): [W(4)][85.7%][r=0KiB/s,w=1356MiB/s][r=0,w=21.7k IOPS][eta 00m:02s]`

## <a name="volume-metrics"></a>卷指標

Azure NetApp 檔效能資料可通過 Azure 監視器計數器獲得。 計數器可通過 Azure 門戶和 REST API GET 請求提供。 

您可以查看歷史資料以獲取有關以下資訊：
* 讀取延遲的平均值 
* 寫入延遲的平均值 
* 閱讀 IOPS（平均）
* 寫入 IOPS（平均）
* 卷邏輯大小（平均值）
* 卷快照大小（平均值）

### <a name="using-azure-monitor"></a>使用 Azure 監視器 

您可以通過"指標"頁按量訪問 Azure NetApp 檔計數器，如下所示：

![Azure 監視器指標](../media/azure-netapp-files/azure-netapp-files-benchmark-monitor-metrics.png)

還可以通過訪問"指標"頁、篩選 NetApp 並指定感興趣的卷計數器，在 Azure 監視器監視器中為 Azure NetApp 檔創建儀表板： 

![Azure 監視器儀表板](../media/azure-netapp-files/azure-netapp-files-benchmark-monitor-dashboard.png)

### <a name="azure-monitor-api-access"></a>Azure 監視器 API 訪問

您可以使用 REST API 呼叫訪問 Azure NetApp 檔計數器。 請參閱 Azure 監視器支援指標：適用于容量池和卷的計數器的[Microsoft.NetApp/NetApp/NetApp 帳戶/容量池/卷](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftnetappnetappaccountscapacitypoolsvolumes)。

下面的示例顯示了用於查看邏輯卷大小的 GET URL：

`#get ANF volume usage`  
`curl -X GET -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts/ANFACCOUNTGOESHERE/capacityPools/ANFPOOLGOESHERE/Volumes/ANFVOLUMEGOESHERE/providers/microsoft.insights/metrics?api-version=2018-01-01&metricnames=VolumeLogicalSize`


## <a name="next-steps"></a>後續步驟

- [Azure NetApp Files 的服務等級](azure-netapp-files-service-levels.md)
- [適用於 Azure NetApp Files 的效能基準測試](azure-netapp-files-performance-benchmarks.md)