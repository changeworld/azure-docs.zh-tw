---
title: 包含檔案
description: 包含檔案
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 04/08/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: c3e5beaef7fcc9d407103834e2040957ff32984c
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/10/2020
ms.locfileid: "81008506"
---
Azure 共用磁碟(預覽)是 Azure 託管磁碟的新功能,它啟用同時將託管磁碟附加到多個虛擬機器 (VM)。 通過將託管磁碟附加到多個 VM,可以部署新的群集應用程式或將現有群集應用程式遷移到 Azure。

## <a name="how-it-works"></a>運作方式

群集中的 VM 可以根據群集應用程式使用[SCSI 持久保留 (SCSI](https://www.t10.org/members/w_spc3.htm) PR) 選擇的保留讀取或寫入您的連接磁碟。 SCSI PR 是一種產業標準,由本地存儲區域網路(SAN) 上運行的應用程式利用。 在託管磁碟上啟用 SCSI PR 允許您將這些應用程式遷移到 Azure。

共用託管磁碟提供可以從多個 VM 訪問的共用塊存儲,這些存儲公開為邏輯單元號 (LUN)。 然後,LUN 從目標(磁碟)呈現給啟動器 (VM)。 這些 LUN 看起來像直接連接儲存 (DAS) 或 VM 的本地驅動器。

共用託管磁碟不提供可以使用 SMB/NFS 訪問的完全託管檔案系統。 您需要使用群集管理器,如 Windows 伺服器故障轉移群集 (WSFC) 或起搏器,該群集處理群集節點通信和寫入鎖定。

## <a name="limitations"></a>限制

[!INCLUDE [virtual-machines-disks-shared-limitations](virtual-machines-disks-shared-limitations.md)]

## <a name="disk-sizes"></a>磁碟大小

[!INCLUDE [virtual-machines-disks-shared-sizes](virtual-machines-disks-shared-sizes.md)]

## <a name="sample-workloads"></a>範例工作負載

### <a name="windows"></a>Windows

大多數基於 Windows 的群集都基於 WSFC 構建,WSFC 處理群集節點通信的所有核心基礎結構,允許應用程式利用並行訪問模式。 WSFC 支援基於 CSV 和非基於 CSV 的選項,具體取決於您的 Windows 伺服器版本。 有關詳細資訊,請參閱[建立故障轉移群組](https://docs.microsoft.com/windows-server/failover-clustering/create-failover-cluster)。

在 WSFC 上執行的一些常用應用程式包括:

- SQL 伺服器故障轉移叢集實體 (FCI)
- 橫向延伸檔案伺服器 (SoFS)
- 一般使用檔案伺服器(IW 工作負載)
- 遠端桌面伺服器使用者設定檔磁碟 (RDS UPD)
- SAP ASCS/SCS

### <a name="linux"></a>Linux

Linux集群可以利用叢集群組管理員,如[起搏器](https://wiki.clusterlabs.org/wiki/Pacemaker)。 起搏器以[Corosync](http://corosync.github.io/corosync/)為基礎,為部署在高可用環境中的應用程式提供集群通信。 一些常見的叢集檔案系統包括[ocfs2](https://oss.oracle.com/projects/ocfs2/)與[gfs2](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/global_file_system_2/ch-overview-gfs2)。 您可以使用[fence_scsi](http://manpages.ubuntu.com/manpages/eoan/man8/fence_scsi.8.html)和[sg_persist](https://linux.die.net/man/8/sg_persist)等實用程式操作預訂和註冊。

## <a name="persistent-reservation-flow"></a>持久預留流程

下圖演示了一個範例2節點群集資料庫應用程式,該應用程式利用SCSI PR啟用從一個節點到另一個節點的故障轉移。

![兩個節點群集。 在叢集上執行的應用程式正在處理對磁碟的存取](media/virtual-machines-disks-shared-disks/shared-disk-updated-two-node-cluster-diagram.png)

流程如下︰

1. 在 Azure VM1 和 VM2 上運行的群集應用程式註冊其讀取或寫入磁碟的意圖。
1. 然後,VM1 上的應用程式實例需要獨佔保留才能寫入磁碟。
1. 此保留在 Azure 磁碟上強制執行,資料庫現在可以專門寫入磁碟。 從 VM2 上的應用程式實例的任何寫入都不會成功。
1. 如果 VM1 上的應用程式實例出現故障,VM2 上的實例現在可以啟動資料庫故障轉移並接管磁碟。
1. 此保留現在在 Azure 磁碟上強制執行,磁碟將不再接受來自 VM1 的寫入。 它僅接受來自 VM2 的寫入。
1. 叢集應用程式可以完成資料庫故障轉移並處理來自 VM2 的請求。

下圖說明了另一個常見的群集工作負載,該工作負載由多個節點組成,這些節點從磁碟讀取數據以運行並行進程,例如機器學習模型的培訓。

![四個節點 VM 群集,每個節點註冊寫入意向,應用程式需要獨佔保留才能正確處理寫入結果](media/virtual-machines-disks-shared-disks/shared-disk-updated-machine-learning-trainer-model.png)

流程如下︰

1. 在所有 VM 上運行的群集應用程式註冊讀取或寫入磁碟的意圖。
1. VM1 上的應用程式實例需要一個獨佔保留來寫入磁碟,同時打開從其他 VM 讀取磁碟。
1. 此保留在 Azure 磁碟上強制執行。
1. 群集中的所有節點現在可以從磁碟讀取。 只有一個節點代表群集中的所有節點將結果寫回磁碟。

### <a name="ultra-disks-reservation-flow"></a>超磁碟預留流

超盤提供額外的油門,總共兩個油門。 因此,超磁碟預留流可以如前面部分所述工作,也可以更精細地限制和分發性能。

:::image type="content" source="media/virtual-machines-disks-shared-disks/ultra-reservation-table.png" alt-text=" ":::

## <a name="ultra-disk-performance-throttles"></a>超磁碟效能限制

Ultra 磁碟具有獨特的功能,允許您透過公開可修改的屬性並允許您修改屬性來設定性能。 默認情況下,只有兩個可修改的屬性,但是,共用超磁碟有兩個附加屬性。


|屬性  |描述  |
|---------|---------|
|磁碟閱讀編寫     |使用寫入存取許可權安裝共用磁碟的所有 VM 中允許的 IOPS 總數。         |
|磁碟BpsReadWrite     |使用寫入存取許可權安裝共用磁碟的所有 VM 中允許的總輸送量 (MB/s)。         |
|只讀入磁碟閱讀*     |在所有將共用磁碟安裝為 ReadOnly 的 VM 中允許的 IOPS 總數。         |
|只磁碟BpsRead*     |在將共用磁碟安裝為 ReadOnly 的所有 VM 中允許的總輸送量 (MB/s)。         |

\*只適用於共享超級磁碟

以下公式解釋了如何設置性能屬性,因為它們是使用者可修改的:

- 磁碟閱讀寫入/磁碟操作唯讀: 
    - IOPS 限制為 300 IOPS/GiB,每個磁碟最多 160K IOPS
    - 最少 100 IOPS
    - 磁碟閱讀寫入 = 磁碟閱讀僅是至少 2 IOPS/GiB
- 磁碟Bps讀取寫入/磁碟BP只讀取:
    - 每個預配 IOPS 單磁碟的輸送量限制為 256 KiB/s,每個磁碟最多 2000 MBps
    - 每個預配 IOPS 的最小保證輸送量為 4KiB/s,總體基線最小為 1 MBps

### <a name="examples"></a>範例

以下示例描述了一些方案,這些場景顯示了限制如何與共用超級磁碟配合使用,具體操作。

#### <a name="two-nodes-cluster-using-cluster-shared-volumes"></a>使用叢集分享卷的兩個節點群集

下面是使用群集共用卷的 2 節點 WSFC 的範例。 使用此配置,兩個 VM 都同時具有對磁碟的寫入訪問許可權,從而導致在兩個 VM 上拆分 ReadWrite 限制和不使用 ReadOnly 限制。

:::image type="complex" source="media/virtual-machines-disks-shared-disks/ultra-two-node-example.png" alt-text="CSV 兩個節點超範例":::

:::image-end:::

#### <a name="two-node-cluster-without-cluster-share-volumes"></a>兩個節點群集,沒有群集共用卷

下面是不使用群集共用卷的 2 節點 WSFC 的範例。 使用此配置,只有一個 VM 具有對磁碟的寫入訪問許可權。 這將導致僅針對主 VM 使用 ReadWrite 油門,而僅由輔助 VM 使用的 ReadOnly 節流。

:::image type="complex" source="media/virtual-machines-disks-shared-disks/ultra-two-node-no-csv.png" alt-text="CSV 兩個節點無 csv 超磁碟範例":::

:::image-end:::

#### <a name="four-node-linux-cluster"></a>四個節點 Linux 叢集

下面是一個 4 節點 Linux 群集的範例,該群集具有單個編寫器和三個橫向擴展讀取器。 使用此配置,只有一個 VM 具有對磁碟的寫入訪問許可權。 這將導致僅針對主 VM 的 ReadWrite 節流,並且由輔助 VM 拆分的 ReadOnly 節流。

:::image type="complex" source="media/virtual-machines-disks-shared-disks/ultra-four-node-example.png" alt-text="四個節點超限制範例":::

:::image-end:::