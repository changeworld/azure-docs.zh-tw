---
title: 使用 Azure 防火牆來保護 Windows 虛擬桌面
description: 瞭解如何使用 Azure 防火牆來保護 Windows 虛擬桌面部署
author: vhorne
ms.service: firewall
services: firewall
ms.topic: conceptual
ms.date: 05/06/2020
ms.author: victorh
ms.openlocfilehash: 86b30b644da929f10f5d7c9642d5f89fbd29a7fa
ms.sourcegitcommit: 602e6db62069d568a91981a1117244ffd757f1c2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/06/2020
ms.locfileid: "82864059"
---
# <a name="use-azure-firewall-to-protect-window-virtual-desktop-deployments"></a>使用 Azure 防火牆來保護 Windows 虛擬桌面部署

Windows 虛擬桌面是在 Azure 上執行的桌面和應用程式虛擬化服務。 當終端使用者連線到 Windows 虛擬桌面環境時，其會話會由主機集區執行。 主機集區是 Azure 虛擬機器的集合，會向 Windows 虛擬桌上型電腦註冊為工作階段主機。 這些虛擬機器會在您的虛擬網路中執行，並且受限於虛擬網路安全性控制。 他們需要 Windows 虛擬桌面服務的輸出網際網路存取，才能正常運作，而且可能也需要終端使用者的輸出網際網路存取。 Azure 防火牆可協助您鎖定環境並篩選輸出流量。

[![Windows 虛擬桌面架構](media/protect-windows-virtual-desktop/windows-virtual-desktop-architecture-diagram.png)](media/protect-windows-virtual-desktop/windows-virtual-desktop-architecture-diagram.png#lightbox)

請遵循本文中的指導方針，使用 Azure 防火牆為您的 Windows 虛擬桌面主機集區提供額外的保護。

## <a name="prerequisites"></a>必要條件


 - 已部署的 Windows 虛擬桌面環境和主機集區。

   如需詳細資訊，請參閱[教學課程：使用 Azure Marketplace 建立主機集](../virtual-desktop/create-host-pools-azure-marketplace.md)區和[使用 Azure Resource Manager 範本建立主機集](../virtual-desktop/virtual-desktop-fall-2019/create-host-pools-arm-template.md)區。

若要深入瞭解 Windows 虛擬桌面環境，請參閱[Windows 虛擬桌面環境](../virtual-desktop/environment-setup.md)。

## <a name="host-pool-outbound-access-to-windows-virtual-desktop"></a>主機集區對 Windows 虛擬桌面的輸出存取

您為 Windows 虛擬桌面建立的 Azure 虛擬機器必須能夠存取數個完整功能變數名稱（Fqdn），才能正常運作。 Azure 防火牆提供 Windows 虛擬桌面 FQDN 標記來簡化此設定。 使用下列步驟來允許輸出 Windows 虛擬桌面平臺流量：

- 部署 Azure 防火牆，並設定您的 Windows 虛擬桌面主機集區子網使用者定義的路由（UDR），以透過 Azure 防火牆路由傳送所有流量。 您的預設路由現在會指向防火牆。
- 建立應用程式規則集合，並新增規則以啟用*WindowsVirtualDesktop* FQDN 標記。 來源 IP 位址範圍是主機集區虛擬網路，通訊協定是**HTTPs**，而目的地是**WindowsVirtualDesktop**。

- Windows 虛擬桌面主機集區所需的儲存體和服務匯流排帳戶集合是部署特定的，因此尚未在 WindowsVirtualDesktop FQDN 標記中加以捕捉。 您可以透過下列其中一種方式來解決此情況：

   - 允許從主機集區子網到 * xt.blob.core.windows.net、* eh.servicebus.windows.net 和 * xt.table.core.windows.net 的 HTTPs 存取。 這些萬用字元 Fqdn 會啟用所需的存取權，但較不嚴格。
   - 使用下列 log analytics 查詢來列出確切所需的 Fqdn，然後在您的防火牆應用程式規則中明確加以允許：
   ```
   AzureDiagnostics
   | where Category == "AzureFirewallApplicationRule"
   | search "Deny"
   | search "gsm*eh.servicebus.windows.net" or "gsm*xt.blob.core.windows.net" or "gsm*xt.table.core.windows.net"
   | parse msg_s with Protocol " request from " SourceIP ":" SourcePort:int " to " FQDN ":" *
   | project TimeGenerated,Protocol,FQDN
   ```

- 建立網路規則集合新增下列規則：

   - 允許 DNS-允許將私人 IP 位址的流量新增至 *，適用于 TCP 和 UDP 埠53。
   - 允許 KMS –允許從您的 Windows 虛擬桌面虛擬機器到 Windows 啟用服務 TCP 通訊埠1688的流量。 如需目的地 IP 位址的詳細資訊，請參閱[Windows 啟用在強制通道案例中失敗](../virtual-machines/troubleshooting/custom-routes-enable-kms-activation.md#solution)。

> [!NOTE]
> 某些部署可能不需要 DNS 規則，例如 Azure Active Directory 網域控制站將 DNS 查詢轉寄到 Azure DNS 168.63.129.16。

## <a name="host-pool-outbound-access-to-the-internet"></a>主機集區對網際網路的輸出存取

根據您的組織需求，您可能會想要為您的終端使用者啟用安全的輸出網際網路存取。 在已妥善定義允許目的地清單的情況下（例如[Office 365 存取](https://docs.microsoft.com/Office365/Enterprise/office-365-ip-web-service)），您可以使用 Azure 防火牆應用程式和網路規則來設定必要的存取權。 這會將使用者流量直接路由傳送至網際網路，以獲得最佳效能。

如果您想要使用現有的內部部署安全 web 閘道來篩選輸出使用者網際網路流量，您可以使用明確的 proxy 設定，在 Windows 虛擬桌面主機集區上執行 web 瀏覽器或其他應用程式。 例如，請參閱[如何使用 Microsoft Edge 命令列選項來設定 proxy 設定](https://docs.microsoft.com/deployedge/edge-learnmore-cmdline-options-proxy-settings)。 這些 proxy 設定只會影響您的使用者網際網路存取，以允許直接透過 Azure 防火牆的 Windows 虛擬桌面平臺輸出流量。

## <a name="additional-considerations"></a>其他考量

視您的需求而定，您可能需要設定其他防火牆規則：

- NTP 伺服器存取

   根據預設，執行 Windows 的虛擬機器會透過 UDP 埠123連線至 time.windows.com，以進行時間同步處理。 建立允許此存取的網路規則，或針對您在環境中使用的時間伺服器。


## <a name="next-steps"></a>後續步驟

- 深入瞭解 Windows 虛擬桌面：[何謂 Windows 虛擬桌面？](../virtual-desktop/overview.md)