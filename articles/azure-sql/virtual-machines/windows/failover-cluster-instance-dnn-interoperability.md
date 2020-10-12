---
title: 與 SQL Server FCI & DNN 的功能互通性
description: '瞭解在 Azure Vm 上的 SQL Server 上使用容錯移轉叢集實例來 DNN) 資源時，使用特定 SQL Server 功能和分散式網路名稱時的其他考慮 (。 '
services: virtual-machines
documentationCenter: na
author: MashaMSFT
editor: monicar
tags: azure-service-management
ms.service: virtual-machines-sql
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/02/2020
ms.author: mathoma
ms.openlocfilehash: ca782e9949f990857db408919cac342d7f712d2b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91272611"
---
# <a name="feature-interoperability-with-sql-server-fci--dnn"></a>與 SQL Server FCI & DNN 的功能互通性
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

有一些相依于硬式編碼虛擬網路名稱 (VNN) 的 SQL Server 功能。 因此，使用分散式網路名稱 (DNN) 資源與您的容錯移轉叢集實例，並在 Azure Vm 上 SQL Server，則有一些額外的考慮。 

在本文中，您將瞭解如何在使用 DNN 資源時設定網路別名，以及哪些 SQL Server 功能需要其他考慮。

## <a name="create-network-alias-fci"></a>建立網路別名 (FCI) 

有些伺服器端元件依賴硬式編碼的 VNN 值，而且需要網路別名將 VNN 對應至 DNN DNS 名稱，才能正常運作。 遵循 [建立伺服器別名](/sql/database-engine/configure-windows/create-or-delete-a-server-alias-for-use-by-a-client) 中的步驟，建立將 VNN 對應至 DNN DNS 名稱的別名。 

若為預設實例，您可以直接將 VNN 對應至 DNN DNS 名稱，例如 VNN = DNN DNS 名稱。
例如，如果 VNN 名稱是 `FCI1` ，則實例名稱是 `MSSQLSERVER` ，而 DNN 是 `FCI1DNN` (用戶端先前連接到的， `FCI` 現在會連接到 `FCI1DNN`) 然後將 VNN 對應 `FCI1` 至 DNN `FCI1DNN` 。 

若為已命名的實例，則應該為完整的實例完成網路別名對應，如此一來 `VNN\Instance`  =  `DNN\Instance` 。 例如，如果 VNN 名稱是 `FCI1` ，則實例名稱是 `instA` ，而 DNN 是 `FCI1DNN` (用戶端先前連接到的， `FCI1\instA` 現在會連接到 `FCI1DNN\instaA`) 然後將 VNN 對應 `FCI1\instaA` 至 DNN `FCI1DNN\instaA` 。 



## <a name="client-drivers"></a>用戶端驅動程式

若為 ODBC、OLEDB、ADO.NET、JDBC、PHP 和 Node.js 驅動程式，使用者必須在連接字串中明確指定 DNN DNS 名稱做為伺服器名稱。 若要確保在容錯移轉時能快速連線，請在 `MultiSubnetFailover=True` SQL 用戶端支援連接字串時加入連接字串。 

## <a name="tools"></a>工具

[SQL Server Management Studio](/sql/ssms/sql-server-management-studio-ssms)、 [sqlcmd](/sql/tools/sqlcmd-utility)、 [Azure Data Studio](/sql/azure-data-studio/what-is)和[SQL Server Data Tools](/sql/ssdt/sql-server-data-tools)的使用者需要在連接字串中明確指定 DNN DNS 名稱來做為伺服器名稱。 

## <a name="availability-groups-and-fci"></a>可用性群組和 FCI

您可以使用容錯移轉叢集實例做為其中一個複本，來設定 Always On 可用性群組。 在此設定中，FCI 複本的鏡像端點 URL 必須使用 FCI DNN。 同樣地，如果使用 FCI 做為唯讀複本，則 FCI 複本的唯讀路由必須使用 FCI DNN。 

鏡像端點的格式為： `ENDPOINT_URL = 'TCP://<DNN DNS name>:<mirroring endpoint port>'` 。 

例如，如果您的 DNN DNS 名稱為 `dnnlsnr` ，而且 `5022` 是 FCI 鏡像端點的埠，則用來建立端點 URL 的 Transact-sql (t-sql) 程式碼片段看起來像這樣： 

```sql
ENDPOINT_URL = 'TCP://dnnlsnr:5022'
```

同樣地，唯讀路由 URL 的格式為： `TCP://<DNN DNS name>:<SQL Server instance port>` 。 

例如，如果您的 DNN DNS 名稱為 `dnnlsnr` ，而且 `1444` 是唯讀目標 SQL Server FCI 所使用的埠，則用來建立唯讀路由 URL 的 t-sql 程式碼片段如下所示： 

```sql
READ_ONLY_ROUTING_URL = 'TCP://dnnlsnr:1444'
```

您可以省略 URL 中的埠（如果它是預設的1433埠）。 若為已命名的實例，請為指定的實例設定靜態埠，並在唯讀路由 URL 中指定它。  

## <a name="replication"></a>複寫

複寫有三個元件：發行者、散發者、訂閱者。 其中任何一個元件都可以是容錯移轉叢集實例。 由於 FCI VNN 會在複寫設定中（明確且隱含地使用），因此可能需要將 VNN 對應至 DNN 的網路別名，才能讓複寫正常運作。 

繼續使用 VNN 名稱作為複寫內的 FCI 名稱，但在 *您設定複寫之前*，請在下列遠端情況下建立網路別名：

| **使用 DNN) 的複寫元件 (FCI ** | **遠端元件** | **網路別名對應** | **具有網路對應的伺服器**| 
|---------|---------|---------|-------- | 
|發行者 | 散發者 | 發行者 VNN 至發行者 DNN| 散發者| 
|散發者|用戶 |散發者 VNN 至散發者 DNN| 用戶 | 
|散發者|發行者 | 散發者 VNN 至散發者 DNN | 發行者| 
|用戶| 散發者| 訂閱者 VNN 至訂閱者 DNN | 散發者| 

例如，假設您的發行者已設定為在複寫拓撲中使用 DNN 的 FCI，而且散發者為遠端。 在此情況下，請在散發者伺服器上建立網路別名，以將發行者 VNN 對應至發行者 DNN： 

:::image type="content" source="media/failover-cluster-instance-dnn-interoperability/alias-in-configuration-manager.png" alt-text="使用 SQL Server 組態管理員將 DNN DNS 名稱設定為網路別名。" :::

使用命名實例的完整實例名稱，如下圖範例所示： 

:::image type="content" source="media/failover-cluster-instance-dnn-interoperability/alias-named-instance-configuration-manager.png" alt-text="使用 SQL Server 組態管理員將 DNN DNS 名稱設定為網路別名。" :::

## <a name="database-mirroring"></a>資料庫鏡像

您可以使用 FCI 做為資料庫鏡像夥伴來設定資料庫鏡像。 使用 [transact-sql (t-sql) ](/sql/database-engine/database-mirroring/example-setting-up-database-mirroring-using-windows-authentication-transact-sql) 而不是 SQL Server Management Studio GUI 來設定它。 使用 T-sql 可確保使用 DNN 而不是 VNN 來建立資料庫鏡像端點。 

例如，如果您的 DNN DNS 名稱是 `dnnlsnr` ，而且資料庫鏡像端點是7022，則下列 t-sql 程式碼片段會設定資料庫鏡像夥伴： 

```sql
ALTER DATABASE AdventureWorks
    SET PARTNER =
    'TCP://dnnlsnr:7022'
GO 
```

針對用戶端存取，[ **容錯移轉夥伴** ] 屬性可以處理資料庫鏡像容錯移轉，但無法處理 FCI 容錯移轉。 

## <a name="msdtc"></a>MSDTC

FCI 可以參與由 Microsoft Distributed Transaction Coordinator (MSDTC) 協調的分散式交易。 雖然叢集 MSDTC 和本機 MSDTC 都支援 FCI DNN，但在 Azure 中，叢集 MSDTC 仍需要負載平衡器。 在 FCI 中定義的 DNN 不會取代 Azure 中叢集 MSDTC 的 Azure Load Balancer 需求。 

## <a name="filestream"></a>FileStream

雖然 FCI 中的資料庫支援 FileStream，但不支援使用檔案系統 Api 搭配 DNN 來存取 FileStream 或 FileTable。 

## <a name="linked-servers"></a>連結的伺服器

支援使用具有 FCI DNN 的連結伺服器。 您可以直接使用 DNN 來設定連結的伺服器，或使用網路別名將 VNN 對應到 DNN。 


例如，若要使用 DNN DNS 名稱建立 `dnnlsnr` 已命名實例的連結伺服器 `insta1` ，請使用下列 Transact-sql (t-sql) 命令：

```sql
USE [master]   
GO   

EXEC master.dbo.sp_addlinkedserver    
    @server = N'dnnlsnr\inst1',    
    @srvproduct=N'SQL Server' ;   
GO 
```

或者，您也可以使用虛擬網路名稱 (VNN) 來建立連結的伺服器，但是您接著需要定義網路別名，將 VNN 對應到 DNN。 

例如，針對 instance name `insta1` 、VNN name `vnnname` 和 DNN name， `dnnlsnr` 請使用下列 transact-sql (t-sql) 命令，以使用 VNN 來建立連結的伺服器：

```sql
USE [master]
GO   

EXEC master.dbo.sp_addlinkedserver   
    @server = N'vnnname\inst1',    
    @srvproduct=N'SQL Server' ;   
GO 

```

然後，建立要對應的網路別名 `vnnname\insta1` `dnnlsnr\insta1` 。 



## <a name="frequently-asked-questions"></a>常見問題集


- 哪些 SQL Server 版本帶來 DNN 支援？ 

   SQL Server 2019 CU2 和更新版本。

- 使用 DNN 時的預期容錯移轉時間為何？

   針對 DNN，容錯移轉時間只會是 FCI 容錯移轉時間，而不會有任何時間新增 (例如使用 Azure Load Balancer) 時的探查時間。

- SQL 用戶端是否有任何版本需求可支援使用 OLEDB 和 ODBC 來 DNN？

   我們建議 `MultiSubnetFailover=True` DNN 的連接字串支援。 從 SQL Server 2012 (11. x) 開始提供。

- 使用 DNN 時，是否需要變更任何 SQL Server 設定？ 

   SQL Server 不需要任何設定變更即可使用 DNN，但某些 SQL Server 功能可能需要更多考慮。 

- DNN 是否支援多重子網叢集？

   是。 叢集會將 DNS 中的 DNN 系結至叢集中所有節點的實體 IP 位址，不論子網為何。 無論子網為何，SQL 用戶端都會嘗試 DNS 名稱的所有 IP 位址。 



## <a name="next-steps"></a>後續步驟

如需詳細資訊，請參閱 

- [Windows 叢集技術](/windows-server/failover-clustering/failover-clustering-overview)   
- [SQL Server 容錯移轉叢集執行個體](/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server)

