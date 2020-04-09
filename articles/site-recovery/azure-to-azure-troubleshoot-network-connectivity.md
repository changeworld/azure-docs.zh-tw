---
title: 使用 Azure 網站恢復解決 Azure 與 Azure 災難復原的連線問題
description: 解決 Azure VM 災難復原的連線問題
author: sideeksh
manager: rochakm
ms.topic: how-to
ms.date: 04/06/2020
ms.openlocfilehash: d2cc4133e52e7cab812413d23948da6ac2660e77
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/08/2020
ms.locfileid: "80884863"
---
# <a name="troubleshoot-azure-to-azure-vm-network-connectivity-issues"></a>解決 Azure 到 Azure VM 網路連線問題

本文介紹將 Azure 虛擬機器 (VM) 從一個區域複製到另一個區域時與網路連接相關的常見問題。 有關網路要求的詳細資訊,請參閱[複製 Azure VM 的連線要求](azure-to-azure-about-networking.md)。

若要使 Site Recovery 複寫正常運作，VM 需要特定 URL 或 IP 範圍的輸出連線能力。 如果您的 VM 位於防火牆後方，或使用網路安全性群組 (NSG) 規則控制輸出連線能力，您可能會遇到下列其中一個問題。

| URL | 詳細資料 |
|---|---|
| `*.blob.core.windows.net` | 需要此項目方可從 VM 將資料寫入來源地區的快取儲存體帳戶中。 如果您知道 VM 的所有快取儲存帳戶,則可以使用特定儲存帳戶網址的允許清單。 例如,`cache1.blob.core.windows.net``cache2.blob.core.windows.net``*.blob.core.windows.net`而不是 。 |
| `login.microsoftonline.com` | 需要此項目方可進行 Site Recovery 服務 URL 的授權和驗證。 |
| `*.hypervrecoverymanager.windowsazure.com` | 需要此項目方可從 VM 進行 Site Recovery 服務通訊。 如果您的防火牆代理支援 IP,則可以使用相應的_站點恢復 IP。_ |
| `*.servicebus.windows.net` | 需要此項目方可從 VM 寫入 Site Recovery 監視和診斷資料。 如果您的防火牆代理支援 IP,則可以使用相應的_站點恢復監視 IP。_ |

## <a name="outbound-connectivity-for-site-recovery-urls-or-ip-ranges-error-code-151037-or-151072"></a>Site Recovery URL 或 IP 範圍的輸出連線能力 (錯誤碼 151037 或 151072)

### <a name="issue-1-failed-to-register-azure-virtual-machine-with-site-recovery-151195"></a>問題 1：無法向 Site Recovery 註冊 Azure 虛擬機器 (151195)

#### <a name="possible-cause"></a>可能的原因

由於功能變數名稱系統 (DNS) 解析失敗,無法建立與網站恢復終結點的連接。 當您在 VM 上失敗,但無法從災難恢復 (DR) 區域到達 DNS 伺服器時,此問題在重新保護期間更常見。

#### <a name="resolution"></a>解決方案

如果使用自定義 DNS,請確保 DNS 伺服器可從災難恢復區域訪問。

要檢查 VM 是否使用自訂 DNS 設定,請進行以下檢查:

1. 打開**虛擬機**並選擇 VM。
1. 導航到 VM**設置**並選擇 **「網路**」 。
1. 在**虛擬網路/子網**中,選擇連結以打開虛擬網路的資源頁面。
1. 跳到**設定「 設定」** 並選擇**DNS 伺服器**。

嘗試從虛擬機訪問 DNS 伺服器。 如果 DNS 伺服器無法訪問,則透過在 DNS 伺服器上失敗或在 DR 網路和 DNS 之間創建網站行來存取它。

  :::image type="content" source="./media/azure-to-azure-troubleshoot-errors/custom_dns.png" alt-text="com-error":::

### <a name="issue-2-site-recovery-configuration-failed-151196"></a>問題 2：Site Recovery 設定失敗 (151196)

> [!NOTE]
> 如果 VM 位於**標準**內部負載均衡器後面,默認情況下,它將無法訪問 Office 365 IP(如`login.microsoftonline.com`)。 將改變為**基本**內部負載均衡器類型,或建立出站存取,如文章使用[Azure CLI 設定負載平衡器「中的負載平衡和出站規則](/azure/load-balancer/configure-load-balancer-outbound-cli)。

#### <a name="possible-cause"></a>可能的原因

無法連接到 Office 365 身份驗證和標識 IP4 終結點。

#### <a name="resolution"></a>解決方案

- Azure 網站恢復需要訪問 Office 365 IP 範圍進行身份驗證。
- 如果使用 Azure 網路安全組 (NSG) 規則/ 防火牆代理來控制 VM 上的出站網路連接,請確保允許與 Office 365 IP 範圍通訊。 創建基於[Azure 活動目錄 (Azure AD) 服務標記的](/azure/virtual-network/security-overview#service-tags)NSG 規則,允許存取與 Azure AD 對應的所有 IP 位址。
- 如果將來將新位址添加到 Azure AD 中,則需要創建新的 NSG 規則。

### <a name="example-nsg-configuration"></a>範例 NSG 設定

這個範例示範如何針對要複寫的 VM 設定 NSG 規則。

- 如果使用 NSG 規則來控制出站連接,請使用允許**HTTPS 出站**規則來移植 443 以處理所有必需的 IP 位址範圍。
- 該示例假定 VM 源位置**為美國東部**,目標位置為美國**中部**。

#### <a name="nsg-rules---east-us"></a>NSG 規則：美國東部

1. 為 NSG 創建 HTTPS 出站安全規則,如下圖所示。 此範例使用**目標服務標記** _:Storage.EastUS_和目標**埠範圍**: _443_。

     :::image type="content" source="./media/azure-to-azure-about-networking/storage-tag.png" alt-text="storage-tag":::

1. 為 NSG 創建 HTTPS 出站安全規則,如下圖所示。 此範例使用**目標服務標記** _:AzureActiveDirectory_和目標**埠範圍**: _443_。

     :::image type="content" source="./media/azure-to-azure-about-networking/aad-tag.png" alt-text="aad-tag":::

1. 為與目標位置對應的站台復原 IP 建立 HTTPS 連接埠 443 出站規則:

   | Location | Site Recovery 位址 | Site Recovery 監視 IP 位址 |
   | --- | --- | --- |
   | 美國中部 | 40.69.144.231 | 52.165.34.144 |

#### <a name="nsg-rules---central-us"></a>NSG 規則：美國中部

這個範例中,需要這些 NSG 規則,以便可以在故障轉移後從目標區域啟用複製到來源區域:

1. 為儲存建立 HTTPS 出站安全規則 _。_

   - **目標服務標籤**:_儲存.中央US_
   - **目標連接埠範圍**: _443_

1. 為_Azure ActiveDirectory_創建 HTTPS 出站安全規則。

   - **目標服務標記**: _Azure ActiveDirectory_
   - **目標連接埠範圍**: _443_

1. 為與來源位置對應的站台復原 IP 建立 HTTPS 連接埠 443 出站規則:

   | Location | Site Recovery 位址 | Site Recovery 監視 IP 位址 |
   | --- | --- | --- |
   | 美國東部 | 13.82.88.226 | 104.45.147.24 |

### <a name="issue-3-site-recovery-configuration-failed-151197"></a>問題 3：	Site Recovery 設定失敗 (151197)

#### <a name="possible-cause"></a>可能的原因

無法建立到 Azure 網站恢復服務終結點的連接。

#### <a name="resolution"></a>解決方案

根據區域的不同，Azure Site Recovery 需要存取不同的 [Site Recovery IP 範圍](azure-to-azure-about-networking.md#outbound-connectivity-using-service-tags)。 確保從 VM 可訪問所需的 IP 範圍。

### <a name="issue-4-azure-to-azure-replication-failed-when-the-network-traffic-goes-through-on-premises-proxy-server-151072"></a>問題 4:當網路流量通過本地代理伺服器 (151072) 時,Azure 到 Azure 複製失敗

#### <a name="possible-cause"></a>可能的原因

自定義代理設定無效,Azure 網站恢復行動服務代理未從 Internet 資源管理員 (IE) 自動偵測代理設定。

#### <a name="resolution"></a>解決方案

1. 移動服務代理在 Windows 和 Linux`/etc/environment`上檢測來自 IE 的代理設置。
1. 如果希望只為 Azure 網站恢復行動服務設定代理,則可以在_ProxyInfo.conf_中提供代理詳細資訊,該詳細資訊位於:

   - **Linux**:`/usr/local/InMage/config/`
   - **視窗**:`C:\ProgramData\Microsoft Azure Site Recovery\Config`

1. _代理資訊.conf_應具有以下_INI_格式的代理設定:

   ```plaintext
   [proxy]
   Address=http://1.2.3.4
   Port=567
   ```

> [!NOTE]
> Azure 網站恢復行動服務代理伺服器不**支援 未經身份認證的代理**程式 。

### <a name="fix-the-problem"></a>修正問題

要允許[所需的 URL](azure-to-azure-about-networking.md#outbound-connectivity-for-urls)或[所需的 IP 範圍](azure-to-azure-about-networking.md#outbound-connectivity-using-service-tags),請按照[網路指導文件中](site-recovery-azure-to-azure-networking-guidance.md)的步驟操作。

## <a name="next-steps"></a>後續步驟

[將 Azure VM 複製到另一個 Azure 區域](azure-to-azure-how-to-enable-replication.md)
