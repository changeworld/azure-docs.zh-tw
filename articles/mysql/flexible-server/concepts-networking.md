---
title: 網路功能總覽-適用於 MySQL 的 Azure 資料庫彈性的伺服器
description: 瞭解適用於 MySQL 的 Azure 資料庫的彈性伺服器部署選項中的連線能力和網路選項
author: ambhatna
ms.author: ambhatna
ms.service: mysql
ms.topic: conceptual
ms.date: 9/23/2020
ms.openlocfilehash: 48265856e5e745e05f6625766f9cd0c9b15c90a5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91708657"
---
# <a name="connectivity-and-networking-concepts-for-azure-database-for-mysql---flexible-server-preview"></a>適用於 MySQL 的 Azure 資料庫彈性的伺服器 (預覽) 的連線能力和網路概念

本文說明您伺服器的公用和私用連線能力選項。 您將詳細瞭解適用於 MySQL 的 Azure 資料庫有彈性的伺服器在 Azure 中安全地建立伺服器的網路概念。

> [!IMPORTANT]
> 適用於 MySQL 的 Azure 資料庫彈性的伺服器處於預覽階段。

## <a name="choosing-a-networking-option"></a>選擇網路選項
您有兩個網路選項可供您的適用於 MySQL 的 Azure 資料庫彈性伺服器使用。 包括**私人存取 (VNet 整合)** 和**公用存取 (允許的 IP 位址)** 。 在建立伺服器時，您必須挑選一個選項。 

> [!NOTE]
> 建立伺服器之後，就無法變更您的網路功能選項。 

* **私人存取 (VNet 整合)** - 您可以將彈性伺服器部署至 [Azure 虛擬網路](../../virtual-network/virtual-networks-overview.md)。 Azure 虛擬網路提供私人且安全的網路通訊。 這可讓虛擬網路中的資源透過私人 IP 位址通訊。

   如果您想使用下列功能，請選擇 VNet 整合選項：
   * 使用私人 IP 位址，從相同虛擬網路中的 Azure 資源連線到您的彈性伺服器
   * 使用 VPN 或 ExpressRoute 從非 Azure 資源連線到您的彈性伺服器
   * 彈性伺服器沒有公用端點

* **公用存取 (允許的 IP 位址) ** –您可透過公用端點存取彈性的伺服器。 公用端點是可公開解析的 DNS 位址。 「允許的 IP 位址」一詞指的是您選擇授與伺服器存取權的 IP 範圍。 這些權限稱為**防火牆規則**。 

   如果您想要下列功能，請選擇公用存取方法：
   * 從不支援虛擬網路的 Azure 資源連接
   * 從 Azure 外部的資源（未透過 VPN 或 ExpressRoute 連接）進行連線 
   * 彈性伺服器具有公用端點

無論您選擇使用私用存取或公用存取選項，都適用下列特性：
* 來自允許的 IP 位址的連線需要使用有效的認證向 MySQL 伺服器進行驗證
* 連線[加密](#tls-and-ssl)可用於您的網路流量
* 伺服器具有 (fqdn) 的完整功能變數名稱。 針對連接字串中的 hostname 屬性，我們建議使用 fqdn，而不是 IP 位址。
* 這兩個選項都會控制伺服器層級的存取，而不是資料庫或資料表層級的存取權。 您可以使用 MySQL 的角色屬性來控制資料庫、資料表和其他物件存取。


## <a name="private-access-vnet-integration"></a>私人存取 (VNet 整合)
使用虛擬網路的私人存取 (vnet) 整合提供適用于 MySQL 彈性伺服器的私用和安全通訊。

### <a name="virtual-network-concepts"></a>虛擬網路概念
以下是搭配使用虛擬網路與 MySQL 彈性伺服器時，必須熟悉的一些概念。

* **虛擬網路** -Azure 虛擬網路 (VNet) 包含針對您的用途設定的私人 IP 位址空間。 若要深入瞭解 Azure 虛擬網路，請造訪 [Azure 虛擬網路總覽](../../virtual-network/virtual-networks-overview.md) 。

    您的虛擬網路必須位於與您的彈性伺服器相同的 Azure 區域中。


* **委派的子網** -虛擬網路包含子網， (子網路) 。 子網可讓您將虛擬網路分割成較小的位址空間。 Azure 資源會部署到虛擬網路內的特定子網中。 

   您的 MySQL 彈性伺服器必須在 **委派** 的子網中，且僅適用于 mysql 彈性伺服器使用。 此委派表示只有適用於 MySQL 的 Azure 資料庫彈性伺服器可以使用該子網路。 委派的子網路中不可以有其他 Azure 資源類型。 您可以委派子網，方法是將其委派屬性指派為 Microsoft.dbformysql/flexibleServers。

* ** (NSG) 的網路安全性群組 ** 網路安全性群組中的安全性規則可讓您篩選可流入和流出虛擬網路子網和網路介面的網路流量類型。 如需詳細資訊，請參閱 [網路安全性群組總覽](../../virtual-network/network-security-groups-overview.md) 。


### <a name="unsupported-virtual-network-scenarios"></a>不支援的虛擬網路案例
* 公用端點 (或公用 IP 或 DNS) -部署至虛擬網路的彈性伺服器不能有公用端點
* 將彈性伺服器部署到虛擬網路和子網之後，您就無法將它移到另一個虛擬網路或子網。 您無法將虛擬網路移至另一個資源群組或訂用帳戶。
* 子網中有資源存在時，無法增加 (位址空間) 的子網大小
* 不支援跨區域的對等互連 Vnet

瞭解如何使用 [Azure 入口網站](how-to-manage-virtual-network-portal.md) 或 [Azure CLI](how-to-manage-virtual-network-cli.md)啟用私人存取 (vnet 整合) 。


## <a name="public-access-allowed-ip-addresses"></a>公用存取 (允許的 IP 位址)
公用存取方法的特性包括：
* 只有您允許的 IP 位址具有存取 MySQL 彈性伺服器的許可權。 預設不允許任何 IP 位址。 您可以在伺服器建立期間或之後新增 IP 位址。
* 您的 MySQL 伺服器具有可公開解析的 DNS 名稱
* 您的彈性伺服器不在其中一個 Azure 虛擬網路中
* 往返于伺服器的網路流量不會經過私人網路。 流量會使用一般網際網路路徑。

### <a name="firewall-rules"></a>防火牆規則
授與 IP 位址的許可權稱為「防火牆規則」。 如果連接嘗試來自您不允許的 IP 位址，則原始用戶端會看到錯誤。


### <a name="allowing-all-azure-ip-addresses"></a>允許所有 Azure IP 位址
如果您的 Azure 服務無法使用固定的連出 IP 位址，您可以考慮啟用來自所有 Azure 資料中心 IP 位址的連線。

> [!IMPORTANT]
> [ **允許來自 azure 服務的公用存取和 azure 中的資源** ] 選項會將防火牆設定為允許所有來自 azure 的連線，包括來自其他客戶訂用帳戶的連接。 選取這個選項時，請確定您的登入和使用者權限會限制為只有授權的使用者才能存取。

瞭解如何啟用和管理公用存取 (允許的 IP 位址) 使用 [Azure 入口網站](how-to-manage-firewall-portal.md) 或 [Azure CLI](how-to-manage-firewall-cli.md)。


### <a name="troubleshooting-public-access-issues"></a>針對公用存取問題進行疑難排解
當對適用于 MySQL 的 Microsoft Azure 資料庫伺服器服務的存取未如預期般運作時，請考慮下列幾點：

* **對允許清單的變更尚未生效：** 對適用於 MySQL 之 Azure 資料庫伺服器防火牆組態的變更最多可能會延遲 5 分鐘才能生效。

* **驗證失敗：** 如果使用者沒有適用於 MySQL 的 Azure 資料庫伺服器的許可權，或所使用的密碼不正確，則會拒絕與適用於 MySQL 的 Azure 資料庫伺服器的連線。 建立防火牆設定只會讓用戶端有機會嘗試連接到您的伺服器。 每個用戶端仍然必須提供必要的安全性認證。

* **動態用戶端 IP 位址：** 如果您有使用動態 IP 位址的網際網路連線，並且在通過防火牆時遇到問題，您可以嘗試下列其中一個解決方案：

   * 針對指派給存取適用於 MySQL 的 Azure 資料庫伺服器的用戶端電腦的 IP 位址範圍，要求您的網際網路服務提供者 (ISP) ，然後將 IP 位址範圍新增為防火牆規則。
   * 改為針對您的用戶端電腦取得靜態 IP 位址，然後將該靜態 IP 位址新增為防火牆規則。


## <a name="hostname"></a>主機名稱
無論您選擇何種網路選項，我們建議您在連線到彈性的伺服器時，一律使用 (FQDN) 作為主機名稱的完整功能變數名稱。 伺服器的 IP 位址不保證保持靜態。 使用 FQDN 將有助於避免變更您的連接字串。 

範例
* 推薦 `hostname = servername.mysql.database.azure.com`
* 可能的話，請避免使用 `hostname = 10.0.0.4` (私人位址) 或 `hostname = 40.2.45.67` (公用 IP) 



## <a name="tls-and-ssl"></a>TLS 和 SSL
適用於 MySQL 的 Azure 資料庫彈性的伺服器支援使用傳輸層安全性 (TLS) ，將用戶端應用程式連接至 MySQL 服務。 TLS 是一種業界標準通訊協定，可確保您的資料庫伺服器與用戶端應用程式之間的加密網路連接。 TLS 是安全通訊端層 (SSL) 的更新通訊協定。

適用於 MySQL 的 Azure 資料庫彈性的伺服器僅支援使用傳輸層安全性 (TLS 1.2) 的加密連接。 TLS 1.0 和 TLS 1.1 的所有連入連線都會遭到拒絕。 您無法停用或變更連接到適用於 MySQL 的 Azure 資料庫彈性伺服器的 TLS 版本。


## <a name="next-steps"></a>後續步驟
* 了解如何使用 [Azure 入口網站](how-to-manage-virtual-network-portal.md)或 [Azure CLI](how-to-manage-virtual-network-cli.md) 啟用私人存取 (vnet 整合)
* 了解如何使用 [Azure 入口網站](how-to-manage-firewall-portal.md) 或 [Azure CLI](how-to-manage-firewall-cli.md) 啟用公用存取 (允許的 IP 位址)
* 瞭解如何 [使用 TLS](how-to-connect-tls-ssl.md)
