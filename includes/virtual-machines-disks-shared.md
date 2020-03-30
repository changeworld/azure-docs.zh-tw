---
title: 包含檔案
description: 包含檔案
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 02/18/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: a14ae76e15c1adb59917e61fbcbdaa34a7efa2d8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77472006"
---
Azure 共用磁片（預覽）是 Azure 託管磁片的新功能，它允許同時將 Azure 託管磁片附加到多個虛擬機器 （VM）。 通過將託管磁片附加到多個 VM，可以部署新的群集應用程式或將現有群集應用程式遷移到 Azure。

## <a name="how-it-works"></a>運作方式

群集中的 VM 可以根據群集應用程式使用[SCSI 持久保留 （SCSI](https://www.t10.org/members/w_spc3.htm) PR） 選擇的保留讀取或寫入您的連接磁片。 SCSI PR 是一個眾所周知的行業標準，由本地存放區域網路 （SAN） 上運行的應用程式利用。 在託管磁片上啟用 SCSI PR 允許您將這些應用程式遷移到 Azure。

啟用共用磁片的託管磁片提供可由多個 VM 訪問的共用塊存儲，這公開為邏輯單元號 （LUN）。 然後，LUN 從目標（磁片）呈現給啟動器 （VM）。 這些 LUN 看起來像直接連接存儲 （DAS） 或 VM 的本地磁碟機。

啟用了共用磁片的託管磁片在本機上不會提供可以使用 SMB/NFS 訪問的完全託管檔案系統。 您需要使用群集管理器，如 Windows 伺服器容錯移轉叢集 （WSFC） 或起搏器，該群集處理叢集節點通信和寫入鎖定。

## <a name="limitations"></a>限制

[!INCLUDE [virtual-machines-disks-shared-limitations](virtual-machines-disks-shared-limitations.md)]

## <a name="disk-sizes"></a>磁碟大小

[!INCLUDE [virtual-machines-disks-shared-sizes](virtual-machines-disks-shared-sizes.md)]

## <a name="sample-workloads"></a>示例工作負載

### <a name="windows"></a>Windows

大多數基於 Windows 的群集都基於 WSFC 構建，WSFC 處理叢集節點通信的所有核心基礎結構，允許應用程式利用並行訪問模式。 WSFC 支援基於 CSV 和非基於 CSV 的選項，具體取決於您的 Windows 伺服器版本。 有關詳細資訊，請參閱[創建容錯移轉叢集](https://docs.microsoft.com/windows-server/failover-clustering/create-failover-cluster)。

在 WSFC 上運行的一些常用應用程式包括：

- SQL 伺服器容錯移轉叢集實例 （FCI）
- 橫向擴展檔案伺服器 （SoFS）
- 一般使用檔案伺服器（IW 工作負載）
- 遠端桌面伺服器使用者設定檔磁片 （RDS UPD）
- SAP ASCS/SCS

### <a name="linux"></a>Linux

Linux集群可以利用集群管理器，如[起搏器](https://wiki.clusterlabs.org/wiki/Pacemaker)。 起搏器以[Corosync](http://corosync.github.io/corosync/)為基礎，為部署在高可用環境中的應用程式提供集群通信。 一些常見的群集檔案系統包括[ocfs2](https://oss.oracle.com/projects/ocfs2/)和[gfs2](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/global_file_system_2/ch-overview-gfs2)。 您可以使用[fence_scsi](http://manpages.ubuntu.com/manpages/eoan/man8/fence_scsi.8.html)和[sg_persist](https://linux.die.net/man/8/sg_persist)等實用程式操作預訂和註冊。

## <a name="persistent-reservation-flow"></a>持久預留流

下圖演示了一個示例 2 節點群集資料庫應用程式，該應用程式利用 SCSI PR 啟用從一個節點到另一個節點的容錯移轉。

![兩個節點群集。 在群集上運行的應用程式正在處理對磁片的訪問](media/virtual-machines-disks-shared-disks/shared-disk-updated-two-node-cluster-diagram.png)

流程如下︰

1. 在 Azure VM1 和 VM2 上運行的群集應用程式註冊其讀取或寫入磁片的意圖。
1. 然後，VM1 上的應用程式實例需要獨佔保留才能寫入磁片。
1. 此保留在 Azure 磁片上強制執行，資料庫現在可以專門寫入磁片。 從 VM2 上的應用程式實例的任何寫入都不會成功。
1. 如果 VM1 上的應用程式實例出現故障，VM2 上的實例現在可以啟動資料庫容錯移轉並接管磁片。
1. 此保留現在在 Azure 磁片上強制執行，磁片將不再接受來自 VM1 的寫入。 它僅接受來自 VM2 的寫入。
1. 群集應用程式可以完成資料庫容錯移轉並處理來自 VM2 的請求。

下圖說明瞭另一個常見的群集工作負載，該工作負載由多個節點組成，這些節點從磁片讀取資料以運行並行進程，例如機器學習模型的培訓。

![四個節點 VM 群集，每個節點註冊寫入意向，應用程式需要獨佔保留才能正確處理寫入結果](media/virtual-machines-disks-shared-disks/shared-disk-updated-machine-learning-trainer-model.png)

流程如下︰

1. 在所有 VM 上運行的群集應用程式註冊讀取或寫入磁片的意圖。
1. VM1 上的應用程式實例需要一個獨佔保留來寫入磁片，同時打開從其他 VM 讀取磁片。
1. 此保留在 Azure 磁片上強制執行。
1. 群集中的所有節點現在可以從磁片讀取。 只有一個節點代表群集中的所有節點將結果寫回磁片。