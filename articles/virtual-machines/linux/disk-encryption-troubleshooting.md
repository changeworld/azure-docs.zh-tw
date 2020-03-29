---
title: 為 Linux VM 解決 Azure 磁片加密的疑難排解
description: 本文為 Linux VM 提供了 Microsoft Azure 磁片加密的故障排除提示。
author: msmbaldwin
ms.service: virtual-machines-linux
ms.subservice: security
ms.topic: article
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: eeacea9e3305865881747801100dc17770b7df63
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78970462"
---
# <a name="azure-disk-encryption-troubleshooting-guide"></a>Azure 磁碟加密疑難排解指南

本指南適用於組織採用 Azure 磁碟加密的 IT 專業人員、資訊安全性分析師和雲端系統管理員。 本文旨在協助磁碟加密相關問題的疑難排解。

在採取以下任一步驟之前，首先確保嘗試加密的 VM 屬於[支援的 VM 大小和作業系統](disk-encryption-overview.md#supported-vms-and-operating-systems)，並且您滿足所有先決條件：

- [VM 的其他要求](disk-encryption-overview.md#supported-vms-and-operating-systems)
- [網路需求](disk-encryption-overview.md#networking-requirements)
- [加密金鑰存儲要求](disk-encryption-overview.md#encryption-key-storage-requirements)

 

## <a name="troubleshooting-linux-os-disk-encryption"></a>針對 Linux OS 磁碟加密進行疑難排解

Linux 作業系統 (OS) 磁碟加密必須先將 OS 磁碟機取消掛接後，才能透過完整磁碟加密流程加以執行。 如果無法取消掛接磁碟機，則可能出現「後續無法取消掛接...」錯誤訊息。

在具有從受支援的股票庫映射更改的環境的 VM 上嘗試作業系統磁片加密時，可能會發生此錯誤。 來自受支援映像的偏差可能會干擾 OS 磁碟機取消掛接擴充功能。 偏差的範例可能包括下列項目：
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

## <a name="update-the-azure-virtual-machine-agent-and-extension-versions"></a>更新 Azure 虛擬機器代理和擴展版本

使用不支援的 Azure 虛擬機器代理版本，Azure 磁片加密操作可能會失敗。 在啟用加密之前，應更新代理版本早于 2.2.38 的 Linux 映射。 有關詳細資訊，請參閱如何在[VM 上更新 Azure Linux 代理](../extensions/update-linux-agent.md)，以及 Azure[中的虛擬機器代理的最低版本支援](https://support.microsoft.com/en-us/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support)。

還需要微軟.Azure.Security.Azure.安全.Azure磁片加密或微軟.Azure.Security.Security.AzureDisk加密為Linux來賓代理擴展。 當滿足 Azure 虛擬機器代理先決條件並且使用虛擬機器代理的支援版本時，平臺會自動維護和更新擴展版本。

Microsoft.OSTC 擴展.AzureDisk加密為 Linux 擴展已被棄用，不再受支援。  

## <a name="unable-to-encrypt-linux-disks"></a>無法將 Linux 磁碟加密

在某些情況下，Linux 磁碟加密似乎會卡在「已啟動的 OS 磁碟加密」且停用 SSH。 在庫存資源庫映像上，此加密程序可能需要 3-16 個小時才能完成。 如果新增多 TB 大小的資料磁碟，此程序可能需要數天的時間。

Linux OS 磁碟加密順序會暫時取消掛接 OS 磁碟機。 然後會對整個 OS 磁碟執行逐區塊加密，再於它處於已加密狀態時重新掛接。 Linux 磁片加密不允許在加密進行期間同時使用 VM。 VM 的效能特性在完成加密所需的時間上有顯著差異。 這些特性包括磁碟的大小以及儲存體帳戶是標準或進階 (SSD) 儲存體。

要檢查加密狀態，請輪詢從[獲取-AzVmDisk加密狀態命令](/powershell/module/az.compute/get-azvmdiskencryptionstatus)返回**的"進度消息**"欄位。 加密 OS 磁碟機時，VM 會進入服務狀態且會停用 SSH，從而避免中斷進行中的流程。 **EncryptionInProgress** 訊息報告加密正在進行時的大部分時間。 數個小時之後，**VMRestartPending** 訊息會提示您重新啟動 VM。 例如：


```azurepowershell
PS > Get-AzVMDiskEncryptionStatus -ResourceGroupName "MyVirtualMachineResourceGroup" -VMName "VirtualMachineName"
OsVolumeEncrypted          : EncryptionInProgress
DataVolumesEncrypted       : EncryptionInProgress
OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
ProgressMessage            : OS disk encryption started

PS > Get-AzVMDiskEncryptionStatus -ResourceGroupName "MyVirtualMachineResourceGroup" -VMName "VirtualMachineName"
OsVolumeEncrypted          : VMRestartPending
DataVolumesEncrypted       : Encrypted
OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
ProgressMessage            : OS disk successfully encrypted, please reboot the VM
```

系統提示您重新啟動 VM，且 VM 重新啟動之後，您必須等待 2-3 分鐘讓重新啟動和最後步驟在目標上執行。 加密最終完成時，狀態訊息會變更。 可以使用此訊息之後，加密的 OS 磁碟機應該會就緒可供使用，且 VM 也就緒可再次使用。

在下列情況中，我們建議您將 VM 還原至加密前進行的快照集或備份：
   - 如果先前所述的重新啟動順序未發生。
   - 如果開機資訊、進度訊息或其他錯誤指標報告 OS 加密在此處理序中間失敗。 訊息的範本是本指南中所述的「無法取消掛接」錯誤。

在下一次嘗試之前，請重新評估 VM 的特性，並確定所有必要條件都已滿足。

## <a name="troubleshooting-azure-disk-encryption-behind-a-firewall"></a>針對防火牆後方的 Azure 磁碟加密進行疑難排解

請參閱[隔離網路上的磁片加密](disk-encryption-isolated-network.md)

## <a name="troubleshooting-encryption-status"></a>針對加密狀態進行疑難排解 

門戶可能會將磁片顯示為加密磁片，即使磁片在 VM 中未加密。  當使用低級命令從 VM 中直接取消加密磁片，而不是使用更高級別的 Azure 磁片加密管理命令時，可能會發生這種情況。  更高級別的命令不僅從 VM 中取消加密磁片，而且在 VM 外部也會更新與 VM 關聯的重要平臺級加密設定和擴展設置。  如果這些不對齊，平臺將無法報告加密狀態或正確預配 VM。   

要禁用使用 PowerShell 的 Azure 磁片加密，請使用[禁用 AzVM 磁片加密](/powershell/module/az.compute/disable-azvmdiskencryption)，然後[刪除-AzVM磁片加密擴展](/powershell/module/az.compute/remove-azvmdiskencryptionextension)。 在禁用加密之前運行刪除-AzVM 磁片加密擴展將失敗。

要禁用使用 CLI 的 Azure 磁片加密，請使用[az vm 加密禁用](/cli/azure/vm/encryption)。 

## <a name="next-steps"></a>後續步驟

在本文件中，您已深入了解 Azure 磁碟加密中的一些常見問題，以及如何進行疑難排解。 如需此服務和其功能的相關資訊，請參閱下列文章：

- [在 Azure 資訊安全中心套用磁碟加密](../../security-center/security-center-apply-disk-encryption.md)
- [待用 Azure 資料加密](../../security/fundamentals/encryption-atrest.md)
