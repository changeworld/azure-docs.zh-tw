---
title: 啟用 Linux Vm 的 Azure 磁碟加密
description: 本文提供針對 Linux Vm 啟用 Microsoft Azure 磁片加密的指示。
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: 96ac1becfed74141b3b1544646f5d82bd0985045
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/05/2020
ms.locfileid: "78396845"
---
# <a name="azure-disk-encryption-for-linux-vms"></a>適用于 Linux Vm 的 Azure 磁碟加密 

Azure 磁碟加密有助於保護和保護您的資料，以符合組織的安全性和合規性承諾。 它會使用 Linux 的[DM Crypt](https://en.wikipedia.org/wiki/Dm-crypt)功能，為 Azure 虛擬機器（vm）的 OS 和資料磁片提供磁片區加密，並與[Azure Key Vault](../../key-vault/index.yml)整合，協助您控制及管理磁片加密金鑰和密碼。 

如果您使用[Azure 資訊安全中心](../../security-center/index.yml)，則會在您有未加密的 vm 時收到警示。 這些警示會顯示為「高嚴重性」，而建議就是加密這些 VM。

![「Azure 資訊安全中心」磁碟加密警示](media/disk-encryption/security-center-disk-encryption-fig1.png)

> [!WARNING]
> - 如果您先前已使用 Azure 磁碟加密搭配 Azure AD 來加密 VM，則必須繼續使用此選項來加密您的 VM。 如需詳細資訊，請參閱[Azure AD 的 Azure 磁碟加密（上一個版本）](disk-encryption-overview-aad.md) 。 
> - 某些建議可能會增加資料、網路或計算資源的使用量，導致額外的授權或訂用帳戶成本。 您必須擁有有效的作用中 Azure 訂用帳戶，才能在 Azure 支援的區域中建立資源。
> - 目前第2代 Vm 不支援 Azure 磁碟加密。 如需詳細資訊，請參閱[Azure 上的第2代 Vm 支援](https://docs.microsoft.com/azure/virtual-machines/windows/generation-2)。

您只要在幾分鐘內就能瞭解 Linux Azure 磁碟加密的基本概念，請使用[Azure CLI 快速入門建立和加密 LINUX vm](disk-encryption-cli-quickstart.md) ，或使用[Azure Powershell 建立和加密 linux vm 快速入門](disk-encryption-powershell-quickstart.md)。

## <a name="supported-vms-and-operating-systems"></a>支援的 Vm 和作業系統

### <a name="supported-vm-sizes"></a>支援的 VM 大小

Linux Vm 有[各種大小的](sizes.md)提供。 在[基本、A 系列 vm](https://azure.microsoft.com/pricing/details/virtual-machines/series/)或不符合這些最低記憶體需求的虛擬機器上，無法使用 Azure 磁碟加密：

| 虛擬機器 | 最小記憶體需求 |
|--|--|
| 僅加密資料磁片區時的 Linux Vm| 2 GB |
| 當加密資料和作業系統磁片區時，以及根（/）檔案系統使用量為4GB 或更少的位置時，Linux Vm | 8 GB |
| 當加密資料和作業系統磁片區，以及根（/）檔案系統使用量大於4GB 時，Linux Vm | 根檔案系統使用方式 * 2。 例如，16 GB 的根檔案系統使用量至少需要32GB 的 RAM |

一旦 Linux 虛擬機器上的 OS 磁片加密程式完成後，即可將 VM 設定成以較少的記憶體執行。 

Azure 磁碟加密也適用于具有 premium 儲存體的 Vm。 

### <a name="supported-operating-systems"></a>支援的作業系統

在經[Azure 背書的 linux](endorsed-distros.md)散發套件的子集中，支援 Azure 磁碟加密，這本身是所有 Linux 伺服器可能散發套件的子集。

![支援 Azure 磁碟加密的 Linux 伺服器散發套件的卞氏圖表](./media/disk-encryption/ade-supported-distros.png)

Azure 未背書的 Linux 伺服器散發套件不支援 Azure 磁碟加密;對於背書的，只有下列散發套件和版本支援 Azure 磁碟加密：

| Linux 發行版本 | 版本 | 支援加密的磁碟區類型|
| --- | --- |--- |
| Ubuntu | 18.04| 作業系統和資料磁碟 |
| Ubuntu | 16.04| 作業系統和資料磁碟 |
| Ubuntu | 14.04.5</br>[搭配更新至 4.15 或更新版本的 Azure 調整核心](disk-encryption-troubleshooting.md) | 作業系統和資料磁碟 |
| RHEL | 7.7 | 作業系統和資料磁片（請參閱下面的附注） |
| RHEL | 7.6 | 作業系統和資料磁片（請參閱下面的附注） |
| RHEL | 7.5 | 作業系統和資料磁片（請參閱下面的附注） |
| RHEL | 7.4 | 作業系統和資料磁片（請參閱下面的附注） |
| RHEL | 7.3 | 作業系統和資料磁片（請參閱下面的附注） |
| RHEL | 7.2 | 作業系統和資料磁片（請參閱下面的附注） |
| RHEL | 6.8 | 資料磁片（請參閱下面的附注） |
| RHEL | 6.7 | 資料磁片（請參閱下面的附注） |
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
> 適用于 RHEL7 隨用隨付映射的 RHEL OS 和資料磁片支援新的 Azure 磁碟加密執行。  
>
> RHEL 自備訂用帳戶金級映射也支援 ADE，但只有在註冊訂閱**之後**才可。 如需詳細資訊，請參閱[在 Azure 中 Red Hat Enterprise Linux 自備訂用帳戶黃金映射](../workloads/redhat/byos.md##encrypt-red-hat-enterprise-linux-bring-your-own-subscription-gold-images)

## <a name="additional-vm-requirements"></a>其他 VM 需求

Azure 磁碟加密需要在系統上出現 dm crypt 和 vfat 模組。 從預設映射移除或停用 vfat，將會導致系統無法讀取金鑰磁片區，並取得在後續重新開機時解除鎖定磁片所需的金鑰。 從系統中移除 vfat 模組的系統強化步驟，與 Azure 磁碟加密不相容。 

啟用加密之前，必須先在/etc/fstab 中正確列出要加密的資料磁片。 請為此項目使用永續性區塊裝置名稱，原因是採用「/dev/sdX」格式的裝置名稱不一定會在重新開機時與相同的磁碟相關聯，在套用加密後尤其如此。 如需此行為的詳細資訊，請參閱：[針對 Linux VM 裝置名稱變更進行疑難排解](troubleshoot-device-names-problems.md)

確定 /etc/fstab 設定已正確設定而能夠掛接。 若要設定這些設定，請執行 mount -a 命令，或重新啟動 VM 再以該方式觸發重新掛接。 完成之後，檢查 lsblk 命令的輸出以確認所需的磁碟機仍有掛接。 
- 如果 /etc/fstab 檔案還未正確掛接磁碟機就啟用加密，Azure 磁碟加密將無法正確地掛接磁碟機。
- Azure 磁碟加密程序會在加密程序進行期間，將掛接資訊從 /etc/fstab 移出，並移入自己的設定檔中。 在資料磁碟機加密完成之後，若發現 /etc/fstab 中有項目遺失，請不要擔心。
- 開始加密之前，請務必停止所有可能寫入已掛接之資料磁片的服務和處理常式，並將其停用，使其不會在重新開機後自動重新開機。 這些磁碟分割上的檔案可能會保持開啟狀態，防止加密程式重新掛接它們，因而導致加密失敗。 
- 重新開機後，Azure 磁碟加密程序需要一點時間才能掛接新加密的磁碟。 這些磁碟無法在重新開機後立即可供使用。 此程序需要時間來將加密的磁碟機啟動、解除鎖定再加以掛接，然後這些磁碟機才可供其他程序來存取。 視系統特性而定，在重新開機後，此程序可能需要進行超過一分鐘。

如需可用來掛接資料磁片並建立必要/etc/fstab 專案的命令範例，請參閱[Azure 磁碟加密必要條件 CLI 腳本](https://github.com/ejarvi/ade-cli-getting-started)（行244-248）和[Azure 磁碟加密必要條件 PowerShell 腳本](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts)。 

## <a name="networking-requirements"></a>網路需求

若要啟用 Azure 磁碟加密功能，Linux Vm 必須符合下列網路端點設定需求：
  - 若要取得權杖以連線到您的金鑰保存庫，Linux VM 必須能夠連接到 Azure Active Directory 端點，\[login.microsoftonline.com\]。
  - 若要將加密金鑰寫入金鑰保存庫，Linux VM 必須能夠連線到金鑰保存庫端點。
  - Linux VM 必須能夠連接到裝載 Azure 擴充功能儲存機制的 Azure 儲存體端點，以及裝載 VHD 檔案的 Azure 儲存體帳戶。
  -  如果您的安全性原則會限制從 Azure VM 至網際網路的存取，您可以解析前述的 URI，並設定特定的規則以允許和這些 IP 的輸出連線。 如需詳細資訊，請參閱[防火牆後方的 Azure Key Vault](../../key-vault/key-vault-access-behind-firewall.md)。  

## <a name="encryption-key-storage-requirements"></a>加密金鑰儲存需求  

Azure 磁碟加密需要 Azure Key Vault 來控制及管理磁片加密金鑰和密碼。 您的金鑰保存庫和 Vm 必須位於相同的 Azure 區域和訂用帳戶中。

如需詳細資訊，請參閱[建立和設定 Azure 磁碟加密的金鑰保存庫](disk-encryption-key-vault.md)。

## <a name="terminology"></a>詞彙
下表定義 Azure 磁片加密檔中所使用的一些常見詞彙：

| 詞彙 | 定義 |
| --- | --- |
| Azure 金鑰保存庫 | Key Vault 是一個密碼編譯金鑰管理服務，以「美國聯邦資訊處理標準」(FIPS) 已驗證的硬體安全性模組為基礎。 這些標準可協助您保護密碼編譯金鑰和敏感性祕密。 如需詳細資訊，請參閱[Azure Key Vault](https://azure.microsoft.com/services/key-vault/)檔，以及[建立和設定 Azure 磁碟加密的金鑰保存庫](disk-encryption-key-vault.md)。 |
| Azure CLI | [Azure CLI](/cli/azure/install-azure-cli) 已針對從命令列管理 Azure 資源進行最佳化。|
| DM-Crypt |[DM Crypt](https://gitlab.com/cryptsetup/cryptsetup/wikis/DMCrypt)是以 linux 為基礎的透明磁片加密子系統，用來在 linux vm 上啟用磁片加密。 |
| 金鑰加密金鑰（KEK） | 您可以用來保護或包裝秘密的非對稱金鑰（RSA 2048）。 您可以提供硬體安全性模組 (HSM) 保護的金鑰或軟體保護的金鑰。 如需詳細資訊，請參閱[Azure Key Vault](https://azure.microsoft.com/services/key-vault/)檔，以及[建立和設定 Azure 磁碟加密的金鑰保存庫](disk-encryption-key-vault.md)。 |
| PowerShell Cmdlet | 如需詳細資訊，請參閱 [Azure PowerShell Cmdlet](/powershell/azure/overview)。 |


## <a name="next-steps"></a>後續步驟

- [快速入門-使用 Azure CLI 建立及加密 Linux VM](disk-encryption-cli-quickstart.md)
- [快速入門-使用 Azure Powershell 建立和加密 Linux VM](disk-encryption-powershell-quickstart.md)
- [Linux VM 上的 Azure 磁碟加密案例](disk-encryption-linux.md)
- [Azure 磁碟加密必要條件 CLI 腳本](https://github.com/ejarvi/ade-cli-getting-started)
- [Azure 磁碟加密必要條件 PowerShell 腳本](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts)
- [建立及設定適用於 Azure 磁碟加密的金鑰保存庫](disk-encryption-key-vault.md)


