---
title: 私人連結-適用於 MySQL 的 Azure 資料庫
description: 瞭解私用連結在適用於 MySQL 的 Azure 資料庫中的運作方式。
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: c2cc4986542404281424286882c046dec39f5daf
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/14/2020
ms.locfileid: "79371285"
---
# <a name="private-link-for-azure-database-for-mysql"></a>適用於 MySQL 的 Azure 資料庫的私用連結

私人連結可讓您透過私人端點連接到 Azure 中的各種 PaaS 服務。 Azure 私用連結基本上會將 Azure 服務帶入您的私用虛擬網路（VNet）內。 您可以使用私人 IP 位址來存取 PaaS 資源，就像 VNet 中的任何其他資源一樣。

如需支援私用連結功能之 PaaS 服務的清單，請參閱私用連結[檔](https://docs.microsoft.com/azure/private-link/index)。 私人端點是特定 [VNet](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) 和子網內的私人 IP 位址。

> [!NOTE]
> 這項功能適用于所有 Azure 區域，其中適用於 MySQL 的 Azure 資料庫支援一般用途和記憶體優化定價層。

## <a name="data-exfiltration-prevention"></a>預防資料外洩

資料的 ex-在適用於 MySQL 的 Azure 資料庫中篩選是當授權的使用者（例如資料庫管理員）能夠從一個系統中解壓縮資料，並將其移至組織外部的另一個位置或系統時。 例如，使用者將資料移至第三方所擁有的儲存體帳戶。

假設有一個在 Azure 虛擬機器（VM）內執行 MySQL 工作臺的使用者，而該應用程式會連線到在美國西部布建的適用於 MySQL 的 Azure 資料庫伺服器。 下列範例示範如何使用網路存取控制，在適用於 MySQL 的 Azure 資料庫上限制具有公用端點的存取。

* 將 [*允許 Azure 服務*] 設為 [關閉]，以停用透過公用端點適用於 MySQL 的 Azure 資料庫的所有 Azure 服務流量。 請確定不允許透過[防火牆規則](https://docs.microsoft.com/azure/mysql/concepts-firewall-rules)或[虛擬網路服務端點](https://docs.microsoft.com/azure/mysql/concepts-data-access-and-security-vnet)來存取伺服器的 IP 位址或範圍。

* 僅允許使用 VM 的私人 IP 位址對適用於 MySQL 的 Azure 資料庫的流量。 如需詳細資訊，請參閱[服務端點](concepts-data-access-and-security-vnet.md)和 [VNet 防火牆規則](howto-manage-vnet-using-portal.md)的相關文章。

* 在 Azure VM 上，使用網路安全性群組（Nsg）和服務標籤，縮小連出連線的範圍，如下所示

    * 指定 NSG 規則，以允許*服務標記 = SQL 的流量。WestUs* -只允許連接到美國西部的適用於 MySQL 的 Azure 資料庫
    * 指定 NSG 規則（優先順序較高）以拒絕服務標籤 *= SQL* -拒絕連線以更新至所有區域中的適用於 MySQL 的 Azure 資料庫</br></br>

在此設定結束時，Azure VM 只能連接到美國西部區域中的適用於 MySQL 的 Azure 資料庫。 不過，連線並不限於單一適用於 MySQL 的 Azure 資料庫。 VM 仍然可以連接到美國西部區域中的任何適用於 MySQL 的 Azure 資料庫，包括不屬於訂用帳戶的資料庫。 雖然在上述案例中，我們已將資料外泄範圍縮減到特定區域，但我們尚未完全消除此問題。</br>

透過私用連結，您現在可以設定網路存取控制，例如 Nsg，以限制對私人端點的存取。 這麼一來，個別的 Azure PaaS 資源就會對應到特定的私人端點。 惡意的 insider 只能存取對應的 PaaS 資源（例如適用於 MySQL 的 Azure 資料庫），而且沒有其他資源。

## <a name="on-premises-connectivity-over-private-peering"></a>透過私人對等互連的內部部署連線

當您從內部部署機器連線到公用端點時，必須使用伺服器層級防火牆規則，將您的 IP 位址新增至以 IP 為基礎的防火牆。 雖然此模型可針對開發或測試工作負載來允許個別機器的存取，但難以在生產環境中進行管理。

使用私人連結，您可以使用[Express Route](https://azure.microsoft.com/services/expressroute/) （ER）、私用對等互連或[VPN](https://docs.microsoft.com/azure/vpn-gateway/)通道，啟用對私人端點的跨單位存取。 他們之後可以透過公用端點停用所有存取，而不使用以 IP 為基礎的防火牆。

## <a name="configure-private-link-for-azure-database-for-mysql"></a>設定適用於 MySQL 的 Azure 資料庫的私人連結

### <a name="creation-process"></a>建立程序

需要私用端點才能啟用私用連結。 您可以使用下列操作指南來完成這項作業。

* [Azure 入口網站](https://docs.microsoft.com/azure/mysql/howto-configure-privatelink-portal)
* [CLI](https://docs.microsoft.com/azure/mysql/howto-configure-privatelink-cli)

### <a name="approval-process"></a>核准流程
一旦網路系統管理員建立私人端點（PE），MySQL 管理員就可以管理要適用於 MySQL 的 Azure 資料庫的私用端點連接（PEC）。 網路系統管理員和 DBA 之間的責任區隔，對於管理適用於 MySQL 的 Azure 資料庫連線很有説明。 

* 流覽至 Azure 入口網站中的適用於 MySQL 的 Azure 資料庫伺服器資源。 
    * 在左窗格中選取 [私人端點連接]
    * 顯示所有私人端點連接的清單（PECs）
    * 已建立對應的私用端點（PE）

![選取私人端點入口網站](media/concepts-data-access-and-security-private-link/select-private-link-portal.png)

* 從清單中選取個別的 PEC。

![選取要等待核准的私人端點](media/concepts-data-access-and-security-private-link/select-private-link.png)

* MySQL 伺服器管理員可以選擇核准或拒絕 PEC，並選擇性地新增簡短文字回應。

![選取私人端點訊息](media/concepts-data-access-and-security-private-link/select-private-link-message.png)

* 核准或拒絕之後，清單會反映適當的狀態以及回應文字

![選取私人端點的最終狀態](media/concepts-data-access-and-security-private-link/show-private-link-approved-connection.png)

## <a name="use-cases-of-private-link-for-azure-database-for-mysql"></a>適用於 MySQL 的 Azure 資料庫的私人連結使用案例

用戶端可以從相同的 VNet 連線到私人端點、在相同區域中對等互連 VNet，或透過跨區域的 VNet 對 VNet 連線。 此外，用戶端可以使用 ExpressRoute、私人對等互連或 VPN 通道從內部部署環境進行連線。 以下是一個簡化的圖表，其中顯示常見的使用案例。

![選取私人端點總覽](media/concepts-data-access-and-security-private-link/show-private-link-overview.png)

### <a name="connecting-from-an-azure-vm-in-peered-virtual-network-vnet"></a>從對等互連虛擬網路 (VNet) 中的 Azure VM 進行連線
設定[VNet 對等互連](https://docs.microsoft.com/azure/virtual-network/tutorial-connect-virtual-networks-powershell)，以從對等互連 VNet 中的 Azure VM 建立對適用於 MySQL 的 Azure 資料庫的連線能力。

### <a name="connecting-from-an-azure-vm-in-vnet-to-vnet-environment"></a>從 VNet 對 VNet 環境中的 Azure VM 進行連線
設定[VNet 對 VNET VPN 閘道聯](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal)機，以建立從不同區域或訂用帳戶中的 Azure VM 連接到適用於 MySQL 的 Azure 資料庫。

### <a name="connecting-from-an-on-premises-environment-over-vpn"></a>透過 VPN 從內部部署環境連線
若要建立從內部部署環境到適用於 MySQL 的 Azure 資料庫的連線，請選擇並執行其中一個選項：

* [點對站連線](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps)
* [站對站 VPN 連線](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell)
* [ExpressRoute 線路](https://docs.microsoft.com/azure/expressroute/expressroute-howto-linkvnet-portal-resource-manager)

## <a name="private-link-combined-with-firewall-rules"></a>結合防火牆規則的私用連結

當您使用私用連結搭配防火牆規則時，可能會發生下列情況和結果：

* 如果您未設定任何防火牆規則，則根據預設，不會有任何流量可以存取適用於 MySQL 的 Azure 資料庫。

* 如果您設定公用流量或服務端點，並建立私人端點，則不同類型的連入流量會由對應類型的防火牆規則所授權。

* 如果您未設定任何公用流量或服務端點，並建立私人端點，則適用於 MySQL 的 Azure 資料庫只能透過私人端點來存取。 如果您未設定公用流量或服務端點，則在拒絕或刪除所有已核准的私人端點之後，就不會有任何流量可以存取適用於 MySQL 的 Azure 資料庫。

## <a name="deny-public-access-for-azure-database-for-mysql"></a>拒絕適用於 MySQL 的 Azure 資料庫的公用存取

如果您只想要依賴私人端點來存取其適用於 MySQL 的 Azure 資料庫，您可以在資料庫伺服器上設定**拒絕公用網路存取**設定，以停用設定所有公用端點（例如[防火牆規則](concepts-firewall-rules.md)和[VNet 服務端點](concepts-data-access-and-security-vnet.md)）。 

當此設定設為 *[是]* 時，只允許透過私人端點的連線到您的適用於 MySQL 的 Azure 資料庫。 當此設定設為 [*否*] 時，用戶端可以根據您的防火牆或 VNet 服務端點設定連線到您的適用於 MySQL 的 Azure 資料庫。 此外，一旦設定了 [私人網路存取] 的值，您就無法新增和/或更新現有的防火牆和 VNet 服務端點規則。

> [!Note]
> 這項功能適用于所有 Azure 區域，其中適用於 PostgreSQL 的 Azure 資料庫單一伺服器支援一般用途和記憶體優化定價層。
>
> 此設定不會影響適用於 MySQL 的 Azure 資料庫的 SSL 和 TLS 設定。

若要瞭解如何為您的適用於 MySQL 的 Azure 資料庫從 Azure 入口網站設定**拒絕公用網路存取**，請參閱[如何設定拒絕公用網路存取](howto-deny-public-network-access.md)。

## <a name="next-steps"></a>後續步驟

若要深入瞭解適用於 MySQL 的 Azure 資料庫的安全性功能，請參閱下列文章：

* 若要設定適用於 MySQL 的 Azure 資料庫的防火牆，請參閱[防火牆支援](https://docs.microsoft.com/azure/mysql/concepts-firewall-rules)。

* 若要瞭解如何為您的適用於 MySQL 的 Azure 資料庫設定虛擬網路服務端點，請參閱[設定從虛擬網路的存取](https://docs.microsoft.com/azure/mysql/concepts-data-access-and-security-vnet)。

* 如需適用於 MySQL 的 Azure 資料庫連線的總覽，請參閱[適用於 MySQL 的 Azure 資料庫連線性架構](https://docs.microsoft.com/azure/mysql/concepts-connectivity-architecture)
