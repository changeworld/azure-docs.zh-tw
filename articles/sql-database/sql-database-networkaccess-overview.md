---
title: 網路存取控制
description: 概述 Azure SQL Database 和 Azure Synapse 分析的網路存取控制來管理存取權，以及設定單一或集區資料庫。
services: sql-database
ms.service: sql-database
ms.subservice: security
titleSuffix: Azure SQL Database and Azure Synapse Analytics
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: vanto
ms.date: 03/09/2020
ms.openlocfilehash: fdeb8ee3fbb01ea007205e02eb247925fb3baea1
ms.sourcegitcommit: 1895459d1c8a592f03326fcb037007b86e2fd22f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/01/2020
ms.locfileid: "82629570"
---
# <a name="azure-sql-database-and-azure-synapse-analytics-network-access-controls"></a>Azure SQL Database 和 Azure Synapse 分析網路存取控制

> [!NOTE]
> 本文適用于 Azure SQL server，以及在 Azure SQL server 上建立 SQL Database 和 Azure Synapse 分析資料庫。 為了簡單起見，在同時參考 SQL Database 和 Azure Synapse 分析時，會使用 SQL Database。

> [!IMPORTANT]
> 本文「不」** 適用於 **Azure SQL Database 受控執行個體**。 如需網路設定的詳細資訊，請參閱[連接到受控執行個體](sql-database-managed-instance-connect-app.md)。

當您從[Azure 入口網站](sql-database-single-database-get-started.md)建立新的 Azure SQL Server 時，其結果會是*yourservername.database.windows.net*格式的公用端點。

您可以使用下列網路存取控制，選擇性地允許透過公用端點存取 SQL Database：
- 允許 Azure 服務：當設定為 ON 時，Azure 界限內的其他資源（例如 Azure 虛擬機器）可以存取 SQL Database

- IP 防火牆規則：使用此功能明確允許來自特定 IP 位址的連線，例如從內部部署機器

您也可以透過下列方式，允許從[虛擬網路](../virtual-network/virtual-networks-overview.md)對 SQL Database 的私用存取：
- 虛擬網路防火牆規則：使用此功能可允許來自 Azure 界限內特定虛擬網路的流量

- 私人連結：使用此功能可在特定虛擬網路內建立 Azure SQL Server 的私用端點



如需這些存取控制的高階說明及其用途，請參閱下列影片：
> [!VIDEO https://channel9.msdn.com/Shows/Data-Exposed/Data-Exposed--SQL-Database-Connectivity-Explained/player?WT.mc_id=dataexposed-c9-niner]


## <a name="allow-azure-services"></a>允許 Azure 服務 
[從 Azure 入口網站](sql-database-single-database-get-started.md)建立新的 Azure SQL Server 期間，會將此設定保留為未核取狀態。



您也可以在建立 Azure SQL Server 之後，透過 [防火牆] 窗格變更此設定，如下所示。
  
 ![管理伺服器防火牆的螢幕擷取畫面][2]

當 azure SQL Server**上**的設定為時，允許從 Azure 界限內的所有資源進行通訊，這不一定屬於您的訂用帳戶。

在許多情況下，[**開啟**] 設定比大多數客戶所需的更寬鬆。 他們可能會想要將此設定設為 [**關閉**]，並將其取代為更嚴格的 IP 防火牆規則或虛擬網路防火牆規則。 這麼做會影響在 Azure 中不屬於 VNet 的 Vm 上執行的下列功能，因此會透過 Azure IP 位址連接到 SQL Database。

### <a name="import-export-service"></a>匯入匯出服務
[**允許存取 Azure 服務**] 設為 [**關閉**] 時，[匯入匯出服務] 無法運作。 不過，您可以[從 AZURE VM 手動執行 sqlpackage，或](https://docs.microsoft.com/azure/sql-database/import-export-from-vm)使用 DACFx API 直接在程式碼中執行匯出，以解決此問題。

### <a name="data-sync"></a>資料同步
若要使用資料同步功能搭配 **[允許存取 Azure 服務**] 設定為 [**關閉**]，您必須建立個別的防火牆規則專案，以便從裝載**中樞**資料庫之區域的**Sql 服務**標籤[新增 IP 位址](sql-database-server-level-firewall-rule.md)。
將這些伺服器層級防火牆規則新增至裝載**中樞**和**成員**資料庫的邏輯伺服器（可能位於不同的區域）

使用下列 PowerShell 腳本來產生對應至「美國西部」區域之 SQL 服務標籤的 IP 位址
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
> AzNetworkServiceTag 會傳回 SQL 服務標記的全域範圍，但不包括指定 Location 參數。 請務必將它篩選為裝載您的同步處理群組所使用之中樞資料庫的區域

請注意，PowerShell 腳本的輸出是無類別網域間路由（CIDR）標記法，而且必須使用 Get-IPrangeStartEnd 將這項轉換成開始和結束 IP 位址的格式，如下所示[。](https://gallery.technet.microsoft.com/scriptcenter/Start-and-End-IP-addresses-bcccc3a9)
```powershell
PS C:\> Get-IPrangeStartEnd -ip 52.229.17.93 -cidr 26                                                                   
start        end
-----        ---
52.229.17.64 52.229.17.127
```

執行下列額外步驟，將所有 IP 位址從 CIDR 轉換成開始和結束 IP 位址格式。

```powershell
PS C:\>foreach( $i in $sql.Properties.AddressPrefixes) {$ip,$cidr= $i.split('/') ; Get-IPrangeStartEnd -ip $ip -cidr $cidr;}                                                                                                                
start          end
-----          ---
13.86.216.0    13.86.216.127
13.86.216.128  13.86.216.191
13.86.216.192  13.86.216.223
```
您現在可以將它們新增為不同的防火牆規則，然後將 [**允許 Azure 服務存取伺服器**] 設定為 [關閉]。


## <a name="ip-firewall-rules"></a>IP 防火牆規則
Ip 型防火牆是 Azure SQL Server 的一項功能，可防止所有對您資料庫伺服器的存取，直到您明確新增用戶端電腦的[IP 位址](sql-database-server-level-firewall-rule.md)為止。


## <a name="virtual-network-firewall-rules"></a>虛擬網路防火牆規則

除了 IP 規則，Azure SQL Server 防火牆可讓您定義*虛擬網路規則*。  
若要深入瞭解，請參閱[Azure SQL Database 的虛擬網路服務端點和規則](sql-database-vnet-service-endpoint-rule-overview.md)，或觀看這段影片：

> [!VIDEO https://channel9.msdn.com/Shows/Data-Exposed/Data-Exposed--Demo--Vnet-Firewall-Rules-for-SQL-Database/player?WT.mc_id=dataexposed-c9-niner]

 ### <a name="azure-networking-terminology"></a>Azure 網路術語  
當您探索虛擬網路防火牆規則時，請注意下列 Azure 網路術語

**虛擬網路：** 您可以有與您的 Azure 訂用帳戶相關聯的虛擬網路 

**子網路：** 虛擬網路包含**子網路**。 您有的任何 Azure 虛擬機器 (VM) 會指派給子網路。 一個子網路可以包含多個 VM 或其他計算節點。 計算虛擬網路外部的節點無法存取虛擬網路，除非您設定安全性來允許存取。

**虛擬網路服務端點：**[虛擬網路服務端點](../virtual-network/virtual-network-service-endpoints-overview.md)是一個子網，其屬性值包含一或多個正式的 Azure 服務類型名稱。 本文中我們探討類型名稱 **Microsoft.Sql**，它參考名為 SQL Database 的 Azure 服務。

**虛擬網路規則：** SQL Database 伺服器的虛擬網路規則是 SQL Database 伺服器的存取控制清單 (ACL) 中所列的子網路。 子網路必須包含 **Microsoft.Sql** 類型名稱，才能列在 SQL Database 的 ACL 中。 虛擬網路規則會指示 SQL Database 伺服器接受來自子網路上每個節點的通訊。


## <a name="ip-vs-virtual-network-firewall-rules"></a>IP 與虛擬網路防火牆規則的比較

Azure SQL Server 防火牆可讓您指定接受通訊的 IP 位址範圍，以 SQL Database。 此方法對 Azure 私人網路外部的穩定 IP 位址很適合。 不過，Azure 私人網路內的虛擬機器（Vm）會以*動態*IP 位址進行設定。 當您的 VM 重新開機時，動態 IP 位址可能會變更，而又會使以 IP 為基礎的防火牆規則失效。 在生產環境中，請勿在防火牆規則中指定動態 IP 位址。

您可以藉由取得 VM 的*靜態*IP 位址來解決這項限制。 如需詳細資料，請參閱[使用 Azure 入口網站設定虛擬機器的私人 IP 位址](../virtual-network/virtual-networks-static-private-ip-arm-pportal.md)。 不過，靜態 IP 方法可能變得難以管理，在大規模使用時成本很高。 

虛擬網路規則可讓您更輕鬆地建立和管理來自包含您 Vm 之特定子網的存取權。

> [!NOTE]
> 子網路上還不能有 SQL Database。 如果 Azure SQL Database 伺服器是虛擬網路中某個子網路的節點，則虛擬網路內的所有節點都可以與 SQL Database 通訊。 在此情況下，VM 可以與 SQL Database 通訊，而不需要任何虛擬網路規則或 IP 規則。

## <a name="private-link"></a>私人連結 
私人連結可讓您透過**私人端點**連接到 Azure SQL Server。 私人端點是特定[虛擬網路](../virtual-network/virtual-networks-overview.md)和子網內的私人 IP 位址。

## <a name="next-steps"></a>後續步驟

- 如需建立伺服器層級 IP 防火牆規則的快速入門，請參閱[建立 AZURE SQL 資料庫](sql-database-single-database-get-started.md)。

- 如需建立伺服器層級 Vnet 防火牆規則的快速入門，請參閱[Azure SQL Database 的虛擬網路服務端點和規則](sql-database-vnet-service-endpoint-rule-overview.md)。

- 如需從開放原始碼或協力廠商應用程式連線到 Azure SQL 資料庫的說明，請參閱[SQL Database 的用戶端快速入門程式碼範例](https://msdn.microsoft.com/library/azure/ee336282.aspx)。

- 如需詳細資訊，請參閱[針對 ADO.NET 4.5 及 SQL Database 的 1433 以外的連接埠](sql-database-develop-direct-route-ports-adonet-v12.md)的〈**SQL Database：外部與內部**〉一節

- 如需 Azure SQL Database 連線的總覽，請參閱[AZURE SQL 連線架構](sql-database-connectivity-architecture.md)

- 如需 Azure SQL Database 安全性的概觀，請參閱[保護您的資料庫](sql-database-security-overview.md)

<!--Image references-->
[1]: ./media/sql-database-get-started-portal/new-server2.png
[2]: ./media/sql-database-get-started-portal/manage-server-firewall.png

