---
title: Azure Migrate 中的實體伺服器遷移支援
description: 瞭解 Azure Migrate 中的實體伺服器遷移支援。
ms.topic: conceptual
ms.date: 01/07/2020
ms.openlocfilehash: e55cf6dddbc8dafd33b444e4a0dbe378d807aea1
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/13/2020
ms.locfileid: "79269545"
---
# <a name="support-matrix-for-physical-server-migration"></a>實體伺服器遷移的支援矩陣

本文摘要說明使用[Azure Migrate：伺服器遷移](migrate-services-overview.md#azure-migrate-server-migration-tool)來遷移實體伺服器的支援設定和限制。 如果您要尋找評估實體伺服器以遷移至 Azure 的相關資訊，請參閱[評估支援對照表](migrate-support-matrix-physical.md)。


## <a name="overview"></a>概觀

您可以使用以代理程式為基礎的複寫，將內部部署機器遷移為實體伺服器。 利用此工具，您可將各種不同的機器遷移至 Azure：

- 內部部署實體伺服器。
- 虛擬化的 Vm，例如 Xen、KVM 等平臺。
- Hyper-v Vm 或 VMware Vm，如果基於某些原因，您不想使用標準[hyper-v](tutorial-migrate-hyper-v.md)或[vmware](server-migrate-overview.md)流程。
- 在私人雲端中執行的 Vm。
- 在公用雲端中執行的 Vm，例如 Amazon Web Services （AWS）或 Google Cloud Platform （GCP）。


## <a name="migration-limitations"></a>遷移限制

您一次最多可以選取10部機器進行複寫。 如果您想要遷移更多電腦，請以10個群組進行複寫。


## <a name="physical-server-requirements"></a>實體伺服器需求

此表格摘要說明您想要使用代理程式型遷移來遷移的實體伺服器支援。

**支援** | **詳細資料**
--- | ---
**機器工作負載** | Azure Migrate 支援在支援的機器上，遷移執行的任何工作負載（例如 Active Directory、SQL server 等）。
**作業系統** | 如需最新資訊，請參閱 Site Recovery 的[作業系統支援](../site-recovery/vmware-physical-azure-support-matrix.md#replicated-machines)。 Azure Migrate 提供相同的作業系統支援。
**Linux 檔案系統/來賓儲存體** | 如需最新資訊，請參閱[Linux 檔案系統](../site-recovery/vmware-physical-azure-support-matrix.md#linux-file-systemsguest-storage)的 Site Recovery 支援。 Azure Migrate 提供相同的 Linux 檔案系統支援。
**網路/儲存體** | 如需最新資訊，請參閱 Site Recovery 的[網路](../site-recovery/vmware-physical-azure-support-matrix.md#network)和[儲存體](../site-recovery/vmware-physical-azure-support-matrix.md#storage)必要條件。 Azure Migrate 提供相同的網路/儲存需求。
**Azure 需求** | 如需最新資訊，請參閱 Site Recovery 的[Azure 網路](../site-recovery/vmware-physical-azure-support-matrix.md#azure-vm-network-after-failover)、[儲存體](../site-recovery/vmware-physical-azure-support-matrix.md#azure-storage)和[計算](../site-recovery/vmware-physical-azure-support-matrix.md#azure-compute)需求。 Azure Migrate 對於實體伺服器遷移具有相同的需求。
**行動服務** | 行動服務代理程式必須安裝在您想要遷移的每部機器上。
**UEFI 開機** | Azure 中已遷移的機器會自動轉換為 BIOS 開機 Azure VM。<br/><br/> OS 磁片應該有最多四個磁碟分割，而且磁片區應該使用 NTFS 格式化。
**目標磁片** | 機器只能遷移至 Azure 中的受控磁片（標準 HDD、premium SSD）。
**磁碟大小** | 2 TB 的 OS 磁片;適用于資料磁片的 8 TB。
**磁片限制** |  每部機器最多63個磁片。
**加密的磁片/磁片區** |  使用加密磁片/磁片區的電腦不支援進行遷移。
**共用磁碟叢集** | 不支援。
**獨立磁片** | 支援。
**傳遞磁片** | 支援。
**NFS** | 裝載為電腦上磁片區的 NFS 磁片區不會複寫。
**iSCSI 目標** | 無代理程式遷移不支援具有 iSCSI 目標的機器。
**多重路徑 IO** | 不支援。
**儲存體 vMotion** | 支援
**組合的 Nic** | 不支援。
**Ipv4** | 不支援。



## <a name="replication-appliance-requirements"></a>複寫設備需求

如果您在實體伺服器上手動設定複寫設備，請確定它符合資料表中摘要說明的需求。 當您使用 Azure Migrate 中樞提供的 OVA 範本，將 Azure Migrate 複寫設備設定為 VMware VM 時，會使用 Windows Server 2016 設定設備，並符合支援需求。 

- 瞭解複寫[設備需求](migrate-replication-appliance.md#appliance-requirements)。
- MySQL 必須安裝在設備上。 瞭解[安裝選項](migrate-replication-appliance.md#mysql-installation)。
- 瞭解複寫設備需要存取的[url](migrate-replication-appliance.md#url-access) 。

## <a name="azure-vm-requirements"></a>Azure VM 需求

複寫至 Azure 的所有內部部署 Vm 都必須符合此表中摘要說明的 Azure VM 需求。 當 Site Recovery 執行複寫的必要條件檢查時，如果不符合某些需求，此檢查將會失敗。

**元件** | **需求** | **詳細資料**
--- | --- | ---
客體作業系統 | 驗證支援的作業系統。<br/> 您可以遷移在支援的作業系統上執行的任何工作負載。 | 若不支援，則檢查會失敗。
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
在遷移後連接-Windows | 若要在遷移後連線至執行 Windows 的 Azure Vm：<br/> -在遷移之前，在內部部署 VM 上啟用 RDP。 確定已針對 [公用] 設定檔新增 TCP 和 UDP 規則，且在 [Windows 防火牆] > [允許的應用程式] 中已針對所有設定檔允許 RDP。<br/> 針對站對站 VPN 存取，啟用 RDP 並允許**Windows 防火牆**中的 Rdp -> **允許的應用程式和功能**用於**網域和專用**網。 此外，請檢查作業系統的 SAN 原則是否設定為**OnlineAll**。 [詳細資訊](prepare-for-migration.md)。 |
在遷移後連接-Linux | 若要在使用 SSH 進行遷移之後連線到 Azure Vm：<br/> 在進行遷移之前，請在內部部署機器上，確認安全殼層服務已設定為 [啟動]，且防火牆規則允許 SSH 連線。<br/> 容錯移轉之後，在 Azure VM 上，允許已容錯移轉的 VM 上的網路安全性群組規則之 SSH 埠的連入連線，以及它所連接的 Azure 子網。 此外，請新增 VM 的公用 IP 位址。 |  


## <a name="next-steps"></a>後續步驟

[遷移](tutorial-migrate-physical-virtual-machines.md)實體伺服器。
