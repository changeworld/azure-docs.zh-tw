---
title: 支援 Azure 遷移中的物理伺服器遷移
description: 瞭解對 Azure 遷移中物理伺服器遷移的支援。
ms.topic: conceptual
ms.date: 01/07/2020
ms.openlocfilehash: e55cf6dddbc8dafd33b444e4a0dbe378d807aea1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79269545"
---
# <a name="support-matrix-for-physical-server-migration"></a>支援物理伺服器遷移矩陣

本文總結了使用[Azure 遷移：伺服器遷移](migrate-services-overview.md#azure-migrate-server-migration-tool)遷移物理伺服器的支援設置和限制。 如果要查找有關評估物理伺服器以遷移到 Azure 的資訊，請查看[評估支援矩陣](migrate-support-matrix-physical.md)。


## <a name="overview"></a>總覽

您可以使用基於代理的複製將本地電腦遷移到物理伺服器。 利用此工具，您可將各種不同的機器遷移至 Azure：

- 本地物理伺服器。
- 由 Xen、KVM 等平臺虛擬化的 VM。
- 如果由於某種原因不想使用標準的[Hyper-V](tutorial-migrate-hyper-v.md)或[VMware](server-migrate-overview.md)流，則 Hyper-VM 或 VMware VM。
- 在私有雲中運行的 VM。
- 在公共雲中運行的 VM，如亞馬遜 Web 服務 （AWS） 或 Google 雲平臺 （GCP）。


## <a name="migration-limitations"></a>移轉限制

一次最多可以選擇 10 台電腦進行複製。 如果要遷移更多電腦，則以 10 的組進行複製。


## <a name="physical-server-requirements"></a>實體伺服器需求

該表總結了對要使用基於代理的遷移遷移的物理伺服器的支援。

**支援** | **詳細資料**
--- | ---
**機器工作負載** | Azure 遷移支援在受支援的電腦上運行的任何工作負荷（如活動目錄、SQL 伺服器等）的遷移。
**作業系統** | 有關最新資訊，請查看作業系統對網站恢復[的支援](../site-recovery/vmware-physical-azure-support-matrix.md#replicated-machines)。 Azure 遷移提供相同的作業系統支援。
**Linux 檔案系統/來賓存儲** | 有關最新資訊，請查看[Linux 檔案系統](../site-recovery/vmware-physical-azure-support-matrix.md#linux-file-systemsguest-storage)對網站恢復的支援。 Azure 遷移提供相同的 Linux 檔案系統支援。
**網路/存儲** | 有關最新[資訊，請查看](../site-recovery/vmware-physical-azure-support-matrix.md#network)網站恢復的網路和[存儲](../site-recovery/vmware-physical-azure-support-matrix.md#storage)先決條件。 Azure 遷移提供相同的網路/存儲要求。
**Azure 需求** | 有關最新資訊，請查看網站恢復的[Azure 網路](../site-recovery/vmware-physical-azure-support-matrix.md#azure-vm-network-after-failover)、[存儲](../site-recovery/vmware-physical-azure-support-matrix.md#azure-storage)和[計算](../site-recovery/vmware-physical-azure-support-matrix.md#azure-compute)要求。 Azure 遷移對物理伺服器遷移具有相同的要求。
**行動服務** | 移動服務代理必須安裝在要遷移的每台電腦上。
**UEFI 啟動** | Azure 中的遷移電腦將自動轉換為 BIOS 引導 Azure VM。<br/><br/> 作業系統磁片應最多具有四個分區，並且卷應使用 NTFS 進行格式化。
**目標磁片** | 電腦只能遷移到 Azure 中的託管磁片（標準硬碟、高級 SSD）。
**磁碟大小** | 2 TB OS 磁片;8 TB 的資料磁片。
**磁片限制** |  每台電腦最多 63 個磁片。
**加密磁片/卷** |  不支援具有加密磁片/卷的電腦進行遷移。
**共用磁碟叢集** | 不支援。
**獨立磁片** | 支援。
**直通磁片** | 支援。
**NFS** | 在電腦上裝載的卷的 NFS 卷不會複製。
**iSCSI 目標** | 不支援具有 iSCSI 目標的電腦進行無代理遷移。
**多路徑 IO** | 不支援。
**存儲 vMotion** | 支援
**已組 NIC** | 不支援。
**IPv6** | 不支援。



## <a name="replication-appliance-requirements"></a>複寫設備需求

如果在物理伺服器上手動設置複製設備，請確保它符合表中概述的要求。 使用 Azure 遷移中心提供的 OVA 範本將 Azure 遷移複製設備設置為 VMware VM 時，該設備將使用 Windows Server 2016 進行設置，並符合支援要求。 

- 瞭解[複製設備要求](migrate-replication-appliance.md#appliance-requirements)。
- 必須在設備上安裝 MySQL。 瞭解[安裝選項](migrate-replication-appliance.md#mysql-installation)。
- 瞭解複製設備需要訪問的[URL。](migrate-replication-appliance.md#url-access)

## <a name="azure-vm-requirements"></a>Azure VM 需求

複製到 Azure 的所有本地 VM 必須滿足此表中總結的 Azure VM 要求。 當網站恢復運行複製的先決條件檢查時，如果某些要求未得到滿足，檢查將失敗。

**元件** | **需求** | **詳細資料**
--- | --- | ---
客體作業系統 | 驗證支援的作業系統。<br/> 您可以遷移在受支援的作業系統上運行的任何工作負載。 | 若不支援，則檢查會失敗。
客體作業系統架構 | 64 位元。 | 若不支援，則檢查會失敗。
作業系統磁碟大小 | 最多 2,048 GB。 | 若不支援，則檢查會失敗。
作業系統磁碟計數 | 1 | 若不支援，則檢查會失敗。
資料磁碟計數 | 64 或以下。 | 若不支援，則檢查會失敗。
資料磁碟大小 | 最多 4,095 GB | 若不支援，則檢查會失敗。
網路介面卡 | 支援多個介面卡。 |
共用 VHD | 不支援。 | 若不支援，則檢查會失敗。
FC 磁碟 | 不支援。 | 若不支援，則檢查會失敗。
BitLocker | 不支援。 | 為電腦啟用複寫之前必須先停用 BitLocker。
VM 名稱 | 從 1 到 63 個字元。<br/> 只能使用字母、數字和連字號。<br/><br/> 電腦名稱必須以字母或數字為開頭或結尾。 |  更新 Site Recovery 中電腦屬性的值。
遷移後連接 - Windows | 要在遷移後連接到運行 Windows 的 Azure VM，請執行：<br/> - 在遷移在本地 VM 上啟用 RDP 之前。 確定已針對 [公用]**** 設定檔新增 TCP 和 UDP 規則，且在 [Windows 防火牆]**** > [允許的應用程式]**** 中已針對所有設定檔允許 RDP。<br/> 對於網站到網站 VPN 訪問，請在**Windows 防火牆** -> **允許的應用和功能**中啟用 RDP。 **Domain and Private** 此外，請檢查作業系統的 SAN 策略是否設置為**OnlineAll**。 [深入了解](prepare-for-migration.md)。 |
遷移後連接 -Linux | 要使用 SSH 在遷移後連接到 Azure VM，<br/> 在遷移之前，在本地電腦上，檢查安全外殼服務是否設置為"開始"，並且防火牆規則是否允許 SSH 連接。<br/> 容錯移轉後，在 Azure VM 上，允許傳入連接到 SSH 埠，用於通過 VM 故障的網路安全性群組規則以及連接到它的 Azure 子網。 此外，為 VM 添加公共 IP 位址。 |  


## <a name="next-steps"></a>後續步驟

[遷移](tutorial-migrate-physical-virtual-machines.md)物理伺服器。
