---
title: 針對 Azure Site Recovery 中的 Azure VM 複寫進行疑難排解
description: 針對損毀修復複寫 Azure 虛擬機器時的錯誤進行疑難排解。
author: rochakm
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 04/08/2019
ms.author: rochakm
ms.openlocfilehash: 32d826f3c27cea3d0993c47e8562360315b7bd2e
ms.sourcegitcommit: d4a4f22f41ec4b3003a22826f0530df29cf01073
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/03/2020
ms.locfileid: "78256052"
---
# <a name="troubleshoot-azure-to-azure-vm-replication-errors"></a>針對 Azure 至 Azure VM 複寫錯誤進行疑難排解

本文說明如何在 Azure 虛擬機器（Vm）從一個區域複寫和復原時，針對 Azure Site Recovery 中的常見錯誤進行疑難排解。 如需受支援組態的詳細資訊，請參閱[複寫 Azure VM 的支援矩陣](site-recovery-support-matrix-azure-to-azure.md)。

## <a name="azure-resource-quota-issues-error-code-150097"></a>Azure 資源配額問題 (錯誤碼 150097)

請確定您的訂用帳戶已啟用，以便在目的地區域中建立 Azure Vm，而您打算將其做為災難復原區域。 此外，請確定您的訂用帳戶有足夠的配額可建立所需大小的 Vm。 根據預設，Site Recovery 會選擇與來源 VM 大小相同的目標 VM 大小。 如果無法使用相符的大小，Site Recovery 會自動選擇最接近的可用大小。

如果沒有可支援來源 VM 設定的大小，則會出現下列訊息：

> 「無法為虛擬機器*VmName*啟用複寫。」

### <a name="possible-causes"></a>可能的原因

- 您的訂用帳戶識別碼未啟用，無法在目的地區域位置中建立任何 Vm。
- 您的訂用帳戶識別碼未啟用，或沒有足夠的配額，無法在目的地區域位置中建立特定的 VM 大小。
- 找不到適合的目標 VM 大小，以符合來源 VM 的網路介面卡（NIC）計數（2），適用于目的地區域位置中的訂用帳戶識別碼。

### <a name="fix-the-problem"></a>修正問題

請聯絡[Azure 計費支援](https://docs.microsoft.com/azure/azure-portal/supportability/resource-manager-core-quotas-request)，讓您的訂用帳戶在目標位置中建立所需大小的 vm。 然後，重試失敗的操作。

如果目標位置具有容量條件約束，請停用對它的複寫。 然後，啟用複寫至您的訂用帳戶具有足夠配額的不同位置，以建立所需大小的 Vm。

## <a name="trusted-root-certificates-error-code-151066"></a>受信任的根憑證 (錯誤碼 151066)

如果 VM 上未出現所有最新的受信任根憑證，您的「啟用複寫」 Site Recovery 作業可能會失敗。 如果沒有這些憑證，驗證和授權來自 VM 的 Site Recovery 服務呼叫就會失敗。 

如果「啟用複寫」作業失敗，則會出現下列訊息：

> 「Site Recovery 設定失敗。」

### <a name="possible-cause"></a>可能的原因

授權和驗證所需的受信任根憑證不存在於虛擬機器上。

### <a name="fix-the-problem"></a>修正問題

#### <a name="windows"></a>Windows

針對執行 Windows 作業系統的 VM，請在 VM 上安裝最新的 Windows 更新，讓所有受信任的根憑證都存在於電腦上。 遵循您組織中的一般 Windows 更新管理或憑證更新管理程式，以取得最新的根憑證以及 Vm 上更新的憑證撤銷清單。

如果您是在中斷連線的環境，請遵循您組織的標準 Windows 更新程序來取得憑證。 如果 VM 上不存在所需的憑證，對於 Site Recovery 進行的呼叫將而於安全性因素而失敗。

若要確認問題已解決，請在 VM 中從瀏覽器移至 login.microsoftonline.com。

如需詳細資訊，請參閱[設定信任的根目錄和不允許的憑證](https://technet.microsoft.com/library/dn265983.aspx)。

#### <a name="linux"></a>Linux

請遵循 Linux 作業系統版本的散發者所提供的指導方針，以取得最新的受信任根憑證以及 VM 上最新的憑證撤銷清單。

因為 SUSE Linux 使用符號連結（或*符號連結*）來維護憑證清單，請遵循下列步驟：

1. 以根使用者身分登入。

1. 執行此命令以變更目錄：

    **# cd/etc/ssl/certs**

1. 檢查 Symantec 根 CA 憑證是否存在：

    **# ls VeriSign_Class_3_Public_Primary_Certification_Authority_G5 pem**

1. 如果找不到 Symantec 根 CA 憑證，請執行下列命令來下載檔案。 檢查是否有任何錯誤，並遵循建議的網路失敗動作。

    **# wget https://docs.broadcom.com/docs-and-downloads/content/dam/symantec/docs/other-resources/verisign-class-3-public-primary-certification-authority-g5-en.pem-O VeriSign_Class_3_Public_Primary_Certification_Authority_G5 pem**

1. 檢查巴爾的摩根 CA 憑證是否存在：

    **# ls Baltimore_CyberTrust_Root pem**

1. 如果找不到巴爾的摩根 CA 憑證，請執行下列命令來下載憑證：

    **# wget https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem-O Baltimore_CyberTrust_Root pem**

1. 檢查 DigiCert_Global_Root_CA 憑證是否存在：

    **# ls DigiCert_Global_Root_CA pem**

1. 如果找不到 DigiCert_Global_Root_CA，請執行下列命令來下載憑證：

    **# wget http://www.digicert.com/CACerts/DigiCertGlobalRootCA.crt**

    **# openssl x509-in DigiCertGlobalRootCA-通知 der-outform pem-out DigiCert_Global_Root_CA pem**

1. 執行 rehash 腳本，以更新新下載之憑證的憑證主體雜湊：

    **# c_rehash**

1. 執行下列命令，以檢查是否已為憑證建立符號連結的主體雜湊：

    - 命令：

        **# ls-l |grep 巴爾的摩**

    - 輸出：

        `lrwxrwxrwx 1 root root   29 Jan  8 09:48 3ad48a91.0 -> Baltimore_CyberTrust_Root.pem`

        `-rw-r--r-- 1 root root 1303 Jun  5  2014 Baltimore_CyberTrust_Root.pem`

    - 命令：

        **# ls-l |grep VeriSign_Class_3_Public_Primary_Certification_Authority_G5**

    - 輸出：

        `-rw-r--r-- 1 root root 1774 Jun  5  2014 VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem`

        `lrwxrwxrwx 1 root root   62 Jan  8 09:48 facacbc6.0 -> VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem`

    - 命令：

        **# ls-l |grep DigiCert_Global_Root**

    - 輸出：

        `lrwxrwxrwx 1 root root   27 Jan  8 09:48 399e7759.0 -> DigiCert_Global_Root_CA.pem`

        `-rw-r--r-- 1 root root 1380 Jun  5  2014 DigiCert_Global_Root_CA.pem`

1. VeriSign_Class_3_Public_Primary_Certification_Authority_G5 檔案名建立檔案名為 b204d74a 的檔案複本：

    **# cp VeriSign_Class_3_Public_Primary_Certification_Authority_G5 pem b204d74a 0**

1. Baltimore_CyberTrust_Root 檔案名建立檔案名為653b494a 的檔案複本：

    **# cp Baltimore_CyberTrust_Root pem 653b494a 0**

1. DigiCert_Global_Root_CA 檔案名建立檔案名為3513523f 的檔案複本：

    **# cp DigiCert_Global_Root_CA pem 3513523f 0**

1. 檢查檔案是否存在：

    - 命令：

        **# ls-l 653b494a 0 b204d74a. 0 3513523f**

    - 輸出

        `-rw-r--r-- 1 root root 1774 Jan  8 09:52 3513523f.0`

        `-rw-r--r-- 1 root root 1303 Jan  8 09:52 653b494a.0`

        `-rw-r--r-- 1 root root 1774 Jan  8 09:52 b204d74a.0`

## <a name="outbound-connectivity-for-site-recovery-urls-or-ip-ranges-error-code-151037-or-151072"></a>Site Recovery URL 或 IP 範圍的輸出連線能力 (錯誤碼 151037 或 151072)

若要讓 Site Recovery 複寫能夠正常執行，從 VM 到特定的 Url 或 IP 範圍都需要輸出連線能力。 如果您的 VM 位於防火牆後方，或使用網路安全性群組 (NSG) 規則控制輸出連線能力，您可能會遇到下列其中一個問題。

### <a name="issue-1-failed-to-register-azure-virtual-machine-with-site-recovery-151195-br"></a>問題1：無法向 Site Recovery 註冊 Azure 虛擬機器（錯誤碼151195）

#### <a name="possible-cause"></a>可能的原因 

因為 DNS 解析失敗，所以無法建立 Site Recovery 端點的連接。

當您已故障處理虛擬機器，但無法從嚴重損壞修復（DR）區域連線到 DNS 伺服器時，此問題最常發生在重新保護期間發生。

#### <a name="fix-the-problem"></a>修正問題

如果您使用的是自訂 DNS，請確定 DNS 伺服器可從故障復原區域存取。 若要瞭解您是否有自訂 DNS，請在 VM 上移至 [嚴重損壞*修復網路*] > [ **DNS 伺服器**]。

![自訂 DNS 伺服器清單](./media/azure-to-azure-troubleshoot-errors/custom_dns.PNG)

嘗試從虛擬機器存取 DNS 伺服器。 如果無法存取伺服器，可透過容錯移轉 DNS 伺服器，或在 DR 網路和 DNS 之間建立這一行的網站，讓它可以存取。

### <a name="issue-2-site-recovery-configuration-failed-error-code-151196"></a>問題2： Site Recovery 設定失敗（錯誤碼151196）

#### <a name="possible-cause"></a>可能的原因

無法建立與 Office 365 驗證和身分識別 IP4 端點的連接。

#### <a name="fix-the-problem"></a>修正問題

Site Recovery 需要存取 Office 365 IP 範圍以進行驗證。
如果您使用 Azure NSG 規則或防火牆 proxy 來控制 VM 上的輸出網路連線能力，請確定您允許與 Office 365 IP 範圍通訊。 建立以[Azure Active Directory （Azure AD）服務標記](../virtual-network/security-overview.md#service-tags)為基礎的 NSG 規則，以允許存取對應至 Azure AD 的所有 IP 位址。 如果未來將新的位址新增至 Azure AD，您就必須建立新的 NSG 規則。

> [!NOTE]
> 如果 Vm 位於*標準*內部負載平衡器後方，則預設負載平衡器無法存取 OFFICE 365 IP 範圍（也就是 login.microsoftonline.com）。 將 [內部負載平衡器類型] 變更為 [*基本*] 或 [建立輸出存取]，如[設定負載平衡和輸出規則](https://aka.ms/lboutboundrulescli)一文所述。

### <a name="issue-3-site-recovery-configuration-failed-error-code-151197"></a>問題3： Site Recovery 設定失敗（錯誤碼151197）

#### <a name="possible-cause"></a>可能的原因

無法建立連接以 Site Recovery 服務端點。

#### <a name="fix-the-problem"></a>修正問題

Site Recovery 需要[SITE RECOVERY IP 範圍](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-about-networking#outbound-connectivity-for-ip-address-ranges)的存取權，視區域而定。 請確定可從虛擬機器存取所需的 IP 範圍。

### <a name="issue-4-azure-to-azure-replication-failed-when-the-network-traffic-goes-through-an-on-premises-proxy-server-error-code-151072"></a>問題4：當網路流量通過內部部署 proxy 伺服器時，Azure 到 Azure 的複寫失敗（錯誤碼151072）

#### <a name="possible-cause"></a>可能的原因

自訂 proxy 設定無效，而且 Site Recovery 行動服務代理程式未自動偵測 Internet Explorer 的 proxy 設定。

#### <a name="fix-the-problem"></a>修正問題

行動服務代理程式會從 Windows 上的 Internet Explorer 和 Linux 上的/etc/environment 偵測 proxy 設定。

如果您只想要為行動服務設定 proxy，您可以在下列位置的 Proxyinfo.conf 檔案中提供 proxy 詳細資料：

- **Linux**：/usr/local/InMage/config/
- **Windows**： C:\ProgramData\Microsoft Azure Site Recovery\Config

在 Proxyinfo.conf 中，以下列初始化檔案格式提供 proxy 設定：

> [*proxy*]

> 位址 = *http://1.2.3.4*

> 埠 =*567*


> [!NOTE]
> Site Recovery 行動服務代理程式僅支援*未經驗證的*proxy。

### <a name="more-information"></a>詳細資訊

若要指定[所需的 url](azure-to-azure-about-networking.md#outbound-connectivity-for-urls)或[所需的 IP 範圍](azure-to-azure-about-networking.md#outbound-connectivity-for-ip-address-ranges)，請遵循[關於 azure 中的網路功能至 azure](site-recovery-azure-to-azure-networking-guidance.md)複寫中的指引。

## <a name="disk-not-found-in-the-machine-error-code-150039"></a>在機器中找不到磁碟 (錯誤碼 150039)

必須先將連接到 VM 的新磁碟初始化。 如果找不到磁片，則會出現下列訊息：

> 「具有邏輯單元編號*LUN* *diskuri*的 Azure 資料磁片*DiskName* *DiskURI* ，並未對應到從具有相同 LUN 值的 VM 內回報的對應磁片。

### <a name="possible-causes"></a>可能的原因

- 已將新的資料磁片連結至 VM，但尚未初始化。
- VM 內的資料磁片未正確報告磁片連接至 VM 的邏輯單元編號（LUN）值。

### <a name="fix-the-problem"></a>修正問題

請確定資料磁片已初始化，然後再次嘗試操作。

- **Windows**：[附加並初始化新的磁片](https://docs.microsoft.com/azure/virtual-machines/windows/attach-managed-disk-portal)。

- **Linux**：[在 linux 中初始化新的資料磁片](https://docs.microsoft.com/azure/virtual-machines/linux/add-disk)。

若問題持續發生，請連絡支援服務。

## <a name="one-or-more-disks-are-available-for-protection-error-code-153039"></a>有一或多個磁片可供保護（錯誤碼153039）

### <a name="possible-causes"></a>可能的原因

- 在保護之後，最近已將一或多個磁片新增至虛擬機器。
- 在保護虛擬機器之後，一或多個磁片已初始化。

### <a name="fix-the-problem"></a>修正問題

若要讓 VM 的複寫狀態再次狀況良好，您可以選擇保護磁片或關閉警告。

#### <a name="to-protect-the-disks"></a>保護磁片

1. 移至 已複寫的**專案** > *VM 名稱* > **磁片**。
1. 選取未受保護的磁片，然後選取 [**啟用**複寫]：

    ![在 VM 磁片上啟用複寫](./media/azure-to-azure-troubleshoot-errors/add-disk.png)

#### <a name="to-dismiss-the-warning"></a>若要關閉警告

1. 移至 已複寫的**專案** > *VM 名稱*。
1. 在 [**總覽**] 區段中選取警告，然後選取 **[確定]** 。

    ![關閉新的磁片警告](./media/azure-to-azure-troubleshoot-errors/dismiss-warning.png)

## <a name="remove-the-virtual-machine-from-the-vault-completed-with-information-error-code-150225"></a>從保存庫移除虛擬機器，並提供資訊（錯誤碼150225）

當它保護虛擬機器時，Site Recovery 會在來源虛擬機器上建立一些連結。 當您移除保護或停用複寫時，Site Recovery 會在清除作業中移除這些連結。 如果虛擬機器具有資源鎖定，則清除作業會以資訊完成。 此資訊指出已從復原服務保存庫中移除虛擬機器，但無法在來源電腦上清除某些過時的連結。

如果您不想要再次保護此虛擬機器，可以忽略此警告。 但是，如果您稍後必須保護此虛擬機器，請遵循「修正問題」底下的步驟來清除連結。

> [!WARNING]
> 如果您不進行清除：
>
> - 當您透過復原服務保存庫來啟用複寫時，將不會列出虛擬機器。
> - 如果您嘗試使用**虛擬機器** > **設定** > 嚴重損壞**修復**來保護 VM，此作業將會失敗，並顯示「無法啟用複寫，因為 VM 上的現有過時資源連結」。

### <a name="fix-the-problem"></a>修正問題

> [!NOTE]
> Site Recovery 在執行這些步驟時，不會刪除來源虛擬機器或以任何方式影響它。

1. 從 VM 或 VM 資源群組中移除鎖定。 例如，在下圖中，必須刪除名為 "MoveDemo" 之 VM 上的資源鎖定：

    ![從 VM 移除鎖定](./media/site-recovery-azure-to-azure-troubleshoot/vm-locks.png)

1. 下載腳本以[移除過時的 Site Recovery](https://github.com/AsrOneSdk/published-scripts/blob/master/Cleanup-Stale-ASR-Config-Azure-VM.ps1)設定。
1. 執行名為 Cleanup-stale-asr-config-Azure-VM 的腳本。 提供訂用帳戶識別碼、VM 資源群組和 VM 名稱作為參數。
1. 如果您要求 Azure 認證，請提供它們。 然後驗證腳本是否執行，而不會發生任何失敗。

## <a name="replication-cant-be-enabled-because-of-stale-resource-links-on-the-vm-error-code-150226"></a>因為 VM 上的資源連結過時，所以無法啟用複寫（錯誤碼150226）

### <a name="possible-cause"></a>可能的原因

虛擬機器具有先前 Site Recovery 保護的過時設定。

如果您使用 Site Recovery 來啟用 Azure VM 的複寫，則 Azure VM 上可能會發生過時的設定，然後：

- 您已停用複寫，但來源 VM 具有資源鎖定。
- 您已刪除 Site Recovery 保存庫，但未明確停用 VM 上的複寫。
- 您已刪除包含 Site Recovery 保存庫的資源群組，但未明確停用 VM 上的複寫。

### <a name="fix-the-problem"></a>修正問題

> [!NOTE]
> Site Recovery 在執行這些步驟時，不會刪除來源虛擬機器或以任何方式影響它。

1. 從 VM 或 VM 資源群組中移除鎖定。 例如，在下圖中，必須刪除名為 "MoveDemo" 之 VM 上的資源鎖定：

    ![從 VM 移除鎖定](./media/site-recovery-azure-to-azure-troubleshoot/vm-locks.png)

1. 下載腳本以[移除過時的 Site Recovery](https://github.com/AsrOneSdk/published-scripts/blob/master/Cleanup-Stale-ASR-Config-Azure-VM.ps1)設定。
1. 執行名為 Cleanup-stale-asr-config-Azure-VM 的腳本。 提供訂用帳戶識別碼、VM 資源群組和 VM 名稱作為參數。
1. 如果您要求 Azure 認證，請提供它們。 然後驗證腳本是否執行，而不會發生任何失敗。

## <a name="unable-to-see-the-azure-vm-or-resource-group-for-the-selection-in-the-enable-replication-job"></a>在「啟用複寫」作業中，無法看到選取的 Azure VM 或資源群組

### <a name="cause-1-the-resource-group-and-source-virtual-machine-are-in-different-locations"></a>原因1：資源群組和來源虛擬機器位於不同的位置

Site Recovery 目前需要來源區域資源群組和虛擬機器位於相同的位置。 如果不是，當您嘗試套用保護時，將無法找到虛擬機器或資源群組。

因應措施是，您可以從 VM 啟用複寫，而不是復原服務保存庫。 移至 [**來源 VM** ] > [**屬性**] > 嚴重損壞**修復**並啟用複寫。

### <a name="cause-2-the-resource-group-is-not-part-of-the-selected-subscription"></a>原因2：資源群組不屬於所選訂用帳戶

如果資源群組不屬於所選的訂用帳戶，您可能會在保護時找不到資源群組。 請確定資源群組屬於您所使用的訂用帳戶。

### <a name="cause-3-stale-configuration"></a>原因3：過時的設定

如果 Azure VM 上已有過時的 Site Recovery 設定，您可能看不到想要啟用複寫的 VM。 如果您使用 Site Recovery 來啟用 Azure VM 的複寫，則會發生此狀況，然後：

- 您已刪除 Site Recovery 保存庫，但未明確停用 VM 上的複寫。
- 您已刪除包含 Site Recovery 保存庫的資源群組，但未明確停用 VM 上的複寫。
- 您已停用複寫，但來源 VM 具有資源鎖定。

### <a name="fix-the-problem"></a>修正問題

> [!NOTE]
> 請務必先更新 "AzureRM" 模組，再使用本節所述的腳本。  Site Recovery 在執行這些步驟時，不會刪除來源虛擬機器或以任何方式影響它。

1. 從 VM 或 VM 資源群組中移除鎖定（如果有的話）。 例如，在下圖中，必須刪除名為 "MoveDemo" 之 VM 上的資源鎖定：

    ![從 VM 移除鎖定](./media/site-recovery-azure-to-azure-troubleshoot/vm-locks.png)

1. 下載腳本以[移除過時的 Site Recovery](https://github.com/AsrOneSdk/published-scripts/blob/master/Cleanup-Stale-ASR-Config-Azure-VM.ps1)設定。
1. 執行名為 Cleanup-stale-asr-config-Azure-VM 的腳本。 提供訂用帳戶識別碼、VM 資源群組和 VM 名稱作為參數。
1. 如果您要求 Azure 認證，請提供它們。 然後驗證腳本是否執行，而不會發生任何失敗。

## <a name="unable-to-select-a-virtual-machine-for-protection"></a>無法選取要保護的虛擬機器

### <a name="cause-1-the-virtual-machine-has-an-extension-installed-in-a-failed-or-unresponsive-state"></a>原因1：虛擬機器在失敗或沒有回應的狀態中安裝了擴充功能

移至 [**虛擬機器**] > **設定** > **延伸**模組，並檢查是否有任何處于失敗狀態的延伸模組。 請卸載任何失敗的延伸模組，然後再試一次來保護虛擬機器。

### <a name="cause-2-the-vms-provisioning-state-is-not-valid"></a>原因2： VM 的布建狀態無效

請參閱本文稍後的[VM](#the-vms-provisioning-state-is-not-valid-error-code-150019)布建狀態中的疑難排解步驟無效。

## <a name="the-vms-provisioning-state-is-not-valid-error-code-150019"></a>VM 的布建狀態無效（錯誤碼150019）

若要在 VM 上啟用複寫，其布建狀態必須是 [**成功**]。 請遵循下列步驟來檢查布建狀態：

1. 在 Azure 入口網站中，選取 **所有服務** 中的**資源總管**。
1. 展開 [訂用帳戶] 清單然後選取您的訂用帳戶。
1. 展開 [ResourceGroups] 清單然後選取 VM 的資源群組。
1. 展開 [**資源**] 清單並選取您的 VM。
1. 檢查右側實例視圖中的 [ **provisioningState** ] 欄位。

### <a name="fix-the-problem"></a>修正問題

- 如果 [provisioningState] 是「失敗」，請連絡支援人員以取得疑難排解的詳細資訊。
- 如果正在**更新** **provisioningState** ，可能會部署另一個延伸模組。 檢查 VM 上是否有任何進行中的作業，等待它們完成，然後重試失敗的 Site Recovery 「啟用複寫」作業。

## <a name="unable-to-select-target-vm-network-selection-tab-is-unavailable"></a>無法選取目標 VM （無法使用 [網路選取] 索引標籤）

### <a name="cause-1-your-vm-is-attached-to-a-network-thats-already-mapped-to-a-target-network"></a>原因1：您的 VM 已連接到已對應到目標網路的網路

如果來源 VM 是虛擬網路的一部分，而且來自相同虛擬網路的另一個 VM 已與目標資源群組中的網路對應，則 [網路選取] 下拉式清單方塊預設為 [無法使用] （呈現暗灰色）。

![網路選取清單無法使用](./media/site-recovery-azure-to-azure-troubleshoot/unabletoselectnw.png)

### <a name="cause-2-you-previously-protected-the-vm-by-using-site-recovery-and-then-you-disabled-the-replication"></a>原因2：您先前使用 Site Recovery 來保護 VM，然後停用複寫

停用 VM 的複寫不會刪除網路對應。 必須從已保護 VM 的復原服務保存庫中刪除對應。 前往 復原*服務保存庫* > **Site Recovery 基礎結構** > **網路對應**。

![刪除網路對應](./media/site-recovery-azure-to-azure-troubleshoot/delete_nw_mapping.png)

在損毀修復設定期間設定的目標網路，可以在 VM 受到保護之後，于初始設定之後變更：

![修改網路對應](./media/site-recovery-azure-to-azure-troubleshoot/modify_nw_mapping.png)

請注意，變更網路對應會影響所有使用相同網路對應的受保護 Vm。

## <a name="com-or-volume-shadow-copy-service-error-error-code-151025"></a>COM + 或磁片區陰影複製服務錯誤（錯誤碼151025）

發生此錯誤時，會出現下列訊息：

> 「無法安裝 Site Recovery 延伸模組」

### <a name="possible-causes"></a>可能的原因

- COM + 系統應用程式服務已停用。
- 已停用磁片區陰影複製服務。

### <a name="fix-the-problem"></a>修正問題

將「COM + 系統應用程式」和「磁片區陰影複製服務」設定為自動或手動啟動模式。

1. 在 Windows 中開啟 [服務] 主控台。
1. 請確定 COM + 系統應用程式和磁片區陰影複製服務未設定為**停用**，作為其**啟動類型**。

    ![檢查 COM + 系統應用程式和磁片區陰影複製服務的啟動類型](./media/azure-to-azure-troubleshoot-errors/com-error.png)

## <a name="unsupported-managed-disk-size-error-code-150172"></a>不受支援的受控磁片大小（錯誤碼150172）

發生此錯誤時，會出現下列訊息：

> 「無法啟用虛擬機器的保護，因為它具有*DiskSize*大小的*DiskName* ） * 小於支援的最小大小 1024 MB」。

### <a name="possible-cause"></a>可能的原因

磁片小於支援的大小 1024 MB。

### <a name="fix-the-problem"></a>修正問題

請確定磁片大小在支援的大小範圍內，然後再次嘗試操作。

## <a name="enable-protection-failed-as-device-name-mentioned-in-the-grub-configuration-instead-of-uuid-error-code-151126"></a>因為 GRUB 設定包含裝置名稱，而不是 UUID （錯誤碼151126），所以未啟用保護

### <a name="possible-cause"></a>可能的原因

Linux GRUB 設定檔（/boot/grub/menu.lst "、/boot/grub/grub.cfg、/boot/grub2/grub.cfg 或/etc/default/grub）可能會指定實際的裝置名稱，而不是*root*和*RESUME*參數的 UUID 值。 Site Recovery 需要 Uuid，因為裝置名稱可能會變更。 重新開機時，VM 在容錯移轉時可能不會有相同的名稱，因而產生問題。

下列範例是來自 GRUB 檔案的線條，其中裝置名稱（以粗體顯示）會出現，而不是所需的 Uuid：

- 檔案/boot/grub2/grub.cfg

  > linux/boot/vmlinuz-3.12.49-11-default**根目錄 =/dev/sda2** $ {extra_cmdline} **resume =/dev/sda1**啟動顯示 = 無訊息安靜 showopts

- 檔案：/boot/grub/menu.lst

  > 核心/boot/vmlinuz-3.0.101-63-default**根目錄 =/dev/sda2** **resume =/dev/sda1**啟動顯示 = 無訊息 crashkernel = 256M-： 128M showopts vga = 0x314


### <a name="fix-the-problem"></a>修正問題

將每個裝置名稱取代為對應的 UUID：

1. 執行命令**blkid** ***device name***來尋找裝置的 UUID。 例如，

    ```
    blkid /dev/sda1
    /dev/sda1: UUID="6f614b44-433b-431b-9ca1-4dd2f6f74f6b" TYPE="swap"
    blkid /dev/sda2
    /dev/sda2: UUID="62927e85-f7ba-40bc-9993-cc1feeb191e4" TYPE="ext3"
   ```

1. 將裝置名稱取代為其 UUID，格式為**root = UUID**=*UUID*和**resume = uuid**=*uuid*。 例如，在取代之後，來自/boot/grub/menu.lst 的程式列（稍早討論）看起來會像這樣：

    > kernel/boot/vmlinuz-3.0.101-63-default **root = UUID = 62927e85-f7ba-40bc-9993-cc1feeb191e4** **resume = UUID = 6f614b44-433b-431b-9ca1-4dd2f6f74f6b**啟動顯示 = 無訊息 crashkernel = 256M-： 128M showopts vga = 0x314

1. 請重試保護。

## <a name="enable-protection-failed-because-the-device-mentioned-in-the-grub-configuration-doesnt-exist-error-code-151124"></a>啟用保護失敗，因為 GRUB 設定中提及的裝置不存在（錯誤碼151124）

### <a name="possible-cause"></a>可能的原因

GRUB 設定檔案（/boot/grub/menu.lst、/boot/grub/grub.cfg、/boot/grub2/grub.cfg 或/etc/default/grub）可能包含參數*rd.lvm.lv*或*rd_LVM_LV*。 這些參數會識別在開機時要探索的邏輯磁片區管理員（LVM）裝置。 如果這些 LVM 裝置不存在，受保護的系統本身將無法開機，且會停滯在開機程式中。 容錯移轉 VM 也會看到相同的問題。 以下提供一些範例：

- File：/boot/grub2/grub.cfg on RHEL7：

    > linux16/vmlinuz-3.10.0-957.el7. x86_64 root =/dev/mapper/rhel_mup--rhel7u6-root ro crashkernel = 128M\@Ed-64m 的**rd. lv = rootvg/root rd。 lv = rootvg/swap** RHGB quiet LANG = en_US。UTF-8

- File：/etc/default/grub on RHEL7：

    > GRUB_CMDLINE_LINUX = "crashkernel = auto **-lvm. lv = rootvg/root rd。 lv = rootvg/swap** rhgb quiet"

- File：/boot/grub/menu.lst on RHEL6：

    > 核心/vmlinuz-2.6.32-754.el6. x86_64 ro root = UUID = 36dd8b45-e90d-40d6-81ac-ad0d0725d69e rd_NO_LUKS LANG = en_US。UTF-8 rd_NO_MD SYSFONT = latarcyrheb-sun16 crashkernel = auto **rd_LVM_LV = rootvg/lv_root** KEYBOARDTYPE = pc KEYTABLE = us **rd_LVM_LV = rootvg/lv_swap** rd_NO_DM rhgb quiet

在每個範例中，以粗體顯示的部分，就表示 GRUB 必須從磁片區群組 "rootvg" 偵測名為 "root" 和 "swap" 的兩個 LVM 裝置。

### <a name="fix-the-problem"></a>修正問題

如果 LVM 裝置不存在，請加以建立，或從 GRUB 設定檔移除對應的參數。 然後，再試一次以啟用保護。

## <a name="a-site-recovery-mobility-service-update-finished-with-warnings-error-code-151083"></a>Site Recovery 行動服務更新已完成，但出現警告（錯誤碼151083）

Site Recovery 行動服務有許多元件，其中一個稱為「篩選器」驅動程式。 篩選器驅動程式只會在系統重新開機期間載入系統記憶體中。 每當行動服務更新包含篩選器驅動程式變更時，電腦就會更新，但您仍然會看到一些修正程式需要重新開機的警告。 此時會出現警告，因為只有在載入新的篩選器驅動程式時，篩選器驅動程式修正才會生效，這只會在重新開機時才會發生。

> [!NOTE]
> 這只是警告。 即使在新的代理程式更新之後，現有的複寫仍會繼續運作。 當您想要新篩選器驅動程式的優點時，可以選擇重新開機，但如果您不重新開機，舊的篩選器驅動程式會繼續運作。
>
> 除了篩選驅動程式之外，行動服務更新中任何其他增強功能和修正的優點也會生效，而不需要重新開機。  

## <a name="protection-couldnt-be-enabled-because-the-replica-managed-disk-already-exists-without-expected-tags-in-the-target-resource-group-error-code-150161"></a>無法啟用保護，因為目標資源群組中的複本受控磁片已經存在，但沒有需要的標記（錯誤碼150161）

### <a name="possible-cause"></a>可能的原因

如果虛擬機器先前已受到保護，而且在停用複寫時未清除複本磁片，就會發生此問題。

### <a name="fix-the-problem"></a>修正問題

刪除錯誤訊息中識別的複本磁片，然後重試失敗的保護工作。

## <a name="next-steps"></a>後續步驟

[複寫 Azure 虛擬機器](site-recovery-replicate-azure-to-azure.md)
