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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81008506"
---
Azure 共用磁片（預覽）是 Azure 受控磁片的新功能，可讓您同時將受控磁片連結至多部虛擬機器（Vm）。 將受控磁片連結至多個 Vm，可讓您將新的叢集應用程式部署至 Azure 或將其遷移至 Azure。

## <a name="how-it-works"></a>運作方式

叢集中的 Vm 可以根據使用[SCSI 持續保留](https://www.t10.org/members/w_spc3.htm)（scsi PR）的叢集應用程式所選擇的保留區，讀取或寫入您連接的磁片。 SCSI PR 是一種業界標準，可供在內部部署存放區域網路（SAN）上執行的應用程式使用。 在受控磁片上啟用 SCSI PR 可讓您依情況將這些應用程式遷移至 Azure。

共用受控磁片提供可從多個 Vm 存取的共用區塊存放裝置，這些儲存體會公開為邏輯單元編號（Lun）。 然後，Lun 會從目標（磁片）呈現給啟動器（VM）。 這些 Lun 看起來像是直接連接存放裝置（DAS）或 VM 的本機磁片磁碟機。

共用的受控磁片本身並不提供完全受控的檔案系統，可以使用 SMB/NFS 來存取。 您需要使用叢集管理員（例如 Windows Server 容錯移轉叢集（WSFC）或 Pacemaker）來處理叢集節點通訊，以及寫入鎖定。

## <a name="limitations"></a>限制

[!INCLUDE [virtual-machines-disks-shared-limitations](virtual-machines-disks-shared-limitations.md)]

## <a name="disk-sizes"></a>磁碟大小

[!INCLUDE [virtual-machines-disks-shared-sizes](virtual-machines-disks-shared-sizes.md)]

## <a name="sample-workloads"></a>範例工作負載

### <a name="windows"></a>Windows

大部分以 Windows 為基礎的叢集建置於 WSFC 上，可處理叢集節點通訊的所有核心基礎結構，讓您的應用程式能夠利用平行存取模式。 WSFC 會根據您的 Windows Server 版本，啟用 CSV 和非 CSV 的選項。 如需詳細資訊，請參閱[建立容錯移轉](https://docs.microsoft.com/windows-server/failover-clustering/create-failover-cluster)叢集。

WSFC 上執行的一些熱門應用程式包括：

- SQL Server 容錯移轉叢集實例（FCI）
- 向外延展檔案伺服器（SoFS）
- 一般用途的檔案伺服器（IW 工作負載）
- 遠端桌面伺服器使用者設定檔磁片（RDS UPD）
- SAP ASCS/SCS

### <a name="linux"></a>Linux

Linux 叢集可以利用叢集管理員，例如[Pacemaker](https://wiki.clusterlabs.org/wiki/Pacemaker)。 Pacemaker 是以[Corosync](http://corosync.github.io/corosync/)為基礎，可針對部署在高可用性環境中的應用程式啟用叢集通訊。 一些常見的叢集檔案系統包括[ocfs2](https://oss.oracle.com/projects/ocfs2/)和[gfs2](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/global_file_system_2/ch-overview-gfs2)。 您可以使用[fence_scsi](http://manpages.ubuntu.com/manpages/eoan/man8/fence_scsi.8.html)和[sg_persist](https://linux.die.net/man/8/sg_persist)等公用程式來操作保留和註冊。

## <a name="persistent-reservation-flow"></a>持續性保留流程

下圖說明一個範例2節點的叢集資料庫應用程式，它利用 SCSI PR 來啟用從一個節點容錯移轉到另一個節點。

![兩個節點的叢集。 在叢集上執行的應用程式正在處理磁片的存取](media/virtual-machines-disks-shared-disks/shared-disk-updated-two-node-cluster-diagram.png)

流程如下︰

1. 在 Azure VM1 和 VM2 上執行的叢集應用程式會註冊其意圖，以讀取或寫入磁片。
1. 然後，VM1 上的應用程式實例會取得獨佔保留以寫入磁片。
1. 系統會在您的 Azure 磁片上強制執行此保留，而資料庫現在可獨佔寫入磁片。 從 VM2 上的應用程式實例進行的任何寫入都不會成功。
1. 如果 VM1 上的應用程式實例停止運作，VM2 上的實例現在可以起始資料庫容錯移轉和磁片的接管。
1. 此保留專案現在已在 Azure 磁片上強制執行，且磁片將不再接受來自 VM1 的寫入。 它只會接受來自 VM2 的寫入。
1. 叢集應用程式可以完成資料庫容錯移轉，並提供來自 VM2 的要求。

下圖說明另一個常見的叢集工作負載，其中包含多個從磁片讀取資料以執行平行處理的節點，例如機器學習模型的定型。

![四個節點的 VM 叢集，每個節點會註冊意圖以進行寫入，應用程式會採用獨佔保留來適當地處理寫入結果](media/virtual-machines-disks-shared-disks/shared-disk-updated-machine-learning-trainer-model.png)

流程如下︰

1. 在所有 Vm 上執行的叢集應用程式會註冊要讀取或寫入磁片的意圖。
1. VM1 上的應用程式實例會在開啟其他 Vm 的磁片讀取時，取得獨佔保留以寫入磁片。
1. 系統會在您的 Azure 磁片上強制執行此保留。
1. 叢集中的所有節點現在都可以從磁片讀取。 只有一個節點會代表叢集中的所有節點，將結果寫回磁片。

### <a name="ultra-disks-reservation-flow"></a>Ultra 磁片保留流程

Ultra 磁片提供額外的節流，總共兩個節流。 因此，ultra 磁片保留流程可以如先前章節中所述的方式來執行，也可以更精確地調節和散發效能。

:::image type="content" source="media/virtual-machines-disks-shared-disks/ultra-reservation-table.png" alt-text=" ":::

## <a name="ultra-disk-performance-throttles"></a>Ultra 磁片效能節流

Ultra 磁片具有獨特的功能，可讓您藉由公開可修改的屬性來設定效能，並可讓您加以修改。 根據預設，只有兩個可修改的屬性，但共用的 ultra 磁片有兩個額外的屬性。


|屬性  |說明  |
|---------|---------|
|DiskIOPSReadWrite     |所有 Vm 上允許的 IOPS 總數，以寫入權限掛接共用磁片。         |
|DiskMBpsReadWrite     |所有 Vm 上允許的總輸送量（MB/s），以寫入權限掛接共用磁片。         |
|DiskIOPSReadOnly*     |將共用磁片掛接為 ReadOnly 的所有 Vm 允許的 IOPS 總數。         |
|DiskMBpsReadOnly*     |將共用磁片掛接為 ReadOnly 的所有 Vm 允許的總輸送量（MB/s）。         |

\*僅適用于共用的 ultra 磁片

下列公式說明如何設定效能屬性，因為它們是使用者可修改的：

- DiskIOPSReadWrite/DiskIOPSReadOnly: 
    - 300 IOPS/GiB 的 IOPS 限制，最多可達每個磁片160K 的 IOPS
    - 最少 100 IOPS
    - DiskIOPSReadWrite + DiskIOPSReadOnly 至少是 2 IOPS/GiB
- DiskMBpsRead Write/DiskMBpsReadOnly：
    - 針對每個布建的 IOPS，單一磁片的輸送量限制為 256 KiB/秒，每個磁片最多可達 2000 MBps
    - 針對每個布建的 IOPS，每個磁片的保證最小輸送量為 4KiB/s，整體基準最小值為 1 MBps

### <a name="examples"></a>範例

下列範例說明一些示範節流如何與共享的 ultra 磁片搭配使用的案例，具體而言就是。

#### <a name="two-nodes-cluster-using-cluster-shared-volumes"></a>使用叢集共用磁片區的兩個節點叢集

以下是使用叢集共用磁片區之2節點 WSFC 的範例。 使用此設定時，這兩個 Vm 會同時對磁片進行寫入存取，這會導致兩個 Vm 上的 ReadWrite 節流被分割，而未使用 ReadOnly 節流。

:::image type="complex" source="media/virtual-machines-disks-shared-disks/ultra-two-node-example.png" alt-text="CSV 二節點 ultra 範例":::

:::image-end:::

#### <a name="two-node-cluster-without-cluster-share-volumes"></a>沒有叢集共用磁片區的兩個節點叢集

以下是未使用叢集共用磁片區之2節點 WSFC 的範例。 使用此設定時，只有一個 VM 具有磁片的寫入存取權。 這會導致主要 VM 僅使用 ReadWrite 節流，而只有次要資料庫才使用 ReadOnly 節流。

:::image type="complex" source="media/virtual-machines-disks-shared-disks/ultra-two-node-no-csv.png" alt-text="CSV 兩個節點沒有 csv ultra 磁片範例":::

:::image-end:::

#### <a name="four-node-linux-cluster"></a>四個節點的 Linux 叢集

以下範例是具有單一寫入器和三個向外延展讀取器的4節點 Linux 叢集。 使用此設定時，只有一個 VM 具有磁片的寫入存取權。 這會導致主要 VM 專用的 ReadWrite 節流，以及由次要 Vm 分割的 ReadOnly 節流。

:::image type="complex" source="media/virtual-machines-disks-shared-disks/ultra-four-node-example.png" alt-text="四個節點 ultra 節流範例":::

:::image-end:::