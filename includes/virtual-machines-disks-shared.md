---
title: 包含檔案
description: 包含檔案
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 02/13/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 09e5a6c5eee21e5432c4cf96a63fd2337307954a
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/14/2020
ms.locfileid: "77211431"
---
Azure 共用磁片（預覽）是 Azure 受控磁片的新功能，可同時將 Azure 受控磁片連接到多部虛擬機器（Vm）。 將受控磁片連結至多個 Vm，可讓您將新的叢集應用程式部署至 Azure 或將其遷移至 Azure。

## <a name="how-it-works"></a>運作方式

叢集中的 Vm 可以根據使用[SCSI 持續保留](https://www.t10.org/members/w_spc3.htm)（scsi PR）的叢集應用程式所選擇的保留區，讀取或寫入您連接的磁片。 SCSI PR 是知名的業界標準，可供在內部部署存放區域網路（SAN）上執行的應用程式使用。 在受控磁片上啟用 SCSI PR 可讓您依情況將這些應用程式遷移至 Azure。

啟用共用磁片的受控磁片提供可由多個 Vm 存取的共用區塊存放裝置，這會公開為邏輯單元編號（Lun）。 然後，Lun 會從目標（磁片）呈現給啟動器（VM）。 這些 Lun 看起來像是直接連接存放裝置（DAS）或 VM 的本機磁片磁碟機。

啟用共用磁片的受控磁片不會以原生方式提供可使用 SMB/NFS 存取的完全受控檔案系統。 您將需要使用叢集管理員（例如 Windows Server 容錯移轉叢集（WSFC）或 Pacemaker）來處理叢集節點通訊，以及寫入鎖定。

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