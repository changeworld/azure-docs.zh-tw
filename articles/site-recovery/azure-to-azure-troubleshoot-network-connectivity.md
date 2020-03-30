---
title: 使用 Azure 網站恢復解決 Azure 與 Azure 災害復原的連接問題
description: 解決 Azure VM 災害復原中的連接問題
author: sideeksh
manager: rochakm
ms.topic: how-to
ms.date: 08/05/2019
ms.openlocfilehash: b082e1aca094dcb335a7268e4c116376d756fd3b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80292018"
---
# <a name="troubleshoot-azure-to-azure-vm-network-connectivity-issues"></a>解決 Azure 到 Azure VM 網路連接問題

本文介紹在將 Azure 虛擬機器從一個區域複製到另一個區域時與網路連接相關的常見問題。 有關網路要求的詳細資訊，請參閱[複製 Azure VM 的連接要求](azure-to-azure-about-networking.md)。

若要使 Site Recovery 複寫正常運作，VM 需要特定 URL 或 IP 範圍的輸出連線能力。 如果您的 VM 位於防火牆後方，或使用網路安全性群組 (NSG) 規則控制輸出連線能力，您可能會遇到下列其中一個問題。

**Url** | **詳細資料**  
--- | ---
*.blob.core.windows.net | 需要此項目方可從 VM 將資料寫入來源地區的快取儲存體帳戶中。 如果您知道 VM 的所有緩存存儲帳戶，則可以允許列出特定的存儲帳戶 URL（例如，cache1.blob.core.windows.net和cache2.blob.core.windows.net），而不是 *.blob.core.windows.net
login.microsoftonline.com | 需要此項目方可進行 Site Recovery 服務 URL 的授權和驗證。
*.hypervrecoverymanager.windowsazure.com | 需要此項目方可從 VM 進行 Site Recovery 服務通訊。 如果您的防火牆代理支援 IP，則可以使用相應的"網站恢復 IP"。
*.servicebus.windows.net | 需要此項目方可從 VM 寫入 Site Recovery 監視和診斷資料。 如果您的防火牆代理支援 IP，則可以使用相應的"網站恢復監視 IP"。

## <a name="outbound-connectivity-for-site-recovery-urls-or-ip-ranges-error-code-151037-or-151072"></a>Site Recovery URL 或 IP 範圍的輸出連線能力 (錯誤碼 151037 或 151072)

## <a name="issue-1-failed-to-register-azure-virtual-machine-with-site-recovery-151195-br"></a><a name="issue-1-failed-to-register-azure-virtual-machine-with-site-recovery-151195-br"></a>問題 1：無法向 Site Recovery 註冊 Azure 虛擬機器 (151195) </br>
- **可能的原因** </br>
  - 由於 DNS 解析失敗，無法建立與網站恢復終結點的連接。
  - 此問題經常會在您已將虛擬機器容錯移轉，但 DR 區域無法連線到 DNS 伺服器的情況下進行重新保護的期間發生。

- **解析度**
   - 如果使用自訂 DNS，請確保 DNS 伺服器可從災害復原區域訪問。 若要查看您是否有自訂 DNS，請移至 VM> [災害復原網路] > [DNS 伺服器]。 嘗試從虛擬機器存取 DNS 伺服器。 如果無法訪問，則通過在 DNS 伺服器上失敗或在 DR 網路和 DNS 之間創建網站行來訪問它。

    ![com-error](./media/azure-to-azure-troubleshoot-errors/custom_dns.png)


## <a name="issue-2-site-recovery-configuration-failed-151196"></a>問題 2：Site Recovery 設定失敗 (151196)

> [!NOTE]
> 如果虛擬機器位於**標準**內部負載等化器後面，則預設情況下無法訪問 O365 IP（即login.microsoftonline.com）。 將其更改為**基本**內部負載等化器類型，或創建出站訪問，如[文章](https://aka.ms/lboutboundrulescli)所述。

- **可能的原因** </br>
  - 無法建立與 Office 365 驗證與身分識別 IP4 端點之間的連線。

- **解析度**
  - Azure Site Recovery 需要存取 Office 365 IP 範圍以進行驗證。
    如果您是使用 Azure 網路安全性群組 (NSG) 規則/防火牆 Proxy 來控制 VM 上的輸出網路連線能力，請確保您已允許與 O365 IP 範圍之間的通訊。 創建基於[Azure 活動目錄 （Azure AD） 服務標記](../virtual-network/security-overview.md#service-tags)的 NSG 規則，允許訪問與 Azure AD 對應的所有 IP 位址
      - 如果將來將新位址添加到 Azure AD 中，則需要創建新的 NSG 規則。

### <a name="example-nsg-configuration"></a>範例 NSG 設定

這個範例示範如何針對要複寫的 VM 設定 NSG 規則。

- 如果您使用 NSG 規則來控制輸出連線，請針對所有必要的 IP 位址範圍，將「允許 HTTPS 輸出」規則應用至連接埠：443。
- 此範例假設 VM 來源位置為「美國東部」，而目標位置為「美國中部」。

### <a name="nsg-rules---east-us"></a>NSG 規則：美國東部

1. 在 NSG 上針對 "Storage.EastUS" 建立輸出 HTTPS (443) 安全性規則，如以下螢幕擷取畫面所示。

      ![storage-tag](./media/azure-to-azure-about-networking/storage-tag.png)

2. 在 NSG 上針對 "AzureActiveDirectory" 建立輸出 HTTPS (443) 安全性規則，如以下螢幕擷取畫面所示。

      ![aad-tag](./media/azure-to-azure-about-networking/aad-tag.png)

3. 針對那些對應至目標位置的站台復原 IP，建立輸出 HTTPS (443) 規則：

   **位置** | **Site Recovery 位址** |  **Site Recovery 監視 IP 位址**
    --- | --- | ---
   美國中部 | 40.69.144.231 | 52.165.34.144

### <a name="nsg-rules---central-us"></a>NSG 規則：美國中部

需要這些規則，才能啟用從目標區域到來源地區容錯移轉後的複寫：

1. 在 NSG 上針對 "Storage.CentralUS" 建立輸出 HTTPS (443) 安全性規則。

2. 在 NSG 上針對 "AzureActiveDirectory" 建立輸出 HTTPS (443) 安全性規則。

3. 針對那些對應至來源位置的站台復原 IP 建立輸出 HTTPS (443) 規則：

   **位置** | **Site Recovery 位址** |  **Site Recovery 監視 IP 位址**
    --- | --- | ---
   美國中部 | 13.82.88.226 | 104.45.147.24
## <a name="issue-3-site-recovery-configuration-failed-151197"></a>問題 3：	Site Recovery 設定失敗 (151197)
- **可能的原因** </br>
  - 無法在連線至 Azure Site Recovery 服務端點。

- **解析度**
  - 根據區域的不同，Azure Site Recovery 需要存取不同的 [Site Recovery IP 範圍](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-about-networking#outbound-connectivity-using-service-tags)。 請確保虛擬機器可存取所需的 IP 範圍。


## <a name="issue-4-a2a-replication-failed-when-the-network-traffic-goes-through-on-premises-proxy-server-151072"></a>問題 4：當網路流量通過本地代理伺服器 （151072） 時，A2A 複製失敗
- **可能的原因** </br>
  - 自訂代理設置無效，Azure 網站恢復移動服務代理未從 IE 自動檢測代理設置


- **解析度**
  1. 行動服務代理程式偵測到 IE 的 Proxy 設定 (在 Windows 上) 和 /etc/environment (在 Linux 上)。
  2. 如果希望只為 Azure 網站恢復移動服務設置代理，則可以在 ProxyInfo.conf 中提供代理詳細資訊，該詳細資訊位於：</br>
     - ``/usr/local/InMage/config/`` (在 ***Linux*** 上)
     - ``C:\ProgramData\Microsoft Azure Site Recovery\Config``在***Windows***上
  3. ProxyInfo.conf 應該要有下列 INI 格式的 Proxy 設定。</br>
                *[代理]*</br>
                *位址*http://1.2.3.4*</br>
                Port=567**</br>
  4. Azure 網站恢復移動服務代理僅支援***未經身份驗證的代理***。

### <a name="fix-the-problem"></a>修正問題
要允許[所需的 URL](azure-to-azure-about-networking.md#outbound-connectivity-for-urls)或[所需的 IP 範圍](azure-to-azure-about-networking.md#outbound-connectivity-using-service-tags)，請按照[網路指導文檔中](site-recovery-azure-to-azure-networking-guidance.md)的步驟操作。


## <a name="next-steps"></a>後續步驟
[複寫 Azure 虛擬機器](site-recovery-replicate-azure-to-azure.md)
