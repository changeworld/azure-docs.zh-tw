---
title: 支援矩陣-Hyper-v 嚴重損壞修復至具有 Azure Site Recovery 的次要 VMM 網站
description: 摘要說明使用 Azure Site Recovery 將 VMM 雲端中的 Hyper-V VM 複寫至次要網站的支援。
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/06/2019
ms.author: raynew
ms.openlocfilehash: 1126a85ed22ee17879767a93ca75dc76dd04b747
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "74132956"
---
# <a name="support-matrix-for-disaster-recovery-of-hyper-v-vms-to-a-secondary-site"></a>將 Hyper-V 虛擬機器災害復原至次要網站的支援矩陣

本文摘要說明當您使用[Azure Site Recovery](site-recovery-overview.md)服務，將在 SYSTEM CENTER VIRTUAL MACHINE MANAGER （VMM）雲端中管理的 hyper-v vm 複寫至次要網站時所支援的專案。 如果您需要將 Hyper-V VM 複寫至 Azure，請檢閱[此支援矩陣](hyper-v-azure-support-matrix.md)。

> [!NOTE]
> 當您的 Hyper-V 主機是在 VMM 雲端中進行管理時，您只能複寫至次要網站。


## <a name="host-servers"></a>主機伺服器

**作業系統** | **詳細資料**
--- | ---
Windows Server 2012 R2 | 伺服器必須執行最新的更新。
Windows Server 2016 |  目前不支援混用 Windows Server 2016 和 2012 R2 主機的 VMM 2016 雲端。<br/><br/> 目前不支援從 System Center 2012 R2 VMM 2012 R2 升級至 System Center 2016 的部署。


## <a name="replicated-vm-support"></a>已複寫的 VM 支援

下表摘要說明使用 Site Recovery 複寫的機器支援的作業系統。 任何工作負載都可以在支援的作業系統上執行。

**Windows 版本** | **Hyper-v （含 VMM）**
--- | ---
Windows Server 2016 | Windows Server 2016 上 [Hyper-V 所支援的](https://docs.microsoft.com/windows-server/virtualization/hyper-v/Supported-Windows-guest-operating-systems-for-Hyper-V-on-Windows)任何一種客體作業系統 
Windows Server 2012 R2 | Windows Server 2012 R2 上 [Hyper-V 所支援的](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn792027%28v%3dws.11%29)任何一種客體作業系統

## <a name="linux-machine-storage"></a>Linux 機器儲存體

只能複寫有下列儲存體的 Linux 機器：

- 檔案系統 (EXT3、ETX4、ReiserFS、XFS)。
- 多重路徑軟體裝置對應程式。
- 磁碟區管理員 (LVM2)。
- 不支援使用 HP CCISS 控制站儲存體的實體伺服器。
- 只有在 SUSE Linux Enterprise Server 11 SP3 上才支援 ReiserFS 檔案系統。

## <a name="network-configuration---hostguest-vm"></a>網路設定 - 主機/客體 VM

**Configuration** | **支援**  
--- | --- 
主機 - NIC 小組 | Yes 
主機 - VLAN | Yes 
主機 - IPv4 | Yes 
主機 - IPv6 | No 
客體 VM - NIC 小組 | No
客體 VM - IPv4 | Yes
客體 VM - IPv6 | No
客體 VM - Windows/Linux - 靜態 IP 位址 | Yes
客體 VM - 多重 NIC | 是


## <a name="storage"></a>儲存體

### <a name="host-storage"></a>主機儲存體

**儲存體 (主機)** | **支援**
--- | --- 
NFS | N/A
SMB 3.0 |  是
SAN (ISCSI) | Yes
多重路徑 (MPIO) | Yes

### <a name="guest-or-physical-server-storage"></a>客體或實體伺服器儲存體

**Configuration** | **支援**
--- | --- | 
VMDK |  N/A
VHD/VHDX | 是 (最多 16 個磁碟)
第 2 代 VM | Yes
共用叢集磁碟 | No
已加密磁碟 | No
UEFI| N/A
NFS | No
SMB 3.0 | No
RDM | N/A
磁碟 > 1 TB | Yes
使用等量磁碟的磁碟區 > 1 TB<br/><br/> LVM | Yes
儲存空間 | Yes
熱新增/移除磁碟 | No
排除磁碟 | Yes
多重路徑 (MPIO) | Yes

## <a name="vaults"></a>保存庫

**動作** | **支援**
--- | --- 
跨資源群組間移動保存庫 (在訂用帳戶之內或跨訂用帳戶) |  No
跨資源群組間移動儲存體、網路、Azure VM (在訂用帳戶之內或跨訂用帳戶) | No

## <a name="azure-site-recovery-provider"></a>Azure Site Recovery 提供者

提供者會協調 VMM 伺服器之間的通訊。 

**最新** | **更新**
--- | --- 
5.1.19 ([可從入口網站取得](https://aka.ms/downloaddra) | [最新功能和修正](https://support.microsoft.com/kb/3155002)



## <a name="next-steps"></a>後續步驟

[將 VMM 雲端中的 Hyper-V VM 複寫至次要網站](tutorial-vmm-to-vmm.md)

