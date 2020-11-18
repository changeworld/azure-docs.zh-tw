---
title: 使用 Azure 防火牆來保護 Windows 虛擬桌面
description: 瞭解如何使用 Azure 防火牆來保護 Windows 虛擬桌面部署
author: vhorne
ms.service: firewall
services: firewall
ms.topic: how-to
ms.date: 05/06/2020
ms.author: victorh
ms.openlocfilehash: ad4f42d0e33f6d70c75abfcd1daab4f5aa9a515f
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/17/2020
ms.locfileid: "94654978"
---
# <a name="use-azure-firewall-to-protect-window-virtual-desktop-deployments"></a>使用 Azure 防火牆來保護 Windows 虛擬桌面部署

Windows 虛擬桌面是在 Azure 上執行的桌面和應用程式虛擬化服務。 當使用者連線到 Windows 虛擬桌面環境時，主機集區會執行其會話。 主機集區是一組 Azure 虛擬機器，會以工作階段主機的形式註冊至 Windows 虛擬桌面。 這些虛擬機器會在您的虛擬網路中執行，並且受限於虛擬網路安全性控制。 他們需要對 Windows 虛擬桌面服務進行輸出網際網路存取，才能正常運作，而且可能也需要終端使用者的輸出網際網路存取權。 Azure 防火牆可協助您鎖定環境並篩選輸出流量。

[![Windows 虛擬桌面架構 ](media/protect-windows-virtual-desktop/windows-virtual-desktop-architecture-diagram.png)](media/protect-windows-virtual-desktop/windows-virtual-desktop-architecture-diagram.png#lightbox)

遵循本文中的指導方針，使用 Azure 防火牆為您的 Windows 虛擬桌面主機集區提供額外的保護。

## <a name="prerequisites"></a>先決條件


 - 已部署的 Windows 虛擬桌面環境和主機集區。

   如需詳細資訊，請參閱 [教學課程：使用 Azure Marketplace 建立主機集](../virtual-desktop/create-host-pools-azure-marketplace.md) 區，並 [建立具有 Azure Resource Manager 範本的主機集](../virtual-desktop/virtual-desktop-fall-2019/create-host-pools-arm-template.md)區。

若要深入瞭解 Windows 虛擬桌面環境，請參閱 [Windows 虛擬桌面環境](../virtual-desktop/environment-setup.md)。

## <a name="host-pool-outbound-access-to-windows-virtual-desktop"></a>Windows 虛擬桌面的主機集區輸出存取

您為 Windows 虛擬桌面建立的 Azure 虛擬機器必須能夠存取數個完整功能變數名稱 (Fqdn) 才能正常運作。 Azure 防火牆提供 Windows 虛擬桌面 FQDN 標籤來簡化此設定。 使用下列步驟來允許輸出 Windows 虛擬桌面平臺流量：

- 部署 Azure 防火牆，並設定您的 Windows 虛擬桌面主機集區子網使用者定義路由 (UDR) ，以透過 Azure 防火牆路由傳送所有流量。 您的預設路由現在會指向防火牆。
- 建立應用程式規則集合，並新增規則以啟用 *WindowsVirtualDesktop* FQDN 標記。 來源 IP 位址範圍是主機集區虛擬網路，通訊協定是 **HTTPs**，目的地是 **WindowsVirtualDesktop**。

- Windows 虛擬桌面主機集區的一組必要的儲存體和服務匯流排帳戶是部署專屬的，因此尚未在 WindowsVirtualDesktop FQDN 標籤中加以捕捉。 您可以透過下列其中一種方式來解決這個情況：

   - 允許從您的主機集區子網進行 HTTPs 存取，以 * xt.blob.core.windows.net、* eh.servicebus.windows.net 和 * xt.table.core.windows.net。 這些萬用字元 Fqdn 允許必要的存取權，但較不嚴格。
   - 使用下列 log analytics 查詢來列出確切的必要 Fqdn，然後在您的防火牆應用程式規則中明確地允許它們：
   ```
   AzureDiagnostics
   | where Category == "AzureFirewallApplicationRule"
   | search "Deny"
   | search "gsm*eh.servicebus.windows.net" or "gsm*xt.blob.core.windows.net" or "gsm*xt.table.core.windows.net"
   | parse msg_s with Protocol " request from " SourceIP ":" SourcePort:int " to " FQDN ":" *
   | project TimeGenerated,Protocol,FQDN
   ```

- 建立網路規則集合新增下列規則：

   - 允許 DNS –允許來自您將私人 IP 位址的流量新增至 TCP 和 UDP 埠53的 *。
   - 允許 KMS –允許從您的 Windows 虛擬桌面虛擬機器到 Windows 啟用服務 TCP 埠1688的流量。 如需目的地 IP 位址的詳細資訊，請參閱 [強制通道案例中的 Windows 啟用失敗](../virtual-machines/troubleshooting/custom-routes-enable-kms-activation.md#solution)。

> [!NOTE]
> 某些部署可能不需要 DNS 規則，例如 Azure Active Directory 網域控制站將 DNS 查詢轉送至在 168.63.129.16 Azure DNS。

## <a name="host-pool-outbound-access-to-the-internet"></a>對網際網路的主機集區輸出存取

視您的組織需求而定，您可能會想要為終端使用者啟用安全的輸出網際網路存取。 如果允許的目的地清單定義完善 (例如， [Microsoft 365 存取](/microsoft-365/enterprise/microsoft-365-ip-web-service)) 您可以使用 Azure 防火牆應用程式和網路規則來設定所需的存取權。 這會將終端使用者流量直接路由傳送至網際網路，以獲得最佳效能。

如果您想要使用現有的內部部署安全 web 閘道來篩選輸出使用者的網際網路流量，您可以使用明確的 proxy 設定，設定在 Windows 虛擬桌面主機集區上執行的網頁瀏覽器或其他應用程式。 例如，請參閱 [如何使用 Microsoft Edge 命令列選項來設定 proxy 設定](/deployedge/edge-learnmore-cmdline-options-proxy-settings)。 這些 proxy 設定只會影響您的終端使用者網際網路存取，讓 Windows 虛擬桌面平臺可透過 Azure 防火牆直接輸出流量。

## <a name="additional-considerations"></a>其他考量

視您的需求而定，您可能需要設定額外的防火牆規則：

- NTP 伺服器存取

   依預設，執行 Windows 的虛擬機器會透過 UDP 埠123連線至 time.windows.com，以進行時間同步處理。 建立網路規則以允許此存取，或您在環境中使用的時間伺服器。


## <a name="next-steps"></a>後續步驟

- 深入瞭解 Windows 虛擬桌面： [什麼是 Windows 虛擬桌面？](../virtual-desktop/overview.md)