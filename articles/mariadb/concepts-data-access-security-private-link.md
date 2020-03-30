---
title: 專用連結 - MariaDB 的 Azure 資料庫
description: 瞭解專用連結如何適用于 MariaDB 的 Azure 資料庫。
author: kummanish
ms.author: manishku
ms.service: mariadb
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: b05a202537492fe54a76cf40a3b15987e099a7e3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79367715"
---
# <a name="private-link-for-azure-database-for-mariadb"></a>MariaDB Azure 資料庫的專用連結

專用連結允許您為 MariaDB 的 Azure 資料庫創建專用終結點，從而將 Azure 服務引入專用虛擬網路 （VNet）。 專用終結點公開可用於連接到 MariaDB 資料庫伺服器的 Azure 資料庫的專用 IP，就像 VNet 中的任何其他資源一樣。

有關支援專用連結功能的 PaaS 服務的清單，請查看專用連結[文檔](https://docs.microsoft.com/azure/private-link/index)。 私人端點是特定 [VNet](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) 和子網內的私人 IP 位址。

> [!NOTE]
> 此功能在所有 Azure 區域都可用，其中 MariaDB 的 Azure 資料庫支援通用和記憶體優化定價層。

## <a name="data-exfiltration-prevention"></a>預防資料外洩

MariaDB Azure 資料庫中的資料外過濾是授權使用者（如資料庫管理員）能夠從一個系統中提取資料並將其移動到組織外部的另一個位置或系統時。 例如，使用者將資料移至第三方所擁有的儲存體帳戶。

請考慮一個方案，即使用者在 Azure VM 內運行 MariaDB 工作臺，連接到 MariaDB 實例的 Azure 資料庫。 此 MariaDB 實例位於美國西部資料中心。 下面的示例演示如何使用網路訪問控制項限制使用 MariaDB Azure 資料庫上的公共終結點進行訪問。

* 通過設置"允許 Azure 服務關閉"，通過公共終結點禁用到 MariaDB Azure 資料庫的所有 Azure 服務流量。 確保不允許通過[防火牆規則](https://docs.microsoft.com/azure/mariadb/concepts-firewall-rules)或[虛擬網路服務終結點](https://docs.microsoft.com/azure/mariadb/concepts-data-access-security-vnet)訪問伺服器。

* 僅允許使用 VM 的私人 IP 位址訪問 MariaDB 的 Azure 資料庫。 如需詳細資訊，請參閱[服務端點](concepts-data-access-security-vnet.md)和 [VNet 防火牆規則](howto-manage-vnet-portal.md)的相關文章。

* 在 Azure VM 上，使用網路安全性群組 （NSG） 和服務標記縮小傳出連接的範圍，如下所示：

    * 指定 NSG 規則以允許服務標記和 SQL 的流量。WestUs - 只允許連接到美國西部 MariaDB 的 Azure 資料庫
    * 指定 NSG 規則（具有更高的優先順序）以拒絕服務標記和 SQL 的流量 - 拒絕與所有區域中的 MariaDB 資料庫的連接</br></br>

在此設置結束時，Azure VM 只能連接到美國西部區域的 MariaDB Azure 資料庫。 但是，連接不僅限於 MariaDB 的單個 Azure 資料庫。 VM 仍然可以連接到美國西部區域的 MariaDB 的任何 Azure 資料庫，包括不屬於訂閱的資料庫。 雖然在上述案例中，我們已將資料外泄範圍縮減到特定區域，但我們尚未完全消除此問題。</br>

使用專用連結，您現在可以設置網路訪問控制項（如 NSG）以限制對專用終結點的訪問。 這麼一來，個別的 Azure PaaS 資源就會對應到特定的私人端點。 惡意內部人員只能訪問映射的 PaaS 資源（例如 MariaDB 的 Azure 資料庫），而無法訪問其他資源。

## <a name="on-premises-connectivity-over-private-peering"></a>透過私人對等互連的內部部署連線

當您從本地電腦連接到公共終結點時，需要使用伺服器級防火牆規則將 IP 位址添加到基於 IP 的防火牆中。 雖然此模型可針對開發或測試工作負載來允許個別機器的存取，但難以在生產環境中進行管理。

使用專用鏈路，您可以使用[快速路由](https://azure.microsoft.com/services/expressroute/)（ER）、專用對等互連或[VPN 隧道](https://docs.microsoft.com/azure/vpn-gateway/)對專用終結點啟用跨本地訪問。 他們隨後可以通過公共終結點禁用所有訪問，而不使用基於 IP 的防火牆。

## <a name="configure-private-link-for-azure-database-for-mariadb"></a>為 MariaDB 配置 Azure 資料庫的專用連結

### <a name="creation-process"></a>建立程序

啟用專用連結需要專用終結點。 這可以使用以下操作指南來完成。

* [Azure 門戶](https://docs.microsoft.com/azure/mariadb/howto-configure-privatelink-portal)
* [Cli](https://docs.microsoft.com/azure/mariadb/howto-configure-privatelink-cli)

### <a name="approval-process"></a>核准流程

網路系統管理員創建專用終結點 （PE） 後，管理員可以管理 MariaDB 到 Azure 資料庫的專用終結點連接 （PEC）。 網路系統管理員和 DBA 之間的這種職責分離有助於管理 MariaDB 連接的 Azure 資料庫。 

* 導航到 Azure 門戶中 MariaDB 伺服器資源的 Azure 資料庫。 
    * 在左側窗格中選擇專用終結點連接
    * 顯示所有專用終結點連接 （PEC） 的清單
    * 創建的相應專用終結點 （PE）

![選擇專用終結點門戶](media/concepts-data-access-and-security-private-link/select-private-link-portal.png)

* 從清單中選取個別的 PEC。

![選擇待審批的專用終結點](media/concepts-data-access-and-security-private-link/select-private-link.png)

* MariaDB 伺服器管理員可以選擇批准或拒絕 PEC，並可以選擇添加簡短的文本回應。

![選擇專用終結點消息](media/concepts-data-access-and-security-private-link/select-private-link-message.png)

* 批准或拒絕後，清單將反映相應的狀態以及回應文本

![選擇私有終結點最終狀態](media/concepts-data-access-and-security-private-link/show-private-link-approved-connection.png)

## <a name="use-cases-of-private-link-for-azure-database-for-mariadb"></a>MariaDB Azure 資料庫的專用連結的用例

用戶端可以從同一 VNet、同一區域中的對等 VNet 或通過跨區域的 VNet 到 VNet 連接連接到專用終結點。 此外，用戶端可以使用 ExpressRoute、私人對等互連或 VPN 通道從內部部署環境進行連線。 以下是一個簡化的圖表，其中顯示常見的使用案例。

![選擇專用終結點概述](media/concepts-data-access-and-security-private-link/show-private-link-overview.png)

### <a name="connecting-from-an-azure-vm-in-peered-virtual-network-vnet"></a>從對等互連虛擬網路 (VNet) 中的 Azure VM 進行連線
配置[VNet 對等互連](https://docs.microsoft.com/azure/virtual-network/tutorial-connect-virtual-networks-powershell)，以便從對等 VNet 中的 Azure VM 建立與 MariaDB Azure 資料庫的連接。

### <a name="connecting-from-an-azure-vm-in-vnet-to-vnet-environment"></a>從 VNet 對 VNet 環境中的 Azure VM 進行連線
配置[VNet 到 VNet VPN 閘道連接](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal)，以便從其他區域或訂閱中的 Azure VM 建立與 MariaDB Azure 資料庫的連接。

### <a name="connecting-from-an-on-premises-environment-over-vpn"></a>透過 VPN 從內部部署環境連線
要建立從本地環境到 MariaDB Azure 資料庫的連接，請選擇並實現以下選項之一：

* [點對點連接](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps)
* [站對站 VPN 連線](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell)
* [ExpressRoute 線路](https://docs.microsoft.com/azure/expressroute/expressroute-howto-linkvnet-portal-resource-manager)

## <a name="private-link-combined-with-firewall-rules"></a>專用連結與防火牆規則相結合

當您將專用連結與防火牆規則結合使用時，可能會出現以下情況和結果：

* 如果不配置任何防火牆規則，則預設情況下，任何流量都無法訪問 MariaDB 的 Azure 資料庫。

* 如果配置公共流量或服務終結點並創建專用終結點，則不同類型的傳入流量將由相應類型的防火牆規則授權。

* 如果不配置任何公共流量或服務終結點，並且創建專用終結點，則 MariaDB 的 Azure 資料庫只能通過專用終結點訪問。 如果不配置公共流量或服務終結點，則在所有已批准的專用終結點被拒絕或刪除後，任何流量都無法訪問 MariaDB 的 Azure 資料庫。

## <a name="deny-public-access-for-azure-database-for-mariadb"></a>拒絕對 MariaDB 的 Azure 資料庫的公共訪問

如果只想完全依賴專用終結點來訪問其用於 MariaDB 的 Azure 資料庫，則可以通過在資料庫伺服器上設置 **"拒絕公共網路訪問**"配置來禁用設置所有公共終結點（[防火牆規則](concepts-firewall-rules.md)和[VNet 服務終結點](concepts-data-access-security-vnet.md)）。 

當此設置設置為 *"是"* 時，只允許通過專用終結點的連接到 MariaDB 的 Azure 資料庫。 當此設置設置為 NO*時*，用戶端可以基於防火牆或 VNet 服務終結點設置連接到 MariaDB 的 Azure 資料庫。 此外，設置私人網路絡訪問的值後，無法添加和/或更新現有防火牆和 VNet 服務終結點規則。

> [!Note]
> 此功能在所有 Azure 區域都可用，其中 Azure 資料庫適用于 PostgreSQL - 單個伺服器支援通用和記憶體優化定價層。
>
> 此設置對 MariaDB 的 Azure 資料庫的 SSL 和 TLS 配置沒有任何影響。

要瞭解如何從 Azure 門戶為 MariaDB 設置 Azure 資料庫**的拒絕公用網路訪問**，請參閱[如何配置拒絕公用網路訪問](howto-deny-public-network-access.md)。

## <a name="next-steps"></a>後續步驟

要瞭解有關 MariaDB 安全功能的 Azure 資料庫的詳細資訊，請參閱以下文章：

* 要為 MariaDB 配置 Azure 資料庫的防火牆，請參閱[防火牆支援](https://docs.microsoft.com/azure/mariadb/concepts-firewall-rules)。

* 要瞭解如何為 MariaDB 的 Azure 資料庫配置虛擬網路服務終結點，請參閱[配置來自虛擬網路的訪問](https://docs.microsoft.com/azure/mariadb/concepts-data-access-security-vnet)。

* 有關 MariaDB 連接的 Azure 資料庫概述，請參閱[MariaDB 連接體系結構的 Azure 資料庫](https://docs.microsoft.com/azure/MariaDB/concepts-connectivity-architecture)
