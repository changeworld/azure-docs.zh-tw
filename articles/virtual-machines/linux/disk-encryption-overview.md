---
title: 啟用適用於 Linux VM 的 Azure 磁碟加密
description: 本文提供啟用適用於 Linux VM 的 Microsoft Azure 磁碟加密的指示。
author: msmbaldwin
ms.service: virtual-machines-linux
ms.subservice: security
ms.topic: article
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: f8e4843ad71455f8e478ef74ee71975c1dbf2925
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/20/2020
ms.locfileid: "86510549"
---
# <a name="azure-disk-encryption-for-linux-vms"></a>適用於 Linux VM 的 Azure 磁碟加密 

Azure 磁碟加密可協助保護資料安全，以符合貴組織安全性和合規性承諾。 其會使用 Linux 的 [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) 功能，為 Azure 虛擬機器 (VM) 的作業系統和資料磁碟提供磁碟區加密，並與 [Azure Key Vault](../../key-vault/index.yml) 整合，協助您控制及管理磁碟加密金鑰和祕密。 

如果您使用 [Azure 資訊安全中心](../../security-center/index.yml)，則會在未加密 VM 時收到警示。 這些警示會顯示為「高嚴重性」，而建議就是加密這些 VM。

![「Azure 資訊安全中心」磁碟加密警示](media/disk-encryption/security-center-disk-encryption-fig1.png)

> [!WARNING]
> - 如果您先前曾使用 Azure 磁碟加密搭配 Azure AD 來加密 VM，則必須繼續使用此選項來加密您的 VM。 如需詳細資料，請參閱 [Azure 磁碟加密與 Azure AD (舊版)](disk-encryption-overview-aad.md)。 
> - 某些建議可能會增加資料、網路或計算資源的使用量，導致額外的授權或訂用帳戶成本。 您必須擁有有效的作用中 Azure 訂用帳戶，才能在 Azure 支援的區域中建立資源。
> - 目前，第二代 VM 不支援 Azure 磁碟加密。 如需詳細資料，請參閱 [Azure 上第 2 代 VM 的支援](../windows/generation-2.md)。

透過[使用 Azure CLI 快速入門來建立並加密 Linux VM](disk-encryption-cli-quickstart.md)，或[使用 Azure PowerShell 快速入門來建立並加密 Linux VM](disk-encryption-powershell-quickstart.md)，您可以在幾分鐘內了解適用於 Linux 的 Azure 磁碟加密的基本概念。

## <a name="supported-vms-and-operating-systems"></a>支援的 VM 與作業系統

### <a name="supported-vms"></a>支援的 VM

Linux VM 有[各種大小](sizes.md)。 [基本、A 系列 VM](https://azure.microsoft.com/pricing/details/virtual-machines/series/)，或在不符合下列最低記憶體需求的虛擬機器上，無法使用 Azure 磁碟加密：

| 虛擬機器 | 最低記憶體需求 |
|--|--|
| 僅加密資料磁碟區時的 Linux VM| 2 GB |
| 同時加密資料和作業系統磁碟區，以及根 (/) 檔案系統使用量為 4GB 或以下的 Linux VM | 8 GB |
| 同時加密資料和作業系統磁碟區，以及根 (/) 檔案系統使用量為 4GB 以上的 Linux VM | 檔案系統使用量 * 2。 例如，16 GB 的根檔案系統使用量至少需要 32GB 的 RAM |

Linux 虛擬機器上的作業系統磁碟加密程序完成之後，就可以將 VM 設為使用較少的記憶體來執行。 

Azure 磁碟加密也適用於具有進階儲存體的 VM。

[第 2 代 VM](generation-2.md#generation-1-vs-generation-2-capabilities)) 和 [Lsv2 系列 VM](../lsv2-series.md)) 上無法使用 Azure 磁碟加密。 如需更多例外狀況，請參閱 [Azure 磁碟加密：不支援的案例](disk-encryption-linux.md#unsupported-scenarios)。

### <a name="supported-operating-systems"></a>支援的作業系統

[Azure 加密 Linux 發行版本](endorsed-distros.md)的子集支援 Azure 磁碟加密，其本身是所有 Linux 伺服器可能發行版本的子集。

![支援 Azure 磁碟加密的 Linux 伺服器發行版本的卞氏圖表](./media/disk-encryption/ade-supported-distros.png)

未經過 Azure 背書的 Linux 伺服器發行版本不支援 Azure 磁碟加密；在經過背書的發行版本中，只有以下發行版本支援 Azure 磁碟加密：

| 發行者 | 供應項目 | SKU | URN | 支援加密的磁碟區類型 |
| --- | --- |--- | --- |
| Canonical | Ubuntu | 18.04-LTS | Canonical:UbuntuServer:18.04-LTS:latest | 作業系統和資料磁碟 |
| Canonical | Ubuntu 18.04 | 18.04-DAILY-LTS | Canonical:UbuntuServer:18.04-DAILY-LTS:latest | 作業系統和資料磁碟 |
| Canonical | Ubuntu 16.04 | 16.04-DAILY-LTS | Canonical:UbuntuServer:16.04-DAILY-LTS:latest | 作業系統和資料磁碟 |
| Canonical | Ubuntu 14.04.5</br>[搭配更新至 4.15 或更新版本的 Azure 調整核心](disk-encryption-troubleshooting.md) | 14.04.5-LTS | Canonical:UbuntuServer:14.04.5-LTS:latest | 作業系統和資料磁碟 |
| Canonical | Ubuntu 14.04.5</br>[搭配更新至 4.15 或更新版本的 Azure 調整核心](disk-encryption-troubleshooting.md) | 14.04.5-DAILY-LTS | Canonical:UbuntuServer:14.04.5-DAILY-LTS:latest | 作業系統和資料磁碟 |
| RedHat | RHEL 7。8 | 7.8 | RedHat： RHEL：7.8：最新 | 作業系統和資料磁碟 (請參閱下列注意事項) |
| RedHat | RHEL 7.7 | 7.7 | RedHat:RHEL:7.7:latest | 作業系統和資料磁碟 (請參閱下列注意事項) |
| RedHat | RHEL 7.7 | 7-LVM | RedHat:RHEL:7-LVM:latest | 作業系統和資料磁碟 (請參閱下列注意事項) |
| RedHat | RHEL 7.6 | 7.6 | RedHat:RHEL:7.6:latest | 作業系統和資料磁碟 (請參閱下列注意事項) |
| RedHat | RHEL 7.5 | 7.5 | RedHat:RHEL:7.5:latest | 作業系統和資料磁碟 (請參閱下列注意事項) |
| RedHat | RHEL 7.4 | 7.4 | RedHat:RHEL:7.4:latest | 作業系統和資料磁碟 (請參閱下列注意事項) |
| RedHat | RHEL 7.3 | 7.3 | RedHat:RHEL:7.3:latest | 作業系統和資料磁碟 (請參閱下列注意事項) |
| RedHat | RHEL 7.2 | 7.2 | RedHat:RHEL:7.2:latest | 作業系統和資料磁碟 (請參閱下列注意事項) |
| RedHat | RHEL 6.8 | 6.8 | RedHat:RHEL:6.8:latest | 資料磁碟 (請參閱下列注意事項) |
| RedHat | RHEL 6.7 | 6.7 | RedHat:RHEL:6.7:latest | 資料磁碟 (請參閱下列注意事項) |
| OpenLogic | CentOS 7.7 | 7.7 | OpenLogic:CentOS:7.7:latest | 作業系統和資料磁碟 |
| OpenLogic | CentOS 7.7 | 7-LVM | OpenLogic:CentOS:7-LVM:latest | 作業系統和資料磁碟 |
| OpenLogic | CentOS 7.6 | 7.6 | OpenLogic:CentOS:7.6:latest | 作業系統和資料磁碟 |
| OpenLogic | CentOS 7.5 | 7.5 | OpenLogic:CentOS:7.5:latest | 作業系統和資料磁碟 |
| OpenLogic | CentOS 7.4 | 7.4 | OpenLogic:CentOS:7.4:latest | 作業系統和資料磁碟 |
| OpenLogic | CentOS 7.3 | 7.3 | OpenLogic:CentOS:7.3:latest | 作業系統和資料磁碟 |
| OpenLogic | CentOS 7.2n | 7.2n | OpenLogic:CentOS:7.2n:latest | 作業系統和資料磁碟 |
| OpenLogic | CentOS 7.1 | 7.1 | OpenLogic:CentOS:7.1:latest | 僅資料磁碟 |
| OpenLogic | CentOS 7.0 | 7.0 | OpenLogic:CentOS:7.0:latest | 僅資料磁碟 |
| OpenLogic | CentOS 6.8 | 6.8 | OpenLogic:CentOS:6.8:latest | 僅資料磁碟 |
| SUSE | openSUSE 42.3 | 42.3 | SUSE:openSUSE-Leap:42.3:latest | 僅資料磁碟 |
| SUSE | SLES 12-SP4 | 12-SP4 | SUSE:SLES:12-SP4:latest | 僅資料磁碟 |
| SUSE | SLES HPC 12-SP3 | 12-SP3 | SUSE:SLES-HPC:12-SP3:latest | 僅資料磁碟 |

> [!NOTE]
> RHEL OS 和適用於 RHEL7 隨用隨付映像的資料磁碟，可支援全新的 Azure 磁碟加密實作。  
>
> RHEL 自備訂閱金級映像也支援 ADE，但僅限於註冊訂閱**之後**。 如需詳細資訊，請參閱 [Azure 中的 Red Hat Enterprise Linux 自備訂閱金級映像](../workloads/redhat/byos.md#encrypt-red-hat-enterprise-linux-bring-your-own-subscription-gold-images)

## <a name="additional-vm-requirements"></a>其他 VM 需求

Azure 磁碟加密需要系統中存在 dm-crypt 和 vfat 模組。 從預設映像中移除或停用 vfat，會讓系統無法讀取金鑰磁碟區，也無法在後續重新啟動時，取得解除鎖定磁碟所需的金鑰。 將 vfat 模組從系統中移除，或強制擴充資料磁片磁碟機上的 OS 掛接點/資料夾的系統強化步驟，與 Azure 磁碟加密不相容。 

在啟用加密之前，要加密的資料磁碟必須在 /etc/fstab 中正確列出。 建立項目時，請使用「nofail」選項，並選擇持續性封鎖裝置名稱 (因為「/dev/sdX」格式的裝置名稱在重新啟動時，不會與相同磁碟相關聯，尤其是加密後；如需關於此行為的詳細資料，請參閱：[針對 Linux VM 裝置名稱變更進行疑難排解](../troubleshooting/troubleshoot-device-names-problems.md))。

確定 /etc/fstab 設定已正確設定而能夠掛接。 若要設定這些設定，請執行 mount -a 命令，或重新啟動 VM 再以該方式觸發重新掛接。 完成之後，檢查 lsblk 命令的輸出以確認所需的磁碟機仍有掛接。 

- 如果 /etc/fstab 檔案還未正確掛接磁碟機就啟用加密，Azure 磁碟加密將無法正確地掛接磁碟機。
- Azure 磁碟加密程序會在加密程序進行期間，將掛接資訊從 /etc/fstab 移出，並移入自己的設定檔中。 在資料磁碟機加密完成之後，若發現 /etc/fstab 中有項目遺失，請不要擔心。
- 啟動加密之前，請務必停止可能會寫入裝載資料磁碟的所有服務和程序，並將其停用，使其不會在重新開機後自動重新啟動。 這些可能會使分割區上的檔案保持開啟狀態，以防加密程序重新裝載這些檔案，造成加密失敗。 
- 重新開機後，Azure 磁碟加密程序需要一點時間才能掛接新加密的磁碟。 這些磁碟無法在重新開機後立即可供使用。 此程序需要時間來將加密的磁碟機啟動、解除鎖定再加以掛接，然後這些磁碟機才可供其他程序來存取。 視系統特性而定，在重新開機後，此程序可能需要進行超過一分鐘。

以下是用於裝載資料磁碟和建立必要 /etc/fstab 項目的命令範例。

```bash
UUID0="$(blkid -s UUID -o value /dev/disk/azure/scsi1/lun0)"
UUID1="$(blkid -s UUID -o value /dev/disk/azure/scsi1/lun1)"
mkdir /data0
mkdir /data1
echo "UUID=$UUID0 /data0 ext4 defaults,nofail 0 0" >>/etc/fstab
echo "UUID=$UUID1 /data1 ext4 defaults,nofail 0 0" >>/etc/fstab
mount -a
```
## <a name="networking-requirements"></a>網路需求

若要啟用 Azure 磁碟加密功能，Linux VM 必須符合下列網路端點組態需求：
  - 若要取得用來連線至金鑰保存庫的權杖，Linux VM 必須能連線至 Azure Active Directory 端點 \[login.microsoftonline.com\]。
  - 若要將加密金鑰寫入您的金鑰保存庫，Linux VM 必須能連線至金鑰保存庫端點。
  - Linux VM 必須能連線至託管 Azure 擴充存放庫的 Azure 儲存體端點，和託管 VHD 檔案的 Azure 儲存體帳戶。
  -  如果您的安全性原則會限制從 Azure VM 至網際網路的存取，您可以解析前述的 URI，並設定特定的規則以允許和這些 IP 的輸出連線。 如需詳細資訊，請參閱[防火牆後方的 Azure Key Vault](../../key-vault/general/access-behind-firewall.md)。  

## <a name="encryption-key-storage-requirements"></a>加密金鑰儲存體需求  

Azure 磁碟加密需要 Azure Key Vault 來控制及管理磁碟加密金鑰與祕密。 您的金鑰保存庫和 VM 位於相同的 Azure 區域和訂閱中。

如需詳細資料，請參閱[建立及設定適用於 Azure 磁碟加密的金鑰保存庫](disk-encryption-key-vault.md)。

## <a name="terminology"></a>詞彙
下表定義 Azure 磁碟加密文件中所使用的一些常見詞彙：

| 詞彙 | 定義 |
| --- | --- |
| Azure 金鑰保存庫 | Key Vault 是一個密碼編譯金鑰管理服務，以「美國聯邦資訊處理標準」(FIPS) 已驗證的硬體安全性模組為基礎。 這些標準可協助您保護密碼編譯金鑰和敏感性祕密。 如需詳細資訊，請參閱 [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) 文件與[建立及設定適用於 Azure 磁碟加密的金鑰保存庫](disk-encryption-key-vault.md)。 |
| Azure CLI | [Azure CLI](/cli/azure/install-azure-cli) 已針對從命令列管理 Azure 資源進行最佳化。|
| DM-Crypt |[DM-Crypt](https://gitlab.com/cryptsetup/cryptsetup/wikis/DMCrypt) 是基於 Linux 的透明磁碟加密子系統，用來在 Linux VM 上啟用磁碟加密。 |
| 金鑰加密金鑰 (KEK) | 可用來保護或包裝密碼的非對稱金鑰 (RSA 2048)。 您可以提供硬體安全性模組 (HSM) 保護的金鑰或軟體保護的金鑰。 如需詳細資訊，請參閱 [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) 文件與[建立及設定適用於 Azure 磁碟加密的金鑰保存庫](disk-encryption-key-vault.md)。 |
| PowerShell Cmdlet | 如需詳細資訊，請參閱 [Azure PowerShell Cmdlet](/powershell/azure/overview)。 |


## <a name="next-steps"></a>後續步驟

- [快速入門 - 使用 Azure CLI 來建立和加密 Linux VM](disk-encryption-cli-quickstart.md)
- [快速入門 - 使用 Azure Powershell 建立和加密 Linux 虛擬機器](disk-encryption-powershell-quickstart.md)
- [Linux VM 上的 Azure 磁碟加密案例](disk-encryption-linux.md)
- [Azure 磁碟加密的必要 CLI 指令碼](https://github.com/ejarvi/ade-cli-getting-started)
- [Azure 磁碟加密的必要 PowerShell 指令碼](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts)
- [建立及設定適用於 Azure 磁碟加密的金鑰保存庫](disk-encryption-key-vault.md)
