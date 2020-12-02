---
title: 網路存取控制
titleSuffix: Azure SQL Database & Azure Synapse Analytics
description: 概述如何管理及控制 Azure SQL Database 和 Azure Synapse Analytics 的網路存取。
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: sqldbrb=3
ms.devlang: ''
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: vanto
ms.date: 03/09/2020
ms.openlocfilehash: 90bc57af3aaf0d11cd354bfe7163014f836a72e8
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/01/2020
ms.locfileid: "96460000"
---
# <a name="azure-sql-database-and-azure-synapse-analytics-network-access-controls"></a>Azure SQL Database 和 Azure Synapse Analytics 網路存取控制

當您從 Azure SQL Database 和 Azure Synapse Analytics 的 [Azure 入口網站](single-database-create-quickstart.md) 建立邏輯 SQL server 時，結果會是 *yourservername.database.windows.net* 格式的公用端點。

您可以使用下列網路存取控制，透過公用端點選擇性地允許存取資料庫：

- 允許 Azure 服務：當設為 ON 時，Azure 界限內的其他資源（例如 Azure 虛擬機器）可以存取 SQL Database
- IP 防火牆規則：使用此功能可明確允許來自特定 IP 位址的連線，例如從內部部署機器

您也可以透過下列方式，允許從 [虛擬網路](../../virtual-network/virtual-networks-overview.md) 對資料庫進行私用存取：

- 虛擬網路防火牆規則：使用此功能可允許來自 Azure 界限內特定虛擬網路的流量
- Private Link：使用此功能可為特定虛擬網路內的 [邏輯 SQL server](logical-servers.md) 建立私人端點

> [!IMPORTANT]
> 本文 *並不適* 用於 **SQL 受控執行個體**。 如需網路設定的詳細資訊，請參閱 [連接到 AZURE SQL 受控執行個體](../managed-instance/connect-application-instance.md) 。

請參閱下列影片，以取得這些存取控制和其用途的概要說明：

> [!VIDEO https://channel9.msdn.com/Shows/Data-Exposed/Data-Exposed--SQL-Database-Connectivity-Explained/player?WT.mc_id=dataexposed-c9-niner]

## <a name="allow-azure-services"></a>允許 Azure 服務

根據預設， [從 Azure 入口網站](single-database-create-quickstart.md)建立新的邏輯 SQL server 時，此設定會設定為 [ **關閉**]。 使用公用服務端點允許連線時，就會顯示此設定。

您也可以在建立邏輯 SQL server 之後，透過防火牆窗格變更此設定，如下所示。
  
![管理伺服器防火牆的螢幕擷取畫面][2]

當設定為 **ON** 時，您的伺服器允許來自 Azure 界限內所有資源的通訊，這些資源不一定是訂用帳戶的一部分。

在許多情況下，「 **開啟** 」設定比大多數客戶想要的更寬鬆。 您可能會想要將此設定設為 **OFF** ，並將其取代為更嚴格的 IP 防火牆規則或虛擬網路防火牆規則。 

不過，這麼做會影響下列功能，這些功能是在 Azure 中的虛擬機器上執行，而這些虛擬機器不屬於您的虛擬網路，因此透過 Azure IP 位址連接到資料庫：

### <a name="import-export-service"></a>匯入匯出服務

當 [ **允許存取 Azure 服務** ] 設為 [ **關閉**] 時，[匯入匯出服務] 無法運作。 不過，您可以 [手動從 AZURE VM 執行 sqlpackage.exe，或](./database-import-export-azure-services-off.md) 使用 DACFx API 直接在程式碼中執行匯出，來解決此問題。

### <a name="data-sync"></a>資料同步

若要搭配使用資料同步功能與 [**允許存取 Azure 服務**] 設為 [**關閉**]，您需要建立個別的防火牆規則專案，以從裝載 **中樞** 資料庫之區域的 **Sql 服務標記**[新增 IP 位址](firewall-create-server-level-portal-quickstart.md)。
將這些伺服器層級防火牆規則新增至裝載 **中樞** 和 **成員** 資料庫 (的伺服器，這些伺服器可能位於不同區域) 

使用下列 PowerShell 腳本來產生對應到美國西部區域的 SQL 服務標記的 IP 位址

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
> Get-AzNetworkServiceTag 會傳回 SQL 服務標記的全域範圍（儘管指定 Location 參數）。 請務必將其篩選為裝載同步處理群組所用中樞資料庫的區域

請注意，PowerShell 腳本的輸出是在無類別 Inter-Domain 路由 (CIDR) 標記法。 這需要使用如下的 [Get-IPrangeStartEnd.ps1](https://gallery.technet.microsoft.com/scriptcenter/Start-and-End-IP-addresses-bcccc3a9) 轉換成開始和結束 IP 位址的格式：

```powershell
PS C:\> Get-IPrangeStartEnd -ip 52.229.17.93 -cidr 26
start        end
-----        ---
52.229.17.64 52.229.17.127
```

您可以使用這個額外的 PowerShell 腳本，將所有 IP 位址從 CIDR 轉換成開始和結束 IP 位址格式。

```powershell
PS C:\>foreach( $i in $sql.Properties.AddressPrefixes) {$ip,$cidr= $i.split('/') ; Get-IPrangeStartEnd -ip $ip -cidr $cidr;}
start          end
-----          ---
13.86.216.0    13.86.216.127
13.86.216.128  13.86.216.191
13.86.216.192  13.86.216.223
```

您現在可以將它們新增為不同的防火牆規則，然後將 [ **允許 Azure 服務存取伺服器**  ] 設定為 [關閉]。

## <a name="ip-firewall-rules"></a>IP 防火牆規則

以 Ip 為基礎的防火牆是 Azure 中邏輯 SQL server 的一項功能，可在您明確新增用戶端電腦的 [IP 位址](firewall-create-server-level-portal-quickstart.md) 之前，防止所有對伺服器的存取。

## <a name="virtual-network-firewall-rules"></a>虛擬網路防火牆規則

除了 IP 規則，伺服器防火牆還可讓您定義 *虛擬網路規則*。  
若要深入瞭解，請參閱 [虛擬網路服務端點和 Azure SQL Database 的規則](vnet-service-endpoint-rule-overview.md) ，或觀賞這段影片：

> [!VIDEO https://channel9.msdn.com/Shows/Data-Exposed/Data-Exposed--Demo--Vnet-Firewall-Rules-for-SQL-Database/player?WT.mc_id=dataexposed-c9-niner]

### <a name="azure-networking-terminology"></a>Azure 網路術語

當您探索虛擬網路防火牆規則時，請注意下列 Azure 網路詞彙

**虛擬網路：** 您可以有與您的 Azure 訂用帳戶相關聯的虛擬網路

**子網路：** 虛擬網路包含 **子網路**。 您有的任何 Azure 虛擬機器 (VM) 會指派給子網路。 一個子網路可以包含多個 VM 或其他計算節點。 除非您將安全性設定為允許存取，否則位於虛擬網路外部的計算節點無法存取您的虛擬網路。

**虛擬網路服務端點：**[虛擬網路服務端點](../../virtual-network/virtual-network-service-endpoints-overview.md)是一個子網，其屬性值包含一或多個正式的 Azure 服務類型名稱。 在本文中，我們想要瞭解 **Microsoft** 的型別名稱，這是指名為 SQL Database 的 Azure 服務。

**虛擬網路規則：** 您伺服器的虛擬網路規則是您伺服器的存取控制清單 (ACL) 中所列的子網。 若要在 SQL Database 的資料庫 ACL 中，子網必須包含 **.sql** 類型名稱。 虛擬網路規則會指示您的伺服器接受來自子網上每個節點的通訊。

## <a name="ip-vs-virtual-network-firewall-rules"></a>IP 與虛擬網路防火牆規則

Azure SQL Database 防火牆可讓您指定 IP 位址範圍，以從中接受通訊到 SQL Database。 此方法對 Azure 私人網路外部的穩定 IP 位址很適合。 不過，在 Azure 私人網路中) 的虛擬機器 (Vm 會設定為使用 *動態* IP 位址。 當您的 VM 重新開機時，動態 IP 位址可能會變更，並接著讓以 IP 為基礎的防火牆規則失效。 在生產環境中，請勿在防火牆規則中指定動態 IP 位址。

您可以藉由取得 VM 的 *靜態* IP 位址來解決這項限制。 如需詳細資訊，請參閱 [使用 Azure 入口網站建立具有靜態公用 IP 位址的虛擬機器](../../virtual-network/virtual-network-deploy-static-pip-arm-portal.md)。 不過，靜態 IP 方法可能會變得難以管理，而且在大規模完成時成本高昂。

虛擬網路規則可讓您更輕鬆地建立和管理從包含您 Vm 的特定子網存取。

> [!NOTE]
> 子網路上還不能有 SQL Database。 如果您的伺服器是虛擬網路中子網上的節點，則虛擬網路中的所有節點都可以與您的 SQL Database 進行通訊。 在此情況下，VM 可以與 SQL Database 通訊，而不需要任何虛擬網路規則或 IP 規則。

## <a name="private-link"></a>Private Link

Private Link 可讓您透過 **私人端點** 連接到伺服器。 私人端點是特定 [虛擬網路](../../virtual-network/virtual-networks-overview.md) 和子網內的私人 IP 位址。

## <a name="next-steps"></a>後續步驟

- 如需有關建立伺服器層級 IP 防火牆規則的快速入門，請參閱 [在 SQL Database 中建立資料庫](single-database-create-quickstart.md)。

- 如需有關建立伺服器層級虛擬網路防火牆規則的快速入門，請參閱 [Azure SQL Database 的虛擬網路服務端點和規則](vnet-service-endpoint-rule-overview.md)。

- 如需從開放原始碼或協力廠商應用程式連接到 SQL Database 中資料庫的說明，請參閱 [SQL Database 的用戶端快速入門程式碼範例](/previous-versions/azure/ee336282(v=azure.100))。

- 如需詳細資訊，請參閱 [針對 ADO.NET 4.5 及 SQL Database 的 1433 以外的連接埠](adonet-v12-develop-direct-route-ports.md)的〈**SQL Database：外部與內部**〉一節

- 如需 Azure SQL Database 連線能力的總覽，請參閱 [AZURE SQL 連線架構](connectivity-architecture.md)

- 如需 Azure SQL Database 安全性的概觀，請參閱[保護您的資料庫](security-overview.md)

<!--Image references-->
[1]: media/quickstart-create-single-database/new-server2.png
[2]: media/quickstart-create-single-database/manage-server-firewall.png
