---
title: 為 Windows VM 啟用 Azure 磁碟加密
description: 本文提供有關為 Windows VM 啟用 Microsoft Azure 磁碟加密的說明。
author: msmbaldwin
ms.service: virtual-machines-windows
ms.subservice: security
ms.topic: article
ms.author: mbaldwin
ms.date: 10/05/2019
ms.custom: seodec18
ms.openlocfilehash: 8bed34e816207c9f0bd0565abab6af4adbaeb7fd
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2020
ms.locfileid: "82081637"
---
# <a name="azure-disk-encryption-for-windows-vms"></a>適用於 Windows VM 的 Azure 磁碟加密 

Azure 磁碟加密可協助您保護資料安全，以符合您組織的安全性和合規性承諾。 它使用 Windows 的[Bitlocker](https://en.wikipedia.org/wiki/BitLocker)功能為 Azure 虛擬機器 (VM) 的作業系統和資料磁碟提供卷加密,並與[Azure 金鑰保管庫](../../key-vault/index.yml)整合,以説明您控制和管理磁碟加密金鑰和機密。 

如果使用[Azure 安全中心](../../security-center/index.yml),則如果有未加密的 VM,則會收到警報。 這些警示會顯示為「高嚴重性」，而建議就是加密這些 VM。

![「Azure 資訊安全中心」磁碟加密警示](../media/disk-encryption/security-center-disk-encryption-fig1.png)

> [!WARNING]
> - 如果以前使用 Azure 磁碟加密與 Azure AD 一起加密 VM,則必須繼續使用此選項對 VM 進行加密。 有關詳細資訊[,請參閱 Azure 磁碟加密與 Azure AD(上一版本)。](disk-encryption-overview-aad.md) 
> - 某些建議可能會增加資料、網路或計算資源的使用量，導致額外的授權或訂用帳戶成本。 您必須擁有有效的作用中 Azure 訂用帳戶，才能在 Azure 支援的區域中建立資源。

只需使用[Azure CLI 快速入門建立和加密 Windows VM,](disk-encryption-cli-quickstart.md)即可在幾分鐘內瞭解 Windows Azure 磁碟加密[的基礎知識,或者使用 Azure Powershell 快速入門創建和加密 Windows VM。](disk-encryption-powershell-quickstart.md)

## <a name="supported-vms-and-operating-systems"></a>支援的 VM 和作業系統

### <a name="supported-vms"></a>支援的 VM

Windows VM 有多種[大小](sizes-general.md)可供選擇。 Azure 磁碟加密在基本[A 系列 VM](https://azure.microsoft.com/pricing/details/virtual-machines/series/)或記憶體小於 2 GB 的虛擬機上不可用。

Azure 磁碟加密也可用於具有進階儲存的 VM。

Azure 磁碟加密在第[2 代 VM](generation-2.md#generation-1-vs-generation-2-capabilities)和[Lsv2 系列 VM](../lsv2-series.md)上不可用。 有關更多例外情況,請參閱[Azure 磁碟加密:不受支援的方案](disk-encryption-windows.md#unsupported-scenarios)。

### <a name="supported-operating-systems"></a>支援的作業系統

- Windows 用戶端:Windows 8 及更高版本。
- Windows 伺服器:Windows 伺服器 2008 R2 及更高版本。  
 
> [!NOTE]
> Windows 伺服器 2008 R2 要求安裝 .NET 框架 4.5 進行加密;安裝它從Windows更新與可選更新微軟.NET框架4.5.2的Windows伺服器2008 R2 x64為基礎的系統[(KB2901983)。](https://www.catalog.update.microsoft.com/Search.aspx?q=KB2901983)  
>  
> Windows 伺服器 2012 R2 核心和 Windows 伺服器 2016 酷睿要求在 VM 上安裝 bdehdcfg 元件以進行加密。


## <a name="networking-requirements"></a>網路需求
要啟用 Azure 磁碟加密,VM 必須滿足以下網路終結點設定要求:
  - 要取得權杖以連接到金鑰保管庫,Windows VM 必須能夠連接到 Azure 活動目錄終結點,login.microsoftonline.com \[ \]。
  - 要將加密密鑰寫入密鑰保管庫,Windows VM 必須能夠連接到密鑰保管庫終結點。
  - Windows VM 必須能夠連接到承載 Azure 延伸儲存庫的 Azure 儲存終結點和承載 VHD 檔的 Azure 儲存帳戶。
  -  如果您的安全性原則會限制從 Azure VM 至網際網路的存取，您可以解析前述的 URI，並設定特定的規則以允許和這些 IP 的輸出連線。 如需詳細資訊，請參閱[防火牆後方的 Azure Key Vault](../../key-vault/general/access-behind-firewall.md)。    


## <a name="group-policy-requirements"></a>群組原則要求

Azure 磁碟加密使用 Windows VM 的 BitLocker 外部密鑰保護器。 對於加入網域的 VM，請勿推送任何會強制使用 TPM 保護裝置的群組原則。 有關「允許未相容 TPM 的 BitLocker」的組策略的資訊,請參閱[BitLocker 組策略參考](/windows/security/information-protection/bitlocker/bitlocker-group-policy-settings#bkmk-unlockpol1)。

具有自訂群組政策的網域加入虛擬機器上的 BitLocker 政策必須包括以下設定:[設定使用者儲存的 BitLocker 復原資訊 ->允许 256 位元修復金鑰](/windows/security/information-protection/bitlocker/bitlocker-group-policy-settings)。 當 BitLocker 的自訂群組原則設定不相容時，Azure 磁碟加密將會失敗。 在沒有正確原則設定的電腦上，您可能必須套用新的原則、強制新的原則進行更新 (gpupdate.exe /force)，然後重新啟動。

如果域級組策略阻止 BitLocker 使用的 AES-CBC 演演演算法,則 Azure 磁碟加密將失敗。

## <a name="encryption-key-storage-requirements"></a>加密金鑰儲存要求  

Azure 磁碟加密需要 Azure 密鑰保管庫來控制和管理磁碟加密密鑰和機密。 密鑰保管庫和 VM 必須駐留在同一 Azure 區域和訂閱中。

關於詳細資訊,請參考[建立與設定 Azure 磁碟加密的金鑰保管庫](disk-encryption-key-vault.md)。

## <a name="terminology"></a>詞彙
下表定義了 Azure 磁碟加密文件中使用的一些常用術語:

| 詞彙 | 定義 |
| --- | --- |
| Azure 金鑰保存庫 | Key Vault 是一個密碼編譯金鑰管理服務，以「美國聯邦資訊處理標準」(FIPS) 已驗證的硬體安全性模組為基礎。 這些標準可協助您保護密碼編譯金鑰和敏感性祕密。 關於詳細資訊,請參閱 Azure[金鑰保管庫](https://azure.microsoft.com/services/key-vault/)文件以及[建立與設定 Azure 磁碟加密的金鑰保管庫](disk-encryption-key-vault.md)。 |
| Azure CLI | [Azure CLI](/cli/azure/install-azure-cli) 已針對從命令列管理 Azure 資源進行最佳化。|
| BitLocker |[BitLocker](https://technet.microsoft.com/library/hh831713.aspx)是一種業界認可的 Windows 卷加密技術,用於在 Windows VM 上啟用磁碟加密。 |
| 金鑰加密金鑰 (KEK) | 可用於保護或包裝機密的不對稱密鑰 (RSA 2048)。 您可以提供硬體安全性模組 (HSM) 保護的金鑰或軟體保護的金鑰。 關於詳細資訊,請參閱 Azure[金鑰保管庫](https://azure.microsoft.com/services/key-vault/)文件以及[建立與設定 Azure 磁碟加密的金鑰保管庫](disk-encryption-key-vault.md)。 |
| PowerShell Cmdlet | 如需詳細資訊，請參閱 [Azure PowerShell Cmdlet](/powershell/azure/overview)。 |


## <a name="next-steps"></a>後續步驟

- [快速入門 ─使用 Azure CLI 建立與加密 Windows VM](disk-encryption-cli-quickstart.md)
- [快速入門 ─使用 Azure 電源外殼建立與加密 Windows VM](disk-encryption-powershell-quickstart.md)
- [Windows VM 上的 Azure 磁碟加密案例](disk-encryption-windows.md)
- [Azure 磁碟加密先決條件 CLI 文稿](https://github.com/ejarvi/ade-cli-getting-started)
- [Azure 磁碟加密先決條件 PowerShell 文稿](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts)
- [建立及設定適用於 Azure 磁碟加密的金鑰保存庫](disk-encryption-key-vault.md)


