---
title: 啟用適用於 Linux VM 的 Azure 磁碟加密
description: 本文提供有關為 Linux VM 啟用 Microsoft Azure 磁片加密的說明。
author: msmbaldwin
ms.service: virtual-machines-linux
ms.subservice: security
ms.topic: article
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: ff1b37c3053ffa91dcb432cd97a7dd6fd71dad1c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79250422"
---
# <a name="azure-disk-encryption-for-linux-vms"></a>適用于 Linux VM 的 Azure 磁片加密 

Azure 磁碟加密可協助您保護資料安全，以符合您組織的安全性和合規性承諾。 它使用 Linux 的[DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt)功能為 Azure 虛擬機器 （VM） 的作業系統和資料磁片提供卷加密，並與[Azure 金鑰保存庫](../../key-vault/index.yml)集成，以説明您控制和管理磁片加密金鑰和機密。 

如果使用[Azure 安全中心](../../security-center/index.yml)，則如果有未加密的 VM，則會收到警報。 這些警示會顯示為「高嚴重性」，而建議就是加密這些 VM。

![「Azure 資訊安全中心」磁碟加密警示](media/disk-encryption/security-center-disk-encryption-fig1.png)

> [!WARNING]
> - 如果以前使用 Azure 磁片加密與 Azure AD 一起加密 VM，則必須繼續使用此選項對 VM 進行加密。 有關詳細資訊[，請參閱 Azure 磁片加密與 Azure AD（上一版本）。](disk-encryption-overview-aad.md) 
> - 某些建議可能會增加資料、網路或計算資源的使用量，導致額外的授權或訂用帳戶成本。 您必須擁有有效的作用中 Azure 訂用帳戶，才能在 Azure 支援的區域中建立資源。
> - 當前第 2 代 VM 不支援 Azure 磁片加密。 有關詳細資訊[，請參閱 Azure 上對第 2 代 VM 的支援](https://docs.microsoft.com/azure/virtual-machines/windows/generation-2)。

只需使用[Azure CLI 快速啟動創建和加密 Linux VM，](disk-encryption-cli-quickstart.md)即可在幾分鐘內瞭解 Linux 的 Azure 磁片加密基礎知識，或者[使用 Azure Powershell 快速入門創建和加密 Linux VM。](disk-encryption-powershell-quickstart.md)

## <a name="supported-vms-and-operating-systems"></a>支援的 VM 和作業系統

### <a name="supported-vm-sizes"></a>支援的 VM 大小

Linux VM 有多種[尺寸](sizes.md)可供選擇。 Azure 磁片加密在[基本、A 系列 VM](https://azure.microsoft.com/pricing/details/virtual-machines/series/)或不符合這些最低記憶體要求的虛擬機器上不可用：

| 虛擬機器 | 最低記憶體要求 |
|--|--|
| 僅加密資料卷時的 Linux VM| 2 GB |
| 加密資料和作業系統卷時的 Linux VM，以及根（/）檔案系統使用量為 4GB 或更少的位置 | 8 GB |
| 加密資料和作業系統卷時的 Linux VM，以及根（/） 檔案系統使用量大於 4GB 的位置 | 根檔案系統使用方式 = 2。 例如，16 GB 的根檔案系統使用方式至少需要 32 GB 的 RAM |

在 Linux 虛擬機器上完成 OS 磁片加密過程後，可以將 VM 配置為以更少的記憶體運行。 

Azure 磁片加密也可用於具有高級存儲的 VM。 

### <a name="supported-operating-systems"></a>支援的作業系統

Azure 磁片加密受 Azure 認可的[Linux 發行版本的](endorsed-distros.md)子集支援，該發行版本本身是所有 Linux 伺服器可能發行版本的子集。

![支援 Azure 磁片加密的 Linux 伺服器發行版本的 Venn 圖](./media/disk-encryption/ade-supported-distros.png)

Azure 不支援的 Linux 伺服器發行版本不支援 Azure 磁片加密;因此，不支援 Azure 磁片加密。在已認可版本中，只有以下版本支援 Azure 磁片加密：

| Linux 發行版本 | 版本 | 支援加密的磁碟區類型|
| --- | --- |--- |
| Ubuntu | 18.04| 作業系統和資料磁碟 |
| Ubuntu | 16.04| 作業系統和資料磁碟 |
| Ubuntu | 14.04.5</br>[搭配更新至 4.15 或更新版本的 Azure 調整核心](disk-encryption-troubleshooting.md) | 作業系統和資料磁碟 |
| RHEL | 7.7 | 作業系統和資料磁片（見下文注釋） |
| RHEL | 7.6 | 作業系統和資料磁片（見下文注釋） |
| RHEL | 7.5 | 作業系統和資料磁片（見下文注釋） |
| RHEL | 7.4 | 作業系統和資料磁片（見下文注釋） |
| RHEL | 7.3 | 作業系統和資料磁片（見下文注釋） |
| RHEL | 7.2 | 作業系統和資料磁片（見下文注釋） |
| RHEL | 6.8 | 資料磁片（見下文注釋） |
| RHEL | 6.7 | 資料磁片（見下文注釋） |
| CentOS | 7.7 | 作業系統和資料磁碟 |
| CentOS | 7.6 | 作業系統和資料磁碟 |
| CentOS | 7.5 | 作業系統和資料磁碟 |
| CentOS | 7.4 | 作業系統和資料磁碟 |
| CentOS | 7.3 | 作業系統和資料磁碟 |
| CentOS | 7.2n | 作業系統和資料磁碟 |
| CentOS | 6.8 | 資料磁碟 |
| openSUSE | 42.3 | 資料磁碟 |
| SLES | 12-SP4 | 資料磁碟 |
| SLES | 12-SP3 | 資料磁碟 |

> [!NOTE]
> RHEL7 即用即付映射的 RHEL 作業系統和資料磁片支援新的 Azure 磁片加密實現。  
>
> ADE 也支援 RHEL 自帶自訂閱的黃金映射，但僅在註冊訂閱**後**。 有關詳細資訊，請參閱[Azure 中的紅帽企業 Linux 自帶自訂閱黃金映射](../workloads/redhat/byos.md#encrypt-red-hat-enterprise-linux-bring-your-own-subscription-gold-images)

## <a name="additional-vm-requirements"></a>其他 VM 要求

Azure 磁片加密要求系統上存在 dm-crypt 和 vfat 模組。 從預設映射中刪除或禁用 vfat 將阻止系統讀取金鑰卷並獲取在後續重新開機時解鎖磁片所需的金鑰。 從系統中刪除 vfat 模組的系統強化步驟與 Azure 磁片加密不相容。 

在啟用加密之前，要加密的資料磁片必須在 /etc/fstab 中正確列出。 請為此項目使用永續性區塊裝置名稱，原因是採用「/dev/sdX」格式的裝置名稱不一定會在重新開機時與相同的磁碟相關聯，在套用加密後尤其如此。 如需此行為的詳細資訊，請參閱：[針對 Linux VM 裝置名稱變更進行疑難排解](troubleshoot-device-names-problems.md)

確定 /etc/fstab 設定已正確設定而能夠掛接。 若要設定這些設定，請執行 mount -a 命令，或重新啟動 VM 再以該方式觸發重新掛接。 完成之後，檢查 lsblk 命令的輸出以確認所需的磁碟機仍有掛接。 
- 如果 /etc/fstab 檔案還未正確掛接磁碟機就啟用加密，Azure 磁碟加密將無法正確地掛接磁碟機。
- Azure 磁碟加密程序會在加密程序進行期間，將掛接資訊從 /etc/fstab 移出，並移入自己的設定檔中。 在資料磁碟機加密完成之後，若發現 /etc/fstab 中有項目遺失，請不要擔心。
- 在開始加密之前，請確保停止所有可能寫入裝載的資料磁片的服務和進程並禁用它們，以便在重新開機後它們不會自動重新開機。 這些分區上的檔將保持打開狀態，從而阻止重新安裝這些檔的加密過程，從而導致加密失敗。 
- 重新開機後，Azure 磁碟加密程序需要一點時間才能掛接新加密的磁碟。 這些磁碟無法在重新開機後立即可供使用。 此程序需要時間來將加密的磁碟機啟動、解除鎖定再加以掛接，然後這些磁碟機才可供其他程序來存取。 視系統特性而定，在重新開機後，此程序可能需要進行超過一分鐘。

可用於裝載資料磁片並創建必要的 /etc/fstab 條目的命令示例可在[Azure 磁片加密先決條件 CLI 腳本](https://github.com/ejarvi/ade-cli-getting-started)（第 244-248 行）和[Azure 磁片加密先決條件 PowerShell 腳本](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts)中找到。 

## <a name="networking-requirements"></a>網路需求

要啟用 Azure 磁片加密功能，Linux VM 必須滿足以下網路終結點配置要求：
  - 要獲取權杖以連接到金鑰保存庫，Linux VM 必須能夠連接到 Azure 活動目錄終結點，login.microsoftonline.com \[ \]。
  - 要將加密金鑰寫入金鑰保存庫，Linux VM 必須能夠連接到金鑰保存庫終結點。
  - Linux VM 必須能夠連接到承載 Azure 擴展存儲庫的 Azure 存儲終結點和承載 VHD 檔的 Azure 存儲帳戶。
  -  如果您的安全性原則會限制從 Azure VM 至網際網路的存取，您可以解析前述的 URI，並設定特定的規則以允許和這些 IP 的輸出連線。 如需詳細資訊，請參閱[防火牆後方的 Azure Key Vault](../../key-vault/key-vault-access-behind-firewall.md)。  

## <a name="encryption-key-storage-requirements"></a>加密金鑰存儲要求  

Azure 磁片加密需要 Azure 金鑰保存庫來控制和管理磁片加密金鑰和機密。 金鑰保存庫和 VM 必須駐留在同一 Azure 區域和訂閱中。

有關詳細資訊，請參閱[創建和配置 Azure 磁片加密的金鑰保存庫](disk-encryption-key-vault.md)。

## <a name="terminology"></a>詞彙
下表定義了 Azure 磁片加密文檔中使用的一些常用術語：

| 詞彙 | 定義 |
| --- | --- |
| Azure 金鑰保存庫 | Key Vault 是一個密碼編譯金鑰管理服務，以「美國聯邦資訊處理標準」(FIPS) 已驗證的硬體安全性模組為基礎。 這些標準可協助您保護密碼編譯金鑰和敏感性祕密。 有關詳細資訊，請參閱 Azure[金鑰保存庫](https://azure.microsoft.com/services/key-vault/)文檔以及[創建和配置 Azure 磁片加密的金鑰保存庫](disk-encryption-key-vault.md)。 |
| Azure CLI | [Azure CLI](/cli/azure/install-azure-cli) 已針對從命令列管理 Azure 資源進行最佳化。|
| DM-Crypt |[DM-Crypt](https://gitlab.com/cryptsetup/cryptsetup/wikis/DMCrypt)是基於 Linux 的透明磁片加密子系統，用於在 Linux VM 上啟用磁片加密。 |
| 金鑰加密金鑰 （KEK） | 可用於保護或包裝機密的不對稱金鑰 （RSA 2048）。 您可以提供硬體安全性模組 (HSM) 保護的金鑰或軟體保護的金鑰。 有關詳細資訊，請參閱 Azure[金鑰保存庫](https://azure.microsoft.com/services/key-vault/)文檔以及[創建和配置 Azure 磁片加密的金鑰保存庫](disk-encryption-key-vault.md)。 |
| PowerShell Cmdlet | 如需詳細資訊，請參閱 [Azure PowerShell Cmdlet](/powershell/azure/overview)。 |


## <a name="next-steps"></a>後續步驟

- [快速入門 - 使用 Azure CLI 創建和加密 Linux VM](disk-encryption-cli-quickstart.md)
- [快速入門 - 使用 Azure 電源外殼創建和加密 Linux VM](disk-encryption-powershell-quickstart.md)
- [Linux VM 上的 Azure 磁碟加密案例](disk-encryption-linux.md)
- [Azure 磁片加密先決條件 CLI 腳本](https://github.com/ejarvi/ade-cli-getting-started)
- [Azure 磁片加密先決條件 PowerShell 腳本](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts)
- [建立及設定適用於 Azure 磁碟加密的金鑰保存庫](disk-encryption-key-vault.md)


