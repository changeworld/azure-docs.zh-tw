---
title: 將您的應用程式連接到 SQL 受控執行個體
titleSuffix: Azure SQL Managed Instance
description: 本文討論如何將您的應用程式連線至 Azure SQL 受控執行個體。
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova, vanto
ms.date: 11/09/2018
ms.openlocfilehash: dd5c6527cd6a0beea291dce94ff0e5949ba00671
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/28/2020
ms.locfileid: "92791251"
---
# <a name="connect-your-application-to-azure-sql-managed-instance"></a>將您的應用程式連線到 Azure SQL 受控執行個體
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

您在決定如何及何處裝載應用程式時，目前有多個選擇。

您可以選擇使用 Azure App Service 或部分 Azure 的虛擬網路整合選項（例如 Azure App Service 環境、Azure 虛擬機器和虛擬機器擴展集），在雲端中裝載應用程式。 您也可以採用混合式雲端方法，將您的應用程式留在內部部署環境。

無論您的選擇為何，您都可以將它連線到 Azure SQL 受控執行個體。 

![高可用性](./media/connect-application-instance/application-deployment-topologies.png)

本文說明如何在許多不同的應用程式案例中，將應用程式連線至 Azure SQL 受控執行個體。 

## <a name="connect-inside-the-same-vnet"></a>在相同的 VNet 內連接

將同一個虛擬網路內的應用程式連接到 SQL 受控執行個體是最簡單的案例。 虛擬網路內的虛擬機器可以直接連接到彼此，即使它們位於不同的子網內也一樣。 這表示您只需要將連接字串連接到 App Service 環境或虛擬機器中的應用程式即可。  

## <a name="connect-inside-a-different-vnet"></a>在不同的 VNet 內連接

將應用程式從 SQL 受控執行個體連接到不同的虛擬網路時，比較複雜一點，因為 SQL 受控執行個體在它自己的虛擬網路中有私人 IP 位址。 若要連接，應用程式需要存取部署 SQL 受控執行個體的虛擬網路。 因此，您需要在應用程式和 SQL 受控執行個體虛擬網路之間建立連線。 虛擬網路不一定要在相同的訂用帳戶中，才能讓此案例運作。

有兩個選項可連接虛擬網路：

- [Azure VNet 對等互連](../../virtual-network/virtual-network-peering-overview.md)
- VNet 對 VNet VPN 閘道 ([Azure 入口網站](../../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)、[PowerShell](../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md)、[Azure CLI](../../vpn-gateway/vpn-gateway-howto-vnet-vnet-cli.md))

使用對等互連是因為它使用 Microsoft 骨幹網路，因此從連線的觀點來看，對等互連虛擬網路中的虛擬機器和相同虛擬網路之間的延遲並沒有明顯的差異。 相同區域中的網路之間支援虛擬網路對等互連。 全域虛擬網路對等互連也支援下列注意事項中所述的限制。  

> [!IMPORTANT]
> [在9/22/2020 上，我們為新建立的虛擬叢集宣佈了全域虛擬網路對等互連](https://azure.microsoft.com/en-us/updates/global-virtual-network-peering-support-for-azure-sql-managed-instance-now-available/)。 這表示在公告日期之後于空白子網中建立的 SQL 受控實例，以及這些子網中建立的所有後續受控實例，都支援全域虛擬網路對等互連。 針對所有其他 SQL 受控實例對等互連支援，受限於相同區域中的網路，原因是 [全域虛擬網路對等互連的限制](../../virtual-network/virtual-network-manage-peering.md#requirements-and-constraints)。 另請參閱 [Azure 虛擬網路常見問題](../../virtual-network/virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers) 文章中的相關章節，以取得詳細資料。 

## <a name="connect-from-on-premises"></a>從內部部署連接 

您也可以將內部部署應用程式連線到 SQL 受控執行個體。 SQL 受控執行個體只能透過私人 IP 位址來存取。 若要從內部部署環境進行存取，您必須在應用程式與 SQL 受控執行個體虛擬網路之間建立站對站連線。

有兩個選項可供您將內部部署連線至 Azure 虛擬網路：

- 站對站 VPN 連線 ([Azure 入口網站](../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)、 [PowerShell](../../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md)、 [Azure CLI](../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-cli.md)) 
- [Azure ExpressRoute](../../expressroute/expressroute-introduction.md) 連線  

如果您已成功建立內部部署至 Azure 的連線，而且無法建立 SQL 受控執行個體的連線，請檢查您的防火牆是否有 SQL 埠1433上的開啟輸出連線，以及可供重新導向的11000-11999 範圍埠。

## <a name="connect-the-developer-box"></a>連接開發人員 box

您也可以將開發人員方塊連接至 SQL 受控執行個體。 SQL 受控執行個體只能透過私人 IP 位址存取，因此若要從您的開發人員方塊進行存取，您必須先在開發人員方塊與 SQL 受控執行個體虛擬網路之間進行連接。 若要這樣做，請使用原生 Azure 憑證驗證設定虛擬網路的點對站連線。 如需詳細資訊，請參閱  [設定從內部部署電腦連線到 AZURE SQL 受控執行個體的點對站](point-to-site-p2s-configure.md)連線。

## <a name="connect-with-vnet-peering"></a>使用 VNet 對等互連連接

客戶所執行的另一個案例是將 VPN 閘道安裝在不同的虛擬網路，而訂用帳戶會與裝載 SQL 受控執行個體的訂用帳戶一併安裝。 這兩個虛擬網路隨後會對等互連。 下列範例架構圖顯示其實作方式。

![虛擬網路對等互連](./media/connect-application-instance/vnet-peering.png)

設定好基本基礎結構之後，您必須修改某些設定，讓 VPN 閘道可以在裝載 SQL 受控執行個體的虛擬網路中看到 IP 位址。 若要這麼做，請在 [對等互連設定]  下方設定下列特定變更。

1. 在裝載 VPN 閘道的虛擬網路中，移至 **對等互連** ，移至適用于 SQL 受控執行個體的對等互連虛擬網路連線，然後按一下 [ **允許閘道傳輸** ]。
2. 在裝載 SQL 受控執行個體的虛擬網路中，移至 **對等互連** ，移至 VPN 閘道的對等互連虛擬網路連線，然後按一下 [ **使用遠端閘道** ]。

## <a name="connect-azure-app-service"></a>連接 Azure App Service 

您也可以連接 Azure App Service 所裝載的應用程式。 SQL 受控執行個體只能透過私人 IP 位址存取，因此若要從 Azure App Service 進行存取，您必須先在應用程式與 SQL 受控執行個體虛擬網路之間建立連線。 請參閱 [將您的應用程式與 Azure 虛擬網路整合](../../app-service/web-sites-integrate-with-vnet.md)。  

如需疑難排解，請參閱針對 [虛擬網路和應用程式進行疑難排解](../../app-service/web-sites-integrate-with-vnet.md#troubleshooting)。 如果無法建立連接，請嘗試 [同步處理網路](azure-app-sync-network-configuration.md)設定。

將 Azure App Service 連接到 SQL 受控執行個體的特殊案例是將 Azure App Service 整合到網路對等互連至 SQL 受控執行個體虛擬網路。 這種情況下需要設定下列組態：

- SQL 受控執行個體虛擬網路不能有閘道  
- SQL 受控執行個體虛擬網路必須 `Use remote gateways` 設定選項
- 對等互連虛擬網路必須 `Allow gateway transit` 設定選項

下圖說明此案例：

![整合式應用程式對等互連](./media/connect-application-instance/integrated-app-peering.png)

>[!NOTE]
>虛擬網路整合功能不會將應用程式與具有 ExpressRoute 閘道的虛擬網路整合。 即使 ExpressRoute 閘道是以共存模式設定，虛擬網路整合還是無法運作。 如果您需要透過 ExpressRoute 連線來存取資源，您可以使用 App Service 環境，其會在您的虛擬網路中執行。

## <a name="troubleshooting-connectivity-issues"></a>對連線問題進行疑難排解

若要對連線問題進行疑難排解，請檢閱以下幾點︰

- 如果您無法從相同虛擬網路內的 Azure 虛擬機器連線到 SQL 受控執行個體，但在不同的子網中，請檢查您是否在可能封鎖存取的 VM 子網上設定網路安全性群組。 此外，請開啟 SQL 埠1433上的輸出連線，以及範圍11000-11999 中的埠，因為透過 Azure 界限內的重新導向連接需要這些連線。
- 確定已針對與虛擬網路相關聯的路由表將 BGP 傳播設定為 [ **已啟用** ]。
- 如果使用 P2S VPN，請檢查 Azure 入口網站中的組態，以確認您是否看到 **輸入/輸出** 數值。 非零的數值表示 Azure 會將流量路由至內部部署，或從中輸出流量。

   ![輸入/輸出數值](./media/connect-application-instance/ingress-egress-numbers.png)

- 檢查執行 VPN 用戶端的用戶端電腦 (，) 具有您需要存取之所有虛擬網路的路由專案。 路由會儲存在 `%AppData%\ Roaming\Microsoft\Network\Connections\Cm\<GUID>\routes.txt`.

   ![route.txt 中](./media/connect-application-instance/route-txt.png)

   如下圖所示，每個涉及的虛擬網路都有兩個專案，而入口網站中設定的 VPN 端點會有第三個專案。

   除此之外，也可以透過下列命令來檢查路由。 下列輸出顯示各種子網路的路由：

   ```cmd
   C:\ >route print -4
   ===========================================================================
   Interface List
   14...54 ee 75 67 6b 39 ......Intel(R) Ethernet Connection (3) I218-LM
   57...........................rndatavnet
   18...94 65 9c 7d e5 ce ......Intel(R) Dual Band Wireless-AC 7265
   1...........................Software Loopback Interface 1
   Adapter===========================================================================

   IPv4 Route Table
   ===========================================================================
   Active Routes:
   Network Destination        Netmask          Gateway       Interface  Metric
          0.0.0.0          0.0.0.0       10.83.72.1     10.83.74.112     35
         10.0.0.0    255.255.255.0         On-link       172.26.34.2     43
         10.4.0.0    255.255.255.0         On-link       172.26.34.2     43
   ===========================================================================
   Persistent Routes:
   None
   ```

- 如果您是使用虛擬網路對等互連，請確定您已遵循設定 [允許閘道傳輸和使用遠端閘道](#connect-from-on-premises)的指示。

- 如果您使用虛擬網路對等互連來連線 Azure App Service 裝載的應用程式，且 SQL 受控執行個體虛擬網路具有公用 IP 位址範圍，請確定您的託管應用程式設定允許將輸出流量路由傳送至公用 IP 網路。 遵循 [區域虛擬網路整合](../../app-service/web-sites-integrate-with-vnet.md#regional-vnet-integration)中的指示。

## <a name="required-versions-of-drivers-and-tools"></a>所需的驅動程式和工具版本

如果您想要連接到 SQL 受控執行個體，建議使用下列最基本版本的工具和驅動程式：

| 驅動程式/工具 | 版本 |
| --- | --- |
|.NET Framework | 4.6.1 (或 .NET Core) |
|ODBC 驅動程式| v17 |
|PHP 驅動程式| 5.2.0 |
|JDBC 驅動程式| 6.4.0 |
|Node.js 驅動程式| 2.1.1 |
|OLEDB 驅動程式| 18.0.2.0 |
|SSMS| 18.0 或 [更高版本](/sql/ssms/download-sql-server-management-studio-ssms) |
|[SMO](/sql/relational-databases/server-management-objects-smo/sql-server-management-objects-smo-programming-guide) | [150](https://www.nuget.org/packages/Microsoft.SqlServer.SqlManagementObjects) 或更高版本 |

## <a name="next-steps"></a>後續步驟

- 如需 SQL 受控執行個體的詳細資訊，請參閱 [何謂 sql 受控執行個體？](sql-managed-instance-paas-overview.md)。
- 如需示範如何建立新受控實例的教學課程，請參閱 [建立受控實例](instance-create-quickstart.md)。