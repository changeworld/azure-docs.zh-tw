---
title: 網路存取控制
description: Azure SQL 資料庫和資料倉儲的網路訪問控制項概述，用於管理訪問並配置單個或池資料庫。
services: sql-database
ms.service: sql-database
ms.subservice: security
titleSuffix: Azure SQL Database and SQL Data Warehouse
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: vanto
ms.date: 03/09/2020
ms.openlocfilehash: 822fab5c00501d415c3c184587141e869523e417
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78945386"
---
# <a name="azure-sql-database-and-data-warehouse-network-access-controls"></a>Azure SQL 資料庫和資料倉儲網路訪問控制項

> [!NOTE]
> 本文適用於 Azure SQL Server，以及在 Azure SQL Server 上建立的 SQL Database 和 SQL 資料倉儲資料庫。 為了簡單起見，參考 SQL Database 和 SQL 資料倉儲時都會使用 SQL Database。

> [!IMPORTANT]
> 本文「不」** 適用於 **Azure SQL Database 受控執行個體**。 有關網路設定的詳細資訊，請參閱[連接到託管實例](sql-database-managed-instance-connect-app.md)。

從[Azure 門戶](sql-database-single-database-get-started.md)創建新的 Azure SQL Server 時，結果為格式的公共終結點 *，yourservername.database.windows.net*。

您可以使用以下網路訪問控制項有選擇地允許通過公共終結點訪問 SQl 資料庫：
- 允許 Azure 服務：當設置為 ON 時，Azure 邊界內的其他資源（例如 Azure 虛擬機器）可以訪問 SQL 資料庫

- IP 防火牆規則：使用此功能顯式允許來自特定 IP 位址的連接，例如來自本地電腦的連接

您還可以允許從[虛擬網路](../virtual-network/virtual-networks-overview.md)從虛擬網路進行專用訪問 SQL 資料庫：
- 虛擬網路防火牆規則：使用此功能允許 Azure 邊界內的特定虛擬網路的流量

- 專用連結：使用此功能在特定虛擬網路中為 Azure SQL Server 創建專用終結點



有關這些存取控制及其操作的高級說明，請參閱以下視頻：
> [!VIDEO https://channel9.msdn.com/Shows/Data-Exposed/Data-Exposed--SQL-Database-Connectivity-Explained/player?WT.mc_id=dataexposed-c9-niner]


## <a name="allow-azure-services"></a>允許 Azure 服務 
[在從 Azure 門戶](sql-database-single-database-get-started.md)創建新的 Azure SQL Server 期間，將取消選中此設置。



在創建 Azure SQL Server 後，還可以通過防火牆窗格更改此設置，如下所示。
  
 ![管理伺服器防火牆的螢幕截圖][2]

設置為**On** Azure SQL Server 時，允許來自 Azure 邊界內所有資源的通信，這些通信可能是訂閱的一部分，也可能不是訂閱的一部分。

在許多情況下 **，ON**設置比大多數客戶想要的更寬鬆。他們可能希望將此設置設置為**OFF，** 並將其替換為限制性更大的 IP 防火牆規則或虛擬網路防火牆規則。 這樣做會影響以下在 Azure 中的 VM 上運行的功能，這些功能不是 VNet 的一部分，因此通過 Azure IP 位址連接到 Sql 資料庫。

### <a name="import-export-service"></a>匯入匯出服務
導入匯出服務不起作用**允許 Azure 服務訪問**伺服器設置為 OFF。 但是，您可以通過從 Azure [VM 手動運行 sqlpackage.exe 或](https://docs.microsoft.com/azure/sql-database/import-export-from-vm)使用 DACFx API 直接在代碼中執行匯出來解決此問題。

### <a name="data-sync"></a>資料同步
要將"資料同步"功能與**允許 Azure 服務訪問伺服器**設置為 OFF 一起，需要創建單個防火牆規則條目，以便從承載**中心**資料庫的區域的**Sql 服務標記**[中添加 IP 位址](sql-database-server-level-firewall-rule.md)。
將這些伺服器級防火牆規則添加到承載**集線器**和**成員**資料庫的邏輯伺服器（可能位於不同的區域）

使用以下 PowerShell 腳本生成對應于美國西部區域 Sql 服務標記的 IP 位址
```powershell
PS C:\>  $serviceTags = Get-AzNetworkServiceTag -Location eastus2
PS C:\>  $sql = $serviceTags.Values | Where-Object { $_.Name -eq "Sql.WestUS" }
PS C:\> $sql.Properties.AddressPrefixes.Count
70
PS C:\> $sql.Properties.AddressPrefixes
13.86.216.0/25
13.86.216.128/26
13.86.216.192/27
13.86.217.0/25
13.86.217.128/26
13.86.217.192/27
```

> [!TIP]
> Get-AzNetworkServiceTag 返回 Sql 服務標記的全域範圍，儘管指定了位置參數。 請務必將其篩選到承載同步組使用的集線器資料庫的區域

請注意，PowerShell 腳本的輸出採用無類域間路由 （CIDR） 標記法，這需要使用[Get-IPrangeStartEnd.ps1](https://gallery.technet.microsoft.com/scriptcenter/Start-and-End-IP-addresses-bcccc3a9)轉換為開始和結束 IP 位址的格式。
```powershell
PS C:\> Get-IPrangeStartEnd -ip 52.229.17.93 -cidr 26                                                                   
start        end
-----        ---
52.229.17.64 52.229.17.127
```

執行以下其他步驟，將所有 IP 位址從 CIDR 轉換為開始和結束 IP 位址格式。

```powershell
PS C:\>foreach( $i in $sql.Properties.AddressPrefixes) {$ip,$cidr= $i.split('/') ; Get-IPrangeStartEnd -ip $ip -cidr $cidr;}                                                                                                                
start          end
-----          ---
13.86.216.0    13.86.216.127
13.86.216.128  13.86.216.191
13.86.216.192  13.86.216.223
```
現在，您可以將這些規則添加為不同的防火牆規則，然後設置**允許 Azure 服務訪問伺服器**到 OFF。


## <a name="ip-firewall-rules"></a>IP 防火牆規則
基於 Ip 的防火牆是 Azure SQL Server 的一項功能，它阻止對資料庫伺服器的所有訪問，直到您顯式添加用戶端電腦的[IP 位址](sql-database-server-level-firewall-rule.md)。


## <a name="virtual-network-firewall-rules"></a>虛擬網路防火牆規則

除了 IP 規則之外，Azure SQL Server 防火牆還允許您定義*虛擬網路規則*。  
要瞭解更多資訊，請參閱[Azure SQL 資料庫的虛擬網路服務終結點和規則](sql-database-vnet-service-endpoint-rule-overview.md)或觀看此視頻：

> [!VIDEO https://channel9.msdn.com/Shows/Data-Exposed/Data-Exposed--Demo--Vnet-Firewall-Rules-for-SQL-Database/player?WT.mc_id=dataexposed-c9-niner]

 ### <a name="azure-networking-terminology"></a>Azure 網路術語  
在流覽虛擬網路防火牆規則時，請注意以下 Azure 網路術語

**虛擬網路：** 可以讓虛擬網路與 Azure 訂閱關聯 

**子網路：** 虛擬網路包含**子網路**。 您有的任何 Azure 虛擬機器 (VM) 會指派給子網路。 一個子網路可以包含多個 VM 或其他計算節點。 計算虛擬網路外部的節點無法存取虛擬網路，除非您設定安全性來允許存取。

**虛擬網路服務終結點：**[虛擬網路服務終結點](../virtual-network/virtual-network-service-endpoints-overview.md)是屬性值包含一個或多個正式 Azure 服務類型名稱的子網。 本文中我們探討類型名稱 **Microsoft.Sql**，它參考名為 SQL Database 的 Azure 服務。

**虛擬網路規則：** SQL Database 伺服器的虛擬網路規則是 SQL Database 伺服器的存取控制清單 (ACL) 中所列的子網路。 子網路必須包含 **Microsoft.Sql** 類型名稱，才能列在 SQL Database 的 ACL 中。 虛擬網路規則會指示 SQL Database 伺服器接受來自子網路上每個節點的通訊。


## <a name="ip-vs-virtual-network-firewall-rules"></a>IP 與虛擬網路防火牆規則

Azure SQL Server 防火牆允許您指定 IP 位址範圍，從中接受通信到 SQL 資料庫中。 此方法對 Azure 私人網路外部的穩定 IP 位址很適合。 但是，Azure 私人網路絡中的虛擬機器 （VM） 配置了*動態*IP 位址。 重新開機 VM 時，動態 IP 位址可能會更改，進而使基於 IP 的防火牆規則失效。 在生產環境中，請勿在防火牆規則中指定動態 IP 位址。

您可以通過獲取 VM 的*靜態*IP 位址來解決此限制。 如需詳細資料，請參閱[使用 Azure 入口網站設定虛擬機器的私人 IP 位址](../virtual-network/virtual-networks-static-private-ip-arm-pportal.md)。 不過，靜態 IP 方法可能變得難以管理，在大規模使用時成本很高。 

虛擬網路規則更容易從包含 VM 的特定子網建立和管理訪問。

> [!NOTE]
> 子網路上還不能有 SQL Database。 如果 Azure SQL Database 伺服器是虛擬網路中某個子網路的節點，則虛擬網路內的所有節點都可以與 SQL Database 通訊。 在此情況下，VM 可以與 SQL Database 通訊，而不需要任何虛擬網路規則或 IP 規則。

## <a name="private-link"></a>私人連結 
專用連結允許您通過**專用終結點**連接到 Azure SQL 伺服器。 專用終結點是特定[虛擬網路和](../virtual-network/virtual-networks-overview.md)子網中的私人 IP 位址。

## <a name="next-steps"></a>後續步驟

- 有關創建伺服器級 IP 防火牆規則的快速入門，請參閱創建[Azure SQL 資料庫](sql-database-single-database-get-started.md)。

- 有關創建伺服器級 Vnet 防火牆規則的快速入門，請參閱[Azure SQL 資料庫的虛擬網路服務終結點和規則](sql-database-vnet-service-endpoint-rule-overview.md)。

- 有關從開源或協力廠商應用程式連接到 Azure SQL 資料庫的説明，請參閱[用戶端快速入門代碼示例到 SQL 資料庫](https://msdn.microsoft.com/library/azure/ee336282.aspx)。

- 如需詳細資訊，請參閱[針對 ADO.NET 4.5 及 SQL Database 的 1433 以外的連接埠](sql-database-develop-direct-route-ports-adonet-v12.md)的〈**SQL Database：外部與內部**〉一節

- 有關 Azure SQL 資料庫連接的概述，請參閱[Azure SQL 連接體系結構](sql-database-connectivity-architecture.md)

- 如需 Azure SQL Database 安全性的概觀，請參閱[保護您的資料庫](sql-database-security-overview.md)

<!--Image references-->
[1]: ./media/sql-database-get-started-portal/new-server2.png
[2]: ./media/sql-database-get-started-portal/manage-server-firewall.png

