---
title: 在 Azure 網站恢復中排除 Azure VM 複製故障
description: 在複製 Azure 虛擬機器以進行災害復原時排除錯誤。
author: rochakm
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 04/08/2019
ms.author: rochakm
ms.openlocfilehash: c131609a5622061e2ea49db422bc4e9da96666d1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80276677"
---
# <a name="troubleshoot-azure-to-azure-vm-replication-errors"></a>排除 Azure 到 Azure VM 複製錯誤

本文介紹如何在 Azure 虛擬機器 （VM） 從一個區域複製和恢復期間解決 Azure 網站恢復中的常見錯誤。 如需受支援組態的詳細資訊，請參閱[複寫 Azure VM 的支援矩陣](site-recovery-support-matrix-azure-to-azure.md)。

## <a name="azure-resource-quota-issues-error-code-150097"></a><a name="azure-resource-quota-issues-error-code-150097"></a>Azure 資源配額問題 (錯誤碼 150097)

確保已啟用訂閱，在計畫用作災害復原區域的目的地區域中創建 Azure VM。 還要確保訂閱有足夠的配額來創建必要大小的 VM。 預設情況下，網站恢復選擇的目標 VM 大小與源 VM 大小相同。 如果匹配大小不可用，網站恢復將自動選擇最接近的可用大小。

如果沒有支援源 VM 配置的大小，將顯示以下消息：

> 無法為虛擬機器*VmName*啟用複製。

### <a name="possible-causes"></a>可能的原因

- 訂閱 ID 未啟用在目的地區域位置創建任何 VM。
- 訂閱 ID 未啟用，或者沒有足夠的配額，無法在目的地區域位置創建特定的 VM 大小。
- 找不到合適的目標 VM 大小來匹配目的地區域位置中的訂閱 ID 的源 VM 網路介面卡 （NIC） 計數 （2）。

### <a name="fix-the-problem"></a>修正問題

請與[Azure 計費支援](https://docs.microsoft.com/azure/azure-portal/supportability/resource-manager-core-quotas-request)聯繫，以使訂閱能夠在目標位置創建所需大小的 VM。 然後，重試失敗的操作。

如果目標位置具有容量約束，請禁用對該位置的複製。 然後，將複製啟用到訂閱有足夠的配額以創建所需大小的 VM 的不同位置。

## <a name="trusted-root-certificates-error-code-151066"></a><a name="trusted-root-certificates-error-code-151066"></a>受信任的根憑證 (錯誤碼 151066)

如果 VM 上未存在所有最新的受根信任證書，則"啟用複製"網站恢復作業可能會失敗。 如果沒有這些證書，來自 VM 的網站恢復服務調用的身份驗證和授權將失敗。

如果"啟用複製"作業失敗，將顯示以下消息：

> "網站恢復配置失敗。

### <a name="possible-cause"></a>可能的原因

虛擬機器上不存在授權和身份驗證所需的可信根憑證。

### <a name="fix-the-problem"></a>修正問題

#### <a name="windows"></a>Windows

對於運行 Windows 作業系統的 VM，請在 VM 上安裝最新的 Windows 更新，以便電腦上存在所有受信任的根憑證。 按照組織中典型的 Windows 更新管理或證書更新管理過程來獲取 VM 上的最新根憑證和更新的憑證撤銷清單。

如果您是在中斷連線的環境，請遵循您組織的標準 Windows 更新程序來取得憑證。 如果 VM 上不存在所需的憑證，對於 Site Recovery 進行的呼叫將而於安全性因素而失敗。

若要確認問題已解決，請在 VM 中從瀏覽器移至 login.microsoftonline.com。

有關詳細資訊，請參閱[配置受信任的根和不允許的證書](https://technet.microsoft.com/library/dn265983.aspx)。

#### <a name="linux"></a>Linux

按照 Linux 作業系統版本的分發伺服器提供的指南獲取最新的受信任的根憑證和 VM 上的最新憑證撤銷清單。

由於 SUSE Linux 使用符號連結（或*符號連結*）來維護證書清單，請按照以下步驟操作：

1. 以根使用者身分登入。

1. 運行此命令以更改目錄：

    **• cd /etc/ssl/證書**

1. 檢查賽門鐵克根 CA 憑證是否存在：

    **# ls VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem**

1. 如果未找到賽門鐵克根 CA 憑證，請運行以下命令以下載該檔。 檢查是否存在任何錯誤，並遵循建議的網路故障操作。

    **• wget https://docs.broadcom.com/docs-and-downloads/content/dam/symantec/docs/other-resources/verisign-class-3-public-primary-certification-authority-g5-en.pem -O VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem**

1. 檢查是否存在巴爾的摩根 CA 憑證：

    **ls Baltimore_CyberTrust_Root.pem**

1. 如果未找到巴爾的摩根 CA 憑證，請運行此命令以下載證書：

    **• wget https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem -O Baltimore_CyberTrust_Root.pem**

1. 檢查是否存在DigiCert_Global_Root_CA證書：

    **ls DigiCert_Global_Root_CA.pem**

1. 如果未找到DigiCert_Global_Root_CA，請運行以下命令下載證書：

    **• wgethttp://www.digicert.com/CACerts/DigiCertGlobalRootCA.crt**

    **• openssl x509 -在 DigiCertGlobalRootCA.crt - 通知德-出型 pem -出 DigiCert_Global_Root_CA.pem**

1. 運行重新雜湊腳本以更新新下載的證書的證書主題雜湊：

    **• c_rehash**

1. 運行這些命令以檢查主題雜湊值是否為證書創建了符號連結：

    - 命令：

        **[ ls -l ]格雷普·巴爾的摩**

    - 輸出：

        `lrwxrwxrwx 1 root root   29 Jan  8 09:48 3ad48a91.0 -> Baltimore_CyberTrust_Root.pem`

        `-rw-r--r-- 1 root root 1303 Jun  5  2014 Baltimore_CyberTrust_Root.pem`

    - 命令：

        **[ ls -l ]VeriSign_Class_3_Public_Primary_Certification_Authority_G5**

    - 輸出：

        `-rw-r--r-- 1 root root 1774 Jun  5  2014 VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem`

        `lrwxrwxrwx 1 root root   62 Jan  8 09:48 facacbc6.0 -> VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem`

    - 命令：

        **[ ls -l ]grep DigiCert_Global_Root**

    - 輸出：

        `lrwxrwxrwx 1 root root   27 Jan  8 09:48 399e7759.0 -> DigiCert_Global_Root_CA.pem`

        `-rw-r--r-- 1 root root 1380 Jun  5  2014 DigiCert_Global_Root_CA.pem`

1. 使用檔案名 b204d74a.0 創建檔VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem 的副本：

    **[ cp VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem b204d74a.0**

1. 使用檔案名 653b494a.0 創建檔Baltimore_CyberTrust_Root.pem 的副本：

    **[ cp Baltimore_CyberTrust_Root.pem 653b494a.0**

1. 使用檔案名 3513523f.0 創建檔DigiCert_Global_Root_CA.pem 的副本：

    **• cp DigiCert_Global_Root_CA.pem 3513523f.0**

1. 檢查檔是否存在：

    - 命令：

        **# ls -l 653b494a.0 b204d74a.0 3513523f.0**

    - 輸出

        `-rw-r--r-- 1 root root 1774 Jan  8 09:52 3513523f.0`

        `-rw-r--r-- 1 root root 1303 Jan  8 09:52 653b494a.0`

        `-rw-r--r-- 1 root root 1774 Jan  8 09:52 b204d74a.0`

## <a name="outbound-connectivity-for-site-recovery-urls-or-ip-ranges-error-code-151037-or-151072"></a>Site Recovery URL 或 IP 範圍的輸出連線能力 (錯誤碼 151037 或 151072)

要使網站恢復複製正常工作，需要 VM 與特定 URL 的出站連接。 如果您的 VM 位於防火牆後方，或使用網路安全性群組 (NSG) 規則控制輸出連線能力，您可能會遇到下列其中一個問題。 請注意，雖然我們繼續支援通過 URL 進行出站訪問，但不再支援使用允許的 IP 範圍清單。

### <a name="issue-1-failed-to-register-azure-virtual-machine-with-site-recovery-151195-br"></a><a name="issue-1-failed-to-register-azure-virtual-machine-with-site-recovery-151195-br"></a>問題 1：無法向 Site Recovery 註冊 Azure 虛擬機器 (151195) </br>
- **可能的原因** </br>
  - 由於 DNS 解析失敗，無法建立與網站恢復終結點的連接。
  - 此問題經常會在您已將虛擬機器容錯移轉，但 DR 區域無法連線到 DNS 伺服器的情況下進行重新保護的期間發生。

- **解析度**
   - 如果您是使用自訂 DNS，請確保災害復原區域可以連線到 DNS 伺服器。 若要查看您是否有自訂 DNS，請移至 VM> [災害復原網路] > [DNS 伺服器]。 嘗試從虛擬機器存取 DNS 伺服器。 如果無法存取，請試著對 DNS 伺服器進行容錯移轉，或在 DR 網路和 DNS 之間建立連線。

    ![com-error](./media/azure-to-azure-troubleshoot-errors/custom_dns.png)


### <a name="issue-2-site-recovery-configuration-failed-151196"></a>問題 2：Site Recovery 設定失敗 (151196)
- **可能的原因** </br>
  - 無法建立與 Office 365 驗證與身分識別 IP4 端點之間的連線。

- **解析度**
  - Azure Site Recovery 需要存取 Office 365 IP 範圍以進行驗證。
    如果使用 Azure 網路安全性群組 （NSG） 規則/防火牆代理來控制 VM 上的出站網路連接，請確保使用基於[Azure 活動目錄 （AAD） 服務標記的](../virtual-network/security-overview.md#service-tags)NSG 規則允許訪問 AAD。 我們不再支援基於 IP 位址的 NSG 規則。


### <a name="issue-3-site-recovery-configuration-failed-151197"></a>問題 3：	Site Recovery 設定失敗 (151197)
- **可能的原因** </br>
  - 無法在連線至 Azure Site Recovery 服務端點。

- **解析度**
  - 如果使用 Azure 網路安全性群組 （NSG） 規則/防火牆代理來控制 VM 上的出站網路連接，請確保使用服務標記。 我們不再支援使用通過 NSG 的 IP 位址允許清單進行 Azure 網站恢復 （ASR）。


### <a name="issue-4-a2a-replication-failed-when-the-network-traffic-goes-through-on-premise-proxy-server-151072"></a>問題 4：當網路流量通過內部部署 Proxy 伺服器時，A2A 複寫失敗 (151072)
 - **可能的原因** </br>
   - 自訂 Proxy 設定無效，且 ASR 行動服務代理程式未自動偵測到 IE 的 Proxy 設定


 - **解析度**
   1.    行動服務代理程式偵測到 IE 的 Proxy 設定 (在 Windows 上) 和 /etc/environment (在 Linux 上)。
   2.  如果您只想要為 ASR 行動服務設定 Proxy，則可在 ProxyInfo.conf 中提供 Proxy 詳細資料，該檔案位於：</br>
       - ``/usr/local/InMage/config/`` (在 ***Linux*** 上)
       - ``C:\ProgramData\Microsoft Azure Site Recovery\Config``在***Windows***上
   3.    ProxyInfo.conf 應該要有下列 INI 格式的 Proxy 設定。</br>
                   *[代理]*</br>
                   *位址*http://1.2.3.4*</br>
                   Port=567**</br>
   4. ASR 行動服務代理程式僅支援***未驗證的 Proxy***。


### <a name="fix-the-problem"></a>修正問題

要[將所需的 URL](azure-to-azure-about-networking.md#outbound-connectivity-for-urls)添加到允許清單中，請按照[網路指導文檔中](site-recovery-azure-to-azure-networking-guidance.md)的步驟操作。


### <a name="more-information"></a>詳細資訊

要指定[所需的 URL](azure-to-azure-about-networking.md#outbound-connectivity-for-urls)或[所需的 IP 範圍](azure-to-azure-about-networking.md#outbound-connectivity-using-service-tags)，請按照 Azure 到 Azure[複製中的"關於網路"中的](site-recovery-azure-to-azure-networking-guidance.md)指南進行操作。

## <a name="disk-not-found-in-the-machine-error-code-150039"></a>在機器中找不到磁碟 (錯誤碼 150039)

必須先將連接到 VM 的新磁碟初始化。 如果找不到磁片，將顯示以下消息：

> 具有邏輯單位編號*LUN LUNValue**LUNValue*的 Azure 資料*磁片名稱* *DiskURI*未映射到從具有相同 LUN 值的 VM 中報告的相應磁片。

### <a name="possible-causes"></a>可能的原因

- 新資料磁片已附加到 VM，但未初始化。
- VM 中的資料磁片無法正確報告磁片附加到 VM 的邏輯單位編號 （LUN） 值。

### <a name="fix-the-problem"></a>修正問題

確保資料磁片已初始化，然後重試該操作。

- **視窗**：[附加和初始化新磁片](https://docs.microsoft.com/azure/virtual-machines/windows/attach-managed-disk-portal)。

- **Linux**：[在Linux中初始化一個新的資料磁片](https://docs.microsoft.com/azure/virtual-machines/linux/add-disk)。

若問題持續發生，請連絡支援服務。

## <a name="one-or-more-disks-are-available-for-protection-error-code-153039"></a>一個或多個磁片可供保護（錯誤代碼 153039）

### <a name="possible-causes"></a>可能的原因

- 保護後，最近向虛擬機器添加了一個或多個磁片。
- 保護虛擬機器後，將初始化一個或多個磁片。

### <a name="fix-the-problem"></a>修正問題

要使 VM 的複製狀態再次正常運行，可以選擇保護磁片或關閉警告。

#### <a name="to-protect-the-disks"></a>保護磁片

1. 轉到**複製專案** > *VM 名稱* > **磁片**。
1. 選擇未受保護的磁片，然後選擇 **"啟用複製**：

    ![在 VM 磁片上啟用複製](./media/azure-to-azure-troubleshoot-errors/add-disk.png)

#### <a name="to-dismiss-the-warning"></a>解除警告

1. 轉到**複製項** > *VM 名稱*。
1. 在 **"概述"** 部分中選擇警告，然後選擇 **"確定**"。

    ![關閉新磁片警告](./media/azure-to-azure-troubleshoot-errors/dismiss-warning.png)

## <a name="remove-the-virtual-machine-from-the-vault-completed-with-information-error-code-150225"></a>從已完成的資訊（錯誤代碼 150225）的保存庫中刪除虛擬機器

保護虛擬機器時，網站恢復會在源虛擬機器上創建一些連結。 刪除保護或禁用複製時，網站恢復將刪除這些連結作為清理作業的一部分。 如果虛擬機器具有資源鎖定，則清理作業將隨資訊完成。 該資訊表示虛擬機器已從恢復服務保存庫中刪除，但源電腦上無法清理某些陳舊連結。

如果您從未打算再次保護此虛擬機器，則可以忽略此警告。 但是，如果以後必須保護此虛擬機器，請按照"修復問題"下的步驟清理連結。

> [!WARNING]
> 如果不進行清理：
>
> - 通過恢復服務保存庫啟用複製時，將不會列出虛擬機器。
> - 如果嘗試使用**虛擬機器** > **設置** > **災害復原**來保護 VM，則操作將失敗，消息為"由於 VM 上現有的陳舊資源連結而無法啟用複製"。

### <a name="fix-the-problem"></a>修正問題

> [!NOTE]
> 在執行這些步驟時，網站恢復不會刪除源虛擬機器或以任何方式影響它。

1. 從 VM 或 VM 資源組中刪除鎖。 例如，在下圖中，必須刪除名為"MoveDemo"的 VM 上的資源鎖：

    ![從 VM 中刪除鎖](./media/site-recovery-azure-to-azure-troubleshoot/vm-locks.png)

1. 下載腳本以[刪除陳舊的網站恢復配置](https://github.com/AsrOneSdk/published-scripts/blob/master/Cleanup-Stale-ASR-Config-Azure-VM.ps1)。
1. 運行腳本，該腳本稱為清理-陳舊-配置-Azure-VM.ps1。 將訂閱 ID、VM 資源組和 VM 名稱作為參數提供。
1. 如果系統要求您提供 Azure 憑據，請提供它們。 然後驗證腳本是否運行沒有任何失敗。

## <a name="replication-cant-be-enabled-because-of-stale-resource-links-on-the-vm-error-code-150226"></a>由於 VM 上陳舊的資源連結（錯誤代碼 150226），無法啟用複製

### <a name="possible-cause"></a>可能的原因

虛擬機器具有以前網站恢復保護的陳舊配置。

如果使用網站恢復為 Azure VM 啟用複製，則 Azure VM 上可能會出現陳舊的配置，然後：

- 您禁用了複製，但源 VM 具有資源鎖定。
- 刪除網站恢復保存庫時未顯式禁用 VM 上的複製。
- 刪除包含網站恢復保存庫的資源組，而無需顯式禁用 VM 上的複製。

### <a name="fix-the-problem"></a>修正問題

> [!NOTE]
> 在執行這些步驟時，網站恢復不會刪除源虛擬機器或以任何方式影響它。

1. 從 VM 或 VM 資源組中刪除鎖。 例如，在下圖中，必須刪除名為"MoveDemo"的 VM 上的資源鎖：

    ![從 VM 中刪除鎖](./media/site-recovery-azure-to-azure-troubleshoot/vm-locks.png)

1. 下載腳本以[刪除陳舊的網站恢復配置](https://github.com/AsrOneSdk/published-scripts/blob/master/Cleanup-Stale-ASR-Config-Azure-VM.ps1)。
1. 運行腳本，該腳本稱為清理-陳舊-配置-Azure-VM.ps1。 將訂閱 ID、VM 資源組和 VM 名稱作為參數提供。
1. 如果系統要求您提供 Azure 憑據，請提供它們。 然後驗證腳本是否運行沒有任何失敗。

## <a name="unable-to-see-the-azure-vm-or-resource-group-for-the-selection-in-the-enable-replication-job"></a>無法在"啟用複製"作業中查看所選內容的 Azure VM 或資源組

### <a name="cause-1-the-resource-group-and-source-virtual-machine-are-in-different-locations"></a>原因 1：資源組和源虛擬機器位於不同位置

網站恢復當前要求源區域資源組和虛擬機器位於同一位置。 否則，當您嘗試應用保護時，您將無法找到虛擬機器或資源組。

作為解決方法，可以從 VM 而不是恢復服務保存庫啟用複製。 轉到**源 VM** > **屬性** > **災害復原**並啟用複製。

### <a name="cause-2-the-resource-group-is-not-part-of-the-selected-subscription"></a>原因 2：資源組不是所選訂閱的一部分

如果資源組不是所選訂閱的一部分，則在保護時可能無法找到資源組。 確保資源組屬於您正在使用的訂閱。

### <a name="cause-3-stale-configuration"></a>原因 3：陳舊配置

如果 Azure VM 上留下了陳舊的網站恢復配置，則可能無法看到要啟用複製的 VM。 如果使用網站恢復為 Azure VM 啟用複製，則可能發生此情況，然後發生：

- 刪除網站恢復保存庫時未顯式禁用 VM 上的複製。
- 刪除包含網站恢復保存庫的資源組，而無需顯式禁用 VM 上的複製。
- 您禁用了複製，但源 VM 具有資源鎖定。

### <a name="fix-the-problem"></a>修正問題

> [!NOTE]
> 在使用本節中提到的腳本之前，請確保更新"AzureRM.Resources"模組。  在執行這些步驟時，網站恢復不會刪除源虛擬機器或以任何方式影響它。

1. 從 VM 或 VM 資源組中刪除鎖（如果有）。 例如，在下圖中，必須刪除名為"MoveDemo"的 VM 上的資源鎖：

    ![從 VM 中刪除鎖](./media/site-recovery-azure-to-azure-troubleshoot/vm-locks.png)

1. 下載腳本以[刪除陳舊的網站恢復配置](https://github.com/AsrOneSdk/published-scripts/blob/master/Cleanup-Stale-ASR-Config-Azure-VM.ps1)。
1. 運行腳本，該腳本稱為清理-陳舊-配置-Azure-VM.ps1。 將訂閱 ID、VM 資源組和 VM 名稱作為參數提供。
1. 如果系統要求您提供 Azure 憑據，請提供它們。 然後驗證腳本是否運行沒有任何失敗。

## <a name="unable-to-select-a-virtual-machine-for-protection"></a>無法選擇虛擬機器進行保護

### <a name="cause-1-the-virtual-machine-has-an-extension-installed-in-a-failed-or-unresponsive-state"></a>原因 1：虛擬機器的擴展安裝處於故障或無回應狀態

轉到**虛擬機器** > **設置** > **擴展，** 並檢查任何處于失敗狀態的擴展。 卸載任何失敗的擴展，然後重試以保護虛擬機器。

### <a name="cause-2-the-vms-provisioning-state-is-not-valid"></a>原因 2：VM 的預配狀態無效

請參閱["VM 的預配狀態](#the-vms-provisioning-state-is-not-valid-error-code-150019)中的故障排除步驟無效"，本文稍後將介紹。

## <a name="the-vms-provisioning-state-is-not-valid-error-code-150019"></a>VM 的預配狀態無效（錯誤代碼 150019）

要在 VM 上啟用複製，必須**成功**其預配狀態。 按照以下步驟檢查預配狀態：

1. 在 Azure 門戶中，從**所有服務**中選擇**資源資源管理器**。
1. 展開 [訂用帳戶]**** 清單然後選取您的訂用帳戶。
1. 展開 [ResourceGroups]**** 清單然後選取 VM 的資源群組。
1. 展開 **"資源"** 清單並選擇 VM。
1. 檢查右側"實例"視圖中的**預配狀態**欄位。

### <a name="fix-the-problem"></a>修正問題

- 如果 [provisioningState]**** 是「失敗」****，請連絡支援人員以取得疑難排解的詳細資訊。
- 如果**預配狀態****正在更新**，則可能正在部署另一個擴展。 檢查 VM 上是否有任何正在進行的操作，等待它們完成，然後重試失敗的網站恢復"啟用複製"作業。

## <a name="unable-to-select-target-vm-network-selection-tab-is-unavailable"></a>無法選擇目標 VM（網路選擇選項卡不可用）

### <a name="cause-1-your-vm-is-attached-to-a-network-thats-already-mapped-to-a-target-network"></a>原因 1：您的 VM 已連接到已映射到目標網路的網路

如果源 VM 是虛擬網路的一部分，並且來自同一虛擬網路的另一個 VM 已與目標資源組中的網路映射，則預設情況下網路選擇下拉式清單方塊不可用（顯示為灰色）。

![網路挑選清單不可用](./media/site-recovery-azure-to-azure-troubleshoot/unabletoselectnw.png)

### <a name="cause-2-you-previously-protected-the-vm-by-using-site-recovery-and-then-you-disabled-the-replication"></a>原因 2：您以前使用網站恢復保護 VM，然後禁用複製

禁用 VM 複製不會刪除網路映射。 必須從保護 VM 的恢復服務保存庫中刪除映射。 轉到*恢復服務保存庫* > **網站恢復基礎結構** > **網路映射**。

![刪除網路映射](./media/site-recovery-azure-to-azure-troubleshoot/delete_nw_mapping.png)

在災害復原設置期間配置的目標網路可以在初始設置後在 VM 受到保護後更改：

![修改網路映射](./media/site-recovery-azure-to-azure-troubleshoot/modify_nw_mapping.png)

請注意，更改網路映射會影響使用同一網路映射的所有受保護 VM。

## <a name="com-or-volume-shadow-copy-service-error-error-code-151025"></a>COM+ 或卷影影複製服務錯誤（錯誤代碼 151025）

發生此錯誤時，將顯示以下消息：

> "網站恢復擴展安裝失敗"

### <a name="possible-causes"></a>可能的原因

- COM+ 系統應用程式服務已禁用。
- 卷影影複製服務已禁用。

### <a name="fix-the-problem"></a>修正問題

將 COM+ 系統應用程式和卷影影複製服務設置為自動或手動啟動模式。

1. 在 Windows 中打開服務主控台。
1. 確保 COM+ 系統應用程式和卷卷卷卷複製服務未設置為 **"已禁用**"，因為它們的**啟動類型**。

    ![檢查 COM+ 系統應用程式和卷卷卷複製服務的啟動類型](./media/azure-to-azure-troubleshoot-errors/com-error.png)

## <a name="unsupported-managed-disk-size-error-code-150172"></a>不受支援的託管磁片大小（錯誤代碼 150172）

發生此錯誤時，將顯示以下消息：

> 無法為虛擬機器啟用保護，因為它具有大小*為 DiskSize*的*DiskName，* 其大小小於受支援的最低大小 1024 MB。

### <a name="possible-cause"></a>可能的原因

磁片小於支援的大小 1024 MB。

### <a name="fix-the-problem"></a>修正問題

請確保磁片大小在支援的大小範圍內，然後重試該操作。

## <a name="protection-was-not-enabled-because-the-grub-configuration-includes-the-device-name-instead-of-the-uuid-error-code-151126"></a><a name="enable-protection-failed-as-device-name-mentioned-in-the-grub-configuration-instead-of-uuid-error-code-151126"></a>未啟用保護，因為 GRUB 配置包括設備名稱而不是 UUID（錯誤代碼 151126）

### <a name="possible-cause"></a>可能的原因

Linux GRUB 設定檔（/引導/grub/menu.lst、/引導/grub/grub.cfg、/引導/grub2/grub.cfg，或 /etc/預設/grub）可能為*根**參數和恢復*參數指定實際設備名稱，而不是 UUID 值。 網站恢復需要 UUD，因為設備名稱可能會更改。 重新開機後，VM 可能無法在容錯移轉時出現相同的名稱，從而導致問題。

以下示例是 GRUB 檔中出現的設備名稱（以粗體顯示）而不是所需 UUD 的行：

- 檔/啟動/grub2/grub.cfg

  > linux   /boot/vmlinuz-3.12.49-11-default **root=/dev/sda2**  ${extra_cmdline} **resume=/dev/sda1** splash=silent quiet showopts

- 檔： /引導/grub/功能表.lst

  > 內核 /引導/vmlinuz-3.0.101-63 預設**根_/dev/sda2** **恢復_/dev/sda1**初始_靜默崩潰內核_256M-：128M 顯示 vga_0x314


### <a name="fix-the-problem"></a>修正問題

將每個設備名稱替換為相應的 UUID：

1. 通過執行命令**blkid** ***設備名稱***查找設備的 UUID。 例如：

    ```
    blkid /dev/sda1
    /dev/sda1: UUID="6f614b44-433b-431b-9ca1-4dd2f6f74f6b" TYPE="swap"
    blkid /dev/sda2
    /dev/sda2: UUID="62927e85-f7ba-40bc-9993-cc1feeb191e4" TYPE="ext3"
   ```

1. 將設備名稱替換為其 UUID，格式為**root_UUID**=*UUID，* 然後**恢復_UUID**=*UUID*。 例如，更換後，從 /boot/grub/menu.lst（前面討論）的行如下所示：

    > kernel /boot/vmlinuz-3.0.101-63-default **root=UUID=62927e85-f7ba-40bc-9993-cc1feeb191e4** **resume=UUID=6f614b44-433b-431b-9ca1-4dd2f6f74f6b** splash=silent crashkernel=256M-:128M showopts vga=0x314

1. 重試保護。

## <a name="enable-protection-failed-because-the-device-mentioned-in-the-grub-configuration-doesnt-exist-error-code-151124"></a>啟用保護失敗，因為 GRUB 配置中提到的設備不存在（錯誤代碼 151124）

### <a name="possible-cause"></a>可能的原因

GRUB 設定檔（/引導/grub/menu/menu.lst、/引導/grub/grub.cfg、/引導/grub2/grub.cfg，或 /etc/預設/grub）可能包含*rd.lvm.lv*或*rd_LVM_LV*的參數。 這些參數標識將在啟動時發現的邏輯卷管理器 （LVM） 設備。 如果這些 LVM 設備不存在，則受保護的系統本身將不會啟動，並且將卡在啟動過程中。 容錯移轉 VM 也會看到同樣的問題。 以下提供一些範例：

- 檔： /引導/grub2/grub.cfg 在 RHEL7：

    > linux16 /vmlinuz-3.10.0-957.el7.el7.x86_64 根_/dev/mapper/rhel_mup--rhel7u6-根r崩潰內核\@=128M 64M **rd.lvm.lv_rootvg/rootm.lv_rootvg/交換**rhgb quiet lang_en_US。UTF-8

- 檔： RHEL7 上 /etc/預設/grub：

    > GRUB_CMDLINE_LINUX="崩潰內核_自動**rd.lvm.lv_rootvg/rootrd.lvm.lv_rootvg/交換**rhgb 安靜"

- 檔： /引導/grub/功能表.lst 在 RHEL6：

    > 內核 /vmlinuz-2.6.32-754.el6.x86_64 ro 根_UUID=36dd8b45-e90d-40d6-81ac-ad0d0725d69e rd_NO_LUKS LANG_en_US。UTF-8 rd_NO_MD SYSFONT_latarcyrheb-sun16 速成內核_自動**rd_LVM_LV_rootvg/lv_root**鍵盤_pc KEYTABLE_我們**rd_LVM_LV_rootvg/lv_swaprd_NO_DM** rhgb 安靜

在每個示例中，粗體顯示 GRUB 必須檢測卷組"rootvg"中的兩個 LVM 設備，其名稱為"root"和"交換"。

### <a name="fix-the-problem"></a>修正問題

如果 LVM 設備不存在，請創建該設備或從 GRUB 設定檔中刪除相應的參數。 然後，請重試以啟用保護。

## <a name="a-site-recovery-mobility-service-update-finished-with-warnings-error-code-151083"></a>網站恢復移動服務更新已完成警告（錯誤代碼 151083）

網站恢復移動服務具有許多元件，其中一個元件稱為篩選器驅動程式。 篩選器驅動程式僅在系統重新開機期間載入到系統記憶體中。 每當移動服務更新包含篩選器驅動程式更改時，電腦都會更新，但仍會看到一條警告，指出某些修補程式需要重新開機。 出現此警告是因為篩選器驅動程式修復僅在載入新篩選器驅動程式時才能生效，這僅在重新開機期間發生。

> [!NOTE]
> 這只是警告。 即使在新的代理更新之後，現有複製仍繼續工作。 您可以選擇在希望新篩選器驅動程式的好處時重新開機，但如果不重新開機，舊篩選器驅動程式將繼續工作。
>
> 除了篩選器驅動程式之外，移動服務更新中任何其他增強和修復功能的好處都生效，無需重新開機。

## <a name="protection-couldnt-be-enabled-because-the-replica-managed-disk-already-exists-without-expected-tags-in-the-target-resource-group-error-code-150161"></a>無法啟用保護，因為目標資源組中已存在副本託管磁片，沒有預期標記（錯誤代碼 150161）

### <a name="possible-cause"></a>可能的原因

如果虛擬機器以前受到保護，並且禁用複製時，副本磁片未清理，則可能會出現此問題。

### <a name="fix-the-problem"></a>修正問題

刪除錯誤訊息中標識的副本磁片，然後重試失敗的保護作業。

## <a name="next-steps"></a>後續步驟

[複寫 Azure 虛擬機器](site-recovery-replicate-azure-to-azure.md)
