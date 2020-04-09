---
title: 在 Azure 網站恢復中排除 Azure VM 複製故障
description: 在複製 Azure 虛擬機以進行災難恢復時排除錯誤。
author: rochakm
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 04/07/2020
ms.author: rochakm
ms.openlocfilehash: 0882eaa8b54966c7a804cf78a3928771b238e056
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/08/2020
ms.locfileid: "80884999"
---
# <a name="troubleshoot-azure-to-azure-vm-replication-errors"></a>排除 Azure 到 Azure VM 複製錯誤

本文介紹如何在 Azure 虛擬機器 (VM) 從一個區域複製和恢復期間解決 Azure 網站恢復中的常見錯誤。 如需受支援組態的詳細資訊，請參閱[複寫 Azure VM 的支援矩陣](azure-to-azure-support-matrix.md)。

## <a name="azure-resource-quota-issues-error-code-150097"></a>Azure 資源配額問題 (錯誤碼 150097)

確保已啟用訂閱,在計畫用作災難恢復 (DR) 區域的目標區域中創建 Azure VM。 您的訂閱需要足夠的配額來創建必要大小的 VM。 預設情況下,網站恢復選擇的目標 VM 大小與源 VM 大小相同。 如果匹配大小不可用,網站恢復將自動選擇最接近的可用大小。

如果沒有支援來源 VM 設定的大小,將顯示以下訊息:

```Output
Replication couldn't be enabled for the virtual machine <VmName>.
```

### <a name="possible-causes"></a>可能的原因

- 訂閱 ID 未啟用在目標區域位置創建任何 VM。
- 訂閱 ID 未啟用,或者沒有足夠的配額,無法在目標區域位置創建特定的 VM 大小。
- 找不到合適的目標 VM 大小來匹配目標區域位置中的訂閱 ID 的來源 VM 網路介面卡 (NIC) 計數 (2)。

### <a name="fix-the-problem"></a>修正問題

請與[Azure 計費支援](/azure/azure-portal/supportability/resource-manager-core-quotas-request)聯繫,以使訂閱能夠在目標位置創建所需大小的 VM。 然後,重試失敗的操作。

如果目標位置具有容量約束,請禁用對該位置的複製。 然後,將複製啟用到訂閱有足夠的配額以創建所需大小的 VM 的不同位置。

## <a name="trusted-root-certificates-error-code-151066"></a>受信任的根憑證 (錯誤碼 151066)

如果 VM 上未存在所有最新的受信任根證書,則啟用網站恢復複製的作業可能會失敗。 如果沒有這些證書,來自 VM 的網站恢復服務調用的身份驗證和授權將失敗。

開啟複製工作失敗,將顯示以下訊息:

```Output
Site Recovery configuration failed.
```

### <a name="possible-cause"></a>可能的原因

虛擬機上不存在授權和身份驗證所需的可信根證書。

### <a name="fix-the-problem"></a>修正問題

#### <a name="windows"></a>Windows

對於運行 Windows 作業系統的 VM,安裝最新的 Windows 更新,以便 VM 上存在所有受信任的根證書。 按照組織中典型的 Windows 更新管理或證書更新管理過程,獲取 VM 上的最新根證書和更新的證書吊銷清單。

- 如果您是在中斷連線的環境，請遵循您組織的標準 Windows 更新程序來取得憑證。
- 如果 VM 上不存在所需的憑證，對於 Site Recovery 進行的呼叫將而於安全性因素而失敗。

要驗證問題已解決,請從 VM`login.microsoftonline.com`中的瀏覽器轉到。

有關詳細資訊,請參閱[設定信任的根和不允許的憑證](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn265983(v=ws.11))。

#### <a name="linux"></a>Linux

按照 Linux 作業系統版本的分發伺服器提供的指南獲取最新的受信任的根證書和 VM 上的最新證書吊銷清單。

由於 SUSE Linux 使用符號連結或符號連結來維護憑證清單,請按照以下步驟操作:

1. 以**根**使用者身份登錄。 哈希符號`#`( ) 是預設命令提示符。

1. 要更改目錄,請執行以下指令:

   `cd /etc/ssl/certs`

1. 檢查賽門鐵克根 CA 證書是否存在:

   `ls VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem`

   - 如果未找到賽門鐵克根 CA 證書,請運行以下命令以下載該檔。 檢查是否存在任何錯誤,並遵循建議的網路故障操作。

     `wget https://docs.broadcom.com/docs-and-downloads/content/dam/symantec/docs/other-resources/verisign-class-3-public-primary-certification-authority-g5-en.pem -O VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem`

1. 檢查是否存在巴爾的摩根 CA 證書:

   `ls Baltimore_CyberTrust_Root.pem`

   - 如果未找到巴爾的摩根 CA 憑證,請執行此指令以下載證書:

     `wget https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem -O Baltimore_CyberTrust_Root.pem`

1. 檢查是否存在DigiCert_Global_Root_CA憑證:

   `ls DigiCert_Global_Root_CA.pem`

    - 找不到DigiCert_Global_Root_CA,請執行以下指令下載憑證:

      ```shell
      wget http://www.digicert.com/CACerts/DigiCertGlobalRootCA.crt

      openssl x509 -in DigiCertGlobalRootCA.crt -inform der -outform pem -out DigiCert_Global_Root_CA.pem
      ```

1. 要更新新下載的憑證的憑證主題哈希,請運行重新哈希腳本:

   `c_rehash`

1. 要檢查主題哈希值是否為憑證建立了符號連結,請執行以下命令:

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

1. 使用檔案名稱_b204d74a.0_建立檔案_VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem_的複本:

   `cp VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem b204d74a.0`

1. 使用檔案名_653b494a.0_建立檔案_Baltimore_CyberTrust_Root.pem_的複本:

   `cp Baltimore_CyberTrust_Root.pem 653b494a.0`

1. 使用檔案名稱_3513523f.0_建立檔案_DigiCert_Global_Root_CA.pem_的複本:

   `cp DigiCert_Global_Root_CA.pem 3513523f.0`

1. 檢查檔是否存在:

   ```shell
   ls -l 653b494a.0 b204d74a.0 3513523f.0
   ```

   ```Output
   -rw-r--r-- 1 root root 1774 Jan  8 09:52 3513523f.0

   -rw-r--r-- 1 root root 1303 Jan  8 09:52 653b494a.0

   -rw-r--r-- 1 root root 1774 Jan  8 09:52 b204d74a.0
   ```

## <a name="outbound-connectivity-for-site-recovery-urls-or-ip-ranges-error-code-151037-or-151072"></a>Site Recovery URL 或 IP 範圍的輸出連線能力 (錯誤碼 151037 或 151072)

要使網站恢復複製正常工作,需要 VM 與特定 URL 的出站連接。 如果您的 VM 位於防火牆後方，或使用網路安全性群組 (NSG) 規則控制輸出連線能力，您可能會遇到下列其中一個問題。 儘管我們繼續支援通過 URL 進行出站訪問,但不再支援使用允許的 IP 範圍清單。

### <a name="issue-1-failed-to-register-azure-virtual-machine-with-site-recovery-151195"></a>問題 1：無法向 Site Recovery 註冊 Azure 虛擬機器 (151195)

#### <a name="possible-causes"></a>可能的原因

- 由於功能變數名稱系統 (DNS) 解析失敗,無法建立與網站恢復終結點的連接。
- 當您在虛擬機上出現故障,但無法從災難恢復 (DR) 區域到達 DNS 伺服器時,此問題在重新保護期間更常見。

#### <a name="fix-the-problem"></a>修正問題

如果使用自定義 DNS,請確保 DNS 伺服器可從災難恢復區域訪問。

要檢查 VM 是否使用自訂 DNS 設定,請進行以下檢查:

1. 打開**虛擬機**並選擇 VM。
1. 導航到 VM**設置**並選擇 **「網路**」 。
1. 在**虛擬網路/子網**中,選擇連結以打開虛擬網路的資源頁面。
1. 跳到**設定「 設定」** 並選擇**DNS 伺服器**。

嘗試從虛擬機訪問 DNS 伺服器。 如果 DNS 伺服器無法訪問,則透過在 DNS 伺服器上失敗或在 DR 網路和 DNS 之間創建網站行來存取它。

:::image type="content" source="./media/azure-to-azure-troubleshoot-errors/custom_dns.png" alt-text="com 錯誤。":::

### <a name="issue-2-site-recovery-configuration-failed-151196"></a>問題 2：Site Recovery 設定失敗 (151196)

#### <a name="possible-cause"></a>可能的原因

無法連接到 Office 365 身份驗證和標識 IP4 終結點。

#### <a name="fix-the-problem"></a>修正問題

Azure 網站恢復需要訪問 Office 365 IP 範圍進行身份驗證。
如果使用 Azure 網路安全組 (NSG) 規則/ 防火牆代理來控制 VM 上的出站網路連接,請確保使用基於[Azure 活動目錄 (AAD) 服務標記的](/azure/virtual-network/security-overview#service-tags)NSG 規則允許存取 AAD。 我們不再支援基於 IP 位址的 NSG 規則。

### <a name="issue-3-site-recovery-configuration-failed-151197"></a>問題 3：	Site Recovery 設定失敗 (151197)

#### <a name="possible-cause"></a>可能的原因

無法建立到 Azure 網站恢復服務終結點的連接。

#### <a name="fix-the-problem"></a>修正問題

如果使用 Azure 網路安全組 (NSG) 規則/ 防火牆代理來控制 VM 上的出站網路連接,請確保使用服務標記。 我們不再支援使用透過 NSG 的 IP 位址允許清單進行 Azure 網站恢復。

### <a name="issue-4-azure-to-azure-replication-failed-when-the-network-traffic-goes-through-on-premises-proxy-server-151072"></a>問題 4:當網路流量通過本地代理伺服器 (151072) 時,Azure 到 Azure 複製失敗

#### <a name="possible-cause"></a>可能的原因

自定義代理設定無效,行動服務代理未從 Internet 資源管理員 (IE) 自動偵測代理設定。

#### <a name="fix-the-problem"></a>修正問題

1. 移動服務代理在 Windows 和 Linux`/etc/environment`上檢測來自 IE 的代理設置。
1. 如果您希望只為行動服務設定代理,則可以在_ProxyInfo.conf_中提供位於:

   - **Linux**:`/usr/local/InMage/config/`
   - **視窗**:`C:\ProgramData\Microsoft Azure Site Recovery\Config`

1. _代理資訊.conf_應具有以下_INI_格式的代理設定。

   ```plaintext
   [proxy]
   Address=http://1.2.3.4
   Port=567
   ```

> [!NOTE]
> 移動服務代理伺服器不**支援 未經認證的代理**程式 。

### <a name="more-information"></a>詳細資訊

要指定[所需的 URL](azure-to-azure-about-networking.md#outbound-connectivity-for-urls)或所需的[IP 範圍](azure-to-azure-about-networking.md#outbound-connectivity-using-service-tags),請按照[Azure 到 Azure 複製中的「關於網路」中的](azure-to-azure-about-networking.md)指南進行操作。

## <a name="disk-not-found-in-the-machine-error-code-150039"></a>在機器中找不到磁碟 (錯誤碼 150039)

必須先將連接到 VM 的新磁碟初始化。 如果未找到磁碟,將顯示以下訊息:

```Output
Azure data disk <DiskName> <DiskURI> with logical unit number <LUN> <LUNValue> was not mapped to a corresponding disk being reported from within the VM that has the same LUN value.
```

### <a name="possible-causes"></a>可能的原因

- 新數據磁碟已附加到 VM,但未初始化。
- VM 中的數據磁碟無法正確報告磁碟附加到 VM 的邏輯單位編號 (LUN) 值。

### <a name="fix-the-problem"></a>修正問題

確保數據磁碟已初始化,然後重試該操作。

- **視窗**:[附加與初始化新的磁碟](/azure/virtual-machines/windows/attach-managed-disk-portal)。
- **Linux**:[在Linux中初始化一個新的資料磁碟](/azure/virtual-machines/linux/add-disk)。

若問題持續發生，請連絡支援服務。

## <a name="one-or-more-disks-are-available-for-protection-error-code-153039"></a>一個或多個磁碟可供保護(錯誤代碼 153039)

### <a name="possible-causes"></a>可能的原因

- 保護后,最近向虛擬機器添加了一個或多個磁碟。
- 保護虛擬機后,將初始化一個或多個磁碟。

### <a name="fix-the-problem"></a>修正問題

要使 VM 的複製狀態再次正常運行,可以選擇保護磁碟或關閉警告。

#### <a name="to-protect-the-disks"></a>保護磁碟

1. 跳到**複製項目** > _VM 名稱_ > **磁碟**。
1. 選擇未受保護的磁碟,然後選擇 **「開啟複製**:

   :::image type="content" source="./media/azure-to-azure-troubleshoot-errors/add-disk.png" alt-text="在 VM 磁碟上啟用複製。":::

#### <a name="to-dismiss-the-warning"></a>解除警告

1. 跳到**複製項目** > _VM 名稱_。
1. 在 **「概述」** 部分中選擇警告,然後選擇 **「確定**」。

   :::image type="content" source="./media/azure-to-azure-troubleshoot-errors/dismiss-warning.png" alt-text="關閉新磁碟警告。":::

## <a name="remove-the-virtual-machine-from-the-vault-completed-with-information-error-code-150225"></a>從已完成的資訊(錯誤代碼 150225)的保管庫中刪除虛擬機器

當網站恢復保護虛擬機器時,它會在源虛擬機器上創建連結。 刪除保護或禁用複製時,網站恢復將刪除這些連結作為清理作業的一部分。 如果虛擬機具有資源鎖定,則清理作業將隨資訊完成。 該資訊表示虛擬機已從恢復服務保管庫中刪除,但源計算機上無法清理某些陳舊連結。

如果您從未打算再次保護此虛擬機器,則可以忽略此警告。 但是,如果以後必須保護此虛擬機,請按照本節中的步驟清理連結。

> [!WARNING]
> 如果不進行清理:
>
> - 通過恢復服務保管庫啟用複製時,將不會列出虛擬機。
> - 如果嘗試使用**虛擬機器** > **設定** > **災難復原**來保護 VM,則操作將失敗, 因為**VM 上存在陳舊的資源連結,因此無法啟用複製**。

### <a name="fix-the-problem"></a>修正問題

> [!NOTE]
> 在執行這些步驟時,網站恢復不會刪除源虛擬機或以任何方式影響它。

1. 從 VM 或 VM 資源組中刪除鎖。 例如,在下圖中,必須刪除命名`MoveDemo`VM 上的資源鎖:

   :::image type="content" source="./media/site-recovery-azure-to-azure-troubleshoot/vm-locks.png" alt-text="從 VM 中刪除鎖。":::

1. 下載文稿以[移除此舊的網站重新設定](https://github.com/AsrOneSdk/published-scripts/blob/master/Cleanup-Stale-ASR-Config-Azure-VM.ps1)。
1. 執行文稿,_清理-陳舊-設定-Azure-VM.ps1_。 將**訂閱ID、VM****資源組**和**VM名稱**作為參數提供。
1. 如果系統提示您使用 Azure 認證,請提供它們。 然後驗證腳本是否運行沒有任何失敗。

## <a name="replication-cant-be-enabled-because-of-stale-resource-links-on-the-vm-error-code-150226"></a>由於 VM 上陳舊的資源連結(錯誤代碼 150226),無法啟用複製

### <a name="possible-causes"></a>可能的原因

虛擬機具有以前站點恢復保護的陳舊配置。

如果使用網站恢復為 Azure VM 啟用複製,則 Azure VM 上可能會出現陳舊的配置,然後:

- 您禁用了複製,但源 VM 具有資源鎖定。
- 刪除網站恢復保管庫時未顯式禁用 VM 上的複製。
- 刪除包含網站恢復保管庫的資源組,而無需顯式禁用 VM 上的複製。

### <a name="fix-the-problem"></a>修正問題

> [!NOTE]
> 在執行這些步驟時,網站恢復不會刪除源虛擬機或以任何方式影響它。

1. 從 VM 或 VM 資源組中刪除鎖。 例如,在下圖中,必須刪除命名`MoveDemo`VM 上的資源鎖:

   :::image type="content" source="./media/site-recovery-azure-to-azure-troubleshoot/vm-locks.png" alt-text="從 VM 中刪除鎖。":::

1. 下載文稿以[移除此舊的網站重新設定](https://github.com/AsrOneSdk/published-scripts/blob/master/Cleanup-Stale-ASR-Config-Azure-VM.ps1)。
1. 執行文稿,_清理-陳舊-設定-Azure-VM.ps1_。 將**訂閱ID、VM****資源組**和**VM名稱**作為參數提供。
1. 如果系統提示您使用 Azure 認證,請提供它們。 然後驗證腳本是否運行沒有任何失敗。

## <a name="unable-to-see-the-azure-vm-or-resource-group-for-the-selection-in-the-enable-replication-job"></a>無法在開啟複製工作中查看所選取的 Azure VM 或資源群組

### <a name="issue-1-the-resource-group-and-source-virtual-machine-are-in-different-locations"></a>問題 1: 資源群組和來源虛擬機器位於不同位置

網站恢復當前要求源區域資源組和虛擬機位於同一位置。 否則,當您嘗試應用保護時,您將無法找到虛擬機或資源組。

作為解決方法,可以從 VM 而不是恢復服務保管庫啟用複製。 轉到**源 VM** > **屬性** > **災難復原**並啟用複製。

### <a name="issue-2-the-resource-group-isnt-part-of-the-selected-subscription"></a>問題 2:資源組不是所選訂閱的一部分

如果資源組不是所選訂閱的一部分,則在保護時可能無法找到資源組。 確保資源組屬於您正在使用的訂閱。

### <a name="issue-3-stale-configuration"></a>問題 3:陳舊配置

如果 Azure VM 上存在陳舊的網站恢復配置,則可能無法看到要啟用複製的 VM。 如果使用網站恢復為 Azure VM 啟用複製,則可能發生此情況,然後發生:

- 刪除網站恢復保管庫時未顯式禁用 VM 上的複製。
- 刪除包含網站恢復保管庫的資源組,而無需顯式禁用 VM 上的複製。
- 您禁用了複製,但源 VM 具有資源鎖定。

### <a name="fix-the-problem"></a>修正問題

> [!NOTE]
> 在使用本節中提到的腳本`AzureRM.Resources`之前,請確保更新模組。 在執行這些步驟時,網站恢復不會刪除源虛擬機或以任何方式影響它。

1. 從 VM 或 VM 資源組中刪除鎖(如果有)。 例如,在下圖中,必須刪除命名`MoveDemo`VM 上的資源鎖:

   :::image type="content" source="./media/site-recovery-azure-to-azure-troubleshoot/vm-locks.png" alt-text="從 VM 中刪除鎖。":::

1. 下載文稿以[移除此舊的網站重新設定](https://github.com/AsrOneSdk/published-scripts/blob/master/Cleanup-Stale-ASR-Config-Azure-VM.ps1)。
1. 執行文稿,_清理-陳舊-設定-Azure-VM.ps1_。 將**訂閱ID、VM****資源組**和**VM名稱**作為參數提供。
1. 如果系統提示您使用 Azure 認證,請提供它們。 然後驗證腳本是否運行沒有任何失敗。

## <a name="unable-to-select-a-virtual-machine-for-protection"></a>無法選擇虛擬機器進行保護

### <a name="possible-cause"></a>可能的原因

虛擬機器的延伸已安裝在故障或無回應狀態

### <a name="fix-the-problem"></a>修正問題

轉到**虛擬機** > **設定** > **擴展,** 並檢查任何處於失敗狀態的擴展。 卸載任何失敗的擴展,然後重試以保護虛擬機。

## <a name="the-vms-provisioning-state-isnt-valid-error-code-150019"></a>VM 的預先狀態無效(錯誤代碼 150019)

要在 VM 上啟用複製,必須**成功**其預配狀態。 依以下步驟檢查預配狀態:

1. 在 Azure 門戶中,從**所有服務**中選擇**資源資源管理員**。
1. 展開 [訂用帳戶]**** 清單然後選取您的訂用帳戶。
1. 展開 [ResourceGroups]**** 清單然後選取 VM 的資源群組。
1. 展開 **「資源」** 清單並選擇 VM。
1. 檢查右側實例檢視中的**預配狀態**欄位。

### <a name="fix-the-problem"></a>修正問題

- 如果**預配狀態****失敗**,請與支援人員聯繫以進行故障排除。
- 如果**預配狀態****正在更新**,則可能正在部署另一個擴展。 檢查 VM 上是否有任何正在進行的操作,等待它們完成,然後重試失敗的網站恢復作業以啟用複製。

## <a name="unable-to-select-target-vm-network-selection-tab-is-unavailable"></a>無法選擇目標 VM(網路選擇選項卡不可用)

### <a name="issue-1-your-vm-is-attached-to-a-network-thats-already-mapped-to-a-target-network"></a>問題 1: 您的 VM 已連線到已映射到目標網路的網路

如果源 VM 是虛擬網路的一部分,並且來自同一虛擬網路的另一個 VM 已與目標資源組中的網路映射,則默認情況下網路選擇下拉列表框不可用(顯示為灰色)。

:::image type="content" source="./media/site-recovery-azure-to-azure-troubleshoot/unabletoselectnw.png" alt-text="網路選擇清單不可用。":::

### <a name="issue-2-you-previously-protected-the-vm-by-using-site-recovery-and-then-you-disabled-the-replication"></a>問題 2:您以前使用網站恢復保護 VM,然後關閉複製

關閉 VM 複製不會刪除網路映射。 必須從保護 VM 的恢復服務保管庫中刪除映射。 跳到**修復服務保存庫** > **站台修復基礎結構** > **網路映射**。

:::image type="content" source="./media/site-recovery-azure-to-azure-troubleshoot/delete_nw_mapping.png" alt-text="刪除網路映射。":::

在災難復原設定期間設定的目標網路可以在初始設定後以及 VM 保護後更改:

:::image type="content" source="./media/site-recovery-azure-to-azure-troubleshoot/modify_nw_mapping.png" alt-text="修改網路映射。":::

更改網路映射會影響使用同一網路映射的所有受保護 VM。

## <a name="com-or-volume-shadow-copy-service-error-error-code-151025"></a>COM+ 或捲影影複製服務錯誤(錯誤代碼 151025)

發生此錯誤時,將顯示以下訊息:

```Output
Site Recovery extension failed to install.
```

### <a name="possible-causes"></a>可能的原因

- COM+ 系統應用程式服務已禁用。
- 捲影影複製服務已禁用。

### <a name="fix-the-problem"></a>修正問題

將 COM+ 系統應用程式和捲影影複製服務設定為自動或手動啟動模式。

1. 在 Windows 中打開服務主控台。
1. 確保 COM+ 系統應用程式和卷卷卷複製服務未設置為 **「已禁用**」,將其設定為 **「啟動類型**」 。

   :::image type="content" source="./media/azure-to-azure-troubleshoot-errors/com-error.png" alt-text="檢查 COM 的啟動類型以及系統應用程式和卷卷卷複製服務。":::

## <a name="unsupported-managed-disk-size-error-code-150172"></a>不支援的託管磁碟大小(錯誤代碼 150172)

發生此錯誤時,將顯示以下訊息:

```Output
Protection couldn't be enabled for the virtual machine as it has <DiskName> with size <DiskSize> that is lesser than the minimum supported size 1024 MB.
```

### <a name="possible-cause"></a>可能的原因

磁碟小於支援的大小 1024 MB。

### <a name="fix-the-problem"></a>修正問題

請確保磁碟大小在支援的大小範圍內,然後重試該操作。

## <a name="protection-wasnt-enabled-because-the-grub-configuration-includes-the-device-name-instead-of-the-uuid-error-code-151126"></a>未開啟保護,因為 GRUB 設定包括裝置名稱而不是 UUID(錯誤代碼 151126)

### <a name="possible-causes"></a>可能的原因

Linux 大統一引導載入程式 (GRUB) 設定檔 _(/boot/grub/menu.lst,_ _/boot/grub/grub.cfg,_ _/引導/grub2/grub.cfg,_ 或 _/etc/預設/grub)_ 可能指定實際的`root``resume`設備名稱,而不是通用唯一標識符 (UUID) 值的 和 參數。 網站恢復需要 UUD,因為設備名稱可能會更改。 重新啟動後,VM 可能無法在故障轉移時出現相同的名稱,從而導致問題。

以下範例是來自 GRUB 檔的行,其中顯示裝置名稱而不是所需的 UUID:

- 檔案 _/開機/grub2/grub.cfg_:

  `linux /boot/vmlinuz-3.12.49-11-default root=/dev/sda2  ${extra_cmdline} resume=/dev/sda1 splash=silent quiet showopts`

- 檔案: _/開機/grub/選單.lst_

  `kernel /boot/vmlinuz-3.0.101-63-default root=/dev/sda2 resume=/dev/sda1 splash=silent crashkernel=256M-:128M showopts vga=0x314`

### <a name="fix-the-problem"></a>修正問題

將每個裝置名稱取代為相應的 UUID:

1. 通過執行命令`blkid <device name>`查找設備的 UUID。 例如：

   ```shell
   blkid /dev/sda1
   /dev/sda1: UUID="6f614b44-433b-431b-9ca1-4dd2f6f74f6b" TYPE="swap"
   blkid /dev/sda2
   /dev/sda2: UUID="62927e85-f7ba-40bc-9993-cc1feeb191e4" TYPE="ext3"
   ```

1. 將裝置名稱取代為其 UUID,`root=UUID=<UUID>`格式`resume=UUID=<UUID>`與 。 例如,更換后,_來自 /boot/grub/menu.lst_的行如下所示:

   `kernel /boot/vmlinuz-3.0.101-63-default root=UUID=62927e85-f7ba-40bc-9993-cc1feeb191e4 resume=UUID=6f614b44-433b-431b-9ca1-4dd2f6f74f6b splash=silent crashkernel=256M-:128M showopts vga=0x314`

1. 重試保護。

## <a name="enable-protection-failed-because-the-device-mentioned-in-the-grub-configuration-doesnt-exist-error-code-151124"></a>開啟保護失敗,因為 GRUB 設定中提到的裝置不存在(錯誤代碼 151124)

### <a name="possible-cause"></a>可能的原因

GRUB設定檔 _(/boot/grub/menu.lst_, _/引導/grub/grub.cfg,_ _/引導/grub2/grub.cfg,_ 或 _/etc/預設/grub)_`rd.lvm.lv`可能包含`rd_LVM_LV`參數或 。 這些參數標識將在啟動時發現的邏輯卷管理器 (LVM) 設備。 如果這些 LVM 設備不存在,則受保護的系統本身將不會啟動,並且將卡在啟動過程中。 故障轉移 VM 也會看到同樣的問題。 以下提供一些範例：

- 檔案: _/引導/grub2/grub.cfg_在 RHEL7:

  `linux16 /vmlinuz-3.10.0-957.el7.x86_64 root=/dev/mapper/rhel_mup--rhel7u6-root ro crashkernel=128M\@64M rd.lvm.lv=rootvg/root rd.lvm.lv=rootvg/swap rhgb quiet LANG=en_US.UTF-8`

- 檔案: RHEL7_上 /etc/預設/grub:_

  `GRUB_CMDLINE_LINUX="crashkernel=auto rd.lvm.lv=rootvg/root rd.lvm.lv=rootvg/swap rhgb quiet`

- 檔案: _/引導/grub/功能表.lst_在 RHEL6:

  `kernel /vmlinuz-2.6.32-754.el6.x86_64 ro root=UUID=36dd8b45-e90d-40d6-81ac-ad0d0725d69e rd_NO_LUKS LANG=en_US.UTF-8 rd_NO_MD SYSFONT=latarcyrheb-sun16 crashkernel=auto rd_LVM_LV=rootvg/lv_root  KEYBOARDTYPE=pc KEYTABLE=us rd_LVM_LV=rootvg/lv_swap rd_NO_DM rhgb quiet`

在每個範例中,GRUB 必須檢測具有`root`名稱`swap`和卷組`rootvg`的兩個 LVM 設備。

### <a name="fix-the-problem"></a>修正問題

如果 LVM 裝置不存在,請創建該設備或從 GRUB 設定檔中刪除相應的參數。 然後,請重試以啟用保護。

## <a name="a-site-recovery-mobility-service-update-finished-with-warnings-error-code-151083"></a>網站恢復行動服務更新已完成警告(錯誤代碼 151083)

網站恢復移動性服務具有許多元件,其中一個元件稱為篩選器驅動程式。 篩選器驅動程式僅在系統重新啟動期間載入到系統記憶體中。 每當行動服務更新包含篩選器驅動程式更改時,計算機都會更新,但仍會看到一條警告,指出某些修補程式需要重新啟動。 出現此警告是因為篩選器驅動程式修復僅在載入新篩選器驅動程式時才能生效,這僅在重新啟動期間發生。

> [!NOTE]
> 這只是警告。 即使在新的代理更新之後,現有複製仍繼續工作。 您可以選擇在希望新篩選器驅動程式的好處時重新啟動,但如果不重新啟動,舊篩選器驅動程式將繼續工作。
>
> 除了篩選器驅動程式之外,行動服務更新中任何其他增強和修復的好處都生效,無需重新啟動。

## <a name="protection-couldnt-be-enabled-because-the-replica-managed-disk-already-exists-without-expected-tags-in-the-target-resource-group-error-code-150161"></a>無法啟用保護,因為目標資源組中已存在複本託管磁碟,沒有預期標記(錯誤代碼 150161)

### <a name="possible-cause"></a>可能的原因

如果虛擬機以前受到保護,並且禁用複製時,副本磁碟未被刪除,則可能會出現此問題。

### <a name="fix-the-problem"></a>修正問題

刪除錯誤消息中識別的副本磁碟,然後重試失敗的保護作業。

## <a name="next-steps"></a>後續步驟

[將 Azure VM 複製到另一個 Azure 區域](azure-to-azure-how-to-enable-replication.md)
