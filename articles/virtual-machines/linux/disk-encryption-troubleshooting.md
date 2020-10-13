---
title: 針對 Linux Vm 的 Azure 磁碟加密進行疑難排解
description: 本文提供適用于 Linux Vm 的 Microsoft Azure 磁片加密的疑難排解秘訣。
author: msmbaldwin
ms.service: virtual-machines-linux
ms.subservice: security
ms.topic: troubleshooting
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: 0a1bdce0478961b27bde7dd32733d95c58ba2361
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/13/2020
ms.locfileid: "91972232"
---
# <a name="azure-disk-encryption-for-linux-vms-troubleshooting-guide"></a>Linux Vm 的 Azure 磁碟加密疑難排解指南

本指南適用於組織採用 Azure 磁碟加密的 IT 專業人員、資訊安全性分析師和雲端系統管理員。 本文旨在協助磁碟加密相關問題的疑難排解。

在採取以下任何步驟之前，請先確定想要加密的 VM 是[支援的 VM 大小與作業系統](disk-encryption-overview.md#supported-vms-and-operating-systems)，且已符合所有先決條件：

- [VM 的其他需求](disk-encryption-overview.md#supported-vms-and-operating-systems)
- [網路需求](disk-encryption-overview.md#networking-requirements)
- [加密金鑰儲存體需求](disk-encryption-overview.md#encryption-key-storage-requirements)

 

## <a name="troubleshooting-linux-os-disk-encryption"></a>針對 Linux OS 磁碟加密進行疑難排解

Linux 作業系統 (OS) 磁碟加密必須先將 OS 磁碟機取消掛接後，才能透過完整磁碟加密流程加以執行。 如果無法取消掛接磁碟機，則可能出現「後續無法取消掛接...」錯誤訊息。

如果 VM 上的 OS 磁片加密的環境已從支援的股票資源庫映射變更，就會發生此錯誤。 來自受支援映像的偏差可能會干擾 OS 磁碟機取消掛接擴充功能。 偏差的範例可能包括下列項目：
- 不再符合支援的檔案系統或資料分割配置的自訂映像。
- 在加密之前已於 OS 中安裝且執行諸如 SAP、MongoDB 或 Apache Cassandra 與 Docker 等大型應用程式時，則不支援這些應用程式。 在依要求準備 OS 磁碟機進行磁碟加密時，Azure 磁碟加密無法安全地關閉這些處理序。 如果仍然有作用中的處理序持有 OS 磁碟機的開啟檔案控制代碼，則 OS 磁碟機無法取消掛接，因而造成無法加密 OS 磁碟機。 
- 自訂指令碼與要啟用的加密在相近時間下執行，或在加密流程期間對 VM 進行任何其他變更。 當 Azure Resource Manager 範本定義要同時執行的多個擴充功能，或當自訂指令碼擴充功能或其他動作同時對磁碟加密執行時，會發生此衝突。 將這些步驟序列化和隔離，就可解決此問題。
- 啟用加密之前尚未停用安全性增強 Linux (SELinux) 時，取消掛接步驟就會失敗。 完成加密後，即可重新啟用 SELinux。
- OS 磁碟會使用邏輯磁碟區管理員 (LVM) 配置。 雖然可以使用有限的 LVM 資料磁碟支援，但是 LVM OS 磁碟並沒有。
- 未符合最小記憶體需求 (OS 磁碟加密建議為 7 GB)。
- 資料磁碟機已遞迴掛接於 /mnt/ directory 下，或彼此掛接 (例如，/mnt/data1、/mnt/data2、/data3 + /data3/data4)。

## <a name="update-the-default-kernel-for-ubuntu-1404-lts"></a> 更新 Ubuntu 14.04 LTS 的預設核心

預設核心版本 4.4 隨附 Ubuntu 14.04 LTS 映像。 此核心版本有已知問題，其中的 Out of Memory Killer 在 OS 加密程序期間不當地終止 dd 命令。 最近的 Azure 微調 Linux 核心已修正這個錯誤 (bug)。 若要避免這個錯誤，在啟用映像加密之前，請使用下列命令，更新為 [Azure 微調核心 4.15](https://packages.ubuntu.com/trusty/linux-azure)或更新版本：

```
sudo apt-get update
sudo apt-get install linux-azure
sudo reboot
```

在 VM 重新啟動到新的核心之後，可以使用下列命令來確認新的核心版本：

```
uname -a
```

## <a name="update-the-azure-virtual-machine-agent-and-extension-versions"></a>更新 Azure 虛擬機器代理程式和擴充功能版本

使用不支援的 Azure 虛擬機器代理程式版本的虛擬機器映射上的 Azure 磁碟加密作業可能會失敗。 在啟用加密之前，應先更新代理程式版本早于2.2.38 的 Linux 映射。 如需詳細資訊，請參閱 [如何在 VM 上更新 Azure Linux 代理程式](../extensions/update-linux-agent.md) ，以及 [在 azure 中更新虛擬機器代理程式的最低版本支援](https://support.microsoft.com/en-us/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support)。

此外，也需要正確的 AzureDiskEncryption 版本或 AzureDiskEncryptionForLinux 來賓代理程式延伸模組的版本。 當符合 Azure 虛擬機器代理程式必要條件，並使用支援的虛擬機器代理程式版本時，平臺會自動維護和更新延伸模組版本。

Microsoft.ostcextensions.customscriptforlinux. AzureDiskEncryptionForLinux 延伸模組已被取代，不再受到支援。  

## <a name="unable-to-encrypt-linux-disks"></a>無法將 Linux 磁碟加密

在某些情況下，Linux 磁碟加密似乎會卡在「已啟動的 OS 磁碟加密」且停用 SSH。 在庫存資源庫映像上，此加密程序可能需要 3-16 個小時才能完成。 如果新增多 TB 大小的資料磁碟，此程序可能需要數天的時間。

Linux OS 磁碟加密順序會暫時取消掛接 OS 磁碟機。 然後會對整個 OS 磁碟執行逐區塊加密，再於它處於已加密狀態時重新掛接。 進行加密時，Linux 磁片加密不允許同時使用 VM。 VM 的效能特性在完成加密所需的時間上有顯著差異。 這些特性包括磁碟的大小以及儲存體帳戶是標準或進階 (SSD) 儲存體。

當 OS 磁片磁碟機加密時，VM 會進入服務狀態並停用 SSH，以防止任何正在進行的進程中斷。  若要檢查加密狀態，請使用 Azure PowerShell [>get-azvmdiskencryptionstatus](/powershell/module/az.compute/get-azvmdiskencryptionstatus) 命令，並檢查 **ProgressMessage** 欄位。 **ProgressMessage** 將報告一系列的狀態，因為資料和作業系統磁片已加密：

```azurepowershell
PS > Get-AzVMDiskEncryptionStatus -ResourceGroupName "MyResourceGroup" -VMName "myVM"

OsVolumeEncrypted          : EncryptionInProgress
DataVolumesEncrypted       : EncryptionInProgress
OsVolumeEncryptionSettings :
ProgressMessage            : Transitioning

PS > Get-AzVMDiskEncryptionStatus -ResourceGroupName "MyResourceGroup" -VMName "myVM"

OsVolumeEncrypted          : EncryptionInProgress
DataVolumesEncrypted       : EncryptionInProgress
OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
ProgressMessage            : Encryption succeeded for data volumes

PS > Get-AzVMDiskEncryptionStatus -ResourceGroupName "MyResourceGroup" -VMName "myVM"

OsVolumeEncrypted          : EncryptionInProgress
DataVolumesEncrypted       : EncryptionInProgress
OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
ProgressMessage            : Provisioning succeeded

PS > Get-AzVMDiskEncryptionStatus -ResourceGroupName "MyResourceGroup" -VMName "myVM"

OsVolumeEncrypted          : EncryptionInProgress
DataVolumesEncrypted       : EncryptionInProgress
OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
ProgressMessage            : OS disk encryption started
```

**ProgressMessage**將會保留在**作業系統磁片加密**中，以進行大部分的加密處理常式。  加密完成且成功時， **ProgressMessage** 會傳回：

```azurepowershell
PS > Get-AzVMDiskEncryptionStatus -ResourceGroupName "MyResourceGroup" -VMName "myVM"

OsVolumeEncrypted          : Encrypted
DataVolumesEncrypted       : NotMounted
OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
ProgressMessage            : Encryption succeeded for all volumes
```

可以使用此訊息之後，加密的 OS 磁碟機應該會就緒可供使用，且 VM 也就緒可再次使用。

如果開機資訊、進度訊息或錯誤報表作業系統加密在此程式中失敗，請將 VM 還原至在加密之前立即執行的快照集或備份。 訊息的範本是本指南中所述的「無法取消掛接」錯誤。

在重新嘗試加密之前，請重新評估 VM 的特性，並確定所有必要條件都已滿足。

## <a name="troubleshooting-azure-disk-encryption-behind-a-firewall"></a>針對防火牆後方的 Azure 磁碟加密進行疑難排解

查看 [隔離網路上的磁片加密](disk-encryption-isolated-network.md)

## <a name="troubleshooting-encryption-status"></a>針對加密狀態進行疑難排解 

即使已在 VM 內解密磁碟，入口網站也可能會將其顯示為加密。  當使用低層級命令直接從 VM 內解密磁碟，而不是使用較高層級的 Azure 磁碟加密管理命令時，就會發生這種情況。  較高層級的命令不只會從 VM 內解密磁碟，也會在 VM 外部更新與 VM 建立關聯的重要平台層級加密設定和延伸模組設定。  如果內外未保持一致，則平台將無法回報加密狀態或適當地佈建 VM。

若要停用 PowerShell 的 Azure 磁碟加密，請於 [Remove-AzVMDiskEncryptionExtension](/powershell/module/az.compute/remove-azvmdiskencryptionextension) 後接著使用 [Disable-AzVMDiskEncryption](/powershell/module/az.compute/disable-azvmdiskencryption)。 若在停用加密之前執行 Remove-AzVMDiskEncryptionExtension 將會導致失敗。

若要停用 CLI 的 Azure 磁碟加密，請使用 [az vm encryption disable](/cli/azure/vm/encryption)。

## <a name="next-steps"></a>後續步驟

在本文件中，您已深入了解 Azure 磁碟加密中的一些常見問題，以及如何進行疑難排解。 如需此服務和其功能的相關資訊，請參閱下列文章：

- [在 Azure 資訊安全中心套用磁碟加密](../../security-center/asset-inventory.md)
- [待用 Azure 資料加密](../../security/fundamentals/encryption-atrest.md)