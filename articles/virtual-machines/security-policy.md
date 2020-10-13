---
title: 保護和使用原則
description: 瞭解 Azure 中虛擬機器的安全性和原則。
author: cynthn
ms.service: virtual-machines
ms.subservice: security
ms.workload: infrastructure-services
ms.date: 11/27/2018
ms.author: cynthn
ms.topic: conceptual
ms.openlocfilehash: 1c2156975eb8d8dcb3580603dfbe1f5d1390b79a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "88852475"
---
# <a name="secure-and-use-policies-on-virtual-machines-in-azure"></a>在 Azure 中的虛擬機器上保護及使用原則

請務必讓虛擬機器 (VM) 安全地用於您執行的應用程式。 保護 VM 可包含一或多項 Azure 服務和功能，其中涵蓋保護 VM 的存取權及保護資料的儲存體。 本文提供可讓您保護 VM 和應用程式的資訊。

## <a name="antimalware"></a>反惡意程式碼

現今雲端環境的威脅型態非常多變，因此為了符合法規和達到安全性需求，在維護有效保護機制方面增加許多壓力。 [適用於 Azure 的 Microsoft Antimalware](../security/fundamentals/antimalware.md) 是即時保護功能，有助於識別和移除病毒、間諜軟體和其他惡意軟體。 您可設定警示，在已知惡意或非必要軟體嘗試自行安裝或在您的 VM 上執行時通知您。 執行 Linux 或 Windows Server 2008 的 Vm 不支援此功能。

## <a name="azure-security-center"></a>Azure 資訊安全中心

[Azure 資訊安全中心](../security-center/security-center-intro.md)可協助您保護 VM、偵測威脅並採取相應的措施。 資訊安全中心能提供您 Azure 訂用帳戶之間的整合式安全性監視和原則管理，協助偵測原先可能會忽略的威脅，且適用於廣泛的安全性解決方案生態系統。

您可以在 VM 部署上套用安全中心的即時存取，以鎖定 Azure Vm 的輸入流量、降低暴露于攻擊的風險，同時在需要時提供簡單的存取權來連線至 Vm。 已啟用 Just-In-Time 且使用者要求存取 VM 時，資訊安全中心會檢查使用者擁有此 VM 的哪些權限。 如果其擁有正確權限，則要求會通過核准，且資訊安全中心會將網路安全性群組 (NSG) 自動設定為在有限的時間內允許輸入流量進入選取的連接埠。 時間到期之後，資訊安全中心會將 NSG 還原為其先前的狀態。 

## <a name="encryption"></a>加密

受控磁片提供兩種加密方法。 在作業系統層級加密（Azure 磁碟加密），以及平台層級的加密，也就是伺服器端加密。

### <a name="server-side-encryption"></a>伺服器端加密

Azure 受控磁碟依預設會在將資料保存到雲端時，自動加密您的資料。 伺服器端加密可保護您的資料，並協助您符合組織的安全性和合規性承諾。 Azure 受控磁碟中的資料會使用 256 位元的 [AES 加密](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) (可用的最強區塊編碼器之一)，以透明的方式進行加密，而且符合 FIPS 140-2 規範。

加密不會影響受控磁片的效能。 加密沒有額外的費用。

您可以依賴由平台管理的金鑰來加密受控磁碟，也可以使用您自己的金鑰來管理加密。 如果您選擇使用自己的金鑰來管理加密，您可以指定*客戶管理的金鑰*，以用於加密和解密受控磁碟中的所有資料。 

若要深入瞭解伺服器端加密，請參閱 [Windows](windows/disk-encryption.md) 或 [Linux](linux/disk-encryption.md)的文章。

### <a name="azure-disk-encryption"></a>Azure 磁碟加密

如需強化 [Windows VM](windows/disk-encryption-overview.md) 和 [Linux VM](linux/disk-encryption-overview.md) 安全性與合規性，可以將 Azure 中的虛擬磁碟加密。 Windows VM 上的虛擬磁碟在待用時是使用 BitLocker 進行加密。 Linux VM 上的待用虛擬磁碟會使用 dm-crypt 進行加密。 

將 Azure 中的虛擬磁碟加密完全免費。 密碼編譯金鑰會使用軟體保護功能儲存在 Azure 金鑰保存庫中，或者您可以在 FIPS 140-2 第 2 級標準認證的硬體安全性模組 (HSM) 中匯入或產生金鑰。 這些密碼編譯金鑰用來加密及解密連接到 VM 的虛擬磁碟。 您可保留這些密碼編譯金鑰的控制權，並可稽核其使用情況。 Azure Active Directory 服務主體會提供一個安全機制，以便在 VM 開機或關機時發出這些密碼編譯金鑰。

## <a name="key-vault-and-ssh-keys"></a>Key Vault 和 SSH 金鑰

祕密和憑證均可模型化為資源並由 [Key Vault](../key-vault/general/basic-concepts.md) 提供。 您可以使用 Azure PowerShell 來建立適用於 [Windows VM](windows/key-vault-setup.md) 的金鑰保存庫以及適用於 [Linux VM](linux/key-vault-setup.md) 的 Azure CLI。 您也可以建立用於加密的金鑰。

金鑰保存庫存取原則可分別授與金鑰、祕密和憑證的權限。 例如，您可以只提供金鑰存取權給使用者，但不提供密碼的權限。 不過，金鑰、密碼或憑證的存取權限是在保存庫層級。 換句話說， [key vault 存取原則](../key-vault/general/secure-your-key-vault.md) 不支持對象層級許可權。

當您連線到 VM 時，您應該使用公開金鑰加密來提供更安全的登入方式。 此程序包括使用安全殼層 (SSH) 命令 (而非使用者名稱和密碼) 來驗證自己的公用和私密金鑰交換。 密碼很容易遭受暴力密碼破解攻擊，尤其是在網際網路對向 VM (例如 Web 伺服器) 上。 您可以利用安全殼層 (SSH) 金鑰組，在 Azure 上建立使用 SSH 金鑰來進行驗證的 [Linux VM](linux/mac-create-ssh-keys.md)，進而免除登入密碼的需求。 您也可以使用 SSH 金鑰從 [Windows VM](linux/ssh-from-windows.md) 連線至 Linux VM。

## <a name="managed-identities-for-azure-resources"></a>適用於 Azure 資源的受控識別

建置雲端應用程式常見的難題是如何管理程式碼中的認證，以向雲端服務進行驗證。 保護好這些認證是相當重要的工作。 在理想情況下，這些認證永不會出現在開發人員工作站且簽入原始程式碼控制。 Azure Key Vault 可安全地儲存認證、祕密和其他金鑰，但是您的程式碼必須向 Key Vault 進行驗證，才可擷取這些項目。 

Azure Active Directory (Azure AD) 中適用於 Azure 資源的受控識別功能可解決此問題。 這項功能會在 Azure AD 中將自動受控識別提供給 Azure 服務。 您可以使用此身分識別來完成任何支援 Azure AD 驗證的服務驗證 (包括 Key Vault)，不需要您程式碼中的任何認證。  您在 VM 上執行的程式碼可以向僅可從 VM 內存取的兩個端點要求權杖。 如需關於這項服務的詳細資訊，請檢閱 [Azure 資源的受控識別](../active-directory/managed-identities-azure-resources/overview.md)概觀頁面。   

## <a name="policies"></a>原則

[Azure 原則](../governance/policy/overview.md)可用來為您組織的 [Windows VM](./windows/policy.md) 和 [Linux VM](./linux/policy.md) 定義所要的行為。 藉由使用原則，組織可以強制執行整個企業的各種慣例和規則。 強制執行所要的行為有助於降低風險，同時促進組織的成功。

## <a name="role-based-access-control"></a>角色型存取控制

使用 [azure 角色型存取控制 (AZURE RBAC) ](../role-based-access-control/overview.md)，您可以將小組內的職責區隔，並只授與 VM 上執行其工作所需的使用者存取權。 您不需為每個人授與 VM 的權限，而是只允許執行特定的動作。 您可以使用 [Azure CLI](https://docs.microsoft.com/cli/azure/role) 或 [Azure PowerShell](../role-based-access-control/role-assignments-powershell.md)，在 [Azure 入口網站](../role-based-access-control/role-assignments-portal.md)中設定 VM 的存取控制。


## <a name="next-steps"></a>接下來的步驟
- 逐步執行步驟，以使用 Azure 資訊安全中心來監視 [Linux](../security/fundamentals/overview.md) 或 [Windows](windows/tutorial-azure-security.md) 虛擬機器安全性。

