---
title: SQL Server FCI & DNN 的功能互通性
description: '瞭解在 Azure Vm 上的 SQL Server 上使用特定 SQL Server 功能和分散式網路名稱（DNN）資源時的其他考慮。 '
services: virtual-machines
documentationCenter: na
author: MashaMSFT
editor: monicar
tags: azure-service-management
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/02/2020
ms.author: mathoma
ms.openlocfilehash: f9c4f58c3318d9d030637f85f3c1597b98d458c7
ms.sourcegitcommit: 845a55e6c391c79d2c1585ac1625ea7dc953ea89
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/05/2020
ms.locfileid: "85965461"
---
# <a name="feature-interoperability-with-sql-server-fci--dnn"></a>SQL Server FCI & DNN 的功能互通性
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

有些 SQL Server 功能依賴硬式編碼的虛擬網路名稱（VNN）。 因此，使用分散式網路名稱（DNN）資源搭配您的容錯移轉叢集實例，並在 Azure Vm 上 SQL Server 時，還有一些額外的考慮。 

在本文中，您將瞭解如何在使用 DNN 資源時設定網路別名，以及哪些 SQL Server 功能需要額外的考慮。

## <a name="create-network-alias-fci"></a>建立網路別名（FCI）

有些伺服器端元件依賴硬式編碼的 VNN 值，而且需要網路別名，將 VNN 對應至 DNN DNS 名稱，才能正常運作。 依照[建立伺服器別名](/sql/database-engine/configure-windows/create-or-delete-a-server-alias-for-use-by-a-client)中的步驟來建立別名，以將 VNN 對應至 DNN DNS 名稱。 

針對預設實例，您可以直接將 VNN 對應至 DNN DNS 名稱，例如 VNN = DNN DNS 名稱。
例如，如果 VNN 名稱是 `FCI1` ，則實例名稱是 `MSSQLSERVER` ，而 DNN 是 `FCI1DNN` （用戶端先前連接到 `FCI` ，現在會連接到），然後將 `FCI1DNN` VNN 對應 `FCI1` 至 DNN `FCI1DNN` 。 

針對已命名的實例，應該針對完整的實例執行網路別名對應，如此 `VNN\Instance`  =  `DNN\Instance` 。 例如，如果 VNN 名稱是 `FCI1` ，則實例名稱是 `instA` ，而 DNN 是 `FCI1DNN` （用戶端先前連接到 `FCI1\instA` ，現在會連接到），然後將 `FCI1DNN\instaA` VNN 對應 `FCI1\instaA` 至 DNN `FCI1DNN\instaA` 。 



## <a name="client-drivers"></a>用戶端驅動程式

針對 ODBC、OLEDB、ADO.NET、JDBC、PHP 和 Node.js 驅動程式，使用者必須在連接字串中明確指定 DNN DNS 名稱做為伺服器名稱。 若要確保在容錯移轉時能夠快速連線，請在 `MultiSubnetFailover=True` SQL 用戶端支援連接字串時，將它加入。 

## <a name="tools"></a>工具

[SQL Server Management Studio](/sql/ssms/sql-server-management-studio-ssms)、 [sqlcmd](/sql/tools/sqlcmd-utility)、 [Azure Data Studio](/sql/azure-data-studio/what-is)和[SQL Server Data Tools](/sql/ssdt/sql-server-data-tools)的使用者，必須在連接字串中明確指定 DNN DNS 名稱做為伺服器名稱。 

## <a name="availability-groups-and-fci"></a>可用性群組和 FCI

您可以使用容錯移轉叢集實例做為其中一個複本來設定 Always On 可用性群組。 在此設定中，FCI 複本的鏡像端點 URL 必須使用 FCI DNN。 同樣地，如果將 FCI 當做唯讀複本使用，FCI 複本的唯讀路由就必須使用 FCI DNN。 

鏡像端點的格式為： `ENDPOINT_URL = 'TCP://<DNN DNS name>:<mirroring endpoint port>'` 。 

例如，如果您的 DNN DNS 名稱是 `dnnlsnr` ，而 `5022` 是 FCI 之鏡像端點的通訊埠，則建立端點 URL 的 Transact-sql （t-sql）程式碼片段看起來像這樣： 

```sql
ENDPOINT_URL = 'TCP://dnnlsnr:5022'
```

同樣地，唯讀路由 URL 的格式為： `TCP://<DNN DNS name>:<SQL Server instance port>` 。 

例如，如果您的 DNN DNS 名稱是 `dnnlsnr` ，而 `1444` 是唯讀目標所使用的通訊埠 SQL Server FCI，則用來建立唯讀路由 URL 的 t-sql 程式碼片段看起來像這樣： 

```sql
READ_ONLY_ROUTING_URL = 'TCP://dnnlsnr:1444'
```

如果 URL 是預設的1433埠，您可以省略此埠。 若為已命名的實例，請為已命名的實例設定靜態通訊埠，並在唯讀路由 URL 中指定它。  

## <a name="replication"></a>複寫

複寫有三個元件：發行者、散發者、訂閱者。 這些元件中的任何一個都可以是容錯移轉叢集實例。 由於 FCI VNN 非常用於複寫設定，因此明確且隱含地，將 VNN 對應至 DNN 的網路別名可能是複寫作業的必要項。 

繼續使用 VNN 名稱作為複寫內的 FCI 名稱，但在*您設定複寫之前*，請先在下列遠端情況中建立網路別名：

| **複寫元件（FCI 與 DNN）** | **遠端元件** | **網路別名對應** | **具有網路對應的伺服器**| 
|---------|---------|---------|-------- | 
|Publisher | 散發者 | 發行者 VNN 至發行者 DNN| 散發者| 
|散發者|訂閱者 |散發者 VNN 至散發者 DNN| 訂閱者 | 
|散發者|發行者 | 散發者 VNN 至散發者 DNN | 發行者| 
|用戶| 散發者| 訂閱者 VNN 訂閱者 DNN | 散發者| 

例如，假設您的發行者已設定為在複寫拓撲中使用 DNN 的 FCI，而且散發者為遠端。 在此情況下，請在散發者伺服器上建立網路別名，以將發行者 VNN 對應到發行者 DNN： 

:::image type="content" source="media/failover-cluster-instance-dnn-interoperability/alias-in-configuration-manager.png" alt-text="使用 SQL Server 組態管理員，將 DNN DNS 名稱設定為網路別名。" :::

針對已命名的實例使用完整的實例名稱，如下列影像範例所示： 

:::image type="content" source="media/failover-cluster-instance-dnn-interoperability/alias-named-instance-configuration-manager.png" alt-text="為已命名的實例設定網路別名時，請使用完整的實例名稱。" :::

## <a name="database-mirroring"></a>資料庫鏡像

您可以使用 FCI 做為資料庫鏡像夥伴來設定資料庫鏡像。 使用[transact-sql （t-sql）](/sql/database-engine/database-mirroring/example-setting-up-database-mirroring-using-windows-authentication-transact-sql) ，而不是 SQL Server Management Studio GUI 來設定它。 使用 T-sql 可確保使用 DNN 而非 VNN 來建立資料庫鏡像端點。 

例如，如果您的 DNN DNS 名稱是 `dnnlsnr` ，而且資料庫鏡像端點為7022，則下列 t-sql 程式碼片段會設定資料庫鏡像夥伴： 

```sql
ALTER DATABASE AdventureWorks
    SET PARTNER =
    'TCP://dnnlsnr:7022'
GO 
```

針對用戶端存取，**容錯移轉夥伴**屬性可以處理資料庫鏡像容錯移轉，但不能 FCI 容錯移轉。 

## <a name="msdtc"></a>MSDTC

FCI 可以參與由 Microsoft 分散式交易協調器（MSDTC）協調的分散式交易。 雖然叢集 MSDTC 和本機 MSDTC 都支援 FCI DNN，但在 Azure 中，叢集 MSDTC 仍然需要負載平衡器。 FCI 中所定義的 DNN 不會取代 Azure 中叢集 MSDTC 的 Azure Load Balancer 需求。 

## <a name="filestream"></a>FileStream

雖然 FCI 中的資料庫支援 FileStream，但不支援使用檔案系統 Api 搭配 DNN 來存取 FileStream 或 FileTable。 

## <a name="linked-servers"></a>連結的伺服器

支援使用連結伺服器搭配 FCI DNN。 請直接使用 DNN 來設定連結的伺服器，或使用網路別名將 VNN 對應至 DNN。 


例如，若要為已命名的實例建立具有 DNN DNS 名稱的連結伺服器 `dnnlsnr` `insta1` ，請使用下列 Transact-sql （t-sql）命令：

```sql
USE [master]   
GO   

EXEC master.dbo.sp_addlinkedserver    
    @server = N'dnnlsnr\inst1',    
    @srvproduct=N'SQL Server' ;   
GO 
```

或者，您也可以改為使用虛擬網路名稱（VNN）來建立連結的伺服器，但接著必須定義網路別名，才能將 VNN 對應至 DNN。 

例如，針對 [實例名稱] `insta1` 、[VNN 名稱] `vnnname` 和 [DNN 名稱] `dnnlsnr` ，使用下列 transact-sql （t-sql）命令來建立使用 VNN 的連結伺服器：

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


- 哪一個 SQL Server 版本提供 DNN 支援？ 

   SQL Server 2019 CU2 和更新版本。

- 使用 DNN 時，預期的容錯移轉時間為何？

   針對 DNN，容錯移轉時間只會是 FCI 容錯移轉時間，而不會新增任何時間（例如使用 Azure Load Balancer 時的探查時間）。

- SQL 用戶端是否有任何版本需求支援使用 OLEDB 和 ODBC 進行 DNN？

   我們建議 `MultiSubnetFailover=True` DNN 的連接字串支援。 從 SQL Server 2012 （11. x）開始提供。

- 是否需要使用 DNN 進行任何 SQL Server 設定變更？ 

   SQL Server 不需要任何設定變更就能使用 DNN，但是有些 SQL Server 功能可能需要更多考慮。 

- DNN 是否支援多重子網叢集？

   是。 叢集會將 DNS 中的 DNN 系結到叢集中所有節點的實體 IP 位址，而不論子網為何。 SQL 用戶端會嘗試所有 DNS 名稱的 IP 位址，而不論子網為何。 



## <a name="next-steps"></a>後續步驟

如需詳細資訊，請參閱 

- [Windows 叢集技術](/windows-server/failover-clustering/failover-clustering-overview)   
- [SQL Server 容錯移轉叢集實例](/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server)

