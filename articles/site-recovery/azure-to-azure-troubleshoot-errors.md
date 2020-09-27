---
title: 針對 Azure Site Recovery 中的 Azure VM 複寫進行疑難排解
description: 針對複寫 Azure 虛擬機器以進行嚴重損壞修復時所發生的錯誤進行疑難排解。
author: rochakm
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 04/07/2020
ms.author: rochakm
ms.openlocfilehash: ab3597ddba3e41e88c8b2b575ed5857aca01e610
ms.sourcegitcommit: 4313e0d13714559d67d51770b2b9b92e4b0cc629
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/27/2020
ms.locfileid: "91397958"
---
# <a name="troubleshoot-azure-to-azure-vm-replication-errors"></a>對 Azure 至 Azure VM 的複寫錯誤進行疑難排解

本文說明如何針對在 Azure 虛擬機器 (VM) 從某個區域到另一個區域的複寫和復原期間，Azure Site Recovery 常見的錯誤進行疑難排解。 如需受支援組態的詳細資訊，請參閱[複寫 Azure VM 的支援矩陣](azure-to-azure-support-matrix.md)。

## <a name="azure-resource-quota-issues-error-code-150097"></a>Azure 資源配額問題 (錯誤碼 150097)

確定您的訂用帳戶已啟用，可在您打算用來作為嚴重損壞修復 (DR) 區域的目的地區域中建立 Azure Vm。 您的訂用帳戶需要足夠的配額，才能建立所需大小的 Vm。 根據預設，Site Recovery 會選擇與來源 VM 大小相同的目標 VM 大小。 如果無法使用相符的大小，Site Recovery 會自動選擇最接近的可用大小。

如果沒有支援來源 VM 設定的大小，則會顯示下列訊息：

```Output
Replication couldn't be enabled for the virtual machine <VmName>.
```

### <a name="possible-causes"></a>可能的原因

- 您的訂用帳戶識別碼未啟用，無法在目的地區域位置中建立任何 Vm。
- 您的訂用帳戶識別碼未啟用，或沒有足夠的配額，無法在目的地區域位置中建立特定的 VM 大小。
- 找不到適合的目標 VM 大小，以符合來源 VM 的網路介面卡 (NIC) 計數 (2) ）作為目的地區域位置中的訂用帳戶識別碼。

### <a name="fix-the-problem"></a>修正問題

請聯絡 [Azure 計費支援](../azure-portal/supportability/resource-manager-core-quotas-request.md) ，讓您的訂用帳戶在目標位置中建立所需大小的 vm。 然後，重試失敗的操作。

如果目標位置具有容量條件約束，請停用複寫至該位置。 然後，啟用複寫至您的訂用帳戶具有足夠配額的不同位置，以建立所需大小的 Vm。

## <a name="trusted-root-certificates-error-code-151066"></a>受信任的根憑證 (錯誤碼 151066)

如果不是所有最新的受信任根憑證都存在於 VM 上，啟用 Site Recovery 複寫的作業可能會失敗。 從 VM 進行 Site Recovery 服務呼叫的驗證與授權，在沒有這些憑證的情況下會失敗。

如果啟用複寫作業失敗，則會顯示下列訊息：

```Output
Site Recovery configuration failed.
```

### <a name="possible-cause"></a>可能的原因

虛擬機器不存在授權和驗證所需的受信任根憑證。

### <a name="fix-the-problem"></a>修正問題

#### <a name="windows"></a>Windows

若為執行 Windows 作業系統的 VM，請安裝最新的 Windows 更新，讓所有受信任的根憑證都存在於 VM 上。 遵循您組織中的一般 Windows update 管理或憑證更新管理程式，以取得最新的根憑證和 Vm 上更新的憑證撤銷清單。

- 如果您是在中斷連線的環境，請遵循您組織的標準 Windows 更新程序來取得憑證。
- 如果 VM 上不存在所需的憑證，對於 Site Recovery 進行的呼叫將而於安全性因素而失敗。

若要確認問題是否已解決，請 `login.microsoftonline.com` 從 VM 中的瀏覽器移至。

如需詳細資訊，請參閱 [設定受信任的根目錄和不允許的憑證](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn265983(v=ws.11))。

#### <a name="linux"></a>Linux

依照您的 Linux 作業系統版本散發者提供的指引，取得最新的受信任根憑證以及 VM 上最新的憑證撤銷清單。

因為 SUSE Linux 使用符號連結或符號連結來維護憑證清單，所以請遵循下列步驟：

1. 以 **根** 使用者的身份登入。 雜湊符號 (`#`) 是預設的命令提示字元。

1. 若要變更目錄，請執行下列命令：

   `cd /etc/ssl/certs`

1. 檢查 Symantec 根 CA 憑證是否存在：

   `ls VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem`

   - 如果找不到 Symantec 根 CA 憑證，請執行下列命令來下載檔案。 檢查是否有任何錯誤，並遵循網路失敗的建議動作。

     `wget https://docs.broadcom.com/docs-and-downloads/content/dam/symantec/docs/other-resources/verisign-class-3-public-primary-certification-authority-g5-en.pem -O VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem`

1. 檢查是否有巴爾的摩根 CA 憑證存在：

   `ls Baltimore_CyberTrust_Root.pem`

   - 如果找不到巴爾的摩根 CA 憑證，請執行下列命令以下載憑證：

     `wget https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem -O Baltimore_CyberTrust_Root.pem`

1. 檢查 DigiCert_Global_Root_CA 憑證是否存在：

   `ls DigiCert_Global_Root_CA.pem`

    - 如果找不到 DigiCert_Global_Root_CA，請執行下列命令以下載憑證：

      ```shell
      wget http://www.digicert.com/CACerts/DigiCertGlobalRootCA.crt

      openssl x509 -in DigiCertGlobalRootCA.crt -inform der -outform pem -out DigiCert_Global_Root_CA.pem
      ```

1. 若要更新新下載憑證的憑證主體雜湊，請執行 rehash 腳本：

   `c_rehash`

1. 若要檢查憑證是否已建立符號連結的主體雜湊，請執行下列命令：

   ```shell
   ls -l | grep Baltimore
   ```

   ```Output
   lrwxrwxrwx 1 root root   29 Jan  8 09:48 3ad48a91.0 -> Baltimore_CyberTrust_Root.pem

   -rw-r--r-- 1 root root 1303 Jun  5  2014 Baltimore_CyberTrust_Root.pem
   ```

   ```shell
   ls -l | grep VeriSign_Class_3_Public_Primary_Certification_Authority_G5
   ```

   ```Output
   -rw-r--r-- 1 root root 1774 Jun  5  2014 VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem

   lrwxrwxrwx 1 root root   62 Jan  8 09:48 facacbc6.0 -> VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem
   ```

   ```shell
   ls -l | grep DigiCert_Global_Root
   ```

   ```Output
   lrwxrwxrwx 1 root root   27 Jan  8 09:48 399e7759.0 -> DigiCert_Global_Root_CA.pem

   -rw-r--r-- 1 root root 1380 Jun  5  2014 DigiCert_Global_Root_CA.pem
   ```

1. 使用檔案名 b204d74a 建立檔案的複本 _VeriSign_Class_3_Public_Primary_Certification_Authority_G5._ _0_：

   `cp VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem b204d74a.0`

1. 使用檔案名653b494a.0 建立檔案的複本 _Baltimore_CyberTrust_Root._ _0_：

   `cp Baltimore_CyberTrust_Root.pem 653b494a.0`

1. 使用檔案名3513523f 建立檔案的複本 _DigiCert_Global_Root_CA._ _0_：

   `cp DigiCert_Global_Root_CA.pem 3513523f.0`

1. 檢查檔案是否存在：

   ```shell
   ls -l 653b494a.0 b204d74a.0 3513523f.0
   ```

   ```Output
   -rw-r--r-- 1 root root 1774 Jan  8 09:52 3513523f.0

   -rw-r--r-- 1 root root 1303 Jan  8 09:52 653b494a.0

   -rw-r--r-- 1 root root 1774 Jan  8 09:52 b204d74a.0
   ```

## <a name="outbound-urls-or-ip-ranges-error-code-151037-or-151072"></a>輸出 Url 或 IP 範圍 (錯誤碼151037或 151072) 

若要讓 Site Recovery 複寫運作，VM 需要有特定 Url 的輸出連線能力。 如果您的 VM 位於防火牆後方，或使用網路安全性群組 (NSG) 規則控制輸出連線能力，您可能會遇到下列其中一個問題。 雖然我們持續支援透過 Url 進行的輸出存取，但不再支援使用 IP 範圍的允許清單。

#### <a name="possible-causes"></a>可能的原因

- 因為網域名稱系統 (DNS) 解析失敗，所以無法建立 Site Recovery 端點的連接。
- 當您已容錯移轉虛擬機器，但無法從嚴重損壞修復 (DR) 區域連線到 DNS 伺服器時，此問題在重新保護期間較為常見。

#### <a name="fix-the-problem"></a>修正問題

如果您是使用自訂 DNS，請確定可從損毀修復區域存取 DNS 伺服器。

若要檢查 VM 是否使用自訂 DNS 設定：

1. 開啟 **虛擬機器** ，然後選取 VM。
1. 流覽至 Vm **設定** ，然後選取 [ **網路**]。
1. 在 [ **虛擬網路/子網**] 中，選取連結以開啟虛擬網路的資源頁面。
1. 移至 [ **設定** ]，然後選取 [ **DNS 伺服器**]。

嘗試從虛擬機器存取 DNS 伺服器。 如果 DNS 伺服器無法存取，可透過在 DNS 伺服器上進行容錯移轉，或在 DR 網路和 DNS 之間建立這兩個網站來存取。

:::image type="content" source="./media/azure-to-azure-troubleshoot-errors/custom_dns.png" alt-text="com-錯誤。":::

### <a name="issue-2-site-recovery-configuration-failed-151196"></a>問題 2：Site Recovery 設定失敗 (151196)

#### <a name="possible-cause"></a>可能的原因

無法建立連線來 Microsoft 365 驗證和身分識別 IP4 端點。

#### <a name="fix-the-problem"></a>修正問題

Azure Site Recovery 需要 Microsoft 365 IP 範圍的存取權，才能進行驗證。
如果您使用 Azure 網路安全性群組 (NSG) 規則/防火牆 proxy 來控制 VM 上的輸出網路連線能力，請確定您使用 [Azure Active Directory (aad) 以服務標記](../virtual-network/security-overview.md#service-tags) 為基礎的 NSG 規則，以允許存取 AAD。 我們不再支援以 IP 位址為基礎的 NSG 規則。

### <a name="issue-3-site-recovery-configuration-failed-151197"></a>問題 3：	Site Recovery 設定失敗 (151197)

#### <a name="possible-cause"></a>可能的原因

無法建立 Azure Site Recovery 服務端點的連接。

#### <a name="fix-the-problem"></a>修正問題

如果您使用 Azure 網路安全性群組 (NSG) 規則/防火牆 proxy 來控制 VM 上的輸出網路連線能力，請確定您使用的是服務標記。 我們不再支援透過 Nsg 使用 IP 位址的允許清單進行 Azure Site Recovery。

### <a name="issue-4-replication-fails-when-network-traffic-uses-on-premises-proxy-server-151072"></a>問題4：當網路流量使用內部部署 proxy 伺服器 (151072) 時，複寫會失敗

#### <a name="possible-cause"></a>可能的原因

自訂 proxy 設定無效，且行動服務代理程式未自動偵測 Internet Explorer (IE) 的 proxy 設定。

#### <a name="fix-the-problem"></a>修正問題

1. 行動服務代理程式會從 Windows 和 Linux 上的 IE 偵測 proxy 設定 `/etc/environment` 。
1. 如果您只想要為行動服務設定 proxy，則可在位於下列位置的 _proxyinfo.conf_ 中提供 proxy 詳細資料：

   - **Linux**： `/usr/local/InMage/config/`
   - **Windows**： `C:\ProgramData\Microsoft Azure Site Recovery\Config`

1. _Proxyinfo.conf_應該具有下列_INI_格式的 proxy 設定。

   ```plaintext
   [proxy]
   Address=http://1.2.3.4
   Port=567
   ```

> [!NOTE]
> 行動服務代理程式只支援未 **驗證**的 proxy。

### <a name="more-information"></a>詳細資訊

若要指定 [必要的 url](azure-to-azure-about-networking.md#outbound-connectivity-for-urls) 或所 [需的 IP 範圍](azure-to-azure-about-networking.md#outbound-connectivity-using-service-tags)，請遵循 [關於 Azure 中的網路到 azure](azure-to-azure-about-networking.md)複寫的指引。

## <a name="disk-not-found-in-vm-error-code-150039"></a>在 VM 中找不到磁片 (錯誤碼 150039) 

必須先將連接到 VM 的新磁碟初始化。 如果找不到磁片，則會顯示下列訊息：

```Output
Azure data disk <DiskName> <DiskURI> with logical unit number <LUN> <LUNValue> was not mapped to a corresponding disk being reported from within the VM that has the same LUN value.
```

### <a name="possible-causes"></a>可能的原因

- 已將新的資料磁片連接到 VM，但尚未初始化。
- VM 內的資料磁片未正確報告磁片連接至 VM 的邏輯單元數目 (LUN) 值。

### <a name="fix-the-problem"></a>修正問題

請確定資料磁片已初始化，然後再次嘗試操作。

- **Windows**： [連接並初始化新的磁片](../virtual-machines/windows/attach-managed-disk-portal.md)。
- **Linux**： [在 linux 中初始化新的資料磁片](../virtual-machines/linux/add-disk.md)。

若問題持續發生，請連絡支援服務。

## <a name="multiple-disks-available-for-protection-error-code-153039"></a>有多個磁片可供保護 (錯誤碼 153039) 

### <a name="possible-causes"></a>可能的原因

- 在保護之後，一或多個磁片最近已新增至虛擬機器。
- 虛擬機器的保護之後，已初始化一或多個磁片。

### <a name="fix-the-problem"></a>修正問題

若要再次使 VM 的複寫狀態變成狀況良好，您可以選擇保護磁片或關閉警告。

#### <a name="to-protect-the-disks"></a>保護磁片

1. 移至**已複寫的專案**  >  _VM 名稱_  >  **磁片**。
1. 選取未受保護的磁片，然後選取 [ **啟用**複寫]：

   :::image type="content" source="./media/azure-to-azure-troubleshoot-errors/add-disk.png" alt-text="在 VM 磁片上啟用複寫。":::

#### <a name="to-dismiss-the-warning"></a>關閉警告

1. 移至 [複寫的**專案**  >  _VM 名稱_]。
1. 在 [ **總覽** ] 區段中選取警告，然後選取 **[確定]**。

   :::image type="content" source="./media/azure-to-azure-troubleshoot-errors/dismiss-warning.png" alt-text="關閉新磁片的警告。":::

## <a name="vm-removed-from-vault-completed-with-information-error-code-150225"></a>從保存庫移除的 VM 已完成，並出現資訊 (錯誤碼 150225) 

當 Site Recovery 保護虛擬機器時，它會在來源虛擬機器上建立連結。 當您移除保護或停用複寫時，Site Recovery 會將這些連結移除成為清除工作的一部分。 如果虛擬機器有資源鎖定，則會以資訊完成清除作業。 此資訊指出已從復原服務保存庫中移除虛擬機器，但無法在來源電腦上清除某些過時的連結。

如果您不想要再次保護此虛擬機器，您可以忽略此警告。 但是，如果您稍後必須保護此虛擬機器，請遵循本節中的步驟來清除連結。

> [!WARNING]
> 如果您不進行清除：
>
> - 當您透過復原服務保存庫啟用複寫時，將不會列出虛擬機器。
> - 如果您嘗試使用**虛擬機器**設定嚴重損壞修復來保護 VM  >  **Settings**  >  ** **，則作業會失敗，並**因為 VM 上現有的過時資源連結而無法啟用**訊息複寫。

### <a name="fix-the-problem"></a>修正問題

> [!NOTE]
> 當您執行這些步驟時，Site Recovery 不會刪除來源虛擬機器，也不會以任何方式影響。

1. 從 VM 或 VM 資源群組中移除鎖定。 例如，在下圖中，名為的 VM 上的資源鎖定 `MoveDemo` 必須刪除：

   :::image type="content" source="./media/site-recovery-azure-to-azure-troubleshoot/vm-locks.png" alt-text="從 VM 移除鎖定。":::

1. 下載腳本以 [移除過時的 Site Recovery](https://github.com/AsrOneSdk/published-scripts/blob/master/Cleanup-Stale-ASR-Config-Azure-VM.ps1)設定。
1. 執行腳本， _Cleanup-stale-asr-config-Azure-VM.ps1_。 提供訂用帳戶 **識別碼**、 **vm 資源群組**和 **vm 名稱** 作為參數。
1. 如果系統提示您提供 Azure 認證，請提供這些認證。 然後驗證腳本是否執行，而不會發生任何失敗。

## <a name="replication-not-enabled-on-vm-with-stale-resources-error-code-150226"></a>未在具有過時資源的 VM 上啟用複寫 (錯誤碼 150226) 

### <a name="possible-causes"></a>可能的原因

虛擬機器具有舊版 Site Recovery 保護的過時設定。

如果您使用 Site Recovery 來啟用 Azure vm 的複寫，則可能會在 Azure VM 上進行過時的設定，然後：

- 您已停用複寫，但來源 VM 有資源鎖定。
- 您已刪除 Site Recovery 保存庫，但未明確停用 VM 上的複寫。
- 您已刪除包含 Site Recovery 保存庫的資源群組，但未明確停用 VM 上的複寫。

### <a name="fix-the-problem"></a>修正問題

> [!NOTE]
> 當您執行這些步驟時，Site Recovery 不會刪除來源虛擬機器，也不會以任何方式影響。

1. 從 VM 或 VM 資源群組中移除鎖定。 例如，在下圖中，名為的 VM 上的資源鎖定 `MoveDemo` 必須刪除：

   :::image type="content" source="./media/site-recovery-azure-to-azure-troubleshoot/vm-locks.png" alt-text="從 VM 移除鎖定。":::

1. 下載腳本以 [移除過時的 Site Recovery](https://github.com/AsrOneSdk/published-scripts/blob/master/Cleanup-Stale-ASR-Config-Azure-VM.ps1)設定。
1. 執行腳本， _Cleanup-stale-asr-config-Azure-VM.ps1_。 提供訂用帳戶 **識別碼**、 **vm 資源群組**和 **vm 名稱** 作為參數。
1. 如果系統提示您提供 Azure 認證，請提供這些認證。 然後驗證腳本是否執行，而不會發生任何失敗。

## <a name="cant-select-vm-or-resource-group-in-enable-replication-job"></a>無法在啟用複寫作業中選取 VM 或資源群組

### <a name="issue-1-the-resource-group-and-source-vm-are-in-different-locations"></a>問題1：資源群組和來源 VM 位於不同的位置

Site Recovery 目前需要來源區域資源群組和虛擬機器位於相同的位置。 如果不是，當您嘗試套用保護時，您將無法找到虛擬機器或資源群組。

因應措施是，您可以從 VM （而不是復原服務保存庫）啟用複寫。 移至**來源 VM**  >  **屬性**嚴重損壞  >  **修復**並啟用複寫。

### <a name="issue-2-the-resource-group-isnt-part-of-the-selected-subscription"></a>問題2：資源群組不屬於選取的訂用帳戶

如果資源群組不屬於所選訂用帳戶的一部分，您可能無法在保護時尋找資源群組。 請確定資源群組屬於您所使用的訂用帳戶。

### <a name="issue-3-stale-configuration"></a>問題3：過時的設定

如果 Azure VM 上有過時的 Site Recovery 設定，您可能看不到想要啟用複寫的 VM。 如果您使用 Site Recovery 來啟用 Azure VM 的複寫，則可能會發生這種情況，然後：

- 您已刪除 Site Recovery 保存庫，但未明確停用 VM 上的複寫。
- 您已刪除包含 Site Recovery 保存庫的資源群組，但未明確停用 VM 上的複寫。
- 您已停用複寫，但來源 VM 有資源鎖定。

### <a name="fix-the-problem"></a>修正問題

> [!NOTE]
> `AzureRM.Resources`使用本節中所述的腳本之前，請務必先更新模組。 當您執行這些步驟時，Site Recovery 不會刪除來源虛擬機器，也不會以任何方式影響。

1. 從 VM 或 VM 資源群組中移除鎖定（如果有的話）。 例如，在下圖中，名為的 VM 上的資源鎖定 `MoveDemo` 必須刪除：

   :::image type="content" source="./media/site-recovery-azure-to-azure-troubleshoot/vm-locks.png" alt-text="從 VM 移除鎖定。":::

1. 下載腳本以 [移除過時的 Site Recovery](https://github.com/AsrOneSdk/published-scripts/blob/master/Cleanup-Stale-ASR-Config-Azure-VM.ps1)設定。
1. 執行腳本， _Cleanup-stale-asr-config-Azure-VM.ps1_。 提供訂用帳戶 **識別碼**、 **vm 資源群組**和 **vm 名稱** 作為參數。
1. 如果系統提示您提供 Azure 認證，請提供這些認證。 然後驗證腳本是否執行，而不會發生任何失敗。

## <a name="unable-to-select-a-vm-for-protection"></a>無法選取要保護的 VM

### <a name="possible-cause"></a>可能的原因

虛擬機器的延伸模組安裝在失敗或沒有回應的狀態

### <a name="fix-the-problem"></a>修正問題

移至 [**虛擬機器**  >  **設定**  >  **延伸**模組]，並檢查是否有任何擴充功能處於失敗狀態。 請卸載任何失敗的延伸模組，然後再試一次保護虛擬機器。

## <a name="vm-provisioning-state-isnt-valid-error-code-150019"></a>VM 布建狀態無效 (錯誤碼 150019) 

若要在 VM 上啟用複寫，其布建狀態必須為「 **成功**」。 請遵循下列步驟來檢查布建狀態：

1. 在 [Azure 入口網站中，選取 [**所有服務**] 的**資源總管**。
1. 展開 [訂用帳戶]**** 清單然後選取您的訂用帳戶。
1. 展開 [ResourceGroups]**** 清單然後選取 VM 的資源群組。
1. 展開 [ **資源** ] 清單，然後選取您的 VM。
1. 檢查右邊實例視圖中的 [ **provisioningState** ] 欄位。

### <a name="fix-the-problem"></a>修正問題

- 如果 **ProvisioningState** **失敗**，請聯絡支援人員，以取得疑難排解的詳細資料。
- 如果正在**更新** **provisioningState** ，可能會部署其他擴充功能。 檢查 VM 上是否有任何進行中的作業，等候它們完成，然後重試失敗的 Site Recovery 作業以啟用複寫。

## <a name="unable-to-select-target-vm"></a>無法選取目標 VM

### <a name="issue-1-vm-is-attached-to-a-network-thats-already-mapped-to-a-target-network"></a>問題1： VM 連接到已對應至目標網路的網路

在嚴重損壞修復設定期間，如果來源 VM 屬於虛擬網路的一部分，而且來自相同虛擬網路的另一個 VM 已與目標資源群組中的網路對應，則 [網路選取] 下拉式清單方塊將無法使用 (預設會顯示為暗灰色) 。

:::image type="content" source="./media/site-recovery-azure-to-azure-troubleshoot/unabletoselectnw.png" alt-text="網路挑選清單無法使用。":::

### <a name="issue-2-you-previously-protected-the-vm-and-then-you-disabled-the-replication"></a>問題2：您先前已保護 VM，然後停用複寫

停用 VM 的複寫並不會刪除網路對應。 您必須從 VM 受保護的復原服務保存庫中刪除對應。 選取 [復原**服務保存庫**]，然後移至 [**管理**  >  Azure 虛擬機器**Site Recovery Infrastructure**  >  **For Azure virtual machines**  >  **網路對應**Site Recovery 基礎結構]。

:::image type="content" source="./media/site-recovery-azure-to-azure-troubleshoot/delete_nw_mapping.png" alt-text="刪除網路對應。":::

在損毀修復設定期間設定的目標網路，可以在初始設定之後以及在 VM 受到保護之後變更。 若要 **修改網路對應** ，請選取網路名稱：

:::image type="content" source="./media/site-recovery-azure-to-azure-troubleshoot/modify_nw_mapping.png" alt-text="修改網路對應。":::


## <a name="com-or-vss-error-code-151025"></a>COM + 或 VSS (錯誤碼 151025) 

當 COM + 或磁碟區陰影複製服務 (VSS) 錯誤發生時，會顯示下列訊息：

```Output
Site Recovery extension failed to install.
```

### <a name="possible-causes"></a>可能的原因

- COM + 系統應用程式服務已停用。
- 磁碟區陰影複製服務已停用。

### <a name="fix-the-problem"></a>修正問題

將 COM + 系統應用程式和磁碟區陰影複製服務設定為自動或手動啟動模式。

1. 在 Windows 中開啟 [服務] 主控台。
1. 請確定 COM + 系統應用程式和磁碟區陰影複製服務不會設定為 [ **停用** ] 作為 **啟動類型**。

   :::image type="content" source="./media/azure-to-azure-troubleshoot-errors/com-error.png" alt-text="檢查 COM 的啟動類型加上系統應用程式，並磁碟區陰影複製服務。":::

## <a name="unsupported-managed-disk-size-error-code-150172"></a>不支援的受控磁片大小 (錯誤碼 150172) 

發生此錯誤時，會顯示下列訊息：

```Output
Protection couldn't be enabled for the virtual machine as it has <DiskName> with size <DiskSize> that is lesser than the minimum supported size 1024 MB.
```

### <a name="possible-cause"></a>可能的原因

磁片小於支援的大小 1024 MB。

### <a name="fix-the-problem"></a>修正問題

請確定磁片大小在支援的大小範圍內，然後再次嘗試操作。

## <a name="protection-not-enabled-when-grub-uses-device-name-error-code-151126"></a>當 GRUB 使用裝置名稱 (錯誤碼151126時，未啟用保護) 

### <a name="possible-causes"></a>可能的原因

Linux 一致的開機載入器 (GRUB) 設定檔 (_/boot/grub/menu.lst_、 _/boot/grub/grub.cfg_、 _/Boot/grub2/grub.cfg_或 _/etc/default/grub_) 可能會指定實際的裝置名稱，而不是和參數 (UUID) 值的通用唯一識別碼 `root` `resume` 。 Site Recovery 需要 Uuid，因為裝置名稱可能會變更。 重新開機時，VM 可能不會在容錯移轉時使用相同的名稱，因此會產生問題。

下列範例是來自 GRUB 檔案的行，其中會顯示裝置名稱，而不是所需的 Uuid：

- File _/boot/grub2/grub.cfg_：

  `linux /boot/vmlinuz-3.12.49-11-default root=/dev/sda2  ${extra_cmdline} resume=/dev/sda1 splash=silent quiet showopts`

- File： _/boot/grub/menu.lst_

  `kernel /boot/vmlinuz-3.0.101-63-default root=/dev/sda2 resume=/dev/sda1 splash=silent crashkernel=256M-:128M showopts vga=0x314`

### <a name="fix-the-problem"></a>修正問題

以對應的 UUID 取代每個裝置名稱：

1. 藉由執行命令來尋找裝置的 UUID `blkid <device name>` 。 例如：

   ```shell
   blkid /dev/sda1
   /dev/sda1: UUID="6f614b44-433b-431b-9ca1-4dd2f6f74f6b" TYPE="swap"
   blkid /dev/sda2
   /dev/sda2: UUID="62927e85-f7ba-40bc-9993-cc1feeb191e4" TYPE="ext3"
   ```

1. 以格式和中的 UUID 取代裝置名稱 `root=UUID=<UUID>` `resume=UUID=<UUID>` 。 例如，取代之後， _/boot/grub/menu.lst_ 中的行會看起來像下面這行：

   `kernel /boot/vmlinuz-3.0.101-63-default root=UUID=62927e85-f7ba-40bc-9993-cc1feeb191e4 resume=UUID=6f614b44-433b-431b-9ca1-4dd2f6f74f6b splash=silent crashkernel=256M-:128M showopts vga=0x314`

1. 請重試保護。

## <a name="protection-failed-because-grub-device-doesnt-exist-error-code-151124"></a>保護失敗，因為 GRUB 裝置不存在 (錯誤碼 151124) 

### <a name="possible-cause"></a>可能的原因

GRUB 設定檔 (_/boot/grub/menu.lst_、 _/boot/grub/grub.cfg_、 _/boot/grub2/grub.cfg_或 _/etc/default/grub_) 可能包含參數 `rd.lvm.lv` 或 `rd_LVM_LV` 。 這些參數會識別要在開機時探索的 (LVM) 裝置的邏輯磁片區管理員。 如果這些 LVM 裝置不存在，受保護的系統本身將不會開機，而且會卡在開機程式中。 容錯移轉 VM 也會看到相同的問題。 以下提供一些範例：

- File： _/boot/grub2/grub.cfg_ on RHEL7：

  `linux16 /vmlinuz-3.10.0-957.el7.x86_64 root=/dev/mapper/rhel_mup--rhel7u6-root ro crashkernel=128M\@64M rd.lvm.lv=rootvg/root rd.lvm.lv=rootvg/swap rhgb quiet LANG=en_US.UTF-8`

- File： _/etc/default/grub_ on RHEL7：

  `GRUB_CMDLINE_LINUX="crashkernel=auto rd.lvm.lv=rootvg/root rd.lvm.lv=rootvg/swap rhgb quiet`

- File： _/boot/grub/menu.lst_ on RHEL6：

  `kernel /vmlinuz-2.6.32-754.el6.x86_64 ro root=UUID=36dd8b45-e90d-40d6-81ac-ad0d0725d69e rd_NO_LUKS LANG=en_US.UTF-8 rd_NO_MD SYSFONT=latarcyrheb-sun16 crashkernel=auto rd_LVM_LV=rootvg/lv_root  KEYBOARDTYPE=pc KEYTABLE=us rd_LVM_LV=rootvg/lv_swap rd_NO_DM rhgb quiet`

在每個範例中，GRUB 都必須偵測具有名稱和磁片區群組的兩部 LVM 裝置 `root` `swap` `rootvg` 。

### <a name="fix-the-problem"></a>修正問題

如果 LVM 裝置不存在，請建立該裝置，或從 GRUB 設定檔中移除對應的參數。 然後再試一次，以啟用保護。

## <a name="mobility-service-update-finished-with-warnings-error-code-151083"></a>行動服務更新已完成，但出現警告 (錯誤碼 151083) 

Site Recovery 行動服務有許多元件，其中一個稱為篩選器驅動程式。 篩選器驅動程式只會在系統重新開機時載入系統記憶體中。 每當行動服務更新包含篩選器驅動程式變更時，電腦就會更新，但您仍然會看到某些修正程式需要重新開機的警告。 出現警告是因為篩選器驅動程式修正程式只有在載入新的篩選器驅動程式時才會生效，而只有在重新開機時才會發生。

> [!NOTE]
> 這只是警告。 即使在新的代理程式更新之後，現有的複寫仍會繼續運作。 您可以選擇在需要新篩選器驅動程式的優點時重新開機，但是舊的篩選器驅動程式會在您沒有重新開機時繼續運作。
>
> 除了篩選器驅動程式之外，行動服務更新中任何其他增強功能和修正的優點也會在不需要重新開機的情況下生效。

## <a name="protection-not-enabled-if-replica-managed-disk-exists"></a>如果複本受控磁片存在，則未啟用保護

當複本受控磁片已存在，但目標資源群組中沒有預期的標記時，就會發生此錯誤。

### <a name="possible-cause"></a>可能的原因

如果虛擬機器先前受到保護，而且停用複寫時，不會移除複本磁片，就會發生此問題。

### <a name="fix-the-problem"></a>修正問題

請刪除錯誤訊息中所識別的複本磁片，然後重試失敗的保護工作。

## <a name="enable-protection-failed-as-the-installer-is-unable-to-find-the-root-disk-error-code-151137"></a>因為安裝程式找不到根磁片 (錯誤碼 151137) ，所以啟用保護失敗。

如果 Linux 機器的 OS 磁片是使用 Azure 磁碟加密 (ADE) 加密，就會發生此錯誤。 這是僅限代理程式版本9.35 中的有效問題。

### <a name="possible-causes"></a>可能的原因

安裝程式找不到裝載根檔案系統的根磁片。

### <a name="fix-the-problem"></a>修正問題

請遵循下列步驟來修正此問題：

1. 使用下列命令，在 RHEL 和 CentOS 機的目錄 _于/var/lib/waagent_ 下尋找代理程式位： <br>

    `# find /var/lib/ -name Micro\*.gz`

   預期輸出：

    `/var/lib/waagent/Microsoft.Azure.RecoveryServices.SiteRecovery.LinuxRHEL7-1.0.0.9139/UnifiedAgent/Microsoft-ASR_UA_9.35.0.0_RHEL7-64_GA_30Jun2020_release.tar.gz`

2. 建立新的目錄，並將目錄變更為這個新目錄。
3. 使用下列命令，將在第一個步驟中找到的代理程式檔案解壓縮：

    `tar -xf <Tar Ball File>`

4. 開啟 _prereq_check_installer.js_ 的檔案，並刪除下列幾行。 在該檔案之後儲存檔案。

    ```
       {
          "CheckName": "SystemDiskAvailable",
          "CheckType": "MobilityService"
       },
    ```
5. 使用命令叫用安裝程式： <br>

    `./install -d /usr/local/ASR -r MS -q -v Azure`
6. 如果安裝程式成功，請重試啟用複寫作業。

## <a name="next-steps"></a>後續步驟

[將 Azure VM 複寫至另一個 Azure 區域](azure-to-azure-how-to-enable-replication.md)
