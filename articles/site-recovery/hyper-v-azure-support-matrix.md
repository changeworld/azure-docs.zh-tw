---
title: 使用 Azure Site Recovery 支援 Hyper-v Vm 至 Azure 的嚴重損壞修復
description: 摘要說明使用 Azure Site recovery 將 Hyper-V VM 災害復原至 Azure 的支援元件和需求
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 7/14/2020
ms.author: raynew
ms.openlocfilehash: 268d8f3b43809e02476757cfe36b1ee52d4eaef1
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87317479"
---
# <a name="support-matrix-for-disaster-recovery-of-on-premises-hyper-v-vms-to-azure"></a>內部部署 Hyper-V VM 至 Azure 的災害復原支援矩陣

本文摘要說明將內部部署 Hyper-V VM 災害復原至 Azure (使用 [Azure Site Recovery](site-recovery-overview.md)) 所支援的元件和設定。

>[!NOTE]
> Site Recovery 不會在目的地區域中移動或儲存客戶資料，因為來源機器已設定嚴重損壞修復。 如果客戶選擇復原服務保存庫，則可以從不同的區域中選取。 復原服務保存庫包含中繼資料，但沒有實際的客戶資料。

## <a name="supported-scenarios"></a>支援的案例

**案例** | **詳細資料**
--- | ---
Hyper-V (含 Virtual Machine Manager) <br> <br>| 您可以針對在 System Center Virtual Machine Manager 網狀架構中管理的 Hyper-V 主機上執行之 VM 執行災害復原至 Azure。<br/><br/> 您可以在 Azure 入口網站中或使用 PowerShell 來部署此案例。<br/><br/> 當 Hyper-V 主機受 Virtual Machine Manager 管理時，您也可以執行災害復原至次要內部部署網站。 若要深入了解此案例，請閱讀[本教學課程](hyper-v-vmm-disaster-recovery.md)。
Hyper-V (不含 Virtual Machine Manager) | 您可以針對在不是由 Virtual Machine Manager 管理的 Hyper-V 主機上執行之 VM 執行災害復原至 Azure。<br/><br/> 您可以在 Azure 入口網站中或使用 PowerShell 來部署此案例。

## <a name="on-premises-servers"></a>內部部署伺服器

**Server** | **需求** | **詳細資料**
--- | --- | ---
Hyper-V (執行時不含 Virtual Machine Manager) |  Windows Server 2019、Windows Server 2016、Windows Server 2012 R2 （含最新的更新）（包括這些作業系統的 Server core 安裝） | 如果您已使用 Azure Site Recovery 設定 Windows Server 2012 R2 和/或 SCVMM 2012 R2，並且打算升級作業系統，請依照此[文件](upgrade-2012R2-to-2016.md)的說明進行。
Hyper-V (執行時含 Virtual Machine Manager) | Virtual Machine Manager 2019、Virtual Machine Manager 2016 Virtual Machine Manager 2012 R2 （包括這些作業系統的 server core 安裝，Virtual Machine Manager 2019 除外） | 如果使用 Virtual Machine Manager，則應該在 Virtual Machine Manager 2019 中管理 Windows Server 2019 主機。 同樣地，Windows Server 2016 主機也應該在 Virtual Machine Manager 2016 中進行管理。

> [!NOTE]
> 請確定內部部署伺服器上有 .NET Framework 4.6.2 或更高版本。

## <a name="replicated-vms"></a>複寫 VM


下表摘要說明 VM 支援。 Site Recovery 支援在受支援作業系統上執行的任何工作負載。

 **元件** | **詳細資料**
--- | ---
VM 設定 | 複寫到 Azure 的 VM 必須符合 [Azure 需求](#azure-vm-requirements)。
客體作業系統 | [Azure 支援的](../cloud-services/cloud-services-guestos-update-matrix.md#family-5-releases)任何客體作業系統。<br/><br/> 不支援 Windows Server 2016 Nano Server。


## <a name="vmdisk-management"></a>VM/磁碟管理

**動作** | **詳細資料**
--- | ---
在複寫的 Hyper-V VM 上調整磁碟大小 | 不支援。 停用複寫、進行變更，然後重新啟用 VM 的複寫。
在複寫的 Hyper-V VM 上新增磁碟 | 不支援。 停用複寫、進行變更，然後重新啟用 VM 的複寫。

## <a name="hyper-v-network-configuration"></a>Hyper-V 網路組態

**元件** | **具有 Virtual Machine Manager 的 hyper-v** | **不 Virtual Machine Manager 的 hyper-v**
--- | --- | ---
主機網路：NIC 小組 | 是 | 是
主機網路：VLAN | 是 | 是
主機網路：IPv4 | 是 | 是
主機網路：IPv6 | 否 | 否
客體 VM 網路：NIC 小組 | 否 | 否
客體 VM 網路：IPv4 | 是 | 是
客體 VM 網路：IPv6 | 否 | 是
客體 VM 網路：靜態 IP (Windows) | 是 | 是
客體 VM 網路：靜態 IP (Linux) | 否 | 否
客體 VM 網路：多重 NIC | 是 | 是
Https Proxy | 否 | 否
Site Recovery 服務的私用連結存取 | 是。 [深入了解](hybrid-how-to-enable-replication-private-endpoints.md)。 | 是。 [深入了解](hybrid-how-to-enable-replication-private-endpoints.md)。




## <a name="azure-vm-network-configuration-after-failover"></a>Azure VM 網路組態 (容錯移轉後)

**元件** | **具有 Virtual Machine Manager 的 hyper-v** | **不 Virtual Machine Manager 的 hyper-v**
--- | --- | ---
Azure ExpressRoute | 是 | 是
ILB | 是 | 是
ELB | 是 | 是
Azure 流量管理員 | 是 | 是
多個 NIC | 是 | 是
保留的 IP | 是 | 是
IPv4 | 是 | 是
保留來源 IP 位址 | 是 | 是
Azure 虛擬網路服務端點<br/> (不含 Azure 儲存體防火牆) | 是 | 是
加速網路 | 否 | 否


## <a name="hyper-v-host-storage"></a>Hyper-V 主機儲存體

**Storage** | **具有 Virtual Machine Manager 的 hyper-v** | **不 Virtual Machine Manager 的 hyper-v**
--- | --- | --- 
NFS | NA | NA
SMB 3.0 | 是 | 是
SAN (ISCSI) | 是 | 是
多重路徑 (MPIO)。 測試工具：<br></br> Microsoft DSM，EMC PowerPath 5.7 SP4，EMC PowerPath DSM for CLARiiON | 是 | 是

## <a name="hyper-v-vm-guest-storage"></a>Hyper-V VM 客體儲存體

**Storage** | **具有 Virtual Machine Manager 的 hyper-v** | **不 Virtual Machine Manager 的 hyper-v**
--- | --- | ---
VMDK | NA | NA
VHD/VHDX | 是 | 是
第 2 代 VM | 是 | 是
EFI/UEFI<br></br>Azure 中已遷移的 VM 會自動轉換為 BIOS 開機 VM。 VM 應僅執行 Windows Server 2012 和更新版本。 OS 磁片最多隻能有五個磁碟分割或更少，且 OS 磁片的大小應小於 300 GB。| 是 | 是
共用叢集磁碟 | 否 | 否
已加密磁碟 | 否 | 否
NFS | NA | NA
SMB 3.0 | 否 | 否
RDM | NA | NA
磁碟 > 1 TB | 是，最多 4,095 GB | 是，最多 4,095 GB
磁碟：4k 邏輯與實體磁區 | 不支援：第 1 代/第 2 代 | 不支援：第 1 代/第 2 代
磁片：4K 邏輯與 512-位元組實體磁區 | 是 |  是
邏輯磁碟區管理 (LVM)。 資料磁碟上才支援 LVM。 Azure 只提供單一 OS 磁碟。 | 是 | 是
使用等量磁碟的磁碟區 > 1 TB | 是 | 是
儲存空間 | 否 | 否
熱新增/移除磁碟 | 否 | 否
排除磁碟 | 是 | 是
多重路徑 (MPIO) | 是 | 是

## <a name="azure-storage"></a>Azure 儲存體

**元件** | **具有 Virtual Machine Manager 的 hyper-v** | **不 Virtual Machine Manager 的 hyper-v**
--- | --- | ---
本地備援儲存體 | 是 | 是
異地備援儲存體 | 是 | 是
讀取權限異地備援儲存體 | 是 | 是
非經常性儲存體 | 否 | 否
經常性存取儲存體| 否 | 否
區塊 Blob | 否 | 否
待用加密 (SSE)| 是 | 是
待用加密 (CMK) <br></br> （僅適用于容錯移轉至受控磁片）| 是（透過 PowerShell Az 3.3.0 module 開始） | 是（透過 PowerShell Az 3.3.0 module 開始）
靜態加密 <br></br> （僅適用于容錯移轉至受控磁片） <br></br> 深入瞭解[Windows](../virtual-machines/windows/disk-encryption.md)和[Linux](../virtual-machines/linux/disk-encryption.md)支援的區域 | 是（透過 PowerShell Az 3.3.0 module 開始） | 是（透過 PowerShell Az 3.3.0 module 開始）
進階儲存體 | 是 | 是
標準儲存體 | 是 | 是
匯入/匯出服務 | 否 | 否
已啟用防火牆的 Azure 儲存體帳戶 | 是。 適用于目標儲存體和快取。 | 是。 適用于目標儲存體和快取。
修改儲存體帳戶 | 不可以。 啟用複寫之後，即無法修改目標 Azure 儲存體帳戶。 修改、停用再重新啟用嚴重損壞修復。 | 否
安全傳輸選項 | 是


## <a name="azure-compute-features"></a>Azure 計算功能

**功能** | **具有 Virtual Machine Manager 的 hyper-v** | **不 Virtual Machine Manager 的 hyper-v**
--- | --- | ---
可用性設定組 | 是 | 是
中樞 | 是 | 是  
受控磁碟 | 是，適用於容錯移轉。<br/><br/> 不支援受控磁碟的容錯回復。 | 是，適用於容錯移轉。<br/><br/> 不支援受控磁碟的容錯回復。

## <a name="azure-vm-requirements"></a>Azure VM 需求

您複寫到 Azure 的內部部署 VM 必須符合此表中摘要說明的 VM 需求。

**元件** | **需求** | **詳細資料**
--- | --- | ---
客體作業系統 | Site Recovery 支援[Azure 支援](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc794868(v=ws.10))的所有作業系統。  | 若不支援，則必要條件檢查會失敗。
客體作業系統架構 | 32位（Windows Server 2008）/64-bit | 若不支援，則必要條件檢查會失敗。
作業系統磁碟大小 | 第 1 代 VM 高達 2,048 G。<br/><br/> 第 2 代 VM 高達 300 GB。  | 若不支援，則必要條件檢查會失敗。
作業系統磁碟計數 | 1 | 若不支援，則必要條件檢查會失敗。
資料磁碟計數 | 16 或以下  | 若不支援，則必要條件檢查會失敗。
資料磁碟 VHD 大小 | 最多 4,095 GB | 若不支援，則必要條件檢查會失敗。
網路介面卡 | 支援多個介面卡 |
共用 VHD | 不支援 | 若不支援，則必要條件檢查會失敗。
FC 磁碟 | 不支援 | 若不支援，則必要條件檢查會失敗。
硬碟格式 | VHD  <br/><br/>  VHDX | Site Recovery 會在您容錯移轉至 Azure 時，自動將 VHDX 轉換為 VHD。 當您容錯回復到內部部署時，虛擬機器仍會繼續使用 VHDX 格式。
BitLocker | 不支援 | 為 VM 啟用複寫之前必須先停用 BitLocker。
VM 名稱 | 介於 1 到 63 個字元。 只能使用字母、數字和連字號。 VM 名稱必須以字母或數字為開頭或結尾。 | 更新 Site Recovery 中 VM 屬性的值。
VM 類型 | 第 1 代<br/><br/> 第 2 代--Windows | OS 磁碟基本類型的第 2 代 VM (其中包含一或兩個格式化為 VHDX 的資料磁碟區) 且支援小於 300 GB 的磁碟空間。<br></br>不支援 Linux 第 2 代 VM。 [深入了解](https://azure.microsoft.com/blog/2015/04/28/disaster-recovery-to-azure-enhanced-and-were-listening/)。|

## <a name="recovery-services-vault-actions"></a>復原服務保存庫動作

**動作** |  **Hyper-V (含 VMM)** | **不具 VMM 的 Hyper-V**
--- | --- | ---
在資源群組間移動保存庫<br/><br/> 內及跨訂用帳戶 | 否 | 否
跨資源群組間移動儲存體、網路、Azure VM<br/><br/> 內及跨訂用帳戶 | 否 | 否

> [!NOTE]
> 將虛擬機器從內部部署複寫至 Azure 時，您只能從一個特定環境複寫到一個 AD 租使用者-Hyper-v 網站或適用于 VMM 的 Hyper-v。


## <a name="provider-and-agent"></a>Provider 和代理程式

若要確定您的部署與本文中的設定相容，請確定您所執行的是最新的提供者和代理程式版本。

**名稱** | **說明** | **詳細資料**
--- | --- | --- 
Azure Site Recovery 提供者 | 協調內部部署伺服器與 Azure 之間的通訊 <br/><br/> HYPER-V (含 Virtual Machine Manager)：安裝在 Virtual Machine Manager 伺服器上<br/><br/> HYPER-V (不含 Virtual Machine Manager)：安裝在 HYPER-V 主機上| 最新版本：5.1.2700.1 (可從 Azure 入口網站取得)<br/><br/> [最新功能和修正](https://support.microsoft.com/help/4091311/update-rollup-23-for-azure-site-recovery)
Microsoft Azure 復原服務代理程式 | 協調 HYPER-V VM 與 Azure 之間的複寫<br/><br/> 安裝在內部部署 Hyper-V 伺服器上 (無論是否有 Virtual Machine Manager) | 最新的代理程式可從入口網站取得






## <a name="next-steps"></a>後續步驟
了解如何[準備 Azure](tutorial-prepare-azure.md) 的內部部署 Hyper-V VM 災害復原。
