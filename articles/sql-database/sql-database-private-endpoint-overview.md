---
title: 私人連結
description: 私人端點功能的概觀
author: rohitnayakmsft
ms.author: rohitna
titleSuffix: Azure SQL Database and SQL Data Warehouse
ms.service: sql-database
ms.topic: overview
ms.reviewer: vanto
ms.date: 03/09/2020
ms.openlocfilehash: ab9c5c5c1134d2e09a790a788a3b7e55f807dd9b
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/26/2020
ms.locfileid: "78945378"
---
# <a name="private-link-for-azure-sql-database-and-data-warehouse"></a>適用於 Azure SQL Database 和資料倉儲的 Private Link

Private Link 可讓您透過**私人端點**連線到 Azure 中的各種 PaaS 服務。 如需支援 Private Link 功能的 PaaS 服務清單，請移至 [Private Link 文件](../private-link/index.yml)頁面。 私人端點是特定 [VNet](../virtual-network/virtual-networks-overview.md) 和子網內的私人 IP 位址。 

> [!IMPORTANT]
> 本文適用於 Azure SQL Server，以及在 Azure SQL Server 上建立的 SQL Database 和 SQL 資料倉儲資料庫。 為了簡單起見，參考 SQL Database 和 SQL 資料倉儲時都會使用 SQL Database。 本文「不」  適用於 Azure SQL Database 中的**受控執行個體**部署。

## <a name="data-exfiltration-prevention"></a>預防資料外洩

當授權使用者 (例如資料庫管理員) 可以從某個系統擷取資料，並將資料移至組織外部的另一個位置或系統時，Azure SQL Database 中就會發生資料外泄。 例如，使用者將資料移至第三方所擁有的儲存體帳戶。

假設有一個案例，其中的使用者在連線到 SQL Database 的 Azure VM 內執行 SQL Server Management Studio (SSMS)。 此 SQL Database 位於「美國西部」資料中心。 下列範例示範如何使用網路存取控制，在 SQL Database 上限制公用端點的存取。

1. 將 [允許 Azure 服務] 設為 [關閉]  ，即可阻止所有 Azure 服務流量透過公用端點流向 SQL Database。 請確定伺服器和資料庫層級防火牆規則中不允許任何 IP 位址。 如需詳細資訊，請參閱 [Azure SQL Database 和資料倉儲網路存取控制](sql-database-networkaccess-overview.md)。
1. 僅允許使用 VM 的私人 IP 位址對 SQL Database 傳送流量。 如需詳細資訊，請參閱[服務端點](sql-database-vnet-service-endpoint-rule-overview.md)和 [VNet 防火牆規則](sql-database-firewall-configure.md)的相關文章。
1. 在 Azure VM 上，請使用[網路安全性群組 (NSG)](../virtual-network/manage-network-security-group.md) 和服務標籤來縮小傳出連線的範圍，如下所示
    - 指定 NSG 規則，以允許服務標記 = SQL.WestUs 的流量 - 僅允許連線到位於美國西部的 SQL Database
    - 指定 NSG 規則 (使用**較高的優先順序**) 以拒絕服務標籤 = SQL 的流量 - 拒絕連線到所有區域中的 SQL Database

在此設定結束時，Azure VM 只能連線到美國西部區域中的 SQL 資料庫。 不過，連線並不限於單一 SQL Database。 VM 仍然可以連接到美國西部區域中的任何 SQL Database，包括不屬於訂用帳戶的資料庫。 雖然在上述案例中，我們已將資料外泄範圍縮減到特定區域，但我們尚未完全消除此問題。

透過 Private Link，客戶現在可以設定網路存取控制 (例如 NSG) 來限制私人端點的存取。 這麼一來，個別的 Azure PaaS 資源就會對應到特定的私人端點。 懷有惡意的測試人員只能存取對應的 PaaS 資源 (例如 SQL Database)，不能存取其他資源。 

## <a name="on-premises-connectivity-over-private-peering"></a>透過私人對等互連的內部部署連線

當客戶從內部部署機器連線到公用端點時，他們的 IP 位址必須透過[伺服器層級防火牆規則](sql-database-server-level-firewall-rule.md)來新增至以 IP 為基礎的防火牆。 雖然此模型可針對開發或測試工作負載來允許個別機器的存取，但難以在生產環境中進行管理。

透過 Private Link，客戶可以使用 [ExpressRoute](../expressroute/expressroute-introduction.md)、私人對等互連或 VPN 通道來啟用私人端點的跨單位存取。 接著，客戶就可以停用所有透過公用端點的存取，而不使用以 IP 為基礎的防火牆來允許任何 IP 位址。

## <a name="how-to-set-up-private-link-for-azure-sql-database"></a>如何設定適用於 Azure SQL Database 的 Private Link 

### <a name="creation-process"></a>建立程序
您可以使用入口網站、PowerShell 或 Azure CLI 來建立私人端點：
- [入口網站](../private-link/create-private-endpoint-portal.md)
- [PowerShell](../private-link/create-private-endpoint-powershell.md)
- [CLI](../private-link/create-private-endpoint-cli.md)

### <a name="approval-process"></a>核准流程
網路管理員建立私人端點 (PE) 後，SQL 管理員就可以管理 SQL Database 的私人端點連線 (PEC)。

1. 依照下列螢幕擷取畫面中所示的步驟，瀏覽至 Azure 入口網站中的 SQL 伺服器資源

    - (1) 在左窗格中選取私人端點連線
    - (2) 顯示所有私人端點連線 (PEC) 的清單
    - (3) 對應的私人端點已建立 (PE) ![所有 PEC 的螢幕擷取畫面][3]

1. 從清單中選取個別的 PEC。
![已選取 PEC 的螢幕擷取畫面][6]

1. SQL 管理員可以選擇核准或拒絕 PEC，並選擇性地新增簡短文字回應。
![PEC 核准的螢幕擷取畫面][4]

1. 核准或拒絕之後，清單會反映適當的狀態以及回應文字。
![核准後的所有 PEC 螢幕擷取畫面][5]

## <a name="use-cases-of-private-link-for-azure-sql-database"></a>適用於 Azure SQL Database 的 Private Link 使用案例 

用戶端可以從相同的 VNet、相同區域中的對等互連 VNet，或透過跨區域的 VNet 對 VNet 連線來連線到私人端點。 此外，用戶端可以使用 ExpressRoute、私人對等互連或 VPN 通道從內部部署環境進行連線。 以下是一個簡化的圖表，其中顯示常見的使用案例。

 ![混合式連線選項][1]

## <a name="test-connectivity-to-sql-database-from-an-azure-vm-in-same-virtual-network-vnet"></a>測試從相同虛擬網路 (VNet) 中的 Azure VM 連線到 SQL Database

在此案例中，我們假設您已建立執行 Windows Server 2016 的 Azure 虛擬機器 (VM)。 

1. [啟動遠端桌面 (RDP) 工作階段並連線至虛擬機器](../virtual-machines/windows/connect-logon.md#connect-to-the-virtual-machine)。 
1. 接著，您可以使用下列工具執行一些基本連線檢查，以確保 VM 可透過私人端點連接到 SQL Database：
    1. Telnet
    1. Psping
    1. Nmap
    1. SQL Server Management Studio (SSMS)

### <a name="check-connectivity-using-telnet"></a>使用 Telnet 檢查連線能力

[Telnet 用戶端](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc754293%28v%3dws.10%29)是一項 Windows 功能，可以用來測試連線能力。 視 Windows 作業系統的版本而定，您可能需要明確地啟用此功能。 

在您安裝 Telnet 之後，請開啟 [命令提示字元] 視窗。 執行 Telnet 命令，並指定 SQL Database 的 IP 位址和私人端點。

```
>telnet 10.1.1.5 1433
```

當 Telnet 成功連線時，您會在命令視窗中看到一個空白畫面，如下圖所示：

 ![Telnet 的圖][2]

### <a name="check-connectivity-using-psping"></a>使用 Psping 檢查連線能力

[Psping](/sysinternals/downloads/psping) 可透過下列方式，檢查私人端點連線 (PEC) 是否正在接聽連接埠 1433 上的連線。

藉由提供 SQL Database 伺服器的 FQDN 和連接埠 1433 來執行 Psping，如下所示：

```
>psping.exe mysqldbsrvr.database.windows.net:1433

PsPing v2.10 - PsPing - ping, latency, bandwidth measurement utility
Copyright (C) 2012-2016 Mark Russinovich
Sysinternals - www.sysinternals.com

TCP connect to 10.6.1.4:1433:
5 iterations (warmup 1) ping test:
Connecting to 10.6.1.4:1433 (warmup): from 10.6.0.4:49953: 2.83ms
Connecting to 10.6.1.4:1433: from 10.6.0.4:49954: 1.26ms
Connecting to 10.6.1.4:1433: from 10.6.0.4:49955: 1.98ms
Connecting to 10.6.1.4:1433: from 10.6.0.4:49956: 1.43ms
Connecting to 10.6.1.4:1433: from 10.6.0.4:49958: 2.28ms
```

輸出顯示 Psping 可以偵測與 PEC 相關聯的私人 IP 位址。

### <a name="check-connectivity-using-nmap"></a>使用 Nmap 檢查連線能力

Nmap (網路對應程式) 是免費的開放原始碼工具，可用於網路探索和安全性審核。 如需詳細資訊和下載連結，請造訪 https://nmap.org 。您可以使用此工具來確保私人端點正在接聽連接埠 1433 上的連線。

藉由提供裝載私人端點的子網路位址範圍來執行 Nmap，如下所示。

```
>nmap -n -sP 10.1.1.0/24
...
...
Nmap scan report for 10.1.1.5
Host is up (0.00s latency).
Nmap done: 256 IP addresses (1 host up) scanned in 207.00 seconds
```

結果會顯示某個 IP 位址已啟動；該位址會對應至私人端點的 IP 位址。


### <a name="check-connectivity-using-sql-server-management-studio-ssms"></a>使用 SQL Server Management Studio (SSMS) 檢查連線能力
> [!NOTE]
> 在用戶端的連接字串中，使用伺服器的**完整網域名稱 (FQDN)** 。 任何直接對 IP 位址進行的登入嘗試都會失敗。 這是刻意設計的行為，因為私人端點會將流量路由至區域中的 SQL 閘道，而且必須指定 FQDN，登入才會成功。

請遵循此處的步驟來使用 [SSMS 連線到 SQL Database](sql-database-connect-query-ssms.md)。 使用 SSMS 連線到 SQL Database 之後，請執行下列查詢，以確認您可以從 Azure VM 的私人 IP 位址進行連線：

````
select client_net_address from sys.dm_exec_connections 
where session_id=@@SPID
````

## <a name="limitations"></a>限制 
私人端點的連線僅支援 **Proxy** 作為[連線原則](sql-database-connectivity-architecture.md#connection-policy)


## <a name="connecting-from-an-azure-vm-in-peered-virtual-network-vnet"></a>從對等互連虛擬網路 (VNet) 中的 Azure VM 進行連線 

設定 [VNet 對等互連](../virtual-network/tutorial-connect-virtual-networks-powershell.md)，以從對等互連 VNet 中的 Azure VM 連線到 SQL Database。

## <a name="connecting-from-an-azure-vm-in-vnet-to-vnet-environment"></a>從 VNet 對 VNet 環境中的 Azure VM 進行連線

設定 [VNet 對 VNet VPN 閘道連線](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)，以從不同區域或訂用帳戶中的 Azure VM 連線到 SQL Database。

## <a name="connecting-from-an-on-premises-environment-over-vpn"></a>透過 VPN 從內部部署環境連線

若要建立從內部部署環境到 SQL Database 的連線，請選擇並執行其中一個選項：
- [點對站連線](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)
- [站對站 VPN 連線](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md)
- [ExpressRoute 線路](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md)


## <a name="connecting-from-an-azure-sql-data-warehouse-to-azure-storage-using-polybase"></a>使用 Polybase 從 Azure SQL 資料倉儲連線到 Azure 儲存體

PolyBase 通常用於將資料從 Azure 儲存體帳戶載入 Azure SQL 資料倉儲。 如果您正在載入資料的來源 Azure 儲存體帳戶限制只能透過私人端點、服務端點或 IP 型防火牆存取一組 VNet 子網路，從 PolyBase 到帳戶的連線會中斷。 如需透過連線至固定到 VNet 的 Azure 儲存體的 Azure SQL 資料倉儲來啟用 PolyBase 匯入和匯出案例，請按照[此處](sql-database-vnet-service-endpoint-rule-overview.md#impact-of-using-vnet-service-endpoints-with-azure-storage)提供的步驟進行。 



## <a name="next-steps"></a>後續步驟

- 如需 Azure SQL Database 安全性的概觀，請參閱[保護您的資料庫](sql-database-security-overview.md)
- 如需 Azure SQL Database 連線的概觀，請參閱 [Azure SQL 連線架構](sql-database-connectivity-architecture.md)

<!--Image references-->
[1]: ./media/sql-database-get-started-portal/pe-connect-overview.png
[2]: ./media/sql-database-get-started-portal/telnet-result.png
[3]: ./media/sql-database-get-started-portal/pec-list-before.png
[4]: ./media/sql-database-get-started-portal/pec-approve.png
[5]: ./media/sql-database-get-started-portal/pec-list-after.png
[6]: ./media/sql-database-get-started-portal/pec-select.png
