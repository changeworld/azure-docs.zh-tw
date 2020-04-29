---
title: 使用 Azure Site Recovery 針對 Azure 到 Azure 嚴重損壞修復的連線進行疑難排解
description: 針對 Azure VM 嚴重損壞修復中的連線問題進行疑難排解
author: sideeksh
manager: rochakm
ms.topic: how-to
ms.date: 04/06/2020
ms.openlocfilehash: d2cc4133e52e7cab812413d23948da6ac2660e77
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80884863"
---
# <a name="troubleshoot-azure-to-azure-vm-network-connectivity-issues"></a>針對 Azure 至 Azure VM 網路連線問題進行疑難排解

本文說明當您從一個區域將 Azure 虛擬機器（VM）複寫及復原到另一個區域時，與網路連線相關的常見問題。 如需網路需求的詳細資訊，請參閱複寫[Azure vm 的連線能力需求](azure-to-azure-about-networking.md)。

若要使 Site Recovery 複寫正常運作，VM 需要特定 URL 或 IP 範圍的輸出連線能力。 如果您的 VM 位於防火牆後方，或使用網路安全性群組 (NSG) 規則控制輸出連線能力，您可能會遇到下列其中一個問題。

| URL | 詳細資料 |
|---|---|
| `*.blob.core.windows.net` | 需要此項目方可從 VM 將資料寫入來源地區的快取儲存體帳戶中。 如果您知道 Vm 的所有快取儲存體帳戶，您可以使用特定儲存體帳戶 Url 的允許清單。 例如，和`cache1.blob.core.windows.net` `cache2.blob.core.windows.net` ，而不`*.blob.core.windows.net`是。 |
| `login.microsoftonline.com` | 需要此項目方可進行 Site Recovery 服務 URL 的授權和驗證。 |
| `*.hypervrecoverymanager.windowsazure.com` | 需要此項目方可從 VM 進行 Site Recovery 服務通訊。 如果您的防火牆 proxy 支援 ip，您可以使用對應的_SITE RECOVERY IP_ 。 |
| `*.servicebus.windows.net` | 需要此項目方可從 VM 寫入 Site Recovery 監視和診斷資料。 如果您的防火牆 proxy 支援 ip，您可以使用對應的_Site Recovery 監視 IP_ 。 |

## <a name="outbound-connectivity-for-site-recovery-urls-or-ip-ranges-error-code-151037-or-151072"></a>Site Recovery URL 或 IP 範圍的輸出連線能力 (錯誤碼 151037 或 151072)

### <a name="issue-1-failed-to-register-azure-virtual-machine-with-site-recovery-151195"></a>問題 1：無法向 Site Recovery 註冊 Azure 虛擬機器 (151195)

#### <a name="possible-cause"></a>可能的原因

因為網域名稱系統（DNS）解析失敗，所以無法建立 Site Recovery 端點的連線。 當您已故障處理 VM，但無法從嚴重損壞修復（DR）區域連線到 DNS 伺服器時，此問題在重新保護期間更為常見。

#### <a name="resolution"></a>解決方案

如果您使用的是自訂 DNS，請確定可從嚴重損壞修復區域存取 DNS 伺服器。

若要檢查 VM 是否使用自訂 DNS 設定：

1. 開啟 [**虛擬機器**]，然後選取 VM。
1. 流覽至 [Vm]**設定**，然後選取 [**網路**]。
1. 在 [**虛擬網路/子網**] 中，選取連結以開啟虛擬網路的資源頁面。
1. 移至 [**設定**]，然後選取 [ **DNS 伺服器**]。

嘗試從虛擬機器存取 DNS 伺服器。 如果無法存取 DNS 伺服器，可透過容錯移轉 DNS 伺服器，或在 DR 網路和 DNS 之間建立這一行網站，讓它可以存取。

  :::image type="content" source="./media/azure-to-azure-troubleshoot-errors/custom_dns.png" alt-text="com-error":::

### <a name="issue-2-site-recovery-configuration-failed-151196"></a>問題 2：Site Recovery 設定失敗 (151196)

> [!NOTE]
> 如果 Vm 位於**標準**內部負載平衡器後方，則根據預設，它無法存取 Office 365 ip，例如`login.microsoftonline.com`。 將它變更為 [**基本**內部負載平衡器類型] 或 [建立輸出存取]，如[使用 Azure CLI 在 Standard Load Balancer 中設定負載平衡和輸出規則](/azure/load-balancer/configure-load-balancer-outbound-cli)一文所述。

#### <a name="possible-cause"></a>可能的原因

無法建立連接到 Office 365 驗證和身分識別 IP4 端點的連線。

#### <a name="resolution"></a>解決方案

- Azure Site Recovery 需要存取 Office 365 IP 範圍以進行驗證。
- 如果您使用 Azure 網路安全性群組（NSG）規則/防火牆 proxy 來控制 VM 上的輸出網路連線能力，請確定您允許與 Office 365 IP 範圍的通訊。 建立[Azure Active Directory （Azure AD）以服務標記](/azure/virtual-network/security-overview#service-tags)為基礎的 NSG 規則，以允許存取對應至 Azure AD 的所有 IP 位址。
- 如果未來將新的位址新增至 Azure AD，您就必須建立新的 NSG 規則。

### <a name="example-nsg-configuration"></a>範例 NSG 設定

這個範例示範如何針對要複寫的 VM 設定 NSG 規則。

- 如果您使用 NSG 規則來控制輸出連線能力，請針對所有必要的 IP 位址範圍使用 [**允許 HTTPS 輸出**規則至埠 443]。
- 此範例假設 VM 來源位置為「**美國東部**」，而目標位置為「**美國中部**」。

#### <a name="nsg-rules---east-us"></a>NSG 規則：美國東部

1. 建立 NSG 的 HTTPS 輸出安全性規則，如下列螢幕擷取畫面所示。 這個範例使用**目的地服務標記**： _EastUS_和**目的地埠範圍**： _443_。

     :::image type="content" source="./media/azure-to-azure-about-networking/storage-tag.png" alt-text="storage-tag":::

1. 建立 NSG 的 HTTPS 輸出安全性規則，如下列螢幕擷取畫面所示。 此範例使用**目的地服務標記**： _AzureActiveDirectory_和**目的地埠範圍**： _443_。

     :::image type="content" source="./media/azure-to-azure-about-networking/aad-tag.png" alt-text="aad-tag":::

1. 針對對應至目標位置的 Site Recovery Ip，建立 HTTPS 埠443輸出規則：

   | 位置 | Site Recovery 位址 | Site Recovery 監視 IP 位址 |
   | --- | --- | --- |
   | 美國中部 | 40.69.144.231 | 52.165.34.144 |

#### <a name="nsg-rules---central-us"></a>NSG 規則：美國中部

在此範例中，需要這些 NSG 規則，以便在容錯移轉後從目的地區域啟用複寫至來源區域：

1. 建立用於儲存的 HTTPS 輸出安全性規則_CentralUS_：

   - **目的地服務標記**：_儲存體. CentralUS_
   - **目的地埠範圍**： _443_

1. 建立_AzureActiveDirectory_的 HTTPS 輸出安全性規則。

   - **目的地服務標記**： _AzureActiveDirectory_
   - **目的地埠範圍**： _443_

1. 針對對應至來源位置的 Site Recovery Ip，建立 HTTPS 埠443輸出規則：

   | 位置 | Site Recovery 位址 | Site Recovery 監視 IP 位址 |
   | --- | --- | --- |
   | 美國東部 | 13.82.88.226 | 104.45.147.24 |

### <a name="issue-3-site-recovery-configuration-failed-151197"></a>問題 3：	Site Recovery 設定失敗 (151197)

#### <a name="possible-cause"></a>可能的原因

無法建立連接以 Azure Site Recovery 服務端點。

#### <a name="resolution"></a>解決方案

根據區域的不同，Azure Site Recovery 需要存取不同的 [Site Recovery IP 範圍](azure-to-azure-about-networking.md#outbound-connectivity-using-service-tags)。 請確定您可以從 VM 存取所需的 IP 範圍。

### <a name="issue-4-azure-to-azure-replication-failed-when-the-network-traffic-goes-through-on-premises-proxy-server-151072"></a>問題4：當網路流量通過內部部署 proxy 伺服器時，Azure 到 Azure 的複寫失敗（151072）

#### <a name="possible-cause"></a>可能的原因

自訂 proxy 設定無效，且 Azure Site Recovery 行動服務代理程式未從 Internet Explorer （IE）自動偵測 proxy 設定。

#### <a name="resolution"></a>解決方案

1. 行動服務代理程式會從 Windows 和`/etc/environment` Linux 上的 IE 偵測 proxy 設定。
1. 如果您只想要為 Azure Site Recovery 行動服務設定 proxy，您可以在_proxyinfo.conf_中提供 proxy 詳細資料，其位於：

   - **Linux**：`/usr/local/InMage/config/`
   - **Windows**：`C:\ProgramData\Microsoft Azure Site Recovery\Config`

1. _Proxyinfo.conf_應該具有下列_INI_格式的 proxy 設定：

   ```plaintext
   [proxy]
   Address=http://1.2.3.4
   Port=567
   ```

> [!NOTE]
> Azure Site Recovery 行動服務代理程式僅支援未**驗證**的 proxy。

### <a name="fix-the-problem"></a>修正問題

若要允許[所需的 url](azure-to-azure-about-networking.md#outbound-connectivity-for-urls)或[所需的 IP 範圍](azure-to-azure-about-networking.md#outbound-connectivity-using-service-tags)，請依照[網路指引檔](site-recovery-azure-to-azure-networking-guidance.md)中的步驟進行。

## <a name="next-steps"></a>後續步驟

[將 Azure Vm 複寫到另一個 Azure 區域](azure-to-azure-how-to-enable-replication.md)
